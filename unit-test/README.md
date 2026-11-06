# Paket unit test T1-1 — `compare_global_balance` di merge asimetris

## Ringkasan

Temuan T1-1: `compare_global_balance()` di `validator/global-balance-calculator.cpp`
menghitung fee blok parent hasil merge dengan fee PENUH, padahal tiap blok child
cuma mencetak SETENGAH fee (fee di-halving per level split, `collator.cpp:2417`).
Saat kedua child maju asimetris (A≠B), formula kelebihan hitung creation sebesar
`(max(A,B) − (A+B)/2) × basechain_fee` → `GLOBAL BALANCE DISCREPANCY` → blok MC
jujur ditolak → interval beku.

Paket ini menguji klaim itu LANGSUNG di fungsi aslinya — bukan lewat PoC Python
dan bukan lewat validator utuh. Test memanggil `compare_global_balance()` dengan
dua snapshot `GlobalInfo` yang dibangun dari `MasterchainStateQ` ASLI (dibangun
dari cell yang di-pack tangan + `MasterchainStateQ::fetch`), jadi semua lookup
config/shard di dalam compare ngejalanin kode produksi.

## File paket

| File | Isi | Cara pakai |
|---|---|---|
| `test-global-balance-calculator.cpp` | File test LENGKAP hasil patch (12 test lama + 6 test baru), siap timpa file aslinya | `cp` ke `test/validator/test-global-balance-calculator.cpp` di clone |
| `test-global-balance-calculator.cpp.patch` | Diff test di atas vs commit `857b8a5` (buat review; isi sama dengan file lengkap) | `git apply` (alternatif dari `cp`) |
| `global-balance-calculator.cpp.patch` | Refactor minimal: `namespace {` → `namespace detail {`, `GlobalInfo` pindah ke header, `create()` di-qualify | `git apply` di root repo |
| `global-balance-calculator-internal.hpp.patch` | Deklarasi `GlobalInfo` + `compare_global_balance` di `ton::validator::detail` | `git apply` di root repo |
| `t1-1-unit-test.yml` | Workflow GitHub Actions (build + run + publish ke branch `results`) — upload via browser web UI (token konektor tidak punya scope `workflow`) | Upload ke `.github/workflows/` di fork |
| `RUNBOOK-PC.md` | Langkah copy-paste buat PC user (Windows + WSL) | Ikuti urutan langkahnya |
| `README.md` | File ini | — |

Semua patch dibuat dari commit `857b8a57a178e6ae0fe7fa86e7d81c5233de8560`
(`ton-blockchain/ton`). Refactor di `.cpp`/`.hpp` bersifat behavior-neutral:
`compare_global_balance` dan `GlobalInfo` sebelumnya hidup di anonymous
namespace (cuma bisa dipakai di TU itu sendiri); setelah patch mereka hidup di
`ton::validator::detail` dan bisa dipanggil test. Tidak ada logika yang diubah
— grep repo-wide memastikan tidak ada TU lain yang memakai kedua nama itu.

## 6 test baru (5 verdict + 1 smoke)

Semua pakai fee ConfigParam 14 LIVE (decode 2026-11-06 dari toncenter):
masterchain 1.699.932.928, basechain 1.000.000.000 nanotons. Saldo awal
prev = 1000 TON (anchor sembarang; saldo next = anchor + fee MC + kreasi
TRUE dari blok-blok yang diproduksi). MC seqno = 0 di kedua snapshot
(dengan seqno 0, `ConfigInfo` skip pengecekan prev_blocks — seqno MC tidak
masuk aritmetika compare sama sekali).

| Test | Skenario (angka dari `poc/track1/global-balance-merge/poc.py`) | Harapan |
|---|---|---|
| `FetchMcStateSmoke` | Bangun 1 MC state, cek `fetch` + config + shard lookups, TANPA `compare` | **OK** (diagnostik; kalau ini crash, bug-nya di fixture/fetch, bukan di compare) |
| `MergeAsymmetricS2` | Merge A+2/B+0, prev tops 100/100, parent top 103 | **ERROR** `GLOBAL BALANCE DISCREPANCY (diff=-1000000000)` |
| `MergeAsymmetricS3` | Merge A+0/B+1, prev tops 100/100, parent top 102 | **ERROR** `GLOBAL BALANCE DISCREPANCY (diff=-500000000)` |
| `MergeSymmetricR1` | Merge NYATA MC 103504461, A=B=0, tops 108274667/108274671 → parent 108274672 | **OK** (kontrol) |
| `MergeSymmetricS1` | Merge simetris A+1/B+1, prev tops 100/100, parent top 102 | **OK** (kontrol) |
| `SplitS4` | Split, parent top 100 → children 103/104 | **OK** (kontrol) |

## Run #1 — diagnosa crash (2026-11-06, run 42242066462, FAILED 34m37s)

**Fakta:** build exit 0; 12 test lama PASS; 5 test baru SEMUA segfault
(Signal 11, SIGSEGV) di run `--filter` terpisah, termasuk kontrol R1/S1/S4.
Backtrace tanpa simbol (build `Release` tanpa `-g`): fault di offset
`+0x1eb360`, return address per test bervariasi (`0x1ead36`/`0x1ebe16`/
`0x1ebe86`/`0x1ebef6`/`0x1ebf75`), frame pemanggil `0x1ce85a` identik.
Tidak ada `CHECK failed` dan tidak ada `LOG(ERROR)` dari
`make_test_mc_state` — jadi bukan error-path yang mengembalikan `Status`
(mestinya abort via CHECK, bukan segfault), dan semua CHECK konstruksi
fixture lolos.

**Koreksi atas asumsi awal:** tidak adanya baris INFO "Parsed shard state"
(`global-balance-calculator.cpp:226`) BUKAN bukti crash sebelum parsing —
baris itu ada di `ParsedShardState::fetch`, yang TIDAK PERNAH dipanggil
oleh `compare_global_balance` (fungsi itu murni aritmetika atas dua
`GlobalInfo`). Jadi tidak ada informasi lokasi dari absennya baris itu.

**Kandidat berperingkat (analisis statis menyeluruh atas source @857b8a5;
tidak ada satu pun null-deref yang terbukti — keduanya tetap hipotesis):**

1. **Crash di dalam `MasterchainStateQ::fetch` (jalur baru, belum pernah
   dieksekusi di binary ini).** Test lama tidak pernah memanggil `fetch` —
   mereka memakai `create_shard_state` + `ParsedShardState::fetch`. Jalur
   `fetch` → `mc_reinit` → `ConfigInfo::extract_config` → `Config::unpack`
   adalah mesin paling kompleks yang disentuh test baru (unpack validator
   set, validasi dict dengan augmentor `aug_ShardAccounts`, tree-walk
   shard hashes). Keyakinan: **sedang**. Bukti yang membantah: tiap langkah
   ditelusuri dan mengembalikan `Status`/CHECK dengan bersih di atas kertas.
2. **Crash di dalam `compare_global_balance` sendiri.** Fungsi ini dipanggil
   langsung dari badan TEST (return address bervariasi per test konsisten
   dengan ini). Deref tak-terguard di dalamnya: `get_config()->get_config_param(14)`
   (null hanya kalau `config_` null — tapi `mc_reinit` CHECK `config_`).
   Keyakinan: **sedang-rendah**.
3. **Crash di helper fixture yang ter-inline ke badan test**
   (`make_test_mc_state_root` / `make_test_shard_leaf` / dict `set`) —
   kemungkinan kecil; semua helper itu lolos CHECK dan sebagian besar
   dipakai juga oleh test lama yang PASS. Keyakinan: **rendah**.

**Cacat fixture TERKONFIRMASI (bukan tebakan, diperbaiki di run #2):**
`make_test_validator_set` menulis tag **`0x11`** (`validators#11`) padahal
layout cell-nya `validators_ext#12` (ada `total_weight` + HashmapE).
`Config::unpack_validator_set` (`mc-config.cpp:577`) mencoba dulu
`Record_validators_ext` (gagal karena tag), lalu jatuh ke jalur
`validators#11` yang lenient (mengabaikan trailing bits) — jadi unpack
lolos, tapi lewat jalur yang salah. Tag diperbaiki ke **`0x12`**.
Bukan penyebab crash yang terbukti (jalur lenient sukses di atas kertas),
tapi ini satu-satunya cacat nyata yang ditemukan dan wajib diperbaiki.

**Yang DIPERBAIKI untuk run #2 (bukan sekadar tebakan):**
- Tag validator `0x11` → `0x12` (jalur unpack yang dimaksud).
- Test baru `FetchMcStateSmoke` (jalan pertama): membangun 1 MC state dan
  mengecek `fetch` + config + shard lookups TANPA `compare`. Ini membelah
  kandidat 1 vs 2 dengan pasti di run #2.
- Marker stderr `[t1-1] ...` + `fflush` di `make_test_mc_state` (root
  dibangun → fetch OK → config + N shard OK), plus CHECK defensif
  (`config != null`, `get_shards().size() == descs.size()`) — fixture yang
  rusak gagal LOUD dengan pesan CHECK, bukan segfault ambigu.
- Workflow build `Release` → **`RelWithDebInfo`** (optimasi sama, tambah
  `-g`) supaya backtrace crash berikutnya menampilkan nama fungsi.
- Script publish: semua `open()` pembacaan log pakai
  `encoding="utf-8", errors="replace"` — run #1 gagal menulis RESULTS.md
  karena `UnicodeDecodeError` byte `0x9c` dari backtrace crash di log.

**Harapan run #2:**
- Smoke PASS + S2/S3 PASS + kontrol PASS → **BUG TERKONFIRMASI**.
- Smoke PASS + S2/S3 FAIL (OK) + kontrol PASS → **temuan GUGUR**.
- Smoke FAIL → crash di fixture/`fetch` — baca marker `[t1-1]` terakhir
  di log untuk lokasi persisnya; S2/S3 tidak menyimpulkan apa-apa.
- Kontrol (R1/S1/S4) FAIL → harness salah.

## Run #6 — diagnosa: patch STALE, bukan crash baru (2026-11-06, run 42257911684, FAILED 22m31s)

**Fakta:** cmake exit 0, build exit 0, full-run exit 139 (SIGSEGV).
12 test lama PASS; 5 test baru SEMUA SIGSEGV, frame faulting sama
`+0x1e92a0`. Log smoke test hanya `0 test(s) passed` (17 byte) — filter
tidak me-match test apa pun. Tidak ada satu pun marker `[t1-1]` di log
crash.

**Penyebab — TERBUKTI dari isi branch (bukan tebakan):** binary run #6
dibangun dari patch test **versi LAMA**. Step "Publish STARTED marker"
(workflow v3) menjalankan `git checkout -B results origin/results` di
checkout harness — branch `results` dicabang saat run #1 dan `patches/`-nya
masih berisi patch test ORIGINAL: 5 test baru (tanpa smoke), tag validator
`0x11`, tanpa marker `[t1-1]` sama sekali. Step "Apply T1-1 patches" lalu
membaca `../patches/` dari tree branch `results` itu. Bukti:
`patches/test-global-balance-calculator.cpp.patch` di branch `results`
(304 baris, 0× `FetchMcStateSmoke`, masih `0x11`) vs di branch
`t1-1-unit-test` (350 baris, smoke test ada, tag `0x12`). Patch produksi
(.cpp/.hpp) di kedua branch IDENTIK — hanya patch test yang stale.
Simulasi git lokal (bare remote + clone, kedua branch) mereproduksi ini
persis: setelah `checkout -B results`, `patches/` stale; setelah restore
dari `origin/t1-1-unit-test`, patch current.

**Koreksi atas kesimpulan run #6 sebelumnya:** "crash di DALAM
`make_test_mc_state_root`, hipotesis fetch/compare MATI" — SALAH. Kesimpulan
itu mengasumsikan binary berisi marker round-1; padahal binary run #6
tidak punya marker sama sekali, jadi absennya marker tidak membuktikan
lokasi apa pun. Crash run #6 = crash fixture LAMA yang sama dengan run #1
(tag `0x11` + tanpa smoke/marker). Perbaikan round-1 (tag `0x12`, smoke
test, marker, CHECK defensif) BELUM PERNAH dieksekusi sampai run #7.
Catatan: dengan tag `0x11`, `Config::unpack_validator_set`
(`mc-config.cpp:577`) jatuh ke jalur `#11` yang membaca `list` sebagai
dict kosong → unpack ERROR → `fetch` ERROR → `move_as_ok()` LOG(FATAL)
(SIGABRT, bukan SIGSEGV). Run #6 mati SIGSEGV (11), jadi crash-nya bukan
di jalur error itu — lokasi pastinya tetap belum diketahui dari artefak
run #6 (backtrace tanpa simbol, tanpa marker). Run #7 yang akan menjawabnya.

**Yang DIPERBAIKI untuk run #7 (workflow v4 + patch test v3):**
- Workflow: step "Apply T1-1 patches" mengembalikan `patches/` dari
  `origin/t1-1-unit-test` dulu (`git fetch` + `git checkout
  origin/t1-1-unit-test -- patches/`), lalu tripwire grep: gagal LOUD
  kalau patch tidak mengandung `FetchMcStateSmoke` atau tag `0x12`.
- Patch test: marker `[t1-1]` + `fflush` di DALAM `make_test_mc_state_root`
  (sebelum/sesudah `make_test_shard_hashes`, `make_test_config_dict`,
  `make_test_out_msg_queue_info`, `make_test_shard_accounts`,
  `t_ShardStateUnsplit.validate_ref`) — kali ini marker benar-benar ada
  di binary, jadi crash berikutnya terlokalisasi ke sub-call yang tepat.
- Workflow: step baru "List registered tests" (`nm` hitung objek registrasi
  `test_instance_…`, harap 18) dan "Resolve crash offsets" (`addr2line`
  atas tiap offset `+0x…` di log crash → nama fungsi + file:line).
  Keduanya non-fatal; hasilnya masuk `RESULTS.md` + branch `results`.

**Verifikasi statis tambahan (TLB, bukan tebakan):** layout
`McStateExtra#cc26`, `ConfigParams` (addr 256-bit + ref), `ValidatorInfo`
(`uint32`+`uint32`+Bool), `ShardHashes`, `OldMcBlocksInfo` di
`crypto/block/block.tlb` semuanya cocok dengan builder fixture.
`extract_addr_prefix`/`shard_contains` (`ton/ton-shard.h:42-48,88-99`)
murni bit-ops, benar untuk workchain −1. Tidak ada cacat fixture baru
yang ditemukan; satu-satunya cacat nyata tetap tag `0x11` (sudah `0x12`
di patch current).

## Apa arti tiap hasil

| Hasil | Arti |
|---|---|
| S2 + S3 error (diff −1 TON / −0,5 TON) DAN R1 + S1 + S4 OK | **Bug TERKONFIRMASI.** Self-check T1-1 naik ke PASS; temuan siap submit (dengan catatan gate v17 + proses 90 hari tetap berlaku). |
| S2 dan/atau S3 OK (passed) | **Temuan GUGUR.** Formula ternyata benar untuk kasus itu — draft T1-1 ditutup, jangan submit. |
| R1 / S1 / S4 error | **Harness yang salah, bukan temuan.** Fixture (cell/config/shard) tidak sesuai ekspektasi kode — jangan disimpulkan apa-apa dari S2/S3 sebelum kontrol ini hijau. |
| Test gagal di `MasterchainStateQ::fetch` (fixture tidak kebangun) | **Fixture gagal, bukan verdict.** Kirim log lengkapnya; kemungkinan besar satu asumsi di bawah ini yang salah. |
| Compile error di test baru | **Paket yang salah, bukan temuan.** Kirim pesan error lengkapnya. |

## Yang TIDAK diuji paket ini

- **Gate v17** (`validate-query.cpp:1112`): `compare_global_balance()` tidak
  menerima argumen versi — gate itu hidup di luar fungsi. Test ini membuktikan
  mekanismenya, bukan bahwa gate-nya aktif di chain (aktivasi v17 sudah dipin
  terpisah di `falsifier-closure/activation-pinning.md`: MC 103190381).
- **Reproduksi end-to-end di validator jalan.** Test ini memanggil fungsi
  compare langsung — itu bukti terkuat kedua setelah repro validator penuh,
  dan cukup untuk menaikkan self-check dari REJECTED ke PASS.
- **Skenario S5 (double split) dan S6 (split+merge satu interval).** S5
  kontrol (formula cocok) dan S6 finding kedua (asimetris) — keduanya
  tercakup logikanya oleh S2/S3/S4; tidak ditulis supaya paket tetap kecil.
  Kalau S2/S3 terkonfirmasi, S6 mengikuti dari formula yang sama.

## Asumsi yang belum terverifikasi (tanpa build, tidak bisa dipastikan)

Semua lokasi di file `test-global-balance-calculator.cpp` kecuali disebut lain.

1. **`make_test_shard_accounts` dengan shard masterchain** — helper lama
   dipanggil dengan `ShardIdFull{masterchainId, shardIdAll}`.
   **TERVERIFIKASI dari source (bukan lagi asumsi):** `shard_contains` /
   `extract_addr_prefix` (`ton/ton-shard.h:42-98`) murni operasi bit —
   untuk shard `{−1, 0x8000…}` dan address top-64 `0x8000…` hasilnya
   `true`; tidak ada deref null di jalur ini. Akun MC juga tervalidasi
   oleh `t_Account.validate_ref` di helper (CHECK lolos di run #1 —
   tidak ada pesan `CHECK failed`).
   Lokasi: `make_test_mc_state_root`, baris pemanggilan
   `make_test_shard_accounts(mc_id.shard_full())`.
2. **Pesan error `td::Status::to_string()` berisi pesan lengkap + `RefInt256`
   di-print desimal** — assertion penentu (`GLOBAL BALANCE DISCREPANCY`)
   robust; assertion nilai diff (`-1000000000` / `-500000000`) bergantung
   pada format print desimal tanpa spasi. Kalau formatnya beda (mis. ada
   prefix kode error), assertion diff bisa gagal padahal discrepancy-nya
   benar — kirim outputnya, jangan anggap temuan gugur.
   Lokasi: `expect_discrepancy`.
3. **`Config::get_zerostate_id()` dengan workchain list kosong tidak error**
   — **TERVERIFIKASI dari source:** `mc_reinit` (`validator/impl/shard.cpp:348`)
   cuma menyimpan hasilnya (`zerostate_id_ = config_->get_zerostate_id()`),
   dan `ConfigInfo::unpack` (`mc-config.cpp:128`) mengisi field itu dari
   cabang seqno-0 (hash nol, workchain masterchain) tanpa menyentuh
   workchain list — param 12 yang absen ditangani eksplisit di
   `unpack_workchain_list_ext` (`mc-config.cpp:500`, return empty set).
   Lokasi: `make_test_mc_state` (via `MasterchainStateQ::fetch`).
4. **Binary test ada di `build/test/validator/test-global-balance-calculator`**
   dan format output doctest standar** — kalau path-nya beda, runbook sudah
   berisi perintah `find` untuk melacaknya.
5. **Build pertama di PC user** — target ini menarik lib validator
   (~150 TU) + RocksDB/Abseil/OpenSSL dari submodule (di-build dari source).
   Perkiraan: **30–60 menit di 8 core/16 GB RAM; 1–2 jam di 4 core**.
   Ini perkiraan dari ukuran dependency closure, bukan tebakan kosong —
   dan akan dikoreksi setelah user melaporkan `nproc` + RAM aslinya.
   Link step adalah puncak pemakaian RAM (makanya runbook mewajibkan
   `.wslconfig` memory=16GB).

## Asumsi yang SUDAH terverifikasi dari source (bukan tebakan)

- `compare_global_balance` tidak memakai field `funds_created`/`fees_collected`
  dari deskriptor shard — kreasi dihitung ulang dari delta seqno
  (`global-balance-calculator.cpp:487-536`). Jadi fixture tidak perlu
  mengisi field itu.
- `McShardHash::pack` + ctor 17-arg publik (`crypto/block/mc-config.h:289`
  dan ctor di ~baris 180); `MasterchainStateQ::fetch` public static
  (`validator/impl/shard.hpp:170`); `vm::Dictionary(int)` ctor
  (`crypto/vm/dict.h:463`); `Dictionary::set(BitArray key, ...)`
  (`crypto/vm/dict.h:507`); `get_config_param` pakai `lookup_ref(BitArray<32>)`.
- Layout cell: `McStateExtra#cc26` (tag 16-bit), `ShardHashes` HashmapE
  (1 bit + ref), `ConfigParams` (256-bit addr + ref), `BlockCreateFees#6b`
  (tag 8-bit + 2 Grams), `validators_ext#12`, `ValidatorDescr#53`,
  `ShardDescr` via `McShardHash::pack` (tag `shard_descr_new#a` = 10, 4-bit),
  leaf BinTree (1 bit 0 + record), fork (1 bit 1 + 2 ref, 0 bit data).
- `ShardStateUnsplit.validate_ref` di akhir `make_test_mc_state_root`
  memvalidasi seluruh root (termasuk `McStateExtra`) — fixture yang salah
  packing gagal LOUD di konstruksi, bukan diam-diam lolos.
