---
layout: default
title: Project 2 - MeshEdit
description: Ke Wang - Alfredo De Goyeneche
---

[Link to (this) Webpage](https://cal-cs184-student.github.io/sp22-project-webpages-asdegoyeneche/proj2/index.html)

[Link to Code](https://github.com/cal-cs184-student/p2-meshedit-sp22-mr_graphics_2)


# Project 2 - MeshEdit

## Section I: Bezier Curves and Surfaces

### Part 1: Bezier Curves with 1D de Casteljau Subdivision (10 pts)

#### Answers

De Casteljau's algorithm is a recursive method to obtain points on the Bezier curve. On each recursive step we start with N control points (p_1, ..., p_n), and we obtain (N-1) intermediate control points given a parameter `t` (in 0-1 range) in the next subdivision level (p'_1, ..., p'_{n-1}). We apply this recursively until we reach a final single point that lies on the Bezier curve for the given `t` value. At each method call, we find the intermediate control points by interpolating the original control points using the parameter t:

`p'_i = lerp(p_i, p_{i+1}, t) = (1-t)*p_i + t*p_{i+1}`.

With this, at each level of evaluation we will obtain the new points in positions between the previous level points, converging to a single point at the end. Then, if we modify the `t` parameter from 0 to 1 we will be able to traverse over the Bezier curve from start to end. While doing this, every intermediate point is also adjusting its relative position with respect to its 'parent' points proportionally to `t`.

Our implementation follows the described algorithm, where we wrote the method used on each recursive call. This methods takes N points and return N-1 intermediate points:

```
  std::vector<Vector2D> BezierCurve::evaluateStep(std::vector<Vector2D> const &points) {

    // I compared (timed) approaches initialize with size vs push_back, and initializing is about twice as fast.
    std::vector<Vector2D> output = std::vector<Vector2D>(points.size() - 1);
    for (int i = 0; i < points.size() - 1; ++i) {
      output[i] = points[i] * (1 - t) + points[i + 1] * t;
    }
    return output;
  }
```

This method is called recursively on the obtained points until we reach a single point that is returned. We explain more about the outer loop implementation in Part 2, which was implemented by us in that part.

#### Results

Step 0           |  Step 1       
:-------------------------:|:-------------------------:
![Figure1_1](./Figures/Figure1_1.png)   |  ![Figure1_2](./Figures/Figure1_2.png) |  
Step 2          | Step 3          
![Figure1_3](./Figures/Figure1_3.png) | ![Figure1_4](./Figures/Figure1_4.png)  
 Step 4         |   Step 5
 ![Figure1_5](./Figures/Figure1_5.png) |  ![Figure1_6](./Figures/Figure1_6.png)
 Bezier curve         | 
![Figure1_7](./Figures/Figure1_7.png)   | 
  Move Control Points        |   Change `t` parameter
 ![Figure1_8](./Figures/Figure1_8.png) |  ![Figure1_9](./Figures/Figure1_9.png)



### Part 2: Bezier Surfaces with Separable 1D de Casteljau (15 pts)


Teapot.bez with wireframe       |  Teapot.bez no wireframe        
:-------------------------:|:-------------------------:
![Figure2_2](./Figures/Figure2_1.png)   |  ![Figure2_1](./Figures/Figure2_2.png) 


## Section II: Triangle Meshes and Half-Edge Data Structure



### Part 3: Area-Weighted Vertex Normals (10 pts)


Without vertex normals         |   With vertex normals
:-------------------------:|:-------------------------:
 ![Figure3_1](./Figures/Figure3_1.png) |  ![Figure1_3](./Figures/Figure3_2.png)
 ![Figure3_3](./Figures/Figure3_3.png) |  ![Figure1_6](./Figures/Figure3_4.png)



### Part 4: Edge Flip (15 pts)

|  Before flips         |   After flips
:-------------------------:|:-------------------------:
![Figure4_1](./Figures/Figure4_1.png) |  ![Figure4_2](./Figures/Figure4_2.png)




### Part 5: Edge Split (15 pts)

#### Answers
Steps to implement the edge split operation:
1. Given a certain `edge` variable, we first assign all the halfedges, vertices, faces to the corresponding data structures.

Example code (one example for each data structure):
```asm
HalfedgeIter h_0 = e0->halfedge();
VertexIter v_0 = h_0->vertex();
FaceIter f_0 = h_0->face();
```
2. Secondly, we create the midpoint (Vertex structure) in the center of the original edge. Meanwhile, we create new halfedges, edges, faces which are added alongside the midpoint.
3. Finally, we assign the neighbors for all halfedges, edges, faces. This part needs a lot debug!!!

Example code (one example for each data structure):
```asm
h_0->setNeighbors(h_1,h_3,midpoint,e0,f_0);
h_1->setNeighbors(h_2m,h_1->twin(),v_1,h_1->edge(),f_0);
e0->halfedge()=h_0;
f_0->halfedge()=h_0;
``` 

During the implementation, we notice that we have to be extremely careful when setting the new neighbors. One trick is to write down all the halfedges/edges and give a clear and systematic naming system, since we created a lot halfedges/edges.
For our implementation, we assigned a new halfedge to the original `e0.halfedge()`, we found that actually the direction of the halfedge can be assigned in either way, we just have to be consistent and follow the rules.

#### Results
* Before & After split
![Figure_5_1](./Figures/Figure5_1.jpg)
* Before & After split & After flip
![Figure_5_2](./Figures/Figure5_2.jpg)

My main debug experience is mainly on how to accurately set the halfedges. One trick we found useful is to name everything in a clear way, so that we can track which edges/halfedges are wrong.

### Part 6: Loop Subdivision for Mesh Upsampling (25 pts)
#### Answers
Steps to implement the loop subdivision.
1. We first loop through all the edges and assign the new position (interpolated by the neighbor vertices) to the edge, this will be used as the position for the generated midpoint during the edge split operation.

Code:
```asm
      for (EdgeIter edge = mesh.edgesBegin(); edge != mesh.edgesEnd(); edge++)
      {
          edge->isNew = false;
          HalfedgeIter h = edge->halfedge();
          VertexIter a = h->vertex();
          VertexIter b = h->twin()->vertex();
          VertexIter c = h->next()->next()->vertex();
          VertexIter d = h->twin()->next()->next()->vertex();
          edge->newPosition = (a->position+b->position)*(3.0f/8.0f) + (c->position+d->position)*(1.0f/8.0f);
      }
```
2. Secondly, we loop through all the vertices and assign the new positions.
```asm
      for (VertexIter v = mesh.verticesBegin();v!= mesh.verticesEnd(); v++)
      {
          v->isNew= false;
          int n_edge = 0;
          HalfedgeIter h = v->halfedge();
          Vector3D neighbors = Vector3D();
          do{
              n_edge +=1;
              h = h->twin()->next();
              neighbors += h->twin()->vertex()->position;
          }while(h!=v->halfedge() && !h->isBoundary());

          if (n_edge==3)
          {
              float u = 3.0f/16.0f;
              v->newPosition = (1-3*u)*v->position + u*neighbors;
          }
          else
          {
              float u = 3.0f/(8.0f*(float)n_edge);
              v->newPosition = (1-n_edge*u)*v->position + u*neighbors;
          }
      }
```
3. Now, we perform the split operation to the old edges. Originally, I thought that it will be the same as step 1, but it turned out that the new edges will be also included in the loop. Therefore, in the loop, we only processed the old edges with `e->isNew = false`.

Code:
```asm
      for (EdgeIter edge = mesh.edgesBegin(); edge!= mesh.edgesEnd(); edge ++)
      {
          if (edge->isNew && (((edge->halfedge()->vertex()->isNew)&&!(edge->halfedge()->twin()->vertex()->isNew))||(!(edge->halfedge()->vertex()->isNew)&&(edge->halfedge()->twin()->vertex()->isNew))))
          {
              mesh.flipEdge(edge);
          }
      }
```

4. Then, we flip any new edge that connects an old vertex and a new vertex.

Code:
```asm
      for (EdgeIter edge = mesh.edgesBegin(); edge!= mesh.edgesEnd(); edge ++)
      {
          if (edge->isNew && (((edge->halfedge()->vertex()->isNew)&&!(edge->halfedge()->twin()->vertex()->isNew))||(!(edge->halfedge()->vertex()->isNew)&&(edge->halfedge()->twin()->vertex()->isNew))))
          {
              mesh.flipEdge(edge);
          }
      }
```

5. Finally, we update the position of all vertices.

Code:
```asm
      for (VertexIter v = mesh.verticesBegin();v!= mesh.verticesEnd(); v++)
      {
          v->position = v->newPosition;
      }
```

#### Results
* Here show our results on loop subdivision:
![Figure_6_1](./Figures/Figure6_1.jpg)
As we can see, loop subdivision gives smaller meshes and a much more smooth surface, with more continuous textures.
Due to the interpolation, sharp corners and edges tends to be smoothed out.


* In this example (exported from blender, Top left), the 3D model has sharper corners and edges (e.g., the hat parts), if we directly perform loop subdivision (Top right), the edges will be smoothed out without keep its original shape.
![Figure_6_2](./Figures/Figure6_2.jpg)
One way to mitigate this is by pre-splitting the edges without interpolation (bottom left), then we are able to keep its original shape. Therefore, when we perform loop subdivision, we can see that the hat region with pre-splitting can better preserve the original shape.

* For the cube loop subdivision, the cube becomes slightly asymmetric after a few steps. From our viewpoint, its caused by the fact that the curvature at the vertex and midpoints are different.
![Figure_6_3](./Figures/Figure6_3.jpg)
One way to alleviate the effects is to make the initial mesh symmetric, here is an easy way - split the diagonal edges on each surface.
![Figure_6_4](./Figures/Figure6_4.jpg)

### Part 7: Design and Edit Your Own Mesh! (Optional, Possible Extra Credit)

In this part, we created a warriors with a shield and a sword using Blender.
![Figure_7_1](./Figures/Figure7_1.jpg)
This figure shows the results after a few steps of loop subdivision. The original warrior becomes a cute penguin-like figure.
Beside what has been introduced in the tutorial, I added the gesture of the figure, which involves rotation, scaling operations. We also create eyes to make the figure more vivid.

