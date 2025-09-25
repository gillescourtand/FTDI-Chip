# FTDI-Chip
Send a trigger signal from software to a device via usb

Our xenopusProject software requires synchronization between the camera recording of eyes movements and the CED amplifier recording the electrophysiological signal. (The camera shutter opening signals are also recorded by the CED amplifier under Spike2)

We use an FTDI chip (model FT232R usb to uart) to send a trigger signal to the amplifier to start recording the experiment at spike2

![ftdi232R_detail](https://github.com/user-attachments/assets/197378ac-140b-44f9-b524-75515ea63f3b)

## Installation
[official page](https://ftdichip.com/drivers/)
FT4232R requires the D2XX drivers : release CDM2123620
Use [Zadig](https://zadig.akeo.ie/) to install the driver :
![Capture_zadig_deviceslist](https://github.com/user-attachments/assets/b03c933b-cf10-4690-a0ad-3b7ba27c3fb7)

Choose libusbK as new driver

![Capture_zadig_selectdriver](https://github.com/user-attachments/assets/53755a3a-2e25-403d-8879-8f6edff66403)

## Python code

```
#ftdi (trigger)
from pylibftdi import BitBangDevice, Driver
...
def get_ftdi_device_list():
    """
    return a list of lines, each a colon-separated
    vendor:product:serial summary of detected devices
    """
    dev_list = []
    # in case of several devices use a list of references
    ref_list = []
    for device in Driver().list_devices():
        # list_devices returns bytes rather than strings
        # dev_info = map(lambda x: x.decode('latin1'), device)
        # device must always be this triple
        vendor, product, serial = device
        dev_list.append("%s:%s:%s" % (vendor, product, serial))
        print("ftdi : ",dev_list)
        ref_list=[vendor, product, serial]
    return ref_list                
        
def trigger():
    try :
        # devices_list=get_ftdi_device_list()
        ftdiRef_list=get_ftdi_device_list()
        # print("ftdi : ",devices_list)
        #QtWidgets.QMessageBox.warning(None,"debug",str(devices_list),QtWidgets.QMessageBox.Ok)
        #with y('FTE00P4L') as bb:
        # if len(devices_list)!=0:
        if len(ftdiRef_list)!=0:    
            with BitBangDevice(ftdiRef_list[1]) as bb:
                bb.direction = 0x0F  # four LSB are output(1), four MSB are input(0)
                bb.port |= 2         # set bit 1
                bb.port &= 0xFE      # clear bit 0                
    except sys.exc_info()[0] as e:
        #e = sys.exc_info()[0]
        print("error sys : ",e)
        QtWidgets.QMessageBox.warning(None,"Error",str(e)) 
        #QMessageBox.warning(self, '', str(e))
    except IOError as e :
        print(e)
        QtWidgets.QMessageBox.warning(None,"Error",str(e))
```

