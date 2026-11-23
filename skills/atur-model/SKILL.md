---
name: atur-model
description: Pilih model Claude (Fable, Opus, Sonnet, Haiku) dan effort sesuai berat pekerjaan agar hemat token, termasuk kapan memindahkan kerja ke subagent atau ke Gemini. WAJIB dipakai di awal setiap tugas yang lebih dari ~3 langkah (kode banyak berkas, riset, reverse engineering, build berulang), saat merencanakan pembagian kerja, atau saat pengguna bilang "hemat token", "pakai Sonnet", "atur model", "sesuaikan effort", "limit habis".
---

# Atur Model dan Effort

Tujuan: pekerjaan berat dipikir model kuat, pekerjaan ringan dikerjakan model murah.
Pengguna sudah mengizinkan memilih `model` dan `effort` pada subagent sendiri sesuai kebutuhan.

## Batas jujur (baca dulu)

Skill ini hanya teks panduan. Claude TIDAK bisa mengganti model sesi yang sedang berjalan.
Yang benar-benar mengganti model hanya empat hal:

| Mekanisme | Siapa yang menjalankan | Berlaku |
|---|---|---|
| `"model": "opusplan"` di settings | otomatis | plan mode = Opus, di luar plan mode = Sonnet |
| `/model`, `/effort` | pengguna (ketik sendiri) | seluruh sesi |
| Parameter `model` + `effort` pada tool Agent | Claude | hanya subagent itu |
| `agy` (skill `gemini-eksekutor`) | Claude | di luar Claude |

Catatan: `opusplan` bisa tidak berlaku di aplikasi desktop atau Cowork. Cek model aktif di awal tugas besar.
Bila sesi masih Sonnet padahal tugasnya berat (desain, arsitektur, reverse engineering), JANGAN diam saja:
katakan satu kalimat "tugas ini berat, ketik `/model opus`" lalu lanjut setelah pengguna mengganti.

## Aturan utama: sesi utama tetap murah

Tugas baca banyak, cari luas, atau loop build/tes berulang membengkakkan konteks sesi utama.
Pindahkan ke subagent supaya hanya ringkasannya yang kembali ke sesi utama.
Sesi utama dipakai untuk keputusan, bukan untuk membaca keluaran panjang.

## Subagent: model dan effort

Tool Agent, parameter `model` (`haiku` | `sonnet` | `opus` | `fable`) dan `effort`:

| Pekerjaan | model | effort |
|---|---|---|
| Cari berkas, petakan kode, "di mana X dipakai" | `haiku` | `low` |
| Ringkas dokumen, baca log panjang, cek format | `haiku` | `low` |
| Ubah mekanis 1-2 berkas yang jelas, tulis tes dari pola | `sonnet` | `medium` |
| Implementasi fitur mengikuti desain yang sudah jelas | `sonnet` | `high` |
| Review kode, cari bug halus, audit keamanan | `opus` | `high` |
| Keputusan arsitektur, masalah buntu, analisis hasil dekompilasi | `opus` atau `fable` | `xhigh` |

Aturan:
- Subagent mulai dari nol: brief lengkap (berkas, tujuan, batasan, bentuk hasil, batas panjang laporan).
- Selesai dalam 1-3 langkah: kerjakan sendiri, jangan buat subagent.
- Hasil subagent dicek singkat sebelum dipakai.
- Pekerjaan banyak berkas mekanis atau audit pendapat kedua: skill `gemini-eksekutor`.

## Urutan memutuskan (jalankan di awal tugas)

1. Selesai dalam 1-3 langkah? Kerjakan langsung.
2. Perlu membaca/mencari banyak? Subagent `haiku`.
3. Banyak berkas mekanis? Gemini atau subagent `sonnet`.
4. Butuh penilaian sulit? Cek model sesi; bila bukan Opus, minta `/model opus` atau pakai subagent `opus`.
5. Satu kalimat ke pengguna: rencana pembagian dan alasannya, lalu jalan.

## Perantara berkas (hemat brief dan hasil)

Konteks yang dipakai berulang (keputusan, batasan, path penting) ditulis sekali ke satu berkas,
misalnya `<scratchpad>/konteks-<proyek>.md`. Brief subagent cukup: "baca <berkas>, kerjakan X".
Minta subagent menulis hasil panjang ke berkas lain dan membalas maksimal 10 baris plus path-nya.
Pakai hanya bila konteks yang sama dipakai 2 subagent atau lebih, atau lintas sesi.
Tugas sekali jalan: brief biasa (membaca berkas tidak lebih murah dari brief). Jangan simpan rahasia.

## Claude Code Router

`musistudio/claude-code-router` tidak dipasang (keputusan 2026-11-20): opusplan sudah menangani
Opus dan Sonnet, sedangkan router menyalurkan semua lalu lintas lewat proxy lokal dan biasanya butuh
kunci API penyedia lain. Pasang hanya bila pengguna minta dan setelah menjelaskan apa yang diubahnya.
