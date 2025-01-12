---
layout: post
title: "Training a MuJoCo Unitree Quadruped Robot in MATLAB: A Step-by-Step Guide"
date: 2025-01-09 00:00:00 -0500
categories: jekyll update
---

<p><div style="width:70%; margin:auto auto;"><img src="/assets/2025-01-09-rl-train-unitree-robot-img-6.png" alt="rl_designer_app"/></div></p>

## Introduction

Reinforcement learning (RL) is like teaching your dog new tricks, but instead of treats, we use rewards and penalties. In this guide, we'll dive into training a reinforcement learning agent for a [Unitree Go1](https://shop.unitree.com/products/unitreeyushutechnologydog-artificial-intelligence-companion-bionic-companion-intelligent-robot-go1-quadruped-robot-dog?srsltid=AfmBOooQYhHjOGPmmBHsxa0tCq7TFbBD1LhzfeFh8-3tnSQ7xL1AHYbJ) quadruped robot using the MuJoCo physics engine for simulation and MATLAB's Reinforcement Learning Toolbox for training. Buckle up, it's going to be a fun ride!

## Prerequisites

Before we jump in, make sure you have the following:

1. **Python and dependencies**:
   Clone the repository.

```
<GITHUB_REPO>
```

Create a Python environment and install dependencies. This will install the gymnasium library with MuJoCo and add the MuJoCo Menagerie repository as a git submodule.

```
python -m venv env
source ./env/bin/activate
pip install gymnasium[mujoco] gymnasium[other]
git submodule add https://github.com/google-deepmind/mujoco_menagerie.git
```

2. **MATLAB and Reinforcement Learning Toolbox**: Follow the instructions on the [MathWorks website](https://www.mathworks.com/products/reinforcement-learning.html) to install MATLAB and the Reinforcement Learning Toolbox.

## Creating the Simulation Model

MuJoCo's simulation for the Unitree Go1 robot is like having a super realistic video game character. It handles complex interactions between the robot's limbs and the environment, ensuring realistic simulation of forces, torques, and contacts.

MuJoCo integrates seamlessly with Gymnasium, a toolkit for developing and comparing RL algorithms. By tweaking the `Mujoco/Ant-v5` framework, we can make it behave like our Unitree robot. This involves adjusting the XML model file, reward weights, control costs, and other environment specifications.

Create a Python file `go1.py` in your project folder.

```python
import gymnasium

def create_env():
  return gymnasium.make(
    'Ant-v5',
    xml_file='./mujoco_menagerie/unitree_go1/scene.xml',
    forward_reward_weight=1,
    ctrl_cost_weight=0.05,
    contact_cost_weight=5e-4,
    healthy_reward=1,
    main_body=1,
    healthy_z_range=(0.195, 0.75),
    include_cfrc_ext_in_observation=True,
    exclude_current_positions_from_observation=False,
    reset_noise_scale=0.1,
    frame_skip=25,
    max_episode_steps=1000,
  )
```

The environment has the following specifications.

**Observation Space**:

- Positions and velocities of the robot's joints, body position, center of mass position and velocity, and contact forces on the robot's limbs.
- Represented as a continuous vector of 115 real numbers.

**Action Space**:

- 12 torque values applied to the robot's joints.
- Bounded by the maximum and minimum torque values the robot's actuators can produce.

**Reward Function**:

- Encourages forward movement while penalizing inefficient or unsafe behaviors.
- **Forward Reward**: Proportional to the forward velocity.
- **Control Cost**: Penalizes large torques.
- **Contact Cost**: Penalizes excessive contact forces.
- **Healthy Reward**: Constant reward for staying upright.

**Termination Logic**:

- Episode ends if the robot's body falls below or exceeds a certain height.

To integrate this environment in MATLAB, we will call Python from MATLAB using the `pyrun` function.

```matlab
result = pyrun("some_python_function()", "result");
```

You can also pass MATLAB variables to Python functions:

```matlab
inputVar = 10;
result = pyrun("some_python_function(inputVar)", "result", inputVar=inputVar);
```

Open MATLAB from a terminal with the activated virtual environment:

```
/Applications/MATLAB_R2024b.app/bin/matlab
```

Create a new MATLAB Script `go_environment.m` and add the following code:

```matlab
pyrun("import go1");
penv = pyrun("env = go1.create_env()","env")
```

And that's it! The `penv` variable now stores information about the gymnasium environment in MATLAB.

## Creating a MATLAB Reinforcement Learning Environment

MATLAB's environments infrastructure can be used to generate data for training agents. We'll wrap the gymnasium environment within a MATLAB environment.

First, define the observation and action specifications:

```matlab
numObs = double(penv.observation_space.shape{1});
numAct = double(penv.action_space.shape{1});
action_low = double(penv.action_space.low);
action_high = double(penv.action_space.high);
```

Create the specifications:

```matlab
obsInfo = rlNumericSpec([numObs,1]);
actInfo = rlNumericSpec([numAct,1]);
actInfo.LowerLimit = action_low;
actInfo.UpperLimit = action_high;
```

Define a step function for the environment:

```matlab
function [nextobs,reward,isdone,info] = stepFun(penv,action,info)
   pyAction = py.numpy.array(action);
   pyStepOut = penv.step(pyAction);
   nextobs = double(pyStepOut{1});
   reward = double(pyStepOut{2});
   isdone = pyStepOut{3} || pyStepOut{4};
end
```

Create a reset function:

```matlab
function [obs,info] = resetFun(penv)
   pyResetOut = penv.reset();
   obs = double(pyResetOut{1});
   info = [];
end
```

Finally, create the environment object:

```matlab
step = @(action,info) stepFun(penv,action,info);
reset = @() reset(penv);
env = rlFunctionEnv(obsInfo,actInfo,step,reset);
```

## Designing a Soft Actor-Critic (SAC) Agent

The Reinforcement Learning Designer app in MATLAB is like a magic wand for RL. It provides an interactive interface for designing, training, and simulating RL agents without writing extensive code.

Open the app:

```matlab
reinforcementLearningDesigner
```

Import the environment in the app workspace. Click **_Import_** and choose the `env` object.

<p><div style="width:80%; margin:auto auto;"><img src="/assets/2025-01-09-rl-train-unitree-robot-img-1.png" alt="rl_designer_app"/></div></p>

Create an agent by clicking the **_New_** button under the Agents section. Select the **_SAC_** algorithm and a choice of hidden units (256), and click **_Ok_**.

<p><div style="width:60%; margin:auto auto;"><img src="/assets/2025-01-09-rl-train-unitree-robot-img-2.png" alt="new_agent"/></div></p>

Analyze the actor and critic networks using the **_View Actor/Critic Model_** button.

<p><div style="width:80%; margin:auto auto;"><img src="/assets/2025-01-09-rl-train-unitree-robot-img-3.png" alt="new_agent"/></div></p>

Tune the hyperparameters:

- Actor and critic learning rates `5e-4` with gradient threshold `1`.
- Batch size `300`.
- Discount factor `0.995`.
- Experience buffer length of `1e6`.

Under **_More Options_**:

- Learning frequency of `1000`.
- Number of epochs `2`.
- Warm start steps `1000`.

<p><div style="width:80%; margin:auto auto;"><img src="/assets/2025-01-09-rl-train-unitree-robot-img-4.png" alt="new_agent"/></div></p>

## Training the RL Agent

With the simulation model and RL agent in place, we can start the training process. This involves running multiple episodes of the simulation, where the agent interacts with the environment and learns from the rewards it receives.

<p><div style="width:80%; margin:auto auto;"><img src="/assets/2025-01-09-rl-train-unitree-robot-img-5.png" alt="train_tab"/></div></p>

Navigate to the **_Train_** tab, and set the following training options:

- Max episodes `10000`.
- Max steps per episode `1000`.
- Averaging window length `20`.
- Stop training criteria `none`.

Click the **_Train_** button to start training. Once the training is finished, **_Accept_** the training results and save the app session.

<p><div style="width:100%; margin:auto auto;"><img src="/assets/2025-01-09-rl-train-unitree-robot-img-7.png" alt="new_agent"/></div></p>

## Evaluating the Trained Agent

After training, we will evaluate the performance of the RL agent by running it in the simulation and observing its behavior.

Add video logging capabilities to the environment using the `RecordVideo` environment wrapper. Add the following code in `go1.py`.

```python
num_eval_episodes = 4

def create_env_with_recording(video_folder="recordings", render_mode="rgb_array"):
  env = gymnasium.make(
    'Ant-v5',
    xml_file='./mujoco_menagerie/unitree_go1/scene.xml',
    forward_reward_weight=1,
    ctrl_cost_weight=0.05,
    contact_cost_weight=5e-4,
    healthy_reward=1,
    main_body=1,
    healthy_z_range=(0.195, 0.75),
    include_cfrc_ext_in_observation=True,
    exclude_current_positions_from_observation=False,
    reset_noise_scale=0.1,
    frame_skip=25,
    max_episode_steps=1000,
    render_mode=render_mode,
    camera_id=0
  )
  env = RecordVideo(
    env,
    video_folder=video_folder,
    name_prefix="eval",
    episode_trigger=lambda x: True
  )
  return RecordEpisodeStatistics(env, buffer_length=num_eval_episodes)
```

Create the MATLAB environment wrapper:

```matlab
pyrun("import go1")
rec_env = pyrun("env = go1.create_env_with_recording()","env");
step = @(action,info) stepFun(rec_env,action,info);
reset = @() resetFun(rec_env);
record_env = rlFunctionEnv(obsInfo,actInfo,step,reset);
```

> **_Note:_** For environment visualization, you will need to download and install **ffmpeg**. This [page](https://www.hostinger.com/tutorials/how-to-install-ffmpeg) has some guidelines for installing it.

Import the `record_env` environment in the app. Navigate to the **_Simulate_** tab and click **_Simulate_**.

## Conclusion

In this guide, we've walked through the process of training a reinforcement learning agent for a Unitree Go1 quadruped robot using MuJoCo and MATLAB. By following these steps, you can leverage the power of RL to tackle complex robotics tasks. Happy training!

```

```
