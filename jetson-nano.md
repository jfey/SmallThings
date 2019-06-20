**NVIDIA Jetson Nano**


Basic Hardware Setup


3D Printed Case

Commercial Case

Connected USB Devices


Screen


**Power Supply**

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
manner: Mode 1 is just using **half of the available CPU cores** running
at reduced speed. Thus, if you want to use all of the available oooomph
from the Nano, always run it in mode 0 and power the board accordingly
with the Barrel Jack connector and a decent 4A supply.

The configuration defines some more parameters including the minimum and
maximum frequencies per core. These settings are needed to describe the
Dynaic Voltage and Frequency Scaling" (DVFS). DVFS is a mechanism to
crank up the CPU depending on system load.

A call to

```
sudo jetson_clocks --show
```

reveals the current settings:

```
SOC family:tegra210  Machine:jetson-nano
Online CPUs: 0-3
CPU Cluster Switching: Disabled
cpu0: Online=1 Governor=schedutil MinFreq=102000 MaxFreq=1428000 CurrentFreq=1428000 IdleStates: WFI=1 c7=1
cpu1: Online=1 Governor=schedutil MinFreq=102000 MaxFreq=1428000 CurrentFreq=1428000 IdleStates: WFI=1 c7=1
cpu2: Online=1 Governor=schedutil MinFreq=102000 MaxFreq=1428000 CurrentFreq=1428000 IdleStates: WFI=1 c7=1
cpu3: Online=1 Governor=schedutil MinFreq=102000 MaxFreq=1428000 CurrentFreq=1428000 IdleStates: WFI=1 c7=1
GPU MinFreq=76800000 MaxFreq=921600000 CurrentFreq=153600000
EMC MinFreq=204000000 MaxFreq=1600000000 CurrentFreq=1600000000 FreqOverride=0
Fan: speed=0
NV Power Mode: MAXN
```

Now use the next command to save the current values into a .conf file:

```
sudo jetson_clocks --store oldClocks.conf
```

The content shows how the initial configuration from
```/etc/nvpmodel/nvpmodel_t210_jetson-nano.conf``` turns into the
related system device settings:


```
/sys/devices/system/cpu/cpu0/online:1
/sys/devices/system/cpu/cpu1/online:1
/sys/devices/system/cpu/cpu2/online:1
/sys/devices/system/cpu/cpu3/online:1
/sys/devices/system/cpu/cpu0/cpufreq/scaling_min_freq:102000
/sys/devices/system/cpu/cpu1/cpufreq/scaling_min_freq:102000
/sys/devices/system/cpu/cpu2/cpufreq/scaling_min_freq:102000
/sys/devices/system/cpu/cpu3/cpufreq/scaling_min_freq:102000
/sys/devices/system/cpu/cpu0/cpuidle/state0/disable:0
/sys/devices/system/cpu/cpu0/cpuidle/state1/disable:0
/sys/devices/system/cpu/cpu1/cpuidle/state0/disable:0
/sys/devices/system/cpu/cpu1/cpuidle/state1/disable:0
/sys/devices/system/cpu/cpu2/cpuidle/state0/disable:0
/sys/devices/system/cpu/cpu2/cpuidle/state1/disable:0
/sys/devices/system/cpu/cpu3/cpuidle/state0/disable:0
/sys/devices/system/cpu/cpu3/cpuidle/state1/disable:0
/sys/devices/57000000.gpu/devfreq/57000000.gpu/min_freq:76800000
/sys/devices/57000000.gpu/railgate_enable:1
/sys/kernel/debug/clk/override.emc/clk_state:0
/sys/devices/pwm-fan/target_pwm:0
/sys/devices/pwm-fan/temp_control:1
```

**Note**: When idling the temperature is about 50 degrees Celsius when
the Nano is running based on the default settings. YOur mileage may vary
depending on the current environmental temperature. It may be a good
idea to watch the temp when playing around. The needed script/info about
temperatures can be found in the "Useful Scripts" chapter.

Now it is save to activate the maximum values right away to check
it out:

```
sudo jetson_clocks
```

The updated configuration file looks like this:

```
/sys/devices/system/cpu/cpu0/online:1
/sys/devices/system/cpu/cpu1/online:1
/sys/devices/system/cpu/cpu2/online:1
/sys/devices/system/cpu/cpu3/online:1
/sys/devices/system/cpu/cpu0/cpufreq/scaling_min_freq:1428000
/sys/devices/system/cpu/cpu1/cpufreq/scaling_min_freq:1428000
/sys/devices/system/cpu/cpu2/cpufreq/scaling_min_freq:1428000
/sys/devices/system/cpu/cpu3/cpufreq/scaling_min_freq:1428000
/sys/devices/system/cpu/cpu0/cpuidle/state0/disable:1
/sys/devices/system/cpu/cpu0/cpuidle/state1/disable:1
/sys/devices/system/cpu/cpu1/cpuidle/state0/disable:1
/sys/devices/system/cpu/cpu1/cpuidle/state1/disable:1
/sys/devices/system/cpu/cpu2/cpuidle/state0/disable:1
/sys/devices/system/cpu/cpu2/cpuidle/state1/disable:1
/sys/devices/system/cpu/cpu3/cpuidle/state0/disable:1
/sys/devices/system/cpu/cpu3/cpuidle/state1/disable:1
/sys/devices/57000000.gpu/devfreq/57000000.gpu/min_freq:921600000
/sys/devices/57000000.gpu/railgate_enable:0
/sys/kernel/debug/clk/override.emc/clk_state:1
/sys/devices/pwm-fan/target_pwm:255
/sys/devices/pwm-fan/temp_control:0
```

Now the systems runs at full speed including the fan (if connected). As
a result the temperature drops to 36 degrees when idling.

To switch back to the default settings:

```
sudo jetson_clocks --restore oldClocks.conf
```





Hardware Extensions

Fan




Software Configuration



**Useful Scripts**



Temperature


Machine/Deep Learning





Operating System


Demos/Examples


Forums

