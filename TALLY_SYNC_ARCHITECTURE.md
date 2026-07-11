# Tally Sync Architecture

## Target Architecture

Task Master must not connect directly from the browser to Tally or to Tally port `9000`.

The intended production architecture is:

```text
Task Master Web App
  <-> Cloud Backend / Database over HTTPS
  <-> Python Tally Sync Agent.exe
  <-> Local Tally Prime XML API
```

## Current Web App Work

The Task Master frontend now includes a `Tally Sync` module for regular users.

It supports two frontend states:

- Agent not connected:
  - Download Agent button
  - Installation steps
  - One-time pairing code
  - Agent and Tally status placeholders

- Agent connected:
  - Agent status
  - Tally status
  - Connected device
  - Selected company
  - Last sync time
  - Sync status
  - Tabs for Overview, Vouchers, Ledgers, Debits, Credits, Outstanding, and Sync Activity

The frontend is prepared for a backend-driven heartbeat and sync system. The current local demo controls should be replaced with real backend API calls when the backend exists.

## Added Scaffold Files

This project now includes a minimal backend and agent scaffold:

```text
backend/
  server.py
agent/
  main.py
  tally_client.py
  api_client.py
  auth.py
  sync_manager.py
  command_processor.py
  config.py
  logger.py
```

The backend is intentionally simple and file-backed for development. It provides:

- pairing code creation
- device pairing
- device heartbeat
- connected-device status
- Tally data storage endpoints
- pending command queue
- command result reporting

The agent scaffold provides:

- pairing by one-time code
- secure-ish local device token storage in `%APPDATA%\TaskMasterTallyAgent`
- heartbeat loop
- sync-data call structure
- pending-command polling
- placeholder adapter methods for the existing Tally logic

## Existing Python Code To Reuse

The existing `C:\Tallysync\tally_server.py` already contains useful Tally integration logic:

- Tally reachability checks
- Company fetching
- Voucher fetching
- Ledger fetching
- Bank statement extraction/import logic
- Import/writeback helpers
- Error parsing and retry-friendly result objects

These should be refactored into an agent layout instead of rewritten:

```text
agent/
  main.py
  tally_client.py
  api_client.py
  auth.py
  sync_manager.py
  command_processor.py
  config.py
  logger.py
```

The current `agent/tally_client.py` marks the exact adapter methods where existing logic from `C:\Tallysync\tally_server.py` should be moved or imported.

## Local Development Commands

Run the backend:

```powershell
python backend/server.py
```

Create a pairing code:

```powershell
Invoke-RestMethod -Method Post -Uri http://127.0.0.1:8080/api/pairing-codes -ContentType application/json -Body '{"user_id":"local-user","organisation_id":"default-org"}'
```

Pair the agent:

```powershell
python -m agent.main pair 847291
```

Run the agent heartbeat loop:

```powershell
python -m agent.main run
```

## Backend Needed For Real Multi-User Sync

GitHub Pages can host the frontend, but it cannot provide secure user/device state or a database.

The backend should support:

- User authentication
- Device pairing
- Pairing codes
- Device tokens
- Agent heartbeat
- Company list storage
- Tally data storage
- Sync jobs
- Pending two-way sync commands
- Sync logs
- Error logs

Suggested entities:

- users
- organisations
- devices
- pairing_codes
- tally_companies
- vouchers
- ledgers
- outstanding_balances
- sync_jobs
- sync_commands
- sync_logs

## Agent Heartbeat Payload

Example:

```json
{
  "device_id": "device_123",
  "agent_status": "online",
  "tally_running": true,
  "tally_port": 9000,
  "selected_company": "ABC Pvt Ltd",
  "last_sync": "timestamp"
}
```

The frontend should show the agent as offline when no heartbeat is received within the configured timeout.

## Security Notes

- Use HTTPS for all frontend/backend/agent communication.
- Never put backend secrets in frontend code.
- Pair every device to a specific user or organisation.
- Use secure device tokens.
- Do not expose local Tally ports publicly.
- Validate backend requests.
- Rate-limit pairing and sync endpoints.
- Do not store user passwords in plain text.
- Prevent duplicate command processing with unique command IDs and idempotency checks.
