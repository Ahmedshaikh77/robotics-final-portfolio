# Project design and evidence guide

This guide explains what the [KinoVaPong presentation](../index.html) reports and what can be checked in this repository. It is not a reconstruction of missing robot software or an independent validation of the project.

## Evidence available here

| Artifact | What the repository establishes | What it does not establish |
| --- | --- | --- |
| [Project page](../index.html) | A static HTML case study with inline CSS, section navigation, a video player, project attribution, and a technical narrative. | That every described ROS 2 component is implemented or integrated. |
| [Demo MP4](../videos/throw_10deg.mp4) | The local video file used by the page's demo player. | Trial counts, hit rate, repeatability, or the release angle implied by the filename. |
| [Center cup JPEG](../images/cup_center.jpeg) and [right cup JPEG](../images/cup_right.jpeg) | Two 640 × 480 images showing a red cup with detection and coordinate overlays. | Camera calibration, detection accuracy, or integration with the arm controller. |

Neither JPEG is referenced by the current HTML. The README links them so readers can inspect the additional supplied media without changing the presentation.

## Reported system design

The descriptions below come from the page's **Project Overview**, **System Design**, and **Control & Trajectory Details** sections. Named nodes, methods, and topics are presentation references; there are no corresponding source files in this repository.

| Component or interface | Role described in the presentation |
| --- | --- |
| Kinova Gen3 Lite and 2F gripper | Pick up and throw a ping-pong ball. |
| Intel RealSense camera | Observe the red cup for alignment. |
| `RedCupAnglePublisher` | Publish the cup yaw angle on `camera_start_angle_deg`. |
| `BeerPongThrow` | Coordinate the pick-lift-throw sequence and call MoveIt 2; the page attributes this node to Muhammad Ahmed. |
| `throw_ball_forward()` | Execute the throwing routine. |
| `joint_1` | Align the base using the camera-derived yaw. |
| `joint_3` and release monitor | Monitor a chosen angle window during the swing and publish a `RELEASE` signal. |
| MoveIt 2 planning scene | Represent an attachable ball collision object. |
| Multi-threaded ROS 2 executor and a separate swing thread | Allow sensing callbacks and timers to run alongside the motion sequence. |

The reported sequence is:

1. A human places the ball and cup and starts the nodes.
2. The vision node supplies a cup angle.
3. The control node moves home, approaches the ball, grasps it, and lifts it.
4. The arm aligns, moves into a backswing, and swings forward.
5. Joint-angle monitoring triggers the release; the arm returns home.
6. The human resets the trial.

The presentation describes a 100 Hz release timer and illustrative joint and release angles. These are reported design settings, not measured timing guarantees or instructions for operating the hardware.

## Learning outcomes and reported observations

- **Sequencing and integration:** separating home, grasp, lift, alignment, swing, release, and reset into identifiable stages.
- **Feedback-triggered behavior:** using joint-state information to trigger release while the arm is moving.
- **Concurrency:** the author reports reorganizing callbacks and timers after early release-timing problems. The explanation motivates a multi-threaded executor and a separate motion thread, but the fix cannot be inspected here.
- **Experiment design:** the page reports varying backswing, swing range, release angle, and velocity scaling. It also reports sensitivity of landing position to these choices, without supplying the trial records needed to quantify it.

## Results that require additional evidence

The **Experiments & Results** table retains the label “Example trial set (fill in your numbers)” and a footnote stating that it is a placeholder. The row contains `35cm`, `1 & 6`, and `100%`; neither the trial count nor the success definition is established by that row.

Accordingly, this repository does not support a measured 100% hit rate or a quantitative repeatability claim. Its narrative about repeated trials is a project-reported account, not a substitute for raw logs.

To support stronger conclusions in a future documentation update, useful additions would include:

- The relevant controller and vision source, with dependency versions and configuration.
- Launch instructions, topic definitions, calibration details, and an explanation of which components were used in the recorded demonstration.
- Trial-by-trial inputs and outcomes, a clear definition of a hit, the number of attempts, and the measurement method.
- Logs or traces for release timing and joint-state processing if timing claims are made.

These materials are not present and are not required to preview the website. Their absence is why this documentation does not supply a robot build or launch procedure.

## Future work described by the author

The page proposes a more detailed ballistics model, landing-point prediction, and automatic optimization of release angles and joint speed profiles for arbitrary cup positions. These are proposed extensions, not features verified in this repository.

For local preview and repository layout, return to the [README](../README.md).
