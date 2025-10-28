# Setup Guide

This guide walks you through setting up the LR.light system after flashing your ESP32 modules with [WLED firmware](https://kno.wled.ge/).  
It assumes you already have the following:

- One or more **ESP32 NodeMCU** modules
- **WLED firmware** installed on each module
- A **working Wi-Fi router** (preferably dedicated for the LR.light system)
- **Ableton Live 12 Suite** or later, with **Max for Live** installed
- The **LR.light Max for Live device** (included in this repository)

---

## 1. Flashing WLED Firmware

Follow the official WLED documentation to flash the firmware onto your ESP32 modules:

👉 [How to Install WLED](https://kno.wled.ge/basics/install-binary/)

You can use the [WLED Web Installer](https://install.wled.me/) for most boards.  
Once your device is flashed and online, you can access its configuration via a browser on the local network.

---

## 2. LR.light-specific Configuration

Once WLED is installed and the ESP32 modules are reachable on your network, continue with the following steps to prepare them for LR.light.

Instead of assigning static IPs, each fixture should be configured with a **custom mDNS address** (e.g. `wled0.local`).  
This address is then used inside the Max for Live device to route the UDP data to the correct ESP32 module.

We’ll cover:

- LED setup (type, length, color order)
- mDNS hostname setup (for each fixture)
- Naming and physically labeling fixtures
- Notes on reliable power and wiring

📸 *[Insert screenshot of the WLED “LED Preferences” and “Wi-Fi Setup” pages here]*

Coming up next:
- [ ] Step-by-step configuration of LED strip parameters  
- [ ] Screenshot showing correct settings for LR.light  
- [ ] mDNS hostname setup  
- [ ] Tips on matching Max devices with physical fixtures

## 3. Configuring WLED for LR.light

Once your ESP32 module is running WLED and accessible via browser, open the **WLED Web UI**.  
The following configuration steps are specific to the components described in the [Build Guide](./build-guide.md). *(link placeholder)*

---

### Step 1: LED Preferences

Go to:  
**Config** → **LED Preferences**

Under **Hardware setup – LED outputs**, set:

- **LED Type:** `SK6812 / WS2814 RGBW`
- **Color Order:** `GRB`, **Swap:** `None`
- **Start:** `0`, **Length:** `144`
- **Skip first LEDs:** `0`
- **Auto-calculate white channel from RGB:** `None` *(leave disabled)*

Leave all other options as they are.

📸 *[Insert screenshot of LED output settings]*

---

### Step 2: Defaults

Scroll down to the **Defaults** section and change:

- **Apply preset** → `1`  
  *(We’ll configure this preset in a later step.)*

---

### Step 3: Transitions

In the **Transitions** section:

- Set **Transition time** → `0 ms`

> WLED’s default settings use transition fades between color changes, which introduces latency.  
> For LR.light, we want lights to respond **instantly** to each RGBW frame. Set this to zero for real-time control.

📸 *[Insert screenshot of transition settings]*

---

### Step 4: Wi-Fi Setup

Return to the **Config** menu and go to:  
**Wi-Fi Setup**

Set the following:

- **Network name:** *(Enter the name of your dedicated LR.light Wi-Fi router)*
- **Network password:** *(Enter your Wi-Fi password)*
- **mDNS address:**  
  This is how the fixture will be identified in the LR.light system.  
  Each fixture **must have a unique name** that matches the address defined in your Max for Live device using `sadam.udpSender`.

  Example hostnames:
  - `wled0.local`
  - `wled1.local`
  - `costume1.local`

📸 *[Insert screenshot of Wi-Fi Setup screen]*

---

### Step 5: Set Preset 1 (Blackout Default)

Return to the **main WLED control UI** (click “BACK” in the menu).  
Here you’ll see the color picker and brightness controls.

- Use the **color dials** and **sliders** on the left side of the UI to set the fixture to **black / off**
- Then, on the **right side**, save this state as **Preset 1**

> Why this matters:  
> If a fixture stops receiving RGBW data (e.g. due to dropped connection or software crash), WLED falls back to its **default preset**.  
> The WLED default is an **orange glow** — which we don’t want in a performance context.  
> By setting Preset 1 to **black/off**, the fixture will turn off silently rather than showing an unintended color.

📸 *[Insert screenshot of Preset 1 being saved]*

---

Once you’ve completed these steps, the WLED fixture is ready to be used with LR.light.

👉 Next: Open your Ableton Live set and load the **LR.light Max for Live device**.  
Make sure the selected fixture (via dropdown) matches the mDNS address you assigned (e.g. `wled0.local`).

> For detailed instructions on how to use, configure, and customize the Max for Live device,  
> please refer to the [Max for Live Device Guide](./m4l-device-guide.md). *(link placeholder)*
