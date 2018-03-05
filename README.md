## Kano OS Recovery

Kano OS Recovery is a systemd service that restores a broken APT database,
due to a sudden power failure during a Kano OS upgrade.

The service runs early on boo, after these services have completed:

 * Kano OS Loader.
 * fsck check disk for any possible file system corruption.
 * Kano low battery check
 * File systems have been remounted in read-write mode


The service runs synchronously - type=oneshot - which guarantees that the bootup process will wait,
until the system software is recovered.

The service will query if APT packages are reported as broken, and a recovery will be attempted only if this is true.

## Querying if system APT is stable

Simply running `sudo kano-os-recovery --info` will display any broken packages, and return exit code 0 if recovery is needed.

## Testing

A simulation option is provided for testing purposes by passing the `--test` parameter,
which can be set in the systemd service file. The recovery will delay the bootup for 30 seconds.

## Debugging

The recovery can take a long number of steps. To fetch the logs after a recovery,
execute this command after bootup: `sudo systemctl status kano-os-recovery -b`.

The best way to debug the service in real time is through a remote serial console,
because it starts shortly after the Kernel takes control.

You will need 2 RaspberryPIs - debugger and test. Follow these steps:

1) Set the cmdline.txt below on the RaspberryPI to be tested:

```
dwc_otg.lpm_enable=0 logo.nologo root=/dev/mmcblk0p2 rootfstype=ext4 elevator=noop ipv6.disable=1
fsck.repair=yes systemd.log_target=console systemd.log_level=info
console=ttyAMA0,115200 kgdboc=ttyAMA0,115200 rootwait splash
```

2) Change the systemd entry to send recovery messages to the remote console: StandardOutput=journal+console

3) Link both the debugger and test RPi, using 3 jumpers.

```
BOARD 6 GNG => BOARD 6 GND
BOARD 8 TXD => BOARD 10 RXD
BOARD 10 RXD => BOARD 8 TXD
```

4) On the debugger Rpi, either install a serial console program like `minicom`, or call `sudo screen /dev/ttyAMA01`.
5) Make sure that both RPIs have the option `enable_uart=1` on their config.txt files.

You an replace the debugger RPi with a UART to USB module, which allows to debug it from a laptop computer.
It must be a TTL compatible at 3V logic.
