- Alfa-HaLow<br>
Create mesh based on AHPI7292S. try to get required bits from Alfa script
Username must be "pi" and must be on 32 bit OS, unsure if lite will work or if full is required like Teledatics
#this process will complete without errors, but does not show alfa board interface and deletes wlan0

- kernel headers<br>
sudo apt install -y raspberrypi-kernel raspberrypi-kernel-headers

- point to driver location<br>
curl -sL "https://downloads.alfa.com.tw/raspbian/raspbian.public.key" | sudo apt-key add -
echo "deb https://downloads.alfa.com.tw/raspbian/ bullseye main contrib non-free firmware rpi" | sudo tee /etc/apt/sources.list.d/alfa.list

sudo apt update

- install driver
sudo apt install -y nrc7292-dkms nrc7292-firmware nrc7292-nrc-pkg 

- Add to boot/firmware/config.txt<br>	
#### Custom newracom block beg ####
[newracom]
dtoverlay=disable-bt<br>
dtoverlay=disable-wifi<br>
dtoverlay=disable-spidev<br>
dtoverlay=miniuart-bt<br>
#### Custom newracom block end ####
  

