# Dataset PT Lokal Komoditi Indonesia

Bahan proyek untuk bot pencatatan transaksi (AI Finance Assistant), Kelompok G3,
Rework Academy. Repo ini berisi data keuangan riil perusahaan.

## Peringatan

Data di dalam repo ini adalah data keuangan riil PT Lokal Komoditi Indonesia:
nama pihak, nomor rekening, nominal transaksi, dan bukti transfer.

**Repo ini sekarang PUBLIK.** Itu keputusan sadar agar seluruh anggota kelompok
bisa mengakses dataset tanpa perlu ditambahkan satu per satu, dan agar evaluasi
bisa dijalankan tanpa token GitHub. Konsekuensinya data ini bisa dibaca siapa
saja yang tahu alamatnya.

Kalau perusahaan meminta data ini ditarik kembali, ubah repo menjadi privat.
Kode evaluasi sudah mendukung repo privat: isi `GITHUB_TOKEN` di `.env`, dan
unduhan otomatis memakai jalur API bertoken.

## Isi

| Folder | Jumlah | Isi |
|---|---|---|
| `SOP Keuangan/` | 3 berkas | Draf SOP uang keluar, rekonsiliasi bank, dan diagram alir |
| `Cash Report dan Mutasi Rekening/` | 5 berkas | 3 Cash Report (BNI, BRI, Operasional) dan 2 mutasi rekening PDF |
| `Bukti Transaksi Januari 2026/` | 118 berkas | Foto bukti transaksi Januari 2026 |
| `pairs.csv` | 1 berkas | daftar 65 pasangan gambar ke baris Cash Report, dipakai bersama semua kriteria |

Total 127 berkas, sekitar 10,9 MB.

## Kegunaan `pairs.csv`

`pairs.csv` memetakan 65 foto bukti transaksi ke baris Cash Report yang
bersesuaian, lengkap dengan kategori yang ditetapkan Admin.

Berkas ini **dipakai bersama oleh semua kriteria keberhasilan**, bukan hanya
SC-01. Kolomnya memasok acuan untuk beberapa kriteria sekaligus:

| Kolom | Dipakai oleh |
|---|---|
| `kategori_acuan` | SC-01 (akurasi kategori) dan SC-02 (kecocokan kategori) |
| `tanggal` | SC-02 (kecocokan tanggal) |
| `nominal_acuan` | SC-02 (kecocokan nominal) |
| `bank` | SC-02 (kecocokan kantong) |
| `keterangan_gambar` | SC-02 (kecocokan keterangan) |

Karena itu namanya tidak lagi memakai awalan `sc01`. Nama lama menyiratkan
berkas ini hanya dipakai satu kriteria, padahal isinya melayani beberapa.

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

Ringkasnya, setelah repo ini di-clone di samping repo kode:

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

## Catatan tentang penamaan kategori

Nilai pada kolom `kategori_acuan` memakai nama resmi dari `cash_report/categories.py`
di repo kode. Kolom KATEGORI pada berkas Excel sumber sebagian memakai penamaan
lain yang tidak ada di daftar resmi, misalnya `Beban Operasional - Transportasi`
alih-alih `Biaya-Transportasi`. Yang dipakai sebagai acuan adalah penamaan resmi,
supaya perbandingan dengan jawaban model setara.
