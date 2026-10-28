# Laporan Praktikum Grafika Komputer — Pertemuan 6

## 1. Identitas Mahasiswa
* **Nama:** Lucky Himawan Prasetya
* **NRP:** 5025241147
* **Kelas:** Grafika Komputer C

---

## 2. Deskripsi Scene 3D
Aplikasi ini merupakan *Mini 3D Scene* yang dibangun menggunakan **Three.js** (v0.160.0) berbasis WebGL. Aplikasi dirancang secara *single-file web client* yang responsif, mengimplementasikan abstraksi grafika 3D seperti `Scene`, `PerspectiveCamera`, `OrthographicCamera`, `WebGLRenderer` dengan bayangan (*shadow mapping*), perhitungans *delta time* independen terhadap *frame rate*, serta overlay HUD (*Heads-Up Display*) teknis.

---

## 3. Rincian Implementasi Tugas Utama & Challenge

### 3.1 Tugas Utama (Core Deliverables)
1. **Project & Abstraksi Scene:**
   - Membangun `THREE.Scene` dengan background gelap (`#0d0e12`) dan *exponential fog* (`THREE.FogExp2`).
   - Menyiapkan `THREE.WebGLRenderer` dengan `antialias: true`, *pixel ratio cap* pada nilai `2`, serta tone mapping `ACESFilmicToneMapping`.
2. **Kamera & Proyeksi:**
   - Menggunakan `THREE.PerspectiveCamera` (FOV 50°, aspect ratio dinamis, near 0.1, far 100) dan mengarahkannya ke titik pusat menggunakan `lookAt(0, 0, 0)`.
3. **Objek Utama (Geometri + Material):**
   - **Ground:** `PlaneGeometry` (30x30) yang dirotasi horizontal ($-90^\circ$ pada sumbu X) dipadukan dengan `THREE.GridHelper`.
   - **Cube:** `BoxGeometry` dipadukan dengan `MeshLambertMaterial` (warna cyan `#0ea5e9`) yang merespons pencahayaan diffuse.
   - **Sphere:** `SphereGeometry` (32x32 segment) dipadukan dengan `MeshPhongMaterial` (warna indigo `#6366f1` dengan `shininess: 80`).
4. **Pencahayaan & Bayangan (Lighting & Shadow):**
   - **AmbientLight:** Intensitas 0.4 warna biru-putih lembut (`#dbeafe`).
   - **DirectionalLight:** Intensitas 1.8 sebagai pencahayaan utama (*key light*) dari koordinat `(6, 10, 5)`.
   - **Shadow Mapping:** Mengaktifkan `renderer.shadowMap.enabled = true` dengan tipe `PCFSoftShadowMap`. Objek kubus dan bola bertindak sebagai *caster* (`castShadow = true`), sedangkan ground sebagai *receiver* (`receiveShadow = true`).
5. **Animation Loop & Responsive Rendering:**
   - Rotasi kubus dan pergerakan membal (*bouncing*) bola dianimasikan menggunakan `THREE.Clock` (*delta time*) agar kecepatan pergerakan konstan di berbagai *refresh rate* layar.
   - Handler event `resize` memperbarui aspect ratio kamera, memanggil `updateProjectionMatrix()`, dan meresize `WebGLRenderer`.
6. **Technical HUD & World Labels:**
   - HUD di pojok kiri atas menampilkan koordinat kamera aktif, tipe kamera, serta jumlah objek mesh di scene.

---

### 3.2 Challenge yang Dikerjakan

#### Challenge A — Addition of Geometries (Geometri Tambahan)
Menambahkan tiga bentuk geometri baru pada baris depan ($Z = 3$):
- **CylinderGeometry:** Dipadukan dengan `MeshStandardMaterial` berwarna hijau emerald (`#10b981`).
- **ConeGeometry:** Dipadukan dengan `MeshStandardMaterial` berwarna amber (`#f59e0b`).
- **TorusGeometry:** Dipadukan dengan `MeshStandardMaterial` berwarna pink magenta (`#ec4899`).
- Seluruh objek challenge dianimasikan berputar dan menginduksi bayangan (*castShadow*).

#### Challenge B — Material Gallery (Galeri Perbandingan Material)
Menyusun 4 bola berjejer pada posisi $Z = -3$ untuk membandingkan karakteristik respon material terhadap cahaya secara visual:
1. `MeshBasicMaterial` (Merah `#ef4444`): Tidak terpengaruh cahaya/shading.
2. `MeshNormalMaterial`: Memetakan arah vektor normal permukaan ($N_x, N_y, N_z$) ke warna RGB.
3. `MeshLambertMaterial` (Biru `#3b82f6`): Shading *matte/diffuse* tanpa *specular highlight*.
4. `MeshPhongMaterial` (Ungu `#8b5cf6`): Shading *diffuse* dilengkapi *specular highlight* berkilau (`shininess: 100`).
*Dilengkapi dengan label HTML 3D dinamis yang terproyeksi sesuai posisi koordinat world objek.*

#### Challenge C — Orthographic Camera Switcher
- Menambahkan kamera kedua berjenis `THREE.OrthographicCamera` dengan perhitungan *frustum size* dinamis.
- Mengimplementasikan fungsi *switch camera* yang dapat dipicu melalui tombol **`P`** pada keyboard atau tombol **"Switch Camera"** pada UI HUD.
- Memastikan matriks proyeksi ortografis ikut diperbarui secara proporsional saat jendela browser di-resize.

---

## 4. Cara Menjalankan Project

### Prasyarat System
* Browser Modern (Chrome, Edge, Firefox, Safari) yang mendukung WebGL 2.0 dan ES Modules.
* Web server lokal sederhana (misalnya extension *Live Server* VS Code, `npx serve`, atau Vite).

### Langkah Menjalankan
1. Kloning / unduh direktori project ini.
2. Buka berkas `index.html` menggunakan Live Server atau jalankan melalui peramban web modern yang terhubung ke jaringan internet (untuk memuat pustaka Three.js via CDN ESM).
3. Untuk mengubah kamera dari **Perspective** ke **Orthographic**, tekan tombol **`P`** pada keyboard atau klik tombol di HUD.

---

## 5. Jawaban Pertanyaan Pemahaman (40 Soal)

1. **Apa hubungan Three.js dan WebGL?**  
   Three.js adalah *high-level abstraction library* di atas API WebGL. WebGL menangani instruksi grafika *low-level* langsung ke GPU, sedangkan Three.js menyederhanakan pengelolaan *scene graph*, *camera*, *geometry*, *shader*, dan *buffer*.
2. **Mengapa Three.js disebut high-level library?**  
   Karena menyembunyikan kompleksitas pembentukan program shader (GLSL), alokasi VBO/EBO manual, dan manipulasi matriks transformasi *low-level* di balik API JavaScript berorientasi objek.
3. **Apakah Three.js menghilangkan konsep buffer dan shader?**  
   Tidak. Three.js tetap membuat dan mengelola *buffer data* (VBO) serta program shader GLSL di balik layar saat proses kompilasi material.
4. **Apa fungsi Scene?**  
   Sebagai kontainer utama (*scene graph*) yang menampung seluruh objek 3D, lampu, kamera, dan elemen visual lainnya yang akan dirender.
5. **Apa komponen minimum agar Mesh dapat terlihat?**  
   Sebuah `BufferGeometry`, sebuah `Material`, dan penambahan objek `Mesh` tersebut ke dalam `Scene` yang dirender oleh `WebGLRenderer` menggunakan `Camera`.

---
