# Using ROS 2 for High-Speed Maneuvering in Autonomous Driving

My Bachelor's Thesis at [CTU FEE](https://fel.cvut.cz/en/) ([ČVUT FEL](https://fel.cvut.cz/cz/))

_This thesis is a continuation of the work from [my semestral project][fel-project]._


## Abstract

Performing high-speed maneuvers in autonomous driving is problematic without proper real-time support. At CTU,
there is a team that competes in the F1/10 Autonomous Driving Competition with autonomous model cars. Their
autonomous driving stack is based on ROS 1, which is not suitable for real-time applications.

The goal is to migrate this stack to ROS 2, which has been designed from the ground up to address many issues
in this area. We propose tracing as an efficient way to analyze a running ROS 2 system and measure important
properties.

We demonstrate the working of the migrated stack on the F1/10 model car and in the Stage simulator. We
evaluate the communication latencies in the new stack using an extended version of ROS 2 tracing tools.
Another result of our work is a publicly-available collection of setup guides, scripts, and documentation that
covers various aspects of working with ROS. These guides have already helped several people.

We hope that the results of this thesis build a foundation that opens the way for the adoption of ROS 2 in the
CTU's F1/10 stack, further improving its real-time properties, while making it more approachable by new
students.


## Content

* 👉 **[Thesis](./text)** (thesis text as TeX source, PDF export, build script) 👈
* [Presentation](./presentation)
* [Formalities](./formalities) (official assignment, thesis requirements)


## Project Repositories

* [pokusew/fel-bachelors-thesis](https://github.com/pokusew/fel-bachelors-thesis) _(this repository)_
  – Thesis homepage, text sources, and PDF exports 📝
* **[pokusew/f1tenth-rewrite](https://github.com/pokusew/f1tenth-rewrite)**
  – ROS 2 port of CTU F1/10 project 🏎
* **[pokusew/ros-setup](https://github.com/pokusew/ros-setup)**
  – Notes on ROS 2 and ROS 1, tips & tricks, using ROS with IDEs (JetBrains CLion, Visual Studio Code), setup
  guides for using ROS on different platforms (Ubuntu, macOS, NVIDIA Jetson TX2)  🛠️
* [pokusew/stage_ros2](https://github.com/pokusew/stage_ros2)
  – ROS 2 wrapper for the Stage simulator (fork containing improvements and fixes)
* [pokusew/ros2_razor_imu](https://github.com/pokusew/ros2_razor_imu)
  – ROS 2 driver and firmware to for Razor IMU 9DOF
* [pokusew/teensy-drive](https://github.com/pokusew/teensy-drive)
  – ROS 2 driver and firmware to for Razor IMU 9DOF
* [pokusew/vesc](https://github.com/pokusew/vesc)
  – ROS 2 driver for VESC (fork containing fixes)
* [pokusew/ros2-tracing-experiments](https://github.com/pokusew/ros2-tracing-experiments)
  – A collection of scripts to perform analysis of trace events collected with ros2_tracing
* [pokusew/ros2-build](https://github.com/pokusew/ros2-build)
  – colcon workspace for building ROS 2 from sources on different platforms together with additional
  packages (e.g. Stage simulator) 📦
* [pokusew/rh](https://github.com/pokusew/rh)
  – A simple helper to make working with different ROS versions and projects easier ⚙️


_Note: See [all my repos with tag `fel-project`][my-fel-project-repos] on my GitHub._

[fel-project]: https://github.com/pokusew/fel-project

[my-fel-project-repos]: https://github.com/pokusew?tab=repositories&q=topic%3Afel-project
