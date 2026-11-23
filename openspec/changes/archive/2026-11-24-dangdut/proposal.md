# Proposal — DANGDUT! Raja Koplo (2026-11-24)

## Apa ini
Game ritme (rhythm game) dangdut koplo yang bisa dimainkan langsung di browser HP.
Pemain mengetuk not yang jatuh mengikuti irama kendang, bass, dan suling — semuanya
disintesis live dengan WebAudio, tanpa file musik berlisensi.

## Kenapa ide ini (efek kejut)
- **Genre yang belum pernah ada di program ini:** dari 39 project sebelumnya belum
  ada satu pun rhythm game, apalagi dangdut. Dangdut adalah musik rakyat Indonesia
  yang paling dikenal — tapi belum pernah ada yang bikin game ketuk-irama koplo
  yang jalan di browser.
- **"Kok kepikiran sih":** kendang "dang-dut", saweran uang, joget, orkes panggung —
  semua ikon dangdut diterjemahkan jadi mekanik game (combo = joget, milestone =
  saweran, hype meter = penonton).
- **Bukan demo teknis:** 3 lagu orisinal dengan BPM dan tingkat kesulitan berbeda,
  sistem penilaian, grade, high-score per lagu, dan mode joget 2x skor.

## Kenapa bentuk output ini
- **Game web artifact (HTML5 + WebAudio, static):** rhythm game butuh audio presisi
  dan input sentuh — browser adalah tempatnya. Static cukup: semua state (skor
  terbaik) di localStorage, tidak butuh login/database.
- **Tanpa backend:** beatmap digenerate deterministik dari pola (seeded), musik
  disintesis di client. Backend tidak menambah apa-apa.

## Riset singkat (2026-11-24)
- Dangdut: tempo 70–140 BPM, koplo lebih cepat (hingga ~150–168 BPM); pola kendang
  "dang-dut" khas; instrumen inti gendang, suling, mandolin, bass elektrik
  sinkopasi (sumber: Sound Atlas, Tunebat — lagu koplo 93–168 BPM).
- Lagu dipilih: "Goyang Pagi" 100 BPM (Santai), "Koplo Pantura" 128 BPM (Joget),
  "Sawer Gila" 150 BPM (Koplo Gila) — BPM dalam rentang koplo nyata.

## Uji bar scope
- Bukan utilitas satu fungsi. Game lengkap: 3 lagu, 3 tingkat kesulitan, sistem
  skor/kombo/grade, high-score tersimpan, efek partikel, mode joget.
- Selevel patokan (TEBAK HARGA!, JUKIR!, TAWAR!) — game dengan mekanik jelas dan
  bisa dimainkan berulang. Lolos.

## IP check
- Semua lagu adalah komposisi orisinal yang digenerate dari pola skala minor /
  pentatonik — BUKAN lagu dangdut sungguhan, tidak ada rekaman, lirik, atau nama
  artis yang dipakai. Tidak ada aset berlisensi. Visual (panggung, penonton,
  lampu disko) digambar prosedural via kode/SVG. Tidak perlu disclaimer fan-made.

## Anti-AI-slop (copy)
- Copy Bahasa Indonesia natural, bahasa panggung dangdut yang hidup ("Goyang!",
  "Saweran!", "Penonton pulang..."), tanpa frasa generik AI ("selamat datang di
  pengalaman luar biasa", dll). Tidak ada lorem ipsum.

## Skill ledger (aturan build: analisis skill dulu)
- `openspec-web-builder` — protokol explore → propose → apply → verify → archive.
  Dipakai. (Gate approval dilewati: scheduler memberi otoritas penuh.)
- `ARCHITECTURE.md` §5 (game) — satu game loop rAF + delta, state machine
  MENU → PLAYING → PAUSED → GAME_OVER, object pooling partikel, keyboard +
  touch, high-score localStorage. Dipakai.
- `local-impeccable` — arah visual + craft floor. Dipakai (code-led: run ini
  unattended, tidak ada user untuk menjawab decision page concept-seed; arah
  visual dikunci di design.md, bukan lewat roll). Craft floor diterapkan:
  tanpa kicker/eyebrow, tanpa gradient text, tanpa kartu ikon seragam, ikon
  digambar SVG (bukan emoji), kontras ≥4.5:1.
- `local-ui-ux-pro-max-skill` — file skill hanya berisi catatan recovery tanpa
  isi actionable → diskip, dicatat di sini.
- Skill game/spesifik lain (game-designer, game-audio-designer) — relevan
  sebagian, tapi brief game ini sudah spesifik dan terdefinisi penuh di spec;
  tidak dibaca agar tidak menggelembungkan scope. Dicatat sebagai skip sadar.

## Keputusan
**DISETUJUI SENDIRI** (otoritas scheduler, tanpa gate). Lanjut ke Apply.
