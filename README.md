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

## Querying if system APT is stable

Simply running `sudo kano-os-recovery --info` will display any broken packages, and return exit code 0 if recovery is needed.

## Testing

Additionally, a simulation option is provided for testing purposes by passing the `--test` parameter,
which can be set in the systemd service file. The recovery will delay the bootup for 30 seconds.
