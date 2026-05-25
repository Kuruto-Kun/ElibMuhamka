# Panduan Deploy eLib Muhamka ke Domain Sendiri

## Opsi 1 — Tetap di Lovable (paling mudah)

1. Klik **Publish** di kanan atas Lovable
2. Setelah live di `*.lovable.app`, buka **Project Settings → Domains → Add Custom Domain**
3. Tambahkan domain Anda (mis. `elib.sekolahanda.sch.id`) dan ikuti petunjuk DNS
4. Database (Lovable Cloud) tetap berjalan otomatis — tidak perlu setup tambahan

> Cara ini paling disarankan: deploy + database + auth + storage semua dikelola otomatis.

---

## Opsi 2 — Self-Host (export ZIP)

### Langkah 1: Export Source Code

1. Di Lovable, klik ikon **GitHub** (kanan atas) → **Connect to GitHub**
2. Setelah terhubung, klik **Create Repository** → pilih nama repo
3. Clone repo Anda:
   ```bash
   git clone https://github.com/USERNAME/REPO_NAME.git
   cd REPO_NAME
   ```
4. (Alternatif tanpa GitHub) Download manual: tidak tersedia langsung — GitHub adalah cara resmi

### Langkah 2: Setup Database Sendiri (Supabase)

Lovable Cloud adalah Supabase di balik layar. Untuk self-host:

1. Daftar gratis di https://supabase.com → buat project baru
2. Di **SQL Editor**, jalankan semua file di `supabase/migrations/` **berurutan** (sesuai tanggal di nama file)
3. Catat **Project URL** dan **anon key** dari Settings → API
4. Aktifkan provider auth Email/Password di Authentication → Providers

### Langkah 3: Konfigurasi Environment

Buat file `.env` di root:
```
VITE_SUPABASE_URL=https://xxxxx.supabase.co
VITE_SUPABASE_PUBLISHABLE_KEY=eyJhbGc...
VITE_SUPABASE_PROJECT_ID=xxxxx
```

### Langkah 4: Build & Test Lokal

```bash
bun install     # atau npm install
bun run dev     # buka http://localhost:5173
```

### Langkah 5: Deploy

**Pilihan A — Vercel (rekomendasi, gratis):**
```bash
npm i -g vercel
vercel
```
Tambahkan env var di dashboard Vercel.

**Pilihan B — Cloudflare Pages:**
- Connect GitHub repo di dash.cloudflare.com → Pages
- Build command: `bun run build`
- Output: `dist/`
- Tambahkan env vars

**Pilihan C — VPS sendiri (Nginx + Node):**
```bash
bun run build
# upload folder dist/ ke server
# arahkan Nginx ke folder dist/
```

### Langkah 6: Custom Domain

Di dashboard Vercel/Cloudflare → Domains → Add → ikuti petunjuk DNS (CNAME ke target hosting).

---

## Admin Default

Setelah deploy, buka `/setup` untuk mengklaim admin pertama (sekali pakai).

---

## Catatan Penting

- Foto profil & gambar soal disimpan di **Supabase Storage** (bucket `avatars` & `question-images`)
- Migrasi database SQL ada di `supabase/migrations/` — jalankan berurutan
- Untuk pindah data dari Lovable Cloud ke Supabase sendiri: gunakan **pg_dump** atau export via dashboard
