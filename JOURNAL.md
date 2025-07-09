---
title: "NeoNav Precision 3D"
author: "Jayx2u"
description: "A three degrees of freedom mouse that allows for swift and efficient movement within 3D virtual environments."
created_at: "2025-06-02"
---

## 2025-06-02 - The Great Unravelling

I want to make a ✨ **computer mouse** ✨. _Revolutionary idea I know right..._ but it's a mouse that can move in a 3D space. Using trackpad for Fusion 360 is pretty annoying so why not make a computer mouse that can pan, home, orbit and fit view by just moving your wrist a bit! There's already a product called _3Dconnexion Spacemouse_ but I am NOT paying A$299 for that...

![](https://github.com/Jayx2u/neonav/blob/main/assets/SCR-20250602-knwy.png?raw=true)

So how do I make something that can turn hand movements into 3D movement on the screen? My system IPO looks like this:
- **Input:** A control object moves from human hand movements
- **Process:** The system captures the movement data of the control object
- **Output:** The cursor position updates on the basis of the processed movement
data.

I was initially thinking of using an IMU within a control cap that moves around but that would have drift so instead I chose instead to use a magnetometer with a permanent magnet that the user moves around the sensor to detect movement in `x, y, z`. I want something stationary like the _3Dconnexion Spacemouse_ butttt how could I move this magnet around whilst having it basically return to a home position? That's right!!! Using a Stewart Platform that relies on passive mechanical elements, specifically springs and elastic bands!! When the input element is pushed in any direction, springs will be stretched and in combination with rubber bands that provides the elastic restoring force; this generates a restoring force that pulls the element back to its home position when released. Basically this ensures that as the user moves the input element further from the centre, the restoring force increases, providing a predictable and smooth resistance that guides the element
back to its home position when released.

TDLR: Here's what I got in mind:

![](https://github.com/Jayx2u/neonav/blob/main/assets/mechanical-cad.png?raw=true)

I want to first see if I can feasibly track in the x, y, z axis using a magnetometer so using a few parts that I "borrowed," I made this proof-of-concept!

![](https://github.com/Jayx2u/neonav/blob/main/assets/mag-prototype.jpeg?raw=true)

It has a _Adafruit TLV493D Triple-Axis Magnetometer_ connected to an Arduino Uno. Here is code to read it!
```c
#include "TLx493D_inc.hpp"
#include <Wire.h>

using namespace ifx::tlx493d;

const uint8_t POWER_PIN = 7;

TLx493D_A1B6 dut(Wire, TLx493D_IIC_ADDR_A0_e);

void setup() {
    Serial.begin(9600);
    while (!Serial) { delay(10); }
    delay(3000);

    dut.setPowerPin(POWER_PIN, OUTPUT, INPUT, HIGH, LOW, 50, 250000);

    dut.begin();

    Serial.println("B(x),B(y),B(z),Temperature");
}

void loop() {
    double temp, x, y, z;


    if (dut.getMagneticFieldAndTemperature(&x, &y, &z, &temp)) {
        Serial.print(x);
        Serial.print(",");
        Serial.print(y);
        Serial.print(",");
        Serial.print(z);
        Serial.println(",");
    }
    
}
```
And here it is in action, it works :D

![](https://github.com/Jayx2u/neonav/blob/main/assets/SCR-20250602-lafm.png?raw=true)

*Time spent: 5h*

## 2025-06-24 - KiCADing: Failing and then not failing

Yeah so this idea was left in the dust for a while... but it's basically the school holidays! Time to design a PCB for the magnetometer and microcontroller. "Off-camera grinding" led to this schematic:

![](https://github.com/Jayx2u/neonav/blob/main/assets/SCR-20250624-ldey.png?raw=true)

A _Seed Studio Xiao RP2040_ as the brains (as I've had previous experience with it and it was very nice) with a _TLV493DA1B6HTSA2_ (basically the same as the one from the proof-of-concept) for the magnetometer sensor. Also level shifting to converting 5V to 3.3V.

And with that, PCB design:

![](https://github.com/Jayx2u/neonav/blob/main/assets/SCR-20250624-lhzx.png?raw=true)

Very nice design with cats and miku I know right. Showed this to one of my friends annndddd they're like "why do you need level shifting..." which was a great question. Turns out, I don't need it. My beautiful tracing all going to waste :c

![](https://github.com/Jayx2u/neonav/blob/main/assets/SCR-20250624-lnop.png?raw=true)
![](https://github.com/Jayx2u/neonav/blob/main/assets/SCR-20250624-loda.png?raw=true)

Some deleting and changes to the traces later, we have the final PCB design done!

*Time spent: 7h*

## 2025-06-25 - CAD Designing
With the electronic systems out the way, it's time for the mechanical systems. 
