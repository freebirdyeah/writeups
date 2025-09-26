# HTB Labs: AI-ML Challenges

## Uplink Artifact

### Description: 

> During an analysis of a compromised satellite uplink, a suspicious dataset was recovered. Intelligence indicates it may encode physical access credentials hidden within the spatial structure of Volnaya’s covert data infrastructure. 

### Files Given:

- uplink_spatial_auth.csv

### Method:

1. I did basic EDA of the `.csv` file, used `df.describe()` and checked the `corr` matrix. I noticed the `.csv` only has 4 labels from 0-3

2. I plotted all the points in a 3d scatter plot at once... but it looked messy and there was no sense of the data.

3. I plotted the 3D scatter plot for points belonging to each label one-at-a-time, the scatter plot of points with `label` as `1` when viewed from atop looked like a QR code, I tried scanning it but it didn't work.

4. I set `z=0` for all points with `label` as `1` by using the following script:

```python
import pandas as pd
import matplotlib.pyplot as plt
from mpl_toolkits.mplot3d import Axes3D

# Load dataset
df = pd.read_csv("uplink_spatial_auth.csv")

# Filter only label 1
df_label1 = df[df['label'] == 1].copy()

# Set all z-values to 0
df_label1['z'] = 0

# 3D Scatter plot
fig = plt.figure(figsize=(8,6))
ax = fig.add_subplot(111, projection='3d')

ax.scatter(df_label1['x'], df_label1['y'], df_label1['z'], c='green', s=50)

ax.set_xlabel('X')
ax.set_ylabel('Y')
ax.set_zlabel('Z (set to 0)')
ax.set_title('Label 1 Points with Z = 0')

plt.show()
```

5. The QR code was clear this time. Scanned it.

FLAG: `HTB{clu5t3r_k3y_l34k3d}` 

## Loyalty Survey

### Description:

>

### Files Given:


### Method:


