---
layout: post
title:  "Bounding Cascade Cloud Generation"
date:   2019-01-16
---

In the past working as an intern, I got to work some with cloud generation and atmospheric radiation and I thought it was really cool! Now, I will show parts of a really simple method to generate heterogenous clouds from this paper:

    Cahalan, Robert. (1994). Bounded cascade clouds: Albedo and effective thickness. Nonlinear Processes in Geophysics. 1. 10.5194/npg-1-156-1994.

I'm not too much in the context of cloud physics so I am not sure how to interpret parts of the paper, but I reproduce below the basics of the **bouding cascade** fractal-based cloud generation method they present.


```python
import random

import matplotlib.pyplot as plt
import numpy as np

%matplotlib inline
%config InlineBackend.figure_format = 'svg'
```

We start with a uniform cloud having a liquid water path of eg. W = 100 g/m^2. We then recursively subdivide and distribute that 100 g/m^2 of water until a certain recursion depth.


```python
def distribute(A, B, fraction):
    """Distribute from A to B."""
    shift_amount = A * fraction

    A -= shift_amount
    B += shift_amount

    return A, B
```

## 1-D Bounding Cascade Cloud Generation

For the 1-D case, imagine we have an even line of water. Then, we cut the line in half and distribute some fraction of the water from one half to another half by flipping a coin. Next, in each of the newly-created halves, we half each of those halves and distribute some smaller fraction of water from one to the other.

![png](/assets/bounding_cascade_cloud/flow.png)

This can go on for any number of steps, so the user needs to specify a recursion depth.


```python
def cascade_1d_recursive(water_content, fraction, max_iter, i=0, decay_rate=0.8):
    # First, initialize left and right by splitting W evenly
    left, right = water_content / 2, water_content / 2

    if np.random.uniform() > 0.5:
        # Distribute from left to right
        left, right = distribute(left, right, fraction)
    else:
        # Distribute from right to left
        right, left = distribute(right, left, fraction)

    if i >= max_iter:
        return left, right

    return (
        cascade_1d_recursive(left, fraction * decay_rate, max_iter, i + 1),
        cascade_1d_recursive(right, fraction * decay_rate, max_iter, i + 1),
    )

def cascade_1d(water_content, fraction, max_iter, decay_rate=0.8):
    nested = cascade_1d_recursive(
        water_content,
        fraction,
        max_iter,
        decay_rate=decay_rate,
    )
    return np.array(nested).flatten()
```


```python
water_content_dist = cascade_1d(water_content=100, fraction=0.5, max_iter=5)

plt.figure(figsize=(10, 10))
plt.title('1-D Bounding Cascade Generated Cloud')
plt.imshow(np.expand_dims(water_content_dist, 0), cmap='plasma')
plt.axis('off')
plt.show()
```


![svg](/assets/bounding_cascade_cloud/output_6_0.svg)


## 2-D Bounding Cascade Cloud Generation

The 2-D case works just like the 1-D case, but instead of distributing water between a single pair at each step, we distribute water between two pairs (a quadrant) at each step.


```python
def cascade_2d(water_content, fraction, max_iter, i=0, decay_rate=0.8):
    # First, initialize by splitting W evenly
    top_left, top_right, bottom_left, bottom_right = [water_content / 4] * 4

    mode = random.choice(['top-down', 'left-right', 'diagonal'])

    if mode == 'left-right':
        # Appologies for the eggregious DRY violation!
        if np.random.uniform() > 0.5:
            top_left, top_right = distribute(top_left, top_right, fraction)
        else:
            top_right, top_left = distribute(top_right, top_left, fraction)

        if np.random.uniform() > 0.5:
            bottom_left, bottom_right = distribute(bottom_left, bottom_right, fraction)
        else:
            bottom_right, bottom_left = distribute(bottom_right, bottom_left, fraction)

    elif mode == 'top-down':
        if np.random.uniform() > 0.5:
            top_left, bottom_left = distribute(top_left, bottom_left, fraction)
        else:
            bottom_left, top_left = distribute(bottom_left, top_left, fraction)

        if np.random.uniform() > 0.5:
            top_right, bottom_right = distribute(top_right, bottom_right, fraction)
        else:
            bottom_right, top_right = distribute(bottom_right, top_right, fraction)

    elif mode == 'diagonal':
        if np.random.uniform() > 0.5:
            top_left, bottom_right = distribute(top_left, bottom_right, fraction)
        else:
            bottom_right, top_left = distribute(bottom_right, top_left, fraction)

        if np.random.uniform() > 0.5:
            top_right, bottom_left = distribute(top_right, bottom_left, fraction)
        else:
            bottom_left, top_right = distribute(bottom_left, top_right, fraction)

    if i >= max_iter:
        return np.array([[top_left, top_right], [bottom_left, bottom_right]])

    top = np.concatenate([
        cascade_2d(top_left, fraction * decay_rate, max_iter, i + 1),
        cascade_2d(top_right, fraction * decay_rate, max_iter, i + 1),
    ], axis=1)

    bottom = np.concatenate([
        cascade_2d(bottom_left, fraction * decay_rate, max_iter, i + 1),
        cascade_2d(bottom_right, fraction * decay_rate, max_iter, i + 1),
    ], axis=1)

    return np.concatenate([top, bottom], axis=0)
```


```python
water_content = 100
water_content_dist = cascade_2d(water_content=water_content, fraction=0.5, max_iter=6)
assert np.isclose(water_content_dist.sum(), water_content)

plt.figure(figsize=(7,7))
plt.title('2-D Bounding Cascade Generated Cloud')
plt.imshow(water_content_dist, cmap='plasma')
plt.axis('off')
plt.show()
```


![svg](/assets/bounding_cascade_cloud/output_9_0.svg)


It's very cool that you can see the fractal structure of the generated cloud by the "box-yness".

Obviously the clouds we see in the sky are not the water content values but instead we see the scattered photons. As far as I understand from the paper though, they directly correlate the water content per pixel with the optical properties of the cloud.

## Future steps

In the future, I'd be interested in two parts:
- Understand the math of what is happening in the paper better
- Figure out how I can do a simple Monte Carlo photon scattering simulation to see what this cloud would actually look like
