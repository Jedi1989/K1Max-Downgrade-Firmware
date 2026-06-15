# K1Max-Downgrade-Firmware
This repository provides a reliable, automated SSH script to downgrade the Creality K1 Max. By rolling back to the CR4CU220812S11_ota_img_V1.3.3.29 base image using a USB drive, it establishes a safe and clean environment for subsequent firmware updates and custom Klipper integrations.


# K1Max Downgrade Firmware

Automated guide and workaround to safely downgrade the Creality K1 Max firmware to the base V1.3.3.29 version via USB drive. This method bypasses the native Creality version check, allowing a clean downgrade environment to resolve update conflicts or prepare the machine for custom Klipper integrations.

**License:** MIT License

## ⚠️ Disclaimer
Use this guide at your own risk. Downgrading firmware carries inherent risks. The authors are not responsible for any damage or data loss that may occur. This software is provided "AS IS", without warranty of any kind.

## Prerequisites
* A USB drive formatted in **FAT32** (Allocation unit size: 4096 bytes is recommended).
* A computer (Windows/Mac/Linux) on the same Wi-Fi network as the printer.
* The base firmware file: `CR4CU220812S11_ota_img_V1.3.3.29.img`.
* Your target firmware file (e.g., V1.3.3.34).

---

## Step-by-Step Guide

### Step 1: Enable Root Access on the Printer
Before modifying any system files, you must enable root access directly from the K1 Max screen.
1. On the printer's screen, go to **Settings** (gear icon) -> **Network**.
2. Scroll down and tap on **Details** (or Network Details).
3. Look for the **Root Account** option and enable it.
4. Accept the disclaimer. A password will be displayed on the screen. **Save this password**.


### Step 2: Prepare the USB Drive
1. Download the base firmware (`CR4CU220812S11_ota_img_V1.3.3.29.img`) and copy it to the root directory of your USB drive. Do not rename the file.
2. Insert the USB drive into the USB port on the front of your K1 Max.

### Step 3: Connect via SSH (Windows)
Open your Command Prompt (CMD) or Windows Terminal and connect to the printer using its IP address:

```bash
ssh root@xxx.xxx.x.xx

(Replace xxx.xxx.x.xx with your printer's actual IP address).
When prompted, type yes to accept the fingerprint, and then enter the password you got from the printer screen.


Step 4: The Version Check Bypass (The Trick)
By default, the Creality system blocks downgrades with an ota: stoped version_is_new error because the system partition is Read-Only. We will copy the updater script to a writable folder, disable the version check, and run it.

Run the following commands one by one in your SSH terminal:

1. Copy the updater script to the user data folder:

```bash
cp /etc/ota_bin/local_ota_update.sh /usr/data/bypass_update.sh

2. Comment out the version check logic (Lines 365 to 381) using sed:

```bash
sed -i '365,381 s/^/#/' /usr/data/bypass_update.sh


Step 5: Execute the Downgrade
Now, run the modified script, pointing it to the firmware file on your USB drive (usually located inside /tmp/udisk/sda1/).

Run this command:

```bash
sh /usr/data/bypass_update.sh /tmp/udisk/sda*/CR4CU220812S11_ota_img_V1.3.3.29.img

(Note: We use sda* because the USB mount point can sometimes be sda1, sdb1, etc.)

Wait for the process to complete. You will see several lines processing data. It is finished when you see ota: stoped success.


Step 6: Target Firmware Clean Install
Once the script finishes successfully:

Power off the K1 Max using the switch on the back of the machine.

Remove the USB drive from the printer and plug it back into your computer.

Delete the base firmware (V1.3.3.29.img) from the USB drive.

Copy your target firmware file (e.g., CR4CU220812S11_ota_img_V1.3.3.36.img) to the root of the USB drive.

With the printer still turned off, insert the USB drive.

Power on the printer. The system will detect the firmware on boot and automatically start the clean installation process.

Enjoy your clean firmware environment!

