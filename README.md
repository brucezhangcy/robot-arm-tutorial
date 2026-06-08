# Programming Robot Arms: A Technical Tutorial

A 10-chapter HTML tutorial on programming the **Franka Emika Panda** (7-DOF collaborative robot arm) using Python, ROS 2, and MoveIt 2. Written for high school students and early undergraduates with no prior robotics experience.

---

## What This Tutorial Covers

| Chapter | Topic |
|---|---|
| Ch 1 | Foundations & Toolchain — hardware overview, ROS 2 concepts, installation |
| Ch 2 | Kinematics & Coordinate Frames — transforms, DH parameters, Jacobian |
| Ch 3 | Simulation Environment — Gazebo setup, URDF, RViz |
| Ch 4 | Position Control — Joint Space — joint angle goals, MoveIt 2 API |
| Ch 5 | Position Control — Cartesian Space — SE(3) poses, IK, path planning |
| Ch 6 | Velocity Control — trapezoidal profiles, PD control, singularity avoidance |
| Ch 7 | Impedance & Torque Control — mass-spring-damper model, compliance |
| Ch 8 | MoveIt 2 Deep Dive — planning pipeline, OMPL, collision objects |
| Ch 9 | Real Hardware Transition — FCI, safety, latency considerations |
| Ch 10 | Pick & Place Capstone — full pipeline + Isaac Sim bonus project |

**Appendices:** A — ROS 2 & Python Quick Reference · B — Panda DH Parameters · C — Troubleshooting Guide · D — Hardware Specifications

---

## File Structure

```
index.html             # Cover page and table of contents
ch01.html – ch10.html  # Main chapters
appendix.html          # Appendices A, B, C
appendix-hardware.html # Appendix D: hardware specs
panda_viz.html         # Interactive 3D arm visualizer
style.css              # Shared stylesheet (all pages)
dev_log.md             # Development log (session notes, bugs, decisions)
```

All pages are self-contained static HTML. No build step — open any file directly in a browser.

---

## Rendering Requirements

| Feature | How it works |
|---|---|
| Math equations | MathJax 3 CDN (`\(...\)` inline, `\[...\]` display) |
| Code highlighting | highlight.js CDN, tokyo-night-dark theme |
| Fonts | Inter (body) + JetBrains Mono (code) via Google Fonts CDN |
| Diagrams | Inline SVG — no external image files |

Requires an internet connection for CDN resources. Works in any modern browser (Chrome, Firefox, Safari, Edge).

---

## Software Stack

The tutorial targets **ROS 2 Humble** on **Ubuntu 22.04**.

> ⚠️ **Important — Chapter 10 Python capstone dependency conflict.**
> The Ch. 10 `pick_and_place.py` uses the **`moveit_py`** Python binding
> (`from moveit.planning import MoveItPy`). As of this writing `moveit_py` is **not
> released for Humble** via apt — it is only packaged for **Iron / Jazzy / Rolling**
> (`ros-<distro>-moveit-py`). Conversely, **`franka_msgs`** (used by the gripper
> actions) is packaged for **Humble but not Iron**. So **no single ROS 2 distro on
> Ubuntu 22.04 apt-satisfies both** `moveit_py` and `franka_msgs` at once.
> To actually run the Ch. 10 Python capstone you must either:
> - use **Iron or Jazzy** (which have `moveit_py`) and **build `franka_msgs` from
>   source** (it is an interface-only package — clone `frankaemika/franka_ros2` and
>   `colcon build --packages-select franka_msgs`), or
> - stay on **Humble** and **build `moveit_py` from source**.
>
> The other chapters (1–9), which use the C++ `move_group` / `ros2 launch` workflow,
> are fine on Humble as written.

### Option A — Docker (recommended for macOS / Windows)

```bash
docker pull osrf/ros:humble-desktop
docker run -it --rm \
  -v $(pwd):/home/user/ws \
  -p 8765:8765 \
  osrf/ros:humble-desktop bash

# Inside container:
source /opt/ros/humble/setup.bash
apt-get install -y ros-humble-moveit ros-humble-franka-description \
  ros-humble-ros-gz ros-humble-ign-ros2-control ros-humble-foxglove-bridge
```

### Option B — Native Ubuntu 22.04

```bash
# ROS 2 Humble (follow docs.ros.org/en/humble/Installation.html)
sudo apt install -y \
  ros-humble-moveit \
  ros-humble-franka-description \
  ros-humble-franka-msgs \
  ros-humble-ros-gz \
  ros-humble-ign-ros2-control \
  ros-humble-foxglove-bridge
```

### Launch the Simulation

```bash
# Terminal 1 — Gazebo (fr3 robot, headless)
ros2 launch franka_gazebo_bringup gazebo_franka_arm_example_controller.launch.py \
  robot_type:=fer gz_args:='-r empty.sdf'

# Terminal 2 — MoveIt 2
ros2 launch franka_fr3_moveit_config moveit.launch.py \
  robot_ip:=dont-care use_fake_hardware:=true

# Browser — Foxglove Studio (no install)
# Open https://foxglove.dev/app → WebSocket → ws://localhost:8765
```

> **macOS note:** GUI tools (RViz, Gazebo window) require a Linux desktop environment. On macOS, use headless mode (`gz_args:='-r -s ...'`) and Foxglove Studio in the browser as the visualizer.

---

## Prerequisites

- Python (any recent version) — loops, functions, classes
- Basic algebra and trigonometry (vectors, sin/cos)
- No prior robotics or ROS experience required

---

## Key Concepts Introduced

- **Homogeneous transforms** and the SE(3) pose representation
- **Forward and inverse kinematics** (DH parameters, KDL solver)
- **Jacobian** — velocity mapping and singularity detection
- **OMPL** motion planning through MoveIt 2
- **ROS 2** topics, services, actions, and `ros2_control`
- **PD / impedance control** — spring-damper model in joint and Cartesian space
- **Isaac Sim** — photorealistic GPU simulation for AI/vision tasks (ch10 bonus)
