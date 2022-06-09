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

Specifically, we made use of Augmented Random Search (ARS), a straightforward Reinforcement Learning technique already implemented within the 


To make this process easier, we modified Pupper's policy training code in the `pupper_api` branch of the StanfordQuadruped repo to build out a more straightforward framework for generating the policies for each action. 

## Usage

In order to train a new policy for one of the four actions (Forward, Backward, Turn Left, Turn Right), simply run the `pupper_ars_train.py` script stored in `StanfordQuadruped/rl`. For example, to train the robot on the Forward Motion task, run the command below:
```
python3 pupper_ars_train.py --rollout_length=200 --n_directions=64 --deltas_used=64 --action=F
```
The `--action` option allows users to select one of the four currently supported actions, Forward Motion (F), Backward Motion (B), Left Turning (L), and Right Turning (R). Once a new policy has been trained, or if you would like to use one of the policies we generated, run the following command to run the policy on a simulation of Pupper:
```
python3 pupper_ars_run_policy.py --json_file=data/params.json --playback_speed=1.0 --action=F
```

## Results


## Reflections
Ultimately, this project and this course in general was an incredibly interesting and informative experience both in the potential use and flaws of reinforcement learning but also in the task of building and interacting with robots.

RL is frustrating to get right and doesn’t always work
The Sim-to-Real gap seems to get larger when going for speed
IRL Pupper will always find a way to slip and flip itself over
Likely close to the fastest Pupper can get using its current controller
Potentially applying a different gait
Exploring more RL learning policies
Robots are prone to break and get broken (or disassembled)
Don’t get COVID during Finals Week



Given more time and less frustrating roadblocks (ex. COVID, disassembled robots) we would have liked to get these policies running on the Pupper robot itself because from what we have observed through work on this project, the Sim-to-Real gap only gets larger as the speed of the robot increases. 
