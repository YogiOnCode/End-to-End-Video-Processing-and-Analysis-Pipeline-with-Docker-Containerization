# End-to-End Video Processing & Analysis Pipeline

![Python](https://img.shields.io/badge/Python-3.9-3776AB?logo=python&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-2496ED?logo=docker&logoColor=white)
![YOLOv8](https://img.shields.io/badge/YOLOv8-Ultralytics-111F68)
![SQLite](https://img.shields.io/badge/SQLite-003B57?logo=sqlite&logoColor=white)

A containerized pipeline that takes a raw video, detects objects frame by frame with **YOLOv8**,
stores the detections in **SQLite**, and plots how the number of detected birds changes over time.
The sample input is a pigeon video, [`Pigeon.mp4`](Pigeon.mp4).

---

## Pipeline

```
Pigeon.mp4
   │
   ▼
Frames_extraction.py   →  frames/*.jpg + per-frame timestamps   (OpenCV)
   │
   ▼
Detection.py           →  Detection_Results.csv                  (YOLOv8n)
   │
   ▼
Create_db.py           →  detection_results.db                   (SQLite)
   │
   ▼
Plot.py                →  bird_detection_plot.png                (SQL query + Seaborn)
```

`main.py` runs the stages in order.

| Stage | What it does |
| --- | --- |
| **Frame extraction** | Splits the video into JPEG frames and computes a timestamp for each frame from the FPS. |
| **Detection** | Runs `yolov8n` on every frame and records class, confidence, bounding box, frame number and timestamp. |
| **Storage** | Loads the CSV into a SQLite table and adds a `duration_in_minutes` column for time-based queries. |
| **Visualization** | Uses SQLAlchemy to query bird counts per time step and plots them as a line chart. |

## Run with Docker

```bash
git clone https://github.com/YogiOnCode/End-to-End-Video-Processing-and-Analysis-Pipeline-with-Docker-Containerization.git
cd End-to-End-Video-Processing-and-Analysis-Pipeline-with-Docker-Containerization

docker build -t video-pipeline .
docker run --name video-pipeline video-pipeline

# copy the plot out of the container
docker cp video-pipeline:/app/bird_detection_plot.png .
```

The image is based on `python:3.9-slim` and installs the OpenCV system libraries. The requirements file is
copied before the source code so that dependency layers stay cached between builds.

## Run locally

```bash
pip install -r requirements.txt
python main.py
```

YOLOv8 weights (`yolov8n.pt`) are downloaded automatically by Ultralytics on the first run.

## Outputs

- `Frames/`: extracted video frames
- `Detection_Results.csv`: raw detections
- `detection_results.db`: SQLite database (`detection_results` table)
- `bird_detection_plot.png`: bird count over time

## Tech stack

Python · OpenCV · Ultralytics YOLOv8 · pandas · SQLite · SQLAlchemy · Matplotlib · Seaborn · Docker

## Author

**Yogeswaran Amsavalli** · [GitHub](https://github.com/YogiOnCode)
