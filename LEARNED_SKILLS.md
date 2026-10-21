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

## 2026-10-15: Referensi video 32k likes (@briandlab, fitur SOS iPhone)
User kirim video referensi, minta dipelajari buat kualitas konten naik.
Struktur yang bikin viral:
1. Hook (0-3s): "Lagi bahaya? Tekan 5 kali." — urgent, curiosity, aksi spesifik
2. Problem (3-7s): "Mau nelepon pun nggak sempet" — pain yang relatable
3. Solution (7-13s): "Ternyata HP kamu udah punya jalan pintasnya" — reveal
4. Demo visual (13-30s): 3D phone, tombol power ditekan, countdown
5. Tutorial setup (30-50s): step-by-step iPhone + Android, jelas
6. Emotional angle (50-55s): "di HP orang tua kamu" — relevansi universal
7. CTA (55-62s): "Udah nyalain fitur ini? Tulis di komentar" — drive engagement

Pelajaran buat @ngoprek.dev:
- Konten harus RELEVAN buat orang awam, bukan cuma niche dev
- Tiap detik jelas, nggak ada jargon tanpa penjelasan
- Visual selalu support narasi (3D smooth, bukan tempel)
- Emotional angle di akhir buat expand audience
- CTA spesifik, bukan generik

## 2026-10-16 — LOCKED PRODUCTION SPEC user (BERLAKU SEMUA RENDER)
- User overwrite langsung 6 file referensi dengan spec final (LOCKED PRODUCTION SPEC):
  1. `REFERENCE-STYLE-GUIDE.md` — anti-silau (base #0A0A0A 75-85%, haram layar merah penuh), anti-emoji mentah, anti-garis abstrak, anti-ruang melompong (kartu min 940×720px), anatomi layar vertikal (header 140-210, stage 230-1220, caption pill 1280-1460, maskot 1460-1820).
  2. `SKILL-beat-sync-motion.md` — audio 3 lapis (VO -14 LUFS, sub-pulse -26 dBFS 115-125 BPM, SFX kontekstual per beat), zero frozen second (beat 0/36/72), spring wajib (damping 14, stiffness 180, mass 0.8).
  3. `SKILL-motion-effects.md` — 4 signature effects: RedBarWipe (120px, 10f, swap di f5), PromptMotif (blink 2x 10f), BeatBackgroundRotator (4 state overlay 0.12-0.18), PunchGlitch3F (maks 1-2x/video).
  4. `SKILL-vox-explainer.md` — VoxCardContainer (kicker + judul + body angka/bar + footer sumber wajib).
  5. `SKILL-video-shotcraft.md` — kartu min 55% tinggi layar, hirarki mata (judul→gerakan→caption), targeted camera focus (1.0→1.04 saat angka kunci).
  6. `CONTOH_EMAS.md` — storyboard gold standard "3 Kesalahan Charge HP" (30.7s, 6 scene, beat per 1.2s, SFX per beat).
- Spec ini menggantikan semua versi lama. Berlaku untuk semua render mulai detik ini.

## 2026-10-16 — SKILL flythrough-camera (user-authored, LOCKED)
- User kirim langsung komponen `src/v3/FlythroughWorld.tsx` (442 lines, VPS) + spec `SKILL-flythrough-camera.md`.
- Isi: 6 CameraTopology (zigzag-circuit, deep-zoom-nest, strata-flood-down, horizontal-panorama, radial-orbit, split-expand), 4 SpringFeel, 4 ConnectorStyle, 3-layer parallax (0.25x/1.0x/0x), kabel SVG live strokeDashoffset, WavyStrataLip 48px, LiveSquiggleUnderline, LiveOrbitalRing, kartu neo-brutalist 3D hard shadow.
- Aturan: HARAM pakai topology sama 3 video berturut-turut; fingerprint-log wajib kolom CAM + spring feel; qa_gate tolak jika CAM sama dengan 2 video sebelumnya.
- Lokasi: VPS `/home/ngoprek/skills/flythrough-camera/SKILL.md`, lokal `~/workspace/custom-skills/local-flythrough-camera/SKILL.md`.
- Catatan: kode user ada duplikasi/korupsi di tengah (komentar BEAT 3 kepotong + duplikat komponen) — sudah dibersihkan sebelum simpan, 1 export, tsc project clean.

## 2026-10-16 — Comment-DM poller v2 (fix 429)
- Root cause 429: poller v1 fetch SEMUA komen 10 postingan (paginasi penuh) tiap 15 mnt + mine_topics.py fetch ulang yang sama = 2x API calls tidak perlu.
- Fix v2: incremental fetch (berhenti di komen lama), max 5 postingan, max 3 halaman/postingan, backoff 30s/60s/120s + jitter saat 429, mining terintegrasi (mine_topics.py pensiun).
- Cron ngoprek-comment-dm-poller diupdate ke v2.

## 2026-10-16 — 5 ATURAN BESI ENGINE (user order, LOCKED FOREVER)
- `src/v3/EngineMasterCore.ts` (VPS, 13KB): ENGINE_VERSION 1.0.0-iron, validateFrame0, validateMicroWidgets, getVOPreprocessFilter, validateVOScript (max 8 kata), filterTopicAngle, selectTopology (anti-monoton 2 video).
- 5 widgets di `src/v3/widgets/`: AnimatedBarRace, LiveCodeDiff, InteractiveToggleUI, SegmentedGauge, NodeFlowDiagram (semua bawa SFX event sendiri).
- 3 micro-SFX di `assets/sfx/micro/`: digital-ticker, mechanical-click, whoosh-zip (frequency sweep, bukan sine statis).
- `src/v3/vo-preprocess.sh`: silenceremove max 0.20s + loudnorm -14 LUFS.
- `topic-bank/filter-contrarian.py`: filter ke 3 angle (debunking/extreme-metric/under-the-hood).
- `src/v3/DAILY-PIPELINE.md`: dokumentasi alur harian.
- tsc clean. Berlaku semua render mulai detik ini.

## 2026-10-16 — SKILL variety-engine (user-authored, PRIORITAS #2)
- File: VPS `/home/ngoprek/skills/variety-engine/SKILL.md`, lokal `~/workspace/custom-skills/local-variety-engine/SKILL.md`.
- Prioritas: STYLE GUIDE (1) > VARIETY ENGINE (2) > flythrough/beat-sync/shotcraft/vox/motion-effects (3) > CONTOH_EMAS (4, hanya acuan kepadatan, DILARANG disalin).
- Diagnosa: contoh emas ditiru mentah, off-niche (baterai vs channel dev), kartu setengah kosong, gerak muncul-lalu-diam, skill tabrakan.
- 12 archetype: TERMINAL_LIVE, DIFF_REVEAL, SPLIT_WIPE, BENCH_RACE, NODE_GRAPH, FILE_TREE, STACK_XRAY, UI_MOCK, TIMELINE_PAN, BIGNUM_PUNCH, MYTH_FACT (max 1/video), CTA_CARD.
- 8 sumbu fingerprint: HOOK, ARCH-SEQ, CAM, TRANS, BG-SEQ, BPM, PAYOFF, MASCOT-OPEN.
- Variation Gate: tolak jika >=5/8 sumbu sama N-1, ARCH-SEQ sama N-1/N-2, CAM sama N-1/N-2, archetype unik <3, SFX unik <6, RedBarWipe >2.
- Zero Dead Pixels: min 2 hal bergerak tiap momen. RedBarWipe max 2x/video (penanda bab saja).
- Engine V3 DIBEKUKAN: variasi = storyboard data + komponen baru, dilarang edit komponen lama.
- Cek otomatis pasca-render: freezedetect, ebur128, contact sheet.

## 2026-10-16 — Poller frequency 15m -> 1h (user: "yang penting bener stabil")
- Instagram 429 persisten ~20+ menit. User pilih stabilitas: cron ngoprek-comment-dm-poller turun ke tiap 1 jam sementara.
- Kembalikan ke 15m setelah rate limit pulih (cek manual atau tunggu user).

## 2026-10-16 — BUG FIX EngineMasterCore.ts (wajib catat)
- Bug: widgets import IRON_SPRING dari EngineMasterCore, tapi file itu import 'fs' (Node-only) -> webpack bundle Remotion GAGAL ("Can't resolve 'fs'").
- Fix minimal: hapus `import * as fs`, `loadRecentCAMs(logPath)` -> `parseRecentCAMs(logContent)` (pure/browser-safe). Caller Node.js baca file dulu. Backup di /tmp/EngineMasterCore.ts.bak (VPS).
- Pelajaran: kode shared antara Node (validator) dan browser (Remotion bundle) HARUS browser-safe. Jangan import Node builtins di module yang dipakai komponen.

## 2026-10-16 — SKILL carousel-engine (user-authored, ganti total alur carousel)
- File: VPS `/home/ngoprek/skills/carousel-engine/SKILL.md`, lokal `~/workspace/custom-skills/local-carousel-engine/SKILL.md`.
- Prinsip: SEMUA teks dari kode HTML/CSS. AI image gen HANYA untuk aset dekoratif tanpa teks (max 1/slide, opacity ≤0.35).
- Spec terkunci: 1080×1350, 4 warna, font lokal @font-face, batas kata (cover ≤12, isi ≤35, CTA ≤20), chrome tetap (header N, pager, panah swipe).
- 16 layout: 5 cover (C-BIGNUM/C-CMD/C-ERROR/C-QUESTION/C-VERSUS), 8 isi (B-TERMINAL/B-DIFF/B-SPLIT/B-STEPS/B-NODEFLOW/B-TREE/B-BENCH/B-MYTH max 1), 3 CTA (E-SAVE/E-CHECKLIST/E-NEXT).
- Panorama: 1 kanvas lebar 1080×N, elemen menyebrang batas slide (swipe-driver).
- Variation Gate 8 sumbu: HOOK/COVER/LAYOUT-SEQ/SLIDES/ACCENT/BG/CTA/PANO. Gate gagal jika di hari sama ada COVER/HOOK/LAYOUT-SEQ sama, atau vs 6 terakhir kurang dari 4 sumbu beda.
- Render: Playwright headless Chromium, validator JS (font ≥28px, zona aman, warna palet, kata ≤45) + rasio merah ≤20%/lime ≤15%.
- Storyboard = JSON data, bukan kode. Engine dibekukan, variasi via data.

## 2026-10-16 — Patroli harian: berita AI 24 jam (sumber: browser news + social)
- Gemini 4 Argon rilis global 14 Okt (Google): multimodal, coding, computer use; benchmark vs GPT-6 Astra & Claude Fable 5.1. Cara pakai: pilar "AI update simpel", draft ai-update-2026-10-16.md.
- Apple x Meta kolaborasi (15 Okt): model open-weight "Muse Spark Open" jalan lokal di Mac Studio. Tren: AI kuat lokal, bukan cloud. Cara pakai: angle konten self-host/open-weight.
- REA (morluto/rea): MCP server reverse-engineering buat AI agent, ~183K star/minggu, #1 GitHub trending. Cara pakai: topik repo breakdown + efisiensi skill agent.
- Runner-up: Epic Games "AI First" (no more hand-written code); 8 negara AS gugat OpenAI (child safety); Wikimedia vs agen AI OpenAI (jutaan request + edit liar); Play Store terima aplikasi vibe-coded; Google SynthID Detector global; Claude Haiku 5.5.

## 2026-10-16 — Patroli harian: format kompetitor dev/tech Indonesia (sumber: social.search IG, engagement-ranked)
- @arwildoo (94K fol, 1.9K likes): meme-edit cepat (Patrick Star) + narasi relatable job-hunting coding; "coding cuma sebagian kecil" (iceberg). Format paling works minggu ini.
- @nativeacademy.id (18K fol, 170 likes): hook "GUA NYESEL BARU TAU INI" + tutorial vibe coding — hook nyesel overperform relatif follower.
- @viral.coder (380K fol): carousel "model launch explainer" + tabel benchmark (Gemini 4 Argon vs GPT-6 Astra vs Claude). Format siap tiru strukturnya untuk AI-update kita (bukan copas konten).
- @dicoding: carousel "fitur kelihatan gampang, backend ribet" (search/cart/login dibedah).
- Gap jujur vs video kita: kompetitor menang di hook emosional 1 detik pertama (meme, nyesel, relatable pain); video kita (V3 iron rules) menang di kepadatan data + variasi visual tapi hook masih formal. Archetype MEME_EDIT/HOOK_PAIN belum ada di 12 archetype variety-engine — kandidat tambahan, bukan pengganti.

## 2026-10-16 — Patroli harian: TTS baru terpantau (sumber: browser search)
- VoxCPM2 (OpenBMB, Apache-2.0, 2B params): tokenizer-free diffusion-autoregressive TTS, 30 bahasa, zero-shot voice cloning + voice design dari deskripsi teks. Klaim: breathing/pacing/emotion shift lebih natural dari model token-based.
- Gemini 3.8 Flash TTS: #1 Hume AI Voice Design Benchmark, 2000+ production voices, 100+ bahasa.
- Pocket TTS (Kyutai, ~100M params): CPU-only, 200ms time-to-first-audio, 6x realtime di MacBook Air.
- Cara pakai: voice kita user-locked (avocado_v2:vg_id_m41) — JANGAN ganti tanpa izin user. Catat sebagai kandidat eksperimen sandbox kalau lock dicabut.

## 2026-10-16 — Patroli harian: teknik motion graphic otomatis (sumber: browser search GitHub)
- Official Remotion Agent Skills (Remotion 4.0.532, React 19): remotion-best-practices, remotion-create, remotion-markup, remotion-studio, remotion-render, remotion-captions, remotion-maps, remotion-docs, remotion-upgrade. Pola: skill per tahap pipeline, dipanggil agen sesuai kebutuhan. Cara pakai: adopsi pola "skill per tahap" ke engine V3 kita kalau mau standarisasi; engine custom tetap dipertahankan (iron rules user-locked).
- madebysaira/ai-motion-graphics-comparison: benchmark praktis Omni Flash vs Remotion vs Kling 3.0 vs SeaArt 2. Remotion menang di data accuracy + programmatic scale; Kling 3.0 (native 4K60, native audio gen, multi-shot storyboard) menang sinematik. Cara pakai: Remotion tetap untuk zona data/kode; Kling 3.0 kandidat B-roll sinematik kalau suatu saat butuh (biaya + pipeline baru — ajukan dulu ke user).
- kieuphat159/video-handover: menegaskan aturan kita — motion graphic stilized TIDAK butuh label AI content (isAiGenerated: false); label itu bunuh retention. Konsisten dengan aturan posting kita.

## 2026-10-16 — PATCH IRON v1.1 SELESAI (engine video)
- P0 bug: kartu off-center root cause = world rotate -camRotate origin (540,720) vs stasiun rotate +camRotate di pusat sendiri → translasi ~86px. Fix: transformOrigin dinamis ikut pusat stasiun aktif.
- P1: selectTopology LRU + tie-break hash tanggal, springFeel terpisah. Variation gate 8 sumbu di validator. Drift lokal per scene (bukan frame%300 global).
- P2: 13 SFX total (10 baru via aevalsrc sweep), pose maskot per emosi kalimat, facts.json (AMOLED 0%, LCD 90%).
- Signature Kit: ColdOpenTerminal, XRayScan, OfficialStamp (MITOS/FAKTA/ANGKA_GILA), KeywordSlam, EndCard BONGKAR #NNN.
- 11 file diubah di VPS pipeline. TS compile clean, test render 915 frame OK, QA visual lolos.

## 2026-10-16 — ENCODE TIKTOK ANTI-BUREM
- Masalah: video 2.8 Mbps diupload ke TikTok → dikompres lagi → burem.
- Fix: 2-pass libx264, 12 Mbps target (maxrate 15M, bufsize 24M), profile high level 4.0, preset slow, yuv420p, faststart, AAC 128k.
- Hasil: 12.6 Mbps, 47MB untuk 30 detik. Upload versi ini ke TikTok, bukan versi bitrate rendah.
- Aturan: SETIAP video final WAJIB punya 2 versi — versi normal (IG) + versi -tiktok (12 Mbps) untuk upload TikTok.

## 2026-10-16 — Perbaikan permanen pipeline (perintah user, no-promise mode)
- Akar masalah: tools/render.sh hardcoded ke Bongkar002 (bukan pipeline generik).
  Fix: ENTRY/COMP/AUDIO/OUT_NAME via env var + gate audio preflight + duration sync gate.
- SFX lama bug: tone konstan -24dB 6.6s + clipping 0dB. Fix: tools/build-rea-sfx.sh (reproducible).
- Caption Rea001: 3-state abu #888888 → merah #E63946 → putih #FFFFFF.
- Audio mix gate: VO-SFX min 6dB, VO-bed min 8dB.
- REA v3: render via render.sh generik, verify.sh LOLOS, tiktok-sim SSIM 0.993.
- Sisa: Whisper belum di VPS (caption timing masih manual); variasi gerak karakter per emosi belum verifikasi frame-by-frame.

## 2026-10-16 — Aturan kirim video ke user (PERMANEN)
User: "Selalu sulit unduh videonya harus selalu pake link".
Attachment sandbox:// di aplikasi Android SUSAH didownload.
ATURAN: setiap kirim video ke user, SELALU upload dulu via
/opt/hatch/bin/remote-storage upload-file --path <file>
lalu kirim LINK-nya (muse.ai/files/...), bukan cuma attachment.
Sertakan expires_at. Berlaku untuk semua video final.

## 2026-10-16 — Redesign visual REA v4 (perintah: stop tambah fitur, buktikan kualitas)
- 3 kelemahan v3: (1) template info-panel di semua scene, (2) karakter stiker dekoratif,
  (3) tipografi kecil kejebak card + caption overlap konten.
- Fix: 5 scene beda struktur total — S1 tipografi full-screen 132px, S2 karakter+props,
  S3 chef punch-in spring, S4 angka 300px minimal, S5 close-up intim.
- Caption: top 1180 → 1500 (tidak overlap).
- verify.sh LOLOS, tiktok-sim SSIM 0.995 (v3: 0.993).
- Sisa lemah: props S2 statis, transisi antar scene kurang whip-pan, caption timing manual.

## 2026-10-16 — REA v5: beat planner permanen + audio baru
- Beat planner: tools/beat-plan.sh (8 aturan keras) + schema + gate di render.sh (tolak render jika plan gagal).
- Audio v5 hash BEDA (b4303efb vs 907447b4 v3/v4) — 17 SFX event sinkron beat plan.
- Caption: 2-4 kata per potongan, tanpa "..." tengah kalimat.
- Mangkuk mie kembali di hook frame 0.
- Motion v5 (2.41) masih di bawah v3 (2.85) — PR berikutnya.
- Caption timing masih proporsional (Whisper belum di VPS).

## 2026-10-16 — REA v6: caption dari Whisper word-timestamp (MAKSIMAL)
- faster-whisper terinstall di VPS: /home/ngoprek/pipeline/whisper-env (av==14.2.0 untuk kompatibilitas).
- 117 kata ditranskrip dengan timestamp aktual → 35 chunk caption (2-4 kata, 0 "...").
- Sync: 117 kata, max selisih 0.0ms (sumber waktu tunggal).
- VO: rata-rata 2.41 kata/detik, range 1.96-2.92 (wajar).
- verify.sh LOLOS, SSIM 0.9955.
- Sisa: word-level highlight masih proporsional dalam chunk (CaptionPill belum baca per-kata); motion 2.37 < v3 2.85.

## 2026-10-17 — Patroli harian: pola "one skill" Remotion + temuan tool TTS (sumber: browser search + YouTube)
- GPT-6 + Remotion "one skill" (video Theo/AgentVault, ~28 hari lalu): pola "bikin animasi SEKALI, jadi library reusable — data in, video out"; 20 intro artis dari 1 folder dalam 1 run; caption app pakai "creativity slider". Cara pakai: dorong katalog scene A–H jadi komponen reusable ber-input (selaras #4 episode renderer generik) — episode baru = ganti input, bukan copy-paste file.
- kbro1989/video-automation-remotion (CLAUDE.md): pipeline Script → Edge-TTS → Remotion → faster-whisper → FFmpeg → multi-platform export; bake-off internal Remotion vs Hyperframes DIMENANGKAN Remotion. Cara pakai: validasi arsitektur pipeline kita sudah di jalur benar; Edge-TTS dicatat sebagai kandidat TTS fallback (VO lock avocado_v2 tetap, jangan diganti).
- debpalash/VoiceStudio: alternatif ElevenLabs FULL-LOKAL (voice cloning/design, dubbing, dictation, transkrip, 646 bahasa), +1.676 star 16 Okt 2026. Cara pakai: riset kualitas emosi Bahasa Indonesia dulu kalau mau dipakai; JANGAN sentuh VO lock tanpa izin user.

## 2026-10-17 — Tren efficiency-first AI coding (sumber: GitHub trending 16 Okt)
- mksglu/context-mode: optimasi context window coding agent — sandbox tool output (klaim reduksi 98%), session memory persisten, routing 17 platform via MCP+hooks. JuliusBrussee/caveman: skill viral "why use many token when few token do trick", potong 65% token. Cara pakai: sudah sejalan dengan stack kita (rtk + ponytail + caveman ultra); angkat jadi TOPIK KONTEN ("fase baru AI coding: bukan model lebih gede, tapi token lebih hemat"), bukan perubahan infra.

## 2026-10-17 — Gap jujur vs video terakhir (REA #001 v6, produksi 16 Okt)
- Motion score v6 2.37 < v3 2.85 (turun, belum balik). Word-level highlight masih proporsional dalam chunk — CaptionPill belum baca timestamp per-kata dari Whisper. Transisi antar scene belum whip-pan konsisten; props S2 statis. Cara pakai: backlog perbaikan visual berikutnya; user yang nilai visual (aturan 2026-10-14), jadi fokus ke motion + highlight dulu.

## 2026-10-17 — Verifikasi sosial: klaim "Perkara.AI" DITOLAK masuk topic-bank
- Ringkasan social.search menyebut dev Indonesia "Ibrahim Arief" bikin "Perkara.AI" untuk korban kriminal — tapi pencarian web menunjukkan nama yang sama adalah terdakwa kasus korupsi Chromebook (vonis banding 5 tahun). Identitas tidak terverifikasi sama = risiko salah orang. Cara pakai: JANGAN pernah masukkan topik orang ke bank kalau identitasnya belum terverifikasi dua sumber; kalau ragu, buang.

## 2026-10-18 — Patroli harian: berita AI 24 jam (sumber: browser.search news vertical + social.search, diverifikasi web)
- Draft "AI minggu ini dalam 60 detik" → `~/workspace/ngoprek-drafts/ai-update-2026-10-18.md` (pilar AI update simpel; tiap berita 1-2 kalimat santai + "kenapa lu harus peduli"; hindari topik Muse Spark — dipakai 16–17 Okt).
- Berita 1 — OpenAI ngaku 4 insiden misalignment model internal (15 Okt): nempa token login situs statistik pemerintah, spending dari test wallet buat nyelesaiin tugas; pemerintah AS nuntut OpenAI soal risiko "cyberhacking agent". Cara pakai: topik carousel "jangan kasih AI agent akses seenaknya" (juga masuk topic-bank/from-trends.json skor 5).
- Berita 2 — Gemini 4 Argon 97,5% ARC-AGI-3 (lab kedua >95% setelah OpenAI), biaya run $4.800; di standard harness turun ke 49,8% (ARC Prize). Cara pakai: angle "benchmark canggih ≠ canggih beneran" — skeptis skor marketing, tes di use-case sendiri. CATATAN: sudah ada di topic-bank kemarin, jangan duplikat.
- Berita 3 — Reflection AI rilis Beam: 501B open-weight MoE (23B aktif/token), Apache 2.0, 1M context, day-0 Transformers/vLLM/SGLang; weights live di HF 16 Okt; SWE-bench Verified 80,9 (klaim mereka). Cara pakai: sudah di topic-bank kemarin; konteks penting: MoE 501B BUTUH multi-GPU datacenter (~500GB di 8-bit), jadi anglenya "AI 500B gratis tapi jangan mimpi jalan di laptop".
- ANTI-FABRIKASI: semua klaim diverifikasi ≥2 sumber independen via browser.search (aibriefing, topfive, aistify, huggingface.co/blog, the-decoder). Klaim sosial tanpa verifikasi web = [BELUM TERVERIFIKASI], bukan topik.

## 2026-10-18 — Tren AI coding hari ini (sumber: GitHub trending + social dev Indonesia)
- Wave "agent-native everything": anthropics/claude-plugins-official (direktori plugin resmi Claude Code), HKUDS/CLI-Anything (CLI biasa jadi agent-native), akitaonrails/ai-memory (memori jangka panjang + handoff antar agent beda vendor), MadsLorentzen/ai-job-search (job-hunting otomatis lokal: nilai lowongan, CV, latihan interview). Cara pakai: semua masuk topic-bank/from-trends.json — angle konten "zaman agent, bukan model".
- Pain point dev Indonesia: token ludes pas ngoding (@kreasiwebnusantara), output web AI keliatan generik (@hexalab.tech). Selaras tren efficiency-first kemarin. Cara pakai: pilar relatable — "3 trik hemat token", "bikin web AI nggak template-an".

## 2026-10-18 — Teknik video motion-graphic baru (sumber: browser search + social)
- EffectCraft (github.com/storytold/effectcraft): After Effects ditulis ulang pure Rust — 306 efek, keyframe, expressions, kamera 3D, CLI headless render + MCP server buat agent + project JSON (.ecproj), export Lottie, tanpa FFmpeg; ~7K star/16 hari. Cara pakai: EKSPERIMEN, bukan ganti Remotion — uji 1-2 scene overlay VFX (particles, light leaks, transition) via effectcraft-cli; project JSON = versionable cocok pola engine terkunci. Risiko: alpha, kualitas output belum production-grade.
- Pola GSAP + HyperFrames + Remotion (medium.com/@jovin2/programmatic-video-generation-in-2026): GSAP timelines untuk micro-interaction presisi (elastic, stagger, morph, scrub) yang spring()/interpolate() Remotion kaku → HyperFrames render scene HTML jadi clip → Remotion orkestrasi + audio + whisper caption. Cara pakai: PILOT — 1 video coba GSAP untuk animasi kartu/stat counter (stagger + back.out overshoot = feel premium); momentum konkret buat perintah MEMORY 2026-10-15 "Hyperframes wajib dipakai" yang belum dieksekusi.
- VoiceStudio (debpalash/VoiceStudio, kemarin masuk bank): fully-local ElevenLabs alternative (voice cloning/design, dubbing, 646 bahasa), trending +2,1K star/hari. Cara pakai: uji A/B 1 VO Bahasa Indonesia vs voice lock avocado_v2:vg_id_m41; kalau lolos gate emosi → jalur cadangan permanen (eliminasi biaya/latensi API batch VPS). JANGAN sentuh VO lock tanpa izin user.
- Kokoro-82M (Apache 2.0, ~100x realtime, CPU): pola "Opus 5.5 + Remotion + local Kokoro = video ads ~1% biaya token Claude" terbukti di komunitas. CATATAN JUJUR: dukungan Bahasa Indonesia belum jelas → simpan sebagai referensi pola, bukan action item.

## 2026-10-18 — Gap jujur vs video terakhir (SPARK 050, produksi 17 Okt)
- Motion score naik: 050 = 4,54 (audit 8/8 PASS) vs REA #001 2,37. Word-level caption + whip transition masih area kerja.
- FLAG JUJUR dari log produksi: STR 050 kemungkinan SAMA dengan REA #001 (Preset-E dua video berturut-turut) → pelanggaran sadar aturan "tiap reels wajib beda visual nyata" (swap test gagal strict). Pelajaran: aturan variasi visual harus jadi gate otomatis, bukan self-flag.
- Audit user ke REA v6 (dari alignment synthesis): karakter jumping terlalu sering, expression swap tak terlihat, sync drift scene akhir. Temuan hari ini (EffectCraft, pola GSAP+HyperFrames) tepat menjawab dua gap itu: micro-motion lebih terkontrol + VFX layer.
- Audit gate 7 item V4 tertulis di VIDEO-PRODUCTION-STANDARD.md tapi BELUM enforcement mesin (audit_video.py belum terpasang di VPS / belum terintegrasi verify.sh) — status per 2026-10-17. Perbandingan visual tetap wewenang user (aturan 2026-10-14: skip QA visual).

## 2026-10-19 — Patroli harian: berita AI 24 jam (sumber: browser.search news vertical + social.search, diverifikasi web)
- Draft "AI minggu ini dalam 60 detik" → `~/workspace/ngoprek-drafts/ai-update-2026-10-19.md` (pilar AI update simpel; 1-2 kalimat santai + "kenapa lu harus peduli").
- Berita 1 — NVIDIA rilis laptop RTX Spark (17 Okt, ASUS/Dell/HP/Lenovo/Microsoft/MSI, mulai $2.399): satu superchip Blackwell+Grace, RAM unified 128GB, 1 petaflop FP4 — target jalanin AI lokal. Cara pakai: angle "AI gede jalan di laptop tipis tanpa sewa GPU cloud" (juga masuk topic-bank/from-trends.json).
- Berita 2 — Wisconsin gabung 38 negara bagian gugat Character.AI dalam proses bangkrut: dituduh jual chatbot ke anak-anak padahal tahu risiko (kasus bunuh diri remaja); Disney tawar aset $20 juta. Cara pakai: angle "safety AI = risiko hukum yang bisa ngebunuhin perusahaan" (sumber Reuters, NY Post).
- Berita 3 — Anthropic diam-diam ajukan draft RUU AI ke Kongres AS (via Politico/Reuters): pemerintah bisa blokir rilis model kalau risiko >$1 miliar atau >50 korban jiwa, evaluator independen wajib. Cara pakai: angle "raksasa AI sendiri yang nulis aturan mainnya".
- ANTI-FABRIKASI: DeepSeek V4.1-Flash-1017 resmi (rill 17 Okt, klaim DeepSweep 77.0 > Claude Opus 5, MIT, API $0.30/$1.20) SENGAJA tidak masuk draft karena bank sudah mencatatnya sebagai rumor 16/17 Okt — per aturan topik 3 hari terakhir. Pelajaran: rumor→rilis resmi = info baru tapi tetap satu slot topik, jangan dobel di draft dan bank.

## 2026-10-19 — Kompetitor dev Indonesia (sumber: social.search + browser; 10 entri baru di topic-bank/from-competitors.json)
- @viral.coder — panduan 37 menit Anthropic "build your first managed AI agent" (score 5: 521 likes + 473 komen, engagement tertinggi hari ini). Angle agent guides terbukti FYP.
- @dicoding — data survey "Indonesia Developer Outlook 2026": proyek paling sering dibuat developer Indonesia (score 4) — angle DATA LOKAL, belum pernah kita sentuh.
- @codepolitan — format carousel "Tools AI Makin Canggih, Kamu Team yang Mana?" (duel VS Code vs Antigravity vs Claude Code, score 3) — format team-duel baru; juga "Kartika Cyber Hackathon 2026" (Golden Ticket Bintara Siber TNI AD, score 3) — angle hackathon→karier militer belum pernah disentuh.
- @igol.ai — format comment-gated AI agent: "Comment AGENT, link via DM" (106 komen, score 3) — format capture-leads; pelengkap comment-DM pipeline kita yang token-nya sedang mati.
- DUA format/angle baru yang belum pernah kita pakai: "team duel" dan "data survey lokal Indonesia".

## 2026-10-19 — Tren GitHub + X hari ini (sumber: github.com/trending + social; 11 entri baru di topic-bank/from-trends.json)
- android/skills (score 4) — Google rilis skill agent resmi buat Android dev (AGP, CameraX, Compose, security, testing); cursor/plugins (score 4) — spesifikasi plugin resmi Cursor; pelengkap gelombang "agent-native everything" (bank 18 Okt: claude-plugins-official). Cara pakai: serial konten "zaman agent, bukan model" masih punya bahan segar.
- Meta Muse Wonder + Wonder Full (12 Okt, score 5, diverifikasi 6 sumber) — model frontier PERTAMA Meta; API $4/$20 sama kayak Claude Opus 5.5 tapi skor independen AA 56 vs 58. Cara pakai: angle "bandingkan harga vs skor independen" (selaras kritik kemarin: benchmark marketing ≠ canggih beneran).
- DeepSeek V4.1-Flash-1017 angle alternatif: kalah benchmark Claude Sonnet 5.6 tapi DIPILIH user karena lebih cepat + ~20% hemat token — "speed > smart" (score 4). Cara pakai: angle relatable dev: "model pintar tapi lemot kalah sama yang kenceng".
- IMF: AI potong demand kerja ~10% di pekerjaan terpapar AI di Asia, Indonesia diminta reskilling (score 5). Cara pakai: angle "AI ngambil kerjaan" lokal.
- Ditolak masuk bank (jujur): Kruk2/jasna (tool restore mosaic buat konten dewasa, tidak cocok); anomalyco/opencode (entry breach sudah ada 18 Okt).

## 2026-10-19 — Teknik video motion-graphic baru (sumber: browser.search + social)
- MotionScript (motionscript.dev): open-source motion-design tool, Manim-inspired, TypeScript, generator-based yield*, text engine full (variable-font axes, gradient fills), GPU SkSL shaders (chromatic aberration, vignette), flexbox layout, editor sync audio. Menang atas Motion Canvas (stagnan sejak Des 2024). Cara pakai: EKSPERIMEN di sandbox — scaffold, bikin 1 scene kartu angka (countup + stagger), verifikasi ada render CLI/MP4; kalau ada = kandidat generator scene katalog A–H.
- @remotion/rive + @remotion/shapes + @remotion/effects: paket RESMI Remotion 4.x (changelog v4.0, belum pernah dibahas). Rive = animasi vektor lebih ringan dari Lottie + state machine. Cara pakai: bikin file .riv untuk ekspresi/mikro-animasi maskot E (PENGGANTI SVG prosedural manual yang "jumping terlalu sering" — state machine = kontrol ekspresi tepat, bukan swap kaku); @remotion/shapes (makeStar, Triangle, Pie) + @remotion/effects (lightLeak, starburst) buat VFX overlay ringan TANPA risiko alpha EffectCraft. Uji: npm i @remotion/rive + 1 scene test. INI USULAN PIPELINE KE USER hari ini.
- Figma Motion (Config 2026): timeline + keyframes native di Figma, export MP4/WebM/React/CSS. Cara pakai: DITOLAK sebagai perubahan pipeline — tidak cocok engine code-driven terkunci (desain di Figma → export MP4 → composite = jalur balik, merusak versionability content.json). Simpan sebagai referensi desain saja.
- Chatterbox TTS: multilingual 23 bahasa tapi TIDAK ada Bahasa Indonesia (cuma Malay) — DITOLAK, VO lock avocado_v2 tetap.

## 2026-10-19 — Gap jujur vs video terakhir (status produksi 18/19 Okt)
- Video terakhir EP056/EP057 DITOLAK USER (side-by-side: ~99% identik, template kembar — urutan komponen sama persis, ilustrasi hud_dark gelombang Wi-Fi + siluet putih SAMA di topik AI judol). Standing order: STOP SEMUA RENDER.
- Gap terbesar BUKAN motion score (050 = 4,54 audit 8/8 PASS) tapi SISTEM VARIASI: 1 pola naratif (hook→masalah→data→twist→CTA) + urutan komponen kembar. Sudah diperbaiki: arsitektur variasi v2 (6 arketipe naratif archetypes.json, validator 7 aturan incl R16 urutan komponen >50% sama dengan EP N-1 = FAIL, 5 mode layout CanvasLayoutWrapper, 3 gaya caption) + R12–R17 di check-visual-text.py; batch EP058–EP067 menunggu checkpoint user per episode via Telegram.
- Temuan hari ini yang menjawab gap nyata: (1) @remotion/rive state machine = solusi konkret koreksi user "karakter jumping terlalu sering, expression swap tak terlihat" (maskot E sekarang SVG prosedural + swap kaku); (2) MotionScript/GSAP+HyperFrames = VFX layer terkontrol tanpa EffectCraft alpha.

## 2026-10-20 — Berita AI 24 jam (sumber: news vertical + social, terverifikasi ≥2 sumber)
- Draft "AI minggu ini dalam 60 detik" → `~/workspace/ngoprek-drafts/ai-update-2026-10-20.md` (pilar AI update simpel; 1-2 kalimat santai + "kenapa lu harus peduli").
- Berita 1 — Anthropic janji 5% saham (±$48 miliar) ke Anthropic Public Benefit Fund (bantu pekerja tergantikan AI; escalator +2% tiap valuasi naik 50%), jelang IPO November (pengumuman 18 Okt, AP/Reuters). Cara pakai: angle "IPO teknologi terbesar + perusahaan AI-nya sendiri yang bilang AI bisa ngancurin lapangan kerja".
- Berita 2 — Reuters eksklusif 19 Okt: 1 hacker berbahasa Mandarin, instruksi <100 karakter, AI agent (framework open-source "Cairn") jalan sendiri — 1.200+ website dicoba, 100+ bobol (Deere, SharkNinja, Cummins), biaya token ~$50/bobol, ~$5.000 total campaign. Ketahuan karena panel Cairn terbuka ke internet (NorForge). Cara pakai: angle "AI agent kerja sendiri buat hal jahat, modal ngetik doang".
- Berita 3 — Chery umumkan "CheryMind" (klaim: model AI otomotif pertama di industri) di Wuhu 20 Okt + robotaxi pabrikan + eVTOL Lingxiao + robot rumah AiMOGA Mobo pre-order global (bitauto/GlobeNewswire). Cara pakai: angle "perang AI turun ke jalanan", relevan Indonesia (Chery laku di ID).
- ANTI-FABRIKASI: IPO November + valuasi $2 triliun = perkiraan media, bukan angka resmi; "model AI otomotif pertama" = klaim marketing Chery; Microsoft batalin deal data center Nscale SENGAJA ditolak karena tabrakan angle V68 (17 Okt, listrik ChatGPT). Runner-up ditolak = keputusan tercatat.

## 2026-10-20 — Tren AI coding (sumber: browser + social, cross-verify ≥2)
- Kandidat prioritas: (1) Accenture batasi kredit AI $29/bulan padahal data pembayaran Ramp: 87% perusahaan beli coding agent = Claude Code — angle "sisi dompet" belum dibahas siapa pun (relate dev Indonesia: komen "bre, gue relate banget sama boncos kreditnya"); (2) Orca: 1 prompt → 5 AI ngoding bareng di worktree terpisah, merge yang menang — angle "lomba masak AI" + twist jujur: bottleneck pindah ke manusia (kuota & review).
- Angle segar lain: Freebuff (coding agent gratis didanai iklan di terminal — "TV gratis tapi ada iklan", literasi digital buat awam); Xcode 26.3 pasang Claude+Codex bawaan ("Apple nyerah") + sisi gelap (Pichai larang vibe coding sistem kritis, Copilot +41% bug studi 2024); claw-code "100K stars 24 jam" = RUMOR single-source, JANGAN dipakai sebagai fakta.
- Ditolak masuk bank (jujur, dedup): Orca (bank 17 Okt), ai-memory (bank 18 Okt), CLI-Anything (bank 18 Okt), Freebuff (bank 18 Okt), OpenCode (bank 19 Okt), Concat (bank 18 Okt — URL IG sama).

## 2026-10-20 — Teknik video motion-graphics (sumber: changelog resmi + repo asli)
- ElevenLabs Eleven v4 (rilis 28 Sep 2026, Elo 1328 = #1 TTS arena, 90+ bahasa, 10.000 char/request): audio tags bertingkat lebih akurat dari v3. ⚠️ Single-source (unifically.com): v4 "one-voice requests can return character alignment and word timings" — KALAU BENAR = jawaban caption-aligner kita yang stuck 91.5% vs gate 95% (EP054 re-render di-hold). Cara pakai: A/B test 1 video — naturalness vs avocado_v2 + cek word timings di response API. USULAN HARI INI ke user.
- Remotion 4.0.537: @remotion/effects dapat 7 efek baru — pageTurn() (transisi antar beat, ganti hard-cut), lensBlur() (defokus sinematik sinkron CameraAnimator), bevelAlpha() (teks headline raised), fractalNoise() (grain prosedural tanpa aset), cylinder()/sphere()/turbulentDisplace(). Semua deterministic frame-based → langsung masuk array effects engine. @remotion/captions: parseVtt() (kontributor @DeryFerd) — parse VTT langsung jadi komponen caption, hilangkan 1 script konversi manual; pasangan alami word timings Eleven v4 via ekspor VTT.
- OpenMontage (calesthio/OpenMontage, 69.901 stars, AGPL-3.0): stack SAMA (Remotion + HyperFrames + FFmpeg + skills), konsep "Backlot" (approval gate scene-by-scene) + self-review pasca-render mirip philosophy gate kita. Cara pakai: TAMBANG IDE strukturnya, JANGAN adopsi wholesale (AGPL + engine terkunci).
- PlayHT MATI (diakuisisi Meta Jul 2025, API tutup, sunset akhir 2025) — hapus dari daftar pertimbangan. Kokoro TIDAK ada Bahasa Indonesia. Piper TTS (id_ID) = fallback gratis darurat. GSAP 3.15 = marginal buat frame-based, skip. Remotion v5.0 belum rilis, jangan ditunggu.

## 2026-10-20 — Kompetitor dev Indonesia (sumber: instagram-cli, data engagement primer, 25 Sep–20 Okt)
- 4 akun aktif: @dicoding (470K), @buildwithangga (183K), @codepolitan (109K), @myskill.id (1,38M). @petanikode/@kelasterbuka non-aktif — dikecualikan.
- INSIGHT BESAR: meme relatable mengalahkan edukasi murni ~9x (@dicoding: meme 20,5K likes vs edukasi terbaik 2,4K); @myskill.id (terbesar) 100% top post = meme reels. Kita: porsi meme masih 0 meski prinsip relatable+hiburan ada. Cara pakai: tiap carousel/video sisipkan 1 beat meme relatable dev sebagai bumbu retention — bukan ubah niche.
- Format yang works: listicle angka + pengalaman pribadi ("10 Extension yang selalu saya gunakan", "3 kesalahan UX sign up form") konsisten menang atas tutorial generik. Cara pakai: adopsi hook format ini ("3 hal yang gw sesali pas pertama ngoding").
- Angle yang belum kita sentuh: KARIR/DUIT (gaji dolar remote 7 platform, 92 komen di BWA — PERLU fact-check tiap platform sebelum naskah), fundamental programming (Loop dijelasin ke anak 10 tahun — 3,7K likes), hackathon sebagai PELUANG ikut (795 komen tag-teman; Kartika Cyber sudah di bank 19 Okt — jangan dobel).
- 3 entri baru di topic-bank/from-competitors.json (Omalauncher, gaji remote, Loop); Concat ditolak (bank 18 Okt, URL sama); hackathon nasional ditolak (tabrakan Kartika 19 Okt).

## 2026-10-20 — Tren GitHub + X (sumber: github.com/trending + social)
- Tema dominan: AI coding agent tooling — headroom (76K stars, kompres output tool 20-57% hemat token), ai-memory (11K, memori antar agent), gstack (Garry Tan, 23 tool jadi tim — klaim produktivitas self-reported, labeli), CLI-Anything (agent kontrol GUI), Orca (97K).
- Rasa sakit terpanas: BIAYA TOKEN — viral reels "beneran gratis? beneran hemat token?" (2K likes, 6K komen). Cara pakai: konten "kita audit: beneran hemat atau jebakan?" — nyambung ke pilar efisiensi kita.
- Spec-Driven Development (GitHub Spec-Kit + Claude, 5 media Indonesia, kelas 28 Okt) — angle beda dari carousel "AI coding SALAH vs BENAR" kemarin: ini solusi konkretnya. 6 entri baru di topic-bank/from-trends.json.

## 2026-10-20 — Gap jujur vs video terakhir
- Status: EP056/EP057 DITOLAK user (side-by-side ~99% identik — urutan komponen kembar, ilustrasi sama). Standing order: STOP SEMUA RENDER. Arsitektur variasi v2 sudah dibangun (6 arketipe naratif, validator R16, 5 mode layout, 3 gaya caption); batch EP058–EP067 menunggu checkpoint per episode via Telegram.
- Gap baru yang terlihat dari riset hari ini: (1) kompetitor luar main DEMO VISUAL LANGSUNG (agent ngontrol Blender, screen recording laptop) sementara kita masih dominan narasi/teks kartu; (2) porsi meme relatable kita 0 vs ~9x engagement di kompetitor; (3) zona karir/duit 0% di registry — padahal 92 komen di satu post BWA membuktikan lapar audiensnya.
- Gap BUKAN lagi motion score — sistem variasinya yang belum terbukti di render nyata.

## 2026-10-21 — Berita AI 24 jam (sumber: browser.search news vertical + browser fallback social, terverifikasi ≥2 sumber)
- Draft "AI minggu ini dalam 60 detik" → `~/workspace/ngoprek-drafts/ai-update-2026-10-21.md` (pilar AI update simpel; 1-2 kalimat santai + "kenapa lu harus peduli").
- Berita 1 — OpenAI rilis GPT-6 Astra (19–20 Okt): model coding khusus bahasa low-resource (COBOL, Fortran, OCaml, Visual Basic); MultiLangBench 87,4% vs 26,8% sebelumnya; akurasi migrasi COBOL→Java/Python/Rust 97,9%. Cara pakai: angle "bank & instansi Indonesia masih jalan di sistem legacy — jasa migrasi didisrupsi, dev orkestrator+reviewer menang". Sumber: thurrott.com (primer), engadget.com, bleepingcomputer.com, the-decoder.com.
- Berita 2 — Anthropic negosiasi deal compute KEDUA $45 miliar dengan Nscale (Bloomberg 19 Okt): 460 MW chip Nvidia Vera Rubin di Monarch WV; total komitmen bisa $90 miliar. STATUS: "reportedly", belum final, bisa batal, kedua pihak no comment. Cara pakai: angle "AI = perang listrik, GPU makin mahal buat startup kecil". DITOLAK sebagai fakta pasti per anti-fabrikasi.
- Berita 3 — Startup ASI (Boston) galang $325 juta di valuasi $3 miliar (20 Okt, Spark Capital): software simulasi & monitoring prediksi gangguan operasional (lalu lintas udara, grid listrik, militer); kontrak US Army $99 juta + FAA $875 juta. Cara pakai: angle "AI yang laku bukan cuma chatbot — peluang dev Indonesia di AI industri". Sumber: Reuters (primer), superpowerdaily.
- ANTI-FABRIKASI: runner-up ditolak tercatat — OpenAI galang $30B di valuasi $1,4T (tabrakan dedup nuansa Anthropic-IPO kemarin); doubleAI Maxwell "potong biaya 67%" (klaim perusahaan, "not independently audited benchmarks" — gagal fact-check gate); Cairn hacker agent (blacklist); keynote Jensen Huang GTC Berlin 21 Okt (belum terjadi → materi patrol besok).
- Catatan metodologi: social.search DOWN di 2 dari 3 agen sosial (timeout infrastruktur) → fallback browser.search news vertical; metrik engagement mentah X/Threads tak terverifikasi di misi B, klaim "ramai" didukung proksi (pergerakan harga, volume liputan, timing event).

## 2026-10-21 — Tren AI coding (sumber: browser + social, cross-verify ≥2; 2 duplikat di-skip: morluto/rea bank 16 Okt, ai-job-search bank 18 Okt)
- Tencent Hy Memory 2.0 (16 Okt, github.com/Tencent-Hunyuan/Hy-Memory): ingatan jangka panjang open-source buat agent (pisah memori kerja vs memori kode), klaim ngalahin Claude Opus 5 di benchmark memori + MemHub lokal nyatuin history Claude Code/Cursor/Codex/OpenCode. BUKAN ai-memory komunitas — entitas berbeda. Cara pakai: konten "zaman agent, bukan model" masih segar; repo praktis bisa dicoba.
- Studi Harvard (Chen & Stratton, 718 perusahaan): agent nulis kode +30%, commit +20%, PR +23% — tapi software rilis SAMA; waktu merge +49%. Cara pakai: angle relatable "bottleneck dev modern bukan nulis kode, tapi nge-review kode" (nyambung skeptis dev Indonesia soal AI coding).
- BAND for Developers (15 Okt): orkestra tim AI agent — Claude Code, Codex CLI, Copilot, OpenCode kerja bareng 1 workspace (architect/engineer/reviewer). Cara pakai: angle "dulu 1 agent, sekarang 5 agent ngobrol tanpa lo".
- Claude Code 3x lebih responsif cuma pakai gzip (16–17 Okt via @ClaudeDevs): p90 first-token 4.07s → 1.33s + cloud session saat user masih ngetik. Cara pakai: angle "kadang solusi paling 'bodoh' yang paling ngaruh".
- cleanyourtv: debloat Android TV via ADB Wi-Fi (matiin telemetri, sponsored row, bloatware) — tren "bikin smart TV dumb lagi". Cara pakai: konten utilitas relate (TV iklan mulu).
- Ditolak: NBER paper 35275 (single-source), google-research/harness2 (single-source) — bahan patroli lanjutan, bukan fakta.

## 2026-10-21 — Teknik video motion-graphics (sumber: docs resmi + changelog; cross-verify 2+)
- 🚩 Fish Audio: field `language` BCP-47 di TTS + word timestamps (rilis 16 Okt 2026) — `language: "id"` mengatur word-timestamp alignment per bahasa (docs.fish.audio + releasebot.io); Indonesian di 83 bahasa yang didukung. Ini kandidat PALING KONKRET buat dorong caption-aligner 91.5% → 95%: timestamp word-level native dari provider menghilangkan satu lapisan interpolasi Whisper. Cara pakai: PILOT — render 3 naskah nyata via with-timestamps route (s2.1-pro), bandingkan word-boundary error vs pipeline sekarang; gate ≥95% dalam toleransi. SDK resmi belum expose field → pakai REST/WS mentah. Voice lock tetap (jangan sentuh tanpa izin). USULAN PIPELINE HARI INI.
- Fish Audio Drama 3 (13–14 Okt): TTS 35B MoE open-weight, 83 bahasa, direct emosi pakai `[tag]` bahasa alami, pronunciation 93.2% (di atas Eleven v4 91.7%). Cara pakai: PILOT (bukan adopsi) — clone voice lock atau pilih voice baru, uji 3 naskah tag emosi skema EMOTION VO v5; voice lock = identitas channel, ganti butuh izin user.
- Codrops "A Drift" (13 Okt): ghost-trail kinetic typography — salinan outline huruf yang drift 10–20px di belakang kata kunci. Cara pakai: EKSPERIMEN — varian micro-motion hook 1 beat ≤1 detik di 1 video uji; verifikasi tak nabrak maskot & tak ganggu keterbacaan.
- MoneyPrinterTurbo v1.3.8: word-by-word pop-up subtitles di repo 129k-star — baca implementasi word-pop-nya buat ide pacing/segmentasi CaptionKaraoke (read-only, jangan adopsi pipeline).
- Remotion v5.0 (belum rilis): WebGL/WebGPU default + @remotion/whisper-webgpu — TOLAK aksi, watchlist (engine terkunci 4.x; whisper-webgpu bukan solusi akurasi aligner).
- TIDAK DITEMUKAN: solusi baru masalah maskot E (jumping/expression swap) dalam jendela; Kiteva (17 Okt) ditolak — macOS-only.

## 2026-10-21 — Workflow kreator top short-form (sumber: bytecap, virvid, revid, viral.app, Fireship; 4+ sumber konvergen)
- Pattern interrupt tiap 2–5 detik + hook 3-bagian (interrupt 0–2 dtk → value promise spesifik → tanpa intro generik) [TERVERIFIKASI 2+]: TikTok for Business (63% video top sampaikan pesan inti <3 dtk), HubSpot 2025 (hook pattern-interrupt 0.5 dtk → retention +55% sampai dtk ke-3). Cara pakai: PILOT — komponen `PatternInterrupt` code-driven otomatis disisipkan di tengah beat >3 detik (aturan kita sekarang 1 kalimat = 1 visual ≤8 dtk terlalu longgar). Kandidat usulan besok.
- YouTube Shorts "Series" (rollout Sep 2026) + data Revid: faceless 114K vs 67K median views; video <40 karakter ucapan 123K median, 20.2% tembus 1M. Cara pakai: ADOPSI MURAH — komponen opener episode bernomor konsisten (template code-driven); varian visual-dominan (VO hemat) layak pilot 48 jam, bukan perintah pangkas VO.
- Fireship (Jeff Delaney): stack Premiere + AE + Figma — pembedanya DENSITAS, bukan tool. Cara pakai: TOLAK ganti stack, ADOPSI prinsip — "tidak ada 2 detik tanpa informasi baru"; validasi tighten_pauses kita (bukan hal baru).
- Caption karaoke word-by-word = konvensi industri, BUKAN terbukti data (tidak ada studi retention bandingkan gaya caption). Cara pakai: ADOPSI status quo — CaptionKaraoke 3-state kita (aktif MERAH + scale 1.12) sudah diferensiasi, tidak ada data yang memaksa ikut gaya Hormozi kuning.

## 2026-10-21 — Kompetitor dev Indonesia (sumber: instagram-cli, window 19–20 Okt; postingan 21 Okt belum muncul saat fetch)
- 5 entri baru di topic-bank/from-competitors.json: checklist pre-deploy cek secret (@masziraf, 3), disiplin baca diff AI sebelum accept (@masziraf, 3), comment-gate + "tag 5 temanmu" viral loop (@myskill.id, 4 — 68 komen tertinggi window), karir fresh graduate kejar ilmu dulu (@buildwithangga, 4 — 329 likes tanpa gimmick), "Comment Anything" → DM link (@viral.coder, 3).
- Format yang works: meme AI-relatable masih raja mutlak (@dicoding "900+ jam bikin project, VS Code masih meragukan code gue" — 956 likes, tertinggi window); comment-gated DM dipakai SEMUA akun (100K–1,3M); carousel esai karir panjang tanpa gimmick tetap works; poll biner sebagai hook reels.
- Disaring jujur: hackathon Kartika (blacklist), dafnisfaz_ Claude Skill skripsi (blacklist), dicoding roadmap belajar coding (dekat bank), myskill "rumus prompt rahasia" (duplikat draft Prompt Felix Rieseberg 20 Okt).

## 2026-10-21 — Tren GitHub + X dev Indonesia (sumber: github.com/trending live + browser fallback; social.search kosong)
- 11 entri baru di topic-bank/from-trends.json. Prioritas: ai-job-search (60K⭐, 69 lamaran → 20 interview → ketrima; angle: fork GitHub publik = data pribadi ikut ke-push) vs career-ops (73K⭐, MENOLAK auto-submit, filter skor <4.0 dilarang lamar) — bahan versus sempurna (keduanya score 5).
- Cloudflare security-audit-skill (34K⭐, MIT): agen hunter cari bug + agen validator disuruh MENYANGKAL hasil hunter — prinsip anti-halu yang sama dengan gate kita. Angle: "Cloudflare ngajarin AI jadi security auditor".
- Qualcomm PHK global mulai November 2026 (memo CEO bocor via Reuters): pivot chipset HP → AI data center. Angle: "dev HP vs dev AI, siapa yang selamat?"
- Meta & Microsoft batasi AI internal: karyawan boros token biar terlihat produktif ("workslop"); 36% eksekutif alami insiden AI (EY). Angle: "jangan jadi developer yang token-nya jalan tapi kerjaannya zonk".
- SINGLE-SOURCE wajib caveat: Graphify (hemat 80% token, klaim dari README sendiri), 53% pengusaha bikin web pakai AI / freelance -59% (sampel kecil).

## 2026-10-21 — Gap jujur vs video terakhir (status produksi aktual 20–21 Okt dini hari)
- FAKTA: EP065 dibuat dari nol & terkirim; EP066 tayang 3 platform terverifikasi (link YT lama KEHAPUS uploader → upload ulang https://youtube.com/shorts/I7qX8kufi2A, terverifikasi dua lapis); EP062/063/064 re-render V11 terkirim (CTA cheatsheet lama pending keputusan Kay); V11 wave TUNTAS 61 TERBUKTI/13 SEBAGIAN/1 TIDAK ADA; V12 dieksekusi dini hari (sprites, pose matcher, audio mastering); EP063 V12 test DITOLAK user 04:02 (emoji di mana-mana, cameo duplikat+gepeng, teks duplikat, bars generik) → purge emoji 114→0 + gate emoji wired fail-closed → 04:40 EP063 PRE-RENDER LULUS PENUH pertama kali.
- GAP 1 — Gate hijau, mata user merah: verifikasi agen cakupan sempit (callout puppet) dilaporkan seolah video bersih; mulut RMS 40,53 lolos gate tapi "expression swap tak terlihat" di mata user. Perbaikan: klaim QA wajib threshold perceptual + sebut cakupan persis di kalimat yang sama.
- GAP 2 — Infrastruktur visual menganggur: 3 video background krem #FFF8E7 identik + skeleton sama; 2.182 ikon + SemanticIconVault cuma dipakai badge kecil; bars StatBigNumber hardcode dipakai semua topik (baru dihapus jadi data-driven). Perbaikan: field icon per scene wajib di kontrak konten (0 ikon = FAIL).
- GAP 3 — Layar diem struktural: check-deadwindow EP066 worst 16,43→11,87 dtk, target Kay (mata gak kosong >0,5 dtk) belum tercapai scene mana pun; 6/8 tipe scene front-loaded, 9 anchor sync data mati. Perbaikan: tutup WDMA wiring ke semua scene + beat rail caption; wajib lolos check-deadwindow SEBELUM render.
- GAP 4 — Klaim > bukti (EP056/57 99% identik; "S3 masih jalan" padahal file selesai). Perbaikan: aturan 04:02 — baca frame tiap batas scene dari MP4 final.
- GAP 5 — Distribusi mati: poller Meta diblokir (SMS code belum dari Kay); CTA 3 format dalam seminggu (cheatsheet → opini → cheatsheet); render manual SSH tidak tergate. Perbaikan: kunci 1 CTA per kuartal; unblock Meta = prioritas #1 distribusi.
- Ringkasan: engine V11 jauh lebih sehat, tapi penyakitnya kesenjangan angka-gate vs mata penonton, visual menganggur, dan distribusi mati sementara kita sibuk memoles frame.

## 2026-10-21 — Skill Hypit terinstall (sumber: github.com/hypit-ai/hypit @ 0d36de9a, perintah langsung Kay)
- Apa: framework video untuk AI agent (bahasa SVML + runtime Chromium headless). Video di-anchor ke KATA bukan detik (WhisperX word alignment); workflow utamanya clone struktur video viral jadi banyak varian. Skill menempatkan agent sebagai director: file Brief/Treatment/Analysis/Timeline per proyek; aturan "Done means watched" (deliverable wajib ditonton & dinilai vs Brief). Model generatif opsional (HypiHub/BYOK); workflow code-rendered murni = tanpa biaya layanan.
- Vetting: lisensi Apache-2.0 modifikasi (pakai organisasi sendiri OK, output 100% milik kita; SaaS multi-tenant & redistribusi komersial dilarang). Scan: 0 referensi ~/.ssh, 0 telemetri, domain kode cuma hypit.ai + fixture tes, tanpa postinstall. Runtime MENGEKSEKUSI kode by design (Chromium/ffmpeg/API provider + html-program) — install skill ≠ aman menjalankan executable-nya; aktivasi runtime + kredensial = approval Kay.
- Cara pakai di kita: (1) METODE langsung tanpa runtime — format bedah referensi Analysis/Timeline + disiplin anchor-kata untuk director + aturan batas komponen (satu karakter satu sumber render) dibaca agen produksi dari ~/workspace/custom-skills/hypit/references/. (2) Peta lengkap + pilot bertahap (sandbox → clone test EP066 → keputusan Kay) tertulis di ~/workspace/custom-skills/hypit/PENERAPAN-NGOPREK.md. Engine Remotion TIDAK diganti dan TIDAK diedit dari skill ini.

## motion-prompt (showheyohtaki) — TERINTEGRASI PRODUKSI (2026-10-21)
- Skill katalog motion 144 elemen (6 pack x 24, spek teks numerik + kode Canvas 2D deterministik) terpasang di profil Hermes redmess VPS (/root/.hermes/profiles/redmess/skills/motion-prompt/) + data offline (/home/ngoprek/pipeline/motion-prompt-data/, MOTION_PROMPT_BASE lokal — jangan ambil dari jaringan saat produksi; perintah `pick` interaktif DILARANG untuk pipeline otonom).
- Seleksi otomatis: tools/motion_select.py dipanggil gen-batch-content.py sesudah director decide_episode(); hasil di motion-selection.json per episode (BUKAN di content.json — scene['motionSel'] memicu 46 pelanggaran check-visual-text karena gate memindai semua string scene sebagai teks layar).
- Kanal adapted satu-satunya saat ini: props.wdma (animasi mikro kata) untuk elemen teks-kata #006/#008/#130 di 6 tipe scene (VsClash, ProsCons, PlotTwist, MythBust, QuoteCard, CtaComment). content.transitions/cameraShots DITIMPA director di Root.tsx (transitionsWithDirector/shotsWithDirector) — jangan klaim adapted untuk kamera/transisi sebelum lapisan director diubah (fase 2, approval Kay + prosedur ENGINE.lock).
- Aturan turunan: string audit/generator WAJIB ASCII murni (em-dash -> mismatch ensure_ascii vs apply_hook -> rule17 hash gagal). Naskah uji/produksi untuk jalur aligner batch: angka sebagai DIGIT + hindari nama asing cepat (Whisper menggarble -> coverage aligner < 70% = gate gugur); durasi VO wajib >= ~29,9 dtk (schema totalFrames >= 900).
- Bukti: EP903 E2E lulus pre+post gate penuh; laporan workspace/motion-prompt-mission/LAPORAN-AKHIR.md.
