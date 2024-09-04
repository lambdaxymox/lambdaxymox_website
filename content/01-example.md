# A General Derivation Of Perspective And Orthographic Projection Matrices

## Projection Matrix Specification

The projection transformations are parametrized by two set of parameters: the viewing frustum bounds
{math}`l`, {math}`r`, {math}`b`, {math}`t`, {math}`n`, {math}`f` where {math}`l > 0`, {math}`r > 0`, 
{math}`b > 0`, {math}`t > 0`, and {math}`f > n > 0`; the canonical view volume parametrized by
{math}`[\alpha_{min}, \alpha_{max}] \times [\beta_{min}, \beta_{max}] \times [\gamma_{min}, \gamma_{max}]`.
where {math}`\alpha_{max} > \alpha_{min}`, {math}`\beta_{max} > \beta_{min}`, and 
{math}`\gamma_{max} > \gamma_{min}`.

The points {math}`\tilde{L} \in \mathbb{E}^{3}`, {math}`\tilde{R} \in \mathbb{E}^{3}` {math}`\tilde{B} \in \mathbb{E}^{3}`, 
{math}`\tilde{T} \in \mathbb{E}^{3}`, {math}`\tilde{N} \in \mathbb{E}^{3}`, and {math}`\tilde{F} \in \mathbb{E}^{3}` 
correspond to points along the edge of the viewport on the near plane of the viewing frustum. The point {math}`\tilde{L}` is 
the point where the near plane, left hand plane, and depth-horizontal-plane intersect each other. The point
{math}`\tilde{R}` is the point where the near plane, right plane, ans depth-horizontal plane intersect each other.
The point {math}`\tilde{B}` is the point where the near plane, bottom plane, and horizontal-vertical plane intersect
each other. The point {math}`\tilde{T}` is the point where the near plane, top plane, and horizontal-vertical plane 
intersect each other. The point {math}`\tilde{N}` is the point along the depth axis inside the near plane. The 
point {math}`\tilde{F}` is the point along the depth axis inside the far plane. More precisely, in the orthonormal
frame {math}`(\tilde{O}_{view}, (\mathbf{\hat{u}}_{h}, \mathbf{\hat{u}}_{v}, \mathbf{\hat{u}}_{d}))`, the points are given
by

```{math}
\tilde{L} &= \tilde{O}_{view} + n \mathbf{\hat{u}}_{d} + l \left( -\mathbf{\hat{u}}_{h} \right) = \tilde{O}_{view} + n \mathbf{\hat{u}}_{d} - l \mathbf{\hat{u}}_{h} \\
\tilde{R} &= \tilde{O}_{view} + n \mathbf{\hat{u}}_{d} + r \mathbf{\hat{u}}_{h} \\
\tilde{B} &= \tilde{O}_{view} + n \mathbf{\hat{u}}_{d} + b \left( -\mathbf{\hat{u}}_{v} \right) = \tilde{O}_{view} + n \mathbf{\hat{u}}_{d} - b \mathbf{\hat{u}}_{v} \\
\tilde{T} &= \tilde{O}_{view} + n \mathbf{\hat{u}}_{d} + t \mathbf{\hat{u}}_{v} \\
\tilde{N} &= \tilde{O}_{view} + n \mathbf{\hat{u}}_{d} \\
\tilde{F} &= \tilde{O}_{view} + f \mathbf{\hat{u}}_{d} \\
```

In the coordinate system of the local view space frame with origin {math}`\tilde{O}_{view}`, the points are represented by

```{math}
L \equiv \tilde{L} - \tilde{O}_{view} &= n \mathbf{\hat{u}}_{d} + l \left( -\mathbf{\hat{u}}_{h} \right) = n \mathbf{\hat{u}}_{d} - l \mathbf{\hat{u}}_{h} \\
R \equiv \tilde{R} - \tilde{O}_{view} &= n \mathbf{\hat{u}}_{d} + r \mathbf{\hat{u}}_{h} \\
B \equiv \tilde{B} - \tilde{O}_{view} &= n \mathbf{\hat{u}}_{d} + b \left( -\mathbf{\hat{u}}_{v} \right) = n \mathbf{\hat{u}}_{d} - b \mathbf{\hat{u}}_{v} \\
T \equiv \tilde{T} - \tilde{O}_{view} &= n \mathbf{\hat{u}}_{d} + t \mathbf{\hat{u}}_{v} \\
N \equiv \tilde{N} - \tilde{O}_{view} &= n \mathbf{\hat{u}}_{d} \\
F \equiv \tilde{F} - \tilde{O}_{view} &= f \mathbf{\hat{u}}_{d} \\
```

The orthonormal frame {math}`\left(\tilde{O}_{view}, \left( \mathbf{\hat{u}}_{h}, \mathbf{\hat{u}}_{v}, \mathbf{\hat{u}}_{d} \right) \right)` 
on {math}`\mathbb{E}^{3}` induces a coordinate chart as follows. A point {math}`\tilde{P} \in \mathbb{E}^{3}` is written as

```{math}
\tilde{P} = \tilde{O}_{view} + P_{h} \mathbf{\hat{u}}_{h} + P_{v} \mathbf{\hat{u}}_{v} + P_{d} \mathbf{\hat{u}}_{d}
```

and the orthonormal frame {math}`\left(\tilde{O}_{view}, \left( \mathbf{\hat{u}}_{h}, \mathbf{\hat{u}}_{v}, \mathbf{\hat{u}}_{d} \right) \right)` 
defines a coordinate chart {math}`\psi : \mathbb{E}^{3} \rightarrow \mathbb{R}^{3}` by 
{math}`\psi( \tilde{P} ) = \tilde{P} - \tilde{O}_{view}`. In particular,

```{math}
P = \psi(\tilde{P}) \equiv \tilde{P} - \tilde{O}_{view} = P_{h} \mathbf{\hat{u}}_{h} + P_{v} \mathbf{\hat{u}}_{v} + P_{d} \mathbf{\hat{u}}_{d}
```

is the representation of {math}`\tilde{P}` in {math}`\mathbb{R}^{3}`. Also, the coordinate map 
{math}`\psi` maps the origin {math}`\tilde{O}_{view}` of the view space frame in {math}`\mathbb{E}^{3}` 
to {math}`\mathbf{0}`: {math}`O_{view}` = {math}`\psi(\tilde{O}_{view}) = \tilde{O}_{view} - \tilde{O}_{view} = \mathbf{0}`. 
This shows that the view space frame origin in {math}`\mathbb{E}^{3}` indeed maps to the vector space origin 
{math}`\mathbf{0}` in {math}`\mathbb{R}^{3}`.

Define a map {math}`\rho : \mathbb{R}^{3} \rightarrow \mathbb{R}^{4} - \{\mathbf{0}\}` by

```{math}
\rho\left( P \right) = \begin{pmatrix} P \\ 1 \\ \end{pmatrix}
```

We define projective space {math}`\mathbb{RP}^{3}` as follows. We define {math}`\mathbf{w_{1}} \sim \mathbf{w_{2}}`
if an only if there exists a nonzero real number {math}`k \in \mathbb{R} - \{0\}` such that 
{math}`\mathbf{w_{1}} = k \mathbf{w_{2}}`. The relation {math}`\sim` is an equivalence relation. 
We define the **real projective space** by {math}`\mathbb{RP}^{3} = ( \mathbb{R}^{4} - \{ \mathbf{0}\} )/\sim`. 
The real projective space identifies lines through the origin in {math}`\mathbb{R}^{3}` with points in 
{math}`\mathbb{RP}^{3}`. The definition of real projective space allows us to define a surjective map 
{math}`\pi : \mathbb{R}^{4} - \{\mathbf{0}\} \rightarrow \mathbb{RP}^{3}` by 

```{math}
\pi\left( \begin{pmatrix} P \\ w \\ \end{pmatrix} \right) = \begin{bmatrix} \begin{pmatrix} P \\ w \\ \end{pmatrix} \end{bmatrix}
```

where {math}`[.]` on the right-hand side indicates the equivalence class of {math}`\begin{pmatrix} P^{T}, 1 \end{pmatrix}^{T}`.
The function {math}`\pi` is well-defined. The maps {math}`\pi` and {math}`\rho` together allow us to map from view space to 
projective view space with the camera orthonormal frame by

```{math}
\left(\pi \circ \rho\right)\left(P\right) 
    = \pi\left( \rho\left( P \right) \right) 
    = \pi\left( \begin{pmatrix} P \\ 1 \\ \end{pmatrix} \right)
    = \begin{bmatrix} \begin{pmatrix} P \\ 1 \\ \end{pmatrix} \end{bmatrix}.
```

To understand what the projection matrices do, we must understand the coordinate systems that 
we map between at each step in the pipeline leading from the view space to the canonical view 
volume. The transformations stem from choosing a convenient coordinate system 
in which to render computer graphics. The convenient coordinate system we choose is often called
**normalized device coordinates**, defined in the following. The canonical coordinate system differs from platform 
to platform. The other coordinates systems exist to articulate a clear path from projective view coordinates to
normalized device coordinates. First we must define the viewing space. 

The **view space** is the Euclidean space {math}`(\mathbb{E}^{3}, (\tilde{O}_{view}, B_{view}))` with 
the following properties:

* The underlying vector space is {math}`\mathbb{R}^{3}`.
* The **origin** of the orthonormal frame is the point {math}`\tilde{O}_{view} \in \mathbb{E}^{3}`.
* The **basis** of the orthonormal frame is 
  {math}`B_{view} = (\mathbf{\hat{u}}_{h}, \mathbf{\hat{u}}_{v}, \mathbf{\hat{u}}_{d})` where
  the basis vector {math}`\mathbf{\hat{u}}_{h}` points to the right, the basis vector {math}`\mathbf{\hat{u}}_{v}`
  points up, and the basis vector {math}`\mathbf{\hat{u}}_{d}` points into the view volume.

The **projected view space** is the real projective space {math}`\mathbb{RP}^{3}` where each 
point is a homogeneous vector in {math}`\mathbb{R}^{3}` in the coordinate frame {math}`(\tilde{O}_{view}, B_{view})`. 
The **view coordinates** coordinate system is the coordinate system defined by the basis {math}`B_{view}`.

## Coordinate Systems To Construct An Orthographic Projection

In order to construct an orthographic projection transformation, we need to define a set 
of coordinate systems and construct the transformations between them to get from projected view 
space to normalized device coordinates. 

The coordinate system **clip coordinates** defined by the orthonormal 
frame {math}`(\tilde{O}_{clip}, B_{clip})` on {math}`\mathbb{RP}^{3}` with the following properties:

* The **origin** of the orthonormal frame is the point {math}`\tilde{O}_{clip} \in \mathbb{E}^{3}`. In 
  {math}`\mathbb{R}^{3}`, this is the vector {math}`O_{clip} = \tilde{O}_{clip} - \tilde{O}_{view}`. 
  In {math}`\mathbb{RP}^{3}`, this is the homogeneous point {math}`[(O^{T}_{clip}, 1)^{T}]`.
* The **basis** of the orthonormal frame is
  {math}`B_{clip} = (\mathbf{\hat{u}}_{clip,h}, \mathbf{\hat{u}}_{clip,v}, \mathbf{\hat{u}}_{clip,d})` where
  the basis vector {math}`\mathbf{\hat{u}}_{clip,h}` points to the right, the basis vector {math}`\mathbf{\hat{u}}_{clip,v}`
  points up, and the basis vector {math}`\mathbf{\hat{u}}_{clip,d}` points into the view volume.
* The viewing volume is parametrized by {math}`[-l, r] \times [-b, t] \times [n, f]`.
  We call this viewing volume the **orthographic viewing volume**.

The coordinate system **normalized device coordinates** defined by the orthonormal
frame {math}`(\tilde{O}_{ndc}, B_{ndc})` on {math}`\mathbb{RP}^{3}` with the following properties:

* The **origin** of the orthonormal frame is the point {math}`\tilde{O}_{ndc} \in \mathbb{E}^{3}`. In
  {math}`\mathbb{R}^{3}`, this is the vector {math}`O_{ndc} = \tilde{O}_{ndc} - \tilde{O}_{view}`.
  In {math}`\mathbb{RP}^{3}`, this is the homogeneous point {math}`[(O^{T}_{ndc}, 1)^{T}]`.
* The **basis** of the orthonormal frame is
  {math}`B_{ndc} = (\mathbf{\hat{u}}_{ndc,h}, \mathbf{\hat{u}}_{ndc,v}, \mathbf{\hat{u}}_{ndc,d})` where
  The basis vector {math}`\mathbf{\hat{u}}_{ndc,h}` points to the right, the basis vector {math}`\mathbf{\hat{u}}_{ndc,v}`
  points up, and the basis vector {math}`\mathbf{\hat{u}}_{ndc,d}` points into the view volume.
* The viewing volume canonical viewing volume is parametrized by 
  {math}`[\alpha_{min}, \alpha_{max}] \times [\beta_{min}, \beta_{max}] \times [\gamma_{min}, \gamma_{max}]`.
  We call the viewing volume in this coordinate system the **canonical view volume**.

## Coordinate Systems To Construct A Perspective Projection

For a perspective projection transformation, we want to map the **perspective viewing volume**
specified by the parameters {math}`l`, {math}`r`, {math}`b`, {math}`t`, {math}`n`, and {math}`f` to our 
choice of **canonical view volume** specified by our normalized device coordinates parameters 
{math}`\alpha_{min}`, {math}`\alpha_{max}`, {math}`\beta_{min}`, {math}`\beta_{max}`, {math}`\gamma_{min}`, 
and {math}`\gamma_{max}`.

The coordinate system **projected coordinates** is defined by the orthonormal 
frame {math}`(\tilde{O}_{proj}, B_{proj})` on {math}`\mathbb{RP}^{3}` with the following properties:

* The **origin** of the orthonormal frame is the point {math}`\tilde{O}_{proj} \in \mathbb{E}^{3}`. In
  {math}`\mathbb{R}^{3}`, this is the vector {math}`O_{proj} = \tilde{O}_{proj} - \tilde{O}_{view}`.
  In {math}`\mathbb{RP}^{3}`, this is the homogeneous point {math}`[(O^{T}_{proj}, 1)]^{T}`.
* The **basis** of the orthonormal frame is
  {math}`B_{proj} = (\mathbf{\hat{u}}_{proj,h}, \mathbf{\hat{u}}_{proj,v}, \mathbf{\hat{u}}_{proj,d})` where
  the basis vector {math}`\mathbf{\hat{u}}_{proj,h}` points to the right, the basis vector {math}`\mathbf{\hat{u}}_{proj,v}`
  points up, and the basis vector {math}`\mathbf{\hat{u}}_{proj,d}` points into the view volume.
* Projected points are scaled by their depth coordinate.

The coordinate system **clip coordinates** defined by the orthonormal 
frame {math}`(O_{clip}, B_{clip})` on {math}`\mathbb{RP}^{3}` with the following properties:

* The **origin** of the orthonormal frame is the point {math}`O_{clip} \in \mathbb{E}^{3}`. In 
  {math}`\mathbb{R}^{3}`, this is the vector {math}`O_{clip} = \tilde{O}_{clip} - \tilde{O}_{view}`. 
  In {math}`\mathbb{RP}^{3}`, this is the homogeneous point {math}`[(O^{T}_{clip}, 1)^{T}]`.
* The **basis** of the orthonormal frame is
  {math}`B_{clip} = (\mathbf{\hat{u}}_{clip,h}, \mathbf{\hat{u}}_{clip,v}, \mathbf{\hat{u}}_{clip,d})` where
  the basis vector {math}`\mathbf{\hat{u}}_{clip,h}` points to the right, the basis vector {math}`\mathbf{\hat{u}}_{clip,v}`
  points up, and the basis vector {math}`\mathbf{\hat{u}}_{clip,d}` points into the view volume.
* The view volume is parametrized by {math}`[-l, r] \times [-b, t] \times [n, f]`.
  We call this view volume the **orthographic view volume**.

The coordinate system **normalized device coordinates** defined by the orthonormal
frame {math}`(O_{ndc}, B_{ndc})` on {math}`\mathbb{RP}^{3}` with the following properties:

* The **origin** of the orthonormal frame is the point {math}`O_{ndc} \in \mathbb{E}^{3}`. In
  {math}`\mathbb{R}^{3}`, this is the vector {math}`O_{ndc} = \tilde{O}_{ndc} - \tilde{O}_{view}`.
  In {math}`\mathbb{RP}^{3}`, this is the homogeneous point {math}`[(O^{T}_{ndc}, 1)^{T}]`.
* The **basis** of the orthonormal frame is
  {math}`B_{ndc} = (\mathbf{\hat{u}}_{ndc,h}, \mathbf{\hat{u}}_{ndc,v}, \mathbf{\hat{u}}_{ndc,d})` where
  The basis vector {math}`\mathbf{\hat{u}}_{ndc,h}` points to the right, the basis vector {math}`\mathbf{\hat{u}}_{ndc,v}`
  points up, and the basis vector {math}`\mathbf{\hat{u}}_{ndc,d}` points into the view volume.
* The view volume is parametrized by 
  {math}`[\alpha_{min}, \alpha_{max}] \times [\beta_{min}, \beta_{max}] \times [\gamma_{min}, \gamma_{max}]`.
  We call the view volume in this coordinate system the **canonical view volume**.


## The Canonical Coordinate Systems

We choose a canonical set of coordinate systems to construct the canonical projective transformations
that will be used to derive the projective transformations in specific settings.

The **canonical view coordinates** is the view space coordinate system with orthnormal frame 
{math}`(\tilde{O}_{view}, B_{view})` such that the depth frame vector {math}`\mathbf{\hat{u}}_{d}` points into 
the view volume. This gives the 
canonincal view space a left-handed orientation. The **canonical projected coordinates** coordinate system 
is the projected coordinate system with the same orthonormal frame as canonical view space coordinates. The
**canonical clip coordinates** coordinate system is the clip coordinates with the same orthonormal frame as 
the canonical view space coordinates. The **canonical normalized device coordinates** coordinate system is
the normalized device coordinate system with the same orthonormal frame as in canonical view coordinates. 
In particular, each coordinate system uses the same orthonormal frame, and has a left-handed orientation.

It is not strictly required that each space have the same orthonormal frame, but using the same coordinate 
system in each step makes it easier to see what is going on in the graphics pipeline, and since we are going 
to derive each transformation between the spaces above, one can always transform any one of them with a
combination of orthogonal transformations and changes of orientation to get the desired ones.

## The Canonical Perspective Projection Matrix

With these considerations, we now proceed to construct the canonical perspective projection matrix for 
the frame {math}`(O_{view}, (\mathbf{\hat{u}}_{h}, \mathbf{\hat{u}}_{v}, \mathbf{\hat{u}}_{d}))` and 
the canonical view volume parametrized by 
{math}`[\alpha_{min}, \alpha_{max}] \times [\beta_{min}, \beta_{max}] \times [\gamma_{min}, \gamma_{max}]`.
Let {math}`P \in \mathbb{R}^{3}` be a point given by 
{math}`P = P_{h} \mathbf{\hat{u}}_{h} + P_{v} \mathbf{\hat{u}}_{v} + P_{d} \mathbf{\hat{u}}_{d}`. We will now 
derive the perspective projected horizontal and vertical coordinates.

Using similar triangles for the horizontal component, we have

```{math}
\frac{P_{h}}{P_{d}} = \frac{P \cdot \mathbf{\hat{u}}_{h}}{P \cdot \mathbf{\hat{u}}_{d}} 
                    = \frac{P_{proj} \cdot \mathbf{\hat{u}}_{h}}{n} 
                    = \frac{P_{proj,h}}{n}
```

which yields

```{math}
P_{proj,h} = n P_{h} \left( \frac{1}{P_{d}} \right).
```

Analagously for the vertical component, applying similar triangles gives us

```{math}
\frac{P_{v}}{P_{d}} 
    = \frac{P \cdot \mathbf{\hat{u}}_{v}}{P \cdot \mathbf{\hat{u}}_{d}} 
    = \frac{P_{proj} \cdot \mathbf{\hat{u}}_{v}}{n} 
    = \frac{P_{proj,v}}{n}
```

implying that

```{math}
P_{proj,v} = n P_{v} \left( \frac{1}{P_{d}} \right).
```

Now we must map from projected coordinates to normalized device coordinates. We deduce the 
transformation from projected space to clip space indirectly by calculating the canonical view 
volume components first. This is necessary since the mapping from projected coordinates to clip 
space depends on the parametrization of the canonical view volume chosen. The resulting transformation
is an orthographic transformation that maps the orthographic view volume {math}`[-l, r] \times [-b, t] \times [n, f]`
to the canonical view volume {math}`[\alpha_{min}, \alpha_{max}] \times [\beta_{min}, \beta_{max}] \times [\gamma_{min}, \gamma_{max}]`.
The map from projected space to clip space is a linear map, so the coordinates must transform affinely.

We need an affine map {math}`\phi_{h} : \mathbb{R} \rightarrow \mathbb{R}` such that

```{math}
:label: eq_phi_h_definition_orth
P_{ndc,h} = \phi_{h}\left( P_{proj,h} \right) = A P_{proj,h} + B
```

with constraints {math}`\phi_{h}\left( -l \right) = \alpha_{min}` and {math}`\phi_{h}\left( r \right) = \alpha_{max}`.

Using the constraints, we have

```{math}
:label: constraints1
\alpha_{max} &= A r + B \\
\alpha_{min} &= A \left( -l \right) + B = -Al + B
```

Subtracting {math}`\alpha_{max}` from {math}`\alpha_{min}` in {ref}`constraints1`

```{math}
\alpha_{max} - \alpha_{min} 
    = \left( A r + B \right) - \left( -Al + B \right) = A \left( r + l \right) 
    = A \left( r - \left( -l \right) \right)
```

and solving for {math}`A`

```{math}
:label: constant1
A = \frac{\alpha_{max} - \alpha_{min}}{r - \left( -l \right)}.
```

Substituting the expression for {math}`A` back into the constraint on {math}`\alpha_{max}` we have

```{math}
\alpha_{max} = \left( \frac{\alpha_{max} - \alpha_{min}}{r - \left( -l \right)} \right) r + B.
```

Solving for {math}`B`

```{math}
B &= \alpha_{max} - \left( \frac{\alpha_{max} - \alpha_{min}}{r + l} \right) r \\
  &= \frac{\alpha_{max} \left( r + l \right) + \left( \alpha_{max} - \alpha_{min} \right) r}{r + l} \\
  &= \frac{\alpha_{max} r + \alpha_{max} l - \alpha_{max} r - \alpha_{min} r}{r + l} \\
  &= \frac{\alpha_{max} l + \alpha_{min} r}{r + l} \\
  &= \frac{\alpha_{min} r + \alpha_{max} l}{r - \left( -l \right)} \\
  &= \frac{\alpha_{min} r - \alpha_{max} \left( -l \right)}{r - \left( -l \right)} \\
```

which gives us

```{math}
:label: constant2
B = \frac{\alpha_{min} r - \alpha_{max} \left( -l \right)}{r - \left( -l \right)}.
```

which is the constant for {math}`\phi_{h}`. Substituting the constants {ref}`constant1` and {ref}`constant2`
back into the definition for {math}`\phi_{h}` in equation {ref}`eq_phi_h_definition_orth`

```{math}
:label: eq_ndc_h
P_{ndc, h} = \phi_{h} \left( P_{proj,h} \right) 
           = \left( \frac{\alpha_{max} - \alpha_{min}}{r - \left( -l \right)} \right) P_{proj,h} 
           + \frac{\alpha_{min} r - \alpha_{min} \left( -l \right)}{r - \left( -l \right)}
```

which completes derivation of the formula for {math}`P_{ndc,h}`.

We need an affine map {math}`\phi_{v} : \mathbb{R} \rightarrow \mathbb{R}` such that

```{math}
:label: eq_phi_v_definition_per
P_{ndc,v} = \phi_{v}\left( P_{proj,v} \right) = A P_{proj,v} + B
```

with constraints {math}`\phi_{v}\left( -b \right) = \beta_{min}` and {math}`\phi_{v}\left( t \right) = \beta_{max}`.

Using the constraints, we have

```{math}
:label: constraints2
\beta_{max} &= A t + B \\
\beta_{min} &= A \left( -b \right) + B = -Ab + B
```
Subtracting {math}`\beta_{max}` from {math}`\beta_{min}` in {ref}`constraints2`

```{math}
\beta_{max} - \beta_{min} 
    = \left( A t + B \right) - \left( -A b + B \right) 
    = A \left( t + b \right) = A \left( t - \left( -b \right) \right)
```

and solving for {math}`A`

```{math}
:label: constant3
A = \frac{\beta_{max} - \beta_{min}}{t - \left( -b \right)}.
```

Substituting the expression for {math}`A` back into the constraint on {math}`\beta_{max}` we have

```{math}
\beta_{max} = \left( \frac{\beta_{max} - \beta_{min}}{t - \left( -b \right)} \right) t + B.
```

Solving for {math}`B`

```{math}
B &= \beta_{max} - \left( \frac{\beta_{max} - \beta_{min}}{t + b} \right) t \\
  &= \frac{\beta_{max} \left( t + b \right) + \left( \beta_{max} - \beta_{min} \right) t}{t + b} \\
  &= \frac{\beta_{max} t + \beta_{max} b - \beta_{max} b - \beta_{min} t}{t + b} \\
  &= \frac{\beta_{max} b + \beta_{min} t}{t + b} \\
  &= \frac{\beta_{min} t + \beta_{max} b}{t - \left( -b \right)} \\
  &= \frac{\beta_{min} t - \beta_{max} \left( -b \right)}{t - \left( -b \right)} \\
```

which gives us

```{math}
:label: constant4
B = \frac{\beta_{min} t - \beta_{max} \left( -b \right)}{t - \left( -b \right)}
```

which is the constant for {math}`\phi_{v}`. Substituting the constants {ref}`constant3` and {ref}`constant4`
back into the definition for {math}`\phi_{v}` in equation {ref}`eq_phi_v_definition_per`

```{math}
:label: eq_ndc_v
P_{ndc,v} = \phi_{v} \left( P_{proj,v} \right) 
           = \left( \frac{\beta_{max} - \beta_{min}}{t - \left( -b \right)} \right) P_{proj,v} 
           + \frac{\beta_{min} t - \beta_{min} \left( -b \right)}{t - \left( -b \right)}
```

which completes the derivation of the formula for {math}`P_{ndc,v}`.


We need an affine map {math}`\phi_{d} : \mathbb{R} \rightarrow \mathbb{R}` such that

```{math}
:label: eq_ndc_d1
P_{ndc,d} = \left( A P_{d} + B P_{w} \right) \left( \frac{1}{P_{d}} \right)
          = \left( A P_{d} + B \right) \left( \frac{1}{P_{d}} \right)
          = \phi_{d} \left( P_{d} \right) \left( \frac{1}{P_{d}} \right) 
```

since {math}`P_{w} = 1` in view space. The last equality in {ref}`eq_ndc_d1` above yields

```{math}
:label: eq_phi_d_per
\phi_{d} \left( P_{d} \right) = A P_{d} + B
```

The map {math}`\phi_{d}` must satisfy the constraints {math}`\phi_{d}\left( n \right) \cdot \frac{1}{n} = \gamma_{min}` 
and {math}`\phi_{d}\left( f \right) \cdot \frac{1}{f} = \gamma_{max}` or equivalently
{math}`\phi_{d}\left( n \right) = \gamma_{min} n` and {math}`\phi_{d}\left( f \right) = \gamma_{max} f`.
Now we need to determine {math}`A` and {math}`B`. Using the constraints on {math}`\phi_{d}` we see that

```{math}
:label: eq3
\phi_{d}(f) - \phi_{d}(n) = \left( A f + B \right) - \left( A n + B \right) 
                          = A \left( f - n \right)
                          = \gamma_{max} f - \gamma_{min} n
```

and therefore 

```{math}
:label: constant5
A = \frac{\gamma_{max} f - \gamma_{min} n}{f - n}
```

from the last equality in {ref}`eq3`. Using the expression for {math}`A` and the constraints on {math}`\phi_{d}` 
again, we have

```{math}
\phi_{d}\left( f \right) = A f + B 
                         = \left(  \frac{\gamma_{max} f - \gamma_{min} n}{f - n} \right) f + B 
                         = \gamma_{max} f.
```

Solving for {math}`B`

```{math}
B &= \gamma_{max} f - \left(  \frac{\gamma_{max} f - \gamma_{min} n}{f - n} \right) f \\
  &= \frac{\gamma_{max} f \left( f - n \right) - \left( \gamma_{max} f - \gamma_{min} n \right) f}{ f - n } \\
  &= \frac{\gamma_{max} f^{2} - \gamma_{max} f n - \gamma_{max} f^{2} - \gamma_{min} f n}{ f - n } \\
  &= - \frac{ \left( \gamma_{max} - \gamma_{min} \right) f n }{ f - n }
```

so that

```{math}
:label: constant6
B = - \frac{ \left( \gamma_{max} - \gamma_{min} \right) f n }{ f - n }.
```

Substituting results {ref}`constant5` and {ref}`constant6` back into {ref}`eq_phi_d_per`, we get the desired
result for {math}`\phi_{d}`

```{math}
\phi_{d}\left( P_{proj,d} \right) = 
    \left( \frac{\gamma_{max} f - \gamma_{min} n}{f - n} \right) P_{proj,d} - \frac{ \left( \gamma_{max} - \gamma_{min} \right) f n }{ f - n }
```

and the final result for {math}`P_{ndc,d}` from {ref}`eq_ndc_d` becomes

```{math}
:label: eq_ndc_d
P_{ndc,d} = 
    \left[
        \left( \frac{\gamma_{max} f - \gamma_{min} n}{f - n} \right) P_{proj,d} 
        - \frac{ \left( \gamma_{max} - \gamma_{min} \right) f n }{ f - n }
    \right]
    \left( \frac{1}{P_{d}} \right)
```

After depth normalization, the {math}`w` component of a homogeneous vector is {math}`1`. Moreover, we require 
that the projected view coordinates to clip coordinates transformations carries the depth coordinate information
to clip space. This leads the the {math}`w` component having the form

```{math}
:label: eq_ndc_w
P_{ndc,w} = 1 = P_{d} \left( \frac{1}{P_{d}} \right).
```

where depth normalization is visible in the equation. This completes the derivation of the components 
for mapping a point {math}`\tilde{P}` from view space to normalized device coordinates. Using the results 
for mapping to normalized device coordinates, it is straightforward to infer the clip space components 
for the point {math}`\tilde{P}`.

From {ref}`eq_ndc_h`
```{math}
P_{ndc,h} \equiv \frac{P_{clip,h}}{P_{clip,w}} 
    &= \left( \frac{\alpha_{max} - \alpha_{min}}{r - \left( -l \right)} \right) P_{proj,h} 
    + \frac{\alpha_{min} r - \alpha_{min} \left( -l \right)}{r - \left( -l \right)} \\
    &= \left( \frac{\alpha_{max} - \alpha_{min}}{r - \left( -l \right)} \right) n P_{h} \left( \frac{1}{P_{d}} \right) 
    + \frac{\alpha_{min} r - \alpha_{min} \left( -l \right)}{r - \left( -l \right)} \\
    &= \left( \frac{\alpha_{max} - \alpha_{min}}{r - \left( -l \right)} \right) n P_{h} \left( \frac{1}{P_{d}} \right) 
    + \left( \frac{ \alpha_{min} r - \alpha_{min} \left( -l \right) }{r - \left( -l \right)} \right) P_{d} \left( \frac{1}{P_{d}} \right) \\
    &= \left( \frac{ \left( \alpha_{max} - \alpha_{min} \right) n }{r - \left( -l \right)} \right) P_{h} \left( \frac{1}{P_{d}} \right) 
    + \left( \frac{ \alpha_{min} r - \alpha_{min} \left( -l \right) }{r - \left( -l \right)} \right) P_{d} \left( \frac{1}{P_{d}} \right) \\
    &= \left[ \left( \frac{ \left( \alpha_{max} - \alpha_{min} \right) n }{r - \left( -l \right)} \right) P_{h} 
    + \left( \frac{ \alpha_{min} r - \alpha_{min} \left( -l \right) }{r - \left( -l \right)} \right) P_{d} \right] \left( \frac{1}{P_{d}} \right) \\
```
and therefore

```{math}
P_{clip,h} = \left( \frac{ \left( \alpha_{max} - \alpha_{min} \right) n }{r - \left( -l \right)} \right) P_{h} 
           + \left( \frac{ \alpha_{min} r - \alpha_{min} \left( -l \right) }{r - \left( -l \right)} \right) P_{d}
```

is the desired clip space horizontal component.

From {ref}`eq_ndc_v`

```{math}
P_{ndc,v} \equiv \frac{P_{clip,v}}{P_{clip,w}} 
    &= \left( \frac{\beta_{max} - \beta_{min}}{t - \left( -b \right)} \right) P_{proj,v} 
    + \frac{\beta_{min} t - \beta_{min} \left( -b \right)}{t - \left( -b \right)} \\
    &= \left( \frac{\beta_{max} - \beta_{min}}{t - \left( -b \right)} \right) n P_{v} \left( \frac{1}{P_{d}} \right) 
    + \frac{\beta_{min} t - \beta_{min} \left( -b \right)}{t - \left( -b \right)} \\
    &= \left( \frac{\beta_{max} - \beta_{min}}{t - \left( -b \right)} \right) n P_{v} \left( \frac{1}{P_{d}} \right) 
    + \left( \frac{ \beta_{min} t - \beta_{min} \left( -b \right) }{t - \left( -b \right)} \right) P_{d} \left( \frac{1}{P_{d}} \right) \\
    &= \left( \frac{ \left( \beta_{max} - \beta_{min} \right) n }{t - \left( -b \right)} \right) P_{v} \left( \frac{1}{P_{d}} \right) 
    + \left( \frac{ \beta_{min} t - \beta_{min} \left( -b \right) }{t - \left( -b \right)} \right) P_{d} \left( \frac{1}{P_{d}} \right) \\
    &= \left[ \left( \frac{ \left( \beta_{max} - \beta_{min} \right) n }{t - \left( -b \right)} \right) P_{v} 
    + \left( \frac{ \beta_{min} t - \beta_{min} \left( -b \right) }{t - \left( -b \right)} \right) P_{d} \right] \left( \frac{1}{P_{d}} \right) \\
```

and therefore

```{math}
P_{clip,v} = \left( \frac{ \left( \beta_{max} - \beta_{min} \right) n }{t - \left( -b \right)} \right) P_{v} 
           + \left( \frac{ \beta_{min} t - \beta_{min} \left( -b \right) }{t - \left( -b \right)} \right) P_{d}
```
is the desired clip space vertical component.

From {ref}`eq_ndc_d`

```{math}
P_{ndc,d} \equiv \frac{P_{clip,d}}{P_{clip,w}} 
    &= \left[
            \left( \frac{\gamma_{max} f - \gamma_{min} n}{f - n} \right) P_{proj,d} 
            - \frac{ \left( \gamma_{max} - \gamma_{min} \right) f n }{ f - n }
        \right] 
        \left( \frac{1}{P_{d}} \right)
        \\
    &= \left[
            \left( \frac{\gamma_{max} f - \gamma_{min} n}{f - n} \right) P_{d} 
            - \frac{ \left( \gamma_{max} - \gamma_{min} \right) f n }{ f - n }
        \right] 
        \left( \frac{1}{P_{d}} \right)
        \\
```

and therefore

```{math}
P_{clip,d} = 
    \left( \frac{\gamma_{max} f - \gamma_{min} n}{f - n} \right) P_{d} 
    - \frac{ \left( \gamma_{max} - \gamma_{min} \right) f n }{ f - n }
```

is the desired clip space depth component.

From {ref}`eq_ndc_w`

```{math}
P_{ndc,w} \equiv \frac{P_{clip,w}}{P_{clip,w}} = 1 = P_{d} \left( \frac{1}{P_{d}} \right)
```

and therefore

```{math}
P_{clip,w} = P_{d}
```

is the desired clip space {math}`w`-component.

Assembling the clip space components together into the resulting matrix equation, we have

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
\end{bmatrix}
= 
M^{C}_{per} 
\begin{bmatrix} 
    P_{h} \\ 
    P_{v} \\ 
    P_{d} \\ 
    P_{w} \\
\end{bmatrix}
.
```

This yields our first major result, the canonical perspective projection matrix.

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

## The Canonical Orthographic Projection Matrix

We now construct the canonical orthographic projection matrix for the frame 
{math}`(O_{view}, (\mathbf{\hat{u}}_{h}, \mathbf{\hat{u}}_{v}, \mathbf{\hat{u}}_{d}))` and 
the canonical view volume parametrized by 
{math}`[\alpha_{min}, \alpha_{max}] \times [\beta_{min}, \beta_{max}] \times [\gamma_{min}, \gamma_{max}]`.
Let {math}`P \in \mathbb{R}^{3}` be a point given by 
{math}`P = P_{h} \mathbf{\hat{u}}_{h} + P_{v} \mathbf{\hat{u}}_{v} + P_{d} \mathbf{\hat{u}}_{d}`.

There is no intermediate projected coordinates for an orthographic projection matrix, because depth
normalization does not apply to an orthographic projection. Thus we map directly to clip coordinates 
from projected view coordinates.

We need an affine map {math}`\phi_{h} : \mathbb{R} \rightarrow \mathbb{R}` such that

```{math}
:label: eq_phi_h_orth
P_{ndc,h} = \phi_{h}\left( P_{h} \right) = A P_{h} + B
```

with constraints {math}`\phi_{h}\left( -l \right) = \alpha_{min}` and {math}`\phi_{h}\left( r \right) = \alpha_{max}`.

Using the constraints, we have

```{math}
:label: contraints_orth_h
\alpha_{max} &= A r + B \\
\alpha_{min} &= A \left( -l \right) + B = -A l + B
```

Subtracting {math}`\alpha_{max}` from {math}`\alpha_{min}` in {ref}`contraints_orth_h`

```{math}
\alpha_{max} - \alpha_{min} = \left( A r + B \right) - \left( -A l + B \right) = A \left(r - \left( -l \right) \right)
```

and solving for {math}`A`

```{math}
:label: constant_orth_h_a
A = \frac{\alpha_{max} - \alpha_{min}}{r - \left( -l \right)}.
```

Substituting the expression for {math}`A` back into the constraint on {math}`\alpha_{max}` we have

```{math}
\alpha_{max} = \left( \frac{\alpha_{max} - \alpha_{min}}{r - \left( -l \right)} \right) r + B
```

Solving for {math}`B`

```{math}
B &= \alpha_{max} - \left( \frac{\alpha_{max} - \alpha_{min}}{r + l} \right) r \\
  &= \frac{\alpha_{max} \left( r + l \right) + \left( \alpha_{max} - \alpha_{min} \right) r}{r + l} \\
  &= \frac{\alpha_{max} r + \alpha_{max} l - \alpha_{max} r - \alpha_{min} r}{r + l} \\
  &= \frac{\alpha_{max} l + \alpha_{min} r}{r + l} \\
  &= \frac{\alpha_{min} r + \alpha_{max} l}{r - \left( -l \right)} \\
  &= \frac{\alpha_{min} r - \alpha_{max} \left( -l \right)}{r - \left( -l \right)} \\
```

which gives us

```{math}
:label: constant_orth_h_b
B = \frac{\alpha_{min} r - \alpha_{max} \left( -l \right) }{r - \left( -l \right)}.
```

which is the constant for {math}`\phi_{h}`. Substituting the constants {ref}`constant_orth_h_a` and {ref}`constant_orth_h_b`
back into the definition for {math}`\phi_{h}` in equation {ref}`eq_phi_h_orth`

```{math}
P_{ndc,h} = \phi_{h}\left( P_{h} \right) 
          = \left( \frac{\alpha_{max} - \alpha_{min}}{r - \left( -l \right)} \right) P_{h}
          + \frac{\alpha_{min} r - \alpha_{max} \left( -l \right) }{r - \left( -l \right)}.
```

We need an affine map {math}`\phi_{v} : \mathbb{R} \rightarrow \mathbb{R}` such that

```{math}
:label: eq_phi_v_orth
P_{ndc,v} = \phi_{v}\left( P_{v} \right) = A P_{v} + B
```

with constraints {math}`\phi_{v}\left( -b \right) = \beta_{min}` and {math}`\phi_{v}\left( r \right) = \beta_{max}`.

Using the constraints, we have 

```{math}
:label: constraints_orth_v
\beta_{max} &= A t + B \\
\beta_{min} &= A \left( -b \right) + B = -A b + B \\
```

Subtracting {math}`\beta_{max}` from {math}`\beta_{min}` in {ref}`constraints_orth_v`

```{math}
\beta_{max} - \beta_{min} = \left( A t + B \right) - \left( A \left( -b \right) + B \right) = A \left( t - \left( -b \right) \right)
```

and solving for {math}`A`

```{math}
:label: constant_orth_v_a
A = \frac{\beta_{max} - \beta_{min}}{t - \left( -b \right)}
```

Substituting the expression for {math}`A` back into the constraint for {math}`\beta_{max}` we have


```{math}
\beta_{max} = \left( \frac{\beta_{max} - \beta_{min}}{t - \left( -b \right)} \right) t + B
```

Solving for {math}`B`

```{math}
B &= \beta_{max} - \left( \frac{\beta_{max} - \beta_{min}}{t - \left( -b \right)} \right) t \\
  &= \frac{ \beta_{max} \left( t + b \right) + \left( \beta_{max} b - \beta_{min} t \right) }{t + b} \\
  &= \frac{\beta_{max} t + \beta_{max} b - \beta_{max} t + \beta_{min} t}{t + b} \\
  &= \frac{\beta_{min} t + \beta_{max} b}{t + b} \\
  &= \frac{\beta_{min} t - \beta_{max} \left( -b \right)}{t + b} \\
  &= \frac{\beta_{min} t - \beta_{max} \left( -b \right)}{t - \left( -b \right)} \\
```

which gives us

```{math}
:label: constant_orth_v_b
B = \frac{\beta_{min} t - \beta_{max} \left( -b \right)}{t - \left( -b \right)}
```

which is the constant for {math}`\phi_{v}`. Substituting the constants {ref}`constant_orth_v_a` and {ref}`constant_orth_v_b`
back into the definition of {math}`\phi_{v}` in equation {ref}`eq_phi_v_orth`

```{math}
P_{ndc,v} = \phi_{v}\left( P_{v} \right)
          = \left( \frac{\beta_{max} - \beta_{min}}{t - \left( -b \right)} \right) P_{v}
          + \frac{\beta_{min} t - \beta_{max} \left( -b \right)}{t - \left( -b \right)}
```

We need an affine map {math}`\phi_{d} : \mathbb{R} \rightarrow \mathbb{R}` such that

```{math}
:label: eq_phi_d_orth
P_{ndc,d} = \phi_{d}\left( P_{d} \right) = A P_{d} + B
```

with constraints {math}`\phi_{d}\left( n \right) = \gamma_{min}` and {math}`\phi_{d}\left( f \right) = \gamma_{max}`.

Using these constraints, we have

```{math}
:label: constraints_orth_z
\gamma_{max} = A f + B \\
\gamma_{min} = A n + B \\
```

Subtracting {math}`\gamma_{max}` from {math}`\gamma_{min}` in {ref}`constraints_orth_z`

```{math}
\gamma_{max} - \gamma_{min} = \left( A f + B \right) - \left( A n + B \right) = A \left( f - n \right)
```

and solving for {math}`A`

```{math}
:label: constant_orth_z_a
A = \frac{\gamma_{max} - \gamma_{min}}{f - n}.
```

Substituting the expression for {math}`A` back into the constraint for {math}`\gamma_{max}` we have

```{math}
\gamma_{max} = \left( \frac{\gamma_{max} - \gamma_{min}}{f - n} \right) f + B
```

Solving for {math}`B`

```{math}
B &= \gamma_{max} - \left( \frac{\gamma_{max} - \gamma_{min}}{f - n} \right) f \\
  &= \frac{\gamma_{max} \left( f - n \right) - \left( \gamma_{max} - \gamma_{min} \right) f}{f - n} \\
  &= \frac{\gamma_{max} f - \gamma_{max} n - \gamma_{max} f + \gamma_{min} f}{f - n} \\
  &= \frac{\gamma_{min} f - \gamma_{max} n}{f - n}
```

which gives us

```{math}
:label: constant_orth_z_b
B = \frac{\gamma_{min} f - \gamma_{max} n}{f - n}
```

which is the constant for {math}`\phi_{d}`. Substituting the constants {ref}`constant_orth_z_a` and {ref}`constant_orth_z_b`
back into the definition of {math}`\phi_{d}` in equation {ref}`eq_phi_d_orth`

```{math}
P_{ndc,d} = \phi_{d}\left( P_{d} \right)
          = \left( \frac{\gamma_{max} - \gamma_{min}}{f - n} \right) P_{d} + \frac{\gamma_{min} f - \gamma_{max} n}{f - n}
```

Since we do not apply depth normalization in orthographic projections, and the view space {math}`w`-component is {math}`1`

```{math}
P_{ndc,w} = P_{w} = 1
```

Finally, since we do not perform depth normalization with orthographic projections, the clip space 
coordinates are identical to the canonical view volume ones. That is {math}`P_{clip,h} = P_{ndc,h}`, 
{math}`P_{clip,v} = P_{ndc,v}`, {math}`P_{clip,d} = P_{ndc,d}`, and {math}`P_{clip,w} = P_{ndc,w}`. 
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
\end{bmatrix}
= 
M^{C}_{orth}
\begin{bmatrix} 
    P_{h} \\ 
    P_{v} \\ 
    P_{d} \\ 
    P_{w} \\
\end{bmatrix}
.
```

This yields the second major result, the canonical orthographic projection matrix.

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

## The Canonical Perspective Matrix

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


## The Canonical Symmetric Vertical Field Of View Perspective Matrix

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

which is the formula we want. This is the third major result, the canonical symmetric vertical field of view perspective
projection matrix.

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
