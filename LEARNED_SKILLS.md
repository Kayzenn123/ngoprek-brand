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
