# LabelOps Future Deployment & Update Guide

This guide details the step-by-step procedures to deploy updates and changes to the **LabelOps Next.js Frontend** and **.NET 8.0 Web API** on your Linux production server.

---

## SECTION 1: UPDATING THE NEXT.JS FRONTEND

Because Next.js compiles environment variables into the client-side bundle during build time, you must build the project locally on Windows and upload the standalone files to the server.

### Step 1: Make Changes Locally
Edit the code files on your computer inside:
* Folder: `d:\mobileapp\labelops`

### Step 2: Build the Application
Open your local Windows command prompt/git bash inside `d:\mobileapp\labelops` and run this exact command:

================ COMMAND TO RUN LOCALLY ================
```bash
pnpm build
```
======================================================

### Step 3: Run the Auto-Packager Script
Go to `D:\mobileapp` in your Windows File Explorer and double-click the script we created:
* File: **`package_frontend.bat`**
* This script will automatically create a clean `deploy_frontend.zip` at `D:\mobileapp\deploy_frontend.zip` containing all correct folder structures and dependencies.

### Step 4: Upload to the Server
* Open **WinSCP** and connect to your server.
* Navigate to: `/var/www/labelops/frontend/`
* Delete the existing files inside that folder (except `deploy_frontend.zip`).
* Drag and drop the new `deploy_frontend.zip` from your PC to the server.

### Step 5: Extract & Restart (On Server)
SSH into your server and run this sequence of commands:

================ COMMANDS TO RUN ON SERVER ================
```bash
cd /var/www/labelops/frontend

# 1. Force extract the new archive
sudo unzip -o deploy_frontend.zip

# 2. Cleanup zip file
sudo rm deploy_frontend.zip

# 3. Apply correct ownership permissions
sudo chown -R labelops:labelops /var/www/labelops/frontend
find /var/www/labelops/frontend -type d -exec chmod 750 {} \;
find /var/www/labelops/frontend -type f -exec chmod 640 {} \;

# 4. Restart the systemd service
sudo systemctl restart labelops-frontend
```
======================================================

---

## SECTION 2: UPDATING THE .NET 8.0 WEB API

When making C# changes, you must compile and publish the API locally, apply any new database schema migrations, and copy the published DLLs to the server.

### Step 1: Make C# Changes
Edit your C# code on your computer inside:
* Folder: `d:\mobileapp\labelops-api`

### Step 2: Handle Database Migrations (If Schema Changed)
If you added new tables, columns, or relations to the database model:

1. **Add a new migration locally:**
   Open PowerShell in `d:\mobileapp\labelops-api` and run:

   ================ COMMAND TO RUN LOCALLY ================
   ```powershell
   dotnet ef migrations add YourMigrationNameHere --project CloudApi
   ```
   ======================================================

2. **Compile a new migration bundle:**
   In the same local PowerShell, run:

   ================ COMMAND TO RUN LOCALLY ================
   ```powershell
   dotnet ef migrations bundle --project CloudApi --self-contained -r linux-x64 -o efbundle
   ```
   ======================================================

3. **Upload & Run the Migration Bundle:**
   * Upload the newly created `efbundle` binary to `/var/www/labelops/api/` via WinSCP.
   * Open your server SSH terminal and run:

   ================ COMMANDS TO RUN ON SERVER ================
   ```bash
   cd /var/www/labelops/api
   chmod +x efbundle
   ./efbundle --connection "Host=localhost;Database=labelops_prod;Username=labelops_user;Password=YourDbPasswordHere;"
   ```
   ======================================================
   *(Remember to replace "YourDbPasswordHere" with your actual database password).*

### Step 3: Publish the API locally
Open your local Windows PowerShell in `d:\mobileapp\labelops-api` and run:

================ COMMAND TO RUN LOCALLY ================
```powershell
dotnet publish CloudApi/CloudApi.csproj -c Release -r linux-x64 --self-contained false -o ./publish
```
======================================================

### Step 4: Upload to the Server
* Open **WinSCP** and connect to your server.
* Navigate to: `/var/www/labelops/api/`
* Upload the contents of your local `publish` folder into this directory, overwriting the old files.

### Step 5: Restart the API Service
In your server SSH terminal, run this command to load the new code changes:

================ COMMAND TO RUN ON SERVER ================
```bash
sudo systemctl restart labelops-api
```
======================================================

---

## SECTION 3: SERVER MAINTENANCE & DIAGNOSTICS

Run these commands in your server SSH terminal to check application health.

### A. View Active Services Status
================ COMMANDS TO RUN ON SERVER ================
```bash
# Check Frontend status
sudo systemctl status labelops-frontend

# Check Backend API status
sudo systemctl status labelops-api

# Check Database status
sudo systemctl status postgresql

# Check Web Server status
sudo systemctl status caddy
```
======================================================

### B. View Live Application Logs (Troubleshooting)
================ COMMANDS TO RUN ON SERVER ================
```bash
# Monitor live logs for Frontend
sudo journalctl -u labelops-frontend -f

# Monitor live logs for Backend API
sudo journalctl -u labelops-api -f

# View Caddy server logs
sudo journalctl -u caddy -f
```
======================================================

### C. Resource Monitoring (RAM & CPU)
================ COMMANDS TO RUN ON SERVER ================
```bash
# Check real-time RAM and CPU consumption
htop
```
======================================================

