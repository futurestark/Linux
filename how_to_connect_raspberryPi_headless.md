# How-To: Raspberry Pi connect with linux system via ssh, headless (without dedicated display unit)
To connect a raspberry pi with your linux system via ssh (and use it even if you don't have a dedicated display unit for raspberry pi)

The steps are given here on [how to ssh your raspi](https://www.raspberrypi.org/documentation/remote-access/ssh/) . Also [this link is more specific to linux system, for the same](https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md).

#### Step-by-step guide
1. What you need:
    1. Raspberry Pi (say raspi from here)
    2. Ethernet cable to connect to your linux system
    3. Linux system - say ubuntu (should work similarly with other linux OS as well) machine from here
2. Make sure you Enable SSH in raspi first (you might need a graphical display for this step - this step is enough if done once):
    1. Launch `Raspberry Pi Configuration` from the `Preferences` menu in the raspi machine.
    2. Navigate to the `Interfaces` tab.
    3. Select `Enabled` next to `SSH`.
    4. Click `OK`.
3. Connect ubuntu machine and raspi via ethernet cable.
4. On Ubuntu machine:
    1. Goto : `Network Connections > Ethernet (wired connection name) > Settings for this wired connection`
    2. Goto : `Ipv4 Settings` Tab
    3. Select : `Method` as `Shared to other computers`.
5. On Ubuntu machine terminal :
    1. Use one of the [method given here to find the IP address of your raspi](https://www.raspberrypi.org/documentation/remote-access/ip-address.md). Usually the first method - `ping raspberrypi.local` - should be enough and easy. This should give you the raspi IP.
    2. Now type `ssh pi@<raspi_IP>`, type password when prompted for. By default the password is `raspberry`. Follow [this article to configure your Pi to allow your computer to access it without providing a password each time you try to connect](https://www.raspberrypi.org/documentation/remote-access/ssh/passwordless.md). Basic idea for password less ssh is:
        1. Generate a public key in your host system with `ssh-keygen`.
        2. Copy the public key to your raspberry pi using `ssh-copy-id <USERNAME>@<RASPI-IP-ADDRESS>`.
6. Now you can do operations for example - [copy file(s) to and from your raspi](https://www.raspberrypi.org/documentation/remote-access/ssh/scp.md).
    1. Remember as seen in the above link you should specify absolute raspi path e.g. `scp source pi@<raspi_IP>:/home/pi/to/your/copy/ destination` , for `scp`.
    2. Enjoy!
7. To disconnect and shutdown your raspi:
    1. type sudo `shutdown -h now` in ubuntu terminal session running the raspberryPi ssh.

#### Note!
When you have to disconnect yourself from raspi and connect ubuntu machine to Internet using Ethernet again, select back Method as _Automatic(DHCP)_ in `Network Connections > Ethernet (wired connection name) > Settings for this wired connection > Ipv4 Settings Tab`.
