# Stanford Quadruped

# CS199P Robotics Final Project: Pupper Mobility Improvements
<p align="center">
AJ Arnolie and Ian Ng, Spring 2021 Final Project Documentation
</p>

<p align="center">
  <img src="https://user-images.githubusercontent.com/57520931/172830198-0aa07c93-ac87-444e-b930-929a41eb14fb.gif"/>
</p>

_Note: To view the original README for the StanfordQuadruped repository, see repo_README.md._

## Overview
Our goal for this project was to determine how far we could push Pupper in terms of optimizing movement speeds for the **Move Forward**, **Move Backward**, **Turn Left**, and **Turn Right** actions. 

After constructing our Pupper, through initial testing, we realized that the trotting movements of Pupper were not necessarily as fast as we imagined they could potentially be. Initially, we attempted simply increasing the expected X Velocity fed to the Pupper controller, but doing so quickly led to a loss in stability and a drastic increase in the frequency with which Pupper fell over (and even turned itself off on occasion). Through toying with the many configuration settings available to modify the gait, stance, and overall movement of the robot, we discovered methods for improving the stability of Pupper when moving at faster speeds. One such method involved manually shifting the amount the robot walks in front or behind it’s Center of Mass, essentially allowing Pupper to lean forward when moving forward and lean backward when walking back. Though these manual tuning methods were fairly effective, they were extremely time consuming and certainly didn't take advantage of all of the configuration options available, so at this point, we turned to **Reinforcement Learning**.

Specifically, we made use of Augmented Random Search (ARS), a Reinforcement Learning technique introduced in a 2018 research paper titled (_Simple random search provides a competitive approach to reinforcement learning_)[https://arxiv.org/pdf/1803.07055.pdf]. From here, we explored which variables could be included in the action space to produce the best movement speed results for each action. Ultimately, we landed on the following set of variables to be the action space for our policy training:
- **X Velocity** - Velocity in the forward/backward directions
- **Y Velocity** - Velocity in the left/right directions
- **Yaw Rate** - The rate at which Pupper rotates around the z-axis
- **Height** - Desired distance from robot body to ground
- **Pitch** - Rotation around y-axis
- **X Shift** - The amount the robot walks in front of/behind it’s Center of Mass 
- **Z Clearance** - The height the robot lifts it's legs
- **Alpha** - Ratio between touchdown distance and total horizontal stance movement
- **Beta** - Ratio between touchdown distance and total horizontal stance movement
- **Delta Y** - The left/right distance between the feet of the robot

Using this set of variables, we trained policies for each of the movement options for 500 iterations using 200 rollouts, 16 deltas, and 16 directions. In terms of the policies we implemented for these four movements, for the Forward and Backward movements, the reward functions were generally structured as:

`Reward = Stability Constant + X Velocity * Time - |Angular Velocity along Z-axis / 10| - |Yaw Displacement over entire Rollout / 100|`

This reward function rewards stability and forward motion speed while discouraging drifting. For the Left Turn and Right Turn movements, the reward functions were structured as:

`Reward = Stability Constant + Angular Velocity along Z-axis * Time - |sqrt(X-displacement^2 + Y-displacement^2) / 10|`

This reward function rewards stability and rotation speed while discouraging movement along the XY plane. To make this process easier, we modified Pupper's policy training code in the `pupper_api` branch of the StanfordQuadruped repo to build out a more straightforward framework for generating the policies for each action. See the process for training and testing these policies below.

## Usage

In order to train a new policy for one of the four actions (Forward, Backward, Turn Left, Turn Right), simply run the `pupper_ars_train.py` script stored in `StanfordQuadruped/rl`. For example, to train the robot on the Forward Motion task, run the command below:
```
python3 pupper_ars_train.py --rollout_length=200 --n_directions=64 --deltas_used=64 --action=F
```
Note that the `--action` option allows users to select one of the four currently supported actions, Forward Motion (F), Backward Motion (B), Left Turning (L), and Right Turning (R). 

Once a new policy has been trained, run the following command to run the policy on a simulation of Pupper:
```
python3 pupper_ars_run_policy.py --json_file=data/params.json --playback_speed=1.0 --action=F
```

We have included the policies we have generated for each of the movements in this repository such that running the second command will allow users to run these policies on the simulated Pupper immediately.

## Results


## Reflections
Ultimately, this project and this course in general was an incredibly interesting introduction both into the potential uses and flaws of reinforcement learning but also in the frustrations of the task of building and interacting with robots IRL.

RL is frustrating to get right and doesn’t always work
The Sim-to-Real gap seems to get larger when going for speed
IRL Pupper will always find a way to slip and flip itself over
Likely close to the fastest Pupper can get using its current controller
Potentially applying a different gait
Exploring more RL learning policies
Robots are prone to break and get broken (or disassembled)
Don’t get COVID during Finals Week



Given more time and less frustrating roadblocks (ex. COVID, disassembled robots) we would have liked to get these policies running on the Pupper robot itself and see how because from what we have observed through work on this project, the Sim-to-Real gap only gets larger as the speed of the robot increases. 
