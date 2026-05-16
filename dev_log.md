# Development Log — Programming Robot Arms Tutorial

## Project Overview
A multi-chapter HTML tutorial on programming the Franka Emika Panda robot arm.
- **Target audience:** High school students, written at undergraduate professional level
- **Hardware:** Franka Emika Panda (7-DOF)
- **Software:** Python + ROS 2, MoveIt 2, franka_ros2
- **Format:** Multi-file HTML, shared CSS, inline SVG diagrams, MathJax equations, highlight.js code

---

## Session 1 — Initial Planning & Scaffolding

### Decisions Made
- **File layout:** `index.html` + `style.css` + `ch01`–`ch10.html` + `appendix.html`
- **CSS layout:** CSS Grid, 220 px fixed sidebar + 900 px max-width content column (MIT OCW style)
- **Typography:** Inter (body), JetBrains Mono (code) via Google Fonts CDN
- **Math rendering:** MathJax 3 CDN (`\(...\)` inline, `\[...\]` display)
- **Code highlighting:** highlight.js CDN, tokyo-night-dark theme
- **Figures:** `<figure>` + `<figcaption>`, IEEE-style numbering (Figure 1.1, 2.3, …)
- **Citations:** Numbered inline `[1]`, `[2]`; full IEEE list at end of each chapter
- **Control modes to cover:** Position (joint & Cartesian), Velocity, MIT impedance/torque
  τ = Kp·(q_d − q) + Kd·(dq_d − dq) + τ_ff  (Hogan 1985)
- **Approach:** Simulation first (Gazebo + RViz), then real hardware

### Files Created
| File | Contents |
|---|---|
| `style.css` | Full shared stylesheet — grid layout, colors, fonts, figure/code styling, print media query, responsive breakpoint at 820 px |
| `index.html` | Cover page — hero banner, prerequisites grid, hardware/software requirements tables, 10-chapter TOC card grid, "how to use" section, IEEE references |
| `ch01.html` | Chapter 1: Foundations & Toolchain |
| `ch02.html` | Chapter 2: Kinematics & Coordinate Frames |
| `ch03.html` | Chapter 3: Simulation Environment |
| `ch04.html` | Chapter 4: Position Control (Joint Space) |

---

## Session 1 — Chapter Contents

### Chapter 1 — Foundations & Toolchain
**Sections:** §1.1 The Franka Panda · §1.2 ROS 2 Overview · §1.3 The Software Stack · §1.4 Installation · §1.5 Verification · Exercises · References

**Diagrams:**
- Figure 1.1 — Panda anatomy (7 joints, links, EEF, torque sensors), annotated SVG
- Figure 1.2 — ROS 2 node graph (franka_ros2, robot_state_publisher, controller_manager, move_group, rviz2, your_node.py)
- Figure 1.3 — Software stack layers (hardware → libfranka → franka_ros2 → ros2_control → ROS 2 → MoveIt 2 → Python)

**Code examples:** `verify_install.sh`, `minimal_ros2_node.py`

### Chapter 2 — Kinematics & Coordinate Frames
**Sections:** §2.1 Coordinate Frames · §2.2 DH Parameters · §2.3 Forward Kinematics · §2.4 Inverse Kinematics · §2.5 The Jacobian · §2.6 Workspace & Singularities · §2.7 Code Examples · Exercises · References

**Diagrams:**
- Figure 2.1 — DH convention 4-step illustration
- Figure 2.2 — Panda kinematic chain with 7 coordinate frames
- Figure 2.3 — Jacobian block diagram (q̇ → J → linear/angular EEF velocity)
- Figure 2.4 — Workspace envelope (side view + top view)
- Figure 2.5 — Singularity configurations (wrist + shoulder)

**Code examples:** `fk_manual.py`, `jacobian_numerical.py`

### Chapter 3 — Simulation Environment
**Sections:** §3.1 Component Overview · §3.2 URDF & XACRO · §3.3 TF Tree · §3.4 Controller Manager · §3.5 Launching Simulation · §3.6 Code Examples · Exercises · References

**Diagrams:**
- Figure 3.1 — Simulation component map (Gazebo, robot_state_publisher, controller_manager, move_group, RViz, user_node)
- Figure 3.2 — URDF/XACRO include tree
- Figure 3.3 — TF tree (world → panda_link0 → … → panda_hand → fingers)
- Figure 3.4 — Controller manager state machine (UNCONFIGURED → INACTIVE → ACTIVE → ERROR)

**Code examples:** `launch_sim.sh`, `inspect_tf.sh`, `list_controllers.sh`

### Chapter 4 — Position Control (Joint Space)
**Sections:** §4.1 MoveIt 2 Planning Pipeline · §4.2 Joint Trajectory Controller · §4.3 Sending a Joint Goal · §4.4 Monitoring Execution · §4.5 Code Examples · Exercises · References

**Diagrams:**
- Figure 4.1 — MoveIt 2 joint-space planning pipeline (goal → OMPL → post-process → JTC → robot + feedback)
- Figure 4.2 — Joint trajectory time-series plot (SVG)
- Figure 4.3 — Action client–server sequence diagram

**Code examples:** `joint_goal_client.py`, `joint_trajectory_client.py`, `monitor_joint_states.py`

---

## Session 1 — SVG Diagram Fixes

### Problem identified
User screenshot review revealed multiple SVG rendering problems across ch01 and ch02.

### Fixes applied

#### Figure 2.1 — DH Convention (ch02.html)
**Step 1 — text overflow:**
- `x_{i-1}` label was at x=138 in a 145 px panel → clipped at right edge
- Fixed: moved label below the arrow at (x=92, y=153)
- `x′ (rotated)` label was too long → clipped
- Fixed: shortened to `x′` at (x=116, y=102)

**Step 1 — bad θ arc:**
- Original used relative move `m 45,0 a 45,45 0 0,0 -17,-38` — arc started from an offset point, not the joint origin
- Fixed: replaced with absolute coords `M 114 140 A 42 42 0 0 0 108 119` centered at (72, 140)

**Step 4 — tilted α arc:**
- Original `m 0,-50 a 50,50 0 0,1 22,-25` started 50 px above origin then drew a relative arc → visually tilted
- Fixed: `M 65 100 A 50 50 0 0 1 89 106` where start and end are both at radius 50 from center (65, 150)

### Pending SVG fixes (identified, not yet applied)
- **Figure 1.1 (ch01):** Arm is drawn as a vertical stack of rectangles; needs redesign to articulated bent-arm pose
- **Figure 1.3 (ch01):** Right-side annotation text at x=452 overflows 580 px viewBox
- **Figure 2.4 (ch02):** Side view workspace shape is a lens (two mirrored arcs); should be an annular sector
- **Figure 2.5 (ch02):** Right panel EEF box overlaps J5 circle; left panel annotation box overlaps arm geometry

### Memory file created
`~/.claude/projects/.../memory/feedback_svg_diagrams.md` — checklist of SVG rules to prevent future recurrence (text margins, overlap checks, arm anatomy, arc math, workspace shape)

---

## Session 2 — Vocabulary & Accessibility Fixes

### Problem identified
User noted that technical terms were appearing without definition, which is problematic for a high school audience.

### Fixes applied

#### ch01.html — §1.1.1 Physical Anatomy
- Added definition of **end-effector (EEF)** in body text before Figure 1.1, which was the term's first appearance; previously it only appeared as a diagram label with no prose explanation.

#### ch01.html — Table 1.1
- **Payload** row: added inline gloss `(maximum mass of the attached tool + object)`
- **Repeatability** row: added inline gloss `(how consistently it returns to the exact same point)`

#### ch01.html — §1.3 Software Stack (MoveIt 2 description)
- **"pose"** was used without definition; added `(the desired position and orientation of the end-effector)` inline on first use. Term is formally defined in ch02 §2.1 but appeared in ch01 first.

#### ch02.html — §2.7.2 Numerical Jacobian Estimation
- **"manipulability"** appeared in code and exercises but not in prose; added an explanatory sentence before the code block with the formula `w = √det(J J^T)`, plain-language meaning (zero at singularities, larger = more dexterous), and Yoshikawa [4] citation.

---

## Planned Chapters (not yet written)

| Chapter | Topic | Key Diagrams |
|---|---|---|
| ch05.html | Position Control — Cartesian | SE(3) pose, compute_cartesian_path, frame chain |
| ch06.html | Velocity Control | Closed-loop block diagram, trapezoidal profile, timing |
| ch07.html | Impedance & Torque Control *(centerpiece)* | Mass-spring-damper, full block diagram, Kp/Kd space |
| ch08.html | MoveIt 2 Deep Dive | OMPL planner trees, planning scene, constraint cone |
| ch09.html | Real Hardware Transition | Safety flowchart, hardware comms, latency comparison |
| ch10.html | Pick & Place Capstone | FSM state diagram, 3D EEF trajectory, full node graph |
| appendix.html | Appendices A–C | ROS 2/Python primer, DH table, troubleshooting |
