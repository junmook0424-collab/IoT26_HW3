# IoT26_HW3# IoT26-HW03 - Raspberry Pi Motion Detector with Photo Capture

## Objective
Use RPI to automatically take photos when motion is detected.

## Circuit
- PIR Motion Sensor : GPIO 4
- Camera Module : CSI port (CAM0)

## How it works
1. PIR sensor detects motion
2. Camera automatically captures a photo
3. Photo saved with timestamp
4. Waits 3 seconds before next detection

## Source Code
```python
from gpiozero import MotionSensor
from picamera2 import Picamera2
from datetime import datetime
import time
import os

os.makedirs("photos", exist_ok=True)

pir = MotionSensor(4)
camera = Picamera2()

config = camera.create_still_configuration()
camera.configure(config)
camera.start()
time.sleep(2)

print("Waiting for motion... (Ctrl+C to quit)")

try:
    while True:
        pir.wait_for_motion()
        timestamp = datetime.now().strftime("%Y%m%d_%H%M%S")
        filename = f"photos/motion_{timestamp}.jpg"
        print(f"Motion detected! → {filename}")
        camera.capture_file(filename)

        time.sleep(3)
        pir.wait_for_no_motion()

except KeyboardInterrupt:
    print("Stopped")
    camera.stop()
```

## Result
![실행화면](./IoT_hw3_image.png)
![카메라사진](./IoT_hw3_camera_image.png)
![데모영상](./IoT_hw3.mp4)

## Note
SSH terminal was used to run the code remotely on Raspberry Pi 5.
Photos and videos were taken separately due to SSH connection limitations.
Camera module was connected to CAM0 CSI port.
