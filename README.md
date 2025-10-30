🥪 LeRobot Sandwich Hackathon – Imitation Learning on SO-101

This repository provides a complete Linux setup for running imitation learning with 🤗 LeRobot
 on SO-101 robots.
Developed during the Hugging Face Embodied AI Hackathon, the goal was to teach a robot how to make a sandwich 🧠🤖.

🚀 Quick Start
git clone git@github.com:karthikpythireddi/lerobot_linux.git
cd lerobot_linux
bash setup/install_env.sh

⚙️ Setup on Linux
🔌 1️⃣ Serial & Camera Configuration
# Grant USB permissions
sudo usermod -aG dialout $USER && newgrp dialout

# List serial devices
ls -l /dev/serial/by-id/

# Export leader and follower ports
export PORT_LEADER=/dev/serial/by-id/usb-1a86_USB_Single_Serial_5AAF270236-if00
export PORT_FOLLOWER=/dev/serial/by-id/usb-1a86_USB_Single_Serial_5A7A015883-if00

# Optional: symbolic links for camera consistency
sudo ln -sf /dev/v4l/by-id/<TOP-CAM-ID>   /dev/cam-top
sudo ln -sf /dev/v4l/by-id/<FRONT-CAM-ID> /dev/cam-front
sudo ln -sf /dev/v4l/by-id/<SIDE-CAM-ID>  /dev/cam-side

⚙️ 2️⃣ Motor Setup & Calibration
# Motor initialization
lerobot-setup-motors --teleop.type=so101_leader --teleop.port="$PORT_LEADER"

# Calibration
lerobot-calibrate --robot.type=so101_follower --robot.port="$PORT_FOLLOWER" --robot.id=ss_follower
lerobot-calibrate --teleop.type=so101_leader  --teleop.port="$PORT_LEADER"  --teleop.id=ss_leader

🎮 3️⃣ Teleoperation Test
lerobot-teleoperate \
  --robot.type=so101_follower --robot.port="$PORT_FOLLOWER" --robot.id=ss_follower \
  --teleop.type=so101_leader  --teleop.port="$PORT_LEADER"  --teleop.id=ss_leader \
  --robot.cameras='{
    "top":   {"type":"opencv","index_or_path":"/dev/cam-top","width":640,"height":480,"fps":30},
    "front": {"type":"opencv","index_or_path":"/dev/cam-front","width":640,"height":480,"fps":30},
    "side":  {"type":"opencv","index_or_path":"/dev/cam-side","width":640,"height":480,"fps":30}
  }' \
  --display_data=true

🎥 Data Collection
🧩 Task	📝 Description	📦 Dataset ID
bread_place	Pick bread slice and place on plate	karthikpythireddi93/breakfast_sandiwch
cucumber_place	Pick cucumber and place on bread	same
carrot_place	Stack carrot slices	same
ketchup_place	Pour ketchup	same
final_bread_place	Place top slice to complete sandwich	same
📸 Record Each Task
lerobot-record \
  --robot.type=so101_follower --robot.port="$PORT_FOLLOWER" --robot.id=ss_follower \
  --teleop.type=so101_leader  --teleop.port="$PORT_LEADER"  --teleop.id=ss_leader \
  --robot.cameras='{
    "top":{"type":"opencv","index_or_path":"/dev/cam-top","width":640,"height":480,"fps":30},
    "front":{"type":"opencv","index_or_path":"/dev/cam-front","width":640,"height":480,"fps":30}
  }' \
  --dataset.repo_id=karthikpythireddi93/breakfast_sandiwch \
  --dataset.single_task="bread_place" \
  --dataset.num_episodes=20 \
  --dataset.episode_time_s=100 \
  --dataset.reset_time_s=15 \
  --dataset.private=false --dataset.push_to_hub=true \
  --dataset.tags='["task:bread_place","sandwich","so101"]' \
  --display_data=true


🔁 Repeat for:
cucumber_place, carrot_place, ketchup_place, and final_bread_place
(change only --dataset.single_task and --dataset.tags)

🎹 Keyboard Shortcuts

→ Next episode

← Re-record

🧠 Training
lerobot-train \
  --dataset.repo_id=karthikpythireddi93/breakfast_sandiwch \
  --policy=act \
  --train.epochs=10 \
  --device=cuda:0

🤖 Inference / Replay
🔁 Replay a Recorded Episode
lerobot-replay \
  --robot.type=so101_follower \
  --robot.port="$PORT_FOLLOWER" \
  --robot.id=ss_follower \
  --dataset.repo_id=karthikpythireddi93/make_a_sandwich \
  --dataset.single_task="e2e_sandwich" \
  --dataset.episode_index=0

🧩 Evaluate a Trained Policy
lerobot-record \
  --robot.type=so101_follower --robot.port="$PORT_FOLLOWER" --robot.id=ss_follower \
  --teleop.type=so101_leader  --teleop.port="$PORT_LEADER"  --teleop.id=ss_leader \
  --robot.cameras='{
    "top":{"type":"opencv","index_or_path":"/dev/cam-top","width":640,"height":480,"fps":30},
    "front":{"type":"opencv","index_or_path":"/dev/cam-front","width":640,"height":480,"fps":30}
  }' \
  --dataset.repo_id=karthikpythireddi93/eval_ACT \
  --dataset.single_task="cucumber_place" \
  --dataset.num_episodes=1 \
  --dataset.episode_time_s=500 \
  --dataset.reset_time_s=15 \
  --display_data=true \
  --policy.path=karthikpythireddi93/Sandwich-ACT \
  --policy.device=cuda:0

📂 Folder Structure
data/
└── recordings/
    ├── sandwich_task1_bread_place/
    ├── sandwich_task2_cucumber_place/
    ├── sandwich_task3_carrot_place/
    ├── sandwich_task4_ketchup_place/
    └── sandwich_task5_final_bread_place/

🌐 Resources

📜 License

© 2025 Karthik Pythireddi

Licensed under the Apache License, Version 2.0
You may obtain a copy of the License at:
👉 http://www.apache.org/licenses/LICENSE-2.0
