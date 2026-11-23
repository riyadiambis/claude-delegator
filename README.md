# Claude Delegator

**EN** · Two Claude Code skills that cut token use: hand repetitive work to Gemini through the Antigravity CLI (`agy`) with a cheap pass/fail check, and pick the right Claude model (Opus, Sonnet, Haiku) and effort for each job.

**ID** · Dua skill Claude Code untuk hemat token: serahkan pekerjaan repetitif ke Gemini lewat CLI Antigravity (`agy`) dengan cek lulus/gagal yang murah, dan pilih model Claude (Opus, Sonnet, Haiku) serta effort yang pas untuk tiap pekerjaan.

> The skill text is written in Bahasa Indonesia. Claude reads it fine in any language. / Isi skill berbahasa Indonesia.

## Skills

| Skill | EN | ID |
|---|---|---|
| `gemini-eksekutor` | Delegate repetitive or mechanical work to Gemini via `agy`, then check it with one command (report, changed files, analyzer, tests) instead of re-reading everything. | Delegasikan pekerjaan repetitif ke Gemini lewat `agy`, lalu cek dengan satu perintah (laporan, berkas yang berubah, analisis, tes) tanpa membaca ulang semuanya. |
| `atur-model` | Route work by weight: `opusplan` (Opus plans, Sonnet executes), Haiku/Sonnet/Opus subagents with matching effort, Gemini for bulk work. | Atur pekerjaan sesuai beratnya: `opusplan` (Opus merencanakan, Sonnet mengeksekusi), subagent Haiku/Sonnet/Opus dengan effort yang sesuai, Gemini untuk kerja massal. |

## Limits / Batas

**EN** · A skill is guidance text; it cannot switch the running session's model. Real switching only comes from `opusplan` in settings, `/model` and `/effort` typed by you, the `model`/`effort` parameters on subagents, and `agy` for Gemini. `atur-model` is written around these four. `opusplan` may be ignored in the desktop app or Cowork; check the active model.

**ID** · Skill hanya teks panduan; tidak bisa mengganti model sesi yang berjalan. Pergantian nyata hanya dari `opusplan`, `/model` dan `/effort` yang kamu ketik, parameter `model`/`effort` pada subagent, dan `agy` untuk Gemini. `opusplan` bisa tidak berlaku di aplikasi desktop atau Cowork; cek model aktif.

## Install / Pasang

```bash
git clone https://github.com/riyadiambis/claude-delegator.git
cp -r claude-delegator/skills/* ~/.claude/skills/      # all projects / semua proyek
# or / atau: cp -r claude-delegator/skills/* <project>/.claude/skills/
```

Restart Claude Code. / Mulai ulang Claude Code.

Optional setting / Pengaturan opsional (`~/.claude/settings.json`):

```json
{ "model": "opusplan", "effortLevel": "medium" }
```

## Requirements / Kebutuhan

- [Claude Code](https://docs.claude.com/en/docs/claude-code)
- For `gemini-eksekutor`: Antigravity CLI (`agy`) installed and signed in. Check models with `agy models`. / Untuk `gemini-eksekutor`: CLI Antigravity (`agy`) terpasang dan sudah login.
- The "Catatan lingkungan" section in `gemini-eksekutor` holds Windows examples; adjust paths to your machine. / Sesuaikan path di bagian "Catatan lingkungan" dengan mesinmu.

## Also worth installing / Layak dipasang juga

- [RTK](https://github.com/rtk-ai/rtk) (Apache-2.0 + BUSL-1.1 for Pro edition): compresses shell command output before it enters context. `winget install rtk-ai.rtk`, then `rtk init -g`, restart Claude Code, check with `rtk gain`. It patches `~/.claude/settings.json` (backup is made); review before installing.
- RTK memangkas keluaran perintah shell sebelum masuk konteks. Ia mengubah `~/.claude/settings.json` (ada cadangan); periksa dulu sebelum memasang.

## Recommended companion: Ponytail / Pendamping yang disarankan

[Ponytail](https://github.com/DietrichGebert/ponytail) (MIT, by DietrichGebert) makes agents write the smallest change that fully solves the task, which pairs well with cheap delegation. Install it in both agents so Claude and Gemini follow the same rules. / Ponytail membuat agen menulis perubahan terkecil yang menyelesaikan tugas, cocok dipadukan dengan delegasi hemat. Pasang di kedua agen supaya Claude dan Gemini memakai aturan yang sama.

```bash
# Claude Code
claude plugin marketplace add DietrichGebert/ponytail
claude plugin install ponytail@ponytail

# Antigravity CLI (agy) — if it fails with "Access is denied", run it again
agy plugin install https://github.com/DietrichGebert/ponytail
```

Antigravity has no always-on hook for Ponytail; for always-on rules copy Ponytail's `AGENTS.md` into your project. / Di Antigravity, Ponytail tidak aktif terus-menerus; salin `AGENTS.md` milik Ponytail ke proyekmu untuk aturan yang selalu aktif.

## License / Lisensi

MIT
