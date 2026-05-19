# Development Log — Programming Robot Arms Tutorial

## Project Overview
A multi-chapter HTML tutorial on programming the Franka Emika Panda robot arm.
- **Target audience:** High school students, written at undergraduate professional level
- **Hardware:** Franka Emika Panda (7-DOF)
- **Software:** Python + ROS 2, MoveIt 2, franka_ros2
- **Format:** Multi-file HTML, shared CSS, inline SVG diagrams, MathJax equations, highlight.js code

---

## 2026-05-15 — Initial Scaffolding & Chapter Authoring

### Architecture Decisions
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

### Chapter Contents

#### Chapter 1 — Foundations & Toolchain
**Sections:** §1.1 The Franka Panda · §1.2 ROS 2 Overview · §1.3 The Software Stack · §1.4 Installation · §1.5 Verification · Exercises · References

**Diagrams:**
- Figure 1.1 — Panda anatomy (7 joints, links, EEF, torque sensors), annotated SVG
- Figure 1.2 — ROS 2 node graph (franka_ros2, robot_state_publisher, controller_manager, move_group, rviz2, your_node.py)
- Figure 1.3 — Software stack layers (hardware → libfranka → franka_ros2 → ros2_control → ROS 2 → MoveIt 2 → Python)

**Code examples:** `verify_install.sh`, `minimal_ros2_node.py`

#### Chapter 2 — Kinematics & Coordinate Frames
**Sections:** §2.1 Coordinate Frames · §2.2 DH Parameters · §2.3 Forward Kinematics · §2.4 Inverse Kinematics · §2.5 The Jacobian · §2.6 Workspace & Singularities · §2.7 Code Examples · Exercises · References

**Diagrams:**
- Figure 2.1 — DH convention 4-step illustration
- Figure 2.2 — Panda kinematic chain with 7 coordinate frames
- Figure 2.3 — Jacobian block diagram (q̇ → J → linear/angular EEF velocity)
- Figure 2.4 — Workspace envelope (side view + top view)
- Figure 2.5 — Singularity configurations (wrist + shoulder)

**Code examples:** `fk_manual.py`, `jacobian_numerical.py`

#### Chapter 3 — Simulation Environment
**Sections:** §3.1 Component Overview · §3.2 URDF & XACRO · §3.3 TF Tree · §3.4 Controller Manager · §3.5 Launching Simulation · §3.6 Code Examples · Exercises · References

**Diagrams:**
- Figure 3.1 — Simulation component map (Gazebo, robot_state_publisher, controller_manager, move_group, RViz, user_node)
- Figure 3.2 — URDF/XACRO include tree
- Figure 3.3 — TF tree (world → panda_link0 → … → panda_hand → fingers)
- Figure 3.4 — Controller manager state machine (UNCONFIGURED → INACTIVE → ACTIVE → ERROR)

**Code examples:** `launch_sim.sh`, `inspect_tf.sh`, `list_controllers.sh`

#### Chapter 4 — Position Control (Joint Space)
**Sections:** §4.1 MoveIt 2 Planning Pipeline · §4.2 Joint Trajectory Controller · §4.3 Sending a Joint Goal · §4.4 Monitoring Execution · §4.5 Code Examples · Exercises · References

**Diagrams:**
- Figure 4.1 — MoveIt 2 joint-space planning pipeline (goal → OMPL → post-process → JTC → robot + feedback)
- Figure 4.2 — Joint trajectory time-series plot (SVG)
- Figure 4.3 — Action client–server sequence diagram

**Code examples:** `joint_goal_client.py`, `joint_trajectory_client.py`, `monitor_joint_states.py`

---

## 2026-05-15 — SVG Diagram Quality Fixes

### Problem
Screenshot review revealed multiple SVG rendering defects across ch01 and ch02: text overflowing panel boundaries, incorrectly anchored arcs, and overlapping elements.

### Fixes Applied

#### Figure 2.1 — DH Convention (ch02.html)
**Step 1 — text overflow:**
- `x_{i-1}` label was at x=138 in a 145 px panel → clipped at right edge
- Fixed: moved label below the arrow at (x=92, y=153)
- `x′ (rotated)` label was too long → clipped
- Fixed: shortened to `x′` at (x=116, y=102)

**Step 1 — bad theta arc:**
- Original used relative move `m 45,0 a 45,45 0 0,0 -17,-38` — arc started from an offset point, not the joint origin
- Fixed: replaced with absolute coords `M 114 140 A 42 42 0 0 0 108 119` centered at (72, 140)

**Step 4 — tilted alpha arc:**
- Original `m 0,-50 a 50,50 0 0,1 22,-25` started 50 px above origin then drew a relative arc → visually tilted
- Fixed: `M 65 100 A 50 50 0 0 1 89 106` where start and end are both at radius 50 from center (65, 150)

### Known Issues (deferred)
- **Figure 1.1 (ch01):** Arm drawn as vertical stack of rectangles; candidate for articulated bent-arm redesign
- **Figure 1.3 (ch01):** Right-side annotation text at x=452 may overflow 580 px viewBox at some zoom levels
- **Figure 2.4 (ch02):** Side view workspace shape is a lens (two mirrored arcs); should be an annular sector
- **Figure 2.5 (ch02):** Right panel EEF box overlaps J5 circle; left panel annotation box overlaps arm geometry

### Process Improvement
Created `~/.claude/projects/.../memory/feedback_svg_diagrams.md` — a persistent checklist of SVG authoring rules (text margins, overlap clearance, arm anatomy, arc math, workspace shape) applied to all future diagrams.

---

## 2026-05-15 — Vocabulary & Accessibility Pass

### Problem
Technical terms appeared without definition, which is problematic for the target high school audience.

### Fixes Applied

#### ch01.html — §1.1.1 Physical Anatomy
- Added definition of **end-effector (EEF)** in body text before Figure 1.1; previously appeared only as a diagram label with no prose explanation.

#### ch01.html — Table 1.1
- **Payload** row: added inline gloss `(maximum mass of the attached tool + object)`
- **Repeatability** row: added inline gloss `(how consistently it returns to the exact same point)`

#### ch01.html — §1.3 Software Stack
- **"pose"** used without definition; added `(the desired position and orientation of the end-effector)` inline on first use. Term is formally defined in ch02 §2.1 but appeared in ch01 first.

#### ch02.html — §2.7.2 Numerical Jacobian Estimation
- **"manipulability"** appeared in code and exercises but not in prose; added an explanatory sentence before the code block with the formula `w = sqrt(det(J J^T))`, plain-language meaning (zero at singularities, larger = more dexterous), and Yoshikawa [4] citation.

---

## 2026-05-15 — Appendix D & ROS Concepts Diagram

### New Figure 1.2 — ROS 2 Communication Primitives (ch01.html)
**Location:** Inserted after §1.2.1 Core Concepts list, before the Panda-specific node graph.

**Content:** Three side-by-side panels in a single SVG (viewBox 620×268):
- **Topic panel** — Publisher Node → `/topic_name` arrow → Subscriber Node. Labeled "continuous data stream, no reply required." Example: `/joint_states` at 1 kHz.
- **Service panel** — Client Node sends Request (solid arrow down), Server Node returns Response (dashed arrow up). Labeled "synchronous call-reply." Example: `add_collision_object`.
- **Action panel** — Action Client sends Goal (solid arrow down), Action Server streams Feedback ×N (dashed arrow up), then sends final Result (solid arrow up). Labeled "async, long-running task, cancellable." Example: MoveGroup motion plan.

**SVG quality:** All text within panel boundaries, 8 px minimum clearance, no raw notation tokens.

**Figure renumbering:** Old Figure 1.2 → Figure 1.3; old Figure 1.3 → Figure 1.4. All in-text references and figure IDs updated accordingly.

### New File — appendix-hardware.html (Appendix D)

| Section | Content |
|---|---|
| D.1 Form Factor | Desktop cobot overview. Table: arm mass (18 kg), base footprint (120×120 mm), bolt circle (85 mm, 4× M6), stowed height (~850 mm), IP40, cable routing. Figure D.1: side-view silhouette with dimension arrows; top-view mounting diagram with bolt circle and workspace footprint. |
| D.2 Mechanical Specs | All-revolute joint explanation. Table: per-joint range (deg), max speed (deg/s), max torque (Nm), link offset (mm) for J1–J7 + EEF. Warning callout on hardware-enforced limits. Note on J4 asymmetric range as DH convention artifact. |
| D.3 Electrical Specs | FCU architecture overview. Table: mains input (100–240 V AC), peak power (~1500 W), idle power (~50 W), internal 48 V DC bus, 1 Gbps Ethernet FCI, PREEMPT_RT kernel requirement, e-stop (IEC 62061 cat. 3). Warning: dedicated NIC required for FCI. |
| D.4 Motion Envelope | Interaction between reach, payload, speed, and acceleration. Table: max reach (855 mm), max payload (3 kg), repeatability (±0.1 mm ISO 9283), max EEF speed (~1.7 m/s), max EEF acceleration (~13 m/s²), collision force threshold (10 N), joint acceleration and jerk limits. Figure D.2: horizontal bar chart of per-joint max torque; explains 7:1 arm-to-wrist torque ratio. |
| D.5 Safety & Compliance | ISO/TS 15066 standard. Three reflex types: joint torque, joint velocity, Cartesian contact (default 10 N / 10 Nm). Soft stops vs. hard mechanical limits. Why 1 kHz control loop makes soft stops effective. Tip on `setCollisionBehavior()` threshold adjustment. |

**SVG figures:**
- **Figure D.1** (viewBox 580×280): Two-panel schematic. Left: stowed-arm side view with red double-headed height arrow (~850 mm), blue max-reach arc (855 mm), purple base-width bracket (120 mm). Right: top-view mounting diagram with 120×120 mm base plate, 85 mm bolt circle, 4 mounting holes, outer workspace footprint.
- **Figure D.2** (viewBox 500×240): Horizontal bar chart. Scale: 350 px = 90 Nm. J1–J4 bars at 338 px (87 Nm, dark blue); J5–J7 bars at 47 px (12 Nm, light blue). Gridlines at 30 and 60 Nm; labeled legend boxes at right. All value labels clear of bar edges; no overflow past 500 px viewBox width.

### Sidebar Updated — All 5 HTML Files
Added `D · Robot Arm Hardware Specs` link to the Appendices nav in `index.html`, `ch01.html`, `ch02.html`, `ch03.html`, `ch04.html`, and `appendix-hardware.html` (active state).

---

## 2026-05-16 — Chapter 5: Position Control — Cartesian Space

### New File — ch05.html

Full chapter (same layout as ch01–ch04), 8 logical sections.

#### Sections & Content

| Section | Content |
|---|---|
| §5.1 Pose Representation in SE(3) | Homogeneous transform T = [R \| p; 0 0 0 1]; explains rotation matrix R and position vector p; Figure 5.1 SVG showing world frame + rotated EEF frame + purple dashed position vector + right-half matrix notation panel |
| §5.2 Orientation Representations | RPY vs Quaternion vs Axis-Angle comparison table; common quaternion lookup table (identity, pointing-down, rotated 90° about Z); scipy conversion tip `R.from_euler('xyz', [roll,pitch,yaw]).as_quat()` |
| §5.3 Pose Goals in MoveIt 2 | IK overview; TRAC-IK hybrid analytical/numerical solver; pose-goal vs joint-goal decision table; PoseStamped workflow |
| §5.4 Straight-Line Cartesian Paths | `compute_cartesian_path()` explanation; fraction concept; max_step guidance (5 mm default, 1 mm high-precision, 10 mm rough scan); Figure 5.2 SVG comparing curved joint-interpolated arc (orange dashed) vs straight-line Cartesian path with 5 green waypoints |
| §5.5 Reference Frames | Planning frame vs EEF link distinction; Figure 5.3 SVG horizontal chain: world → panda_link0 → panda_hand → panda_EE with labeled arrows |
| §5.6 Code Examples | `move_to_pose.py` (single-pose IK goal via MoveIt 2 Python API) and `cartesian_path.py` (5-waypoint straight-line sweep, MIN_FRACTION = 0.95 guard) |
| Exercises | 4 exercises: modify quaternion to point EEF sideways, add pick/place waypoints, measure path deviation, tune max_step |
| Key Terms / References | 12 key terms; 5 IEEE references (Lozano-Pérez 1981, Chirikjian 2011, Quigley 2015, Beeson & Ames 2015, Franka Emika 2023) |

#### Figures

| Figure | Description |
|---|---|
| Figure 5.1 (viewBox 556×285) | Left panel: world frame XYZ at (70,235), EEF frame with tilted axes at (195,112), purple dashed position vector p. Right panel: matrix block diagram with blue R (3×3) and red p (3×1) |
| Figure 5.2 (viewBox 556×255) | Left panel: curved orange dashed Bézier arc (joint-interpolated). Right panel: straight-line path with 5 green waypoints at evenly spaced X positions |
| Figure 5.3 (viewBox 520×148) | Horizontal chain: world (dark) → panda_link0 (blue) → panda_hand (green) → panda_EE (orange), with labeled transforms |

#### Sidebar
Includes Appendix D link; ch04.html footer already linked to ch05.html.

---

## 2026-05-19 — Chapter 6: Velocity Control

### New File — ch06.html

Full chapter (same layout as ch01–ch05), 8 logical sections.

#### Sections & Content

| Section | Content |
|---|---|
| §6.1 Why Velocity Control? | Comparison table: position control vs. velocity control (use cases, replanning latency, safety complexity, ros2_control interface, command type); FCI 1 kHz watchdog explained |
| §6.2 The Velocity Control Loop | Open-loop vs. closed-loop distinction; PD controller equation v_cmd = Kp·e + Kd·ė; Figure 6.1 closed-loop block diagram |
| §6.3 Velocity Profiles | Trapezoidal profile derivation (t_acc, t_cruise formulas); Figure 6.2 trapezoidal plot; S-curve profile overview |
| §6.4 Jacobian-Based Cartesian Velocity Control | ẋ = J(q)q̇ inversion; redundancy (n=7 > m=6); DLS pseudoinverse J_dls = Jᵀ(JJᵀ + λ²I)⁻¹; Figure 6.3 full velocity control loop diagram |
| §6.5 The ros2_control Interface | JointGroupVelocityController YAML config; spawner / switch_controllers commands; watchdog warning callout; Float64MultiArray publish example |
| §6.6 Code Examples | velocity_ramp.py (trapezoidal single-joint ramp at 200 Hz) and jacobian_velocity.py (DLS J+ with per-joint safety clamp, 200 Hz) |
| Exercises | 4 exercises: multi-joint ramp with limit guards, triangular profile derivation, damping experiment, proportional Cartesian position control |
| Key Terms / References | 10 key terms; 5 IEEE references (Siciliano 2009, Chiaverini 2016, Maciejewski 1985, Nakamura 1986, Franka FCI docs) |

#### Figures

| Figure | Description |
|---|---|
| Figure 6.1 (viewBox 560×185) | Closed-loop block diagram: v_ref → summing junction → Controller (Kp·e+Kd·ė) → Robot/Plant → v_actual; red dashed feedback path from branch dot back to junction |
| Figure 6.2 (viewBox 480×210) | Trapezoidal velocity profile: blue trapezoid with shaded fill; dashed v_max reference; phase labels Accel/Cruise/Decel; t₁/t₂/t₃ ticks |
| Figure 6.3 (viewBox 560×185) | Jacobian velocity loop: v_EEF desired → summing junction → J⁺(q) → Robot → J(q) → v_EEF actual; red dashed feedback |

---

## Planned Work

| File | Topic | Key Diagrams |
|---|---|---|
| ~~`ch05.html`~~ | ~~Position Control — Cartesian~~ | ✓ Complete (2026-05-16) |
| ~~`ch06.html`~~ | ~~Velocity Control~~ | ✓ Complete (2026-05-19) |
| `ch06.html` | Velocity Control | Closed-loop block diagram, trapezoidal profile, timing |
| `ch07.html` | Impedance & Torque Control *(centerpiece)* | Mass-spring-damper, full block diagram, Kp/Kd space |
| `ch08.html` | MoveIt 2 Deep Dive | OMPL planner trees, planning scene, constraint cone |
| `ch09.html` | Real Hardware Transition | Safety flowchart, hardware comms, latency comparison |
| `ch10.html` | Pick & Place Capstone | FSM state diagram, 3D EEF trajectory, full node graph |
| `appendix.html` | Appendices A–C | ROS 2/Python primer, DH table, troubleshooting |
