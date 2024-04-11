# Alfa-HaLow<br>
Create mesh based on AHPI7292S. try to get required bits from Alfa script
Username must be "pi" and must be on 32 bit OS, unsure if lite will work or if full is required like Teledatics
### this process will complete without errors, but does not show alfa board interface and deletes wlan0
### unsure if driver actually loaded? looking at that now


# Kernel headers<br>
- sudo apt install -y raspberrypi-kernel raspberrypi-kernel-headers

# point to driver location<br>
- curl -sL "https://downloads.alfa.com.tw/raspbian/raspbian.public.key" | sudo apt-key add -<br>
- echo "deb https://downloads.alfa.com.tw/raspbian/ bullseye main contrib non-free firmware rpi" | sudo tee /etc/apt/sources.list.d/alfa.list<br>
- sudo apt update

# install driver<br>
- sudo apt install -y nrc7292-dkms nrc7292-firmware nrc7292-nrc-pkg 

# Add to boot/firmware/config.txt<br>	
[newracom]
dtoverlay=disable-bt<br>
dtoverlay=disable-wifi<br>
dtoverlay=disable-spidev<br>
dtoverlay=miniuart-bt<br>

now going to try this https://github.com/newracom/nrc7292_sw_pkg/blob/master/package/doc/UG-7292-018-Raspberry_Pi_setup.pdf
stuck at nrc7292_sw..../src/nrc make step 4.2
"Makefile:5: /lib/modules/6.6.20+rpt-rpi-v8/build/.config: No such file or directory
make: *** No rule to make target '/lib/modules/6.6.20+rpt-rpi-v8/build/.config'.  Stop."
after looking through /lib/modules  6.6.20+rpt-rpi-v8 does not have "build" inside its directory. That seems to be what the "make" command failure is going on about. may need an earlier kernel. some talk on the forums about install headers, but did that and still no build config in v8.
 6.1.21-v8+ also does not have build file , looks like the v7's do though 6.6 and 6.1
 otherwise this process was going well

 "Question: When running sudo sh install-driver.sh on my RasPi 4B or 400, I see the following:

Your kernel header files aren't properly installed.
Please consult your distro documentation or user support forums.
Once the header files are properly installed, please run...

possible fix below

"Answer: The Pi 4/400 firmware now prefers the 64-bit kernel if one exists so even if you installed the 32 bit version of the RasPiOS, you may now have the 64 bit kernel active.
The fix:
add the following to /boot/config.txt and reboot:
arm_64bit=0
Reference:
https://forums.raspberrypi.com/viewtopic.php?p=2091532&hilit=Tp+link#p2091532"

seems to have worked, now loading the 6.6.20-..-v7l kernel which has the build file. now to run through entire process again to see if compile works

step 3.5 after adding mac80211 to etc/modules it mentions blacklisting the broadcom wifi module. not doing this on this run. not sure if that will cause issues down the line.

### actually managed to get an interface to show following this https://github.com/newracom/nrc7292_sw_pkg/blob/master/package/doc/UG-7292-018-Raspberry_Pi_setup.pdf
while on 32 bit bullseye full image and doing the arm_64bit=0 mod in /boot/config.txt
issue now is what arguments to pass through with insmod when loading the driver. and somehow I've deleted my normal wifi interface in this process.
Running the fullmesh commands does allow the alfa board to see the teledatics boards using mpath dump, but no pings go across either way and ip a on the alfa unit says no-carrier and state dormant

