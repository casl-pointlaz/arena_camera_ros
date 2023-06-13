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

Follow the instructions in *~/ArenaSDK_Linux_x64/docs/html/arena_sdk_linux.html* to configure the Ethernet ports for the cameras.
