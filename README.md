# 🤖 Project Praeceptor: The Autonomous Robot Companion

## 📜 Vision

**Praeceptor** is a project to build an autonomous robot companion for a young child, designed to navigate and interact with a home environment. The system will be built in two distinct phases:
1.  **A traditional robotics pipeline** for safe, reliable navigation.
2.  **An intelligent, conversational layer** for high-level interaction.

The entire system will be powered by a distributed "split-brain" architecture, leveraging a Ryzen 9 PC and an M2 Mac Mini for all heavy computation. The core philosophy is to use each machine for what it does best, creating a robust and scalable system.

---

## 🏛️ Architecture: The Distributed Brain

The system is a distributed network of three core components, each with a specialized role:

* **The Body (Moorebot Scout):** A simple, network-connected client. Its sole purpose is to stream raw sensor data (video, audio, IMU, ToF) and to execute low-level motor commands. It is a dumb client with no complex logic. Its fixed IP address is **`192.168.86.48

<img width="1159" height="869" alt="image" src="https://github.com/user-attachments/assets/697b7c96-ba3e-42e0-ac06-eab654f8c91d" />


* **The Central Nervous System (Ryzen PC):** The primary development hub. This machine runs the main control script, the SLAM system, and all networking logic. It makes all real-time decisions regarding navigation and control. All development and the central repository reside here.
* **The AI Cortex (M2 Mac Mini):** A dedicated, local API server for AI inference. It will host two separate services: a vision model for real-time object detection and an LLM for semantic planning and conversation (to be implemented in Phase 2).

### The LLM's Role in Semantic Navigation

The LLM will be integrated as a high-level planner, working in tandem with the traditional control loop. It will not be involved in low-level, real-time control.

1.  **Conversational Role (Phase 2):** The LLM will handle all natural language input and output. It will understand your voice commands and generate a friendly, conversational response.
2.  **Semantic Navigation Role:** When a high-level command like "go to the kitchen" is given, the LLM will translate this abstract request into a tangible goal for the robot. It does this by generating a plan (e.g., a sequence of navigation goals) that your traditional control system can understand and execute.

---

## 🛠️ Core Components & Dependencies

### **Hardware**
* **Moorebot Scout:** Our mobile robot platform.
    * **IP Address:** `192.168.86.48`
    * **SSH Username/Password:** `linaro/linaro`
* **Ryzen PC:** The central hub, running Ubuntu 22.04 LTS (recommended for ROS 2).
* **M2 Mac Mini:** The local AI server.

### **Software**
* **Moorebot:** Default ROS framework.
* **Ryzen PC:**
    * **ROS 2 Humble:** The central communication layer.
    * **Python 3.10:** With necessary libraries (`rospy`, `cv_bridge`, `ffmpeg`, etc.).
    * **SLAM Package:** A ROS 2-compatible SLAM package (e.g., `slam_toolbox`).
* **Mac Mini:**
    * **LM Studio:** To run a quantized LLM model as a local API server (for Phase 2).
    * **FastAPI:** A Python framework to create the local API server for the YOLOv8 model.
    * **Ultralytics YOLOv8:** The object detection model library.
    * **Whisper/Piper:** For local STT/TTS (for Phase 2).

---

## ⚙️ Setup Instructions

### 1. Moorebot Scout Setup

1.  **SSH Access:** Confirm you can log in via SSH using `ssh linaro@192.168.86.48`.
2.  **Root Privileges:** Confirm `sudo` access is configured without a password.

### 2. Ryzen PC Setup (The Central Repo)

1.  **Install OS & ROS 2:** Set up Ubuntu 22.04 LTS and install ROS 2 Humble.
2.  **Configure ROS Network:** In your shell's startup file (`~/.bashrc`), set the `ROS_MASTER_URI` environment variable to point to the Moorebot's IP: `export ROS_MASTER_URI=http://192.168.86.48:11311/`.
3.  **Clone this Repository:** Clone this project's Git repository to a central location on your PC.
4.  **Install Dependencies:** Navigate to the repository's `src/` directory and run `pip install -r requirements.txt`.

### 3. M2 Mac Mini Setup (Local API Servers)

1.  **YOLOv8 Vision Server:** Set up a FastAPI server that loads a YOLOv8 model and serves a local API endpoint for real-time object detection. The server should be configured to run on the M2's GPU via MPS.
2.  **LLM Server (Phase 2):** Download and install LM Studio. Download a quantized LLM (e.g., Llama 3 8B) and configure it as a local API server.

---

## 🗺️ Project Plan

The project will be executed in two distinct, sequential phases to ensure a stable and reliable foundation.

### **Phase 1: The Navigational Core**

**Goal:** Build a fully autonomous robot capable of mapping, localizing, and safely navigating its environment using traditional robotics algorithms.

| ID | Title | Description | Status |
| :--- | :--- | :--- | :--- |
| **FEAT-P1** | **Core Communication** | Establish a reliable, bidirectional data link between the Moorebot Scout and the Ryzen PC using ROS 2. | To Do |
| **TASK-P1-T1** | **SSH & ROS Setup** | Confirm SSH access and configure the Ryzen PC to act as a ROS 2 client to the Moorebot's ROS master. Moorebot IP is `192.168.86.48`. | Done |
| **TASK-P1-T2** | **Data Stream Validation** | Write Python scripts on the PC to subscribe to and verify data from the robot's ROS topics (IMU, ToF, video, audio). | To Do |
| **TASK-P1-T3** | **Robot Control Script** | Create a Python script on the PC that publishes to the Scout's movement topic (`/cmd_vel`) and test basic commands. | To Do |
| **FEAT-P2** | **Local AI & SLAM** | Integrate local AI models and a robust SLAM system across the Ryzen PC and Mac Mini to handle navigation and object detection. | To Do |
| **TASK-P2-T1** | **Mac Mini Vision Server Setup** | On the Mac Mini, set up a FastAPI server to run a YOLOv8 model for real-time object detection via a local API endpoint. | To Do |
| **TASK-P2-T2** | **PC-based SLAM System** | On the Ryzen PC, install a ROS 2-compatible SLAM package (`slam-toolbox`) and configure it to build a map using the Scout’s sensor data. | To Do |
| **TASK-P2-T3** | **Integrated Control Script** | Create the main Python script on the Ryzen PC that orchestrates all data flow and API calls, integrating SLAM, vision, and robot control. | To Do |
| **FEAT-P3** | **Autonomous Skills** | Develop and test the robot's core navigational and object recognition skills. | To Do |
| **TASK-P3-T1** | **Object-Based Navigation** | Implement logic for the robot to navigate to and identify specific objects (e.g., "find the toy car") using its SLAM map and vision model. | To Do |
| **TASK-P3-T2** | **Final Testing** | Conduct end-to-end testing to ensure the robot can safely and reliably move around the home, avoiding obstacles. | To Do |

---

### **Phase 2: The Conversational Layer (Future Work)**

**Goal:** Integrate a natural language interface so the robot can understand and respond to high-level commands. This phase will be implemented once the core navigation is complete.

| ID | Title | Description | Status |
| :--- | :--- | :--- | :--- |
| **FEAT-P4** | **Conversational AI** | Add a natural language interface so the robot can understand and respond to high-level commands. | To Do |
| **TASK-P4-T1** | **LLM Integration** | Connect the Ryzen PC's main script to the Mac Mini's local LLM server. This LLM will be used for both conversation and semantic planning. | To Do |
| **TASK-P4-T2** | **TTS/STT Pipeline** | Implement a local STT model (like Whisper) and a TTS model (like Piper) on the Mac Mini to handle all voice input and output. | To Do |
| **TASK-P4-T3** | **High-Level Planning** | The LLM will translate natural language requests (e.g., "please patrol the living room") into specific goals for the traditional navigation system. | To Do |
