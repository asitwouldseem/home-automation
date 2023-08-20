# Home Automation

> "Why spend five minutes doing a task when you can spend five days automating it?"
> - Ancient Proverb

This is my take on home automation (and homelab, I suppose) based on the following:
- Home Assistant (for automagic goodness)
- Logitech Media Server (multi-room audio)
- Jellyfin (video management)
- TVHeadEnd (DVB streaming and XMLTV management)
- xTeve (TVHeadEnd emulation for Jellyfin)
- Pi-hole (DNS server and network-wide ad blocker)

To take advantage of Home Assistant's OS, I run Home Assistant in a VM on a Late 2014 Mac Mini that lives full-time in my HiFi. Where possible, everything else runs in Podman containers on the same Mac Mini. Don't worry fellow nerds, I flashed the Mac with Ubuntu Server to make this easier to manage. 

## Guiding Principles
1. Local first. Snappy and private.
2. Maintainable and scalable. I'm not a sysadmin.
3. Automation not apps. I shouldn't be reaching for my phone to turn on a light.
4. Just be cool.

## Installation
This is more a reference for me, so the documentation isn't perfect. You can find it in the [/docs](/docs) directory.

## Nuts and bolts
### Multi-Room Audio
[Logitech Media Server](https://github.com/Logitech/slimserver/) is an interesting project. Originally developed by SlimDevices for a suite of hardware players (that Logitech stopped selling in 2012), the community continues to develop and maintain the server software licensed under GPL. It's a bit rough out of the box, but with some tweaks, it really punches above its weight against the likes of Sonos and Roon.

Plugins:
- TVHeadEnd (DVB Audio)
- Spotty (Spotify Connect)
- DSDPlayer (Enable DSD/DXD playback)
- WaveInput (Stream audio from LMS)

The 'server' software runs on the Mac Mini in a Podman container. The Mac also runs a copy of [Squeezelite](https://github.com/ralph-irving/squeezelite) to stream audio from the server to the HiFi. I am a part-time audiophile and have enjoyed a bit of boffinry in getting DSD to work on my main listening setup. `-D u32b` was the argument I needed to get it to play nicely with the Topping E30.

```
squeezelite -o hw:CARD=E30,DEV=0 -s 127.0.0.1 -D u32b -n HiFi
```

I have a number of other players around the apartment either running [PiCorePlayer](https://picoreplayer.org) or Squeezelite. Where possible, I have automations setup to turn on/off players based on the transport controls inside Home Assistant.

I'm struggling to get WaveInput to work inside Podman (most guides cover Docker or local). Any advice is welcome! I am currently feeding the tape loop from my amplifer back into the Mac Mini through its line in.

### Presence Detection
I know [ESPresence](https://espresense.com/) is the darling in this space, but try as I may I've never been able to get it to work. I use Andrew Freyer's [monitor.sh](https://github.com/andrewjfreyer/monitor/blob/master/monitor.sh) on a Pi Zero which plays double duty as my TVHeadEnd server. The Bluetooth confidence reported by this controls an input_select which sets my presence to a number of settings (Away, Home, Recently Arrived/Left). This works a lot better for me than a binary home/away.

### Media Playback
I fell into this rabbit hole in an attempt to slim down how many remotes sat on my lounge. A small Aqara button remote sits on my bookshelf which controls the power state of the components inside the HiFi. 
- Pressing it once triggers a script to toggle the state of the TV. 
- A double press prepares the setup to listen to a record. 
- Music streaming support *should* be handled by starting/stopping the LMS stream.

I am still trying to find a nice way to automate source selection. At the moment I pick the source in Apple Home. BD/SACD playback is also handled from Apple Home.

I use the brilliant [iPeng](http://penguinlovesmusic.de/) on my phone and [Jivelite](https://github.com/ralph-irving/jivelite) for players with screens.

### Climate
I'm not a fan (hah!) of connected A/Cs so use a handy project called [SmartIR]https://(github.com/smarthomehub/smartir) to do all the heavy lifting. I think it's a dead project, but it's still working so I'll keep using it until it breaks! I also use it for my Cambridge amp and Sony BD transport.

I have an IR controlled fan in my bedroom which is controlled by the Broadlink in a Rube Goldberg of YAML.

### Dashboard
I'm working on a System 7 themed ESP32 ePaper display to display important information at a glance. At present my main interface with my automation routines is via ZHA remotes or via Apple Home.

### Voice Control
This is an ongoing effort, but I use a [Grandstream HT801](https://www.grandstream.com/products/gateways-and-atas/analog-telephone-adaptors/product/ht801) to make an old Telecom Australia 800 series rotary phone talk to Home Assistant. My dream would be a [Telecom Gold Phone](https://youtube.com/watch?v=we5zxv5c00g) and tie into the coin circuitry for further automations. I use the built in Whisper and Piper voice pipelines.

### ZHA
Z-wave products are super expensive down under and I'm not entirely sure why. I use a [ConBee II](https://phoscon.de/en/conbee2) dongle to bring Zigbee devices into my Home Assistant install. If, like me, you use KVM for virtualisation. You'll need to [pass through the serial device](https://community.home-assistant.io/t/conbee-ii-and-kvm/333560).

Current Zigbee devices:
- 3x Aqara Water Leak Sensors
- 2x Aqara remote
- 3x IKEA TRÅDFRI lamps (E14/E27)
- 1x IKEA TRÅDFRI remote control
- 1x Philips Hue Indoor Motion Sensor
- 1x GLEDOPTO RGB-CCT LED strip

I have also exposed a Bluetooth dongle for future expandability, though I don't rate Bluetooth for privacy and reliability.

## About Me
Cameron M - [@asitwouldseem](https://twitter.com/asitwouldseem) - hello@cameron-morgan.com