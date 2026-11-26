## Custom firmware 01.01.00.03 – configurable static IPv4 (v4.6a.2, 26 November 2026)

| Date format | Firmware v4.6a.2 | Testing |
| --- | --- | --- |
| UK: DD-MM-YYYY | [Download UK date version](Firmware/01.01.00.03/panda_sense_pro_01.01.00.03_UK-SI-APv2-STATIC-POC-v4.6a.2-WEB-DHCP-RESET.bin) | Tested on hardware |
| Original BTT: YYYY-MM-DD | [Download original date version](Firmware/01.01.00.03/panda_sense_pro_01.01.00.03_Original-date-SI-APv2-STATIC-POC-v4.6a.2-WEB-DHCP-RESET.bin) | Binary integrity verified; hardware testing outstanding |

Both variants retain the same static IPv4, SI notation, AP toggle and web factory reset functionality. The original-date variant restores the vendor date format on the dashboard and screensaver; only 21 date-related bytes and the image integrity fields differ from the UK build.

Unofficial, hardware-tested community firmware based on BIGTREETECH 01.01.00.03. This is the currently tested static IPv4 build, not an official BTT release. It retains the earlier UK date, proofreading, SI notation, font, web interface and AP toggle corrections.

### What's new

- Wi-Fi & IP settings now offer Automatic (DHCP) or Manual (static IPv4).
- Manual mode provides IP address, subnet mask, gateway, primary DNS and optional secondary DNS, with input validation.
- Settings are saved and verified in non-volatile storage; restart separately to apply them.
- DHCP remains the default when no valid manual IPv4 record is present. A completely fresh device has not yet been physically tested with this build.
- The web factory-reset action first saves verified DHCP defaults before requesting the original BTT reset. It aborts if the save is not confirmed. The physical-button reset and direct WebSocket reset command do not use this new preparation.
- Addresses are configurable; no personal static address is preconfigured by this release.

This addresses the manual-IP feature requested in [BTT issue #9](https://github.com/bigtreetech/PandaSensePro/issues/9) and retains the AP toggle correction for [issue #11](https://github.com/bigtreetech/PandaSensePro/issues/11).

### Tested on hardware

- OTA installation, static IPv4 and DHCP selection, saving, restarting, switching between modes, and cold-boot persistence.
- Web factory reset from manual mode and subsequent reconfiguration.
- Short Wi-Fi reconnection in static mode; local web interface and MQTT sensor updates resumed.
- Primary DNS, NTP and secondary DNS failover (including after a short reconnection). The release image also passes ESP image checksum and digest checks and ten simulated browser reset tests.

### Known limitations

- In a prolonged UniFi client-block/unblock test, automatic reconnection failed in both DHCP and manual IPv4 modes. A power cycle restored connectivity in manual mode. A sustained recovery AP was not observed. The underlying cause has not been isolated against unmodified BTT firmware; this is under separate investigation. Do not rely on the recovery AP being available during a prolonged outage.
- The physical-button reset does not clear the custom static IPv4 record; use the web factory reset when possible. Hardware recovery remains an option.
- The added network-setting WebSocket command uses the existing local interface without additional authentication. Use only on a trusted local network; do not expose the device to the Internet.
- Testing has been on one physical unit; fresh-device provisioning and wider network compatibility remain to be validated. The original-date variant has not yet been tested on hardware.

### Installation and rollback

Use the normal BTT firmware upgrade procedure for Panda Sense Pro. Keep a previous working firmware and the official hardware recovery procedure available. After upgrading, hard-refresh the browser, open Wi-Fi & IP, select the desired mode, save, confirm success, and restart. For manual mode choose an unused address appropriate to your own network and avoid DHCP conflicts. The prior AP fix v2 binaries below remain available for rollback.

**File size:** 1,531,472 bytes

**SHA-256 (UK date variant):** `29c56910bd848079f2956568b2949b0e42fb4e4d71ad6348c860c3fd5e1ca7c6`

**SHA-256 (original BTT date variant):** `3993e4bd24b60717678413251a567005cc49aad6c2f1e7c0af5f806d280f163b`

---

## Custom firmware 01.01.00.03 – Wi-Fi AP fix v2 (25 November 2026)

Two variants are available:

| Date format | Firmware | Testing |
| --- | --- | --- |
| UK: DD-MM-YYYY | [Download UK date version](Firmware/01.01.00.03/panda_sense_pro_01.01.00.03_UK-SI-WiFi-AP-toggle-test-v2.bin) | Tested on device |
| Original BTT: YYYY-MM-DD | [Download original date version](Firmware/01.01.00.03/panda_sense_pro_01.01.00.03_Original-date-SI-WiFi-AP-fix-v2.bin) | Binary integrity verified; device testing outstanding |

Both versions include the same Wi-Fi AP fix and all other corrections. The original-date variant restores only the vendor date format on the dashboard and screensaver.

This version is based on BIGTREETECH firmware 01.01.00.03 and retains the previous proofreading, CO₂, PM₂.₅/PM₁₀, SI unit, font and web interface corrections.

It additionally addresses [BTT issue #11 – AP mode not shutting off](https://github.com/bigtreetech/PandaSensePro/issues/11). Previously, disabling the built-in Wi-Fi access point could leave it broadcasting, and the AP toggle would return to On when revisiting the settings page.

The patch allows the AP disable request to reach the existing firmware control routines and requests an AP-mode recheck when the device obtains an IP address. With AP always-on disabled, the unit can operate on the local Wi-Fi network without its own access point continuing to broadcast.

**Testing (UK date variant):** Successfully installed over OTA and confirmed working on my Panda Sense Pro. Automatic AP recovery following prolonged Wi-Fi loss has not yet been verified. This is unofficial modified firmware; retain the previous working firmware and recovery tool in case recovery is required.

**SHA-256 (UK date variant):** `b54c4264aec8d0d9b9360f43f2919db7d63c6234a926431e5d0a213502e9527e`

**SHA-256 (original BTT date variant):** `c2f04a1a501415f2662bbb6578edfb34f6267608b73d5552ab18a407c8c75928`

The previous custom firmware remains available in the same firmware directory for rollback.

---

Edit: Firmware in 01.01.00.03 is based on latest BTT one, contains all fixes

A fork to add a firmware file with UK date format of dd-mm-yyyy for main screen and screensaver as the date format is not correct when UK timezone is applied. New version is in firmware.

And a second one which does much more:

panda_sense_pro_01.00.00.00_UK-date-proofreading-fonts-v8-final.bin fixes the UK date, fixes typographical errors and correct the units to be better represented although still not strictly SI:

DD-MM-YYYY HH:MM on the dashboard
DD-MM-YYYY on the screensaver
Monday and Wednesday proofreading fixes
µg/m³ for PM2.5 and PM10
mg/m³ for eTVOC and formaldehyde

And a final one: https://github.com/IainPHay/PandaSensePro/blob/master/Firmware/01.00.00.00/panda_sense_pro_01.00.00.00_UK-date-proofreading-fonts-CO2-SI-PMsub-andwebfixes.bin

Which patches the PM2.5 and PM10 to subscript, changes the µg/m³ to more formal -3 form and patches the debpage to correct notation as well
