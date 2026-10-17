# RomHack Camp 2026 badge with the USB HID FIDO mode

The [badge firmware](https://github.com/CyberSaiyanIT/rhc2026-badge) carries its own copy of Tactility
(ESP-IDF 5.5.2) in `firmware/tactility`. The workflow `.github/workflows/rhc2026-badge.yml` on this
branch builds it with:

- `0001-Add-USB-HID-FIDO-mode-for-CTAPHID-authenticators.patch`: the same patch as branch
  `usb-hid-fido-mode`, applied without its `Devices/` changes (the badge's USB HID driver is
  identical to upstream's).
- `0002-romhack2026-badge-usb-fido-hid.patch`: enables `usbdevice0`/`usbdevicehid0` and
  `hardware.tinyUsbHid` on the `romhack2026-badge` device.

Artifacts of a run:

| Artifact | Contents |
|---|---|
| `rhc2026-badge-fido-full-image` | `rhc2026-badge-fido.bin`: Tactility + Retro-Go, one 16 MB image for offset `0x0` |
| `rhc2026-badge-fido-tactility` | Tactility only, with `flash_project_args` |
| `rhc2026-badge-fido-sdk` | the TactilitySDK of this firmware, laid out for `TACTILITY_SDK_PATH` |

Flashing the full image erases the badge's data partition (like the badge's own release image):

```sh
python -m esptool --chip esp32s3 -p /dev/ttyACM0 -b 460800 write-flash 0x0 rhc2026-badge-fido.bin
```

The app that uses the FIDO mode is [passkey-tactility](https://github.com/RobertoD91/passkey-tactility).
