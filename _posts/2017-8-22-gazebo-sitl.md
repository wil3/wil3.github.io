---
title: Understanding the IRIS with Standoffs Demo 
---


Proper testing of a flight controller (FC) is crucial for safety and continuous integration. 
Software in the 
loop (SITL) allows us to run an FC in a simulated environment. Many
FC firmwares support SITL including
[PX4](https://dev.px4.io/en/simulation/gazebo.html) and
[Betaflight](https://github.com/betaflight/betaflight/tree/master/src/main/target/SITL)
which allows the FC to execute on an x86 Linux machine and interact with the
[Gazebo simulator](http://gazebosim.org/).
In this write up we will be looking specifically at Betaflight.


# Betaflight SITL 
When the firmware is [complied](http://gazebosim.org/tutorials?tut=components) and run for SITL a TCP 
server is started to receive [MultiWii commands](http://www.multiwii.com/wiki/index.php?title=Multiwii_Serial_Protocol), and a UDP server to communicate with Gazebo 
through a Gazebo plugin called ArduCopterPlugin.  

We can also connect to the FC using the configurator by selecting a manual 
connection and adding,
```
tcp://127.0.0.1:5761
```
as the Port value.

To send MultiWii commands over this channel I added [TCP
support](https://github.com/wil3/pyMultiWii/tree/feature-tcp) to the
[pyMultiWii](https://github.com/alduxvm/pyMultiWii) Python module. Using this
API we can then send RC commands to the FC and control the drone in a simulated
environment.  


Before we send commands to the FC we need to get the quadcopter running in the simulated environment. First start Gazebo,
```
cd /usr/share/gazebo-8/worlds
gazebo --verbose ./iris_arducopter_demo.world
```

Then from your BetaFlight source start the FC in SITL,
```
./obj/main/betaflight_SITL.elf
```

You will know the Betaflight FC has connected to the Gazebo ArduCopter if you
see the following in the Gazebo output,
```
[Dbg] [ArduCopterPlugin.cc:573] ArduCopter controller online detected.
```

# Demo Explained

A world file is an XML document defining a world including the physical objects 
it contains, lighting and the physics engine. The XML file uses the [SDF 
format](http://sdformat.org/) which is a popular  way to describe robotic simulators.

In the file `/usr/share/gazebo-8/worlds/iris_arducopter_demo.world`
there is a model that is loaded called, `iris_with_standoffs_demo`.  
This model  is included with the Gazebo installation,  its source code is
available 
[here](https://bitbucket.org/osrf/gazebo_models/src/73f89ecb1622/iris_with_standoffs_demo/?at=default). This model actually only contains two files, one of 
which is an SDF file. This SDF file assembles the quadcopters parts such as the
frame, gimbal and rotors as well as loading the all
the necessary plugins (libArduCopterPlugin, and libLiftDragPlugin).

The `iris_with_standoffs` model is the  quadcopter frame/body which exists
[here](https://bitbucket.org/osrf/gazebo_models/src/73f89ecb1622f0bce7f9da3827958c40e62fb83f/iris_with_standoffs/?at=default).
`iris_with_standoffs` contains the physical characteristics of 
the quadcopter including the 3D mesh files.  

As for the plugins, the
[libLiftDragPlugin](http://gazebosim.org/tutorials?tut=aerodynamics&cat=plugins) provides the ability to simulate the
aerodynamics of the quadrotor. 
The ArduCopterPlugin is included with Gazebo, and provides the network interface
to allow the FC to communicate with Gazebo. Its source code is available
[here](https://bitbucket.org/osrf/gazebo/src/7343f9d4ec32d83ed4d792a4ad9a639362da568b/plugins/ArduCopterPlugin.cc).


# Modifications
Now that we've walked through all the pieces needed for Betaflight SITL we will want to make some modifications to either our
world, or quadcopter, after all everything is open source!  Thus far everything is included in the Gazebo
installation (obviously excluding Betaflight).
Gazebo exposes some [environment
variables](http://gazebosim.org/tutorials?tut=components) allowing us to specify
our own locations for models, worlds, plugins, etc. So  set these environment
variables, clone/fork these repos, and get hacking! 


