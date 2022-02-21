# Python3 compatibility
This fork contains a python 3 compatible version of the ROS Odrive interface. These are the steps for installing properly:
1. First follow the instructions below to install general Odrive tools.
```
sudo pip3 install --upgrade odrive
```
2. Install ROS from source for python 3. Follow the instruction [here](https://www.miguelalonsojr.com/blog/robotics/ros/python3/2019/08/20/ros-melodic-python-3-build.html). Source the workspace inside .bashrc file.
```
source ~/ros_catkin_ws/install/setup.bash
```

3. Create another workspace & Clone this fork into your ROS workspace.
```
mkdir -p ~/python3_ws/src
git clone https://github.com/ArghyaChatterjee/odrive_ros.git
catkin_make -DPYTHON_EXECUTABLE=/usr/bin/python3.8
```

4. First login as root user; to do this type sudo -i on the command line. Then go to the directory /usr/local/lib/python3.8/dist-packages/odrive/pyfibre/fibre (if you don't see it look for something similar). In `protocol.py` add the following code:
`class ChannelBrokenException(Exception):pass`. In `__init__.py` add the following code:`from .protocol import ChannelDamagedException, ChannelBrokenException`


5. If code doesn't work now, add the following to your .bashrc file (or the corresponding filepaths on your system):
```
export PYTHONPATH=/home/__your_user_here__/python3_ws/src/odrive_ros/src/odrive_ros/:/usr/local/lib/python3.8/dist-packages/odrive/pyfibre/fibre/
```
The `/opt/ros/melodic/lib/python2.7/dist-packages` automatically comes in the `$PYTHONPATH` if the installation is from binary. This makes your life difficult by importing some packages from local repository while importing others from global binary package installed during standard ros melodic installation based on python2. This assumes installation in Linux and catkin; other installations require slightly different filepaths and commands.

# Python2 compatibility
# ARCHIVE NOTICE 2021-02-04
I have taken on a new position no longer have access to an ODrive for testing. Happy to add a maintainer role if anyone wants to step up to it, otherwise this repo will just stay up here as a reference for other implementations. 

# odrive_ros
ROS driver for the [ODrive motor driver](https://odriverobotics.com/)

This is a basic pass at a ROS driver to operate the ODrive as a differential drive. It's Python-based, so not super-fast, but it'll get you started. Maybe in time this will have an optimised C++ version, but I woudn't count on it anytime soon. ;)

Future plans include adding a simulation mode so you can continue to use this driver in a simulation mode within gazebo.

Feedback, issues and pull requests welcome.

## Usage

You will need the main ODrive Python tools installed.

To install:
```sh
git clone https://github.com/madcowswe/ODrive
cd ODrive/tools
sudo pip install monotonic # required for py < 3

# sudo python setup.py install # doesn't work due to weird setup process, so do the following:
python setup.py sdist
sudo pip install dist/odrive-xxx.tar.gz
```

then `rosrun odrive_ros odrive_node` will get you on your way. 

There is a sample launch file showing the use of the wheelbase, tyre circumference, and encoder count parameters too, try `roslaunch odrive_ros odrive.launch` or copy it into your own package and edit the values to correspond to your particular setup.

If you want to test out the driver separate from ROS, you can also install as a regular Python package.

```sh
roscd odrive_ros
sudo pip install -e .
```

If you want to use a bare Python shell, see below for the import which exposes the ODrive with setup and drive functions. You can use either ODriveInterfaceAPI (uses USB) or ODriveInterfaceSerial (requires the /dev/ttyUSBx corresponding to the ODrive as parameter, but not tested recently).

```python
from odrive_ros import odrive_interface
od = odrive_interface.ODriveInterfaceAPI()
od.connect()
od.calibrate()      # does a calibration
od.engage()         # get ready to drive
od.drive(1000,1000) # drive axis0 and axis1
od.release()        # done
```

You can now (as of v0.5) use this wrapper from within an `odrivetool shell`. Once the shell has launched and connected to your ODrive, run the following, then you can use the commands as if in a bare Python interactive session above, and still get the help provided by odrivetool.

```python
import odrive_interface
od = odrive_interface.ODriveInterfaceAPI(active_odrive=odrv0)
```


## Acknowledgements

Thanks to the ODrive team for a great little bit of hardware and the active community support. Hope this helps!

- [ODrive homepage](https://odriverobotics.com)
- [ODrive getting started](https://docs.odriverobotics.com)
- [ODrive main repo](https://github.com/madcowswe/ODrive)

Thanks to Simon Birrell for his tutorial on [how to package a Python-based ROS package](http://www.artificialhumancompanions.com/structure-python-based-ros-package/).

