# StandingDeskControl
A UWP App which will run on a RaspBerry Pi to control the height of an Ikea Bekant Standing Desk.

# Hardware Interface
Moving the desk requires interfacing with the control hardware. The control hardware is an overly complex digital control system that Ikea purchased from ROL Ergo. It uses the ROL Ergo iDrive system.

The Ikea Bekant Standing Desk Frame uses Molex Part Number 39-01-4031 (Digikey Part Number WM23608-ND) recepticles and Molex Part Number 39-01-4033 (Digikey part number WM23611-ND) plugs for the control electronic interconnections. The goal of this project is to build another plug-in module which can provide automated control of the standing desk electronics without interfering with the original manual controls.

# Theory of Operation
The standing desk uses the ROL Ergo iDrive electronic control system. See: http://www.rolergo.com/products/control-systems/

From the ROL Ergo product manuals, the standing desk electronics contain a "memory" of the height at which the desk was last set. Taking the electronics apart I discovered that there are no limit switches which control the table. Instead, I suspect that there are current sensors in the motor control electronics which detect when the motors are under high load. It uses this to detect the top and bottom of the table during homing and movement.

The entire table is deceptively complex. The standing desk leg units each have an iDrive circuit board in them, which contains a set of relays as well as an MCU. The legs that I have are stamped with "FW 3.8".

I've poked around in the control boxes and I've worked out the pinout for the wires connecting the system:

Pin 1 (rounded): 24-34VDC

Pin 2 (long square): System ground

Pin 3 (short square): Table Movement Control

All the wires loop around the desk to form a continuous bus. No matter where you plug the various elements in it is guaranteed to work since all the components share a common bus.

# Table Movement Control Bus
With an oscilloscope attached it's clear that the control bus is a digital signal of some kind. It's clearly a single wire serial protocol, though it doesn't match the expected waveforms of a 1-Wire compliant bus. The signal floats at 12v (voltage divider from power supply's 24v acting as the pullup) and the bus is active low at 1.6v.

There's a "heartbeat" transmission which is being broadcast over the TMC bus at all times, even when the desk isn't moving. When the desk is moving, the heartbeat transmission doesn't visibly change.

Each heartbeat transmission seems to be composed of multiple packets: a single, very long and complex packet followed by 7 small packets followed by another longer and more complex packet. This sequence then repeats.

The "Start condition" for this bus seems to be ~680us of logic 0 (12v) followed by ~680us of logic 1 (1.6v).

Based on the bit timings, this bus appears to be running at 10 Khz.

# IoT Controller Design

TBD.
