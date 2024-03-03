# Earth Movers Distance

This repository contains the reference implementation of several algorithms that are described in my paper, "Properties of the d-dimensional earth mover’s problem". 

The optimal value of the objective function of the $d$-dimensional earth mover’s problem can be viewed as a real-valued functional $\phi$ that is defined on normalized and finite multisets. The paper shows that  $\phi$ possesses a variety of useful properties: it is homogeneous, translation invariant, it has a monotonicity property of the form $\mathrm{conv}(X)\subset\mathrm{conv}(Y)$ implies $\phi(X)\leq \phi(Y)$ and $\phi$ is Minkowski additive, i.e.,  $\phi(X)+\phi(Y)=\phi(X+Y)$. We also show that for admissible $X$, solutions to the primal and dual linear programs that define $\phi$ may be generated simultaneously using a single-phase greedy algorithm and that solutions to the dual reflect the geometry of $X$.

This code is free software; you can redistribute it and/or modify it under the terms of the GNU General Public License as published by the Free Software Foundation; 
either version 3 of the License, or (at your option) any later version. https://www.gnu.org/licenses/gpl-3.0.html

The published reference is 

   "Properties of the d-dimensional earth mover’s problem"  
   Discrete Applied Mathematics  
   Volume 265, 31 July 2019, Pages 128-141.  
   https://doi.org/10.1016/j.dam.2019.02.042

```
@article{Kline_Properties_of_the_2019,
author = {Kline, Jeffery},
doi = {10.1016/j.dam.2019.02.042},
journal = {Discrete Applied Mathematics},
month = jul,
number = {1},
pages = {128--141},
title = {{Properties of the d-dimensional earth mover’s problem}},
volume = {265},
year = {2019}
}
```

# Examples

## Shift
```
$ ./emd.shift.py 

Does obj(A) == obj(A+rand(n)*10)? True

A
  |A|: 121
  primal obj:                   4.039692169582531
  dual   obj:                   4.039692169582532
B = A + rand(n)*10
  |B|: 121
  primal obj:                   4.039692169582514
  dual   obj:                   4.039692169582537
```


## Extrema
```
$ ./emd.extrema.py 
COMMENT
  Check for equality, since A and B share extreme vertices

Theory check:
  Is objective A == objective B? True
A
  |A|: 47
  primal obj:                   0.49525109284942603
  dual   obj:                   0.4952510928494265
B
  |B|: 250
  primal obj:                   0.4952510928494266
  dual   obj:                   0.4952510928494265
```

## Convex monotonicity

```
$ ./emd.cvxmonotone.py 

SANITY CHECK:
  is conv(A) \subset conv(B)?: True

COMMENT
  Check for /strict/ inequality, since A is strictly contained in B

Theory check:
  Is objective A < objective B? True
A
  |A|: 100
  primal obj:                   1.836273466603692
  dual   obj:                   1.8362734666036937
B
  |B|: 22
  primal obj:                   6.961358677491533
  dual   obj:                   6.9613586774915355
```


## Minkowski additivity

```
$ ./emd.minkowski.py 

Does obj(A) + obj(B) == obj(A+B)? True

A
  |A|: 10
  primal obj:                   2.203173289573501
  dual   obj:                   2.203173289573501
B
  |B|: 11
  primal obj:                   2.5579575734755813
  dual   obj:                   2.5579575734755795
C = A + B
  |C|: 110
  primal obj:                   4.761130863049078
  dual   obj:                   4.761130863049079
  primal obj(A) + primal obj(B) 4.7611308630490825
```


## Compare the greedy solution against the gold standard
In spite of the singular KKT matrix warnings generated by cvxopt, 
we use the yielded solution since the duality gap is usually small.
It is expected that a better solver would find the optimal 
solution and work significantly faster. 

Also note that the dual solution optimal value is the 
negative of the primal value. This is because the dual
maximizes the problem; when cast as the primal it must be negated.
The output of cvxopt displays the negated objective value. The
final output written at the end of the process is the negative
of that negated objective value, i.e., it aligns with the other
solutions.


Reformulating the problem as the dual is completely 
unnecessary. Surprisingly, the dual solution runs /much/
faster than the primal solution.  This also serves as a 
sanity check on things. I recall that admissible dual solutions
are not unique. However, solutions found through the greedy
method are integer-valued and within each vector of the
dual solutions, adjacent entries differ by at most +/- 1.
```
$ ./emd.cvxopt.py 
     pcost       dcost       gap    pres   dres   k/t
 0:  2.5238e+00 -2.3400e+01  2e+04  4e+01  8e+00  1e+00
 1: -1.5686e+00 -2.1669e+00  4e+02  1e+00  2e-01  1e-02
 2: -5.8214e-01 -8.4289e-01  1e+02  4e-01  8e-02  5e-03
....
14:  1.1463e+00  1.1463e+00  8e-06  3e-08  6e-09  2e-09
Terminated (singular KKT matrix).
     pcost       dcost       gap    pres   dres   k/t
 0:  0.0000e+00 -1.0951e+04  4e+00  1e+00  3e+03  1e+00
 1:  5.8360e-02 -1.0806e+04  3e+02  1e+00  3e+03  8e+01
 2:  7.3950e-01 -8.9710e+03  4e+03  9e-01  3e+03  1e+03
...
14: -1.1463e+00 -1.1464e+00  6e-05  5e-09  2e-05  3e-08
Terminated (singular KKT matrix).
==================================================
Objectives
  greedy primal obj      : 1.1463
  greedy dual   obj      : 1.1463
  cvxopt primal obj      : 1.1463
  cvxopt dual   obj      : 1.1463
  cvxopt dual primal obj : 1.1463
  cvxopt dual dual obj   : 1.1464
```
