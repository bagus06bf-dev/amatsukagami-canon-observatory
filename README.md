# Amatsukagami — Canon Observatory

Aplikasi web statis interaktif untuk memantau konsistensi kanon fanfic **One Piece: Amatsukagami**.

**Baseline:** Master Story Bible v0.6, 4 November 2026: https://docs.google.com/document/d/1X-4Op-Vh5DVr7D5UWDSQyRnH5LwMUmaeu6WNNHhAVos/edit

## Kemampuan

- **Dashboard:** indikator jumlah karakter, kronologi, rilis yang dicatat, dan usulan menunggu persetujuan.
- **Timeline:** sejarah Kenjiro–Ruby, Hagoromo, migrasi generasi ke-5, Abad Kekosongan, Toki, God Valley, masa pelatihan, dan era Luffy.
- **Database:** karakter, relik, delapan Buah Iblis AU, relasi Kozuki, tautan rujukan resmi, dan spekulasi yang **secara eksplisit tidak dikunci**.
- **Pantauan manga:** formulir wajib nomor bab, tanggal, sumber URL, ringkasan, dampak kontinuitas, bagian cerita yang terdampak, dan topik (God Valley, Garp, Abad Kekosongan, Kozuki, Toki). Setiap entri dimulai sebagai *Belum diverifikasi*. Verifikasi dilakukan oleh pengguna.
- **Audit:** menandai klaim yang bertentangan dengan keputusan terkunci (misalnya Hagoromo memigrasikan klan, Akira memakan Buah Iblis, Vivi ikut berlayar permanen, atau Akira tiba sebelum akhir God Valley). Peringatan tetap tentang Nidai Kitetsu versus redaksi lama pedang pendiri juga tersedia.
- **Usulan:** usulan baru berstatus *Menunggu persetujuan*. Hanya klik persetujuan manual yang mengubah status menjadi *Disetujui lokal*. **Tidak ada pengubahan dokumen master otomatis.**
- **Backup:** ekspor dan impor JSON untuk membawa catatan, proposal, dan klaim antarperangkat.

## Menjalankan di Windows

**Cara termudah**: buka file `Amatsukagami-Canon-Observatory.html` (versi satu berkas) dengan Chrome/Edge. Jika penyimpanan lokal dibatasi pada file lokal, jalankan melalui server lokal:

1. Pastikan Python terpasang.
2. Ekstrak ZIP ini ke folder, lalu buka terminal pada folder itu.
3. Jalankan `python -m http.server 8000`.
4. Buka `http://localhost:8000` pada Chrome/Edge.

Tidak memerlukan Node.js atau instalasi paket.

## Menempatkan aplikasi di Google Sites

Google Sites tidak dapat diterbitkan secara langsung melalui koneksi yang tersedia dalam sesi ini. Aplikasi ini dapat **di-embed di Google Sites** dengan alamat web setelah di-host:

1. Unggah isi ZIP (folder yang memuat `index.html`) ke **Cloudflare Pages** melalui opsi unggah aset statis. Pengguna sebelumnya telah menggunakan Cloudflare.
2. Salin alamat HTTPS Pages yang diberikan Cloudflare.
3. Buka editor **Google Sites** → **Sisipkan / Insert** → **Sematkan / Embed** → **URL**, tempel alamat Pages, sesuaikan ukuran, dan publikasikan situs.

**Alternatif:** gunakan web app ini sebagai website mandiri di Cloudflare Pages tanpa Google Sites.

## Perlindungan data dan batasan

- **Tidak ada koneksi otomatis ke Google Drive, Google Docs, atau feed bab manga.** Baseline statis diperoleh dari Master Story Bible v0.6.
- **Tidak ada AI atau pembaca manga otomatis.** Mesin audit menggunakan aturan berbasis pola teks yang terbatas dan dapat salah mendeteksi atau melewatkan kontradiksi.
- **Catatan disimpan di localStorage per browser/per situs.** Data pada komputer berbeda tidak saling tersinkron. Cadangkan secara berkala menggunakan `Ekspor JSON`.
- Domain sumber resmi hanya diberi penanda asal URL yang dikenal; isi bab tetap memerlukan verifikasi manusia.
- **Persetujuan lokal tidak mengubah Story Bible.** Setelah persetujuan, penulis menyalin redaksi ke master secara manual; dalam pengembangan masa depan dapat dibuat sinkronisasi yang tetap memerlukan persetujuan eksplisit.
- **Jangan mempublikasikan informasi privat yang tidak ingin terlihat publik.** Jika Cloudflare Pages atau Google Sites diterbitkan tanpa kontrol akses, konten awal aplikasi dapat dibaca umum. Catatan lokal tersimpan di perangkat pengunjung sendiri; tidak diunggah ke server situs.

## Struktur

- `index.html` — kerangka antarmuka.
- `styles.css` — visual dan desain responsif.
- `data.js` — baseline kanon read-only (dalam kode).
- `rules.js` — aturan audit konsistensi.
- `app.js` — alur aplikasi, form, penyimpanan, persetujuan, ekspor-impor.
- `Amatsukagami-Canon-Observatory.html` — salinan satu berkas mandiri.

## Sumber resmi untuk verifikasi

- MANGA Plus: https://mangaplus.shueisha.co.jp/titles/100020
- ONE PIECE.com, daftar volume: https://one-piece.com/comics/index.html?category=elbaph

Volume 114 mencakup bab 1156–1166 (God Valley), menurut daftar volume di situs resmi; **ini bukan pengisian otomatis ringkasan bab**.

## Supabase cloud sync (v1.1)

Aplikasi tetap diterbitkan sebagai GitHub Pages statis. Login email/password dan penyimpanan catatan kerja diaktifkan lewat Supabase dengan Project URL `https://zzvcfsueioaalwtgqwjy.supabase.co` dan **publishable key** (bukan secret). Jangan pernah memasukkan database password, `sb_secret_...` atau `service_role` ke kode publik.

**Tabel wajib:** `public.observatory_state` dengan kolom `user_id uuid primary key`, `state jsonb not null`, `updated_at timestamptz not null`. Row Level Security harus aktif. Policy SELECT/INSERT/UPDATE untuk role `authenticated` mensyaratkan `user_id = (select auth.uid())`. Pengguna anonim tidak mendapat akses.

**Konfigurasi Auth:** Supabase → Authentication → URL Configuration: Site URL dan Redirect URL diatur ke `https://bagus06bf-dev.github.io/amatsukagami-canon-observatory/`. Provider Email aktif. Konfirmasi email direkomendasikan.

**Cara menggunakan:**
1. Buka website lalu klik **Masuk · Cloud**. Daftar dengan email dan kata sandi jika belum mempunyai akun; konfirmasi email lalu login.
2. Jika ada catatan lokal lama, pilih **Gabungkan & simpan ke cloud** agar catatan lama dan catatan cloud dipertahankan. Pilih **Gunakan data cloud** hanya jika memang ingin memakai isi cloud dan mengabaikan catatan lokal yang belum diimpor.
3. Perhatikan indikator **Cloud terhubung · tersinkron**. Jika sinkron gagal, catatan tetap berada di browser; gunakan **Sinkronkan sekarang** setelah jaringan pulih.
4. Lakukan **Ekspor JSON** secara berkala sebagai cadangan.

Penyimpanan menggunakan satu dokumen JSON per pengguna dan pengamanan konflik berbasis versi kolom `updated_at`. Jika perangkat lain telah memperbarui data, penggabungan harus dipilih secara manual. Semua *baseline* kanon tetap *read-only* dan **Master Story Bible di Google Drive tidak pernah diperbarui otomatis**.

**Batasan:** Versi ini belum melakukan realtime push, sinkronisasi Google Docs, atau pemeriksaan manga otomatis. Pengujian lokal dilakukan dengan simulasi Supabase; keberhasilan login dan penulisan ke database produksi harus diverifikasi dari website aktif.
