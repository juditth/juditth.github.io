# [juditth.github.io](https://juditth.github.io/)

## Menu

\| [Home](https://juditth.github.io/)
\| [Install dockar to Raspberry Pi B from scratch](https://juditth.github.io/raspberryPiB)
\| 

## Install webserver to Raspberry Pi B

### Hardware

For the installation i used:
* computer to install and turn on all packages, functions and images needed for Pi
* SD card (32 GB)
* Raspberry RPi 3 Model B as a server. The specification you can see in [Wiki: Raspberry RPi 3 Model B](https://en.wikipedia.org/wiki/Raspberry_Pi#Specifications)

| Parameter | Value |
| --- | --- |
| Release date | February 2016 |
| Instruction set | ARMv8-A (64/32-bit) |
| SoC | Broadcom BCM2837 |
| FPU | VFPv4 + NEON |
| CPU | 4× Cortex-A53 1.2 GHz |
| GPU | Broadcom VideoCore IV @ 250 MHz (BCM2837: 3D part of GPU @ 300 MHz, video part of GPU @ 400 MHz) <br> OpenGL ES 2.0 (BCM2835, BCM2836: 24 GFLOPS / BCM2837: 28.8 GFLOPS) <br> MPEG-2 and VC-1 (with license), 1080p30 H.264/MPEG-4 AVC high-profile decoder and encoder (BCM2837: 1080p60) |
| Memory | 1 GB (shared with GPU) |
| USB | 4 (via on-board 5-port USB hub) |
| Video in | 15-pin MIPI camera interface (CSI) connector, used with the Raspberry Pi camera or Raspberry Pi NoIR camera |
| Video out | HDMI (rev 1.3), <br> composite video (3.5 mm TRRS jack), <br> MIPI display interface (DSI) for raw LCD panels |
| Audio | Analog via 3.5 mm phone jack, <br> Digital via HDMI and, as of revision 2 boards |
| Storage | MicroSDHC slot, <br> USB Boot Mode |
| Network | 10/100 Mbit/s Ethernet <br> 802.11b/g/n single band 2.4 GHz wireless, <br> Bluetooth 4.1 BLE |
| Power | 300 mA (1.5 W) average when idle, <br> 1.34 A (6.7 W) maximum under stress (monitor, keyboard, mouse and WiFi connected) |



### Installing Raspbian

Instalation Raspbian on Pi is very simple. You'll just the formatted SD card and Pi. 

According the instructions in
[thepi.io:how-to-install-raspbian-on-the-raspberry-pi](https://thepi.io/how-to-install-raspbian-on-the-raspberry-pi/) I downloaded latest version of Raspbian Stretch lite from  [raspberrypi.org:downloads/raspbian](https://www.raspberrypi.org/downloads/raspbian/) and unzipped it. By using [Win32 Disk Imager](https://sourceforge.net/projects/win32diskimager/) I wrote the image to the MicroSD card. 

When done, just put the card into Pi. That's all for the installation of OS to Pi :-)
 



### Turn on SSH

To be able to communicate with Pi it's needed to turn on the SSH. After few minutes of googling I found out it's even simplier than instalation of OS itself. 

By help from [howchoo.com:how-to-enable-ssh-on-raspbian-without-a-screen](https://howchoo.com/g/ote0ywmzywj/how-to-enable-ssh-on-raspbian-without-a-screen) just create empty file named `ssh` (yes, without ending) to the card (to the root folder). Then put the card to the Pi and SSH is working :-)

It's only needed to find out what's its IP address, you can find it e.g. in the router settings. My IP address was 192.168.1.5.

To log in into Pi just use SSH, defaults for first login is username `pi` and password `raspberry`. It's recommended to change it of course. 

`ssh pi@192.168.1.1 -p 22`

I also find interesting information-rich log with many commands and experiences, it might help in the future with some installation also. [downloads.raspberrypi.org:raspbian/release_notes.txt](http://downloads.raspberrypi.org/raspbian/release_notes.txt) - for this purpose just search for "SSH disabled by default".




### Turn on the Wifi

Next step is turn on the Wifi on Pi. Actually many information is on the RaspberryPi web itself including manual how to turn on the wifi on Pi: [raspberrypi.org:configuration/wireless/wireless-cli.md](https://www.raspberrypi.org/documentation/configuration/wireless/wireless-cli.md)

So log in via SSH to Pi and write `sudo raspi-config`. Terminal window will open with many setting available. Just pick the "2. Network options", in next step "N2. Wifi", but the country code (if necessary), enter the SID and password to the network and it's done.

In my case I needed to update to network record because of hidden network. Type `sudo nano /etc/wpa_supplicant/wpa_supplicant.conf` and add "scan_ssid=1" line to the record:
```
network={
    ssid="your-hidden-SSID"
    scan_ssid=1
    psk="Your-wifi-password"
}
```

Just note - changes done by nano editor can by saved by Ctrl+O (save), Enter (confirm) and Ctrl+X (Close).

For sure reboot the Pi by `sudo reboot`. You can check the the PI address by `ifconfig`:
```
wlan0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.168.1.8  netmask 255.255.255.0  broadcast 192.168.1.255
        ...
```



## Install Docker

For now we have everything prepared on Pi to start with software installation. I used Docker for this purpose.

There is [many ways](https://www.binarytides.com/linux-command-check-disk-partitions/) how to check if there is enough space left for the installation. My card was empty in the beginning so I didn't expect any problem with storage.

For sure update to packages installed on Pi already by `sudo apt-get update`.

On [medium.freecodecamp.org:the-easy-way-to-set-up-docker-on-a-raspberry-pi](https://medium.freecodecamp.org/the-easy-way-to-set-up-docker-on-a-raspberry-pi-7d24ced073ef) you can find wery easy manual how to install Docker on Pi. 

1) Save script from [get.docker.com](https://get.docker.com/) to pi (I choosed folder `/home/pi/_installation/get-docker.sh`)
2) Run script 
```
curl -fsSL get.docker.com -o get-docker.sh && sh /home/pi/_installation/get-docker.sh
```

At the end of installation you can see which Docker versin was installed to your Pi:
```
Client:
 Version:           18.09.0
 API version:       1.39
 Go version:        go1.10.4
 Git commit:        4d60db4
 Built:             Wed Nov  7 00:57:21 2018
 OS/Arch:           linux/arm
 Experimental:      false

Server: Docker Engine - Community
 Engine:
  Version:          18.09.0
  API version:      1.39 (minimum version 1.12)
  Go version:       go1.10.4
  Git commit:       4d60db4
  Built:            Wed Nov  7 00:17:57 2018
  OS/Arch:          linux/arm
  Experimental:     false
```

You can check the Docker version anytime by `docker -v`
```
Docker version 18.09.0, build 4d60db4
```

Add "pi" user to group "docker" to be access the be able to use docker: `sudo usermod -aG docker pi`

And reboot Pi: `sudo reboot`



#### Install Adminer

The site [hub.docker.com](https://hub.docker.com/) is amazing source of all software available for docker with their various versions. I installed latest version of Adminer to Pi as first application. 

By command `docker images` you can see the empty list of images running in Docker. So let's fill it with Adminer. 
```
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
```

To install and run Adminer package just run `docker run adminer --name adminer`. If Adminer is not present in Docker (like in our case), Docker will install it too. 

When installation is done it's needed to map ports of Adminer out of Pi to be able to see the Adminer console in browser. This can be done by `pi@raspberrypi:/home $ docker run -p "8080:8080" adminer`.


Now you can call `http://192.168.1.8:8080/` in the browser and you'll see the Adminer console. 

Also in `docker images` list you'll see the running adminer. 
```
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
adminer             latest              9a076f5aa5ec        3 weeks ago         76.4MB
```

The Adminer process itself you can see in the list of docker processes by calling `docker ps`. You can also see the mapped ports here:
```
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                    NAMES
27c666d5852a        adminer             "entrypoint.sh docke…"   17 seconds ago      Up 15 seconds       0.0.0.0:8080->8080/tcp   adminer
```

Adminer is succesfully running in Docker!




#### PostgreSQl

Adminer is running, but it's not able to connecto to any database yet. So let's install Postgre and setup it. 

Cool news is there exists alpine. By this I don't mean the outdoor fashion brand, but [alpine-linux.org](https://alpinelinux.org/) - small version of original software. In the list of installation on [hub.docker.com](https://hub.docker.com/) page you can see also alpine version. They're ideal for Docker beucase of their size.

So let's ïnstall the alpine version of postgress database> `pi@raspberrypi:/home $ docker pull postgres:11-alpine`. By part ":11-alpine" you can specify concrete version of the application, in this case we'll install 11-alpine.

During installation you'll see something like this:
```
11-alpine: Pulling from library/postgres
05276f4299f2: Already exists
5657e63df536: Already exists
d8c77ba51bee: Pull complete
f0a6f03aaddc: Pull complete
f9e13e44d986: Extracting [=================================================> ]  23.86MB/24.31MB
6db40a5b7ccb: Download complete
3cdeed8b3991: Download complete
8e31c5ecbd7f: Download complete
20204d7544c1: Waiting
a732c333b1ea: Waiting
```

When installation is done we have to run postgre. You can do it by `docker run --name postgre-test -e POSTGRES_PASSWORD=mysecretpassword -d postgres:11-alpine`

Explanation:
```
$ docker run                            # run the application
--name postgre-test                     # specify the container of the image to be able to find it in the list. This will be also the database name
-e POSTGRES_PASSWORD=mysecretpassword   # -e will be env. variable in docker, here with password
-d                                      # -d to run it in background
postgres                                # which application (image, instance) is running (from hub.docker.com list)
```


##### Map Adminer to Postgre

First it's needed to running adminer: `docker stop adminer`

To allow Adminer access it's needed to map these applications together by `docker run --name adminer --link postgre-test:postgre-test -p "8080:8080" -d adminer`

Explanation:
```
$ docker run                    # run the application
--name adminer                  # container name 
--link postgre-test:postgres    # link to postgre-test container (instance) of postgres image 
-p "8080:8080"                  # -p port mapping outside:inside ports
-d                              # -d to run it in background
adminer                         # which application (image, instance) is running (from hub.docker.com list) 
```



## Tags

[RaspberryPiB](https://juditth.github.io/raspberry-pi-b)