# OmniFetch Plugin for Unraid API

Extended functionality plugin for the Unraid API providing hardware monitoring, power tracking, and VM snapshot management.

## Installation

### Via Unraid Community Applications (Recommended)

1. Open the **Apps** tab in Unraid WebUI
2. Search for "OmniFetch"
3. Click **Install**

### Manual Installation

1. Go to **Plugins** tab in Unraid WebUI
2. Click **Install Plugin**
3. Paste this URL:
   ```
   https://raw.githubusercontent.com/FetchLabsLtd/unraid-api-plugin-omnifetch-release/main/omnifetch.plg
   ```
4. Click **Install**

## Features

### Power Monitoring
- Real-time power consumption tracking
- Historical data collection (5-minute intervals)
- Cost calculation and tracking
- Integration with various power monitoring tools

### Hardware Sensors
- Temperature monitoring (CPU, motherboard, drives)
- Fan speed monitoring
- Voltage monitoring
- lm-sensors integration

### GPU Monitoring
- GPU statistics and utilization
- Temperature and power tracking
- Multi-GPU support

### VM Snapshot Management
- Create VM snapshots with memory state
- Delete snapshots with proper cleanup
- Revert to snapshots (removes snapshot after revert)
- Full Unraid WebUI compatibility
- Automatic database synchronization

### System Management
- System uptime tracking
- Shutdown and reboot scheduling
- Operation cancellation
- GraphQL API extensions

## GraphQL API

### Queries

**OmniFetch Data**
```graphql
query {
  omnifetch {
    data {
      hardware {
        fans
        temperatures
        voltages
        capabilities
        gpu
      }
      power {
        available
        current
        daily
        cost
      }
      status
    }
  }
}
```

**VM Listing**
```graphql
query {
  listVMs {
    available
    vms
    totalVMs
    runningVMs
    stoppedVMs
  }
}
```

**VM Details**
```graphql
query {
  getVMDetail(name: "VMName") {
    vm
  }
}
```

### Mutations

**Create VM Snapshot**
```graphql
mutation {
  createVMSnapshot(
    vmName: "VMName"
    snapshotName: "snapshot-name"
    description: "Optional description"
  )
}
```

**Delete VM Snapshot**
```graphql
mutation {
  deleteVMSnapshot(
    vmName: "VMName"
    snapshotName: "snapshot-name"
  )
}
```

**Revert VM Snapshot**
```graphql
mutation {
  revertVMSnapshot(
    vmName: "VMName"
    snapshotName: "snapshot-name"
  )
}
```

**System Operations**
```graphql
mutation {
  shutdownSystem(input: { delayMinutes: 0, message: "Shutting down" })
}

mutation {
  rebootSystem(input: { delayMinutes: 0, message: "Rebooting" })
}

mutation {
  cancelSystemOperation
}
```

## Requirements

- Unraid 7.0.0 or higher
- Unraid API installed and running
- Node.js 20+ (provided by Unraid API)
- PM2 process manager (provided by Unraid API)

### Optional Dependencies

- **lm-sensors** - For hardware temperature/fan/voltage monitoring
  ```bash
  # Install via Nerd Tools plugin or manually
  sensors-detect --auto
  ```

- **Power monitoring tool** - Such as:
  - nut-upsc (for UPS monitoring)
  - Custom power monitoring scripts

## Configuration

The plugin integrates directly with the Unraid API and requires no additional configuration. All settings are managed through the GraphQL API.

### Environment Variables

Configure via `/boot/config/plugins/OmniFetch/settings.cfg`:

```bash
# Power monitoring collection interval (seconds)
POWER_COLLECTION_INTERVAL=300

# Enable detailed logging
DEBUG=false
```

## Snapshot Management

### How Snapshots Work

OmniFetch creates **external QCOW2 snapshots** that are fully compatible with Unraid's VM Manager:

1. **Creating a snapshot**:
   - Captures disk state as QCOW2 overlay
   - Saves memory state for running VMs (`.mem` file)
   - Creates VM state XML (`.running` file)
   - Updates Unraid's snapshot database

2. **Reverting a snapshot**:
   - Restores VM to snapshot state
   - Removes the snapshot after reverting (matches Unraid WebUI behavior)
   - Cleans up all snapshot files

3. **Deleting a snapshot**:
   - Removes snapshot metadata
   - Cleans up snapshot files
   - Updates Unraid database

### Snapshot Files

Snapshots are stored in `/mnt/user/vDisks/{VMName}/`:
- `vdisk1.{snapshotName}.qcow2` - Disk overlay
- `memory.{snapshotName}.mem` - Memory dump (running VMs)
- `{snapshotName}.running` - VM state XML

### WebUI Integration

Snapshots created via the API appear immediately in:
- Unraid VM Manager WebUI
- `virsh snapshot-list` command
- GraphQL `listVMs` query

## Troubleshooting

### Plugin Not Loading

```bash
# Check if plugin is installed
ls -la /usr/local/unraid-api/plugins/omnifetch/

# Check Unraid API logs
tail -f /var/log/graphql-api.log | grep OmniFetch

# Restart Unraid API
pm2 restart unraid-api
```

### lm-sensors Not Detected

```bash
# Install sensors package (via Nerd Tools or manually)
sensors-detect --auto

# Test sensors
sensors

# Restart plugin
pm2 restart unraid-api
```

### Snapshot Creation Fails

```bash
# Check VM state
virsh domstate VMName

# Check disk space
df -h /mnt/user/vDisks/

# Check logs
tail -f /var/log/graphql-api.log | grep VMService
```

## Support

- **GitHub Issues**: [Report bugs or request features](https://github.com/FetchLabsLtd/unraid-api-plugin-omnifetch-release/issues)
- **Unraid Forums**: [Plugin Support Thread](https://forums.unraid.net/topic/...)

## Documentation

- **API Reference**: Full GraphQL API documentation available at [omnifetch.app/docs](https://omnifetch.app/docs)
- **Changelog**: See [CHANGELOG.md](CHANGELOG.md) for version history
- **Setup Guide**: Detailed installation and configuration instructions

## License

**Free for Non-Commercial Use**

```
NON-COMMERCIAL USE LICENSE

Copyright (c) 2025 FetchLabs Ltd. All rights reserved.

GRANT OF LICENSE
FetchLabs Ltd. grants you a free, non-exclusive, non-transferable license to
use, copy, and modify this software for NON-COMMERCIAL purposes only.

NON-COMMERCIAL USE DEFINITION
Non-commercial use means use solely for personal, educational, academic, or
non-profit purposes where no monetary compensation or commercial advantage is gained.

PERMITTED USES (Non-Commercial)
You MAY:
- Use the Software for personal home server monitoring
- Use the Software for educational or research purposes
- Modify the Software for your own non-commercial use
- Share the Software with others for non-commercial purposes

RESTRICTIONS
You may NOT:
- Use the Software for any commercial purpose, including:
  * Providing services to third parties for compensation
  * Using the Software in a commercial product or service
  * Selling, licensing, or sublicensing the Software
- Remove or alter any copyright notices or license information

COMMERCIAL LICENSING
For commercial use, enterprise deployments, or revenue-generating activity,
contact us on Discord: https://discord.gg/T2hMfq4hWy

DISCLAIMER
THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND.
```

This plugin is provided as compiled code. Source code is proprietary.

## Support & Contact

For issues, questions, and commercial licensing:

- **Discord**: [Join our Discord](https://discord.gg/T2hMfq4hWy)
- **Issues**: [Report bugs or request features](https://github.com/FetchLabsLtd/unraid-api-plugin-omnifetch-release/issues)
- **Unraid Forums**: [Plugin Support Thread](https://forums.unraid.net/topic/...)
- **Documentation**: [omnifetch.app/docs](https://omnifetch.app/docs)

---

**Built by FetchLabs** | [Website](https://fetchlabs.co.uk) | [OmniFetch App](https://omnifetch.app) | [GitHub](https://github.com/FetchLabsLtd)
