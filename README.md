# Alfa-HaLow
Create mesh based on AHPI7292S. try to get required bits from Alfa script

- #kernel headers
sudo apt install -y raspberrypi-kernel raspberrypi-kernel-headers
#not sure what this line is for
touch /tmp/.need_reboot

#looks like we need to enable SPI and serial maybe?

#variable to set repo location
APT_REPOS_URL="https://downloads.alfa.com.tw/raspbian"

- # this looks like the section to ahndle the drivers, not sure whats important here yet
etup_nrc7292_pkgs() {
	[ -e /etc/apt/sources.list.d/alfa.list ] || {
		curl -sL "$APT_REPOS_URL/raspbian.public.key" | sudo apt-key add -
		echo "deb $APT_REPOS_URL/ bullseye main contrib non-free firmware rpi" | sudo tee /etc/apt/sources.list.d/alfa.list
	}

	(dpkg -s nrc7292-dkms >/dev/null 2>&1) &&
		(dpkg -s nrc7292-firmware >/dev/null 2>&1) &&
		(dpkg -s nrc7292-nrc-pkg >/dev/null 2>&1) && return

	sudo apt update
	sudo apt install -y nrc7292-dkms nrc7292-firmware nrc7292-nrc-pkg || {
		echo "[Err] Install nrc7292 packages failed."
		exit 1

- #DT overlay setup, there was something about disableing SPI so it wouldnt be busy for the NRC spi controller or something
setup_dtoverlays() {
	local target_file="/boot/config.txt"

	(cat $target_file | grep -e "#### Custom newracom block" >/dev/null 2>&1) || touch /tmp/.need_reboot

	local block_beg=$(cat $target_file | grep -n "#### Custom newracom block beg ####" | cut -d: -f1)
	block_beg=${block_beg:-0}
	local block_end=$(cat $target_file | grep -n "#### Custom newracom block end ####" | cut -d: -f1)
	block_end=${block_end:-0}

	if [ $block_beg -gt 0 -a $block_end -gt 0 -a $block_end -gt $block_beg ]; then
		# Remove duplicated block to keep idempotent
		sudo sed -i $target_file -e "${block_beg},${block_end}d"
	fi

	cat | sudo tee -a $target_file <<EOD
#### Custom newracom block beg ####
[newracom]
dtoverlay=disable-bt
dtoverlay=disable-wifi
dtoverlay=disable-spidev
dtoverlay=miniuart-bt
#### Custom newracom block end ####
  

