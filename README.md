This code is adopted from GeoFM288KC, Fall 2025. 

## Code Overview

This workflow:

Downloads Sentinel-2 L2A imagery via STAC (Microsoft Planetary Computer or EarthSearch).

Computes NDVI from Red (B04) and NIR (B08) bands.

Extracts image patches over the selected area (e.g., Buffalo, NY).

Uses a geo-foundational model (e.g., Prithvi, GeoFM, TorchGeo backbones) to embed the patches.

Aggregates embeddings across time into a structured NDVI time series.

Creates visualizations to support interpretation.

This enables downstream tasks such as seasonal change detection, vegetation anomaly analysis, or self-supervised representation learning.

## Repository Structure
ndvi_timeseries/
│
├── data/                    # Optional local cache for STAC assets
├── src/
│   ├── stac_utils.py        # STAC search & download helpers
│   ├── ndvi_compute.py      # NDVI computation
│   ├── patching.py          # Patch extraction
│   ├── model_inference.py   # Geo-Foundation model embeddings
│   ├── visualization.py     # Histograms, maps, time-series plots
│   └── bbox_config.py       # Bounding boxes for regions (e.g., Buffalo)
│
├── README.md                # ← you are here
└── environment.yml          # Required packages

## Region of Interest Example (Buffalo, NY)
bbox = [-78.90, 42.80, -78.78, 42.95]


You can swap this with any region in bbox_config.py.

## NDVI Computation

NDVI is computed as:

NDVI = (NIR - RED) / (NIR + RED)


Using Sentinel-2:

NIR = B08

RED = B04

Cloud filtering is optional (SCL mask recommended).

## Visualization Helpers

This project includes helper functions for exploring NDVI quality, patch-level variation, and temporal dynamics.

1. NDVI Histogram

Shows the distribution of vegetation values for a given date or patch.

Useful for:

QA/QC

vegetation intensity overview

detecting outliers (clouds, snow, water)

2. NDVI Time Series Plot

Plots aggregated NDVI across time (median, percentile, or model-derived feature).

Useful for:

phenology

recovery after disturbance

inter-year comparison

3. NDVI Map

A single-date NDVI composite for your region (tile or full bounding box).

Useful for:

spatial patterns (urban vegetation, agricultural fields)

selecting patches of interest

comparing seasons

## Patch Visualizations During Model Training

The pipeline includes:

Patch Preview Grids

Shows a grid of example patches (RGB or NDVI color ramp).

Helps detect:

cloud contamination

off-target areas (water, snow)

seasonal variation coverage

Embedding Intuition

Foundation model embeddings reflect:

texture patterns

seasonal cycles

land cover type

spatial context

You can add PCA or UMAP maps to visualize embedding clusters.

## Interpreting the Outputs
NDVI Histogram

Peak near 0.6–0.8 → healthy vegetation

Peak near 0–0.2 → urban / soil

Negative → water or snow

Time Series

Spring rise (April–June)

Summer plateau (July–August)

Fall decline (September–November)

Irregular dips often indicate cloud contamination.

Embedding Trends

Changes in embeddings across time can signal:

phenological shifts

disturbance events

year-over-year anomalies

## Requirements
rasterio
numpy
torch
torchvision
matplotlib
pystac-client
planetary-computer
pyproj
shapely


If using Prithvi or GeoFM:

pip install torchgeo

## Quickstart
from stac_utils import fetch_s2_collection
from ndvi_compute import compute_ndvi
from patching import extract_patches
from model_inference import embed_patches
from visualization import plot_ndvi_hist, plot_ndvi_timeseries, show_ndvi_map

 1. Search STAC
items = fetch_s2_collection(bbox)

 2. Compute NDVI
ndvi_list = compute_ndvi(items)

 3. Patch extraction
patches = extract_patches(ndvi_list, patch_size=256)

 4. Embeddings
emb = embed_patches(patches, model="prithvi")

 5. Visualization
plot_ndvi_timeseries(emb, ndvi_list)

## Notes

Supports Buffalo, New York State, or any global region with Sentinel-2 coverage.

Designed to be modular for fine-tuning or zero-shot embedding extraction.

Works for multiyear time series.
