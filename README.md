# People Flow Detection & Heatmap Visualization

## Objective
This project tracks and counts people in a video using deep learning–based detection and tracking.
It classifies human movement as **IN** or **OUT** based on crossing two horizontal lines, and also generates a **motion heatmap** showing where people were most active in the scene.

---

## Detection Method

### 1. **People Detection**
- **Model:** YOLOv8x (from the Ultralytics library)
- **Purpose:** Detect only the *person* class (class ID = 0) from each video frame.
- **Why YOLOv8x?** It offers high accuracy and real-time performance, ideal for surveillance or flow analysis.

### 2. **Object Tracking**
- **Tracker Used:** ByteTrack (`bytetrack.yaml`)
- **Purpose:** Assigns a unique ID to each detected person and maintains identity across frames.
- **Why ByteTrack?** It is efficient, stable, and works well in crowded scenes.

### 3. **Counting Logic**
The system maintains a dictionary of object IDs with their previous Y-axis positions.
By comparing these positions with two predefined horizontal lines, it determines movement direction and updates counters.

---

## Line Coordinates (Defined Using PolygonZone)

| Line | Coordinates | Mean Y-value |
|------|--------------|--------------|
| **Top Line** | `[[1, 365], [1920, 365]]` | `top_y = 365` |
| **Bottom Line** | `[[1, 625], [1920, 625]]` | `bottom_y = 625` |

Both lines span the width of the video (1920 pixels), positioned at different vertical heights (Y = 365 and Y = 625).

---

## IN/OUT Logic Explanation

| Condition | Description | Count Updated | Visual Indicator |
|------------|--------------|----------------|------------------|
| **IN** | When a person moves **downward** (from above the top line) and crosses the **top line**. | `in_count += 1` | Green Box |
| **OUT** | When a person moves **upward** (from below the bottom line) and crosses the **bottom line**. | `out_count += 1` | Red Box |

**Implementation Insight:**
For each tracked person:
1. The system stores their previous vertical coordinate (`prev_y`).
2. If their direction indicates they crossed one of the lines, it marks them as “in” or “out” once.
3. Each person’s movement state is stored in a dictionary (`id_states`) to prevent double-counting.

---

## Heatmap Visualization

- **Purpose:** Highlights regions where people appeared most frequently.
- **Method:** For each detected person, the center point of their bounding box adds intensity to a 2D array.
- **Output:**
  - A **color heatmap** (`final_heatmap.jpg`) showing areas of dense movement.
  - A **heatmap video** (`heatmap_output.mp4`) showing real-time activity overlay.

---

## Setup (Google Colab)

1. Upload the video file here:
   ```bash
   /content/people-walking.mp4
2. Then Run all cells accordingly.
