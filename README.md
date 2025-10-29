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
lerobot.replay \
  --robot.type=so101_follower \
  --robot.port="$PORT_FOLLOWER" \
  --robot.id=ss_follower \
  --dataset.repo_id="$REPO_ID" \
  --dataset.episode="$EP"

lerobot-record \
  --robot.type=so101_follower \
  --robot.port="$PORT_FOLLOWER" \
  --robot.id=ss_follower \
  --teleop.type=so101_leader \
  --teleop.port="$PORT_LEADER" \
  --teleop.id=ss_leader \
  --robot.cameras='{
    "top":{"type":"opencv","index_or_path":"/dev/cam-top","width":640,"height":480,"fps":30},
    "front":{"type":"opencv","index_or_path":"/dev/cam-front","width":640,"height":480,"fps":30}
  }' \
  --dataset.repo_id="$EVAL_REPO" \
  --dataset.single_task="$TASK_NAME" \
  --dataset.num_episodes=3 \
  --dataset.episode_time_s=120 \
  --policy.path="$POLICY_PATH" \
  --policy.device=cuda:0


📂 Folder Structure
data/
└── recordings/
    ├── sandwich_task1_bread_place/
    ├── sandwich_task2_cucumber_place/
    └── ...



[![Open in Hugging Face Spaces](https://img.shields.io/badge/Open%20in-Spaces-blue?logo=huggingface)](https://huggingface.co/spaces/karthikpythireddi93/sandwich_replay)


Copyright 2025 Karthik …

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0
…

