---
layout: default
title: Project 2 - Pathtracer
description: Ke Wang - Alfredo De Goyeneche
---

[Link to (this) Webpage](https://cal-cs184-student.github.io/sp22-project-webpages-asdegoyeneche/proj3-1/index.html)

[Link to Code](https://github.com/cal-cs184-student/p3-1-pathtracer-sp22-mr_graphics_3)

# Project 2 - Pathtracer

## Part 4:
### Implementation of the indirect light function
In this part, we implemented the algorithm to perform indirect light rendering. Recall from Part 3, where we successfully implemented `one_bounce_radiance` function. Here, we recursively call that function to estimate the higher bounces.
Specifically, assume we have the intersection for the first bounce, we can create a new ray starting from the intersection with a sampled input light direction. Then, we can recursively call the `one_bounce_radiance` on top of the new rays.
To save the computation, we also implemented Russian Roulette for unbiased random termination. 

Our codes:
```asm
Vector3D PathTracer::at_least_one_bounce_radiance(const Ray &r,
                                                  const Intersection &isect) {
  Matrix3x3 o2w;
  make_coord_space(o2w, isect.n);
  Matrix3x3 w2o = o2w.T();

  Vector3D hit_p = r.o + r.d * isect.t;
  Vector3D w_out = w2o * (-r.d);

  Vector3D L_out = one_bounce_radiance(r,isect);

  double termination = 0.4;

  if (r.depth == 1)
  {
      return L_out;
  }

  Vector3D wi;
  double pdf;

  Vector3D f = isect.bsdf->sample_f(w_out,&wi,&pdf);
  Vector3D wi_world = o2w * wi;
  Ray next_ray = Ray(hit_p,wi_world);
  next_ray.depth = r.depth - 1;
  next_ray.min_t = EPS_F;
  Intersection next_intersection;
  bool next_isec_bool = bvh->intersect(next_ray,&next_intersection);
  if (next_isec_bool)
  {
      if (r.depth == max_ray_depth)
      {
          L_out += at_least_one_bounce_radiance(next_ray,next_intersection)*f* cos_theta(wi)/pdf;
      }
      else if (coin_flip(1-termination))
      {
          L_out += at_least_one_bounce_radiance(next_ray,next_intersection)*f* cos_theta(wi)/(pdf*(1-termination));

      }
  }
  return L_out;
```

### Results
#### 1. Images rendered with global (direct and indirect) illumination (Using 1024 samples)

Direct illumination (m=1)          |                   Indirect illumination (m=5)                    
:-------------------------:|:----------------------------------------------------------------:
Bunny Results![Figure4_1](./Figures/bunny_m1_1024_Part4_2.png)   |  Bunny Results![Figure4_2](./Figures/bunny_m5_1024_Part4_2.png)  |  
Sphere Results![Figure4_3](./Figures/spheres_m1_1024_Part4_2.png)   | Bunny Results![Figure4_4](./Figures/spheres_m5_1024_Part4_2.png) |  

#### 2. Images rendered with only `direct` illumination and only `indirect` illumination (Using 1024 samples)

Only Direct illumination (m=1)          |                    Only Indirect illumination (m=3)                     
:-------------------------:|:-----------------------------------------------------------------------:
Bunny Results![Figure4_5](./Figures/bunny_m1_1024_Part4_2.png)   | Bunny Results![Figure4_6](./Figures/bunny_m3_indirect_1024_Part4_3.png) |

#### 3. Images rendered with different `max_ray_depth` (Using 1024 samples)

m=0        |                    m=1
:-------------------------:|:-----------------------------------------------------------------------:
Bunny Results![Figure4_5](./Figures/bunny_m1_1024_Part4_2.png)   | Bunny Results![Figure4_6](./Figures/bunny_m3_indirect_1024_Part4_3.png) |
