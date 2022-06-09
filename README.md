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

Specifically, we made use of Augmented Random Search (ARS), a Reinforcement Learning technique introduced in a 2018 research paper titled [Simple random search provides a competitive approach to reinforcement learning](https://arxiv.org/pdf/1803.07055.pdf). From here, we explored which variables could be included in the action space to produce the best movement speed results for each action. Ultimately, we landed on the following set of variables to be the action space for our policy training:
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

First, clone this repo and switch to the correct branch using the following set of commands:
```
git clone https://github.com/AJArnolie/StanfordQuadruped.git
cd StanfordQuadruped
checkout pupper_api
```

Then, in order to ensure that all of the necessary packages are installed, run the following command in the outermost directory of the `StanfordQuadruped` repository:
```
python3 -m pip install -e .
```

From here, in order to train a new policy for one of the four actions (Forward, Backward, Turn Left, Turn Right), simply run the `pupper_ars_train.py` script stored in `StanfordQuadruped/rl`. For example, to train the robot on the Forward Motion task, run the command below:
```
python3 pupper_ars_train.py --rollout_length=200 --n_directions=64 --deltas_used=64 --action=F
```
Note that the `--action` option allows users to select one of the four currently supported actions, Forward Motion (F), Backward Motion (B), Left Turning (L), and Right Turning (R). Additionally, note that we have included the policies we generated for each of the movements in this repository such that this step can be skipped if desired.

Once a new policy has been trained, run the following command to run the policy on a simulation of Pupper:
```
python3 pupper_ars_run_policy.py --json_file=data/params.json --playback_speed=1.0 --action=F
```
If you would like to tweak the reward functions being used for each of the movements, feel free to modify them in `StanfordQuadruped/pupper_controller/src/pupperv2/pupper_env.py`.

## Results
Below, we've included some clips from the results our of top performing policies in terms of speed:

<p align="center">
  <img src="https://user-images.githubusercontent.com/57520931/172830198-0aa07c93-ac87-444e-b930-929a41eb14fb.gif"/>
</p>

<p align="center">
  <img src="https://user-images.githubusercontent.com/57520931/172841125-6d5bd62c-d359-4b06-abe4-e7c709388d5e.gif"/>
</p>

## Reflections
Ultimately, this project and this course in general was an incredibly interesting introduction into the awesome things that Reinforcement Learning can do along as well as the many challenges that come with working with real robots. Given more time and less frustrating roadblocks (ex. COVID, disassembled robots) we would have liked to get these policies running on the Pupper robot itself and see how the real-life robot performed in comparison to the simulated Pupper. This would be interesting to see, as from what we have observed through work on this project, the Sim-to-Real gap only gets larger as the speed of the robot increases. We also would have loved to spend time exploring what else could be done in order to increase Pupper's speed past configuration tweaks. For example, perhaps developing a new gait for Pupper or employing different Reinforcement Learning techniques other than ARS could have potentially been an interesting next step. Regardless, we are proud of what we were able to accomplish given the roadblocks we encountered along the way.
