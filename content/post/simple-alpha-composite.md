---
title: "Simple Alpha Composite"
date: 2022-01-10T23:00:00+10:00
tags: [programming, python]
toc: false
draft: false
---

I recently had to do some transparent image layering in batches and wanted complete control over the outcome using code. Naturally, I turned to Python due to my familiarity. 🐍🐍🐍

Unfortunately, [Pillow](https://pillow.readthedocs.io/en/stable/) didn't seem to work well for images with an alpha channel, particularly when resizing and transposing.

Instead, I ended up using [scikit-image](https://scikit-image.org/) to load image data into [NumPy](https://numpy.org/) arrays and manipulate it from there.

This short snippet allowed me to achieve transparent image layering.

```python
def alpha_composite(im1, im2):
    h, w = im1.shape[:2]
    final = np.array([[[0.0] * 4] * w] * h)
    for x in range(w):
        for y in range(h):
            p1 = im1[x, y]
            p2 = im2[x, y]
            
            rgb1 = p1[0:3]
            rgb2 = p2[0:3]
            a1 = p1[3]
            a2 = p2[3]
            
            blend = a1 * (1 - a2)
            af = a2 + blend
            
            c2 = 0.0 if af == 0 else a2 / af
            c1 = 1 - c2
            
            final[x, y] = np.append(rgb1 * c1 + rgb2 * c2, af)
    
    final = np.clip(final, 0.0, 1.0)
    
    return final
```

I'm sure there's many efficiency improvements to be made and I cannot guarentee it's bug-free, especially on some edge case I didn't encounter.

###### Todo
* Test non-square dimensions
* Efficiency improvement
* Add typing
* Add comment explanations to operations