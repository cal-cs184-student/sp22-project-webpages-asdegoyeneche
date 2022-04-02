---
layout: default
title: Project 3.2 - Pathtracer II
description: Ke Wang - Alfredo De Goyeneche
---

[Link to (this) Webpage](https://cal-cs184-student.github.io/sp22-project-webpages-asdegoyeneche/proj3-2/index.html)

[Link to Code](https://github.com/cal-cs184-student/p3-2-pathtracer-sp22-mr_graphics_3_2)


# Project 3-2 Pathtracer II

#### Overview


Please grade parts 1 and 4.


## Part I: Mirror and Glass Materials (Graded)



| m = 0 |                      m=1         |
|:------------------:|:--------------:|
|   ![Spheres_Part1](./Figures/spheres_m0.png)    | ![Spheres_Part1](./Figures/spheres_m1.png) | 
|    m=2               |            m=3      |
|   ![Spheres_Part1](./Figures/spheres_m2.png)   | ![Spheres_Part1](./Figures/spheres_m3.png)  |
|    m=4               |            m=5      |
|   ![Spheres_Part1](./Figures/spheres_m4.png)   | ![Spheres_Part1](./Figures/spheres_m5.png)  |
|    m=100               |        |
|   ![Spheres_Part1](./Figures/spheres_m100.png)   | |



## Part II: Microfacet Material (Extra)

1. Scene `CBdragon_microfacet_au.dae` rendered with <img src="https://render.githubusercontent.com/render/math?math=\alpha"> set to 0.005, 0.05, 0.25 and 0.5. 128 samples per pixel and 4 samples per light.


| <img src="https://render.githubusercontent.com/render/math?math=\alpha"> = 0.005 |                      0.05                      |
|:--------------------------------------------------------------------------------:|:----------------------------------------------:|
|                 ![CBdragon_Part1](./Figures/cbdragon_0_005.png)                  | ![CBdragon_Part1](./Figures/cbdragon_0_05.png) | 
|                                       0.25                                       |                      0.5                       |
|                  ![CBdragon_Part1](./Figures/cbdragon_0_25.png)                  | ![CBdragon_Part1](./Figures/cbdragon_0_5.png)  |

As shown in the figures, <img src="https://render.githubusercontent.com/render/math?math=\alpha"> represents the roughness of the macro surface. Lower <img src="https://render.githubusercontent.com/render/math?math=\alpha"> corresponds to smoother surface (more like mirror), while higher <img src="https://render.githubusercontent.com/render/math?math=\alpha"> corresponds to rougher material (more like diffuse material).

2. Scene `CBbunny_microfacet_cu.dae` rendered using cosine hemisphere sampling (default) and importance sampling. Using 64 samples per pixel and 1 sample per light in each.

|        Default cosine hemisphere sampling        |                 Importance sampling                 |
|:------------------------------------------------:|:---------------------------------------------------:|
| ![CBdragon_Part1](./Figures/cbbunny_default.png) | ![CBdragon_Part1](./Figures/cbbunny_importance.png) | 

As shown in the figures, the importance sampling have much lower noise level and better rendered image quality. In contrast, since the default sampling strategy samples uniformly on the hemisphere, which results in a lower effective sampling rate and higher noise level (some of the sampling directions are not feasible for the material).  

3. For the `CBdragon_microfacet_au.dae` scene, we modified the material from Au (gold) to Fe (iron) and Mg (Magnesium).
   
    parameters for Fe: R-614nm: eta = 3.1700, k = 6.1200; G-549nm: eta = 2.9500, k = 2.9300; B-466nm: eta = 2.6500, k = 2.8075.

    parameters for Mg: R-614nm: eta = 0.37635, k = 5.6806; G-549nm: eta = 0.31405, k = 5.0262; B-466nm: eta = 0.23451, k = 4.1906.

|                         Fe                          |                         Mg                          |
|:---------------------------------------------------:|:---------------------------------------------------:|
| ![CBdragon_Part1](./Figures/cbdragon_new_fe_05.png) | ![CBdragon_Part1](./Figures/cbdragon_new_mg_05.png) | 

Look nice!!


## Task IV: Depth of Field (Graded)

| d = 4.3 |                      d=4.5         |
|:------------------:|:--------------:|
|   ![Dragon_Part4](./Figures/dragon_lens_d43.png)    | ![Dragon_Part4](./Figures/dragon_lens_d45.png) | 
|    d=4.7               |            d=4.9      |
|   ![Dragon_Part4](./Figures/dragon_lens_d47.png)   | ![Dragon_Part4](./Figures/dragon_lens_d49.png)  |



## Note on collaboration

We've been working together since the first project, as well as collaborating in research in our lab. For the CS284A course projects we've worked independently on the each task of the coding part of the assignment (in separate branches), and we would discuss issues / point out bugs / discuss alternative implementations. At the end we would either merge one of the two branches into master or combine parts of each branch. Now, for the write-up, we usually split the tasks. This has been working since we both have tight schedules and allows both of us to dig into the code (and learn in this process).

