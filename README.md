# Path Planning Project

## Goals

In this project, the your goal is to safely navigate around a virtual highway with other traffic that is driving +-10 MPH of the 50 MPH speed limit. The car's localization and sensor fusion data are provided, there is also a sparse map list of waypoints around the highway. The car should try to go as close as possible to the 50 MPH speed limit, which means passing slower traffic when possible, note that other cars will try to change lanes too. The car should avoid hitting other cars at all cost as well as driving inside of the marked road lanes at all times, unless going from one lane to another. The car should be able to make one complete loop around the 6946m highway. Since the car is trying to go 50 MPH, it should take a little over 5 minutes to complete 1 loop. Also the car should not experience total acceleration over 10 m/s^2 and jerk that is greater than 10 m/s^3.

## Basic Build Instructions

1.  Clone this repo.
2.  Make a build directory:  `mkdir build && cd build`
3.  Compile:  `cmake .. && make`
4.  Run it:  `./path_planning`.

## Details

1.  The car uses a perfect controller and will visit every (x,y) point it receives in the list every .02 seconds. The units for the (x,y) points are in meters and the spacing of the points determines the speed of the car. The vector going from a point to the next point in the list dictates the angle of the car. Acceleration both in the tangential and normal directions is measured along with the jerk, the rate of change of total Acceleration. The (x,y) point paths that the planner recieves should not have a total acceleration that goes over 10 m/s^2, also the jerk should not go over 50 m/s^3. (NOTE: As this is BETA, these requirements might change. Also currently jerk is over a .02 second interval, it would probably be better to average total acceleration over 1 second and measure jerk from that.
    
----------

## Dependencies

-   cmake >= 3.5
    -   All OSes:  [click here for installation instructions](https://cmake.org/install/)
-   make >= 4.1
    -   Linux: make is installed by default on most Linux distros
    -   Mac:  [install Xcode command line tools to get make](https://developer.apple.com/xcode/features/)
    -   Windows:  [Click here for installation instructions](http://gnuwin32.sourceforge.net/packages/make.htm)
-   gcc/g++ >= 5.4
    -   Linux: gcc / g++ is installed by default on most Linux distros
    -   Mac: same deal as make - [install Xcode command line tools](([https://developer.apple.com/xcode/features/](https://developer.apple.com/xcode/features/))
    -   Windows: recommend using  [MinGW](http://www.mingw.org/)
-   [uWebSockets](https://github.com/uWebSockets/uWebSockets)
    -   Run either  `install-mac.sh`  or  `install-ubuntu.sh`.
    -   If you install from source, checkout to commit  `e94b6e1`, i.e.
        
        ```
        git clone https://github.com/uWebSockets/uWebSockets 
        cd uWebSockets
        git checkout e94b6e1
`
## Code model for generating paths

### Prediction and decision using telemetry and sensor fusion

The first part of the code is executed when a telemetry event take place. The sensor fusion data are then analyzed to get an idea of the environment; it permits to know if there is a car in our lane blocking us and if other lanes are free to optionally change lane safely.

A minimum distance of 20 meters with the car ahead, as well as 20 meters (forward and backward) with any car on the other lanes, is required.

The choice was made to:
- have the car accelerate to reach a cruising speed of 49 mph, 
- change lane if a car in front of us (at less than 20 meters) is present and another lane is free,
- If no lane is free, slow down to adapt to the car's speed in front of us.

### Trajectory

After the first step, the decision is taken into account, as well the past path points and the vehicle's position. 

We use the last two points to make the path tangent to the path's previous endpoint. The previous path's endpoint is as starting reference. If we do not have previous point (at the beginning of the run), we use the car position as starting reference. Then, using Frenet (getXY() function), we add 30 meters evenly spaced points ahead of the starting reference.

Finally, the computed waypoints (50 in total, indicated by the green line in the simulator) are transformed using spline, making it possible to have a curved/smooth trajectory instead of sudden/abrupt changes in direction (increasing the jerk that should be minimized).


