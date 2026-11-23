# Dataset PT Lokal Komoditi Indonesia

Bahan proyek untuk bot pencatatan transaksi (AI Finance Assistant), Kelompok G3,
Rework Academy. Repo ini **privat** karena berisi data keuangan riil perusahaan.

## Peringatan

Data di dalam repo ini adalah data keuangan riil PT Lokal Komoditi Indonesia.
Jangan dijadikan publik, jangan dibagikan ke luar kelompok, dan jangan
diunggah ke layanan lain tanpa izin.

## Isi

| Folder | Jumlah | Isi |
|---|---|---|
| `SOP Keuangan/` | 3 berkas | Draf SOP uang keluar, rekonsiliasi bank, dan diagram alir |
| `Cash Report dan Mutasi Rekening/` | 5 berkas | 3 Cash Report (BNI, BRI, Operasional) dan 2 mutasi rekening PDF |
| `Bukti Transaksi Januari 2026/` | 118 berkas | Foto bukti transaksi Januari 2026 |
| `sc01/` | 1 berkas | `sc01_pairs.csv` — daftar 65 pasangan gambar ke baris Cash Report |

Total 127 berkas, sekitar 10,9 MB.

## Kegunaan folder `sc01`

`sc01_pairs.csv` memetakan 65 foto bukti transaksi ke baris Cash Report yang
bersesuaian, lengkap dengan kategori yang ditetapkan Admin. Berkas ini dipakai
untuk mengukur SC-01, yaitu akurasi usulan kategori terhadap kategori yang
ditetapkan Admin.

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
| `baris_xlsx` | nomor baris pada berkas Excel Cash Report |

Dari 118 foto, hanya 65 yang bisa dipasangkan 1:1 ke baris Cash Report lewat
tanggal dan nominal. Sisanya tidak dipakai karena tidak ada baris yang cocok.

## Cara memakai bersama kode

Kode ada di repo terpisah: `Project-Rework-G3-AI/ai-finance-assistant`, branch
`test/sc01-kategori-accuracy`. Petunjuk menjalankan evaluasi ada di
`docs/sc01-cara-menjalankan.md` pada repo tersebut.

Ringkasnya, setelah repo ini di-clone di samping repo kode:

```bash
# dari dalam folder repo kode
python3 tests/sc01_harness.py --pairs  ../rework-ai-finance-assistant-dataset/sc01/sc01_pairs.csv \
                             --images "../rework-ai-finance-assistant-dataset/Bukti Transaksi Januari 2026"
```

Atau isi `.env`:

```ini
SC01_PAIRS=../rework-ai-finance-assistant-dataset/sc01/sc01_pairs.csv
SC01_IMAGES=../rework-ai-finance-assistant-dataset/Bukti Transaksi Januari 2026
```

Cara kedua lebih praktis karena tidak perlu menyebut dua jalur setiap kali
menjalankan perintah.

## Catatan tentang penamaan kategori

Nilai pada kolom `kategori_acuan` memakai nama resmi dari `cash_report/categories.py`
di repo kode. Kolom KATEGORI pada berkas Excel sumber sebagian memakai penamaan
lain yang tidak ada di daftar resmi, misalnya `Beban Operasional - Transportasi`
alih-alih `Biaya-Transportasi`. Yang dipakai sebagai acuan adalah penamaan resmi,
supaya perbandingan dengan jawaban model setara.
