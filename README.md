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
after looking through /lib/modules  6.6.20+rpt-rpi-v8 is the only version that does not have "build" inside its directory. That seems to be what the "make" command failure is going on about. may need an earlier kernel. some talk on the forums about install headers, but did that and still no build config in v8.
  

