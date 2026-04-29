# SmartOLT API

Repository untuk testing API SmartOLT menggunakan Postman Collection dan Newman di GitHub Actions.

## Fitur

- **Automated API Testing** - Menjalankan Postman Collection secara otomatis menggunakan Newman
- **Scheduled Runs** - Dijalankan setiap hari pukul 00:00 UTC
- **Manual Trigger** - Dapat dijalankan manual melalui GitHub Actions
- **HTML Report** - Menghasilkan laporan testing dalam format HTML

## Workflow

Workflow CI/CD dikonfigurasi di `.github/workflows/postman-api.yml` dengan trigger:
- Push ke branch `main` atau `master`
- Pull Request ke branch `main` atau `master`
- Jadwal harian (cron: `0 0 * * *`)
- Manual dispatch dari GitHub

## Setup

### Secrets yang Diperlukan

Tambahkan secrets berikut di repository settings:

| Secret | Deskripsi |
|--------|-----------|
| `POSTMAN_API_KEY` | API Key dari Postman |
| `SMARTOLT_SUBDOMAIN` | Subdomain SmartOLT untuk testing |

### Menjalankan Secara Lokal

```bash
# Install Newman
npm install -g newman newman-reporter-htmlextra

# Jalankan collection
newman run "https://api.getpostman.com/collections/6034668-25ef5a4b-f9da-4a68-9f16-cf9d888e10da?apikey=YOUR_API_KEY" \
  --env-var "subdomain=YOUR_SUBDOMAIN" \
  --reporters cli,htmlextra \
  --reporter-htmlextra-export results/report.html \
  --timeout-request 30000
```

## MCP Server (Model Context Protocol)

Jadikan SmartOLT API sebagai **tools AI** via Postman MCP Server. AI agents (Claude Desktop, Cursor, OpenCode) bisa langsung query ONU, reboot, authorize, dll.

### 1. Import Collection ke Postman

1. Buka Postman → **Import** → pilih `smartolt-postman-collection.json`
2. Import `smartolt-postman-environment.json` sebagai Environment
3. Set `subdomain` dan `api_key` di Environment
4. Test salah satu request untuk verifikasi

### 2. Dapatkan Postman API Key & Workspace ID

```bash
# API Key: https://web.postman.co/settings/me/api-keys
# Workspace ID: Buka workspace → Settings → Workspace ID
```

### 3. Jalankan MCP Server

```bash
npx @postman/postman-mcp-server \
  --api-key PMAK-xxxxxxxxxxxx \
  --workspace-id ws_xxxxxxxxxxxx
```

### 4. Konfigurasi AI Client

**Claude Desktop** — salin `mcp-config/claude_desktop_config.json` ke:
- macOS: `~/Library/Application Support/Claude/claude_desktop_config.json`
- Windows: `%APPDATA%\Claude\claude_desktop_config.json`

**Cursor** — salin `mcp-config/cursor_mcp_config.json` ke:
- `~/.cursor/mcp.json`

**OpenCode** — salin isi `mcp-config/opencode_mcp.json` ke `opencode.json` di root project, atau merge bagian `mcp` ke `opencode.json` yang sudah ada.

Atau langsung pakai file `opencode.json` yang sudah disediakan di repo ini — tinggal ganti `YOUR_POSTMAN_API_KEY` dan `YOUR_WORKSPACE_ID`.

```json
{
  "$schema": "https://opencode.ai/config.json",
  "mcp": {
    "smartolt": {
      "type": "local",
      "command": ["npx", "-y", "@postman/postman-mcp-server", "--api-key", "YOUR_POSTMAN_API_KEY", "--workspace-id", "YOUR_WORKSPACE_ID"],
      "enabled": true,
      "timeout": 10000
    }
  }
}
```

Kemudian jalankan OpenCode dan minta:
- `use smartolt to list all OLTs`
- `use smartolt to reboot ONU ONU-123`

Setelah itu, AI bisa menjalankan commands seperti:
- "Cari ONU dengan serial HWTCCXXXXXX"
- "Reboot ONU dengan ID ONU-123"
- "Authorize ONU baru untuk pelanggan"
- "Cek signal level semua ONU"
- "Disable ONU yang tidak aktif"

## Struktur

```
smartolt-api/
├── .github/
│   └── workflows/
│       └── postman-api.yml          # GitHub Actions workflow
├── results/
│   └── .gitkeep                     # Placeholder untuk folder results
├── mcp-config/
│   ├── claude_desktop_config.json   # Config untuk Claude Desktop MCP
│   ├── cursor_mcp_config.json       # Config untuk Cursor MCP
│   └── opencode_mcp.json            # Config untuk OpenCode MCP
├── smartolt-postman-collection.json # Postman collection (MCP-ready)
├── smartolt-postman-environment.json # Environment variables
├── opencode.json                    # OpenCode config dengan MCP smartolt
└── README.md
```

## License

MIT
