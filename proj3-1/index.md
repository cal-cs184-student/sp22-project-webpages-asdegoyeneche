---
layout: default
title: Project 3.1 - Pathtracer
description: Ke Wang - Alfredo De Goyeneche
---

[Link to (this) Webpage](https://cal-cs184-student.github.io/sp22-project-webpages-asdegoyeneche/proj3-1/index.html)

[Link to Code](https://github.com/cal-cs184-student/p3-1-pathtracer-sp22-mr_graphics_3)


# Project 3-1 Pathtracer

#### Overview

Some overview


## Part I: Ray Generation and Scene Intersection



## Part II: Bounding Volume Hierarchy



## Part III: Direct Illumination









## Part IV: Global Illumination

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

m=0        |                               m=1                               
:-------------------------:|:---------------------------------------------------------------:
Bunny Results![Figure4_7](./Figures/bunny_m0_1024_Part4_4.png)   | Bunny Results![Figure4_8](./Figures/bunny_m1_1024_Part4_2.png)  |
m=2        |                               m=3                               
Bunny Results![Figure4_9](./Figures/bunny_m2_1024_Part4_4.png)   | Bunny Results![Figure4_10](./Figures/bunny_m3_1024_Part4_4.png) |
m=100      |                                                            
Bunny Results![Figure4_9](./Figures/bunny_m100_1024_Part4_4.png)   |

#### 4. Images rendered with different `sample-per-pixel` rates. (m=3)
s=1        |                                s=2                                
:-------------------------:|:-----------------------------------------------------------------:
Dragon Results![Figure4_10](./Figures/dragon_m3_l4_1_Part4_5.png)   | Dragon Results![Figure4_11](./Figures/dragon_m3_l4_2_Part4_3.png) |
s=4        |                                s=8                          
Dragon Results![Figure4_12](./Figures/dragon_m3_l4_4_Part4_3.png)   | Dragon Results![Figure4_13](./Figures/dragon_m3_l4_8_Part4_3.png) |
s=16      | s=64
Dragon Results![Figure4_14](./Figures/dragon_m3_l4_16_Part4_3.png) | Dragon Results![Figure4_15](./Figures/dragon_m3_l4_64_Part4_3.png) |
s=1024     |
Dragon Results![Figure4_16](./Figures/dragon_m3_l4_1024_Part4_5.png)|



## Part V: Adaptive Sampling

To implement the adaptive sampling, we computed the average `mu` and variance `sigma^2` every `samplesPerBatch` samples, and terminate the sampling when `I<=maxTolerance*mu`, where `I = 1.96* sqrt(sigma_2)/sqrt(actual_sample)`.
Our implementatin codes:
```asm
void PathTracer::raytrace_pixel(size_t x, size_t y) {
  int num_samples = ns_aa;          // total samples to evaluate
  Vector2D origin = Vector2D(x, y); // bottom left corner of the pixel
  Vector3D illum_average = Vector3D();
  double weight = 1/(float)num_samples;
  double s1 = 0;
  double s2 = 0;
  int actual_sample = 0;
  for (int i = 0; i< num_samples; i++)
  {
      if (i!=0 && (i % samplesPerBatch) ==0)
      {
          double mu = s1/double(actual_sample);
          double sigma_2 = (1/(double(actual_sample)-1))*((s2)-(s1*s1/(actual_sample)));
          double I = 1.96* sqrt(sigma_2)/sqrt(actual_sample);
          if (I<=maxTolerance*mu)
          {
              break;
          }
      }
      Vector2D xy_sub = gridSampler->get_sample();
      double x_image = (x+xy_sub.x)/sampleBuffer.w;
      double y_image = (y+xy_sub.y)/sampleBuffer.h;
      Ray ray_sample = camera->generate_ray(x_image,y_image);
      ray_sample.depth = max_ray_depth;
      Vector3D radiance = est_radiance_global_illumination(ray_sample);
      s1 += radiance.illum();
      s2 += (radiance.illum())*(radiance.illum());
      illum_average = illum_average + radiance;
      actual_sample++;

  }
  illum_average = illum_average/actual_sample;


  sampleBuffer.update_pixel(illum_average, x, y);
  sampleCountBuffer[x + y * sampleBuffer.w] = actual_sample;


}
```

### Results
Images rendered with adaptive sampling.

Rendered image        |                            Sampling rate                             
:-------------------------:|:--------------------------------------------------------------------:
Bunny Results![Figure4_17](./Figures/bunny_m5_2048_Part4_5.png)   | Bunny Results![Figure4_18](./Figures/bunny_m5_2048_Part4_5_rate.png) |

We can see clearly visible differences in sampling rate over various regions and pixels.