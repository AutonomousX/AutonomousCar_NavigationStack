# AutuonomousCar_NavigationStack

This project was developed as a part of CSE668 Advanced Robotics course taken under prof. Nils Napp.

![proj1](https://user-images.githubusercontent.com/34932185/51647668-b919e100-1f4b-11e9-90dd-e26e08419bf5.PNG)

![proj2](https://user-images.githubusercontent.com/34932185/51647671-bdde9500-1f4b-11e9-9569-7a288efe572b.PNG)

![proj3](https://user-images.githubusercontent.com/34932185/51647678-c3d47600-1f4b-11e9-9db4-678e762ec880.PNG)

# Summary

UB’s Self Driving car is developed by DataSpeed and AutonomouStuff. DataSpeed not only
provides the modules to work with the car but also provides a ROS simulation framework to test
on the simulated car before trying on the real car.

We made use of DataSpeed simulation framework to test out the VLP - 16 LiDAR provided with
the car (both real and simulated). To achieve the same we had two major challenges:
1. Create Gazebo world
2. Navigate autonomously on the Gazebo world

To create the Gazebo world we start with placing Google Satellite image on the ground plane
for the area in concern. Next we place the in-built 3D Gazebo objects - closest available
alternatives to the original objects work fine for our goal.

To navigate autonomously, we needed to set up the navigation stack.
Navigation Stack steps:
1. Create map by driving the car around manually - This is the map the car creates (and
visualized in Rviz) after observing the world through it’s sensors (VLP-16 LiDAR in this
case).
2. The car is next localized
3. Using the map, and path planning module the car can be navigated from its current
location to a specified location in the map.

# Challanges faced in building the simulation system

1. ORB-SLAM 2: Sparse Representation
Description : As ORB-SLAM 2 provides sparse representation of point clouds, it is difficult
for slam algorithm to generate map using those point cloud as there won’t be proper 3d
objects being represented.
Solution : Google Maps API as ground frame

2. HectorSLAM requires /LaserScan, but dbw provides velodyne/pointcloud2
Description : As the Hector slam requires Laserscan as the input to process, it is
important to convert the pointcloud(from dbw simulation) to laserscan. This was
achieved using but_velodye_proc package
Solution : but_velodyne_proc

3. Facing issues in HectorSLAM Map generation
Description : As we were using open world as our simulation, it was difficult to extract
sufficient features in order to build the map. So we manually placed some objects in the
world and we were successfully able to build the map.
Solution : While navigating the autonomous car, we can think of making pavements and
poles for the road, so that can serve as features to keep the car on the road.

# Final Result: Achieved rviz map using Hector slam 

# Future Work 
Now that we have done mapping, we can go for path planning using navigation stack.
We can further improve the gazebo world, by building the pavements along the road and try to
navigate the car from one point to another, without going off the road.

# How to run

1. Run the dbw_mkz_gazebo/launch/closed_joy_ub.launch
2. Build the but_velodyne_package.
3. Run the laserscan_node.launch file from 'but_velodyne_proc' package(to convert
Pointcloud to Laserscan).
4. Build the hector_slam package
5. Run the /hector_slam/hector_mapping/launch/mapping_defaut.launch file
6. Drive the car around in gazebo and map would be formed in the rviz.
7. See the link below to video illustrating the above steps.

https://drive.google.com/file/d/10rXFgINGAYj2-lKGeZGJ0qQ3QYkR9gU_/view?usp=sharing
