## Materials per Fixture

| Item | Description |
|------|-------------|
| **1× Mini NodeMCU (ESP8266MOD 12-F)** | Microcontroller running WLED |
| **1× SK6812 RGBW LED strip** | 1 m, 144 LEDs/m, 5 V, individually addressable |
| **1× Light housing** | REV LED Lichtleiste XS 20 W (120 cm), from OBI |
| **1× Power supply** | 5 V 3 A AC/DC or battery pack |
| **1× Battery holder** | 4× NiMH AA batteries, wired in parallel |
| Electrical wire | For power and data connections |
| WAGO clamps or 2-pin socket | For removable power connection |
| Electrical tape | For insulation and wire protection |

---

## Tools Required

| Tool | Use |
|------|-----|
| **Soldering iron + solder** | Connecting microcontroller, LED strip, and power |
| **Wire cutters** | Preparing cables and shortening the fixture |
| **Hack-saw** | Cutting the diffuser layer cleanly |
| **Drill + drill bit** | Drilling a hole in the end cap for the power cable |
| **Glue (e.g. hot glue)** | Mounting the LED strip in place |

---

## Section 1 – Preparing the Housing

![Image of Lichtleiste](./images/LR.light_fixture_housing.webp)
[Source: OBI](https://www.obi.de/p/5191838/led-komplett-lichtleiste-1-x-20-w-laenge-123-cm)


### Step 1: Open the REV LED Lichtleiste

> ⚠️ Use the **exact model from OBI**. Other fixtures look similar but are glued shut. This is the only one I've found that can be opened without breaking it.

To open:
- Press in the **pin** on the underside of the end caps
- Pull off the caps

![Image of end cap mechanism](./images/LR.light_endcap_pin.HEIC)

### Step 2: Remove the Diffusion Layer

The white plastic diffuser is hooked into the aluminum housing.  
Gently lift and slide it out.

### Step 3: Remove the Pre-installed LED Strip

Slide out the internal LED strip — you won’t need it for LR.light, but see the tip at the end for how to reuse it.

### Step 4: Shorten the Housing

The fixture is **120 cm** long, but your LED strip is **1 m**.  
Shorten the housing to **110 cm** to create a clean fit.

- Use **wire cutters** for the aluminum body
- Use a **hack saw** for the diffuser


### Step 5: Cut a Notch for the Wires

At one end of the housing, cut a **notch** where the LED wires can fold down to the underside (where the NodeMCU will be mounted).  
Deburr or tape off the metal to avoid cutting the wires.

### Step 6: Drill Hole in End Cap

Drill a hole in the **end cap closest to the NodeMCU**.  
This is where the **power cable** will exit the fixture.

---

## Section 2 – Preparing the NodeMCU

### Step 1: Flash and Test

Flash your **Mini NodeMCU (ESP8266MOD 12-F)** with the [WLED firmware](https://kno.wled.ge/) and test that it runs correctly before installation.

### Step 2: Solder the Connections

Follow the [wiring diagram](./images/LR.light_wiring_diagram.png). You’ll need to:

- Connect **D4 (GPIO2)** → **DIN on LED strip**
- Connect **GND** → **GND on LED strip and power**
- Connect **5V (from power)** → **5V to both strip and NodeMCU**

Wire length: around **20 cm** is ideal to reach from the strip to the underside of the housing.


### Step 3: Insulate the NodeMCU

Wrap the microcontroller in **electrical tape** to protect against short circuits and stabilize it inside the housing.

---

## Section 3 – Assembling the Fixture

### Step 1: Mount the LED Strip

Glue or tape the LED strip to the **top inside surface** of the aluminum housing (where the original strip was).

### Step 2: Route the Wires

Carefully fold the wires through the **notch** to the back.  
Make sure no sharp metal edges can cut or damage the insulation.

### Step 3: Mount the NodeMCU

Place the NodeMCU on the underside of the fixture — if you’re using the same model as I do, it should fit **snugly into the cavity** on the housing's back side.

### Step 4: Replace the Diffusion Cover

Slide the diffuser back onto the housing — it should hook in securely.

### Step 5: Close the End Caps

Push the end caps back on.  
They will be held by friction — no glue required — which is practical in case you need to access the internals again later.

![Image of finished fixture](./images/LR.light_fixture_back.heic)

---

## ✅ Build Complete

Your LR.light fixture is now assembled and ready to configure.

> 💡 **Tip:** You may want to configure and test your LED strip and NodeMCU setup **before assembling** the full housing.  
> See the [Setup Guide](./setup-guide.md) for step-by-step instructions.

---

## Bonus Tip: Reusing the Pre-installed LED Strip

The strip that comes with the REV Lichtleiste is quite usable — I often install it in hidden locations for **backlighting**.

- The color temperature is **6000 K** (cold white)
- If you want a **warmer tone**, just tape over the strip with **white gaffer tape**
- This warms the color considerably — cheap and very effective
- ⚠️ *Not the intended use — try at your own risk. Heat hasn't been an issue for me.*

---
