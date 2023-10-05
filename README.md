# FTDI-Chip
Send a trigger signal from software to a device via usb

Our xenopusProject software requires synchronization between the camera recording of eyes movements and the CED amplifier recording the electrophysiological signal. (The camera shutter opening signals are also recorded by the CED amplifier under Spike2)

We use an FTDI chip (model FT232R usb to uart) to send a trigger signal to the amplifier to start recording the experiment at spike2

