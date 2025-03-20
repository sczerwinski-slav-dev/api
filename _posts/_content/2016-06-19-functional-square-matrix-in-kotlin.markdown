> I would have absolutely messed up ‘The Matrix’.
>
> —Will Smith

Working with OpenGL and 3D graphics would be impossible without
[affine transformations][wiki:affine_transform],
which in general can be represented as:

$$\vec{y} = \boldsymbol{A} \vec{x} + \vec{a}$$

where $$\vec{a}$$ is a translation vector, and $$\boldsymbol{A}$$ is
a matrix representation of a [linear mapping][wiki:linear_map].

The same transformation might also be written as a single multiplication
of an augmented matrix (also called an _affine transformation matrix_)
and an augmented vector:

$$
\begin{bmatrix}\vec{y}\\1\end{bmatrix} = \left[\begin{array}{ccc|c}
  & \boldsymbol{A} & & \vec{a} \\
  0 & … & 0 & 1
\end{array}\right] \begin{bmatrix}\vec{x}\\1\end{bmatrix}
$$

In 3D space, an augmented vector consists of 4 coordinates: $$(x, y, z, 1)$$,
while the size of an augmented matrix is 4×4.

Using this representation, we can compose affine transformations by simply
multiplying augmented matrices: 

$$
\begin{bmatrix}\vec{y}\\1\end{bmatrix} = \left[\begin{array}{ccc|c}
  & \boldsymbol{A} & & \vec{a} \\
  0 & … & 0 & 1
\end{array}\right] \left[\begin{array}{ccc|c}
  & \boldsymbol{B} & & \vec{b} \\
  0 & … & 0 & 1
\end{array}\right] \begin{bmatrix}\vec{x}\\1\end{bmatrix}
$$

## The problem: Normal matrix

While matrix multiplication is a relatively simple operation, computing
a so-called _normal matrix_ proves to be quite challenging.

When a 3D model is transformed using an augmented matrix $$\boldsymbol{A}$$,
the [normals][wiki:normals] should be transformed using a normal matrix
$$\boldsymbol{N}$$ defined as follows:

$$
\boldsymbol{N} = {\big(\boldsymbol{A}^{-1}\big)}^\mathrm{T} =
  {\big(\boldsymbol{A}^\mathrm{T}\big)}^{-1}
$$

This calculation could be done in a single line of a vertex shader:

```glsl
mat4 normalMatrix = transpose(inverse(modelViewMatrix));
```

However, this would mean that the GPU should perform the same operation
for each vertex. Hence, it is much better to calculate normal matrix only
once per each frame with the CPU instead.

Unfortunately, a typical implementation of
[matrix inversion][grepcode:matrix_inv] is long and hard to understand.

## Functional implementation of a square matrix

After studying Wikipedia, I have come up with a nice and clear algorithm
for matrix inversion. Since multiple matrices are used in the course of this
calculation, it is better to avoid copying the entries every time a new matrix
is created. Thus, I have decided to use a functional approach to the problem.

Let’s start with creating a class representing a square matrix of a specific
size _n_×_n_, the elements of which are defined by a function:

$$f: \{1, 2 \ldots n\}^2 \to \Bbb{R}$$

We may implement such a matrix as:

```kotlin
class SquareMatrix(val size: Int, private val elements: (Int, Int) -> Float) {
    operator fun get(row: Int, col: Int): Float {
        require(row in 0..size - 1) { "Row ${row} out of bounds: 0..${size - 1}" }
        require(col in 0..size - 1) { "Column ${col} out of bounds: 0..${size - 1}" }
        return elements(row, col)
    }
}
```

The `elements` function, provided in the constructor, returns a matrix entry
in the specified row and column, e.g. identity matrix $$\boldsymbol{I}_n$$
of a requested size can be defined as:

```kotlin
fun identity(size: Int) = SquareMatrix(size) { row, col ->
    if (row == col) 1f else 0f
}
```

As you may have noticed, both `row` and `col` are required to fit into range
of 0 to _n_−1. That’s because, by convention, in computer programming indices
start with 0. It is not a problem though, as this shift does not influence
any of our calculations.

Now, let’s try to define some operations on the matrix.

### Matrix transposition

The [transpose][wiki:transpose] of the matrix can be created
by simply swapping indices of the rows and the columns:

$$
\left[\boldsymbol{A}^\mathrm{T}\right]_{i,j} =
  \left[\boldsymbol{A}\right]_{j,i}
$$

So the method responsible for this operation can be defined as follows:

```kotlin
fun transpose(): SquareMatrix = SquareMatrix(size) { row, col -> this[col, row] }
```

Note that the transpose does not copy entries from the original matrix,
but rather refers to them.

### Determinant

Before we can go any further, we need to calculate the
[determinant][wiki:determinant] of the matrix.

Let’s start with the simplest case of a (rather degenerated) 1×1 matrix.
Wikipedia does not describe, how to perform the calculation in this case,
but we may try to infer the formula from determinant’s properties.

For _n_×_n_ matrices, the following statements are always true:

$$\mathrm{det}(\boldsymbol{I}_n) = 1$$

$$\mathrm{det}(c\boldsymbol{A}) = c^n \mathrm{det}(\boldsymbol{A})$$

On the other hand, for any 1×1 matrix $$\boldsymbol{A}$$ we can say:

$$\boldsymbol{A} = a_{1,1} \boldsymbol{I}_1$$

Therefore, we can calculate the determinant of a 1×1 matrix as:

$$
\mathrm{det}(\boldsymbol{A}) =
\mathrm{det}(a_{1,1} \boldsymbol{I}_1) =
\left(a_{1,1}\right)^1 \mathrm{det}(\boldsymbol{I}_1) =
a_{1,1} \cdot 1 =
a_{1,1}
$$

For any larger _n_×_n_ matrix, the determinant can be defined as
a sum of all elements in the first row multiplied by respective cofactors:

$$\mathrm{det}(\boldsymbol{A}) = \sum_{j=1}^n a_{1,j} C_{1,j}$$

So, taking both cases into account, the implementation should look like this:

```kotlin
val det: Float by lazy {
    if (size == 1) this[0, 0]
    else (0..size - 1).map { item -> this[0, item] * comatrix[0, item] }.sum()
}
```

Lazily initialized value is used instead of a function
to prevent the same determinant from being calculated multiple times.

### Cofactors and comatrix

To calculate the determinant, we’ve used a _comatrix_ or, in other words,
a matrix of _cofactors_:

```kotlin
private val comatrix: SquareMatrix by lazy {
    SquareMatrix(size) { row, col -> cofactor(row, col) }
}
```

A cofactor is a _first minor_ of the matrix ($$M_{i,j}$$) multiplied by
$$(-1)^{i+j}$$, where $$i$$ and $$j$$ are indices of the row and the column
of the matrix:

$$C_{i,j} = (-1)^{i+j} M_{i,j}$$

```kotlin
private fun cofactor(row: Int, col: Int): Float =
        minor(row, col) * if ((row + col) % 2 == 0) 1f else -1f
```

Even though the indices have been shifted, it happened both for row and
for column. So the parity of the sum `row + col` is still preserved.

### First minor

A [first minor][wiki:first_minor] $$M_{i,j}$$ is the determinant
of the submatrix created by removing _i_-th row and _j_-th column
from the original matrix.

```kotlin
private fun minor(row: Int, col: Int): Float = sub(row, col).det
```

```kotlin
private fun sub(delRow: Int, delCol: Int) = SquareMatrix(size - 1) { row, col ->
    this[if (row < delRow) row else row + 1, if (col < delCol) col else col + 1]
}
```

As you may have noticed, the algorithm for finding the determinant used
in `SquareMatrix` is recursive. This fact may cause risks related to stack
space. However, since in 3D graphics only 4×4 matrices are used, we may
be sure that nested method calls will not lead to stack overflow.

### Adjugate matrix

Now, we can find the [adjugate matrix][wiki:adjugate_matrix], which is simply
a transpose of the comatrix:

$$\mathrm{adj}(\boldsymbol{A}) = \boldsymbol{C}^\mathrm{T}$$

```kotlin
val adj: SquareMatrix by lazy { comatrix.transpose() }
```

### Matrix inversion

With the determinant and the adjugate matrix, we have all the elements
needed to finally perform matrix inversion:

$$
\boldsymbol{A}^{-1} = \dfrac{1}{\mathrm{det}(\boldsymbol{A})}
  \mathrm{adj}(\boldsymbol{A})
$$

```kotlin
fun inverse(): SquareMatrix = SquareMatrix(size) { row, col -> adj[row, col] / det }
```

## Usage

The matrix inversion algorithm is ready. Now, let’s try to use it.

As an example, we will use a [diagonal matrix][wiki:diagonal_matrix],
which should give some predictable results:

```kotlin
val diag = SquareMatrix(4) { row, col ->
    if (row == col) (row + 1).toFloat() else 0f
}
```

The matrix `diag` should look like this:

$$
\begin{bmatrix}
    1 & 0 & 0 & 0 \\
    0 & 2 & 0 & 0 \\
    0 & 0 & 3 & 0 \\
    0 & 0 & 0 & 4 \\
\end{bmatrix}
$$

Now, let’s create an inverse of this matrix:

```kotlin
val inv = diag.inverse()
```

The result should be as follows:

$$
\begin{bmatrix}
    1 & 0 & 0 & 0 \\
    0 & \frac{1}{2} & 0 & 0 \\
    0 & 0 & \frac{1}{3} & 0 \\
    0 & 0 & 0 & \frac{1}{4} \\
\end{bmatrix}
$$

Let’s verify that by calling:

```kotlin
println(inv[2, 2])
```

In the console, we should receive approximately $$\frac{1}{3}$$:

```
0.33333334
```

---

Full implementation of `SquareMatrix` class can be found at
[GitHub Gist][gist:SquareMatrix].

---

## Credits

- Will Smith quote comes from [BrainyQuote][quote:matrix]


[wiki:affine_transform]: https://en.wikipedia.org/wiki/Affine_transformation
[wiki:linear_map]: https://en.wikipedia.org/wiki/Linear_map
[wiki:normals]: https://en.wikipedia.org/wiki/Normal_(geometry)
[wiki:transpose]: https://en.wikipedia.org/wiki/Transpose
[wiki:determinant]: https://en.wikipedia.org/wiki/Determinant
[wiki:first_minor]: https://en.wikipedia.org/wiki/Minor_(linear_algebra)#First_minors
[wiki:adjugate_matrix]: https://en.wikipedia.org/wiki/Adjugate_matrix
[wiki:diagonal_matrix]: https://en.wikipedia.org/wiki/Diagonal_matrix

[grepcode:matrix_inv]: http://grepcode.com/file/repository.grepcode.com/java/ext/com.google.android/android/4.0.1_r1/android/opengl/Matrix.java#Matrix.invertM%28float%5B%5D%2Cint%2Cfloat%5B%5D%2Cint%29

[gist:SquareMatrix]: https://gist.github.com/sczerwinski/3d98549ebb8c48f7b4b38e898e30fb30

[quote:matrix]: https://www.brainyquote.com/quotes/will_smith_451172
