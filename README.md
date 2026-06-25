# 🚦 Traffic Light Violation Detection System

## Why I Built This Project

Many computer vision projects stop at object detection—detecting objects and drawing bounding boxes around them. While this is an important step, I wanted to explore how AI can be used to solve a real-world problem and create something that provides actual value.

Traffic violations, especially running red lights, are among the major causes of road accidents and traffic congestion. Monitoring these violations manually requires significant effort and resources, making it difficult to scale across multiple intersections.

This led me to a simple question:

**Can I build a system that not only detects vehicles but also understands what is happening in a traffic scene and automatically identifies violations?**

This project is my attempt to answer that question.

---

## Project Overview

The goal of this project is to automatically detect vehicles that cross a stop line while the traffic signal is red.

Rather than treating this as a pure object detection task, I designed a complete workflow that combines detection, tracking, event analysis, and decision-making logic to transform raw traffic footage into meaningful traffic insights.

The system can:

- Detect vehicles and traffic lights.
- Determine the current traffic light state.
- Track vehicles across video frames.
- Monitor a virtual stop line.
- Detect when vehicles cross the stop line.
- Identify red-light violations automatically.
- Generate traffic statistics and violation records.
- Produce an annotated output video for review.

---

## The Problem

Traffic monitoring is often performed manually or requires expensive infrastructure. Human monitoring can be time-consuming, error-prone, and difficult to maintain continuously.

An automated system capable of monitoring intersections and detecting violations could help:

- Improve road safety.
- Reduce manual monitoring efforts.
- Support traffic law enforcement.
- Generate useful traffic analytics.
- Enable smarter transportation systems.

---

## Solution Approach

The idea behind this project is simple:

1. Detect vehicles and traffic lights.
2. Understand the current traffic signal state.
3. Track each vehicle individually.
4. Monitor vehicle movement relative to a stop line.
5. Detect crossing events.
6. Decide whether the crossing was legal or a violation.

This transforms object detections into meaningful traffic events that can be analyzed automatically.

---

## System Workflow

### Step 1: Object Detection

A custom-trained YOLOv8 model is used to detect:

- Vehicles
- Red Traffic Lights
- Green Traffic Lights
- Yellow Traffic Lights

These detections provide the foundation for all subsequent analysis.

---

### Step 2: Traffic Light State Recognition

For every frame, the system determines the active traffic light state by selecting the detected signal with the highest confidence score.

Possible states include:

- Red
- Green
- Yellow
- Unknown

This allows the system to understand the traffic situation before making decisions.

---

### Step 3: Vehicle Tracking

Detection alone is not enough because vehicles appear across many consecutive frames.

To solve this problem, ByteTrack is used to assign a unique ID to every vehicle and maintain that identity throughout the video.

Example:

```text
Vehicle #3
Vehicle #7
Vehicle #12
```

Tracking enables the system to analyze vehicle behavior over time rather than treating every frame independently.

---

### Step 4: Stop Line Monitoring

A virtual stop line is defined within the scene.

The center point of each tracked vehicle is continuously monitored.

Whenever a vehicle crosses the stop line, the system records the event and checks the traffic light state at that exact moment.

---

### Step 5: Violation Detection

This is where the project moves beyond traditional object detection.

If a vehicle crosses the stop line while the traffic signal is red:

- The vehicle is classified as a violator.
- The tracking ID is stored.
- The violation is recorded.
- The vehicle is highlighted in the output video.
- Statistics are updated automatically.

If the vehicle crosses during a green or yellow signal, the crossing is recorded as non-violating behavior.

---

## System Architecture

```text
Input Video
      │
      ▼
YOLOv8 Detection
      │
      ├── Vehicle Detection
      │
      └── Traffic Light Detection
                │
                ▼
     Traffic Signal Recognition
                │
                ▼
         ByteTrack Tracking
                │
                ▼
      Stop Line Analysis
                │
                ▼
      Violation Detection
                │
                ▼
      Traffic Analytics
                │
                ▼
      Annotated Output Video
```

---

## Dataset

A custom dataset was collected and annotated for training the detection model.

### Classes

```text
vehicles
red-light
green-light
yellowlight
```

The dataset includes traffic scenes containing vehicles and traffic lights under different conditions.

---

## Model Training

The detection model was trained using YOLOv8 with the following configuration:

```bash
yolo detect train \
model=yolov8s.pt \
data=data.yaml \
epochs=120 \
imgsz=800 \
batch=16 \
device=0 \
workers=2 \
patience=30 \
optimizer=AdamW \
lr0=0.001 \
cos_lr=True \
close_mosaic=15 \
cache=True \
amp=True \
plots=True
```

### Training Configuration

| Parameter | Value |
|------------|--------|
| Model | YOLOv8s |
| Epochs | 120 |
| Image Size | 800 |
| Batch Size | 16 |
| Optimizer | AdamW |
| Learning Rate | 0.001 |
| Mixed Precision | Enabled |
| Cosine Learning Rate | Enabled |

---

## Technologies Used

- Python
- OpenCV
- YOLOv8
- Ultralytics
- ByteTrack
- NumPy

---

## Key Features

- Vehicle Detection
- Traffic Light Detection
- Traffic Signal State Recognition
- Multi-Object Tracking
- Stop Line Monitoring
- Automatic Violation Detection
- Traffic Statistics Generation
- Annotated Video Output

---

## Output

The system generates an output video containing:

- Vehicle IDs
- Traffic light status
- Stop line visualization
- Violation indicators
- Traffic statistics panel

Example statistics:

```text
Cars passed on RED: 5
Cars passed on GREEN: 18
Cars passed on YELLOW: 4

Violators IDs:
[3, 8, 12, 17, 24]
```

---

## What I Learned

One of the biggest lessons from this project was realizing that training a model is only one piece of the puzzle.

Building a useful AI solution requires thinking beyond model accuracy and focusing on how predictions can be transformed into meaningful decisions.

During development, I had to think about:

- How to track vehicles reliably.
- How to detect crossing events accurately.
- How to synchronize vehicle movement with traffic light states.
- How to convert detections into actionable information.

These challenges were just as important as training the detection model itself.

---

## Real-World Applications

This project can be extended for use in:

- Smart Cities
- Intelligent Transportation Systems
- Traffic Monitoring Platforms
- CCTV-Based Traffic Enforcement
- Road Safety Analytics
- Urban Traffic Management

---

## Future Improvements

Potential future enhancements include:

- Automatic License Plate Recognition (ANPR)
- Multi-camera support
- Vehicle speed estimation
- Traffic congestion analysis
- Automated violation reports
- Real-time CCTV deployment
- Cloud-based analytics dashboard
- Integration with smart city platforms

---

## Project Structure

```text
Traffic-Light-Violation-Detection/
│
├── dataset/
│   ├── train/
│   ├── valid/
│   └── test/
│
├── runs/
│   └── detect/
│
├── output/
│
├── traffic_violation.py
├── data.yaml
├── requirements.txt
├── README.md
│
└── assets/
```

---

## Installation

Clone the repository:

```bash
git clone https://github.com/your-username/Traffic-Light-Violation-Detection.git

cd Traffic-Light-Violation-Detection
```

Install dependencies:

```bash
pip install -r requirements.txt
```

---

## Usage

Update the following paths inside the script:

```python
MODEL_PATH = "best.pt"
VIDEO_PATH = "input_video.mp4"
OUTPUT_PATH = "traffic_violation_output.mp4"
```

Run the project:

```bash
python traffic_violation.py
```

The processed video will be generated automatically after completion.

---

## Final Thoughts

The most interesting part of this project was not training the YOLO model itself, but designing a system that could understand events happening within a traffic scene.

My goal was to move beyond simple object detection and build a solution that analyzes vehicle behavior, understands traffic conditions, and automatically identifies violations.

This project reflects my interest in applying Computer Vision and AI to solve practical real-world problems and transform raw data into actionable insights.

---

## Author

**Mohamed Ahmed**

Artificial Intelligence Student | Machine Learning & Computer Vision Enthusiast
