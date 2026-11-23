# DANGDUT! — Raja Koplo

Game ritme dangdut koplo yang jalan langsung di browser HP maupun desktop.
Ketuk not yang jatuh mengikuti irama kendang, bass, dan suling — semuanya
disintesis live dengan WebAudio, tanpa satu pun file musik berlisensi.

> **Rilis:** 2026-11-24 · **Program:** 1 Hari 1 Project (#40) · **Jenis:** game (web artifact static, tanpa backend)

## Kenapa ide ini dipilih

Dangdut adalah musik rakyat Indonesia yang paling dikenal — tapi belum pernah ada
yang membuat game ketuk-irama koplo yang bisa dimainkan di browser. Semua ikon
dangdut diterjemahkan jadi mekanik game: kombo = joget, milestone kombo =
saweran uang, hype meter = penonton yang makin panas. Efek kejutnya: "kok
kepikiran sih bikin game ritme dangdut?"

## Fitur

- **3 lagu orisinal** (komposisi prosedural, bukan lagu sungguhan):
  - *Goyang Pagi* — 100 BPM, Santai, akor Am–F–C–G
  - *Koplo Pantura* — 128 BPM, Joget, akor Dm–Bb–F–C
  - *Sawer Gila* — 150 BPM, Koplo Gila, akor Em–C–G–D
- **Musik disintesis live (WebAudio):** kendang "dang"/"tak", bass sinkopasi,
  suling pentatonik; scheduler lookahead berbasis jam audio (bukan jam dinding)
  supaya not dan musik tetap sinkron.
- **Gameplay 4 lajur:** keyboard **D F J K** atau tap lajur di layar sentuh.
- **Penilaian:** SEMPURNA ±60 ms (300 poin), BAGUS ±120 ms (100 poin), MISS
  (kombo putus). Menekan lajur kosong tidak menghukum.
- **SAWERAN!** — kombo 25/50/100 menghujankan uang Rupiah + bonus
  500/1000/2500 poin.
- **Joget Meter** — penonton makin panas tiap hit; penuh (100) → Mode Joget:
  skor ×2 selama 8 detik, lampu panggung menyala penuh.
- **Layar hasil:** skor, kombo maks, akurasi %, hitungan SEMPURNA/BAGUS/MISS,
  grade S–F, dan label **REKOR BARU!** bila memecahkan rekor.
- **High-score per lagu** tersimpan di localStorage (`dangdut-highscore-v1`).
- **Visual panggung malam:** lampu disko berdenyut mengikuti ketuk, siluet
  penonton yang melompat, siluet penyanyi, partikel (object pooling).
- **Jeda:** tombol Jeda / `Esc` / `P` — musik ikut berhenti; Lanjut / Ulangi /
  Menu. Tidak ada jalan buntu di alur mana pun.

## Cara main

1. Buka gamenya, tekan **MULAI GOYANG**.
2. Pilih lagu (BPM dan tingkat kesulitan tertera; rekor terbaikmu tampil di
   kartu lagu).
3. Setelah hitung mundur **3-2-1-GOYANG!**, ketuk not saat mencapai garis emas:
   - Keyboard: **D F J K** untuk lajur 1–4.
   - Layar sentuh: tap langsung pada lajurnya.
4. Jaga kombo tetap hidup — makin panjang kombo, makin besar saweran.
   Isi Joget Meter sampai penuh untuk skor ×2.
5. Di akhir lagu, lihat grade-mu (S ≥ 95% akurasi). Ulangi untuk memecahkan
   rekor!

## Cara menjalankan dari source

`src/index.html` adalah satu file mandiri (HTML + CSS + JS, tanpa build step,
tanpa dependensi). Cukup buka di browser modern (Chrome/Edge/Firefox/Safari)
— bisa juga disajikan lewat server statis apa pun:

```bash
# contoh: sajikan lokal
python3 -m http.server -d src 8080
# lalu buka http://localhost:8080
```

> Catatan: musik butuh interaksi pertama (klik/tap) karena kebijakan autoplay
> browser — itu disengaja, bukan bug.

## Tech stack

- HTML5 + CSS + JavaScript (single file, tanpa framework, tanpa bundler)
- WebAudio API (sintesis kendang/bass/suling + lookahead scheduler)
- Canvas 2D overlay untuk partikel (object pooling)
- localStorage untuk high-score

## Struktur folder

```
2026-11-24-dangdut/
├── README.md
├── src/
│   └── index.html          # game lengkap (export artifact)
└── openspec/
    └── changes/
        ├── 2026-11-24-dangdut/          # proposal, spec, design, tasks
        └── archive/2026-11-24-dangdut/  # arsip setelah selesai
```

## Link hasil

- Game: artifact `dangdut-raja-koplo` (link kartu artifact di laporan harian)
- Repo: https://github.com/junanda24/daily-dangdut

## Catatan / temuan saat build

- **Lagu 100% orisinal.** Melodi digenerate dari pola skala minor/pentatonik
  dengan seed tetap (deterministik) — bukan lagu dangdut sungguhan, jadi tidak
  ada isu lisensi. Akor dan BPM diambil dari rentang koplo nyata (riset:
  dangdut 70–140 BPM, koplo lebih cepat; pola kendang "dang-dut" khas).
- **Lead-in not pertama 4,8 detik** pada "Goyang Pagi" (spec minta ≥ 2 detik) —
  pemain punya waktu siap setelah hitung mundur.
- **Verifikasi mandiri (jsdom + AudioContext & canvas di-stub), 2 skenario:**
  1. Alur penuh 31 cek — menu → pilih lagu → main 20 detik (20 SEMPURNA,
     0 MISS) → jeda → lanjut → lompat ke akhir lagu (grade F, akurasi 19,6%,
     "Penonton pulang...") → Main Lagi → Menu — semua lolos, tanpa error console.
  2. Main sempurna satu lagu penuh — SAWERAN tampil di kombo 25 (+500),
     Mode Joget aktif di kombo 50, 102/102 SEMPURNA, grade S, akurasi 100,0%,
     skor 41.200, REKOR BARU! + high-score tersimpan — semua lolos.
- **Yang tidak terverifikasi langsung:** bunyi audio itu sendiri (lingkungan
  audit headless tanpa keluaran suara; sintesis berjalan tanpa error) —
  perlu cek telinga manusia di HP.
- **Bukan tujuan:** lagu sungguhan/lirik/vokal, multiplayer/leaderboard,
  editor beatmap.
