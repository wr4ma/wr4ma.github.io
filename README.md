# wr4ma.github.io

Situs statis di GitHub Pages: halaman profil pribadi dan kumpulan alat bantu untuk PIC courier **MyRobin** (Jabar 1 & Parahyangan Kulon).

Tanpa build, tanpa framework. Semua halaman berupa satu file HTML yang berisi HTML, CSS, dan JavaScript sendiri.

**Situs:** https://wr4ma.github.io

## Struktur repo

```
.
├── index.html                    Halaman profil "Wil's"
├── profile.jpeg                  Foto profil
├── myrobin/
│   ├── index.html                Dashboard utama MyRobin
│   ├── forms.html                Formulir pendaftaran kurir SPX
│   ├── panel-pic.html            Panel pendaftar untuk PIC (perlu kode akses)
│   └── mass-wa.html              Pembuat link WhatsApp massal
└── .github/workflows/
    └── apply-update.yml          Otomatis menerapkan update.zip
```

## Fitur

### Dashboard MyRobin (`myrobin/index.html`)

| Menu | Isi | Sumber data |
| --- | --- | --- |
| Lokasi Semua Hub | Alamat, link Maps, PIC, filter, pencarian, jarak dari lokasi pengguna | Google Sheets (live) |
| Lm Needs & Backlog Status | Kebutuhan kurir per hub, status hypercare, pipeline ready | Google Sheets (live) |
| Jadwal Roster Onsite | Belum ada data | - |
| KPI PIC Performance | Target, aktual, persentase, rank, dan keterangan | Google Sheets (live) |
| Tools | Pintasan ke Mass WA, Pendaftaran Kurir, dan Panel PIC | - |

Tersedia juga mode terang/gelap dan mode hemat.

**Lokasi saya & hub terdekat**
- Lokasi diminta otomatis saat halaman dibuka, di menu mana pun. Tombol **Matikan** menghentikan permintaan otomatis di kunjungan berikutnya.
- Hub diurutkan dari yang terdekat, lengkap dengan jarak dan tombol rute. Kartu lokasi menampilkan peta, alamat, koordinat, akurasi, dan jumlah hub dalam radius 10 km. Kartu bisa disembunyikan.
- Koordinat hub dicari berurutan: kolom `Latitude`/`Longitude` (atau `Koordinat`) di sheet, lalu koordinat di link Maps, lalu perkiraan lokasi kecamatan lewat OpenStreetMap Nominatim (diberi label "perkiraan").

### Formulir pendaftaran (`myrobin/forms.html`)

Formulir pendaftaran kurir MyRobin x SPX Express, mengirim data ke Google Apps Script. Gambar bukti pendaftaran dibuat dengan html2canvas, yang dimuat saat dibutuhkan.

### Panel PIC (`myrobin/panel-pic.html`)

Panel bagi PIC untuk melihat pendaftar di areanya. Membaca data dari Google Apps Script yang sama dengan formulir dan memerlukan kode akses PIC.

### WhatsApp Mass Send (`myrobin/mass-wa.html`)

Membuat link WhatsApp untuk banyak nomor sekaligus dengan satu pesan. Tidak mengambil data dari mana pun.

## Data live dari Google Sheets

Dashboard membaca CSV dari Google Sheets yang dipublikasikan (`File > Bagikan > Publikasikan ke web > CSV`). Data dimuat saat halaman dibuka, lalu diperbarui tiap 5 menit dan saat pengguna kembali ke tab. Kartu tidak digambar ulang kalau isi sheet tidak berubah. Kalau sheet gagal dimuat dalam 20 detik, data terakhir tetap tampil dan indikator berubah menjadi pesan gagal.

| Tab (gid) | Dipakai untuk | Kolom |
| --- | --- | --- |
| `0` | Lokasi Semua Hub | `Courier PIC`, `Hub`, `District`, `Alamat Hub`, `Google Maps`, `PIC BPOM` (opsional: `Latitude`, `Longitude` atau `Koordinat`) |
| `899028723` | Lm Needs & Backlog | Hub, PIC BPOM, kota, PIC BPO, status backlog, gap per tipe kurir, total, pipeline ready |
| `248735701` | KPI PIC Performance | Tanggal, bagian/tim, PIC, hub cover, target, aktual, persentase, rank, perform, serta baris `Keterangan` yang diawali `*` |

Aturan penting:
- Urutan dan nama kolom di sheet jangan diubah tanpa menyesuaikan kodenya.
- Bobot di kartu KPI dibaca dari kalimat `Persentase Bobot adalah 20% Supply, 30% Rilis NHE, 50% Joiner`.
- Baris terakhir tiap bagian KPI tanpa rank dianggap subtotal dan tidak ditampilkan.
- Hub di data bawaan (Sukabumi/Cianjur) yang belum ada di sheet tetap ditampilkan dari data lama. Kalau ditambahkan ke sheet, otomatis pindah menjadi live.
- Pengelompokan **Sub District** masih daftar manual di kode (`SDM` di `myrobin/index.html`).

### Pengaturan di kode (`myrobin/index.html`)

| Variabel | Fungsi | Bawaan |
| --- | --- | --- |
| `KPI_SECTIONS` | Bagian sheet KPI yang ditampilkan | `['Jabar 1 & Parahyangan Kulon']` |
| `NEAR_KM` | Radius hitungan hub terdekat | `10` |
| `LIVE_MS` | Jeda penyegaran data (ms) | `300000` |

## Cara memperbarui situs

Workflow `apply-update.yml` menerapkan perubahan lewat satu file zip:

1. Siapkan `update.zip` berisi file `.html` yang berubah, dengan struktur folder yang sama seperti repo (misalnya `myrobin/index.html`).
2. Upload (commit) `update.zip` ke root repo di branch `main`.
3. Workflow mengekstrak semua file `.html`, menimpa file lama, menghapus `update.zip`, lalu commit otomatis. GitHub Pages memperbarui situs setelah commit itu.

Batasan workflow:
- Hanya file `.html` yang diproses. Gambar, `README.md`, dan file lain harus di-upload manual.
- Tiap file harus mengandung tag `<html`, tidak kosong, dan maksimal 3 MB.
- Workflow juga bisa dijalankan manual lewat tab **Actions** (`workflow_dispatch`).

## Data yang disimpan di browser

| Kunci | Isi |
| --- | --- |
| `hm-theme` | Pilihan tema terang/gelap |
| `hm-lite` | Mode hemat |
| `hm-memin` | Kartu lokasi disembunyikan atau tidak |
| `hm-meoff` | Lokasi otomatis dimatikan pengguna |
| `hm-geo` | Cache perkiraan lokasi hub |

Lokasi pengguna hanya diproses di browser. Untuk menampilkan nama tempat dan mencari perkiraan lokasi hub, koordinat dikirim ke OpenStreetMap (Nominatim).

## Catatan

- Layanan eksternal yang dipakai: Google Fonts (halaman utama), Google Sheets (data live), Google Apps Script (formulir dan panel PIC), OpenStreetMap (peta dan geocoding), cdnjs (html2canvas), dan WhatsApp (`wa.me`).
- Halaman memakai `localStorage`. Kalau diakses lewat pratinjau yang memblokir penyimpanan, preferensi tidak tersimpan tapi halaman tetap berfungsi.

---

Dibuat oleh **wr4ma**.
