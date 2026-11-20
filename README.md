# Claude Code Skills Ofi Labs

| Skill | Fungsi |
|---|---|
| `gemini-eksekutor` | Mendelegasikan pekerjaan ke Gemini lewat CLI Antigravity (`agy`) untuk menghemat token Claude, lalu mengaudit hasilnya. |
| `atur-model` | Memilih model Claude (Opus, Sonnet, Haiku) dan effort sesuai berat pekerjaan agar hemat token, termasuk kapan memindahkan kerja ke subagent atau Gemini. |

## Cara pasang

Salin folder di `skills/` ke `~/.claude/skills/` (global, semua proyek) atau ke `.claude/skills/` di root proyek (hanya proyek itu).

```bash
git clone https://github.com/riyadiambis/claude-skills.git
cd claude-skills
mkdir -p ~/.claude/skills
cp -r skills/* ~/.claude/skills/
```

Lalu mulai ulang Claude Code.

## Catatan

- `gemini-eksekutor` butuh CLI Antigravity (`agy`) terpasang dan sudah login.
- Bagian "Catatan lingkungan" di skill itu khusus laptop pemilik; sesuaikan di mesin lain.
