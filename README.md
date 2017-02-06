# StandingDeskControl
A UWP App which will run on a RaspBerry Pi to control the height of an Ikea Bekant Standing Desk.

# Hardware Interface
Controlling the desk requires interfacing with the control hardware. Thankfully the desk has a very simple 3-wire control system which runs on 12V.

From what I can tell, The Ikea Bekant Standing Desk Frame uses Molex Part Number 39-01-4031 (Digikey Part Number WM23608-ND) recepticles and Molex Part Number 39-01-4033 (Digikey part number WM23611-ND) plugs for the control electronic interconnections. The goal of this project is to build another plug-in module which can provide automated control of the standing desk electronics without interfering with the original manual controls.

# Theory of Operation
The standing desk does not contain any height sensing electronics as far as I can tell: it uses limit switches to prevent the desk from travelling higher than allowed or lower than allowed.

The control box is more complex than it looks. I initially thought the control box acted as a dumb switch that changed the direction of current flow based on which button was pressed, and that the saftey key was just an in-line citcuit breaker which would prevent the entire circuit from operating if it was removed. This doens't appear to be the case: I think the control box generates signal based on which switch is pressed which travels back down the wire loop to a set of relays. The signal may be a simple +/- voltage signal which is captured by a set of diodes, or it may actually be some sort of message transmitted down a single wire bus. I'm waiting on Molex connector hardware so that I can build a tap-in point without damaging my desk hardware.

I've poked around  I beleive the following pinout is correct:

Pin 1 (rounded): 24v DC (VERIFIED)
Pin 2 (long square): System ground (Strongly suspected but not verified)
Pin 3 (short square): Table Movement Control (Strongly suspected but not verified)

Pin 2 and 3 assumptions are based on the power supply: the power supply only has connections on Pin 1 and 2 and has no connection to Pin 3. This leads me to suspect that:

- There is a continuous power bus which travels around the entire standing desk from the power supply, with Pin 1 being +24v and Pin 2 being ground
- The table control signal has no effect on the power supply so Pin 3 is left empty on that connector

# IoT Controller Design

Based on this theory of operation, the RaspBerry Pi will sit in-line with the manual control panel. Normally, the Rasperry Pi will allow the manual controls to dictate how the table moves. When the Raspberry Pi is issued a table move command, it will:

1. Isolate the manual controls from the system using a relay or other appropriate mechanism to prevent conflicts on the signal wire
2. Move the table to the extreme limit of movement in the direction of travel to trip the limit switch. Ex: if we are moving to the standing position, move the table to maximum height
3. Move the table in the oposite direction for N milliseconds to get the table to the correct height
4. Engage the manual controls

The Raspberry Pi must be able to detect when the manual controls are in use. This will be used to calibrate the user selected heights. To calibrate the table, the Raspberry Pi will:

1. Isolate the manual controls.
2. Move the table to the extreme limit of movement in the direction of travel to trip the limit switch.
3. Engage the manual controls.
4. Observe how long the user presses the manual controls to determine the correct height settings.
