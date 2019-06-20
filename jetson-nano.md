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
highly recommended to switch to a 5V 4A power supply with the Barrel
Jack connector (5V inside). Now everything runs inside the specs and the
power hungry DL inference can run safely.

To activate the Barrel Jack power supply a Jumper needs to be set


Power Mode

Show current power mode:

```
sudo nvpmodel -q
```

To set mode 0 (10W):

```
sudo nvpmodel -m 0
```


To set mode 0 (10W):

```
sudo nvpmodel -m 1
```

It is possible to define your own nvpmodel - the respective
configuration file can be found here:

```
nano /etc/nvpmodel/nvpmodel_t210_jetson-nano.conf
```

The configuration allows one to change clock rates and many other
details (to be covered later). But the "Power_Model Definitions"
sections is revealing:


```
###########################
#                         #
# POWER_MODEL DEFINITIONS #
#                         #
###########################

# MAXN is the NONE power model to release all constraints
< POWER_MODEL ID=0 NAME=MAXN >
CPU_ONLINE CORE_0 1
CPU_ONLINE CORE_1 1
CPU_ONLINE CORE_2 1
CPU_ONLINE CORE_3 1
CPU_A57 MIN_FREQ  0
CPU_A57 MAX_FREQ -1
GPU_POWER_CONTROL_ENABLE GPU_PWR_CNTL_EN on
GPU MIN_FREQ  0
GPU MAX_FREQ -1
GPU_POWER_CONTROL_DISABLE GPU_PWR_CNTL_DIS auto
EMC MAX_FREQ 0

< POWER_MODEL ID=1 NAME=5W >
CPU_ONLINE CORE_0 1
CPU_ONLINE CORE_1 1
CPU_ONLINE CORE_2 0
CPU_ONLINE CORE_3 0
CPU_A57 MIN_FREQ  0
CPU_A57 MAX_FREQ 918000
GPU_POWER_CONTROL_ENABLE GPU_PWR_CNTL_EN on
GPU MIN_FREQ 0
GPU MAX_FREQ 640000000
GPU_POWER_CONTROL_DISABLE GPU_PWR_CNTL_DIS auto
EMC MAX_FREQ 1600000000
```

As can be seen the power consumption is controlled in a very easy
manner: Mode 1 is just using half of the available CPU cores running at
reduced speed. This, if you want to use all of the available oooomph
from the Nano, run in mode 0 and power the board with the Barrel Jack
connector.




Hardware Extensions


Software Configuration



**Useful Scripts**

Dynamic Voltage and Frequency Scaling (DVFS)




Machine/Deep Learning





Operating System


Demos/Examples


Forums

