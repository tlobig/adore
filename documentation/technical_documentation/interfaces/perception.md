<!--
********************************************************************************
* Copyright (C) 2017-2020 German Aerospace Center (DLR). 
* Eclipse ADORe, Automated Driving Open Research https://eclipse.org/adore
*
* This program and the accompanying materials are made available under the 
* terms of the Eclipse Public License 2.0 which is available at
* http://www.eclipse.org/legal/epl-2.0.
*
* SPDX-License-Identifier: EPL-2.0 
*
* Contributors: 
* - Daniel Heß
********************************************************************************
-->
## ADORe's Perception Interface
An autonomous vehicle's control processes have to be provided with information about the state of the surrounding environment and the state of the vehicle itself. The vehicle has to know where it is and what is around it.
The following has to be provided to the ADORe control system by the perception layer:

### Where am I? Ego Localization State
A localization state has to be provided with 10-20Hz to allow the ADORe vehicle to plan trajectories and find its way to the desired goal location.
The localization state should be the current, best known estimate of the ego vehicle position and orientation in an absolute (global) frame of reference as well as dynamic states relevant for planning and control. It is made available in libadore via ```adore::fun::AFactory::getVehicleMotionStateReader()``` with the type ```adore::env::VehicleMotionState9d```, which contains the 3d vehicle position in X, Y and Z, the vehicle yaw angle PSI, longitudinal speed vx, lateral speed vy, yaw-rate omega, longitudinal acceleration ax, measured steering angle delta and a time stamp t.

The localization state is employed by the environment model, trajectory planners and decision making, as these require the position and orientation in an absolute frame of reference in order to relate ego state, observed traffic participants and HD-map data.

The interface module adore_if_ros defines reception of the localization state on three different topics ```localization```, ```VEH/ax``` and ```VEH/steering_angle_measured```. The topic ```localization``` transports nav_msgs::Odometry, which is typically supplied by a localization node of the Perception module. The information about current acceleration and steering angle might originate the vehicle platform's ESP and both are of type ```std_msgs::Float32```.

#### Ego Odometry State 
To facilitate stable trajectory tracking by the ADORe control system, an odometry estimate should be provided with 20-50Hz, depending on the ego vehicle's dynamics.
The odometry state is the current estimate of the ego vehicle progress in a relative frame of reference and is often attained by integrating velocities. See for example [ROS robot_localization](http://docs.ros.org/en/melodic/api/robot_localization/html/index.html). The signal should be jump-free and may drift over time.
The odometry state is available in libadore via ```adore::fun::AFactory::getVehicleOdometryMotionStateReader()``` with the type ```adore::env::VehicleMotionState9d```, see above.

The odometry state is employed by the vehicle's stabilization layer, e.g. high-frequency feedback control.

The interface module adore_if_ros defines reception of the odometry state on the topics ```odom```, ```VEH/ax``` and ```VEH/steering_angle_measured``` in the same manner as described above.

If a perception system does not supply a separate odometry estimate, it is possible to just use the localization state here, e.g. in ROS terms relay ```localization``` to ```odom```.

### What do I see? Traffic Participant Set
In order to make ADORe vehicles react to other traffic participants and obstacles, the perception layer should provide frequently updated traffic participant sets.
A traffic participant set can be the result of the Perception layer's object detection, tracking and fusion. 
It should contain all objects which are currently believed to be in the vicinity of the ego vehicle. It is available in libadore via ```adore::env::AFactory::getTrafficParticipantSetReader()``` and contains objects of type ```adore::env::traffic::Participant```. Participants are represented as boxes which have to define at least position in X, Y, Z, yaw-angle, longitudinal and lateral speed, yaw-rate, length, width and a time stamp that corresṕonds to the given participant's state.

The interface module adore_if_ros defines reception of the traffic participant set on the topic ```traffic``` with the ros message type ```adore_if_ros_msg::TrafficParticipantSet```. 

#### Unclassifiable Clutter
It may not be possible to find a good object-oriented representation for some things in the environment of the vehicle.
This could for example be the case for clutter on the side of the road.
Unspecific areas to be avoided by the autonomous vehicle can be provided as a list of cylinders to the collision-detection module.

In libadore the set of static obstacles is made available via ```adore::env::AFactory::getStaticObstaclesPredictionSetReader()``` with the type ```adore::env::OccupancyCylinderPredictionSet```.

The interface module adore_if_ros defines reception of the obstacle set on the topic ```ENV/Prediction/static``` with the type ```adore_if_ros_msg::OccupancyCylinderPredictionSet```.


#### Traffic Control Devices
Observations about traffic lights or other traffic control devices such as stop signs have to be supplied to the control system to stop the ego vehicle at for example red-lights and to allow to predict that other traffic participants will stop at red-ligths too.
The concept of controlled connections is used to specify which route along the lane-graph may be traversed: A connection is identified by a tuple of points, defining entry and exit of a controlled zone. The connection state defines the mode of traversal. A controlled connection associates a mapping from time to the expected state, which should at least define the current state and may be used to define predictions of future states.

In libadore the set of known connections and connection states is accessible via ```adore::env::AFactory::getControlledConnectionFeed()```, which supplies objects of type ```adore::env::ControlledConnection```.

The interface module adore_if_ros defines reception of observations/predictions about traffic control devices on the topic ```ENV/tcd``` with the type ```adore_if_ros_msg::TCDConnectionStateTrace```.
