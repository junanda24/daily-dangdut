# Design — DANGDUT! Raja Koplo

## Stack
- Web artifact static: satu halaman HTML + CSS + JS (client-side penuh).
- Audio: WebAudio API murni (tanpa file audio). Lookahead scheduler.
- Render: DOM + CSS untuk UI dan lajur; `<canvas>` overlay untuk partikel
  (uang saweran, spark) dengan object pooling.
- Font: display tebal (Anton/Archivo Black via Google Fonts) + Plus Jakarta Sans
  untuk body. (Catatan: craft floor menyarankan self-host; artifact memakai
  Google Fonts link — deviasi dicatat, font fallback sistem bila offline.)

## Dunia visual (direction contract, code-led)
- **Thesis:** panggung dangdut keliling di lapangan malam hari — bukan aplikasi
  musik yang rapi, tapi hajatan yang berdenyut. Layar pertama harus terasa
  seperti berdiri di depan panggung, bukan membuka menu.
- **Own-world:** malam ungu pekat (#14081f–#1e0b2e), magenta neon (#ff2e88),
  emas (#ffc93c), hijau lampu panggung (#39ff88). Lampu sorot menyapu,
  siluet penonton bergoyang di bawah, tirai panggung di tepi. Huruf display
  sangat tebal, miring sedikit, dengan bayangan keras magenta/emas.
- **First viewport:** judul "DANGDUT!" raksasa memenuhi lebar, subjudul
  "RAJA KOPLO" emas di bawahnya, siluet penyanyi + mikrofon di tengah,
  tombol "MULAI GOYANG" besar di bawah, dan baris "3 lagu • ketuk D F J K /
  tap layar". Lampu disko menyapu di belakang judul.
- **Signature interaction:** tiap ketukan SEMPURNA memantulkan cahaya ke
  seluruh panggung (flash singkat) dan penonton melompat; SAWERAN
  menghujankan lembar uang Rp dari atas layar.
- **Finish:** build berakhir dengan finish review (cek visual + cek spec),
  bukan sekadar "sudah jadi".

## Arsitektur (ikut ARCHITECTURE.md §1 + §5)
Satu file halaman, struktur JS mengikuti pola baku:

```
// 1. STATE — satu objek state
const state = { screen, song, score, combo, maxCombo, counts, hype, modeJoget, ... };

// 2. DATA — lagu, pola kendang, pola melodi (konstanta, terpisah dari logic)
const SONGS = [ { id, title, bpm, level, seed, bars, chordRoots, melody } ... ];
const KENDANG_PATTERN = {...}; // pola dang/tak per 16 langkah

// 3. AUDIO — AudioEngine: init(), schedule(bar, step), suara kendang/bass/suling
//    (synth: sine pitch-drop utk "dang", noise bandpass utk "tak", saw lowpass utk bass,
//     triangle + vibrato utk suling; scheduler lookahead 25ms / 0.12s)

// 4. BEATMAP — buildBeatmap(song): [ {time, lane} ] deterministik dari seed;
//    not dari aksen melodi + ketuk kendang (kepadatan per level)

// 5. GAME — state machine + satu rAF loop (delta time):
//    update(): posisi not dari audioClock; penilaian hit; hype; partikel (pool)
//    render(): DOM lajur + canvas partikel

// 6. STORE — satu modul localStorage: loadBest()/saveBest()

// 7. EVENTS — handler tipis: keydown D/F/J/K, pointerdown lajur, tombol UI
// 8. INIT — render menu
```

Aturan yang dijaga: logic bisnis tidak di event handler; querySelector
di-cache; tidak ada variabel global berserakan; tidak ada god component
(pecah per modul: audio / beatmap / game / render / store).

## Keranjang state (lima keranjang ARCHITECTURE.md)
1. **Lokal** — skor, kombo, hype, posisi not, partikel (state game).
2. **URL** — tidak dipakai (game tidak butuh state shareable).
3. **Server state** — tidak ada (tanpa backend).
4. **Global client UI** — screen saat ini, lagu terpilih, jeda (satu state object).
5. **Form** — tidak ada form.

## Audio — detail synth
- **Kendang "dang":** sine 160→55 Hz, 0.18 dtk + click noise pendek.
- **Kendang "tak":** noise bandpass 900 Hz, 0.07 dtk + sine 300 Hz pendek.
- **"Dut" (isi):** sine 110→70 Hz, 0.1 dtk.
- **Bass:** sawtooth → lowpass 400 Hz, root chord per bar, sinkopasi ringan.
- **Suling:** triangle + vibrato LFO 6 Hz, frasa pentatonik minor (A C D E G /
  D F G A C / E G A B D), nada panjang di akhir frasa.
- **Pola kendang per bar (16 langkah):** dang di 0 & 8, tak di 4 & 12,
  dut pengisi di 2, 6, 10, 14 (variasi per lagu/level); koplo cepat menambah
  tak di langkah ganjil pada bar tertentu.
- **Chord per lagu:** Goyang Pagi Am–F–C–G; Koplo Pantura Dm–Bb–F–C;
  Sawer Gila Em–C–G–D. Melodi digenerate dari chord tone + passing note,
  seeded (mulberry32) agar sama tiap main.

## Beatmap — detail
- Not = tiap nada melodi + tiap aksen kendang "dang"/"tak" pada level Joget
  ke atas; lajur dipilih dari pitch (rendah→kiri) dengan variasi seeded.
- Kepadatan: Santai ±1 not/ketuk, Joget ±1.5, Koplo Gila ±2 + stream 4 not
  cepat di akhir tiap 4 bar.
- Lead-in 2 detik; approach time (not terlihat sebelum garis hit) 1.6 dtk
  (Santai) / 1.3 dtk (Joget) / 1.1 dtk (Koplo Gila).

## Animasi / motion
- Satu momen authored: flash panggung + lompat penonton saat SEMPURNA beruntun.
- Lampu sorot: dua beam CSS yang berayun mengikuti BPM (transform rotate,
  ease-out eksponensial dari default terlihat).
- Transisi layar: fade + scale kecil (150 ms), bukan entrance identik tiap section.
- Partikel: pool 120 partikel (spark + uang), reuse.

## Responsif
- Mobile-first: lajur 4 kolom memenuhi lebar (max 480 px, tengah di desktop),
  area tap lajur full-height, tombol minimal 48 px.
- Desktop: panggung lebih lebar (max 720 px), keyboard hint terlihat.
