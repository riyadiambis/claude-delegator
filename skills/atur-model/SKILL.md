---
name: atur-model
description: Pilih model Claude (Opus, Sonnet, Haiku) dan effort sesuai berat pekerjaan agar hemat token, termasuk kapan memindahkan kerja ke subagent Sonnet/Haiku atau ke Gemini. Pakai di awal tugas yang besar atau berulang, saat merencanakan pembagian kerja, atau saat pengguna bilang "hemat token", "pakai Sonnet", "atur model", "sesuaikan effort".
---

# Atur Model dan Effort

Tujuan: pekerjaan berat dipikir oleh model kuat, pekerjaan ringan dikerjakan model murah.
Pengguna sudah mengizinkan memilih model dan effort sendiri sesuai kebutuhan (izin berlaku untuk parameter `model` dan `effort` pada subagent).

## Lapisan 1: otomatis dari pengaturan (sudah aktif)

`~/.claude/settings.json` berisi `"model": "opusplan"` dan `"effortLevel": "medium"`:
- **Plan mode** memakai Opus (merancang, memutuskan).
- **Di luar plan mode** memakai Sonnet (mengeksekusi).

Jadi pekerjaan besar: masuk plan mode dulu (EnterPlanMode) untuk merancang dengan Opus, lalu keluar
untuk eksekusi dengan Sonnet. Jangan mengubah pengaturan ini tanpa izin pengguna.
Pengguna bisa ganti manual kapan saja dengan `/model` atau `/effort`.

## Lapisan 2: subagent dengan model dan effort pilihan

Pakai tool Agent dengan parameter `model` dan `effort` untuk memisahkan kerja:

| Pekerjaan | model | effort |
|---|---|---|
| Cari berkas, petakan kode, "di mana X dipakai" | `haiku` | `low` |
| Ringkas dokumen, baca log panjang, cek format | `haiku` | `low` |
| Ubah mekanis 1-2 berkas yang jelas, tulis tes dari pola | `sonnet` | `medium` |
| Implementasi fitur mengikuti desain/ISSUE yang sudah jelas | `sonnet` | `high` |
| Review kode, cari bug halus, audit keamanan | `opus` | `high` |
| Keputusan arsitektur, rencana besar, masalah buntu | `opus` | `xhigh` |

Aturan:
- Subagent mulai dari nol: tulis brief lengkap (berkas, tujuan, batasan, bentuk hasil). Brief panjang = token juga.
- Tugas yang selesai dalam beberapa langkah: kerjakan sendiri, jangan buat subagent.
- Hasil subagent tetap dicek sebelum dipakai.

## Lapisan 3: keluar dari Claude

Tugas repetitif skala besar atau audit pendapat kedua: pakai skill `gemini-eksekutor` (Gemini lewat agy).

## Urutan memutuskan

1. Bisa selesai dalam beberapa langkah? Kerjakan langsung, effort sekarang.
2. Butuh pencarian luas? Subagent `haiku`.
3. Banyak berkas mekanis? Gemini (`gemini-eksekutor`) atau subagent `sonnet`.
4. Butuh penilaian sulit? Plan mode (Opus) atau subagent `opus`.

## Claude Code Router

`musistudio/claude-code-router` tidak dipasang (keputusan 2026-11-20): opusplan sudah menangani
Opus dan Sonnet, sedangkan router menyalurkan semua lalu lintas lewat proxy lokal dan biasanya butuh
kunci API penyedia lain. Pasang hanya bila pengguna minta dan setelah menjelaskan apa yang diubahnya.
