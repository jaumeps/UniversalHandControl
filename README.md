# Universal Hand Control
**Using your hand to control your connected devices and applications.**

WIP. Some parts non essential to what I wanted to demonstrate in this project, are not yet fully implemented (service messages exchanged between client and service, logging, debugging option, starting the service as a linux service, ...). Also the documentation describing how to configure Universal Hand Control on the client side is missing (mainly describing gestures, airzones and their parameters).

For now, a brief description of the files in this repository :


### `UniversalHandControlService.py`
Implements Universal Hand Control on the server side. Waiting to be contacting by a client app via MQTT, then analyzing frames from the sensor, detecting the events that the client is interesred in, and sending the events to the client via MQTT.<br>
Currently, started by : 
``` 
python UniversalHandControlService.py 
```

### `UniversalHandControl.py`
Implements Universal Hand Control on the client side. This is the code to import in your application code:
```
from UniversalHandControl import *
```

### `HandPose.py`
Called by UniversalHandControlService. This is where almost the hard and complex job is done (communicating with the sensor, analyzing the frames).
A part of the frame analysis is an adaptation in python of the code from the [Mediapipe Hands solution](https://google.github.io/mediapipe/solutions/hands.html).

### uhc_common.py
Some common classes and definitions

### `RealsenseSensor.py`
A class that manages a Realsense D4xx camera. Used by HandPose via the methods:
- next_frames() : that returns synchronized frames (currently only the color frame is directly used by HandPose). Depth information is indirectly queried via the 2 follwing methods;
- deproject() : takes a pixel position (x,y) in the color frame as argument and returns its (X,Y,Z) position in the 3D camera coordinate system. Used to get the location of the hand in space;
- project() : takes a (X,Y,Z) position in the 3D camera coordinate system as argument and returns its projection in the color image. Used to draw airzones in the image (for debugging).

### `DepthAISensor.py`
Equivalent of the previous file for the BW1092, with the same methods. Temporary version. Will modified when aligned color and depth frames will be available in depthai library.

### `TTS.py`, `TTS_win.py`
Text-to-speech function accessible to the client app via UniversalHandControl method 'say(sentence)'. TTS_win.py is the Windows version but currently does not work well.

### `SoundPlayer.py`
Playing a sound (a wav file) is accessible to the client app via UniversalHandControl method 'play(wav_file').

### `SmoothingFilter.py`
Module with a simple and a double exponential filters. Useful in a client app for instance when you want to control the computer mouse with a pad airzone. As the coordinates of the hand can be noisy, applying a filter makes the mouse less jittery.

### demo_airzones.py
A basic example that demonstrates the airzones.

## Examples
### ex01_light_switch.py
Switching on/off a light bulb via a gesture.
Uses yeelight python package.
### ex_02_remote_control_by_gestures.py
Controlling devices (light, TV, music speaker) with gestures.
Uses soco (controlling Sonos), yeelight (controlling light), RM3 (controlling TV via RM3 mini controller) python packages.
### ex_03_remote_control_by_airzones.py
Controlling devices (light, music speaker) with airzones.
Uses soco (controlling Sonos), yeelight (controlling light).
### ex04_tetris.py
Controlling the keyboard with gestures to play Tetris.
### ex05_angrybirds.py
Controlling the mouse with pad and gestures to play Angry Birds.
### ex06_synth.py
Virtual synth with pad, slider and gestures.

## Models directory
The 2 models, the palm detection model and the hand landmark model, are [Openvino IR models generated by PINTO0309](https://github.com/PINTO0309/PINTO_model_zoo/tree/master/033_Hand_Detection_and_Tracking/07_openvino). PINTO0309 converted himself the models from their [original Tensorflow Lite version](https://google.github.io/mediapipe/solutions/models.html#hands).

