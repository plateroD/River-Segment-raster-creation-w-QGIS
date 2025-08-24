# River-Segment-raster-creation-w-QGIS
just some instructions 


# Creating a River Segment Raster in QGIS with WhiteboxTools

This workflow describes how to generate a **river segment raster** (required by WaTEM/SEDEM when `Output per river segment = 1`) using **QGIS with the WhiteboxTools plugin**.

---

## 📌 Prerequisites
- Breached DEM (to remove sinks)
- D8 Pointer raster (flow directions)
- D8 Flow Accumulation raster
- WhiteboxTools plugin installed in QGIS

---

## 🛠 Workflow Steps

### 1. Extract the Stream Network
- Tool: **`ExtractStreams`**
- **Input:** D8 Flow Accumulation raster  
- **Threshold:** Choose based on watershed size & DEM resolution  
  - Example: `1000 – 5000` upslope cells  
- **Output:** Binary stream raster (`1 = stream`, `0 = no stream`)

---

### 2. Generate River Segments
- Tool: **`StreamLink`**
- **Inputs:**  
  - Stream raster (from Step 1)  
  - D8 Pointer raster  
- **Output:** River segment raster  
  - Each segment between tributary junctions gets a **unique integer ID**  
  - Non-stream cells = `0`  
  - Output datatype: `Int16`

---

### 3. (Optional) Vectorize for Inspection
- Convert raster → vector (Polygonize in QGIS, then dissolve to lines)  
- Useful for checking if thresholds and segment splits look reasonable.

---

### 4. Save for WaTEM/SEDEM
- Export the **StreamLink raster** as `.rst` format  
- Ensure projection, extent, and resolution match other WaTEM/SEDEM inputs.

---

## ✅ Final Notes
- The **`StreamLink` tool** is the key step: it transforms a stream raster into a **river segment raster** with unique IDs per reach.  
- This raster is directly compatible with WaTEM/SEDEM’s `RiverSegment` input requirement.

---

## 🔗 Example Command-Line (WhiteboxTools CLI)
```bash
whitebox_tools -r=ExtractStreams -v \
  --d8_pntr="d8_pointer.tif" \
  --flow_accum="d8_flow_accum.tif" \
  --output="streams.tif" \
  --threshold=2000

whitebox_tools -r=StreamLink -v \
  --d8_pntr="d8_pointer.tif" \
  --streams="streams.tif" \
  --output="river_segments.tif"
