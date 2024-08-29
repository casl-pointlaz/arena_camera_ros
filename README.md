## Arena Camera Driver for ROS1

# Getting Started
setup and usage https://support.thinklucid.com/using-ros-for-linux/

for ROS2 driver contact LUCID support for more information
https://support.thinklucid.com/contact-support/

___
## This is the Pointlaz modified version

Last modification 2024-08-29 by Alexandre Guénette.

### How to update this version

Since the PointLaz modifications are contained on a branch, it is possible to isolate the modifications from the update.
- While working on this fork, start by checking out on the master branch
```
git checkout ros1_master
```
- Then sync the fork with the update source version by using the method of your choice (see https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/working-with-forks/syncing-a-fork)
- Then checkout back to the Pointlaz branch and merge the updated master into the branch.
```
git checkout Pointlaz
git merge ros1_master
git push
```
- It's possible that the merge doesn't complete automatically depending on the complexity of Pointlaz's modifications. In this case, be prepared to do a manual merge. You can use the method of your choice, by example using CLION: check https://www.jetbrains.com/help/clion/resolving-conflicts.html#distributed-version-control-systems

## Time of Flight cameras configuration

### Ethernet configuration

Follow the next instructions to configure the Ethernet ports for the cameras.
The next instructions have been taken from *~/ArenaSDK_Linux_x64/docs/html/arena_sdk_linux.html* and adapted.
Follow the steps from **Ethernet Adapter Configuration**, **Installing the Ethernet Driver**, **Jumbo Frames**, and **Receive Buffers** from *~/ArenaSDK_Linux_x64/docs/html/arena_sdk_linux.html*.
Once you reach the title **IP Address Assignment**, do not follow the steps from *~/ArenaSDK_Linux_x64/docs/html/arena_sdk_linux.html*, and follow the next steps instead.

#### First ToF camera (camera 0)

1. Unplug all your ToF cameras before starting the configuration.
2. On your computer, click on the arrow down at the top right of your screen.
3. Then click on **Settings**. A **Settings** window should open.
4. On the **Settings** window, go to **Network**. You should see all the Ethernet Ports of your computer under **Wired**.
5. Plug your **camera 0** into its Ethernet port. You should see one Ethernet port toggle button connecting (the button turn to purple).
6. Click on the "gear" icon of this Ethernet Port. A new window should open.
7. Choose the **IPv4** Tab, then choose **Manual** for **IPv4 Method**.
8. Then, in **Adresses** list, fill the first line with:
    - Address = `169.254.0.1`
    - Netmask = `255.255.0.0`
    - Leave Gateway empty
9. Click on **Apply** to save the changes. The window should close.
10. Disable and enable the interface once to assign the IP address to the interface (toggle the purple button).
11. In a terminal, run the next commands:

```
cd ~/ArenaSDK_Linux_x64/Utilities
./IpConfigUtility /list
```

12. You should see the echo of a list, containing one item. It is your **camera 0**.
13. Note the IP of the camera, and return to the **Settings/Network** window.
14. Click the "gear" icon of the Ethernet port of your **camera 0**.
15. In the **IPv4** Tab, change the **Address** by the IP Address of the camera, and change the last number by 1. For example, if your camera IP Address is `169.254.40.215`, enter the Address `169.254.40.1`.
16. Click on **Apply** to save the changes. The window should close.
17. Disable and enable the interface once to assign the IP address to the interface (toggle the purple button).
18. Return to the terminal and run the next command to force the new IP address `192.168.4.10` to your camera:

```
./IpConfigUtility /force -i 0 -a 192.168.4.10 -s 255.255.255.0 -g 0.0.0.0
```

19. Wait for the process to finish and press enter when asked.
20. Return to your Network settings and click the "gear" icon of the Ethernet port of your **camera 0**.
21. In the **IPv4** Tab, change the **Address** to `192.168.4.1`.
22. Change the **Netmask** to `255.255.255.0`.
23. Click on **Apply** to save the changes. The window should close.
24. Disable and enable the interface once to assign the IP address to the interface (toggle the purple button).
25. Return to the terminal and run the next command to set the IP address to persistent:

```
./IpConfigUtility /persist -i 0 -p true -a 192.168.4.10 -s 255.255.255.0 -g 0.0.0.0
```

26. Wait for the process to finish and press enter when asked.
27. The Ethernet configuration of your **camera 0** is completed.

#### Second ToF camera (camera 1)

DIsable to Ethernet port of your **camera 0** or unplug it, plug your **camera 1** in its Ethernet port and repeat the same steps you did with the **camera 0**, but for the **camera 1** with the addresses `192.168.5.1` and `192.168.5.10`.
When these steps finished, enable the Ethernet port of your **camera 0** and your Ethernet ports configuration should be properly done. You can verify it using:

```
./IpConfigUtility /list
```

### Device User ID configuration to run multiple ToF at the same time

To differentiate the cameras, the ROS node use the Device User ID of each camera.
This ID has to be set manually by the user. The node **write_device_user_id_to_camera** has been made specially for this.
Follow the next steps to configure the Device User ID of your cameras:
1. We consider the Ethernet configuration of your cameras is properly done.
2. Identify the camera you want to be the **number 0** and the one you want to be the **number 1**.
3. Disconnect the Ethernet port of the **camera 1** (either manually or by clicking on the arrow down at the top right of your screen, **Settings**, **Network** and then toggling the purple button of the Ethernet port).
4. Run the next command:
```
 rosrun arena_camera write_device_user_id_to_camera "camera_ToF_0"
```
5. Reconnect your **camera 1**.
6. Disconnect your **camera 0**.
7. Run the next command:
```
 rosrun arena_camera write_device_user_id_to_camera "camera_ToF_1"
```
8. Reconnect your **camera 0**.
9. The Device User ID of your cameras should be properly configured. You can now run the next command to run 2 ToF cameras at the same time:
```
 roslaunch arena_camera multi_arena_camera_node.launch
```

### Launch files

Even if multiple launch exist in */arena_camera_ros/arena_camera/launch*. Only 2 are useful for us:
- **multi_arena_camera_node.launch**: To operate two ToF cameras using ROS.
- **arena_camera_node.launch**: To operate one ToF camera using ROS.

The parameters of the cameras for either launch files can be tune from the config file */arena_camera_ros/arena_camera/config/**default.yaml***


#### Possible configurations

Here are the 3 different behaviors for the ToF image acquisition synchronisation and how to tune the parameters to reach them
(the other parameters values do not matter for the image acquisition synchronisation):

- **ToF image acquisition synchronised on a physical trigger sent at 10Hz:**
   - acquisition_mode: "Continuous"
   - trigger_selector: "FrameStart"
   - trigger_mode: "On"
   - trigger_source: "Line0"
   - trigger_activation: "RisingEdge"
   - trigger_delay: 0.0

- **ToF image acquisition synchronised on a Software Trigger sent at 10Hz (standard node behavior):**
   - acquisition_mode: "Continuous"
   - trigger_selector: "FrameStart"
   - trigger_mode: "On"
   - trigger_source: "Software"
   - trigger_activation: "RisingEdge"
   - trigger_delay: 0.0

- **ToF image acquisition synchronised on the camera internal timer at 10Hz:**
   - frame_rate: 10.0
   - acquisition_mode: "Continuous"
   - trigger_mode: "Off"
