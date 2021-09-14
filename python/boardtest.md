---
sort: 1
---

# Boardtest

Testprogramm für das Zusatzboard mit LEDs und Buttons

```scss
#!/usr/bin/env python3
# Zusatzboard - Test

from gpiozero import LED, Button
from time import sleep
from signal import pause

OUTPINS = [18, 23, 24, 25]
INPINS = [22, 27, 17]

def leds_on():
    list(map(lambda led:led.on(), leds))

def leds_off():
    list(map(lambda led:led.off(), leds))

leds = []
buttons = []
for pin in OUTPINS:
    leds.append(LED(pin))
for pin in INPINS:
    buttons.append(Button(pin))

i = 0
while i < 2:
    i += 1
    for led in leds:
        led.on()
        sleep(0.2)
        led.off()
        sleep(0.2)

for button in buttons:
    for led in leds:
        button.when_pressed = leds_on
        button.when_released = leds_off

pause()
```

