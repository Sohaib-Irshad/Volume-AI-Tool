# Volume AI Tool

A Python CLI tool that uses a **Random Forest classifier** to recommend the optimal coordinate system (rectangular, cylindrical, or spherical) for triple integral volume problems — with integral verification, volume estimation, and 3D visualization.

---

## What It Does

Given a plain-text description of a 3D region (e.g., *"a sphere with radius 4"* or *"cone with base radius 3 and height 5"*), the tool:

1. Extracts geometric features using regex and keyword matching
2. Runs the features through a trained Random Forest model
3. Returns the recommended coordinate system with a confidence score
4. Optionally verifies your integral setup, estimates volume, or renders a 3D plot

---

## Demo

```
📦 Volume AI Tool
✅ Model trained! Accuracy: 0.87

What would you like to do?
1. Suggest coordinate system
2. Verify integral setup
3. Estimate volume
4. Visualize region
5. Exit
Enter choice (1-5): 1

Enter region description: solid bounded above by x^2 + y^2 + z^2 = 9 and below by the cone
🔵 Suggested: spherical (Confidence: 0.85)
For computing the volume of this region, spherical coordinates are recommended (confidence: 0.85)
```

---

## Project Structure

```
volume-ai-tool/
├── coord_trans.py                 # All logic: ML, feature extraction, CLI menu
├── expanded_volume_dataset.json   # Training data (region descriptions + labels)
├── requirements.txt               # pip dependencies
└── README.md
```

---

## Installation

**Requirements:** Python 3.8+

```bash
git clone https://github.com/<your-username>/volume-ai-tool.git
cd volume-ai-tool

# Optional but recommended
python -m venv venv
source venv/bin/activate        # Windows: venv\Scripts\activate

pip install -r requirements.txt
```

---

## Usage

```bash
python coord_trans.py
```

### Menu Options

| Option | What it does |
|--------|-------------|
| 1 | Input a region description → get recommended coordinate system + confidence |
| 2 | Input description + your chosen system + integral text → get validation |
| 3 | Input shape + dimensions in plain text → get estimated volume |
| 4 | Input a shape name → opens a 3D Matplotlib plot |
| 5 | Exit |

**Volume estimation examples:**
```
cone radius 3 height 5      → 47.1239
sphere with radius 4        → 268.0825
cylinder radius 2 z from 0 to 6  → 75.3982
```

---

## How It Works

### Feature Extraction
Each description is converted into 8 boolean features:

| Feature | Detected by |
|---------|------------|
| `sphere` | keyword "sphere" or "hemisphere" |
| `cylinder` | keyword "cylinder" |
| `cone` | keyword "cone" |
| `paraboloid` | keyword "paraboloid" |
| `ellipsoid` | keyword "ellipsoid" |
| `x^2+y^2` | regex pattern |
| `x^2+y^2+z^2` | regex pattern |
| `bounded_z` | phrase "bounded between z" |

### ML Model
- **Algorithm:** Random Forest (100 trees, `random_state=42`)
- **Split:** 80% train / 20% test
- **Labels:** `rectangular`, `cylindrical`, `spherical`
- Confidence is taken from `predict_proba()` — the max class probability

### Collision / Integral Verification
Checks if your stated coordinate system matches the model's suggestion, then applies basic sanity rules (e.g., cylindrical integrals must contain `r`; spherical must contain `ρ²sin(ϕ)`).

### 3D Visualization
Renders spheres (skyblue), cylinders (lightgreen), and cones (salmon) as parametric surfaces using Matplotlib's 3D toolkit. Uses default dimensions for display.

---

## Dataset Format

`expanded_volume_dataset.json` — each entry:

```json
{
  "desc": "Cone with base radius 2 and height 6",
  "coord": "cylindrical",
  "integral": "r from 0 to 2, θ from 0 to 2π, z from 0 to 6 - r, integrand = r",
  "volume": 58.6431
}
```

---

## Dependencies

```
numpy
pandas
scikit-learn
matplotlib
```

Install with: `pip install -r requirements.txt`

---

## Known Limitations

- Volume estimation only supports spheres, cones, and cylinders with explicit numeric dimensions
- Visualization uses fixed default sizes regardless of input
- Integral verification is heuristic, not symbolic
- The classifier is keyword-based — complex symbolic descriptions may confuse it

---

## License

Free for educational use.