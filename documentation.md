# Documentation

## Overview

**LR.light** is a system for wirelessly controlling addressable LEDs directly from **Ableton Live**, using a custom **Max for Live** device. It enables both *audioreactive* and *pre-programmed* lighting setups, tightly integrated into your musical workflow. By making light as mappable and immediate as any parameter in Live, it allows artists to seamlessly blend sound and light in real time.

Designed with **live performance**, **audiovisual installations**, and **theatrical contexts** in mind, LR.light empowers creative control over lighting without the need for external lighting software, DMX interfaces, or complex programming environments.

What sets LR.light apart is its:
- **Native Ableton integration**, using MIDI mapping and automation to control lights like instruments or effects.
- **Wireless architecture**, built around the WLED firmware and ESP32 modules, reducing setup time and cabling on stage or in installations.
- **Customizability and DIY-friendliness**, making it possible to build your own system at a fraction of the cost of commercial wireless lighting setups—though it does require some time and technical effort.

LR.light was developed out of a personal need: to realize an artistic vision in which light and sound form a cohesive whole—without spending days wiring and configuring each new setup. The system emerged as a streamlined, flexible solution that preserves aesthetic freedom while reducing the friction of technical preparation.

> **Note:** LR.light is not a plug-and-play system.  
> While its components are modular and well-documented, setting up and maintaining the system requires some familiarity with networking, microcontroller flashing, and basic LED hardware. A willingness to tinker is part of the experience.

---

## How It Works

**LR.light** is based on the [**WLED**](https://kno.wled.ge/) platform.

> **WLED** is “a fast and feature-rich implementation of an ESP32/ESP8266 web server to control NeoPixel (WS2812B, WS2811, SK6812) LEDs, as well as SPI-based chipsets like the WS2801 and APA102!”

It uses a custom **Max for Live** device to send RGBW data via **UDP** to LEDs connected to **ESP32** modules running the WLED firmware. This allows direct control of lighting from within Ableton Live using familiar workflows like MIDI mapping and automation lanes.

---

## System Components

LR.light consists of a small number of open and widely available hardware and software components.

### Hardware

- **ESP32 NodeMCU boards** are used as wireless LED controllers, each running the [WLED](https://kno.wled.ge/) firmware. Each LED fixture is paired with its own ESP32 module.
- **SK6812 RGBW LED strips** provide the light output. In the current setup, each fixture uses a 1 m strip at 144 LEDs/m.
- Power is supplied using either:
  - A **5V 3A DC power supply** (e.g. wall-plug AC/DC adapter), or
  - **4× AA NiMH rechargeable batteries**, for mobile or installation contexts.
- A **dedicated Wi-Fi router** is used to create a private local network for low-latency and stable communication between Ableton Live and the ESP32 modules. Public or venue networks are not recommended.

> **Note:** A detailed list of tested components and wiring instructions can be found in the [Build Guide](./build-guide.md). *(link placeholder)*

### Software

- The ESP32 modules run **WLED**, used in its standard form without modification.
- Initial configuration (Wi-Fi setup, LED settings, naming) is done via the **WLED web interface**. See the [Installation & Configuration Guide](./setup-guide.md) for details. *(link placeholder)*
- The **Max for Live device** is custom-built for LR.light and included in this repository.  
  It sends UDP packets to one or more WLED fixtures. Each instance of the device must be matched to a specific fixture.

> If a Max for Live device is active but no matching ESP32 receiver is online, Ableton Live may become unstable or crash. Always ensure correct mapping between device and fixture.

### Communication Protocol

- LR.light communicates via **UDP** for minimal latency.
- Each fixture receives a stream of **RGBW values (0–255)**, which is applied uniformly to all LEDs on the strip.
- Although current behavior sets the entire strip to a single color, it would be technically possible to implement **per-pixel addressing** with WLED. This is not yet implemented in the Max for Live device — but the architecture allows for future development or user contributions.

> In practice, the system has been tested with up to 8 simultaneous ESP32 receivers, each with a dedicated Max for Live device. Higher numbers may be possible, but stability is not guaranteed beyond this.

---

## Addressing & Fixture Mapping

Each LR.light fixture (ESP32 + LED strip) is assigned a unique **mDNS hostname** (e.g. `wled0.local`) within the local network.  
This address is used to route lighting data from the Max for Live device directly to the correct receiver via **UDP**, avoiding the need for static IPs or DHCP reservations.

### Addressing via `sadam.udpSender`

The Max for Live device uses an external object called [`sadam.udpSender`](http://www.sadam.hu/en/software) by Ádám Siska. Unlike Max/MSP’s native `udpsend`, this object allows **raw data transmission** (non-OSC) — required for sending RGBW streams directly to WLED.

In the Max patch, the UDP address of each fixture is **set using a hostname**, e.g. `wled0.local`, passed as an argument to the `sadam.udpSender` object.  
This hostname is defined in the **WLED web interface**, under **Wi-Fi Setup → mDNS address**.

It must match exactly between the Max for Live device and the WLED configuration.

> 📌 Hostnames should be short, lowercase, and contain no spaces.  
> Good examples: `wled0.local`, `fixture1.local`, `costumeL`. Bad examples: `My Fixture`, `wled.local!`

---

### Max for Live Fixture Mapping

In **LR.light v1.1**, each Max for Live device instance includes a **dropdown menu** in the UI for selecting which fixture to control. The rest of the routing logic happens under the hood.  
This approach replaces the older method (**v1.0**) where a separate device had to be manually configured per fixture.

> ⚠️ As of now, the new dropdown feature is untested. Future updates will confirm full functionality.

By default, the device includes entries for up to **8 fixtures**. To support more, users must:
1. Open the Max for Live device in edit mode.
2. Add new fixture addresses to the dropdown logic (instructions included as comments in the patch).

Each fixture should be **physically labeled** with its assigned hostname to avoid confusion, since the UDP address is hardcoded into the Max patch.

---

### Best Practices

- Make sure each Max for Live instance **only targets one fixture**, and that every fixture has a matching device in Live.
- If a Max for Live device is active but no corresponding ESP32 is online, **Ableton Live may crash** due to unresolved UDP routing.
- Clearly label each fixture with its assigned mDNS hostname (e.g. `wled0.local`) so you can easily match hardware to its corresponding Max device.

---

## Limitations & Design Choices

LR.light is a flexible and modular system, but it is not a commercial product — nor is it intended to be plug-and-play. It reflects a series of practical and artistic decisions, shaped by real-world use in live performance contexts.

### Current Limitations

- **Not plug-and-play**  
  LR.light requires users to configure mDNS hostnames, flash firmware, and manually map devices. Some technical literacy is required to set up, maintain, and extend the system.

- **No per-pixel control (yet)**  
  The current Max for Live device sends a single RGBW value per fixture. This value is applied uniformly to the entire LED strip.  
  Future updates (or user forks) could implement per-pixel or pattern-based control, as WLED supports this on the firmware level.

- **Manual setup for more than 8 fixtures**  
  Out of the box, the Max for Live device supports up to 8 fixtures. To add more, users must manually edit the device in Max and update the dropdown menu logic.

- **Limited tested scale**  
  The system has been tested with up to 8 simultaneous receivers. It may support more, but performance and stability at larger scales have not been tested.

- **Ableton Live may crash if misconfigured**  
  If a Max for Live device sends data to an unreachable address (i.e., no active ESP32 receiver), Ableton Live may become unstable or crash. Proper mapping is critical.

---

### Design Choices

- **UDP over OSC or other protocols**  
  Raw UDP was chosen for its low latency. Max’s native `udpsend` object was unsuitable, as it only supports OSC-formatted data. The external `sadam.udpSender` allows direct transmission of raw RGBW streams.

- **DIY over commercial solutions**  
  LR.light was built out of a need to streamline light control in performance settings without the cost and rigidity of commercial wireless lighting systems. It favors flexibility and openness over polished user experience.

- **Local-only networking**  
  The system assumes a local Wi-Fi router and communicates via mDNS hostnames rather than static IPs.  
  This avoids latency, interference, and dependency on venue networks, which are often unreliable or locked down.

- **Modular and hackable**  
  The entire system is open for modification. Users comfortable with Max, WLED, or ESP32 firmware can extend the functionality as needed. No proprietary components or closed-source binaries are used.

> LR.light is intentionally lean, minimal, and adaptable — but it rewards curiosity, experimentation, and a DIY mindset.

---

### Cost & Support Considerations

The cheapest out-of-the-box solution for wireless LED light tubes I’ve found is roughly **six times the price** of a single LR.light fixture. If you're looking for a **reliable, plug-and-play** system with **customer support**, and don’t want to spend time debugging or soldering, then investing in a commercial system might still be the better option for you.

LR.light is a **DIY system**. It’s offered *as is*, without a support hotline — and while I’ve done my best to make it usable and flexible, I most likely **won’t have the time to provide individual tech support** if something doesn’t work on your end.

That said, if you **enjoy building your own tools**, and have some technical confidence, LR.light opens up a lot of possibilities. Its true strength lies in **customization**:  
You can design your own fixture shapes, tweak the Max patch, or extend the code to add new behavior. One direction I’d love to explore myself is **sewing LEDs into costumes or stage outfits**, controlled live through LR.light.  
This kind of bespoke integration is only possible with systems that are both open and modular.

> If you're here to experiment, shape your own workflows, and make the tool your own — have fun. That's what LR.light is made for.

---

## Compatibility

### Ableton Live
- Developed and tested with **Ableton Live 12 Suite**
- Requires **Max for Live** (included in Suite, or available separately)
- May work with **Ableton Live 11 or 10** (with Max for Live), but has not been tested

### Operating Systems
- Developed and tested on **macOS**
- Should work on **Windows** (if using the correct version of Max and `sadam.udpSender`), but not supported or tested

### Max for Live / Max Version
- **LR.light v1.0**: programmed in **Max 8**
- **LR.light v1.1**: programmed in **Max 9** (bundled with Ableton Live 12.2+)
- Uses the external object [`sadam.udpSender`](http://www.sadam.hu/en/software) — must be installed manually

### ESP Modules
- Designed for and tested with **ESP32 NodeMCU** boards
- **ESP8266** does work with WLED but has not been tested in LR.light

### LED Types
- Designed for **SK6812 RGBW** strips
- In principle, other **WLED-supported chipsets** (e.g. WS2812B, WS2811, APA102) could be used, but only SK6812 RGBW has been tested

### Network Requirements
- Requires a **dedicated Wi-Fi router**
- Fixtures are addressed via **mDNS hostnames** (e.g. `wled0.local`)
- All communication happens over **local UDP**

> ⚠️ This system is not designed for use over public or venue-managed Wi-Fi networks.  
> For stable performance, always use your own router and isolate the lighting network from other traffic.

---

## Conceptual Notes & Use Cases

LR.light was created with one goal in mind: to make **lighting design and control as immediate and performative** as music production in Ableton Live.

It treats light not as a separate medium to be synchronized, but as an **integral parameter of live expression** — one that can be automated, mapped, or modulated in exactly the same space as sound. The result is a system where lighting can be part of a composition, an improvisation, or a responsive environment.

### Artistic Contexts

LR.light has been designed with a range of real-world creative settings in mind:

- **Live electronic performance**  
  Use light as a visual voice within your set — reactive to envelopes, LFOs, automation lanes, MIDI notes, or expressive controls.

- **Audiovisual installations**  
  Build modular setups where each lighting fixture becomes a spatial node, tuned by data or driven by sound.

- **Theatre and dance**  
  Integrate responsive light into choreographic systems, giving performers dynamic control over wearable or embedded LEDs.

### A System Meant for Expansion

Because LR.light is **open-ended and modular**, its most interesting use cases may not have been imagined yet.  
Some directions the system naturally supports:

- Custom fixture layouts (e.g. pixel matrices, sculptural forms, wearable pieces)
- Time-based compositions that integrate light like any other musical timeline
- Generative or procedural light patterns created in Max and pushed live
- Wireless setups that can travel — minimal cabling, maximum spatial freedom

One idea I plan to explore is **sewing addressable LEDs into stage costumes**, turning the performer into a live-mapped light source, fully integrated into the musical and visual score. LR.light makes this kind of experimentation possible — not because it prescribes it, but because it *allows* it.

> This system is not just about controlling lights. It's about rethinking what a lighting instrument can be.
