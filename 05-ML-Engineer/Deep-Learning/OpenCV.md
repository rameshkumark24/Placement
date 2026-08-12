Ayy macha 😎🔥
**Here is the BEST “OpenCV Beginner → Industrial Expert” note**, crisp-a, clear-a, interview-friendly + project-ready.
Ithula:

✔ Basics
✔ Core functions
✔ Image processing
✔ Computer vision techniques
✔ Contours, masks, morphology
✔ Face detection
✔ Object detection
✔ Real-time video
✔ Industry-level workflows
✔ Interview answers

Everything in one MASTER document.
Save as: **`opencv_master_notes.md`**

---

````md
# 🚀 OPENCV — BEGINNER TO INDUSTRY-LEVEL COMPLETE NOTES  
(For ML Engineer, AI Engineer, CV Engineer, Robotics & DS Roles)

---

# 1️⃣ WHAT IS OPENCV?

OpenCV = “Open Source Computer Vision Library”.

Used for:
- Image processing  
- Video processing  
- Object detection  
- Face recognition  
- OCR  
- ML/AI + deep learning integration  

Supports: **Python, C++, Java**  
Highly optimized + used in real industries (AI cameras, robotics, AR, self-driving, etc.)

---

# 2️⃣ INSTALLATION

```bash
pip install opencv-python
pip install opencv-contrib-python   # for advanced modules
````

Import:

```python
import cv2
import numpy as np
```

---

# 3️⃣ LOAD & DISPLAY IMAGE

```python
img = cv2.imread("image.jpg")
cv2.imshow("Image", img)
cv2.waitKey(0)
cv2.destroyAllWindows()
```

---

# 4️⃣ SAVE IMAGE

```python
cv2.imwrite("output.jpg", img)
```

---

# 5️⃣ IMAGE PROPERTIES

```python
print(img.shape)  # (height, width, channels)
print(img.size)
print(img.dtype)
```

---

# 6️⃣ IMAGE COLOR SPACES (IMPORTANT)

```python
gray = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)
hsv  = cv2.cvtColor(img, cv2.COLOR_BGR2HSV)
rgb  = cv2.cvtColor(img, cv2.COLOR_BGR2RGB)
```

Industrial note:
💡 Most CV models prefer **RGB**
💡 Color filtering uses **HSV**

---

# 7️⃣ RESIZE, CROP, ROTATE

### Resize

```python
resize = cv2.resize(img, (300, 300))
```

### Crop

```python
crop = img[50:200, 100:300]
```

### Rotate

```python
rot = cv2.rotate(img, cv2.ROTATE_90_CLOCKWISE)
```

---

# 8️⃣ DRAWING SHAPES

```python
cv2.rectangle(img, (10,10), (200,200), (0,255,0), 3)
cv2.circle(img, (100,100), 50, (255,0,0), -1)
cv2.putText(img, "Hello", (50,50),
            cv2.FONT_HERSHEY_SIMPLEX, 1, (0,255,255), 2)
```

---

# 9️⃣ BLURRING (NOISE REDUCTION)

```python
blur = cv2.GaussianBlur(img, (5,5), 0)
median = cv2.medianBlur(img, 5)
bilateral = cv2.bilateralFilter(img, 9, 75, 75)
```

Industrial uses:

* Gaussian → general smoothing
* Median → salt & pepper noise
* Bilateral → preserves edges

---

# 🔟 EDGE DETECTION (VERY IMPORTANT)

### Canny Edge

```python
edges = cv2.Canny(img, 100, 200)
```

Used in:

* Lane detection
* Document scanning
* Object boundaries

---

# 1️⃣1️⃣ THRESHOLDING

```python
ret, thresh = cv2.threshold(gray, 127, 255, cv2.THRESH_BINARY)
```

Otsu Threshold (auto):

```python
ret, otsu = cv2.threshold(gray, 0, 255, cv2.THRESH_OTSU)
```

Used in:

* OCR
* Segmentation

---

# 1️⃣2️⃣ MORPHOLOGY (ADVANCED)

### Define kernel

```python
kernel = np.ones((5,5), np.uint8)
```

### Operations

```python
erosion = cv2.erode(img, kernel)
dilation = cv2.dilate(img, kernel)
opening = cv2.morphologyEx(img, cv2.MORPH_OPEN, kernel)
closing = cv2.morphologyEx(img, cv2.MORPH_CLOSE, kernel)
```

Industrial use:

* Remove noise
* Close gaps in text
* Extract shapes

---

# 1️⃣3️⃣ CONTOURS (IMPORTANT FOR INTERVIEWS)

```python
contours, hierarchy = cv2.findContours(thresh, cv2.RETR_TREE, cv2.CHAIN_APPROX_SIMPLE)
cv2.drawContours(img, contours, -1, (0,255,0), 2)
```

Use cases:

* Object boundary
* Shape detection
* Hand gesture recognition

---

# 1️⃣4️⃣ FACE DETECTION (HAAR CASCADE)

```python
face_cascade = cv2.CascadeClassifier("haarcascade_frontalface_default.xml")

gray = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)
faces = face_cascade.detectMultiScale(gray, 1.3, 5)

for (x,y,w,h) in faces:
    cv2.rectangle(img, (x,y), (x+w, y+h), (255,0,0), 2)
```

---

# 1️⃣5️⃣ REAL-TIME VIDEO PROCESSING (WEBCAM)

```python
cap = cv2.VideoCapture(0)

while True:
    ret, frame = cap.read()
    cv2.imshow("Video", frame)
    
    if cv2.waitKey(1) & 0xFF == ord('q'):
        break

cap.release()
cv2.destroyAllWindows()
```

---

# 1️⃣6️⃣ OBJECT DETECTION (YOLO + OpenCV)

Load YOLO weights:

```python
net = cv2.dnn.readNet("yolov3.weights", "yolov3.cfg")
```

Prepare blob:

```python
blob = cv2.dnn.blobFromImage(img, 1/255, (416,416), swapRB=True)
net.setInput(blob)
output = net.forward(net.getUnconnectedLayersNames())
```

Uses:

* Vehicle detection
* CCTV analytics
* Human detection

---

# 1️⃣7️⃣ IMAGE MASKING (CRITICAL)

```python
lower = np.array([0,150,50])
upper = np.array([10,255,255])

hsv = cv2.cvtColor(img, cv2.COLOR_BGR2HSV)
mask = cv2.inRange(hsv, lower, upper)
result = cv2.bitwise_and(img, img, mask=mask)
```

Used in:

* Color detection
* Object segmentation

---

# 1️⃣8️⃣ FEATURE DETECTION (SIFT, ORB)

### ORB (Free, fast)

```python
orb = cv2.ORB_create()
kp, des = orb.detectAndCompute(gray, None)
```

### Draw keypoints:

```python
out = cv2.drawKeypoints(img, kp, None)
```

---

# 1️⃣9️⃣ PERSPECTIVE TRANSFORM (DOCUMENT SCANNER)

```python
pts1 = np.float32([[50,50],[200,50],[50,200],[200,200]])
pts2 = np.float32([[0,0],[300,0],[0,300],[300,300]])

matrix = cv2.getPerspectiveTransform(pts1, pts2)
result = cv2.warpPerspective(img, matrix, (300,300))
```

---

# 2️⃣0️⃣ TEMPLATE MATCHING

```python
res = cv2.matchTemplate(img, template, cv2.TM_CCOEFF_NORMED)
min_val, max_val, min_loc, max_loc = cv2.minMaxLoc(res)
```

Used in:

* Icon detection
* UI automation

---

# 2️⃣1️⃣ OCR (TEXT EXTRACTION) + OpenCV + Tesseract

```python
import pytesseract
text = pytesseract.image_to_string(img)
```

Used in:

* ID card scanning
* Invoice extraction

---

# 2️⃣2️⃣ INDUSTRY WORKFLOW (VERY IMPORTANT)

Typical CV project flow:

1. Load image/video
2. Preprocess (blur, threshold, morphology)
3. Feature extraction (edges, contours, ORB)
4. Object detection / segmentation
5. Model inference
6. Draw bounding boxes / annotations
7. Export results (JSON, API, dashboard)

---

# 2️⃣3️⃣ PERFORMANCE OPTIMIZATION (COMPANY LEVEL)

✔ Use smaller frames (480p instead of 1080p)
✔ Convert to grayscale when possible
✔ Use GPU (OpenCV CUDA build)
✔ Batch detection (YOLO)
✔ Use contour simplification
✔ Avoid unnecessary conversions

---

# 2️⃣4️⃣ MOST COMMON INTERVIEW QUESTIONS

1. Difference between RGB, BGR, HSV?
2. What is Gaussian blur & why is it used?
3. What are morphological ops?
4. What is contour detection?
5. How does Canny edge work?
6. Face detection vs face recognition?
7. YOLO vs Haar Cascade difference?
8. What is perspective transform?
9. ORB vs SIFT difference?
10. Real-time video pipeline explanation.

---

# 🎯 PERFECT INTERVIEW ANSWER (If asked “What is OpenCV?”)

> “OpenCV is a Computer Vision library used for image and video processing, object detection, segmentation, motion tracking, face recognition, and real-time ML pipelines. It provides optimized algorithms that run efficiently on CPU/GPU, making it suitable for industry-level CV applications like surveillance, robotics, autonomous driving, and document processing.”

---
Ayy macha 😎🔥 — ready! Below are **practical, production-ready OpenCV end-to-end project templates** you can clone, run, and adapt for interviews or real systems. Each template includes:

* Project goal & use-case
* Repo layout (files/folders)
* `requirements.txt` / key libs
* Core scripts & short code snippets (ready-to-run)
* Training / data notes (if needed)
* Local dev, Docker + deploy, and CI/CD hints
* Testing, metrics, monitoring, performance tips
* Interview talking points

Copy whichever template you want and I’ll generate a full repo for you. Let’s go — 7 high-value templates.

---

# 1) Document Scanner (Mobile → Clean PDF)

Use-case: scan receipts, ID cards, docs from phone camera → auto crop, perspective correct, deskew, enhance, save PDF.

## Repo layout

```
document-scanner/
├─ data/                # sample images
├─ src/
│  ├─ main.py           # CLI + Flask API
│  ├─ scan.py           # core pipeline
│  ├─ utils.py
│  └─ models/           # if using ML for detection (optional)
├─ docker/
│  └─ Dockerfile
├─ requirements.txt
├─ README.md
└─ tests/
   └─ test_scan.py
```

## requirements.txt (core)

```
opencv-python
numpy
flask
pillow
imutils
pyPDF2
```

## Core pipeline (scan.py — simplified)

```python
import cv2, numpy as np
from imutils import perspective, contours
import imutils

def four_point_transform(image, pts):
    return perspective.four_point_transform(image, pts)

def scan_image(image_path):
    img = cv2.imread(image_path)
    ratio = img.shape[0] / 500.0
    orig = img.copy()
    img = imutils.resize(img, height=500)

    gray = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)
    gray = cv2.GaussianBlur(gray, (5,5), 0)
    edged = cv2.Canny(gray, 75, 200)

    cnts = cv2.findContours(edged.copy(), cv2.RETR_LIST, cv2.CHAIN_APPROX_SIMPLE)
    cnts = imutils.grab_contours(cnts)
    cnts = sorted(cnts, key=cv2.contourArea, reverse=True)[:5]

    for c in cnts:
        peri = cv2.arcLength(c, True)
        approx = cv2.approxPolyDP(c, 0.02 * peri, True)
        if len(approx) == 4:
            screenCnt = approx
            break

    warped = four_point_transform(orig, screenCnt.reshape(4,2) * ratio)
    warped_gray = cv2.cvtColor(warped, cv2.COLOR_BGR2GRAY)
    _, th = cv2.threshold(warped_gray, 0, 255, cv2.THRESH_BINARY + cv2.THRESH_OTSU)
    return th
```

## Run (CLI)

```bash
python src/main.py --input data/photo.jpg --output out.pdf
```

## Docker (high-level)

* Base: `python:3.10-slim`
* Copy project, `pip install -r requirements.txt`, expose Flask port, entrypoint `gunicorn main:app`.

## Tests

* Unit test for `four_point_transform` on known image.
* Integration: pipeline on a folder of images and check generated PDF pages count.

## Interview talking points

* Edge detection + contour approximation for document boundary.
* Perspective transform and Otsu for binarization.
* Handling curved pages, multiple pages, lighting correction.
* Performance: downscale for speed, operate on ROI.

---

# 2) Face Recognition (Auth & Attendance)

Use-case: real-time webcam recognition for door/attendance. Template integrates face detectors (Haar/DNN) + Face embeddings (FaceNet/ArcFace) or simple LBPH.

## Repo layout

```
face-recognition/
├─ data/
│  └─ known/           # subfolders per person
├─ src/
│  ├─ enroll.py        # create embeddings
│  ├─ recognize.py     # real-time app
│  ├─ model_utils.py   # load detectors/embeddings
│  └─ api.py           # FastAPI for remote inference
├─ requirements.txt
├─ docker/
└─ README.md
```

## requirements.txt (core)

```
opencv-contrib-python
numpy
face-recognition   # dlib based (optional)
flask or fastapi[all]
torch              # if using pretrained FaceNet/ArcFace
```

## Enrollment (enroll.py snippet using face_recognition lib)

```python
import face_recognition, os, pickle

def enroll(known_dir="data/known"):
    embeddings = {}
    for person in os.listdir(known_dir):
        img_path = os.path.join(known_dir, person, os.listdir(os.path.join(known_dir, person))[0])
        img = face_recognition.load_image_file(img_path)
        enc = face_recognition.face_encodings(img)[0]
        embeddings[person] = enc
    with open("embeddings.pkl","wb") as f:
        pickle.dump(embeddings, f)
```

## Real-time recognition (recognize.py)

* Open webcam, detect faces, compute embeddings, `compare_faces()` against stored embeddings with threshold.

## Deployment

* Service: FastAPI endpoint `/predict` that accepts base64 image, returns names + confidence.
* Edge: Run on device (Raspberry Pi + Coral/Jetson) for offline.

## Metrics & Tests

* Accuracy on holdout dataset, FAR/FRR, latency per frame.
* Load test API for throughput.

## Interview talking points

* Embedding vs classification tradeoffs.
* Face anonymization, GDPR concerns.
* Handling lighting, angle, occlusion; liveness detection.

---

# 3) Object Detection with YOLO (Realtime CCTV Analytics)

Use-case: detect people, vehicles, count, tracking.

## Repo layout

```
yolo-object-detection/
├─ data/
├─ src/
│  ├─ detect.py        # inference pipeline (OpenCV dnn)
│  ├─ track.py         # simple SORT tracker integration
│  ├─ annotate.py
│  └─ export_to_db.py
├─ models/             # yolo.weights & cfg or ONNX
├─ docker/
├─ requirements.txt
└─ README.md
```

## requirements.txt (core)

```
opencv-python
numpy
filterpy       # for SORT tracker
onnxruntime    # if using ONNX model
```

## Inference (detect.py)

```python
net = cv2.dnn.readNet(weights_path, cfg_path)
blob = cv2.dnn.blobFromImage(frame, 1/255.0, (416,416), swapRB=True, crop=False)
net.setInput(blob)
outs = net.forward(output_layer_names)
# parse outputs -> boxes, confidences, class_ids -> NMS -> results
```

## Tracking

* Use SORT or DeepSORT to assign IDs, maintain counts, detect line-crossing events.

## Deploy

* Containerize with GPU support (nvidia runtime) for real-time throughput.
* Streaming: capture from RTSP, process, push detections to Kafka or Redis.

## Monitoring

* TPS (frames/sec), detection latency, false positive rates, resource usage per GPU.

## Interview points

* NMS, IOU thresholds, anchor boxes, quantization for edge, batching vs per-frame.

---

# 4) OCR Pipeline (Invoice / Receipt Extraction)

Use-case: extract structured fields (vendor, date, total) from receipts using OpenCV preprocessing + Tesseract or deep OCR models.

## Repo layout

```
ocr-pipeline/
├─ data/
│  ├─ raw_images/
│  └─ annotations/   # for training (optional)
├─ src/
│  ├─ preprocess.py
│  ├─ ocr_engine.py
│  ├─ postprocess.py
│  └─ pipeline.py
├─ models/
├─ requirements.txt
└─ README.md
```

## requirements.txt (core)

```
opencv-python
pytesseract
numpy
pandas
regex
```

## Key steps

1. Deskew + crop → adaptiveThreshold → morphology
2. Text detection (EAST or CRAFT) for line/box detection
3. OCR via Tesseract / TrOCR / EasyOCR
4. Post-process: regex extract date/amount, fuzzy vendor match

## Example (preprocess.py)

```python
gray = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)
gray = cv2.bilateralFilter(gray,9,75,75)
th = cv2.adaptiveThreshold(gray,255,cv2.ADAPTIVE_THRESH_GAUSSIAN_C, cv2.THRESH_BINARY,11,2)
```

## Evaluation

* Field-level precision/recall, end-to-end extraction F1.
* Use labeled dataset with ground truth JSON.

## Deployment

* REST API for bulk upload; use job queue (Celery + Redis) for heavy OCR tasks.

---

# 5) License Plate Recognition (ANPR)

Use-case: read plates from CCTV, map to vehicles, tolls.

## Repo layout

```
anpr/
├─ data/
├─ src/
│  ├─ detect_plate.py       # detect plate bbox with YOLO/EAST
│  ├─ segment_chars.py
│  ├─ recognize.py          # OCR per char or CRNN model
│  └─ api.py
├─ models/
├─ requirements.txt
└─ docker/
```

## Pipeline

1. Detect plate bbox (object detector)
2. Align & crop plate (perspective transform)
3. Clean & threshold for segmentation
4. Use CRNN / Tesseract for sequence recognition
5. Postprocess: country format, checksum

## Performance

* Plate detection mAP, char BER, end-to-end accuracy, latency.

## Deployment

* Edge devices for low latency; stream detections to central DB.

---

# 6) Motion Detection & Tracking (Industrial Surveillance)

Use-case: detect intrusions, track movement heatmaps.

## Repo layout

```
motion-tracking/
├─ src/
│  ├─ motion_bgsub.py     # background subtraction pipeline
│  ├─ heatmap.py
│  └─ alert.py
├─ docker/
├─ requirements.txt
└─ README.md
```

## Core technique

* BackgroundSubtractorMOG2 or KNN
* Morphology to remove noise
* Contour detection → bbox → track with simple tracker

## Alerts

* When object crosses restricted zone → push alert (Slack / webhook)
* Store snapshots + metadata to DB

## Scaling

* Use stream processing: ingest RTSP → process worker pool → central aggregator.

---

# 7) Industrial Defect Detection (Manufacturing)

Use-case: find surface defects on assembly line (high FPS).

## Repo layout

```
defect-detection/
├─ data/
│  ├─ good/
│  └─ defective/
├─ src/
│  ├─ augment.py
│  ├─ train.py            # train classification/segmentation
│  ├─ infer.py            # fast inference optimized for edge
│  └─ eval.py
├─ models/
├─ requirements.txt
└─ README.md
```

## Model choices

* Simple: classical vision (threshold + morphology) for consistent parts
* Advanced: classification (ResNet) or segmentation (U-Net) depending on defect type

## Training tips

* Use heavy augmentation (brightness, blur) to mimic production variability
* Use focal loss if imbalance
* Prune/quantize for edge deployment (TFLite / TensorRT)

## Deployment

* Real-time inference on Jetson / Coral, stream alarms + defect images to MES.

---

## Common pieces across templates

### Dockerfile (generic)

```dockerfile
FROM python:3.10-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY . /app
CMD ["gunicorn", "src.api:app", "-b", "0.0.0.0:8000", "--workers", "4"]
```

### CI/CD (GitHub Actions) — high-level

* `on: push`
* Steps:

  * Checkout
  * Set up Python
  * Install deps
  * Run unit tests
  * Lint
  * Build Docker image
  * Push to registry (on tag)
  * Deploy to staging (manual approval to prod)

### Deployment patterns

* **Edge**: containerize + hardware acceleration (CUDA/TensorRT)
* **Cloud**: serverless endpoints (FastAPI + autoscaling), GPU nodes for heavy inference
* **Streaming**: Kafka topics for events, workers consume frames, aggregator saves results

### Monitoring & Observability

* Logs: structured JSON (timestamp, frame_id, latency, detections)
* Metrics: Prometheus (fps, latency, error rate)
* Traces: Jaeger (for multi-service pipelines)
* Alerts: Slack/email for degraded performance or high FP rates

### Testing

* Unit tests for core transforms (deskew, threshold)
* Integration test on a small sample of images
* Performance benchmark tests (throughput + latency)

### Security & Privacy

* Secure video streams (RTSP over TLS), secure storage
* Mask/obfuscate PII (faces/license plates) if required
* GDPR: retention policies, access control

### Dataset & Annotation tools

* Tools: LabelImg, CVAT, VOTT
* Formats: COCO, Pascal VOC, YOLO txt
* Versioning: DVC or Git LFS for large binaries

---

## Interview-ready summary (one-liner per project)

* Document Scanner: contour → perspective → binarize → export PDF. (Explain Otsu + adaptive threshold.)
* Face Recognition: enroll embeddings → compare → threshold → liveness & GDPR considerations.
* YOLO CCTV: real-time object detection → NMS → SORT tracker → stream to Kafka.
* OCR Pipeline: detect text regions → Tesseract/trOCR → regex field extraction.
* ANPR: detect plate → align → CRNN → format validation + DB mapping.
* Motion Tracking: background subtraction → morphology → contour → alerting.
* Defect Detection: augment → classify/segment → edge-optimized inference with quantization.

---

# 🚀 **TOP 50 OPENCV INTERVIEW QUESTIONS & ANSWERS (COMPANY LEVEL)**

---

# ✅ **BASICS & FOUNDATIONS**

---

### **1️⃣ What is OpenCV?**

**Answer:**
OpenCV (Open Source Computer Vision Library) is a fast, optimized library used for image processing, computer vision, and real-time AI applications.

Used in: surveillance, robotics, self-driving, AR, face detection, object tracking.

---

### **2️⃣ Why is OpenCV used in industry?**

**Answer:**

* Extremely fast (written in C++)
* Real-time video processing
* Supports deep learning models
* Works on edge devices (Raspberry Pi, Jetson)
* Integrates with Python, C++, Java

---

### **3️⃣ What color format does OpenCV use by default?**

**Answer:**
BGR (not RGB).

---

### **4️⃣ How do you convert BGR to RGB?**

```python
cv2.cvtColor(img, cv2.COLOR_BGR2RGB)
```

---

### **5️⃣ How do you load and display an image?**

```python
img = cv2.imread("img.jpg")
cv2.imshow("image", img)
cv2.waitKey(0)
```

---

### **6️⃣ What does `img.shape` return?**

**Answer:**
(height, width, channels)

---

### **7️⃣ Difference between grayscale and binary image?**

* Grayscale → 0–255 pixel intensities
* Binary → 0 or 255 (after thresholding)

---

### **8️⃣ What is a kernel?**

**Answer:**
A matrix used for filtering operations (blur, sharpen, edge detection).

---

# ✅ **IMAGE PROCESSING**

---

### **9️⃣ What is Gaussian blur? Why used?**

**Answer:**
Smooths the image, reduces noise, preserves edges better than normal blur.

---

### 🔟 What is median blur used for?

**Answer:**
Salt-and-pepper noise removal.

---

### **1️⃣1️⃣ What is bilateral filter?**

**Answer:**
Smooths image while preserving edges.
Used for beauty filters, face smoothing.

---

### **1️⃣2️⃣ What is thresholding?**

**Answer:**
Converts grayscale → binary.
Used in OCR, segmentation.

---

### **1️⃣3️⃣ What is Otsu thresholding?**

**Answer:**
Automatically calculates best threshold value.

```python
ret, th = cv2.threshold(img, 0, 255, cv2.THRESH_OTSU)
```

---

### **1️⃣4️⃣ Difference between dilation & erosion?**

| Operation | Effect                |
| --------- | --------------------- |
| Dilation  | Expands white regions |
| Erosion   | Shrinks white regions |

Used for noise removal & shape operations.

---

### **1️⃣5️⃣ What is opening & closing (morphology)?**

| Operation | Purpose      |
| --------- | ------------ |
| Opening   | Remove noise |
| Closing   | Fill gaps    |

---

### **1️⃣6️⃣ What is Canny edge detection?**

**Answer:**
A multi-stage algorithm for detecting edges using gradient, non-max suppression & hysteresis.

---

### **1️⃣7️⃣ Why convert image to HSV?**

**Answer:**
HSV makes color segmentation easier and lighting independent.

---

### **1️⃣8️⃣ What is image normalization?**

**Answer:**
Rescaling pixel values to a fixed range (0–1 or -1 to 1).

---

# ✅ **CONTOURS & SHAPES**

---

### **1️⃣9️⃣ What are contours?**

**Answer:**
Boundaries of shapes detected using edges or thresholds.

---

### **2️⃣0️⃣ How do you find contours?**

```python
contours, _ = cv2.findContours(img, cv2.RETR_TREE, cv2.CHAIN_APPROX_SIMPLE)
```

---

### **2️⃣1️⃣ What is contour approximation?**

**Answer:**
Approximates a contour to fewer points (e.g., turning curves into polygons).

---

### **2️⃣2️⃣ How do you detect shapes (triangle, rectangle)?**

Use contour approximation + number of edges.

---

### **2️⃣3️⃣ What is convex hull?**

**Answer:**
Smallest convex shape enclosing a contour.
Used for gesture recognition.

---

# ✅ **FEATURE DETECTION**

---

### **2️⃣4️⃣ What is SIFT?**

Scale Invariant Feature Transform → extracts strong keypoints.
Scale & rotation invariant. (Non-free earlier, now open-source)

---

### **2️⃣5️⃣ What is SURF?**

Similar to SIFT but faster.
Still patented → in `opencv-contrib`.

---

### **2️⃣6️⃣ What is ORB?**

Free, fast alternative to SIFT/SURF.
Used in SLAM, mobile apps.

---

### **2️⃣7️⃣ What are keypoints?**

Distinctive points in an image (corners, blobs).

---

### **2️⃣8️⃣ What is feature matching?**

Comparison of keypoints between two images using descriptors.

---

### **2️⃣9️⃣ What are FLANN & BFMatcher?**

Tools for matching feature descriptors.

---

# ✅ **TRANSFORMS & PROJECTIONS**

---

### **3️⃣0️⃣ What is perspective transform?**

Used to warp images → document scanning, plate recognition.

---

### **31️⃣ What is affine transform?**

Linear transform preserving parallelism → rotation, scaling, shear.

---

### **3️⃣2️⃣ Purpose of Hough Transform?**

Detects lines & circles using voting technique.

Use cases: lane detection, coin counting.

---

### **3️⃣3️⃣ What is homography?**

Mapping between two planes → used in panorama stitching.

---

# ✅ **VIDEO PROCESSING**

---

### **3️⃣4️⃣ How to read webcam video?**

```python
cap = cv2.VideoCapture(0)
```

---

### **3️⃣5️⃣ What is frame differencing?**

Detects motion by subtracting consecutive frames.

---

### **3️⃣6️⃣ What is background subtraction?**

Removes static background → detects moving objects.

Models:

* MOG2
* KNN

---

### **3️⃣7️⃣ How to set FPS in video?**

```python
cap.set(cv2.CAP_PROP_FPS, 30)
```

---

# ✅ **FACE DETECTION & RECOGNITION**

---

### **3️⃣8️⃣ Haar cascade vs DNN face detector?**

| Haar Cascade | DNN Detector          |
| ------------ | --------------------- |
| Fast         | Accurate              |
| Lightweight  | Slow                  |
| Works on CPU | Requires more compute |
| Old method   | Modern CNN-based      |

---

### **3️⃣9️⃣ How does Haar cascade work?**

Uses features + Adaboost classifier + cascade stages.

---

### **4️⃣0️⃣ How does face recognition work?**

Steps:

1. Detect face
2. Extract embedding
3. Compare embedding with known vectors
4. Use threshold for identity

---

# ✅ **OBJECT DETECTION**

---

### **4️⃣1️⃣ Can OpenCV run YOLO / SSD / MobileNet models?**

**Answer:**
Yes, via `cv2.dnn` module.

---

### **4️⃣2️⃣ Steps in object detection (YOLO/SSD)?**

1. Preprocess image (blob)
2. Forward pass
3. Filter by confidence
4. Apply NMS
5. Draw bounding boxes

---

# ✅ **TRACKING**

---

### **4️⃣3️⃣ What is object tracking?**

Following detected object across multiple frames.

Trackers:

* KCF
* CSRT
* MOSSE
* MedianFlow
* Boosting
* GOTURN (DNN)

---

### **4️⃣4️⃣ Difference between detection & tracking?**

| Detection    | Tracking            |
| ------------ | ------------------- |
| Finds object | Follows object      |
| Heavy (CNN)  | Light & fast        |
| Per-frame    | Uses previous frame |

---

### **4️⃣5️⃣ What is optical flow?**

Estimates pixel motion between frames.
Used in gesture tracking, video stabilization.

---

# ✅ **DEEP LEARNING IN OPENCV**

---

### **4️⃣6️⃣ What is DNN module?**

OpenCV’s deep learning inference engine supporting models from:

* TensorFlow
* PyTorch (ONNX)
* Caffe
* Darknet (YOLO)

---

### **4️⃣7️⃣ How do you load a model in OpenCV DNN?**

```python
net = cv2.dnn.readNetFromONNX("model.onnx")
```

---

### **4️⃣8️⃣ What is blobFromImage?**

Converts image → normalized tensor for CNN inference.

```python
blob = cv2.dnn.blobFromImage(img, 1/255, (416,416))
```

---

### **4️⃣9️⃣ What is NMS (Non-Max Suppression)?**

Removes duplicate bounding boxes based on IOU threshold.

---

### **5️⃣0️⃣ How do you improve CV model performance?**

* Use GPU acceleration
* Resize frames for speed
* Use quantized ONNX models
* Apply batch inference
* Reduce resolution
* Use CSRT or MOSSE for tracking
* Use multi-threading

---

# 🎯 **BONUS: 1-Line Answers for HR + Panel Impress**

**“What is the difference between classical CV and deep CV?”**
Classical → edges, contours, filters
Deep → CNN extract features automatically

**“Why is OpenCV still used when deep learning exists?”**
Speed, simplicity, low compute requirement, edge deployment.

**“How do you deploy OpenCV pipeline?”**
FastAPI / Flask API → Docker → GPU server / edge device → monitoring.

---

