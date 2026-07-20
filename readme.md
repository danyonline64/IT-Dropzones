# IT Dropzones

A small, self-hosted web app for quickly sharing files, links, text snippets and notes — no database, no user accounts, just a password.

**Perfect for IT technicians who need to instantly move files, snippets, or links from one machine to another, from a smartphone to a PC, or vice versa, without any friction.**

Built for personal / small-team use: 5 independent workspaces, installable as an app (PWA) on mobile and desktop, and usable for temporary public sharing.

![IT Dropzones Dashboard](captureecran2.png)

## Features

- **5 independent workspaces**, each with its own files, URLs, snippets and a notepad.
- **Smart input**: paste or type content into a single field and the app automatically routes links to URLs and everything else to Snippets.
- **Drag-and-drop** file uploads, paste images directly from the clipboard.
- **Move items between workspaces** by dragging one or several selected items onto another workspace tab.
- **Bulk actions**: select multiple files, URLs or snippets to delete them at once, download selected files as a ZIP, or copy selected snippets to the clipboard (newline-separated).
- **Public sharing** of a workspace via a link (read-only, or read + write), revocable at any time. Shared workspaces are visually flagged.
- **Trash** with restore, automatic 30-day retention.
- **QR code sign-in**: scan a link with any QR code reader app — or with the built-in scanner button on mobile — from a device that's already signed in, to instantly authorize a new device for 1 hour without retyping the password.
- **Installable PWA** (home screen icon on mobile, full-screen mode).
- **Password authentication**, with an optional "stay signed in" (7 days).
- Custom name per workspace, plus an optional app-wide logo.
- **Multilingual UI**: French, English and Basque, with a discreet language switcher (top-right corner). The choice is remembered in a cookie.
- **Multiple color themes** (Iluna, Natura, Amber, Zuri-beltz, Beltz-zuri), selectable per browser in Settings, with a Docker-configurable default.
- **Display preferences**: hide empty columns or the notepad, remembered per browser.

## Tech stack

- **Backend**: Python / Flask, single file (`app.py`)
- **Storage**: plain JSON files on disk (`uploads/`) — no database
- **Frontend**: HTML/CSS/JS embedded in Jinja2 templates, no front-end framework
- **Production server**: Gunicorn (see `Dockerfile`)


## Environment variables

| Variable | Default | Description |
| --- | --- | --- |
| `PASSWORD` | `yourpassword` | Access password for the application |
| `SECRET_KEY` | auto-generated and stored in `uploads/secret_key.txt` | Flask session signing key. Set it explicitly if you run multiple workers/instances. |

## Production Deployment (Docker Compose)

Create a `docker-compose.yml` file and paste the following configuration:

```yaml
version: '3.8'
services:
  it-dropzones:
    image: argitasuna/it-dropzones:latest
    container_name: it-dropzones
    ports:
      - "8080:5000"
    volumes:
      - ./uploads:/app/uploads
    environment:
      - PASSWORD=your_password # Only required for public access; ignored on local network
      - DEFAULT_LANG=en
      - DEFAULT_THEME= iluna
    restart: unless-stopped
```

Run the application with:

```bash
docker compose up -d
```

⚠️ Make sure to mount `/app/uploads` as a volume: it holds uploaded files, the data store (`data.json`), auth tokens and the secret key. Without it, everything is lost on every container redeploy.

## Sharing a workspace

Public sharing is only available when the app is served over HTTPS on a real domain (it's disabled for local/LAN access, where full access is already direct). Each share link can be instantly revoked by the workspace owner, and write access (add/edit/delete) can be toggled independently from read access.

## Security

- Auth cookie is `HttpOnly`, `Secure` (over HTTPS), `SameSite=Lax`.
- Single shared password — no multi-account management. Change `PASSWORD` before deploying.
- Set `SECRET_KEY` explicitly in production if the app runs with multiple workers or multiple instances.

---

© 2026 Argitasuna
