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

## Struktur

```
smartolt-api/
├── .github/
│   └── workflows/
│       └── postman-api.yml    # GitHub Actions workflow
├── results/
│   └── .gitkeep               # Placeholder untuk folder results
└── README.md
```

## License

MIT
