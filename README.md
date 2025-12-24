# EAFS - Eggdrop Anti Flood Script

**Version:** 1.0.5 (2007.11.04)

An anti join/part flood script for Eggdrop bots on the Undernet IRC Network. This script uses channel modes +D (delay join) and +m (moderated) combined with selective voice granting to protect channels from flooding attacks.

## Features

- Automatic flood protection using +D and +m channel modes
- Intelligent user classification and voice granting based on authentication status
- Proxy detection and blacklist management
- Configurable delays for different user types
- Manual activation/deactivation controls
- Persistent blacklist storage
- Debug mode for troubleshooting

## Requirements

- Eggdrop bot with operator status in the target channel
- Undernet IRC Network
- TCL support

## Installation

1. Copy `eafs.tcl` to your Eggdrop scripts directory
2. Add the following line to your `eggdrop.conf`:
   ```tcl
   source scripts/eafs.tcl
   ```
3. Configure the script settings (see Configuration section)
4. Rehash or restart your bot

## Configuration

Edit the configuration section at the top of `eafs.tcl`:

```tcl
# Target channel
set namechan "#yourchannel"

# Back channel for notifications (optional, set to "" to disable)
set backchan "#ops"

# Check interval in seconds (minimum recommended: 10)
set period 15

# Starting status: "on" or "off"
set status "off"

# Active/inactive channel modes
set active_modes "+Dm"
set inactive_modes "-Dm"

# Voice delay for logged users without +x (seconds)
set logged_no_x 10

# Voice delay for non-logged users (seconds)
set not_logged 30

# Welcome messages
set welcome_no_x "Welcome! You will be voiced shortly."
set welcome_not_logged "Welcome! You will be voiced after verification."

# Block known proxies from receiving voice (1=yes, 0=no)
set noproxysvoiced 1

# Proxy notification target: "f" for front channel, "b" for back channel
set proxymsgtarget "b"
```

## User Classification

The script categorizes users into three types:

1. **Type 1** - Logged in with +x mode (`*.users.undernet.org`)
   - Voiced immediately
   
2. **Type 2** - Logged in without +x mode
   - Voiced after `logged_no_x` seconds
   - Receives welcome message

3. **Type 3** - Not logged in
   - Scanned for proxy ports
   - Voiced after `not_logged` seconds (if not on blacklist)
   - Receives welcome message

## Commands

All commands require operator status (global +o or channel +o):

- `!eafs on` - Activate the script
- `!eafs off` - Deactivate the script
- `!eafs status` - Check current status
- `!eafs debug-on` - Enable debug mode
- `!eafs debug-off` - Disable debug mode
- `!eafs voice-list` - Display users waiting for voice
- `!eafs addproxy <host/ip>` - Add host to blacklist
- `!eafs delproxy <host/ip>` - Remove host from blacklist

## Proxy Detection

The script scans for common proxy ports:
- 179, 1080, 3138, 8080, 8088, 2080, 8010, 5490

Detected proxies are:
- Added to `proxys.txt` blacklist file
- Optionally blocked from receiving voice (based on `noproxysvoiced` setting)
- Reported to operators via channel notices or back channel

## Files

- `eafs.tcl` - Main script file
- `proxys.txt` - Blacklist file (created automatically)

## How It Works

1. When activated, the script sets the channel to +D+m modes
2. Periodically scans for invisible users using `/NAMES -d`
3. Checks each user with `/WHO` command
4. Classifies users based on authentication status
5. Performs proxy scan for unauthenticated users
6. Grants voice after appropriate delay
7. Maintains blacklist of detected proxies

## Troubleshooting

- **Bot not giving voice:** Ensure bot has operator status
- **Script not starting:** Check that channel name is correct
- **Excessive lag:** Increase `period` value (minimum 10 seconds)
- **False proxy detections:** Adjust `proxy_ports` list in script

Enable debug mode to see detailed logs:
```
!eafs debug-on
```

## Credits

- **Project:** #ayuda @ Undernet
- **Coded by:** ^The_law^ (#thelawchannel @ Undernet)

## Disclaimer

This script is provided as-is without any guarantees. Use at your own risk.

## License

Free to use and modify. Please retain original credits.
