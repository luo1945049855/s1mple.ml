---
title:  "Matrix"
date:   2020-09-17
categories: Math
---

## Matrix

A Matrix is an array of numbers:

[6  4 29]
[1 -9  8]

## Adding, Negative, Subtracting, Multiply by a constant

To add two matrices: add the numbers in the matching positions.

[3  8] + [4  0] = [7  8]
[4  6]   [1 -9]   [5 -3]

To negative a matrix: negative the numbers in the matching positions.

-[2 -4] = [-2   4]
 [7 10]   [-7 -10]

To subtract two matrices: subtract the numbers in the matching positions.

[3  8] - [4  0] = [-1  8]
[4  6]   [1 -9]   [ 3 15]

We can multiply a matrix by a constant. We call the constant a scalar,
so officially this is called "scalar multiplication"(标量乘法).

2 * [4  0] = [8   0]
    [1 -9]   [2 -18]

## Multiplying by another Matrix

To multiply two matrices together is a bit more difficult.

```math
Result[r,c] = M[r,1] * N[1,c] + M[r,2] * N[2,c] + ... + M[r,column_count] * N[row_count, c];
```

[1  2  3] * [7   8] = [58  #]
[4  5  6]   [9  10]   [ #  #]
            [11 12]

row_count of Result is same to row_count of M.
column_count of Result is same to column_count of N.

### Identity Matrix(单位矩阵)

* It's `square` which has same number of rows as columns.
* It can be large or small (`2*2`, ... , `100*100`).
* It has `1s` on the main diagonal and `0s` everywhere else.
* Its symbol is the capital letter `I`.

It is a `special matrix`, because when we multiply by it, the original is unchanged: 
`A * I = A`; `I * A = A`;

### Order of multiplication

Matrix multiplication isn't `commutative`.

## Dividing

What about division? We don't actually divide matrices, we do it by `multiply by an inverse`.

```math
                        -1
A/B = A * (1/B) = A * B
```

## Inverse of a Matrix

When we multiply a matrix by its inverse we get the `Identity Matrix`. Sometimes there is no inverse at all.

```math
     -1
A * A = I

 -1
A * A = I
```

```math
      -1
[a  b]   = 1/(ad-bc) * [ d  -b]
[c  d]                 [-c   a]

      -1
[4  7]   = 1/(4*6-7*2)[6  -7] = [ 0.6  -0.7]
[2  6]                [-2  4]   [-0.2   0.4]
```

`swap` the a and d, `negative` b and c, and divide everything by the `determinant` of (ad-bc).

### The Inverse may not exist

```math
      -1
[3  4]  = 1/(3*8-4*6)[ 8  -4] = 1/0[#  #]
[6  8]               [-6   3]      [#  #]
```

1/0 is undefined. Such matrix is called singular matrix(奇异矩阵).

### Bigger Matrices

The inverse of a 2x2 is easy ... compared to larger matrices (such as a 3x3, 4x4, etc).

For those larger matrices there are three main methods to work out the inverse:

* [Inverse of a Matrix using Elementary Row Operations (Gauss-Jordan)](https://www.mathsisfun.com/algebra/matrix-inverse-row-operations-gauss-jordan.html)
* [Inverse of a Matrix using Minors, Cofactors and Adjugate](https://www.mathsisfun.com/algebra/matrix-inverse-minors-cofactors-adjugate.html)

## A Real Life Exampple

[A Real Life Example: Bus and Train](https://www.mathsisfun.com/algebra/matrix-inverse.html)

## Transposing(转置)

To "transpose" a matrix, swap the rows and columns.
We put a "T" in the top right-hand corner to mean transpose:

```math
          T
[6  4  24] = [ 6  1]
[1 -9   8]   [ 4 -9]
             [24  8]
```

## Reference

1. [matrix-introduction](https://www.mathsisfun.com/algebra/matrix-introduction.html)
