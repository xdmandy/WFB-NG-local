# Wfb-ng-on-rpi5
Under Testing Using and building wfb-ng on rpi5 for drone side for wifi packet injection and reciving 
# Building WFB from source with wfb-ng

## Created by and main project [wfb-ng](https://github.com/svpcom/wfb-ng)

### Link to [rpi-zero2w-image 25.01](https://github.com/svpcom/wfb-ng/releases/download/wfb-ng-25.01/wfb-ng_25.01-rpi3.img.gz)

### Community [link/wfb-ng-support](https://t.me/wfb-ng)

---

# Getting Started for rpi 5

## Installing Dependencies
- ```bash
  git clone https://github.com/jedisct1/libsodium --branch stable
  cd libsodium
  ./configure
  make && make check
  sudo make install
  ```
  <button onclick="navigator.clipboard.writeText('git clone https://github.com/jedisct1/libsodium --branch stable\ncd libsodium\n./configure\nmake && make check\nsudo make install')">Copy</button>

- Install wfb from [this link](apt.wfb-ng.org)
- Then follow [SETUP-HOWTO](https://github.com/svpcom/wfb-ng/wiki/Setup-HOWTO)

---

# Creating Services

In your Raspi 5, go to `/etc/` and create a file `wifibroadcast.service`. Refer to the scripts given in the above repository. This is the main service that contains all the main link channel configurations and other troubleshooting solutions. This is the file to modify.

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
<button onclick="navigator.clipboard.writeText('scp pi@ip-of-gcs:/etc/drone.key /home/yourusername/path-to-folder')"></button>

To paste to the drone:
```bash
scp /home/yourusername/path-to-file pi@ip-of-drone:/etc/
```
<button onclick="navigator.clipboard.writeText('scp /home/yourusername/path-to-file pi@ip-of-drone:/etc/')"></button>

---

# Troubleshooting
```bash
sudo wfb-cli drone
sudo wfb-cli gs
```
<button onclick="navigator.clipboard.writeText('sudo wfb-cli drone\nsudo wfb-cli gs')"></button>

In `/etc/wifibroadcast.cfg`, check `channel` and `link_set`; they should be the same. Then check the status of all services.
