# Generator Prompt Game Edukasi — EVEGROW x MIA

Versi Next.js dari generator prompt, siap deploy ke **Vercel**, dengan sistem
**daftar/login pakai email**, **pilih status (Guru / Orang Tua / Murid / Creator)**,
dan **riwayat prompt** tersimpan di database (Supabase).

Tanpa login, tool tetap bisa dipakai penuh (mode tamu) — hanya saja riwayat
prompt tidak akan tersimpan.

## 1. Siapkan database (Supabase — gratis)

1. Buat akun & project baru di https://supabase.com
2. Buka **Project Settings > API**, salin:
   - `Project URL`
   - `anon public key`
3. Buka **SQL Editor > New query**, tempel seluruh isi file
   `supabase/schema.sql` dari folder ini, lalu klik **Run**.
   Ini akan membuat tabel `profiles` (nama + status/role) dan
   `prompt_history` (riwayat prompt), lengkap dengan Row Level Security
   supaya setiap user hanya bisa melihat datanya sendiri.
4. (Opsional, untuk testing lebih cepat) Di **Authentication > Providers >
   Email**, kamu bisa menonaktifkan sementara "Confirm email" supaya akun
   baru langsung bisa login tanpa klik link konfirmasi dulu. Untuk produksi,
   sebaiknya biarkan aktif.

## 2. Jalankan di komputer kamu

```bash
npm install
cp .env.local.example .env.local
# lalu isi .env.local dengan Project URL & anon key dari langkah 1
npm run dev
```

Buka http://localhost:3000

## 3. Deploy ke Vercel

1. Push folder ini ke sebuah repo GitHub.
2. Buka https://vercel.com/new, import repo tersebut.
3. Saat diminta **Environment Variables**, isi:
   - `NEXT_PUBLIC_SUPABASE_URL`
   - `NEXT_PUBLIC_SUPABASE_ANON_KEY`
   (nilai yang sama seperti di `.env.local`)
4. Klik **Deploy**. Selesai — tool sudah online dengan login & riwayat
   prompt yang benar-benar tersimpan di database, bukan cuma di browser.

## Struktur proyek

```
app/
  layout.tsx        -> layout dasar + import font
  globals.css        -> semua styling (dipindah dari versi HTML asli)
  page.tsx            -> seluruh UI generator + logika login/riwayat
lib/
  data.ts             -> semua daftar opsi (jenis game, materi, usia, dst)
  promptBuilder.ts     -> fungsi yang merangkai Master Prompt
  supabaseClient.ts    -> koneksi ke Supabase
supabase/
  schema.sql           -> skema tabel + RLS, tinggal dijalankan di Supabase
```

## Cara kerja login & status

- **Daftar**: wajib pakai email + password.
- **Masuk**: hanya berhasil kalau email sudah terdaftar.
- Setelah login pertama kali, muncul pilihan status: Guru 🍎, Orang Tua
  👨‍👩‍👧, Murid 🎓, atau Creator 🎨 — ikon profil di pojok kanan atas berubah
  sesuai pilihan ini. Status bisa diganti lagi lewat menu profil.
- Setiap kali tombol **"Buat Prompt Game"** ditekan dan user sedang login,
  ringkasan prompt (brand, jenis game, jumlah soal, level) otomatis
  tersimpan ke tabel `prompt_history` dan muncul di menu profil.
- Tanpa login, generator tetap berfungsi penuh — riwayat saja yang tidak
  tersimpan.

## Catatan

Ini scaffold yang sudah lengkap secara logika (auth, RLS, penyimpanan
riwayat) tapi belum sempat dijalankan `npm install` di lingkungan chat ini
karena tidak ada akses jaringan di sini. Setelah `npm install` &
`npm run dev` di komputer kamu, kalau ada error kecil (biasanya cuma
soal versi paket), tinggal kirim pesan error-nya dan akan langsung
dibantu perbaiki.
