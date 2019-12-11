# SFND 2D Feature Tracking

<img src="images/keypoints.png" width="820" height="248" />

The idea of the camera course is to build a collision detection system - that's the overall goal for the Final Project. As a preparation for this, you will now build the feature tracking part and test various detector / descriptor combinations to see which ones perform best. This mid-term project consists of four parts:

* First, you will focus on loading images, setting up data structures and putting everything into a ring buffer to optimize memory load. 
* Then, you will integrate several keypoint detectors such as HARRIS, FAST, BRISK and SIFT and compare them with regard to number of keypoints and speed. 
* In the next part, you will then focus on descriptor extraction and matching using brute force and also the FLANN approach we discussed in the previous lesson. 
* In the last part, once the code framework is complete, you will test the various algorithms in different combinations and compare them with regard to some performance measures. 

See the classroom instruction and code comments for more details on each of these parts. Once you are finished with this project, the keypoint matching part will be set up and you can proceed to the next lesson, where the focus is on integrating Lidar points and on object detection using deep-learning. 

## Solution
* MP.1 Data Buffer Optimization
	I just erase the first index of dataBuffer if vector size exceeds dataBufferSize

* MP.2 Keypoint Detection
	Harris corner detection code is from previous excercise. FAST, BRISK, ORB, AKAZE, and SIFT goes under detKeypointsModern.

* MP.3 Keypoint Removal
	This is done to make it easier to debug and/or to compare results better. All the keypoints are erased except inside the box. Also, erase function erases the current index and the next index replaces it. Therefore, current index needs to be visited again.

* MP.4 Keypoint Descriptors
	Similar to previous excercise, I set up BRIEF, ORB, FREAK, AKAZE, and SIFT descriptors.

* MP.5 Descriptor Matching
	This was done in previous excercise. (descriptor_matching.cpp)

* MP.6 Descriptor Distance Ratio
	This was done in previous excercise. (descriptor_matching.cpp)

## Dependencies for Running Locally
* cmake >= 2.8
  * All OSes: [click here for installation instructions](https://cmake.org/install/)
* make >= 4.1 (Linux, Mac), 3.81 (Windows)
  * Linux: make is installed by default on most Linux distros
  * Mac: [install Xcode command line tools to get make](https://developer.apple.com/xcode/features/)
  * Windows: [Click here for installation instructions](http://gnuwin32.sourceforge.net/packages/make.htm)
* OpenCV >= 4.1
  * This must be compiled from source using the `-D OPENCV_ENABLE_NONFREE=ON` cmake flag for testing the SIFT and SURF detectors.
  * The OpenCV 4.1.0 source code can be found [here](https://github.com/opencv/opencv/tree/4.1.0)
* gcc/g++ >= 5.4
  * Linux: gcc / g++ is installed by default on most Linux distros
  * Mac: same deal as make - [install Xcode command line tools](https://developer.apple.com/xcode/features/)
  * Windows: recommend using [MinGW](http://www.mingw.org/)

## Basic Build Instructions

1. Clone this repo.
2. Make a build directory in the top level directory: `mkdir build && cd build`
3. Compile: `cmake .. && make`
4. Run it: `./2D_feature_tracking`.

## Benchmark

#### Number of Keypoints for 10 Images for Vehicle

| Detectors | Number of Key-points |
| :-------: | :------------------: |
| SHITOMASI |         1179         |
|  HARRIS   |          248         |
|   FAST    |         1491         |
|   BRISK   |         2762         |
|    ORB    |         1161         |
|   AKAZE   |         1670         |
|   SIFT    |         1386         |



#### Number of Matched Keypoints for 10 Images

| Detectors\Descriptors | BRISK |  BRIEF  |      ORB      | FREAK | AKAZE | SIFT |
| :-------------------: | :---: | :-----: | :-----------: | :---: | :---: | :--: |
|       SHITOMASI       |  767  |   944   |      908      |  768  |  N/A  | 1067 |
|        HARRIS         |  142  |   173   |      162      |  144  |  N/A  | 163  |
|         FAST          |  899  |   1099  |      1071     |  878  |  N/A  | 1047 |
|         BRISK         |  1570 |   1704  |      1514     |  1524 |  N/A  | 1657 |
|          ORB          |  751  |   545   |      763      |  420  |  N/A  | 765  |
|         AKAZE         |  1215 |   1266  |      1182     |  1187 |  1259 | 1274 |
|         SIFT          |  592  |   702   | Out of Memory |  593  |  N/A  | 803  |

* KAZE/AKAZE descriptors will only work with KAZE/AKAZE keypoints. This is because they store certain information in the class_id field of keypoints, which is then retrieved when computing descriptors. All other detectors set it to -1 (unused).

Bottom line, you shouldn't mix KAZE/AKAZE with other algorithms.
* In the matching step, the BF approach is used with the descriptor distance ratio set to 0.8.



#### Keypoint Detection and Descriptor Extraction Time (in ms)

| Detectors\Descriptors |  BRISK  |    BRIEF    |      ORB      |  FREAK  |  AKAZE  |    SIFT    |
| :-------------------: | :-----: | :---------: | :-----------: | :-----: | :-----: | :--------: |
|       SHITOMASI       | 195.5   |   176.328   |    183.755    | 539.365 |   N/A   |  304.125   |
|        HARRIS         | 231.484 |   183.609   |    187.892    | 548.292 |   N/A   |  385.324   |
|         FAST          | 33.13   |   20.66     |    20.4359    | 426.884 |   N/A   |  266.03    |
|         BRISK         | 465.21  |   435.481   |    467.626    | 843.533 |   N/A   |  863.874   |
|          ORB          | 98.922  |   83.82     |    122.513    | 479.51  |   N/A   |  564.617   |
|         AKAZE         | 864.804 |   840.391   |    847.08     | 1216.28 | 1457.11 |  1021.24   |
|         SIFT          | 1328.65 |   1309.85   | Out of Memory | 1738.11 |   N/A   |  1936.3    |



#### Efficiency (matches/ms)

| Detectors\Descriptors |  BRISK   |    BRIEF    |      ORB      |  FREAK   |  AKAZE   |   SIFT   |
| :-------------------: | :------: | :---------: | :-----------: | :------: | :------: | :------: |
|       SHITOMASI       | 3.92327  |   5.3536    |    4.94136    | 1.4239   |   N/A    | 3.50843  |
|        HARRIS         | 0.6134   |   0.9422    |    0.8622     | 0.26263  |   N/A    | 0.42302  |
|         FAST          | 27.1355  |   53.1946   |    52.4078    | 2.05676  |   N/A    | 3.93565  |
|         BRISK         | 3.37482  |   3.91291   |    3.23763    | 1.80669  |   N/A    | 1.9181   |
|          ORB          | 7.59184  |   6.50203   |    6.22791    | 0.87589  |   N/A    | 1.3549   |
|         AKAZE         | 1.40494  |   1.50644   |    1.39538    | 0.97593  | 0.86404  | 1.2475   |
|         SIFT          | 0.44557  |  0.53594    | Out of Memory | 0.34118  |   N/A    | 0.41471  |



## TOP3 detector / descriptor combinations

1. FAST + BRIEF
2. FAST + ORB
3. FAST + BRISK