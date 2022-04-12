---
layout: default
title: Project 4: Cloth Sim
description: Ke Wang - Alfredo De Goyeneche
---

[Link to (this) Webpage](https://cal-cs184-student.github.io/sp22-project-webpages-asdegoyeneche/proj4/index.html)

[Link to Code](https://github.com/cal-cs184-student/p4-clothsim-sp22-mr_graphics_4/tree/master)


## Part 4: Handling self-collisions
With self-collision, we can see the cloth folding on itself rather than clipping through it.

|     Early, initial self-collision     |                Intermediate state                 |
|:-------------------------------------:|:-------------------------------------------------:|
| ![Part4_1_1](./Figures/Part4_1_1.png) |       ![Part4_1_2](./Figures/Part4_1_2.png)       | 
|             Restful state             |              Entire Simulation (GIF)              |
| ![Part4_1_3](./Figures/Part4_1_3.png) | <img src="./Figures/Part4_1_gif.gif" width="440"> |

### How different `density` and `ks` affect the behavior
* First, we decrease the density to `1`, which indicates that the material become 15 times lighter.

|     Early, initial self-collision     |              Intermediate state               |
|:-------------------------------------:|:---------------------------------------------:|
| ![Part4_1_1](./Figures/Part4_2_1.png) |     ![Part4_1_2](./Figures/Part4_2_2.png)     |
|             Restful state             |            Entire Simulation (GIF)            |
| ![Part4_1_3](./Figures/Part4_2_3.png) | <img src="./Figures/Part4_2.gif" width="440"> |

As we can see from the results, the falling cloth has much less folding and smother surfaces. Since the mass is much smaller, given the same `ks`, the local deformation become smaller, therefore, the cloth will have less stretching and results in smother surface.

* Now, we increase the density to `100`, which indicates that the material become around 7 times denser or heavier.


|     Early, initial self-collision     |              Intermediate state               |
|:-------------------------------------:|:---------------------------------------------:|
| ![Part4_1_1](./Figures/Part4_3_1.png) |     ![Part4_1_2](./Figures/Part4_3_2.png)     |
|             Restful state             |            Entire Simulation (GIF)            |
| ![Part4_1_3](./Figures/Part4_3_3.png) | <img src="./Figures/Part4_3.gif" width="440"> |

As we can see from the results, the falling cloth has much more folding and rougher surfaces. Since the cloth is much heavier, given the same `ks`, the local deformation become bigger, therefore, the cloth will have much more stretching and results in rougher surface.
Due to the deformation, the cloth will have smaller surface area compared to the initial state.

* Now, we decrease `ks` to `50`, and set the density as `15`, with smaller `ks`, the springs will have larger deformations with the same force on it.


|     Early, initial self-collision     |              Intermediate state               |
|:-------------------------------------:|:---------------------------------------------:|
| ![Part4_1_1](./Figures/Part4_4_1.png) |     ![Part4_1_2](./Figures/Part4_4_2.png)     |
|             Restful state             |            Entire Simulation (GIF)            |
| ![Part4_1_3](./Figures/Part4_4_3.png) | <img src="./Figures/Part4_4.gif" width="440"> |

As we can see from the results, the falling cloth has much more folding and rougher surfaces, which is similar to the effect with higher density value. Since `ks` is smaller, given the same `density` value, the local deformation become bigger, therefore, the cloth will have much more stretching and results in rougher surface.
Due to the deformation, the cloth will have smaller surface area compared to the initial state.