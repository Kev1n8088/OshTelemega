# Mega3080 v1.0


The Mega3080 FCU is an evolved clone of the Altus Metrum Telemega. Using the same firmware (meaning same sensor suite), the primary developments are:
- USB-C
- Wago style spring terminals
- Better GPS RF routing, and external GPS antenna
- Companion port with extra UART and space for potential direct Mosaic Integration
- Mag switch with "hard to disarm" feature 
- Slightly smaller (30x80mm instead of 31.25x82.55mm)
- Better reverse polarity protection, including on pyro charges
- Cheaper than purchasing a TeleMega

## Basic Manual

All wago ports are the same as the Telemega screw terminals, except for the addition of a connector to select between either an external switch, or the internal mag switch (you could use both!)

To enable the internal mag switch, bridge the terminals marked **MG**. A magnet's **N** pole perpendicular to the top surface magnetic sensor (under the square buzzer) arms the device. 

A fully armed device has the dual-LED next to the mag sensor fully lit (green and red). To begin the disarm process, expose the sensor to a **S** pole, then a **N** pole, and then an **S** pole. This will cycle the LED lights and prevents any disarm risk from launch towers that may generate magnetic fields.

If a device on the pad has a partially lit LED, the disarm process has started. It is recommended that a disarm and arming cycle be completed to reduce the risk of an inadvertent disarm; however, even in the started disarm process, the device is still just as, if not less, sensitive to magnetic fields as industry-standard altimeters.

To use an external switch, simply wire it in series with the terminals marked **SW**. 

If you want an external switch in parallel with the internal mag switch **(NOT RECOMMENDED)** - you can wire both a switch in series with the **SW** terminals, and bridge the **MG** terminals. 

If you want an external switch in series with the internal mag switch **(MAYBE ALRIGHT?)** - you can wire a switch in series with the **MG** terminals. 

To enable GPS operation, connect a standard L1 patch antenna to the rear UFL connector marked L1 GPS. This port supports both passive and active patch antennas. 

Companion and servo connector documentation TBD

See the `flashing.md` file for flashing info. 

## Production
All SMT components can be assembled via JLCPCB.

THT BOM is to be produced soon. 

Use the `jlcpcb/production_files` subdirectory for production files. I do not guarantee that everything will be in stock on JLC, nor do I guarantee that the rotations are correct (they are not, **CHECK VERY CAREFULLY!**).

Recommended configuration is **JLC04161H-7628**. I recommend ENIG, but it's likely not necessary. 

## Notes
Use at your own risk! This device has not been validated as of Dec 2025, and custom production from JLC always carries the risk of QC issues. 

You must provide your own patch antenna (preferably passive) to use the GPS

There are a few minor issues known with the v1.0 model: 

- When powered through the mag switch, there is a diode in the path for pyro power. This shouldn't be an issue in practice, but it is technically rated only for 1A continuous. The transient rating is more than enough at 50A, so it's not really a huge issue. There is also a slight voltage drop. Not an issue when the mag switch is bypassed or when a separate pyro battery is used.
- Theoretically, the vertical USB-C Connector is more fragile than a horizontal one - and also harder to solder with a hot air station. Take care when using heavy cables, and if you are concerned, epoxy the connector down.
- Due to a technical oversight, the separate pyro battery capability of the stock telemega, which is rated up to 30v, has to be derated to 12v on the Mega3080. Not sure how many people use a 12+v external pyro battery, but something to be aware of.

The v1.1 release has fixed the USB-C issue; however, the v1.1 device has not been produced yet. Use at your own risk.  

---

**Device Version Last Produced Officially:** v1.0

**Last Updated:** December 2025  

**Written by:** Kevin Zheng (Kev1n8088)
