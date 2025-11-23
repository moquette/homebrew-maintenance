# Homebrew Maintenance

A comprehensive shell script for automated Homebrew package management, system maintenance, and health checks on macOS.

## Features

### Core Maintenance
- ✅ **Update Homebrew** - Keep formulae repositories current
- ✅ **Upgrade Packages** - Update all installed formulae to latest versions
- ✅ **Upgrade Casks** - Update all installed applications (casks)
- ✅ **Cleanup** - Remove orphaned dependencies and old package versions
- ✅ **Health Checks** - Run `brew doctor` for diagnostic validation

### Advanced Maintenance
- ✅ **Tap Management** - Update all third-party taps automatically
- ✅ **Symlink Validation** - Detect and remove broken symlinks
- ✅ **Disk Usage Report** - Show space usage by Cellar, Cache, and Git
- ✅ **Git Pruning** - Compress Homebrew's git repository for space savings
- ✅ **Permission Repair** - Fix Homebrew directory ownership (optional, with sudo)

### Developer-Friendly
- ✅ **Verbose Mode** - Detailed debug output for troubleshooting
- ✅ **Dry-run Mode** - Preview changes without executing
- ✅ **Task Selection** - Run only specific maintenance tasks
- ✅ **Execution Timing** - See how long each task takes
- ✅ **System Notifications** - Get macOS notifications on completion
- ✅ **Graceful Error Handling** - Continue on non-critical failures
- ✅ **Signal Handling** - Properly cleanup on interruption (Ctrl+C)

## Installation

### Quick Install with Curl

```bash
# Download and run directly
curl -sL https://raw.githubusercontent.com/moquette/homebrew-maintenance/main/maintenance | zsh

# Or download to current directory
curl -sL https://raw.githubusercontent.com/moquette/homebrew-maintenance/main/maintenance -o homebrew-maintenance && chmod +x homebrew-maintenance && echo "✓ Saved to: $(pwd)/homebrew-maintenance"

# Then move it wherever you prefer (e.g., ~/bin, /usr/local/bin, etc.)
mv homebrew-maintenance ~/bin/  # or any other location
```

### Clone Setup

```bash
# Clone or download to your preferred location
git clone https://github.com/moquette/homebrew-maintenance ~/bin/homebrew-maintenance
cd ~/bin/homebrew-maintenance

# Make executable
chmod +x maintenance

# Optional: Add to PATH for easy access
ln -s "$PWD/maintenance" /usr/local/bin/maintenance
```

### Verify Installation

```bash
./maintenance --help
```

## Usage

### Basic Commands

```bash
# Run full maintenance
./maintenance

# Preview changes without executing
./maintenance -n

# Show detailed debug output
./maintenance -v

# Preview with verbose output
./maintenance -v -n
```

### Task Selection

```bash
# Update Homebrew only
./maintenance --update-only

# Upgrade packages and casks, skip cleanup
./maintenance --upgrade-only

# Cleanup operations only
./maintenance --cleanup-only
```

### Skip Specific Tasks

```bash
# Skip cask upgrades
./maintenance --skip-cask

# Skip cleanup operations
./maintenance --skip-cleanup

# Skip health checks
./maintenance --skip-doctor

# Skip tap updates
./maintenance --skip-taps

# Skip symlink checks
./maintenance --skip-links

# Skip git pruning
./maintenance --skip-prune
```

### Advanced Options

```bash
# Enable permission repair (requires sudo)
./maintenance --repair-permissions

# Combine multiple options
./maintenance -v --upgrade-only --skip-cask

# Preview with all optional tasks
./maintenance -n -v --repair-permissions
```

### Help

```bash
./maintenance --help
./maintenance -h
```

## Output Examples

### Standard Run
```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🍺 Homebrew maintenance
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

→ Updating Homebrew formulae
✓ Updating Homebrew formulae completed (3s)
✓ All formulae up to date
→ Removing orphaned dependencies
✓ Removing orphaned dependencies completed (1s)
ℹ Homebrew cache already clean
→ Running brew doctor health check
✓ Homebrew is ready to brew

ℹ Found 2 tap(s)
→ Updating taps
✓ Updating taps completed (2s)

→ Checking symlinks
✓ All symlinks are valid

→ Disk usage report
ℹ Homebrew disk usage:
ℹ   Cellar: 1.5G
ℹ   Cache: 578M
ℹ   Git: 75M

→ Pruning Homebrew git history
✓ Git history pruned (128M → 75M)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Summary (25s)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

ℹ Successful tasks: 9
ℹ Failed tasks: 0
ℹ Skipped tasks: 0
```

### Dry-run Mode
```bash
$ ./maintenance -n

ℹ [dry-run] Would update Homebrew
ℹ [dry-run] Would upgrade 3 package(s)
ℹ [dry-run] Would clean 125M from cache
ℹ [dry-run] Would run brew doctor health check
```

### Verbose Mode
```bash
$ ./maintenance -v

◆ DEBUG: Verbose mode enabled
◆ DEBUG: DRY_RUN: false
◆ DEBUG: SKIP_UPDATE: false
◆ DEBUG: SKIP_UPGRADE: false
◆ DEBUG: Homebrew found at: /opt/homebrew/bin/brew
```

## Task Summary

The script tracks and reports three categories of tasks:

- **Successful Tasks** - Operations that completed without errors
- **Failed Tasks** - Operations that encountered issues
- **Skipped Tasks** - Tasks skipped due to dry-run mode or explicit flags

## Environment Variables

```bash
# Enable dry-run mode via environment variable
export DRY_RUN=true
./maintenance

# Disable notifications (they still work via macOS)
osascript disabled
```

## Scheduling with Cron/LaunchAgent

### Run Weekly via Cron
```bash
# Edit crontab
crontab -e

# Add this line for Sunday at 2 AM
0 2 * * 0 /path/to/maintenance -n >> ~/Library/Logs/homebrew-maintenance.log 2>&1
```

### Run Weekly via LaunchAgent (Recommended for macOS)

Create `~/Library/LaunchAgents/com.homebrew.maintenance.plist`:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>Label</key>
    <string>com.homebrew.maintenance</string>
    <key>ProgramArguments</key>
    <array>
        <string>/path/to/maintenance</string>
    </array>
    <key>StartCalendarInterval</key>
    <dict>
        <key>Hour</key>
        <integer>2</integer>
        <key>Minute</key>
        <integer>0</integer>
        <key>Weekday</key>
        <integer>0</integer>
    </dict>
    <key>StandardOutPath</key>
    <string>/var/log/homebrew-maintenance.log</string>
    <key>StandardErrorPath</key>
    <string>/var/log/homebrew-maintenance-error.log</string>
</dict>
</plist>
```

Then load it:
```bash
launchctl load ~/Library/LaunchAgents/com.homebrew.maintenance.plist
```

## Performance Metrics

Each operation is timed and displayed. Here are typical execution times:

- **Update Homebrew** - 2-5s
- **Upgrade packages** - Variable (1-30s depending on updates)
- **Autoremove** - 1-3s
- **Cache cleanup** - 1-5s
- **Brew doctor** - 3-10s
- **Tap updates** - 1-5s
- **Symlink check** - <1s
- **Git pruning** - 2-10s (saves significant space)

**Total runtime:** 15-60 seconds (depending on system and updates available)

## Troubleshooting

### Homebrew not found
```bash
error "Homebrew not installed or not in PATH"
```
**Solution:** Install Homebrew or ensure it's in your PATH.

### Permission denied on git pruning
```bash
error "Git pruning failed"
```
**Solution:** Usually recovers automatically. Try running again.

### Password prompt for permissions
```bash
# Use this flag to skip permission repair by default
./maintenance --skip-permissions

# Or enable only when needed
./maintenance --repair-permissions
```

### Slow performance
- Use `--update-only` to skip heavy operations
- Check disk space: `df -h`
- Run with `--dry-run` to preview without waiting

### Notifications not appearing
- Check macOS Settings > Notifications
- Verify Focus mode isn't blocking them
- Restart the script: `./maintenance -v`

## Requirements

- macOS (10.12+)
- Homebrew installed
- Zsh or Bash shell
- Sudo access (optional, only for `--repair-permissions`)

## File Structure

```
maintenance/                    # Main script directory
├── maintenance                 # Executable script
└── README.md                   # This file
```

## Contributing

To report issues or suggest features:
1. Test thoroughly with `./maintenance -v -n`
2. Document the exact command and output
3. Include your Homebrew version: `brew --version`

## Maintenance Notes

### When to Use Each Feature

| Feature | When to Use |
|---------|-----------|
| `--update-only` | Quick formulae check without upgrades |
| `--upgrade-only` | Install updates without cleanup |
| `--cleanup-only` | Free up disk space |
| `--repair-permissions` | After Homebrew installation issues |
| `--skip-prune` | When working with git-based formulae |
| `-v` (verbose) | Debugging or learning what's happening |
| `-n` (dry-run) | Safe preview before running |

### Performance Tips

1. **First run** - Takes longer (downloading updates)
2. **Subsequent runs** - Faster if nothing changed
3. **Git pruning** - Saves 20-50% disk space over time
4. **Cask updates** - Skip if you manage app updates manually

## Advanced Usage

### Create Alias
```bash
# Add to ~/.zshrc or ~/.bashrc
alias brew-maint='~/bin/homebrew-maintenance/maintenance'

# Then use as
brew-maint -v
```

### Chain Multiple Runs
```bash
# Update only, then cleanup
./maintenance --update-only && ./maintenance --cleanup-only
```

### Redirect Logs
```bash
# Save output to file
./maintenance >> ~/homebrew-maintenance.log 2>&1

# Append to log with timestamp
./maintenance 2>&1 | sed 's/^/['"$(date)"'] /' >> ~/homebrew-maintenance.log
```

## License

MIT License - See [LICENSE](./LICENSE) file for details. Copyright © 2025 Joaquin A. Moquette

## Support

For issues or questions, run with verbose mode for diagnostic info:
```bash
./maintenance -v
```

This provides detailed debug output helpful for troubleshooting.

## Version History

- **v1.0** - Initial release

---

**Last Updated:** November 2025
