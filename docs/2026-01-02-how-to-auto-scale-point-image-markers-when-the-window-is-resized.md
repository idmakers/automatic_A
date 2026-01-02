---
title: "How to Auto-Size Point Image Markers When Resizing in LightningChart Python"
tags:
  - lightningchart-python
  - chart-resizing
  - point-series-markers

## Core Problem

When using LightningChart Python to create a chart with multiple point-series markers that use custom images, the marker images do not scale correctly when the window is resized. This results in markers appearing too large or too small after resizing.

## Solution & Analysis

To solve this issue, we can utilize the `set_point_image_scale` method provided by LightningChart Python. Here's how to modify your existing code:

```python
import lightningchart as lc

# Create a new chart instance
chart = lc.ChartXY(theme=lc.Themes.Light, title='ChartXY', legend={'add_entries_automatically': False})

# ... (rest of the initialization code remains the same)

BASE_IMAGE_SIZE = 0.5

def add_marker(point, image_path):
    series = chart.add_point_series(colors=True, sizes=True)
    series.add(*point)
    # Set point image scale to maintain correct proportions
    series.set_point_image_scale(1 / BASE_IMAGE_SIZE)  # Scale down the image size
    series.set_point_image_style(source=image_path)
    return series

markers = [
    add_marker((10.000, 10.500), 'chest.png'),
    add_marker((10.315, 5.000), 'left_ankle.png'),
    add_marker((10.940, 11.355), 'left_arm.png'),
    add_marker((10.005, 13.100), 'right_arm.png'),
]

# Open the chart with live updates
chart.open(live=True)
```

By setting `series.set_point_image_scale(1 / BASE_IMAGE_SIZE)`, we ensure that the marker images are scaled down proportionally to maintain correct proportions relative to the chart size.

## Conclusion

To auto-size point image markers when resizing in LightningChart Python, you can utilize the `set_point_image_scale` method. This approach ensures that custom marker images scale correctly and maintain their aspect ratio, providing a more visually appealing chart experience.