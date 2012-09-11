node-automation
===============

Modular, event-driven home automation library

Features:
---------

- Easily build your own home automation server
- Modular design allows one to support *any* device
- [Built-in support for a few basic devices](#built-in-devices)

Install:
--------

Install using NPM

```bash
npm install automation
```

API Overview
------------

### Background

node-automation is an event-driven, modular library designed to help
developers and hobbyists build customized home automation solutions.

node-automation is built upon the idea that every device has one or more of
the following characteristics:

- the ability to receive and execute commands
- the ability to emit a signal when a particular event occurs
- and, the ability to respond to queries that inquire about its state

That is, any device (whether it be a physical device or a device implemented
in software) can be sent a command to perform an action, can emit signals when
an event occurs, or can communicate information about its current state.

Consider a temperature sensor, for example. A temperature sensor would likely
not respond to commands, but rather, it would have the ability to respond to
state queries. Or, perhaps, the sensor would emit temperature information once
every 200 milliseconds. A more sophisticated temperature sensor might also
have the ability to receive commands that configure it.

### Sub-devices

The purpose of some devices is to bridge communication between other
"sub-devices." For example, the X10 CM11A is a device that attaches to a
computer's RS-232 serial port and allows the computer to interface with other
X10 devices over the power line (like appliance modules or lamp modules). When
"bridging" devices like the CM11A are used, it is possible to use the
sub-devices associated with that particular device.

### Autumn8 Programming Language

When a device emits an event, any corresponding event handlers will be
executed, as one might expect. In node-automation, one can program event
handlers using JavaScript, or they can build event handlers using the Autumn8
(pronouned aw-tuh-meyt) programming language. Autumn8 is a very simple
programming language that allows one to define event handlers, conditions
under which the event handlers will be triggered, and the commands that will
be sent to various devices. The purpose of Autumn8 is to provide a simple,
cross-platform, independent language for defining the behavior of automation
routines. In addition, *.otom8 files can be used to quickly configure
node-automation servers.

An example Autumn8 file is shown below. This file defines an X10 CM11A module,
which is a physical device connected to the computer's serial port, and it
defines a PandoraMusicPlayer device, a third-party device implemented entirely
in software. Notice how the inner-workings of each device is hidden from the
user.

```autumn8
//Define a CM11A device connected to a USB-to-RS-232 port
Device `x10` CM11A("/dev/ttyUSB0") {
	/* Within the scope of CM11A, we can define its Sub-devices
		like an ApplicanceModule or LampModule.
	*/
	Device `stereo` ApplianceModule("A2") {
		Event "on" {
			//Allow A2 on to also allow the user to skip a song
			if(pandora.playing)
				pandora.next();
			else
				pandora.play();
		}
		Event "off" {
			pandora.pause();
		}
		Event "dim" {
			volume.down();
		}
		Event "bright" {
			volume.up();
		}
	}
}
/* The following line looks in /use/lib/node_modules/automation-pandora
	for the code that exposes the PandoraMusicPlayer node-automation device.
*/
Device `pandora` automation-pandora/PandoraMusicPlayer("username", "pass") {}
Device `volume` automation-alsa/ALSAVolumeControl() {}
```

Built-in devices:
-----------------

node-automation ships with built-in support for the following devices:

### Built-in Physical Devices and Sub-devices:

- X10 CM11A (CM17A might work)
	- Appliance Module (AM486, SR227 and similar devices)
	- Lamp Module (LM465 and similar devices)
	- MS16A ActiveEye Motion Sensor

### Built-in Software Devices:

- Pianobar interface (to stream music from Pandora in Linux)
- ALSA Mixer controller (to change volume and other sound settings in Linux)
- Bluetooth Device Proximity Sensor (to detect how far away a particular
	Bluetooth device is; works only in Linux)
- Timer (emits an event after a fixed amount of time has passed)
- Scheduler (emits events at regular time intervals)
	- Also emits sunrise/sunset events and dawn/dusk events (based on the
	definition of civil twilight)
	- Special thanks to [node-schedule]
	(https://github.com/mattpat/node-schedule) for making this an easy-add
