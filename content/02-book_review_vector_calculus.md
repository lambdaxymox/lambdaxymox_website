---
title: 
    "BOOK REVIEW: Vector Calculus, Linear Algebra, And Differential Forms A Unified Approach: 5th Edition"
subtitle:
    "A Superlative And Rigorous Treatment Of Modern Multivariable Calculus"
authors:
  - name: Lambda Xymox
  - email: lambdaxymox@gmail.com
  - github: https://github.com/lambdaxymox
license: CC-BY-SA-4.0
date: 2025-07-26 23:30:00 +0000
keywords: 
    computer-graphics, computer-science, linear-algebra, rendering, computer-vision, 
    geometric-modeling, projective-geometry, applied-computing
abstract: 
    "A review of the book _Vector Calculus, Linear Algebra, And Differential Forms A 
    Unified Approach, 5th Edition_ by John Hamal Hubbard and Barbara Burke Hubbard 
    published under Matrix Editions. This book provides a superlative and rigorous 
    treatment of modern multivariable calculus and the calculus of differential forms 
    at the right level bridging the pure mathematics of the subject to the applied 
    reality of using mutlivariable calculus to solve real problems."
---

## Introduction

There is a huge hole in the literature for good rigorous treatments of multivariable 
calculus in arbitrarily many dimensions for applied domains. Traditional applied 
treatments aim at dimensions two and three, since the typical audiences are in fields 
like electrical engineering, mechanical engineering, physics, and computer graphics. 
The physical world is 3+1-dimensional, so we typically teach vector algebra and vector 
calculus which only works in two and three spatial dimensions. As a consequence, learning 
proper generalizations of these ideas via the machinery of either tensor calculus, 
differential forms, even just plain matrix calculus tends to be the purview of very 
rarefied domains, such as general relativity, quantum field theory, mathematical physics, 
differential geometry, continuum mechanics, and optimization.

## The Downsides Of The Existing Literature

In contemporary times--with the rise of machine learning in particular--multivariable 
calculus in arbitrarily many dimensions is everywhere, but good pedagogy on 
arbitrarily-dimensional multivariable calculus is severely lacking. One's options tend to 
be either very pure mathematics treatments of the topic, material aimed at theoretical 
physicists, or applied treatments that are surprisingly lacking in problems to chew on
the formalisms with. Given that arbitrarily-dimensional multivariable calculus--matrix 
calculus in particular--is the bread and butter of both contemporary machine learning 
(good luck understanding neural networks without it), and mathematical optimization 
(good luck _training_ machine learning models without that), good rigorous treatments of
multivariable calculus in many dimensions are surprisingly lacking for people outside of 
mathematics or mathematical physics.

The treatments fall into several categories:

1. Rigorous multivariable real analysis or differential geometry texts for mathematicians.
2. Books on general relativity or mathematical physics.
3. Tutorials on matrix calculus for computer scientists, and for working on optimization 
   problems.
4. Applied differential geometry texts.

The quantity of excellent titles in (1) is legion, but these are pure mathematics texts, 
so they tend to reflect the priorities and interests of mathematicians, and possibly 
people adjacent to mathematics, such as mathematical physicists. Real analysis is important 
if one wants to build calculus and measure theory from the ground up, and truly understand 
what the ideas mean, but this tends to be in the interest of developing the mathematics, 
not applying the ideas. In short, they're great for building the formalisms up from first 
principles, but they are not a helpful guide for applying the ideas to practical domains.

The books in category (2) rectify this problem for physicists, since there are numerous 
excellent and careful treatments of differential geometry aimed at physicists nowadays, but 
it is in the interest of doing physics, most of all general relativity, so that creates a 
huge barrier to translating the ideas over to other domains that can benefit from this 
stuff. Moreover, in the context of machine learning, the search spaces in question do not 
tend to have a meaningful notion of manifold structure, so barring doing machine learning in 
domains with symmetries or invariants, it does not always make sense to talk about full bore 
tensor calculus or differential forms in the context of neural networks.

The materials in category (3) tend to be either handwavey tutorials or reference materials. 
I find works in this category fall too much into _here is the formula. Don't think too hard 
about it_ for my tastes. The conceptual underpinnings for why that stuff works are too 
elided for my tastes, especially for a topic as mature as multivariable calculus. The books 
in category (4) tend to have two problems: they're are not rigorous enough to get purchase 
on what the underlying ideas actually mean, and the problems tend to be too sparse to really 
understand how to use the formalism to solve practical problems with.

In summary, books in category (1) are too abstract to build intuition, books in category (4)
are at once too applied to build rigor, _and_ too sparse to build intuition. Works in category
(3) just hand a bunch of formulas and relationships without really developing them at all.
Books in category (2) are aimed mostly at theoretical physicists, doubling the burden or 
learning the physics to get enough purchase on working with the underlying mathematical ideas.
Even more briefly, the reference materials already assume one already knows the underlying 
mathematics, and the other materials don't form a bridge to understanding the mathematics.

## About The Book Itself

Enter _Vector Calculus, Linear Algebra, And Differential Forms: A Unified Approach, 5th Edition_ 
by John Hamal Hubbard and Barbara Book Hubbard. This chunky text aimed at undergraduate 
students develops multivariable calculus from first principles. The book is divided into 
seven chapters and an appendix. The preliminary chapter is a perfunctory chapter on set 
theory and proof techniques that every pure mathematics text has. Chapters one two, three, 
four, and five develop the theory of multivariable calculus itself. The book culminates in 
chapter six with the theory of differential forms. Finally, the rather sizeable appendix 
treats the more detailed theorems and results typically found in a real analysis course, 
though not as fully as a dedicate real analysis book does. This is still a calculus book, 
just a much more advanced one.

The book proceeds with a few organizing principles in mind:

1. Solve a linear algebra problem by posing the problem in the language of linear maps, 
   then apply a computationally effective algorithm to the resulting matrix to yield the 
   result.
2. Multivariable calculus and linear algebra belong together, and should be developed 
   alongside each other.
3. Multivariable calculus is highly (diffeo-)geometric underneath the formalism.

With these principles in mind, the book starts developing the subject in chapter one by
introducing vectors and matrices, then deriving vector algebra as a consequence of linear
algebra. The chapter then treats differentiation properly from the start geometrically 
as the _best possible local linear approximation_ of a function between vector spaces. In 
dimension one, this dispatches a common confusion of the derivative being a number, when 
it is a function, and it properly shows the role of the differential operator as a mapping
between function spaces.

The first payoff comes in the first chapter: armed with the knowledge of the proper
notion of the derivative of a function in arbitrarily many dimensions, one can apply the
derivative operator to functions of all sorts of vector spaces. One of those vector spaces 
is matrices. Even starting from the first chapter, one has enough understanding to work out
all those matrix calculus identities one encounters in a reference such as the 
_Matrix Cookbook_ and understand where those formulas come from, since they are a 
straightforward consequence of applying the theory to matrices.

Chapter two develops a slew of tools for solving matrix equations, and in keeping with 
organizing principle (1), develops the machinery of row reduction, abstract vector spaces, 
and the language abstract linear algebra. The biggest hightlights from this chapter are 
Newton's theorem in many dimensions, and the implicit and inverse function theorems. 
Particularly interesting, the book proves the implicit inverse function theorem using 
Kantorovich's theorem in conjunction with Newton's method. So using a computationally
effective algorithm via Newton's method, the authors prove the inverse function theorem
and provide a concrete value for the radius of the resulting open set given by the theorem.

Chapter three is a grab bag chapter treating Taylor polynomials, quadratic forms, 
extrinsic manifolds, extrema on manifolds, and Lagrange multipliers. Particularly 
delightful in this chapter, the book develops a couple of key theorems about Taylor 
polynomials not seen in most other places. Namely, the results for adding, multiplying, 
and the chain rule for Taylor polynomials. This continues what the correct definition 
of derivative started that the Taylor polynomial of degree {math}`k` is the _best 
possible local polynomial approximation of a function_. This chapter also studies 
curvature, and illuminates the actual meaning a mean curvative and Gaussian curvature.

Chapter four continues with some more linear algebra, and introduces the theory of the 
Riemann integral. In keeping with the theme of the geometric interpretation, this chapter 
elucidates the geometric picture of the determinant as the signed volume of a parallelepiped.
The most unique feature of this chapter is the treatment of Lebesgue integration. This 
book defines Lebesgue integrals Finally the book treats Lebesgue integration in a unique
way by defining Lebesgue integrability in terms of the convergence of Riemnann integrable 
functions. This way, the book develops basic measure theory without needing sigma algebras.

With integration in hand, chapter five treats unoriented volumes of manifolds, and gives 
an illuminating treatment of the meaning of Gaussian curvature and mean curvature on a
manifold.

The real meat of the book is Chapter six: differential forms and vector calculus. The 
chapter begins by discussion orientation, and orientability of manifolds, since 
differential forms require an orientation in order for their integral to be well-defined. 
After defining differential forms and how to integrate them on oriented manifolds, the 
book tackles the exterior derivative. The exterior derivative is the critical operation 
that allows us to generalize the fundamental theorem of calculust to many dimensions. 

The chapter culminates with the statement and proof of the Generalized Stoke's theorem, 
the proper statement of thefundamental theorem of calculus to many dimensions. With the 
exterior derivative and the Generalized Stoke's theorem in hand, the authors succinctly 
derive all the identities of regular vector calculus in terms of the exterior derivative, 
and all the integral theorems of vector calculus from the Generalized Stoke's Theorem. 
Geometrically, the way to generalize vector calculus is to generalize from oriented line 
segments (vectors) to oriented areas, oriented volumes, etc. via exterior algebra. The 
algebra and calculus then takes care of all the particulars in the same unified language. 
The remainder of the chapter applies the language of differential forms to potential theory 
and classical electrodynamics.

Throughout the book, the authors provide ample concrete examples and many exercises and 
problems to solidify understanding the material. The proper notion of differential operator
unifies matrix calculus and vector calculus by showing that it is the same idea underneath.
Since matrix algebras are also vector spaces, the derivative applies to them just as well.

## Conclusion

This book shows the proper way to introduce multivariable calculus, and make the topic 
accessible to a wide variety of fields, including more parts of physics than just 
General relativity and HEP. Exterior algebra and exterior calculus have a trouble history 
with adoption outside a handful of domains going back to Flander's first attempt at it in 
the 1960s. This title is the best all-around introduction to the subject out there, and 
serves as a bridge between more advanced real analysis, differential topology, and 
differential geometry on the one hand, and physics, engineering, and computer science on 
the other hand.
