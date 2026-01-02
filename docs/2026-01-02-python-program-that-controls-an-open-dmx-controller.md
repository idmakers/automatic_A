---
title: "Controlling an Open DMX Controller with Python"
tags:
  - python
  - open-dmx
  - dmx-control
---

# Controlling an Open DMX Controller with Python

## Core Problem
Controlling a DMX lighting system can be a complex task, especially when using a computer-controlled lighting console. In this solution, we will use Python to control an Enttec OpenDMX controller.

## Solution & Analysis

### Installing the Required Libraries

```python
import socket
import json
import struct
import math
import time
from PyDMXControl.controllers import OpenDMXController
```

### Setting Up the DMX Controller

```python
print("--- ZAPUSK DMX KLIENTA ---")
print("Poisk kontrollera OpenDMX...")
try:
    # Initialize the Enttec OpenDMX controller
    dmx = OpenDMXController()
    print(">>> Kontroler uspeshno poklazhen! ")
except Exception as e:
    print(f">>> Oshiba pridavanii kontrolera: {e}")
    print("Poverka, prisvozdeni USB kabely i naibolshie privilia (sudo).")
    exit()
```

### Defining the DMX Channels

```python
CH_PAN = 1
CH_TILT = 3
CH_COLOR = 5
CH_STROBE = 7
CH_DIMMER = 8

MAX_PAN_ANGLE = 540.0
MAX_TILT_ANGLE = 270.0
```

### Defining the `set_dmx_val` Function

```python
def set_dmx_val(channel, value):
    """
    Safe sending of a DMX value (0-255)
    """
    safe_val = max(0, min(255, int(value)))
    dmx.set_channel(channel, safe_val)
```

### Defining the `recvall` Function

```python
def recvall(sock, n):
    """
    Helper function to read exactly n bytes from a socket
    """
    data = bytearray()
    while len(data) < n:
        packet = sock.recv(n - len(data))
        if not packet:
            return None
        data.extend(packet)
    return data
```

### Defining the `recv_msg` Function

```python
def recv_msg(sock):
    """
    Reading a message from a socket with 4-byte header length
    """
    # 1. Read 4 bytes (header length)
    raw_msglen = recvall(sock, 4)
    if not raw_msglen:
        return None

    # 2. Unpack length as big-endian integer
    msglen = struct.unpack('>', raw_msglen)[0]

    # 3. Read the message itself
    return recvall(sock, msglen)
```

### Defining the `calculate_dmx_angles` Function

```python
def calculate_dmx_angles(cam_x, cam_y):
    """
    Calculate DMX angles from camera coordinates
    """
    # 1. Find offset from center in pixels
    if cam_x == 640:  # Center
        offset_x = 0
    else:
        offset_x = cam_x - (CAMERA_WIDTH / 2)

    # Invert Y for projector orientation
    offset_y = cam_y - (CAMERA_HEIGHT / 2)
    if offset_y < 0:  # Invert Y
        offset_y *= -1

    # 2. Calculate angles using arctangent
    angle_x = math.degrees(math.atan(offset_x / DISTANCE_TO_STAGE))
    angle_y = math.degrees(math.atan(offset_y / DISTANCE_TO_STAGE))

    # 3. Convert angles to DMX range (0-255)
    pan_dmx = 128 + (angle_x * (255 / MAX_PAN_ANGLE))
    tilt_dmx = 128 + (angle_y * (255 / MAX_TILT_ANGLE))
    return int(pan_dmx), int(tilt_dmx)
```

### Defining the `main` Function

```python
def main():
    while True:
        try:
            print(f"Connecting to stracker.py ({SERVER_IP}:{SERVER_PORT})...")
            s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
            s.connect((SERVER_IP, SERVER_PORT))
            print(">>> Connection established! Waiting for data...")
            while True:
                # Get JSON from stracker.py
                data_bytes = recv_msg(s)
                if not data_bytes:
                    print("Server closed connection.")
                    break

                # Decode JSON
                json_str = data_bytes.decode('utf-8')
                targets = json.loads(json_str)  # List [...]

                # Check for target to follow
                if len(targets) > 0:
                    target = targets[0]
                    x = target.get('CenterX', 0)
                    y = target.get('CenterY', 0)
                    state = target.get('State', 0)  # 1 - red (on), 0 - green (off)

                    # --- 1. Movement ---
                    pan, tilt = calculate_dmx_angles(x, y)
                    set_dmx_val(CH_PAN, pan)
                    set_dmx_val(CH_TILT, tilt)

                    # --- 2. Lighting control ---
                    if state == 1:
                        set_dmx_val(CH_COLOR, 0)   # White color (0-139)
                        set_dmx_val(CH_STROBE, 0)  # Strobe open/closed
                        set_dmx_val(CH_DIMMER, 255)  # Brightness max
                    else:
                        set_dmx_val(CH_DIMMER, 0)
                        set_dmx_val(CH_STROBE, 0)

                s.close()
        except ConnectionRefusedError:
            print("Cannot connect. Is stracker.py running? Try again in 3 seconds...")
            time.sleep(3)
        except Exception as e:
            print(f"Error in loop: {e}")
            time.sleep(1)
```

## Conclusion

This solution provides a basic structure for controlling an Enttec OpenDMX controller using Python. The `main` function continuously listens to the DMX protocol, calculates angles based on camera coordinates, and adjusts lighting settings accordingly.

Note that this is just a starting point, and you may need to modify the code to suit your specific requirements or lighting system configuration.