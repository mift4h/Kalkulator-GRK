# Kalkulator GRK Indonesia: paket web & Android

Isi folder:

| Berkas | Fungsi |
|---|---|
| `index.html` | Aplikasi lengkap dalam satu berkas (mesin hitung + tampilan) |
| `manifest.webmanifest` | Identitas aplikasi untuk pemasangan di Android (PWA) |
| `sw.js` | Service worker supaya aplikasi bisa dipakai **offline** |
| `icons/` | Ikon 192 px & 512 px (biasa dan maskable) |

Data pengguna tersimpan di perangkat masing-masing (localStorage) dan tidak dikirim ke server mana pun. Gunakan tombol **Cadangkan (JSON)** untuk memindahkan data antar-perangkat.

---

## A. Pakai lewat web, lalu pasang di Android (paling cepat, tanpa APK)

1. Unggah isi folder ini ke hosting statis HTTPS gratis, misalnya:
   - **GitHub Pages**: buat repo, unggah berkas, lalu buka Settings → Pages → Deploy from branch.
   - **Netlify Drop** (app.netlify.com/drop): seret folder ke halaman.
   - **Cloudflare Pages**.
2. Buka alamatnya di **Chrome Android**.
3. Ketuk menu ⋮ → **Instal aplikasi** atau **Tambahkan ke layar utama**.
4. Aplikasi muncul sebagai ikon, terbuka layar penuh, dan tetap jalan tanpa internet.

Di komputer, Chrome/Edge juga menampilkan tombol instal di bilah alamat.

## B. Jadikan APK/AAB untuk Google Play (Trusted Web Activity)

Syarat: langkah A sudah selesai, sehingga aplikasi punya alamat HTTPS.

**Cara termudah: PWABuilder**
1. Buka https://www.pwabuilder.com, lalu masukkan alamat aplikasi.
2. Pilih **Package for stores → Android**.
3. Unduh paket berisi `.aab` (untuk Play Store), `.apk` (untuk uji coba), dan `assetlinks.json`.
4. Unggah `assetlinks.json` ke `/.well-known/assetlinks.json` di hosting Anda. Tanpa berkas ini, bilah alamat browser akan tetap tampil di aplikasi.

**Alternatif lewat baris perintah: Bubblewrap**
```bash
npm i -g @bubblewrap/cli
bubblewrap init --manifest https://DOMAIN-ANDA/manifest.webmanifest
bubblewrap build        # menghasilkan app-release-signed.apk & .aab
```

## C. APK yang sepenuhnya offline (berkas dibundel di dalam APK): Capacitor

```bash
mkdir kalkulator-grk-android && cd kalkulator-grk-android
npm init -y
npm install @capacitor/core @capacitor/cli @capacitor/android
mkdir www && cp -r /path/ke/folder-ini/* www/
npx cap init "Kalkulator GRK" id.grk.kalkulator --web-dir=www
npx cap add android
npx cap sync
npx cap open android    # build APK/AAB di Android Studio: Build → Generate Signed Bundle/APK
```
Catatan: di dalam APK Capacitor, ekspor berkas tetap bisa lewat tombol **Salin**. Bila ingin unduhan berkas langsung, tambahkan plugin `@capacitor/filesystem`.

---

## Memperbarui faktor emisi

Semua faktor ada di bagian `MESIN HITUNG GRK` di `index.html` (objek `FUELS`, `GRID`, `GWP`, `REFRIG`, `N2O_SET`). Bila ESDM menerbitkan faktor grid baru, ubah nilai `om`/`cm` pada `GRID`. Setelah itu naikkan versi `CACHE` di `sw.js` agar perangkat mengunduh versi terbaru.

## Dasar perhitungan (ringkas)

- Perpres 110/2025 (NEK & pengendalian emisi GRK) dan Permen LH/BPLH 7/2026 (inventarisasi mandiri pelaku usaha, pedoman IPCC)
- IPCC 2006 Guidelines + 2019 Refinement (Tier 1)
- GHG Protocol Corporate Standard / Scope 2 Guidance / Scope 3 Standard, ISO 14064-1:2018
- Faktor grid ESDM, data 2019 (Kepmen ESDM 163.K/HK.02/MEM.S/2021)
- Biodiesel B50 sejak 1 Juli 2026 (Kepmen ESDM 257.K/EK.01/MEM.E/2026)
- GWP AR5 (bawaan, konsisten dengan inventaris nasional) atau AR6
