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

## 2026-05-24 — Chapters 7–10 and Appendix A–C (Tutorial Complete)

### New Files

#### ch07.html — Impedance & Torque Control
- §7.1 What Is Impedance Control? (virtual spring analogy, stiffness vs. damping table)
- §7.2 Mass-Spring-Damper Model (Figure 7.1 SVG; M·ẍ + B·ẋ + Kx = F_ext)
- §7.3 Impedance Control Law (joint-space and Cartesian-space forms; Figure 7.2 block diagram; gravity/friction feed-forward)
- §7.4 Stiffness-Damping Space (critical damping Kd = 2√(M·Kp); Figure 7.3 parameter space plot)
- §7.5 Implementation on the Panda (CartesianImpedanceController YAML; runtime stiffness update)
- §7.6 Code Examples (compliance_demo.py — gravity-comp mode; joint_impedance.py — direct torque interface)

#### ch08.html — MoveIt 2 Deep Dive
- §8.1 Architecture (Figure 8.1 component diagram; move_group, OMPL, FCL, planning scene, ros2_control)
- §8.2 Planning Scene (Figure 8.2 top-down scene; add_box(); attach_object())
- §8.3 Motion Planners (Figure 8.3 OMPL RRT tree; planner comparison table: RRTConnect, RRT*, LBKPIECE, PRM, STOMP)
- §8.4 Motion Constraints (4 constraint types: Joint, Position, Orientation, Visibility; upright_constraint() example)
- §8.5 Advanced Techniques (pipeline plugins YAML; Pilz PTP/LIN/CIRC; trajectory caching)
- §8.6 Code Examples (moveit_deep_dive.py — full pipeline with scene + constraints + planner selection)

#### ch09.html — Real Hardware Transition
- §9.1 Sim vs. Real comparison table (loop rate, comms, noise, collision detection, latency)
- §9.2 Network Setup (Figure 9.1 hardware comms diagram; static IP config; RT kernel verification)
- §9.3 Safety Procedures (Figure 9.2 bringup flowchart; E-stop recovery; collision threshold adjustment)
- §9.4 Hardware Bringup (launch sequence; homing command; Python connection verify)
- §9.5 Latency & Timing (Figure 9.3 latency bar chart; loop_timer.py measurement tool)
- §9.6 Code Examples (hw_switch.py — identical code for sim/hardware via --use-sim flag)

#### ch10.html — Pick & Place Capstone
- §10.1 Task Overview (9-step deterministic sequence)
- §10.2 FSM Design (Figure 10.1 state diagram: INIT→PRE-GRASP→DESCEND→GRASP→LIFT→TRANSPORT→PLACE→RELEASE→RETRACT→DONE)
- §10.3 Node Graph (Figure 10.2: pick_place_node, move_group, gripper_node, robot_state_publisher, ros2_control, planning scene, RViz, hardware)
- §10.4 EEF Trajectory (Figure 10.3 X-Z plane view; Cartesian descents/ascents; straight-line transport)
- §10.5 Full Implementation (pick_and_place.py — 200-line FSM with gripper actions, Cartesian paths, error handling)

#### appendix.html — Appendices A–C
- §A: ROS 2 & Python Quick Reference (CLI cheat sheet, minimal node pattern, MoveItPy cheat sheet, common Python patterns table)
- §B: Panda DH Parameter Table (modified DH for J1–J7 + flange; notes on J4 asymmetric range; FK implementation in Python)
- §C: Troubleshooting Guide (4 tables: installation, simulation, real hardware, MoveIt 2 planning; diagnostic command block)

---

## 2026-05-28 — Full SVG Quality Audit and Fixes

### Scope
Audited every SVG figure across all 10 chapters, appendix pages, and appendix-hardware.html for: text overflowing viewBox bounds, element overlaps, invisible/too-small elements, and labels too close to container edges.

### Fixes Applied

| File | Figure | Issue | Fix |
|---|---|---|---|
| `ch01.html` | Fig 1.1 | Caption text baseline y=516 in 520 px viewBox (4 px margin, potential clip) | Moved text to y=508 |
| `ch04.html` | Fig 4.2 | "t (s)" axis label at x=540 overflows 560 px viewBox right edge | Moved label to x=525 |
| `ch07.html` | Fig 7.1 | "K" and "(stiffness)" labels too close to spring peaks at y=70 | Moved "K" y=61→52, "(stiffness)" y=73→63 |
| `ch07.html` | Fig 7.1 | "(damping)" at y=105 and "B" at y=113 reversed order and essentially touching | Swapped order: "B" now at y=102 (above), "(damping)" at y=113 (below) |
| `ch08.html` | Fig 8.2 | "Work surface" label at y=192 in 200 px viewBox (8 px margin, potential clip) | Expanded viewBox to 215 px; moved text to y=207 |
| `ch09.html` | Fig 9.3 | Row 4 (MoveIt plan) hw bar height=2 (invisible); entire row crammed against x-axis at y=175 | Expanded viewBox 215→230; moved x-axis to y=190; made hw bar height=9; added "~100 ms (hw)" label |
| `ch10.html` | Fig 10.1 | DONE box (x=185) overlaps INIT box (right edge x=195) by 10 px | Moved DONE box to x=210 (15 px gap); updated text center x=265→290 |
| `ch10.html` | Fig 10.1 | RETRACT→DONE arrow drawn as 3 lines (one invisible, one 1 px) | Replaced with single clean L-shaped polyline: 330,86→290,86→290,58 |

### No-Action Items (confirmed acceptable)
- **ch01 Fig 1.3** (viewBox 700×400): previously flagged as "x=452 overflow 580px" — actual viewBox is 700 px; no overflow.
- **ch02 Fig 2.4**: lens-shaped workspace is an aesthetic/accuracy concern (annular sector preferred), not a text/layout bug.
- **ch02 Fig 2.5**: J6/J7 circles touch in wrist-singularity panel — intentional visual (showing alignment).
- **ch06 Fig 6.3**: branch dot at cx=500 and arrow x1=500 are aligned; no visual gap.
- **ch09 Fig 9.3**: x-axis does NOT cut through any bar (bars end at y=168/172, axis at y=175 original; now y=190 after fix).

---

## 2026-06-02 — Readability Overhaul & Diagram Fixes

### Vocabulary Cards → Merged into End-of-Chapter Key Terms
- Removed the "New terms in this chapter — read these first" callout-note box from the top of every chapter (ch01–ch10); it duplicated the end-of-chapter Key Terms aside.
- Merged all unique terms from the removed cards into the `<aside class="key-terms">` at the end of each chapter.
- Key Terms asides were then moved to the TOP of each chapter (directly after the chapter abstract) so students see definitions before reading the content.
- `style.css`: styled `.callout-note dl dt` as blue badge pills (bold, accent-lt background, border-radius 3px) to visually distinguish terms from definitions.

### Figure Fixes
| File | Figure | Issue | Fix |
|---|---|---|---|
| `ch01.html` | Fig 1.3 (ROS 2 node graph) | `controller_manager` in far-left column; `follow_joint_trajectory` arrow was a confusing L-shape crossing the diagram | Redesigned entire SVG layout: `controller_manager` now in center column directly below `move_group`; arrow is a clean straight-down line labeled "trajectory commands" |
| `ch01.html` | Fig 1.1 (Panda anatomy) | `rotate(-30)` transform on Link 3 made arm look awkwardly bent; several joint labels unclear | Removed rotation; redrew as clean vertical schematic; wrist group (J5–J7) in lighter blue; all 7 joints labeled; annotation lines shortened |
| `ch10.html` | Fig 10.3 (EEF trajectory) | "Object" and "Goal" labels covered by vertical arrows at x=90 and x=370 | Moved "Object" label to left of box (`text-anchor="end"`); moved "Goal" label to right of its box |
| `appendix-hardware.html` | Fig D.1 (top-view mounting) | "Baseplate" and "origin" text overlapping (7 px apart over center dot) | Moved "Base plate / 120×120mm" to top of rect (y=104/116); "origin" shifted right of dot (x=440, y=152) |
| `appendix-hardware.html` | Fig D.2 (torque bar chart) | "87 Nm" value labels for J1/J2 overlapping the Arm legend box | Moved labels inside the dark blue bars as white text (`text-anchor="end"`) |

### Prose Improvements (all 10 chapters)
- Rewrote chapter abstracts in plain English for all chapters via agent
- Added transition sentences linking paragraphs and backward/forward references between chapters
- Added occasional rhetorical questions and relatable analogies
- Standardized terminology: "MoveIt 2", "end-effector", "ROS 2" consistent throughout
- `style.css`: added `.callout-summary` (purple) and `.callout-analogy` (orange) callout types

### ch10 §10.6 — Isaac Sim Bonus Project
Added complete §10.6 "Bonus Project: Visualizing Pick & Place in Isaac Sim" including:
- scene_setup.py: loads Franka + DynamicCuboid in Isaac Sim World
- replay_trajectory.py: interpolates HOME → PRE-GRASP → GRASP → PLACE joint configs at 120 steps each
- Gazebo vs. Isaac Sim comparison table

---

## 2026-06-03 (session 4) — Testing Scope Clarification, Apple Silicon Limitation, Tutorial Note

### What Was Actually Tested (and What Was Not)
Static analysis only — no runtime execution was performed. Honest breakdown:

| Test | Result |
|---|---|
| Python syntax (`py_compile`) on all 26 code blocks | ✅ 26/26 pass (after 1 bug fix) |
| Import availability inside ROS 2 Humble container | ✅ 11/11 pass |
| apt package names valid in Humble repo | ✅ 7/7 confirmed |
| Code actually runs (runtime behavior) | ❌ Not tested — needs full ROS 2 stack |
| Gazebo simulation executes | ❌ Not tested — blocked on Apple Silicon |
| MoveIt 2 planning produces correct trajectories | ❌ Not tested |
| pick_and_place.py end-to-end | ❌ Not tested |

Runtime testing requires a full Ubuntu 22.04 machine with ROS 2 Humble, franka_ros2 built, and either hardware or Gazebo running. This cannot be automated from macOS Apple Silicon.

### Apple Silicon + Docker GUI Limitation Discovered
Attempted to launch RViz 2 inside `osrf/ros:humble-desktop` via Docker Desktop + XQuartz:
- X11 TCP connection established successfully (XQuartz `nolisten_tcp` changed from 1 → 0)
- URDF generated correctly from `franka_description` (15 KB, all 14 links parsed)
- `robot_state_publisher` and `joint_state_publisher_gui` started successfully
- **RViz 2 crashed** with `GLXContext` error on every attempt

**Root cause:** `osrf/ros:humble-desktop` is amd64-only. On Apple Silicon Macs, Docker Desktop runs it via Rosetta 2 emulation in a VM with no GPU access. XQuartz provides X11 but not hardware-accelerated GLX. OGRE (RViz 2's renderer) requires hardware OpenGL and has no software fallback in this configuration. Mesa software renderer env vars (`LIBGL_ALWAYS_SOFTWARE=1`, `GALLIUM_DRIVER=softpipe`) did not help because GLX context creation fails before OGRE reaches the Mesa layer.

**Affected tools:** RViz 2, Gazebo (both use hardware OpenGL). ROS 2 nodes, Python scripts, MoveIt 2 planning (headless) all work correctly on Apple Silicon Docker.

**Does not affect:** Intel Mac + Docker + XQuartz (hardware GLX available), Ubuntu 22.04 native/VM with GPU.

### Tutorial Updated
- **ch01.html §1.4**: Rewrote the "Which option?" decision box to be honest about platform limitations. Added a `callout-warning` table before Option A/B showing exactly what works per platform (Ubuntu, macOS Apple Silicon, macOS Intel, Windows). Updated Option A heading from "recommended for macOS" to "code development · macOS · Windows". Added `callout-warning` before A6 explaining the Apple Silicon OpenGL crash. Updated Chapter 1 Summary bullet to replace misleading Docker claim with the honest recommendation: Ubuntu 22.04 is the only platform where everything works; Mac users need a VM or cloud Ubuntu.

### Key Design Decision
The tutorial was previously misleading — it implied Docker on macOS was a complete solution. It is not: Docker on Mac supports code development and headless ROS 2, but Gazebo and RViz (central to Chapters 3–10) cannot render on macOS Apple Silicon due to the OpenGL/Rosetta/XQuartz limitation. The tutorial now states this upfront with a platform compatibility table and recommends UTM/Parallels VM or a cloud Ubuntu VM as the Mac path.

### Browser-Based FK Visualization Generated
- `panda_viz.html`: Interactive Plotly 3D visualization of 4 pick-and-place configurations (Home, Pre-grasp, Grasp, Place) computed from the tutorial's DH parameters (ch02). Opened in browser. Not part of the tutorial — generated as a verification artifact.

---

## 2026-06-03 (session 3) — Docker Verification, Code Syntax & Import Checks, Bug Fix

### Docker Environment Verified
- Confirmed Docker Desktop running (v29.5.2) and XQuartz installed (`/opt/X11/bin/xquartz`).
- Pulled `osrf/ros:humble-desktop` image (~2 GB).
- Verified all 7 tutorial apt packages exist in the ROS 2 Humble repo: `ros-humble-moveit`, `ros-humble-franka-description`, `ros-humble-franka-msgs`, `ros-humble-gazebo-ros2-control`, `ros-humble-ros2-control`, `ros-humble-ros2-controllers`, `python3-rosdep`.

### Python Syntax Check — 26/26 Pass
- Extracted all 26 Python code blocks from ch01–ch10 HTML files.
- Ran `python3 -m py_compile` on each block inside `osrf/ros:humble-desktop` container (Python 3.10).
- **1 failure found and fixed:** `ch04.html` line 575 — f-string with nested single-quotes (`print(f'{'Joint':<16} ...')`) is valid Python 3.12+ but a `SyntaxError` in Python 3.10 (Ubuntu 22.04). Fixed by extracting the header string to a separate variable: `header = f"{'Joint':<16} ..."; print(header)`.
- All 26 blocks pass after fix.

### Python Import Check — 11/11 Pass
Ran inside a single container session (apt-get install → source setup.bash → python3):
- `rclpy`, `rclpy.node.Node`, `rclpy.action.ActionClient` ✓
- `sensor_msgs.msg.JointState` ✓
- `geometry_msgs.msg.Pose/Point/Quaternion` ✓
- `trajectory_msgs.msg.JointTrajectory/JointTrajectoryPoint` ✓
- `control_msgs.action.FollowJointTrajectory` ✓ (requires `ros-humble-control-msgs`)
- `franka_msgs.action.Grasp, Move as GripperMove` ✓ (requires `ros-humble-franka-msgs`)
- `numpy`, `math`, `scipy.spatial.transform.Rotation` ✓

### Summary Heading Cleanup
- Removed "— Big picture:" suffix from all 10 chapter summary headings (became just "Chapter N Summary").
- Removed trailing "—" from all 10 headings.

### SVG Overlap Fixes (ch02)
- **Fig 2.4 top view center:** Split "dead"/"zone" two-line label into single "dead zone" label left of the center dot; "Base" label moved to right of dot. Both on same Y line, separated by the 10 px dot — no overlap.
- **Fig 2.5 shoulder singularity:** Removed "Arm fully extended" subtitle that overlapped J5 circle top (only 1 px clearance). Title + vertical arm shape already communicate this.

---

## 2026-06-03 (session 2) — Chapter Summaries, Docker Intro, SO(3) Explanation, Singularity Section, SVG Overlap Fixes

### All 10 Chapter Summaries Rewritten
Every chapter summary (`callout-summary`) rewritten to lead with the chapter's **single biggest idea** rather than listing implementation details. Key changes:
- Each summary now answers: "what is THIS chapter's most important insight, and why does it matter?"
- Forward references added (e.g. ch02 summary notes Jacobian is used in ch06; ch03 notes sim-first rule applies through ch09)
- Removed weak closing bullets like "your environment is installed — you are ready for Chapter 2"
- Chapters: ch01 (layered stack + "never talk directly to motors"), ch02 (FK/IK as two-way translation), ch03 (simulate-first mandate + three-tool distinction), ch04 (planning pipeline separates your code from the 1 kHz PID), ch05 (Cartesian = task-level thinking), ch06 (velocity control = continuous loop not dispatch-and-wait), ch07 (impedance = programmable stiffness), ch08 (planning scene quality = plan quality), ch09 (hardware transition NOT just a parameter swap), ch10 (pick-and-place = integration of every prior chapter + FSM structure)

### Docker "What is Docker?" Introduction
- **ch01.html §1.4**: Added `callout-analogy` box before the "Which option?" decision box. Explains Docker in plain English: containerized Ubuntu laptop living inside your Mac, why ROS 2 needs it on macOS/Windows, and three key beginner facts (file sharing, ephemeral installs, XQuartz for GUIs).
- **ch01 Key Terms**: Added `Docker` and `Container` definitions.
- **ch01 Chapter Summary**: Added bullet mentioning Docker + XQuartz as the macOS/Windows path.

### SO(3) Explained in ch05
- Updated `.eq-note` under the SE(3) matrix to lead with a plain-English definition of SO(3): "the set of all valid 3D rotation matrices; R ∈ SO(3) means R is a legal rotation."
- **ch05 Key Terms**: Added `SO(3)` and `SE(3)` entries.
- Quaternion comparison table: replaced jargon "double cover of SO(3)" with a parenthetical explanation.

### §2.6.2 Singularities — Full Rewrite
- **ch02.html §2.6.2**: Replaced single vague paragraph with full explanation including:
  - Concrete "pushing a door along its hinge" analogy (orange `callout-analogy`)
  - Bullet list explaining exactly what motion is LOST for each singularity type (wrist: loses rotation about EEF z-axis; shoulder: loses radial translation)
  - Explanation of what happens in practice (velocity spike → protective stop → MoveIt 2 avoids, DLS handles near-singular)
- Warning callout condensed (no longer repeats the new explanation above it)

### SVG Overlap Fixes
- **ch02 Fig 2.5 — Wrist singularity**: Removed subtitle that overlapped J7 circle; replaced with smaller text "J5, J6, J7 axes aligned" positioned 14px above J7. Moved J5/J6/J7 circles down (J7: cy=52, J6: cy=68, J5: cy=88) to create 21px clearance from subtitle.
- **ch02 Fig 2.5 — Shoulder singularity**: Moved EEF tag from overlapping the title to right side with dashed leader line (x=196, y=27). Fixed truncated "without J1,J2 racing" text → expanded box (88×70 px) with 5 shorter lines: "EEF can't move / sideways without / J1+J2 both / spinning fast".
- **ch02 Fig 2.4 — Top view**: Moved Y-axis label from x=124,y=14 (overlapping title) to x=109,y=30,text-anchor="end" (left side of axis, clear of title).

---

## 2026-06-03 — Isaac Sim Intro, Math Annotations, Installation Fix, Code Bug Fix

### Isaac Sim Introduction Added
- **ch01.html §1.3**: Added `callout-note` "What about Isaac Sim?" after software stack layer descriptions. Explains Gazebo vs Isaac Sim trade-off (physics accuracy vs photorealistic rendering), GPU requirement, and forward reference to ch10 §10.6.
- **ch03.html**: Added comparison paragraph in Gazebo intro section distinguishing Gazebo (physics, no GPU) from Isaac Sim (photorealistic, GPU required).

### Math Formula Plain-English Annotations
Added `.eq-note` class to `style.css` (italic, muted, left-bordered). Applied to every significant display math block:

| Chapter | Formulas annotated |
|---|---|
| ch02 | Homogeneous transform T=[R,p;0,1] · FK chain T_EEF=T₁…T₇ · T₀→₂ composition · 4×4 DH matrix · Jacobian velocity v=Jq̇ |
| ch05 | SE(3) pose matrix |
| ch06 | PD control law v_cmd=Kp·e+Kd·ė · Trapezoidal profile t_acc=v_max/a_max · DLS pseudoinverse J⁺_DLS |
| ch07 | Mass-spring-damper Mẍ+Bẋ+Kx=F_ext · Impedance Z(s)=Ms²+Bs+K · Joint impedance τ=Kp(q_d−q)+… · Cartesian impedance F_cmd=Kp(x_d−x)+… · Wrench-to-torque τ=JᵀF+τ_ff |

### Installation Restructured (ch01.html §1.4)
- Added **Option A — Docker** (recommended for macOS/Windows) before the Ubuntu steps, with copy-paste `docker pull` and `docker run` commands.
- Existing Ubuntu Steps 1–3 relabeled as **Option B — Native Ubuntu 22.04**; all package names verified correct.
- Step 1 heading clarified as "Ubuntu native only."

### ch10 Code Bug Fix
- Lines 582–585: gripper action topic was `/fr3_gripper/grasp` and `/fr3_gripper/move` (Franka Research 3 robot).
- Fixed to `/franka_gripper/grasp` and `/franka_gripper/move` (correct namespace for Franka Panda).


---

## 2026-06-03 (Session 5) — franka_ros2 Launch Command Investigation & Fix

### Problem Discovered
The tutorial's §1.4 A6 launch commands referenced packages that no longer exist in the current franka_ros2 Humble branch:
- `ros2 launch franka_gazebo panda.launch.py` — package `franka_gazebo` removed
- `ros2 launch panda_moveit_config move_group.launch.py` — package `panda_moveit_config` removed

The franka_ros2 Humble branch was refactored (≥ 2024) to primarily target the **FR3** (Franka Research 3) robot. The old Panda launch infrastructure was replaced.

### Investigation (via Docker)
Built franka_ros2 from source and inspected the actual available packages:

| Package | Contains |
|---|---|
| `franka_gazebo_bringup` | `gazebo_franka_arm_example_controller.launch.py` (supports `robot_type:=fer/fr3/fp3`) |
| `franka_fr3_moveit_config` | `move_group.launch.py` (FR3-targeted, usable with FER/Panda) |
| `franka_bringup` | Real hardware launch files |

Available robot types in `ros-humble-franka-description`:
- `fer` — Franka Emika Robot (= classic Panda)
- `fr3` — Franka Research 3 (default)
- `fp3` — Franka Production 3
- `fr3_duo`, `fr3v2` — newer variants

### xacro Import Fix
`import xacro` failed because the xacro Python module lives in `/opt/ros/humble/local/lib/python3.10/dist-packages/` which is only added to PYTHONPATH by `source /opt/ros/humble/setup.bash`. Always source setup.bash before any Python import test.

### Tutorial Fix Applied (ch01.html §1.4 A6)
Updated launch commands:
```bash
# Before (broken)
ros2 launch franka_gazebo panda.launch.py
ros2 launch panda_moveit_config move_group.launch.py

# After (correct)
ros2 launch franka_gazebo_bringup gazebo_franka_arm_example_controller.launch.py robot_type:=fer
ros2 launch franka_fr3_moveit_config move_group.launch.py
```
Added explanatory callout-note: `robot_type:=fer` selects the classic Panda (FER = Franka Emika Robot).

### Step 3 Build Note Added
Added comment to colcon build command recommending `--parallel-workers 1 MAKEFLAGS="-j2"` for low-RAM systems (Docker default: 3.8 GB RAM → OOM-kill with default 8 workers).

---

## 2026-06-03 (Session 6) — Headless Simulation via Docker + Foxglove

### Goal
Run the tutorial simulation on macOS (Apple Silicon) without a GUI, using Docker + Foxglove Studio in the browser as the viewer.

### What Blocks Full Gazebo Simulation
- Gazebo and RViz use the OGRE 3D renderer which requires hardware OpenGL (GLX)
- Docker on Apple Silicon (Rosetta 2) has no GPU passthrough → no hardware GLX
- XQuartz provides X11 but not hardware-accelerated GLX → OGRE crashes before Mesa fallback
- `LIBGL_ALWAYS_SOFTWARE=1` does not help — OGRE fails before reaching Mesa

### Approach Tried: Headless Gazebo (franka_gazebo_bringup)
Launched `gazebo_franka_arm_example_controller.launch.py robot_type:=fer gz_args:='-r -s empty.sdf' rviz:=false`.

**Result:** Robot description loaded (all 9 fer_link* segments), robot spawned in Ignition Gazebo 6 — but `controller_manager` service never came up because `franka_ign_ros2_control/IgnitionSystem` plugin (the Gazebo-side ros2_control hardware interface) was never built. It is not in the `franka_ros2` source tree and not available via apt.

### Approach That Works: Fake Hardware + MoveIt 2 + Foxglove
Skipped Gazebo entirely. Used the `franka_fr3_moveit_config` built-in fake hardware mode:

```bash
ros2 launch franka_fr3_moveit_config moveit.launch.py \
  robot_ip:=dont-care \
  use_fake_hardware:=true \
  fake_sensor_commands:=true
```

**Key lesson:** Must run this launch file standalone. Running it alongside a separate `franka_bringup` launch creates joint-name conflicts (`fer_joint*` vs `fr3_joint*`) that crash `move_group`.

**What comes up:**
- `controller_manager` at 1000 Hz ✓
- `fr3_arm_controller` (JointTrajectoryController) ✓
- `joint_state_broadcaster` ✓
- `move_group` with OMPL ✓
- RViz crashes (expected — no GLX) but everything else runs

### Packages Required (all via apt)
```bash
ros-humble-ros-gz          # Ignition Gazebo + ROS bridge
ros-humble-ros2-control
ros-humble-ros2-controllers
ros-humble-ign-ros2-control
ros-humble-franka-description
ros-humble-xacro
ros-humble-moveit
ros-humble-foxglove-bridge
```

### Verified Working
| Check | Result |
|---|---|
| FK at home pose (q=[0,-0.785,0,-2.356,0,1.571,0.785]) | EEF at x=0.307m, z=0.590m ✓ |
| MoveGroup action plan to home config | SUCCESS: 3 waypoints, 0.175s ✓ |
| Foxglove bridge on port 8765 | 25 channels advertised, port open ✓ |
| `/joint_states` topic | Live ✓ |
| `/planning_scene`, `/display_planned_path` | Live ✓ |

### Not Verified
- `moveit_py` Python API (`from moveit.planning import MoveItPy`) — not in apt for Humble, needs source build
- Gazebo 3D physics simulation — blocked by GLX on Apple Silicon
- Actual trajectory execution (plan + execute, not just plan)

### Foxglove Viewer (browser-based, no install)
With Docker running, open `https://foxglove.dev/app` → Open connection → WebSocket → `ws://localhost:8765`. Shows live joint states, TF tree, planning scene, and planned trajectories.

### Outstanding Missing Piece
`franka_ign_ros2_control` needs to be cloned and built to enable Gazebo simulation. It is not in `dependency.repos` and not on apt. Source: unknown — not listed in any franka_ros2 documentation found. Likely at `github.com/frankarobotics/franka_ign_ros2_control` but unconfirmed.

---

## 2026-06-03 — Session 7: moveit_py API Validated + Gazebo Simulation Running

### Summary
Two objectives were completed in this session:
1. **moveit_py API end-to-end** — built from source (pybind11 C++ bindings), fixed all API errors, got a confirmed `SUCCESS` plan using OMPL/RRTConnect.
2. **Gazebo simulation** — headless fr3 robot running in Ignition Gazebo Fortress; live `/joint_states` from physics; `joint_state_broadcaster` and `fr3_arm_controller` active.

---

### Task 1 — moveit_py End-to-End API Validation

#### Build
- Source: `moveit2` GitHub `humble` branch, `moveit_py/` subdirectory only
- Build path: `/tmp/moveit_py_ws/` inside Docker container `panda_sim`
- Build strategy (OOM avoidance on 3.8 GB Docker RAM):
  ```bash
  cd /tmp/moveit_py_ws/build/moveit_py
  cmake --build . --target core -- -j1     # ~2 GB RAM peak
  cmake --build . --target planning -- -j1  # ~2 GB RAM peak
  cmake --build . --target install -- -j1
  cd /tmp/moveit_py_ws && colcon build      # finalizes in ~1.5s
  ```
- Required flag: `-DCMAKE_INTERPROCEDURAL_OPTIMIZATION=OFF` (disables LTO which conflicts with `-j1` jobserver)
- Import verified: `from moveit.planning import MoveItPy` → `<class 'moveit.planning.MoveItPy'>` ✓

#### Parameters Required (the hard part)
`MoveItPy()` needs `robot_description`, `robot_description_semantic`, kinematics, and pipeline config as ROS 2 node parameters. Fix: generate a `params.yaml` file and pass via `--ros-args --params-file`.

Key structural facts discovered from `moveit_cpp.h`:
- Planning pipeline list: `planning_pipelines.pipeline_names: [ompl]` (NOT `planning_pipelines: [ompl]`)
- OMPL plugin: `ompl.planning_plugin: ompl_interface/OMPLPlanner`
- OMPL adapters: `ompl.request_adapters: "default_planner_request_adapters/..."` (space-separated)
- Kinematics: `robot_description_kinematics.fr3_arm.kinematics_solver: kdl_kinematics_plugin/KDLKinematicsPlugin`

Generation script (runs inside container):
```bash
python3 << 'PYEOF'
import yaml, copy, subprocess

# Generate URDF and SRDF from xacro
urdf = subprocess.check_output(["xacro",
    "/opt/ros/humble/share/franka_description/robots/fr3/fr3.urdf.xacro",
    "hand:=true", "robot_ip:=dont-care", "ee_id:=franka_hand",
    "use_fake_hardware:=true", "fake_sensor_commands:=false", "ros2_control:=true"
]).decode()
srdf = subprocess.check_output(["xacro",
    "/opt/ros/humble/share/franka_description/robots/fr3/fr3.srdf.xacro",
    "hand:=true", "ee_id:=franka_hand"
]).decode()

with open('/panda_ws/install/franka_fr3_moveit_config/share/franka_fr3_moveit_config/config/ompl_planning.yaml') as f:
    ompl_yaml = yaml.safe_load(f)

ompl_pipeline = {
    'planning_plugin': 'ompl_interface/OMPLPlanner',
    'request_adapters': ('default_planner_request_adapters/AddTimeOptimalParameterization '
        'default_planner_request_adapters/ResolveConstraintFrames '
        'default_planner_request_adapters/FixWorkspaceBounds '
        'default_planner_request_adapters/FixStartStateBounds '
        'default_planner_request_adapters/FixStartStateCollision '
        'default_planner_request_adapters/FixStartStatePathConstraints'),
    'start_state_max_bounds_error': 0.1,
}
ompl_pipeline.update(copy.deepcopy(ompl_yaml))
ompl_pipeline['fr3_arm'] = copy.deepcopy(ompl_yaml.get('panda_arm', {}))

params = {'moveit_py_test': {'ros__parameters': {
    'robot_description': urdf,
    'robot_description_semantic': srdf,
    'robot_description_kinematics': {
        'fr3_arm': {'kinematics_solver': 'kdl_kinematics_plugin/KDLKinematicsPlugin',
                    'kinematics_solver_search_resolution': 0.005,
                    'kinematics_solver_timeout': 0.005}},
    'planning_pipelines': {'pipeline_names': ['ompl']},
    'default_planning_pipeline': 'ompl',
    'ompl': ompl_pipeline,
}}}

class NoAlias(yaml.Dumper):
    def ignore_aliases(self, data): return True

with open('/tmp/moveit_py_params.yaml', 'w') as f:
    yaml.dump(params, f, default_flow_style=False, Dumper=NoAlias)
PYEOF
```

#### API Corrections Found
| Wrong | Correct |
|---|---|
| `robot_model.joint_model_group('fr3_arm')` | `next(g for g in robot_model.joint_model_groups if g.name=='fr3_arm')` |
| `PlanRequestParameters(moveit, 'ompl')` | `p = PlanRequestParameters(moveit); p.planning_pipeline = 'ompl'` |
| `traj.joint_trajectory.points` | `traj.get_waypoint_durations()`, `traj.duration` |

#### Working Test Script
```python
# /tmp/test_moveit_py4.py  (run with --ros-args --params-file /tmp/moveit_py_params.yaml)
import rclpy, sys
from moveit.planning import MoveItPy, PlanRequestParameters
from moveit.core.robot_state import RobotState

rclpy.init()
moveit = MoveItPy(node_name='moveit_py_test')
rm = moveit.get_robot_model()   # → fr3
jmg = next(g for g in rm.joint_model_groups if g.name == 'fr3_arm')
arm = moveit.get_planning_component('fr3_arm')
rs = RobotState(rm)
rs.set_to_default_values(jmg, 'ready')
arm.set_start_state_to_current_state()
arm.set_goal_state(robot_state=rs)
plan_params = PlanRequestParameters(moveit)
plan_params.planning_pipeline = 'ompl'
plan_params.planner_id = 'RRTConnectkConfigDefault'
plan_params.planning_time = 5.0
plan_params.planning_attempts = 3
result = arm.plan(plan_params)
# → SUCCESS: plan returned ✓
```

#### To Run
```bash
docker exec panda_sim bash -c "
source /opt/ros/humble/setup.bash
source /panda_ws/install/setup.bash
source /tmp/moveit_py_ws/install/setup.bash
python3 /tmp/test_moveit_py4.py --ros-args --params-file /tmp/moveit_py_params.yaml
"
```
**Note:** `/tmp/` build artifacts are in Docker overlay filesystem — persist as long as container runs but lost on full container removal. Container name: `panda_sim`.

---

### Task 2 — Gazebo Simulation

#### Status
Running successfully with `robot_type:=fr3` in headless mode. Key command:
```bash
ros2 launch franka_gazebo_bringup gazebo_franka_arm_example_controller.launch.py \
  robot_type:=fr3 gz_args:='-r -s empty.sdf' rviz:=false controller:=joint_state_broadcaster
```
Sources: `source /opt/ros/humble/setup.bash && source /panda_ws/install/setup.bash && source /tmp/shim_ws/install/setup.bash`

#### franka_ign_ros2_control Shim (solves missing plugin)
The package `franka_ign_ros2_control` is not on apt or GitHub. Solved via a shim package that:
1. Registers `franka_ign_ros2_control/IgnitionSystem` as an alias for `ign_ros2_control/IgnitionSystem` (pluginlib)
2. Creates a symlink for the Ignition system plugin: `libfranka_ign_ros2_control-system.so → libign_ros2_control-system.so`

Shim source at `/tmp/shim_ws/src/franka_ign_ros2_control/` inside the container.

`fer_link4 invalid inertia` issue (FER robot type) avoided by using `robot_type:=fr3`.

#### Verified Working
| Check | Result |
|---|---|
| Ignition Gazebo headless launch | ✓ |
| `controller_manager` active at 1000 Hz | ✓ |
| `joint_state_broadcaster` spawned | ✓ |
| `/joint_states` publishing live physics data | ✓ |
| `fr3_arm_controller` (JointTrajectoryController) | ✓ |
| Foxglove bridge 25 channels incl. joint_states | ✓ |

#### Still Not Verified
- Gazebo + MoveIt 2 simultaneously — container hits 3.8 GB Docker RAM limit and OOM-kills
- Actual trajectory execution (plan + send to Gazebo controller) — requires running both simultaneously

---

### Session 7 — Cumulative Verified Stack
| Component | Status |
|---|---|
| ROS 2 Humble | ✓ |
| franka_ros2 (fr3) | ✓ |
| MoveIt 2 (`move_group`) | ✓ |
| MoveGroup action planning | ✓ (3 waypoints, 0.175s) |
| moveit_py Python bindings | ✓ (import + MoveItPy() + OMPL plan) |
| Ignition Gazebo simulation (headless, fr3) | ✓ |
| Foxglove Studio viewer | ✓ |
| Trajectory execution | ✗ (needs more RAM) |

---

## 2026-06-04 — Session 8: moveit_py Params & API Finalized

### What Happened
Continuation of session 7's moveit_py work. The end-to-end planning test was blocked by two issues that were diagnosed and fixed in this session.

**Issue 1 — Wrong `planning_pipelines` param structure**
`MoveItCpp::PlanningPipelineOptions::load()` reads `planning_pipelines.pipeline_names` (a dot-namespaced key), not a bare list under `planning_pipelines`. Previous YAML had:
```yaml
planning_pipelines:
  - ompl          # WRONG — bare list
```
Fix:
```yaml
planning_pipelines:
  pipeline_names:  # CORRECT — nested key
    - ompl
```

**Issue 2 — YAML aliasing rejected by rcl**
Python's `yaml.dump` creates YAML anchors (`&id001`) when the same dict is referenced twice. `rcl` params parser rejects anchored YAML with `"Will not support aliasing"`. Fix: custom `NoAliasDumper`:
```python
class NoAliasDumper(yaml.Dumper):
    def ignore_aliases(self, data): return True
yaml.dump(params, f, Dumper=NoAliasDumper)
```

**Issue 3 — `PlanRequestParameters` constructor**
Takes only one argument (`MoveItCpp` instance); pipeline name is set as an attribute:
```python
p = PlanRequestParameters(moveit)
p.planning_pipeline = 'ompl'   # NOT: PlanRequestParameters(moveit, 'ompl')
```

### Final Result
```
rclpy initialized
MoveItPy instantiated OK
Robot model: fr3
Planning with OMPL/RRTConnect...
SUCCESS: plan returned ✓
```

### Outstanding
- Trajectory execution (plan + execute to Gazebo controller) — needs Docker RAM > 3.8 GB
- All `/tmp/` build artifacts (moveit_py_ws, shim_ws) are volatile — lost if container is removed

---

## 2026-06-07 — Session 9: Ch.10 Isaac Sim Code Updated to 5.0 API

### Context
A separate machine (Ubuntu 22.04, 4× RTX 6000 Ada, no ROS 2) ran the ch10 Isaac Sim code and hit failures. Fixes were identified by that session and handed off for integration into ch10.html.

### Environment (remote machine)
- No ROS 2 — `pick_and_place.py` (§10.5) is not runnable there; no edits needed, Python is correct.
- Isaac Sim 5.0.0 in conda env `/data/bruce/envs/isaaclab` (Python 3.11).
- Multi-GPU machine; GPUs 1–3 typically occupied → must pin to GPU 0.

### Bugs Fixed in ch10.html

**B1 — Import renames (Isaac Sim 2.x → 5.0)**
The old `omni.isaac.*` package tree does not exist in Isaac Sim 5.0. All four imports in `scene_setup.py` were renamed:

| Old (2.x) | New (5.0) |
|---|---|
| `from omni.isaac.kit import SimulationApp` | `from isaacsim import SimulationApp` |
| `from omni.isaac.core import World` | `from isaacsim.core.api import World` |
| `from omni.isaac.franka import Franka` | `from isaacsim.robot.manipulators.examples.franka import Franka` |
| `from omni.isaac.core.objects import DynamicCuboid` | `from isaacsim.core.api.objects import DynamicCuboid` |

**B2 — EULA + GPU pinning**
Without `OMNI_KIT_ACCEPT_EULA=YES` the app hangs waiting for interactive input. Without GPU pinning it crashes with `ERROR_OUT_OF_DEVICE_MEMORY` on a busy multi-GPU node.
```python
os.environ["OMNI_KIT_ACCEPT_EULA"] = "YES"
simulation_app = SimulationApp({
    "headless": True, "active_gpu": 0, "physics_gpu": 0, "multi_gpu": False,
})
```

**B3 — Missing ground plane**
`scene_setup.py` never added a floor. The `DynamicCuboid` fell through space indefinitely. Fix: one line after `World()`:
```python
world.scene.add_default_ground_plane()
```

**B4 — Joint-vector shape mismatch in `move_to_joints`**
`robot.get_joint_positions()` returns **9 values** (7 arm + 2 fingers). The tutorial's `target = np.array(target_joints)` is only 7 values → NumPy broadcast error on `current + t*(target-current)`. Fix: copy current state, overwrite only arm joints:
```python
current = np.array(robot.get_joint_positions(), dtype=float)
target  = current.copy()
target[:7] = np.array(target_joints)
```

### Also Updated
- §10.6.1 "What You Need": Isaac Sim 4.x → **5.0**, Python 3.10 → **3.11**
- §10.6.2 intro: mention headless server path alongside Script Editor path
- Added callout after `replay_trajectory.py` with the terminal run command:
  ```bash
  OMNI_KIT_ACCEPT_EULA=YES CUDA_VISIBLE_DEVICES=0 \
    /path/to/isaaclab/bin/python scene_setup.py
  ```

### Known Limitation (not fixed, noted in handoff)
Hardcoded `GRASP_JOINTS` don't place the gripper exactly on the cube at `[0.5, 0, 0.025]`. `set_joint_positions` is a kinematic teleport (no contact forces), so the cube is not physically picked up — the arm moves through the sequence but the cube stays. This matches the tutorial intent; a physics-accurate grasp would require IK-tuned joint targets and prim attachment on gripper close.
