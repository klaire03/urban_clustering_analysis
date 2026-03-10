# 🗺️ UK & Spain City Clustering — Venue-Based K-Means Analysis

> Clustering UK and Spanish cities by their dominant venue profiles using the Foursquare Places API, K-Means, and Folium interactive maps.

---

## 📌 Project Overview

This project applies **unsupervised machine learning** to answer a geographic and cultural question: do UK and Spanish cities cluster into distinct groups based on the types of venues found within them?

Using the **Foursquare Places API**, venue data is collected for cities across the UK and Spain within a 5 km radius of each city centre. Venue categories (e.g. Restaurant, Gym, Museum, River) are one-hot encoded and aggregated by city. **K-Means clustering (k=8)** is then applied to group cities by their venue frequency profiles. Results are visualised on interactive **Folium maps** — one showing the full cluster geography, and a second filtered to cities with a specific venue type (River).

---

## 🧪 Research Questions

- Do UK and Spanish cities form distinct geographic clusters, or do they mix across borders?
- What venue types define each cluster — and what does that reveal about a city's character?
- Are there cities that punch above their weight in a specific category (e.g. cultural venues, outdoor spaces)?

---

## 🗂️ Data Sources

| Source | Description |
|--------|-------------|
| `UK and Spain.csv` | Input file with city names, countries, latitudes and longitudes |
| Foursquare Places API | Venue data — up to 100 venues per city, 5 km radius |

**API parameters:**
- `radius = 5000` (metres)
- `LIMIT = 100` (venues per city)
- `VERSION = 20180604`

---

## 🔧 Pipeline Overview

```
UK and Spain.csv
       │
       ▼
Foursquare API ──── getVenues() ──── venues DataFrame
       │
       ▼
One-Hot Encode Venue Categories (pd.get_dummies)
       │
       ▼
Group by City → Mean Frequency per Category
       │
       ▼
Top 10 Most Common Venues per City
       │
       ▼
K-Means Clustering (k=8, random_state=1)
       │
       ├── Map 1: All cities, coloured by cluster (rainbow palette)
       └── Map 2: Filtered to cities with River venues
```

---

## 📊 Key Steps

### 1. Data Collection
Cities loaded from CSV with coordinates. Foursquare API called via `getVenues()` — returns venue name, category, and coordinates for up to 100 venues per city within 5 km.

### 2. Feature Engineering
Venue categories are one-hot encoded with `pd.get_dummies`. Rows are grouped by city and the **mean frequency** of each venue category is computed — giving each city a profile vector representing its typical venue mix.

### 3. Top Venues
A helper function `return_most_common_venues()` sorts each city's feature vector in descending order, extracting the top 10 venue types. This produces a human-readable ranking alongside the numeric cluster assignments.

### 4. K-Means Clustering
Scikit-learn `KMeans(n_clusters=8, random_state=1)` is fit to the city venue profiles. `random_state=1` ensures reproducibility. Cluster labels are joined back to the cities DataFrame.

### 5. Visualisation
Two Folium maps are generated:
- **Full cluster map**: all cities plotted with circle markers coloured by cluster using a `cm.rainbow` palette
- **River map**: filtered to cities where the `River` venue category has a non-zero frequency — showing which cities have riverfront activity recorded in Foursquare data

---

## 📁 File Structure

```
ClusteringProject.ipynb     ← Main notebook
UK and Spain.csv            ← Input city data (city, country, lat, lng)
```

---

## 🚀 Setup & Usage

### Prerequisites

```bash
pip install geopy folium requests numpy pandas scikit-learn matplotlib
```

### API Credentials

Replace the placeholder credentials in Cell 1 with your own Foursquare API keys:

```python
CLIENT_ID = 'your_client_id_here'
CLIENT_SECRET = 'your_client_secret_here'
VERSION = '20180604'
```

> ⚠️ **Note:** The Foursquare Places API (v2 legacy) used in this project has been deprecated. The included notebook reflects the original implementation. For new runs, migrate to the [Foursquare Places API v3](https://developer.foursquare.com/docs/places-api/).

### Run

Open `ClusteringProject.ipynb` in Jupyter or Google Colab and run all cells in order.

---

## 🛠️ Tech Stack

| Tool | Purpose |
|------|---------|
| Python 3 | Core language |
| pandas | Data manipulation |
| numpy | Vectorised operations |
| scikit-learn | K-Means clustering |
| requests | Foursquare API calls |
| folium | Interactive map visualisation |
| matplotlib | Colour palette generation |
| geopy | Geographic utilities |
| Google Colab | Development environment |

---

## 📌 Design Decisions

**Why k=8?** Eight clusters were chosen to allow sufficient granularity across two countries with different urban profiles, while keeping clusters interpretable. No elbow method is documented in the notebook — a dendrogram or inertia plot would be a natural extension.

**Why mean frequency?** Grouping by city and taking the mean of one-hot encoded venue categories gives each city a fractional venue profile (e.g. "15% of this city's venues are Restaurants"). This normalises for cities with different total venue counts.

**Why Folium?** Folium renders Leaflet.js interactive maps directly in Jupyter/Colab notebooks — no separate visualisation tool needed.

---

## ⚠️ Known Limitations

- **Foursquare API v2 is deprecated** — credentials no longer generate live results without migration to v3
- **k=8 is not data-derived** — no elbow/silhouette analysis is performed to justify cluster count
- **Venue data density varies** — major cities return close to 100 venues; smaller towns may return far fewer, biasing their profiles
- **5 km radius is fixed** — this favours compact city centres over sprawling metropolitan areas

---

Data Science Portfolio Project  
Tools: Python · scikit-learn · Foursquare API · Folium · Google Colab
