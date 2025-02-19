# unifi-video-api

[![Build Status](https://travis-ci.org/yuppity/unifi-video-api.svg?branch=master)](https://travis-ci.org/yuppity/unifi-video-api)

Python API for interfacing with UniFi Video.

**Supported UniFi Video versions**: v3.9.12.

**Supported Ubiquiti camera models**: UVC, UVC G3, UVC G3 Dome, UVC Dome, UVC Pro, UVC G3 Pro,
UVC G3 Flex, UVC Micro, UVC G3 Micro, airCam, airCam Dome, and airCam Mini.


## Features
**For a single UniFi Video server**:
* Support both username/password and API key auths
* Provide GET, POST, PUT, and DELETE methods
* Handle session tracking and login when necessary
* Provide iterable collections for cameras and recordings that the UniFi Video server
  is aware of

**Per camera**:
* Set or show picture settings: brightness, contrast, saturation, hue, denoise,
  sharpness, dynamic range
* Set or show IR led state
* Set or show on-display text
* Set or show timestamp state
* Set or show watermark/logo state
* Set recording mode to fulltime, motion, or disabled
* Set recording pre/post padding
* Take and download pictures (snapshots)
* Download camera footage between arbitrary start and end times

**Per recording**:
* Delete
* Download
* Snapshot (thumbnail) download

## Installation
In your project directory:

```
git clone https://github.com/yuppity/unifi-video-api && \
  ln -s unifi-video-api/unifi_video .
```

Alternatively, place the [unifi_video](unifi_video) folder somewhere in your filesystem and
include the path in `$PYTHONPATH`.

You shouldn't need any external libraries, unless you want to run the tests or
build the docs (see [requirements_dev.txt](requirements_dev.txt)).
*unifi-video-api* does use the [six](https://pypi.org/project/six/) library but
will fallback to using the included *six* should it fail to import *six* from
system level packages.

Both python 2.7+ and python3 are supported.

## Usage
```python
from unifi_video import UnifiVideoAPI

# Default kwargs: addr = 'localhost', port = 7080, schema = http
uva = UnifiVideoAPI(username='username', password='password', addr='10.3.2.1')

# Use API key (can be set per user in Unifi NVR user settings)
uva = UnifiVideoAPI(api_key='xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx', addr='10.3.2.1')

# Use HTTPS and skip cert verification
uva = UnifiVideoAPI(api_key='xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx', addr='10.3.2.1',
  port=7443, schema='https', verify_cert=False)

# Save snapshot from camera whose id, name or onscreen display text
# is "Garage"
uva.get_camera('Garage').snapshot('some/path/snapshot.jpg')

# Save snapshot from all cameras to ./snapshot_camera id_timestamp.jpg
for camera in uva.cameras:
  camera.snapshot()

# Get footage from camera "Garage" for specific timespan.
# (The resulting file will be 0 bytes when no footage is found.)
uva.get_camera('Garage').recording_between('2018-12-01 00:00:00',
  '2018-12-01 00:05:00')

# Specify filename
uva.get_camera('Garage').recording_between('2018-12-01 00:00:00',
  '2018-12-01 00:05:00', 'first_mins_of_dec.mp4')

# Change onscreen display text
uva.get_camera('Garage').set_onscreen_text('Home garage')

# Set IR leds to auto mode
uva.get_camera('Garage').ir_leds('auto')

# Turn off IR leds (manual mode implied)
uva.get_camera('Garage').ir_leds('off')

# Turn of IR leds (manual mode implied)
uva.get_camera('Garage').ir_leds('on')

# Set camera to record at all times and to pre capture 5 secs
uva.get_camera('Garage').set_recording_settings('fulltime',
  pre_padding_secs=5)

# Set camera to record motion events only
uva.get_camera('Garage').set_recording_settings('motion')

# Disable recording altogether
uva.get_camera('Garage').set_recording_settings('disable')

# List recordings
for rec in uva.recordings:
  print(rec)

# Download recording, write to local file recording01.mp4
uva.recordings['xxxxxxxxxxxxxxxxxxxx'].download('recording01.mp4')
```


## Warning
This software has been tested against UniFi Video v3.9.12 and a single UVC G3
camera. While unlikely, should any of the POST payloads result in software or
hardware failure, the maintainer of this package is not liable.

Proceed at your own risk.
