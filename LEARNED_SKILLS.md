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

## 2026-10-14 — RISET: Remocn (350+ komponen video copy-paste utk Remotion)
- Sumber: browsing web 2026-10-14 (next.jqueryscript.net, Product Hunt).
- Inti: library komponen Remotion siap pakai — Typography (Typewriter,
  Matrix Decode, Marquee), Transitions (Whip Pan, Zoom Blur, Wave Wipe),
  Shaders (Mesh Gradient, God Rays), UI Blocks (Terminal Simulator,
  Glass Code Block, Animated Line Chart), AI Demos (Claude Chat, Claude Code),
  Captions (Karaoke, Word Pop, Highlight Box), Effects (Confetti, VHS, CRT).
- Kenapa penting: semua yang user minta (terminal typing, caption pill,
  transisi whip) SUDAH ADA jadi komponen — tidak perlu bikin dari nol.
- Status: BELUM diinstall. Menunggu izin user (protokol Usul & Minta Izin).
- Link: https://next.jqueryscript.net/shadcn-ui/remocn-video-components-remotion/

## 2026-10-14 — RISET: Lanskap AI coding tools (Okt 2026)
- Sumber: DEV.to, saascity.io, mameara.com (berita minggu ini).
- Taksonomi: (1) prompt-to-app builders: Lovable, Bolt.new, Replit, v0, Base44;
  (2) AI code editors: Cursor, Windsurf; (3) terminal/repo agents:
  Claude Code, OpenAI Codex.
- Pemain baru: ByteDance Trae IDE (gratis, Builder Mode), Devin Desktop
  (ex-Windsurf, di bawah Cognition), Google Antigravity (agent-first).
- Topik video potensial: "Cursor vs Trae" (pemain baru ByteDance),
  "3 kelas AI coding tools" (taxonomy di atas — belum banyak yang bahas).

## 2026-10-14 — Remotion Opus render 1080x1920@60fps OOM di VM 2-core
- Render `cursor-vs-windsurf` (engine flythrough, 22 dtk VO, 60fps) gagal 2x: Chrome OOM di ~50% (concurrency 2) dan ~60% (concurrency 1). Tidak ada mp4 yang jadi.
- Pelajaran: jangan rerun render full dengan setting sama setelah OOM — itu cara bodoh. Jalur pintar: (1) preview 540x960 dulu buat validasi, (2) render per-scene dengan checkpoint + concat (tahan VM mati/OOM), (3) TMPDIR diarahkan ke ~/workspace/tmp (bukan /tmp 512MB tmpfs).
- Font Google Fonts CDN gagal di Chromium VM (ERR_CONNECTION_RESET) — embed font lokal base64 ke engine (src/engine/fonts.ts, fonts-local.ts) terbukti jalan.

## 2026-10-14 — Bedah 3 referensi Remotion (style guide ngoprek.dev)
- LRA 1.1–1.5 LU (kompresi agresif) = kunci audio padat di HP; music bed kontinu (-18dB); target baru LRA 1.0–1.5.
- Full-bleed color zones (100% layar terisi), micro-beat tiap ~1 detik, transisi = gerakan kontinu (2 hard cut/16s).
- Elemen berkarakter (maskot/stamp/badge) + chaptering "BAB xx" untuk topik berat.
- Oranye referensi → merah brand #E63946 (lock oranye tetap berlaku).
- Detail: REFERENCE-STYLE-GUIDE.md

## [TEMUAN] 2026-10-14 — alimiter auto-level nge-boost sinyal
[MASALAH LAMA] True Peak selalu 0.0 dBFS walau chain sudah `alimiter=limit=0.92`.
[KEROKOKAN] ffmpeg `alimiter` punya `level` (auto level) default **true** — dia nge-gain UP
dulu sebelum nge-limit. Terbukti: file -1.0dB -> alimiter(limit=0.89) -> 0.0dB.
[SOLUSI] Selalu pakai `alimiter=limit=X:level=disabled`. Hasil: I=-14.0, LRA=1.2-1.7, TP=-1.0 pas.
[ATURAN] Jangan pernah pakai alimiter tanpa `level=disabled` di pipeline ini.

## [TEMUAN] 2026-10-14 — mix-bus compressor kunci LRA rendah
Vokal ratio=6 saja tidak cukup: jeda antar VO bikin LRA 3.7. Tambah
`acompressor=threshold=-22dB:ratio=5:attack=8:release=180:makeup=6dB` di MIX BUS
(setelah amix, sebelum loudnorm) + bed -15dB (bukan -18dB) -> LRA 1.2-1.7.
SFX gain 1.0 terlalu hot (LRA 1.9, TP -0.9); gain 0.5 -> LRA 1.7, TP -1.0.

## [TEMUAN] 2026-10-14 — cek safe-area QA harus deteksi putih, bukan merah
Cek lama pakai max red channel >200 — mustahil lolos di Zona 1 (full red #E63946,
r=230 di semua pixel). Fix: `min(r,g,b) > 200` (near-white).

## 2026-10-15 — RISET: AI coding agents +30% kode, TAPI software tidak nambah (Harvard, 700+ firms)
- Sumber: tech-insider.org (liputan studi Harvard, publikasi berita 2026-10-12)
  https://tech-insider.org/ai-coding-agents-30-percent-more-code-not-software-2026/
- Inti: dev pakai AI coding agents nulis ~30% lebih banyak kode, commit +20%,
  PR +23% — TAPI penyelesaian issue/epic tidak naik signifikan. Review manusia
  jadi bottleneck yang tidak ikut melebar.
- Cara pakai: topik carousel kuat untuk pilar AI/tools @ngoprek.dev — angle
  "AI bikin lu nulis kode lebih banyak, tapi bukan berarti kerjaan selesai".
  Angka spesifik (30/20/23%) cocok untuk slide data-driven (format VERDICT).
- Claim log (kalau dipakai di konten): "Harvard study >700 firms, dikutip
  tech-insider.org 2026-10-12". Butuh claim_log.json sebelum konten.

## 2026-10-15 — RISET: Pi 1.0 (Earendil, MIT) + rilis coding tools 2 minggu terakhir
- Sumber: github.com/earendil-works/pi/blob/HEAD/packages/coding-agent/CHANGELOG.md
  (release 2026-10-01); tech-insider.org/pi-1-0-coding-agent-tokens-cut-2026/
- Inti: Pi 1.0 — "leaner codemode" ~40% lebih sedikit prompt token
  (5.300 -> ~3.300 token/request di contoh), native MCP, deferred tool loading,
  cache warming Anthropic. Rilis seangkatan: OpenCode v2.0.28, Copilot +
  Gemini 4 Argon (GA), Claude Code 2.1.296 (subagent compaction independen),
  GPT-6.1 Sol (harga ~1/5 Astra).
- Cara pakai: materi topik "AI coding tools" untuk pilar AI — Pi 1.0 MIT =
  alternatif open buat dev Indonesia yang budget terbatas (sambung ke
  topik 2026-10-14: "3 kelas AI coding tools").
- Catatan: klaim 40% = di SETUP codemode default, bukan janji potong tagihan
  40% tiap task. Jangan dibesar-besarkan.

## 2026-10-15 — RISET: scene rhythm skill Remotion publik (banding ke pipeline kita)
- Sumber: github.com/haidrrrry/claude-remotion-skill/blob/HEAD/remotion-motion-graphics/SKILL.md;
  github.com/ahmedflow/motion-graphics (motion-graphics-remotion SKILL.md)
- Inti: pola publik — "HIT -> hold (15-20 frame) -> build -> HIT";
  "something must move in the first 15 frames"; "never >90 frames tanpa
  elemen visual baru"; komponen reusable (BgMesh, Grade, Grain, Vignette,
  KenBurns, WordReveal, Stagger, Counter).
- Banding jujur ke pipeline kita: ritme kita (1 perubahan visual/1.0-1.5 dtk
  = tiap 30-45 frame; hook tanpa dead space) SUDAH memenuhi/mendahului ini
  (threshold kita lebih ketat dari >90 frame mereka). Grain+vignette sudah
  kita pakai. TIDAK ada upgrade yang layak dari sini — konfirmasi bahwa
  kunci ritme kita sudah benar, bukan gap.
- Status: bukan usulan perubahan pipeline. Catat sebagai validasi.
