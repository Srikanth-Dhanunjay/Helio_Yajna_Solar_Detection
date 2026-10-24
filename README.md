# ☀️ Helio Yajna — AI-Based Rooftop Solar Detection

**Hackathon Submission | Team HELIO_YAJNA**

A Dockerized, CPU-only computer vision pipeline that detects rooftop solar panels from satellite imagery using instance segmentation, governance-aware buffer logic, and fallback inference strategies.

## 🚀 Highlights

- **Computer Vision:** YOLO-based solar panel instance segmentation.
- **Satellite Imagery:** Google Maps Static API integration.
- **Governance Logic:** Checks 1,200 sq ft first and 2,400 sq ft when required.
- **Fallback Inference:** Image enhancement and SAHI slicing.
- **Dockerized Execution:** Package and run the application in a consistent environment.
- **Structured Outputs:** Annotated images and JSON predictions for each site.

## 🏗️ How It Works

```text
Excel Input (Site Coordinates)
            |
            v
Fetch Satellite Imagery
            |
            v
YOLO-Based Instance Segmentation
            |
            v
Governance-Aware Buffer Evaluation
            |
            v
Fallback: Image Enhancement / SAHI
            |
            v
Solar Detection Decision
            |
            v
Annotated Images + JSON Predictions
```

## 📥 Input Data

Provide an Excel file at `input_data/input.xlsx` containing the following columns:

| Column | Description |
|---|---|
| `sample_id` | Unique site identifier |
| `latitude` | Latitude in WGS84 coordinates |
| `longitude` | Longitude in WGS84 coordinates |

## 📤 Output

### 1. Annotated Satellite Images

Output directory:

`output_data/artefacts/test/`

Each site produces an annotated image named `<sample_id>_overlay.jpg`.

- 🟢 Green: Panels inside the buffer
- 🔴 Red: Panels outside the buffer
- ⭕ Buffer circles: Visual reference for interpreting detections

### 2. JSON Predictions

Output directory:

`output_data/prediction_files/test/`

Each site produces a JSON file named `<sample_id>.json`, containing prediction details such as:

- Site identifier and coordinates
- Solar detection decision
- Confidence score
- Buffer information
- Estimated solar panel area
- Quality-control status
- Image metadata and inference mode

Example structure:

```json
{
  "sample_id": 1234,
  "lat": 12.9716,
  "lon": 77.5946,
  "has_solar": true,
  "confidence": 0.92,
  "buffer_radius_sqft": 1200,
  "pv_area_sqm_est": 23.5,
  "euclidean_distance_m_est": 0,
  "qc_status": "VERIFIABLE",
  "bbox_or_mask": "mask",
  "image_metadata": {
    "source": "Google Static Maps",
    "zoom": 20,
    "inference_mode": "PRIMARY"
  }
}
```

*Example only; actual values and fields depend on the pipeline output.*

## 🛠️ Prerequisites

- Docker Desktop or a compatible Docker Engine
- A valid Google Maps Static API key
- Google Maps Static API enabled in Google Cloud Console
- Input Excel file
- Required model weights and application configuration

## ⚙️ Configuration

Create a `.env` file in the project root:

```dotenv
MODEL_PATH=trained_model/weights.pt
INPUT_FILE=/data/input.xlsx
OUTPUT_DIR=/app/output_data
ZOOM_LEVEL=20
GOOGLE_API_KEY=YOUR_GOOGLE_MAPS_API_KEY
```

**Security notes:**
- Replace the placeholder with your own API key.
- Do not commit `.env` to GitHub or publish credentials in a Docker image.
- Keep your API key private and configure appropriate API restrictions.
- Verify that the trained model is included in the image or available at the configured path.

## 🐳 Run Using Docker

### Option A — Pull the published image

Pull the image from Docker Hub:

```bash
docker pull srikanthdhanunjay/helio-yajna-solar-detection:v1
```

Ensure `input_data/input.xlsx` and `.env` exist in your project directory.

**Windows PowerShell:**

```powershell
docker run --rm --env-file .env `
  -v "${PWD}/input_data:/data" `
  -v "${PWD}/output_data:/app/output_data" `
  srikanthdhanunjay/helio-yajna-solar-detection:v1
```

**Linux / macOS:**

```bash
docker run --rm --env-file .env \
  -v "$(pwd)/input_data:/data" \
  -v "$(pwd)/output_data:/app/output_data" \
  srikanthdhanunjay/helio-yajna-solar-detection:v1
```

### Option B — Build and run locally

Clone the repository:

```bash
git clone https://github.com/Srikanth-Dhanunjay/Helio_Yajna_Solar_Detection.git
cd Helio_Yajna_Solar_Detection
```

Build the Docker image:

```bash
docker build -t helio-yajna-solar-detection .
```

Run the pipeline:

```powershell
docker run --rm --env-file .env `
  -v "${PWD}/input_data:/data" `
  -v "${PWD}/output_data:/app/output_data" `
  helio-yajna-solar-detection
```

## 🧠 Key Technical Concepts

| Concept | Role in the project |
|---|---|
| Docker | Packages the application and its dependencies |
| YOLO instance segmentation | Identifies solar panels and their segmented regions |
| Google Maps Static API | Retrieves satellite imagery for site coordinates |
| SAHI | Enables sliced inference for difficult detection cases |
| Image enhancement | Provides an additional inference fallback |
| Volume mounts | Connect local input and output folders to the container |
| Environment variables | Configure paths, zoom level, and API credentials |

## 👨‍💻 My Hands-On Work

As a member of Team HELIO_YAJNA, I practiced running the project locally and worked through the Docker workflow.

- Built a Docker image from the project's Dockerfile.
- Resolved a Docker Desktop engine connection issue.
- Executed the container successfully.
- Practiced configuring environment variables and host-to-container volume mounts.
- Published a Docker image for easier distribution.

*The project was developed collaboratively by Team HELIO_YAJNA. Contributions should be attributed according to each team member's actual work.*

## 🐛 Troubleshooting

**Google Maps API error (403)**

- Check that the Static Maps API is enabled.
- Verify the API key and billing/API restrictions.
- Ensure the key is configured correctly in `.env`.

**Input file not found**

- Confirm that `input_data/input.xlsx` exists.
- Verify that the input volume is mounted to `/data`.
- Check that the configured input path is `/data/input.xlsx`.

**Docker engine connection error**

- Start Docker Desktop.
- Wait for the engine to finish starting.
- Run `docker info` to check connectivity.

**Docker build takes a long time**

- The first build may download the base image and install machine-learning dependencies.
- Subsequent builds can benefit from Docker's build cache.

**Model weights not found**

- Verify that the trained weights are included in the image or mounted at the expected path.
- Check `MODEL_PATH` against the application's configuration.

## 📌 Project Status

The Docker image has been built and the container has been run locally. The published image should be tested from a clean environment before being presented as a reproducible deployment.

---

**Team:** HELIO_YAJNA  
**Project:** AI-Based Rooftop Solar Detection  
**Technologies:** Python · Computer Vision · YOLO · SAHI · Docker · Google Maps Static API
