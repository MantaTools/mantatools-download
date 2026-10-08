# releases-mantatools

## Wajib sebelum deploy (aturan owner 2026-10-09)
- Semua perubahan WAJIB di-commit dan di-push ke GitHub sebelum deploy, termasuk branch kerja dan catatan memory. Cek dulu: `git status` bersih dan `git log @{u}..HEAD` kosong. Tidak boleh ada commit atau file penting yang hanya tertinggal di PC.
- File rahasia (`.env`, token, kunci, kredensial akun) tetap TIDAK di-commit: pastikan tercakup `.gitignore`.
