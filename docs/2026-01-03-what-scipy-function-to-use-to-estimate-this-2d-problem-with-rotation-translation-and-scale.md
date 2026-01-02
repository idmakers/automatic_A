---
title: "Estimating 2D Rotation, Translation, and Scale with Python's SciPy"
tags:
  - Least Squares Optimization
  - SciPy Functions
  - 2D Transformation Model

---

# Estimating 2D Rotation, Translation, and Scale with Python's SciPy

## Core Problem
Fixing a grid with known nodes (u,v) and observed coordinates (x,y) with sigma for each point using python. The model is x,y = f(u,v) : x = xl + u*c*cos(a) - v*c*sin(a), y = yl + u*c*sin(a) + v*c*cos(a). We want to estimate xl, yl, c and a, using python least squares.

## Solution & Analysis

We can use the `scipy.optimize.least_squares` function to solve this problem. This function is suitable for nonlinear least squares problems, where the objective function is not linear in the variables.

```python
import numpy as np
from scipy import optimize
import matplotlib.pyplot as plt

# Define the model
def func(params, u, v):
    xl, yl, c, a = params
    return (xl + u*c*np.cos(a) - v*c*np.sin(a) - x) / sigma, (yl + u*c*np.sin(a) + v*c*np.cos(a) - y) / sigma

# Define the initial parameters
params0 = [10000.0, 20000.0, 700.00, 3.000]

# Define the observations
x_obs = [-79055.6, -81032.2, -83007.5, -84980.8, -86964.5,
         -113710.4, -115682.6, -117657.9, -119632.2, -121610.9]
y_obs = [-108720.7, -122577.8, -136440.7, -150304.5, -164160.7,
         -103788.9, -117643.3, -131497.5, -145362.1, -159218.0]
sigma = np.array([1.0, 2.7, 0.5, 4.9, 7.2, 8.6, 4.5, 3.7, 2.6, 3.5])

# Create the design matrix
u, v = np.meshgrid(np.linspace(100, 150), np.linspace(200, 280))
design_matrix = np.array([xl + u*c*np.cos(a) - v*c*np.sin(a) for xl in [10000.0] * len(u)
                           for yl in [20000.0] * len(v) for c in [700.00] * len(u)
                           for a in [3.000] * len(v)])

# Define the function to minimize
def func_to_minimize(params):
    return np.sum((design_matrix[:, None, None] - x_obs[None, :, None]) / sigma[None, :, None])**2

# Perform least squares optimization
res = optimize.least_squares(func_to_minimize, params0)

# Print the results
print("Estimated parameters:", res.x)
print("Residuals:", res.fun)
```

## Conclusion
In this example, we used `scipy.optimize.least_squares` to estimate the parameters of a 2D transformation model. We defined the model as x,y = f(u,v) and created a design matrix for the observations. The function to minimize is the sum of the squared residuals. The results are printed out, including the estimated parameters and the residual sum.

<!-- ADSENSE_PLACEHOLDER -->