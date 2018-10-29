# How-To: Raspberry Pi connect with linux system via ssh, headless (without dedicated display unit)
To connect a raspberry pi with your linux system via ssh (and use it even if you don't have a dedicated display unit for raspberry pi)

The steps are given here on [how to ssh your raspi](https://www.raspberrypi.org/documentation/remote-access/ssh/) . Also [this link is more specific to linux system, for the same](https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md).

#### Step-by-step guide
1. What you need:
    a. Raspberry Pi (say raspi from here)
    b. Ethernet cable to connect to your linux system
    c. Linux system - say ubuntu (should work similarly with other linux OS as well) machine from here
2. Make sure you Enable SSH in raspi first (you might need a graphical display for this step - this step is enough if done once):
    a. Launch _Raspberry Pi Configuration_ from the _Preferences_ menu in the raspi machine.
    b. Navigate to the _Interfaces_ tab
    c. Select _Enabled_ next to _SSH_
    d. Click _OK_.
3. Connect ubuntu machine and raspi via ethernet cable.
4. On Ubuntu machine:
    a. Goto : `Network Connections > Ethernet (wired connection name) > Settings for this wired connection`
    b. Goto : `Ipv4 Settings Tab`
    c. Select : `Method` as `Shared to other computers`.
5. On Ubuntu machine terminal :
    a. Use one of the [method given here to find the IP address of your raspi](https://www.raspberrypi.org/documentation/remote-access/ip-address.md). Usually the first method - **ping raspberrypi.local** - should be enough and easy. This should give you the raspi IP.
    b. Now type `ssh pi@<raspi_IP>` , type password when prompted for. By default the password is **raspberry**. Follow [this article to configure your Pi to allow your computer to access it without providing a password each time you try to connect](https://www.raspberrypi.org/documentation/remote-access/ssh/passwordless.md). Basic idea for password less ssh is:
        - Generate a public key in your host system with `ssh-keygen`.
        - Copy the public key to your raspberry pi using `ssh-copy-id <USERNAME>@<RASPI-IP-ADDRESS>`.
6. Now you can do operations for example - [copy file(s) to and from your raspi](https://www.raspberrypi.org/documentation/remote-access/ssh/scp.md).
    a. Remember as seen in the above link you should specify absolute raspi path e.g. **scp source pi@<raspi_IP>:/home/pi/to/your/copy/ destination** , for **scp**.
    b. Enjoy!
7. To disconnect and shutdown your raspi:
    a. type sudo `shutdown -h now` in ubuntu terminal session running the raspberryPi ssh.

#### Note!
When you have to disconnect yourself from raspi and connect ubuntu machine to Internet using Ethernet again, select back Method as _Automatic(DHCP)_ in `Network Connections > Ethernet (wired connection name) > Settings for this wired connection > Ipv4 Settings Tab`.