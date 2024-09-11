---
title: Perspective Projection And Orthographic Projection Matrices For Computer Graphics
authors:
  - name: Lambda Xymox
  - email: lambdaxymox@gmail.com
license: CC-BY-4.0
keywords: computer-graphics, computer-science, linear-algebra
abstract: We derive a general formula for perspective and orthographic projection matrices in {math}`\mathbb{RP}^{3}`, and then
apply them to deriving the corresponding matrices for specific platform APIs.
---

## Introduction

We construct the perspective and orthographic projection matrices common to computer graphics in a 
very general way. The usual parametrizations are defined with respect to specific coordinate system 
and a specific canonincal view volume, such that the view volume parameters define the frustum 
planes directly in terms of coordinates. This is how most computer graphics books construct the 
projection transformations.

Rendering is done in the manifold {math}`\mathbb{RP}^{3}` (i.e. homogeneous coordinates) for several
reasons: (1) affine transformations become linear transformations one dimension higher, so translations
can be treated like any other linear transformations; (2) transformations in {math}`\mathbb{RP}^{3}` 
are well-defined under changes in scale, so we can handle projective transformations in a unified fashion;
(3) we can treat projective transformations in the same setting as affine transformations;
(4) Coordinate systems and scales become equivalent, allowing spatial simulation problems to be 
expressed directly in a coordinate independent way; (5) we can change coordinate systems to whatever 
makes coordinate system makes the simulation problem at hand convenient to work with; (6) for very 
practical reasons, we can express the problem in a coordinate system that affords the best numerical 
precision possible on the hardware. 

Regarding the last point, a platform such as OpenGL, Vulkan, DirectX, Metal, or WebGPU typically defines
its normalized device coordinate system as one that is a numerically favorable coordinate system. Any 
spatial computing domain whose problems are done in Euclidean space can take advantage of the 
manifold {math}`\mathbb{RP}^{3}`, including computer graphics, computer vision, geometric modeling, and 
robotics. In the context of computer graphics, the platform coordinate system is whichever one maps 
the view volume in the viewing space to the canonical view volume defined by the platform interface. 
In particular, the canonical view volume tends to be parametrized by either
{math}`[-1, 1] \times [-1, 1] \times [-1, 1]` or {math}`[-1, 1] \times \ [-1, 1] \times [0, 1]`. In 
either case, transforming the problem to a unit interval adds one free bit of extra precision when 
working with floating point numbers. This maximizes the accuracy of floating point computations on 
the GPU, including tasks such as intersection testing, depth testing, sampling, and clipping.

We parameterize the view space viewing frustum in a slightly different way than the usual one to 
make the perspective view volume specification coordinate invariant. This allows us to construct the 
matrices for any specific source view space coordinate system, source perspective view volume, source 
orthographic view volume, target normalized device coordinate system, and target canonical view volume. 
We define a canonical set of transformations where the view space with a left-handed orthonormal frame 
where the vertical axis points up, the horizontal axis points right, and the depth axis points into the 
viewing volume, and a clip coordinate system with a left-handed orthonormal frame where the vertical axis 
points up, the horizontal axis points right, and the depth axis points into the viewing volume. We show 
how to construct a general perspective projection or orthographic projection from any source view coordinate 
system to any targe clip coordinate system, and that the resulting transformation is coordinate invariant.

## The Topological Manifold Structure Of Real Projective Space

This section establishes the topological structure and manifold structure of the real 
projective space {math}`\mathbb{RP}^{3}`. It can be skipped if the reader so desires.

We define real projective space {math}`\mathbb{RP}^{3}` as follows. Define {math}`\mathbf{w_{1}} \sim \mathbf{w_{2}}`
if an only if there exists a nonzero real number {math}`\lambda \in \mathbb{R} - \{0\}` such that 
{math}`\mathbf{w_{1}} = \lambda \mathbf{w_{2}}`. The relation {math}`\sim` is an equivalence relation. 
To prove this, we need to show that {math}`\sim` is reflexive, symmetric, and transitive. For reflexivity,
trivially {math}`\mathbf{w} = \mathbf{w}` so we can take {math}`\lambda = 1` which shows that 
{math}`\mathbf{w} \sim \mathbf{w}`. To show symmetry, suppose that {math}`\mathbf{w}_{1} \sim \mathbf{w}_{2}`.
Then there exists {math}`\lambda \in \mathbb{R} - \{ 0 \}` such that {math}`\mathbf{w}_{2} = \lambda \mathbf{w}_{1}`
implying that {math}`\mathbf{w}_{1} = \frac{1}{\lambda} \mathbf{w}_{2}` hence {math}`\mathbf{w}_{2} \sim \mathbf{w}_{1}`.
Now it remains to prove that {math}`\sim` is transitive. Suppose that {math}`\mathbf{w}_{1} \sim \mathbf{w}_{2}`
and {math}`\mathbf{w}_{2} \sim \mathbf{w}_{3}`. Then there exist real numbers 
{math}`\mathbf{\lambda}, \mathbf{\mu} \in \mathbb{R} - \{ 0 \}` such that {math}`\mathbf{w}_{2} = \lambda \mathbf{w}_{1}`
and {math}`\mathbf{w}_{3} = \mu \mathbf{w}_{2}`. This implies that

```{math}
\mathbf{w}_{3} 
    = \mu \mathbf{w}_{2}
    = \mu \left( \lambda \mathbf{w}_{1} \right) 
    = \left( \mu \lambda \right) \mathbf{w}_{1}
    = \mu \lambda \mathbf{w}_{1}
```

implying that {math}`\mathbf{w}_{1} \sim \mathbf{w}_{3}`. This proves transitivity. Therefore, {math}`\sim` is
and equivalence relation.

We define the **real projective space** by {math}`\mathbb{RP}^{3} = ( \mathbb{R}^{4} - \{ \mathbf{0}\} )/\sim`. 
The real projective space identifies lines through the origin in {math}`\mathbb{R}^{3}` with points in 
{math}`\mathbb{RP}^{3}`. 

Define a map 
{math}`\pi : \mathbb{R}^{4} - \{\mathbf{0}\} \rightarrow \mathbb{RP}^{3}` by 

```{math}
\pi\left( P \right) = \begin{bmatrix} P \end{bmatrix}
``` 

where {math}`[.]` on the right-hand side indicates the equivalence class of 
{math}`\begin{pmatrix} P^{T}, w \end{pmatrix}^{T}`. The function {math}`\pi` is surjective. To show this, 
suppose that {math}`[P]` is a homogeneous point in {math}`\mathbb{RP}^{3}`. Since 
{math}`[P]` is an equivalence class, it is nonempty, so there is at least one element in {math}`[P]`, namely 
{math}`P` itself. Since the map {math}`\pi` maps elements to its equivalence class, we obtain 
{math}`\pi(P) = [P]`. Since the equivalence class {math}`[P]` was chosen 
arbitrarily, the function {math}`\pi` is surjective.

Now that we have established that {math}`\pi` is surjective, we can use {math}`\pi` to define a topology on 
{math}`\mathbb{RP}^{3}`. We say that a set {math}`U \subset \mathbb{RP}^{3}` is **open** if and only if 
the inverse image {math}`\pi^{-1}(U)` is open in {math}`\mathbb{R}^{4}`. In particular, we define the topology 
of {math}`\mathbb{RP}^{3}` to be the quotient topology induced by {math}`\pi`. The real projective 
space {math}`\mathbb{RP}^{3}` in conjunction with the quotient toplogy induced by {math}`\pi` is a topological 
space. The quotient topology automatically makes the surjection {math}`\pi` a continuous function. 

Now we want to create a topological manifold out of {math}`\mathbb{RP}^{3}`. We need to define an atlas,
then show that the charts in the atlas are homeomorphic to open subsets of 
{math}`\mathbb{R}^{3} - \{ \mathbf{0} \}`. Then we need to show that the resulting atlas makes 
{math}`\mathbb{RP}^{3}` locally Euclidean. That is, every point in {math}`\mathbb{RP}^{3}` has an open
neighborhood homeomorphic to {math}`\mathbb{R}^{3} - \{ \mathbf{0} \}`. After that, we show that 
{math}`\mathbb{RP}^{3}` is Hausdorff and second countable. All of this together shows that 
{math}`\mathbb{RP}^{3}` is a topological {math}`3`-manifold.

For every {math}`i \in \{ 0, 1, 2, 3 \}`, define the set {math}`U_{i}` by

```{math}
U_{i} = \{ \mathbf{x} \in \mathbb{R}^{4} - \{ \mathbf{0} \} \mid x_{i} \neq 0 \}
```

The set {math}`U_{i}` is an open set in {math}`\mathbb{R}^{4} - \{ \mathbf{0} \}`. To prove this, we will
show that {math}`U_{i}` can be covered by open balls of elements in {math}`U_{i}`. Suppose that 
{math}`\mathbf{p} \in U_{i}`. Let {math}`h = |\mathbf{p} \cdot \mathbf{\hat{e}}_{i}| = |p_{i}|` be the distance 
of the point {math}`\mathbf{p}` from the hyperplane defined by {math}`x_{i} = 0`. Let 
{math}`d = \Vert \mathbf{p} \Vert =  \Vert \mathbf{p} - \mathbf{0} \Vert` be the Euclidean norm of the vector 
{math}`\mathbf{p}`. Let {math}`r = \min \{ d, h \}` and consider the open ball {math}`B_{r}(\mathbf{p})` in 
{math}`\mathbb{R}^{4}`. Choose {math}`\mathbf{q} \in B_{r}(\mathbf{p})`. By the triangle inequality

```{math}
\Vert \mathbf{q} \Vert 
    \geq \Vert \mathbf{p} \Vert - \Vert \mathbf{q} - \mathbf{p} \Vert 
    > d - r 
    \geq d - \frac{d}{2} 
    = \frac{d}{2} 
    > 0.
```

Hence {math}`\mathbf{q} \in \mathbb{R}^{4} - \{ \mathbf{0} \}`. Since {math}`\mathbf{q}` was chosen arbitrarily,
this implies that {math}`B_{r}(\mathbf{p}) \subset \mathbb{R}^{4} - \{ \mathbf{0} \}`.
Applying the triangle inequality again,

```{math}
|q_{i}| \geq |p_{i}| - |q_{i} - p_{i}| > h - r \geq h - \frac{h}{2} = \frac{h}{2} > 0.
```

Thus {math}`\mathbf{q}` does not lie on the hyperplane defined by {math}`x_{i} = 0`. Therefore 
{math}`\mathbf{q} \in U_{i}` implying that {math}`B_{r}(\mathbf{p}) \subset U_{i}`. Since the vector 
{math}`\mathbf{p} \in U_{i}` was arbitrary, every point in {math}`U_{i}` lies in an open neighborhood 
that contains only elements of {math}`U_{i}`, we see that

```{math}
\bigcup_{\mathbf{p} \in U_{i}} B_{r(\mathbf{p})}(\mathbf{p}) = U_{i}.
```

The open balls constructed above cover {math}`U_{i}` in open sets. Since {math}`\mathbb{R}^{4}` is a 
topological space, and {math}`U_{i}` is the union of open sets in the topology of {math}`\mathbb{R}^{4}`,
then {math}`U_{i}` is an open set in {math}`\mathbb{R}^{4}`. Moreover, since {math}`\mathbb{R}^{4} - \{ \mathbf{0} \}`
is an open set in {math}`\mathbb{R}^{4}`, the set {math}`U_{i}` is an open set of {math}`\mathbb{R}^{4} - \{ \mathbf{0} \}` in 
the subspace topology that {math}`\mathbb{R}^{4} - \{ \mathbf{0} \}` inherits from {math}`\mathbb{R}^{4}`.

Define the set {math}`V_{i} \subset \mathbb{RP}^{3}` by {math}`V_{i} = \pi\left(U_{i}\right)`. The set 
{math}`V_{i}` is open in {math}`\mathbb{RP}^{3}`. To show this, observe the following equalities

```{math}
\pi^{-1}\left( V_{i} \right) 
    &= \pi^{-1}\left( \pi\left( U_{i} \right) \right) \\
    &= \{ \mathbf{p} \in \mathbb{R}^{4} - \{ \mathbf{0} \} \mid \pi\left( \mathbf{p} \right) \in V_{i} \} \\
    &= \{ \mathbf{p} \in \mathbb{R}^{4} - \{ \mathbf{0} \} \mid \exists \lambda \in \mathbb{R} - \{ 0 \} \hspace{4 pt} \text{such that} \hspace{4 pt} \mathbf{p} \in \lambda U_{i} \} \\
    &= U_{i}
```

where the last equality follows because the preimage of any element in {math}`V_{i}` contains all nonzero 
multiples of an element of {math}`U_{i}`, which are still an elements of {math}`U_{i}`. Therefore, the set 
{math}`\pi^{-1}(V_{i})` is an open set in {math}`\mathbb{R}^{4} - \{ \mathbf{0} \}`, which makes {math}`V_{i}`
an open set in {math}`\mathbb{RP}^{3}` under the quotient topology.

For each {math}`i \in \{ 0, 1, 2, 3 \}` define the map {math}`\pi_{i} : U_{i} \rightarrow V_{i}` by 
{math}`\pi_{i} = \pi|_{U_{i}}`. Since the map {math}`\pi_{i}` is the restriction of a continuous map, it is also a
continuous map. It is surjective by definition, since the codomain is the image {math}`\pi(U_{i})`.
To show that {math}`\pi_{i}` is a quotient map, we must prove that a subset {math}`W \subset V_{i}` is open
in {math}`V_{i}` if and only if {math}`\pi^{-1}_{i}(W)` is open in {math}`U_{i}`. Suppose that {math}`W` is
open in {math}`V_{i}`. Then the inverse image of {math}`W` under {math}`\pi` is open in 
{math}`\mathbb{R}^{4} - \{ \mathbf{0} \}`. Since {math}`U_{i}` is open in {math}`\mathbb{R}^{4} - \{ \mathbf{0} \}`
we see that {math}`\pi^{-1}_{i}(W) = \pi^{-1}(W) \cap U_{i}`. Since {math}`W` is open in {math}`V_{i}`, there exists an 
open set {math}`X \subset \mathbb{RP}^{3}` such that {math}`W = X \cap V_{i}`. Since {math}`\pi` is continuous, 
{math}`\pi^{-1}(X)` is open in {math}`\mathbb{R}^{4} - \{ \mathbf{0} \}`, which implies that {math}`\pi^{-1}(X) \cap U_{i}`
is open in {math}`U_{i}`. But

```{math}
\pi^{-1}\left( X \right) \cap U_{i} 
    = \pi^{-1}\left( X \cap V_{i} \right) \cap U_{i} 
    = \pi^{-1}\left( W \right) \cap U_{i} 
    = \pi^{-1}_{i}\left( W \right)
```

which is an open set in {math}`U_{i}`. Thus {math}`\pi^{-1}_{i}(W)` is an open set in {math}`U_{i}`. This 
proves that if {math}`W \subset V_{i}` is open, then {math}`\pi^{-1}(W) \subset U_{i}` is open. Conversely, suppose 
that {math}`\pi^{-1}_{i}(W)` is open in {math}`U_{i}`. Since {math}`U_{i}` is open in 
{math}`\mathbb{R}^{4} - \{ \mathbf{0} \}`, there exists an open set {math}`X` of {math}`\mathbb{R}^{4} - \{ \mathbf{0} \}`
such that {math}`\pi^{-1}_{i}(W) = X \cap U_{i}`. Define the set {math}`X^{\prime} = X \cup ((\mathbb{R}^{4} - \{ \mathbf{0} \}) - U_{i})`.
We show that the set {math}`X^{\prime}` is open in {math}`\mathbb{R}^{4} - \{ \mathbf{0} \}`.
Suppose that {math}`\mathbf{q} \in (\mathbb{R}^{4} - \{ \mathbf{0} \}) - U_{i}`. We can find a radius {math}`r > 0`
such that {math}`B_{r}(\mathbf{q})` does not intersect {math}`U_{i}` such that {math}`B_{r}(\mathbf{q}) \cap U_{i} = \emptyset`.
Indeed, we can choose {math}`r` to be less than the distance to the nearest point where the {math}`i^{th}` coordinate 
is zero, i.e. {math}`r < |q_{i}|`. With the chosen {math}`r`, we have
{math}`B_{r}(\mathbf{q}) \subset \mathbb{R}^{4} - \{ \mathbf{0} \} - U_{i}` for each 
{math}`\mathbf{q} \in \mathbb{R}^{4} - \{ \mathbf{0} \} - U_{i}`. This shows that {math}`\mathbb{R}^{4} - \{ \mathbf{0} \} - U_{i}` is open in {math}`\mathbb{R}^{4} - \{ \mathbf{0} \}`. Since {math}`X^{\prime}` is the union of two open sets, it is also open.
The set {math}`X^{\prime}` is open and saturated with respect to {math}`\pi`, so that image {math}`\pi(X^{\prime})` 
is open in {math}`\mathbb{RP}^{3}` because {math}`\pi` is a quotient map. We have 

```{math}
W = \pi_{i}(\pi^{-1}_{i}\left( W \right)) = \pi_{i}\left( X \cap U_{i} \right) = \pi\left( X^{\prime} \right) \cap V_{i}.
```

and it follows that {math}`\pi^{-1}_{i}(W)` is open in {math}`\mathbb{R}^{4} - \{ \mathbf{0} \}`. This demonstrates
that {math}`\pi_{i}` is a quotient map.

We show that each set {math}`V_{i}` is homeomorphic to {math}`\mathbb{R}^{4}`. For each {math}`i \in \{ 0, 1, 2, 3 \}`, 
define the map {math}`H_{i} : V_{i} \rightarrow \mathbb{RP}^{3}` by

```{math}
H_{i}  \left(
       \begin{bmatrix} 
            \begin{pmatrix} 
                P_{0}     \\ 
                P_{1}     \\
                \vdots    \\
                P_{i - 1} \\
                P_{i}     \\
                P_{i + 1} \\
                \vdots    \\
                P_{3}     \\
            \end{pmatrix} 
        \end{bmatrix} 
        \right) 
    =   \begin{bmatrix} 
            \begin{pmatrix}
                P_{0} / P_{i}     \\
                P_{1} / P_{i}     \\
                \vdots            \\
                P_{i-1} / P_{i}   \\
                1                 \\
                P_{i+1} / {P_{i}} \\
                \vdots            \\
                P_{3} / P_{i}     \\
            \end{pmatrix}
        \end{bmatrix}.
```

That is, the {math}`i^{th}` coordinate is {math}`1`, and the rest are divided by {math}`P_{i}`. This map sends 
a homogeneous point to the homogeneous point with the {math}`i^{th}` coordinate normalized. Let {math}`W_{i}` 
be the set of points in {math}`\mathbb{RP}^{3}` whose {math}`i^{th}` coordinate is {math}`1`. Define the map 
{math}`\text{proj}_{i} : \mathbb{RP}^{3} \supset W_{i} \rightarrow \mathbb{R}^3` by 

```{math}
\text{proj}_{i}
    \left(   
        \begin{bmatrix} 
            \begin{pmatrix}
                P_{0}   \\
                P_{1}   \\
                \vdots  \\
                P_{i-1} \\
                1       \\
                P_{i+1} \\
                \vdots  \\
                P_{3}   \\
            \end{pmatrix}
        \end{bmatrix}
    \right)
    = 
    \begin{pmatrix}
        P_{0}   \\
        P_{1}   \\
        \vdots  \\
        P_{i-1} \\
        P_{i+1} \\
        \vdots  \\
        P_{3}   \\
    \end{pmatrix}
```

which maps the homogeneous point to a Euclidean point with the {math}`i^{th}` component removed.
The maps {math}`\text{proj}_{i}` and {math}`H_{i}` together allow us to define our coordinate maps. Define the map
{math}`\psi_{i} : V_{i} \rightarrow \mathbb{R}^{3}` by {math}`\psi_{i} = \text{proj}_{i} \circ H_{i}`,
written out as

```{math}
\psi_{i}
    \left( 
        \begin{bmatrix}
            \begin{pmatrix} 
                P_{0}     \\ 
                P_{1}     \\
                \vdots    \\
                P_{i - 1} \\
                P_{i}     \\
                P_{i + 1} \\
                \vdots    \\
                P_{3}     \\
            \end{pmatrix} 
        \end{bmatrix}
    \right)
    =
    \begin{pmatrix}
        P_{0} / P_{i}     \\
        P_{1} / P_{i}     \\
        \vdots            \\
        P_{i-1} / P_{i}   \\
        P_{i+1} / {P_{i}} \\
        \vdots            \\
        P_{3} / P_{i}     \\
    \end{pmatrix}.
```

Let {math}`\varphi_{i} : U_{i} \rightarrow \mathbb{R}^{3}` be given by {math}`\varphi_{i} = \psi_{i} \circ \pi`, 
written out as

```{math}
\varphi_{i}
    \left( 
        \begin{pmatrix} 
            P_{0}     \\ 
            P_{1}     \\
            \vdots    \\
            P_{i - 1} \\
            P_{i}     \\
            P_{i + 1} \\
            \vdots    \\
            P_{3}     \\
        \end{pmatrix}     
    \right)
    =
    \begin{pmatrix}
        P_{0} / P_{i}     \\
        P_{1} / P_{i}     \\
        \vdots            \\
        P_{i-1} / P_{i}   \\
        P_{i+1} / {P_{i}} \\
        \vdots            \\
        P_{3} / P_{i}     \\
    \end{pmatrix}.
```

The map {math}`\varphi_{i}` is continuous by the universal property of product spaces applied to {math}`\mathbb{R}^{4}`. 
Since the map {math}`\varphi_{i}` is continuous, the universal property of quotient maps implies that the map 
{math}`\psi_{i}` is continuous. We must show that {math}`\psi_{i}` is bijective. Let's prove surjectivity: let 
{math}`\begin{pmatrix} P_{0} \ P_{1} \ \dots \ P_{i-1} \ P_{i+1} \dots \ P_{3}  \end{pmatrix}^{T} \in \mathbb{R}^{3}`,
let
{math}`\begin{bmatrix} \begin{pmatrix} P_{0} \ P_{1} \ \dots \ P_{i-1} \ 1 \ P_{i+1} \dots \ P_{3} \end{pmatrix} \end{bmatrix}^{T} \in V_{i}`,
From the definition of {math}`\psi_{i}`, we see that

```{math}
\psi_{i}
    \left( 
        \begin{bmatrix}
            \begin{pmatrix} 
                P_{0}     \
                P_{1}     \
                \dots    \
                P_{i - 1} \
                1         \
                P_{i + 1} \
                \dots    \
                P_{3}     \
            \end{pmatrix}
        \end{bmatrix}^{T}
    \right)
    =
    \begin{pmatrix}
        P_{0}   \
        P_{1}   \
        \dots  \
        P_{i-1} \
        P_{i+1} \
        \dots  \
        P_{3}   \
    \end{pmatrix}^{T}.
```

which establishes surjectivity. To show injectivity, we require the following fact: every element in {math}`V_{i}`
has a unique representative whose {math}`i^{th}` coordinate is {math}`1`. To see this, suppose that {math}`[Q]` is a
homogeneous point in {math}`V_{i}` with representatives 
{math}`\begin{pmatrix} Q_{0} \ Q_{1} \ \dots \ Q_{i-1} \ 1 \ Q_{i+1} \dots \ Q_{3}  \end{pmatrix}^{T} \in \mathbb{R}^{3}`
and 
{math}`\begin{pmatrix} Q^{\prime}_{0} \ Q^{\prime}_{1} \ \dots \ Q^{\prime}_{i-1} \ 1 \ Q^{\prime}_{i+1} \dots \ Q^{\prime}_{3}  \end{pmatrix}^{T} \in \mathbb{R}^{3}`
where the {math}`i^{th}` component is {math}`1`. Since both representatives represent the same point, they 
must be equivalent, i.e. there exists {math}`\lambda \in \mathbb{R} - \{0\}` such that

```{math}
\begin{pmatrix} Q_{0} \ Q_{1} \ \dots \ Q_{i-1} \ 1 \ Q_{i+1} \dots \ Q_{3} \end{pmatrix}^{T}
=
\lambda 
\begin{pmatrix} Q^{\prime}_{0} \ Q^{\prime}_{1} \ \dots \ Q^{\prime}_{i-1} \ 1 \ Q^{\prime}_{i+1} \dots \ Q^{\prime}_{3} \end{pmatrix}^{T}.
```

The fact that the {math}`i^{th}` component is {math}`1` in both representatives implies that 
{math}`Q^{\prime}_{j} = Q_{j}` for each {math}`j \in \{ 0, 1, 2, 3 \}`. Therefore

```{math}
\begin{pmatrix} Q_{0} \ Q_{1} \ \dots \ Q_{i-1} \ 1 \ Q_{i+1} \dots \ Q_{3} \end{pmatrix}^{T}
=
\begin{pmatrix} Q^{\prime}_{0} \ Q^{\prime}_{1} \ \dots \ Q^{\prime}_{i-1} \ 1 \ Q^{\prime}_{i+1} \dots \ Q^{\prime}_{3} \end{pmatrix}^{T}.
```

establishing uniqueness of the representative of {math}`[Q]` whose {math}`i^{th}` component is {math}`1`.
Proving the injectivity of {math}`\psi_{i}` becomes easy. Recall the original definition of {math}`\psi_{i}` as
{math}`\psi_{i} = \text{proj}_{i} \circ H_{i}`. Since a given homogeneous point has a unique representative whose
{math}`i^{th}` component is {math}`1`, the normalization map {math}`H_{i}` is injective. That is, given homogeneous 
points {math}`[Q]` and {math}`[Q^{\prime}]` with {math}`H_{i}([Q]) = H_{i}([Q^{\prime}])`, we conclude 
{math}`[Q] = [Q^{\prime}]`. Also, notice that {math}`\text{proj}_{i}` is bijective with inverse map 
{math}`\text{proj}^{-1}_{i} : \mathbb{R}^{3} \rightarrow \mathbb{RP}^{3}` defined by

```{math}
\text{proj}^{-1}_{i}
    \left(   
        \begin{pmatrix}
            P_{0}   \
            P_{1}   \
            \dots   \
            P_{i-1} \
            P_{i+1} \
            \dots   \
            P_{3}   \
        \end{pmatrix}^{T}
    \right)
    = 
    \begin{bmatrix} 
        \begin{pmatrix}
            P_{0}   \
            P_{1}   \
            \dots   \
            P_{i-1} \
            1       \
            P_{i+1} \
            \dots   \
            P_{3}   \
        \end{pmatrix}^{T}
    \end{bmatrix}.
```

Since {math}`\text{proj}_{i}` is bijective, it is injective, and since {math}`H_{i}` is injective, so is 
their composite {math}`\psi_{i}`. This proves that {math}`\psi_{i}` is bijective.

Next, we show that {math}`\psi^{-1}_{i}` is continuous. Consider the map 
{math}`\rho_{i} : \mathbb{R}^{3} \rightarrow \mathbb{R}^{4} - \{ \mathbf{0} \}` given by

```{math}
\rho_{i}
    \left(  
        \begin{pmatrix}
            P_{0}   \
            P_{1}   \
            \dots   \
            P_{i-1} \
            P_{i+1} \
            \dots   \
            P_{3}   \
        \end{pmatrix}^{T}
    \right)
    =
    \begin{pmatrix}
        P_{0}   \
        P_{1}   \
        \dots  \
        P_{i-1} \
        1       \
        P_{i+1} \
        \dots   \
        P_{3}   \
    \end{pmatrix}^{T}.
```

The map {math}`\rho_{i}` is continuous, its image is contained in {math}`V_{i}`, and 
{math}`\pi_{i} \circ \rho_{i} = \psi^{-1}_{i}`. This implies that {math}`\psi^{-1}_{i}` is continuous, since
it is the composite of continuous functions. We have shown that {math}`\psi_{i}` is continuous, bijective,
and has a continuous inverse. Therefore, it is a homeomorphism. Moreover, we can cover {math}`\mathbb{RP}^{3}`
with the charts {math}`\{ (V_{i}, \psi_{i}) \mid i \in \{ 0, 1, 2, 3 \} \}`, which means that 
{math}`\mathbb{RP}^{3}` is locally Euclidean.

We show that {math}`\mathbb{RP}^{3}` is Hausdorff. Suppose that {math}`[P]` and {math}`[Q]` are two distinct
points in {math}`\mathbb{RP}^{3}`. There are two possibilities: there is an {math}`0 \leq i \leq 2` such that 
both points lie in {math}`V_{i}`, or no such {math}`i` exists such that both points lie in {math}`V_{i}`.
In the first case, {math}`\psi_{i}([P])` and {math}`\psi_{i}([Q])` are distinct points in {math}`\mathbb{R}^{3}`.
Since {math}`\mathbb{R}^{3}` is Hausdorff, there exist disjoint sets {math}`A` and {math}`B` such that
{math}`\psi_{i}([P]) \in A` and {math}`\psi_{i}([Q]) \in B`. Hence, {math}`\psi^{-1}_{i}(A)` and {math}`\psi^{-1}_{i}`
are disjoint open subsets of {math}`V_{i}`, and hence {math}`\psi^{-1}_{i}(A)` and {math}`\psi^{-1}_{i}` are open 
subsets of {math}`\mathbb{RP}^{3}`. This yields the first case. Consider the second case. Suppose that no
such {math}`i` exists such that {math}`\psi_{i}([P])` and {math}`\psi_{i}([Q])` are distinct points in {math}`V_{i}`.
Let {math}`(x_{0}, x_{1}, x_{2}, x_{3})` be a representative of {math}`[P]` and let {math}`(y_{0}, y_{1}, y_{2}, y_{3})` 
be a representative of {math}`[Q]`. There exists {math}`i \neq j` or {math}`0 \leq i, j \leq 3` such that
{math}`x_{i} \neq 0, y_{i} = 0` and {math}`x_{j} = 0, y_{j} \neq 0`. Choose a representative such that {math}`x_{i} = 1`
and {math}`y_{j} = 1`. Assume without loss of generality that {math}`i < j`, and choose {math}`0 < \epsilon < 1`.
The set 

```{math}
A = \{ 
        \begin{pmatrix} a_{0} \ a_{1} \ \dots a_{i-1} \ 1 \ a_{i + 1} \dots a_{3} \end{pmatrix}^{T}
        \mid  
        \forall k \neq i \hspace{4 pt} \lvert a_{k} - x_{k} \rvert < \epsilon
    \} 
    \subset
    V_{i}
```

is an open set containing {math}`[P]` and the set

```{math}
B = \{ 
        \begin{pmatrix} b_{0} \ b_{1} \ \dots b_{j-1} \ 1 \ b_{j + 1} \dots b_{3} \end{pmatrix}^{T}
        \mid
        \forall k \neq i \hspace{4 pt} \lvert b_{k} - y_{k} \rvert < \epsilon
    \} 
    \subset
    V_{j}
```

is an open set containing {math}`[Q]`. The image {math}`\psi_{i}(A)` is an open rectangle in {math}`\mathbb{R}^{3}`
centered on {math}`\psi_{i}([P])` with a side length of {math}`2 \epsilon`. Similarly, the image {math}`\psi_{i}(B)` is 
an open rectangle in {math}`\mathbb{R}^{3}` centered on {math}`\psi_{i}([Q])` with a side length of {math}`2 \epsilon`.
The sets {math}`A` and {math}`B` are disjoint. To show this, suppose {math}`A` and {math}`B` are not disjoint. Then for 
{math}`\begin{pmatrix} a_{0} \ a_{1} \ \dots a_{i-1} \ 1 \ a_{i + 1} \dots a_{3} \end{pmatrix}^{T} = \begin{pmatrix} b_{0} \ b_{1} \ \dots b_{j-1} \ 1 \ b_{j + 1} \dots b_{3} \end{pmatrix}^{T}`
we must have {math}`a_{j} \neq 0` and {math}`b_{i} \neq 0` which implies that {math}`a_{j} b_{i} = 1`. But this is 
impossible, because {math}`\lvert a_{j} \rvert < 1` and {math}`\lvert b_{i} \rvert < 1`.
Therefore, the sets {math}`A` and {math}`B` must be disjoint. This proves that {math}`\mathbb{RP}^{3}` is Hausdorff.

Finally, second countability follows from the fact that {math}`\mathbb{R}^{4} - \{ \mathbf{0} \}` is 
second countable. More precisely, let {math}`\mathcal{B}` be a countable base for the topology on
{math}`\mathbb{R}^{4} - \{ \mathbf{0} \}`. The set

```{math}
\mathcal{B}^{\prime} = \{ \pi\left( U \right) \mid U \in \mathcal{B} \}
```

is a countable set of open sets in {math}`\mathbb{RP}^{3}` because {math}`\pi` is saturated. Since {math}`\pi`
is surjective, {math}`\cup_{B \in \mathcal{B}^{\prime}} B = \mathbb{RP}^{3}`, i.e. the set 
{math}`\mathcal{B}^{\prime}` covers {math}`\mathbb{RP}^{3}`. Hence {math}`\mathbb{RP}^{3}` is second countable.
Note that second countability implies that {math}`\mathbb{RP}^{3}` is compact. We sketch a proof here.
It comes from the fact that the {math}`3`-sphere {math}`S^{3}` is a compact subset of {math}`\mathbb{R}^{4}`,
that the canonical surjection {math}`\pi_{S^{3}}` is continuous, and that the image of a compact set by a 
continuous map is compact. Since {math}`\pi_{S^{3}}(S^{3})` is compact, and {math}`\pi_{S^{3}}(S^{3}) = \mathbb{RP}^{3}`
we immediately infer that {math}`\mathbb{RP}^{3}` is compact.

We have topologized real projective space, and proven that {math}`\mathbb{RP}^{3}` is second countable, 
Hausdorff and locally Euclidean. Therefore {math}`\mathbb{RP}^{3}` is a topological {math}`3`-manifold 
(or we just say that it is a {math}`3`-manifold, or a manifold). Finally, {math}`\mathbb{RP}^{3}` is 
compact, so it is a nice setting to work with topologically.

## Representing Transformations In Real Projective Space

Any orthonormal frame {math}`(\tilde{O}_{frame}, \left( \mathbf{\hat{u}}_{h}, \mathbf{\hat{u}}_{v}, \mathbf{\hat{u}}_{d} \right))` 
on {math}`\mathbb{E}^{3}` induces a coordinate chart as follows. A point {math}`\tilde{P} \in \mathbb{E}^{3}` is written as

```{math}
\tilde{P} = \tilde{O}_{frame} + P_{h} \mathbf{\hat{u}}_{h} + P_{v} \mathbf{\hat{u}}_{v} + P_{d} \mathbf{\hat{u}}_{d}
```

and the orthonormal frame 
{math}`(\tilde{O}_{frame}, \left( \mathbf{\hat{u}}_{h}, \mathbf{\hat{u}}_{v}, \mathbf{\hat{u}}_{d} \right))` 
defines a coordinate chart {math}`\varphi_{frame} : \mathbb{E}^{3} \rightarrow \mathbb{R}^{3}` by 
{math}`\varphi_{frame}( \tilde{P} ) = \tilde{P} - \tilde{O}`. Written out

```{math}
P = \varphi_{frame}(\tilde{P}) \equiv \tilde{P} - \tilde{O}_{frame} = P_{h} \mathbf{\hat{u}}_{h} + P_{v} \mathbf{\hat{u}}_{v} + P_{d} \mathbf{\hat{u}}_{d}
```

is the representation of {math}`\tilde{P}` in {math}`\mathbb{R}^{3}`. Also, the coordinate chart
{math}`\varphi_{frame}` maps the origin {math}`\tilde{O}_{frame}` of the view coordinate system in {math}`\mathbb{E}^{3}` 
to {math}`\mathbf{0}`: 
{math}`O_{frame}` = {math}`\varphi_{frame}(\tilde{O}_{frame}) = \tilde{O}_{frame} - \tilde{O}_{frame} = \mathbf{0}`. 
This shows that the view space frame origin in {math}`\mathbb{E}^{3}` indeed maps to the vector space origin 
{math}`\mathbf{0}` in {math}`\mathbb{R}^{3}`.

Recall that the definition of real projective space defines the manifold structure using the surjection 
{math}`\pi : \mathbb{R}^{4} - \{\mathbf{0}\} \rightarrow \mathbb{RP}^{3}` given by 

```{math}
\pi\left( \begin{pmatrix} P \\ w \\ \end{pmatrix} \right) = \begin{bmatrix} \begin{pmatrix} P \\ w \\ \end{pmatrix} \end{bmatrix}
```

where {math}`[.]` on the right-hand side indicates the equivalence class of 
{math}`\begin{pmatrix} P^{T}, w \end{pmatrix}^{T}`.

Define a map {math}`\rho : \mathbb{R}^{3} \rightarrow \mathbb{R}^{4} - \{\mathbf{0}\}` by

```{math}
\rho\left( P \right) = \begin{pmatrix} P \\ 1 \\ \end{pmatrix}
```

The maps {math}`\pi` and {math}`\rho` together allow us to map from view space to 
projective view space with the camera orthonormal frame by

```{math}
\left(\pi \circ \rho\right)\left(P\right) 
    = \pi\left( \rho\left( P \right) \right) 
    = \pi\left( \begin{pmatrix} P \\ 1 \\ \end{pmatrix} \right)
    = \begin{bmatrix} \begin{pmatrix} P \\ 1 \\ \end{pmatrix} \end{bmatrix}.
```

The map {math}`\rho` defines an embedding (injection) of {math}`\mathbb{R}^{3}` into {math}`\mathbb{R}^{4} - \{ \mathbf{0} \}`,
because each element in {math}`\rho(\mathbb{R}^{3})` has a unique elements {math}`P \in \mathbb{R}^{3}` such that
{math}`\rho(P) = \begin{pmatrix} P^{T} \ 1 \end{pmatrix}^{T}`. Since {math}`\rho` is surjective on its image, injective, and
continuous with continuous inverse, it is a homeomorphism on its image, hence an embedding. Since every element
of {math}`\mathbb{R}^{3}` has a unique homogeneous point {math}`Q` such that 
{math}`Q = [\begin{pmatrix} P^{T} \ 1 \end{pmatrix}^{T}]`, the map {math}`\pi \circ \rho` is also an embedding.
The set {math}`\pi(\rho(\mathbb{R}^{3}))` is sometimes called the **affine patch** or **Euclidean patch** of 
{math}`\mathbb{RP}^{3}`.

We can lift points in {math}`\mathbb{E}^{3}` into {math}`\mathbb{RP}^{3}`. Let's do the same for transformations.
Suppose that {math}`A : \mathbb{R}^{3} \rightarrow \mathbb{R}^{3}` is an affine map. We define the **lifted** map 
of {math}`A` over to {math}`\mathbb{RP}^{3}` to be the map 
{math}`\hat{A} : \mathbb{RP}^{3} \rightarrow \mathbb{RP}^{3}` such that

```{math}
:label: eq_lift_affine1
\left( \rho \circ A \right)\left( P \right) = \left( \hat{A} \circ \rho \right) \begin{pmatrix} P \end{pmatrix}.
```

Recall that an affine map has the form {math}`A(P) = L(P) + \mathbf{t}` where 
{math}`L : \mathbb{R}^{3} \rightarrow \mathbb{R}^{3}` is a linear map, and
{math}`\mathbf{t}` is a vector, i.e. a linear map plus a translation term. Expanding this out, 
the left-hand side of {ref}`eq_lift_affine1` becomes

```{math}
\left( \rho \circ A \right)\left( P \right)
    = \begin{pmatrix} A\left( P \right) \\ 1 \\ \end{pmatrix}
    = \begin{pmatrix} L\left( P \right) + \mathbf{t} \\ 1 \\ \end{pmatrix}
    = \begin{bmatrix} L & \mathbf{t} \\ \mathbf{0}^{T} & 1 \\ \end{bmatrix} \begin{pmatrix} P \\ 1 \\ \end{pmatrix}
```

and on the right-hand side we have

```{math}
\left( \hat{A} \circ \rho \right) \left( P \right)
    = \hat{A} \left(\rho \left( P \right) \right)
    = \hat{A} \left( \begin{pmatrix} P \\ 1 \\ \end{pmatrix} \right).
```

Equating both sides of {ref}`eq_lift_affine1` we have

```{math}
\hat{A} \left( \begin{pmatrix} P \\ 1 \\ \end{pmatrix} \right) 
    = \begin{bmatrix} L & \mathbf{t} \\ \mathbf{0}^{T} & 1 \\ \end{bmatrix} \begin{pmatrix} P \\ 1 \\ \end{pmatrix}
```

or

```{math}
:label: matrix_repr_affine
\hat{A} = \begin{bmatrix} L & \mathbf{t} \\ \mathbf{0}^{T} & 1 \\ \end{bmatrix}
```

so that {math}`\hat{A}` is unique. An affine map in {math}`\mathbb{R}^{3}` becomes a linear map in 
{math}`\mathbb{RP}^{3}`. In the case of a linear map {math}`L`, {math}`\mathbf{t} = \mathbf{0}` and

```{math}
:label: matrix_repr_linear
\hat{L} = \begin{bmatrix} L & \mathbf{0} \\ \mathbf{0}^{T} & 1 \\ \end{bmatrix}.
```

Now suppose that {math}`T : \mathbb{R}^{3} \rightarrow \mathbb{R}^{3}` is a projective transformation. 
A projective transformation is a map of the form

```{math}
T \left( P \right) = \left( \frac{1}{g(P)} \right) A \left( P \right) = \left( \frac{1}{g(P)} \right) \left( L(P) + \mathbf{t} \right)
```

where {math}`A` is an affine map and {math}`g : \mathbb{R}^{3} \rightarrow \mathbb{R}` is an affine 
scalar function. This means that {math}`g` has the form {math}`g(P) = \mathbf{c} \cdot P + h`. We define 
the **lifted** map of {math}`T` over to {math}`\mathbb{RP}^{3}` to be the map 
{math}`\hat{T} : \mathbb{RP}^{3} \rightarrow \mathbb{RP}^{3}` such that

```{math}
:label: eq_lift_projective1
g\left( P \right) \left( \rho \circ T \right) \left( P \right) = \left( \hat{T} \circ \rho \right) \left( P \right)
```

Expanding out the right-hand side of {ref}`eq_lift_projective1`

```{math}
:label: eq_lift_projective2
\left( \hat{T} \circ \rho \right) \left( P \right)
    = \hat{T} \left( \rho \left( P \right) \right)
    = \hat{T} \begin{pmatrix} P \\ 1 \end{pmatrix}.
```

More interestingly, expanding out the left-hand side of {ref}`eq_lift_projective1` gives

```{math}
:label: eq_lift_projective3
g\left( P \right) \left( \rho \circ T \right) \left( P \right) 
    &= g\left( P \right) \begin{pmatrix} T(P) \\ 1 \end{pmatrix} \\
    &= \begin{pmatrix} g\left( P \right) T(P) \\ g\left( P \right) \end{pmatrix} \\
    &= \begin{pmatrix} A(P) \\ g\left( P \right) \end{pmatrix} \\
    &= \begin{pmatrix} L(P) + \mathbf{t} \\ g\left( P \right) \end{pmatrix} \\
    &= \begin{pmatrix} L(P) + \mathbf{t} \\ \mathbf{c} \cdot P + h \end{pmatrix} \\
    &= \begin{bmatrix} L & \mathbf{t} \\ \mathbf{c}^{T} & h \\ \end{bmatrix} \begin{pmatrix} P \\ 1 \\ \end{pmatrix}
```

where the third equality follows from the definition of the projective transformation. Combining 
{ref}`eq_lift_projective2` and {ref}`eq_lift_projective3` back into {ref}`eq_lift_projective1` yields

```{math}
\hat{T} \begin{pmatrix} P \\ 1 \end{pmatrix} 
    = \begin{bmatrix} L & \mathbf{t} \\ \mathbf{c}^{T} & h \\ \end{bmatrix} \begin{pmatrix} P \\ 1 \\ \end{pmatrix}
```

and therefore

```{math}
:label: matrix_repr_projective
\hat{T} = \begin{bmatrix} L & \mathbf{t} \\ \mathbf{c}^{T} & h \\ \end{bmatrix}.
```

This is a unique representation of the projective transformation {math}`T` lifted over to {math}`\mathbb{RP}^{3}`.
Here is a wonderful discovery: in {math}`\mathbb{RP}^{3}` where all coordinate scales are treated as equivalent, 
we can work with linear, affine, and projective transformations in a unified setting by 
going one dimension higher in our representation from {math}`T : \mathbb{R}^{3} \rightarrow \mathbb{R}^{3}`
to its lifted counterpart {math}`\hat{T} : \mathbb{RP}^{3} \rightarrow \mathbb{RP}^{3}`.

The punchline is that we can lift the Euclidean space {math}`\mathbb{E}^{3}` into real projective space, in such a way that
each point in {math}`\mathbb{E}^{3}` has a corresponding unique point in the affine subspace of {math}`\mathbb{RP}^{3}`
under any coordinate chart. This lift allows us to construct our projections between different coordinate systems 
in real-projective space in a coherent and well-defined manner. Via the embedding {math}`\pi \circ \rho`, the manifold 
structure of {math}`\mathbb{RP}^{3}` allows us to scale our coordinates in any way we like, because in projective 
coordinates we don't care about scaling. We can also lift transformations from {math}`\mathbb{E}^{3}` 
to {math}`\mathbb{RP}^{3}` as a consequence of the embedding {math}`\pi \circ \rho` too. This setting makes it 
convenient to construct perspective and orthographic projections, because at the end, we are back in the affine 
portion of {math}`\mathbb{RP}^{3}` after normalization, so we can map back out of {math}`\mathbb{RP}^{3}` again by choosing 
a chart on {math}`\mathbb{RP}^{3}` and applying it.

We have shown how to map Euclidean space to real projective space, and how to represent linear, affine, 
and projective transformations as linear transformations in linear projective space. It remains to apply
these developments to our primary goal: constructing orthographic and perspective projection matrices 
in homogeneous coordinates.

## Specifying Projection Matrices

To define a projection matrix, we need two coordinate systems: the view coordinate system, and the 
normalized device coordinate system. The view coordinte system is where the view volume is defined.
The normalized device coordinate system is where the canonical view volume is defined. The task of
the projection transformation is to map the view volume to the canonical view volume.

To understand what the projection matrices do, we must understand the coordinate systems that 
we map between at each step in the pipeline leading from the view space to the canonical view 
volume. The transformations stem from choosing a convenient coordinate system 
in which to render computer graphics. The convenient coordinate system we choose is often called
**normalized device coordinates**, defined in the following. The canonical coordinate system differs 
from platform to platform. The other coordinates systems exist to articulate a clear path from projective 
view coordinates to normalized device coordinates. First we must define the view space and the view coordinates. 

The **view coordinate system** for Euclidean space {math}`\mathbb{E}^{3}` is given by the 
orthonormal frame {math}`(\tilde{O}_{view}, \mathcal{B}_{view})` where (1) the **origin** of the orthonormal frame is 
the point {math}`\tilde{O}_{view} \in \mathbb{E}^{3}`; (2) The **basis** of the orthonormal frame is 
{math}`\mathcal{B}_{view} = (\mathbf{\hat{u}}_{h}, \mathbf{\hat{u}}_{v}, \mathbf{\hat{u}}_{d})` where
the basis vector {math}`\mathbf{\hat{u}}_{h}` points to the right, the basis vector {math}`\mathbf{\hat{u}}_{v}`
points up, and the basis vector {math}`\mathbf{\hat{u}}_{d}` points into the view volume or out of the 
view volume, depending on the choice of orientation; (3) the view volume in this coordinate system is 
called the **view volume** (or in the case of perspective projection, the **perspective view volume**).

The **projected coordinate system** for Euclidean space {math}`\mathbb{E}^{3}` is given by the 
orthonormal frame {math}`(\tilde{O}_{proj}, \mathcal{B}_{proj})` where (1) the **origin** of the orthonormal frame is 
the point {math}`\tilde{O}_{proj} \in \mathbb{E}^{3}`; (2) The **basis** of the orthonormal frame is 
{math}`\mathcal{B}_{proj} = (\mathbf{\hat{u}}_{proj,h}, \mathbf{\hat{u}}_{proj,v}, \mathbf{\hat{u}}_{proj,d})` where
the basis vector {math}`\mathbf{\hat{u}}_{proj,h}` points to the right, the basis vector {math}`\mathbf{\hat{u}}_{proj,v}`
points up, and the basis vector {math}`\mathbf{\hat{u}}_{proj,d}` points into the view volume or out of the 
view volume, depending on the choice of orientation; (3) the view volume in this coordinate system is 
called the **projected view volume** (or in the case of perspective projection, the 
**perspective projected view volume**).

The **clip coordinate system** for Euclidean space {math}`\mathbb{E}^{3}` is given by the orthonormal frame 
{math}`(\tilde{O}_{clip}, \mathcal{B}_{clip})` defined on {math}`\mathbb{E}^{3}` where (1) The **origin** of 
the orthonormal frame {math}`(\tilde{O}_{clip}, \mathcal{B}_{clip})` is the point {math}`\tilde{O}_{clip} \in \mathbb{E}^{3}`; 
(2) The **basis** of the orthonormal frame is 
{math}`\mathcal{B}_{clip} = (\mathbf{\hat{u}}_{clip,h}, \mathbf{\hat{u}}_{clip,v}, \mathbf{\hat{u}}_{clip,d})` 
where the basis vector {math}`\mathbf{\hat{u}}_{clip,h}` points to the right, the basis vector 
{math}`\mathbf{\hat{u}}_{clip,v}` points up, and the basis vector {math}`\mathbf{\hat{u}}_{clip,d}` points into the 
view volume, or out of the view volume depending on the choice or orientation; (3) The view volume in this
coordinate system is called the **orthographic view volume**.

The **normalized device coordinate system** for Euclidean space {math}`\mathbb{E}^{3}` is given by the 
orthonormal frame {math}`(\tilde{O}_{ndc}, \mathcal{B}_{ndc})` defined on {math}`\mathbb{E}^{3}` where 
(1) The **origin** of the orthonormal {math}`(\tilde{O}_{ndc}, \mathcal{B}_{ndc})` frame is the point 
{math}`\tilde{O}_{ndc} \in \mathbb{E}^{3}`; (2) The **basis** of the orthonormal frame is 
{math}`\mathcal{B}_{ndc} = (\mathbf{\hat{u}}_{ndc,h}, \mathbf{\hat{u}}_{ndc,v}, \mathbf{\hat{u}}_{ndc,d})` 
where the basis vector {math}`\mathbf{\hat{u}}_{ndc,h}` points to the right, the basis vector 
{math}`\mathbf{\hat{u}}_{ndc,v}` points up, and the basis vector {math}`\mathbf{\hat{u}}_{ndc,d}` points into the 
view volume, or out of the view volume depending on the choice or orientation; (3) The view volume in this
coordinate system is called the **canonical view volume**.

The view coordinate system, projected coordinate system, clip coordinate system, and normalized device 
coordinate system induce coordinate charts via {math}`\varphi_{view}(\tilde{P}) = \tilde{P} - \tilde{O}_{view}`, 
{math}`\varphi_{proj}(\tilde{P}) = \tilde{P} - \tilde{O}_{proj}`, 
{math}`\varphi_{clip}(\tilde{P}) = \tilde{P} - \tilde{O}_{clip}`, 
and {math}`\varphi_{ndc}(\tilde{P}) = \tilde{P} - \tilde{O}_{ndc}`, respectively.

The projection transformations are parametrized by two set of parameters. The first one is the view volume 
parameters {math}`l`, {math}`r`, {math}`b`, {math}`t`, {math}`n`, {math}`f` where {math}`l > 0`, {math}`r > 0`, 
{math}`b > 0`, {math}`t > 0`, and {math}`f > n > 0` such that the view volume is parametrized by 
{math}`[-l, r] \times [-b, t] \times [n, f]`. The second one is the canonical view volume parametrized by
{math}`[\alpha_{min}, \alpha_{max}] \times [\beta_{min}, \beta_{max}] \times [\gamma_{min}, \gamma_{max}]`.
where {math}`\alpha_{max} > \alpha_{min}`, {math}`\beta_{max} > \beta_{min}`, and 
{math}`\gamma_{max} > \gamma_{min}`.

## The Canonical Projection Matrices

In this section, we construct the canonical projection matrices. We call them canonical because each of the 
projections has the same output coordinate system as the input coordinate system. So it is particularly 
convenient to work with.

### The Canonical Coordinate System

We choose a canonical set of coordinate systems to construct the canonical projective transformations
that will be used to derive the projective transformations in specific settings.

The **canonical view coordinates** is the view space coordinate system with orthnormal frame 
{math}`(\tilde{O}_{view}, \mathcal{B}_{view})` where 
{math}`\mathcal{B}_{view} = (\mathbf{\hat{u}}_{h}, \mathbf{\hat{u}}_{v}, \mathbf{\hat{u}}_{d})` such that the depth 
frame vector {math}`\mathbf{\hat{u}}_{d}` points into the view volume. This gives the canonincal view space a 
left-handed orientation. The **canonical projected coordinates** coordinate system is the projected coordinate system 
with the same orthonormal frame as the canonical view space coordinates. The **canonical clip coordinates** coordinate 
system is the clip coordinates with the same orthonormal frame as the canonical view space coordinates. The 
**canonical normalized device coordinates** coordinate system is the normalized device coordinate system with the same 
orthonormal frame as in canonical view coordinates. In particular, each coordinate system uses the same orthonormal 
frame, and has a left-handed orientation.

It is not strictly required that each space have the same orthonormal frame, but using the same coordinate 
system in each step makes it easier to see what is going on in the transformation pipeline. Since we derive 
each transformation between the coordinate systems above, we can always transform any one of them with a
combination of orthogonal transformations and changes of orientation to get the desired ones. It is also much 
less error-prone with fewer pesky signs to deal with.

### The Canonical Perspective Projection Matrix

With these considerations, we construct the canonical perspective projection matrix for 
the frame {math}`(\tilde{O}_{view}, (\mathbf{\hat{u}}_{h}, \mathbf{\hat{u}}_{v}, \mathbf{\hat{u}}_{d}))` with the 
perspective view volume parametrized by {math}`[-l, r] \times [-b, t] \times [n, f]` and 
the canonical view volume parametrized by 
{math}`[\alpha_{min}, \alpha_{max}] \times [\beta_{min}, \beta_{max}] \times [\gamma_{min}, \gamma_{max}]`.
Let {math}`P \in \mathbb{R}^{3}` be a point given by 
{math}`P = P_{h} \mathbf{\hat{u}}_{h} + P_{v} \mathbf{\hat{u}}_{v} + P_{d} \mathbf{\hat{u}}_{d}`. We 
derive the perspective projected horizontal and vertical components.

Using similar triangles for the horizontal component, we have

```{math}
\frac{P_{h}}{P_{d}} = \frac{P \cdot \mathbf{\hat{u}}_{h}}{P \cdot \mathbf{\hat{u}}_{d}} 
                    = \frac{P^{\prime}_{proj} \cdot \mathbf{\hat{u}}_{h}}{n} 
                    = \frac{P^{\prime}_{proj,h}}{n}
```

which yields

```{math}
P^{\prime}_{proj,h} = n P_{h} \left( \frac{1}{P_{d}} \right).
```

Analagously for the vertical component, applying similar triangles gives us

```{math}
\frac{P_{v}}{P_{d}} 
    = \frac{P \cdot \mathbf{\hat{u}}_{v}}{P \cdot \mathbf{\hat{u}}_{d}} 
    = \frac{P^{\prime}_{proj} \cdot \mathbf{\hat{u}}_{v}}{n} 
    = \frac{P^{\prime}_{proj,v}}{n}
```

implying that

```{math}
P^{\prime}_{proj,v} = n P_{v} \left( \frac{1}{P_{d}} \right).
```

Observe that the horizontal and vertical components transforms like a projective transformation. Indeed,
the perspective projection transformation is a projective transformation where the affine function is
given by {math}`P_{d}`. In other words, the perspective projection is dividing by depth. Using our reasoning
about representing projective transformations that lead to the matrix representation {ref}`matrix_repr_projective`, we 
can deduce the lifted mapping from rearranging the perspective equations

```{math}
P_{d} P^{\prime}_{proj,h} = n P_{h} \\
P_{d} P^{\prime}_{proj,v} = n P_{v} \\
```

suggestively. The right hand side in both cases are affine transformations. This suggests to form
of the transformation for the depth component. We define our projected coordinate system components as follows.
Let {math}`P_{proj,h} = P_{d} P^{\prime}_{proj,h}` and {math}`P_{proj,v} = P_{d} P^{\prime}_{proj,v}`.
Since the affine scalar function is perspective division, we immediately see that {math}`P_{proj,w} = P_{d}`. This leaves the
depth component. The depth component does not directly participate in depth normalization, and
we our coordinate system is orthogonal, so {math}`P_{proj,d}` cannot depend on {math}`P_{h}` or {math}`P_{v}`.
This implies that {math}`P_{proj,d}` must be a function of {math}`P_{d}` and {math}`P_{w}`. Since the 
transformation is projective, the depth component must transform affinely. Since {math}`P_{w} = 1`. This leaves

```{math}
P_{proj,d} = \theta \left( P_{d} \right) = A^{\prime} P_{d} + B^{\prime}.
```

Taken together, the projected coordinates components of the perspective transformation are

```{math}
:label: eq_persp_proj1
P_{proj,h} &= n P_{h} \\
P_{proj,v} &= n P_{v} \\
P_{proj,d} &= \theta \left ( P_{d} \right) = A^{\prime} P_{d} + B^{\prime} \\
P_{proj,w} &= P_{d} \\
```

where we solve for the constants {math}`A^{\prime}` and {math}`B^{\prime}` later.

After projection, we must map the projected coordinates to clip coordinates. To obtain the right
clip coordinates, we need to consider normalized device coordinates. This is necessary since the 
mapping from projected coordinates to clip coordinates depends on the choice of parametrization 
of the canonical view volume. The resulting transformation is an orthographic transformation that 
maps the orthographic view volume {math}`[-l, r] \times [-b, t] \times [n, f]` to the canonical view volume 
{math}`[\alpha_{min}, \alpha_{max}] \times [\beta_{min}, \beta_{max}] \times [\gamma_{min}, \gamma_{max}]`.
The map from projected coordinates to clip coordinates is a linear map in {math}`\mathbb{RP}^{3}`, so 
the coordinates must transform affinely.

We need a set of maps {math}`\phi_{h}, \phi_{v}, \phi_{d}, \phi_{w} : \mathbb{R}^{3} \rightarrow \mathbb{R}` such that

```{math}
:label: eq_persp_ndc1
P_{ndc,h} &= \frac{P_{clip,h}}{P_{clip,w}} 
          &&= \left( H_{w} \circ \phi_{h} \right) \left( P_{proj} \right) 
          &&= \phi_{h} \left( P_{proj} \right) \left( \frac{1}{\phi_{w} \left( P_{proj} \right) } \right) 
          &&\equiv \xi_{h} \left( P \right) \\
P_{ndc,v} &= \frac{P_{clip,v}}{P_{clip,w}} 
          &&= \left( H_{w} \circ \phi_{v} \right) \left( P_{proj} \right) 
          &&= \phi_{v} \left( P_{proj} \right) \left( \frac{1}{\phi_{w} \left( P_{proj} \right) } \right) 
          &&\equiv \xi_{v} \left( P \right) \\
P_{ndc,d} &= \frac{P_{clip,d}}{P_{clip,w}}
          &&= \left( H_{w} \circ \phi_{d} \right) \left( P_{proj} \right) 
          &&= \phi_{d} \left( P_{proj} \right) \left( \frac{1}{\phi_{w} \left( P_{proj} \right) } \right)
          &&\equiv \xi_{d} \left( P \right) \\
P_{ndc,w} &= \frac{P_{clip,w}}{P_{clip,w}}
          &&= \left( H_{w} \circ \phi_{w} \right) \left( P_{proj} \right)
          &&= \phi_{w} \left( P_{proj} \right) \left( \frac{1}{\phi_{w} \left( P_{proj} \right)} \right) 
          &&= 1 \\
```

where {math}`P_{proj}` denotes a point {math}`P \in \mathbb{R}^{3}` in projected coordinates,
{math}`P_{clip,w} = \phi_{w}(P_{proj})` is an affine scalar function, and 
{math}`H_{w} : \mathbb{R}^{4} \rightarrow \mathbb{R}^{4}` denotes homogenization by the {math}`w` component.
Observe that the functions {math}`\xi_{h}, \xi_{v}, \xi_{d} : \mathbb{R}^{3} \rightarrow \mathbb{R}`
on the right-hand side of {ref}`eq_persp_ndc1` are projective functions of the view space point {math}`P`.
This implies that the clip coordinates must be affine functions of {math}`P_{proj}`. In particular,

```{math}
P_{clip,h} &= \phi_{h} \left( P_{proj} \right) \\
P_{clip,v} &= \phi_{v} \left( P_{proj} \right) \\
P_{clip,d} &= \phi_{d} \left( P_{proj} \right) \\
P_{clip,w} &= \phi_{w} \left( P_{proj} \right)
```

is the form of the clip components. Since the purpose of the {math}`w` coordinate is to perform perspective 
division, this implies that

```{math}
\phi_{w} \left( P_{proj} \right) = P_{d}
```

so that the equations in {ref}`eq_persp_ndc1` become

```{math}
:label: eq_persp_ndc2
P_{ndc,h} &= \xi_{h} \left( P \right) = \phi_{h} \left( P_{proj} \right) \left( \frac{1}{ P_{d} } \right) \\
P_{ndc,v} &= \xi_{v} \left( P \right) = \phi_{v} \left( P_{proj} \right) \left( \frac{1}{ P_{d} } \right) \\
P_{ndc,d} &= \xi_{d} \left( P \right) = \phi_{d} \left( P_{proj} \right) \left( \frac{1}{ P_{d} } \right) \\
```

To derive the perspective projection matrix, we solve for the clip coordinate functions {math}`\phi_{h}, \phi_{v}, \phi_{d}`
indirectly using the auxiliary functions {math}`\xi_{h}, \xi_{v}, \xi_{d}` in {ref}`eq_persp_ndc2`, and
use the constraints on the orthographic view volume to compute the auxiliary functions. To establish constraints, we need
to talk about some well chosen points. We need to construct the maps {math}`\xi_{h}, \xi_{v}, \xi_{d}` such that
the parametrization of the orthographic view volume maps to the parametrization of the canonical view volume.
That it, such that coordinates map as 
{math}`-l \mapsto \alpha_{min}, r \mapsto \alpha_{max}, -b \mapsto beta_{min}, t \mapsto \beta_{max}, n \mapsto \gamma_{min}, f \mapsto \gamma_{max}`.
Consider the points in view coordinates

```{math}
Q_{left}   &= -l \mathbf{\hat{u}}_{h} + n \mathbf{\hat{u}}_{d} \\
Q_{right}  &=  r \mathbf{\hat{u}}_{h} + n \mathbf{\hat{u}}_{d} \\
Q_{bottom} &= -b \mathbf{\hat{u}}_{v} + n \mathbf{\hat{u}}_{d} \\
Q_{top}    &=  t \mathbf{\hat{u}}_{v} + n \mathbf{\hat{u}}_{d} \\
Q_{near}   &=  n \mathbf{\hat{u}}_{d}                          \\
Q_{far}    &=  f \mathbf{\hat{u}}_{d}                          \\
```

The points {math}`Q_{near}` and {math}`Q_{far}` are the points along the viewing axis
that intersect the near plane and the far plane, respectively, of the perspective view volume. The 
point {math}`Q_{left}` represents the point of intersection of the left plane, near plane, and the 
horizontal-vertical plane of the view volume. The point {math}`Q_{right}` represents the point of 
intersection of the right plane, near plane, and the horizontal-vertical plane of the view volume.
The point {math}`Q_{bottom}` represents the point of intersection of the bottom plane, near plane, 
and depth-vertical plane of the view volume. The point {math}`Q_{top}` represents the point of 
intersection of the top plane, near plane, and the depth-vertical plane of the view volume. In short,
the points {math}`Q_{near}` and {math}`Q_{far}` are the origins of the near and far planes, respectively.
The other four points and points chosen along the edge of the viewport in the near plane that allow us
to easily set up the boundary conditions to compute the functions {math}`\xi_{h}, \xi_{v}, \xi_{d}`.

We now calculate the auxiliary functions. Consider the map {math}`\xi_{h}`, where

```{math}
\xi_{h} \left( P \right) 
    &= \left( A P_{proj,h} + B P_{proj,v} + C^{\prime} P_{proj,d} + D^{\prime} \right) \left( \frac{1}{P_{d}} \right) \\
    &= \left( A P_{proj,h} + B P_{proj,v} + C^{\prime} \theta \left( P_{d} \right) + D^{\prime} \right) \left( \frac{1}{P_{d}} \right) \\
    &= \left( A P_{proj,h} + B P_{proj,v} + C^{\prime} \left( A^{\prime} P_{d} + B^{\prime} \right) + D^{\prime} \right) \left( \frac{1}{P_{d}} \right) \\
    &= \left( A P_{proj,h} + B P_{proj,v} + C^{\prime} A^{\prime} P_{d} + C^{\prime} B^{\prime} + D^{\prime} \right) \left( \frac{1}{P_{d}} \right) \\
    &= \left( A P_{proj,h} + B P_{proj,v} + \left( C^{\prime} A^{\prime} \right) P_{d} + \left( C^{\prime} B^{\prime} + D^{\prime} \right) \right) \left( \frac{1}{P_{d}} \right) \\
    &= \left( A P_{proj,h} + B P_{proj,v} + C P_{d} + D \right) \left( \frac{1}{P_{d}} \right) \\
    &= \left( A n P_{h} + B n P_{v} + C P_{d} + D \right) \left( \frac{1}{P_{d}} \right) \\
    &= A n P_{h} \left( \frac{1}{P_{d}} \right) + B n P_{v} \left( \frac{1}{P_{d}} \right) + C + D \left( \frac{1}{P_{d}} \right) \\
```

where {math}`C = C^{\prime} A^{\prime}` and {math}`D = C^{\prime} B^{\prime} + D^{\prime}`, so that

```{math}
:label: xi_h_persp_def
\xi_{h} \left( P \right) = A n P_{h} \left( \frac{1}{P_{d}} \right) + B n P_{v} \left( \frac{1}{P_{d}} \right) + C + D \left( \frac{1}{P_{d}} \right).
```

Define the boundary conditions for our chosen points

```{math}
\xi_{h} \left( Q_{left} \right)   &= \alpha_{min} \\ 
\xi_{h} \left( Q_{right} \right)  &= \alpha_{max} \\
\xi_{h} \left( Q_{bottom} \right) &= 0 \\
\xi_{h} \left( Q_{top} \right)    &= 0 \\
\xi_{h} \left( Q_{near} \right)   &= 0 \\
\xi_{h} \left( Q_{far} \right)    &= 0 \\
```

which we need to justify. The view space coordinates are orthogonal to each other, and the normalized device coordinates
are also orthogonal to each other. This means that {math}`\xi_{h}` should only be a function of the horizontal component
and not the vertical component. The points {math}`Q_{bottom}, Q_{top}, Q_{near}, Q_{far}` lie on the depth-vertical plane, which 
have a zero horizontal component, so they should keep a zero horizontal component after transformation.

Applying the boundary conditions, we have

```{math}
\xi_{h} \left( Q_{left} \right)
    &= A n \cdot \left( -l \right) \cdot\left( \frac{1}{n} \right) + B n \cdot 0 \cdot \left( \frac{1}{n} \right) + C + D \left( \frac{1}{n} \right) \\
    &= -A l + C + D \left( \frac{1}{n} \right) \\
    &= \alpha_{min} \\
\xi_{h} \left( Q_{right} \right) 
    &= A n \cdot r \cdot \left( \frac{1}{n} \right) + B n \cdot 0 \cdot \left( \frac{1}{n} \right) + C + D \left( \frac{1}{n} \right) \\
    &= A r + C + D \left( \frac{1}{n} \right) \\ 
    &= \alpha_{max} \\
\xi_{h} \left( Q_{bottom} \right) 
    &= A n \cdot 0 \cdot \left( \frac{1}{n} \right) + B n \cdot \left( -b \right) \cdot \left( \frac{1}{n} \right) + C + D \left( \frac{1}{n} \right) \\
    &= -B b + C + D \left( \frac{1}{n} \right) \\
    &= 0 \\
\xi_{h} \left( Q_{top} \right)
    &= A n \cdot 0 \cdot \left( \frac{1}{n} \right) + B n \cdot t \cdot \left( \frac{1}{n} \right) + C + D \left( \frac{1}{n} \right) \\
    &= B t + C + D \left( \frac{1}{n} \right) \\
    &= 0 \\
\xi_{h} \left( Q_{near} \right)
    &= A n \cdot 0 \cdot \left( \frac{1}{n} \right) + B n \cdot 0 \cdot \left( \frac{1}{n} \right) + C + D \left( \frac{1}{n} \right) \\
    &= C + D \left( \frac{1}{n} \right) \\
    &= 0 \\
\xi_{h} \left( Q_{far} \right)
    &= A n \cdot 0 \cdot \left( \frac{1}{f} \right) + B n \cdot 0 \cdot \left( \frac{1}{f} \right) + C + D \left( \frac{1}{f} \right) \\
    &= C + D \left( \frac{1}{f} \right) \\
    &= 0 \\
```

so that

```{math}
:label: xi_h_persp_constraints
\xi_{h} \left( Q_{left} \right)
    &= -A l + C + D \left( \frac{1}{n} \right)
    &&= \alpha_{min} \\
\xi_{h} \left( Q_{right} \right) 
    &= A r + C + D \left( \frac{1}{n} \right)
    &&= \alpha_{max} \\
\xi_{h} \left( Q_{bottom} \right) 
    &= -B b + C + D \left( \frac{1}{n} \right)
    &&= 0 \\
\xi_{h} \left( Q_{top} \right)
    &= B t + C + D \left( \frac{1}{n} \right)
    &&= 0 \\
\xi_{h} \left( Q_{near} \right)
    &= C + D \left( \frac{1}{n} \right)
    &&= 0 \\
\xi_{h} \left( Q_{far} \right)
    &= C + D \left( \frac{1}{f} \right)
    &&= 0 \\
```

and now we compute the constants. Subtracting {math}`\xi_{h} \left( Q_{right} \right)` from 
{math}`\xi_{h} \left( Q_{left} \right)` in {ref}`xi_h_persp_constraints` yields

```{math}
\xi_{h} \left( Q_{right} \right) - \xi_{h} \left( Q_{left} \right) 
    &= \left[ A r + C + D \left( \frac{1}{n} \right) \right] - \left[ -A l + C + D \left( \frac{1}{n} \right) \right] \\
    &= A r - \left( - A l \right) \\
    &= A \left( r - \left( -l \right) \right) \\
    &= \alpha_{max} - \alpha_{min}.
```

Solving for {math}`A`, we see that

```{math}
:label: xi_h_persp_constant_a
A = \frac{\alpha_{max} - \alpha_{min}}{r - \left( -l \right)}.
```

Subtracting {math}`\xi_{h} \left( Q_{top} \right)` from 
{math}`\xi_{h} \left( Q_{bottom} \right)` in {ref}`xi_h_persp_constraints` yields

```{math}
\xi_{h} \left( Q_{top} \right) - \xi_{h} \left( Q_{bottom} \right)
    &= \left[ B t + C + D \left( \frac{1}{n} \right) \right] - \left[ -B b + C + D \left( \frac{1}{n} \right) \right] \\
    &= B t - B \left( -b \right) \\
    &= B \left( t - \left( -b \right) \right) \\
    &= 0.
```

Solving for {math}`B`, we see that 

```{math}
:label: xi_h_persp_constant_b
B = 0
```

since {math}`t - (-b) = t + b \neq 0`. Subtracting {math}`\xi_{h} \left( Q_{far} \right)` from
{math}`\xi_{h} \left( Q_{near} \right)` in {ref}`xi_h_persp_constraints` yields

```{math}
\xi_{h} \left( Q_{far} \right) - \xi_{h} \left( Q_{near} \right)
    &= \left[ C + D \left( \frac{1}{f} \right) \right] - \left[ C + D \left( \frac{1}{n} \right) \right] \\
    &= D \left( \frac{1}{f} - \frac{1}{n} \right) \\
    &= 0.
```

Solving for {math}`D`, we see that

```{math}
:label: xi_h_persp_constant_d
D = 0
```

since {math}`\frac{1}{f} - \frac{1}{n} \neq 0`. Substituting the constants {ref}`xi_h_persp_constant_a`,
{ref}`xi_h_persp_constant_b`, and {ref}`xi_h_persp_constant_d` back into {math}`\xi_{h}(Q_{right})` in 
{ref}`xi_h_persp_constraints` gives us

```{math}
\xi_{h} \left( Q_{right} \right) 
    = A r + C + D \left( \frac{1}{n} \right)
    = A r + C 
    = \left( \frac{\alpha_{max} - \alpha_{min}}{r - \left( -l \right)} \right) r + C
    = \alpha_{max}.
```

Solving for {math}`C`, we see that

```{math}
C &= \alpha_{max} - \left( \frac{\alpha_{max} - \alpha_{min}}{r - \left( -l \right)} \right) r \\
  &= \alpha_{max} - \left( \frac{\alpha_{max} - \alpha_{min}}{r + l} \right) r \\
  &= \frac{\alpha_{max} \left( r + l \right) - \left( \alpha_{max} - \alpha_{min} \right) r}{r + l} \\
  &= \frac{\alpha_{max} r + \alpha_{max} l - \alpha_{max} r + \alpha_{min} r}{r + l} \\
  &= \frac{\alpha_{max} r + \alpha_{max} l - \alpha_{max} r + \alpha_{min} r}{r + l} \\
  &= \frac{\alpha_{max} l + \alpha_{min} r}{r + l} \\
  &= \frac{\alpha_{min} r - \alpha_{max} \left( -l \right)}{r - \left( -l \right)} \\
```

therefore

```{math}
:label: xi_h_persp_constant_c
C =  \frac{\alpha_{min} r - \alpha_{max} \left( -l \right)}{r - \left( -l \right)}.
```

Assembling the constants {ref}`xi_h_persp_constant_a`, {ref}`xi_h_persp_constant_b`, {ref}`xi_h_persp_constant_c`, 
{ref}`xi_h_persp_constant_d` back into {ref}`xi_h_persp_def` we have the complete formula for the 
auxiliary function {math}`\xi_{h}`

```{math}
:label: xi_h_persp_final
\xi_{h} \left( P \right) 
    = \left( \frac{ \left( \alpha_{max} - \alpha_{min} \right) n }{ r - \left( -l \right)} \right) P_{h} \left( \frac{1}{P_{d}} \right)
    + \frac{\alpha_{min} r - \alpha_{max} \left( -l \right)}{r - \left( -l \right)}.
```

Consider the map {math}`\xi_{v}`, where

```{math}
\xi_{v} \left( P \right) 
    &= \left( A P_{proj,h} + B P_{proj,v} + C^{\prime} P_{proj,d} + D^{\prime} \right) \left( \frac{1}{P_{d}} \right) \\
    &= \left( A P_{proj,h} + B P_{proj,v} + C^{\prime} \theta \left( P_{d} \right) + D^{\prime} \right) \left( \frac{1}{P_{d}} \right) \\
    &= \left( A P_{proj,h} + B P_{proj,v} + C^{\prime} \left( A^{\prime} P_{d} + B^{\prime} \right) + D^{\prime} \right) \left( \frac{1}{P_{d}} \right) \\
    &= \left( A P_{proj,h} + B P_{proj,v} + C^{\prime} A^{\prime} P_{d} + C^{\prime} B^{\prime} + D^{\prime} \right) \left( \frac{1}{P_{d}} \right) \\
    &= \left( A P_{proj,h} + B P_{proj,v} + \left( C^{\prime} A^{\prime} \right) P_{d} + \left( C^{\prime} B^{\prime} + D^{\prime} \right) \right) \left( \frac{1}{P_{d}} \right) \\
    &= \left( A P_{proj,h} + B P_{proj,v} + C P_{d} + D \right) \left( \frac{1}{P_{d}} \right) \\
    &= \left( A n P_{h} + B n P_{v} + C P_{d} + D \right) \left( \frac{1}{P_{d}} \right) \\
    &= A n P_{h} \left( \frac{1}{P_{d}} \right) + B n P_{v} \left( \frac{1}{P_{d}} \right) + C + D \left( \frac{1}{P_{d}} \right) \\
```

where {math}`C = C^{\prime} A^{\prime}` and {math}`D = C^{\prime} B^{\prime} + D^{\prime}`, so that

```{math}
:label: xi_v_persp_def
\xi_{v} \left( P \right) = A n P_{h} \left( \frac{1}{P_{d}} \right) + B n P_{v} \left( \frac{1}{P_{d}} \right) + C + D \left( \frac{1}{P_{d}} \right).
```

Define the boundary conditions for our chosen points

```{math}
\xi_{v} \left( Q_{left} \right)   &= 0 \\ 
\xi_{v} \left( Q_{right} \right)  &= 0 \\
\xi_{v} \left( Q_{bottom} \right) &= \beta_{min} \\
\xi_{v} \left( Q_{top} \right)    &= \beta_{max} \\
\xi_{v} \left( Q_{near} \right)   &= 0 \\
\xi_{v} \left( Q_{far} \right)    &= 0 \\
```

which we need to justify. The view space coordinates are orthogonal to each other, and the normalized device coordinates
are also orthogonal to each other. This means that {math}`\xi_{v}` should only be a function of the vertical component
and not the horizontal component. The points {math}`Q_{left}, Q_{right}, Q_{near}, Q_{far}` lie on the depth-horizontal plane, which 
have a zero vertical component, so they should keep a zero vertical component after transformation.

Applying the boundary conditions, we have

```{math}
\xi_{v} \left( Q_{left} \right)
    &= A n \cdot \left( -l \right) \cdot\left( \frac{1}{n} \right) + B n \cdot 0 \cdot \left( \frac{1}{n} \right) + C + D \left( \frac{1}{n} \right) \\
    &= -A l + C + D \left( \frac{1}{n} \right) \\
    &= 0 \\
\xi_{v} \left( Q_{right} \right) 
    &= A n \cdot r \cdot \left( \frac{1}{n} \right) + B n \cdot 0 \cdot \left( \frac{1}{n} \right) + C + D \left( \frac{1}{n} \right) \\
    &= A r + C + D \left( \frac{1}{n} \right) \\ 
    &= 0 \\
\xi_{v} \left( Q_{bottom} \right) 
    &= A n \cdot 0 \cdot \left( \frac{1}{n} \right) + B n \cdot \left( -b \right) \cdot \left( \frac{1}{n} \right) + C + D \left( \frac{1}{n} \right) \\
    &= -B b + C + D \left( \frac{1}{n} \right) \\
    &= \beta_{min} \\
\xi_{v} \left( Q_{top} \right)
    &= A n \cdot 0 \cdot \left( \frac{1}{n} \right) + B n \cdot t \cdot \left( \frac{1}{n} \right) + C + D \left( \frac{1}{n} \right) \\
    &= B t + C + D \left( \frac{1}{n} \right) \\
    &= \beta_{max} \\
\xi_{v} \left( Q_{near} \right)
    &= A n \cdot 0 \cdot \left( \frac{1}{n} \right) + B n \cdot 0 \cdot \left( \frac{1}{n} \right) + C + D \left( \frac{1}{n} \right) \\
    &= C + D \left( \frac{1}{n} \right) \\
    &= 0 \\
\xi_{v} \left( Q_{far} \right)
    &= A n \cdot 0 \cdot \left( \frac{1}{f} \right) + B n \cdot 0 \cdot \left( \frac{1}{f} \right) + C + D \left( \frac{1}{f} \right) \\
    &= C + D \left( \frac{1}{f} \right) \\
    &= 0 \\
```

so that

```{math}
:label: xi_v_persp_constraints
\xi_{v} \left( Q_{left} \right)
    &= -A l + C + D \left( \frac{1}{n} \right)
    &&= 0 \\
\xi_{v} \left( Q_{right} \right) 
    &= A r + C + D \left( \frac{1}{n} \right)
    &&= 0 \\
\xi_{v} \left( Q_{bottom} \right) 
    &= -B b + C + D \left( \frac{1}{n} \right)
    &&= \beta_{min} \\
\xi_{v} \left( Q_{top} \right)
    &= B t + C + D \left( \frac{1}{n} \right)
    &&= \beta_{max} \\
\xi_{v} \left( Q_{near} \right)
    &= C + D \left( \frac{1}{n} \right)
    &&= 0 \\
\xi_{v} \left( Q_{far} \right)
    &= C + D \left( \frac{1}{f} \right)
    &&= 0 \\
```

and now we compute the constants.  Subtracting {math}`\xi_{v} \left( Q_{right} \right)` from 
{math}`\xi_{v} \left( Q_{left} \right)` in {ref}`xi_v_persp_constraints` yields

```{math}
\xi_{v} \left( Q_{right} \right) - \xi_{v} \left( Q_{left} \right) 
    &= \left[ A r + C + D \left( \frac{1}{n} \right) \right] - \left[ -A l + C + D \left( \frac{1}{n} \right) \right] \\
    &= A r - \left( - A l \right) \\
    &= A \left( r - \left( -l \right) \right) \\
    &= 0.
```

Solving for {math}`A`, we see that

```{math}
:label: xi_v_persp_constant_a
A = 0
```

since {math}`r - (-l) = r + l \neq 0`. Subtracting {math}`\xi_{v} \left( Q_{top} \right)` from 
{math}`\xi_{v} \left( Q_{bottom} \right)` in {ref}`xi_v_persp_constraints` yields

```{math}
\xi_{v} \left( Q_{top} \right) - \xi_{v} \left( Q_{bottom} \right)
    &= \left[ B t + C + D \left( \frac{1}{n} \right) \right] - \left[ -B b + C + D \left( \frac{1}{n} \right) \right] \\
    &= B t - B \left( -b \right) \\
    &= B \left( t - \left( -b \right) \right) \\
    &= \beta_{max} - \beta_{min}.
```

Solving for {math}`B`, we have

```{math}
:label: xi_v_persp_constant_b
B = \frac{\beta_{max} - \beta_{min}}{t - \left( -b \right)}.
```

Subtracting {math}`\xi_{v} \left( Q_{far} \right)` from
{math}`\xi_{v} \left( Q_{near} \right)` in {ref}`xi_v_persp_constraints` yields

```{math}
\xi_{v} \left( Q_{far} \right) - \xi_{v} \left( Q_{near} \right)
    &= \left[ C + D \left( \frac{1}{f} \right) \right] - \left[ C + D \left( \frac{1}{n} \right) \right] \\
    &= D \left( \frac{1}{f} - \frac{1}{n} \right) \\
    &= 0.
```

Solving for {math}`D`, we see that

```{math}
:label: xi_v_persp_constant_d
D = 0
```

since {math}`\frac{1}{f} - \frac{1}{n} \neq 0`. Substituting the constants {ref}`xi_v_persp_constant_a`,
{ref}`xi_v_persp_constant_b`, and {ref}`xi_v_persp_constant_d` back into {math}`\xi_{v}(Q_{right})` in 
{ref}`xi_v_persp_constraints` gives us

```{math}
\xi_{v} \left( Q_{top} \right) 
    = B t + C + D \left( \frac{1}{n} \right)
    = B t + C 
    = \left( \frac{\beta_{max} - \beta_{min}}{t - \left( -b \right)} \right) t + C
    = \beta_{max}.
```

Solving for {math}`C`, we see that

```{math}
C &= \beta_{max} - \left( \frac{\beta_{max} - \beta_{min}}{t - \left( -b \right)} \right) t \\
  &= \beta_{max} - \left( \frac{\beta_{max} - \beta_{min}}{t + b} \right) t \\
  &= \frac{\beta_{max} \left( t + b \right) - \left( \beta_{max} - \beta_{min} \right) t}{t + b} \\
  &= \frac{\beta_{max} t + \beta_{max} b - \beta_{max} t + \beta_{min} t}{t + b} \\
  &= \frac{\beta_{max} t + \beta_{max} b - \beta_{max} t + \beta_{min} t}{t + b} \\
  &= \frac{\beta_{max} b + \beta_{min} t}{t + b} \\
  &= \frac{\beta_{min} t - \beta_{max} \left( -b \right)}{t - \left( -b \right)} \\
```

therefore

```{math}
:label: xi_v_persp_constant_c
C =  \frac{\beta_{min} t - \beta_{max} \left( -b \right)}{t - \left( -b \right)}.
```

Assembling the constants {ref}`xi_v_persp_constant_a`, {ref}`xi_v_persp_constant_b`, {ref}`xi_v_persp_constant_c`, 
{ref}`xi_v_persp_constant_d` back into {ref}`xi_v_persp_def` we have the complete formula for the 
auxiliary function {math}`\xi_{v}`

```{math}
:label: xi_v_persp_final
\xi_{v} \left( P \right) 
    = \left( \frac{ \left( \beta_{max} - \beta_{min} \right) n }{ t - \left( -b \right)} \right) P_{v} \left( \frac{1}{P_{d}} \right)
    + \frac{\beta_{min} t - \beta_{max} \left( -b \right)}{t - \left( -b \right)}.
```

Consider the map {math}`\xi_{d}`, where

```{math}
\xi_{d} \left( P \right) 
    &= \left( A P_{proj,h} + B P_{proj,v} + C^{\prime} P_{proj,d} + D^{\prime} \right) \left( \frac{1}{P_{d}} \right) \\
    &= \left( A P_{proj,h} + B P_{proj,v} + C^{\prime} \theta \left( P_{d} \right) + D^{\prime} \right) \left( \frac{1}{P_{d}} \right) \\
    &= \left( A P_{proj,h} + B P_{proj,v} + C^{\prime} \left( A^{\prime} P_{d} + B^{\prime} \right) + D^{\prime} \right) \left( \frac{1}{P_{d}} \right) \\
    &= \left( A P_{proj,h} + B P_{proj,v} + C^{\prime} A^{\prime} P_{d} + C^{\prime} B^{\prime} + D^{\prime} \right) \left( \frac{1}{P_{d}} \right) \\
    &= \left( A P_{proj,h} + B P_{proj,v} + \left( C^{\prime} A^{\prime} \right) P_{d} + \left( C^{\prime} B^{\prime} + D^{\prime} \right) \right) \left( \frac{1}{P_{d}} \right) \\
    &= \left( A P_{proj,h} + B P_{proj,v} + C P_{d} + D \right) \left( \frac{1}{P_{d}} \right) \\
    &= \left( A n P_{h} + B n P_{v} + C P_{d} + D \right) \left( \frac{1}{P_{d}} \right) \\
    &= A n P_{h} \left( \frac{1}{P_{d}} \right) + B n P_{v} \left( \frac{1}{P_{d}} \right) + C + D \left( \frac{1}{P_{d}} \right) \\
```

where {math}`C = C^{\prime} A^{\prime}` and {math}`D = C^{\prime} B^{\prime} + D^{\prime}`, so that

```{math}
:label: xi_d_persp_def
\xi_{d} \left( P \right) = A n P_{h} \left( \frac{1}{P_{d}} \right) + B n P_{v} \left( \frac{1}{P_{d}} \right) + C + D \left( \frac{1}{P_{d}} \right).
```

Define the boundary conditions for our chosen points

```{math}
\xi_{d} \left( Q_{left} \right)   &= 0 \\ 
\xi_{d} \left( Q_{right} \right)  &= 0 \\
\xi_{d} \left( Q_{bottom} \right) &= 0 \\
\xi_{d} \left( Q_{top} \right)    &= 0 \\
\xi_{d} \left( Q_{near} \right)   &= \gamma_{min} \\
\xi_{d} \left( Q_{far} \right)    &= \gamma_{max} \\
```

which we need to justify. The view space coordinates are orthogonal to each other, and the normalized device coordinates
are also orthogonal to each other. This means that {math}`\xi_{d}` should only be a function of the depth component
and not the horizontal or vertical components. The points {math}`Q_{left}, Q_{right}, Q_{bottom}, Q_{top}` lie on the near plane, which 
have a depth component of {math}`n`. Since perspective projection projects depth components onto the near plane,
points already on the near plane should stay there. Moreover, points on the far plane should stay on the far
plane to satisfy to transform the perspective view volume into the orthographic view volume correctly. Consequently, the depth component should have no dependence on the horizontal or vertical components, only
a depth term and an affine translation term.

Applying the boundary conditions, we have

```{math}
\xi_{d} \left( Q_{left} \right)
    &= A n \cdot \left( -l \right) \cdot\left( \frac{1}{n} \right) + B n \cdot 0 \cdot \left( \frac{1}{n} \right) + C + D \left( \frac{1}{n} \right) \\
    &= -A l + C + D \left( \frac{1}{n} \right) \\
    &= 0 \\
\xi_{d} \left( Q_{right} \right) 
    &= A n \cdot r \cdot \left( \frac{1}{n} \right) + B n \cdot 0 \cdot \left( \frac{1}{n} \right) + C + D \left( \frac{1}{n} \right) \\
    &= A r + C + D \left( \frac{1}{n} \right) \\ 
    &= 0 \\
\xi_{d} \left( Q_{bottom} \right) 
    &= A n \cdot 0 \cdot \left( \frac{1}{n} \right) + B n \cdot \left( -b \right) \cdot \left( \frac{1}{n} \right) + C + D \left( \frac{1}{n} \right) \\
    &= -B b + C + D \left( \frac{1}{n} \right) \\
    &= 0 \\
\xi_{d} \left( Q_{top} \right)
    &= A n \cdot 0 \cdot \left( \frac{1}{n} \right) + B n \cdot t \cdot \left( \frac{1}{n} \right) + C + D \left( \frac{1}{n} \right) \\
    &= B t + C + D \left( \frac{1}{n} \right) \\
    &= 0 \\
\xi_{d} \left( Q_{near} \right)
    &= A n \cdot 0 \cdot \left( \frac{1}{n} \right) + B n \cdot 0 \cdot \left( \frac{1}{n} \right) + C + D \left( \frac{1}{n} \right) \\
    &= C + D \left( \frac{1}{n} \right) \\
    &= \gamma_{min} \\
\xi_{d} \left( Q_{far} \right)
    &= A n \cdot 0 \cdot \left( \frac{1}{f} \right) + B n \cdot 0 \cdot \left( \frac{1}{f} \right) + C + D \left( \frac{1}{f} \right) \\
    &= C + D \left( \frac{1}{f} \right) \\
    &= \gamma_{max} \\
```

so that

```{math}
:label: xi_d_persp_constraints
\xi_{d} \left( Q_{left} \right)
    &= -A l + C + D \left( \frac{1}{n} \right)
    &&= \gamma_{min} \\
\xi_{d} \left( Q_{right} \right) 
    &= A r + C + D \left( \frac{1}{n} \right)
    &&= \gamma_{min} \\
\xi_{d} \left( Q_{bottom} \right) 
    &= -B b + C + D \left( \frac{1}{n} \right)
    &&= \gamma_{min} \\
\xi_{d} \left( Q_{top} \right)
    &= B t + C + D \left( \frac{1}{n} \right)
    &&= \gamma_{min} \\
\xi_{d} \left( Q_{near} \right)
    &= C + D \left( \frac{1}{n} \right)
    &&= \gamma_{min} \\
\xi_{d} \left( Q_{far} \right)
    &= C + D \left( \frac{1}{f} \right)
    &&= \gamma_{max} \\
```

and now we compute the constants. and now we compute the constants. Subtracting {math}`\xi_{d} \left( Q_{right} \right)` from 
{math}`\xi_{d} \left( Q_{left} \right)` in {ref}`xi_d_persp_constraints` yields

```{math}
\xi_{d} \left( Q_{right} \right) - \xi_{d} \left( Q_{left} \right) 
    &= \left[ A r + C + D \left( \frac{1}{n} \right) \right] - \left[ -A l + C + D \left( \frac{1}{n} \right) \right] \\
    &= A r - \left( - A l \right) \\
    &= A \left( r - \left( -l \right) \right) \\
    &= \gamma_{min} -  \gamma_{min} \\
    &= 0.
```

Solving for {math}`A`, we see that

```{math}
:label: xi_d_persp_constant_a
A = 0
```

since {math}`r - (-l) = r + l \neq 0`. Subtracting {math}`\xi_{d} \left( Q_{top} \right)` from 
{math}`\xi_{d} \left( Q_{bottom} \right)` in {ref}`xi_h_persp_constraints` yields

```{math}
\xi_{d} \left( Q_{top} \right) - \xi_{d} \left( Q_{bottom} \right)
    &= \left[ B t + C + D \left( \frac{1}{n} \right) \right] - \left[ -B b + C + D \left( \frac{1}{n} \right) \right] \\
    &= B t - B \left( -b \right) \\
    &= B \left( t - \left( -b \right) \right) \\
    &= \gamma_{min} - \gamma_{min} \\
    &= 0.
```

Solving for {math}`B`, we see that 

```{math}
:label: xi_d_persp_constant_b
B = 0
```

since {math}`t - (-b) = t + b \neq 0`. Subtracting {math}`\xi_{d} \left( Q_{far} \right)` from
{math}`\xi_{d} \left( Q_{near} \right)` in {ref}`xi_d_persp_constraints` yields

```{math}
\xi_{d} \left( Q_{far} \right) - \xi_{d} \left( Q_{near} \right)
    &= \left[ C + D \left( \frac{1}{f} \right) \right] - \left[ C + D \left( \frac{1}{n} \right) \right] \\
    &= D \left( \frac{1}{f} - \frac{1}{n} \right) \\
    &= D \left( \frac{n - f}{f n} \right) \\
    &= D \left( -\frac{f - n}{f n} \right) \\
    &= \gamma_{max} - \gamma_{min}.
```

Solving for {math}`D`, we see that

```{math}
:label: xi_d_persp_constant_d
D = -\frac{ \left( \gamma_{max} - \gamma_{min} \right) f n }{f - n}.
```

Substituting the constants {ref}`xi_d_persp_constant_a`,
{ref}`xi_d_persp_constant_b`, and {ref}`xi_d_persp_constant_d` back into {math}`\xi_{d}(Q_{far})` in 
{ref}`xi_d_persp_constraints` gives us

```{math}
\xi_{d} \left( Q_{far} \right) 
    &= C + D \left( \frac{1}{f} \right) \\
    &= C + \left( -\frac{ \left( \gamma_{max} - \gamma_{min} \right) f n }{f - n} \right) \left( \frac{1}{f} \right) \\
    &= C + \left( -\frac{ \left( \gamma_{max} - \gamma_{min} \right) n }{f - n} \right) \\
    &= \gamma_{max}.
```

Solving for {math}`C`

```{math}
C &= \gamma_{max} + \left( \frac{ \left( \gamma_{max} - \gamma_{min} \right) n }{f - n} \right) \\
  &= \frac{\gamma_{max} \left( f - n \right) + \left( \gamma_{max} - \gamma_{min} \right) n}{f - n} \\
  &= \frac{\gamma_{max} f - \gamma_{max} n + \gamma_{max} n - \gamma_{min} n}{f - n} \\
  &= \frac{\gamma_{max} f - \gamma_{min} n}{f - n} \\
```

we have

```{math}
:label: xi_d_persp_constant_c
C = \frac{\gamma_{max} f - \gamma_{min} n}{f - n}.
```

Assembling the constants {ref}`xi_d_persp_constant_a`, {ref}`xi_d_persp_constant_b`, {ref}`xi_d_persp_constant_c`, 
{ref}`xi_d_persp_constant_d` back into {ref}`xi_d_persp_def` we have the complete formula for the 
auxiliary function {math}`\xi_{d}`

```{math}
:label: xi_d_persp_final
\xi_{d} \left( P \right)
    = \frac{\gamma_{max} f - \gamma_{min} n}{f - n} 
    - \left( \frac{ \left( \gamma_{max} - \gamma_{min} \right) f n }{f - n} \right) \left( \frac{1}{P_{d}} \right).
```

Now we return to the definitions for the clip space functions. Recall from {ref}`eq_persp_ndc1`
that 

```{math}
:label: eq_persp_ndc2
P_{ndc,h} \equiv \xi_{h} \left( P \right) &\equiv \phi_{h} \left( P_{proj} \right) \left( \frac{1}{\phi_{w} \left( P_{proj} \right) } \right) = \frac{P_{clip,h}}{P_{clip,w}} \\
P_{ndc,v} \equiv \xi_{v} \left( P \right) &\equiv \phi_{v} \left( P_{proj} \right) \left( \frac{1}{\phi_{w} \left( P_{proj} \right) } \right) = \frac{P_{clip,v}}{P_{clip,w}} \\
P_{ndc,d} \equiv \xi_{d} \left( P \right) &\equiv \phi_{d} \left( P_{proj} \right) \left( \frac{1}{\phi_{w} \left( P_{proj} \right) } \right) = \frac{P_{clip,d}}{P_{clip,w}} \\
```

where {math}`P_{proj}` denotes a point {math}`P \in \mathbb{R}^{3}` in projected coordinates, and
{math}`P_{clip,w} = \phi_{w}(P_{proj}) = P_{d}` is an affine scalar function. We now derive the clip 
coordinate functions {math}`\phi_{h}, \phi_{v}, \phi_{d}` given by

```{math}
:label: eq_persp_ndc3
P_{clip,h} &= \phi_{h} \left( P_{proj} \right) \\
P_{clip,v} &= \phi_{v} \left( P_{proj} \right) \\
P_{clip,d} &= \phi_{d} \left( P_{proj} \right) \\
```

by comparing the equations in {ref}`eq_persp_ndc2` with {ref}`xi_h_persp_final`, {ref}`xi_v_persp_final`, and {ref}`xi_d_persp_final`. Consider the affine map {math}`\phi_{h}`. Rearranging terms in {ref}`xi_h_persp_final`,
we have

```{math}
:label: eq_persp_ndc4
P_{ndc,h}
    &= \left( \frac{\alpha_{max} - \alpha_{min}}{r - \left( -l \right)} \right) P_{proj,h}
    + \frac{\alpha_{min} r - \alpha_{min} \left( -l \right)}{r - \left( -l \right)} \\
    &= \left( \frac{\alpha_{max} - \alpha_{min}}{r - \left( -l \right)} \right) n P_{h} \left( \frac{1}{P_{d}} \right) 
    + \frac{\alpha_{min} r - \alpha_{min} \left( -l \right)}{r - \left( -l \right)} \\
    &= \left( \frac{\alpha_{max} - \alpha_{min}}{r - \left( -l \right)} \right) n P_{h} \left( \frac{1}{P_{d}} \right) 
    + \left( \frac{ \alpha_{min} r - \alpha_{min} \left( -l \right) }{r - \left( -l \right)} \right) P_{d} \left( \frac{1}{P_{d}} \right) \\
    &= \left( \frac{ \left( \alpha_{max} - \alpha_{min} \right) n }{r - \left( -l \right)} \right) P_{h} \left( \frac{1}{P_{d}} \right) 
    + \left( \frac{ \alpha_{min} r - \alpha_{min} \left( -l \right) }{r - \left( -l \right)} \right) P_{d} \left( \frac{1}{P_{d}} \right) \\
    &= \left[ \left( \frac{ \left( \alpha_{max} - \alpha_{min} \right) n }{r - \left( -l \right)} \right) P_{h} 
    + \left( \frac{ \alpha_{min} r - \alpha_{min} \left( -l \right) }{r - \left( -l \right)} \right) P_{d} \right] \left( \frac{1}{P_{d}} \right).
```

Comparing {ref}`eq_persp_ndc4` with {ref}`eq_persp_ndc2`, we see that

```{math}
:label: phi_h_persp_final
P_{clip,h} = \phi_{h} \left( P_{proj} \right) = \left( \frac{ \left( \alpha_{max} - \alpha_{min} \right) n }{r - \left( -l \right)} \right) P_{h} 
           + \left( \frac{ \alpha_{min} r - \alpha_{min} \left( -l \right) }{r - \left( -l \right)} \right) P_{d}.
```

Consider the affine map {math}`\phi_{v}`. Rearranging terms in {ref}`xi_v_persp_final` we have

```{math}
:label: eq_persp_ndc5
P_{ndc,v}
    &= \left( \frac{\beta_{max} - \beta_{min}}{t - \left( -b \right)} \right) P_{proj,v} 
    + \frac{\beta_{min} t - \beta_{min} \left( -b \right)}{t - \left( -b \right)} \\
    &= \left( \frac{\beta_{max} - \beta_{min}}{t - \left( -b \right)} \right) n P_{v} \left( \frac{1}{P_{d}} \right) 
    + \frac{\beta_{min} t - \beta_{min} \left( -b \right)}{t - \left( -b \right)} \\
    &= \left( \frac{\beta_{max} - \beta_{min}}{t - \left( -b \right)} \right) n P_{v} \left( \frac{1}{P_{d}} \right) 
    + \left( \frac{ \beta_{min} t - \beta_{min} \left( -b \right) }{t - \left( -b \right)} \right) P_{d} \left( \frac{1}{P_{d}} \right) \\
    &= \left( \frac{ \left( \beta_{max} - \beta_{min} \right) n }{t - \left( -b \right)} \right) P_{v} \left( \frac{1}{P_{d}} \right) 
    + \left( \frac{ \beta_{min} t - \beta_{min} \left( -b \right) }{t - \left( -b \right)} \right) P_{d} \left( \frac{1}{P_{d}} \right) \\
    &= \left[ \left( \frac{ \left( \beta_{max} - \beta_{min} \right) n }{t - \left( -b \right)} \right) P_{v} 
    + \left( \frac{ \beta_{min} t - \beta_{min} \left( -b \right) }{t - \left( -b \right)} \right) P_{d} \right] \left( \frac{1}{P_{d}} \right).
```

Comparing {ref}`eq_persp_ndc5` with {ref}`eq_persp_ndc2` we see that

```{math}
P_{clip,v} = \phi_{v} \left( P_{proj} \right) = \left( \frac{ \left( \beta_{max} - \beta_{min} \right) n }{t - \left( -b \right)} \right) P_{v} 
           + \left( \frac{ \beta_{min} t - \beta_{min} \left( -b \right) }{t - \left( -b \right)} \right) P_{d}.
```

Finally, consider the affine map {math}`\phi_{d}`. Rearranging terms in {ref}`xi_d_persp_final` we have

```{math}
:label: eq_persp_ndc6
P_{ndc,d}
    &= \frac{\gamma_{max} f - \gamma_{min} n}{f - n} 
    - \left( \frac{ \left( \gamma_{max} - \gamma_{min} \right) f n }{f - n} \right) \left( \frac{1}{P_{d}} \right) \\
    &= \left[
            \left( \frac{\gamma_{max} f - \gamma_{min} n}{f - n} \right) P_{d} 
            - \frac{ \left( \gamma_{max} - \gamma_{min} \right) f n }{ f - n }
        \right] 
        \left( \frac{1}{P_{d}} \right)
        \\
```

Comparing {ref}`eq_persp_ndc6` with {ref}`eq_persp_ndc2` we see that

```{math}
:label: phi_d_persp_final
P_{clip,d} 
    = \phi_{d} \left( P_{proj} \right)
    = \left( \frac{\gamma_{max} f - \gamma_{min} n}{f - n} \right) P_{d} 
    - \frac{ \left( \gamma_{max} - \gamma_{min} \right) f n }{ f - n }.
```

Since {math}`\phi_{h}`, {math}`\phi_{v}`, and {math}`\phi_{d}` are the components of
an affine transformation, and normalized device coordinates are a projective function
of the view coordinates with {math}`\phi_{w}(P_{proj}) = P_{d}` as the denominator, the 
perspective projection transformation has the form of the matrix in {ref}`matrix_repr_projective` lifted into 
{math}`\mathbb{RP}^{3}`. The resulting matrix equation for the matrix representation is then


```{math}
\begin{bmatrix} 
    P_{clip,h} \\ 
    P_{clip,v} \\ 
    P_{clip,d} \\ 
    P_{clip,w} \\
\end{bmatrix}
= 
\begin{bmatrix}
    \frac{\left( \alpha_{max} - \alpha_{min} \right) n }{r - \left( -l \right)} 
    & 0 
    & \frac{\alpha_{min}r - \alpha_{max} \left( -l \right)}{r - \left( -l \right)} 
    & 0
    \\
    0 
    & \frac{\left( \beta_{max} - \beta_{min} \right) n }{t - \left( -b \right)} 
    & \frac{\beta_{min}t - \beta_{max} \left( -b \right)}{t - \left( -b \right)} 
    & 0
    \\
    0 
    & 0 
    & \frac{\gamma_{max}f - \gamma_{min}n}{f - n} 
    & -\frac{\left( \gamma_{max} - \gamma_{min} \right) f n }{f - n}
    \\
    0 
    & 0 
    & 1 
    & 0
    \\
\end{bmatrix}
\begin{bmatrix} 
    P_{h} \\ 
    P_{v} \\ 
    P_{d} \\ 
    P_{w} \\
\end{bmatrix}.
```

This yields the first major result, the canonical perspective projection matrix {math}`M^{C}_{per}`.

```{admonition} Perspective Projection Matrix
```{math}
:label: matrix_per_canonical
M^{C}_{per} = 
    \begin{bmatrix}
        \frac{\left( \alpha_{max} - \alpha_{min} \right) n }{r - \left( -l \right)} 
        & 0 
        & \frac{\alpha_{min}r - \alpha_{max} \left( -l \right)}{r - \left( -l \right)} 
        & 0 
        \\
        0 
        & \frac{\left( \beta_{max} - \beta_{min} \right) n }{t - \left( -b \right)} 
        & \frac{\beta_{min}t - \beta_{max} \left( -b \right)}{t - \left( -b \right)} 
        & 0
        \\
        0 
        & 0 
        & \frac{\gamma_{max}f - \gamma_{min}n}{f - n} 
        & -\frac{\left( \gamma_{max} - \gamma_{min} \right) f n }{f - n}
        \\
        0 
        & 0 
        & 1 
        & 0
        \\
    \end{bmatrix}
```

This completes the derivation of the perspective projection matrix.

### The Canonical Orthographic Projection Matrix

We now construct the canonical orthographic projection matrix for 
the frame {math}`(\tilde{O}_{view}, (\mathbf{\hat{u}}_{h}, \mathbf{\hat{u}}_{v}, \mathbf{\hat{u}}_{d}))` with the 
orthographic view volume parametrized by {math}`[-l, r] \times [-b, t] \times [n, f]` and 
the canonical view volume parametrized by 
{math}`[\alpha_{min}, \alpha_{max}] \times [\beta_{min}, \beta_{max}] \times [\gamma_{min}, \gamma_{max}]`.

An orthographic projection works by removing the component parallel to the direction normal
to the plane of projection in three dimensions. Orthographic projections preserve 
the lengths of lines parallel to the projection plane. It produces the component of a geometric 
figure that is parallel to the plane of projection. Unlike perspective projection, there 
is no depth distortion. In terms of our orthonormal frame, an orthographic projection removes the
depth component from a vector. Let {math}`\tilde{P} \in \mathbb{E}^{3}` be a point. The orthographic projection 
is a map {math}`T^{\prime}_{orth} : \mathbb{E}^{3} \rightarrow \mathbb{E}^{3}` given by

```{math}
T^{\prime}_{orth} \left( \tilde{P} \right) = \tilde{P} - \left( \left( \tilde{P} - \tilde{O}_{view} \right) \cdot \mathbf{\hat{n}} \right) \mathbf{\hat{n}}.
```

In the coordinate frame 
{math}`(\tilde{O}_{view}, (\mathbf{\hat{u}}_{h}, \mathbf{\hat{u}}_{v}, \mathbf{\hat{u}}_{d}))`
let {math}`P = \tilde{P} - \tilde{O}_{view}` be a representation of the point {math}`P`
given by {math}`P = P_{h} \mathbf{\hat{u}}_{h} + P_{v} \mathbf{\hat{u}}_{v} + P_{d} \mathbf{\hat{u}}_{d}`.
The orthographic projection has the form

```{math}
T^{\prime}_{orth} \left( P \right) 
    = P - \left( P \cdot \mathbf{\hat{u}}_{d} \right) \mathbf{\hat{u}}_{d}
    = P - P_{d} \mathbf{\hat{u}}_{d}
    = P_{h} \mathbf{\hat{u}}_{h} + P_{v} \mathbf{\hat{u}}_{v}
```

so that {math}`T^{\prime}_{orth}` indeed projects out the depth component as claimed. This is the
usual defintion of orthographic projection. The problem is that we still need depth information for 
rendering, so this information must be preserved in our true projection. Since orthographic projection 
does not distort depth in any way, and indeed has no denominator, it is an affine transformation. Thus the 
orthographic projection needs to carry depth information. We need to add the depth information 
along the normal vector to the projection plane into the transformation to get our projected coordinates. 
Let {math}`T_{orth} : \mathbb{E}^{3} \rightarrow \mathbb{E}^{3}` be the transformation given by

```{math}
T_{orth} \left( \tilde{P} \right) 
    = T^{\prime}_{orth} \left( \tilde{P} \right) 
    + \left( \left( \tilde{P} - \tilde{O}_{view} \right) \cdot \mathbf{\hat{n}} \right) \mathbf{\hat{n}}.
```

Expanding out the definition

```{math}
T_{orth} \left( \tilde{P} \right)
    = \tilde{P} 
    - \left( \left( \tilde{P} - \tilde{O}_{view} \right) \cdot \mathbf{\hat{n}} \right) \mathbf{\hat{n}}
    + \left( \left( \tilde{P} - \tilde{O}_{view} \right) \cdot \mathbf{\hat{n}} \right) \mathbf{\hat{n}}
    = \tilde{P}
    = I \left( P \right)
```

where {math}`I` denotes the identity map {math}`I : \mathbb{E}^{3} \rightarrow \mathbb{E}^{3}`. We see
that the orthographic projection is simply the identity map when the depth information if factored
back in. In components,

```{math}
T_{orth} \left( P \right) = I \left( P \right) = P
```

so that our projected coordinates become

```{math}
P_{proj,h} &= P_{h} \\
P_{proj,v} &= P_{v} \\
P_{proj,d} &= P_{d} \\
P_{proj,w} &= 1     \\
```

Thus we map directly to clip coordinates from view coordinates. To complete the orthographic projection 
transformation, we need an affine transformation that maps the orthographic view volume to the canonical 
view volume in normalized device coordinates. Just like the perspective projection earlier, we infer
the orthographic projection matrix indirectly using constraints on how the orthographic view volume
transforms into the canonical view volume. We require affine maps 
{math}`\phi_{h}, \phi_{v}, \phi_{d}, \phi_{w} : \mathbb{R^{3}} \rightarrow \mathbb{R}` such that

```{math}
:label: eq_orth_clip1
P_{clip,h} &= \phi_{h} \left( P_{proj} \right) \\
P_{clip,v} &= \phi_{v} \left( P_{proj} \right) \\
P_{clip,d} &= \phi_{d} \left( P_{proj} \right) \\
P_{clip,w} &= \phi_{w} \left( P_{proj} \right) \\
```

where we get {math}`\phi_{w}` immediately because orthographic projections are affine maps.
That is

```{math}
:label: phi_w_orth_final
\phi_{w} \left( P \right) = 1.
```

where we use the fact that {math}`P_{proj} = P`. Since affine transformations do not perform depth 
normalization, we see that

```{math}
P_{ndc,h} &= P_{clip,h} \\
P_{ndc,v} &= P_{clip,v} \\
P_{ndc,d} &= P_{clip,d} \\
P_{ndc,w} &= P_{clip,w} \\
```

therefore

```{math}
:label: eq_orth_ndc1
P_{ndc,h} &= \phi_{h} \left( P_{proj} \right) &= \phi_{h} \left( P \right) \\
P_{ndc,v} &= \phi_{v} \left( P_{proj} \right) &= \phi_{v} \left( P \right) \\
P_{ndc,d} &= \phi_{d} \left( P_{proj} \right) &= \phi_{d} \left( P \right) \\
```

are the equations we need to solve for. As with the perspective projection transformation, we 
use the constraints on the orthographic view volume to compute the functions. To establish constraints, we 
need to talk about some well chosen points. We need to construct the maps {math}`\phi_{h}, \phi_{v}, \phi_{d}` such that the 
parametrization of the orthographic view volume maps to the parametrization of the canonical view volume. That it, such that 
coordinates map as 
{math}`-l \mapsto \alpha_{min}, r \mapsto \alpha_{max}, -b \mapsto beta_{min}, t \mapsto \beta_{max}, n \mapsto \gamma_{min}, f \mapsto \gamma_{max}`.
Consider the points in view coordinates

```{math}
Q_{left}   &= -l \mathbf{\hat{u}}_{h} + n \mathbf{\hat{u}}_{d} \\
Q_{right}  &=  r \mathbf{\hat{u}}_{h} + n \mathbf{\hat{u}}_{d} \\
Q_{bottom} &= -b \mathbf{\hat{u}}_{v} + n \mathbf{\hat{u}}_{d} \\
Q_{top}    &=  t \mathbf{\hat{u}}_{v} + n \mathbf{\hat{u}}_{d} \\
Q_{near}   &=  n \mathbf{\hat{u}}_{d}                          \\
Q_{far}    &=  f \mathbf{\hat{u}}_{d}                          \\
```

The points {math}`Q_{near}` and {math}`Q_{far}` are the points along the viewing axis
that intersect the near plane and the far plane, respectively, of the perspective view volume. The 
point {math}`Q_{left}` represents the point of intersection of the left plane, near plane, and the 
horizontal-vertical plane of the view volume. The point {math}`Q_{right}` represents the point of 
intersection of the right plane, near plane, and the horizontal-vertical plane of the view volume.
The point {math}`Q_{bottom}` represents the point of intersection of the bottom plane, near plane, 
and depth-vertical plane of the view volume. The point {math}`Q_{top}` represents the point of 
intersection of the top plane, near plane, and the depth-vertical plane of the view volume. In short,
the points {math}`Q_{near}` and {math}`Q_{far}` are the origins of the near and far planes, respectively.
The other four points and points chosen along the edge of the viewport in the near plane that allow us
to easily set up the boundary conditions to compute the functions {math}`\phi_{h}, \phi_{v}, \phi_{d}`.

Consider the map {math}`\phi_{h}`, where

```{math}
:label: phi_h_orth_def
\phi_{h} \left( P \right) = A P_{h} + B P_{v} + C P_{d} + D
```

Define the boundary conditions for our chosen points

```{math}
\phi_{h} \left( Q_{left} \right)   &= \alpha_{min} \\ 
\phi_{h} \left( Q_{right} \right)  &= \alpha_{max} \\
\phi_{h} \left( Q_{bottom} \right) &= 0 \\
\phi_{h} \left( Q_{top} \right)    &= 0 \\
\phi_{h} \left( Q_{near} \right)   &= 0 \\
\phi_{h} \left( Q_{far} \right)    &= 0 \\
```

which we need to justify. The view space coordinates are orthogonal to each other, and the normalized device coordinates
are also orthogonal to each other. This means that {math}`\phi_{h}` should only be a function of the horizontal component
and not the vertical component. The points {math}`Q_{bottom}, Q_{top}, Q_{near}, Q_{far}` lie on the depth-vertical plane, which 
have a zero horizontal component, so they should keep a zero horizontal component after transformation.

Applying the boundary conditions, we have

```{math}
\phi_{h} \left( Q_{left} \right)
    &= A \cdot \left( -l \right) + B \cdot 0 + C n + D
    &&= -A l + C n + D
    &&= \alpha_{min} \\
\phi_{h} \left( Q_{right} \right) 
    &= A \cdot r + B \cdot 0 + C n + D
    &&= A r + C n + D
    &&= \alpha_{max} \\
\phi_{h} \left( Q_{bottom} \right) 
    &= A \cdot 0 + B \cdot \left( -b \right) + C n + D
    &&= -B b + C n + D
    &&= 0 \\
\phi_{h} \left( Q_{top} \right)
    &= A \cdot 0 + B \cdot t + C n + D
    &&= B t + C n + D
    &&= 0 \\
\phi_{h} \left( Q_{near} \right)
    &= A \cdot 0 + B \cdot 0 + C n + D
    &&= C n + D
    &&= 0 \\
\phi_{h} \left( Q_{far} \right)
    &= A \cdot 0 + B \cdot 0 + C f + D
    &&= C f + D 
    &&= 0 \\
```

so that

```{math}
:label: phi_h_orth_constraints
\phi_{h} \left( Q_{left} \right)   &= -A l + C n + D &&= \alpha_{min} \\
\phi_{h} \left( Q_{right} \right)  &= A r + C n + D  &&= \alpha_{max} \\
\phi_{h} \left( Q_{bottom} \right) &= -B b + C n + D &&= 0 \\
\phi_{h} \left( Q_{top} \right)    &= B t + C n + D  &&= 0 \\
\phi_{h} \left( Q_{near} \right)   &= C n + D        &&= 0 \\
\phi_{h} \left( Q_{far} \right)    &= C f + D        &&= 0 \\
```

and now we compute the constants. Subtracting {math}`\phi_{h} \left( Q_{right} \right)` from 
{math}`\phi_{h} \left( Q_{left} \right)` in {ref}`phi_h_orth_constraints` yields

```{math}
\phi_{h} \left( Q_{right} \right) - \phi_{h} \left( Q_{left} \right) 
    &= \left[ A r + C n + D \right] - \left[ -A l + C n + D \right] \\
    &= A r - \left( - A l \right) \\
    &= A \left( r - \left( -l \right) \right) \\
    &= \alpha_{max} - \alpha_{min}.
```

Solving for {math}`A`, we see that

```{math}
:label: phi_h_orth_constant_a
A = \frac{\alpha_{max} - \alpha_{min}}{r - \left( -l \right)}.
```

Subtracting {math}`\phi_{h} \left( Q_{top} \right)` from 
{math}`\phi_{h} \left( Q_{bottom} \right)` in {ref}`phi_h_orth_constraints` yields

```{math}
\phi_{h} \left( Q_{top} \right) - \phi_{h} \left( Q_{bottom} \right)
    &= \left[ B t + C n + D \right] - \left[ -B b + C n + D \right] \\
    &= B t - B \left( -b \right) \\
    &= B \left( t - \left( -b \right) \right) \\
    &= 0.
```

Solving for {math}`B`, we see that 

```{math}
:label: phi_h_orth_constant_b
B = 0
```

since {math}`t - (-b) = t + b \neq 0`. Subtracting {math}`\phi_{h} \left( Q_{far} \right)` from
{math}`\phi_{h} \left( Q_{near} \right)` in {ref}`phi_h_orth_constraints` yields

```{math}
\phi_{h} \left( Q_{far} \right) - \phi_{h} \left( Q_{near} \right)
    &= \left[ C f + D \right] - \left[ C n + D \right] \\
    &= C f - C n \\
    &= C \left( f - n \right) \\
    &= 0.
```

Solving for {math}`C`, we see that

```{math}
:label: phi_h_orth_constant_c
C = 0
```

since {math}`f - n \neq 0`. Substituting the constants {ref}`phi_h_orth_constant_a`,
{ref}`phi_h_orth_constant_b`, and {ref}`phi_h_orth_constant_c` back into {math}`\phi_{h}(Q_{right})` in 
{ref}`phi_h_orth_constraints` gives us

```{math}
\phi_{h} \left( Q_{right} \right) 
    = A r + C n + D
    = A r + D
    = \left( \frac{\alpha_{max} - \alpha_{min}}{r - \left( -l \right)} \right) r + D
    = \alpha_{max}.
```

Solving for {math}`D`

```{math}
D &= \alpha_{max} - \left( \frac{\alpha_{max} - \alpha_{min}}{r - \left( -l \right)} \right) r \\
  &= \alpha_{max} - \left( \frac{\alpha_{max} - \alpha_{min}}{r + l} \right) r \\
  &= \frac{\alpha_{max} \left( r + l \right) - \left( \alpha_{max} - \alpha_{min} \right) r}{r + l} \\
  &= \frac{\alpha_{max} r + \alpha_{max} l - \alpha_{max} r + \alpha_{min} r}{r + l} \\
  &= \frac{\alpha_{max} r + \alpha_{max} l - \alpha_{max} r + \alpha_{min} r}{r + l} \\
  &= \frac{\alpha_{max} l + \alpha_{min} r}{r + l} \\
  &= \frac{\alpha_{min} r - \alpha_{max} \left( -l \right)}{r - \left( -l \right)} \\
```

we have

```{math}
:label: phi_h_orth_constant_d
D =  \frac{\alpha_{min} r - \alpha_{max} \left( -l \right)}{r - \left( -l \right)}.
```

Assembling the constants {ref}`phi_h_orth_constant_a`, {ref}`phi_h_orth_constant_b`, {ref}`phi_h_orth_constant_c`, 
{ref}`phi_h_orth_constant_d` back into {ref}`phi_h_orth_def` we have the complete formula for the 
function {math}`\phi_{h}`

```{math}
:label: phi_h_orth_final
\phi_{h} \left( P \right) 
    = \left( \frac{ \alpha_{max} - \alpha_{min} }{ r - \left( -l \right)} \right) P_{h}
    + \frac{\alpha_{min} r - \alpha_{max} \left( -l \right)}{r - \left( -l \right)}.
```

Consider the map {math}`\phi_{v}`, where

```{math}
:label: phi_v_orth_def
\phi_{v} \left( P \right) = A P_{h} + B P_{v} + C P_{d} + D
```

Define the boundary conditions for our chosen points

```{math}
\phi_{v} \left( Q_{left} \right)   &= 0 \\ 
\phi_{v} \left( Q_{right} \right)  &= 0 \\
\phi_{v} \left( Q_{bottom} \right) &= beta_{min} \\
\phi_{v} \left( Q_{top} \right)    &= beta_{max} \\
\phi_{v} \left( Q_{near} \right)   &= 0 \\
\phi_{v} \left( Q_{far} \right)    &= 0 \\
```

which we need to justify. The view space coordinates are orthogonal to each other, and the normalized device coordinates
are also orthogonal to each other. This means that {math}`\phi_{v}` should only be a function of the vertical component
and not the horizontal component. The points {math}`Q_{left}, Q_{right}, Q_{near}, Q_{far}` lie on the depth-horizontal plane, which 
have a zero vertical component, so they should keep a zero vertical component after transformation.

Applying the boundary conditions, we have

```{math}
\phi_{v} \left( Q_{left} \right)
    &= A \cdot \left( -l \right) + B \cdot 0 + C n + D
    &&= -A l + C n + D
    &&= 0 \\
\phi_{v} \left( Q_{right} \right) 
    &= A \cdot r + B \cdot 0 + C n + D
    &&= A r + C n + D
    &&= 0 \\
\phi_{v} \left( Q_{bottom} \right) 
    &= A \cdot 0 + B \cdot \left( -b \right) + C n + D
    &&= -B b + C n + D
    &&= \beta_{min} \\
\phi_{v} \left( Q_{top} \right)
    &= A \cdot 0 + B \cdot t + C n + D
    &&= B t + C n + D
    &&= \beta_{max} \\
\phi_{v} \left( Q_{near} \right)
    &= A \cdot 0 + B \cdot 0 + C n + D
    &&= C n + D
    &&= 0 \\
\phi_{v} \left( Q_{far} \right)
    &= A \cdot 0 + B \cdot 0 + C f + D
    &&= C f + D 
    &&= 0 \\
```

so that

```{math}
:label: phi_v_orth_constraints
\phi_{v} \left( Q_{left} \right)   &= -A l + C n + D &&= 0 \\
\phi_{v} \left( Q_{right} \right)  &= A r + C n + D  &&= 0 \\
\phi_{v} \left( Q_{bottom} \right) &= -B b + C n + D &&= \beta_{min} \\
\phi_{v} \left( Q_{top} \right)    &= B t + C n + D  &&= \beta_{max} \\
\phi_{v} \left( Q_{near} \right)   &= C n + D        &&= 0 \\
\phi_{v} \left( Q_{far} \right)    &= C f + D        &&= 0 \\
```

and now we compute the constants. Subtracting {math}`\phi_{v} \left( Q_{right} \right)` from 
{math}`\phi_{v} \left( Q_{left} \right)` in {ref}`phi_v_orth_constraints` yields

```{math}
\phi_{v} \left( Q_{right} \right) - \phi_{v} \left( Q_{left} \right) 
    &= \left[ A r + C n + D \right] - \left[ -A l + C n + D \right] \\
    &= A r - \left( - A l \right) \\
    &= A \left( r - \left( -l \right) \right) \\
    &= 0.
```

Solving for {math}`A`, we see that

```{math}
:label: phi_v_orth_constant_a
A = 0
```

since {math}`r - (-l) = r + l \neq 0`. Subtracting {math}`\phi_{v} \left( Q_{top} \right)` from 
{math}`\phi_{v} \left( Q_{bottom} \right)` in {ref}`phi_v_orth_constraints` yields

```{math}
\phi_{v} \left( Q_{top} \right) - \phi_{v} \left( Q_{bottom} \right)
    &= \left[ B t + C n + D \right] - \left[ -B b + C n + D \right] \\
    &= B t - B \left( -b \right) \\
    &= B \left( t - \left( -b \right) \right) \\
    &= \beta_{max} - \beta_{min}.
```

Solving for {math}`B`, we see that 

```{math}
:label: phi_v_orth_constant_b
B = \frac{\beta_{max} - \beta_{min}}{t - \left( -b \right)}.
```

Subtracting {math}`\phi_{v} \left( Q_{far} \right)` from {math}`\phi_{v} \left( Q_{near} \right)` in 
{ref}`phi_v_orth_constraints` yields

```{math}
\phi_{v} \left( Q_{far} \right) - \phi_{v} \left( Q_{near} \right)
    &= \left[ C f + D \right] - \left[ C n + D \right] \\
    &= C f - C n \\
    &= C \left( f - n \right) \\
    &= 0.
```

Solving for {math}`C`, we see that

```{math}
:label: phi_v_orth_constant_c
C = 0
```

since {math}`f - n \neq 0`. Substituting the constants {ref}`phi_v_orth_constant_a`,
{ref}`phi_v_orth_constant_b`, and {ref}`phi_v_orth_constant_c` back into {math}`\phi_{v}(Q_{top})` in 
{ref}`phi_v_orth_constraints` gives us

```{math}
\phi_{v} \left( Q_{top} \right) 
    = B t + C n + D
    = B t + D
    = \left( \frac{\beta_{max} - \beta_{min}}{t - \left( -b \right)} \right) t + D
    = \beta_{max}.
```

Solving for {math}`D`

```{math}
D &= \beta_{max} - \left( \frac{\beta_{max} - \beta_{min}}{t - \left( -b \right)} \right) t \\
  &= \beta_{max} - \left( \frac{\beta_{max} - \beta_{min}}{t + b} \right) t \\
  &= \frac{\beta_{max} \left( t + b \right) - \left( \beta_{max} - \beta_{min} \right) t}{t + b} \\
  &= \frac{\beta_{max} t + \beta_{max} b - \beta_{max} t + \beta_{min} t}{t + b} \\
  &= \frac{\beta_{max} t + \beta_{max} b - \beta_{max} t + \beta_{min} t}{t + b} \\
  &= \frac{\beta_{max} b + \beta_{min} t}{t + b} \\
  &= \frac{\beta_{min} t - \beta_{max} \left( -b \right)}{t - \left( -b \right)} \\
```

we have

```{math}
:label: phi_v_orth_constant_d
D =  \frac{\beta_{min} t - \beta_{max} \left( -b \right)}{t - \left( -b \right)}.
```

Assembling the constants {ref}`phi_v_orth_constant_a`, {ref}`phi_v_orth_constant_b`, {ref}`phi_v_orth_constant_c`, 
{ref}`phi_v_orth_constant_d` back into {ref}`phi_v_orth_def` we have the complete formula for the 
function {math}`\phi_{v}`

```{math}
:label: phi_v_orth_final
\phi_{v} \left( P \right) 
    = \left( \frac{ \beta_{max} - \beta_{min} }{ t - \left( -b \right)} \right) P_{v}
    + \frac{\beta_{min} t - \beta_{max} \left( -b \right)}{t - \left( -b \right)}.
```

Consider the map {math}`\phi_{d}`, where

```{math}
:label: phi_d_orth_def
\phi_{d} \left( P \right) = A P_{h} + B P_{v} + C P_{d} + D
```

Define the boundary conditions for our chosen points

```{math}
\phi_{d} \left( Q_{left} \right)   &= \gamma_{min} \\ 
\phi_{d} \left( Q_{right} \right)  &= \gamma_{min} \\
\phi_{d} \left( Q_{bottom} \right) &= \gamma_{min} \\
\phi_{d} \left( Q_{top} \right)    &= \gamma_{min} \\
\phi_{d} \left( Q_{near} \right)   &= \gamma_{min} \\
\phi_{d} \left( Q_{far} \right)    &= \gamma_{max} \\
```

which we need to justify. The view space coordinates are orthogonal to each other, and the normalized device coordinates
are also orthogonal to each other. This means that {math}`\phi_{d}` should only be a function of the depth component
and not the horizontal or vertical components. The points {math}`Q_{left}, Q_{right}, Q_{bottom}, Q_{top}` lie on the 
near plane, which have a depth component of {math}`n`. Since orthographic projection is just the identity, 
points already on the near plane should stay there. Moreover, points on the far plane should stay on the far
plane to transform the orthographic view volume into the canonical view volume correctly. Consequently, the 
depth component should have no dependence on the horizontal or vertical components, only a depth term and an affine 
translation term.

Applying the boundary conditions, we have

```{math}
\phi_{d} \left( Q_{left} \right)
    &= A \cdot \left( -l \right) + B \cdot 0 + C n + D
    &&= -A l + C n + D
    &&= \gamma_{min} \\
\phi_{d} \left( Q_{right} \right) 
    &= A \cdot r + B \cdot 0 + C n + D
    &&= A r + C n + D
    &&= \gamma_{min} \\
\phi_{d} \left( Q_{bottom} \right) 
    &= A \cdot 0 + B \cdot \left( -b \right) + C n + D
    &&= -B b + C n + D
    &&= 0 \\
\phi_{d} \left( Q_{top} \right)
    &= A \cdot 0 + B \cdot t + C n + D
    &&= B t + C n + D
    &&= 0 \\
\phi_{d} \left( Q_{near} \right)
    &= A \cdot 0 + B \cdot 0 + C n + D 
    &&= C n + D
    &&= \gamma_{min} \\
\phi_{d} \left( Q_{far} \right)
    &= A \cdot 0 + B \cdot 0 + C f + D
    &&= C f + D
    &&= \gamma_{max} \\
```

so that

```{math}
:label: phi_d_orth_constraints
\phi_{d} \left( Q_{left} \right)   &= -A l + C n + D &&= \gamma_{min} \\
\phi_{d} \left( Q_{right} \right)  &= A r + C n + D  &&= \gamma_{min} \\
\phi_{d} \left( Q_{bottom} \right) &= -B b + C n + D &&= \gamma_{min} \\
\phi_{d} \left( Q_{top} \right)    &= B t + C n + D  &&= \gamma_{min} \\
\phi_{d} \left( Q_{near} \right)   &= C n + D        &&= \gamma_{min} \\
\phi_{d} \left( Q_{far} \right)    &= C f + D        &&= \gamma_{max} \\
```

and now we compute the constants. Subtracting {math}`\phi_{d} \left( Q_{right} \right)` from 
{math}`\phi_{d} \left( Q_{left} \right)` in {ref}`phi_d_orth_constraints` yields

```{math}
\phi_{d} \left( Q_{right} \right) - \phi_{d} \left( Q_{left} \right) 
    &= \left[ A r + C n + D \right] - \left[ -A l + C n + D \right] \\
    &= A r - \left( - A l \right) \\
    &= A \left( r - \left( -l \right) \right) \\
    &= \gamma_{min} -  \gamma_{min} \\
    &= 0.
```

Solving for {math}`A`, we see that

```{math}
:label: phi_d_orth_constant_a
A = 0
```

since {math}`r - (-l) = r + l \neq 0`. Subtracting {math}`\phi_{d} \left( Q_{top} \right)` from 
{math}`\phi_{d} \left( Q_{bottom} \right)` in {ref}`phi_d_orth_constraints` yields

```{math}
\phi_{d} \left( Q_{top} \right) - \phi_{d} \left( Q_{bottom} \right)
    &= \left[ B t + C n + D \right] - \left[ -B b + C n + D \right] \\
    &= B t - B \left( -b \right) \\
    &= B \left( t - \left( -b \right) \right) \\
    &= \gamma_{min} - \gamma_{min} \\
    &= 0.
```

Solving for {math}`B`, we see that 

```{math}
:label: phi_d_orth_constant_b
B = 0
```

since {math}`t - (-b) = t + b \neq 0`. Subtracting {math}`\phi_{d} \left( Q_{far} \right)` from
{math}`\phi_{d} \left( Q_{near} \right)` in {ref}`phi_d_orth_constraints` yields

```{math}
\phi_{d} \left( Q_{far} \right) - \phi_{d} \left( Q_{near} \right)
    &= \left[ C f + D \right] - \left[ C n + D \right] \\
    &= C f - C n \\
    &= C \left( f - n \right) \\
    &= \gamma_{max} - \gamma_{min}.
```

Solving for {math}`C`, we see that

```{math}
:label: phi_d_orth_constant_c
C = \frac{ \gamma_{max} - \gamma_{min} }{f - n}
```

Substituting the constants {ref}`phi_d_orth_constant_a`,
{ref}`phi_d_orth_constant_b`, and {ref}`phi_d_orth_constant_c` back into {math}`\phi_{d}(Q_{far})` in 
{ref}`phi_d_orth_constraints` gives us

```{math}
\phi_{d} \left( Q_{far} \right) 
    &= C f + D 
    &= \left( \frac{\gamma_{max} - \gamma_{min}}{f - n} \right) f + D 
    &= \gamma_{max}.
```

Solving for {math}`D`

```{math}
D &= \gamma_{max} - \left( \frac{\gamma_{max} - \gamma_{min}}{f - n} \right) f \\
  &= \frac{\gamma_{max} \left( f - n \right) - \left( \gamma_{max} - \gamma_{min} \right) f}{f - n} \\
  &= \frac{\gamma_{max} f - \gamma_{max} n - \gamma_{max} f + \gamma_{min}f}{f - n} \\
  &= \frac{\gamma_{min} f - \gamma_{max} n}{f - n} \\
```

we have

```{math}
:label: phi_d_orth_constant_d
D = \frac{\gamma_{min} f - \gamma_{max} n}{f - n}.
```

Assembling the constants {ref}`phi_d_orth_constant_a`, {ref}`phi_d_orth_constant_b`, {ref}`phi_d_orth_constant_c`, 
{ref}`phi_d_orth_constant_d` back into {ref}`phi_d_orth_def` we have the complete formula for the 
function {math}`\phi_{d}`

```{math}
:label: phi_d_orth_final
\phi_{d} \left( P \right)
    = \left( \frac{\gamma_{max} - \gamma_{min}}{f - n} \right) P_{d}
    + \frac{ \gamma_{min} f - \gamma_{max} n }{f - n}.
```

Finally, we substitute {ref}`phi_h_orth_final`, {ref}`phi_v_orth_final`, and {ref}`phi_d_orth_final`
back into {ref}`eq_orth_clip1` to get

```{math}
P_{clip,h} 
    &= \phi_{h} \left( P \right)
    &&= \left( \frac{ \alpha_{max} - \alpha_{min} }{ r - \left( -l \right)} \right) P_{h}
    + \frac{\alpha_{min} r - \alpha_{max} \left( -l \right)}{r - \left( -l \right)} \\
P_{clip,v}
    &= \phi_{v} \left( P \right) 
    &&= \left( \frac{ \beta_{max} - \beta_{min} }{ t - \left( -b \right)} \right) P_{v}
    + \frac{\beta_{min} t - \beta_{max} \left( -b \right)}{t - \left( -b \right)} \\
P_{clip,d} 
    &= \phi_{d} \left( P \right)
    &&= \left( \frac{\gamma_{max} - \gamma_{min}}{f - n} \right) P_{d} 
    + \frac{ \gamma_{min} f - \gamma_{max} n }{f - n}  \\
P_{clip,w} 
    &= \phi_{w} \left( P \right) 
    &&= 1 \\
```

Assembling the clip space components into the resulting matrix equation, we have

```{math}
\begin{bmatrix} 
    P_{clip,h} \\ 
    P_{clip,v} \\ 
    P_{clip,d} \\ 
    P_{clip,w} \\
\end{bmatrix}
= 
\begin{bmatrix}
    \frac{\alpha_{max} - \alpha_{min}}{r - \left( -l \right)}
    & 0 
    & 0 
    & \frac{\alpha_{min}r - \alpha_{max} \left(-l \right) }{r - \left( -l \right)}
    \\
    0 
    & \frac{\beta_{max} - \beta_{min}}{t - \left( -b \right)} 
    & 0 
    & \frac{\beta_{min}t - \beta_{max}\left( -b \right) }{t - \left(-b \right)}
    \\
    0 
    & 0 
    & \frac{\gamma_{max} - \gamma_{min} }{f - n} 
    & \frac{\gamma_{min}f - \gamma_{max}n}{f - n}
    \\
    0 
    & 0 
    & 0 
    & 1
    \\
\end{bmatrix}
\begin{bmatrix} 
    P_{h} \\ 
    P_{v} \\ 
    P_{d} \\ 
    P_{w} \\
\end{bmatrix}.
```

This yields the second major result, the canonical orthographic projection matrix {math}`M^{C}_{orth}`.

```{admonition} Orthographic Projection Matrix
```{math}
:label: matrix_orth_canonical
M^{C}_{orth} = 
\begin{bmatrix}
    \frac{\alpha_{max} - \alpha_{min}}{r - \left( -l \right)} 
    & 0 
    & 0 
    & \frac{\alpha_{min}r - \alpha_{max} \left(-l \right) }{r - \left( -l \right)}
    \\
    0 
    & \frac{\beta_{max} - \beta_{min}}{t - \left( -b \right)} 
    & 0 
    & \frac{\beta_{min}t - \beta_{max}\left( -b \right) }{t - \left(-b \right)}
    \\
    0 
    & 0 
    & \frac{\gamma_{max} - \gamma_{min} }{f - n} 
    & \frac{\gamma_{min}f - \gamma_{max}n}{f - n}
    \\
    0 
    & 0 
    & 0 
    & 1
    \\
\end{bmatrix}
```

This completes the derivation of the canonical orthographic projection matrix.

### The Canonical Perspective Matrix

Now that we have the canonical perspective projection matrix and the canonical orthographic projection matrix,
we can work out the canonical perspective matrix. The canonical perspective matrix maps from view space to projected
space. Since the perspective projection transformation consists of a projective transformation multiplied by an orthographic
transformation, we can get the perspective matrix by premultiplying {ref}`matrix_per_canonical` by {math}`(M^{C}_{orth})^{-1}`
given by

```{math}
\left(M^{C}_{orth}\right)^{-1} =
    \begin{bmatrix}
        \frac{r + l}{\alpha_{max} - \alpha_{min}} 
        &  0 
        &  0 
        & -\frac{\alpha_{min} r - \alpha_{max} \left( -l \right)}{\alpha_{max} - \alpha_{min}}
        \\
        0 
        &  \frac{t + b}{\beta_{max} - \beta_{min}}
        &  0 
        & -\frac{\beta_{min} t - \beta_{max} \left( -b \right)}{\beta_{max} - \beta_{min}}
        \\
        0 
        &  0 
        &  \frac{f - n}{\gamma_{max} - \gamma_{min}} 
        & -\frac{\gamma_{min} f - \gamma_{max} n}{\gamma_{max} - \gamma_{min}}
        \\
        0 
        & 0 
        & 0 
        & 1
        \\
    \end{bmatrix}.
```

Premultiplying {math}`M^{C}_{per}` by {math}`(M^{C}_{orth})^{-1}` gives

```{math}
P^{C} &= \left(M^{C}_{orth}\right)^{-1} M^{C}_{per} \\ 
    &= \begin{bmatrix}
            \frac{r + l}{\alpha_{max} - \alpha_{min}} 
            &  0 
            &  0 
            & -\frac{\alpha_{min} r - \alpha_{max} \left( -l \right)}{\alpha_{max} - \alpha_{min}}
            \\
            0 
            &  \frac{t + b}{\beta_{max} - \beta_{min}}
            &  0 
            & -\frac{\beta_{min} t - \beta_{max} \left( -b \right)}{\beta_{max} - \beta_{min}}
            \\
            0 
            &  0 
            &  \frac{f - n}{\gamma_{max} - \gamma_{min}} 
            & -\frac{\gamma_{min} f - \gamma_{max} n}{\gamma_{max} - \gamma_{min}}
            \\
            0 
            & 0 
            & 0 
            & 1
            \\
        \end{bmatrix}
        \begin{bmatrix}
            \frac{\left( \alpha_{max} - \alpha_{min} \right) n }{r - \left( -l \right)} 
            & 0 
            & \frac{\alpha_{min}r - \alpha_{max} \left( -l \right)}{r - \left( -l \right)} 
            & 0 
            \\
            0 
            & \frac{\left( \beta_{max} - \beta_{min} \right) n }{t - \left( -b \right)} 
            & \frac{\beta_{min}t - \beta_{max} \left( -b \right)}{t - \left( -b \right)} 
            & 0
            \\
            0 
            & 0 
            & \frac{\gamma_{max}f - \gamma_{min}n}{f - n} 
            & -\frac{\left( \gamma_{max} - \gamma_{min} \right) f n }{f - n}
            \\
            0 
            & 0 
            & 1 
            & 0
            \\
        \end{bmatrix}
        \\
    &=  \begin{bmatrix}
            n & 0 & 0     &  0   \\
            0 & n & 0     &  0   \\
            0 & 0 & f + n & -f n \\
            0 & 0 & 1     &  0   \\
        \end{bmatrix}
```

which gives us the desired perspective matrix

```{admonition} Perspective Matrix
```{math}
P^{C} = 
    \begin{bmatrix}
        n & 0 & 0     &  0   \\
        0 & n & 0     &  0   \\
        0 & 0 & f + n & -f n \\
        0 & 0 & 1     &  0   \\
    \end{bmatrix}
```

This shows that the perspective projection matrix {math}`M^{C}_{per}` is indeed the product of 
a projective transformation and an orthographic transformation

```{math}
M^{C}_{per} = M^{C}_{orth} P^{C}.
```

Notice that the perspective matrix passes along the input depth coordinate undistorted via the 
{math}`w` component, but nonlinearly distorts the output depth component.

### The Canonical Symmetric Vertical Field Of View Perspective Matrix

In the symmetric case, {math}`r = l` and {math}`t = b`. The **width** of the viewport is 
{math}`\text{width} = r - (-l) = r + l`. The **height** of the viewport is 
{math}`\text{height} = t - (-b) = t + b`. The aspect ratio {math}`\text{aspect}` is given by 

```{math}
\text{aspect} \equiv \frac{\text{width}}{\text{height}}
    = \frac{r - \left( -l \right)}{t - \left( -b \right)}
    = \frac{r + l}{t + b}
    = \frac{r + r}{t + t}
    = \frac{2 r}{2 t}
    = \frac{r}{t}
```

which implies that {math}`r = \text{aspect} \cdot t`. Since the viewing frustum is symmetric, the tangent 
of {math}`\theta_{vfov} / 2` is {math}`\tan\left( \theta_{vfov} / 2 \right) = t / n`. From these facts we 
see that

```{math}
:label: eq_symmetric_tangent_vfov
t = b = n \tan\left( \frac{\theta_{vfov}}{2} \right) \\
r = l = \text{aspect} \cdot t = \text{aspect} \cdot n \tan\left( \frac{\theta_{vfov}}{2} \right)
```

We now use {ref}`eq_symmetric_tangent_vfov` inside the general perspective projection 
matrix {ref}`matrix_per_canonical` to derive the symmetric vertical field of view perspective matrix.
Consider the elements of the matrix {math}`M^{C}_{per}`. The nonzero elements of {math}`M^{C}_{per}`
become

```{math}
:label: matrix_per_vfov_canonical_elements
M^{C}_{per}\left[0, 0 \right] = \frac{\left( \alpha_{max} - \alpha_{min} \right) n}{r - \left( -l \right)}
    &= \frac{\left( \alpha_{max} - \alpha_{min} \right) n}{r + l} \\
    &= \frac{\left( \alpha_{max} - \alpha_{min} \right) n}{2 r} \\
    &= \left( \frac{\alpha_{max} - \alpha_{min}}{2} \right) \frac{n}{r} \\
    &= \left( \frac{\alpha_{max} - \alpha_{min}}{2} \right) \frac{n}{\text{aspect} \cdot n \tan\left( \frac{\theta_{vfov}}{2} \right)} \\
    &= \left( \frac{\alpha_{max} - \alpha_{min}}{2} \right) \frac{1}{\text{aspect} \cdot \tan\left( \frac{\theta_{vfov}}{2} \right)} \\

M^{C}_{per}\left[1, 1 \right] = \frac{\left( \beta_{max} - \beta_{min} \right) n}{t - \left( -b \right)}
    &= \frac{\left( \beta_{max} - \beta_{min} \right) n}{t + b} \\
    &= \frac{\left( \beta_{max} - \beta_{min} \right) n}{2 t} \\
    &= \left( \frac{\beta_{max} - \beta_{min}}{2} \right) \frac{n}{t} \\
    &= \left( \frac{\beta_{max} - \beta_{min}}{2} \right) \frac{n}{n \tan\left( \frac{\theta_{vfov}}{2} \right)} \\
    &= \left( \frac{\beta_{max} - \beta_{min}}{2} \right) \frac{1}{\tan\left( \frac{\theta_{vfov}}{2} \right)} \\

M^{C}_{per}\left[0, 2 \right] = \frac{\alpha_{min} r - \alpha_{max}\left( -l \right)}{r - \left( -l \right)}
    &= \frac{\alpha_{min} r + \alpha_{max} l}{r + l} \\
    &= \frac{\alpha_{min} r + \alpha_{max} r}{2 r} \\
    &= \frac{\alpha_{min} + \alpha_{max}}{2} \\

M^{C}_{per}\left[1, 2 \right] = \frac{\beta_{min} t - \beta_{max}\left( -b \right)}{t - \left( -b \right)}
    &= \frac{\beta_{min} t + \beta_{max} b}{t + b} \\
    &= \frac{\beta_{min} t + \beta_{max} t}{2 t} \\
    &= \frac{\beta_{min} + \beta_{max}}{2} \\

M^{C}_{per}\left[2, 2 \right] &= \frac{\gamma_{max} f - \gamma_{min} n}{f - n} \\
M^{C}_{per}\left[2, 3 \right] &= -\frac{\left(\gamma_{max} - \gamma_{min} \right) f n}{f - n}
```

Substituting {ref}`matrix_per_vfov_canonical_elements` back into {ref}`matrix_per_canonical` the matrix
{math}`M^{C}_{per}` takes the form

```{math}
M^{C}_{per,vfov} = 
    \begin{bmatrix}
        \left( \frac{\alpha_{max} - \alpha_{min}}{2} \right) \frac{1}{\text{aspect} \cdot \tan\left( \frac{\theta_{vfov}}{2} \right)} 
        & 0 
        & \frac{\alpha_{min} + \alpha_{max}}{2} 
        & 0
        \\
        0 
        & \left( \frac{\beta_{max} - \beta_{min}}{2} \right) \frac{1}{\tan\left( \frac{\theta_{vfov}}{2} \right)}
        & \frac{\beta_{min} + \beta_{max}}{2}
        & 0
        \\
        0 
        & 0 
        & \frac{\gamma_{max}f - \gamma_{min}n}{f - n} 
        & -\frac{\left( \gamma_{max} - \gamma_{min} \right) f n }{f - n}
        \\
        0 
        & 0 
        & 1 
        & 0
        \\
    \end{bmatrix}
```

which is the desired formula. This is the third major result, the canonical symmetric vertical field of 
view perspective projection matrix.

```{admonition} Symmetric Vertical Field Of View Perspective Matrix
```{math}
M^{C}_{per, vfov} = 
    \begin{bmatrix}
        \left( \frac{\alpha_{max} - \alpha_{min}}{2} \right) \frac{1}{\text{aspect} \cdot \tan\left( \frac{\theta_{vfov}}{2} \right)} 
        & 0 
        & \frac{\alpha_{min} + \alpha_{max}}{2} 
        & 0
        \\
        0 
        & \left( \frac{\beta_{max} - \beta_{min}}{2} \right) \frac{1}{\tan\left( \frac{\theta_{vfov}}{2} \right)}
        & \frac{\beta_{min} + \beta_{max}}{2}
        & 0
        \\
        0 
        & 0 
        & \frac{\gamma_{max}f - \gamma_{min}n}{f - n} 
        & -\frac{\left( \gamma_{max} - \gamma_{min} \right) f n }{f - n}
        \\
        0 
        & 0 
        & 1 
        & 0
        \\
    \end{bmatrix}
```

## Every Other Matrix

For any given projected view coordinate system and clip coordinate system, there exists a perspective 
projection matrix that can be expressed as {math}`M_{per} = T^{-1}_{clip} M^{C}_{per} T_{view}` where 
{math}`M^{C}_{per}` is the canonical perspective projection matrix. Let {math}`[\mathbf{x}]` be a homogeneous 
point in {math}`\mathbb{RP}^3`. Let {math}`\mathbf{x}_{view}` be a representive of {math}`[\mathbf{x}]` in 
projected view coordinates, {math}`\mathbf{x}_{clip}` be a representative of {math}`[\mathbf{x}]` in clip 
coordinates, {math}`\mathbf{x}^{C}_{view}` be a representive of {math}`[\mathbf{x}]` in the canonically 
chosen view space coordinate system, {math}`\mathbf{x}^{C}_{clip}` be a representive of {math}`[\mathbf{x}]` 
in the canonically chosen clip coordinates, such that

```{math}
:label: eq_view_to_clip_can1
\mathbf{x}^{C}_{clip} = M^{C}_{per} \mathbf{x}^{C}_{view}.
```

That is, {math}`\mathbf{x}^{C}_{clip}` represents the perspective projected point {math}`\mathbf{x}`. 
Let {math}`T_{view} : \mathbb{RP}^{3} \rightarrow \mathbb{RP}^{3}` denote the coordinate transformation 
from projected view coordinates to the canonical projected view coordinates. Let 
{math}`T_{clip} : \mathbb{RP}^{3} \rightarrow \mathbb{RP}^{3}` denote the coordinate transformation 
from clip coordinates to canonical clip coordinates. The coordinate relations are given by 
{math}`\mathbf{x}^{C}_{view} = T_{view} \mathbf{x}^{C}_{view}` and 
{math}`\mathbf{x}^{C}_{clip} = T_{clip} \mathbf{x}^{C}_{clip}`. Applying these relationships in
{ref}`eq_view_to_clip_can1`

```{math}
T_{clip} \mathbf{x}_{clip} = M^{C}_{per} T_{view} \mathbf{x}_{view}
```

or equivalently

```{math}
\mathbf{x}_{clip} 
    = T^{-1}_{clip} M^{C}_{per} T_{view} \mathbf{x}_{view} 
    = \left( T^{-1}_{clip} M^{C}_{per} T_{view} \right) \mathbf{x}_{view}
```

therefore we can identify 

```{math}
:label: eq_view_to_clip_can2
M_{per} = T^{-1}_{clip} M^{C}_{per} T_{view}.
```

This proves the existence of a perspective projection for any source projected view space 
coordinate system, and any target clip coordinate system. To show uniqueness, suppose that 
{math}`M^{\prime}_{per}` is another transformation that maps view coordinates to clip 
coordinates, such that {math}`\mathbf{x}_{clip} = M^{\prime}_{per} \mathbf{x}_{view}`. Using the 
coordinate transformations again, we can write {math}`\mathbf{x}^{C}_{clip} = T_{clip} \mathbf{x}_{clip}`
and {math}`\mathbf{x}^{C}_{view} = T_{view} \mathbf{x}_{view}`. Inverting these relations, we get
{math}`\mathbf{x}^{C}_{view} = T^{-1}_{view} \mathbf{x}_{view}` and
{math}`\mathbf{x}^{C}_{clip} = T^{-1}_{clip} \mathbf{x}_{clip}`. Using the relation for 
{math}`M^{\prime}_{per}` in equation {ref}`eq_view_to_clip_can2` implies

```{math}
T^{-1}_{clip} \mathbf{x}^{C}_{clip} = M^{\prime}_{per} T^{-1}_{view} \mathbf{x}^{C}_{view}
```

or equivalently

```{math}
\mathbf{x}^{C}_{clip} 
    = T_{clip} M^{\prime}_{per} T^{-1}_{view} \mathbf{x}^{C}_{view}
    = \left( T_{clip} M^{\prime}_{per} T^{-1}_{view} \right) \mathbf{x}^{C}_{view}.
```

Applying the relation for the canonical perspective projection matrix

```{math}
\mathbf{x}^{C}_{clip} 
    = M^{C}_{per} \mathbf{x}^{C}_{view} 
    = \left( T_{clip} M^{\prime}_{per} T^{-1}_{view} \right) \mathbf{x}^{C}_{view}
```

which imples that

```{math}
M^{C}_{per} = T_{clip} M^{\prime}_{per} T^{-1}_{view}
```

or equivalently

```{math}
:label: eq_view_to_clip_can3
M^{\prime}_{per} = T^{-1}_{clip} M^{C}_{per} T_{view} = M_{per}
```

where the last equality in {ref}`eq_view_to_clip_can3` follows from {ref}`eq_view_to_clip_can2`.
This proves uniqueness for the perspective projection matrix.

To show that any perspective projection is well-defined, let {math}`[\mathbf{x}] \in \mathbb{RP}^{3}` be 
a point and let {math}`\mathbf{x}_{1} \sim \mathbf{x_{2}}` be representatives of {math}`[\mathbf{x}]`.
Then there exists {math}`\lambda \in \mathbb{R} - \{ 0 \}` such that {math}`\mathbf{x}_{2} = \lambda \mathbf{x_{1}}`.
By linearity of the canonical perspective projection, 
{math}`\lambda M^{C}_{per} \mathbf{x}_{1} = M^{C}_{per} ( \lambda \mathbf{x}_{1} ) = M^{C}_{per} \mathbf{x}_{2}`
which shows that {math}`M^{C}_{per}` is well-defined. By the linearity of homogeneous orthogonal transformations 

```{math}
\lambda M_{per} \mathbf{x}_{1}
    &= \lambda T^{-1}_{clip} M^{C}_{per} T_{view} \mathbf{x}_{1} \\
    &= \left( T^{-1}_{clip} M^{C}_{per} T_{view} \right) \left( \lambda \mathbf{x}_{1} \right) \\
    &= \left( T^{-1}_{clip} M^{C}_{per} T_{view} \right) \mathbf{x}_{2} \\
    &= M_{per} \mathbf{x}_{2} \\
    &= M_{per} \left( \lambda \mathbf{x}_{1} \right) \\
```

which shows that {math}`M_{per}` is well-defined.

## Summary

We construct a set of projection matrices using a canonically chosen set of coordinates which makes 
it easy to derive any other projection matrix using coordinate transformations in conjunction with
the relevant coordinate transformations to create the final result. We chose a view space coordinate 
system where the horizontal axis points to the right, the vertical axis points up, and the depth
axis points into the view volume. This has the benefit of keeping all of the computations in the same
orthonormal frame, which makes the behavior of the projection more obvious. Operating in real projective 
space allows us to express our rendering problems in a coordinate system and scale independent way, such
that the choice of coordinate system is a degree of freedom for the problem at hand. Moreover, the 
coordinate system independent formulation is a low-key form of separation of concerns in software 
engineering for spatial computing domains.

We show how to construct perspective and orthographic projection transformations in {math}`\mathbb{RP}^{3}`
from any view space orthonormal frame to any clip coordinate frame by first defining the matrix
in a specially chosen coordinate chart, and then show that one can construct any other one by
using the appropriate orthogonal transformations and changes of orientation to map from the 
desired view space coordinate system to the canonical one on one side, and mapping from the canonical
clip coordinate system to the desired clip coordinate system using the same process. This
result shows that perspective and orthographic projections are indeed coordinate-independent 
transformations. In future work, it would be desirable to find a way to define the transformations
directly in a more geometrical fashion without mentioning any coordinate systems, and then the particular
matrices would fall out going to other direction.


## Application Of The Canonical Transformations For Deriving Specific Situations

## OpenGL

We derive the perspective and orthographic transformation matrices in the standard left-handed
orthonormal frame and the standard right-handed coordinate frame.

### The Canonical Projection Matrices

The canonical projected view space coordinate system for OpenGL is the frame 
{math}`(\mathbf{0}, (\mathbf{\hat{x}}, \mathbf{\hat{y}}, \mathbf{\hat{z}}))` in {math}`\mathbb{RP}^{3}`,
where {math}`\mathbf{\hat{x}} = [1, 0, 0]^{T}`, {math}`\mathbf{\hat{y}} = [0, 1, 0]^{T}`, 
{math}`\mathbf{\hat{z}} = [0, 0, 1]^{T}`, where {math}`\mathbf{\hat{z}}` points into the view volume. 
The canonical view volume for OpenGL in normalized device coordinates is parametrized by 
{math}`[-1, 1] \times [-1, 1] \times [-1, 1]`. The canonical perpsective projection matrix for 
this parametrization is given by

```{math}
M^{C, OpenGL}_{per} =
\begin{bmatrix}
\frac{ 2 n }{r - \left( -l \right)} & 0                                   & -\frac{r + \left( -l \right)}{r - \left( -l \right)} &  0                    \\
0                                   & \frac{ 2 n }{t - \left( -b \right)} & -\frac{t + \left( -b \right)}{t - \left( -b \right)} &  0                    \\
0                                   & 0                                   &  \frac{f + n}{f - n}                                 & -\frac{2 f n }{f - n} \\
0                                   & 0                                   &  1                                                   &  0                    \\
\end{bmatrix}.
```

The canonical orthographic projection matrix for OpenGL is given by

```{math}
M^{C, OpenGL}_{orth} = 
\begin{bmatrix}
\frac{2}{r - \left( -l \right)} & 0                               & 0               & -\frac{r + \left( -l \right)}{r - \left( -l \right)} \\
0                               & \frac{2}{t - \left( -b \right)} & 0               & -\frac{t + \left( -b \right)}{t - \left( -b \right)} \\
0                               & 0                               & \frac{2}{f - n} & -\frac{f + n}{f - n}                                 \\
0                               & 0                               & 0               &  1                                                   \\
\end{bmatrix}.
```

The canonical symmetric vertical field of view perspective projection matrix for OpenGL is given by

```{math}
M^{C,OpenGL}_{per,vfov} = 
    \begin{bmatrix}
        \frac{1}{\text{aspect} \cdot \tan\left( \frac{\theta_{vfov}}{2} \right)} 
        & 0 
        & 0
        & 0 
        \\
        0 
        & \frac{1}{\tan\left( \frac{\theta_{vfov}}{2} \right)}
        & 0
        & 0
        \\
        0 
        &  0 
        & \frac{f + n}{f - n}
        & -\frac{ 2 f n }{f - n}
        \\
        0 
        & 0 
        & 1 
        & 0
        \\
    \end{bmatrix}.
```

This finishes the statement of the canonical matrices for OpenGL. We now derive the specific matrices for OpenGL.

### Right-Handed View Space

The right-handed projected view space coordinate system for OpenGL is the frame 
{math}`(\mathbf{0}, (\mathbf{\hat{x}}, \mathbf{\hat{y}}, \mathbf{\hat{z}}))` in {math}`\mathbb{RP}^{3}`,
where {math}`\mathbf{\hat{x}} = [1, 0, 0]^{T}`, {math}`\mathbf{\hat{y}} = [0, 1, 0]^{T}`, 
{math}`\mathbf{\hat{z}} = [0, 0, 1]^{T}` where {math}`\mathbf{\hat{z}}` points out of the view volume towards 
the viewer. This is a right-handed coordinate system. The clip coordinate system for OpenGL is the canonical left-handed one. 
The orthogonal transformations are given by

```{math}
Q^{OpenGL}_{lh} = Q^{OpenGL}_{rh} = I
```
where {math}`I` is the identity matrix. The change of orientation matrices are given by

```{math}
\Omega_{rh \rightarrow lh} = 
    \begin{bmatrix}
        1 & 0 &  0 & 0 \\
        0 & 1 &  0 & 0 \\
        0 & 0 & -1 & 0 \\
        0 & 0 &  0 & 1 \\
    \end{bmatrix}
,
\hspace{4 pt}
\Omega_{lh \rightarrow lh} = 
    \begin{bmatrix}
        1 & 0 & 0 & 0 \\
        0 & 1 & 0 & 0 \\
        0 & 0 & 1 & 0 \\
        0 & 0 & 0 & 1 \\
    \end{bmatrix}
.
```

To compute the projections, we need to coordinate transform from the chosen view coordinates to
the canonical view coordinates, apply the canonical projection, and then transform from the canonical clip coordinates 
to the target clip coordinates. We can map any view coordinates to and clip coordinates using the same process. Each 
coordinate transformation is the product of an orthogonal transform and a change of orientation matrix. Let us 
calculate the perspective projection

```{math}
M^{OpenGL}_{per,rh \rightarrow lh} 
    &= \Omega_{lh \rightarrow lh} Q^{OpenGL}_{lh} M^{C,OpenGL}_{per} \Omega_{rh \rightarrow lh} Q^{OpenGL}_{rh} \\
    &= \left( \Omega_{lh \rightarrow lh} Q^{OpenGL}_{lh} \right) M^{C,OpenGL}_{per} \left( \Omega_{rh \rightarrow lh} Q^{OpenGL}_{rh} \right) \\
    &= \left( \Omega_{lh \rightarrow lh} I \right) M^{C,OpenGL}_{per} \left( \Omega_{rh \rightarrow lh} I \right) \\
    &= \Omega_{lh \rightarrow lh} M^{C,OpenGL}_{per} \Omega_{rh \rightarrow lh} \\
    &= I M^{C,OpenGL}_{per} \Omega_{rh \rightarrow lh} \\
    &= M^{C,OpenGL}_{per} \Omega_{rh \rightarrow lh} \\
    &=  \begin{bmatrix}
        \frac{ 2 n }{r - \left( -l \right)} & 0                                   & -\frac{r + \left( -l \right)}{r - \left( -l \right)}  &  0                    \\
        0                                   & \frac{ 2 n }{t - \left( -b \right)} & -\frac{t + \left( -b \right)}{t - \left( -b \right)}  &  0                    \\
        0                                   & 0                                   &  \frac{f + n}{f - n}                                  & -\frac{2 f n }{f - n} \\
        0                                   & 0                                   &  1                                                    &  0                    \\
        \end{bmatrix}
        \begin{bmatrix}
            1 & 0 &  0 & 0 \\
            0 & 1 &  0 & 0 \\
            0 & 0 & -1 & 0 \\
            0 & 0 &  0 & 1 \\
       \end{bmatrix} \\
    &= \begin{bmatrix}
        \frac{ 2 n }{r - \left( -l \right)} & 0                                   &  \frac{r + \left( -l \right)}{r - \left( -l \right)} &  0                    \\
        0                                   & \frac{ 2 n }{t - \left( -b \right)} &  \frac{t + \left( -b \right)}{t - \left( -b \right)} &  0                    \\
        0                                   & 0                                   & -\frac{f + n}{f - n}                                 & -\frac{2 f n }{f - n} \\
        0                                   & 0                                   & -1                                                   &  0                    \\
        \end{bmatrix}
```

therefore

```{math}
M^{OpenGL}_{per,rh \rightarrow lh} = 
    \begin{bmatrix}
    \frac{ 2 n }{r - \left( -l \right)} & 0                                   &  \frac{r + \left( -l \right)}{r - \left( -l \right)} &  0                    \\
    0                                   & \frac{ 2 n }{t - \left( -b \right)} &  \frac{t + \left( -b \right)}{t - \left( -b \right)} &  0                    \\
    0                                   & 0                                   & -\frac{f + n}{f - n}                                 & -\frac{2 f n }{f - n} \\
    0                                   & 0                                   & -1                                                   &  0                    \\
    \end{bmatrix}.
```

Here is the calculation for the orthographic matrix

```{math}
M^{OpenGL}_{orth,rh \rightarrow lh} 
    &= \Omega_{lh \rightarrow lh} Q^{OpenGL}_{lh} M^{C, OpenGL}_{orth} \Omega_{rh \rightarrow lh} Q^{OpenGL}_{rh} \\
    &= \left( \Omega_{lh \rightarrow lh} Q^{OpenGL}_{lh} \right) M^{C, OpenGL}_{orth} \left( \Omega_{rh \rightarrow lh} Q^{OpenGL}_{rh} \right) \\
    &= \left( \Omega_{lh \rightarrow lh} I \right) M^{C, OpenGL}_{orth} \left( \Omega_{rh \rightarrow lh} I \right) \\
    &= \Omega_{lh \rightarrow lh} M^{C, OpenGL}_{orth} \Omega_{rh \rightarrow lh} \\
    &= I M^{C, OpenGL}_{orth} \Omega_{rh \rightarrow lh} \\
    &= M^{C, OpenGL}_{orth} \Omega_{rh \rightarrow lh} \\
    &=  \begin{bmatrix}
        \frac{2}{r - \left( -l \right)} & 0                               & 0               & -\frac{r + \left( -l \right)}{r - \left( -l \right)} \\
        0                               & \frac{2}{t - \left( -b \right)} & 0               & -\frac{t + \left( -b \right)}{t - \left( -b \right)} \\
        0                               & 0                               & \frac{2}{f - n} & -\frac{f + n}{f - n}                                 \\
        0                               & 0                               & 0               &  1                                                   \\
        \end{bmatrix}
        \begin{bmatrix}
            1 & 0 &  0 & 0 \\
            0 & 1 &  0 & 0 \\
            0 & 0 & -1 & 0 \\
            0 & 0 &  0 & 1 \\
       \end{bmatrix} \\
    &= \begin{bmatrix}
        \frac{2}{r - \left( -l \right)} & 0                                   &  0                & -\frac{r + \left( -l \right)}{r - \left( -l \right)} \\
        0                                   & \frac{2}{t - \left( -b \right)} &  0                & -\frac{t + \left( -b \right)}{t - \left( -b \right)} \\
        0                                   & 0                               & -\frac{2}{f - n}  & -\frac{f + n}{f - n}                                 \\
        0                                   & 0                               & 0                 &  1                                                   \\
        \end{bmatrix}
```

therefore

```{math}
M^{OpenGL}_{orth,rh \rightarrow lh} = 
    \begin{bmatrix}
    \frac{2}{r - \left( -l \right)} & 0                                   &  0                & -\frac{r + \left( -l \right)}{r - \left( -l \right)} \\
    0                                   & \frac{2}{t - \left( -b \right)} &  0                & -\frac{t + \left( -b \right)}{t - \left( -b \right)} \\
    0                                   & 0                               & -\frac{2}{f - n}  & -\frac{f + n}{f - n}                                 \\
    0                                   & 0                               & 0                 &  1                                                   \\
    \end{bmatrix}.
```

Finally, we calculate the matrix for the symmetric vertical field of view perspective projection

```{math}
M^{OpenGL}_{per,vfov,rh \rightarrow lh} 
    &= \Omega_{lh \rightarrow lh} Q^{OpenGL}_{lh} M^{C,OpenGL}_{per,vfov} \Omega_{rh \rightarrow lh} Q^{OpenGL}_{rh} \\
    &= \left( \Omega_{lh \rightarrow lh} Q^{OpenGL}_{lh} \right) M^{C,OpenGL}_{per,vfov} \left( \Omega_{rh \rightarrow lh} Q^{OpenGL}_{rh} \right) \\
    &= \left( \Omega_{lh \rightarrow lh} I \right) M^{C,OpenGL}_{per,vfov} \left( \Omega_{rh \rightarrow lh} I \right) \\
    &= \Omega_{lh \rightarrow lh} M^{C,OpenGL}_{per,vfov} \Omega_{rh \rightarrow lh} \\
    &= I M^{C,OpenGL}_{per,vfov} \Omega_{rh \rightarrow lh} \\
    &= M^{C,OpenGL}_{per,vfov} \Omega_{rh \rightarrow lh} \\
    &=  \begin{bmatrix}
        \frac{1}{\text{aspect} \cdot \tan\left( \frac{\theta_{vfov}}{2} \right)} 
        & 0 
        & 0
        & 0
        \\
        0 
        & \frac{1}{\tan\left( \frac{\theta_{vfov}}{2} \right)}
        & 0
        & 0
        \\
        0 
        &  0 
        & \frac{f + n}{f - n}
        & -\frac{ 2 f n }{f - n}
        \\
        0 
        & 0 
        & 1 
        & 0
        \\
        \end{bmatrix}
        \begin{bmatrix}
            1 & 0 &  0 & 0 \\
            0 & 1 &  0 & 0 \\
            0 & 0 & -1 & 0 \\
            0 & 0 &  0 & 1 \\
        \end{bmatrix} \\
    &=  \begin{bmatrix}
        \frac{1}{\text{aspect} \cdot \tan\left( \frac{\theta_{vfov}}{2} \right)} 
        & 0 
        & 0
        & 0
        \\
        0 
        & \frac{1}{\tan\left( \frac{\theta_{vfov}}{2} \right)}
        & 0
        & 0 
        \\
        0 
        &  0 
        & -\frac{f + n}{f - n}
        & -\frac{ 2 f n }{f - n}
        \\
        0 
        &  0 
        & -1 
        &  0
        \\
        \end{bmatrix}
```

therefore

```{math}
M^{OpenGL}_{per,vfov,rh \rightarrow lh} = 
    \begin{bmatrix}
        \frac{1}{\text{aspect} \cdot \tan\left( \frac{\theta_{vfov}}{2} \right)} 
        & 0 
        & 0
        & 0
        \\
        0 
        & \frac{1}{\tan\left( \frac{\theta_{vfov}}{2} \right)}
        & 0
        & 0
        \\
        0 
        &  0 
        & -\frac{f + n}{f - n}
        & -\frac{ 2 f n }{f - n}
        \\
        0 
        &  0 
        & -1 
        &  0
        \\
    \end{bmatrix}
.
```

This completes the derivation of the matrices for the right-handed OpenGL projected view space coordinates.

### Left-Handed View Space

The left-handed projected view space coordinate system for OpenGL is the frame 
{math}`(\mathbf{0}, (\mathbf{\hat{x}}, \mathbf{\hat{y}}, \mathbf{\hat{z}}))` in {math}`\mathbb{RP}^{3}`,
where {math}`\mathbf{\hat{x}} = [1, 0, 0]^{T}`, {math}`\mathbf{\hat{y}} = [0, 1, 0]^{T}`, 
{math}`\mathbf{\hat{z}} = [0, 0, 1]^{T}` where {math}`\mathbf{\hat{z}}` points into the view volume away from 
the viewer. This is a left-handed coordinate system. The clip coordinate system is the canonical left-handed one. 
The orthogonal transformation is given by

```{math}
Q^{OpenGL}_{lh} = I
```
where {math}`I` is the identity matrix. The change of orientation matrix are given by

```{math}
\Omega_{lh \rightarrow lh} = 
    \begin{bmatrix}
        1 & 0 & 0 & 0 \\
        0 & 1 & 0 & 0 \\
        0 & 0 & 1 & 0 \\
        0 & 0 & 0 & 1 \\
    \end{bmatrix}
.
```

To compute the projections, we need to coordinate transform from the chosen view coordinates to
the canonical view coordinates, apply the canonical projection, and then transform from the canonical clip coordinates 
to the target clip coordinates. We can map any view coordinates to and clip coordinates using the same process. Each 
coordinate transformation is the product of an orthogonal transform and a change of orientation matrix. Let us 
calculate the perspective projection

```{math}
M^{OpenGL}_{per,lh \rightarrow lh} 
    &= \Omega_{lh \rightarrow lh} Q^{OpenGL}_{lh} M^{C,OpenGL}_{per} \Omega_{lh \rightarrow lh} Q^{OpenGL}_{lh} \\
    &= \left( \Omega_{lh \rightarrow lh} Q^{OpenGL}_{lh} \right) M^{C,OpenGL}_{per} \left( \Omega_{lh \rightarrow lh} Q^{OpenGL}_{lh} \right) \\
    &= \left( \Omega_{lh \rightarrow lh} I \right) M^{C,OpenGL}_{per} \left( \Omega_{lh \rightarrow lh} I \right) \\
    &= \Omega_{lh \rightarrow lh} M^{C,OpenGL}_{per} \Omega_{lh \rightarrow lh} \\
    &= I M^{C,OpenGL}_{per} I \\
    &= M^{C,OpenGL}_{per} \\
    &=  \begin{bmatrix}
        \frac{ 2 n }{r - \left( -l \right)} & 0                                   & -\frac{r + \left( -l \right)}{r - \left( -l \right)}  &  0                    \\
        0                                   & \frac{ 2 n }{t - \left( -b \right)} & -\frac{t + \left( -b \right)}{t - \left( -b \right)}  &  0                    \\
        0                                   & 0                                   &  \frac{f + n}{f - n}                                  & -\frac{2 f n }{f - n} \\
        0                                   & 0                                   &  1                                                    &  0                    \\
        \end{bmatrix}
```

therefore

```{math}
M^{OpenGL}_{per,lh \rightarrow lh} =
    \begin{bmatrix}
        \frac{ 2 n }{r - \left( -l \right)} & 0                                   & -\frac{r + \left( -l \right)}{r - \left( -l \right)}  &  0                    \\
        0                                   & \frac{ 2 n }{t - \left( -b \right)} & -\frac{t + \left( -b \right)}{t - \left( -b \right)}  &  0                    \\
        0                                   & 0                                   &  \frac{f + n}{f - n}                                  & -\frac{2 f n }{f - n} \\
        0                                   & 0                                   &  1                                                    &  0                    \\
    \end{bmatrix}.
```

Here is the calculation for calculation for the orthographic matrix

```{math}
M^{OpenGL}_{orth,lh \rightarrow lh} 
    &= \Omega_{lh \rightarrow lh} Q^{OpenGL}_{lh} M^{C, OpenGL}_{orth} \Omega_{lh \rightarrow lh} Q^{OpenGL}_{lh} \\
    &= \left( \Omega_{lh \rightarrow lh} Q^{OpenGL}_{lh} \right) M^{C, OpenGL}_{orth} \left( \Omega_{lh \rightarrow lh} Q^{OpenGL}_{lh} \right) \\
    &= \left( \Omega_{lh \rightarrow lh} I \right) M^{C, OpenGL}_{orth} \left( \Omega_{lh \rightarrow lh} I \right) \\
    &= \Omega_{lh \rightarrow lh} M^{C, OpenGL}_{orth} \Omega_{lh \rightarrow lh} \\
    &= I M^{C, OpenGL}_{orth} I \\
    &= M^{C, OpenGL}_{orth} \\
    &=  \begin{bmatrix}
        \frac{2}{r - \left( -l \right)} & 0                               & 0               & -\frac{r + \left( -l \right)}{r - \left( -l \right)} \\
        0                               & \frac{2}{t - \left( -b \right)} & 0               & -\frac{t + \left( -b \right)}{t - \left( -b \right)} \\
        0                               & 0                               & \frac{2}{f - n} & -\frac{f + n}{f - n}                                 \\
        0                               & 0                               & 0               &  1                                                   \\
        \end{bmatrix}
```

therefore

```{math}
M^{OpenGL}_{orth,lh \rightarrow lh} = 
    \begin{bmatrix}
        \frac{2}{r - \left( -l \right)} & 0                               & 0               & -\frac{r + \left( -l \right)}{r - \left( -l \right)} \\
        0                               & \frac{2}{t - \left( -b \right)} & 0               & -\frac{t + \left( -b \right)}{t - \left( -b \right)} \\
        0                               & 0                               & \frac{2}{f - n} & -\frac{f + n}{f - n}                                 \\
        0                               & 0                               & 0               &  1                                                   \\
    \end{bmatrix}.
```

Finally, we calculate the matrix for the symmetric vertical field of view perspective projection

```{math}
M^{OpenGL}_{per,vfov,lh \rightarrow lh} 
    &= \Omega_{lh \rightarrow lh} Q^{OpenGL}_{lh} M^{C,OpenGL}_{per,vfov} \Omega_{lh \rightarrow lh} Q^{OpenGL}_{lh} \\
    &= \left( \Omega_{lh \rightarrow lh} Q^{OpenGL}_{lh} \right) M^{C,OpenGL}_{per,vfov} \left( \Omega_{lh \rightarrow lh} Q^{OpenGL}_{lh} \right) \\
    &= \left( \Omega_{lh \rightarrow lh} I \right) M^{C,OpenGL}_{per,vfov} \left( \Omega_{lh \rightarrow lh} I \right) \\
    &= \Omega_{lh \rightarrow lh} M^{C,OpenGL}_{per,vfov} \Omega_{lh \rightarrow lh} \\
    &= I M^{C,OpenGL}_{per,vfov} I \\
    &= M^{C,OpenGL}_{per,vfov} \\
    &=  \begin{bmatrix}
        \frac{1}{\text{aspect} \cdot \tan\left( \frac{\theta_{vfov}}{2} \right)} 
        & 0 
        & 0
        & 0
        \\
        0 
        & \frac{1}{\tan\left( \frac{\theta_{vfov}}{2} \right)}
        & 0
        & 0
        \\
        0 
        &  0 
        & \frac{f + n}{f - n}
        & -\frac{ 2 f n }{f - n}
        \\
        0 
        & 0 
        & 1 
        & 0
        \\
        \end{bmatrix}
```

therefore

```{math}
M^{OpenGL}_{per,vfov,lh \rightarrow lh} =
    \begin{bmatrix}
        \frac{1}{\text{aspect} \cdot \tan\left( \frac{\theta_{vfov}}{2} \right)} 
        & 0 
        & 0
        & 0
        \\
        0 
        & \frac{1}{\tan\left( \frac{\theta_{vfov}}{2} \right)}
        & 0
        & 0
        \\
        0 
        &  0 
        & \frac{f + n}{f - n}
        & -\frac{ 2 f n }{f - n}
        \\
        0 
        & 0 
        & 1 
        & 0
        \\
    \end{bmatrix}.
```

This completes the derivation of the matrices for the left-handed OpenGL projected view space coordinates.

## DirectX

We derive the perspective and orthographic transformation matrices in the standard left-handed
orthonormal frame and the standard right-handed coordinate frame.

### The Canonical Matrices

The canonical projected view space coordinate system for DirectX is the frame 
{math}`(\mathbf{0}^{T}, (\mathbf{\hat{x}}, \mathbf{\hat{y}}, \mathbf{\hat{z}})` in {math}`\mathbb{RP}^{3}`,
where {math}`\mathbf{\hat{x}} = [1, 0, 0]^{T}`, {math}`\mathbf{\hat{y}} = [0, 1, 0]^{T}`, 
{math}`\mathbf{\hat{z}} = [0, 0, 1]^{T}`, where {math}`\mathbf{\hat{z}}` points into the view volume. 
The canonical view volume in normalized device coordinates is parametrized by 
{math}`[-1, 1] \times [-1, 1] \times [0, 1]`. The canonical perpsective projection matrix for this 
parametrization is given by

```{math}
M^{C, DirectX}_{per} =
    \begin{bmatrix}
        \frac{ 2 n }{r - \left( -l \right)} & 0                                   & -\frac{r + \left( -l \right)}{r - \left( -l \right)} &  0                  \\
        0                                   & \frac{ 2 n }{t - \left( -b \right)} & -\frac{t + \left( -b \right)}{t - \left( -b \right)} &  0                  \\
        0                                   & 0                                   &  \frac{f}{f - n}                                     & -\frac{f n }{f - n} \\
        0                                   & 0                                   &  1                                                   &  0                  \\
        \end{bmatrix}.
```

The canonical orthographic projection matrix for DirectX is given by

```{math}
M^{C, DirectX}_{orth} = 
    \begin{bmatrix}
        \frac{2}{r - \left( -l \right)} & 0                               & 0               & -\frac{r + \left( -l \right)}{r - \left( -l \right)} \\
        0                               & \frac{2}{t - \left( -b \right)} & 0               & -\frac{t + \left( -b \right)}{t - \left( -b \right)} \\
        0                               & 0                               & \frac{1}{f - n} & -\frac{n}{f - n}                                     \\
        0                               & 0                               & 0               &  1                                                   \\
    \end{bmatrix}.
```

The canonical symmetric vertical field of view perspective projection matrix for DirectX is given by

```{math}
M^{C,DirectX}_{per,vfov} = 
    \begin{bmatrix}
        \frac{1}{\text{aspect} \cdot \tan\left( \frac{\theta_{vfov}}{2} \right)} 
        & 0 
        & 0
        & 0
        \\
        0 
        & \frac{1}{\tan\left( \frac{\theta_{vfov}}{2} \right)}
        & 0
        & 0 
        \\
        0 
        &  0 
        &  \frac{f}{f - n}
        & -\frac{ f n }{f - n}
        \\
        0 
        & 0 
        & 1 
        & 0
        \\
    \end{bmatrix}.
```

This finishes the statement of the canonical matrices for DirectX. We now derive the specific matrices for DirectX.

### Right-Handed View Space

The right-handed projected view space coordinate system for DirectX is the frame 
{math}`(\mathbf{0}, (\mathbf{\hat{x}}, \mathbf{\hat{y}}, \mathbf{\hat{z}}))` in {math}`\mathbb{RP}^{3}`,
where {math}`\mathbf{\hat{x}} = [1, 0, 0]^{T}`, {math}`\mathbf{\hat{y}} = [0, 1, 0]^{T}`, 
{math}`\mathbf{\hat{z}} = [0, 0, 1]^{T}` where {math}`\mathbf{\hat{z}}` points out of the view volume towards 
the viewer. This is a right-handed coordinate system. The clip coordinate system for DirectX is the canonical left-handed one. The orthogonal transformations are given by

```{math}
Q^{DirectX}_{lh} = Q^{DirectX}_{rh} = I
```
where {math}`I` is the identity matrix. The change of orientation matrices are given by

```{math}
\Omega_{rh \rightarrow lh} = 
    \begin{bmatrix}
        1 & 0 &  0 & 0 \\
        0 & 1 &  0 & 0 \\
        0 & 0 & -1 & 0 \\
        0 & 0 &  0 & 1 \\
    \end{bmatrix}
,
\hspace{4 pt}
\Omega_{lh \rightarrow lh} = 
    \begin{bmatrix}
        1 & 0 & 0 & 0 \\
        0 & 1 & 0 & 0 \\
        0 & 0 & 1 & 0 \\
        0 & 0 & 0 & 1 \\
    \end{bmatrix}.
```

To compute the projections, we need to coordinate transform from the chosen view coordinates to
the canonical view coordinates, apply the canonical projection, and then transform from the canonical clip coordinates 
to the target clip coordinates. We can map any view coordinates to and clip coordinates using the same process. Each 
coordinate transformation is the product of an orthogonal transform and a change of orientation matrix. Let us 
calculate the perspective projection

```{math}
M^{DirectX}_{per,rh \rightarrow lh} 
    &= \Omega_{lh \rightarrow lh} Q^{DirectX}_{lh} M^{C,DirectX}_{per} \Omega_{rh \rightarrow lh} Q^{DirectX}_{rh} \\
    &= \left( \Omega_{lh \rightarrow lh} Q^{DirectX}_{lh} \right) M^{C,DirectX}_{per} \left( \Omega_{rh \rightarrow lh} Q^{DirectX}_{rh} \right) \\
    &= \left( \Omega_{lh \rightarrow lh} I \right) M^{C,DirectX}_{per} \left( \Omega_{rh \rightarrow lh} I \right) \\
    &= \Omega_{lh \rightarrow lh} M^{C,DirectX}_{per} \Omega_{rh \rightarrow lh} \\
    &= I M^{C,DirectX}_{per} \Omega_{rh \rightarrow lh} \\
    &= M^{C,DirectX}_{per} \Omega_{rh \rightarrow lh} \\
    &=  \begin{bmatrix}
        \frac{ 2 n }{r - \left( -l \right)} & 0                                   & -\frac{r + \left( -l \right)}{r - \left( -l \right)} &  0                  \\
        0                                   & \frac{ 2 n }{t - \left( -b \right)} & -\frac{t + \left( -b \right)}{t - \left( -b \right)} &  0                  \\
        0                                   & 0                                   &  \frac{f}{f - n}                                     & -\frac{f n }{f - n} \\
        0                                   & 0                                   &  1                                                   &  0                  \\
        \end{bmatrix}
        \begin{bmatrix}
            1 & 0 &  0 & 0 \\
            0 & 1 &  0 & 0 \\
            0 & 0 & -1 & 0 \\
            0 & 0 &  0 & 1 \\
       \end{bmatrix} \\
    &= \begin{bmatrix}
        \frac{ 2 n }{r - \left( -l \right)} & 0                                   &  \frac{r + \left( -l \right)}{r - \left( -l \right)} &  0                   \\
        0                                   & \frac{ 2 n }{t - \left( -b \right)} &  \frac{t + \left( -b \right)}{t - \left( -b \right)} &  0                   \\
        0                                   & 0                                   & -\frac{f}{f - n}                                     & -\frac{ f n }{f - n} \\
        0                                   & 0                                   & -1                                                   &  0                   \\
        \end{bmatrix}
```

therefore

```{math}
M^{DirectX}_{per,rh \rightarrow lh} = 
    \begin{bmatrix}
    \frac{ 2 n }{r - \left( -l \right)} & 0                                   &  \frac{r + \left( -l \right)}{r - \left( -l \right)} &  0                   \\
    0                                   & \frac{ 2 n }{t - \left( -b \right)} &  \frac{t + \left( -b \right)}{t - \left( -b \right)} &  0                   \\
    0                                   & 0                                   & -\frac{f}{f - n}                                     & -\frac{ f n }{f - n} \\
    0                                   & 0                                   & -1                                                   &  0                   \\
    \end{bmatrix}.
```

Here is the calculation for the orthographic matrix

```{math}
M^{DirectX}_{orth,rh \rightarrow lh} 
    &= \Omega_{lh \rightarrow lh} Q^{DirectX}_{lh} M^{C, DirectX}_{orth} \Omega_{rh \rightarrow lh} Q^{DirectX}_{rh} \\
    &= \left( \Omega_{lh \rightarrow lh} Q^{DirectX}_{lh} \right) M^{C, DirectX}_{orth} \left( \Omega_{rh \rightarrow lh} Q^{DirectX}_{rh} \right) \\
    &= \left( \Omega_{lh \rightarrow lh} I \right) M^{C, DirectX}_{orth} \left( \Omega_{rh \rightarrow lh} I \right) \\
    &= \Omega_{lh \rightarrow lh} M^{C, DirectX}_{orth} \Omega_{rh \rightarrow lh} \\
    &= I M^{C, DirectX}_{orth} \Omega_{rh \rightarrow lh} \\
    &= M^{C, DirectX}_{orth} \Omega_{rh \rightarrow lh} \\
    &=  \begin{bmatrix}
        \frac{2}{r - \left( -l \right)} & 0                               & 0               & -\frac{r + \left( -l \right)}{r - \left( -l \right)} \\
        0                               & \frac{2}{t - \left( -b \right)} & 0               & -\frac{t + \left( -b \right)}{t - \left( -b \right)} \\
        0                               & 0                               & \frac{1}{f - n} & -\frac{n}{f - n}                                     \\
        0                               & 0                               & 0               &  1                                                   \\
        \end{bmatrix}
        \begin{bmatrix}
            1 & 0 &  0 & 0 \\
            0 & 1 &  0 & 0 \\
            0 & 0 & -1 & 0 \\
            0 & 0 &  0 & 1 \\
       \end{bmatrix} \\
    &= \begin{bmatrix}
        \frac{2}{r - \left( -l \right)} & 0                               &  0                & -\frac{r + \left( -l \right)}{r - \left( -l \right)} \\
        0                               & \frac{2}{t - \left( -b \right)} &  0                & -\frac{t + \left( -b \right)}{t - \left( -b \right)} \\
        0                               & 0                               & -\frac{1}{f - n}  & -\frac{n}{f - n}                                     \\
        0                               & 0                               &  0                &  1                                                   \\
        \end{bmatrix}
```

therefore

```{math}
M^{DirectX}_{orth,rh \rightarrow lh} = 
    \begin{bmatrix}
    \frac{2}{r - \left( -l \right)} & 0                               &  0                & -\frac{r + \left( -l \right)}{r - \left( -l \right)} \\
    0                               & \frac{2}{t - \left( -b \right)} &  0                & -\frac{t + \left( -b \right)}{t - \left( -b \right)} \\
    0                               & 0                               & -\frac{1}{f - n}  & -\frac{n}{f - n}                                     \\
    0                               & 0                               &  0                &  1                                                   \\
    \end{bmatrix}.
```

Finally, we calculate the matrix for the symmetric vertical field of view perspective projection

```{math}
M^{DirectX}_{per,vfov,rh \rightarrow lh} 
    &= \Omega_{lh \rightarrow lh} Q^{DirectX}_{lh} M^{C,DirectX}_{per,vfov} \Omega_{rh \rightarrow lh} Q^{DirectX}_{rh} \\
    &= \left( \Omega_{lh \rightarrow lh} Q^{DirectX}_{lh} \right) M^{C,DirectX}_{per,vfov} \left( \Omega_{rh \rightarrow lh} Q^{DirectX}_{rh} \right) \\
    &= \left( \Omega_{lh \rightarrow lh} I \right) M^{C,DirectX}_{per,vfov} \left( \Omega_{rh \rightarrow lh} I \right) \\
    &= \Omega_{lh \rightarrow lh} M^{C,DirectX}_{per,vfov} \Omega_{rh \rightarrow lh} \\
    &= I M^{C,DirectX}_{per,vfov} \Omega_{rh \rightarrow lh} \\
    &= M^{C,DirectX}_{per,vfov} \Omega_{rh \rightarrow lh} \\
    &=  \begin{bmatrix}
        \frac{1}{\text{aspect} \cdot \tan\left( \frac{\theta_{vfov}}{2} \right)} 
        & 0 
        & 0
        & 0 
        \\
        0 
        & \frac{1}{\tan\left( \frac{\theta_{vfov}}{2} \right)}
        & 0
        & 0 
        \\
        0 
        &  0 
        & \frac{f}{f - n}
        & -\frac{ f n }{f - n}
        \\
        0 
        & 0 
        & 1 
        & 0
        \\
        \end{bmatrix}
        \begin{bmatrix}
            1 & 0 &  0 & 0 \\
            0 & 1 &  0 & 0 \\
            0 & 0 & -1 & 0 \\
            0 & 0 &  0 & 1 \\
        \end{bmatrix} \\
    &=  \begin{bmatrix}
        \frac{1}{\text{aspect} \cdot \tan\left( \frac{\theta_{vfov}}{2} \right)} 
        & 0 
        & 0
        & 0 
        \\
        0 
        & \frac{1}{\tan\left( \frac{\theta_{vfov}}{2} \right)}
        & 0
        & 0
        \\
        0 
        &  0 
        & -\frac{f}{f - n}
        & -\frac{ f n }{f - n}
        \\
        0 
        &  0 
        & -1 
        &  0
        \\
        \end{bmatrix}
```

therefore

```{math}
M^{DirectX}_{per,vfov,rh \rightarrow lh} = 
    \begin{bmatrix}
        \frac{1}{\text{aspect} \cdot \tan\left( \frac{\theta_{vfov}}{2} \right)} 
        & 0 
        & 0
        & 0 
        \\
        0 
        & \frac{1}{\tan\left( \frac{\theta_{vfov}}{2} \right)}
        & 0
        & 0 
        \\
        0 
        &  0 
        & -\frac{f}{f - n}
        & -\frac{ f n }{f - n}
        \\
        0 
        &  0 
        & -1 
        &  0
        \\
    \end{bmatrix}.
```

This completes the derivation of the matrices for the right-handed DirectX projected view space coordinates.

### Left-Handed View Space

The left-handed projected view space coordinate system for OpenGL is the frame 
{math}`(\mathbf{0}, (\mathbf{\hat{x}}, \mathbf{\hat{y}}, \mathbf{\hat{z}}))` in {math}`\mathbb{RP}^{3}`,
where {math}`\mathbf{\hat{x}} = [1, 0, 0]^{T}`, {math}`\mathbf{\hat{y}} = [0, 1, 0]^{T}`, 
{math}`\mathbf{\hat{z}} = [0, 0, 1]^{T}` where {math}`\mathbf{\hat{z}}` points into the view volume away from 
the viewer. This is a right-handed coordinate system. The clip coordinate system is the canonical left-handed one. 
The orthogonal transformation is given by

```{math}
Q^{DirectX}_{lh} = I
```
where {math}`I` is the identity matrix. The change of orientation matrix are given by

```{math}
\Omega_{lh \rightarrow lh} = 
    \begin{bmatrix}
        1 & 0 & 0 & 0 \\
        0 & 1 & 0 & 0 \\
        0 & 0 & 1 & 0 \\
        0 & 0 & 0 & 1 \\
    \end{bmatrix}.
```

To compute the projections, we need to coordinate transform from the chosen view coordinates to
the canonical view coordinates, apply the canonical projection, and then transform from the canonical clip coordinates 
to the target clip coordinates. We can map any view coordinates to and clip coordinates using the same process. Each 
coordinate transformation is the product of an orthogonal transform and a change of orientation matrix. Let us 
calculate the perspective projection

```{math}
M^{DirectX}_{per,lh \rightarrow lh} 
    &= \Omega_{lh \rightarrow lh} Q^{DirectX}_{lh} M^{C,DirectX}_{per} \Omega_{lh \rightarrow lh} Q^{DirectX}_{lh} \\
    &= \left( \Omega_{lh \rightarrow lh} Q^{DirectX}_{lh} \right) M^{C,DirectX}_{per} \left( \Omega_{lh \rightarrow lh} Q^{DirectX}_{lh} \right) \\
    &= \left( \Omega_{lh \rightarrow lh} I \right) M^{C,DirectX}_{per} \left( \Omega_{rl \rightarrow lh} I \right) \\
    &= \Omega_{lh \rightarrow lh} M^{C,DirectX}_{per} \Omega_{lh \rightarrow lh} \\
    &= I M^{C,DirectX}_{per} I \\
    &= M^{C,DirectX}_{per} \\
    &=  \begin{bmatrix}
        \frac{ 2 n }{r - \left( -l \right)} & 0                                   & -\frac{r + \left( -l \right)}{r - \left( -l \right)} &  0                  \\
        0                                   & \frac{ 2 n }{t - \left( -b \right)} & -\frac{t + \left( -b \right)}{t - \left( -b \right)} &  0                  \\
        0                                   & 0                                   &  \frac{f}{f - n}                                     & -\frac{f n }{f - n} \\
        0                                   & 0                                   &  1                                                   &  0                  \\
        \end{bmatrix}
```

therefore

```{math}
M^{DirectX}_{per,lh \rightarrow lh} = 
    \begin{bmatrix}
        \frac{ 2 n }{r - \left( -l \right)} & 0                                   & -\frac{r + \left( -l \right)}{r - \left( -l \right)} &  0                  \\
        0                                   & \frac{ 2 n }{t - \left( -b \right)} & -\frac{t + \left( -b \right)}{t - \left( -b \right)} &  0                  \\
        0                                   & 0                                   &  \frac{f}{f - n}                                     & -\frac{f n }{f - n} \\
        0                                   & 0                                   &  1                                                   &  0                  \\
    \end{bmatrix}.
```

Here is the calculation for calculation for the orthographic matrix

```{math}
M^{DirectX}_{orth,lh \rightarrow lh} 
    &= \Omega_{lh \rightarrow lh} Q^{DirectX}_{lh} M^{C, DirectX}_{orth} \Omega_{lh \rightarrow lh} Q^{DirectX}_{lh} \\
    &= \left( \Omega_{lh \rightarrow lh} Q^{DirectX}_{lh} \right) M^{C, DirectX}_{orth} \left( \Omega_{lh \rightarrow lh} Q^{DirectX}_{lh} \right) \\
    &= \left( \Omega_{lh \rightarrow lh} I \right) M^{C, DirectX}_{orth} \left( \Omega_{lh \rightarrow lh} I \right) \\
    &= \Omega_{lh \rightarrow lh} M^{C, DirectX}_{orth} \Omega_{lh \rightarrow lh} \\
    &= I M^{C, DirectX}_{orth} I \\
    &= M^{C, DirectX}_{orth} \\
    &=  \begin{bmatrix}
        \frac{2}{r - \left( -l \right)} & 0                               & 0               & -\frac{r + \left( -l \right)}{r - \left( -l \right)} \\
        0                               & \frac{2}{t - \left( -b \right)} & 0               & -\frac{t + \left( -b \right)}{t - \left( -b \right)} \\
        0                               & 0                               & \frac{1}{f - n} & -\frac{n}{f - n}                                     \\
        0                               & 0                               & 0               &  1                                                   \\
        \end{bmatrix}
```

therefore

```{math}
M^{DirectX}_{orth,lh \rightarrow lh} = 
    \begin{bmatrix}
        \frac{2}{r - \left( -l \right)} & 0                               & 0               & -\frac{r + \left( -l \right)}{r - \left( -l \right)} \\
        0                               & \frac{2}{t - \left( -b \right)} & 0               & -\frac{t + \left( -b \right)}{t - \left( -b \right)} \\
        0                               & 0                               & \frac{1}{f - n} & -\frac{n}{f - n}                                     \\
        0                               & 0                               & 0               &  1                                                   \\
    \end{bmatrix}.
```

Finally, we calculate the matrix for the symmetric vertical field of view perspective projection

```{math}
M^{DirectX}_{per,vfov,lh \rightarrow lh} 
    &= \Omega_{lh \rightarrow lh} Q^{DirectX}_{lh} M^{C,DirectX}_{per,vfov} \Omega_{lh \rightarrow lh} Q^{DirectX}_{lh} \\
    &= \left( \Omega_{lh \rightarrow lh} Q^{DirectX}_{lh} \right) M^{C,DirectX}_{per,vfov} \left( \Omega_{lh \rightarrow lh} Q^{DirectX}_{lh} \right) \\
    &= \left( \Omega_{lh \rightarrow lh} I \right) M^{C,DirectX}_{per,vfov} \left( \Omega_{lh \rightarrow lh} I \right) \\
    &= \Omega_{lh \rightarrow lh} M^{C,DirectX}_{per,vfov} \Omega_{lh \rightarrow lh} \\
    &= I M^{C,DirectX}_{per,vfov} I \\
    &= M^{C,DirectX}_{per,vfov} \\
    &=  \begin{bmatrix}
        \frac{1}{\text{aspect} \cdot \tan\left( \frac{\theta_{vfov}}{2} \right)} 
        & 0 
        & 0
        & 0
        \\
        0 
        & \frac{1}{\tan\left( \frac{\theta_{vfov}}{2} \right)}
        & 0
        & 0
        \\
        0 
        &  0 
        & \frac{f}{f - n}
        & -\frac{ f n }{f - n}
        \\
        0 
        & 0 
        & 1 
        & 0
        \\
        \end{bmatrix}
```

therefore

```{math}
M^{DirectX}_{per,vfov,lh \rightarrow lh} = 
    \begin{bmatrix}
        \frac{1}{\text{aspect} \cdot \tan\left( \frac{\theta_{vfov}}{2} \right)} 
        & 0 
        & 0
        & 0 
        \\
        0 
        & \frac{1}{\tan\left( \frac{\theta_{vfov}}{2} \right)}
        & 0
        & 0 
        \\
        0 
        &  0 
        & \frac{f}{f - n}
        & -\frac{ f n }{f - n}
        \\
        0 
        & 0 
        & 1 
        & 0
        \\
    \end{bmatrix}.
```

This completes the derivation of the matrices for the left-handed DirectX projected view space coordinates.

## Metal

We derive the perspective and orthographic transformation matrices in the standard left-handed
orthonormal frame and the standard right-handed coordinate frame.

### The Canonical Matrices

The canonical projected view space coordinate system for Metal is the frame 
{math}`(\mathbf{0}^{T}, (\mathbf{\hat{x}}, \mathbf{\hat{y}}, \mathbf{\hat{z}})` in {math}`\mathbb{RP}^{3}`,
where {math}`\mathbf{\hat{x}} = [1, 0, 0]^{T}`, {math}`\mathbf{\hat{y}} = [0, 1, 0]^{T}`, 
{math}`\mathbf{\hat{z}} = [0, 0, 1]^{T}`, where {math}`\mathbf{\hat{z}}` points into the view volume. 
The canonical view volume for for Metal in normalized device coordinates is parametrized by 
{math}`[-1, 1] \times [-1, 1] \times [0, 1]`. The canonical perpsective projection matrix for 
Metal is given by

```{math}
M^{C, Metal}_{per} =
\begin{bmatrix}
\frac{ 2 n }{r - \left( -l \right)} & 0                                   & -\frac{r + \left( -l \right)}{r - \left( -l \right)} &  0                  \\
0                                   & \frac{ 2 n }{t - \left( -b \right)} & -\frac{t + \left( -b \right)}{t - \left( -b \right)} &  0                  \\
0                                   & 0                                   &  \frac{f}{f - n}                                     & -\frac{f n }{f - n} \\
0                                   & 0                                   &  1                                                   &  0                  \\
\end{bmatrix}
.
```

The canonical orthographic projection matrix for Metal is given by

```{math}
M^{C, Metal}_{orth} = 
\begin{bmatrix}
\frac{2}{r - \left( -l \right)} & 0                               & 0               & -\frac{r + \left( -l \right)}{r - \left( -l \right)} \\
0                               & \frac{2}{t - \left( -b \right)} & 0               & -\frac{t + \left( -b \right)}{t - \left( -b \right)} \\
0                               & 0                               & \frac{1}{f - n} & -\frac{n}{f - n}                                     \\
0                               & 0                               & 0               &  1                                                   \\
\end{bmatrix}
.
```

The canonical symmetric vertical field of view perspective projection matrix for Metal is given by

```{math}
M^{C,Metal}_{per,vfov} = 
    \begin{bmatrix}
        \frac{1}{\text{aspect} \cdot \tan\left( \frac{\theta_{vfov}}{2} \right)} 
        & 0 
        & 0
        & 0
        \\
        0 
        & \frac{1}{\tan\left( \frac{\theta_{vfov}}{2} \right)}
        & 0
        & 0
        \\
        0 
        &  0 
        &  \frac{f}{f - n}
        & -\frac{ f n }{f - n}
        \\
        0 
        & 0 
        & 1 
        & 0
        \\
    \end{bmatrix}.
```

This finishes the statement of the canonical matrices for Metal. We now derive the specific matrices for Metal.

### Right-Handed View Space

The right-handed projected view space coordinate system for Metal is the frame 
{math}`(\mathbf{0}, (\mathbf{\hat{x}}, \mathbf{\hat{y}}, \mathbf{\hat{z}}))` in {math}`\mathbb{RP}^{3}`,
where {math}`\mathbf{\hat{x}} = [1, 0, 0]^{T}`, {math}`\mathbf{\hat{y}} = [0, 1, 0]^{T}`, 
{math}`\mathbf{\hat{z}} = [0, 0, 1]^{T}` where {math}`\mathbf{\hat{z}}` points out of the view volume towards 
the viewer. This is a right-handed coordinate system. The clip coordinate system for Metal is the canonical left-handed one. The orthogonal transformations are given by

```{math}
Q^{Metal}_{lh} = Q^{Metal}_{rh} = I
```
where {math}`I` is the identity matrix. The change of orientation matrices are given by

```{math}
\Omega_{rh \rightarrow lh} = 
    \begin{bmatrix}
        1 & 0 &  0 & 0 \\
        0 & 1 &  0 & 0 \\
        0 & 0 & -1 & 0 \\
        0 & 0 &  0 & 1 \\
    \end{bmatrix}
,
\hspace{4 pt}
\Omega_{lh \rightarrow lh} = 
    \begin{bmatrix}
        1 & 0 & 0 & 0 \\
        0 & 1 & 0 & 0 \\
        0 & 0 & 1 & 0 \\
        0 & 0 & 0 & 1 \\
    \end{bmatrix}.
```

To compute the projections, we need to coordinate transform from the chosen view coordinates to
the canonical view coordinates, apply the canonical projection, and then transform from the canonical clip coordinates 
to the target clip coordinates. We can map any view coordinates to and clip coordinates using the same process. Each 
coordinate transformation is the product of an orthogonal transform and a change of orientation matrix. Let us 
calculate the perspective projection

```{math}
M^{Metal}_{per,rh \rightarrow lh} 
    &= \Omega_{lh \rightarrow lh} Q^{Metal}_{lh} M^{C,Metal}_{per} \Omega_{rh \rightarrow lh} Q^{Metal}_{rh} \\
    &= \left( \Omega_{lh \rightarrow lh} Q^{Metal}_{lh} \right) M^{C,Metal}_{per} \left( \Omega_{rh \rightarrow lh} Q^{Metal}_{rh} \right) \\
    &= \left( \Omega_{lh \rightarrow lh} I \right) M^{C,Metal}_{per} \left( \Omega_{rh \rightarrow lh} I \right) \\
    &= \Omega_{lh \rightarrow lh} M^{C,Metal}_{per} \Omega_{rh \rightarrow lh} \\
    &= I M^{C,Metal}_{per} \Omega_{rh \rightarrow lh} \\
    &= M^{C,Metal}_{per} \Omega_{rh \rightarrow lh} \\
    &=  \begin{bmatrix}
        \frac{ 2 n }{r - \left( -l \right)} & 0                                   & -\frac{r + \left( -l \right)}{r - \left( -l \right)} &  0                  \\
        0                                   & \frac{ 2 n }{t - \left( -b \right)} & -\frac{t + \left( -b \right)}{t - \left( -b \right)} &  0                  \\
        0                                   & 0                                   &  \frac{f}{f - n}                                     & -\frac{f n }{f - n} \\
        0                                   & 0                                   &  1                                                   &  0
        \end{bmatrix}
        \begin{bmatrix}
            1 & 0 &  0 & 0 \\
            0 & 1 &  0 & 0 \\
            0 & 0 & -1 & 0 \\
            0 & 0 &  0 & 1
       \end{bmatrix} \\
    &= \begin{bmatrix}
        \frac{ 2 n }{r - \left( -l \right)} & 0                                   &  \frac{r + \left( -l \right)}{r - \left( -l \right)} &  0                   \\
        0                                   & \frac{ 2 n }{t - \left( -b \right)} &  \frac{t + \left( -b \right)}{t - \left( -b \right)} &  0                   \\
        0                                   & 0                                   & -\frac{f}{f - n}                                     & -\frac{ f n }{f - n} \\
        0                                   & 0                                   & -1                                                   &  0
        \end{bmatrix}
```

therefore

```{math}
M^{Metal}_{per,rh \rightarrow lh} = 
    \begin{bmatrix}
    \frac{ 2 n }{r - \left( -l \right)} & 0                                   &  \frac{r + \left( -l \right)}{r - \left( -l \right)} &  0                   \\
    0                                   & \frac{ 2 n }{t - \left( -b \right)} &  \frac{t + \left( -b \right)}{t - \left( -b \right)} &  0                   \\
    0                                   & 0                                   & -\frac{f}{f - n}                                     & -\frac{ f n }{f - n} \\
    0                                   & 0                                   & -1                                                   &  0
    \end{bmatrix}.
```

Here is the calculation for the orthographic matrix

```{math}
M^{Metal}_{orth,rh \rightarrow lh} 
    &= \Omega_{lh \rightarrow lh} Q^{Metal}_{lh} M^{C, Metal}_{orth} \Omega_{rh \rightarrow lh} Q^{Metal}_{rh} \\
    &= \left( \Omega_{lh \rightarrow lh} Q^{Metal}_{lh} \right) M^{C, Metal}_{orth} \left( \Omega_{rh \rightarrow lh} Q^{Metal}_{rh} \right) \\
    &= \left( \Omega_{lh \rightarrow lh} I \right) M^{C, Metal}_{orth} \left( \Omega_{rh \rightarrow lh} I \right) \\
    &= \Omega_{lh \rightarrow lh} M^{C, Metal}_{orth} \Omega_{rh \rightarrow lh} \\
    &= I M^{C, Metal}_{orth} \Omega_{rh \rightarrow lh} \\
    &= M^{C, Metal}_{orth} \Omega_{rh \rightarrow lh} \\
    &=  \begin{bmatrix}
        \frac{2}{r - \left( -l \right)} & 0                               & 0               & -\frac{r + \left( -l \right)}{r - \left( -l \right)} \\
        0                               & \frac{2}{t - \left( -b \right)} & 0               & -\frac{t + \left( -b \right)}{t - \left( -b \right)} \\
        0                               & 0                               & \frac{1}{f - n} & -\frac{n}{f - n}                                     \\
        0                               & 0                               & 0               &  1
        \end{bmatrix}
        \begin{bmatrix}
            1 & 0 &  0 & 0 \\
            0 & 1 &  0 & 0 \\
            0 & 0 & -1 & 0 \\
            0 & 0 &  0 & 1
       \end{bmatrix} \\
    &= \begin{bmatrix}
        \frac{2}{r - \left( -l \right)} & 0                               &  0                & -\frac{r + \left( -l \right)}{r - \left( -l \right)} \\
        0                               & \frac{2}{t - \left( -b \right)} &  0                & -\frac{t + \left( -b \right)}{t - \left( -b \right)} \\
        0                               & 0                               & -\frac{1}{f - n}  & -\frac{n}{f - n}                                     \\
        0                               & 0                               &  0                &  1
        \end{bmatrix}
```

therefore

```{math}
M^{Metal}_{orth,rh \rightarrow lh} = 
    \begin{bmatrix}
    \frac{2}{r - \left( -l \right)} & 0                               &  0                & -\frac{r + \left( -l \right)}{r - \left( -l \right)} \\
    0                               & \frac{2}{t - \left( -b \right)} &  0                & -\frac{t + \left( -b \right)}{t - \left( -b \right)} \\
    0                               & 0                               & -\frac{1}{f - n}  & -\frac{n}{f - n}                                     \\
    0                               & 0                               &  0                &  1
    \end{bmatrix}.
```

Finally, we calculate the matrix for the symmetric vertical field of view perspective projection

```{math}
M^{Metal}_{per,vfov,rh \rightarrow lh} 
    &= \Omega_{lh \rightarrow lh} Q^{Metal}_{lh} M^{C,Metal}_{per,vfov} \Omega_{rh \rightarrow lh} Q^{Metal}_{rh} \\
    &= \left( \Omega_{lh \rightarrow lh} Q^{Metal}_{lh} \right) M^{C,Metal}_{per,vfov} \left( \Omega_{rh \rightarrow lh} Q^{Metal}_{rh} \right) \\
    &= \left( \Omega_{lh \rightarrow lh} I \right) M^{C,Metal}_{per,vfov} \left( \Omega_{rh \rightarrow lh} I \right) \\
    &= \Omega_{lh \rightarrow lh} M^{C,Metal}_{per,vfov} \Omega_{rh \rightarrow lh} \\
    &= I M^{C,Metal}_{per,vfov} \Omega_{rh \rightarrow lh} \\
    &= M^{C,Metal}_{per,vfov} \Omega_{rh \rightarrow lh} \\
    &=  \begin{bmatrix}
        \frac{1}{\text{aspect} \cdot \tan\left( \frac{\theta_{vfov}}{2} \right)} 
        & 0 
        & 0
        & 0 
        \\
        0 
        & \frac{1}{\tan\left( \frac{\theta_{vfov}}{2} \right)}
        & 0
        & 0
        \\
        0 
        &  0 
        & \frac{f}{f - n}
        & -\frac{ f n }{f - n}
        \\
        0 
        & 0 
        & 1 
        & 0
        \\
        \end{bmatrix}
        \begin{bmatrix}
            1 & 0 &  0 & 0 \\
            0 & 1 &  0 & 0 \\
            0 & 0 & -1 & 0 \\
            0 & 0 &  0 & 1 \\
        \end{bmatrix} \\
    &=  \begin{bmatrix}
        \frac{1}{\text{aspect} \cdot \tan\left( \frac{\theta_{vfov}}{2} \right)} 
        & 0 
        & 0
        & 0
        \\
        0 
        & \frac{1}{\tan\left( \frac{\theta_{vfov}}{2} \right)}
        & 0
        & 0
        \\
        0 
        &  0 
        & -\frac{f}{f - n}
        & -\frac{ f n }{f - n}
        \\
        0 
        &  0 
        & -1 
        &  0
        \\
        \end{bmatrix}
```

therefore

```{math}
M^{Metal}_{per,vfov,rh \rightarrow lh} = 
    \begin{bmatrix}
        \frac{1}{\text{aspect} \cdot \tan\left( \frac{\theta_{vfov}}{2} \right)} 
        & 0 
        & 0
        & 0
        \\
        0 
        & \frac{1}{\tan\left( \frac{\theta_{vfov}}{2} \right)}
        & 0
        & 0
        \\
        0 
        &  0 
        & -\frac{f}{f - n}
        & -\frac{ f n }{f - n}
        \\
        0 
        &  0 
        & -1 
        &  0
        \\
    \end{bmatrix}.
```

This completes the derivation of the matrices for the right-handed Metal projected view space coordinates.

### Left-Handed View Space

The left-handed projected view space coordinate system for Metal is the frame 
{math}`(\mathbf{0}, (\mathbf{\hat{x}}, \mathbf{\hat{y}}, \mathbf{\hat{z}}))` in {math}`\mathbb{RP}^{3}`,
where {math}`\mathbf{\hat{x}} = [1, 0, 0]^{T}`, {math}`\mathbf{\hat{y}} = [0, 1, 0]^{T}`, 
{math}`\mathbf{\hat{z}} = [0, 0, 1]^{T}` where {math}`\mathbf{\hat{z}}` points into the view volume away from 
the viewer. This is a right-handed coordinate system. The clip coordinate system is the canonical left-handed one. 
The orthogonal transformation is given by

```{math}
Q^{Metal}_{lh} = I
```
where {math}`I` is the identity matrix. The change of orientation matrix are given by

```{math}
\Omega_{lh \rightarrow lh} = 
    \begin{bmatrix}
        1 & 0 & 0 & 0 \\
        0 & 1 & 0 & 0 \\
        0 & 0 & 1 & 0 \\
        0 & 0 & 0 & 1 \\
    \end{bmatrix}.
```

To compute the projections, we need to coordinate transform from the chosen view coordinates to
the canonical view coordinates, apply the canonical projection, and then transform from the canonical clip coordinates 
to the target clip coordinates. We can map any view coordinates to and clip coordinates using the same process. Each 
coordinate transformation is the product of an orthogonal transform and a change of orientation matrix. Let us 
calculate the perspective projection


```{math}
M^{Metal}_{per,lh \rightarrow lh} 
    &= \Omega_{lh \rightarrow lh} Q^{Metal}_{lh} M^{C,Metal}_{per} \Omega_{lh \rightarrow lh} Q^{Metal}_{lh} \\
    &= \left( \Omega_{lh \rightarrow lh} Q^{Metal}_{lh} \right) M^{C,Metal}_{per} \left( \Omega_{lh \rightarrow lh} Q^{Metal}_{lh} \right) \\
    &= \left( \Omega_{lh \rightarrow lh} I \right) M^{C,Metal}_{per} \left( \Omega_{rl \rightarrow lh} I \right) \\
    &= \Omega_{lh \rightarrow lh} M^{C,Metal}_{per} \Omega_{lh \rightarrow lh} \\
    &= I M^{C,Metal}_{per} I \\
    &= M^{C,Metal}_{per} \\
    &=  \begin{bmatrix}
        \frac{ 2 n }{r - \left( -l \right)} & 0                                   & -\frac{r + \left( -l \right)}{r - \left( -l \right)} &  0                  \\
        0                                   & \frac{ 2 n }{t - \left( -b \right)} & -\frac{t + \left( -b \right)}{t - \left( -b \right)} &  0                  \\
        0                                   & 0                                   &  \frac{f}{f - n}                                     & -\frac{f n }{f - n} \\
        0                                   & 0                                   &  1                                                   &  0
        \end{bmatrix}
```

therefore

```{math}
M^{Metal}_{per,lh \rightarrow lh} = 
    \begin{bmatrix}
        \frac{ 2 n }{r - \left( -l \right)} & 0                                   & -\frac{r + \left( -l \right)}{r - \left( -l \right)} &  0                  \\
        0                                   & \frac{ 2 n }{t - \left( -b \right)} & -\frac{t + \left( -b \right)}{t - \left( -b \right)} &  0                  \\
        0                                   & 0                                   &  \frac{f}{f - n}                                     & -\frac{f n }{f - n} \\
        0                                   & 0                                   &  1                                                   &  0
    \end{bmatrix}.
```

Here is the calculation for calculation for the orthographic matrix

```{math}
M^{Metal}_{orth,lh \rightarrow lh} 
    &= \Omega_{lh \rightarrow lh} Q^{Metal}_{lh} M^{C, Metal}_{orth} \Omega_{lh \rightarrow lh} Q^{Metal}_{lh} \\
    &= \left( \Omega_{lh \rightarrow lh} Q^{Metal}_{lh} \right) M^{C, Metal}_{orth} \left( \Omega_{lh \rightarrow lh} Q^{Metal}_{lh} \right) \\
    &= \left( \Omega_{lh \rightarrow lh} I \right) M^{C, Metal}_{orth} \left( \Omega_{lh \rightarrow lh} I \right) \\
    &= \Omega_{lh \rightarrow lh} M^{C, Metal}_{orth} \Omega_{lh \rightarrow lh} \\
    &= I M^{C, Metal}_{orth} I \\
    &= M^{C, Metal}_{orth} \\
    &=  \begin{bmatrix}
        \frac{2}{r - \left( -l \right)} & 0                               & 0               & -\frac{r + \left( -l \right)}{r - \left( -l \right)} \\
        0                               & \frac{2}{t - \left( -b \right)} & 0               & -\frac{t + \left( -b \right)}{t - \left( -b \right)} \\
        0                               & 0                               & \frac{1}{f - n} & -\frac{n}{f - n}                                     \\
        0                               & 0                               & 0               &  1
        \end{bmatrix}
```

therefore

```{math}
M^{Metal}_{orth,lh \rightarrow lh} = 
    \begin{bmatrix}
        \frac{2}{r - \left( -l \right)} & 0                               & 0               & -\frac{r + \left( -l \right)}{r - \left( -l \right)} \\
        0                               & \frac{2}{t - \left( -b \right)} & 0               & -\frac{t + \left( -b \right)}{t - \left( -b \right)} \\
        0                               & 0                               & \frac{1}{f - n} & -\frac{n}{f - n}                                     \\
        0                               & 0                               & 0               &  1
    \end{bmatrix}.
```

Finally, we calculate the matrix for the symmetric vertical field of view perspective projection

```{math}
M^{Metal}_{per,vfov,lh \rightarrow lh} 
    &= \Omega_{lh \rightarrow lh} Q^{Metal}_{lh} M^{C,Metal}_{per,vfov} \Omega_{lh \rightarrow lh} Q^{Metal}_{lh} \\
    &= \left( \Omega_{lh \rightarrow lh} Q^{Metal}_{lh} \right) M^{C,Metal}_{per,vfov} \left( \Omega_{lh \rightarrow lh} Q^{Metal}_{lh} \right) \\
    &= \left( \Omega_{lh \rightarrow lh} I \right) M^{C,Metal}_{per,vfov} \left( \Omega_{lh \rightarrow lh} I \right) \\
    &= \Omega_{lh \rightarrow lh} M^{C,Metal}_{per,vfov} \Omega_{lh \rightarrow lh} \\
    &= I M^{C,Metal}_{per,vfov} I \\
    &= M^{C,Metal}_{per,vfov} \\
    &=  \begin{bmatrix}
        \frac{1}{\text{aspect} \cdot \tan\left( \frac{\theta_{vfov}}{2} \right)} 
        & 0 
        & 0
        & 0 \\

        0 
        & \frac{1}{\tan\left( \frac{\theta_{vfov}}{2} \right)}
        & 0
        & 0 \\

        0 
        &  0 
        & \frac{f}{f - n}
        & -\frac{ f n }{f - n} \\
    
        0 
        & 0 
        & 1 
        & 0
        \end{bmatrix}
```

therefore

```{math}
M^{Metal}_{per,vfov,lh \rightarrow lh} = 
    \begin{bmatrix}
        \frac{1}{\text{aspect} \cdot \tan\left( \frac{\theta_{vfov}}{2} \right)} 
        & 0 
        & 0
        & 0 \\

        0 
        & \frac{1}{\tan\left( \frac{\theta_{vfov}}{2} \right)}
        & 0
        & 0 \\

        0 
        &  0 
        & \frac{f}{f - n}
        & -\frac{ f n }{f - n} \\
    
        0 
        & 0 
        & 1 
        & 0
    \end{bmatrix}.
```

This completes the derivation of the matrices for the left-handed Metal projected view space coordinates.

## Vulkan (Vulkan Coordinates)

We derive the perspective and orthographic transformation matrices in the standard left-handed
orthonormal frame and the standard right-handed coordinate frame.

### The Canonical Matrices

The canonical projected view space coordinate system for Vulkan is the frame 
{math}`(\mathbf{0}^{T}, (\mathbf{\hat{x}}, \mathbf{\hat{y}}, \mathbf{\hat{z}})` in {math}`\mathbb{RP}^{3}`,
where {math}`\mathbf{\hat{x}} = [1, 0, 0]^{T}`, {math}`\mathbf{\hat{y}} = [0, 1, 0]^{T}`, 
{math}`\mathbf{\hat{z}} = [0, 0, 1]^{T}`, where {math}`\mathbf{\hat{z}}` points into the view volume. 
The canonical view volume for for Vulkan in normalized device coordinates is parametrized by 
{math}`[-1, 1] \times [-1, 1] \times [0, 1]`. The canonical perspective projection matrix for 
Vulkan is given by

```{math}
M^{C, Vulkan}_{per} =
\begin{bmatrix}
\frac{ 2 n }{r - \left( -l \right)} & 0                                   & -\frac{r + \left( -l \right)}{r - \left( -l \right)} &  0                  \\
0                                   & \frac{ 2 n }{t - \left( -b \right)} & -\frac{t + \left( -b \right)}{t - \left( -b \right)} &  0                  \\
0                                   & 0                                   &  \frac{f}{f - n}                                     & -\frac{f n }{f - n} \\
0                                   & 0                                   &  1                                                   &  0                  \\
\end{bmatrix}
.
```

The canonical orthographic projection matrix for Vulkan is given by

```{math}
M^{C, Vulkan}_{orth} = 
\begin{bmatrix}
\frac{2}{r - \left( -l \right)} & 0                               & 0               & -\frac{r + \left( -l \right)}{r - \left( -l \right)} \\
0                               & \frac{2}{t - \left( -b \right)} & 0               & -\frac{t + \left( -b \right)}{t - \left( -b \right)} \\
0                               & 0                               & \frac{1}{f - n} & -\frac{n}{f - n}                                     \\
0                               & 0                               & 0               &  1                                                   \\
\end{bmatrix}
.
```

The canonical symmetric vertical field of view perspective projection matrix for Vulkan is given by

```{math}
M^{C, Vulkan}_{per,vfov} = 
    \begin{bmatrix}
        \frac{1}{\text{aspect} \cdot \tan\left( \frac{\theta_{vfov}}{2} \right)} 
        & 0 
        & 0
        & 0 
        \\
        0 
        & \frac{1}{\tan\left( \frac{\theta_{vfov}}{2} \right)}
        & 0
        & 0
        \\
        0 
        &  0 
        &  \frac{f}{f - n}
        & -\frac{ f n }{f - n}
        \\
        0 
        & 0 
        & 1 
        & 0
        \\
    \end{bmatrix}
```

This finishes the statement of the canonical matrices for Vulkan. We now derive the specific matrices for Vulkan.

### Right-Handed View Space

The right-handed projected view space coordinate system for Vulkan is the frame 
{math}`(\mathbf{0}, (\mathbf{\hat{x}}, -\mathbf{\hat{y}}, \mathbf{\hat{z}}))` in {math}`\mathbb{RP}^{3}`,
where {math}`\mathbf{\hat{x}} = [1, 0, 0]^{T}`, {math}`\mathbf{\hat{y}} = [0, 1, 0]^{T}`, 
{math}`\mathbf{\hat{z}} = [0, 0, 1]^{T}` where {math}`\mathbf{\hat{z}}` points into the view volume away from
the viewer. Notice that the vertical axis points down in this frame. This is a right-handed coordinate system. 
The clip coordinate system for Vulkan is the same as the right-handed projected projected view space coordinate
system. A homogeneous rotation about the x-axis is defined as

```{math}
R_{x}\left( \theta \right) = 
    \begin{bmatrix}
        1 & 0                         &  0                         & 0 \\
        0 & \cos\left( \theta \right) & -\sin\left( \theta \right) & 0 \\
        0 & \sin\left( \theta \right) &  \cos\left( \theta \right) & 0 \\
        0 & 0                         &  0                         & 1 \\
    \end{bmatrix}.
```

The orthogonal transformations for the right-handed Vulkan view space coordinates are given by

```{math}
Q^{Vulkan}_{rh} 
    = R_{x}\left( \pi \right) 
    =  \begin{bmatrix}
            1 & 0                      &  0                      & 0 \\
            0 & \cos\left( \pi \right) & -\sin\left( \pi \right) & 0 \\
            0 & \sin\left( \pi \right) &  \cos\left( \pi \right) & 0 \\
            0 & 0                      &  0                      & 1 \\
        \end{bmatrix}
    =   \begin{bmatrix}
            1 &  0 &  0 & 0 \\
            0 & -1 &  0 & 0 \\
            0 &  0 & -1 & 0 \\
            0 &  0 &  0 & 1 \\
        \end{bmatrix}
    \\
Q^{Vulkan}_{lh} 
    = R_{x}\left( -\pi \right) 
    =  \begin{bmatrix}
            1 & 0                       &  0                       & 0 \\
            0 & \cos\left( -\pi \right) & -\sin\left( -\pi \right) & 0 \\
            0 & \sin\left( -\pi \right) &  \cos\left( -\pi \right) & 0 \\
            0 & 0                       &  0                       & 1 \\
        \end{bmatrix}
    =   \begin{bmatrix}
            1 &  0 &  0 & 0 \\
            0 & -1 &  0 & 0 \\
            0 &  0 & -1 & 0 \\
            0 &  0 &  0 & 1 \\
        \end{bmatrix}.
```

The change of orientation matrices are given by

```{math}
\Omega_{rh \rightarrow lh} = 
    \begin{bmatrix}
        1 & 0 &  0 & 0 \\
        0 & 1 &  0 & 0 \\
        0 & 0 & -1 & 0 \\
        0 & 0 &  0 & 1 \\
    \end{bmatrix}
,
\hspace{4 pt}
\Omega_{lh \rightarrow rh} = 
    \begin{bmatrix}
        1 & 0 &  0 & 0 \\
        0 & 1 &  0 & 0 \\
        0 & 0 & -1 & 0 \\
        0 & 0 &  0 & 1 \\
    \end{bmatrix}.
```

To compute the projections, we need to coordinate transform from the chosen view coordinates to
the canonical view coordinates, apply the canonical projection, and then transform from the canonical clip coordinates 
to the target clip coordinates. We can map any view coordinates to and clip coordinates using the same process. Each 
coordinate transformation is the product of an orthogonal transform and a change of orientation matrix. Let us 
calculate the perspective projection

```{math}
M^{Vulkan}_{per, rh \rightarrow rh}
    &= \Omega_{lh \rightarrow rh} \left(Q^{Vulkan}_{lh}\right)^{-1} M^{C,Vulkan}_{per} \Omega_{rh \rightarrow lh} Q^{Vulkan}_{rh} \\
    &= \left( \Omega_{lh \rightarrow rh} \left(Q^{Vulkan}_{lh}\right)^{-1} \right) M^{C,Vulkan}_{per} \left( \Omega_{rh \rightarrow lh} Q^{Vulkan}_{rh} \right) \\
    &= \left( 
            \begin{bmatrix}
                1 & 0 &  0 & 0 \\
                0 & 1 &  0 & 0 \\
                0 & 0 & -1 & 0 \\
                0 & 0 &  0 & 1 \\
            \end{bmatrix}
            \begin{bmatrix}
                1 &  0 &  0 & 0 \\
                0 & -1 &  0 & 0 \\
                0 &  0 & -1 & 0 \\
                0 &  0 &  0 & 1 \\
            \end{bmatrix}
        \right)
        M^{C,Vulkan}_{per}
        \left(
            \begin{bmatrix}
                1 & 0 &  0 & 0 \\
                0 & 1 &  0 & 0 \\
                0 & 0 & -1 & 0 \\
                0 & 0 &  0 & 1 \\
            \end{bmatrix}
            \begin{bmatrix}
                1 &  0 &  0 & 0 \\
                0 & -1 &  0 & 0 \\
                0 &  0 & -1 & 0 \\
                0 &  0 &  0 & 1 \\
            \end{bmatrix}
        \right)
        \\
    &= \begin{bmatrix}
            1 &  0 & 0 & 0 \\
            0 & -1 & 0 & 0 \\
            0 &  0 & 1 & 0 \\
            0 &  0 & 0 & 1 \\
        \end{bmatrix}
        M^{C,Vulkan}_{per}
        \begin{bmatrix}
            1 &  0 & 0 & 0 \\
            0 & -1 & 0 & 0 \\
            0 &  0 & 1 & 0 \\
            0 &  0 & 0 & 1 \\
        \end{bmatrix}
        \\
    &= \begin{bmatrix}
            1 &  0 & 0 & 0 \\
            0 & -1 & 0 & 0 \\
            0 &  0 & 1 & 0 \\
            0 &  0 & 0 & 1 \\
        \end{bmatrix}
        \begin{bmatrix}
            \frac{ 2 n }{r - \left( -l \right)} & 0                                   & -\frac{r + \left( -l \right)}{r - \left( -l \right)} &  0                  \\
            0                                   & \frac{ 2 n }{t - \left( -b \right)} & -\frac{t + \left( -b \right)}{t - \left( -b \right)} &  0                  \\
            0                                   & 0                                   &  \frac{f}{f - n}                                     & -\frac{f n }{f - n} \\
            0                                   & 0                                   &  1                                                   &  0                  \\
        \end{bmatrix}
        \begin{bmatrix}
            1 &  0 & 0 & 0 \\
            0 & -1 & 0 & 0 \\
            0 &  0 & 1 & 0 \\
            0 &  0 & 0 & 1 \\
        \end{bmatrix}
        \\
    &= \begin{bmatrix}
            1 &  0 & 0 & 0 \\
            0 & -1 & 0 & 0 \\
            0 &  0 & 1 & 0 \\
            0 &  0 & 0 & 1 \\
        \end{bmatrix}
        \begin{bmatrix}
            \frac{ 2 n }{r - \left( -l \right)} &  0                                   & -\frac{r + \left( -l \right)}{r - \left( -l \right)} &  0                  \\
            0                                   & -\frac{ 2 n }{t - \left( -b \right)} & -\frac{t + \left( -b \right)}{t - \left( -b \right)} &  0                  \\
            0                                   &  0                                   &  \frac{f}{f - n}                                     & -\frac{f n }{f - n} \\
            0                                   &  0                                   &  1                                                   &  0                  \\
        \end{bmatrix}
        \\
    &=  \begin{bmatrix}
            1 &  0 & 0 & 0 \\
            0 & -1 & 0 & 0 \\
            0 &  0 & 1 & 0 \\
            0 &  0 & 0 & 1 \\
        \end{bmatrix}
        \begin{bmatrix}
            \frac{ 2 n }{r + l} &  0                   & -\frac{r - l}{r + l} &  0                  \\
            0                   & -\frac{ 2 n }{t + b} & -\frac{t - b}{t + b} &  0                  \\
            0                   &  0                   &  \frac{f}{f - n}     & -\frac{f n }{f - n} \\
            0                   &  0                   &  1                   &  0                  \\
        \end{bmatrix}
        \\
    &=  \begin{bmatrix}
            1 &  0 & 0 & 0 \\
            0 & -1 & 0 & 0 \\
            0 &  0 & 1 & 0 \\
            0 &  0 & 0 & 1 \\
        \end{bmatrix}
        \begin{bmatrix}
            \frac{ 2 n }{r + l} &  0                   & -\frac{r - l}{r + l} &  0                  \\
            0                   & -\frac{ 2 n }{b + t} &  \frac{b - t}{b + t} &  0                  \\
            0                   &  0                   &  \frac{f}{f - n}     & -\frac{f n }{f - n} \\
            0                   &  0                   &  1                   &  0                  \\
        \end{bmatrix}
        \\
    &=  \begin{bmatrix}
            \frac{ 2 n }{r + l} & 0                   & -\frac{r - l}{r + l} &  0                  \\
            0                   & \frac{ 2 n }{b + t} & -\frac{b - t}{b + t} &  0                  \\
            0                   & 0                   &  \frac{f}{f - n}     & -\frac{f n }{f - n} \\
            0                   & 0                   &  1                   &  0                  \\
        \end{bmatrix}
        \\
    &=  \begin{bmatrix}
            \frac{ 2 n }{r - \left( -l \right)} & 0                                   & -\frac{r + \left( -l \right)}{r - \left( -l \right)} &  0                  \\
            0                                   & \frac{ 2 n }{b - \left( -t \right)} & -\frac{b + \left( -t \right)}{b - \left( -t \right)} &  0                  \\
            0                                   & 0                                   &  \frac{f}{f - n}                                     & -\frac{f n }{f - n} \\
            0                                   & 0                                   &  1                                                   &  0                  \\
        \end{bmatrix}
```

therefore

```{math}
M^{Vulkan}_{per, rh \rightarrow rh} = 
    \begin{bmatrix}
        \frac{ 2 n }{r - \left( -l \right)} & 0                                   & -\frac{r + \left( -l \right)}{r - \left( -l \right)} &  0                  \\
        0                                   & \frac{ 2 n }{b - \left( -t \right)} & -\frac{b + \left( -t \right)}{b - \left( -t \right)} &  0                  \\
        0                                   & 0                                   &  \frac{f}{f - n}                                     & -\frac{f n }{f - n} \\
        0                                   & 0                                   &  1                                                   &  0                  \\
    \end{bmatrix}.
```

Here is the calculation for the orthographic matrix

```{math}
    M^{Vulkan}_{orth, rh \rightarrow rh}
    &= \Omega_{lh \rightarrow rh} \left(Q^{Vulkan}_{lh}\right)^{-1} M^{C,Vulkan}_{orth} \Omega_{rh \rightarrow lh} Q^{Vulkan}_{rh} \\
    &= \left( \Omega_{lh \rightarrow rh} \left(Q^{Vulkan}_{lh}\right)^{-1} \right) M^{C,Vulkan}_{orth} \left( \Omega_{rh \rightarrow lh} Q^{Vulkan}_{rh} \right) \\
    &= \left( 
            \begin{bmatrix}
                1 & 0 &  0 & 0 \\
                0 & 1 &  0 & 0 \\
                0 & 0 & -1 & 0 \\
                0 & 0 &  0 & 1 \\
            \end{bmatrix}
            \begin{bmatrix}
                1 &  0 &  0 & 0 \\
                0 & -1 &  0 & 0 \\
                0 &  0 & -1 & 0 \\
                0 &  0 &  0 & 1 \\
            \end{bmatrix}
        \right)
        M^{C,Vulkan}_{orth}
        \left(
            \begin{bmatrix}
                1 & 0 &  0 & 0 \\
                0 & 1 &  0 & 0 \\
                0 & 0 & -1 & 0 \\
                0 & 0 &  0 & 1 \\
            \end{bmatrix}
            \begin{bmatrix}
                1 &  0 &  0 & 0 \\
                0 & -1 &  0 & 0 \\
                0 &  0 & -1 & 0 \\
                0 &  0 &  0 & 1 \\
            \end{bmatrix}
        \right)
        \\
    &= \begin{bmatrix}
            1 &  0 & 0 & 0 \\
            0 & -1 & 0 & 0 \\
            0 &  0 & 1 & 0 \\
            0 &  0 & 0 & 1
        \end{bmatrix}
        M^{C,Vulkan}_{orth}
        \begin{bmatrix}
            1 &  0 & 0 & 0 \\
            0 & -1 & 0 & 0 \\
            0 &  0 & 1 & 0 \\
            0 &  0 & 0 & 1 \\
        \end{bmatrix}
        \\
    &= \begin{bmatrix}
            1 &  0 & 0 & 0 \\
            0 & -1 & 0 & 0 \\
            0 &  0 & 1 & 0 \\
            0 &  0 & 0 & 1 \\
        \end{bmatrix}
        \begin{bmatrix}
            \frac{2}{r - \left( -l \right)} & 0                               & 0               & -\frac{r + \left( -l \right)}{r - \left( -l \right)} \\
            0                               & \frac{2}{t - \left( -b \right)} & 0               & -\frac{t + \left( -b \right)}{t - \left( -b \right)} \\
            0                               & 0                               & \frac{1}{f - n} & -\frac{n}{f - n}                                     \\
            0                               & 0                               & 0               &  1                                                   \\
        \end{bmatrix}
        \begin{bmatrix}
            1 &  0 & 0 & 0 \\
            0 & -1 & 0 & 0 \\
            0 &  0 & 1 & 0 \\
            0 &  0 & 0 & 1 \\
        \end{bmatrix}
        \\
    &= \begin{bmatrix}
            1 &  0 & 0 & 0 \\
            0 & -1 & 0 & 0 \\
            0 &  0 & 1 & 0 \\
            0 &  0 & 0 & 1 \\
        \end{bmatrix}
        \begin{bmatrix}
            \frac{2}{r - \left( -l \right)} &  0                               & 0               & -\frac{r + \left( -l \right)}{r - \left( -l \right)} \\
            0                               & -\frac{2}{t - \left( -b \right)} & 0               & -\frac{t + \left( -b \right)}{t - \left( -b \right)} \\
            0                               &  0                               & \frac{1}{f - n} & -\frac{n}{f - n}                                     \\
            0                               &  0                               & 0               &  1                                                   \\
        \end{bmatrix}
        \\
    &= \begin{bmatrix}
            1 &  0 & 0 & 0 \\
            0 & -1 & 0 & 0 \\
            0 &  0 & 1 & 0 \\
            0 &  0 & 0 & 1 \\
        \end{bmatrix}
        \begin{bmatrix}
            \frac{2}{r + l} &  0               & 0               & -\frac{r - l}{r + l} \\
            0               & -\frac{2}{t + b} & 0               & -\frac{t - b}{t + b} \\
            0               &  0               & \frac{1}{f - n} & -\frac{n}{f - n}     \\
            0               &  0               & 0               &  1                   \\
        \end{bmatrix}
        \\
    &= \begin{bmatrix}
            1 &  0 & 0 & 0 \\
            0 & -1 & 0 & 0 \\
            0 &  0 & 1 & 0 \\
            0 &  0 & 0 & 1 \\
        \end{bmatrix}
        \begin{bmatrix}
            \frac{2}{r + l} &  0               & 0               & -\frac{r - l}{r + l} \\
            0               & -\frac{2}{b + t} & 0               &  \frac{b - t}{b + t} \\
            0               &  0               & \frac{1}{f - n} & -\frac{n}{f - n}     \\
            0               &  0               & 0               &  1                   \\
        \end{bmatrix}
        \\
    &=  \begin{bmatrix}
            \frac{2}{r + l} & 0               & 0               & -\frac{r - l}{r + l}  \\
            0               & \frac{2}{b + t} & 0               & -\frac{b - t}{b + t}  \\
            0               & 0               & \frac{1}{f - n} & -\frac{n}{f - n}      \\
            0               & 0               & 0               &  1                    \\
        \end{bmatrix}
        \\
    &=  \begin{bmatrix}
            \frac{2}{r - \left( -l \right)} & 0                               & 0               & -\frac{r + \left( -l \right)}{r - \left( -l \right)}  \\
            0                               & \frac{2}{b - \left( -t \right)} & 0               & -\frac{b + \left( -t \right)}{b - \left( -t \right)}  \\
            0                               & 0                               & \frac{1}{f - n} & -\frac{n}{f - n}                                      \\
            0                               & 0                               & 0               &  1                                                    \\
        \end{bmatrix}
```

therefore

```{math}
M^{Vulkan}_{orth, rh \rightarrow rh} = 
    \begin{bmatrix}
        \frac{2}{r - \left( -l \right)} & 0                               & 0               & -\frac{r + \left( -l \right)}{r - \left( -l \right)}  \\
        0                               & \frac{2}{b - \left( -t \right)} & 0               & -\frac{b + \left( -t \right)}{b - \left( -t \right)}  \\
        0                               & 0                               & \frac{1}{f - n} & -\frac{n}{f - n}                                      \\
        0                               & 0                               & 0               &  1                                                    \\
    \end{bmatrix}.
```

Finally, we calculate the matrix for the symmetric vertical field of view perspective projection

```{math}
M^{Vulkan}_{per,vfov,rh \rightarrow rh}
    &= \Omega_{lh \rightarrow rh} \left(Q^{Vulkan}_{lh}\right)^{-1} M^{C,Vulkan}_{per,vfov} \Omega_{rh \rightarrow lh} Q^{Vulkan}_{rh} \\
    &= \left( \Omega_{lh \rightarrow rh} \left(Q^{Vulkan}_{lh}\right)^{-1} \right) M^{C,Vulkan}_{per,vfov} \left( \Omega_{rh \rightarrow lh} Q^{Vulkan}_{rh} \right) \\
    &= \left( 
            \begin{bmatrix}
                1 & 0 &  0 & 0 \\
                0 & 1 &  0 & 0 \\
                0 & 0 & -1 & 0 \\
                0 & 0 &  0 & 1 \\
            \end{bmatrix}
            \begin{bmatrix}
                1 &  0 &  0 & 0 \\
                0 & -1 &  0 & 0 \\
                0 &  0 & -1 & 0 \\
                0 &  0 &  0 & 1 \\
            \end{bmatrix}
        \right)
        M^{C,Vulkan}_{per,vfov}
        \left(
            \begin{bmatrix}
                1 & 0 &  0 & 0 \\
                0 & 1 &  0 & 0 \\
                0 & 0 & -1 & 0 \\
                0 & 0 &  0 & 1 \\
            \end{bmatrix}
            \begin{bmatrix}
                1 &  0 &  0 & 0 \\
                0 & -1 &  0 & 0 \\
                0 &  0 & -1 & 0 \\
                0 &  0 &  0 & 1 \\
            \end{bmatrix}
        \right)
        \\
    &= \begin{bmatrix}
            1 &  0 & 0 & 0 \\
            0 & -1 & 0 & 0 \\
            0 &  0 & 1 & 0 \\
            0 &  0 & 0 & 1 \\
        \end{bmatrix}
        M^{C,Vulkan}_{per,vfov}
        \begin{bmatrix}
            1 &  0 & 0 & 0 \\
            0 & -1 & 0 & 0 \\
            0 &  0 & 1 & 0 \\
            0 &  0 & 0 & 1 \\
        \end{bmatrix}
        \\
    &= \begin{bmatrix}
            1 &  0 & 0 & 0 \\
            0 & -1 & 0 & 0 \\
            0 &  0 & 1 & 0 \\
            0 &  0 & 0 & 1 \\
        \end{bmatrix}
        \begin{bmatrix}
            \frac{1}{\text{aspect} \cdot \tan\left( \frac{\theta_{vfov}}{2} \right)} 
            & 0 
            & 0
            & 0
            \\
            0 
            & \frac{1}{\tan\left( \frac{\theta_{vfov}}{2} \right)}
            & 0
            & 0
            \\
            0 
            &  0 
            &  \frac{f}{f - n}
            & -\frac{ f n }{f - n}
            \\
            0 
            & 0 
            & 1 
            & 0
            \\
        \end{bmatrix}
        \begin{bmatrix}
            1 &  0 & 0 & 0 \\
            0 & -1 & 0 & 0 \\
            0 &  0 & 1 & 0 \\
            0 &  0 & 0 & 1 \\
        \end{bmatrix}
        \\
    &= \begin{bmatrix}
            1 &  0 & 0 & 0 \\
            0 & -1 & 0 & 0 \\
            0 &  0 & 1 & 0 \\
            0 &  0 & 0 & 1 \\
        \end{bmatrix}
        \begin{bmatrix}
            \frac{1}{\text{aspect} \cdot \tan\left( \frac{\theta_{vfov}}{2} \right)} 
            & 0 
            & 0
            & 0
            \\
            0 
            & -\frac{1}{\tan\left( \frac{\theta_{vfov}}{2} \right)}
            &  0
            &  0
            \\
            0 
            &  0 
            &  \frac{f}{f - n}
            & -\frac{ f n }{f - n}
            \\
            0 
            & 0 
            & 1 
            & 0
            \\
        \end{bmatrix}
        \\
    &=  \begin{bmatrix}
            \frac{1}{\text{aspect} \cdot \tan\left( \frac{\theta_{vfov}}{2} \right)} 
            & 0 
            & 0
            & 0
            \\
            0 
            & \frac{1}{\tan\left( \frac{\theta_{vfov}}{2} \right)}
            & 0
            & 0
            \\
            0 
            &  0 
            &  \frac{f}{f - n}
            & -\frac{ f n }{f - n}
            \\
            0 
            & 0 
            & 1 
            & 0
            \\
        \end{bmatrix}
```

therefore

```{math}
M^{Vulkan}_{per,vfov,rh \rightarrow rh} = 
    \begin{bmatrix}
        \frac{1}{\text{aspect} \cdot \tan\left( \frac{\theta_{vfov}}{2} \right)} 
        & 0 
        & 0
        & 0
        \\
        0 
        & \frac{1}{\tan\left( \frac{\theta_{vfov}}{2} \right)}
        & 0
        & 0
        \\
        0 
        &  0 
        &  \frac{f}{f - n}
        & -\frac{ f n }{f - n}
        \\
        0 
        & 0 
        & 1 
        & 0
        \\
    \end{bmatrix}.
```

This completes the derivation of the matrices for the right-handed Vulkan projected view space coordinates.

### Left-Handed View Space

The right-handed projected view space coordinate system for Vulkan is the frame 
{math}`(\mathbf{0}, (\mathbf{\hat{x}}, -\mathbf{\hat{y}}, -\mathbf{\hat{z}}))` in {math}`\mathbb{RP}^{3}`,
where {math}`\mathbf{\hat{x}} = [1, 0, 0]^{T}`, {math}`\mathbf{\hat{y}} = [0, 1, 0]^{T}`, 
{math}`\mathbf{\hat{z}} = [0, 0, 1]^{T}` where {math}`-\mathbf{\hat{z}}` points out of the view volume towards 
the viewer. Notice that the vertical axis points down in this frame. This is a left-handed coordinate system. 
The clip coordinate system for Vulkan is the same as the right-handed projected projected view space coordinate
system. A homogeneous rotation about the x-axis is defined as

```{math}
R_{x}\left( \theta \right) = 
    \begin{bmatrix}
        1 & 0                         &  0                         & 0 \\
        0 & \cos\left( \theta \right) & -\sin\left( \theta \right) & 0 \\
        0 & \sin\left( \theta \right) &  \cos\left( \theta \right) & 0 \\
        0 & 0                         &  0                         & 1 \\
    \end{bmatrix}.
```

The orthogonal transformations for the left-handed Vulkan view space coordinates are given by

```{math}
Q^{Vulkan}_{lh} 
    = R_{x}\left( \pi \right) 
    = \begin{bmatrix}
            1 & 0                      &  0                      & 0 \\
            0 & \cos\left( \pi \right) & -\sin\left( \pi \right) & 0 \\
            0 & \sin\left( \pi \right) &  \cos\left( \pi \right) & 0 \\
            0 & 0                      &  0                      & 1 \\
        \end{bmatrix}
    =   \begin{bmatrix}
            1 &  0 &  0 & 0 \\
            0 & -1 &  0 & 0 \\
            0 &  0 & -1 & 0 \\
            0 &  0 &  0 & 1 \\
        \end{bmatrix}.
```

The change of orientation matrices are given by

```{math}
\Omega_{lh \rightarrow lh} = 
    \begin{bmatrix}
        1 & 0 & 0 & 0 \\
        0 & 1 & 0 & 0 \\
        0 & 0 & 1 & 0 \\
        0 & 0 & 0 & 1 \\
    \end{bmatrix}
,
\hspace{4 pt}
\Omega_{lh \rightarrow rh} = 
    \begin{bmatrix}
        1 & 0 &  0 & 0 \\
        0 & 1 &  0 & 0 \\
        0 & 0 & -1 & 0 \\
        0 & 0 &  0 & 1 \\
    \end{bmatrix}.
```

To compute the projections, we need to coordinate transform from the chosen view coordinates to
the canonical view coordinates, apply the canonical projection, and then transform from the canonical clip coordinates 
to the target clip coordinates. We can map any view coordinates to and clip coordinates using the same process. Each 
coordinate transformation is the product of an orthogonal transform and a change of orientation matrix. Let us 
calculate the perspective projection

```{math}
M^{Vulkan}_{per, lh \rightarrow rh}
    &= \Omega_{lh \rightarrow rh} \left(Q^{Vulkan}_{lh}\right)^{-1} M^{C,Vulkan}_{per} \Omega_{lh \rightarrow lh} Q^{Vulkan}_{lh} \\
    &= \left( \Omega_{lh \rightarrow rh} \left( Q^{Vulkan}_{lh}\right)^{-1} \right) M^{C,Vulkan}_{per} \left( \Omega_{lh \rightarrow lh} Q^{Vulkan}_{lh} \right) \\
    &= \left( \Omega_{lh \rightarrow rh} \left( Q^{Vulkan}_{lh}\right)^{-1} \right) M^{C,Vulkan}_{per} \left( I Q^{Vulkan}_{lh} \right) \\
    &= \left( \Omega_{lh \rightarrow rh} \left( Q^{Vulkan}_{lh}\right)^{-1} \right) M^{C,Vulkan}_{per} Q^{Vulkan}_{lh} \\
    &= \left( 
            \begin{bmatrix}
                1 & 0 &  0 & 0 \\
                0 & 1 &  0 & 0 \\
                0 & 0 & -1 & 0 \\
                0 & 0 &  0 & 1 \\
            \end{bmatrix}
            \begin{bmatrix}
                1 &  0 &  0 & 0 \\
                0 & -1 &  0 & 0 \\
                0 &  0 & -1 & 0 \\
                0 &  0 &  0 & 1 \\
            \end{bmatrix}
        \right)
        M^{C,Vulkan}_{per}
        \begin{bmatrix}
            1 &  0 &  0 & 0 \\
            0 & -1 &  0 & 0 \\
            0 &  0 & -1 & 0 \\
            0 &  0 &  0 & 1 \\
        \end{bmatrix}
        \\
    &= \begin{bmatrix}
            1 &  0 & 0 & 0 \\
            0 & -1 & 0 & 0 \\
            0 &  0 & 1 & 0 \\
            0 &  0 & 0 & 1 \\
        \end{bmatrix}
        M^{C,Vulkan}_{per}
        \begin{bmatrix}
            1 &  0 &  0 & 0 \\
            0 & -1 &  0 & 0 \\
            0 &  0 & -1 & 0 \\
            0 &  0 &  0 & 1 \\
        \end{bmatrix}
        \\
    &= \begin{bmatrix}
            1 &  0 & 0 & 0 \\
            0 & -1 & 0 & 0 \\
            0 &  0 & 1 & 0 \\
            0 &  0 & 0 & 1 \\
        \end{bmatrix}
        \begin{bmatrix}
            \frac{ 2 n }{r - \left( -l \right)} & 0                                   & -\frac{r + \left( -l \right)}{r - \left( -l \right)} &  0                  \\
            0                                   & \frac{ 2 n }{t - \left( -b \right)} & -\frac{t + \left( -b \right)}{t - \left( -b \right)} &  0                  \\
            0                                   & 0                                   &  \frac{f}{f - n}                                     & -\frac{f n }{f - n} \\
            0                                   & 0                                   &  1                                                   &  0                  \\
        \end{bmatrix}
        \begin{bmatrix}
            1 &  0 &  0 & 0 \\
            0 & -1 &  0 & 0 \\
            0 &  0 & -1 & 0 \\
            0 &  0 &  0 & 1 \\
        \end{bmatrix}
        \\
    &= \begin{bmatrix}
            1 &  0 & 0 & 0 \\
            0 & -1 & 0 & 0 \\
            0 &  0 & 1 & 0 \\
            0 &  0 & 0 & 1 \\
        \end{bmatrix}
        \begin{bmatrix}
            \frac{ 2 n }{r - \left( -l \right)} &  0                                   &  \frac{r + \left( -l \right)}{r - \left( -l \right)} &  0                  \\
            0                                   & -\frac{ 2 n }{t - \left( -b \right)} &  \frac{t + \left( -b \right)}{t - \left( -b \right)} &  0                  \\
            0                                   &  0                                   & -\frac{f}{f - n}                                     & -\frac{f n }{f - n} \\
            0                                   &  0                                   & -1                                                   &  0                  \\
        \end{bmatrix}
        \\
    &=  \begin{bmatrix}
            1 &  0 & 0 & 0 \\
            0 & -1 & 0 & 0 \\
            0 &  0 & 1 & 0 \\
            0 &  0 & 0 & 1 \\
        \end{bmatrix}
        \begin{bmatrix}
            \frac{ 2 n }{r + l} &  0                   &  \frac{r - l}{r + l} &  0                  \\
            0                   & -\frac{ 2 n }{t + b} &  \frac{t - b}{t + b} &  0                  \\
            0                   &  0                   & -\frac{f}{f - n}     & -\frac{f n }{f - n} \\
            0                   &  0                   & -1                   &  0                  \\
        \end{bmatrix}
        \\
    &=  \begin{bmatrix}
            1 &  0 & 0 & 0 \\
            0 & -1 & 0 & 0 \\
            0 &  0 & 1 & 0 \\
            0 &  0 & 0 & 1 \\
        \end{bmatrix}
        \begin{bmatrix}
            \frac{ 2 n }{r + l} &  0                   &  \frac{r - l}{r + l} &  0                  \\
            0                   & -\frac{ 2 n }{b + t} & -\frac{b - t}{b + t} &  0                  \\
            0                   &  0                   & -\frac{f}{f - n}     & -\frac{f n }{f - n} \\
            0                   &  0                   & -1                   &  0                  \\
        \end{bmatrix}
        \\
    &=  \begin{bmatrix}
            \frac{ 2 n }{r + l} & 0                   &  \frac{r - l}{r + l} &  0                  \\
            0                   & \frac{ 2 n }{b + t} &  \frac{b - t}{b + t} &  0                  \\
            0                   & 0                   & -\frac{f}{f - n}     & -\frac{f n }{f - n} \\
            0                   & 0                   & -1                   &  0                  \\
        \end{bmatrix}
        \\
    &=  \begin{bmatrix}
            \frac{ 2 n }{r - \left( -l \right)} & 0                                   &  \frac{r + \left( -l \right)}{r - \left( -l \right)} &  0                  \\
            0                                   & \frac{ 2 n }{b - \left( -t \right)} &  \frac{b + \left( -t \right)}{b - \left( -t \right)} &  0                  \\
            0                                   & 0                                   & -\frac{f}{f - n}                                     & -\frac{f n }{f - n} \\
            0                                   & 0                                   & -1                                                   &  0                  \\
        \end{bmatrix}
```

therefore

```{math}
M^{Vulkan}_{per, lh \rightarrow rh} = 
    \begin{bmatrix}
        \frac{ 2 n }{r - \left( -l \right)} & 0                                   &  \frac{r + \left( -l \right)}{r - \left( -l \right)} &  0                  \\
        0                                   & \frac{ 2 n }{b - \left( -t \right)} &  \frac{b + \left( -t \right)}{b - \left( -t \right)} &  0                  \\
        0                                   & 0                                   & -\frac{f}{f - n}                                     & -\frac{f n }{f - n} \\
        0                                   & 0                                   & -1                                                   &  0                  \\
    \end{bmatrix}.
```

Here is the calculation for the orthographic matrix

```{math}
    M^{Vulkan}_{orth, lh \rightarrow rh}
    &= \Omega_{lh \rightarrow rh} \left(Q^{Vulkan}_{lh}\right)^{-1} M^{C,Vulkan}_{orth} \Omega_{lh \rightarrow lh} Q^{Vulkan}_{lh} \\
    &= \left( \Omega_{lh \rightarrow rh} \left(Q^{Vulkan}_{lh}\right)^{-1} \right) M^{C,Vulkan}_{orth} \left( \Omega_{lh \rightarrow lh} Q^{Vulkan}_{lh} \right) \\
    &= \left( \Omega_{lh \rightarrow rh} \left(Q^{Vulkan}_{lh}\right)^{-1} \right) M^{C,Vulkan}_{orth} \left( I Q^{Vulkan}_{lh} \right) \\
    &= \left( \Omega_{lh \rightarrow rh} \left(Q^{Vulkan}_{lh}\right)^{-1} \right) M^{C,Vulkan}_{orth} Q^{Vulkan}_{lh} \\
    &= \left( 
            \begin{bmatrix}
                1 & 0 &  0 & 0 \\
                0 & 1 &  0 & 0 \\
                0 & 0 & -1 & 0 \\
                0 & 0 &  0 & 1 \\
            \end{bmatrix}
            \begin{bmatrix}
                1 &  0 &  0 & 0 \\
                0 & -1 &  0 & 0 \\
                0 &  0 & -1 & 0 \\
                0 &  0 &  0 & 1 \\
            \end{bmatrix}
        \right)
        M^{C,Vulkan}_{orth}
        \begin{bmatrix}
            1 &  0 &  0 & 0 \\
            0 & -1 &  0 & 0 \\
            0 &  0 & -1 & 0 \\
            0 &  0 &  0 & 1 \\
        \end{bmatrix}
        \\
    &= \begin{bmatrix}
            1 &  0 & 0 & 0 \\
            0 & -1 & 0 & 0 \\
            0 &  0 & 1 & 0 \\
            0 &  0 & 0 & 1 \\
        \end{bmatrix}
        M^{C,Vulkan}_{orth}
        \begin{bmatrix}
            1 &  0 &  0 & 0 \\
            0 & -1 &  0 & 0 \\
            0 &  0 & -1 & 0 \\
            0 &  0 &  0 & 1 \\
        \end{bmatrix}
        \\
    &= \begin{bmatrix}
            1 &  0 & 0 & 0 \\
            0 & -1 & 0 & 0 \\
            0 &  0 & 1 & 0 \\
            0 &  0 & 0 & 1 \\
        \end{bmatrix}
        \begin{bmatrix}
            \frac{2}{r - \left( -l \right)} & 0                               & 0               & -\frac{r + \left( -l \right)}{r - \left( -l \right)} \\
            0                               & \frac{2}{t - \left( -b \right)} & 0               & -\frac{t + \left( -b \right)}{t - \left( -b \right)} \\
            0                               & 0                               & \frac{1}{f - n} & -\frac{n}{f - n}                                     \\
            0                               & 0                               & 0               &  1                                                   \\
        \end{bmatrix}
        \begin{bmatrix}
            1 &  0 &  0 & 0 \\
            0 & -1 &  0 & 0 \\
            0 &  0 & -1 & 0 \\
            0 &  0 &  0 & 1 \\
        \end{bmatrix}
        \\
    &= \begin{bmatrix}
            1 &  0 & 0 & 0 \\
            0 & -1 & 0 & 0 \\
            0 &  0 & 1 & 0 \\
            0 &  0 & 0 & 1 \\
        \end{bmatrix}
        \begin{bmatrix}
            \frac{2}{r - \left( -l \right)} &  0                               &  0               & -\frac{r + \left( -l \right)}{r - \left( -l \right)} \\
            0                               & -\frac{2}{t - \left( -b \right)} &  0               & -\frac{t + \left( -b \right)}{t - \left( -b \right)} \\
            0                               &  0                               & -\frac{1}{f - n} & -\frac{n}{f - n}                                     \\
            0                               &  0                               &  0               &  1                                                   \\
        \end{bmatrix}
        \\
    &= \begin{bmatrix}
            1 &  0 & 0 & 0 \\
            0 & -1 & 0 & 0 \\
            0 &  0 & 1 & 0 \\
            0 &  0 & 0 & 1
        \end{bmatrix}
        \begin{bmatrix}
            \frac{2}{r + l} &  0               &  0               & -\frac{r - l}{r + l} \\
            0               & -\frac{2}{t + b} &  0               & -\frac{t - b}{t + b} \\
            0               &  0               & -\frac{1}{f - n} & -\frac{n}{f - n}     \\
            0               &  0               &  0               &  1                   \\
        \end{bmatrix}
        \\
    &= \begin{bmatrix}
            1 &  0 & 0 & 0 \\
            0 & -1 & 0 & 0 \\
            0 &  0 & 1 & 0 \\
            0 &  0 & 0 & 1 \\
        \end{bmatrix}
        \begin{bmatrix}
            \frac{2}{r + l} &  0               &  0               & -\frac{r - l}{r + l} \\
            0               & -\frac{2}{b + t} &  0               &  \frac{b - t}{b + t} \\
            0               &  0               & -\frac{1}{f - n} & -\frac{n}{f - n}     \\
            0               &  0               &  0               &  1                   \\
        \end{bmatrix}
        \\
    &=  \begin{bmatrix}
            \frac{2}{r + l} & 0               &  0               & -\frac{r - l}{r + l}  \\
            0               & \frac{2}{b + t} &  0               & -\frac{b - t}{b + t}  \\
            0               & 0               & -\frac{1}{f - n} & -\frac{n}{f - n}      \\
            0               & 0               &  0               &  1                    \\
        \end{bmatrix}
        \\
    &=  \begin{bmatrix}
            \frac{2}{r - \left( -l \right)} & 0                               &  0               & -\frac{r + \left( -l \right)}{r - \left( -l \right)}  \\
            0                               & \frac{2}{b - \left( -t \right)} &  0               & -\frac{b + \left( -t \right)}{b - \left( -t \right)}  \\
            0                               & 0                               & -\frac{1}{f - n} & -\frac{n}{f - n}                                      \\
            0                               & 0                               &  0               &  1                                                    \\
        \end{bmatrix}
```

therefore

```{math}
M^{Vulkan}_{orth, lh \rightarrow rh} = 
    \begin{bmatrix}
        \frac{2}{r - \left( -l \right)} & 0                               &  0               & -\frac{r + \left( -l \right)}{r - \left( -l \right)}  \\
        0                               & \frac{2}{b - \left( -t \right)} &  0               & -\frac{b + \left( -t \right)}{b - \left( -t \right)}  \\
        0                               & 0                               & -\frac{1}{f - n} & -\frac{n}{f - n}                                      \\
        0                               & 0                               &  0               &  1                                                    \\
    \end{bmatrix}.
```

Finally, we calculate the matrix for the symmetric vertical field of view perspective projection

```{math}
M^{Vulkan}_{per,vfov,lh \rightarrow rh}
    &= \Omega_{lh \rightarrow rh} \left(Q^{Vulkan}_{lh}\right)^{-1} M^{C,Vulkan}_{per,vfov} \Omega_{lh \rightarrow lh} Q^{Vulkan}_{lh} \\
    &= \left( \Omega_{lh \rightarrow rh} \left(Q^{Vulkan}_{lh}\right)^{-1} \right) M^{C,Vulkan}_{per,vfov} \left( \Omega_{lh \rightarrow lh} Q^{Vulkan}_{lh} \right) \\
    &= \left( \Omega_{lh \rightarrow rh} \left(Q^{Vulkan}_{lh}\right)^{-1} \right) M^{C,Vulkan}_{per,vfov} \left( I Q^{Vulkan}_{lh} \right) \\
    &= \left( \Omega_{lh \rightarrow rh} \left(Q^{Vulkan}_{lh}\right)^{-1} \right) M^{C,Vulkan}_{per,vfov} Q^{Vulkan}_{lh} \\
    &= \left( 
            \begin{bmatrix}
                1 & 0 &  0 & 0 \\
                0 & 1 &  0 & 0 \\
                0 & 0 & -1 & 0 \\
                0 & 0 &  0 & 1 \\
            \end{bmatrix}
            \begin{bmatrix}
                1 &  0 &  0 & 0 \\
                0 & -1 &  0 & 0 \\
                0 &  0 & -1 & 0 \\
                0 &  0 &  0 & 1 \\
            \end{bmatrix}
        \right)
        M^{C,Vulkan}_{per,vfov}
        \begin{bmatrix}
            1 &  0 &  0 & 0 \\
            0 & -1 &  0 & 0 \\
            0 &  0 & -1 & 0 \\
            0 &  0 &  0 & 1 \\
        \end{bmatrix}
        \\
    &= \begin{bmatrix}
            1 &  0 & 0 & 0 \\
            0 & -1 & 0 & 0 \\
            0 &  0 & 1 & 0 \\
            0 &  0 & 0 & 1 \\
        \end{bmatrix}
        M^{C,Vulkan}_{per,vfov}
        \begin{bmatrix}
            1 &  0 &  0 & 0 \\
            0 & -1 &  0 & 0 \\
            0 &  0 & -1 & 0 \\
            0 &  0 &  0 & 1 \\
        \end{bmatrix}
        \\
    &= \begin{bmatrix}
            1 &  0 & 0 & 0 \\
            0 & -1 & 0 & 0 \\
            0 &  0 & 1 & 0 \\
            0 &  0 & 0 & 1 \\
        \end{bmatrix}
        \begin{bmatrix}
            \frac{1}{\text{aspect} \cdot \tan\left( \frac{\theta_{vfov}}{2} \right)} 
            & 0 
            & 0
            & 0 
            \\
            0 
            & \frac{1}{\tan\left( \frac{\theta_{vfov}}{2} \right)}
            & 0
            & 0 
            \\
            0 
            &  0 
            &  \frac{f}{f - n}
            & -\frac{ f n }{f - n} 
            \\
            0 
            & 0 
            & 1 
            & 0
            \\
        \end{bmatrix}
        \begin{bmatrix}
            1 &  0 &  0 & 0 \\
            0 & -1 &  0 & 0 \\
            0 &  0 & -1 & 0 \\
            0 &  0 &  0 & 1 \\
        \end{bmatrix}
        \\
    &= \begin{bmatrix}
            1 &  0 & 0 & 0 \\
            0 & -1 & 0 & 0 \\
            0 &  0 & 1 & 0 \\
            0 &  0 & 0 & 1 \\
        \end{bmatrix}
        \begin{bmatrix}
            \frac{1}{\text{aspect} \cdot \tan\left( \frac{\theta_{vfov}}{2} \right)} 
            & 0 
            & 0
            & 0 
            \\
            0 
            & -\frac{1}{\tan\left( \frac{\theta_{vfov}}{2} \right)}
            &  0
            &  0 
            \\
            0 
            &  0 
            & -\frac{f}{f - n}
            & -\frac{ f n }{f - n} 
            \\
            0 
            &  0 
            & -1 
            &  0
            \\
        \end{bmatrix}
        \\
    &=  \begin{bmatrix}
            \frac{1}{\text{aspect} \cdot \tan\left( \frac{\theta_{vfov}}{2} \right)} 
            & 0 
            & 0
            & 0
            \\
            0 
            & \frac{1}{\tan\left( \frac{\theta_{vfov}}{2} \right)}
            & 0
            & 0
            \\
            0 
            &  0 
            & -\frac{f}{f - n}
            & -\frac{ f n }{f - n}
            \\
            0 
            &  0 
            & -1 
            &  0
            \\
        \end{bmatrix}
```

therefore

```{math}
M^{Vulkan}_{per,vfov,rh \rightarrow rh} = 
    \begin{bmatrix}
        \frac{1}{\text{aspect} \cdot \tan\left( \frac{\theta_{vfov}}{2} \right)} 
        & 0 
        & 0
        & 0 
        \\
        0 
        & \frac{1}{\tan\left( \frac{\theta_{vfov}}{2} \right)}
        & 0
        & 0 
        \\
        0 
        &  0 
        & -\frac{f}{f - n}
        & -\frac{ f n }{f - n} 
        \\
        0 
        &  0 
        & -1 
        &  0
        \\
    \end{bmatrix}.
```

This completes the derivation of the matrices for the left-handed Vulkan projected view space coordinates.
