This post describes how to interface with a NewPort Motion Controller over USB using a Mac. 

This process uses Python, with the [pyUSB](http://walac.github.io/pyusb/) module and the [libusb-compat](http://www.libusb.org) backend. 
The script launches a console type interface, which accepts valid NewPort style commands (see bottom of page).  

**Note** This script has only been tested with a single [closed-loop Picomotor controller (model 8745)](https://www.newport.com/Closed-Loop-Picomotor-Motion-Controller/1023364/1033/info.aspx).

###Install

#### Install [Homebrew](http://brew.sh) and Python

Follow the steps at [this guide](http://docs.python-guide.org/en/latest/starting/install/osx/) for setting up Python and homebrew. You'll need homebrew to install the USB backend, and Python 2.7.x to run my code. 

####Install the USB Backend - libusb

```bash
$ brew install libusb-compat
```

####Install pyUSB

```bash
$ pip install pyusb
```

### Using the Python Code

Launch the code by typing the following into terminal

```bash
$ python newport.py
```

You should see this prompt open up, asking you to identify the motor to connect to. 

```bash
#######################################################################
#	Python controller for NewFocus Picomotor Controller
#######################################################################



Run the following command in a new terminal window:
	$ system_profiler SPUSBDataType

Enter Product ID:
>
```

In another terminal window, query the connected USB devices with `system_profiler SPUSBDataType`. You're looking for a similar connection to this one:

```bash
    USB 3.0 Hi-Speed Bus:

      Host Controller Location: Built-in USB
      Host Controller Driver: AppleUSBXHCI
      PCI Device ID: 0x1e31
      PCI Revision ID: 0x0004
      PCI Vendor ID: 0x8086
      Bus Number: 0x0a

        Picomotor Controller:

          Product ID: 0x4000
          Vendor ID: 0x104d
          Version: 1.00
          Serial Number: 12345678
          Speed: Up to 12 Mb/sec
          Manufacturer: New Focus
          Location ID: 0x14200000 / 6
          Current Available (mA): 500
          Current Required (mA): Unknown (Device has not been configured)
```

Enter your controller's `Product ID` and `Vendor ID`. The script should find the device and report back the connected motors. Try it out by sending a command to move one of these motors. 

```bash
###############################################################################
#	Python controller for NewFocus Picomotor Controller
################################################################################


Run the following command in a new terminal window:
	$ system_profiler SPUSBDataType

Enter Product ID:
> 0x4000
Enter Vendor ID:
> 0x104d


Connected to Motor Controller Model 8742. Firmware Version 2.2 08/01/13

Motor #1: 'Standard' Motor
Motor #2: No motor connected
Motor #3: No motor connected
Motor #4: No motor connected

        Picomotor Command Line
        ---------------------------

        Enter a valid NewFocus command, or 'quit' to exit the program.

        Common Commands:
            xMV[+-]: .....Indefinitely move motor 'x' in + or - direction
                 ST: .....Stop all motor movement
              xPRnn: .....Move motor 'x' 'nn' steps



Input > 1MV+
Input > ST
Input >
```


For a full list of commands on the 8745 controller, see section 6.2 in the [user manual](https://assets.newport.com/webDocuments-EN/images/8743_CL_User_Manual_revA.pdf)

**Note** Not all of these have been tested, and there are undoubtedly some bugs. 

Once you have the Product ID and Vendor ID, you can write these directly into the python script, so you don't have to re-enter them every time you start the program.

*Inside newport.py*

```python
if __name__ == '__main__':
    print('\n\n')
    print('#'*80)
    print('#\tPython controller for NewFocus Picomotor Controller')
    print('#'*80)
    print('\n')

    idProduct = None #'0x4000'
    idVendor = None  #'0x104d'

    if not (idProduct or idVendor):
        print('Run the following command in a new terminal window:')
        print('\t$ system_profiler SPUSBDataType\n')
        print('Enter Product ID:')
        idProduct = raw_input('> ') 
        print('Enter Vendor ID:')
        idVendor = raw_input('> ') 
        print('\n')
```

Replace the `None` after `idProduct` and `idVendor` with your Product ID and Vendor ID. When the script runs in the future, it won't prompt you for these values. 
