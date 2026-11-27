## Custom firmware 01.01.00.03 — v4.7 (27 November 2026)

Unofficial community firmware based on BIGTREETECH Panda Sense Pro firmware 01.01.00.03, with custom firmware by Demig. This consolidates v4.6a.2 through v4.6a.5b. It is not an official BTT release.

| Date format | Download | Validation |
| --- | --- | --- |
| UK: DD-MM-YYYY | [v4.7 UK](Firmware/01.01.00.03/panda_sense_pro_01.01.00.03_UK-SI-APv2-v4.7.bin) | UK v4.6a.5b hardware-tested; v4.7 version-only promotion statically verified; extended stability review pending |
| Original BTT: YYYY-MM-DD | [v4.7 original date](Firmware/01.01.00.03/panda_sense_pro_01.01.00.03_ORIGINAL-DATE-SI-APv2-v4.7.bin) | Binary integrity verified; hardware testing outstanding |

Both variants contain the same functional changes, apart from the date format and variant label. The original-date build retains the vendor date order on the dashboard and screensaver. The UK build uses DD-MM-YYYY. Each file is 1,532,112 bytes.

### New in v4.7 compared with v4.6a.2

**Home Assistant / MQTT**

- Correct native MQTT discovery device classes for PM10 and AQI; PM10 uses `pm10` and AQI uses `aqi`. The eight existing environmental sensor identities, state topic and payload are preserved.
- All eight environmental sensors have a 60-second `expire_after` value, so Home Assistant marks missing data unavailable rather than showing stale values indefinitely. Unchanged readings remain available while packets continue arriving.
- Two automatically discovered diagnostic sensors: Uptime (seconds, duration class) and Wi-Fi signal strength (dBm, signal_strength class, measurement state class). They belong to the existing Panda device, not a second device.
- Diagnostics use a separate state topic and are normally published every 30 seconds. Both expire after 60 seconds. Uptime uses the monotonic device timer, independent of NTP, survives Wi-Fi/MQTT reconnects and resets on a device reboot. RSSI is the signal measured by the Panda from its associated access point; it may differ from the access point's measurement of the Panda. Invalid or unavailable RSSI is represented as JSON null, not a fabricated 0 dBm.
- Discovery uses retained configuration; environmental and diagnostic states are non-retained QoS 0. No new diagnostic tasks or persistent configuration writes. RSSI is obtained using the matched ESP-IDF 5.3.3 driver data and locking routines; the vendor build does not appear to include the public RSSI getter as a callable function.
- Direct binding to an external MQTT broker is an existing BTT feature, not a new custom implementation. This release documents that feature and improves its Home Assistant discovery and diagnostics.

**Web interface and localisation**

- Settings shows both the original BTT firmware version and the custom release, e.g. `01.01.00.03 / v4.7 UK` or `01.01.00.03 / v4.7 Original`. The vendor version metadata used internally is unchanged.
- Retains BIGTREETECH attribution and adds “Custom firmware by Demig” on the language/setup and app pages.
- Added Simplified Chinese translations for custom IPv4 controls, labels, validation errors, dynamic save/reset status and confirmation messages. Changing language updates these messages without discarding unsaved form entries. Version/credit text remains in English.
- On initial **web** setup, selecting English defaults the timezone to London (including GMT/BST); Chinese retains its previous default. Later language changes do not deliberately overwrite a manually selected timezone. Fresh-provisioning hardware validation is pending.

### Retained features from earlier releases

- UK dashboard/screensaver dates (UK variant only), Monday/Wednesday proofreading, CO₂ and PM₂.₅/PM₁₀ typography, SI unit notation, font and web corrections.
- AP toggle correction ([BTT #11](https://github.com/bigtreetech/PandaSensePro/issues/11)).
- Automatic DHCP or configurable manual IPv4, subnet mask, gateway, primary DNS and optional secondary DNS ([BTT #9](https://github.com/bigtreetech/PandaSensePro/issues/9)). Validated non-volatile configuration; restart separately after saving.
- Web factory reset saves and verifies DHCP defaults before invoking the original reset. It aborts if the save is uncertain. This does not apply to the physical reset button or direct reset command.
- No personal network address or broker credentials are preconfigured.

### Connecting directly to Home Assistant

1. Set up an MQTT broker (such as Mosquitto) and configure Home Assistant's MQTT integration.
2. Open the Panda's web interface, go to its HA page and select **Bind a Broker**. Enter your broker's IPv4 address, port (typically 1883), MQTT username and password; use your own broker values.
3. Save/bind and allow the Panda to connect. Confirm that the Panda device and ten entities appear in Home Assistant. The eight environmental readings are temperature, humidity, CO₂, PM₂.₅, PM₁₀, formaldehyde, eTVOC and vendor AQI; the other two are uptime and signal strength.
4. If migrating from a bridge, first back up configuration, verify the direct client and new entities, and retire only the redundant Panda bridge. Preserve existing entity IDs/history or repoint legacy YAML sensors to the native topic if needed. Check dashboards and automations. Removing historical entities or purging Recorder data is not required. Legacy and native entities may record duplicate data if both remain enabled.

Native MQTT topics (replace `<device_id>` with the identifier advertised by the device):

| Topic | Purpose |
| --- | --- |
| `panda_sense_pro/<device_id>/state` | Environmental JSON (normally ~5 seconds) |
| `panda_sense_pro/<device_id>/diagnostic` | Uptime and RSSI JSON (~30 seconds) |
| `homeassistant/sensor/<device_id>_<field>/config` | Home Assistant discovery |

Environmental example (illustrative):
```json
{"data":{"temper":20.1,"humidity":48.2,"pm2_5":3,"pm10":4,"ch2o":0.012,"etvoc":0.100,"co2":700,"aqi":5}}
```

Diagnostic example (illustrative):
```json
{"uptime":5070,"rssi":-61}
```

The diagnostic discovery field suffixes are `uptime` and `rssi`. The environmental field names remain those used by BTT, including `temper`, `etvoc` and `pm2_5`. MQTT entity IDs are assigned by Home Assistant and may vary. The vendor AQI is not UK DAQI.

### Testing and limitations

- UK v4.6a.5b was installed successfully, with all ten native MQTT entities reporting, correct diagnostic units/classes, changing RSSI values and no unexpected reboot during the initial stability period. A prior OTA attempt stalled at 6%; retry succeeded. The cause of that transient stall is unconfirmed.
- Uptime-only v4.6a.5a passed deliberate cold boot, short Wi-Fi interruption and sustained client-block/unblock tests; uptime reset only on the cold boot.
- UK v4.6a.5b passed an 8.8-second forced Wi-Fi reconnection and approximately 100-second sustained client interruption. All ten entities expired correctly and recovered automatically. Uptime advanced by 116 seconds across a 116.1-second reporting gap; both diagnostics expired at approximately 60 seconds. No new MQTT errors were detected. The access point was unchanged in these tests, so RSSI response to roaming is not yet verified.
- Both v4.7 images pass ESP image segment, checksum and appended SHA-256 checks. Relative to the matching v4.6a.5b image, only the compressed web version label, compressed-resource length and integrity fields change; executable and diagnostic code are identical. v4.7 itself has not yet been flashed. Extended stability testing, physical Chinese localisation and fresh English provisioning tests, and original-date hardware testing remain outstanding.
- Prolonged Wi-Fi recovery is still under investigation: earlier much longer client-block tests stranded the device in DHCP and manual modes. Passing a short outage does not establish recovery after prolonged RF loss. No new reconnection logic or fallback-AP implementation is included in v4.7.
- Current availability uses expiry, not a native MQTT last-will implementation. There is no new MQTT password masking or transport-security mechanism. Use on a trusted local network; do not expose the interface to the Internet. The custom IPv4 command uses the existing web interface without additional authentication.
- The physical-button reset does not clear the custom IPv4 record. Do not rely on the fallback AP during extended loss. Retain a known-good image and the official BTT hardware recovery procedure.

### Installation, rollback and checksums

Use the official [Panda Sense Pro firmware upgrade instructions](https://neo.bttwiki.com/en/docs/panda-series/module/panda-sense-pro/). Select the appropriate date variant. Allow the upload and reboot to finish; hard-refresh the browser afterwards and check the version. Do not interrupt an update that is still progressing. Keep the previous working firmware available. The v4.6a.2 UK baseline below remains unchanged as the known-good rollback option.

SHA-256:

- UK: `efe47a7a5468d8a7c04535070a98a8c586b7ecfed7254f9f3d5fabdc4de56747`
- Original date: `0675f81682b2aafc65c89cae9b20a0a004169c993619f1ceff5487f49d1006ec`

Next development: prolonged Wi-Fi recovery (v4.8), followed by optional fallback AP and provisioning improvements. Sensor calibration, additional languages and expanded diagnostics are separate future work.


---

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
