# FOPD-tuner
Fractional order proportional derivative controller tuner

# Related Publications:
Bo Shang, Jianxin Liu, Tiebiao Zhao and YangQuan Chen, "Fractional order robust visual servoing control of a quadrotor UAV with larger sampling period," 2016 International Conference on Unmanned Aircraft Systems (ICUAS), Arlington, VA, 2016, pp. 1228-1234. [PDF](https://github.com/cnpcshangbo/FOPD-tunner/raw/master/Fractional%20Order%20%20Robust%20Visual%20Servoing%20Control%20of%20%20A%20%20Quadrotor%20UAV%20with%20%20Larger%20Sampling%20%20Period.pdf)

# 1. License
FOPD-tunner is released under a [MIT license](https://github.com/cnpcshangbo/FOPD-tunner/blob/master/LICENSE).

If you use FOPD-tunner in an academic work, please cite:

    @inproceedings{shang2016fractional,
      title={Fractional order robust visual servoing control of a quadrotor UAV with larger sampling period},
      author={Shang, Bo and Liu, Jianxin and Zhao, Tiebiao and Chen, YangQuan},
      booktitle={Unmanned Aircraft Systems (ICUAS), 2016 International Conference on},
      pages={1228--1234},
      year={2016},
      organization={IEEE}
    }

# 2. Prerequisites
We have tested the library in **MATLAB 2018a** and **Windows 7**, but it should be easy to run in other platforms.

## Control model
<img width="433" alt="screenshot 2017-08-15 17 51 47" src="https://user-images.githubusercontent.com/4831029/29342990-04fdb158-81e3-11e7-82b1-55edb5378ec8.png">

## MATLAB System Identification Toolbox
Convert the identified model to this format:

<img width="170" alt="screenshot 2017-08-15 20 25 37" src="https://user-images.githubusercontent.com/4831029/29346105-f81f3aaa-81f7-11e7-9510-2267dedcafc9.png">

Then we can get the parameter k and tau.

## FOTF Toolbox
https://www.mathworks.com/matlabcentral/fileexchange/60874-fotf-toolbox

## Oustaloup

# 3. Downloading FOPD-tunner library and run examples
## Clone the repository:
```
git clone https://github.com/cnpcshangbo/FOPD-tunner.git
```

# 4. Run examples

## Tune IOPID controller
Enter the folder named "1. iopid_tune_pid", open "iopid_tune.m", update the plant parameters identified with MATLAB System Identification Toolbox and set cross-over frequency _omega_c_ and phase margin _phi_.
```matlab
%% Setting plant parameters
global K T1 wc phi %inputs
K = -0.8592; %plant gain
T1 = 1.0710; %plant time constant
wc = 1.2; %crossoverfrequency
phi = 83.9; %phase margin
```
then run it to tune IOPID controller and get related parameters (integer order PID parameters _kp_, _ki_, _kd_, ).

## Tune FOPD controller

### Setting parameters
Open file "cal_lambda_ki_kp_bode.m". Set fractional order _lambda_ to -1~0.

Note: When _lambda_ is negative, a FOPI controller becomes a FOPD controller.

### Plotting ki against lambda
Run the first cell of "cal_lambda_ki_kp_bode.m". You will see the three curves. Zoom the curves to find crosspoints. The coordinates of the crosspoints are _lambda_ and _ki_.

### Verify the crosspoints
Run the "Solution 1" cell of "cal_lambda_ki_kp_bode.m" to double check if the crosspoint is the REAL solution of the equations. If _ki_ is complex number, there's no real solution. It means the phase curve at cross-over frequency can not be flat under current cross-over frequency _omega_c_ and phase margin _phi_. Either change them or use optimization method to make the derivate of phase the smallest at cross-over frequency.

### Verify the tuned FOPD controller
Run the "Verify the controller 1" cell of "cal_lambda_ki_kp_bode.m" to verify if the phase curve is flat at crossover frequency. At the same time _kp_ will be displayed. The we can get the transfer function of the tuned FOPD controller.

### Controller analysis with Simulink
Open the file "run_simulink.m", update the parameters and run the script.
![image](https://user-images.githubusercontent.com/4831029/29388574-4c7dc9de-829a-11e7-973c-0b3f28b3df58.png)
![image](https://user-images.githubusercontent.com/4831029/29388561-3d999862-829a-11e7-9cc8-073a782e5854.png)

## Co-Simulation with Gazebo
We use the [Robotics System Toolbox](https://www.mathworks.com/products/robotics.html) to cooperate with Gazebo. The Gazebo part follows [Erlerobotics simulation](http://docs.erlerobotics.com/simulation).

### Follow this document to take off the drone:
http://docs.erlerobotics.com/simulation/vehicles/erle_copter/tutorial_5
![drone taken off](https://user-images.githubusercontent.com/4831029/29427393-80214438-833e-11e7-9db6-0e7f07d23f63.png)

### Launch MATLAB
Install Robotics System Toolbox. Initial ROS in MATLAB.

![ROS with MATLAB node](https://user-images.githubusercontent.com/4831029/29428228-48d8f50e-8341-11e7-9a25-2be983c9e238.png)

Use the ROS Publish block in Simulink to publish topic for velocity setpoint.
![ROS Publish block](https://user-images.githubusercontent.com/4831029/29436852-87877c46-8362-11e7-94ab-7453cf5a540a.png)

[![Use Simulink to move a drone in Gazebo](https://user-images.githubusercontent.com/4831029/29438864-60043fcc-836e-11e7-84a4-ef462bb2f434.png)](https://www.youtube.com/watch?v=9ukGdrOceOk)

