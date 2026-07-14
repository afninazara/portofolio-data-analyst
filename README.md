# 📊 Analisis Performa Penjualan \& Kesehatan Bisnis — PT Ritel Nusantara



Analisis end-to-end data ritel (SQL → DAX → dashboard Power BI 3 halaman) untuk membedah performa penjualan sekaligus kesehatan bisnis. 

**Punchline-nya:** omzet yang *hilang* akibat pesanan batal (Rp 717,49 jt) ternyata **lebih besar** dari omzet yang berhasil dibukukan (Rp 694,49 jt) — artinya menekan pembatalan adalah peluang pertumbuhan omzet terbesar yang dimiliki perusahaan.







## 1\. Konteks Bisnis \& Rumusan Masalah



PT Ritel Nusantara adalah perusahaan ritel dengan 1.000 pesanan dari 200 pelanggan di 8 kota, menjual 80 produk yang terbagi dalam 6 kategori. 

Manajemen ingin tahu dua hal sekaligus: **seberapa bagus performa penjualannya (Pilar A)** dan **seberapa sehat kondisi bisnisnya (Pilar C)**.

Begitu data dibedah, satu masalah langsung menonjol dan menjadi **benang merah** seluruh proyek ini:

> Pembatalan pesanan adalah masalah prioritas #1.
> Dari 1.000 pesanan, 34,3% dibatalkan. Nilai omzet yang lenyap karena pembatalan mencapai Rp 717,49 jt bahkan sedikit melampaui omzet yang benar-benar masuk (Rp 694,49 jt). Perusahaan praktis "kehilangan bisnis sebesar bisnis yang berhasil dijalankannya."





Rumusan masalahnya: **di mana sumber pembatalan terbesar, dan kategori/produk mana yang paling perlu diselamatkan lebih dulu?**

## 

## 

## 2\. Dataset \& Tools





|**Komponen**|**Detail**|
|-|-|
|Database|PostgreSQL 18 (localhost:5432), db `ritel\_nusantara`, schema `public`, mode read-only|
|Query tool|DBeaver CE 26.1.1|
|Visualisasi|Power BI Desktop (Microsoft Store), mode Import|
|Cakupan data|1.000 pesanan, rentang Jan 2023 – Jun 2025|





**Struktur 5 tabel:**

|**Tabel**|**Baris**|**Kolom kunci**|
|-|-|-|
|`kategori`|6|id, nama\_kategori|
|`produk`|80|id, nama\_produk, kategori\_id, harga, stok|
|`pelanggan`|200|id, nama, kota, tanggal\_daftar|
|`pesanan`|1.000|id, pelanggan\_id, tanggal, status|
|`pesanan\_item`|3.000|id, pesanan\_id, produk\_id, qty, harga\_satuan|





Relasi 1: `pelanggan → pesanan`, `pesanan → pesanan\_item`, `produk → pesanan\_item`, `kategori → produk`.









## 3\. Metodologi / Alur Kerja





Alur kerja proyek mengikuti pipeline analitik standar: **eksplorasi \& agregasi di SQL → modeling \& kalkulasi di DAX → visualisasi \& storytelling di dashboard.**






SQL (DBeaver)                 Power BI (DAX + Model)            Dashboard
─────────────                 ──────────────────────            ──────────
Eksplorasi data       →       5 Measure DAX + 1 calc col   →    12 visual
9 query tervalidasi           relasi 5 tabel                     3 halaman
(Pilar A \& Pilar C)           validasi silang ke DBeaver         cerita bisnis






Measure DAX yang dipakai	: Total Omzet, AOV, Omzet Hilang, % Batal, Jumlah Pesanan, Jumlah Batal, Total Terjual.
Calculated column		: `Bulan` = `FORMAT(tanggal, "YYYY-MM")` untuk tren bulanan.



Setiap angka di dashboard divalidasi silang terhadap hasil query DBeaver agar tidak ada selisih antara sumber data dan visual.









## 4\. Temuan Utama







### KPI Utama



|**KPI**|**Nilai**|**Catatan**|
|-|-|-|
|Total Omzet (selesai)|Rp 694.494.477|hanya status `selesai`|
|AOV (Average Order Value)|Rp 2.054.717|694,49 jt ÷ 338 pesanan selesai|
|Omzet Hilang (pesanan batal)|Rp 717.489.486|**lebih besar dari omzet berhasil**|
|% Batal|34,3%|343 dari 1.000 pesanan|

### 

### 

### Komposisi Status 1.000 Pesanan

|**Status**|**Jumlah**|**Persentase**|
|-|-|-|
|Selesai|338|33,8%|
|Batal|343|34,3%|
|Pending|319|31,9%|





Ketiga status hampir seimbang. Pembatalan bukan anomali kecil, melainkan sepertiga dari seluruh aktivitas bisnis.

### 





### Performa Penjualan



|**Analisis**|**Temuan**|
|-|-|
|Omzet per Kategori|Olahraga tertinggi dengan nilai Rp 154.324.319 (dari 6 kategori). <br />Rumah Tangga terendah.<br />Distribusi antar kategori relatif merata.|
|Top 10 Produk|Produk 79 teratas bernilai Rp 22.423.680. <br />Top-10 relatif merata, tidak ada satu produk yang mendominasi ekstrem.|
|Tren Omzet Bulanan|Fluktuatif (Jan 2023 – Jun 2025), ada puncak di awal 2025 lalu turun tajam di Jun 2025.|
|Omzet per Kota|Jakarta tertinggi dengan nilai Rp 121.666.366 (dari 8 kota). Sebaran antar kota merata.|

### 

### 

### Kesehatan Bisnis

|**Analisis**|**Temuan**|
|-|-|
|Komposisi Status|Sama seperti tabel status di atas (selesai/batal/pending ≈ sepertiga masing-masing).|
|Batal per Produk (Top 10)|Produk 55 teratas \~21 kali batal, dengan banyak produk seri (*ties*). <br />Ini indikasi kuat pembatalan bersifat sistemik, bukan disebabkan 1–2 produk bermasalah melainkan pola yang menyebar.|
|Watchlist Restock (stok ≤ 10)|6 produk kritis, dipimpin Produk 18 (stok 0, terjual 42). Barang laku tapi stoknya menipis/habis.|
|Omzet Hilang per Kategori|Olahraga tertinggi Rp 150.026.790 dari total \~717 jt omzet hilang.|

### 

### 

> Olahraga adalah kategori dengan omzet TERTINGGI (Rp 154 jt) tapi sekaligus penyumbang omzet hilang TERTINGGI (Rp 150 jt)
> Kategori paling bernilai justru yang paling banyak kehilangan potensi. Untuk setiap rupiah omzet Olahraga yang berhasil masuk, hampir satu rupiah lagi lenyap di pintu pembatalan. Inilah titik ungkit terbesar: memperbaiki pembatalan di kategori Olahraga saja berpotensi menggandakan kontribusinya.









## Rekomendasi Bisnis



Rekomendasi berikut diturunkan langsung dari temuan, bukan asumsi umum:



1. **Jadikan penekanan pembatalan sebagai prioritas #1 perusahaan.** 
Dengan omzet hilang (Rp 717 jt) yang menyamai omzet berhasil (Rp 694 jt), tidak ada inisiatif pertumbuhan lain yang bernilai sebesar ini. Bahkan menekan pembatalan 10% saja setara tambahan omzet ± Rp 70 jt.

2. **Audit kategori Olahraga lebih dulu.**
Karena Olahraga memimpin di dua sisi (omzet tertinggi *dan* kehilangan tertinggi), inilah kandidat pilot perbaikan dengan potensi dampak terbesar per unit usaha. Cari akar penyebab pembatalan spesifik di kategori ini (stok, harga, pengiriman, atau pola pelanggan).

3. **Perlakukan pembatalan sebagai isu sistemik, bukan per-produk.** 
Temuan C2 (banyak produk batal dengan frekuensi mirip) menunjukkan akar masalah kemungkinan ada di proses (mis. alur checkout, ketersediaan stok, konfirmasi pesanan), bukan pada satu produk cacat. Investigasi difokuskan ke proses, bukan sekadar mem-blacklist produk.

4. **Bangun sistem restock untuk produk watchlist.** 
Enam produk di C3 (dipimpin Produk 18 yang stoknya sudah 0 padahal terjual 42) adalah barang laku yang berisiko kehilangan penjualan karena kehabisan stok. Restock cepat = menutup kebocoran omzet dari sisi ketersediaan.

5. **Investigasi penurunan tajam Jun 2025 (A3).**

&#x20;  Tren omzet sempat memuncak di awal 2025 lalu jatuh. Perlu dipastikan apakah ini efek musiman, data belum lengkap, atau sinyal masalah nyata sebelum dijadikan dasar keputusan.

## 

## 

## Struktur Dashboard



Dashboard terdiri dari 3 halaman, dirancang mengalir dari gambaran besar → performa → kesehatan:



|**Halaman**|**Fokus**|**Isi**|
|-|-|-|
|**1. Ringkasan**|Gambaran eksekutif|KPI card (Total Omzet, AOV, Omzet Hilang, % Batal) + donut komposisi status|
|**2. Performa** |Seberapa bagus penjualannya|Omzet per Kategori <br />Top 10 Produk · A3 Tren Omzet Bulanan · A4 Omzet per Kota|
|**3. Kesehatan** |Seberapa sehat bisnisnya|C1 Komposisi Status · C2 Batal per Produk · C3 Watchlist Restock · C4 Omzet Hilang per Kategori|





**Bahasa warna dashboard:** batal/masalah = merah, selesai = hijau, pending = abu-abu, performa = biru. Tabel watchlist C3 memakai *conditional formatting*: stok 0 = merah, stok 1–5 = oranye.

## 

Sebagai bagian dari praktik analisis yang jujur, dua hal dicatat terbuka:



* 17 dari 338 pesanan berstatus `selesai` tercatat tanpa item (nilai Rp 0). 



&#x20;  Dalam perhitungan AOV, ke-17 pesanan ini tetap dimasukkan ke denominator (338), sehingga AOV final = Rp 694.494.477 ÷ 338 = Rp 2.054.717. Keputusan ini diambil secara sadar agar AOV mencerminkan *seluruh* pesanan selesai yang tercatat

&#x20;  sistem, bukan hanya yang "sempurna".



* Definisi omzet dibatasi hanya pada status `selesai`. Status `batal` dan `pending` sengaja tidak dihitung sebagai omzet, melainkan dipakai untuk mengukur besarnya masalah (omzet hilang \& potensi tertunda).

## 

## 

## Isi Folder / Cara Menggunakan





Folder portofolio berisi:



|**File**|**Keterangan**|
|-|-|
|`PT\_Ritel\_Nusantara.pbix`|Dashboard Power BI final (buka dengan Power BI Desktop)|
|`Rangkuman Query 18 dan Power BI.html`|Arsip resmi seluruh query SQL + catatan Power BI|
|`README.md`|Dokumen ini|





**Cara menggunakan:**

1. Buka `PT\_Ritel\_Nusantara.pbix` di Power BI Desktop untuk menjelajahi dashboard interaktif 3 halaman.
2. Buka file HTML arsip untuk melihat seluruh query SQL yang menghasilkan tiap angka.
3. README ini menjadi ringkasan naratif proyek untuk pembaca non-teknis maupun teknis.

\---

## 

## 

## Skills yang Didemonstrasikan

|**Area**|**Skill konkret**|
|-|-|
|SQL|`JOIN` multi-tabel, agregasi (`SUM`, `COUNT`, `GROUP BY`), filter kondisional, eksplorasi data|
|Data Modeling|Menyusun relasi 5 tabel (1→\*) di Power BI, calculated column|
|DAX|7 measure bisnis (Total Omzet, AOV, Omzet Hilang, % Batal, dll)|
|Data Visualization|12 visual di 3 halaman, bahasa warna konsisten, conditional formatting|
|Data Storytelling|Membangun benang merah tunggal (pembatalan = masalah #1) dari banyak angka|
|Data Quality Awareness|Mendokumentasikan anomali (17 pesanan Rp 0) \& menjelaskan keputusan metodologis secara transparan|



