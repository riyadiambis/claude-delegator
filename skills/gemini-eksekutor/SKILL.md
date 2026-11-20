---
name: gemini-eksekutor
description: Delegasikan pekerjaan ke Gemini lewat CLI Antigravity (agy) untuk hemat token Claude, lalu audit hasilnya. Pakai saat tugas repetitif atau mekanis (banyak berkas serupa, boilerplate, rename massal, dokumentasi, terjemahan), saat butuh audit kode baca-saja dari model lain, atau saat pengguna bilang "pakai Gemini", "suruh agy", "hemat token".
---

# Gemini Eksekutor (agy)

Claude = perencana dan auditor. Gemini (lewat `agy`) = eksekutor tugas mekanis dan auditor kedua.
Claude tetap bertanggung jawab atas hasil: tidak ada hasil Gemini yang di-commit tanpa dicek.

## Kapan delegasikan, kapan jangan

| Delegasikan ke Gemini | Kerjakan sendiri (Claude) |
|---|---|
| Banyak berkas dengan pola sama (layar CRUD, model, tes serupa) | Desain dan tampilan yang harus konsisten dengan mockup |
| Boilerplate, rename massal, format ulang | Logika inti, keamanan, uang, data pengguna |
| Dokumentasi, README, terjemahan teks UI | Keputusan arsitektur |
| Audit BACA-SAJA (pendapat kedua) | Perbaikan hasil audit (Claude verifikasi dulu) |

Tugas kecil (kurang dari ~3 berkas atau ~100 baris) lebih murah dikerjakan sendiri: menulis prompt yang jelas juga makan token.

## Pilih model dan effort

Cek daftar terbaru dengan `agy models` bila ragu. Gemini 3.8 Flash tidak dipakai.

| Tugas | Model |
|---|---|
| Audit kode BACA-SAJA, cari bug | `gemini-4-hydrogen-xhigh` |
| Menulis kode banyak berkas mengikuti pola yang ada | `gemini-4-hydrogen-high` |
| Dokumentasi, teks, rename, format | `gemini-4-hydrogen-medium` |
| Tugas sepele (satu perintah jelas) | `gemini-4-hydrogen-low` |

## Perintah

Tulis prompt ke berkas dulu (scratchpad), lalu jalankan dari root repo. Selalu `run_in_background: true` (bisa >10 menit), tunggu notifikasi, jangan polling.

```bash
# Menulis kode: boleh mengubah berkas, tidak boleh perintah berbahaya
agy -p "$(cat <berkas-prompt>)" --model gemini-4-hydrogen-high --mode accept-edits --print-timeout 30m > <laporan>.txt 2>&1

# Audit: mode plan = tidak mengubah apa pun
agy -p "$(cat <berkas-prompt>)" --model gemini-4-hydrogen-xhigh --mode plan --print-timeout 20m > <audit>.txt 2>&1
```

- Butuh membaca folder di luar repo (mockup, referensi): tambah `--add-dir <folder>`.
- **Dilarang** `--dangerously-skip-permissions`.
- Sebelum mendelegasikan tugas menulis kode: commit dulu pekerjaan Claude, supaya diff Gemini terlihat bersih (`git diff`).
- Proses macet atau arah berubah: hentikan dengan TaskStop, cek `git status`, buang perubahan setengah jadi hanya bila pengguna setuju.

## Bentuk prompt (wajib lengkap; Gemini tidak melihat percakapan ini)

```
Tugas: <ISSUE-NN / judul>. Kamu HANYA menulis berkas yang disebut di bawah.
BACA DULU: AGENTS.md, <docs acuan>, <berkas kode yang jadi pola>.
LARANGAN KERAS: jangan jalankan git; jangan ubah pubspec/package.json; jangan tambah paket;
jangan sentuh <folder terlarang>; <aturan proyek, mis. warna hanya dari token tema>.
BERKAS YANG DITULIS: 1. <path>: <isi spesifik>  2. ...
WAJIB SEBELUM SELESAI: jalankan analisis dan tes proyek sampai bersih, perbaiki sendiri bila gagal.
AKHIRI dengan LAPORAN maksimal 10 baris, tepat di akhir keluaran:
VONIS: LULUS|GAGAL
UJI: <ringkasan analyze, jumlah tes lulus/gagal>
BERKAS: <daftar berkas yang diubah>
RAGU: <hal yang perlu dicek Claude, atau "tidak ada">
```

Prompt audit diawali `BACA-SAJA.` dan meminta setiap temuan memuat `berkas:baris`, contoh pemicu
konkret, dan perbaikan terkecil. Temuan tanpa pemicu tidak dilaporkan.

## Setelah Gemini selesai: cek hemat

Tujuan delegasi adalah hemat token Claude. Jangan membaca ulang semua yang dikerjakan Gemini.
Gemini sendiri yang wajib menjalankan analisis dan tes (lihat bentuk prompt).

**Cek standar (selalu, satu perintah, keluaran ~5 baris):**

```bash
tail -6 <laporan>.txt; git status --short; <perintah analisis> | tail -1; <perintah tes> | tail -1
```

Lolos bila: VONIS LULUS, berkas yang berubah sesuai daftar yang diizinkan, analisis bersih, tes lulus.
Lolos = langsung commit `feat(issue-NN): ... (draf Gemini)` dan lanjut. Tidak membaca diff.

**Cek dalam (hanya bila salah satu terjadi):**
- Cek standar gagal: baca bagian laporan yang relevan saja, lalu kirim perbaikan balik ke Gemini
  (prompt pendek berisi galatnya) daripada memperbaiki sendiri, kecuali perbaikannya 1-2 baris.
- Ada berkas di luar daftar yang berubah: `git diff --stat`, kembalikan yang tidak diizinkan.
- RAGU menyebut hal berisiko, atau tugasnya menyentuh keamanan, data pengguna, uang, atau skema
  basis data: baca diff berkas itu saja (`git diff -- <berkas>`), bukan semua.

**Jangan:**
- Membaca seluruh laporan Gemini atau seluruh diff untuk tugas rutin.
- Menjalankan audit Gemini kedua untuk tiap tugas; audit hanya di akhir ISSUE besar atau bila diminta.
- Memverifikasi ulang tampilan dengan screenshot kecuali pengguna minta.

Hasil audit BACA-SAJA: cek hanya temuan yang akan diperbaiki, lalu delegasikan perbaikannya ke Gemini.

## Catatan lingkungan (contoh Windows, sesuaikan)

- `agy` ada di `C:\Users\Rahmad pengguna\AppData\Local\agy\bin\agy`.
- Build Gradle/Flutter di sandbox gagal "Unable to establish loopback connection": jalankan di luar sandbox
  dengan `TMP=C:\gtmp TEMP=C:\gtmp JAVA_TOOL_OPTIONS='-Djdk.net.unixdomain.tmpdir=C:\gtmp -Djava.io.tmpdir=C:\gtmp'`.
