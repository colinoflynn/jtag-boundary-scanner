# Python Interface (ULTRA BETA)

This is a Python interface for JTAG Boundary Scanner. This calls the DLL using CTYPES.

## Notes

* The DLL included in this repo is built for Windows, and is a 32-bit DLL. Thus you will *need to use 32-bit Python*.


## Example

    jtag = JTAGCore()

    try:
        probes = jtag.get_probe_names()
        
        print(probes)
        jtag.open_probe(probes[b"USB JLINK ARM"])
        jtag.scan_init_chain()
        numdev = jtag.get_number_devices()

        print("Found %d devices."%numdev)

        for n in range(0, numdev):
            print("Device %d: %x"%(n, jtag.get_devid(n)))

        print("%x"%jtag.bsdl_devid(r"bsdl_files/CortexMx.bsd"))
        print("%x"%jtag.bsdl_devid(r"bsdl_files/STM32F405_415_407_417_WLCSP90.bsd"))

        jtag.bsdl_attach(r"bsdl_files/STM32F405_415_407_417_WLCSP90.bsd", 1)

        total_pins = jtag.pins_get_number(1)
        print("{} Pins:".format(total_pins))

        for pinid in range(0, total_pins):
            pinname, pinprop = jtag.pin_get_properties(1, pinid)
            print("{:3d} {} {}".format(pinid, pinname, pinprop))

        jtag.set_scan_mode(1, "passive")

        for i in range(0, 25):
            jtag.scan() #Get updated pin status
            print(jtag.pin_get_state(1, "PA9")) #Print status of a pin
            time.sleep(0.05)

        jtag.set_scan_mode(1, "active")
        
        jtag.pin_set_state(1, "PA11", True) #Set pin high (active out)
        jtag.pin_set_state(1, "PA11", "high") #Set pin low (active out)
        jtag.pin_set_state(1, "PA11", False) #Set pin low (active out)
        jtag.pin_set_state(1, "PA11", "low") #Set pin low (active out)
        jtag.pin_set_state(1, "PA11", None) #Set pin to high-z

    except:
        #If we don't do this it hangs!
        jtag.deinit()
        raise
    
    #OK good to go, lets rock and/or roll
    jtag.deinit()