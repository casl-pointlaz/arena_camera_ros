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
Follow the instructions in *~/ArenaSDK_Linux_x64/docs/html/arena_sdk_linux.html* to configure the Ethernet ports for the cameras.

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