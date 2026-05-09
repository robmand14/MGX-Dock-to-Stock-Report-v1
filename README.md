# MGX Dock to Stock — GitHub Actions Pipeline

Automates the weekly Oracle → Excel → SharePoint workflow for the
MGX Dock to Stock report. Runs every Monday at 6:00 AM CST.

---

## Repo Structure

```
.
├── .github/
│   └── workflows/
│       └── mgx_dock_to_stock.yml   ← GitHub Actions workflow
├── pipeline.py                     ← Main automation script
├── requirements.txt                ← Python dependencies
└── README.md
```

---

## One-Time Setup

### 1. Create the GitHub Repository
```bash
git init mgx-dock-to-stock
cd mgx-dock-to-stock
# copy all files in, then:
git add .
git commit -m "Initial pipeline setup"
git remote add origin https://github.com/YOUR_ORG/mgx-dock-to-stock.git
git push -u origin main
```

### 2. Add GitHub Secrets
Go to: **GitHub repo → Settings → Secrets and variables → Actions → New repository secret**

Add each of these:

| Secret Name              | Value / Where to find it                          |
|--------------------------|---------------------------------------------------|
| `ORACLE_USERNAME`        | Your Oracle Cloud username                        |
| `ORACLE_PASSWORD`        | Your Oracle Cloud password                        |
| `ORACLE_DSN`             | e.g. `myhost.fa.ocs.oraclecloud.com:1521/ORCLPDB` |
| `ORACLE_INVENTORY_ORG`   | e.g. `1013`                                       |
| `AZURE_TENANT_ID`        | Azure AD → Overview → Tenant ID                   |
| `AZURE_CLIENT_ID`        | Azure AD → App Registrations → your app           |
| `AZURE_CLIENT_SECRET`    | Azure AD → App Registrations → Certificates & Secrets |
| `SHAREPOINT_SITE_ID`     | See step 3 below                                  |
| `SHAREPOINT_DRIVE_ID`    | See step 3 below                                  |
| `SHAREPOINT_FILE_PATH`   | e.g. `/MGX Dock to Stock/MGX Dock to Stock Master.xlsx` |

### 3. Get SharePoint IDs (one time)
Open your browser and go to (you must be signed in to Microsoft 365):

```
https://graph.microsoft.com/v1.0/sites/mrcglobal.sharepoint.com:/teams/ProcessImprovementTeam
```
Copy the `id` field → that's your `SHAREPOINT_SITE_ID`.

Then:
```
https://graph.microsoft.com/v1.0/sites/{SITE_ID}/drives
```
Find the document library drive (usually named "Documents") → copy its `id` → that's `SHAREPOINT_DRIVE_ID`.

### 4. Azure App Registration Permissions
In Azure portal → App Registrations → your app → API Permissions, add:
- `Sites.ReadWrite.All` (Application permission)
- `Files.ReadWrite.All` (Application permission)

Then click **Grant admin consent**.

---

## Running Manually

In GitHub: **Actions tab → MGX Dock to Stock Report → Run workflow**

You can optionally override:
- **date_from** / **date_to** — run for any date range (format: `YYYY-MM-DD`)
- **organization** — override the org code (comma-separated for multiple)

---

## Schedule
Runs automatically every **Monday at 6:00 AM CST** (12:00 UTC).
Cron: `0 12 * * 1`

---

## Output
- Excel file saved as a **workflow artifact** (retained 30 days) under the Actions run
- Master SharePoint file is updated with new rows appended
