#!/bin/bash

# Check if Script is Run as Root
if [[ $EUID -ne 0 ]]; then
	echo "You must be a root user to run this script, please run sudo ./install.sh" 2>&1
	exit 1
fi

username=$(id -u -n 1000)
builddir=$(pwd)

# Update packages list and update system
apt update
apt upgrade -y

# Install Qogir-theme
cd $builddir
apt-get install gtk2-engines-murrine gtk2-engines-pixbuf -y
git clone https://github.com/vinceliuice/Qogir-theme.git
cd Qougir-theme
./install.sh
./install.sh --tweaks round

# Install Qogir-icon-theme
cd $builddir
git clone https://github.com/vinceliuice/Qogir-icon-theme.git
cd Qougir-icon-theme
./install.sh

# Move fonts
cd $builddir
mv fonts /home/$username/.local/share/

# Move wallpapers
cd $builddir
mv wallpaper /home/$username/.local/share/

# Install Ulanuncher
cd $builddir
add-apt-repository universe -y && add-apt-repository ppa:agornostal/ulauncher -y && apt update && apt install ulauncher -y

# Move Ulauncher themes
cd $builddir
mkdir -p /home/$username/.config/ulauncher/user-themes
mv goxir-dark /home/$username/.config/ulauncher/user-themes/
mv goxir-light /home/$username/.config/ulauncher/user-themes/

# Install xfce4-docklike-plugin
cd $builddir
git clone https://gitlab.xfce.org/panel-plugins/xfce4-docklike-plugin.git && cd xfce4-docklike-plugin
./autogen.sh --prefix=/usr/local
make
make install

# Install xfce4-panel-profiles
cd $builddir
tar xf xfce4-panel-profiles-xfce4-panel-profiles-1.0.14.tar.bz2
cd xfce4-panel-profiles-xfce4-panel-profiles-1.0.14
./configure
make
make install

# Install picom
cd $builddir
apt install picom -y
mv picom /home/$username/.config/
mv /home/$username/.config/picom/picom.desktop /home/$username/.config/autostart
