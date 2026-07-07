# 📸 Stereo Vision 3D Reconstruction

> A complete stereo vision pipeline for sparse 3D reconstruction using camera calibration, SIFT feature matching, Essential Matrix estimation, and triangulation.

![Python](https://img.shields.io/badge/Python-3.10-blue)
![OpenCV](https://img.shields.io/badge/OpenCV-Computer_Vision-green)
![Open3D](https://img.shields.io/badge/Open3D-Visualization-orange)
![License](https://img.shields.io/badge/License-MIT-lightgrey)

---

## 📖 Overview

This project implements a complete **Stereo Vision** pipeline capable of reconstructing the 3D structure of a scene from two images captured with a known camera translation.

The system estimates the **camera pose**, computes **3D coordinates** of matched feature points, scales the reconstruction into **real-world millimeters**, and visualizes the resulting point cloud using **Open3D**.

The project was developed as part of the **Visual Computing (Computer Vision)** course at **USTHB**.

---

## ✨ Features

- 📷 Camera calibration using chessboard images
- 🔧 Lens distortion correction
- 🔍 Robust SIFT feature detection
- 🤝 FLANN-based feature matching
- ✅ Lowe's Ratio Test filtering
- 📐 Essential Matrix estimation with RANSAC
- 🎯 Camera pose recovery (Rotation & Translation)
- 📦 Sparse 3D reconstruction via triangulation
- 📏 Metric scaling using the physical camera baseline
- 🧹 Statistical outlier removal
- 🌍 Interactive 3D visualization with Open3D
- 💾 Export point clouds to `.ply`

---

# 🧠 Stereo Vision Principle

Stereo vision estimates depth by observing the same scene from two different viewpoints.

The horizontal displacement (called **disparity**) between corresponding image points allows the depth to be computed.

\[
Z=\frac{f \times B}{d}
\]

Where

| Symbol | Description |
|---------|-------------|
| **Z** | Depth |
| **f** | Camera focal length (pixels) |
| **B** | Baseline between the two camera positions (mm) |
| **d** | Disparity between corresponding points |

As disparity increases, the object is closer to the camera.

---

# 🏗️ Pipeline

The project is divided into five independent modules.

## 1️⃣ Camera Calibration

**File**

```text
step1_calibration.py
```

### Responsibilities

- Detect chessboard corners
- Estimate intrinsic camera parameters
- Compute distortion coefficients
- Save calibration matrices

### Outputs

```
camera_K.npy
camera_dist.npy
```

---

## 2️⃣ Image Acquisition & Undistortion

**File**

```text
step2_acquisition.py
```

### Responsibilities

- Capture stereo image pair
- Apply lens distortion correction
- Save undistorted images

---

## 3️⃣ Feature Detection & Matching

**File**

```text
step3_sift_matching.py
```

### Methodology

- SIFT detector
- FLANN matcher
- Lowe's Ratio Test (0.70)

Only reliable correspondences are kept for reconstruction.

### Output

```
sift_matches.jpg
```

---

## 4️⃣ Sparse 3D Reconstruction

**File**

```text
step4_reconstruction_3d.py
```

This is the core of the project.

### Processing Steps

### • Essential Matrix

Estimate the geometric relationship between the two camera positions using:

```python
cv2.findEssentialMat()
```

RANSAC automatically removes incorrect matches.

---

### • Camera Pose Recovery

Recover

- Rotation matrix **R**
- Translation vector **t**

using

```python
cv2.recoverPose()
```

---

### • Triangulation

Projection matrices are constructed as

\[
P_1 = K[I|0]
\]

\[
P_2 = K[R|t]
\]

3D points are computed using

```python
cv2.triangulatePoints()
```

---

### • Metric Scale Recovery

Monocular stereo reconstruction suffers from **scale ambiguity**.

To recover real dimensions:

\[
Scale=\frac{Baseline}{||t||}
\]

All reconstructed points are then converted into **millimeters**.

---

### • Point Cloud Filtering

The reconstruction is refined by removing

- Points behind the camera
- Invalid triangulations
- Statistical outliers using a **3σ rule**

---

## 5️⃣ Visualization

**File**

```text
step5_visualization.py
```

The reconstructed point cloud is visualized using

- Matplotlib
- Open3D

and exported as

```
nuage_points.ply
```

for visualization in MeshLab or CloudCompare.

---

# 📂 Project Structure

```
Stereo-Reconstruction/

│
├── step1_calibration.py
├── step2_acquisition.py
├── step3_sift_matching.py
├── step4_reconstruction_3d.py
├── step5_visualization.py
│
├── main_stereo.py
│
├── camera_K.npy
├── camera_dist.npy
│
├── image_left_undist.jpg
├── image_right_undist.jpg
│
├── points_3d.npy
├── points_3d.txt
├── nuage_points.ply
│
├── resultat_3d.png
└── sift_matches.jpg
```

---

# ▶️ Running the Project

Execute the complete pipeline

```bash
python main_stereo.py
```

or run each stage individually

```bash
python step1_calibration.py

python step2_acquisition.py

python step3_sift_matching.py

python step4_reconstruction_3d.py

python step5_visualization.py
```

---

# 📊 Experimental Results

Two different baselines were evaluated.

| Baseline | Reconstructed Points | Observation |
|-----------|--------------------:|-------------|
| **100 mm** | 776 | Better feature matching due to smaller viewpoint change |
| **150 mm** | 791 | Larger disparity improves depth resolution while maintaining high inlier count |

### Feature Matching

SIFT naturally concentrated its detections on highly textured regions such as box edges while ignoring flat walls with little texture.

---

# ✅ Strengths

- Robust outlier rejection using **Lowe's Ratio Test** and **RANSAC**
- Real metric reconstruction using physical baseline scaling
- Accurate estimation of camera pose
- Modular architecture that simplifies debugging and experimentation
- Easy extension toward more advanced stereo vision pipelines

---

# ⚠️ Limitations

- Produces only a **sparse** point cloud
- Reconstruction quality depends on textured surfaces
- Manual camera translation introduces slight rotational errors
- Sensitive to feature matching quality

---

# 🚀 Future Improvements

- Dense reconstruction using **StereoSGBM**
- Fixed stereo camera rig
- Bundle Adjustment optimization
- GPU acceleration
- Surface reconstruction and mesh generation
- Camera pose refinement using nonlinear optimization

---

# 🛠️ Technologies Used

- Python
- OpenCV
- NumPy
- Matplotlib
- Open3D

---

# 📚 References

- Hartley & Zisserman — *Multiple View Geometry in Computer Vision*
- OpenCV Documentation
- Lowe, D. G. (2004). *Distinctive Image Features from Scale-Invariant Keypoints.*
- Szeliski, R. *Computer Vision: Algorithms and Applications.*

---

# 👨‍💻 Author

**Chakib Kedjour**

Master's Student in Image Processing & Artificial Intelligence (MIV)

USTHB — Algeria
