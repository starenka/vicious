Contrib
-------
Contrib libraries, or widget types, are extra snippets of code you can
use. Some are for less common hardware, and other were contributed by
Vicious users. The contrib directory also holds widget types that were
obsoleted or rewritten. Contrib widgets will not be imported by init
unless you explicitly enable it, or load them in your rc.lua.


Usage within Awesome
--------------------
To use contrib widgets uncomment the line that loads them in
init.lua. Or you can load them in your rc.lua after you require
Vicious:

  vicious = require("vicious")
  vicious.contrib = require("vicious.contrib")


Widget types
------------
Most widget types consist of worker functions that take the "format"
argument given to vicious.register as the first argument, "warg" as
the second, and return a table of values to insert in the format
string. But we have not insisted on this coding style in contrib. So
widgets like PulseAudio have emerged that are different. These widgets
could also depend on Lua libraries that are not distributed with the
core Lua distribution. Ease of installation and use does not
necessarily have to apply to contributed widgets.

**vicious.contrib.ac**

Provide status about the power supply (AC)
Supported platforms: Linux (required tools: `sysfs`)

- Arguments:
  * takes the AC device as an argument, i.e "AC" or "ACAD"
  * the device is linked under /sys/class/power_supply/ and should
    have a file called "online"
- Returns
  * if AC is connected, $1 returns "On", if not it returns "Off",
    if AC doesn't exist, $1 is "N/A"

**vicious.contrib.ati**

Provides various info about ATI GPU status.
Supported platforms: Linux (required tools: `sysfs`)

- Arguments:
  * takes card ID as an argument, i.e. "card0" (and where possible,
    uses debugfs to gather data on radeon power management)
- Returns:
  * a table with string keys: {method}, {dpm_state},
    {dpm_perf_level}, {profile}, {engine_clock mhz}, {engine_clock khz},
    {memory_clock mhz}, {memory_clock khz}, {voltage v}, {voltage mv}

**vicious.contrib.batpmu**

**vicious.contrib.batproc**

**vicious.contrib.countfiles**

**vicious.contrib.dio**

Provides I/O statistics for requested storage devices

- Arguments:
  - takes the disk as an argument, i.e. "sda" (or a specific
    partition, i.e. "sda/sda2")
- Returns:
  - a table with string keys: {total_s}, {total_kb}, {total_mb},
    {read_s}, {read_kb}, {read_mb}, {write_s}, {write_kb}, {write_mb}
    and {sched}

**vicious.contrib.mpc**

vicious.contrib.netcfg
  -

**vicious.contrib.net**

**vicious.contrib.openweather**

Provides weather information for a requested city

- Arguments
  * takes OpenWeatherMap city ID as an argument, i.e. "1275339"
- Returns
  * a table with string keys: {city}, {wind deg}, {wind aim},
    {wind kmh}, {wind mps}, {sky}, {weather}, {temp c}, {humid}, {press}

**vicious.contrib.nvinf**

provides GPU utilization, core temperature, clock frequency information about Nvidia GPU from nvidia-settings
Supported Platforms: platform independent

- Arguments 
  * takes optional card ID as an argument, i.e. "1", or defaults to ID 0
- Returns 
  * first 4 values as usage of GPU core, memory, video engine and
    PCIe bandwidth, 5th as temperature of requested graphics device, 6th
    as frequency of GPU core, 7th as memory transfer rate

**vicious.contrib.nvsmi**

Provides (very basic) information about Nvidia GPU status from SMI
Supported Platforms: platform independent

- Arguments:
  * takes optional card ID as an argument, i.e. "1", or defaults to ID 0
- Returns:
  * returns 1st value as temperature of requested graphics device

**vicious.contrib.ossvol**

**vicious.contrib.pop**

**vicious.contrib.pulse**

Provides volume levels of requested pulseaudio sinks and functions to
manipulate them

- Arguments
  * takes the name of a sink as an optional argument.  a number will
    be interpret as an index, if no argument is given, it will take
    the first-best
  * to get a list of available sinks use the command: pacmd
    list-sinks | grep 'name:'
- Returns
  * returns 1st value as the volume level
- vicious.contrib.pulse.add(percent, sink)
  * @percent is a number, which increments or decrements the volume
    level by its value in percent
  * @sink optional, same usage as in vicious.contrib.pulse
  * returns the exit status of pacmd
- vicious.contrib.pulse.toggle(sink)
  * inverts the volume state (mute -> unmute; unmute -> mute)
  * @sink optional, same usage as in vicious.contrib.pulse
  * returns the exit status of pacmd

**vicious.contrib.rss**

**vicious.contrib.sensors**

**vicious.contrib.wpa**

Provides information about the wifi status
Supported Platforms: platform independent (`wpa_cli`)

- Arguments
  - takes the interface as an argument, i.e "wlan0" or "wlan1"
- Returns
  - a table with string keys: {ssid}, {qual}, {ip}, {bssid}

**vicious.contrib.buildbot**

Provides last build status for configured buildbot builders (http://trac.buildbot.net/)
Supported Platforms: platform independent

- Returns:
  * returns build status in the format: [<builderName>.<currentBuildNumber>.<lastSuccessfulBuildNumber>]
  * if <currentBuildNumber> is the same as <lastSuccessfulBuildNumber> only one number is displayed
  * <buildNumber> colors: red - failed, green - successful, yellow - in progress
  * it depends on lua json parser (e.g. liblua5.1-json on Ubuntu 12.04)


Usage examples
---------------------------------
Pulse Audio widget
  vol = wibox.widget.textbox()
  vicious.register(vol, vicious.contrib.pulse, " $1%", 2, "alsa_output.pci-0000_00_1b.0.analog-stereo")
  vol:buttons(awful.util.table.join(
    awful.button({ }, 1, function () awful.util.spawn("pavucontrol") end),
    awful.button({ }, 4, function () vicious.contrib.pulse.add(5,"alsa_output.pci-0000_00_1b.0.analog-stereo") end),
    awful.button({ }, 5, function () vicious.contrib.pulse.add(-5,"alsa_output.pci-0000_00_1b.0.analog-stereo") end)
  ))

Buildbot widget
  buildbotwidget = wibox.widget.textbox()
  local buildbotwidget_warg = {
    {builder="coverage", url="http://buildbot.buildbot.net"},
    {builder="tarball-slave", url="http://buildbot.buildbot.net"}
  }
  vicious.register(buildbotwidget, vicious.contrib.buildbot, "$1,", 3600, buildbotwidget_warg)
