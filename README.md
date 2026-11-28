# Dataset PT Lokal Komoditi Indonesia

Bahan proyek untuk bot pencatatan transaksi (AI Finance Assistant), Kelompok G3,
Rework Academy. Repo ini berisi data keuangan riil perusahaan.

## Peringatan

Data di dalam repo ini adalah data keuangan riil PT Lokal Komoditi Indonesia:
nama pihak, nomor rekening, nominal transaksi, dan bukti transfer.

**Repo ini publik.** Seluruh anggota kelompok bisa mengakses dataset tanpa
ditambahkan satu per satu, dan evaluasi bisa dijalankan tanpa token GitHub.
Data di dalamnya bisa dibaca siapa saja yang tahu alamat repo ini.

Kalau perusahaan meminta data ini ditarik kembali, ubah repo menjadi privat.
Kode evaluasi mendukung repo privat: isi `GITHUB_TOKEN` di `.env`, dan unduhan
otomatis memakai jalur API bertoken.

## Isi

| Folder | Jumlah | Isi |
|---|---|---|
| `SOP Keuangan/` | 3 berkas | Draf SOP uang keluar, rekonsiliasi bank, dan diagram alir |
| `Cash Report dan Mutasi Rekening/` | 5 berkas | 3 Cash Report (BNI, BRI, Operasional) dan 2 mutasi rekening PDF |
| `Bukti Transaksi Januari 2026/` | 118 berkas | Foto bukti transaksi Januari 2026 |
| `pairs.csv` | 1 berkas | daftar 65 pasangan gambar ke baris Cash Report, dipakai SC-01, SC-02, SC-03, SC-04, dan SC-06 |

Total 127 berkas data, 11,4 MB.

## Bahan untuk setiap kriteria

Tabel ini memetakan bahan mana yang dipakai tiap kriteria.

| Kriteria | Bahan dari repo ini | Wajib? |
|---|---|---|
| SC-01 akurasi kategori | `pairs.csv` + 65 foto yang dipasangkan | ya |
| SC-02 kelengkapan field | `pairs.csv` + 65 foto yang dipasangkan | ya |
| SC-03 waktu proses | 65 foto yang dipasangkan | ya |
| SC-04 biaya per gambar | 65 foto yang dipasangkan | ya |
| SC-05 penolakan duplikat | 1 foto, dikirim ulang | ya |
| SC-06 pemetaan kolom | 3 Cash Report di `Cash Report dan Mutasi Rekening/` | ya |
| SC-07 rekonsiliasi | 2 mutasi rekening PDF di `Cash Report dan Mutasi Rekening/` | ya |
| SC-08 tanya jawab | tidak ada | tidak, memakai ledger hasil SC-01 sampai SC-04 |

SC-07 mengunduh kedua PDF mutasi langsung dari repo ini. Nama berkas di sini
memakai spasi, sedangkan kode menyimpannya dengan garis bawah; pemetaannya ada
di `STATEMENT_FILES` pada `tests/sc_common.py`, jadi tidak perlu menyalin dan
mengganti nama secara manual.

## Kegunaan `pairs.csv`

`pairs.csv` memetakan 65 foto bukti transaksi ke baris Cash Report yang
bersesuaian, lengkap dengan kategori yang ditetapkan Admin. Berkas ini dipakai
SC-01, SC-02, SC-03, SC-04, dan SC-06. SC-05 dan SC-08 tidak memakainya.

| Kolom | Dipakai oleh |
|---|---|
| `file` | SC-01, SC-02, SC-03, SC-04, SC-06 (menentukan foto yang diproses) |
| `kategori_acuan` | SC-01 (jawaban benar untuk akurasi kategori) |
| `nominal_acuan` | SC-02 (acuan kelengkapan nominal) |
| `tanggal` | SC-02 (acuan kelengkapan tanggal) |
| `bank` | SC-02 (acuan kelengkapan kantong) |
| `keterangan_gambar` | SC-02 (acuan kelengkapan keterangan) |

Kolomnya:

| Kolom | Arti |
|---|---|
| `file` | nama berkas gambar bukti transaksi |
| `tanggal` | tanggal transaksi |
| `nominal_gambar` | nominal yang terbaca pada gambar |
| `nominal_acuan` | nominal pada baris Cash Report |
| `fee` | biaya transfer bila ada |
| `bank` | kantor bank yang menangani |
| `keterangan_gambar` | keterangan pada bukti |
| `kategori_acuan` | kategori yang ditetapkan Admin, dipakai sebagai jawaban benar |
| `kategori_acuan_asli` | penulisan kategori apa adanya dari Excel, sebelum diselaraskan |
| `sumber_acuan` | berkas dan tab asal baris acuan |
| `baris_xlsx` | nomor baris pada berkas Excel Cash Report |

Dari 118 foto, hanya 65 yang bisa dipasangkan 1:1 ke baris Cash Report lewat
tanggal dan nominal. Sisanya tidak dipakai karena tidak ada baris yang cocok.

## Cara memakai bersama kode

Kode ada di repo terpisah: `Project-Rework-G3-AI/ai-finance-assistant`, branch
`test/integration-testing`. Petunjuk menjalankan evaluasi ada di
`docs/sc-cara-menjalankan.md` pada repo tersebut.

Kode evaluasi bisa mengunduh bahan dari repo ini sendiri, jadi repo ini tidak
wajib di-clone lebih dulu:

```bash
# dari dalam folder repo kode
python3 tests/sc_runner.py --download      # foto dan pairs.csv
python3 tests/_sc07_run.py                 # kedua PDF mutasi, lalu ukur SC-07
```

Kalau ingin memakai salinan lokal, clone repo ini di samping repo kode:

```bash
git clone https://github.com/Project-Rework-G3-AI/ai-finance-assistant.git
git clone https://github.com/Project-Rework-G3-AI/rework-ai-finance-assistant-dataset.git

# dari dalam folder repo kode
python3 tests/sc_runner.py --pairs  ../rework-ai-finance-assistant-dataset/pairs.csv \
                           --images "../rework-ai-finance-assistant-dataset/Bukti Transaksi Januari 2026"
```

Atau isi `.env`:

```ini
SC_PAIRS=../rework-ai-finance-assistant-dataset/pairs.csv
SC_IMAGES=../rework-ai-finance-assistant-dataset/Bukti Transaksi Januari 2026
```

Cara kedua lebih praktis karena tidak perlu menyebut dua jalur setiap kali
menjalankan perintah.

Nama repo dan folder bisa diganti tanpa mengubah kode, lewat variabel berikut:

| Variabel | Bawaan |
|---|---|
| `GITHUB_DATASET_REPO` | `Project-Rework-G3-AI/rework-ai-finance-assistant-dataset` |
| `GITHUB_DATASET_DIR` | kosong, berarti berkas ada di akar repo |
| `GITHUB_DATASET_BRANCH` | `main` |

## Catatan tentang penamaan kategori

Nilai pada kolom `kategori_acuan` memakai nama resmi dari `cash_report/categories.py`
di repo kode. Kolom KATEGORI pada berkas Excel sumber sebagian memakai penamaan
lain yang tidak ada di daftar resmi, misalnya `Beban Operasional - Transportasi`
alih-alih `Biaya-Transportasi`. Yang dipakai sebagai acuan adalah penamaan resmi,
supaya perbandingan dengan jawaban model setara.
