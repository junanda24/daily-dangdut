# Spec — DANGDUT! Raja Koplo

## Overview
Rhythm game 4-lajur bertema panggung dangdut koplo. Musik disintesis live
(WebAudio): kendang, bass, suling. 3 lagu orisinal, 3 tingkat kesulitan.
Skor, kombo, grade, dan high-score per lagu tersimpan di localStorage.

## Lagu (data)
| # | Judul | BPM | Kesulitan | Karakter |
|---|-------|-----|-----------|----------|
| 1 | Goyang Pagi | 100 | Santai | Dangdut klasik, not rapat sedang |
| 2 | Koplo Pantura | 128 | Joget | Koplo, not lebih rapat + sinkop |
| 3 | Sawer Gila | 150 | Koplo Gila | Koplo cepat, stream not padat |

Tiap lagu: intro 2 bar (4 ketuk hitung mundur "3-2-1-GOYANG"), durasi ±60–75 detik,
diakhiri outro dan layar hasil.

## Requirements

### R-01 — State machine eksplisit
- SHALL: game punya state MENU → PILIH_LAGU → HITUNG_MUNDUR → MAIN → JEDA →
  HASIL, dengan transisi yang jelas dan tidak ada jalan buntu.
- WHEN pemain di MAIN menekan tombol Jeda THEN game berhenti (audio ikut
  berhenti) dan menampilkan layar jeda dengan tombol Lanjut / Ulangi / Menu.
- WHEN lagu selesai THEN tampil layar HASIL (bukan kembali diam-diam ke menu).

### R-02 — Beatmap deterministik sinkron audio
- SHALL: not digenerate dari pola lagu (seeded, sama tiap main) dan posisinya
  dihitung dari jam audio (AudioContext.currentTime), bukan dari jam dinding.
- SHALL: ada lead-in (not pertama muncul ≥ 2 detik setelah musik mulai) dan
  audio dijadwalkan dengan lookahead scheduler (interval 25 ms, jadwal 0.12 dtk
  ke depan) agar tidak ngelag.
- WHEN audio belum diizinkan browser (autoplay policy) THEN musik mulai hanya
  setelah interaksi pemain (tap tombol), dan game tidak jalan tanpa audio.

### R-03 — Input 4 lajur, keyboard + sentuh
- SHALL: 4 lajur, tombol keyboard D F J K, dan tap langsung pada lajur di layar
  sentuh; tiap lajur punya area sentuh besar (min. 64 px lebar di HP).
- WHEN pemain menekan lajur THEN game menilai not terdekat di lajur itu dalam
  jendela waktu; not yang ditekan di luar jendela tidak memberi skor dan tidak
  merusak kombo (tombol kosong = aman, bukan miss).

### R-04 — Penilaian & skor
- SHALL: jendela penilaian SEMPURNA ±60 ms (300 poin), BAGUS ±120 ms (100 poin),
  selebihnya MISS (0 poin, kombo putus).
- SHALL: kombo bertambah tiap hit, reset saat MISS; skor akhir = jumlah poin
  × pengali Mode Joget bila aktif.
- WHEN kombo mencapai 25 / 50 / 100 THEN event SAWERAN: hujan uang Rp di layar
  + bonus 500 / 1000 / 2500 poin + teks "SAWERAN!".

### R-05 — Joget Meter (penonton)
- SHALL: ada meter hype penonton: naik tiap hit (+2 SEMPURNA, +1 BAGUS),
  turun tiap MISS (−5), 0–100.
- WHEN meter mencapai 100 THEN Mode Joget aktif 8 detik: skor ×2, lampu
  panggung menyala penuh, penonton melompat; meter reset ke 40 setelah selesai.

### R-06 — Layar hasil & grade
- SHALL: layar hasil menampilkan skor, kombo maks, akurasi %, hitungan
  SEMPURNA/BAGUS/MISS, grade (S ≥95%, A ≥85%, B ≥70%, C ≥50%, F <50%),
  dan skor terbaik per lagu (label "REKOR BARU!" bila pecah).
- WHEN akurasi < 50% THEN teks hasil "Penonton pulang... latihan lagi, Bos!"
  (grade F) — tetap bisa ulangi langsung.

### R-07 — High-score persisten
- SHALL: skor terbaik per lagu disimpan di localStorage (key
  `dangdut-highscore-v1`) dan ditampilkan di layar pilih lagu.
- WHEN pemain memecahkan rekor THEN skor baru tersimpan dan tampil di layar
  hasil dan layar pilih lagu.

### R-08 — Visual panggung (delight)
- SHALL: latar panggung malam (ungu gelap + magenta + emas), lampu disko
  berdenyut mengikuti ketuk, siluet penonton di bawah yang melompat saat hit,
  partikel saat SEMPURNA, hujan uang saat SAWERAN.
- SHALL: semua visual digambar prosedural (CSS/SVG/canvas) — tanpa gambar
  pihak ketiga, tanpa emoji sebagai ikon.
- SHALL: teks penilaian (SEMPURNA/BAGUS/MISS) muncul di dekat garis hit dengan
  animasi pop, dan kombo besar tampil di tengah atas saat ≥10.

### R-09 — Responsif & performa
- SHALL: playable di HP (lebar 360 px) dan desktop; lajur menyesuaikan lebar,
  tidak ada scroll horizontal, tidak ada teks terpotong.
- SHALL: satu game loop requestAnimationFrame + delta time; partikel memakai
  object pooling (tidak alokasi baru tiap frame).
- SHALL: tidak ada error console saat alur penuh: menu → pilih lagu → main →
  hasil → ulangi → menu.

### R-10 — Aksesibilitas & kejelasan
- SHALL: semua tombol punya label jelas Bahasa Indonesia; tombol Jeda terlihat
  selama MAIN; cara main dijelaskan di layar menu (tombol D F J K / tap lajur).
- SHALL: kontras teks ≥ 4.5:1; tidak ada teks placeholder/lorem ipsum.

## Non-goals (sengaja TIDAK dibuat)
- Lagu dangdut sungguhan / lirik / vokal (alasan: lisensi).
- Mode multiplayer / leaderboard online (alasan: static, tanpa backend).
- Editor beatmap (alasan: scope satu sesi).
