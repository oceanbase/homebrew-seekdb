# SeekDB Homebrew Package

This directory contains the Homebrew installation configuration for OceanBase SeekDB.

## Directory Structure

```
homebrew-seekdb/
├── Formula/
│   └── seekdb.rb              # Homebrew formula
└── README.md                  # This document
```

## Installation

### Install via Homebrew Tap

```bash
# Add tap (if not already added)
brew tap oceanbase/seekdb

# Install SeekDB
brew install seekdb
```

After installation, SeekDB will automatically create the following directory structure:
- Data directory: `/opt/homebrew/var/seekdb/data`
- PID file: `/opt/homebrew/var/seekdb/run/seekdb.pid`
- Log file: `/opt/homebrew/var/seekdb/data/log/seekdb.log`

## Usage

### Starting SeekDB

#### Method 1: Using Management Commands (Recommended)

```bash
seekdb-start
```

This will start SeekDB as a background daemon and automatically:
- Check if it's already running
- Create necessary directories
- Write PID file
- Output logs to log file

#### Method 2: Foreground Mode (for debugging)

```bash
seekdb --nodaemon
```

Foreground mode starts faster on first launch, suitable for debugging and development.

You can also specify a custom data directory:

```bash
seekdb --base-dir=/custom/path
```

### Stopping SeekDB

#### Method 1: Using Management Commands (Recommended)

```bash
seekdb-stop
```

This will:
- Read the PID file
- Force terminate the process using `kill -KILL`
- Clean up the PID file

### Checking Status

#### Method 1: Using Management Commands (Recommended)

```bash
seekdb-status
```

This will display:
- Whether the process is running
- PID information
- Data directory, PID file, and log file paths
- Detailed process information (CPU, memory, runtime, etc.)

#### Method 2: Check Process

```bash
# Check PID file
cat /opt/homebrew/var/seekdb/run/seekdb.pid

# Or use ps
ps aux | grep seekdb
```

### Connecting to Database

```bash
# Using MySQL client
mysql -h 127.0.0.1 -P 2881 -u root

# Or use mycli (better command-line experience)
mycli -h 127.0.0.1 -P 2881 -u root
```

## File Paths

When installed via Homebrew, the default paths are as follows:

### Apple Silicon Mac (M1/M2/M3)
- Data directory: `/opt/homebrew/var/seekdb/data`
- PID file: `/opt/homebrew/var/seekdb/run/seekdb.pid`
- Log file: `/opt/homebrew/var/seekdb/data/log/seekdb.log`
- Binary file: `/opt/homebrew/bin/seekdb`
- Management commands: `/opt/homebrew/bin/seekdb-start`, `/opt/homebrew/bin/seekdb-stop`, `/opt/homebrew/bin/seekdb-status`

### Intel Mac
- Data directory: `/usr/local/var/seekdb/data`
- PID file: `/usr/local/var/seekdb/run/seekdb.pid`
- Log file: `/usr/local/var/seekdb/data/log/seekdb.log`
- Binary file: `/usr/local/bin/seekdb`
- Management commands: `/usr/local/bin/seekdb-start`, `/usr/local/bin/seekdb-stop`, `/usr/local/bin/seekdb-status`

## Port Information

| Port | Purpose |
|------|---------|
| 2881 | MySQL protocol port |

## Dependencies

SeekDB depends on the following Homebrew packages:
- `zstd` - Compression library
- `utf8proc` - UTF-8 processing library
- `thrift` - RPC framework
- `re2` - Regular expression library
- `brotli` - Compression algorithm

These dependencies will be automatically installed when installing SeekDB.

## Troubleshooting

### SeekDB Fails to Start

1. **Check if port is in use**:
```bash
lsof -i :2881
```

2. **Check log file**:
```bash
# View logs
tail -f /opt/homebrew/var/seekdb/data/log/seekdb.log

# Or
cat /opt/homebrew/var/seekdb/data/log/seekdb.log
```

3. **Check PID file**:
```bash
# If PID file exists but process doesn't, it might be a stale file
cat /opt/homebrew/var/seekdb/run/seekdb.pid

# Clean up stale PID file
rm /opt/homebrew/var/seekdb/run/seekdb.pid
```

4. **Check disk space**:
```bash
df -h
```

### Permission Issues

```bash
# Ensure data directory has correct permissions
chmod -R 755 /opt/homebrew/var/seekdb
```

### macOS Security Settings

If macOS blocks the application from running:
1. Open "System Preferences" > "Security & Privacy"
2. Click "Open Anyway" or "Allow" to permit SeekDB to run

### Insufficient Memory

SeekDB requires a minimum of 2GB of memory. Check system memory:
```bash
sysctl hw.memsize
```

### Slow Background Startup

The first background startup may take approximately 10 seconds, which is normal macOS thread priority optimization behavior. For faster startup, you can use foreground mode:
```bash
seekdb --nodaemon
```

## Uninstallation

```bash
# Stop service
seekdb-stop

# Uninstall SeekDB
brew uninstall seekdb

# Remove data directory (optional, will delete all data)
rm -rf /opt/homebrew/var/seekdb
```

## Version Information

- Current version: 1.0.0
- Homepage: https://github.com/oceanbase/seekdb
- License: Apache-2.0

## Related Links

- [OceanBase SeekDB](https://github.com/oceanbase/seekdb)
- [Homebrew Formula Cookbook](https://docs.brew.sh/Formula-Cookbook)
