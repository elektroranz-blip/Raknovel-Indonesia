# 📚 Rak Cerita

Web untuk berbagi karya tulis & suara: **Novel 📖, Cerita Bersambung 🧵, Cerpen ✍️, dan Voice Note (VN) 🎧**.
Punya fitur komentar, like, share, login Google, dan hanya penulis yang bisa mengedit karyanya sendiri.

Satu file aplikasi (`index.html`) + Supabase sebagai backend. Siap di-hosting di **GitHub Pages**.

---

## 1️⃣ Setup Supabase (±5 menit)

1. Buka [supabase.com/dashboard](https://supabase.com/dashboard) → pilih proyekmu (atau buat baru).
2. Buka **SQL Editor** → **New query** → salin **seluruh isi file `supabase-setup.sql`** → **Run**.
   - Ini membuat tabel `stories`, `story_chapters`, `story_comments`, `story_likes` + keamanan RLS + bucket storage `covers` & `vn-audio`.
3. **Authentication → Sign In / Providers → Google** → aktifkan, isi **Client ID** & **Client Secret** dari Google Cloud Console (lihat langkah bawah).
4. **Authentication → URL Configuration → Redirect URLs** → tambahkan:
   - `https://USERNAME.github.io/REPO/rak-cerita/index.html` (nanti disesuaikan setelah deploy)
   - `http://localhost:PORT/index.html` (untuk tes lokal)

### Membuat OAuth Google (sekali saja)
1. Buka [console.cloud.google.com](https://console.cloud.google.com) → buat project.
2. **APIs & Services → OAuth consent screen** → External → isi nama app → simpan.
3. **Credentials → Create Credentials → OAuth client ID → Web application**.
4. Isi:
   - **Authorized JavaScript origins**: `https://USERNAME.github.io` dan `http://localhost:PORT`
   - **Authorized redirect URIs**: `https://PROJECT-REF.supabase.co/auth/v1/callback` (ganti PROJECT-REF dengan punyamu, contoh: `ijfcmpykouafpdgvzsrf`)
5. Salin **Client ID** & **Client Secret** → paste ke Supabase (langkah 3 di atas).

---

## 2️⃣ Deploy ke GitHub Pages

1. Buat repository baru di GitHub, misal `rak-cerita`.
2. Upload **`index.html`** (dan README ini) ke repo.
3. **Settings → Pages** → Source: **Deploy from a branch** → Branch: `main`, folder `/ (root)` → Save.
4. Tunggu ±1 menit → URL situsmu: `https://USERNAME.github.io/REPO/index.html`.
5. **WAJIB:** tambahkan URL itu ke **Supabase → Authentication → URL Configuration → Redirect URLs** (langkah 1.4), supaya login Google berhasil.

> Aplikasi otomatis memakai URL halaman sebagai redirect login, jadi tidak perlu mengubah kode setelah deploy. Kalau domain berubah, cukup perbarui daftar Redirect URLs di Supabase.

---

## 3️⃣ Jalankan lokal (opsional)

```bash
# di folder rak-cerita
python -m http.server 8080
# buka http://localhost:8080
```
Tambahkan `http://localhost:8080/index.html` ke Redirect URLs Supabase untuk tes login di lokal.

---

## Fitur

| Fitur | Status |
|---|---|
| 4 jenis konten (novel / cerita bersambung / cerpen / VN) | ✅ dengan filter di beranda |
| Login Google (edit hanya karya sendiri) | ✅ Supabase OAuth + PKCE |
| Komentar, Like, Share | ✅ Share pakai Web Share API + fallback salin tautan |
| VN: rekam langsung dari browser | ✅ MediaRecorder + upload ke Supabase Storage |
| Sampul buku (crop otomatis 2:3) | ✅ |
| Pembaca nyaman: tema terang/sepia/gelap + ukuran huruf | ✅ tersimpan per perangkat |
| Anti stuck loading (timeout + tombol coba lagi) | ✅ |

## Struktur file

```
rak-cerita/
├── index.html            ← seluruh aplikasi (HTML+CSS+JS)
├── supabase-setup.sql    ← jalankan SEKALI di SQL Editor Supabase
└── README.md
```

## Catatan penyimpanan

- **Sampul** dikompres jadi JPEG ±600×900 lalu disimpan sebagai data URL di kolom `cover` (praktis, tanpa upload terpisah).
- **Audio VN** diunggah ke bucket `vn-audio` Supabase Storage (URL publik disimpan di kolom `audio_url`).
- Batas ukuran: judul 160 char, sinopsis 2.000, komentar 1.000, isi bab 100.000 char.
