# A General Derivation Of Perspective And Orthographic Projection Matrices

## Projection Matrix Specification

The projection transformations are parametrized by two set of parameters: the viewing frustum bounds
{math}`l`, {math}`r`, {math}`b`, {math}`t`, {math}`n`, {math}`f` where {math}`l > 0`, {math}`r > 0`, 
{math}`b > 0`, {math}`t > 0`, and {math}`f > n > 0`; the canonical view volume parametrized by
{math}`[\alpha_{min}, \alpha_{max}] \times [\beta_{min}, \beta_{max}] \times [\gamma_{min}, \gamma_{max}]`.
where {math}`\alpha_{max} > \alpha_{min}`, {math}`\beta_{max} > \beta_{min}`, and {math}`\gamma_{max} > \gamma_{min}`.

To understand what the projection matrices do, we must understand the spaces that we map between 
at each step in the pipeline leading from the view space to the canonical view volume.

The **view space** is the Euclidean space {math}`(\mathbb{E}^{3}, (O_{V}, B_{V}))` with 
the following properties:

* The underlying vector space is {math}`\mathbb{R}^{3}`.
* The **origin** of the orthonormal frame is the point {math}`O_{V} \in \mathbb{E}^{3}`.
* The **basis** of the orthonormal frame is 
  {math}`B_{V} = (\mathbf{\hat{u_{h}}}, \mathbf{\hat{u_{v}}}, \mathbf{\hat{u_{d}}})` where
  the basis vector {math}`\mathbf{\hat{u_{h}}}` points to the right, the basis vector {math}`\mathbf{\hat{u_{v}}}`
  points up, and the basis vector {math}`\mathbf{\hat{u_{d}}}` points into the view volume.
* The orthonormal frame {math}`(O_{V}, B_{V})` has a left-handed orientation.

The **projected space** is the Euclidean space 
{math}`(\mathbb{E}^{3}, (O_{proj}, B_{proj}))` with the following properties:

* The underlying vector space is {math}`\mathbb{R}^{3}`.
* The **origin** of the orthonormal frame is the point {math}`O_{proj} = O_{V} \in \mathbb{E}^{3}`.
* The **basis** of the orthonormal frame is
  {math}`B_{proj} = (\mathbf{\hat{u_{h}}}, \mathbf{\hat{u_{v}}}, \mathbf{\hat{u_{d}}})` where
  the basis vector {math}`\mathbf{\hat{u_{h}}}` points to the right, the basis vector {math}`\mathbf{\hat{u_{v}}}`
  points up, and the basis vector {math}`\mathbf{\hat{u_{d}}}` points into the view volume.
* The orthonormal frame {math}`(O_{proj}, B_{proj})` has a left-handed orientation.
* The viewing volume is parametrized by {math}`[-l, r] \times [-b, t] \times [n, f]`.

The **clip space** is the Euclidean space {math}`(\mathbb{E}^{3}, (O_{clip}, B_{clip}))` with
the following properties:

* The underlying vector space is {math}`\mathbb{R}^{3}`.
* The **origin** of the orthonormal frame is the point {math}`O_{clip} = O_{V} \in \mathbb{E}^{3}`.
* The **basis** of the orthonormal frame is
  {math}`B_{clip} = (\mathbf{\hat{u_{h}}}, \mathbf{\hat{u_{v}}}, \mathbf{\hat{u_{d}}})` where
  the basis vector {math}`\mathbf{\hat{u_{h}}}` points to the right, the basis vector {math}`\mathbf{\hat{u_{v}}}`
  points up, and the basis vector {math}`\mathbf{\hat{u_{d}}}` points into the view volume.
* The orthonormal frame {math}`(O_{clip}, B_{clip})` has a left-handed orientation.

The **canonical view volume** is a subset of the Euclidean space {math}`(\mathbb{E}^{3}, (O_{cvv}, B_{cvv}))` 
with the following properties:

* The underlying vector space is {math}`\mathbb{R}^{3}`.
* The **origin** of the orthonormal frame is the point {math}`O_{cvv} = O_{V} \in \mathbb{E}^{3}`.
* The **basis** of the orthonormal frame is
  {math}`B_{cvv} = (\mathbf{\hat{u_{h}}}, \mathbf{\hat{u_{v}}}, \mathbf{\hat{u_{d}}})` where
  The basis vector {math}`\mathbf{\hat{u_{h}}}` points to the right, the basis vector {math}`\mathbf{\hat{u_{v}}}`
  points up, and the basis vector {math}`\mathbf{\hat{u_{d}}}` points into the view volume.
* The orthonormal frame {math}`(O_{cvv}, B_{cvv})` has a left-handed orientation.
* The canonical viewing volume is parametrized by 
  {math}`[\alpha_{min}, \alpha_{max}] \times [\beta_{min}, \beta_{max}] \times [\gamma_{min}, \gamma_{max}]`.

In particular, each geometric space above has the same coordinate system and orientation, but 
slightly different properties in terms of the shape of the viewing volume. It is not strictly required
that each space have the same orthonormal frame, but using the same coordinate system in each step
makes it easier to see what is going on in the graphics pipeline, and since we are going to derive each
transformation between the spaces above, one can always transform any one of them with a combination of
orthogonal transformations and changes of orientation to get the desired ones.

The points {math}`L \in \mathbb{E}^{3}`, {math}`R \in \mathbb{E}^{3}` {math}`B \in \mathbb{E}^{3}`, 
{math}`T \in \mathbb{E}^{3}`, {math}`N \in \mathbb{E}^{3}`, and {math}`F \in \mathbb{E}^{3}` correspond to 
points along the edge of the viewport on the near plane of the viewing frustum. The point {math}`L` is 
the point where the near plane, left hand plane, and depth-horizontal-plane intersect each other. The point
{math}`R` is the point where the near plane, right plane, ans depth-horizontal plane intersect each other.
The point {math}`B` is the point where the near plane, bottom plane, and horizontal-vertical plane intersect
each other. The point {math}`T` is the point where the near plane, top plane, and horizontal-vertical plane 
intersect each other. The point {math}`N` is the point along the depth axis inside the near plane. The 
point {math}`F` is the point along the depth axis inside the far plane. More precisely, in the orthonormal
frame {math}`(O_{V}, (\mathbf{\hat{u_{h}}}, \mathbf{\hat{u_{v}}}, \mathbf{\hat{u_{d}}}))`, the points are given
by

```{math}
L &= O_{V} + n \mathbf{\hat{u_{d}}} + l \left( -\mathbf{\hat{u_{h}}} \right) = O_{V} + n \mathbf{\hat{u_{d}}} - l \mathbf{\hat{u_{h}}} \\
R &= O_{V} + n \mathbf{\hat{u_{d}}} + r \mathbf{\hat{u_{h}}} \\
B &= O_{V} + n \mathbf{\hat{u_{d}}} + b \left( -\mathbf{\hat{u_{v}}} \right) = O_{V} + n \mathbf{\hat{u_{d}}} - b \mathbf{\hat{u_{v}}} \\
T &= O_{V} + n \mathbf{\hat{u_{d}}} + t \mathbf{\hat{u_{v}}} \\
N &= O_{V} + n \mathbf{\hat{u_{d}}} \\
F &= O_{V} + f \mathbf{\hat{u_{d}}} \\
```

In the coordinate system of the local view space frame with origin {math}`O_{V}`, the points are represented by

```{math}
\mathbf{l} \equiv L - O_{V} &= n \mathbf{\hat{u_{d}}} + l \left( -\mathbf{\hat{u_{h}}} \right) = n \mathbf{\hat{u_{d}}} - l \mathbf{\hat{u_{h}}} \\
\mathbf{r} \equiv R - O_{V} &= n \mathbf{\hat{u_{d}}} + r \mathbf{\hat{u_{h}}} \\
\mathbf{b} \equiv B - O_{V} &= n \mathbf{\hat{u_{d}}} + b \left( -\mathbf{\hat{u_{v}}} \right) = n \mathbf{\hat{u_{d}}} - b \mathbf{\hat{u_{v}}} \\
\mathbf{t} \equiv T - O_{V} &= n \mathbf{\hat{u_{d}}} + t \mathbf{\hat{u_{v}}} \\
\mathbf{n} \equiv N - O_{V} &= n \mathbf{\hat{u_{d}}} \\
\mathbf{f} \equiv F - O_{V} &= f \mathbf{\hat{u_{d}}} \\
```

## The Canonical Perspective Projection Matrix

The orthonormal frame {math}`\left(O_{V}, \left( \mathbf{\hat{u_{h}}}, \mathbf{\hat{u_{v}}}, \mathbf{\hat{u_{d}}} \right) \right)` 
on {math}`\mathbb{E}^{3}` induces a coordinate chart as follows. A point {math}`\tilde{P} \in \mathbb{E}^{3}` is written as

```{math}
\tilde{P} = O_{V} + P_{h} \mathbf{\hat{u_{h}}} + P_{v} \mathbf{\hat{u_{v}}} + P_{d} \mathbf{\hat{u_{d}}}
```

and the orthonormal frame {math}`\left(O_{V}, \left( \mathbf{\hat{u_{h}}}, \mathbf{\hat{u_{v}}}, \mathbf{\hat{u_{d}}} \right) \right)` 
defines a coordinate chart {math}`\psi : \mathbb{E}^{3} \rightarrow \mathbb{R}^{3}` by 
{math}`\psi( \tilde{P} ) = \tilde{P} - O_{V}`. In particular,

```{math}
P = \psi(\tilde{P}) \equiv \tilde{P} - O_{V} = P_{h} \mathbf{\hat{u_{h}}} + P_{v} \mathbf{\hat{u_{v}}} + P_{d} \mathbf{\hat{u_{d}}}
```

is the representation of {math}`\tilde{P}` in {math}`\mathbb{R}^{3}`. Also, the coordinate map {math}`\psi` maps the 
origin {math}`O_{V}` of the view space frame in {math}`\mathbb{E}^{3}` to {math}`\mathbf{0}`: 
{math}`\psi(O_{V}) = O_{V} - O_{V} = \mathbf{0}`. This shows that the view space frame origin in {math}`\mathbb{E}^{3}`
indeed maps to the vector space origin {math}`\mathbf{0}` in {math}`\mathbb{R}^{3}`.

Using similar triangles for the horizontal component, we have

```{math}
\frac{P_{h}}{P_{d}} = \frac{P \cdot \mathbf{\hat{u_{h}}}}{P \cdot \mathbf{\hat{u_{d}}}} 
                    = \frac{P_{proj} \cdot \mathbf{\hat{u_{h}}}}{n} 
                    = \frac{P_{proj,h}}{n}
```

which yields

```{math}
P_{proj,h} = n P_{h} \left( \frac{1}{P_{d}} \right).
```

Using similar triangles for the vertical component, we have

```{math}
\frac{P_{v}}{P_{d}} = \frac{P \cdot \mathbf{\hat{u_{v}}}}{P \cdot \mathbf{\hat{u_{d}}}} = \frac{P_{proj} \cdot \mathbf{\hat{u_{v}}}}{n} = \frac{P_{proj,v}}{n}
```

which yields

```{math}
P_{proj,v} = n P_{v} \left( \frac{1}{P_{d}} \right).
```

Now we must map from projected coordinates to normalized device coordinates. We deduce the 
transformation from projected space to clip space indirectly by calculating the canonical view 
volume components first. This is necessary since the mapping from projected coordinates to clip 
space depends on the parametrization of the canonical view volume chosen. The resulting transformation
is an orthographic transformation that maps the projected view volume {math}`[-l, r] \times [-b, t] \times [n, f]`
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
  &= \frac{\alpha_{min} r + \alpha_{max} l}{r - \left( -l \right)}
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
  &= \frac{\beta_{min} t + \beta_{max} b}{t - \left( -b \right)}
```

which gives us

```{math}
:label: constant4
B = \frac{\beta_{min} t + \beta_{max} b}{t - \left( -b \right)}
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

After depth normalization, the {math}`w` component of a homogeneous vector is {math}`1`, thus

```{math}
:label: eq_ndc_w
P_{ndc,w} = 1 = P_{d} \left( \frac{1}{P_{d}} \right).
```

This completes the derivation of the components for mapping a point {math}`\tilde{P}` from view space
to normalized device coordinates. Using the results for mapping to normalized device coordinates,
it is straightforward to infer the clip space components for the point {math}`\tilde{P}`.

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
    \end{bmatrix}
```

## The Canonical Orthographic Projection Matrix

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
  &= \frac{\alpha_{min} r + \alpha_{max} l}{r - \left( -l \right)}
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
  &= \frac{ \beta_{max} \left( t + b \right) + \left( \beta_{max} b - \beta_{min} t \right) }{t - \left( -b \right)} \\
  &= \frac{\beta_{max} t + \beta_{max} b - \beta_{max} t + \beta_{min} t}{t - \left( -b \right)} \\
  &= \frac{\beta_{min} t - \beta_{max} \left( -b \right)}{t - \left( -b \right)}
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
    \frac{\alpha_{max} - \alpha_{min}}{r - \left( -l \right)} & 0 & 0 & \frac{\alpha_{min}r - \alpha_{max} \left(-l \right) }{r - \left( -l \right)} \\
    0 & \frac{\beta_{max} - \beta_{min}}{t - \left( -b \right)} & 0 & \frac{\beta_{min}t - \beta_{max}\left( -b \right) }{t - \left(-b \right)} \\
    0 & 0 & \frac{\gamma_{max} - \gamma_{min} }{f - n} & \frac{\gamma_{min}f - \gamma_{max}n}{f - n} \\
    0 & 0 & 0 & 1
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
    & \frac{\alpha_{min}r - \alpha_{max} \left(-l \right) }{r - \left( -l \right)} \\

    0 
    & \frac{\beta_{max} - \beta_{min}}{t - \left( -b \right)} 
    & 0 
    & \frac{\beta_{min}t - \beta_{max}\left( -b \right) }{t - \left(-b \right)} \\
    
    0 
    & 0 
    & \frac{\gamma_{max} - \gamma_{min} }{f - n} 
    & \frac{\gamma_{min}f - \gamma_{max}n}{f - n} \\
    
    0 
    & 0 
    & 0 
    & 1
\end{bmatrix}
```

## The Canonical Perspective Matrix

Now that we have the canonical perspective projection matrix and the canonical orthographic projection matrix,
we can work out the canonical perspective matrix. The canonical perspective matrix maps from view space to projected
space. Since the perspective projection transformation consists of a projection transformation multiplied by an orthographic
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
a projection transformation and an orthographic transformation

```{math}
M^{C}_{per} = M^{C}_{orth} P^{C}.
```


## The Canonical Symmetric Vertical Field Of View Perspective Matrix

In the symmetric case, {math}`r = l` and {math}`t = b`. The aspect ratio {math}`aspect` is
given by 
```{math}
aspect \equiv \frac{width}{height} 
    = \frac{r - \left( -l \right)}{t - \left( -b \right)}
    = \frac{r + l}{t + b}
    = \frac{r + r}{t + t}
    = \frac{2 r}{2 t}
    = \frac{r}{t}
```
which implies {math}`r = aspect \cdot t`. Since the viewing frustum is symmetric, the tangent 
of {math}`\theta_{vfov} / 2` is {math}`\tan\left( \theta_{vfov} / 2 \right) = t / n`. These 
facts imply that
```{math}
:label: eq_symmetric_tangent_vfov
t = b = n \tan\left( \frac{\theta_{vfov}}{2} \right) \\
r = l = aspect \cdot t = aspect \cdot n \tan\left( \frac{\theta_{vfov}}{2} \right)
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
    &= \left( \frac{\alpha_{max} - \alpha_{min}}{2} \right) \frac{n}{aspect \cdot n \tan\left( \frac{\theta_{vfov}}{2} \right)} \\
    &= \left( \frac{\alpha_{max} - \alpha_{min}}{2} \right) \frac{1}{aspect \cdot \tan\left( \frac{\theta_{vfov}}{2} \right)} \\

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
        \left( \frac{\alpha_{max} - \alpha_{min}}{2} \right) \frac{1}{aspect \cdot \tan\left( \frac{\theta_{vfov}}{2} \right)} 
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
        \left( \frac{\alpha_{max} - \alpha_{min}}{2} \right) \frac{1}{aspect \cdot \tan\left( \frac{\theta_{vfov}}{2} \right)} 
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

### The Canonical Perspective Projection Matrix With OpenGL's Normalized Device Coordinates

The canonical view volume for for OpenGL in normalized device coordinates is parametrized by 
{math}`[-1, 1] \times [-1, 1] \times [-1, 1]`. The canonical perpsective projection matrix for 
OpenGL is given by

```{math}
M^{C, OpenGL}_{per} =
\begin{bmatrix}
\frac{ 2 n }{r - \left( -l \right)} & 0                                   & -\frac{r + \left( -l \right)}{r - \left( -l \right)} &  0                    \\
0                                   & \frac{ 2 n }{t - \left( -b \right)} & -\frac{t + \left( -b \right)}{t - \left( -b \right)} &  0                    \\
0                                   & 0                                   &  \frac{f + n}{f - n}                                 & -\frac{2 f n }{f - n} \\
0                                   & 0                                   &  1                                                   &  0                    \\
\end{bmatrix}
.
```

The canonical orthographic projection matrix for OpenGL is given by

```{math}
M^{C, OpenGL}_{orth} = 
\begin{bmatrix}
\frac{2}{r - \left( -l \right)} & 0                               & 0               & -\frac{r + \left( -l \right)}{r - \left( -l \right)} \\
0                               & \frac{2}{t - \left( -b \right)} & 0               & -\frac{t + \left( -b \right)}{t - \left( -b \right)} \\
0                               & 0                               & \frac{2}{f - n} & -\frac{f + n}{f - n}                                 \\
0                               & 0                               & 0               &  1                                                   \\
\end{bmatrix}
.
```

The canonical symmetric vertical field of view perspective projection matrix for OpenGL is given by

```{math}
M^{C,OpenGL}_{per,vfov} = 
    \begin{bmatrix}
        \frac{1}{aspect \cdot \tan\left( \frac{\theta_{vfov}}{2} \right)} 
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

### Right-Handed View Space

```{math}
Q^{OpenGL}_{lh} = Q^{OpenGL}_{rh} = I
```
where {math}`I` is the identity matrix.

Recall that

Perspective projection


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
        0                                   & 0                                   &  1                                                    &  0
        \end{bmatrix}
        \begin{bmatrix}
            1 & 0 &  0 & 0 \\
            0 & 1 &  0 & 0 \\
            0 & 0 & -1 & 0 \\
            0 & 0 &  0 & 1
       \end{bmatrix} \\
    &= \begin{bmatrix}
        \frac{ 2 n }{r - \left( -l \right)} & 0                                   &  \frac{r + \left( -l \right)}{r - \left( -l \right)} &  0                    \\
        0                                   & \frac{ 2 n }{t - \left( -b \right)} &  \frac{t + \left( -b \right)}{t - \left( -b \right)} &  0                    \\
        0                                   & 0                                   & -\frac{f + n}{f - n}                                 & -\frac{2 f n }{f - n} \\
        0                                   & 0                                   & -1                                                   &  0
        \end{bmatrix}
```

so

```{math}
M^{OpenGL}_{per,rh \rightarrow lh} = 
    \begin{bmatrix}
    \frac{ 2 n }{r - \left( -l \right)} & 0                                   &  \frac{r + \left( -l \right)}{r - \left( -l \right)} &  0                    \\
    0                                   & \frac{ 2 n }{t - \left( -b \right)} &  \frac{t + \left( -b \right)}{t - \left( -b \right)} &  0                    \\
    0                                   & 0                                   & -\frac{f + n}{f - n}                                 & -\frac{2 f n }{f - n} \\
    0                                   & 0                                   & -1                                                   &  0
    \end{bmatrix}.
```


Calculation for orthographic

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
        0                               & 0                               & 0               &  1
        \end{bmatrix}
        \begin{bmatrix}
            1 & 0 &  0 & 0 \\
            0 & 1 &  0 & 0 \\
            0 & 0 & -1 & 0 \\
            0 & 0 &  0 & 1
       \end{bmatrix} \\
    &= \begin{bmatrix}
        \frac{2}{r - \left( -l \right)} & 0                                   &  0                & -\frac{r + \left( -l \right)}{r - \left( -l \right)} \\
        0                                   & \frac{2}{t - \left( -b \right)} &  0                & -\frac{t + \left( -b \right)}{t - \left( -b \right)} \\
        0                                   & 0                               & -\frac{2}{f - n}  & -\frac{f + n}{f - n}                                 \\
        0                                   & 0                               & 0                 &  1
        \end{bmatrix}
```

so

```{math}
M^{OpenGL}_{orth,rh \rightarrow lh} = 
    \begin{bmatrix}
    \frac{2}{r - \left( -l \right)} & 0                                   &  0                & -\frac{r + \left( -l \right)}{r - \left( -l \right)} \\
    0                                   & \frac{2}{t - \left( -b \right)} &  0                & -\frac{t + \left( -b \right)}{t - \left( -b \right)} \\
    0                                   & 0                               & -\frac{2}{f - n}  & -\frac{f + n}{f - n}                                 \\
    0                                   & 0                               & 0                 &  1
    \end{bmatrix}.
```

Symmetric Vertical Field Of View Perspective Projection

```{math}
M^{OpenGL}_{per,vfov,rh \rightarrow lh} 
    &= \Omega_{lh \rightarrow lh} Q^{OpenGL}_{lh} M^{C,OpenGL}_{per,vfov} \Omega_{rh \rightarrow lh} Q^{OpenGL}_{rh} \\
    &= \left( \Omega_{lh \rightarrow lh} Q^{OpenGL}_{lh} \right) M^{C,OpenGL}_{per,vfov} \left( \Omega_{rh \rightarrow lh} Q^{OpenGL}_{rh} \right) \\
    &= \left( \Omega_{lh \rightarrow lh} I \right) M^{C,OpenGL}_{per,vfov} \left( \Omega_{rh \rightarrow lh} I \right) \\
    &= \Omega_{lh \rightarrow lh} M^{C,OpenGL}_{per,vfov} \Omega_{rh \rightarrow lh} \\
    &= I M^{C,OpenGL}_{per,vfov} \Omega_{rh \rightarrow lh} \\
    &= M^{C,OpenGL}_{per,vfov} \Omega_{rh \rightarrow lh} \\
    &=  \begin{bmatrix}
        \frac{1}{aspect \cdot \tan\left( \frac{\theta_{vfov}}{2} \right)} 
        & 0 
        & 0
        & 0 \\

        0 
        & \frac{1}{\tan\left( \frac{\theta_{vfov}}{2} \right)}
        & 0
        & 0 \\

        0 
        &  0 
        & \frac{f + n}{f - n}
        & -\frac{ 2 f n }{f - n} \\
    
        0 
        & 0 
        & 1 
        & 0
        \end{bmatrix}
        \begin{bmatrix}
            1 & 0 &  0 & 0 \\
            0 & 1 &  0 & 0 \\
            0 & 0 & -1 & 0 \\
            0 & 0 &  0 & 1
        \end{bmatrix} \\
    &=  \begin{bmatrix}
        \frac{1}{aspect \cdot \tan\left( \frac{\theta_{vfov}}{2} \right)} 
        & 0 
        & 0
        & 0 \\

        0 
        & \frac{1}{\tan\left( \frac{\theta_{vfov}}{2} \right)}
        & 0
        & 0 \\

        0 
        &  0 
        & -\frac{f + n}{f - n}
        & -\frac{ 2 f n }{f - n} \\
    
        0 
        &  0 
        & -1 
        &  0
        \end{bmatrix}
```

so 

```{math}
M^{OpenGL}_{per,vfov,rh \rightarrow lh} = 
    \begin{bmatrix}
        \frac{1}{aspect \cdot \tan\left( \frac{\theta_{vfov}}{2} \right)} 
        & 0 
        & 0
        & 0 \\

        0 
        & \frac{1}{\tan\left( \frac{\theta_{vfov}}{2} \right)}
        & 0
        & 0 \\

        0 
        &  0 
        & -\frac{f + n}{f - n}
        & -\frac{ 2 f n }{f - n} \\
    
        0 
        &  0 
        & -1 
        &  0
    \end{bmatrix}
```

### Left-Handed View Space

Perspective Projection

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
        0                                   & 0                                   &  1                                                    &  0
        \end{bmatrix}
```

so

```{math}
M^{OpenGL}_{per,lh \rightarrow lh} =
    \begin{bmatrix}
        \frac{ 2 n }{r - \left( -l \right)} & 0                                   & -\frac{r + \left( -l \right)}{r - \left( -l \right)}  &  0                    \\
        0                                   & \frac{ 2 n }{t - \left( -b \right)} & -\frac{t + \left( -b \right)}{t - \left( -b \right)}  &  0                    \\
        0                                   & 0                                   &  \frac{f + n}{f - n}                                  & -\frac{2 f n }{f - n} \\
        0                                   & 0                                   &  1                                                    &  0
    \end{bmatrix}
```

Orthographic projection

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
        0                               & 0                               & 0               &  1
        \end{bmatrix}
```

so 

```{math}
M^{OpenGL}_{orth,lh \rightarrow lh} = 
    \begin{bmatrix}
        \frac{2}{r - \left( -l \right)} & 0                               & 0               & -\frac{r + \left( -l \right)}{r - \left( -l \right)} \\
        0                               & \frac{2}{t - \left( -b \right)} & 0               & -\frac{t + \left( -b \right)}{t - \left( -b \right)} \\
        0                               & 0                               & \frac{2}{f - n} & -\frac{f + n}{f - n}                                 \\
        0                               & 0                               & 0               &  1
    \end{bmatrix}
```

Symmetric Vertical Field Of View Perspective Projection

```{math}
M^{OpenGL}_{per,vfov,lh \rightarrow lh} 
    &= \Omega_{lh \rightarrow lh} Q^{OpenGL}_{lh} M^{C,OpenGL}_{per,vfov} \Omega_{lh \rightarrow lh} Q^{OpenGL}_{lh} \\
    &= \left( \Omega_{lh \rightarrow lh} Q^{OpenGL}_{lh} \right) M^{C,OpenGL}_{per,vfov} \left( \Omega_{lh \rightarrow lh} Q^{OpenGL}_{lh} \right) \\
    &= \left( \Omega_{lh \rightarrow lh} I \right) M^{C,OpenGL}_{per,vfov} \left( \Omega_{lh \rightarrow lh} I \right) \\
    &= \Omega_{lh \rightarrow lh} M^{C,OpenGL}_{per,vfov} \Omega_{lh \rightarrow lh} \\
    &= I M^{C,OpenGL}_{per,vfov} I \\
    &= M^{C,OpenGL}_{per,vfov} \\
    &=  \begin{bmatrix}
        \frac{1}{aspect \cdot \tan\left( \frac{\theta_{vfov}}{2} \right)} 
        & 0 
        & 0
        & 0 \\

        0 
        & \frac{1}{\tan\left( \frac{\theta_{vfov}}{2} \right)}
        & 0
        & 0 \\

        0 
        &  0 
        & \frac{f + n}{f - n}
        & -\frac{ 2 f n }{f - n} \\
    
        0 
        & 0 
        & 1 
        & 0
        \end{bmatrix}
```

so

```{math}
M^{OpenGL}_{per,vfov,lh \rightarrow lh} =
    \begin{bmatrix}
        \frac{1}{aspect \cdot \tan\left( \frac{\theta_{vfov}}{2} \right)} 
        & 0 
        & 0
        & 0 \\

        0 
        & \frac{1}{\tan\left( \frac{\theta_{vfov}}{2} \right)}
        & 0
        & 0 \\

        0 
        &  0 
        & \frac{f + n}{f - n}
        & -\frac{ 2 f n }{f - n} \\
    
        0 
        & 0 
        & 1 
        & 0
    \end{bmatrix}
```

## DirectX

### Canonical Matrix

The canonical view volume for for DirectX in normalized device coordinates is parametrized by 
{math}`[-1, 1] \times [-1, 1] \times [0, 1]`. The canonical perpsective projection matrix for 
DirectX is given by

```{math}
M^{C, DirectX}_{per} =
\begin{bmatrix}
\frac{ 2 n }{r - \left( -l \right)} & 0                                   & -\frac{r + \left( -l \right)}{r - \left( -l \right)} &  0                  \\
0                                   & \frac{ 2 n }{t - \left( -b \right)} & -\frac{t + \left( -b \right)}{t - \left( -b \right)} &  0                  \\
0                                   & 0                                   &  \frac{f}{f - n}                                     & -\frac{f n }{f - n} \\
0                                   & 0                                   &  1                                                   &  0
\end{bmatrix}
.
```

The canonical orthographic projection matrix for DirectX is given by

```{math}
M^{C, DirectX}_{orth} = 
\begin{bmatrix}
\frac{2}{r - \left( -l \right)} & 0                               & 0               & -\frac{r + \left( -l \right)}{r - \left( -l \right)} \\
0                               & \frac{2}{t - \left( -b \right)} & 0               & -\frac{t + \left( -b \right)}{t - \left( -b \right)} \\
0                               & 0                               & \frac{1}{f - n} & -\frac{n}{f - n}                                     \\
0                               & 0                               & 0               &  1
\end{bmatrix}
.
```

The canonical symmetric vertical field of view perspective projection matrix for DirectX is given by

```{math}
M^{C,DirectX}_{per,vfov} = 
    \begin{bmatrix}
        \frac{1}{aspect \cdot \tan\left( \frac{\theta_{vfov}}{2} \right)} 
        & 0 
        & 0
        & 0 \\

        0 
        & \frac{1}{\tan\left( \frac{\theta_{vfov}}{2} \right)}
        & 0
        & 0 \\

        0 
        &  0 
        &  \frac{f}{f - n}
        & -\frac{ f n }{f - n} \\
    
        0 
        & 0 
        & 1 
        & 0
    \end{bmatrix}
```

### Right-Handed View Space

```{math}
Q^{DirectX}_{lh} = Q^{DirectX}_{rh} = I
```
where {math}`I` is the identity matrix.

Recall that

Perspective projection

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

so

```{math}
M^{DirectX}_{per,rh \rightarrow lh} = 
    \begin{bmatrix}
    \frac{ 2 n }{r - \left( -l \right)} & 0                                   &  \frac{r + \left( -l \right)}{r - \left( -l \right)} &  0                   \\
    0                                   & \frac{ 2 n }{t - \left( -b \right)} &  \frac{t + \left( -b \right)}{t - \left( -b \right)} &  0                   \\
    0                                   & 0                                   & -\frac{f}{f - n}                                     & -\frac{ f n }{f - n} \\
    0                                   & 0                                   & -1                                                   &  0
    \end{bmatrix}.
```


Calculation for orthographic

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

so

```{math}
M^{DirectX}_{orth,rh \rightarrow lh} = 
    \begin{bmatrix}
    \frac{2}{r - \left( -l \right)} & 0                               &  0                & -\frac{r + \left( -l \right)}{r - \left( -l \right)} \\
    0                               & \frac{2}{t - \left( -b \right)} &  0                & -\frac{t + \left( -b \right)}{t - \left( -b \right)} \\
    0                               & 0                               & -\frac{1}{f - n}  & -\frac{n}{f - n}                                     \\
    0                               & 0                               &  0                &  1
    \end{bmatrix}.
```

Symmetric Vertical Field Of View Perspective Projection

```{math}
M^{DirectX}_{per,vfov,rh \rightarrow lh} 
    &= \Omega_{lh \rightarrow lh} Q^{DirectX}_{lh} M^{C,DirectX}_{per,vfov} \Omega_{rh \rightarrow lh} Q^{DirectX}_{rh} \\
    &= \left( \Omega_{lh \rightarrow lh} Q^{DirectX}_{lh} \right) M^{C,DirectX}_{per,vfov} \left( \Omega_{rh \rightarrow lh} Q^{DirectX}_{rh} \right) \\
    &= \left( \Omega_{lh \rightarrow lh} I \right) M^{C,DirectX}_{per,vfov} \left( \Omega_{rh \rightarrow lh} I \right) \\
    &= \Omega_{lh \rightarrow lh} M^{C,DirectX}_{per,vfov} \Omega_{rh \rightarrow lh} \\
    &= I M^{C,DirectX}_{per,vfov} \Omega_{rh \rightarrow lh} \\
    &= M^{C,DirectX}_{per,vfov} \Omega_{rh \rightarrow lh} \\
    &=  \begin{bmatrix}
        \frac{1}{aspect \cdot \tan\left( \frac{\theta_{vfov}}{2} \right)} 
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
        \begin{bmatrix}
            1 & 0 &  0 & 0 \\
            0 & 1 &  0 & 0 \\
            0 & 0 & -1 & 0 \\
            0 & 0 &  0 & 1
        \end{bmatrix} \\
    &=  \begin{bmatrix}
        \frac{1}{aspect \cdot \tan\left( \frac{\theta_{vfov}}{2} \right)} 
        & 0 
        & 0
        & 0 \\

        0 
        & \frac{1}{\tan\left( \frac{\theta_{vfov}}{2} \right)}
        & 0
        & 0 \\

        0 
        &  0 
        & -\frac{f}{f - n}
        & -\frac{ f n }{f - n} \\
    
        0 
        &  0 
        & -1 
        &  0
        \end{bmatrix}
```

so 

```{math}
M^{DirectX}_{per,vfov,rh \rightarrow lh} = 
    \begin{bmatrix}
        \frac{1}{aspect \cdot \tan\left( \frac{\theta_{vfov}}{2} \right)} 
        & 0 
        & 0
        & 0 \\

        0 
        & \frac{1}{\tan\left( \frac{\theta_{vfov}}{2} \right)}
        & 0
        & 0 \\

        0 
        &  0 
        & -\frac{f}{f - n}
        & -\frac{ f n }{f - n} \\
    
        0 
        &  0 
        & -1 
        &  0
    \end{bmatrix}
```

### Left-Handed View Space

Perspective projection

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
        0                                   & 0                                   &  1                                                   &  0
        \end{bmatrix}
```

so

```{math}
M^{DirectX}_{per,lh \rightarrow lh} = 
    \begin{bmatrix}
        \frac{ 2 n }{r - \left( -l \right)} & 0                                   & -\frac{r + \left( -l \right)}{r - \left( -l \right)} &  0                  \\
        0                                   & \frac{ 2 n }{t - \left( -b \right)} & -\frac{t + \left( -b \right)}{t - \left( -b \right)} &  0                  \\
        0                                   & 0                                   &  \frac{f}{f - n}                                     & -\frac{f n }{f - n} \\
        0                                   & 0                                   &  1                                                   &  0
    \end{bmatrix}.
```


Calculation for orthographic

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
        0                               & 0                               & 0               &  1
        \end{bmatrix}
```

so

```{math}
M^{DirectX}_{orth,lh \rightarrow lh} = 
    \begin{bmatrix}
        \frac{2}{r - \left( -l \right)} & 0                               & 0               & -\frac{r + \left( -l \right)}{r - \left( -l \right)} \\
        0                               & \frac{2}{t - \left( -b \right)} & 0               & -\frac{t + \left( -b \right)}{t - \left( -b \right)} \\
        0                               & 0                               & \frac{1}{f - n} & -\frac{n}{f - n}                                     \\
        0                               & 0                               & 0               &  1
    \end{bmatrix}.
```

Symmetric Vertical Field Of View Perspective Projection

```{math}
M^{DirectX}_{per,vfov,lh \rightarrow lh} 
    &= \Omega_{lh \rightarrow lh} Q^{DirectX}_{lh} M^{C,DirectX}_{per,vfov} \Omega_{lh \rightarrow lh} Q^{DirectX}_{lh} \\
    &= \left( \Omega_{lh \rightarrow lh} Q^{DirectX}_{lh} \right) M^{C,DirectX}_{per,vfov} \left( \Omega_{lh \rightarrow lh} Q^{DirectX}_{lh} \right) \\
    &= \left( \Omega_{lh \rightarrow lh} I \right) M^{C,DirectX}_{per,vfov} \left( \Omega_{lh \rightarrow lh} I \right) \\
    &= \Omega_{lh \rightarrow lh} M^{C,DirectX}_{per,vfov} \Omega_{lh \rightarrow lh} \\
    &= I M^{C,DirectX}_{per,vfov} I \\
    &= M^{C,DirectX}_{per,vfov} \\
    &=  \begin{bmatrix}
        \frac{1}{aspect \cdot \tan\left( \frac{\theta_{vfov}}{2} \right)} 
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

so 

```{math}
M^{DirectX}_{per,vfov,lh \rightarrow lh} = 
    \begin{bmatrix}
        \frac{1}{aspect \cdot \tan\left( \frac{\theta_{vfov}}{2} \right)} 
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

## Metal

### Canonical Matrix

The canonical view volume for for Metal in normalized device coordinates is parametrized by 
{math}`[-1, 1] \times [-1, 1] \times [0, 1]`. The canonical perpsective projection matrix for 
Metal is given by

```{math}
M^{C, Metal}_{per} =
\begin{bmatrix}
\frac{ 2 n }{r - \left( -l \right)} & 0                                   & -\frac{r + \left( -l \right)}{r - \left( -l \right)} &  0                  \\
0                                   & \frac{ 2 n }{t - \left( -b \right)} & -\frac{t + \left( -b \right)}{t - \left( -b \right)} &  0                  \\
0                                   & 0                                   &  \frac{f}{f - n}                                     & -\frac{f n }{f - n} \\
0                                   & 0                                   &  1                                                   &  0
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
0                               & 0                               & 0               &  1
\end{bmatrix}
.
```

The canonical symmetric vertical field of view perspective projection matrix for Metal is given by

```{math}
M^{C,Metal}_{per,vfov} = 
    \begin{bmatrix}
        \frac{1}{aspect \cdot \tan\left( \frac{\theta_{vfov}}{2} \right)} 
        & 0 
        & 0
        & 0 \\

        0 
        & \frac{1}{\tan\left( \frac{\theta_{vfov}}{2} \right)}
        & 0
        & 0 \\

        0 
        &  0 
        &  \frac{f}{f - n}
        & -\frac{ f n }{f - n} \\
    
        0 
        & 0 
        & 1 
        & 0
    \end{bmatrix}
```

### Right-Handed View Space

```{math}
Q^{Metal}_{lh} = Q^{Metal}_{rh} = I
```
where {math}`I` is the identity matrix.

Recall that

Perspective projection

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

so

```{math}
M^{Metal}_{per,rh \rightarrow lh} = 
    \begin{bmatrix}
    \frac{ 2 n }{r - \left( -l \right)} & 0                                   &  \frac{r + \left( -l \right)}{r - \left( -l \right)} &  0                   \\
    0                                   & \frac{ 2 n }{t - \left( -b \right)} &  \frac{t + \left( -b \right)}{t - \left( -b \right)} &  0                   \\
    0                                   & 0                                   & -\frac{f}{f - n}                                     & -\frac{ f n }{f - n} \\
    0                                   & 0                                   & -1                                                   &  0
    \end{bmatrix}.
```


Calculation for orthographic

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

so

```{math}
M^{Metal}_{orth,rh \rightarrow lh} = 
    \begin{bmatrix}
    \frac{2}{r - \left( -l \right)} & 0                               &  0                & -\frac{r + \left( -l \right)}{r - \left( -l \right)} \\
    0                               & \frac{2}{t - \left( -b \right)} &  0                & -\frac{t + \left( -b \right)}{t - \left( -b \right)} \\
    0                               & 0                               & -\frac{1}{f - n}  & -\frac{n}{f - n}                                     \\
    0                               & 0                               &  0                &  1
    \end{bmatrix}.
```

Symmetric Vertical Field Of View Perspective Projection

```{math}
M^{Metal}_{per,vfov,rh \rightarrow lh} 
    &= \Omega_{lh \rightarrow lh} Q^{Metal}_{lh} M^{C,Metal}_{per,vfov} \Omega_{rh \rightarrow lh} Q^{Metal}_{rh} \\
    &= \left( \Omega_{lh \rightarrow lh} Q^{Metal}_{lh} \right) M^{C,Metal}_{per,vfov} \left( \Omega_{rh \rightarrow lh} Q^{Metal}_{rh} \right) \\
    &= \left( \Omega_{lh \rightarrow lh} I \right) M^{C,Metal}_{per,vfov} \left( \Omega_{rh \rightarrow lh} I \right) \\
    &= \Omega_{lh \rightarrow lh} M^{C,Metal}_{per,vfov} \Omega_{rh \rightarrow lh} \\
    &= I M^{C,Metal}_{per,vfov} \Omega_{rh \rightarrow lh} \\
    &= M^{C,Metal}_{per,vfov} \Omega_{rh \rightarrow lh} \\
    &=  \begin{bmatrix}
        \frac{1}{aspect \cdot \tan\left( \frac{\theta_{vfov}}{2} \right)} 
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
        \begin{bmatrix}
            1 & 0 &  0 & 0 \\
            0 & 1 &  0 & 0 \\
            0 & 0 & -1 & 0 \\
            0 & 0 &  0 & 1
        \end{bmatrix} \\
    &=  \begin{bmatrix}
        \frac{1}{aspect \cdot \tan\left( \frac{\theta_{vfov}}{2} \right)} 
        & 0 
        & 0
        & 0 \\

        0 
        & \frac{1}{\tan\left( \frac{\theta_{vfov}}{2} \right)}
        & 0
        & 0 \\

        0 
        &  0 
        & -\frac{f}{f - n}
        & -\frac{ f n }{f - n} \\
    
        0 
        &  0 
        & -1 
        &  0
        \end{bmatrix}
```

so 

```{math}
M^{Metal}_{per,vfov,rh \rightarrow lh} = 
    \begin{bmatrix}
        \frac{1}{aspect \cdot \tan\left( \frac{\theta_{vfov}}{2} \right)} 
        & 0 
        & 0
        & 0 \\

        0 
        & \frac{1}{\tan\left( \frac{\theta_{vfov}}{2} \right)}
        & 0
        & 0 \\

        0 
        &  0 
        & -\frac{f}{f - n}
        & -\frac{ f n }{f - n} \\
    
        0 
        &  0 
        & -1 
        &  0
    \end{bmatrix}
```

### Left-Handed View Space

Perspective projection

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

so

```{math}
M^{Metal}_{per,lh \rightarrow lh} = 
    \begin{bmatrix}
        \frac{ 2 n }{r - \left( -l \right)} & 0                                   & -\frac{r + \left( -l \right)}{r - \left( -l \right)} &  0                  \\
        0                                   & \frac{ 2 n }{t - \left( -b \right)} & -\frac{t + \left( -b \right)}{t - \left( -b \right)} &  0                  \\
        0                                   & 0                                   &  \frac{f}{f - n}                                     & -\frac{f n }{f - n} \\
        0                                   & 0                                   &  1                                                   &  0
    \end{bmatrix}.
```


Calculation for orthographic

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

so

```{math}
M^{Metal}_{orth,lh \rightarrow lh} = 
    \begin{bmatrix}
        \frac{2}{r - \left( -l \right)} & 0                               & 0               & -\frac{r + \left( -l \right)}{r - \left( -l \right)} \\
        0                               & \frac{2}{t - \left( -b \right)} & 0               & -\frac{t + \left( -b \right)}{t - \left( -b \right)} \\
        0                               & 0                               & \frac{1}{f - n} & -\frac{n}{f - n}                                     \\
        0                               & 0                               & 0               &  1
    \end{bmatrix}.
```

Symmetric Vertical Field Of View Perspective Projection

```{math}
M^{Metal}_{per,vfov,lh \rightarrow lh} 
    &= \Omega_{lh \rightarrow lh} Q^{Metal}_{lh} M^{C,Metal}_{per,vfov} \Omega_{lh \rightarrow lh} Q^{Metal}_{lh} \\
    &= \left( \Omega_{lh \rightarrow lh} Q^{Metal}_{lh} \right) M^{C,Metal}_{per,vfov} \left( \Omega_{lh \rightarrow lh} Q^{Metal}_{lh} \right) \\
    &= \left( \Omega_{lh \rightarrow lh} I \right) M^{C,Metal}_{per,vfov} \left( \Omega_{lh \rightarrow lh} I \right) \\
    &= \Omega_{lh \rightarrow lh} M^{C,Metal}_{per,vfov} \Omega_{lh \rightarrow lh} \\
    &= I M^{C,Metal}_{per,vfov} I \\
    &= M^{C,Metal}_{per,vfov} \\
    &=  \begin{bmatrix}
        \frac{1}{aspect \cdot \tan\left( \frac{\theta_{vfov}}{2} \right)} 
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

so 

```{math}
M^{Metal}_{per,vfov,lh \rightarrow lh} = 
    \begin{bmatrix}
        \frac{1}{aspect \cdot \tan\left( \frac{\theta_{vfov}}{2} \right)} 
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

## Vulkan (Vulkan Coordinates)

### Canonical Matrix

The canonical view volume for for Vulkan in normalized device coordinates is parametrized by 
{math}`[-1, 1] \times [-1, 1] \times [0, 1]`. The canonical perspective projection matrix for 
Vulkan is given by

```{math}
M^{C, Vulkan}_{per} =
\begin{bmatrix}
\frac{ 2 n }{r - \left( -l \right)} & 0                                   & -\frac{r + \left( -l \right)}{r - \left( -l \right)} &  0                  \\
0                                   & \frac{ 2 n }{t - \left( -b \right)} & -\frac{t + \left( -b \right)}{t - \left( -b \right)} &  0                  \\
0                                   & 0                                   &  \frac{f}{f - n}                                     & -\frac{f n }{f - n} \\
0                                   & 0                                   &  1                                                   &  0
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
0                               & 0                               & 0               &  1
\end{bmatrix}
.
```

The canonical symmetric vertical field of view perspective projection matrix for Vulkan is given by

```{math}
M^{C, Vulkan}_{per,vfov} = 
    \begin{bmatrix}
        \frac{1}{aspect \cdot \tan\left( \frac{\theta_{vfov}}{2} \right)} 
        & 0 
        & 0
        & 0 \\

        0 
        & \frac{1}{\tan\left( \frac{\theta_{vfov}}{2} \right)}
        & 0
        & 0 \\

        0 
        &  0 
        &  \frac{f}{f - n}
        & -\frac{ f n }{f - n} \\
    
        0 
        & 0 
        & 1 
        & 0
    \end{bmatrix}
```

### Right-Handed View Space

```{math}
R_{x}\left( \theta \right) = 
    \begin{bmatrix}
        1 & 0                         &  0                         & 0 \\
        0 & \cos\left( \theta \right) & -\sin\left( \theta \right) & 0 \\
        0 & \sin\left( \theta \right) &  \cos\left( \theta \right) & 0 \\
        0 & 0                         &  0                         & 1
    \end{bmatrix}
```

```{math}
Q^{Vulkan}_{rh} = R_{x}\left( \pi \right) 
                = \begin{bmatrix}
                      1 & 0                      &  0                   & 0 \\
                      0 & \cos\left( \pi \right) & -\sin\left( \pi \right) & 0 \\
                      0 & \sin\left( \pi \right) &  \cos\left( \pi \right) & 0 \\
                      0 & 0                      &  0                   & 1
                  \end{bmatrix}
                = \begin{bmatrix}
                      1 &  0 &  0 & 0 \\
                      0 & -1 &  0 & 0 \\
                      0 &  0 & -1 & 0 \\
                      0 &  0 &  0 & 1
                  \end{bmatrix}
```

Perspective Projection

```{math}
M^{Vulkan}_{per, rh \rightarrow rh}
    &= \Omega_{lh \rightarrow rh} \left(Q^{Vulkan}_{lh}\right)^{-1} M^{C,Vulkan}_{per} \Omega_{rh \rightarrow lh} Q^{Vulkan}_{rh} \\
    &= \left( \Omega_{lh \rightarrow rh} \left(Q^{Vulkan}_{lh}\right)^{-1} \right) M^{C,Vulkan}_{per} \left( \Omega_{rh \rightarrow lh} Q^{Vulkan}_{rh} \right) \\
    &= \left( 
            \begin{bmatrix}
                1 & 0 &  0 & 0 \\
                0 & 1 &  0 & 0 \\
                0 & 0 & -1 & 0 \\
                0 & 0 &  0 & 1
            \end{bmatrix}
            \begin{bmatrix}
                1 &  0 &  0 & 0 \\
                0 & -1 &  0 & 0 \\
                0 &  0 & -1 & 0 \\
                0 &  0 &  0 & 1
            \end{bmatrix}
        \right)
        M^{C,Vulkan}_{per}
        \left(
            \begin{bmatrix}
                1 & 0 &  0 & 0 \\
                0 & 1 &  0 & 0 \\
                0 & 0 & -1 & 0 \\
                0 & 0 &  0 & 1
            \end{bmatrix}
            \begin{bmatrix}
                1 &  0 &  0 & 0 \\
                0 & -1 &  0 & 0 \\
                0 &  0 & -1 & 0 \\
                0 &  0 &  0 & 1
            \end{bmatrix}
        \right)
        \\
    &= \begin{bmatrix}
            1 &  0 & 0 & 0 \\
            0 & -1 & 0 & 0 \\
            0 &  0 & 1 & 0 \\
            0 &  0 & 0 & 1
        \end{bmatrix}
        M^{C,Vulkan}_{per}
        \begin{bmatrix}
            1 &  0 & 0 & 0 \\
            0 & -1 & 0 & 0 \\
            0 &  0 & 1 & 0 \\
            0 &  0 & 0 & 1
        \end{bmatrix}
        \\
    &= \begin{bmatrix}
            1 &  0 & 0 & 0 \\
            0 & -1 & 0 & 0 \\
            0 &  0 & 1 & 0 \\
            0 &  0 & 0 & 1
        \end{bmatrix}
        \begin{bmatrix}
            \frac{ 2 n }{r - \left( -l \right)} & 0                                   & -\frac{r + \left( -l \right)}{r - \left( -l \right)} &  0                  \\
            0                                   & \frac{ 2 n }{t - \left( -b \right)} & -\frac{t + \left( -b \right)}{t - \left( -b \right)} &  0                  \\
            0                                   & 0                                   &  \frac{f}{f - n}                                     & -\frac{f n }{f - n} \\
            0                                   & 0                                   &  1                                                   &  0
        \end{bmatrix}
        \begin{bmatrix}
            1 &  0 & 0 & 0 \\
            0 & -1 & 0 & 0 \\
            0 &  0 & 1 & 0 \\
            0 &  0 & 0 & 1
        \end{bmatrix}
        \\
    &= \begin{bmatrix}
            1 &  0 & 0 & 0 \\
            0 & -1 & 0 & 0 \\
            0 &  0 & 1 & 0 \\
            0 &  0 & 0 & 1
        \end{bmatrix}
        \begin{bmatrix}
            \frac{ 2 n }{r - \left( -l \right)} &  0                                   & -\frac{r + \left( -l \right)}{r - \left( -l \right)} &  0                  \\
            0                                   & -\frac{ 2 n }{t - \left( -b \right)} & -\frac{t + \left( -b \right)}{t - \left( -b \right)} &  0                  \\
            0                                   &  0                                   &  \frac{f}{f - n}                                     & -\frac{f n }{f - n} \\
            0                                   &  0                                   &  1                                                   &  0
        \end{bmatrix}
        \\
    &=  \begin{bmatrix}
            1 &  0 & 0 & 0 \\
            0 & -1 & 0 & 0 \\
            0 &  0 & 1 & 0 \\
            0 &  0 & 0 & 1
        \end{bmatrix}
        \begin{bmatrix}
            \frac{ 2 n }{r + l} &  0                   & -\frac{r - l}{r + l} &  0                  \\
            0                   & -\frac{ 2 n }{t + b} & -\frac{t - b}{t + b} &  0                  \\
            0                   &  0                   &  \frac{f}{f - n}     & -\frac{f n }{f - n} \\
            0                   &  0                   &  1                   &  0
        \end{bmatrix}
        \\
    &=  \begin{bmatrix}
            1 &  0 & 0 & 0 \\
            0 & -1 & 0 & 0 \\
            0 &  0 & 1 & 0 \\
            0 &  0 & 0 & 1
        \end{bmatrix}
        \begin{bmatrix}
            \frac{ 2 n }{r + l} &  0                   & -\frac{r - l}{r + l} &  0                  \\
            0                   & -\frac{ 2 n }{b + t} & \frac{b - t}{b + t} &  0                  \\
            0                   &  0                   & \frac{f}{f - n}     & -\frac{f n }{f - n} \\
            0                   &  0                   & 1                   &  0
        \end{bmatrix}
        \\
    &=  \begin{bmatrix}
            \frac{ 2 n }{r + l} & 0                   & -\frac{r - l}{r + l} &  0                  \\
            0                   & \frac{ 2 n }{b + t} & -\frac{b - t}{b + t} &  0                  \\
            0                   & 0                   & \frac{f}{f - n}     & -\frac{f n }{f - n} \\
            0                   & 0                   & 1                   &  0
        \end{bmatrix}
        \\
    &=  \begin{bmatrix}
            \frac{ 2 n }{r - \left( -l \right)} & 0                                   & -\frac{r + \left( -l \right)}{r - \left( -l \right)} &  0                  \\
            0                                   & \frac{ 2 n }{b - \left( -t \right)} & -\frac{b + \left( -t \right)}{b - \left( -t \right)} &  0                  \\
            0                                   & 0                                   &  \frac{f}{f - n}                                     & -\frac{f n }{f - n} \\
            0                                   & 0                                   &  1                                                   &  0
        \end{bmatrix}
```

so

```{math}
M^{Vulkan}_{per, rh \rightarrow rh} = 
    \begin{bmatrix}
        \frac{ 2 n }{r - \left( -l \right)} & 0                                   & -\frac{r + \left( -l \right)}{r - \left( -l \right)} &  0                  \\
        0                                   & \frac{ 2 n }{b - \left( -t \right)} & -\frac{b + \left( -t \right)}{b - \left( -t \right)} &  0                  \\
        0                                   & 0                                   &  \frac{f}{f - n}                                     & -\frac{f n }{f - n} \\
        0                                   & 0                                   &  1                                                   &  0
    \end{bmatrix}
```

Calculation for orthographic projection

```{math}
    M^{Vulkan}_{orth, rh \rightarrow rh}
    &= \Omega_{lh \rightarrow rh} \left(Q^{Vulkan}_{lh}\right)^{-1} M^{C,Vulkan}_{orth} \Omega_{rh \rightarrow lh} Q^{Vulkan}_{rh} \\
    &= \left( \Omega_{lh \rightarrow rh} \left(Q^{Vulkan}_{lh}\right)^{-1} \right) M^{C,Vulkan}_{orth} \left( \Omega_{rh \rightarrow lh} Q^{Vulkan}_{rh} \right) \\
    &= \left( 
            \begin{bmatrix}
                1 & 0 &  0 & 0 \\
                0 & 1 &  0 & 0 \\
                0 & 0 & -1 & 0 \\
                0 & 0 &  0 & 1
            \end{bmatrix}
            \begin{bmatrix}
                1 &  0 &  0 & 0 \\
                0 & -1 &  0 & 0 \\
                0 &  0 & -1 & 0 \\
                0 &  0 &  0 & 1
            \end{bmatrix}
        \right)
        M^{C,Vulkan}_{orth}
        \left(
            \begin{bmatrix}
                1 & 0 &  0 & 0 \\
                0 & 1 &  0 & 0 \\
                0 & 0 & -1 & 0 \\
                0 & 0 &  0 & 1
            \end{bmatrix}
            \begin{bmatrix}
                1 &  0 &  0 & 0 \\
                0 & -1 &  0 & 0 \\
                0 &  0 & -1 & 0 \\
                0 &  0 &  0 & 1
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
            0 &  0 & 0 & 1
        \end{bmatrix}
        \\
    &= \begin{bmatrix}
            1 &  0 & 0 & 0 \\
            0 & -1 & 0 & 0 \\
            0 &  0 & 1 & 0 \\
            0 &  0 & 0 & 1
        \end{bmatrix}
        \begin{bmatrix}
            \frac{2}{r - \left( -l \right)} & 0                               & 0               & -\frac{r + \left( -l \right)}{r - \left( -l \right)} \\
            0                               & \frac{2}{t - \left( -b \right)} & 0               & -\frac{t + \left( -b \right)}{t - \left( -b \right)} \\
            0                               & 0                               & \frac{1}{f - n} & -\frac{n}{f - n}                                     \\
            0                               & 0                               & 0               &  1
        \end{bmatrix}
        \begin{bmatrix}
            1 &  0 & 0 & 0 \\
            0 & -1 & 0 & 0 \\
            0 &  0 & 1 & 0 \\
            0 &  0 & 0 & 1
        \end{bmatrix}
        \\
    &= \begin{bmatrix}
            1 &  0 & 0 & 0 \\
            0 & -1 & 0 & 0 \\
            0 &  0 & 1 & 0 \\
            0 &  0 & 0 & 1
        \end{bmatrix}
        \begin{bmatrix}
            \frac{2}{r - \left( -l \right)} &  0                               & 0               & -\frac{r + \left( -l \right)}{r - \left( -l \right)} \\
            0                               & -\frac{2}{t - \left( -b \right)} & 0               & -\frac{t + \left( -b \right)}{t - \left( -b \right)} \\
            0                               &  0                               & \frac{1}{f - n} & -\frac{n}{f - n}                                     \\
            0                               &  0                               & 0               &  1
        \end{bmatrix}
        \\
    &= \begin{bmatrix}
            1 &  0 & 0 & 0 \\
            0 & -1 & 0 & 0 \\
            0 &  0 & 1 & 0 \\
            0 &  0 & 0 & 1
        \end{bmatrix}
        \begin{bmatrix}
            \frac{2}{r + l} &  0               & 0               & -\frac{r - l}{r + l} \\
            0               & -\frac{2}{t + b} & 0               & -\frac{t - b}{t + b} \\
            0               &  0               & \frac{1}{f - n} & -\frac{n}{f - n}     \\
            0               &  0               & 0               &  1
        \end{bmatrix}
        \\
    &= \begin{bmatrix}
            1 &  0 & 0 & 0 \\
            0 & -1 & 0 & 0 \\
            0 &  0 & 1 & 0 \\
            0 &  0 & 0 & 1
        \end{bmatrix}
        \begin{bmatrix}
            \frac{2}{r + l} &  0               & 0               & -\frac{r - l}{r + l} \\
            0               & -\frac{2}{b + t} & 0               &  \frac{b - t}{b + t} \\
            0               &  0               & \frac{1}{f - n} & -\frac{n}{f - n}     \\
            0               &  0               & 0               &  1
        \end{bmatrix}
        \\
    &=  \begin{bmatrix}
            \frac{2}{r + l} & 0               & 0               & -\frac{r - l}{r + l}  \\
            0               & \frac{2}{b + t} & 0               & -\frac{b - t}{b + t}  \\
            0               & 0               & \frac{1}{f - n} & -\frac{n}{f - n}      \\
            0               & 0               & 0               &  1
        \end{bmatrix}
        \\
    &=  \begin{bmatrix}
            \frac{2}{r - \left( -l \right)} & 0                               & 0               & -\frac{r + \left( -l \right)}{r - \left( -l \right)}  \\
            0                               & \frac{2}{b - \left( -t \right)} & 0               & -\frac{b + \left( -t \right)}{b - \left( -t \right)}  \\
            0                               & 0                               & \frac{1}{f - n} & -\frac{n}{f - n}                                      \\
            0                               & 0                               & 0               &  1
        \end{bmatrix}
```

so

```{math}
M^{Vulkan}_{orth, rh \rightarrow rh} = 
    \begin{bmatrix}
        \frac{2}{r - \left( -l \right)} & 0                               & 0               & -\frac{r + \left( -l \right)}{r - \left( -l \right)}  \\
        0                               & \frac{2}{b - \left( -t \right)} & 0               & -\frac{b + \left( -t \right)}{b - \left( -t \right)}  \\
        0                               & 0                               & \frac{1}{f - n} & -\frac{n}{f - n}                                      \\
        0                               & 0                               & 0               &  1
    \end{bmatrix}
```

Symmetric Vertical Field Of View Perspective Projection

```{math}
M^{Vulkan}_{per,vfov,rh \rightarrow rh}
    &= \Omega_{lh \rightarrow rh} \left(Q^{Vulkan}_{lh}\right)^{-1} M^{C,Vulkan}_{per,vfov} \Omega_{rh \rightarrow lh} Q^{Vulkan}_{rh} \\
    &= \left( \Omega_{lh \rightarrow rh} \left(Q^{Vulkan}_{lh}\right)^{-1} \right) M^{C,Vulkan}_{per,vfov} \left( \Omega_{rh \rightarrow lh} Q^{Vulkan}_{rh} \right) \\
    &= \left( 
            \begin{bmatrix}
                1 & 0 &  0 & 0 \\
                0 & 1 &  0 & 0 \\
                0 & 0 & -1 & 0 \\
                0 & 0 &  0 & 1
            \end{bmatrix}
            \begin{bmatrix}
                1 &  0 &  0 & 0 \\
                0 & -1 &  0 & 0 \\
                0 &  0 & -1 & 0 \\
                0 &  0 &  0 & 1
            \end{bmatrix}
        \right)
        M^{C,Vulkan}_{per,vfov}
        \left(
            \begin{bmatrix}
                1 & 0 &  0 & 0 \\
                0 & 1 &  0 & 0 \\
                0 & 0 & -1 & 0 \\
                0 & 0 &  0 & 1
            \end{bmatrix}
            \begin{bmatrix}
                1 &  0 &  0 & 0 \\
                0 & -1 &  0 & 0 \\
                0 &  0 & -1 & 0 \\
                0 &  0 &  0 & 1
            \end{bmatrix}
        \right)
        \\
    &= \begin{bmatrix}
            1 &  0 & 0 & 0 \\
            0 & -1 & 0 & 0 \\
            0 &  0 & 1 & 0 \\
            0 &  0 & 0 & 1
        \end{bmatrix}
        M^{C,Vulkan}_{per,vfov}
        \begin{bmatrix}
            1 &  0 & 0 & 0 \\
            0 & -1 & 0 & 0 \\
            0 &  0 & 1 & 0 \\
            0 &  0 & 0 & 1
        \end{bmatrix}
        \\
    &= \begin{bmatrix}
            1 &  0 & 0 & 0 \\
            0 & -1 & 0 & 0 \\
            0 &  0 & 1 & 0 \\
            0 &  0 & 0 & 1
        \end{bmatrix}
        \begin{bmatrix}
            \frac{1}{aspect \cdot \tan\left( \frac{\theta_{vfov}}{2} \right)} 
            & 0 
            & 0
            & 0 \\

            0 
            & \frac{1}{\tan\left( \frac{\theta_{vfov}}{2} \right)}
            & 0
            & 0 \\

            0 
            &  0 
            &  \frac{f}{f - n}
            & -\frac{ f n }{f - n} \\
    
            0 
            & 0 
            & 1 
            & 0
        \end{bmatrix}
        \begin{bmatrix}
            1 &  0 & 0 & 0 \\
            0 & -1 & 0 & 0 \\
            0 &  0 & 1 & 0 \\
            0 &  0 & 0 & 1
        \end{bmatrix}
        \\
    &= \begin{bmatrix}
            1 &  0 & 0 & 0 \\
            0 & -1 & 0 & 0 \\
            0 &  0 & 1 & 0 \\
            0 &  0 & 0 & 1
        \end{bmatrix}
        \begin{bmatrix}
            \frac{1}{aspect \cdot \tan\left( \frac{\theta_{vfov}}{2} \right)} 
            & 0 
            & 0
            & 0 \\

            0 
            & -\frac{1}{\tan\left( \frac{\theta_{vfov}}{2} \right)}
            &  0
            &  0 \\

            0 
            &  0 
            &  \frac{f}{f - n}
            & -\frac{ f n }{f - n} \\
    
            0 
            & 0 
            & 1 
            & 0
        \end{bmatrix}
        \\
    &=  \begin{bmatrix}
            \frac{1}{aspect \cdot \tan\left( \frac{\theta_{vfov}}{2} \right)} 
            & 0 
            & 0
            & 0 \\

            0 
            & \frac{1}{\tan\left( \frac{\theta_{vfov}}{2} \right)}
            & 0
            & 0 \\

            0 
            &  0 
            &  \frac{f}{f - n}
            & -\frac{ f n }{f - n} \\
    
            0 
            & 0 
            & 1 
            & 0
        \end{bmatrix}
```

so

```{math}
M^{Vulkan}_{per,vfov,rh \rightarrow rh} = 
    \begin{bmatrix}
        \frac{1}{aspect \cdot \tan\left( \frac{\theta_{vfov}}{2} \right)} 
        & 0 
        & 0
        & 0 \\

        0 
        & \frac{1}{\tan\left( \frac{\theta_{vfov}}{2} \right)}
        & 0
        & 0 \\

        0 
        &  0 
        &  \frac{f}{f - n}
        & -\frac{ f n }{f - n} \\
    
        0 
        & 0 
        & 1 
        & 0
    \end{bmatrix}
```


### Left-Handed View Space

```{math}
R_{x}\left( \theta \right) = 
    \begin{bmatrix}
        1 & 0                         &  0                         & 0 \\
        0 & \cos\left( \theta \right) & -\sin\left( \theta \right) & 0 \\
        0 & \sin\left( \theta \right) &  \cos\left( \theta \right) & 0 \\
        0 & 0                         &  0                         & 1
    \end{bmatrix}
```

```{math}
Q^{Vulkan}_{lh} = R_{x}\left( \pi \right) 
                = \begin{bmatrix}
                      1 & 0                      &  0                   & 0 \\
                      0 & \cos\left( \pi \right) & -\sin\left( \pi \right) & 0 \\
                      0 & \sin\left( \pi \right) &  \cos\left( \pi \right) & 0 \\
                      0 & 0                      &  0                   & 1
                  \end{bmatrix}
                = \begin{bmatrix}
                      1 &  0 &  0 & 0 \\
                      0 & -1 &  0 & 0 \\
                      0 &  0 & -1 & 0 \\
                      0 &  0 &  0 & 1
                  \end{bmatrix}
```

Perspective Projection

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
                0 & 0 &  0 & 1
            \end{bmatrix}
            \begin{bmatrix}
                1 &  0 &  0 & 0 \\
                0 & -1 &  0 & 0 \\
                0 &  0 & -1 & 0 \\
                0 &  0 &  0 & 1
            \end{bmatrix}
        \right)
        M^{C,Vulkan}_{per}
        \begin{bmatrix}
            1 &  0 &  0 & 0 \\
            0 & -1 &  0 & 0 \\
            0 &  0 & -1 & 0 \\
            0 &  0 &  0 & 1
        \end{bmatrix}
        \\
    &= \begin{bmatrix}
            1 &  0 & 0 & 0 \\
            0 & -1 & 0 & 0 \\
            0 &  0 & 1 & 0 \\
            0 &  0 & 0 & 1
        \end{bmatrix}
        M^{C,Vulkan}_{per}
        \begin{bmatrix}
            1 &  0 &  0 & 0 \\
            0 & -1 &  0 & 0 \\
            0 &  0 & -1 & 0 \\
            0 &  0 &  0 & 1
        \end{bmatrix}
        \\
    &= \begin{bmatrix}
            1 &  0 & 0 & 0 \\
            0 & -1 & 0 & 0 \\
            0 &  0 & 1 & 0 \\
            0 &  0 & 0 & 1
        \end{bmatrix}
        \begin{bmatrix}
            \frac{ 2 n }{r - \left( -l \right)} & 0                                   & -\frac{r + \left( -l \right)}{r - \left( -l \right)} &  0                  \\
            0                                   & \frac{ 2 n }{t - \left( -b \right)} & -\frac{t + \left( -b \right)}{t - \left( -b \right)} &  0                  \\
            0                                   & 0                                   &  \frac{f}{f - n}                                     & -\frac{f n }{f - n} \\
            0                                   & 0                                   &  1                                                   &  0
        \end{bmatrix}
        \begin{bmatrix}
            1 &  0 &  0 & 0 \\
            0 & -1 &  0 & 0 \\
            0 &  0 & -1 & 0 \\
            0 &  0 &  0 & 1
        \end{bmatrix}
        \\
    &= \begin{bmatrix}
            1 &  0 & 0 & 0 \\
            0 & -1 & 0 & 0 \\
            0 &  0 & 1 & 0 \\
            0 &  0 & 0 & 1
        \end{bmatrix}
        \begin{bmatrix}
            \frac{ 2 n }{r - \left( -l \right)} &  0                                   &  \frac{r + \left( -l \right)}{r - \left( -l \right)} &  0                  \\
            0                                   & -\frac{ 2 n }{t - \left( -b \right)} &  \frac{t + \left( -b \right)}{t - \left( -b \right)} &  0                  \\
            0                                   &  0                                   & -\frac{f}{f - n}                                     & -\frac{f n }{f - n} \\
            0                                   &  0                                   & -1                                                   &  0
        \end{bmatrix}
        \\
    &=  \begin{bmatrix}
            1 &  0 & 0 & 0 \\
            0 & -1 & 0 & 0 \\
            0 &  0 & 1 & 0 \\
            0 &  0 & 0 & 1
        \end{bmatrix}
        \begin{bmatrix}
            \frac{ 2 n }{r + l} &  0                   &  \frac{r - l}{r + l} &  0                  \\
            0                   & -\frac{ 2 n }{t + b} &  \frac{t - b}{t + b} &  0                  \\
            0                   &  0                   & -\frac{f}{f - n}     & -\frac{f n }{f - n} \\
            0                   &  0                   & -1                   &  0
        \end{bmatrix}
        \\
    &=  \begin{bmatrix}
            1 &  0 & 0 & 0 \\
            0 & -1 & 0 & 0 \\
            0 &  0 & 1 & 0 \\
            0 &  0 & 0 & 1
        \end{bmatrix}
        \begin{bmatrix}
            \frac{ 2 n }{r + l} &  0                   &  \frac{r - l}{r + l} &  0                  \\
            0                   & -\frac{ 2 n }{b + t} & -\frac{b - t}{b + t} &  0                  \\
            0                   &  0                   & -\frac{f}{f - n}     & -\frac{f n }{f - n} \\
            0                   &  0                   & -1                   &  0
        \end{bmatrix}
        \\
    &=  \begin{bmatrix}
            \frac{ 2 n }{r + l} & 0                   &  \frac{r - l}{r + l} &  0                  \\
            0                   & \frac{ 2 n }{b + t} &  \frac{b - t}{b + t} &  0                  \\
            0                   & 0                   & -\frac{f}{f - n}     & -\frac{f n }{f - n} \\
            0                   & 0                   & -1                   &  0
        \end{bmatrix}
        \\
    &=  \begin{bmatrix}
            \frac{ 2 n }{r - \left( -l \right)} & 0                                   &  \frac{r + \left( -l \right)}{r - \left( -l \right)} &  0                  \\
            0                                   & \frac{ 2 n }{b - \left( -t \right)} &  \frac{b + \left( -t \right)}{b - \left( -t \right)} &  0                  \\
            0                                   & 0                                   & -\frac{f}{f - n}                                     & -\frac{f n }{f - n} \\
            0                                   & 0                                   & -1                                                   &  0
        \end{bmatrix}
```

so

```{math}
M^{Vulkan}_{per, lh \rightarrow rh} = 
    \begin{bmatrix}
        \frac{ 2 n }{r - \left( -l \right)} & 0                                   &  \frac{r + \left( -l \right)}{r - \left( -l \right)} &  0                  \\
        0                                   & \frac{ 2 n }{b - \left( -t \right)} &  \frac{b + \left( -t \right)}{b - \left( -t \right)} &  0                  \\
        0                                   & 0                                   & -\frac{f}{f - n}                                     & -\frac{f n }{f - n} \\
        0                                   & 0                                   & -1                                                   &  0
    \end{bmatrix}
```

Calculation for orthographic projection

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
                0 & 0 &  0 & 1
            \end{bmatrix}
            \begin{bmatrix}
                1 &  0 &  0 & 0 \\
                0 & -1 &  0 & 0 \\
                0 &  0 & -1 & 0 \\
                0 &  0 &  0 & 1
            \end{bmatrix}
        \right)
        M^{C,Vulkan}_{orth}
        \begin{bmatrix}
            1 &  0 &  0 & 0 \\
            0 & -1 &  0 & 0 \\
            0 &  0 & -1 & 0 \\
            0 &  0 &  0 & 1
        \end{bmatrix}
        \\
    &= \begin{bmatrix}
            1 &  0 & 0 & 0 \\
            0 & -1 & 0 & 0 \\
            0 &  0 & 1 & 0 \\
            0 &  0 & 0 & 1
        \end{bmatrix}
        M^{C,Vulkan}_{orth}
        \begin{bmatrix}
            1 &  0 &  0 & 0 \\
            0 & -1 &  0 & 0 \\
            0 &  0 & -1 & 0 \\
            0 &  0 &  0 & 1
        \end{bmatrix}
        \\
    &= \begin{bmatrix}
            1 &  0 & 0 & 0 \\
            0 & -1 & 0 & 0 \\
            0 &  0 & 1 & 0 \\
            0 &  0 & 0 & 1
        \end{bmatrix}
        \begin{bmatrix}
            \frac{2}{r - \left( -l \right)} & 0                               & 0               & -\frac{r + \left( -l \right)}{r - \left( -l \right)} \\
            0                               & \frac{2}{t - \left( -b \right)} & 0               & -\frac{t + \left( -b \right)}{t - \left( -b \right)} \\
            0                               & 0                               & \frac{1}{f - n} & -\frac{n}{f - n}                                     \\
            0                               & 0                               & 0               &  1
        \end{bmatrix}
        \begin{bmatrix}
            1 &  0 &  0 & 0 \\
            0 & -1 &  0 & 0 \\
            0 &  0 & -1 & 0 \\
            0 &  0 &  0 & 1
        \end{bmatrix}
        \\
    &= \begin{bmatrix}
            1 &  0 & 0 & 0 \\
            0 & -1 & 0 & 0 \\
            0 &  0 & 1 & 0 \\
            0 &  0 & 0 & 1
        \end{bmatrix}
        \begin{bmatrix}
            \frac{2}{r - \left( -l \right)} &  0                               &  0               & -\frac{r + \left( -l \right)}{r - \left( -l \right)} \\
            0                               & -\frac{2}{t - \left( -b \right)} &  0               & -\frac{t + \left( -b \right)}{t - \left( -b \right)} \\
            0                               &  0                               & -\frac{1}{f - n} & -\frac{n}{f - n}                                     \\
            0                               &  0                               &  0               &  1
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
            0               &  0               &  0               &  1
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
            0               & -\frac{2}{b + t} &  0               &  \frac{b - t}{b + t} \\
            0               &  0               & -\frac{1}{f - n} & -\frac{n}{f - n}     \\
            0               &  0               &  0               &  1
        \end{bmatrix}
        \\
    &=  \begin{bmatrix}
            \frac{2}{r + l} & 0               &  0               & -\frac{r - l}{r + l}  \\
            0               & \frac{2}{b + t} &  0               & -\frac{b - t}{b + t}  \\
            0               & 0               & -\frac{1}{f - n} & -\frac{n}{f - n}      \\
            0               & 0               &  0               &  1
        \end{bmatrix}
        \\
    &=  \begin{bmatrix}
            \frac{2}{r - \left( -l \right)} & 0                               &  0               & -\frac{r + \left( -l \right)}{r - \left( -l \right)}  \\
            0                               & \frac{2}{b - \left( -t \right)} &  0               & -\frac{b + \left( -t \right)}{b - \left( -t \right)}  \\
            0                               & 0                               & -\frac{1}{f - n} & -\frac{n}{f - n}                                      \\
            0                               & 0                               &  0               &  1
        \end{bmatrix}
```

so

```{math}
M^{Vulkan}_{orth, lh \rightarrow rh} = 
    \begin{bmatrix}
        \frac{2}{r - \left( -l \right)} & 0                               &  0               & -\frac{r + \left( -l \right)}{r - \left( -l \right)}  \\
        0                               & \frac{2}{b - \left( -t \right)} &  0               & -\frac{b + \left( -t \right)}{b - \left( -t \right)}  \\
        0                               & 0                               & -\frac{1}{f - n} & -\frac{n}{f - n}                                      \\
        0                               & 0                               &  0               &  1
    \end{bmatrix}
```

Symmetric Vertical Field Of View Perspective Projection

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
                0 & 0 &  0 & 1
            \end{bmatrix}
            \begin{bmatrix}
                1 &  0 &  0 & 0 \\
                0 & -1 &  0 & 0 \\
                0 &  0 & -1 & 0 \\
                0 &  0 &  0 & 1
            \end{bmatrix}
        \right)
        M^{C,Vulkan}_{per,vfov}
        \begin{bmatrix}
            1 &  0 &  0 & 0 \\
            0 & -1 &  0 & 0 \\
            0 &  0 & -1 & 0 \\
            0 &  0 &  0 & 1
        \end{bmatrix}
        \\
    &= \begin{bmatrix}
            1 &  0 & 0 & 0 \\
            0 & -1 & 0 & 0 \\
            0 &  0 & 1 & 0 \\
            0 &  0 & 0 & 1
        \end{bmatrix}
        M^{C,Vulkan}_{per,vfov}
        \begin{bmatrix}
            1 &  0 &  0 & 0 \\
            0 & -1 &  0 & 0 \\
            0 &  0 & -1 & 0 \\
            0 &  0 &  0 & 1
        \end{bmatrix}
        \\
    &= \begin{bmatrix}
            1 &  0 & 0 & 0 \\
            0 & -1 & 0 & 0 \\
            0 &  0 & 1 & 0 \\
            0 &  0 & 0 & 1
        \end{bmatrix}
        \begin{bmatrix}
            \frac{1}{aspect \cdot \tan\left( \frac{\theta_{vfov}}{2} \right)} 
            & 0 
            & 0
            & 0 \\

            0 
            & \frac{1}{\tan\left( \frac{\theta_{vfov}}{2} \right)}
            & 0
            & 0 \\

            0 
            &  0 
            &  \frac{f}{f - n}
            & -\frac{ f n }{f - n} \\
    
            0 
            & 0 
            & 1 
            & 0
        \end{bmatrix}
        \begin{bmatrix}
            1 &  0 &  0 & 0 \\
            0 & -1 &  0 & 0 \\
            0 &  0 & -1 & 0 \\
            0 &  0 &  0 & 1
        \end{bmatrix}
        \\
    &= \begin{bmatrix}
            1 &  0 & 0 & 0 \\
            0 & -1 & 0 & 0 \\
            0 &  0 & 1 & 0 \\
            0 &  0 & 0 & 1
        \end{bmatrix}
        \begin{bmatrix}
            \frac{1}{aspect \cdot \tan\left( \frac{\theta_{vfov}}{2} \right)} 
            & 0 
            & 0
            & 0 \\

            0 
            & -\frac{1}{\tan\left( \frac{\theta_{vfov}}{2} \right)}
            &  0
            &  0 \\

            0 
            &  0 
            & -\frac{f}{f - n}
            & -\frac{ f n }{f - n} \\
    
            0 
            &  0 
            & -1 
            &  0
        \end{bmatrix}
        \\
    &=  \begin{bmatrix}
            \frac{1}{aspect \cdot \tan\left( \frac{\theta_{vfov}}{2} \right)} 
            & 0 
            & 0
            & 0 \\

            0 
            & \frac{1}{\tan\left( \frac{\theta_{vfov}}{2} \right)}
            & 0
            & 0 \\

            0 
            &  0 
            & -\frac{f}{f - n}
            & -\frac{ f n }{f - n} \\
    
            0 
            &  0 
            & -1 
            &  0
        \end{bmatrix}
```

so

```{math}
M^{Vulkan}_{per,vfov,rh \rightarrow rh} = 
    \begin{bmatrix}
        \frac{1}{aspect \cdot \tan\left( \frac{\theta_{vfov}}{2} \right)} 
        & 0 
        & 0
        & 0 \\

        0 
        & \frac{1}{\tan\left( \frac{\theta_{vfov}}{2} \right)}
        & 0
        & 0 \\

        0 
        &  0 
        & -\frac{f}{f - n}
        & -\frac{ f n }{f - n} \\
    
        0 
        &  0 
        & -1 
        &  0
    \end{bmatrix}
```

## Vulkan (Standard Coordinates)

### Canonical Matrix

The canonical view volume for for Vulkan in normalized device coordinates is parametrized by 
{math}`[-1, 1] \times [-1, 1] \times [0, 1]`. The canonical perspective projection matrix for 
Vulkan is given by

```{math}
M^{C, Vulkan}_{per} =
\begin{bmatrix}
\frac{ 2 n }{r - \left( -l \right)} & 0                                   & -\frac{r + \left( -l \right)}{r - \left( -l \right)} &  0                  \\
0                                   & \frac{ 2 n }{t - \left( -b \right)} & -\frac{t + \left( -b \right)}{t - \left( -b \right)} &  0                  \\
0                                   & 0                                   &  \frac{f}{f - n}                                     & -\frac{f n }{f - n} \\
0                                   & 0                                   &  1                                                   &  0
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
0                               & 0                               & 0               &  1
\end{bmatrix}
.
```

The canonical symmetric vertical field of view perspective projection matrix for Vulkan is given by

```{math}
M^{C, Vulkan}_{per,vfov} = 
    \begin{bmatrix}
        \frac{1}{aspect \cdot \tan\left( \frac{\theta_{vfov}}{2} \right)} 
        & 0 
        & 0
        & 0 \\

        0 
        & \frac{1}{\tan\left( \frac{\theta_{vfov}}{2} \right)}
        & 0
        & 0 \\

        0 
        &  0 
        &  \frac{f}{f - n}
        & -\frac{ f n }{f - n} \\
    
        0 
        & 0 
        & 1 
        & 0
    \end{bmatrix}
```

### Right-Handed View Space

```{math}
R_{x}\left( \theta \right) = 
    \begin{bmatrix}
        1 & 0                         &  0                         & 0 \\
        0 & \cos\left( \theta \right) & -\sin\left( \theta \right) & 0 \\
        0 & \sin\left( \theta \right) &  \cos\left( \theta \right) & 0 \\
        0 & 0                         &  0                         & 1
    \end{bmatrix}
```

```{math}
Q^{Vulkan}_{rh} = R_{x}\left( \pi \right) 
                = \begin{bmatrix}
                      1 & 0                      &  0                   & 0 \\
                      0 & \cos\left( \pi \right) & -\sin\left( \pi \right) & 0 \\
                      0 & \sin\left( \pi \right) &  \cos\left( \pi \right) & 0 \\
                      0 & 0                      &  0                   & 1
                  \end{bmatrix}
                = \begin{bmatrix}
                      1 &  0 &  0 & 0 \\
                      0 & -1 &  0 & 0 \\
                      0 &  0 & -1 & 0 \\
                      0 &  0 &  0 & 1
                  \end{bmatrix}
```

Perspective Projection

```{math}
M^{Vulkan}_{per, rh \rightarrow rh}
    &= \Omega_{lh \rightarrow rh} \left(Q^{Vulkan}_{lh}\right)^{-1} M^{C,Vulkan}_{per} \Omega_{rh \rightarrow lh} Q^{Vulkan}_{rh} \\
    &= \left( \Omega_{lh \rightarrow rh} \left(Q^{Vulkan}_{lh}\right)^{-1} \right) M^{C,Vulkan}_{per} \left( \Omega_{rh \rightarrow lh} Q^{Vulkan}_{rh} \right) \\
    &= \left( 
            \begin{bmatrix}
                1 & 0 &  0 & 0 \\
                0 & 1 &  0 & 0 \\
                0 & 0 & -1 & 0 \\
                0 & 0 &  0 & 1
            \end{bmatrix}
            \begin{bmatrix}
                1 &  0 &  0 & 0 \\
                0 & -1 &  0 & 0 \\
                0 &  0 & -1 & 0 \\
                0 &  0 &  0 & 1
            \end{bmatrix}
        \right)
        M^{C,Vulkan}_{per}
        \left(
            \begin{bmatrix}
                1 & 0 &  0 & 0 \\
                0 & 1 &  0 & 0 \\
                0 & 0 & -1 & 0 \\
                0 & 0 &  0 & 1
            \end{bmatrix}
            \begin{bmatrix}
                1 &  0 &  0 & 0 \\
                0 & -1 &  0 & 0 \\
                0 &  0 & -1 & 0 \\
                0 &  0 &  0 & 1
            \end{bmatrix}
        \right)
        \\
    &= \begin{bmatrix}
            1 &  0 & 0 & 0 \\
            0 & -1 & 0 & 0 \\
            0 &  0 & 1 & 0 \\
            0 &  0 & 0 & 1
        \end{bmatrix}
        M^{C,Vulkan}_{per}
        \begin{bmatrix}
            1 &  0 & 0 & 0 \\
            0 & -1 & 0 & 0 \\
            0 &  0 & 1 & 0 \\
            0 &  0 & 0 & 1
        \end{bmatrix}
        \\
    &= \begin{bmatrix}
            1 &  0 & 0 & 0 \\
            0 & -1 & 0 & 0 \\
            0 &  0 & 1 & 0 \\
            0 &  0 & 0 & 1
        \end{bmatrix}
        \begin{bmatrix}
            \frac{ 2 n }{r - \left( -l \right)} & 0                                   & -\frac{r + \left( -l \right)}{r - \left( -l \right)} &  0                  \\
            0                                   & \frac{ 2 n }{t - \left( -b \right)} & -\frac{t + \left( -b \right)}{t - \left( -b \right)} &  0                  \\
            0                                   & 0                                   &  \frac{f}{f - n}                                     & -\frac{f n }{f - n} \\
            0                                   & 0                                   &  1                                                   &  0
        \end{bmatrix}
        \begin{bmatrix}
            1 &  0 & 0 & 0 \\
            0 & -1 & 0 & 0 \\
            0 &  0 & 1 & 0 \\
            0 &  0 & 0 & 1
        \end{bmatrix}
        \\
    &= \begin{bmatrix}
            1 &  0 & 0 & 0 \\
            0 & -1 & 0 & 0 \\
            0 &  0 & 1 & 0 \\
            0 &  0 & 0 & 1
        \end{bmatrix}
        \begin{bmatrix}
            \frac{ 2 n }{r - \left( -l \right)} &  0                                   & -\frac{r + \left( -l \right)}{r - \left( -l \right)} &  0                  \\
            0                                   & -\frac{ 2 n }{t - \left( -b \right)} & -\frac{t + \left( -b \right)}{t - \left( -b \right)} &  0                  \\
            0                                   &  0                                   &  \frac{f}{f - n}                                     & -\frac{f n }{f - n} \\
            0                                   &  0                                   &  1                                                   &  0
        \end{bmatrix}
        \\
    &=  \begin{bmatrix}
            1 &  0 & 0 & 0 \\
            0 & -1 & 0 & 0 \\
            0 &  0 & 1 & 0 \\
            0 &  0 & 0 & 1
        \end{bmatrix}
        \begin{bmatrix}
            \frac{ 2 n }{r + l} &  0                   & -\frac{r - l}{r + l} &  0                  \\
            0                   & -\frac{ 2 n }{t + b} & -\frac{t - b}{t + b} &  0                  \\
            0                   &  0                   &  \frac{f}{f - n}     & -\frac{f n }{f - n} \\
            0                   &  0                   &  1                   &  0
        \end{bmatrix}
        \\
    &=  \begin{bmatrix}
            1 &  0 & 0 & 0 \\
            0 & -1 & 0 & 0 \\
            0 &  0 & 1 & 0 \\
            0 &  0 & 0 & 1
        \end{bmatrix}
        \begin{bmatrix}
            \frac{ 2 n }{r + l} &  0                   & -\frac{r - l}{r + l} &  0                  \\
            0                   & -\frac{ 2 n }{b + t} & \frac{b - t}{b + t} &  0                  \\
            0                   &  0                   & \frac{f}{f - n}     & -\frac{f n }{f - n} \\
            0                   &  0                   & 1                   &  0
        \end{bmatrix}
        \\
    &=  \begin{bmatrix}
            \frac{ 2 n }{r + l} & 0                   & -\frac{r - l}{r + l} &  0                  \\
            0                   & \frac{ 2 n }{b + t} & -\frac{b - t}{b + t} &  0                  \\
            0                   & 0                   & \frac{f}{f - n}     & -\frac{f n }{f - n} \\
            0                   & 0                   & 1                   &  0
        \end{bmatrix}
        \\
    &=  \begin{bmatrix}
            \frac{ 2 n }{r - \left( -l \right)} & 0                                   & -\frac{r + \left( -l \right)}{r - \left( -l \right)} &  0                  \\
            0                                   & \frac{ 2 n }{b - \left( -t \right)} & -\frac{b + \left( -t \right)}{b - \left( -t \right)} &  0                  \\
            0                                   & 0                                   &  \frac{f}{f - n}                                     & -\frac{f n }{f - n} \\
            0                                   & 0                                   &  1                                                   &  0
        \end{bmatrix}
```

so

```{math}
M^{Vulkan}_{per, rh \rightarrow rh} = 
    \begin{bmatrix}
        \frac{ 2 n }{r - \left( -l \right)} & 0                                   & -\frac{r + \left( -l \right)}{r - \left( -l \right)} &  0                  \\
        0                                   & \frac{ 2 n }{b - \left( -t \right)} & -\frac{b + \left( -t \right)}{b - \left( -t \right)} &  0                  \\
        0                                   & 0                                   &  \frac{f}{f - n}                                     & -\frac{f n }{f - n} \\
        0                                   & 0                                   &  1                                                   &  0
    \end{bmatrix}
```

Calculation for orthographic projection

```{math}
    M^{Vulkan}_{orth, rh \rightarrow rh}
    &= \Omega_{lh \rightarrow rh} \left(Q^{Vulkan}_{lh}\right)^{-1} M^{C,Vulkan}_{orth} \Omega_{rh \rightarrow lh} Q^{Vulkan}_{rh} \\
    &= \left( \Omega_{lh \rightarrow rh} \left(Q^{Vulkan}_{lh}\right)^{-1} \right) M^{C,Vulkan}_{orth} \left( \Omega_{rh \rightarrow lh} Q^{Vulkan}_{rh} \right) \\
    &= \left( 
            \begin{bmatrix}
                1 & 0 &  0 & 0 \\
                0 & 1 &  0 & 0 \\
                0 & 0 & -1 & 0 \\
                0 & 0 &  0 & 1
            \end{bmatrix}
            \begin{bmatrix}
                1 &  0 &  0 & 0 \\
                0 & -1 &  0 & 0 \\
                0 &  0 & -1 & 0 \\
                0 &  0 &  0 & 1
            \end{bmatrix}
        \right)
        M^{C,Vulkan}_{orth}
        \left(
            \begin{bmatrix}
                1 & 0 &  0 & 0 \\
                0 & 1 &  0 & 0 \\
                0 & 0 & -1 & 0 \\
                0 & 0 &  0 & 1
            \end{bmatrix}
            \begin{bmatrix}
                1 &  0 &  0 & 0 \\
                0 & -1 &  0 & 0 \\
                0 &  0 & -1 & 0 \\
                0 &  0 &  0 & 1
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
            0 &  0 & 0 & 1
        \end{bmatrix}
        \\
    &= \begin{bmatrix}
            1 &  0 & 0 & 0 \\
            0 & -1 & 0 & 0 \\
            0 &  0 & 1 & 0 \\
            0 &  0 & 0 & 1
        \end{bmatrix}
        \begin{bmatrix}
            \frac{2}{r - \left( -l \right)} & 0                               & 0               & -\frac{r + \left( -l \right)}{r - \left( -l \right)} \\
            0                               & \frac{2}{t - \left( -b \right)} & 0               & -\frac{t + \left( -b \right)}{t - \left( -b \right)} \\
            0                               & 0                               & \frac{1}{f - n} & -\frac{n}{f - n}                                     \\
            0                               & 0                               & 0               &  1
        \end{bmatrix}
        \begin{bmatrix}
            1 &  0 & 0 & 0 \\
            0 & -1 & 0 & 0 \\
            0 &  0 & 1 & 0 \\
            0 &  0 & 0 & 1
        \end{bmatrix}
        \\
    &= \begin{bmatrix}
            1 &  0 & 0 & 0 \\
            0 & -1 & 0 & 0 \\
            0 &  0 & 1 & 0 \\
            0 &  0 & 0 & 1
        \end{bmatrix}
        \begin{bmatrix}
            \frac{2}{r - \left( -l \right)} &  0                               & 0               & -\frac{r + \left( -l \right)}{r - \left( -l \right)} \\
            0                               & -\frac{2}{t - \left( -b \right)} & 0               & -\frac{t + \left( -b \right)}{t - \left( -b \right)} \\
            0                               &  0                               & \frac{1}{f - n} & -\frac{n}{f - n}                                     \\
            0                               &  0                               & 0               &  1
        \end{bmatrix}
        \\
    &= \begin{bmatrix}
            1 &  0 & 0 & 0 \\
            0 & -1 & 0 & 0 \\
            0 &  0 & 1 & 0 \\
            0 &  0 & 0 & 1
        \end{bmatrix}
        \begin{bmatrix}
            \frac{2}{r + l} &  0               & 0               & -\frac{r - l}{r + l} \\
            0               & -\frac{2}{t + b} & 0               & -\frac{t - b}{t + b} \\
            0               &  0               & \frac{1}{f - n} & -\frac{n}{f - n}     \\
            0               &  0               & 0               &  1
        \end{bmatrix}
        \\
    &= \begin{bmatrix}
            1 &  0 & 0 & 0 \\
            0 & -1 & 0 & 0 \\
            0 &  0 & 1 & 0 \\
            0 &  0 & 0 & 1
        \end{bmatrix}
        \begin{bmatrix}
            \frac{2}{r + l} &  0               & 0               & -\frac{r - l}{r + l} \\
            0               & -\frac{2}{b + t} & 0               &  \frac{b - t}{b + t} \\
            0               &  0               & \frac{1}{f - n} & -\frac{n}{f - n}     \\
            0               &  0               & 0               &  1
        \end{bmatrix}
        \\
    &=  \begin{bmatrix}
            \frac{2}{r + l} & 0               & 0               & -\frac{r - l}{r + l}  \\
            0               & \frac{2}{b + t} & 0               & -\frac{b - t}{b + t}  \\
            0               & 0               & \frac{1}{f - n} & -\frac{n}{f - n}      \\
            0               & 0               & 0               &  1
        \end{bmatrix}
        \\
    &=  \begin{bmatrix}
            \frac{2}{r - \left( -l \right)} & 0                               & 0               & -\frac{r + \left( -l \right)}{r - \left( -l \right)}  \\
            0                               & \frac{2}{b - \left( -t \right)} & 0               & -\frac{b + \left( -t \right)}{b - \left( -t \right)}  \\
            0                               & 0                               & \frac{1}{f - n} & -\frac{n}{f - n}                                      \\
            0                               & 0                               & 0               &  1
        \end{bmatrix}
```

so

```{math}
M^{Vulkan}_{orth, rh \rightarrow rh} = 
    \begin{bmatrix}
        \frac{2}{r - \left( -l \right)} & 0                               & 0               & -\frac{r + \left( -l \right)}{r - \left( -l \right)}  \\
        0                               & \frac{2}{b - \left( -t \right)} & 0               & -\frac{b + \left( -t \right)}{b - \left( -t \right)}  \\
        0                               & 0                               & \frac{1}{f - n} & -\frac{n}{f - n}                                      \\
        0                               & 0                               & 0               &  1
    \end{bmatrix}
```

Symmetric Vertical Field Of View Perspective Projection

```{math}
M^{Vulkan}_{per,vfov,rh \rightarrow rh}
    &= \Omega_{lh \rightarrow rh} \left(Q^{Vulkan}_{lh}\right)^{-1} M^{C,Vulkan}_{per,vfov} \Omega_{rh \rightarrow lh} Q^{Vulkan}_{rh} \\
    &= \left( \Omega_{lh \rightarrow rh} \left(Q^{Vulkan}_{lh}\right)^{-1} \right) M^{C,Vulkan}_{per,vfov} \left( \Omega_{rh \rightarrow lh} Q^{Vulkan}_{rh} \right) \\
    &= \left( 
            \begin{bmatrix}
                1 & 0 &  0 & 0 \\
                0 & 1 &  0 & 0 \\
                0 & 0 & -1 & 0 \\
                0 & 0 &  0 & 1
            \end{bmatrix}
            \begin{bmatrix}
                1 &  0 &  0 & 0 \\
                0 & -1 &  0 & 0 \\
                0 &  0 & -1 & 0 \\
                0 &  0 &  0 & 1
            \end{bmatrix}
        \right)
        M^{C,Vulkan}_{per,vfov}
        \left(
            \begin{bmatrix}
                1 & 0 &  0 & 0 \\
                0 & 1 &  0 & 0 \\
                0 & 0 & -1 & 0 \\
                0 & 0 &  0 & 1
            \end{bmatrix}
            \begin{bmatrix}
                1 &  0 &  0 & 0 \\
                0 & -1 &  0 & 0 \\
                0 &  0 & -1 & 0 \\
                0 &  0 &  0 & 1
            \end{bmatrix}
        \right)
        \\
    &= \begin{bmatrix}
            1 &  0 & 0 & 0 \\
            0 & -1 & 0 & 0 \\
            0 &  0 & 1 & 0 \\
            0 &  0 & 0 & 1
        \end{bmatrix}
        M^{C,Vulkan}_{per,vfov}
        \begin{bmatrix}
            1 &  0 & 0 & 0 \\
            0 & -1 & 0 & 0 \\
            0 &  0 & 1 & 0 \\
            0 &  0 & 0 & 1
        \end{bmatrix}
        \\
    &= \begin{bmatrix}
            1 &  0 & 0 & 0 \\
            0 & -1 & 0 & 0 \\
            0 &  0 & 1 & 0 \\
            0 &  0 & 0 & 1
        \end{bmatrix}
        \begin{bmatrix}
            \frac{1}{aspect \cdot \tan\left( \frac{\theta_{vfov}}{2} \right)} 
            & 0 
            & 0
            & 0 \\

            0 
            & \frac{1}{\tan\left( \frac{\theta_{vfov}}{2} \right)}
            & 0
            & 0 \\

            0 
            &  0 
            &  \frac{f}{f - n}
            & -\frac{ f n }{f - n} \\
    
            0 
            & 0 
            & 1 
            & 0
        \end{bmatrix}
        \begin{bmatrix}
            1 &  0 & 0 & 0 \\
            0 & -1 & 0 & 0 \\
            0 &  0 & 1 & 0 \\
            0 &  0 & 0 & 1
        \end{bmatrix}
        \\
    &= \begin{bmatrix}
            1 &  0 & 0 & 0 \\
            0 & -1 & 0 & 0 \\
            0 &  0 & 1 & 0 \\
            0 &  0 & 0 & 1
        \end{bmatrix}
        \begin{bmatrix}
            \frac{1}{aspect \cdot \tan\left( \frac{\theta_{vfov}}{2} \right)} 
            & 0 
            & 0
            & 0 \\

            0 
            & -\frac{1}{\tan\left( \frac{\theta_{vfov}}{2} \right)}
            &  0
            &  0 \\

            0 
            &  0 
            &  \frac{f}{f - n}
            & -\frac{ f n }{f - n} \\
    
            0 
            & 0 
            & 1 
            & 0
        \end{bmatrix}
        \\
    &=  \begin{bmatrix}
            \frac{1}{aspect \cdot \tan\left( \frac{\theta_{vfov}}{2} \right)} 
            & 0 
            & 0
            & 0 \\

            0 
            & \frac{1}{\tan\left( \frac{\theta_{vfov}}{2} \right)}
            & 0
            & 0 \\

            0 
            &  0 
            &  \frac{f}{f - n}
            & -\frac{ f n }{f - n} \\
    
            0 
            & 0 
            & 1 
            & 0
        \end{bmatrix}
```

so

```{math}
M^{Vulkan}_{per,vfov,rh \rightarrow rh} = 
    \begin{bmatrix}
        \frac{1}{aspect \cdot \tan\left( \frac{\theta_{vfov}}{2} \right)} 
        & 0 
        & 0
        & 0 \\

        0 
        & \frac{1}{\tan\left( \frac{\theta_{vfov}}{2} \right)}
        & 0
        & 0 \\

        0 
        &  0 
        &  \frac{f}{f - n}
        & -\frac{ f n }{f - n} \\
    
        0 
        & 0 
        & 1 
        & 0
    \end{bmatrix}
```


### Left-Handed View Space

```{math}
R_{x}\left( \theta \right) = 
    \begin{bmatrix}
        1 & 0                         &  0                         & 0 \\
        0 & \cos\left( \theta \right) & -\sin\left( \theta \right) & 0 \\
        0 & \sin\left( \theta \right) &  \cos\left( \theta \right) & 0 \\
        0 & 0                         &  0                         & 1
    \end{bmatrix}
```

```{math}
Q^{Vulkan}_{lh} = R_{x}\left( \pi \right) 
                = \begin{bmatrix}
                      1 & 0                      &  0                   & 0 \\
                      0 & \cos\left( \pi \right) & -\sin\left( \pi \right) & 0 \\
                      0 & \sin\left( \pi \right) &  \cos\left( \pi \right) & 0 \\
                      0 & 0                      &  0                   & 1
                  \end{bmatrix}
                = \begin{bmatrix}
                      1 &  0 &  0 & 0 \\
                      0 & -1 &  0 & 0 \\
                      0 &  0 & -1 & 0 \\
                      0 &  0 &  0 & 1
                  \end{bmatrix}
```

Perspective Projection

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
                0 & 0 &  0 & 1
            \end{bmatrix}
            \begin{bmatrix}
                1 &  0 &  0 & 0 \\
                0 & -1 &  0 & 0 \\
                0 &  0 & -1 & 0 \\
                0 &  0 &  0 & 1
            \end{bmatrix}
        \right)
        M^{C,Vulkan}_{per}
        \begin{bmatrix}
            1 &  0 &  0 & 0 \\
            0 & -1 &  0 & 0 \\
            0 &  0 & -1 & 0 \\
            0 &  0 &  0 & 1
        \end{bmatrix}
        \\
    &= \begin{bmatrix}
            1 &  0 & 0 & 0 \\
            0 & -1 & 0 & 0 \\
            0 &  0 & 1 & 0 \\
            0 &  0 & 0 & 1
        \end{bmatrix}
        M^{C,Vulkan}_{per}
        \begin{bmatrix}
            1 &  0 &  0 & 0 \\
            0 & -1 &  0 & 0 \\
            0 &  0 & -1 & 0 \\
            0 &  0 &  0 & 1
        \end{bmatrix}
        \\
    &= \begin{bmatrix}
            1 &  0 & 0 & 0 \\
            0 & -1 & 0 & 0 \\
            0 &  0 & 1 & 0 \\
            0 &  0 & 0 & 1
        \end{bmatrix}
        \begin{bmatrix}
            \frac{ 2 n }{r - \left( -l \right)} & 0                                   & -\frac{r + \left( -l \right)}{r - \left( -l \right)} &  0                  \\
            0                                   & \frac{ 2 n }{t - \left( -b \right)} & -\frac{t + \left( -b \right)}{t - \left( -b \right)} &  0                  \\
            0                                   & 0                                   &  \frac{f}{f - n}                                     & -\frac{f n }{f - n} \\
            0                                   & 0                                   &  1                                                   &  0
        \end{bmatrix}
        \begin{bmatrix}
            1 &  0 &  0 & 0 \\
            0 & -1 &  0 & 0 \\
            0 &  0 & -1 & 0 \\
            0 &  0 &  0 & 1
        \end{bmatrix}
        \\
    &= \begin{bmatrix}
            1 &  0 & 0 & 0 \\
            0 & -1 & 0 & 0 \\
            0 &  0 & 1 & 0 \\
            0 &  0 & 0 & 1
        \end{bmatrix}
        \begin{bmatrix}
            \frac{ 2 n }{r - \left( -l \right)} &  0                                   &  \frac{r + \left( -l \right)}{r - \left( -l \right)} &  0                  \\
            0                                   & -\frac{ 2 n }{t - \left( -b \right)} &  \frac{t + \left( -b \right)}{t - \left( -b \right)} &  0                  \\
            0                                   &  0                                   & -\frac{f}{f - n}                                     & -\frac{f n }{f - n} \\
            0                                   &  0                                   & -1                                                   &  0
        \end{bmatrix}
        \\
    &=  \begin{bmatrix}
            1 &  0 & 0 & 0 \\
            0 & -1 & 0 & 0 \\
            0 &  0 & 1 & 0 \\
            0 &  0 & 0 & 1
        \end{bmatrix}
        \begin{bmatrix}
            \frac{ 2 n }{r + l} &  0                   &  \frac{r - l}{r + l} &  0                  \\
            0                   & -\frac{ 2 n }{t + b} &  \frac{t - b}{t + b} &  0                  \\
            0                   &  0                   & -\frac{f}{f - n}     & -\frac{f n }{f - n} \\
            0                   &  0                   & -1                   &  0
        \end{bmatrix}
        \\
    &=  \begin{bmatrix}
            1 &  0 & 0 & 0 \\
            0 & -1 & 0 & 0 \\
            0 &  0 & 1 & 0 \\
            0 &  0 & 0 & 1
        \end{bmatrix}
        \begin{bmatrix}
            \frac{ 2 n }{r + l} &  0                   &  \frac{r - l}{r + l} &  0                  \\
            0                   & -\frac{ 2 n }{b + t} & -\frac{b - t}{b + t} &  0                  \\
            0                   &  0                   & -\frac{f}{f - n}     & -\frac{f n }{f - n} \\
            0                   &  0                   & -1                   &  0
        \end{bmatrix}
        \\
    &=  \begin{bmatrix}
            \frac{ 2 n }{r + l} & 0                   &  \frac{r - l}{r + l} &  0                  \\
            0                   & \frac{ 2 n }{b + t} &  \frac{b - t}{b + t} &  0                  \\
            0                   & 0                   & -\frac{f}{f - n}     & -\frac{f n }{f - n} \\
            0                   & 0                   & -1                   &  0
        \end{bmatrix}
        \\
    &=  \begin{bmatrix}
            \frac{ 2 n }{r - \left( -l \right)} & 0                                   &  \frac{r + \left( -l \right)}{r - \left( -l \right)} &  0                  \\
            0                                   & \frac{ 2 n }{b - \left( -t \right)} &  \frac{b + \left( -t \right)}{b - \left( -t \right)} &  0                  \\
            0                                   & 0                                   & -\frac{f}{f - n}                                     & -\frac{f n }{f - n} \\
            0                                   & 0                                   & -1                                                   &  0
        \end{bmatrix}
```

so

```{math}
M^{Vulkan}_{per, lh \rightarrow rh} = 
    \begin{bmatrix}
        \frac{ 2 n }{r - \left( -l \right)} & 0                                   &  \frac{r + \left( -l \right)}{r - \left( -l \right)} &  0                  \\
        0                                   & \frac{ 2 n }{b - \left( -t \right)} &  \frac{b + \left( -t \right)}{b - \left( -t \right)} &  0                  \\
        0                                   & 0                                   & -\frac{f}{f - n}                                     & -\frac{f n }{f - n} \\
        0                                   & 0                                   & -1                                                   &  0
    \end{bmatrix}
```

Calculation for orthographic projection

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
                0 & 0 &  0 & 1
            \end{bmatrix}
            \begin{bmatrix}
                1 &  0 &  0 & 0 \\
                0 & -1 &  0 & 0 \\
                0 &  0 & -1 & 0 \\
                0 &  0 &  0 & 1
            \end{bmatrix}
        \right)
        M^{C,Vulkan}_{orth}
        \begin{bmatrix}
            1 &  0 &  0 & 0 \\
            0 & -1 &  0 & 0 \\
            0 &  0 & -1 & 0 \\
            0 &  0 &  0 & 1
        \end{bmatrix}
        \\
    &= \begin{bmatrix}
            1 &  0 & 0 & 0 \\
            0 & -1 & 0 & 0 \\
            0 &  0 & 1 & 0 \\
            0 &  0 & 0 & 1
        \end{bmatrix}
        M^{C,Vulkan}_{orth}
        \begin{bmatrix}
            1 &  0 &  0 & 0 \\
            0 & -1 &  0 & 0 \\
            0 &  0 & -1 & 0 \\
            0 &  0 &  0 & 1
        \end{bmatrix}
        \\
    &= \begin{bmatrix}
            1 &  0 & 0 & 0 \\
            0 & -1 & 0 & 0 \\
            0 &  0 & 1 & 0 \\
            0 &  0 & 0 & 1
        \end{bmatrix}
        \begin{bmatrix}
            \frac{2}{r - \left( -l \right)} & 0                               & 0               & -\frac{r + \left( -l \right)}{r - \left( -l \right)} \\
            0                               & \frac{2}{t - \left( -b \right)} & 0               & -\frac{t + \left( -b \right)}{t - \left( -b \right)} \\
            0                               & 0                               & \frac{1}{f - n} & -\frac{n}{f - n}                                     \\
            0                               & 0                               & 0               &  1
        \end{bmatrix}
        \begin{bmatrix}
            1 &  0 &  0 & 0 \\
            0 & -1 &  0 & 0 \\
            0 &  0 & -1 & 0 \\
            0 &  0 &  0 & 1
        \end{bmatrix}
        \\
    &= \begin{bmatrix}
            1 &  0 & 0 & 0 \\
            0 & -1 & 0 & 0 \\
            0 &  0 & 1 & 0 \\
            0 &  0 & 0 & 1
        \end{bmatrix}
        \begin{bmatrix}
            \frac{2}{r - \left( -l \right)} &  0                               &  0               & -\frac{r + \left( -l \right)}{r - \left( -l \right)} \\
            0                               & -\frac{2}{t - \left( -b \right)} &  0               & -\frac{t + \left( -b \right)}{t - \left( -b \right)} \\
            0                               &  0                               & -\frac{1}{f - n} & -\frac{n}{f - n}                                     \\
            0                               &  0                               &  0               &  1
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
            0               &  0               &  0               &  1
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
            0               & -\frac{2}{b + t} &  0               &  \frac{b - t}{b + t} \\
            0               &  0               & -\frac{1}{f - n} & -\frac{n}{f - n}     \\
            0               &  0               &  0               &  1
        \end{bmatrix}
        \\
    &=  \begin{bmatrix}
            \frac{2}{r + l} & 0               &  0               & -\frac{r - l}{r + l}  \\
            0               & \frac{2}{b + t} &  0               & -\frac{b - t}{b + t}  \\
            0               & 0               & -\frac{1}{f - n} & -\frac{n}{f - n}      \\
            0               & 0               &  0               &  1
        \end{bmatrix}
        \\
    &=  \begin{bmatrix}
            \frac{2}{r - \left( -l \right)} & 0                               &  0               & -\frac{r + \left( -l \right)}{r - \left( -l \right)}  \\
            0                               & \frac{2}{b - \left( -t \right)} &  0               & -\frac{b + \left( -t \right)}{b - \left( -t \right)}  \\
            0                               & 0                               & -\frac{1}{f - n} & -\frac{n}{f - n}                                      \\
            0                               & 0                               &  0               &  1
        \end{bmatrix}
```

so

```{math}
M^{Vulkan}_{orth, lh \rightarrow rh} = 
    \begin{bmatrix}
        \frac{2}{r - \left( -l \right)} & 0                               &  0               & -\frac{r + \left( -l \right)}{r - \left( -l \right)}  \\
        0                               & \frac{2}{b - \left( -t \right)} &  0               & -\frac{b + \left( -t \right)}{b - \left( -t \right)}  \\
        0                               & 0                               & -\frac{1}{f - n} & -\frac{n}{f - n}                                      \\
        0                               & 0                               &  0               &  1
    \end{bmatrix}
```

Symmetric Vertical Field Of View Perspective Projection

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
                0 & 0 &  0 & 1
            \end{bmatrix}
            \begin{bmatrix}
                1 &  0 &  0 & 0 \\
                0 & -1 &  0 & 0 \\
                0 &  0 & -1 & 0 \\
                0 &  0 &  0 & 1
            \end{bmatrix}
        \right)
        M^{C,Vulkan}_{per,vfov}
        \begin{bmatrix}
            1 &  0 &  0 & 0 \\
            0 & -1 &  0 & 0 \\
            0 &  0 & -1 & 0 \\
            0 &  0 &  0 & 1
        \end{bmatrix}
        \\
    &= \begin{bmatrix}
            1 &  0 & 0 & 0 \\
            0 & -1 & 0 & 0 \\
            0 &  0 & 1 & 0 \\
            0 &  0 & 0 & 1
        \end{bmatrix}
        M^{C,Vulkan}_{per,vfov}
        \begin{bmatrix}
            1 &  0 &  0 & 0 \\
            0 & -1 &  0 & 0 \\
            0 &  0 & -1 & 0 \\
            0 &  0 &  0 & 1
        \end{bmatrix}
        \\
    &= \begin{bmatrix}
            1 &  0 & 0 & 0 \\
            0 & -1 & 0 & 0 \\
            0 &  0 & 1 & 0 \\
            0 &  0 & 0 & 1
        \end{bmatrix}
        \begin{bmatrix}
            \frac{1}{aspect \cdot \tan\left( \frac{\theta_{vfov}}{2} \right)} 
            & 0 
            & 0
            & 0 \\

            0 
            & \frac{1}{\tan\left( \frac{\theta_{vfov}}{2} \right)}
            & 0
            & 0 \\

            0 
            &  0 
            &  \frac{f}{f - n}
            & -\frac{ f n }{f - n} \\
    
            0 
            & 0 
            & 1 
            & 0
        \end{bmatrix}
        \begin{bmatrix}
            1 &  0 &  0 & 0 \\
            0 & -1 &  0 & 0 \\
            0 &  0 & -1 & 0 \\
            0 &  0 &  0 & 1
        \end{bmatrix}
        \\
    &= \begin{bmatrix}
            1 &  0 & 0 & 0 \\
            0 & -1 & 0 & 0 \\
            0 &  0 & 1 & 0 \\
            0 &  0 & 0 & 1
        \end{bmatrix}
        \begin{bmatrix}
            \frac{1}{aspect \cdot \tan\left( \frac{\theta_{vfov}}{2} \right)} 
            & 0 
            & 0
            & 0 \\

            0 
            & -\frac{1}{\tan\left( \frac{\theta_{vfov}}{2} \right)}
            &  0
            &  0 \\

            0 
            &  0 
            & -\frac{f}{f - n}
            & -\frac{ f n }{f - n} \\
    
            0 
            &  0 
            & -1 
            &  0
        \end{bmatrix}
        \\
    &=  \begin{bmatrix}
            \frac{1}{aspect \cdot \tan\left( \frac{\theta_{vfov}}{2} \right)} 
            & 0 
            & 0
            & 0 \\

            0 
            & \frac{1}{\tan\left( \frac{\theta_{vfov}}{2} \right)}
            & 0
            & 0 \\

            0 
            &  0 
            & -\frac{f}{f - n}
            & -\frac{ f n }{f - n} \\
    
            0 
            &  0 
            & -1 
            &  0
        \end{bmatrix}
```

so

```{math}
M^{Vulkan}_{per,vfov,rh \rightarrow rh} = 
    \begin{bmatrix}
        \frac{1}{aspect \cdot \tan\left( \frac{\theta_{vfov}}{2} \right)} 
        & 0 
        & 0
        & 0 \\

        0 
        & \frac{1}{\tan\left( \frac{\theta_{vfov}}{2} \right)}
        & 0
        & 0 \\

        0 
        &  0 
        & -\frac{f}{f - n}
        & -\frac{ f n }{f - n} \\
    
        0 
        &  0 
        & -1 
        &  0
    \end{bmatrix}
```
