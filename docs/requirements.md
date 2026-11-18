# Product Requirements Document (PRD)
## Demo Linux Infrastructure Monitoring Application

| Field | Value |
|-------|-------|
| **Product** | Demo Linux Infrastructure Monitoring |
| **Version** | 1.1 (Demo) |
| **Status** | Draft |
| **Audience** | Internal L1, L2, L3, Infrastructure, and Application Development teams |
| **Deployment** | Local (where Cursor is running) |

---

## 1. Problem

Users must log into individual Linux servers to check real-time CPU, memory, network usage, and listening port details. There is no single place to view this information quickly across hosts.

## 2. Goal

Provide a small, locally deployable demo that centralizes Linux monitoring in one modern, professional, interactive dashboard:

- Show live CPU, memory, network, and listening ports
- Collect metrics over SSH from Flask (read-only)
- Support a single admin login and minimal server registration
- Keep the solution simple—do not over-engineer

## 3. Users

| Role | Access |
|------|--------|
| Admin (single default user) | Sign in, view dashboards, add/remove servers in the app |
| Internal teams (L1 / L2 / L3 / Infra / App Dev) | Use the same admin account for this demo to view monitoring data |

Multi-user accounts and RBAC are out of scope.

## 4. Tech Stack

| Layer | Choice |
|-------|--------|
| Backend | Python + Flask |
| Frontend | HTML, CSS, lightweight JavaScript for interactivity |
| Auth | Single `admin` user; password stored as a hash |
| Metric collection | SSH from the Flask app to Linux hosts |
| Storage | **In-memory** for this demo (server inventory and session state). No database unless absolutely necessary |
| Dependencies | Keep minimal |

---

## 5. Core Features

### Feature 1 — Modern Monitoring Dashboard

**User story:** As an engineer, I open a modern infrastructure-style dashboard and quickly see all monitored servers.

**Requirements:**

- Professional, classy, visually impactful UI that remains simple and easy to use
- Display each monitored server as a clean card showing:
  - Server name
  - IP / hostname
  - Availability status
  - CPU usage
  - Memory usage
  - Network status

### Feature 2 — Interactive Health Status

**User story:** As an engineer, I understand server health at a glance through color and visual indicators.

**Requirements:**

- Color-coded health indicators:
  - **Green** — healthy
  - **Amber** — warning
  - **Red** — critical / unreachable
- CPU and memory shown with attractive progress bars or gauges
- Health should be understandable without reading raw numbers first

### Feature 3 — Server Overview + Detail View

**User story:** As an engineer, I scan all servers on the main dashboard, then open one server for full details.

**Requirements:**

- **Overview dashboard** — all monitored Linux servers
- **Detail view** — selected server shows:
  - CPU
  - Memory
  - Network information
  - Listening ports

**Metrics (v1 only):**

| Metric | Display |
|--------|---------|
| CPU | Usage % with progress bar / gauge |
| Memory | Used / total and % with progress bar / gauge |
| Network | Usage / status as available via SSH |
| Ports | Listening port details |

### Feature 4 — Auto Refresh and Manual Refresh

**User story:** As an engineer, metrics stay current automatically, and I can force a refresh when needed.

**Requirements:**

- Auto-refresh at a reasonable fixed interval (e.g. every 5–10 seconds)
- Manual **Refresh** button to request the latest metrics immediately
- Show last refreshed time

### Feature 5 — Simple Search and Filter

**User story:** As an engineer, I quickly find a server by name/IP or by health status.

**Requirements:**

- Search / filter by server name or IP / hostname
- Filter by health status (healthy / warning / critical)

### Feature 6 — Authentication

**User story:** As an admin, I sign in before using the app.

**Requirements:**

- Simple login page
- Default username: `admin`
- Password verified against a stored hash (not plain text)
- No password-change UI
- No multi-user auth or RBAC
- Unauthenticated users cannot access dashboards or server management

### Feature 7 — Add Linux Servers

**User story:** As an admin, I register a Linux server with only the minimum connection details.

**Required fields:**

| Field | Required |
|-------|----------|
| Server name | Yes |
| Hostname / IP | Yes |
| SSH username | Yes |
| SSH port | Yes (default `22` allowed) |

Keep configuration minimal. SSH key path may come from local env/config if needed; do not build a heavy credential UI.

Admin may remove a server from the **in-memory** app inventory. Removal must not change anything on the remote host.

### Feature 8 — Read-Only Monitoring

**User story:** As an operator, I trust that this tool only observes systems.

**Requirements:**

- Monitoring-only: retrieve information over SSH
- **Must not**:
  - Restart services
  - Terminate processes
  - Change configurations
  - Reboot servers
  - Otherwise modify monitored Linux systems

### Feature 9 — Small UI Enhancements

**User story:** As an engineer, the UI feels interactive and clear even when things fail.

**Requirements (keep simple):**

- Online / offline status badges
- Last refreshed time
- Summary counts (e.g. total servers, healthy / warning / critical)
- Light hover effects on cards / interactive elements
- Clear empty states (no servers added)
- Clear error states when a server cannot be reached

---

## 6. Non-Functional Requirements (Demo)

- Run locally on the machine where Cursor is running
- Suitable for local demonstration and for monitoring reachable Linux servers over SSH
- Modern UI with HTML/CSS and light JS only—no heavy frontend framework
- In-memory data is acceptable for the demo (data may reset on restart)
- Fail gracefully if a host is unreachable; do not crash the app
- Do not over-engineer

---

## 7. Out of Scope

- Windows monitoring
- Historical metric storage
- Complex historical graphs
- Alert notifications
- Email / SMS integration
- RBAC and multiple users
- HTTPS setup
- Server remediation / remote actions
- Enterprise database
- Cloud deployment
- Production high availability
- Password change in UI
- Agents installed on target hosts
- Disk, uptime, and process lists beyond the metrics above

---

## 8. Minimal Local Setup (Demo)

1. Install Python dependencies
2. Configure admin password hash (and SSH key/path if needed)
3. Start the Flask app locally
4. Sign in as `admin`
5. Add one or more Linux servers (name, hostname/IP, SSH user, SSH port)
6. Use overview dashboard, open a detail view, try search/filter, and confirm auto + manual refresh

---

## 9. Acceptance Criteria

- [x] Unauthenticated access is blocked
- [x] Login works with `admin` + hashed password
- [x] Admin can add a server with name, hostname/IP, SSH user, and SSH port
- [x] Server inventory is held in memory for the demo
- [x] Overview shows servers as cards with name, IP/hostname, status, CPU, memory, and network status
- [x] Health uses green / amber / red indicators; CPU and memory use progress bars or gauges
- [x] Detail view shows CPU, memory, network, and listening ports
- [x] Metrics auto-refresh and a manual Refresh button works
- [x] Last refreshed time is visible
- [x] Search/filter by name/IP and by health status works
- [x] Summary counts and online/offline badges are present
- [x] Empty and unreachable-server states are clear
- [x] Metric collection uses SSH from Flask
- [x] App never modifies monitored servers
- [x] App runs locally for demo use

---

## 10. Success Definition

Internal teams can open one modern local dashboard, quickly judge Linux server health, drill into CPU/memory/network/ports detail, and avoid logging into each host—using a single admin login, in-memory server list, and SSH-based read-only collection.
