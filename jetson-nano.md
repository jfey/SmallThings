**NVIDIA Jetson Nano**


Basic Hardware Setup


3D Printed Case

Commercial Case

Connected USB Devices


Screen


Power Supply

The Jetson Nano can run in two different power modes. Per default it is
running in the "10 Watt" mode (also known as mode 0) but there is also
the optional/fallback "5 Watt" mode. The 10 Watt mode has some catch:
The Jetson module itself already needs 10 Watt and thus there is no
reserve available if you are running the board via the Micro-USB
connector with 5V and 2A max. The module does not get what it wants
since the Jetson board and all connected USB devices drain some power
too.

During the first test runs the board was still fine (mouse, keyboard,
Webcam) and it ran also nicely even after adding the fan. Still it is
highly recommended to switch to a 5V 4A power supply with the barrel
jack connector (5V inside). Now everything runs inside the specs.

To activate the barrel power supply a Jumper needs to be set




Hardware Extensions


Software Configuration



Useful Scripts


Power Mode

Show current power mode:

```
sudo nvpmodel -q
```

Machine/Deep Learning





Operating System


Demos/Examples


Forums

