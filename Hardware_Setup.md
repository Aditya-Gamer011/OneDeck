# Hardware Setup Guide

Complete wiring diagram and setup instructions for the OneDeck console.

---

## 📦 Parts List

### Essential Components
| Part | Quantity | Notes |
|------|----------|-------|
| Arduino Mega 2560 | 1 | Main microcontroller |
| MCUFRIEND 320×240 TFT Shield | 1 | ILI9341 display controller |
| 2-Axis Analog Joystick Module | 1 | With center push button |
| Push Button (6mm) | 3 | For ENTER, BACK, Joy Click |
| Piezo Buzzer | 1 | Optional, for sound effects |
| USB Cable | 1 | Type B for programming |
| Jumper Wires | ~20 | For breadboard connections |
| Breadboard | 1 | Optional, for prototyping |

### Optional Components
| Part | Purpose |
|------|---------|
| 5V Power Supply | External power (2A+ recommended) |
| 470Ω Resistor | Button debouncing (optional) |
| 10µF Capacitor | Power smoothing (optional) |
| Project Box | Enclosure for portable build |
| 3D Printed Case | Custom housing |

---

## 🔌 Pin Configuration

### Digital Pins Used
```
Pin 22 → BUTTON_ENTER    (Push button - Select/Action)
Pin 23 → BUTTON_BACK     (Push button - Back/Menu)
Pin 24 → JOYSTICK_CLICK  (Joystick center push)
Pin 25 → BUZZER_PIN      (Piezo buzzer - PWM)
```

### Analog Pins Used
```
Pin A8  → JOYSTICK_X     (X-axis, 0-1023)
Pin A9  → JOYSTICK_Y     (Y-axis, 0-1023)
```

### TFT Shield
```
Uses SPI interface (pins 50, 51, 52, 53)
Automatically detected by MCUFRIEND library
No additional wiring needed - shield plugs directly on top
```

---

## 🔗 Wiring Diagram

### Block Diagram
```
┌─────────────────────────────────────────────────────────┐
│                    Arduino Mega 2560                     │
│  ┌─────────────────────────────────────────────────┐    │
│  │                                                 │    │
│  │  GND  ····················· GND                 │    │
│  │  5V   ····················· 5V                  │    │
│  │  A8   ← JOYSTICK_X (0-1023)                    │    │
│  │  A9   ← JOYSTICK_Y (0-1023)                    │    │
│  │  22   ← BUTTON_ENTER (Active LOW)              │    │
│  │  23   ← BUTTON_BACK (Active LOW)               │    │
│  │  24   ← JOYSTICK_CLICK (Active LOW)            │    │
│  │  25   → BUZZER_PIN (PWM Output)                │    │
│  │                                                 │    │
│  │  50, 51, 52, 53 → TFT SHIELD (SPI)             │    │
│  │  GND, 5V        → TFT SHIELD (Power)           │    │
│  │                                                 │    │
│  └─────────────────────────────────────────────────┘    │
│         ▲           ▲            ▲         ▲             │
└─────────┼───────────┼────────────┼─────────┼─────────────┘
          │           │            │         │
    ┌─────┴─┐  ┌──────┴──┐   ┌─────┴──┐  ┌──┴───┐
    │ TFT   │  │ Joystick│   │ Buttons│  │Buzzer│
    │Shield │  │ Module  │   │ (3×)   │  │      │
    └───────┘  └─────────┘   └────────┘  └──────┘
```

### Detailed Connection Schematic

```
POWER CONNECTIONS:
══════════════════
    Arduino GND ──┬──→ GND (TFT Shield)
                  ├──→ GND (All Components)
                  └──→ Joystick GND
    
    Arduino 5V  ──┬──→ 5V (TFT Shield)
                  ├──→ 5V (Joystick)
                  └──→ 5V (Push Buttons - via resistors)


JOYSTICK CONNECTIONS:
═════════════════════
    Joystick GND    ──→ Arduino GND
    Joystick VCC    ──→ Arduino 5V
    Joystick X      ──→ Arduino A8
    Joystick Y      ──→ Arduino A9
    Joystick SW     ──→ Arduino 24 (via 10kΩ pullup to 5V)


PUSH BUTTON CONNECTIONS:
════════════════════════
    All buttons use internal pullups (INPUT_PULLUP mode)
    
    Button 1 (ENTER):
        One leg → Arduino Pin 22
        Other leg → GND
    
    Button 2 (BACK):
        One leg → Arduino Pin 23
        Other leg → GND
    
    Button 3 (Joy Click - if external):
        One leg → Arduino Pin 24
        Other leg → GND


PIEZO BUZZER CONNECTION:
════════════════════════
    Positive (+) ──→ Arduino Pin 25 (PWM)
    Negative (-) ──→ Arduino GND
    
    Optional: Add 100µF capacitor across buzzer for smoothing


TFT SHIELD CONNECTION:
══════════════════════
    Stack directly on top of Arduino Mega 2560
    No additional wiring required
    
    Shield auto-detects display via MCUFRIEND library
    Pins 50, 51, 52, 53 (SPI) handled internally
```

---

## 📐 Component Connections Guide

### 1. Joystick Module Setup

```
Joystick Pin Layout (looking at module):
┌─────────────────┐
│ VCC  GND  SW    │  (Header row)
│ X    Y          │  (Axis row)
└─────────────────┘

Connections:
VCC (Red wire)      → Arduino 5V
GND (Black wire)    → Arduino GND
X axis (Green)      → Arduino A8
Y axis (Yellow)     → Arduino A9
SW (Blue)           → Arduino 24 (with 10kΩ pullup resistor to 5V)
```

**Joystick Value Ranges:**
- X-axis: 0 (left) → 512 (center) → 1023 (right)
- Y-axis: 0 (up) → 512 (center) → 1023 (down)
- SW: Pressed = LOW (0), Released = HIGH (1)

---

### 2. Push Button Setup

```
Standard Push Button (6mm):
┌────────┐
│  Button│
├────┬───┤
│ 1  │ 2│
└────┴───┘

Pin 1 → Arduino Digital Pin (22, 23, or 24)
Pin 2 → Arduino GND

Connection method:
Arduino Pin ──┐
              │
           ╱  (Button)
              │
            GND

Code uses INPUT_PULLUP, so internal pullup handles 5V automatically.
```

**Button Debouncing:**
- Code includes 20ms hardware debounce
- No external debouncing capacitors needed

---

### 3. Piezo Buzzer Setup

```
Piezo Buzzer (5mm):
┌─────────────┐
│  + (longer) │  → Arduino Pin 25 (PWM)
│  - (shorter)│  → Arduino GND
└─────────────┘

Optional Protection:
    Arduino Pin 25 ─────┬─────── Piezo (+)
                        │
                    100µF cap
                        │
                     Arduino GND ─ Piezo (-)

Frequency: Tone() function generates various pitches
Duty cycle: PWM modulation for volume control
```

**Audio Output:**
- SOUND_BEEP: 1000 Hz, 50ms
- SOUND_SCORE: 800 Hz + 1200 Hz
- SOUND_LOSS: 300 Hz × 3
- SOUND_COLLISION: 600 Hz, 75ms
- SOUND_LEVELUP: 1000/1200/1400 Hz cascade
- SOUND_SELECT: 900 Hz, 80ms

---

### 4. TFT Shield Installation

```
Step 1: Align Shield with Arduino
        ┌──────────────────────────┐
        │   MCUFRIEND TFT Shield   │
        │ ┌──────────────────────┐ │
        │ │   320x240 Display    │ │
        │ └──────────────────────┘ │
        │                          │
        │ ████ (Gold connectors)   │
        └──────────────────────────┘
            ║ ║ ║ ║ ║ ║ ║ ║
            ▼ ▼ ▼ ▼ ▼ ▼ ▼ ▼
        ┌──────────────────────────┐
        │   Arduino Mega 2560      │
        │ (Stacked headers)        │
        └──────────────────────────┘

Step 2: Push down firmly until fully seated
Step 3: Verify all pins are aligned
Step 4: Power on - display should initialize automatically
```

**Display Orientation:**
- Rotation: 3 (landscape mode, USB left)
- Resolution: 320×240 pixels
- Color depth: 16-bit RGB565

---

## 🔧 Assembly Steps

### Phase 1: Component Preparation
1. ✅ Test Arduino Mega with basic LED blink sketch
2. ✅ Test TFT Shield with MCUFRIEND example
3. ✅ Test Joystick analog readings
4. ✅ Test Push buttons separately

### Phase 2: Breadboard Prototyping
1. Place Arduino Mega on main bench (not on breadboard)
2. Stack TFT Shield on top
3. Connect Joystick to breadboard:
   - VCC → 5V rail
   - GND → GND rail
   - X → A8 (via wire)
   - Y → A9 (via wire)
   - SW → 24 (via 10kΩ pullup resistor)
4. Connect buttons to GND rail with individual wires

### Phase 3: Programming & Testing
1. Upload OneDeck.ino to Arduino Mega
2. Open Serial Monitor (115200 baud)
3. Test each input:
   - Move joystick - observe A8/A9 values
   - Press buttons - observe pin states
   - Move around menu with joystick
   - Select game with ENTER button

### Phase 4: Final Assembly
1. Mount all components in project box
2. Secure wiring with hot glue or cable clips
3. Label all connections
4. Double-check power connections
5. Test all games before final use

---

## ⚙️ Configuration & Calibration

### Joystick Calibration

The code includes automatic joystick calibration:

```cpp
// Default calibration (center at 512)
input.joyXCalibMin = 256;    // Left extreme
input.joyXCalibMax = 768;    // Right extreme
input.joyYCalibMin = 256;    // Up extreme
input.joyYCalibMax = 768;    // Down extreme

// Deadzone (prevent drift)
if (abs(input.joyX) < 25) input.joyX = 0;
if (abs(input.joyY) < 25) input.joyY = 0;
```

**To recalibrate:**
1. Open Serial Monitor
2. Add debug prints to `updateInput()` function
3. Move joystick fully in all directions
4. Note min/max values
5. Update calibration constants

### Button Debounce Tuning

```cpp
// Currently: 20ms debounce threshold
if (input.btnEnterDebounce < 20) {
    input.btnEnterDebounce++;
    if (input.btnEnterDebounce == 20 && !input.btnEnter) {
        input.btnEnterPressed = true;  // Button registered
    }
}

// To increase debounce, change 20 to 30 (30ms)
// To decrease debounce, change 20 to 15 (15ms)
```

### Display Rotation

```cpp
// In setup():
tft.setRotation(3);  // 0=portrait, 1=landscape-right, 2=upside-down, 3=landscape-left

// Change based on your physical orientation
// Current: 3 = USB port on left, display in landscape
```

---

## 🔋 Power Specifications

### Power Requirements
```
Component          | Current Draw | Voltage
─────────────────────────────────────────
Arduino Mega       | ~50 mA       | 5V
TFT Display        | ~100 mA      | 5V
Joystick Module    | ~5 mA        | 5V
Push Buttons       | ~1 mA        | 5V
Piezo Buzzer       | ~10 mA       | 5V
─────────────────────────────────────────
Total (peak)       | ~166 mA      | 5V
Recommended PSU    | 2A minimum   | 5V
```

### Power Options
1. **USB Power** (via Arduino USB port)
   - Up to 500mA from computer
   - Suitable for testing
   - Limited by USB standard

2. **External 5V Supply** (recommended)
   - 5V, 2A power adapter
   - Via Arduino barrel jack
   - Better stability, more current available

3. **Battery Power** (portable option)
   - 4× AA batteries in series (6V)
   - Regulated to 5V via LDO (e.g., LM7805)
   - Portable but heavier

---

## 🚨 Troubleshooting

### Display Not Showing
```
✗ Display is blank
  ✓ Check TFT Shield is firmly seated on Arduino
  ✓ Verify power connections (5V and GND)
  ✓ Try different rotation value (setRotation 0-3)
  ✓ Check MCUFRIEND library is installed
  ✓ Verify display ID detection in serial output

✗ Display shows garbage/static
  ✓ Check SPI pins (50, 51, 52, 53) are not shorted
  ✓ Reduce baud rate (try 9600)
  ✓ Add 100µF capacitor across power rails
```

### Joystick Not Responding
```
✗ Joystick frozen in one direction
  ✓ Verify A8/A9 wiring is correct
  ✓ Check joystick module gets 5V power
  ✓ Read analog values in Serial Monitor
  ✓ Recalibrate min/max values

✗ Joystick drifts randomly
  ✓ Increase deadzone threshold (from 25 to 30)
  ✓ Add 0.1µF capacitor on X and Y lines
  ✓ Move joystick away from power lines (EMI)
```

### Buttons Not Working
```
✗ Button always registers as pressed
  ✓ Check wiring to GND (not floating)
  ✓ Verify INPUT_PULLUP mode in code
  ✓ Check for solder bridges or shorts

✗ Button needs repeated pressing
  ✓ Increase debounce threshold (from 20 to 30)
  ✓ Add 0.1µF capacitor across button pins
  ✓ Check button contacts are clean
```

### Sound Not Playing
```
✗ Buzzer silent
  ✓ Check Pin 25 wiring to buzzer (+)
  ✓ Verify buzzer (-) is connected to GND
  ✓ Test with digitalWrite(25, HIGH) in Serial monitor
  ✓ Check USE_SOUND is set to 1

✗ Sound is very quiet
  ✓ Reduce piezo buzzer distance to ear
  ✓ Increase tone() duration in code
  ✓ Verify 5V is delivering to buzzer
```

---

## 📊 Pin Usage Summary

```
ANALOG PINS:
A8  ← Joystick X-axis
A9  ← Joystick Y-axis

DIGITAL PINS:
22  ← Button ENTER (INPUT_PULLUP)
23  ← Button BACK (INPUT_PULLUP)
24  ← Button JOY_CLICK (INPUT_PULLUP)
25  → Piezo Buzzer (PWM OUTPUT)

50, 51, 52, 53 → SPI (TFT Shield - handled by MCUFRIEND)

POWER:
GND → All components
5V  → All components
```

---

## 🎯 Verification Checklist

Before running OneDeck:

- [ ] Arduino Mega powers on (LED blinks)
- [ ] TFT Shield displays boot screen
- [ ] Joystick moves cursor smoothly
- [ ] All 3 buttons respond in menu
- [ ] Buzzer makes sounds (if present)
- [ ] Menu shows "Total Games: X played"
- [ ] Can select and start games
- [ ] Score increments during gameplay
- [ ] Game-over screen appears when needed
- [ ] HIGH SCORE saves and persists

---

## 📞 Still Having Issues?

1. **Check the code comments** — Each section explains functionality
2. **Review game logic** — Look at specific `updateGame()` function
3. **Test components individually** — Isolate hardware vs software issues
4. **Check Serial output** — Add debug prints to track execution
5. **Verify all connections** — Use multimeter to check continuity

---

## 🎮 Next Steps

✅ Complete hardware setup  
✅ Upload OneDeck.ino  
✅ Run first game  
✅ Enjoy! 

**Need custom hardware?** See the Customization section in README.md

---

**Version:** 1.0  
**Last Updated:** 27 December 2025  
**Status:** Verified ✅
