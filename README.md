# Building WFB-NG from source locally on your UBUNTU system

## Created by Vasily aka SVPCOM and main project [wfb-ng](https://github.com/svpcom/wfb-ng)

### Link to [rpi-zero2w-image 25.01](https://github.com/svpcom/wfb-ng/releases/download/wfb-ng-25.01/wfb-ng_25.01-rpi3.img.gz)

### Community [link/wfb-ng-support](https://t.me/wfb-ng)

---
## These are commands I searched and ran and got everything in one place 
# Getting Started for your local system

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
  <button onclick="navigator.clipboard.writeText('sudo apt-get install dkms # For 8812au:\ngit clone -b v5.2.20 https://github.com/svpcom/rtl8812au.git\ncd rtl8812au/\n# For 8812eu:\ngit clone -b v5.15.0.1 https://github.com/svpcom/rtl8812eu.git\ncd rtl8812eu/\n# For both:\nsudo ./dkms-install.sh')"></button>

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
  

- Then follow [SETUP-HOWTO](https://github.com/svpcom/wfb-ng/wiki/Setup-HOWTO) if your using BL-8812eu modules there is a special setup you need to else just read and skip to next part 

---

# Creating Services

We have to create a cfg over main cfg to make changes according to user requriments so make a configuration file in both side as follows below <br>
In your local system, go to `/etc/` and create a file if this file is not present `wifibroadcast.cfg`. Refer to the scripts given in the above repository. This is the main service that contains all the main link channel configurations and other troubleshooting solutions. This is the file to modify.

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

# Final Setup
After installing wfb-ng and all other dependencies, you should have two files in `/etc/systemd/system/wifibroadcast.services.wants`:
- `wifibroadcast@drone`
- `wifibroadcast@gs`

These services need to be started for broadcasting. By below commands

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

You should get your output on GCS. These commands can be run on local ubuntu system as GCS as well, but services need to be set up properly.
But if you have flashed SD cards with image then just run above commands in respective sides and it will configure itself and start broadcasting 

### Default Image Passwords:
- **For Pi Zero 2W**: `pi@raspberry`
- **For Radxa**: `rock@rock`

<h3>🔧 TROUBLESHOOT</h3>

<p>If you're getting a problem in setting the link, try the following settings:</p>

<ol>
  <li>Go to the WiFi configuration file located in <code>/etc/</code> by running:
    <pre><code>sudo nano /etc/wifibroadcast.cfg</code></pre>
    <button onclick="navigator.clipboard.writeText('sudo nano /etc/wifibroadcast.cfg')"></button>
  </li>

  <li>In that file, set the following values on <strong>both</strong> the <strong>drone</strong> and <strong>GCS</strong> side:
    <pre><code>stbc = 0
ldpc = 0</code></pre>
  </li>

  <li>Then run the wifibroadcast services again and check if the link appears.</li>
</ol>

<blockquote><strong>Note:</strong> If the link is established after doing this, the issue might be with your WiFi card. Some non-authentic AU cards often cause this problem.</blockquote>

<h1>USB Camera Pipeline</h1>
<p>A generic GStreamer pipeline for USB cameras using hardware H.264 encoding (Raspberry Pi).</p>

<h2>Pipeline Code</h2>
<pre><code class="language-bash">gst-launch-1.0 v4l2src device=/dev/video0 ! "video/x-raw,width=640,height=480,framerate=30/1" ! videoconvert ! v4l2h264enc ! "video/x-h264,level=(string)4" ! h264parse disable-passthrough=true ! rtph264pay config-interval=1 pt=96 mtu=1400 aggregate-mode=zero-latency ! udpsink host=127.0.0.1 port=5602 sync=false</code></pre>

<h2>Usage Notes</h2>
<ol>
  <li>
    <strong>Camera Resolution Check</strong>:<br>
    Before running the pipeline, verify your camera's supported resolutions with:<br>
    <pre><code class="language-bash">v4l2-ctl --all --device /dev/videoX</code></pre>
    Replace <code>X</code> with your camera's device number (e.g., <code>video0</code>).
  </li>
  <li>
    <strong>Hardware H.264 Encoding</strong>:<br>
    This pipeline uses <code>v4l2h264enc</code> (Raspberry Pi's hardware encoder).
  </li>
  <li>
    <strong>Performance Note</strong>:<br>
    <ul>
      <li>Currently uses <code>videoconvert</code> (CPU usage ~60%).</li>
      <li><em>Potential Optimization</em>: Replacing <code>videoconvert</code> with <code>v4l2convert</code> may reduce CPU usage but may cause CAPS errors (needs testing).</li>
      <li>While Pasting this into fpv-camera service add single quotes(') where ever there is bracket else the kernel will through error at opening brackets.</li>
    </ul>
  </li>
</ol>

