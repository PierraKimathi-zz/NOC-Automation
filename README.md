![corsica](https://raw.github.com/mozilla/corsica/master/static/corsica.png)

![build status: gold](https://travis-ci.org/mozilla/corsica.png)

# Introduction to Raspberry Pi and Mozilla Corsica.

## Introduction

## Linux Setup

 To use the Raspberry Pi, you will first need to download:

**Raspbian Stretch Lite** i.e. Debian-based operating system and **Etcher** to write Raspbian to the SD Card.

## Raspberry Pi Setup

### Login

Login to the Pi using the following credentials:
User: pi
Password: raspberry

### Wifi Setup

The Raspberry Pi 3 comes with an inbuilt wifi chip so to configure it to connect to your office/home wifi, proceed with the following commands:

```
    sudo nano /etc/network/interfaces
```

- Edit the `interfaces` file and add your WLAN details i.e. the wifi name (`wpa-ssid`) and password (`wpa-psk`):

  ```
     auto lo
     iface lo inet loopback
     iface eth0 inet dhcp
  
     allow-hotplug wlan0
     auto wlan0
  
     iface wlan0 inet dhcp
             wpa-ssid "ssid"
             wpa-psk "password"
  ```

- Reboot the raspberry pi:
  `sudo reboot`

- Check if the Pi has connected to your WLAN by running `sudo ifconfig`. The wireless interface should have an assigned IP address.

### Enabling SSH On The Raspberry Pi

`sudo raspi-config`



select  **Interfacing Options**

Navigate to and select **SSH**

Choose  **Yes**

Select **Ok**

Choose **Finish**   

### Mozilla Corsica Setup

This section is a guide on how to install Mozilla Corsica on the Raspberry Pi. **Corsica** is an extensible digital signage solution that consists of a server and display clients. The client machines will run on M$

### Install Node JS

Corsica uses `nodejs` which assists in running server-side Javascript and `npm` i.e. the node package manger for `nodejs`.

- Check the version of the processor: `uname -m`. For **armv7** and later, type:

     `curl -sL https://deb.nodesource.com/setup_9.x | sudo -E bash -`

This will update the Debian `apt` package to include *NodeSource* packages.

- To install Nodejs:

     `sudo apt install -y nodejs`

- Check the node version after installation using `node -v` command to show what version of node is running.

- Perform a quick calculation to confirm successful installation:

    `$node
  ​    16/2
  ​    8`

- Press Ctrl+C twice to Exit

### Install Corsica

1. Clone Corsica from the Gihub repository to home directory of the pi user i.e.

   ```
   git clone https://github.com/mozilla/corsica.git
   ```

2. Change to **corsica** directory (`cd corsica`) and install node modules i.e. `npm install`.

3. Then run `npm start`

4. The application will run at the address `0.0.0.0:8080`

5. Add **state.json**

6. Add links to **state.json**

### Create systemd service file

 `/etc/systemd/system/brck_tv_signage.service`

```
    [Unit]
    Description=BRCK TV Signage Service
    After=network.target

    [Service]
    ExecStart=/usr/bin/node index.js
    WorkingDirectory=/home/pi/brck_tv_signage
    StandardOutput=inherit
    StandardError=inherit
    Restart=always
        User=pi

        [Install]
        WantedBy=multi-user.target
       

```

```
     sudo systemctl start brck_tv_signage
```

### Check if the service is running

```
    * `sudo systemctl status brck_tv_signage`
```

### Configuring Raspberry Pi To Boot In ``Kiosk`` Mode

1. Install Unclutter (hides the cursor from the sreen)

      `sudo apt-get install unclutter`

2. When the GUI starts up chromium needs to boot in kiosk-mode and open the webpage. Because of that we edit the autostart file:

```
    nano ~/.config/lxsession/LXDE-pi/autostart
```

Type the following into the autostart file:

```
	 bash
    @lxpanel --profile LXDE-pi
    @pcmanfm --desktop --profile LXDE-pi
    @xset s off
    @xset -dpms
    @xset s noblank
    @sed -i 's/"exited_cleanly": false/"exited_cleanly": true/' ~/.config/chromium-browser Default/Preferences
    @chromium-browser --noerrdialogs --kiosk [URL] --incognito --disable-translate

```

![Signage Setup](https://github.com/brck/operations-utils/utilities/tv_signage/images/Brck Signage Setup.png)

![Signage Server](https://github.com/brck/operations-utils/utilities/tv_signage/images/Brck Signage Server.png)
