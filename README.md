# Task Master

Task Master is a browser-based workflow app for task tracking, daily work updates, admin-managed invitations, and login links.

## GitHub Pages Deployment

1. Create a new GitHub repository.
2. Upload these files to the repository root:
   - `index.html`
   - `.nojekyll`
   - `README.md`
3. Open the repository on GitHub.
4. Go to `Settings` -> `Pages`.
5. Under `Build and deployment`, choose:
   - Source: `Deploy from a branch`
   - Branch: `main`
   - Folder: `/root`
6. Save.
7. GitHub will give you a public link like:
   `https://your-username.github.io/your-repository-name/`

After publishing, paste that public link into the app Admin page under `App link users should open`.

## Default Admin Login

User ID: `TM-ADMIN`

Password: `Admin@123`

## Important

This version stores app data in each browser's local storage. GitHub Pages gives everyone a public link to open the app, but a shared database is needed later if all users should see the exact same live tasks and users automatically.

## Tally Sync Integration

The app now includes a Tally Sync module designed for this architecture:

```text
Web App -> Backend -> Windows Tally Sync Agent -> Local Tally
```

Do not connect GitHub Pages directly to Tally or port `9000`.

Development scaffold files:

- `backend/server.py`
- `agent/main.py`
- `agent/tally_client.py`
- `agent/api_client.py`
- `agent/auth.py`
- `agent/sync_manager.py`
- `agent/command_processor.py`
- `agent/config.py`
- `agent/logger.py`

Run the development backend:

```powershell
python backend/server.py
```

Pair a local agent:

```powershell
python -m agent.main pair <pairing-code>
```

Run the local agent:

```powershell
python -m agent.main run
```

See `TALLY_SYNC_ARCHITECTURE.md` for the full design.
