<h1 align="center">🎯 Face Detection using RetinaFace</h1>

<p align="center">
  <img src="https://img.shields.io/badge/Python-3.8+-3776AB?style=for-the-badge&logo=python&logoColor=white"/>
  <img src="https://img.shields.io/badge/OpenCV-4.x-5C3EE8?style=for-the-badge&logo=opencv&logoColor=white"/>
  <img src="https://img.shields.io/badge/RetinaFace-InsightFace-FF6F00?style=for-the-badge"/>
  <img src="https://img.shields.io/badge/Google%20Colab-T4%20GPU-F9AB00?style=for-the-badge&logo=googlecolab&logoColor=white"/>
</p>

<p align="center">
  Detects and localizes human faces in video using <strong>RetinaFace</strong> — a state-of-the-art single-stage dense face localization model.
  Every detected face is annotated with a bounding box and a confidence score.
</p>

---

## 🎬 Demo — Output Video

> The video below shows the annotated output with bounding boxes and confidence scores drawn on every detected face.

<video src="output_video.mp4" controls width="100%">
  Your browser does not support the video tag. 
  <a href="output_video.mp4">Click here to download the output video</a>.
</video>

**Input:** [`test_video.mp4`](test_video.mp4) &nbsp;|&nbsp; **Output:** [`output_video.mp4`](output_video.mp4)

---

## 📌 Objective

> Given an input video file, **localize and draw bounding boxes** around the faces of all characters appearing in the video.

---

## 🧠 Approach

### Model Selection

Three detectors were evaluated before settling on the final choice:

| Detector | Accuracy | Notes |
|---|---|---|
| OpenCV DNN (SSD/ResNet-10) | ❌ Low | Struggled with backlit and small faces |
| MediaPipe Face Detection | ⚠️ Medium | Fast, but missed faces in crowd/group shots |
| **RetinaFace** ✅ | **High** | Best overall; handles low-light, backlit, and group shots |

**RetinaFace** was chosen as the final model due to its superior performance on the challenging footage in this video — backlit subjects, dim indoor lighting, and multiple small faces in a single frame.

---

### Pipeline

```
test_video.mp4
      │
      ▼
┌─────────────────────────┐
│  OpenCV VideoCapture    │  ← Read frames one-by-one
└─────────────────────────┘
      │
      ▼
┌─────────────────────────┐
│  BGR → RGB Conversion   │  ← RetinaFace expects RGB
└─────────────────────────┘
      │
      ▼
┌─────────────────────────────────────────────────┐
│  RetinaFace.detect_faces(frame)                 │
│  ResNet-50 + FPN backbone + Multi-scale Anchors │
└─────────────────────────────────────────────────┘
      │
      ▼
┌─────────────────────────┐
│  Filter by confidence   │  ← Threshold: 0.5
│  Clip boxes to frame    │
└─────────────────────────┘
      │
      ▼
┌─────────────────────────┐
│  Draw bounding boxes    │  ← Green rectangle + % confidence label
│  cv2.rectangle()        │
└─────────────────────────┘
      │
      ▼
┌─────────────────────────┐
│  OpenCV VideoWriter     │  ← Write annotated frame to output
└─────────────────────────┘
      │
      ▼
output_video.mp4
```

---

### Step-by-Step

1. **Video I/O** — OpenCV (`cv2`) reads input frames and writes annotated output.
2. **Warm-up** — A dummy 64×64 blank image is passed through RetinaFace at startup to load and cache model weights before processing begins.
3. **Per-frame detection** — `RetinaFace.detect_faces()` returns a dict of detected faces, each with:
   - `facial_area`: `[x1, y1, x2, y2]` pixel coordinates
   - `score`: confidence value between 0 and 1
4. **Filtering** — Only detections with `score ≥ 0.5` are kept.
5. **Bounding box clipping** — Coordinates are clipped to stay within frame boundaries.
6. **Annotation** — A green rectangle and confidence percentage are drawn on each frame.

---

## 🤖 Model Details

**RetinaFace** is a single-stage dense face localizer from the [InsightFace](https://github.com/deepinsight/insightface) project.

- **Backbone**: ResNet-50 with Feature Pyramid Network (FPN)
- **Multi-scale anchors**: Handles both small and large faces
- **Key strengths**: Robust to occlusion, varying poses, low-light, and dense crowds
- **License**: MIT
- **Weights**: Auto-downloaded from `deepface_models` (~119 MB)

> 📄 **Paper**: Deng et al., *"RetinaFace: Single-stage Dense Face Localisation in the Wild"*, CVPR 2020.  
> 🔗 **GitHub**: [serengil/retinaface](https://github.com/serengil/retinaface)

---

## 📊 Results

| Metric | Value |
|---|---|
| Input video | `test_video.mp4` |
| Total frames processed | **956 frames** |
| Processing time | ~**2 hours 21 minutes** (Google Colab T4 GPU) |
| Confidence threshold | **0.5** |
| Output | `output_video.mp4` |

---

## 🚀 Installation & Usage

### Option 1: Google Colab (Recommended)

1. Open the notebook: [`Human face detection.ipynb`](Human%20face%20detection.ipynb)
2. Click **"Open in Colab"** or upload to your Google Drive
3. Enable **GPU runtime**: `Runtime > Change runtime type > T4 GPU`
4. Run all cells in order

### Option 2: Local Setup

```bash
# Clone the repository
git clone https://github.com/shankyyyy/Face-Detection-using-RetinaFace.git
cd Face-Detection-using-RetinaFace

# Install dependencies
pip install retina-face opencv-python numpy tqdm

# Run the notebook
jupyter notebook "Human face detection.ipynb"
```

> **Note**: Processing 956 frames is computationally intensive. A GPU is strongly recommended. On CPU, runtime will be significantly longer.

---

## 📁 File Structure

```
Face-Detection-using-RetinaFace/
│
├── Human face detection.ipynb   # Main Jupyter notebook (full solution)
├── test_video.mp4                # Input video (7.5 MB)
├── output_video.mp4              # Annotated output video (18.5 MB)
├── .gitignore                    # Python/Jupyter gitignore
└── README.md                     # This file
```

---

## 🔗 References

- [RetinaFace GitHub (serengil)](https://github.com/serengil/retinaface) — pip package used (MIT License)
- [InsightFace — Original RetinaFace](https://github.com/deepinsight/insightface/tree/master/detection/retinaface)
- Deng, J., Guo, J., Ververas, E., Kotsia, I., & Zafeiriou, S. (2020). [*RetinaFace: Single-stage Dense Face Localisation in the Wild*](https://arxiv.org/abs/1905.00641). CVPR 2020.
- [OpenCV Documentation](https://docs.opencv.org/)
- [tqdm — Progress Bars for Python](https://github.com/tqdm/tqdm)

---

## 👤 Author

**Shashank Yadav**  
📧 [shashankyadav4123@gmail.com](mailto:shashankyadav4123@gmail.com)

---

<p align="center">
  Made with ❤️ using <strong>RetinaFace</strong> + <strong>OpenCV</strong> on Google Colab
</p>
