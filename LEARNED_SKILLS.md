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

## 2026-10-15 — REFERENSI MOTION: @sipalinglogistic "GUDANG TANPA RAK" (cross-dock explainer, 74 dtk, 9:16)
- Sumber: video referensi dari Zenn (di-upload ke media_library, 74 dtk).
- Inti: explainer satu konsep (cross-dock) dengan sistem visual ketat — bg hitam,
  krem + oranye sebagai warna utama, merah/hijau/biru sebagai warna data,
  tipografi condensed raksasa, ilustrasi isometric wireframe.
- TEKNIK YANG KITA BELUM PUNYA (gap, layak diadopsi):
  1. Color-bar wipe transition — 3 bar vertikal (merah/hijau/biru) wipe full-screen
     antar scene (transisi di dtk 3-4). Beda dari whip-pan kita; lebih punchy.
  2. Staggered letter entrance — judul raksasa masuk huruf per huruf dengan stagger
     (CROSS/DOCK). Kita baru word-level di caption.
  3. Count-up numbers — angka 0→18, 0→20 animasi naik. Widget stat kita statis.
  4. Running clock HUD — "JAM 07:02→07:10" jalan selama adegan (efek time-lapse).
     Cocok untuk konten tutorial/workflow.
  5. Stamp badges — badge "RAK ✕" / "PUT-AWAY ✕" / "DOCK MACET ✕" menghantam masuk
     kayak stempel karet. Device penekanan yang kuat.
  6. Segmented progress bar + counter sinkron — "PALET TERMUAT 9/18→18/18" dengan
     bar segmen warna-warni terisi bertahap.
  7. Radial timer — lingkaran 0→20 MENIT untuk durasi/rata-rata.
  8. Legend color-coding — chip "TOKO 1/2/3" + warna atas = tujuan; sistem kode
     warna data dengan legenda.
  9. Bar meters + panah arah — "STOK ↓ / BIAYA SIMPAN ↓ / WAKTU KIRIM ↓",
     bar horizontal menyusut dengan panah.
  10. Failure state — visual "kalau gagal": stamp + flash merah + timeline telat.
      Beat "syarat/risiko" yang dramatis.
  11. Kicker labels — label kecil oranye di atas judul ("01 — 07:00 PAGI",
      "KODE WARNA", "DWELL TIME", "KENAPA DIPAKAI"). Pola widget baru.
  12. Perbandingan split + counter — "GUDANG BIASA: HARI KE-15" vs
      "CROSS-DOCK: ±20 MENIT", dua kolom dengan counter jalan.
- KOMBINASI ADOPSI untuk @ngoprek.dev (dipetakan ke katalog widget):
  A. "Stat punch": kicker + judul stagger + count-up + bar meter → untuk klaim
     angka tool (mis. "10x lebih cepat", "0 config").
  B. "Process explainer": clock HUD + ilustrasi line/isometric + label langkah +
     progress bar → untuk tutorial/workflow (git bisect, CI, dsb).
  C. "Versus upgrade": kolom perbandingan + stamp ✕/✓ + counter → untuk format
     versus/1v1.
  D. "Transition pack": color-bar wipe + varian whip-pan per beat (pakai palet
     4 warna yang baru dibuka).
  E. "Data flow": legend kode warna + alur animasi (paket bergerak di jalur) →
     untuk menjelaskan aliran data (request flow, git branching, dsb).
- Status: analisis + daftar adopsi. Eksekusi = tambah widget baru ke engine
  Ngoprek.tsx (countup, clock, stamp, progress, radial, legend, bars-animated,
  kicker-headline, compare) — perlu persetujuan Zenn sebelum ubah pipeline utama.

## 2026-10-15 — REFERENSI MOTION: video engineering "Anthropic Bikin Version Control Sendiri / Scion" (13 slide, 9:16, dari blog "Redesigning Source Control, Okt 2026")
- Sumber: video referensi kedua dari Zenn (di-upload ke media_library) — frame per frame sudah dibedah.
- Inti: explainer engineering dengan sistem visual "slide deck sinematik" — bg krem terang, tipografi serif/sans raksasa, kartu putih, aksen bata/oranye + hijau. Beda total dari gaya gelap @ngoprek.dev, tapi ARSITEKTUR LAYOUT-nya yang kita adopsi (bukan warnanya).
- TEKNIK LAYOUT & STORYTELLING (gap, layak diadopsi):
  1. Top bar + slide indicator — header tipis paling atas: topik di kiri ("Anthropic · Source Control"), indikator "01 / 13" + progress fill di kanan. Penonton tahu struktur & posisi.
  2. Dynamic top headline — headline raksasa di ATAS kartu (bukan subtitle bawah), kata muncul satu per satu mengikuti narasi, kata kunci diberi warna aksen. Gantiin kotak subtitle bawah yang bikin fokus pecah.
  3. Kicker pill badge — pill kecil di atas judul ("Anthropic · Engineering") untuk konteks instan tiap beat.
  4. Git branching tree — garis-garis cabang dari node-node menyatu ke satu titik tengah (box "git"), titik node berjalan di sepanjang garis. Untuk visualisasi merge/branch.
  5. Count-up agresif — 900→2.500→30.401, ~25→200x, 0%→25%, semua angka di-count-up, bukan statis.
  6. Bar chart growth — bar Mei→Sep tumbuh bertahap mengikuti narasi.
  7. Bento grid 2x2 stat — 4 kartu statistik muncul bertahap (History ~100 GB / Working Copy 25 GB+ / Checkout CI ~25 Jt / Mirror Git Ratusan), tiap kartu: label kecil + angka count-up + unit + sublabel.
  8. Split comparison 2 kolom — "Git Mengira: Offline" (ikon laptop, wifi disilang) vs "Kenyataannya: Online" (grid kotak agent). Asumsi lama vs realita.
  9. Numbered list reveal — daftar prinsip 01–05 muncul satu per satu.
  10. Layered node diagram — alur vertikal Client → scion daemon → scion cache → scion server → (Spanner | Cloud Storage), panah penghubung + badge metrik di samping ("99%+ Hit").
  11. Horizontal comparison bar — bar berdampingan dalam satu kartu (git 87 dtk vs scn 3,5 dtk; git 7,1 mnt vs scn 5,1 dtk).
  12. Visual unit counter (dot/block array) — di bawah angka "7 Orang" ada 7 titik, "5 Bulan" ada 5 blok, menyala berurutan.
  13. Strikethrough concept replacement — teks konsep lama dicoret horizontal ("Nulis Kode") lalu poin baru muncul (Review, Deploy).
  14. Stamp badge — stempel "Nggak" menghantam di kartu "Bilang Nggak Ke Claude".
  15. 6-grid system monitor — 6 kotak (daemon, cache, server, ingest, spanner, storage) + titik hijau/abu-abu + garis heartbeat animasi.
  16. Pipeline flow — Merge → Test → Deploy dengan pill "Konflik Dibereskan Claude".
  17. Timeline slider — garis 0–12 bln dengan blok rentang estimasi (6–12 bln).
  18. Source footer — "Sumber · ..." kecil di bawah tiap slide (kredibilitas, cocok dengan claim_log.json kita).
- ADAPTASI KE IDENTITAS @ngoprek.dev (wajib, bukan opsional):
  - JANGAN tiru palet krem/oranye video ini. Pakai palet brand: hitam #0A0A0A, putih, merah #E63946 (+ krem terang seperlunya sebagai bg alternatif).
  - Color-bar wipe pakai palet brand (merah menyala / hitam / putih), BUKAN merah-hijau-biru mentah.
  - Oranye TETAP DILARANG (aturan permanen 2026-10-14). Aksen hijau terminal & hazard stripe merah-hitam boleh dipakai sebagai ciri khas.
- SCENE PRESETS BARU (gabungan kedua referensi, bisa dipanggil per konten):
  F. "Architecture Deep-Dive": top headline + layered node diagram / git tree + badge persentase (99%+) → bedah cara kerja sistem/backend.
  G. "System Health / Benchmark Grid": bento 2x2 atau 6-grid heartbeat + horizontal speed bar + count-up → pamer performa / resource usage.
  H. "Myth vs Reality (Paradigm Shift)": split 2 kolom + stamp ✕/✓ + strikethrough → patahin kebiasaan coding lama.
  (A–E dari video logistik tetap berlaku: Stat punch, Process explainer, Versus upgrade, Transition pack, Data flow.)
- Status: 2026-10-15 — dicatat atas perintah Zenn ("gabungin kedua referensi, eksekusi setelah render Ollama kelar"). Render Ollama SELESAI 2026-10-15 ~07:54 WIB → eksekusi widget engine jalan sekarang.
