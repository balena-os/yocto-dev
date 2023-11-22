# yocto-dev

Shared Yocto development environment

## Features

- Yocto and OpenEmbedded build dependencies based on Ubuntu 18.04
- SSH daemon background service w/ rotating logs
- Fail2ban blocking IPs after failed login attempts
- Docker daemon background service
- Per-user home directories
- Per-user SSH authorized keys synced with GitHub profiles

## Planned Features

- Update locking

## Administration

### Adding users

1. Open [yocto-build-env/s6-overlay/scripts/addusers](yocto-build-env/s6-overlay/scripts/addusers) for editing
2. Add GitHub username and new user ID to `user_ids`
3. Open PR and merge

### Removing users

1. Open [yocto-build-env/s6-overlay/scripts/addusers](yocto-build-env/s6-overlay/scripts/addusers) for editing
2. Remove GitHub username and user ID from `user_ids`
3. Open PR and merge
4. Optionally delete the associated home directory on device
