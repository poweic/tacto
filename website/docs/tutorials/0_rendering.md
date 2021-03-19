---
id: renderer
title: Rendering Touch Readings
---

This tutorial shows how to use the rendering engine in Tacto.
We will first generate a depth map that looks like a ball touching the sensor, and then pass it to `tacto.Renderer` for rendering the touch readings.

You can try this example yourself by running `python examples/demo_renderer.py`.

## Generating a Depth Map

To generate the depth map we want, we can simply use `numpy` to create a 2D array that has a Gaussian blob at the center of it. This can be done through `scipy.signal.convolve2d`.

```python
import numpy as np
from scipy import signal

def generate_ball(xyz):
    N = 100
    M = 150

    x = np.linspace(-1, 1, N)
    y = np.linspace(-1 / N * M, 1 / N * M, M)
    xv, yv = np.meshgrid(x, y)

    # Generate ball contact
    R = 0.25
    r = np.minimum(((xv - xyz[0]) ** 2 + (yv - xyz[1]) ** 2) ** 0.5, R)
    depthmap = (R ** 2 - r ** 2) ** 0.5

    # Smooth the surface
    kern = np.ones([5, 5])
    kern /= kern.sum()
    depthmap = signal.convolve2d(depthmap, kern, boundary="symm", mode="same")

    return depthmap
```

## Rendering Touch Readings

Now, let's create the rendering engine:
```
import tacto

renderer = tacto.Renderer(
    width=240,
    height=320,
    background=None,
    config_path=tacto.get_digit_config_path(),
)
```

Once having both the depth map and the renderer, we can simply call `renderer.render_from_depth` to render a RGB color image and a depth image.
```python
depthmap = generate_ball([0, 0])
color, depth = renderer.render_from_depth(depthmap, scale=0.01)
```

## Visualizing the Results

Now, let's visualize the touch readings (`color`) with the help of `matplotlib`.

```python
import matplotlib.pyplot as plt

# Plot the imprints
plt.subplot(121)
plt.imshow(color)
plt.subplot(122)
plt.imshow(0.022 - depth, cmap="gray")
plt.show()
```

Here is the final result:

![demo_renderer_rgb](/img/basic_rendering.png)
