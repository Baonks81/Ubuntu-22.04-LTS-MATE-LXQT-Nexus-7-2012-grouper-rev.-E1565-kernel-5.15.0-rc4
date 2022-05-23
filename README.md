# Ubuntu-22.04-LTS-MATE-LXQT-Nexus-7-2012-grouper-rev.-E1565-kernel-5.15.0-rc4
Ubuntu 22.04 nexus 7 2012 grouper rev. E1565

Link tải trên Gdrive:



Ubuntu 22.04 LTS MATE/LXQT ext4 kernel-5.15.0-rc4-next-20211011-postmarketos-grate

https://drive.google.com/drive/u/1/folders/1cIJaryNta43DQVxzOQu3RDl4IJNVQ9i0



Không biết tại sao bài bị xoá, đăng lại link pdf cho ai cần

http://www.mediafire.com/file/r37ylhmk7wsb0a4



Link gốc bài bị xoá không rõ nguyên nhân:

https://tinhte.vn/threads/cai-postmarketos-len-nexus-7-2012-16gb.3113099/?_xfNoRedirect=1#post-56448223



Các bước chuẩn bị tạo .img cơ bản như sau:



1. Mở img bằng lệnh sau để mount và chỉnh sửa trong quyền sudo su

# sudo modprobe loop

# sudo losetup -f

# sudo losetup /dev/loop0 /home/[username]/jammy-preinstalled-server-armhf+raspi.img

# sudo partprobe /dev/loop0

# sudo gparted /dev/loop0

2. Format /boot thành ext2 và đặt label là pmOS_boot, chọn manage flag là boot. Đặt label cho rootfs là pmOS_root

3. Extract kernel-5.15.0-rc4-next-20211011-postmarketos-grate.zip

4. Copy boot.img, vmlinuz, initramfs, extend đến phân vùng /pmOS_boot

5. Copy /lib/module và /lib/firmware vào đúng /lib trên phân vùng /pmOS_root

6. Sửa /etc/fstab vì kernel 5.15.0-rc4 tìm LABEL pmOS_boot và pmOS_root để khởi động trong Ubuntu

7. Copy thư mục dtb vào /user/share. Copy libturbojpeg.so.0.2 vào /lib/armhfgnueabi. Copy asound.conf, sysctl.conf, modules, deviceinfo, pointercal vào /etc. Copy asound.state /var/lib/alsa, /xorg vào /usr/lib, /xorg.conf.d vào /usr/share/X11, sửa cloud.cfg trong /etc/cloud

8. cpufreq.start, temp_throttle, clear_ram copy vào /opt để chỉnh lại virtual memory nếu cần.

9. Dùng losetup, partprobe, gparted, truncate để resize và cut vùng unallocated trong file .img cho vừa kích thước userdata trên Nexus7 8GB(có thể dùng với 16GB và 32GB)



# sudo gparted /dev/loop0















# sudo losetup -d /dev/loop0



# fdisk -l /home/[username]/jammy-preinstalled-server-armhf+raspi.img



Disk jammy-preinstalled-server-armhf+raspi.img: 6144 MB, 6144000000 bytes, 12000000 sectors

Units = sectors of 1 * 512 = 512 bytes

Sector size (logical/physical): 512 bytes / 512 bytes

I/O size (minimum/optimal): 512 bytes / 512 bytes

Disk identifier: 0x000ea37d



attachFull5304790



default user was kim, default passwd was possible



default user was ubuntu, default passwd was ubuntu for LXQT



default user: oem/passwd: ubuntu, user: su - / passwd: ubuntu for MATE



Đã thử và thành công boot được Ubuntu 22.04  Jammy Jellyfish lên Nexus 7 2012, tạo local user/passwd theo bài viết này cho cloud-init như 1 phần bổ sung khi bị cloud-init: used fallback datasource thì module cuối trong cloud.cfg không chạy để khởi tạo user/passwd ubuntu:ubuntu

https://stackoverflow.com/questions/61591885/how-do-i-set-a-custom-password-with-cloud-init-on-ubuntu-20-04



Đây là bản preinstalled server nên không có GUI và cần có micro usb-otg keyboard để nhập lệnh



Cách kiểm tra Nexus 7 2012 là mã cũ PM269 hay E1565. Tham khảo:

https://wiki.postmarketos.org/wiki/Google_Nexus_7_2012_(asus-grouper)



Variants

grouper rev. PM269 - without GSM (oldest)
grouper rev. E1565 - without GSM (modern revision)
tilapia rev. E1565 - with GSM



Do I have grouper or tilapia?



TWRP (adb shell) $ grep androidboot.baseband=unknown /proc/cmdline && echo grouper || echo tilapia



Which hardware revision of grouper do I have?





TWRP (adb shell) $ find /sys/devices/ | grep -c max776 && echo You have E1565



TWRP (adb shell) $ find /sys/devices/ | grep -c tps6591 && echo You have PM269



Install Alpine Linux on Virtualbox:

[MEDIA=youtube]1_bsycXrFcI[/MEDIA]



Đặt máy về bootloader, để flash boot.img qua fastboot hoặc dùng method của postmarketOS. Kết nối Nexus 7 vào máy tính qua cáp usb chuẩn 1.1 → 2.0



$ sudo adb start-server



$ sudo adb reboot bootloader



$ sudo fastboot flash boot <boot_filename>.img



Vào TWRP for grouper 3.3.1-0 trở lên https://dl.twrp.me/grouper/

Dùng adb shell trên PC/laptop hoặc Advance/Terminal trong twrp để umount mmcblk0p9 (làm 2 lần cho chắc ăn vì android mount /data và /sdcard trên cùng mmcblk0p9) [với tilapia (bản 3G) là mmcblk0p10]



1. TWRP(Advance → Terminal): $ df

2. TWRP(Advance → Terminal): $ umount /dev/block/mmcblk0p__  <- fill partition number

3. TWRP(Advance → Terminal): $ umount /dev/block/mmcblk0p__  <- fill partition number



On PC/Laptop terminal:



$ adb push <rootfs_filename>.img /dev/block/mmcblk0p__  <- fill partition number



grouper has likely data on /dev/block/mmcblk0p9 but make sure!
tilapia has likely data on /dev/block/mmcblk0p10 but make sure!



remove cloud-init, snapd, needrestart service

https://askubuntu.com/questions/1346874/failed-to-check-for-processor-microcode-upgrades-at-the-end-of-apt-get-how-ca



# sudo apt --purge remove  needrestart



Rotate fbcon



# sudo su



# echo 1 | sudo tee /sys/class/graphics/fbcon/rotate



# echo 1 | sudo tee /sys/class/graphics/fbcon/rotate_all



# chown -hvR ubuntu /home/ubuntu



Connect router wifi:



# sudo su



# sudo wpa_passphrase [your_ssid_name] [your_router_passwd] > wpa.conf



Loading wpa.conf vào wpa_supplicant và ping thử google.com



# sudo wpa_supplicant -B -i wlan0 -c wpa.conf



# sudo dhclient wlan0



# sudo ping -c 3 google.com



Tạo kết nối wifi bang iwd daemon



# sudo apt install iwd



# sudo systemctl stop wpa_supplicant



# sudo systemctl disable wpa_supplicant



# sudo systemctl start iwd



# sudo systemctl enable iwd



# sudo nano /etc/iwd/main.conf để đặt EnableNetworkConfiguration=true



# sudo iwctl



[iwctl]# device list



[iwctl]# station wlan0 scan



[iwctl]# station wlan0 get-networks



[iwctl]# station wlan0 connect [your_ssid]



Passwd: [your_router_passwd]



[iwctl]# exit



# sudo ping -c 3 google.com



Install lxqt-core, sddm



# sudo apt install lxqt-core sddm

# sudo apt install bluez blueman htop neofetch iio-sensor-proxy perl x11-xserver-utils xinput rfkill network-manager onboard cpufrequtils



Firefox-esr v.91



# sudo add-apt-repository ppa:mozillateam/ppa

# sudo apt-get update

# sudo apt-get install firefox-esr



Autologin:



# sudo nano /usr/lib/sddm/sddm.conf.d/autologin



[Autologin]

User=ubuntu

Session=lxqt.desktop

Relogin=



Install QTvirtualkeyboard at sddm



# sudo apt-get install qtvirtualkeyboard-plugin qml-module-qt-labs-folderlistmodel qml-module-qtquick-dialogs qml-module-qtquick-controls qml-module-qtquick-layouts qml-module-qtquick-windows2



Qt environment



# sudo nano /etc/environment



QT_IM_MODULE=qtvirtualkeyboard

QT_QUICK_BACKEND=software

LIBGL_ALWAYS_SOFTWARE=1

QT_AUTO_SCREEN_SCALE_FACTOR=2

#XCURSOR_SIZE=48

MOZ_USE_XINPUT2=1

MOZ_X11_EGL=1



# nano ~/.profiles



   unset QT_IM_MODULE



Grate-driver GPU driver on launchpad. Thanks Dmitry Osipenko aka digetx

https://launchpad.net/~grate-driver/+archive/ubuntu/ppa/+packages?field.name_filter=&field.status_filter=published&field.series_filter=jammy



# sudo add-apt-repository ppa:grate-driver/ppa

# sudo apt-get update

# sudo apt-get install xserver-xorg-video-opentegra libvdpau-tegra linux-firmware libd3dadapter9-mesa libegl-mesa0 libgbm1 libgl1-mesa-dri libglapi-mesa libglx-mesa0 libosmesa6 mesa-opencl-icd mesa-va-drivers mesa-vdpau-drivers mesa-vulkan-drivers



# sudo apt install dphys-swapfile zram-config



Rotate screen:

https://github.com/donbowman/kde-auto-rotate



https://gitlab.com/gullradriel/asus-grouper-nexus-7-sensor-daemon



Các phần mềm hỗ trợ để trong /opt



Upgrade to systemd service



Temp_throttle.service https://www.mediafire.com/file/ren0d9i45nz60x3/temp_throttle.service



Temp_throttle control

https://www.mediafire.com/file/4z5ain4n7oopoxe/temp_throttle_ctl



# sudo cp temp_throttle_ctl /usr/bin/

# sudo chmod u+x /usr/bin/temp_throttle_ctl

# sudo cp temp_throttle.service /etc/systemd/system/

# sudo visudo



   ALL ALL=(ALL) NOPASSRD: /opt/temp_throttle



# sudo systemctl enable --now temp_throttle.service

# sudo systemctl start temp_throttle.service



Chromium browser from Debian repository

Dùng search-key để đăng ký keys update sources.list



# sudo gpg --keyserver keyserver.ubuntu.com --search-keys admin@mobian-project.org



# sudo gpg --keyserver keyserver.ubuntu.com --search-keys 951D61F2BC232697



# sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 393F924A855FB27D



# sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys DC30D7C23CBBABEE



# sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 73A4F27B8DD47936



# sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 4DFAB270CAA96DFA



# sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys A48449044AAD5C5D



# sudo nano /etc/apt/sources.list.d/debian.list



deb http://repo.mobian-project.org/ bookworm main non-free



deb http://deb.debian.org/debian bookworm main contrib non-free

deb http://security.debian.org/debian-security bookworm-security main contrib non-free

deb http://deb.debian.org/debian bookworm-backports main contrib non-free

deb http://deb.debian.org/debian bookworm-updates main contrib non-free



# sudo apt-get update

# sudo apt-get install chromium



Image source là Raspberry Pi Generic (Hard-Float) preinstalled server image:

https://cdimage.ubuntu.com/releases/22.04/release/ubuntu-22.04-preinstalled-server-armhf+raspi.img.xz



Xem thêm hướng dẫn trong bài post này:

https://tinhte.vn/thread/ubuntu-21-04-1-hirsute-hippo-pre-image-cho-nexus-7-2012-wifi-rev-e1565-kernel-5-14-rc3-next-grate.3392201/



[MEDIA=youtube]it-JM-GkJxw[/MEDIA]



[MEDIA=youtube]MIG1uvGcIJc[/MEDIA]
