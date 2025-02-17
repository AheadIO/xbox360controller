# xbox360controller
> A pythonic Xbox360 controller API built on top of the `xpad` Linux kernel driver.

[![PyPI](https://img.shields.io/pypi/v/xbox360controller)](https://pypi.org/project/xbox360controller/)
![Python Version](https://img.shields.io/pypi/pyversions/xbox360controller)
[![Downloads](https://pepy.tech/badge/xbox360controller)](https://pepy.tech/project/xbox360controller)
[![License](https://img.shields.io/github/license/linusg/xbox360controller?color=d63e97)](https://github.com/linusg/xbox360controller/blob/master/LICENSE)
[![Black](https://img.shields.io/badge/code%20style-black-000000)](https://github.com/ambv/black)
[![Travis CI](https://api.travis-ci.org/linusg/xbox360controller.svg?branch=master)](https://travis-ci.org/linusg/xbox360controller)
[![Issues](https://img.shields.io/github/issues/linusg/xbox360controller)](https://github.com/linusg/xbox360controller/issues)

This Python Package aims to provide a pythonic and complete API for your Xbox360 and similar game controllers.
Currently it's built on top of the Linux kernel driver `xpad` so you can use it on almost any Linux distribution including your Rasperry Pi projects etc.

The following features are supported:

- Registering callbacks for **all** Buttons, Axes, Triggers and Hat in a `gpiozero`-inspired way
- Setting the LED circle; all `xpad` provided options are possible: blinking, rotating, setting individual LEDs on and off, ...
- Rumbling, both the left and right side can be controlled from 0 to 100 percent

## Installation

You will need Python 3.4 or above.

Any Linux distribution:

```
pip3 install -U xbox360controller
```

You might also use a _virtual environment_ or do a per-user install by providing the `--user` flag to above command.
Global installations might require the usage of `sudo` or working directly from a root shell but are **not recommended**.

If the `pip3` command cannot be found, try `pip` or make sure to have pip installed properly:

```
sudo apt install python3-pip
```

Of course you don't need `sudo` when working from a root shell.

## Usage

### Basics

```python
import signal
from xbox360controller import Xbox360Controller

with Xbox360Controller() as controller:
    controller.info()


def on_button_pressed(button):
    print('Button {0} was pressed'.format(button.name))


def on_button_released(button):
    print('Button {0} was released'.format(button.name))


def on_axis_moved(axis):
    if hasattr(axis,'x'):
        print('Axis {0} moved to {1} {2}'.format(axis.name, axis.x, axis.y))
    else:
        print('Axis {0} moved to {1}'.format(axis.name, axis.value))


def on_axis_released(axis):
    if hasattr(axis,'x'):
        print('Axis {0} released to {1} {2}'.format(axis.name, axis.x, axis.y))
    else:
        print('Axis {0} released to {1}'.format(axis.name, axis.value))

try:
    with Xbox360Controller(0, axis_threshold=0.2, raw_mode=False, event_timeout=0.2) as controller:
        # Buttons Events
        controller.button_a.when_pressed = on_button_pressed
        controller.button_a.when_released = on_button_released
        
        controller.button_b.when_pressed = on_button_pressed
        controller.button_b.when_released = on_button_released
        
        controller.button_x.when_pressed = on_button_pressed
        controller.button_x.when_released = on_button_released

        controller.button_y.when_pressed = on_button_pressed
        controller.button_y.when_released = on_button_released

        controller.button_bumper_l.when_pressed = on_button_pressed
        controller.button_bumper_l.when_released = on_button_released

        controller.button_bumper_r.when_pressed = on_button_pressed
        controller.button_bumper_r.when_released = on_button_released
                
        controller.button_xbox.when_pressed = on_button_pressed
        controller.button_xbox.when_released = on_button_released

        controller.button_menu.when_pressed = on_button_pressed
        controller.button_menu.when_released = on_button_released

        controller.button_view.when_pressed = on_button_pressed
        controller.button_view.when_released = on_button_released

        # Left and right axis move event
        controller.axis_l.when_moved = on_axis_moved
        controller.axis_l.when_released = on_axis_released

        controller.axis_r.when_moved = on_axis_moved
        controller.axis_r.when_released = on_axis_released
        
        # Stick move event
        controller.stick_l.when_moved = on_axis_moved
        controller.stick_l.when_released = on_axis_released

        controller.stick_r.when_moved = on_axis_moved
        controller.stick_r.when_released = on_axis_released

        # Pad move event
        controller.hat.when_moved = on_axis_moved
        controller.hat.when_released = on_axis_released

        signal.pause()
except KeyboardInterrupt:
    pass
```

The above code will run until `Ctrl+C` is pressed. Each time on of the left or right axis is moved, the event will be processed. Additionally, the events of the A button are being processed.

See the [API reference](https://github.com/linusg/xbox360controller/blob/master/docs/API.md#xbox360controller-parameters) for a more detailed explanation of the `Xbox360Controller` class and how to use all available buttons, axes and the hat.

### Rumbling

```python
import time
from xbox360controller import Xbox360Controller

with Xbox360Controller() as controller:
    controller.set_rumble(0.5, 0.5, 1000)
    time.sleep(1)
```

This will enable rumble on both sides of the controller with each 50% strength for one second (1000ms). Note that the method call is non-blocking, thus we need to manually wait one second for the rumble to finish. You won't need this in a regular use case with `signal.pause()`.

### LED

```python
import time
from xbox360controller import Xbox360Controller

with Xbox360Controller() as controller:
    controller.set_led(Xbox360Controller.LED_ROTATE)
    time.sleep(1)
    controller.set_led(Xbox360Controller.LED_OFF)
```

This will let the LED circle rotate for one second and then turn it off.

See the [API reference](https://github.com/linusg/xbox360controller/blob/master/docs/API.md#led) for all available LED modes.

### Debug information

```python
from xbox360controller import Xbox360Controller

with Xbox360Controller() as controller:
    controller.info()
```

The output may look like this:

```
Microsoft X-Box 360 pad at index 0
Axes: 5
	axis_l
	axis_r
	hat
	trigger_l
	trigger_r
Buttons: 11
	button_a
	button_b
	button_x
	button_y
	button_trigger_l
	button_trigger_r
	button_select
	button_start
	button_mode
	button_thumb_l
	button_thumb_r
Rumble: yes
Driver version: 2.1.0 1.0.1
```

## Development/contributing

This project is now in a somewhat stable state, and I really appreciate all kinds of contributions - may it be new or improved code, documentation or just a simple typo fix.
Just provide me a PR and I'll be happy to include your work!

For feature requests, general questions or problems you face regarding this package please [open an issue](https://github.com/linusg/xbox360controller/issues/new).

## Release History

Please see [`CHANGES.md`](https://github.com/linusg/xbox360controller/blob/master/CHANGES.md) for a complete release history.

## Authors

- Linus Groh ([**@linusg**](https://github.com/linusg/)) – mail@linusgroh.de

Thanks to [**@VidyaPuri**](https://github.com/VidyaPuri/) and [**@jennib3**](https://github.com/jennib3/) for pointing out installation issues and providing a fix!

## License

All the code and documentation are distributed under the MIT license. See [`LICENSE`](https://github.com/linusg/xbox360controller/blob/master/LICENSE) for more information.
