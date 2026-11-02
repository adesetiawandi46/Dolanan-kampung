# Dolanan Kampung 🌾

Game web permainan tradisional Indonesia (Engklek & Benteng). Satu file `index.html`, tanpa build step.

## 1. Supabase (leaderboard online)
1. Buat project di https://supabase.com, lalu buka **SQL Editor** dan jalankan:

```sql
create table public.scores (
  id bigint generated always as identity primary key,
  name text not null check (char_length(name) between 1 and 14),
  game text not null check (game in ('engklek','benteng','congklak','gundu','tali','sumput','egrang')),
  score int not null check (score between 0 and 100000),
  created_at timestamptz default now()
);

alter table public.scores enable row level security;

create policy "baca skor" on public.scores for select using (true);
create policy "kirim skor" on public.scores for insert with check (true);
```

2. Buka **Project Settings → API**, salin **Project URL** dan **anon public key**.
3. Tempel ke `index.html` pada bagian:

```js
const SB_URL = "https://xxxx.supabase.co";
const SB_KEY = "anon-key-kamu";
```

Kalau tabel sudah dibuat sebelumnya, jalankan ini agar game baru bisa menyimpan skor:

```sql
alter table public.scores drop constraint scores_game_check;
alter table public.scores add constraint scores_game_check check (game in ('engklek','benteng','congklak','gundu','tali','sumput','egrang'));
```

Anon key memang boleh ada di frontend, keamanannya dijaga oleh RLS di atas. Kalau dikosongkan, game memakai leaderboard lokal (localStorage).

## 2. GitHub
```bash
git init
git add .
git commit -m "Kampung Games"
git branch -M main
git remote add origin https://github.com/USERNAME/kampung-games.git
git push -u origin main
```

## 3. Vercel
1. Buka https://vercel.com → **Add New → Project** → import repo GitHub tadi.
2. Framework Preset: **Other**, tanpa build command. Klik **Deploy**.
3. Setiap `git push` ke `main` otomatis ter-deploy.

## Karakter & suasana kampung
Ada 6 karakter (Dimas, Sari, Asep, Ayu, Joko, Wati) yang bisa dipilih di menu. Pilihannya tersimpan di browser dan dipakai di semua game. Layar menang dan naik level diberi konfeti, dan latar punya awan, burung, serta layang-layang yang bergerak (otomatis dimatikan bagi pengguna yang memilih "reduce motion").

Anak-anak dan pemandangan kampung (sawah terasering, gunung, pohon kelapa, gubuk) digambar sebagai ilustrasi SVG di dalam kode, jadi tidak perlu file gambar. Mau lebih nyata? Taruh file di folder `assets/` dan game otomatis memakainya:

- `assets/boy.png`, `boy2.png`, `boy3.png`, `girl.png`, `girl2.png`, `girl3.png`: karakter (PNG transparan, rasio kira-kira 40:64)
- `assets/scene.jpg`: foto/ilustrasi latar kampung

Gunakan hanya aset yang kamu punya hak pakainya. Kalau memakai foto anak-anak, pastikan sudah ada izin tertulis dari orang tua/wali.

## Suara
Semua efek suara (kentongan, bel gamelan, tek-tek biji, klak kelereng) dibuat langsung lewat WebAudio, jadi tidak ada file audio tambahan. Tombol 🔊 di pojok kanan atas untuk mute, pilihannya tersimpan di browser.

## Lagu dolanan
Ada 9 lagu daerah yang diputar instrumental ala gamelan (Cublak-Cublak Suweng, Gundul-Gundul Pacul, Lir-Ilir, Burung Kakak Tua, Ampar-Ampar Pisang, Soleram, Pok Ame-Ame, Manuk Dadali, Es Lilin), berganti otomatis tiap 2 putaran. Tombol 🎵 untuk menyalakan/mematikan, ⏭ untuk ganti lagu. Nadanya ada di array `SONGS` di `index.html` dan bisa kamu sesuaikan atau tambah lagu baru.

### Pakai rekaman asli
Lagu bawaan adalah aransemen gamelan sintesis. Kalau kamu punya rekaman sendiri, taruh file mp3 di `assets/music/` dengan nama sesuai judul, dan game otomatis memutarnya menggantikan versi sintesis:
`cublak-cublak-suweng.mp3`, `gundul-gundul-pacul.mp3`, `lir-ilir.mp3`, `burung-kakak-tua.mp3`, `ampar-ampar-pisang.mp3`, `soleram.mp3`, `pok-ame-ame.mp3`, `manuk-dadali.mp3`, `es-lilin.mp3`.
Melodi lagu daerahnya sendiri adalah warisan budaya, tapi sebuah rekaman punya hak cipta tersendiri. Pakai rekaman buatanmu, berlisensi bebas (CC0/CC-BY), atau yang sudah kamu beli lisensinya.

## Ide pengembangan
Gasing, balap karung, tarik tambang, login Google lewat Supabase Auth, skor mingguan, dan mode duel online dengan Supabase Realtime.
