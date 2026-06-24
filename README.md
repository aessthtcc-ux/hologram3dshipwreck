# WebAR Shipwreck Viewer

Scan marker → kamera terbuka → model 3D muncul melayang di atas marker di dunia nyata.
Tidak perlu install app — jalan langsung di browser HP.

---

## Struktur folder

```
webar-shipwreck/
├── index.html              ← AR app utama
├── compile-markers.html    ← tool compile marker (buka di browser)
├── public/
│   ├── markers/            ← gambar marker siap cetak (PNG)
│   │   ├── marker-1-shipwreck.png
│   │   ├── marker-2-submarine.png
│   │   └── marker-3-treasure.png
│   ├── mind/
│   │   └── targets.mind    ← hasil compile (kamu generate sendiri, lihat Step 2)
│   └── models/
│       ├── shipwreck.glb   ← ganti dengan file GLB kamu
│       ├── submarine.glb
│       └── treasure.glb
```

---

## Setup step by step

### Step 1 — Masukkan file GLB kamu

Salin file GLB ke `public/models/`:

```
shipwreck.glb   → muncul saat kamera scan marker-1-shipwreck
submarine.glb   → muncul saat kamera scan marker-2-submarine
treasure.glb    → muncul saat kamera scan marker-3-treasure
```

Kalau nama file GLB kamu berbeda, edit bagian `<a-assets>` di `index.html`:

```html
<a-asset-item id="model-shipwreck" src="/models/NAMAFILE_KAMU.glb"></a-asset-item>
```

### Step 2 — Compile markers jadi targets.mind

File `.mind` adalah marker yang sudah dikompilasi agar bisa ditrack kamera secara real-time.

1. Buka `compile-markers.html` di browser (bisa langsung double-click filenya, TIDAK perlu server)
2. Upload 3 marker image **dengan urutan yang benar**:
   - File pertama  → targetIndex 0 → marker-1-shipwreck.png
   - File kedua    → targetIndex 1 → marker-2-submarine.png
   - File ketiga   → targetIndex 2 → marker-3-treasure.png
3. Klik **Compile targets.mind**
4. Download hasilnya, taruh di `public/mind/targets.mind`

### Step 3 — Cetak marker

Cetak 3 file PNG dari folder `public/markers/`:
- Ukuran minimal 8x8 cm (makin besar makin mudah dideteksi kamera)
- Cetak di kertas putih biasa, tidak perlu kertas khusus
- Atau tampilkan di tablet/monitor kalau tidak mau cetak

### Step 4 — Deploy ke Vercel (wajib HTTPS untuk akses kamera)

```bash
npm i -g vercel
cd webar-shipwreck
vercel --prod
```

Atau push ke GitHub → import di vercel.com → deploy otomatis.

### Step 5 — Test di HP

1. Buka URL Vercel di browser HP (Chrome Android / Safari iOS)
2. Tap **"Open Camera"**
3. Izinkan akses kamera
4. Arahkan kamera ke salah satu marker yang sudah dicetak
5. Model 3D muncul berputar di atas marker

---

## Menambah model baru

**1. Siapkan gambar marker baru** (PNG kontras tinggi, detail banyak)

**2. Tambah entry di `index.html`:**

```html
<!-- Di <a-assets> -->
<a-asset-item id="model-kapal-baru" src="/models/kapal-baru.glb"></a-asset-item>

<!-- Di bawah marker terakhir, targetIndex = nomor urut berikutnya -->
<a-entity mindar-image-target="targetIndex: 3" id="target-3">
  <a-entity
    gltf-model="#model-kapal-baru"
    position="0 0.05 0"
    rotation="-90 0 0"
    scale="0.08 0.08 0.08"
    animation="property: rotation; from: -90 0 0; to: -90 360 0;
               loop: true; dur: 8000; easing: linear"
  ></a-entity>
</a-entity>
```

**3. Update NUM_TARGETS** di bagian script `index.html`:
```js
const NUM_TARGETS = 4; // ganti sesuai jumlah marker
```

**4. Re-compile targets.mind** dengan semua marker (termasuk yang baru), urutan yang sama + marker baru di akhir.

**5. Deploy ulang.**

---

## Tuning posisi / skala model

Kalau model muncul tapi terlalu besar, terbalik, atau melayang terlalu tinggi — edit di `index.html`:

```html
position="0 0.05 0"    <!-- x y z offset dari pusat marker, dalam meter -->
rotation="-90 0 0"     <!-- koreksi orientasi — coba 0 0 0 kalau tampak aneh -->
scale="0.08 0.08 0.08" <!-- perkecil angka kalau model terlalu besar -->
```

Tidak perlu re-compile .mind saat ubah ini — langsung refresh browser.

---

## Troubleshooting

| Masalah | Solusi |
|---|---|
| Kamera tidak bisa diakses | Harus HTTPS — pastikan buka dari URL Vercel, bukan localhost di HP lain |
| Model tidak muncul saat scan | Pastikan `targets.mind` sudah di-compile dan ada di `public/mind/` |
| Marker tidak terdeteksi | Cetak lebih besar, pastikan cahaya cukup, jangan terlalu dekat |
| Model terlalu besar/kecil | Ubah nilai `scale` di index.html |
| Model terbalik/miring | Coba `rotation="0 0 0"` atau `-90 0 0` atau `0 0 180` |
| File GLB tidak muat | Pastikan nama file di `<a-assets>` sama persis dengan file di `public/models/` |
