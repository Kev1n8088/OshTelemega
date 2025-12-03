# Building TeleMega v7.0 Bootloader on Windows

This guide shows how to build the bootloader firmware for TeleMega v7.0 using Windows Subsystem for Linux (WSL). After the bootloader is installed once via ST-Link, all future firmware updates can be done through AltosUI over USB. 

This procedure should be identical for all other USB-flashed Altus Devices, just with a different ```src``` subdirectory - but this has not been tested by the author. 

## Prerequisites

- Windows 10/11 with WSL support
- ST-Link programmer (for initial bootloader flash only)
- Blank STM32L1 chip on TeleMega v7.0 board
- STM32 ST-Link Utility installed on Windows
- AltOS Software installed
- AltOS Source downloaded - from [here](https://altusmetrum.org/AltOS/)

## Step 1: Install WSL Ubuntu

Open PowerShell as Administrator and run:

```powershell
wsl --install -d Ubuntu-24.04
```

Restart your computer when prompted, then launch Ubuntu from the Start menu and create a user account.

## Step 2: Install Build Dependencies

In the Ubuntu terminal:

```bash
sudo apt update
sudo apt install -y gcc-arm-none-eabi build-essential autoconf automake \
    libtool picolibc-arm-none-eabi default-jdk libjansson-dev nickle
```

## Step 3: Copy Source to WSL

Copy the AltOS source to WSL's native filesystem (required to avoid NTFS permission issues):

```bash
cp -r /mnt/c/Users/<YourUsername>/Desktop/altos-1.9.22 ~/altos-1.9.22
cd ~/altos-1.9.22
```

Replace `<YourUsername>` with your Windows username.

## Step 4: Configure Build System

```bash
./configure --with-arm-cc=arm-none-eabi-gcc
```

This will check for all required dependencies and configure the build system.

## Step 5: Build the Bootloader

```bash
cd src/telemega-v7.0/flash-loader
make
```

The bootloader will be built as:
- `telemega-v7.0-altos-flash-1.9.22.elf` (ELF binary)
- `telemega-v7.0-altos-flash-1.9.22.hex` (Intel HEX format for ST-Link)

Verify the build succeeded:

```bash
ls -lh telemega-v7.0-altos-flash-1.9.22.*
```

You should see both `.elf` and `.hex` files (~4KB each).

## Step 6: Copy Bootloader to Windows

```bash
cp telemega-v7.0-altos-flash-1.9.22.hex /mnt/c/Users/<YourUsername>/Desktop/
```

## Step 7: Flash Bootloader with ST-Link

1. Connect ST-Link programmer to TeleMega v7.0 SWD interface:
   - **SWDIO** → SWDIO
   - **SWCLK** → SWCLK
   - **GND** → GND
   - **3.3V** → 3v3
   - Make sure the board is powered by a seperate power supply as the ST-Link Programmer does not provide power.

2. Open **STM32 ST-Link Utility** on Windows

3. Click **Target → Connect**

4. Click **Target → Program & Verify...**

5. Browse to `telemega-v7.0-altos-flash-1.9.22.hex` on your Desktop

6. Click **Start**

7. Wait for "Verification...OK" message

8. Disconnect ST-Link programmer

## Step 8: Flash Main Firmware via AltosUI

1. Connect TeleMega v7.0 to PC via USB

3. Launch **AltosUI**

4. Click **Flash Image**

5. Select serial port (e.g., COM13)

6. Browse for the Telemega 7.0 flight firmware in the AltosMetrum file directory - look for something named ```telemega-v7.0-1.9.22.ihx``` or similar

7. Click **OK** to flash

8. Wait for completion message

9. Device will reboot into main firmware

## Step 9: Initial Configuration

Conduct the standard initialization procedure in AltOS, including accelerometer calibration - this should clear the "Sensor failure" beeps. Add your callsign, configure deployments, and calibrate the radio (TBD)

## Memory Map Reference

- **Bootloader:** `0x08000000` - `0x08001000` (4KB)
- **Main Firmware:** `0x08001000` - `0x08020000` (124KB)

## Troubleshooting

### Build Errors

**"No JVM files found" during configure:**
- Java is required even though it's not used for bootloader
- Install: `sudo apt install default-jdk`

**Permission denied errors:**
- Ensure source is in `~/altos-1.9.22` (WSL filesystem)
- Don't build from `/mnt/c/` (NTFS mounts have permission issues)

**Missing picolibc:**
- Install: `sudo apt install picolibc-arm-none-eabi`

### Flashing Issues

**ST-Link cannot connect:**
- Check SWD wiring (SWDIO, SWCLK, GND)
- Verify board has power (LED should be on)
- Try "Connect under reset" option in ST-Link Utility

**Device not recognized by AltOS after bootloader flash:**
- Attempt a power cycle. 
- If not functional, do not panic - try to reflash the bootloader **OR:** 
- Follow the recovery procedure of connecting the Companion port CS0 (pin 6) to GND when powering the device on to force it into recovery mode.  

**COM port not visible:**
- Install Altus Metrum drivers (included with AltosUI)
- Check Device Manager for unrecognized devices
- Manually install `altusmetrum.inf` if needed

**Sensor failure beeps (dah dit dit dah):**
- Run accelerometer calibration in AltOS
- If not fixed, confirm all sensors operation in Serial terminal by opening the COM port and sending in the ```?``` command at 115200 baud for the commands required for querying sensors. Note that for some reason this COM port does not show up in the VSCode for some reason - it worked for on PuTTY for the author. 

## Future Firmware Updates

After the bootloader is installed, **ST-Link is no longer needed**. All future updates can be done via USB:

1. Connect device via USB
2. Launch AltosUI
3. Click **Flash Image**
4. Select your device and new firmware file
5. Flash completes automatically

## Notes

- The bootloader only needs to be flashed **once per board**
- Standard `telemega-v7.0` firmware is for normal flight operations
- `telemega-v7.0-seeed` variant is for hardware validation only (requires GPS lock in 10 seconds)
- Always calibrate accelerometer before first flight
- Firmware version 1.9.22 includes Mosaic X5 GPS support (configurable via `c g` command)

## Additional Resources

- AltOS Documentation: https://altusmetrum.org/AltOS/doc/
- Source Repository: https://altusmetrum.org/AltOS/

---

**Version:** 1.9.22  
**Last Updated:** December 2025  
**Target Hardware:** TeleMega v7.0 (STM32L1 series)

**Written by:** Kevin Zheng (Kev1n8088)
