# NTSEC-Based NDVI Shadow Correction in Google Earth Engine

## Overview

This project implements a **terrain-aware NDVI shadow correction workflow** in **Google Earth Engine (GEE)** using Sentinel-2 imagery.  
The goal is to reduce NDVI distortions caused by **terrain-induced shadows** in mountainous regions under **low-sun illumination conditions**.

The implementation follows the **core NTSEC concept**, but is realized **entirely as a deterministic GEE script**, without:
- machine learning models
- parameter training
- external simulation frameworks

All operations are explicitly defined using GEE image processing functions.

---

## Key Features

- Image-based shadow detection using a spectral Shadow Index (SI)
- Continuous shadow strength estimation (α-map)
- Explicit handling of **self-shadows** and **cast-shadows**
- Band-wise reflectance compensation (RED & NIR)
- NDVI correction under low-sun conditions
- Independent validation using high-sun Sentinel-2 imagery
- Export of corrected NDVI, difference maps, and statistical data

---

## Data Used

### Satellite Data
- **Sentinel-2 Level-2A (Surface Reflectance)**
  - Coastal
  - Green
  - Red
  - Near-Infrared (NIR)
  - Scene Classification Layer (SCL)

### Elevation Data
- **SRTM DEM**
  - Used only to derive slope and aspect
  - No terrain correction models are applied

---

## Methodology Summary

### 1. Data Pre-processing
- Filter Sentinel-2 images by area of interest and date
- Remove clouds and cloud shadows using SCL
- Scale reflectance to physical range [0, 1]
- Prepare required spectral bands

### 2. Base Image Selection
- Select a low-cloud Sentinel-2 image acquired under **low sun elevation**
- Extract scene-level solar zenith and azimuth angles

### 3. Illumination Geometry
- Compute pixel-wise illumination cosine using slope, aspect, and solar angles
- Use geometry only for classification and constraints (not division-based correction)

### 4. Shadow Detection
- Compute a spectral Shadow Index (SI)
- Derive an adaptive shadow threshold from image statistics
- Generate a continuous shadow strength parameter (α ∈ [0, 1])

### 5. Radiometric Compensation
- Estimate direct, diffuse, and adjacency irradiance terms
- Apply band-wise reflectance compensation using α
- Prevent overcorrection on steep terrain

### 6. NDVI Correction
- Compute original NDVI
- Add compensation terms to RED and NIR reflectance
- Compute corrected NDVI

### 7. Shadow Classification
- Separate shadowed pixels into:
  - Self-shadows
  - Cast-shadows
- Used for analysis and validation only

### 8. Validation
- Use a high-sun Sentinel-2 image as reference NDVI
- Compute Bias and RMSE:
  - Original NDVI vs reference
  - Corrected NDVI vs reference
- Evaluate improvement quantitatively

---

## Outputs

The script generates and/or exports:

- Original NDVI (low sun)
- Corrected NDVI (NTSEC-based)
- NDVI difference maps
- Shadow masks (sunlit, self-shadow, cast-shadow)
- Statistical summaries (mean, standard deviation)
- Validation metrics (Bias, RMSE)
- Scatter plot data (NDVI vs illumination geometry)

All exports are handled via the GEE Tasks tab.

---

## How to Run

1. Open **Google Earth Engine Code Editor**
2. Paste the provided JavaScript file
3. Set the Area of Interest (AOI)
4. Adjust date ranges if needed
5. Run the script
6. Execute export tasks from the **Tasks** panel

---

## Generalization

- The script is **location-agnostic**
- Works for any mountainous or rugged terrain
- No region-specific tuning required
- Thresholds are derived directly from image statistics

---

## Limitations

- Requires at least one low-cloud Sentinel-2 image
- Validation depends on availability of a high-sun reference image
- Designed for terrain-shadow correction (not cloud shadow correction)

---

## License

This project is intended for academic and research use.  
Please cite appropriately if used in publications or reports.

---

## Notes

- This implementation is **fully deterministic**
- No learned parameters
- No machine learning
- No external models
- Every step maps directly to GEE operations
