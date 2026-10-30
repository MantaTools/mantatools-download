# releases-mantatools: panduan agent

## Apa ini

Repo publik tempat rilis installer app desktop MantaTools dipublikasikan. Folder lokal ini = repo
GitHub `MantaTools/mantatools-download` (nama repo tidak ikut di-rename saat reorganisasi folder).
Isinya BUKAN kode app (kode app ada di `app-mantatools`), melainkan:

- **GitHub Releases** repo ini: installer `mantatools-<versi>-setup.exe` + `latest.yml` per tag `v<versi>`.
  `electron-updater` di app membaca `latest.yml` dari sini (`app-mantatools/electron-builder.yml`:
  `publish.provider github`, `owner MantaTools`, `repo mantatools-download`), jadi rilis di sini = update
  otomatis ke SEMUA user.
- `README.md`: halaman unduhan publik (badge versi, fitur, link `releases/latest`). Copy user-facing,
  Bahasa Indonesia.
- `.github/workflows/discord-release-notify.yml`: mengumumkan rilis ke Discord.
- `assets/` (logo, `release-banner.html` sumber + `release-banner.png` hasil render) dan `resources/`
  (screenshot README).

Tag terbaru di remote saat dicek 2026-10-30: `v2.5.10`. Peta ekosistem dimuat dari CLAUDE.md root.

## Stack + perintah

Tidak ada build di repo ini. Rilis dibuat dari repo app:

```bash
# di E:\MANTAGAMES.ID\app-mantatools, setelah menaikkan "version" di package.json
yarn release        # = powershell -ExecutionPolicy Bypass -File scripts/release-win.ps1
```

`scripts/release-win.ps1` (app): cek `gh` login, tolak kalau tag `v<versi>` sudah ada, susun catatan
rilis dari entri `changelog.json` versi itu (bahasa Inggris; fallback kalimat generik), `npx electron-vite build`,
`npx electron-builder --win nsis --publish never`, lalu `gh release create v<versi> --repo MantaTools/mantatools-download --latest`
dengan HANYA dua file: setup `.exe` + `latest.yml` (tanpa portable, tanpa `.blockmap`).

## Deploy

- Rilis LANGSUNG terpublikasi (bukan draft) dan tanpa gerbang review: begitu script selesai, update
  bergulir ke semua user dan Discord mengumumkannya. Naikkan versi dan cek changelog dulu.
- Workflow Discord jalan pada event `release: published`, POST embed ke secret repo
  `DISCORD_RELEASE_WEBHOOK` (secret kosong = job gagal dengan error). Embed: judul `MantaTools <versi>`,
  isi catatan rilis (dipotong 3000 byte), banner `raw.githubusercontent.com/.../assets/release-banner.png?v=3`,
  logo `api.mantagames.id/imgs/logo.png?v=2`, tanpa link (app memperbarui diri sendiri).
- Ganti banner: render ulang `assets/release-banner.png` dari `release-banner.html`, lalu naikkan `?v=`
  di workflow supaya Discord mengambil ulang gambarnya.
- Push ke `main` repo ini hanya mengubah README/workflow/aset; tidak ada deploy server.

## Aturan kritis + jebakan

- Jangan membuat atau menghapus release/tag secara manual tanpa izin owner: `latest.yml` di release
  `--latest` yang menentukan versi yang diunduh semua app.
- Komentar di workflow masih menyebut "electron-builder uploads the installers to a DRAFT release";
  itu basi, script sekarang membuat release yang langsung published lewat `gh`.
- `.gitattributes` memaksa LF untuk `*.yml`/`*.sh`/`*.html` (CRLF pernah merusak blok `run` bash di workflow).
- Copy README: fokus main offline, multiplayer hanya lewat online-fix, pindah PC lewat
  `mantagames.id/unbind`, bonus Denuvo hanya untuk beli Premium langsung di website (aturan copy owner,
  canonical `website-mantagames/docs/agent-memory/copy-offline-first-2026-10.md`). Jangan cantumkan
  nomor versi lama. Tanpa em-dash.
- Tidak ada rahasia di repo ini; webhook Discord hanya di GitHub Secrets.

## Peta kode

| File | Isi |
|------|-----|
| `README.md` | Halaman unduhan publik |
| `.github/workflows/discord-release-notify.yml` | Pengumuman rilis ke Discord |
| `assets/release-banner.html` / `.png` | Banner embed Discord (sumber HTML + hasil render) |
| `assets/logo.png`, `resources/*.png` | Logo dan screenshot README |
| `app-mantatools/scripts/release-win.ps1` | Script rilis (di repo app) |

## Peta dokumen

- Riwayat rilis app dan versi: `app-mantatools/CLAUDE.md` + memory app.
- Aturan copy produk: `website-mantagames/docs/agent-memory/copy-offline-first-2026-10.md`.

## Wajib sebelum deploy (aturan owner 2026-10-09)
- Semua perubahan WAJIB di-commit dan di-push ke GitHub sebelum deploy, termasuk branch kerja dan catatan memory. Cek dulu: `git status` bersih dan `git log @{u}..HEAD` kosong. Tidak boleh ada commit atau file penting yang hanya tertinggal di PC.
- File rahasia (`.env`, token, kunci, kredensial akun) tetap TIDAK di-commit: pastikan tercakup `.gitignore`.

## Memory

@docs/agent-memory/MEMORY.md

Memory git-synced di `docs/agent-memory/` mengikuti standar
`website-mantagames/docs/agent-memory/standar-memory.md`: fakta baru jadi file topik berfrontmatter +
satu baris di `MEMORY.md`, ikut di-commit (`git commit -- CLAUDE.md docs/agent-memory`). Jangan simpan
memory di `~/.claude*/projects/`.
