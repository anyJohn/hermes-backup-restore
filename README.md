# Hermes Agent Backup & Restore

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

Portable backup and restore scripts for [Hermes Agent](https://hermes-agent.nousresearch.com) data.

## What Gets Backed Up

- Config (`config.yaml`, `.env`, `auth.json`)
- Skills, Memories, Sessions (state.db), Cron
- Kanban, Hooks, SOUL.md
- Platform configs, Skins, Plugins, Widgets, Pets
- Profiles (multi-profile support)
- SQLite databases (via `sqlite3 .backup` for consistent snapshots)

**Excludes:** source code, caches, locks, sockets, logs, runtime ephemera.

## Install

### As a Claude Code / Agent Skill

```bash
# Install via skills.sh CLI
npx skills add anyJohn/hermes-backup-restore

# Or clone manually
git clone https://github.com/anyJohn/hermes-backup-restore.git
```

### Dependencies

```bash
# Required
sudo apt install rsync tar sqlite3   # Debian/Ubuntu
sudo pacman -S rsync tar sqlite       # Arch

# Optional (for manifest parsing)
python3
```

## Quick Start

### Backup

```bash
# Default backup to ~/hermes-backup-<hostname>-<timestamp>.tar.gz
./scripts/hermes-backup.sh

# Custom output path
./scripts/hermes-backup.sh -o /path/to/backup.tar.gz

# Include hermes-agent source code
./scripts/hermes-backup.sh --full

# Back up only a specific profile
./scripts/hermes-backup.sh --profile work

# Preview what would be backed up (no archive created)
./scripts/hermes-backup.sh --dry-run
```

### Restore

```bash
# Restore everything (overwrites existing)
./scripts/hermes-restore.sh backup.tar.gz

# Preview what would be restored
./scripts/hermes-restore.sh backup.tar.gz --dry-run

# Merge (don't overwrite existing files)
./scripts/hermes-restore.sh backup.tar.gz --merge

# Auto-stop running gateway before restore
./scripts/hermes-restore.sh backup.tar.gz --force

# Restore into a specific profile
./scripts/hermes-restore.sh backup.tar.gz --profile work

# Restore to a custom target directory
./scripts/hermes-restore.sh backup.tar.gz -t /target/path
```

## Cross-Machine Migration

```bash
# On machine A
./scripts/hermes-backup.sh -o /tmp/my-backup.tar.gz

# Transfer to machine B
scp /tmp/my-backup.tar.gz user@machineB:/tmp/

# On machine B
./scripts/hermes-restore.sh /tmp/my-backup.tar.gz --force
```

After restoring on a new machine, re-authenticate if API keys or OAuth tokens are machine-specific:

```bash
hermes setup   # or   hermes model
```

## Features

- **Consistent SQLite snapshots**: Uses `sqlite3 .backup` to avoid WAL corruption
- **Gateway safety check**: Restore detects and can auto-stop a running Hermes gateway
- **Manifest**: Every backup includes a JSON manifest with metadata (hostname, user, Hermes version, file list)
- **Profile-aware**: Back up and restore individual profiles
- **Integrity verification**: Restored databases are checked with `PRAGMA integrity_check`
- **Permission hardening**: Config and secrets set to `chmod 600` after restore
- **Dry run**: Both scripts support `--dry-run` for safe preview

## Structure

```
hermes-backup-restore/
├── skills/
│   └── hermes-backup-restore/
│       ├── SKILL.md                          # Frontmatter + usage
│       └── scripts/
│           ├── hermes-backup.sh              # Backup script
│           └── hermes-restore.sh            # Restore script
├── skills.sh.json                           # Category metadata
├── README.md
└── LICENSE
```

## License

MIT

[![skills.sh](https://skills.sh/b/anyJohn/hermes-backup-restore)](https://skills.sh/anyJohn/hermes-backup-restore)
