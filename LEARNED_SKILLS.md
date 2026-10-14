# LEARNED_SKILLS — Catatan Belajar Kuro (Muse)

Aturan: tiap selesai belajar repo baru / trik baru / koreksi user → tulis
intisari di sini + push ke GitHub. Format: tanggal, sumber, inti, cara pakai.

---

## 2026-10-14 — remotion-opus-skill (direkturcrypto/remotion-opus-skill)
- Sumber: GitHub, atas perintah user (upgrade engine utama dari Pillow).
- Inti: framework Remotion berbasis spec JSON. `ros new` → brief → facts →
  spec.json → words.json → stills (QA frame) → render. Engine: flythrough /
  poster / kinetic. Looks: graphite-studio, midnight-glass, paper-ink,
  aurora-soft, mono-lab, ember-noir. Story: launch, problem-solution, versus,
  demo, reasons, one-number.
- Cara pakai: `node bin/ros.mjs render <slug> --aspect portrait`.
  WAJIB baca `skill/remotion-opus/SKILL.md` sebelum bikin video — jangan tulis
  engine Remotion baru.
- Gotcha: schema kunci fps=60; concurrency default > jumlah core → set
  RENDER_CONCURRENCY=1 di VM kecil (Chrome OOM di concurrency 2, RAM 8GB).

## 2026-10-14 — Font loading di sandbox render (penting!)
- Masalah: Chromium headless Remotion kena ERR_CONNECTION_RESET ke
  fonts.gstatic.com (curl dari shell BISA, browser TIDAK — rute network beda).
- Solusi: download woff2 via Google Fonts CSS API (curl OK), embed sebagai
  base64 data URI di `src/engine/fonts-local.ts`, loader `loadLocalFamily()`
  di `src/engine/fonts.ts` pakai `new FontFace(family, url(data:...))`.
- Berlaku untuk pair 'archivo' (Archivo Black / Inter Tight / DM Mono).
  Pair lain masih pakai CDN — kalau kena masalah yang sama, ulangi pola ini.

## 2026-10-14 — TTS Indonesia + caption (warisan pipeline Pillow, tetap valid)
- TTS: `tts` CLI (Meta AI voice, --language id), ~2,5 kata/detik. Tidak ada
  word timestamp → caption pakai timing proporsional huruf + silencedetect.
- ffmpeg drawtext WAJIB `textfile=` (teks inline pecah kalau ada ':').
- Audio: 48kHz stereo, loudnorm -14 LUFS, true peak -1dB.
- faster-whisper TIDAK bisa (model download diblokir proxy). Jangan coba lagi.
- /tmp cuma 512MB tmpfs → kerja di ~/workspace.

## 2026-10-14 — Brand @ngoprek.dev (kunci visual)
- Palet: #0A0A0A / #141414 / #E63946 / #FFFFFF. Oranye DILARANG (alay).
- Font brand: Inter Bold/Regular di ~/workspace/ngoprek-brand/fonts/.
- Postingan: JANGAN PERNAH centang label "AI content" / "Made with AI".
- Pronunciation TTS: cursor→"Kursor", windsurf→"Wind-serf", repo→"ripo"
  (~/workspace/ngoprek-brand/pronunciation.json, 27 entry).
