# Mbodied Agents

<img src="assets/logo.jpeg" alt="Mbodied Agents Logo" style="width: 200px;">

[![License](https://img.shields.io/badge/License-Apache_2.0-blue.svg)](https://opensource.org/licenses/Apache-2.0)
[![MacOS | Python 3.12|3.11|3.10](https://github.com/MbodiAI/opensource/actions/workflows/macos.yml/badge.svg?branch=main)](https://github.com/MbodiAI/opensource/actions/workflows/macos.yml)
[![Ubuntu](https://github.com/MbodiAI/opensource/actions/workflows/ubuntu.yml/badge.svg)](https://github.com/MbodiAI/opensource/actions/workflows/ubuntu.yml)
[![PyPI Version](https://img.shields.io/pypi/v/mbodied-agents.svg)](https://pypi.python.org/pypi/mbodied-agents)
[![Example Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/drive/1DAQkuuEYj8demiuJS1_10FIyTI78Yzh4?usp=sharing)

Welcome to **Mbodied Agents**! A toolkit and platform for integrating various state-of-the-art transformers in robotics for any embodiments. Mbodied Agents has a consistent interface for calling different AI models, handling multimodal data, using/creating datasets trained on different robots, and more!

<img src="assets/architecture.jpg" alt="Architecture Diagram" style="width: 650px;">

Each time you interact with a robot, the data is automatically recorded into a dataset, which can be augmented and used for model training. To learn more about how to process the dataset, augment the data, or train/finetune a foundation model, please fill out this [form](https://forms.gle/rv5rovK93dLucma37).

<img src="assets/demo_gif.gif" alt="Demo GIF" style="width: 625px;">

We welcome any questions, issues, or PRs!

Please join our [Discord](https://discord.gg/RNzf3RCxRJ) for interesting discussions!

**⭐ Give us a star on GitHub if you like us!**

## Overview

Mbodied Agents offers the following features:

- **Configurability** : Define your desired Observation and Action spaces and read data into the format that works best for your system.
- **Natural Language Control** : Use verbal prompts to correct a cognitive agent's actions and calibrate its behavior to a new environment.
- **Modularity** : Easily swap out different backends, transformers, and hardware interfaces. For even better results, run multiple agents in separate threads.
- **Validation** : Ensure that your data is in the correct format and that your actions are within the correct bounds before sending them to the robot.

### Support Matrix

If you would like to integrate a new backend, sense, or motion control, it is very easy to do so. Please refer to the [contributing guide](CONTRIBUTING.md) for more information.

- OpenAI
- Anthropic
- Mbodi (Coming Soon)
- HuggingFace (Coming Soon)

### In Beta

For access (or just to say hey 😊), don't hesitate to fill out this [form](https://forms.gle/rv5rovK93dLucma37) or reach out to us at info@mbodi.ai.

- **Conductor**: A service for processing and managing datasets, and automatically training your models on your own data.
- **Data Augmentation**: Build invariance to different environments by augmenting your dataset with Mbodi's diffusion-based data augmentation to achieve better generalization.
- **Mbodied SVLM**: A new Spatial Vision Language Model trained specifically for spatial reasoning and robotics control.
- **FAISS Indexing**: Use FAISS to index your robot's recent memory and perform RAG rather than pollute its context.

### Idea

The core idea behind Mbodied Agents is end-to-end continual learning. We believe that the best way to train a robot is to have it learn from its own experiences.

## Installation

`pip install mbodied-agents`

## Dev Environment Setup

1. Clone this repo:

   ```console
   git clone https://github.com/MbodiAI/mbodied-agents.git
   ```

2. Install system dependencies:

   ```console
   source install.bash
   ```

3. Then for each new terminal, run:

   ```console
   hatch shell
   ```

## Getting Started

Please refer to [examples/simple_robot_agent.py](examples/simple_robot_agent.py) or use the Colab below to get started.

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/drive/1DAQkuuEYj8demiuJS1_10FIyTI78Yzh4?usp=sharing)

To run `simple_robot_agent.py`, if you want to use OpenAI, for example, as your backend:

```shell
export OPENAI_API_KEY=your_api_key
python examples/simple_robot_agent.py --backend=openai
```

Upcoming feature: if you want to use `mbodi` as your backend:

```shell
python examples/simple_robot_agent.py --backend=mbodi
```

## Directory Structure

```
├─ assets/ ............. Images, icons, and other static assets
├─ examples/ ........... Example scripts and usage demonstrations
├─ resources/ .......... Additional resources for examples
├─ src/
│  └─ mbodied/
│     ├─ agents/ ....... Modules for robot agents
│     │  ├─ backends/ .. Backend implementations for different services for agents
│     │  ├─ language/ .. Language based agents modules
│     │  └─ sense/ ..... Sensory, e.g. audio, processing modules
│     ├─ base/ ......... Base classes and core infra modules
│     ├─ data/ ......... Data handling and processing
│     ├─ hardware/ ..... Hardware interface and interaction
│     └─ types/ ........ Common types and definitions
└─ tests/ .............. Unit tests
```

## Glossary

- **Agent**: A unit of intelligent computation that takes in an `Observation` and outputs an `Action`. This can involve multiple sub-agents.

- **Backend**: The system that embodied agents query. This typically involves a vision-language model or other specially purposed models.

- **Control**: An atomic action that is “handed off” to other processes outside the scope of consideration. An example is HandControl, which includes x, y, z, roll, pitch, yaw, and grasp. This is a motion control used to manage the position, orientation, and hand-openness of an end-effector. Typically, this is passed to lower-level hardware interfaces or libraries.

## Details

### Cognitive Agent

The Cognitive Agent is the main entry point for intelligent robot agents. It can connect to different backends or transformers of your choice.

For example, to use OpenAI for your robot backend. Currently supported API services are OpenAI and Anthropic. Upcoming API services include Mbodi, Ollama, and HuggingFace.

Stay tuned for our Mbodi backend service!

```python
robot_agent = CognitiveAgent(context=context_prompt, api_service="openai")
```

To execute an instruction:

```python
response = robot_agent.act(instruction, observation)[0]
```

You can also pass an arbituary number of text and image to the agent:

```python
response = robot_agent.act([instruction1, image1, instruction2, image2])[0]
```

### Hardware Interface

Mapping robot actions from any model to any embodiment is very easy.

In our example script, we use a mock hardware interface. We also have an XArm interface as an example at [src/mbodied/hardware/xarm_interface.py)](src/mbodied/hardware/xarm_interface.py).

Upcoming: a remote hardware interface with a communication protocol. This will be very convenient for controlling robots that have a computer attached, e.g., LoCoBot.

### Dataset Recording

To record your conversation and the robot's actions to a dataset as you interact with/teach the robot.

Here's an example of recording observation, instruction, and the output HandControl (x, y, z, r, p, y, grasp).

```python
observation_space = spaces.Dict({
    'image': Image(size=(224, 224)).space(),
    'instruction': spaces.Text(1000)
})
action_space = HandControl().space()
recorder = Recorder('example_recorder', out_dir='saved_datasets', observation_space=observation_space, action_space=action_space)

# Every time robot makes a conversation or performs an action:
recorder.record(observation={'image': image, 'instruction': instruction,}, action=hand_control)
```

The dataset is saved to `./saved_datasets`. Please fill out this [form](https://forms.gle/rv5rovK93dLucma37) if you are interested in getting the dataset processed, augmented, or use it for training etc.

## Contributing

We believe in the power of collaboration and open-source development. This platform is designed to be shared, extended, and improved by the community. See the [contributing guide](CONTRIBUTING.md) for more information.

Feel free to report any issues, ask questions, ask for features, or submit PRs.

## About Mbodi AI

Mbodi AI is an open-source robotics and AI platform designed to support end-to-end robotics applications involving artificial intelligence, data handling and augmentation, human-user interaction, and much more!
