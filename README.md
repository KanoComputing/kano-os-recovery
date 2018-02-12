## Kano OS Recovery

Kano OS Recovery is a systemd service that restores a broken APT database,
due to a sudden power failure during a Kano OS upgrade.

The service runs early on boot, immediately after kano-os-loader.

It is executed synchronously - type=oneshot - which guarantees that the bootup process will wait,
until the system software is recovered.

The service will query if APT is broken, and only if so, a recovery will be attempted.

Due to the earliness of the operation, the rootfs and boot partitions are manually mounted read-write.
After the recovery, they are restored to the previous state.

To fetch the log from a recovery, execute after bootup: `sudo systemctl status kano-os-recovery -b`.
