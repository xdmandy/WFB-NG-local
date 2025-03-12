# Building WFB from source with wfb-ng

## Created by and main project [wfb-ng](https://github.com/svpcom/wfb-ng)

### Link to [rpi-zero2w-image 25.01](https://github.com/svpcom/wfb-ng/releases/download/wfb-ng-25.01/wfb-ng_25.01-rpi3.img.gz)

### Community [link/wfb-ng-support](https://t.me/wfb-ng)

---

# Getting Started for rpi 5

## Installing Dependencies
- Install patched RTL8812AU or RTL8812EU driver:
  ```bash
  sudo apt-get install dkms
  # For 8812au:
  git clone -b v5.2.20 https://github.com/svpcom/rtl8812au.git
  cd rtl8812au/
  # For 8812eu:
  git clone -b v5.15.0.1 https://github.com/svpcom/rtl8812eu.git
  cd rtl8812eu/
  # For both:
  sudo ./dkms-install.sh
  ```
  <button onclick="navigator.clipboard.writeText('sudo apt-get install dkms\n# For 8812au:\ngit clone -b v5.2.20 https://github.com/svpcom/rtl8812au.git\ncd rtl8812au/\n# For 8812eu:\ngit clone -b v5.15.0.1 https://github.com/svpcom/rtl8812eu.git\ncd rtl8812eu/\n# For both:\nsudo ./dkms-install.sh')"></button>

- ```bash
  git clone https://github.com/jedisct1/libsodium --branch stable
  cd libsodium
  ./configure
  make && make check
  sudo make install
  ```
  <button onclick="navigator.clipboard.writeText('git clone https://github.com/jedisct1/libsodium --branch stable\ncd libsodium\n./configure\nmake && make check\nsudo make install')"></button>

- Install wfb from [this link](apt.wfb-ng.org):
  ```bash
  # Install wfb-ng release-25.01:
  curl -s https://apt.wfb-ng.org/public.asc | sudo gpg --dearmor --yes -o /usr/share/keyrings/wfb-ng.gpg
  echo "deb [signed-by=/usr/share/keyrings/wfb-ng.gpg] https://apt.wfb-ng.org/ $(lsb_release -cs) release-25.01" | sudo tee /etc/apt/sources.list.d/wfb-ng.list
  sudo apt update
  sudo apt install wfb-ng
  
  # Install wfb-ng from master branch:
  curl -s https://apt.wfb-ng.org/public.asc | sudo gpg --dearmor --yes -o /usr/share/keyrings/wfb-ng.gpg
  echo "deb [signed-by=/usr/share/keyrings/wfb-ng.gpg] https://apt.wfb-ng.org/ $(lsb_release -cs) master" | sudo tee /etc/apt/sources.list.d/wfb-ng.list
  sudo apt update
  sudo apt install wfb-ng
  ```
  

- Then follow [SETUP-HOWTO](https://github.com/svpcom/wfb-ng/wiki/Setup-HOWTO)

---

# Creating Services

In your Raspi 5, go to `/etc/` and create a file `wifibroadcast.cgf`. Refer to the scripts given in the above repository. This is the main service that contains all the main link channel configurations and other troubleshooting solutions. This is the file to modify.

There are two services in `/etc/systemd/system/`:
- `fpv-camera`: This is the drone-side service.
- `fpv-video`: This is for the GCS side.

These services contain the main pipelines for starting the camera service using GStreamer and transmitting the feed to the GCS side.

Refer to the above repository for these services.

---

# Copying and Pasting Keys for Drone Side

Generate a key using the command below:
```bash
wfb_keygen
```
<button onclick="navigator.clipboard.writeText('wfb_keygen')"></button>

Run this command to copy from GCS (you should be on the same network as GCS):
```bash
scp pi@ip-of-gcs:/etc/drone.key /home/yourusername/path-to-folder
```
<button onclick="navigator.clipboard.writeText('scp pi@ip-of-gcs:/etc/drone.key /home/yourusername/path-to-folder')">Copy</button>

To paste to the drone:
```bash
scp /home/yourusername/path-to-file pi@ip-of-drone:/etc/
```
<button onclick="navigator.clipboard.writeText('scp /home/yourusername/path-to-file pi@ip-of-drone:/etc/')">Copy</button>

---

# Final Setup
After installing wfb-ng and all other dependencies, you should have two files in `/etc/systemd/system/wifibroadcast.services.wants`:
- `wifibroadcast@drone`
- `wifibroadcast@gs`

These services need to be started for broadcasting. If these services are empty, update them in this folder.

For the **drone side**, run:
```bash
sudo systemctl enable wifibroadcast@drone
sudo systemctl enable fpv-camera
sudo reboot
```
<button onclick="navigator.clipboard.writeText('sudo systemctl enable wifibroadcast@drone\nsudo systemctl enable fpv-camera\nsudo reboot')"></button>

For the **GCS side**, run:
```bash
sudo systemctl enable wifibroadcast@gs
sudo systemctl enable rtsp
sudo systemctl enable fpv-video
sudo systemctl enable osd # can skip this if osd is not required
sudo reboot
```
<button onclick="navigator.clipboard.writeText('sudo systemctl enable wifibroadcast@gs\nsudo systemctl enable rtsp\nsudo systemctl enable fpv-video\nsudo systemctl enable osd\nsudo reboot')"></button>

You should get your output on GCS. These commands can be run on RPi5 as GCS as well, but services need to be set up properly.

### Default Image Passwords:
- **For Pi Zero 2W**: `pi@raspberry`
- **For Radxa**: `rock@rock`
