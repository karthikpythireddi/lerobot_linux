# lerobot_linux
This repository has the setup instructions useful for the linux machine. 


# 🥪 LeRobot Sandwich Hackathon – Imitation Learning on SO-101

This repo contains a **complete step-by-step setup** for running imitation learning
using [🤗 LeRobot](https://github.com/huggingface/lerobot) on **SO-101 robots**.
It was developed during the **Hugging Face Embodied AI Hackathon** for the
task of making a sandwich 🧠🤖.

---

## 🚀 Quick Start

```bash
git clone git@github.com:karthikpythireddi/lerobot_linux.git
cd lerobot-linux
bash setup/install_env.sh


| Task              | Description                         | Dataset ID                               |
| ----------------- | ----------------------------------- | ---------------------------------------- |
| bread_place       | Pick bread slice and place on plate | `karthikpythireddi93/breakfast_sandiwch` |
| cucumber_place    | Pick cucumber and place on bread    | same                                     |
| carrot_place      | Stack carrot slices                 | same                                     |
| ketchup_place     | Pour ketchup                        | same                                     |
| final_bread_place | Complete sandwich                   | same                                     |


🎥 Data Collection

Each task is recorded using:

lerobot-record \
  --robot.type=so101_follower \
  --teleop.type=so101_leader \
  --dataset.single_task="bread_place" \
  --dataset.repo_id=karthikpythireddi93/breakfast_sandiwch \
  --dataset.push_to_hub=true

Keyboard shortcuts:
→ next episode

← re-record



🧠 Training
lerobot-train \
  --dataset.repo_id=karthikpythireddi93/breakfast_sandiwch \
  --policy=act \
  --train.epochs=10 \
  --device=cuda:0



🤖 Inference / Replay
lerobot-replay \
  --robot.type=so101_follower \
  --dataset.repo_id=karthikpythireddi93/make_a_sandwich \
  --dataset.episode=0

📂 Folder Structure
data/
└── recordings/
    ├── sandwich_task1_bread_place/
    ├── sandwich_task2_cucumber_place/
    └── ...
