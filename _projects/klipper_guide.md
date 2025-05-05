---
title: Ultimate Guide for Klipper Installation on Ender 3 V3 SE
date: 2024-05-20
last_modified_at: 2025-05-02
collection: projects
categories: 3dprinting
header:
  image: /assets/images/klipper_guide/ender3.jpeg
  teaser: /assets/images/klipper_guide/ender3.jpeg
---

<script type='text/javascript' src='https://storage.ko-fi.com/cdn/widget/Widget_2.js'></script><script type='text/javascript'>kofiwidget2.init('Help keep the site ad-free', '#41b5eb', 'B0B6YCPC6');kofiwidget2.draw();</script>

# Overview of the project

This project consists in changing the default Marlin software that comes with the Ender 3 V3 SE from factory to Klipper. There are several benefits to running Klipper apart from the huge support you can find online, for example:

- Send gcode directly from your PC instead of manual SD card transfer
- Remote monitoring and command sending
- Web interface you can access from any device in your home
- Improved bed leveling with [KAMP](https://github.com/kyleisah/Klipper-Adaptive-Meshing-Purging)
- Pressure advance and input shaping

# Requirements

- An Ender 3 V3 SE
- A Raspberry Pi (or PC) running Linux
- microSD card for the Pi
- Power Supply for the Pi
- SD card
- USB C to USB A cable to connect the Ender and the Pi

**Disclaimer:** The Ender 3 V3 SE display doesn't work with Klipper (yet) so you would lose the ability to operate your printer from the display knob, however there are some solutions like [this repo](https://github.com/jpcurti/E3V3SE_display_klipper) to restore some of the display's functionality
{: .notice--warning}

# Installation

## Raspberry Pi Setup

The first step would be to have a working Raspberry Pi (or PC) to which you can SSH into. If you already do, you can skip this step. If you don't, there is [a great guide](https://www.raspberrypi.com/documentation/computers/getting-started.html) by the Raspberry Pi team on how to set it up.

The steps without going into much detail are:

1. Install Raspberry Pi Imager
2. Choose your device (which Raspberry Pi do you have)
3. Choose the operating system (I recommend the lite version of Raspberry Pi OS)
4. Choose your storage device (the microSD which you will install in your Raspberry Pi)
5. Customize OS to generate a username and password, add Wi-Fi credentials, set hostname and enable SSH
6. Flash the microSD
7. Plug the microSD into your Raspberry Pi
8. Connect the power supply
9. SSH into your Raspberry Pi

## KIAUH

Once your Raspberry Pi is set up and you are SSH'd into it, you can proceed to download and install [KIAUH](https://github.com/dw-0/kiauh) which is a script that provides an easy way of installing Klipper and other features that we will need like Moonraker, Fluidd or Mainsail.

### Steps to Install KIAUH

A prerequisite to installing KIAUH is to have Git installed. If you don't have it installed already (or if unsure) then run the following command:

```bash
sudo apt-get update && sudo apt-get install git -y
```

Once git is installed, run the following command to clone KIAUH to your home directory:

```bash
cd ~ && git clone https://github.com/dw-0/kiauh.git
```

After successfully cloning, run KIAUH by running the following command:

```bash
./kiauh/kiauh.sh
```

## Installing Klipper and Necessary Packages

You should now see a similar screen to the one shown below, which is the main menu of KIAUH, from where we will install the necessary packages to run Klipper.
{% include figure image_path="/assets/images/klipper_guide/KG_01_KIAUH_main_menu.png" alt="screenshot of KIAUH main menu" caption="KIAUH main menu" %}
The necessary packages to install will be:

- Klipper
- Moonraker
- Fluidd or Mainsail

### Installing Klipper

To install each package we have to type: 1 (for Install) and hit Enter which takes us to the following screen:
{% include figure image_path="/assets/images/klipper_guide/KG_02_KIAUH_install_menu.png" alt="screenshot of KIAUH install menu" caption="KIAUH install menu" %}
Now we have to type: 1 (for Klipper) and hit Enter again. Then follow any prompts that appear to complete the installation.

### Installing Moonraker

To install Moonraker we have to repeat the steps from above but selecting Moonraker in the second screen. The steps would be:

1. Run KIAUH or go to Main Menu if already running
2. Type "1" (without quotation marks) and hit Enter
3. Type "2" (without quotation marks) and hit Enter
4. Follow the prompts to install

### Installing Fluidd or Mainsail

[Fluidd](https://docs.fluidd.xyz/) and [Mainsail](https://docs.mainsail.xyz/) are user interfaces for Klipper, they are the ones we will interact with to send commands to our printer. Selecting one is completely up to personal preference so you can visit their sites and see which one you like the most, their functionality is pretty much the same.

To install one of them we have to repeat the steps from above but selecting our preference in the second screen. The steps would be:

1. Run KIAUH or go to Main Menu if already running
2. Type "1" (without quotation marks) and hit Enter
3. Type "3" OR "4" (without quotation marks) and hit Enter
4. Follow the prompts to install

# Configuration

Once the steps above are complete, you should be able to access your web interface by going to `http://[hostname-of-your-pi].local` and see something similar to this:
{% include figure image_path="/assets/images/klipper_guide/KG_03_Fluidd_home_menu.png" alt="screenshot of Fluidd home menu" caption="Fluidd home menu" %}
Next you need to go to the Configuration tab (below image) and you should see a `printer.cfg` file and a `macro.cfg` file. If those files are nowhere to be seen, you can create them by clicking on the '+' button on top of the file list.
{% include figure image_path="/assets/images/klipper_guide/KG_04_config_menu.png" alt="screenshot of Fluidd config menu" caption="Fluidd config menu" %}

Then go to one of the many Github Repos to get a configuration file that is pre-set. I recommend [bootuz-dinamon](https://github.com/bootuz-dinamon/ender3-v3-se-full-klipper). Copy and paste the contents of `printer.cfg` and `macro.cfg` into yours.

**2025 Update:** I recommend [shubham0x13's](https://github.com/shubham0x13/ender-3-v3-se-klipper) config file that has several upgrades compared to bootuz's config file. Copy and paste the contents of `printer.cfg` and `macro.cfg` into yours.
{: .notice--warning}

## Create Printer Firmware (.bin file)

Make sure your SD card is formatted as FAT32 with an allocation size of 4096, otherwise you might run into issues and be unable to flash
{: .notice--warning}

**2025 Update thanks to @derlaft:** In the printer screen, check your "hardware version". If it's "CR4NS200320C14" you have the new motherboard version. Keep this in mind for step 2:
{: .notice--warning}

To create the actual firmware that will be running in your 3D printer, you have to compile the firmware on your Rasberry Pi. For this, you need to follow the following steps:

1. Go to the `/klipper` directory and run the menuconfig command by running the following code:

   ```bash
   cd ~/klipper/
   make menuconfig
   ```

2. Select the following settings depending on your motherboard:

   ```
   Original motherboard:
   - Micro-controller architecture: STMicroelectronics STM32
   - Processor model: STM32F103
   - Bootloader offset: 28KiB
   - Communication interface: Serial (on USART1 PA10/PA09)

   New motherboard:
   - Micro-controller architecture: STMicroelectronics STM32
   - Processor model: STM32F401
   - Bootloader offset: 64KiB
   - Communication interface: Serial (on USART1 PA10/PA9)
   ```

3. After everything is selected, press q and save your changes, then run:

   ```bash
   make
   ```

## Flash 3D Printer

Once completed, there will be a `klipper.bin` file in the `klipper/out/` folder. To get the file you will have to copy it from your Raspberry Pi to your computer, but first we have to move it from where it is generated to a folder Fluidd/Mainsail can access.

To move the file, SSH into your Raspberry Pi and navigate to the `klipper/out/` folder by running the following command:

```bash
cd ~/klipper/out
```

Then run the `ls` command to confirm the file is there and if it is, you can send it to your `config` folder so it is visible from Fluidd/Mainsail by running the following command

```bash
cp klipper.bin ~/printer_data/config/
```

> Note: If you get an error that says "No such file or directory" you need to verify that the previous steps (compiling the firmware) were done correctly and look for the location of `klipper/out/` or `printer_data/config/` in your Raspberry Pi, then modify the previous commands accordingly.

Now when you go to the configuration tab of your Fluid/Mainsail UI, the `klipper.bin` file should be there so you can just right click and download to your computer. Once you download the file, the next steps are:

If you have the new motherboard version: create a directory named `STM32F4_UPDATE` in your SD card and move the `klipper.bin` file there, then go to step 2. If you have the old motherboard then proceed as per below steps.
{: .notice--warning}

1. Transfer the `klipper.bin` file to your printer's SD card
2. Turn off the printer
3. Insert SD card
4. Turn on the printer and wait 15 seconds
5. Turn off the printer
6. Take the SD card out and remove the .bin file
7. Insert the empty SD card into the printer
8. Turn on the printer (the screen should be a blue screensaver image)
9. Connect your Raspberry Pi to the printer via USB cable
10. Navigate to your Fluidd or Mainsail web interface

The printer should be there and you should be looking at something like this:
{% include figure image_path="/assets/images/klipper_guide/KG_05_Fluidd_home_menu.png" alt="screenshot of Fluidd home menu" caption="Fluidd home menu" %}

In case the printer is not recognized, SSH into your Raspberry Pi and run the following command:

```bash
dmesg | grep usb
```

which after running should show something like this:
{% include figure image_path="/assets/images/klipper_guide/KG_06_terminal_usb.png" alt="screenshot of a terminal" caption="The line you should look for" %}
Then navigate to your printer's `printer.cfg` file and look for the `[mcu]` code block. There should be 2 lines under it that start with "serial:". Comment the long one by adding "#" to the start (without quotation marks) and undocument the short one, changing the value to whatever you got when running the previous command.

Now you should have a working Ender 3 V3 SE with Klipper installed, congratulations! Now you can continue to the calibration section to get your printer calibrated and start printing.

# Calibration

## Calibrate PID Coefficients

Go to your main Fluidd or Mainsail web interface and scroll down to "Macros". Then run the `PID_BED` and `PID_EXTRUDER`. Run one first and after it finishes, run the other.
{% include figure image_path="/assets/images/klipper_guide/KG_07_macros.png" alt="screenshot of the macros box" caption="Macros box" %}

> Note: If macros don't appear in the home screen or if they don't run, go to `printer.cfg` and add `[include macro.cfg]` somewhere in the file

## Calibrate Z-Offset

To calibrate your Z-Offset go to the Tune tab on your left and click on "Calibrate" to generate a mesh matrix of your bed.
{% include figure image_path="/assets/images/klipper_guide/KG_08_bed_mesh.png" alt="screenshot of bed mesh menu" caption="Bed mesh menu" %}
After it is generated, go to the Home tab and go to the Tool box and select `PROBE_CALIBRATE` under the "Tools" drop-down menu.
{% include figure image_path="/assets/images/klipper_guide/KG_09_tool.png" alt="screenshot of tool box" caption="Tool box" %}
Get a piece of paper and run the [paper test](https://www.klipper3d.org/Bed_Level.html#the-paper-test) to calibrate the correct Z-offset for your printer, adjust the values by clicking on the '+' or '-' buttons that appear. You should be able to move the paper but there should be some drag present. I have had better results doing this when the printer is cold but some recommend doing it when the nozzle and the bed are at operating temperature, give it a try and see which one works best for you.
{% include figure image_path="/assets/images/klipper_guide/KG_10_probe.png" alt="screenshot of manual probe menu" caption="Manual probe menu" %}

# Additional Installations and Configurations

## KAMP

I highly recommend installing Klipper Adaptive Meshing & Purging ([KAMP](https://github.com/kyleisah/Klipper-Adaptive-Meshing-Purging)) to get the full benefits of improved bed leveling and adaptive purging. The installation steps on their site are pretty straightforward but I'll provide them here as well.

First you need to define `[exclude_object]` in your `printer.cfg` file.

1. Go to your `printer.cfg` file and add a line that says `[exclude_object]`
   {% include figure image_path="/assets/images/klipper_guide/KG_11_exclude_obj.png" alt="screenshot of exclude object module" caption="Exclude object module" %}

2. Go to your `moonraker.conf` file and add 2 lines

   ```
   [file_manager]
   enable_object_processing: True
   ```

3. Make sure to go to your slicer and enable the "Label Objects" option. I use Cura and it labels objects by default.

   > (The next steps are copied from the [KAMP Github](https://github.com/kyleisah/Klipper-Adaptive-Meshing-Purging?tab=readme-ov-file#installation))

4. SSH into your Raspberry Pi and execute the following commands:

   ```bash
    cd

    git clone https://github.com/kyleisah/Klipper-Adaptive-Meshing-Purging.git

    ln -s ~/Klipper-Adaptive-Meshing-Purging/Configuration printer_data/config/KAMP

    cp ~/Klipper-Adaptive-Meshing-Purging/Configuration/KAMP_Settings.cfg ~/printer_data/config/KAMP_Settings.cfg
   ```

   > **Note:** This will change to the home directory, clone the KAMP repo, create a symbolic link of the repo to your printer's config folder, and create a copy of `KAMP_Settings.cfg` in your config directory, ready to edit.
   >
   > It is also possible that with older setups of klipper or moonraker that your config path will be different. Be sure to use the correct config path for your machine when making the symbolic link, and when copying `KAMP_Settings.cfg` to your config directory.

5. Open your `moonraker.conf` file and add this configuration:

   ```
   [update_manager Klipper-Adaptive-Meshing-Purging]
   type: git_repo
   channel: dev
   path: ~/Klipper-Adaptive-Meshing-Purging
   origin: https://github.com/kyleisah/Klipper-Adaptive-Meshing-Purging.git
   managed_services: klipper
   primary_branch: main
   ```

   > **Note:** Whenever Moonraker configurations are changed, it must be restarted for changes to take effect. If you do not want moonraker to notify you of future updates to KAMP, feel free to skip this.

6. Depending on what features you want from KAMP, you'll need to `[include]` some files in `KAMP_Settings.cfg`:

   ![](https://github.com/kyleisah/Klipper-Adaptive-Meshing-Purging/raw/main/Photos/Include-Tutorial.gif)

   > **Note:** The KAMP configuration files are broken up like this to allow those who do not use bed probes to benefit from adaptive purging, and other features.

7. After you `[include]` the features you want, be sure to restart your firmware so those inclusions take effect. Don't forget to add `[include KAMP_Settings.cfg]` to your `printer.cfg`!

## Axis Twist Compensation

I've seen that a lot of people seem to have issues with bed leveling even after they have gone through all the possible solutions for it. The issue I found is that most Ender 3 V3 SE printers have a little twist on their X rail which skews the results of their bed level. [This video](https://www.youtube.com/watch?v=qju5RECjVUM) by Nero3D explains the issue in pretty good detail.
I recommend to enable the [Axis Twist Compensation](https://www.klipper3d.org/Axis_Twist_Compensation.html) module and set it up to get your printer running smoothly.

## Calibrate Pressure Advance

Pressure Advance is another great way to improve your prints, I recommend to follow the Klipper documentation on [Pressure Advance](https://www.klipper3d.org/Pressure_Advance.html) to tune yours correctly.

# Final thoughts

I hope you found this guide useful and that you were able to get your Ender 3 V3 SE up and running with Klipper. I also want to recognize the guides and videos I followed to initially set up mine:

- [arismelachroinos](https://www.reddit.com/user/arismelachroinos/) amazing [reddit post](https://www.reddit.com/r/Ender3V3SE/comments/17ijuu9/klipper_on_the_ender_3_v3_se/)
- [BootUse UA video](https://www.youtube.com/watch?v=LrBiwabN-Y8) (Ukranian with english subs)
- pblvsky's Ender 3 V3 SE's [guide](https://pblvsky.gitbook.io/ender3v3se/remote-control/klipper)

If you run into any issues while going through this guide, don't hesitate to reach out and I'll do my best to help you. Happy printing!

<script type='text/javascript' src='https://storage.ko-fi.com/cdn/widget/Widget_2.js'></script><script type='text/javascript'>kofiwidget2.init('Help keep the site ad-free', '#41b5eb', 'B0B6YCPC6');kofiwidget2.draw();</script>
