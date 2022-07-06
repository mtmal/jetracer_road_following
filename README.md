# jetracer_road_following
Modification of jetracer's road following example, where direct steering and throttle are used for training rather than manually-selected pixels.

## General Info
The original road following example requires a user to select pixels in images in interactive_regression.ipynb manually. This is a tedious and lengthy task. Furthermore, in the training process, there is no notion of the underhood locomotion, i.e. in the case of jetracer - the Ackerman steering geometry; only a user-labelled pixel is used to decide where to steer. Finally, in the inference, only steering is used with pre-set default throttle.

This code aims to improve the experience of training a car to follow the road, by simply utilising a gamepad. Rather than pixels, direct steering and throttle are used together with images in the training process. Thus, the inference provides both steering and throttle.

## Setup
Copy the content into your jetracer folder and follow the notebooks.

## Code Overview
Both provided notebooks are based on the original NVIDIA examples with the following modifications:
* the teleoperated_interactive_regression.ipynb notebook starts by creating a controller widget to command a robot. The first section sets up NvidiaRacer and CSICamera. Most notably, the camera only acquires images when the racer moves forward (negative throttle direction in the case of this example).
* in the second part of teleoperated_interactive_regression.ipynb, data is saved only when a new image is acquired, thus ony when the racer is moving. It avoids flooding the dataset with images when stationary. Also, it allows backward motion to reposition the racer if required. Note the xy_dataset_float.py which creates a tensor from floating-point steering and throttle (both are already scaled, by default, from -1 to 1).
* live execution for the training notebook is removed. It would be meaningful to overlay pixel based on steering and throttle, as its position may not overlay with the actual road (Y position is throttle controlled by the network).
* the drive_road_following.ipynb is almost the same as the original road_following.ipynb. The only difference is how the output data is handled in the last cell. Also, the model optimisation was moved to a separate notebook optimise_model.ipynb

## memory allocation in static TLS block
Sometimes, when executing code in Jupyter, the following error may occur:
<br><code>OSError: /usr/lib/aarch64-linux-gnu/libgomp.so.1: cannot allocate memory in static TLS block</code><br>
So far, I have not found any reliable solution. Even rebooting Jetson may not always solve it. Should it happen, the easies thing to do is to ssh to Jetson, run Python3 from notebook's working directory, and execute all commands there.
