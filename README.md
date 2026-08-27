# Hermes Agent Backup & Restore

Portable backup and restore scripts for [Hermes Agent](https://hermes-agent.nousresearch.com) data.

## What Gets Backed Up

- Config (`config.yaml`, `.env`, `auth.json`)
- Skills, Memories, Sessions (state.db), Cron
- Kanban, Hooks, SOUL.md
- Platform configs, Skins, Plugins, Widgets, Pets
- Profiles (multi-profile support)
- SQLite databases (via `sqlite3 .backup` for consistent snapshots)

**Excludes:** source code, caches, locks, sockets, logs, runtime ephemera.

## Quick Start

### Backup

```bash
# Default backup to ~/hermes-backup-<hostname>-<timestamp>.tar.gz
./hermes-backup.sh

# Custom output path
./hermes-backup.sh -o /path/to/backup.tar.gz

# Include hermes-agent source code
./hermes-backup.sh --full

# Back up only a specific profile
./hermes-backup.sh --profile work

# Preview what would be backed up (no archive created)
./hermes-backup.sh --dry-run
```

### Restore

```bash
# Restore everything (overwrites existing)
./hermes-restore.sh backup.tar.gz

# Preview what would be restored
./hermes-restore.sh backup.tar.gz --dry-run

# Merge (don't overwrite existing files)
./hermes-restore.sh backup.tar.gz --merge

# Auto-stop running gateway before restore
./hermes-restore.sh backup.tar.gz --force

# Restore into a specific profile
./hermes-restore.sh backup.tar.gz --profile work

# Restore to a custom target directory
./hermes-restore.sh backup.tar.gz -t /target/path
```

## Features

- **Consistent SQLite snapshots**: Uses `sqlite3 .backup` to avoid WAL corruption
- **Gateway safety check**: Restore detects and can auto-stop a running Hermes gateway
- **Manifest**: Every backup includes a JSON manifest with metadata (hostname, user, Hermes version, file list)
- **Profile-aware**: Back up and restore individual profiles
- **Integrity verification**: Restored databases are checked with `PRAGMA integrity_check`
- **Permission hardening**: Config and secrets set to `chmod 600` after restore
- **Dry run**: Both scripts support `--dry-run` for safe preview

## Requirements

- `bash` 4.0+
- `rsync`
- `tar`
- `sqlite3` (recommended — falls back to raw copy if unavailable)
- `python3` (for manifest parsing during restore, optional)

## Cross-Machine Migration

```bash
# On machine A
./hermes-backup.sh -o /tmp/my-backup.tar.gz

# Transfer to machine B
scp /tmp/my-backup.tar.gz user@machineB:/tmp/

# On machine B
./hermes-restore.sh /tmp/my-backup.tar.gz --force
```

After restoring on a new machine, re-authenticate if API keys or OAuth tokens are machine-specific:

```bash
hermes setup   # or   hermes model
```

## License

MIT
