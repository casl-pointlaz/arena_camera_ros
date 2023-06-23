## Arena Camera Driver for ROS1

# Getting Started
setup and usage https://support.thinklucid.com/using-ros-for-linux/

for ROS2 driver contact LUCID support for more information 
https://support.thinklucid.com/contact-support/

___
## This is the Pointlaz modified version
Last modification 2023-04-07 by CASL and Antoine Gruet.
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
    - Address = 169.254.0.1
    - Netmask = 255.255.0.0
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
15. In the **IPv4** Tab, change the **Address** by the IP Address of the camera, and change the last number by 1 (or something different from the last number of your camera IP Address). For example, if your camera IP Address is "169.254.40.215", enter the Address "169.254.40.1".
16. Change the **Netmask** by "255.255.255.0".
17. Click on **Apply** to save the changes. The window should close. 
18. Disable and enable the interface once to assign the IP address to the interface (toggle the purple button).

#### Second ToF camera (camera 1)
Without unplugging your **camera 0**, plug your **camera 1** in its Ethernet port and repeat the same steps you did with the **camera 0**, but for the **camera 1**.  
When these steps finished, your Ethernet ports configuration should be properly done.

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