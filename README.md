# Grimmory Stack

[Grimmory](https://github.com/grimmory-tools/grimmory) — self-hosted digital library (community fork of BookLore) for ebooks (EPUB/MOBI/AZW/FB2), PDFs, comics (CBZ/CBR/CB7), and audiobooks (M4B/M4A/MP3), with metadata lookup, built-in readers, OPDS device sync, and multi-user support. Runs on the **N100** (`192.168.1.10`).

Web UI at `https://books.apps.hematic.net`. Requires MariaDB 11.4.5, run as a sidecar container (`grimmory-db`) with its data in a named volume.

## Storage

- `/app/data` — app config/covers/metadata (named volume `grimmory-data`)
- `/books` — the book library. Grimmory organizes files inside it, so it is mounted **read-write** (unlike the Jellyfin media mounts).
- `/bookdrop` — watched ingest folder; files dropped here are auto-detected, metadata-matched, and queued for review.

## Environment Variables (`stack.env`)

| Variable | Description |
|---|---|
| `NETWORK` | Docker network (e.g. `proxy`) |
| `PUID` / `PGID` | User/group IDs |
| `TZ` | Timezone |
| `BOOKS` | Host path to the book library (NAS mount) |
| `BOOKDROP` | Host path to the bookdrop ingest folder |
| `DB_USER` | MariaDB user for Grimmory (e.g. `grimmory`) |
| `DB_PASSWORD` | MariaDB password for that user |
| `DB_ROOT_PASSWORD` | MariaDB root password |

## Deployment

Deployed via Komodo to the N100 periphery. Confirm the `BOOKS` / `BOOKDROP` paths are mounted on the N100 before deploying. First user to register in the web UI becomes admin.

Traefik (NUC) routes `books.apps.hematic.net` → `http://192.168.1.10:6060` with `default-headers` + `geoblock` (no Authelia — Grimmory has its own auth, and OPDS clients need direct access), configured in `traefik_stack/config/dynamic/combined-services.yml`.
