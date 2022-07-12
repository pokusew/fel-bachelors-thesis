# OBSOLETE: It was rewritten to TeX. See the [tex](./tex) directory.

---

# Migration of F1/10 Autonomous Driving Stack to ROS 2

**Possible names:**
* Porting autonomous software/driving stack of F1/10 model car to ROS 2
* Using ROS 2 on the autonomous F1/10 model car
* Migration of F1/10 Autonomous Driving Stack to ROS 2
* Migrating F1/10 Autonomous Driving Stack to ROS 2

**Useful vocabulary:**
* The goal of this project is to ... and ...
* goal, aim
* outcome, result
* present, introduce
* describe, cover
* implement
* study, explore
* deal with, focus, cover
* propose, suggest, come up with
* verify, compare
* groundwork
* highlight
* The project lays the foundations for future migration


## Abstract

The new version of the Robot Operating System – ROS 2 – brings significant improvements over its predecessor
ROS 1. We briefly describe both versions and compare their differences. Then we cover the process of migrating
a ROS 1 application to ROS 2 on the example of the Follow the Gap app that is part of CTU's F1/10 project. In
the next part, we focus on setting up an NVIDIA Jetson TX2 module so that it can run ROS 2 applications and
provide good developer experience in environments where Jetson module is shared among many students and
researches. The first result of this project is a ROS 2 port of the Follow the Gap application. Its working is
demonstrated in the Stage simulator on Ubuntu and macOS. The second result is a setup guide for NVIDIA Jetson
TX2 that covers creating a fully-setup OS image with ROS 2 on a bootable SD card. The last result is a
collection of setup guides and documentation that cover various aspects of working with ROS.


## Keywords

ROS, ROS 1, ROS 2, ROS 2 migration, F1/10, Follow the Gap, autonomous model car, NVIDIA Jetson TX2


---


# Introduction

Since the Robot Operating System was started in 2007 [[*]][ros-history], it has gained great popularity and
has become the standard in robotics community. However, it has turned out that the original architecture
(ROS 1) has **some limitations** concerning **performance, efficiency and real-time safety**. Thus, a new
version of ROS – **ROS 2** – has been designed from ground up to allow more use-cases and solve pain points of
ROS 1 [[*]][ros-design-why]. Missing features and incompatible packages have been slowing down the adoption of
ROS 2 since its first public release in 2017. But in recent years, the situation has improved a lot and the
**adoption of ROS 2 has accelerated** [[ros-metrics]][ros-metrics]. Thus, now it might be the right time start
migrating applications from ROS 1 to ROS 2 and benefit from new possibilities.

The F1/10 platform is scaled-down (1:10) model of an autonomous car that originates from F1/10 Autonomous
Racing Competition. Thanks to its affordability, the platform can be used for development, testing and
verification of autonomous driving systems and related algorithms. At CTU, multiple F1/10 models have been
created and used [[mt-vajnar]][mt-vajnar] [[bt-dusil]][bt-dusil] [[mt-klapalek]][mt-klapalek]. **Currently**,
all of them are based on NVIDIA Jetson computing modules and powered by ROS 1 Kinetic Kame.

**The goal** of this project is to migrate a selected part of the CTU's F1/10 project from ROS 1 Kinetic Kame
to ROS 2 Foxy Fitzroy. The result should be a working port running on ROS 2 in a simulator and on a physical
model car with NVIDIA Jetson computing module.

In the Chapter 2, both versions of ROS are briefly described and ROS 2 and its main differences to ROS 1 are
highlighted.

In the Chapter 3, the software and hardware stack of CTU's F1/10 project is introduced. Then the Follow the
Gap application is selected for migration to ROS 2. The migration is described. The result

The Chapter 4 focuses on setting up an NVIDIA Jetson TX2 module so that it can run ROS 2 applications. The
emphasis is given on providing a good developer experience in environments where the Jetson module is shared
among many students and researches. Thus, boot options of the Jetson modules are explored. Then a solution
with a bootable SD card is presented.

Finally, the achieved results are summarized in the last Chapter 5.


# Robot Operating System

The Robot Operating System (or ROS) is “an open-source, meta-operating system” [[ros1-intro]][ros1-intro]
that consists of “tools, libraries, and conventions that aim to simplify the task of creating complex and
robust robot applications across a wide variety of robotic platforms“ [[ros2-foxy-root]][ros2-foxy-root].

A typical ROS application is composed of loosely coupled processes – nodes – (potentially distributed across
machines) that communicate with each other and work together to accomplish a certain goal (for example,
autonomous vehicle control). Each node can perform only a specific limited set of functions (e.g.: one node
can read data from LIDAR, another node can implement an obstacle detection algorithm from the LIDAR data and
another node can use the detected obstacles to plan the trajectory, etc.).

Such architecture greatly supports the separations of concerns and allows code reuse. That further enables
efficient code sharing of common functional among different projects with different applications. That in
fact, is one of the most significant features of ROS as there are thousands of ROS packages provided by the
ROS community. Then, developers can focus on their application specific problems while reusing the code for
the common parts.


## ROS Computation Graph

At runtime, ROS nodes and their communication interactions form so called “ROS Computation
Graph” [[ros1-concepts]][ros1-concepts]. The nodes are represented by the graph's vertices, while the edges
depicts the communication interactions.

(TODO: computation graph image)

ROS provides several communication primitives that can be used by nodes:
1. messages and topics – publish/subscribe  
   Nodes (publishers) can publish messages to a named topic. Other nodes (subscribers) can subscribe to a
   topic and receive the published messages.
2. services – synchronous RPC (Remote Procedure Call) (server/client)
   Nodes (service servers) can provide services. A service has a name. Other nodes (service clients) can
   invoke/call a service and synchronously get a result.
3. actions – asynchronous preemptible RPC with continuous feedback (server/client)
   Node (action servers) can provide actions. An action is preemptible task that has a goal, can provide
   continuous feedback and returns a result if it is not cancelled. Other nodes (action clients) can invoke an
   action (request a goal and subscribe for its feedback and result).

The communication is strongly typed and ROS provides an interface description language (IDL) for describing
message types (used for pub/sub), service types and action types.


## Common Concepts

In addition to the ROS Computation Graph, both versions of ROS share many common concepts, some of which are
described below:

* _package_ – A container for code, IDL files (messages, services, actions), configuration files or anything
  else. It is “the most atomic build and release item” that groups together common functionality that can be
  easily shared and reused [[ros1-concepts]][ros1-concepts]. Each package has a package manifest
  file (`package.xml`) that provides “metadata about a package, including its name, version, description,
  license information, dependencies, and other information“. There are currently 3 package manifest formats,
  which are defined in [REP-127][rep-127], [REP-140][rep-140], [REP-149][rep-149] respectively.

* _distribution_ – “A versioned set of ROS packages” [[ros1-distros]][ros1-distros]. ROS 1 distributions are
  listed at [[ros1-distros]][ros1-distros], ROS 2 distributions are listed at [[ros2-distros]][ros2-distros].

* _workspace_ – A directory containing packages that are built together using ROS _build tool_ (_catkin_make_,
  _catkin_make_, _catkin_make_isolated_, _catkin_tools_, _colcon_) and _build system_ (_catkin_ or _ament_).

* _Graph Resource Names_ – “A hierarchical naming structure that is used for all resources in a ROS
  Computation Graph, such as Nodes, Parameters, Topics, and Services“ [[ros1-concepts]][ros1-concepts].

* _Package Resource Name_ – A simplified method of “referring to files and data types on disk“. It consists of
  the name of the package that the resource is in plus the name of the resource. For example, the
  name `std_msgs/String` refers to the `String` message type in the `std_msgs` package.

* _ROS client library_ – A collection of code that simplifies the task of implementing of a ROS node in a
  certain programming language. “It takes many of the ROS concepts and makes them accessible via
  code” [[ros1-client.libs]][ros1-client.libs]. It provides an API (functions, methods, classes) that allows
  to interact with other nodes (pub/sub, services, actions/ parameters) and do other common things. The main
  ROS client libraries are for C++ and for Python. The internal architecture of client libraries differs
  greatly between ROS 1 and ROS 2.


## ROS 1

ROS 1 is the original version of ROS that dates back to 2007. The version 1.0 was published in 2010. Since
then, 13 (TODO: footnote as of today) ROS 1 _distributions_ has been released. The full list can be found
at [[ros1-distros]][ros1-distros].

Full documentation of ROS 1 can be found at [[ros1-docs]][ros1-docs].


### Architecture

In ROS 1 ROS Computation Graph, there must always be a special node called ROS Master. It provides name
registration and lookup services to the rest of the Computation Graph. It coordinates the communication among
the nodes. That includes graph changes notifications and establishing of connections connection between nodes.
It offers two XML-RPC based APIs – Master API and Parameter Server API.

ROS 1 has a concept of central data key/value data storage called Parameter Server. It is currently part of
ROS Master (Parameter Server API). A key/value pair is called parameter. All nodes in the Computation Graph
can get and manipulate the key/value data stored in the Parameter Server. The parameters can be used as
configuration storage for nodes, which allows easy altering of the system (nodes') behavior at runtime.

ROS 1 offers two underlying data transports protocols – TCPROS and UDPROS. As the names suggest, they are
based on TCP and UDP respectively.

**All concepts are implemented directly (natively) in ROS 1 Client Libraries.** The two main client libraries
are **roscpp** (for C++) and **rospy** (for Python). Their performance and features availability varies
greatly. While the C++ ROS 1 Client Library implements all ROS 1 features and provides high performance, the
Python ROS 1 Client Library lacks some features and provides worse performance. Even when a feature is
available in both libraries, the actual implementation often comes with minor differences that might not be
expected.

(TODO: ROS 1 architecture image)


### Build System

ROS 1 uses catkin build system. (TODO: footnote about rosbuild and colcon possibility) Catkin supports CMake
packages that uses special catkin_ CMake macros. The actual build is controlled by a build tool. ROS 1 catkin
workspace can be built using different build tools: catkin_make, catkin_make_isolated, catkin_tools.


### CLI

The CLI is composed of several commands that covers all ROS 1 features. These include for example
`rostopic`, `rosservice`, `rosparam`, `rosmsg`, `rosrun`, `roslaunch`, etc. [[ros1-cli]][ros1-cli]


### Launch System

While nodes can be started manually (via running the corresponding executable), for complex system it may be
cumbersome. For this reason, ROS 1 offers allows to describe a system using a special XML file. Then the
command `rosluanch` handles starting up all nodes and supplying correct arguments
[[ros1-launch]][ros1-launch].


## ROS 2

“Since ROS was started in 2007, a lot has changed in the robotics and ROS community. The goal of the ROS 2
project is to adapt to these changes, leveraging what is great about ROS 1 and improving what is
not.” [[ros2-foxy-root]][ros2-foxy-root]

Full documentation of the latest ROS 2 release can be found at [[ros2-docs]][ros2-docs].


### Architecture

The architecture of ROS 2 was designed from the ground up addressing issues of ROS
1 [[ros2-design-why]][ros2-design-why]. The newly designed architecture should address new use-cases as well
as many issues from ROS 1:
* truly distributed system (no master node)
* support for real-time
* more nodes in one process (composable nodes)
* better support for communication in non-ideal networks
* small embedded platforms support

While ROS 1 communications stack is built almost entirely from scratch, ROS 2 relies on Data Distribution
Service (DDS). DDS is “a middleware protocol and API standard for data-centric connectivity from the Object
Management Group (OMG). It provides low-latency data connectivity, extreme reliability, and a scalable
architecture for Internet of Things applications need“ [[omg-what-is-dds]][omg-what-is-dds].

ROS 2 Client Libraries have a different architecture compared to the ROS 1 ones. Instead of reimplementing all
the features in programming language separately, the common functionality is implemented in rcl library that
exposes a C API. Client Libraries use rcl

Because ROS 2 supports different DDS implementations from different vendors [[ros2-foxy-dds]][ros2-foxy-dds],
rcl library cannot directly communicate with the DDS implementation. Instead, there an abstraction layer
called rmw (ROS middleware) that provides a unified accesses to DDS. The specific DDS implementation can be
even supplied dynamically at runtime.

The whole relationship among different parts of ROS 2 Client Libraries is shown in the (TODO: link image).
Detailed information can be found at [[ros2-foxy-internal]][ros2-foxy-internal].

(TODO: architecture image)


### Build System

ROS 2 uses ament as build system and colcon as build tool. Ament supports three types of packages: CMake with
ament_cmake, pure Python packages (Python setuptools based) and pure CMake packages. Colcon always builds all
packages in isolation and in a correct order. For ensuring the correct build order, a dependencies graph is
constructed which must be a directed acyclic graph in order for the workspace to be buildable. It also tries
to parallelize the build up to the level that mutual dependencies among packages allow.


### CLI

It is very similar to ROS 1 CLI but instead of having multiple commands, ROS 2 has one central command `ros2`
that has several subcommands.


### Launch System

Launch system was redesigned. Currently, it uses Python 3 code to describe the system to
launch [[ros2-foxy-launch]][ros2-foxy-launch] [[ros2-design-launch]][ros2-design-launch].'


## Summary of Differences between ROS 1 and ROS 2

ROS 2 has better architecture and offers more features. Thanks to DDS, it offers fine-grained QoS. It brings
better reliability while communicating in non-ideal networks. Furthermore, the ROS 2 client libraries offer
better control over code execution and threading as they support writing custom executors. ROS 2 architecture
is designed with real-time support in mind (TODO: footnote)

The following table summarizes the most notable differences between ROS 1 and ROS 2:

| Feature                                                               | ROS 1 Kinetic Kame                                           | ROS 2 Foxy Fitzroy                                                                                   |
|-----------------------------------------------------------------------|--------------------------------------------------------------|------------------------------------------------------------------------------------------------------|
| Supported Platforms                                                   | only Ubuntu officially supported (TODO: footnote)            | Ubuntu, macOS, Windows (TODO: footnote) officially supported                                         |
| Client Libraries                                                      | roscpp (C++), rospy (Python)                                 | rclcpp (C++), rclpy (Python)                                                                         |
| C++                                                                   | C++03                                                        | C++14                                                                                                |
| Python                                                                | Python 2                                                     | Python 3                                                                                             |
| Real-Time Support                                                     | no                                                           | yes                                                                                                  |
| Runtime Node Composition                                              | no (TODO: Nodelets)                                          | Composable Nodes                                                                                     |
| Parameters                                                            | global parameter server                                      | parameters per node (no global parameter server), out-of-the-box “dynamic_reconfigure”-like features |
| Launch System                                                         | XML-based                                                    | Python-based                                                                                         |
| Transport                                                             | TCPROS or UDPROS                                             | fine-grained QoS (DDS)                                                                               |
| Communication Primitives                                              | pub/sub, services, actions                                   | pub/sub, services, actions                                                                           |
| Threading Model                                                       | not much customizable                                        | granular execution models, custom executors                                                          |
| Build                                                                 | catkin + catkin_make/catkin_make_isolated                    | ament + colcon                                                                                       |
| Pure Python Packages                                                  | no                                                           | yes                                                                                                  |
| Pure CMake Packages                                                   | no                                                           | yes                                                                                                  |
| IDL                                                                   | .msg/.srv                                                    | .msg/.srv/.action + extended features such as constraints                                            |


# F1/10 platform

TODO


# NVIDIA Jetson TX2

TODO


## Initial Setup

TODO


## Building ROS 2 from sources

TODO


## Running the Follow the Gap Demo

TODO


## Bootable SD Card

TODO


# Conclusion

TODO

documentation created

should be pretty straightforward missing pieces, Orbitty carrier setup, porting of HW packages

analysis of real-time properties, temporal determinism, communication overheads, performance, in particular
when compared with the ROS 1


# References

[ros-about]: https://www.ros.org/about-ros/

[ros-history]: https://www.ros.org/history/

[ros1-docs]: https://wiki.ros.org/

[ros1-intro]: https://wiki.ros.org/ROS/Introduction

[ros1-concepts]: https://wiki.ros.org/ROS/Concepts

[ros1-client-libs]: https://wiki.ros.org/Client%20Libraries

[ros1-cli]: https://wiki.ros.org/ROS/CommandLineTools

[ros1-launch]: https://wiki.ros.org/roslaunch

[ros2-docs]: https://docs.ros.org/en/rolling/

[ros2-foxy-root]: https://docs.ros.org/en/foxy/index.html

[ros2-foxy-launch]: https://docs.ros.org/en/foxy/Tutorials/Launch-system.html

[ros2-foxy-dds]: https://docs.ros.org/en/foxy/Concepts/About-Different-Middleware-Vendors.html

[ros2-foxy-internal]: https://docs.ros.org/en/foxy/Concepts/About-Internal-Interfaces.html

[ros2-design-why]: https://design.ros2.org/articles/why_ros2.html

[ros2-design-launch]: https://design.ros2.org/articles/roslaunch.html

[ros2-design-changes]: https://design.ros2.org/articles/changes.html

[ros1-distros]: https://wiki.ros.org/Distributions

[ros2-distros]: https://docs.ros.org/en/rolling/Releases.html

[ros-metrics]: https://metrics.ros.org/

[rep]: https://ros.org/reps/rep-0000.html

[rep-127]: https://ros.org/reps/rep-0127.html

[rep-140]: https://ros.org/reps/rep-0140.html

[rep-149]: https://ros.org/reps/rep-0149.html

[omg-what-is-dds]: https://www.dds-foundation.org/what-is-dds-3/

[mt-vajnar]: https://hdl.handle.net/10467/68472

[bt-dusil]: https://hdl.handle.net/10467/82910

[mt-klapalek]: https://hdl.handle.net/10467/83424


# Glossary

* CTU – Czech Technical University
* OS – Operating System
* SD card – Secure Digital card
* REP – ROS Enhancement Proposal, a document that standardizes certain aspect of ROS, a standard
* ROS – Robot Operating System
* IDL – Interface Description Language or Interface Definition Language
* LIDAR – Light Detection And Ranging
* XML – Extensible Markup Language
* RCP – Remote Procedure Call
* HTTP – Hypertext Transfer Protocol
* XML-RPC – an RPC protocol which uses XML to encode its calls and HTTP as a transport mechanism
* OMG – Object Management Group
* DDS – Data Distribution Service
* QoS – Quality of Service
