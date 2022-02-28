# Project 2 here
---
layout: default
title: Project 2 - MeshEdit
description: Ke Wang - Alfredo De Goyeneche
---

[Link to (this) Webpage](https://cal-cs184-student.github.io/sp22-project-webpages-asdegoyeneche/proj2/index.html)

[Link to Code](https://github.com/cal-cs184-student/p2-meshedit-sp22-mr_graphics_2)

## Task 5: Edge Split (15 pts)

### Answers
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

### Answers