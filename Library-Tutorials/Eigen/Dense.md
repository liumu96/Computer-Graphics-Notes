## The Matrix class

In Eigen, all matrices and vectors are objects of the **Matrix** template class. **Vectors** are just a special case of matrices, with either 1 row or 1 column.

### The first three template parameters of Matrix

```cpp
Matrix<typename Scalar, int RowsAtCompileTime, int ColsAtCompileTime>
```

- Scalar is the scalar type, i.e. the type of the coefficients.
- RowsAtCompileTime and ColsAtCompileTime are the number of rows and columns of the matrix as known at compile time.

We offer a lot of convenience typedefs to cover the usual cases. For example

```cpp
typedef Matrix<float, 4, 4> Matrix4f;
```

### Vectors

As mentioned above, in Eigen, vectors are just a special case of matrices, with either 1 row or 1 column. The case where they have 1 column is the most common; such vectors are called **column-vectors**, often abbreviated as just vectors. In the other case where they have 1 row, they are called **row-vectors**.

```cpp
typedef Matrix<float, 3, 1> Vector3f;
typedef Matrix<int, 1, 2> RowVector2i;
```

### The special value Dynamic

The RowsAtCompileTime and ColsAtCompileTime template parameters can take the special value Dynamic which indicates that the size is unknown at compile time, so must be handled as a run-time variable.

In Eigen terminology, such a size is referred to as a dynamic size; while a size that is known at compile time is called a fixed size. For example, the convenience typedef `MatrixXd`, meaning a matrix of doubles with dynamic size, is defined as follows:

```cpp
typedef Matrix<double, Dynamic, Dynamic> MatrixXd;
```

And similarly, we define a self-explanatory typedef VectorXi as follows:

```cpp
typedef Matrix<int, Dynamic, 1> VectorXi;
```

### Constructors

A default constructor is always available, never performs any dynamic memory allocation, and never initializes the matrix coefficients.

```cpp
Matrix3f a;
MatrixXf b;
```

Here,

- `a` is a 3-by-3 matrix, with a plain float[9] array of uninitialized coefficients,
- `b` is a dynamic-size matrix whose size is currently 0-by-0, and whose array of coefficients hasn't yet been allocated at all.

### Fixed vs. Dynamic size

Use fixed sizes for very small sizes where you can, and use dynamic sizes for larger sizes or where you have to.

### Optional template parameters

```cpp
Matrix<typename Scalar,
       int RowsAtCompileTime,
       int ColsAtCompileTime,
       int Options = 0,
       int MaxRowsAtCompileTime = RowsAtCompileTime,
       int MaxColsAtCompileTime = ColsAtCompileTime>
```

### Convenience typedefs

- `MatrixNt` for `Matrix<type, N, N>`
  ```cpp
  MatrixXi for Matrix<int, Dynamic, Dynamic>
  ```
- `MatrixNXt` for `Matrix<type, N, Dynamic>`

  ```cpp
  MatrixX3i for Matrix<int, Dynamic, 3>
  ```

- `MatrixXNt` for `Matrix<type, Dynamic, N>`
  ```cpp
  MatrixX3i for Matrix<int, 3, Dynamic>
  ```
- `VectorNt` for `Matrix<type, N, 1>`
  ```cpp
  Vector2f for Matrix<float, 2, 1>
  ```
- `RowVectorNt` for `Matrix<type, 1, N>`
  `cpp
VRowVector3d for Matrix<double, 1, 3>
`
  Where:
- N can be any one of 2, 3, 4, or X (meaning Dynamic).
- t can be any one of i (meaning int), f (meaning float), d (meaning double), cf (meaning complex<float>), or cd (meaning complex<double>).
