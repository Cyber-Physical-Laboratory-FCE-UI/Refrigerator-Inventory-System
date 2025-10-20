# Supply Management — YOLO Inference & HTML Report

## Table of Contents
- [Overview](#overview)
- [Project Structure](#project-structure)
- [Model Summary](#model-summary)
- [Requirements](#requirements)
- [Setup: Virtual Environment](#setup-virtual-environment)
- [Install Dependencies](#install-dependencies)
  - [YOLOv8 & YOLOv11](#yolov8--yolov11)
  - [YOLOv5](#yolov5)
- [How to Run](#how-to-run)
  - [Examples](#examples)
  - [Sample Console Output](#sample-console-output)
- [Outputs & HTML Server](#outputs--html-server)
- [Running on Raspberry Pi](#running-on-raspberry-pi)
- [License](#license)

---

## Overview
This project runs object detection with **YOLO v5, v8, and v11**, writes detections to a **CSV**, and renders a browsable **HTML report** served locally.

---

## Project Structure
```
supply_management/
├─ models/               # Trained weights
│  ├─ yolov5m_9cls.pt    # YOLOv5 (medium) — fruit dataset (9 classes)
│  ├─ yolov8m_9cls.pt    # YOLOv8 (medium) — fruit dataset (9 classes)
│  └─ yolo11n-12cls.pt   # YOLOv11 (nano)  — food dataset (12 classes)
├─ html/                 # Code/templates to build and render the HTML report
│  └─ ...                # index.html is generated from CSV
├─ inference.py          # Main script: runs models, writes CSV, serves HTML
└─ (optional) yolov5/    # Clone the YOLOv5 repo here to run v5
```

---

## Model Summary
| File name        | YOLO version | Size   | #Classes | Dataset    |
|---               |---:          |---     |---:      |---         |
| `yolov5m_9cls`| v5           | Medium | 9        | Fruits     |
| `yolov8m_9cls`| v8           | Medium | 9        | Fruits     |
| `yolov11n_12cls` | v11          | Nano   | 12       | Food items |


---

## Requirements
- **Python 3.9+** recommended.

---

## Setup: Virtual Environment
From the project root (`supply_management/`):

**Linux / macOS**
```bash
python3 -m venv .venv
source .venv/bin/activate
```

**Windows (PowerShell)**
```powershell
python -m venv .venv
.\.venv\Scripts\Activate
```

---

## Install Dependencies

### YOLOv8 & YOLOv11
```bash
pip install ultralytics
```

### YOLOv5
1) Clone the official repo **into this directory**:
```bash
git clone https://github.com/ultralytics/yolov5.git
```
2) Install its requirements:
```bash
pip install -r yolov5/requirements.txt
```

> YOLOv8/11 work via the `ultralytics` package; YOLOv5 needs the `yolov5/` folder and its requirements.

---

## How to Run
General command:
```bash
python inference.py -m models/<model_name> -n <N>
```

- `-m, --model` : path to the weight file
- `-n, --num`   : number of consecutive images to capture/process

### Examples

```powershell
python .\inference.py -m models\yolo11-nano.pt -n 3
```

### Sample Console Output
```
WILL BE TAKEN IN ...
        1
        2
        3

image 1/1 path\supply_managment\original0.jpg: 480x640 1 Fries, 111.3ms
Speed: 3.4ms preprocess, 111.3ms inference, 235.0ms postprocess per image at shape (1, 3, 480, 640)
```

---

## Outputs & HTML Server
- The script writes **`database.csv`** with detections.
- Then it renders **`index.html`** using code in **`html/`** and serves it via Python’s `http.server` on **`localhost:9000`** by default.
- You can change the port in `inference.py` (default: **9000**).

Open in your browser:
```
http://<machine-ip>:9000
```
For local-only viewing:
```
http://localhost:9000
```

---

## Running on Raspberry Pi
Export lighter formats and run with TFLite or ONNX Runtime.

1) **Export on your dev machine** with Ultralytics:
```bash
# TFLite
yolo export model=models/yolo11-nano.pt format=tflite

# ONNX
yolo export model=models/yolo8-medium.pt format=onnx opset=12
```

2) Copy the exported `.tflite` or `.onnx` to the Pi.

3) **Install runtimes on the Pi:**
```bash
# For TFLite
pip install tflite-runtime

# Or for ONNX
pip install onnxruntime
```

4) Use a lightweight inference script (or adapt `inference.py`) to load the TFLite/ONNX model, write `database.csv`, render `index.html`, and serve via `http.server`.

> Tips for Pi: prefer **nano** models, lower input resolution, and use accelerators (e.g., Coral) if available.

---

## License
Add your project license here.
