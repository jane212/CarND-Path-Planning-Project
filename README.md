# CarND-Path-Planning-Project
Self-Driving Car Engineer Nanodegree Program

## Goals

In this project your goal is to safely navigate around a virtual highway with other traffic that is driving +-10 MPH of the 50 MPH speed limit. You will be provided the car's localization and sensor fusion data, there is also a sparse map list of waypoints around the highway. The car should try to go as close as possible to the 50 MPH speed limit, which means passing slower traffic when possible, note that other cars will try to change lanes too. The car should avoid hitting other cars at all cost as well as driving inside of the marked road lanes at all times, unless going from one lane to another. The car should be able to make one complete loop around the 6946m highway. Since the car is trying to go 50 MPH, it should take a little over 5 minutes to complete 1 loop. Also the car should not experience total acceleration over 10 m/s^2 and jerk that is greater than 10 m/s^3.

## Dependencies

* cmake >= 3.5
  * All OSes: [click here for installation instructions](https://cmake.org/install/)
* make >= 4.1
  * Linux: make is installed by default on most Linux distros
  * Mac: [install Xcode command line tools to get make](https://developer.apple.com/xcode/features/)
  * Windows: [Click here for installation instructions](http://gnuwin32.sourceforge.net/packages/make.htm)
* gcc/g++ >= 5.4
  * Linux: gcc / g++ is installed by default on most Linux distros
  * Mac: same deal as make - [install Xcode command line tools]((https://developer.apple.com/xcode/features/)
  * Windows: recommend using [MinGW](http://www.mingw.org/)
* [uWebSockets](https://github.com/uWebSockets/uWebSockets)
  * Run either `install-mac.sh` or `install-ubuntu.sh`.
  * If you install from source, checkout to commit `e94b6e1`, i.e.
    ```
    git clone https://github.com/uWebSockets/uWebSockets 
    cd uWebSockets
    git checkout e94b6e1
    ```

## Basic Build Instructions

1. Clone this repo.
2. Make a build directory: `mkdir build && cd build`
3. Compile: `cmake .. && make`
4. Run it: `./path_planning`.

## Simulator Settings

* Screen resolution: 640x480
* Graphics quality: Fastest

## Details

This project is built on the starter code provided by Udacity CarND team.

1. Speed keeping

The `ref_vel` is used to control the target speed. At the beginning, it's set to 5mph to start up a little bit faster than set to 0mph previously. Ultimately the `ref_vel` cannot exceed 49.5mph to obey the speed limit.

2. Collision avoidance

To avoid collision, the project uses `d` value to detect if any other cars in the simulation are in front of and close to ego. The threshold has been set to 30 meters to ask ego to take action if there is an obstacle. Two actions could be made.

* Lane change

First the ego check all the availabilities to make a safe lane change. The strategy is described as follows.
a. Detect obstacle in front, record its `s` and speed;
b. Detect vehicles in left lane and driving before ego, search vehicles upto 200m and record the nearest one `left_up` with its `s` and speed;
c. Detect vehicles in left lane and driving behind ego, if any vehicles showed up within 30m range, `left_open` set to `false`;
d. Compare `left_up` with the obstacle in ego's lane, if obstacle is faster, no need to shift to left, `left_open` set to `false`;
e. Check the `s` value of `left_up`, if it is too close to ego (threshold set to be 30m), not safe to shift to left, `left_open` set to `false`;
f. If there is no left lane, `left_open` set to `false`;
g. Repeat `Step b. - f.` for right lane;
h. If both left lane and right lane are open, choose the one with faster `up` vehicle;
i. Change lane according to availability. `lane-=1` for left lane and `lane+=1` for right lane.

* Keep lane and slow down

If above method ended up with no availablity, ego just gradually slows down (`ref_vel -= 0.224`) and keeps current lane, until the left or right lane is open to make change.

3. Trajectory generation

This project uses `spline` library to smooth the trajectory with guidiance from Aaron and David in walkthrough video.

The anchor points are selected based on the previous path, with 30 meters increment in x-axis and fitted by a function in spline. The points are first converted into vehicle's angle, which means shifting the theta to 0. And after fitting the polyline, the points are converted back to original x-y coordiantes and fed to the simulator.










