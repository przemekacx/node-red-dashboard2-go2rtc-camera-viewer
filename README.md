# node-red-dashboard2-go2rtc-camera-viewer
Simple live low-latency WebRTC camera viewer for Node-RED FlowFuse Dashboard 2

![Node-RED](https://img.shields.io/badge/Node--RED-5.x-red)
![Dashboard](https://img.shields.io/badge/FlowFuse-Dashboard_2-blue)


## 📖  What is this?
With this project you can define and view live camera streams on **Node-RED Dashboard 2**.

## 🎯 Scope
This project does one thing only:

**Displays live camera streams in Node-RED Dashboard 2 with the lowest possible latency.**

It is **not** intended to replace any NVR.
If you need:
- recording
- object detection
- AI
- timeline
- playback
- advanced camera management

use Frigate, ZoneMinder, Blue Iris or another dedicated solution.

## 🎯 Design goals

- Fast live preview
- Low latency (WebRTC via go2rtc)
- Simple setup
- Designed for wall tablets and dashboards
- Not intended to replace an NVR in any way

## 📌 Project status

This project is considered feature complete.
The goal is to provide a simple, low-latency live camera viewer for **Node-RED Dashboard 2**.

Future updates will primarily focus on:
- bug fixes
- compatibility with newer **Node-RED Dashboard 2** releases
- compatibility with newer go2rtc releases
- documentation improvements

New features are intentionally kept out of scope.

## ✨ Features

- 🚀 WebRTC low latency streaming
- 📱 Optimized for tablets and desktop dashboards
  - Phones are supported by enabling **Desktop site** in the browser and using pinch-to-zoom.
- 🏠 Home automation ready
- 🎥 Multiple camera support
- 🐳 Docker Compose example included
- 🌐 Works with both direct local connections and reverse proxy setups

## 🖼 Screenshot
Example dashboard:
![Dashboard](assets/screenshot.png)

## 🎬 Demo
Live preview:
![Dashboard](assets/demo.gif)

## 📋 Requirements

- 🔹 Node-RED
- 🔹 FlowFuse Dashboard 2.x
- 🔹 go2rtc
- 🔹 Docker (optional)

## 🚀 Installation

### 1. Install go2rtc
This project uses go2rtc as the WebRTC gateway. You can either install go2rtc by following its official documentation:
https://github.com/AlexxIT/go2rtc

or use pre-prepared docker-compose.yml running:

```bash
git clone https://github.com/przemekacx/node-red-dashboard2-go2rtc-camera-viewer
cd node-red-dashboard2-go2rtc-camera-viewer
cd go2rtc
docker compose up -d
```

### 2. Configure your cameras
The next step is to configure your cameras in go2rtc.
Example configuration file:

`examples/go2rtc/config/go2rtc.yaml`

In short:
```yaml
streams:
  domofon:
    - rtsp://user:password@192.168.1.20/cam/realmonitor?channel=1&subtype=0#backchannel=0
```

Where:

`streams:`
starts the stream definitions

`  domofon:`
is the stream name used by go2rtc and referenced by this project. Replace the RTSP URL and credentials with those provided by your camera.

The following configuration is required:

```yaml
api:
  listen: ":1984"
  origin: "*"
  
webrtc:
  listen: ":8555"
  candidates:
    - YOUR_GO2RTC_IP:8555

ffmpeg:
  timeout: 15
```
These settings are required for WebRTC to work correctly.

Some cameras need more time to start streaming. A value of `15` seconds worked reliably during testing.

> [!NOTE]
> Browser support for H.265 WebRTC is limited.
> Consider transcoding to H.264 in go2rtc for better experience.

Since this viewer is intended only for live preview, there is little benefit in using high-resolution streams.

A 1280×720 stream (or even lower) is usually sufficient.

`    - rtsp://user:password@192.168.1.20/cam/realmonitor?channel=1&subtype=0#backchannel=0`

This is the RTSP URL of your camera, including the username, password and stream path. For live preview, a lower-resolution H.264 sub stream is usually sufficient. It reduces CPU usage and minimizes transcoding.
This path is camera specific and differs based on camera vendor.
If you don't know your camera RTSP URL, search for your camera model together with "RTSP stream".
Example web page:

https://security.world/rtsp/


### 3. Test RTSP
Before continuing, verify that your camera RTSP stream is accessible.
The easiest way is to use VLC (https://www.videolan.org/)
Open network stream using **Ctrl+N** and paste following link:

rtsp://YOUR_GO2RTC_IP:8554/YOUR_GO2RTC_STREAM_NAME

Where:

`YOUR_GO2RTC_IP:` is your go2rtc server IP address

`YOUR_GO2RTC_STREAM_NAME:` is your stream camera name from go2rtc config file.

If the stream plays successfully without asking for credentials, you can continue with the next step.
If the RTSP stream does not work, please refer to the go2rtc documentation before opening an issue here.
https://github.com/AlexxIT/go2rtc/blob/master/internal/app/README.md


### 4. Import Node-RED flow
Now switch to Node-RED.

This project assumes you already have FlowFuse Dashboard 2 installed and know how to create pages, groups and widgets.

Now you can import flow from the following file:
`/examples/flows.json`

The import creates:
- &lt;ui-template&gt; *node*
- dashboard/examples *page*
- node-red-dashboard2-go2rtc-camera-viewer *group*

### 5. Update configuration
When done update the following settings in newly created &lt;ui-template&gt; node:


| Setting | Description |
|---------|-------------|
| `cameras` | List of camera stream names from your go2rtc configuration. |
| `tallCameras` | Cameras displayed with double-height tiles. |
| `go2rtcUrl` | URL of your go2rtc WebRTC endpoint. |

> **Note**
>
> `tallCameras` is intended for dual-lens cameras that combine two images into a single vertical stream.

That is all.
Now you should have in your Node-RED menu *page* named *Examples* and *group* named *node-red-dashboard2-go2rtc-camera-viewer* with all your defined cameras.

### Reverse proxy (optional, recommended)

If you expose Node-RED through a reverse proxy, you can proxy the go2rtc WebRTC endpoint as well.

Then update:

| Setting | Value |
|---------|-------|
| `go2rtcUrl` | `/go2rtc/webrtc` |

instead of

| Setting | Value |
|---------|-------|
| `go2rtcUrl` | `http://YOUR_GO2RTC_IP:1984/api/webrtc` |

## 🛠 Debug

Enable browser console logging (F12):

```javascript
debug: true
```

Open browser Developer Tools (F12) and Console.
```text
Example output:
[12:34:56] [domofon] START
[12:34:56] [domofon] REMOTE SDP
[12:34:56] [domofon] TRACK
[12:34:56] [domofon] ICE: connected
[12:34:56] [domofon] STATE: connected
```

## 🙏 Credits

- [Node-RED](https://nodered.org/)
- [FlowFuse Dashboard 2](https://dashboard.flowfuse.com/)
- [go2rtc](https://github.com/AlexxIT/go2rtc)
  
# Changelog

## v1.0.0

- Initial release