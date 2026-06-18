# WebCarPlay

A pure browser-based Apple CarPlay client. No Node.js, no build tools, no dependencies, just a single HTML file you open in Chrome. It uses [WebUSB](https://developer.mozilla.org/en-US/docs/Web/API/WebUSB_API) to talk to a USB CarPlay dongle and [WebCodecs](https://developer.mozilla.org/en-US/docs/Web/API/WebCodecs_API) to decode the H.264 video stream.

<p align="center">
  <a href="https://ded811.github.io/WebCarPlay/carplay.html" target="_blank" rel="noopener">
    <img src="https://img.shields.io/badge/▶%20Open%20WebCarPlay%20Demo-007AFF?style=for-the-badge&logoColor=white" alt="Open WebCarPlay Demo" height="52">
  </a>
  <br>
  <sub>Requires Chrome and a USB CarPlay dongle</sub>
</p>

---

## What adapter do you need?

You need a **USB CarPlay relay dongle**, the kind that sits between your iPhone and your PC. These are sometimes called "CarPlay box" or "AutoKit" dongles. Common examples:

- Carlinkit CPC200-CCPM
- Carlinkit CPC200-CCPA
- Most other Carlinkit / AutoKit USB dongles

These dongles support two connection modes, and this client handles both:

| Mode | Phone connection | Dongle to PC |
| --- | --- | --- |
| **Wired** | Phone USB cable to dongle | USB |
| **Wireless** | Phone WiFi to dongle's hotspot | USB |

> **Not compatible with** wireless-to-car adapters (the kind that adds wireless CarPlay to a factory wired-only head unit). Those use a different protocol entirely.

---

## Requirements

- **Chrome 94+** (WebUSB + WebCodecs). Other Chromium-based browsers may work.
- **Linux / macOS / Windows**: just open `carplay.html` directly in Chrome, no server needed.
- **Windows only**: requires a driver, see the [Windows section](#windows) below.

---

## Getting started

1. Open `carplay.html` directly in Chrome. Just double-click the file or drag it into Chrome.

2. Plug in your CarPlay dongle.

3. Click **Connect CarPlay**. Chrome will show a device picker, select the dongle. After the first time, the dongle is remembered and will auto-connect on subsequent visits.

4. Connect your iPhone:
   - **Wired**: plug your iPhone into the dongle via USB.
   - **Wireless**: the dongle broadcasts a WiFi hotspot; your iPhone will find it automatically if it has previously paired with the dongle.

---

## Configuration

Open `carplay.html` in a text editor. Near the top of the `<script>` block you will find the `CONFIG` object. Edit it to suit your setup, no other code needs to change.

```javascript
const CONFIG = {
  // false = auto-fit to the browser window (recommended)
  // true  = use the fixed width/height values below
  customResolution: false,
  width:  1280,
  height:  720,

  // Only matters when customResolution is true.
  // false = letterbox (black bars)
  // true  = stretch to fill the window
  stretchToFill: false,

  // Show the bottom menu bar when the page loads.
  // Set to false to hide it permanently (see "Menu bar" section below).
  showMenuBar: true,

  // Frames per second sent to the dongle.
  // 30 is recommended, smooth without wasting CPU. Most dongles won't go above 30.
  // Drop to 20 on slower hardware, go up to 60 only if your dongle supports it.
  fps: 30,

  // CarPlay dark/night mode (affects iPhone UI chrome, not the video itself).
  nightMode: false,

  // true  = left-hand drive (steering wheel on left, US/EU/etc.)
  // false = right-hand drive (steering wheel on right, UK/AU/JP/etc.)
  leftHandDrive: true,

  // Wi-Fi band for wireless CarPlay pairing.
  // '5ghz' is faster and less congested; use '2.4ghz' if your phone won't pair.
  wifiType: '5ghz',

  // Set to false to mute all audio output (useful for debugging).
  audioEnabled: true,

  // Seconds to wait for a phone before the dongle broadcasts a pairing request.
  pairTimeoutSecs: 15,

  // Bottom bar button size: 'small', 'medium', or 'large'
  buttonSize: 'medium',
};
```

---

## Menu bar

The bottom bar has Home, Siri, and media controls.

**To hide it:** click the **∨** (chevron-down) button on the right side of the bar. A small pull-tab appears at the very bottom of the screen.

**To restore it:** click that pull-tab, or double-click anywhere on the CarPlay video.

**To hide it permanently** (e.g. for a dedicated car display): set `showMenuBar: false` in CONFIG. The bar and pull-tab will never appear. CarPlay's own touch UI handles everything.

---

## Windows

Chrome's WebUSB on Windows requires the **WinUSB** driver to be installed for your dongle. Use [Zadig](https://zadig.akeo.ie/):

1. Plug in the dongle and open Zadig
2. **Options > List All Devices**
3. Find your dongle in the list (look for Auto Box, Carlinkit, or an unknown USB device)
4. Select **WinUSB** in the driver dropdown, not libusb-win32 or LibusbK
5. Click **Install Driver**

After that, Chrome's device picker should show the dongle.

**Known issue:** some dongles boot-cycle rapidly while waiting for a CarPlay host, and the WinUSB driver stack can't finish loading before the device resets. This shows up as a persistent "USB device not recognised" error even after Zadig succeeds. There is no known fix for this, if you hit it, use Linux or macOS instead.

---

## Using other dongles

The device picker shows all connected USB devices, so any Carlinkit/AutoKit-compatible dongle should appear. Look for something named **Auto Box**, **AutoKit**, **Carlinkit**, or similar. Pick it once and WebCarPlay remembers it for future sessions. The underlying protocol is shared across the whole family, so it should work as long as your dongle uses the same protocol.

---

## Credits

Protocol reverse-engineered from [rhysmorgan134/node-CarPlay](https://github.com/rhysmorgan134/node-CarPlay) (TypeScript, MIT licence).
