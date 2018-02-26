# Lab 3: Wall Following on the Racecar

It's time to use the actual racecar!
In this lab you will be taking the wall following code that you ran in simulation and running it on the car.
You will also be building a safety controller to prevent your racecar from crashing into obstacles.

## Disclaimer

The racecar platform is exciting and fast but it is not a toy.
The hardware we have on board
[is](https://www.amazon.com/NVIDIA-Jetson-TX2-Development-Kit/dp/B06XPFH939)
[extremely](https://www.spar3d.com/news/lidar/velodyne-cuts-vlp-16-lidar-price-4k/)
[expensive](https://www.robotshop.com/en/hokuyo-ust-10lx-scanning-laser-rangefinder.html?gclid=Cj0KCQiAq6_UBRCEARIsAHyrgUxYmgjfz734t-zWCqa2U4l7LAVsZ1_cp2CuvuD3WalcBQ9tCp2_WmMaAjbAEALw_wcB)
and it is your responsibility to keep it in good condition for future classes.
The racecar can survive a couple light bumps but if it goes flying into a wall it can be destroyed. The whole frame can split in half, the lidar can get scratched, the TX2 can get damaged, etc. Any one of these repairs can cost hundreds if not thousands of dollars.

If your car develops hardware issues for any reason, please tell the TAs and we will replace your car and then refurbish the damaged one. Most teams will probably have some sort of hardware issue throughout the course and it is typically not a big deal.
But if you damage the car in an extreme way through obviously reckless behaviour you may find yourself working on the simulated car for the rest of the course.

## Electrical Safety

The racecar runs on relatively low voltage ([≤ 20V](https://www.amazon.com/Energizer-XP18000-Universal-External-Netbooks/dp/B002K8M9HC)) so we are not too concered about dangerous shocks.
But as with any electrical system it is always important to take proper safety precautions.
For example ground yourself before touching any of the exposed circuit boards like the TX2.

Please have all members of your team read and sign the electrical safety form here before starting work on the racecar:
https://eecs-ug.scripts.mit.edu:444/safety/index.py/6.141

## The Racecar

### Connections and Power

Once you have your, car search for its number. You can find it in two places; on top of the car's lidar and on the front of your router. The number will be in block letter stickers. If you have an older car or router there might be other numbers written or labeled on it that you can ignore.

![car_number](https://farm5.staticflickr.com/4723/40500596821_e133bedd83_k.jpg)

Plug your router into an outlet and use the ethernet cable to connect it to a working ethernet port (not all the ports in 32-080 work). 
Then connect to either of these two wifi networks on your laptop using the password ```g0_fast!```:

    RACECAR_AP_[YOUR_CAR_NUMBER]
    RACECAR_AP_[YOUR_CAR_NUMBER]_5GHZ

The 5ghz network provides a faster connection but has more limited range.

TODO: picture of router with adapter

Check the battery status on your racecar by pressing the power button on your black energizer battery. 
This power button is merely an indicator and does not actually power the battery on or off.
It is always on, so make sure you unplug the power cables when the car is not in use.
On the hokuyo cars the battery sits right on top of the car.
On the velodyne cars the battery is velcroed under the car (be careful when pulling it out).

![hokuyo_battery](https://farm5.staticflickr.com/4628/40500597871_792493a139_k.jpg)
![velodyne_battery](https://farm5.staticflickr.com/4659/39604959195_914cb8f59f_k.jpg)

If your energizer is low on battery, charge it with the adapter.
Note that the car will probably turn off when you disconnect the power adapter; during the switch back to battery power there is a moment where the TX2 does not have enough power to stay on.
You will need to disconnect the car from the power adapter when you want to drive it around.
The battery lasts a suprisingly long time, so as long as you keep the battery charged when you are not working it can last the entire lab.

![energizer_power](https://farm5.staticflickr.com/4761/39604959385_d248f64e8d_k.jpg)

Also charge your motor battery by plugging it into the charger that looks like a blue block.
Hold the start button to charge.
This battery won't last as long, especially when you are going fast, so remember to charge it when the car is not moving. The TX2 will not be affected if the motor battery gets unplugged. We have given each team two motor batteries so you can swap them out.

![motor_power](https://farm5.staticflickr.com/4750/39790637494_e1ef9b0292_k.jpg)


Connect the two power cables to the energizer battery.
One powers the lidar and the TX2. 
The other powers the USB hub (which powers the ZED camera and IMU).
If everything is receiving power, you should see LEDs light up on the TX2 and IMU and you should hear the lidar spinning (listen closely).

![energizer_plugged](https://farm5.staticflickr.com/4626/39604959525_44ff049f74_k.jpg)

Power on the TX2 by pressing the rightmost button on the port side of the car labeled "power".
The button should light up green.

![TX2](https://farm5.staticflickr.com/4709/40500596601_71f9b0ede8_k.jpg)

### SSH

With everything powered on, you can connect directly to the car using:

    ssh racecar@192.168.0.[CAR_NUMBER]
        
The password is ```racecar@mit```. If you can't connect make sure you are still on the correct Wi-Fi network.

The car is running Ubuntu just like the virtual machine.
It should be familiar, but poke around to get comfortable with the structure.
Just like in the simulator, you will often need multiple terminal windows open in order to launch different ros nodes.
There are many ways to do this through ```ssh```:

- Open multiple windows on your local machine and ```ssh racecar@192.168.0.[CAR_NUMBER]``` in each one of them. You can even ssh from multiple computers at the same time but make sure you are communicating with your team members if you do this.
- Use [screen](https://kb.iu.edu/d/acuy) to open layered windows in terminal and navigate through them with key commands.
- Use ```ssh``` with the ```-X``` flag to enable X11 forwarding. With this flag you can launch graphical programs in the ```ssh``` client and have them displayed on your local machine. For example you could run ```xterm &``` to get a new terminal window. 

### Manual Navigation

When you are ready, disconnect the power adapters to the energizer and motors and plug the motors in.

![motor_plugged](https://farm5.staticflickr.com/4616/39604958785_8e8161b88e_k.jpg)

Turn on the TX2 and recconect to the racecar if necessary.
Get the car to a safe place (_not on a table!_) and launch teleop just like in the simulator:

    teleop

Now you should be able to move the car around with the joystick!
**You need press the left bumper before the car can move, *even for autonomous driving*.**
This way you can stop the car from crashing by letting go of the trigger if necessary.

Your computer will disconnect from the racecar if it gets too far away from the router, but the code running on it will not be affected.

#### The car isn't moving

- Make sure the joystick is connected and in the right mode by running ~/joystick/test_joystick.sh
- Are you pressing the left bumped on the joystick?
- Make sure the motors are plugged in and charged. 

### RViz

Because ```rviz``` requires 3D libraries you can't run it straight through SSH. So to visualize topics published by the car run the following:

    runcar [YOUR_CAR_NUMBER] rviz

This command sets the ROS master to the ip of the car. Add the ```/scan``` topic and make sure you can visualize the laser scan.

### Cleaning Up

Before you get too far ahead, remember that when you are done using the racecar, you **must unplug all power cables**. This includes 2 cables that connect to the energizer battery and the motor battery. Not doing this can destroy the batteries and the servo.

![motor_unplugged](https://farm5.staticflickr.com/4654/39604958985_bd32f3ea16_k.jpg)

## Wall Following

Use ```scp``` or ```git clone``` to get one of your team members' wall following code from Lab 2 onto the car.
Just like in Lab 2 the wall follower should live in the ```src``` folder of your workspace, ```~/racecar_ws/src/[WALL_FOLLOWER_CODE]```.
```catkin_make``` in the root of your workspace to rebuild it.
Get the car into a safe location and make sure ```teleop``` is running. In another terminal launch

    roslaunch wall_follower wall_follower.launch
    
Hopefully this will work without any changes!
If nessesary, tune the parameters in the wall follower so that it works well in real life.
Combine ideas from multiple team members' implimentations of the wall follower to make a more robust controller.

Consider how to quantify how well a controller performs, and techniques to improve controller performance.

## Safety Controller

Now that you’ve got your wall follower working we want you to build a safety controller.
In future labs the racecar will be moving at high speeds so we need you to build a system that protects it from crashes. 

Create a new package for your safety controller (place it in ```~/racecar_ws/src```).
Your goal is to make a node in this pacakge that prevents the racecar from crashing into obstacles.

We want you to be able to demonstrate that your safety controller is robust. You should be able to attempt to crash the racecar in a variety of senarioes and have the safety controller prevent the crashes. You should also be able to walk in front of the racecar without it running into you. 

At the same time your racecar should not be "scared". You should still be able to drive close to walls, turn around corners, go fast etc. without the racecar freezing in it's tracks. You will be required to run your safety controller in all future labs so don't cripple yourself with something overprotective.

__Please be careful when you are testing__. Always have your joystick ready to stop the racecar and start very slow. 

### Muxes

The racecar has a command mux with different levels of priority that you will need in building your safety controller.

![Muxes](https://i.imgur.com/Y8oQCLe.png)

The navigation topic you have been publishing to is an alias for the highest priority navigation topic in the mux ([defined here](https://github.mit.edu/2018-RSS/racecar_base_ros_install/blob/vm/racecar/racecar/launch/mux.launch)):

    /vesc/ackermann_cmd_mux/input/navigation -> /vesc/high_level/ackermann_cmd_mux/input/nav_0

For brevity we will refer to ```/vesc/high_level/ackermann_cmd_mux/input/nav_i``` as ```.../nav_i``` in this handout (_this doesn't work on the actual racecar_).
Driving commands sent to ```.../nav_0``` override driving commands sent to ```.../nav_1```, ```.../nav_2```, etc.
Likewise driving commands sent to ```.../nav_1``` override driving commands sent to ```.../nav_2```, ```.../nav_3```, etc.
You can use this structure to layer levels of control.

For example, a robot whose job it is to explore randomly and collect minerals as it finds them could use 2 muxes.
The controller that explores randomly could publish to a lower priotiy topic like ```.../nav_1```.
Whenever the vision system detects minerals, it could begin to publish commands to a higher priority topic like ```.../nav_0```. ```.../nav_0``` would override ```.../nav_1``` until the minerals have been depleted and commands stopped being published to```.../nav_0```.

The navigation command with the highest priority is then published to ```/vesc/high_level/ackermann_cmd_mux/output```.
This topic is then piped to ```/vesc/low_level/ackermann_cmd_mux/input/navigation``` and fed into another mux with the following priorities (from highest to lowest):

    /vesc/low_level/ackermann_cmd_mux/input/teleop
    /vesc/low_level/ackermann_cmd_mux/input/safety
    /vesc/low_level/ackermann_cmd_mux/input/navigation

```.../teleop``` is the topic that the joystick publishes to.
This will always have the highest priority.
```.../safety``` has the next highest priority. It will override anything published to ```.../navigation```. This is where your safety controller will publish.

So for your safety controller this means:

- Subscribe to ```/vesc/high_level/ackermann_cmd_mux/output``` to intercept the driving command that is being published.
- Subscribe to sensors like ```/scan```.
- Publish to ```/vesc/low_level/ackermann_cmd_mux/input/safety``` if the command being published to the navigation topic is in danger of crashing the racecar.

## Deliverables

From now on your lab deliverables will consist of two parts; a presentation and a lab report.
Make sure that in each you demonstrate your ability to 

- Log into the physical car and manually drive.
- Autonomously drive the racecar with your wall following code.
- Prevent crashes using your safety controller while maintaining flexibility.

Use of video, screen shots, etc. is highly recommended. Make quantitative and qualitative evaluations of your resuls.
