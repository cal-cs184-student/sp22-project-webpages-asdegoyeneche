---
layout: default
---

# Project 1 here


[Link to Code](https://github.com/cal-cs184-student/p1-rasterizer-sp22-mr_graphics)

## Task 4: Barycentric coordinates (10 pts)
**Solutions:** From our understanding, the barycentric coordinates of a certain point can be represented as a linear combination of reference points (a triangle for points in a plane).
More specifically, as shown in the figure below, suppose we have some specified values (e.g., texture, coordinates) at the vertices of a triangle: `(x_A,y_A);(x_B,y_B);(x_C,y_C)`. Then, the value of any point on the plane `(x,y)` can be written as a linear combination of the values at vertices. $x_a_b$

![Figure_4_1](Figures/Figure4_1.jpg)
Another example shows the Barcentric coords linearly interpolate colors at vertices (Figure borrowed from lecture slides):

![Figure_4_2](Figures/Figure4_2.jpg)

### Results:
Interpolated color wheel:
![Figure_4_3](Figures/Figure4_3.jpg)

## Task 5: "Pixel sampling" for texture mapping (15 pts)


