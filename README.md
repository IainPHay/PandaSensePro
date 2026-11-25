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
