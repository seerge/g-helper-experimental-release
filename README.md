Hello fellow community,

[Recently I have discovered a way to **directly set fan speed** in percentage points for each fan individually.](https://github.com/seerge/g-helper/discussions/2272)

It uses same method as MyASUS does when runs your "fan testing".

In existing and standard way to set a "custom" fan curve - fan behavior would be still **handled by BIOS** and follow it's internal algorithm. This BIOS algorithm is different for different models, sometimes includes a very high hysteresis (delays before acting), can override some custom fan behavior, etc.

In "alternative" approach, it's possible to actually literally **manually** set fan speed every second based on temperature readings.

This build implements "manual" fan control as soon as you check ``Apply Custom Fan Curve``, when you uncheck it - fan behavior will be returned back to BIOS.

### Current implementation details
- More precise fan speeds, following custom fan curve as close as possible
- Fan speeds are being updated every second
- CPU and GPU temps are calculated as an average from **last 6 seconds** (can be set via config)
- CPU fan follows CPU temp, GPU fan follows GPU temp, Mid fan follows CPU temp
- If GPU temp is not available by some reason (for example GPU is disabled or sleeping) - GPU fan will follow CPU temp instead
- If CPU temp is also not available by some reason - fan behavior will be returned back to BIOS defaults
- Minimum fan speed is 20% unless it's set to OFF completely (similar to how BIOS handles this)
- Maximum fan speed is 100%
- Fan speed will follow line between dots, not only dots themselves
- Fans will react on temp change almost immediately

### Potential downsides and risks
- If you quit application incorrectly during manual fan control, fans may keep running on last used speed and will not react on temperature changes
- No idiot-protection. If you set fan speeds to be low, they will be low and your device will heat up quickly

### Please test on your own risk (!) and only as a last resort in case you can't achieve desired fan behavior otherwise

# [Download Experimental Release](https://github.com/seerge/g-helper-experimental-release/releases/latest/download/GHelper.zip)

> [!NOTE]
> Latest Asus System Control Interface v 3.1.**41+** restricts access to driver required for the "manual" fan control only for SYSTEM processes. So if it doesn't work for you - you can (temporarily) [uninstall new one](https://github.com/seerge/g-helper/wiki/Troubleshooting#reinstalling-asus-system-control-interface) and install previous [version 3.1.40](https://dlcdnets.asus.com/pub/ASUS/GamingNB/Image/Software/SoftwareandUtility/16402/ASUSSystemControlInterfacev3_ASUS_Z_V3.1.40.0_16402.exe) (or below)


Possible params for ``%APPDATA%\GHelper\config.json`` (with their default values)

``"fan_min": 20,`` - minimum fan speed (in %) when it's not OFF
``"fan_hysteresis": 6,`` - number of seconds to calculate average CPU / GPU temp (set 1 for instant reaction)
``"fan_shift" : 50,`` - weighted average coefficient between CPU (or GPU) temp and MAX(CPU,GPU) temps in a range from 0 to 100

- If you want GPU (CPU) fan to follow only GPU (CPU) temp, leave it as is
- If you want GPU (CPU) fan to follow MAX(CPU,GPU) temps, set it to 100
- If you want GPU (CPU) fan to follow average between GPU(CPU) and MAX(CPU,GPU) set it to 50, etc :)

If you see a big (more than 500RPM) difference between fan curve and actual readings - try to click ``Calibrate``, it will run a quick measurement process and adjust Y-axis to match actual maximum RPMs

You will see what is actually being set as your fan speeds on ``Fans+Power`` screen under fan curves chart. Don't forget to click ``Apply``.

![Screenshot 2024-03-23 155637](https://github.com/seerge/g-helper/assets/5920850/232ccad5-6256-49eb-b868-6ca1887f06c2)
