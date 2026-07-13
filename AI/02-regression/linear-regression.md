
```
g(X) = y


g(x_i) = y_i


x_i = {x_i_1, x_i_2, .... x_i_n}

g(x_i_1, x_i_2, .... x_i_n) = y_i

```



```python

df_train.iloc[10]

x_i = [300.0, 6.0, 2.0, 28, 20, 3916, 39600]
```

```python

def g(xi):
	# does something
	
	return 10000
```

```
g(xi) = w0 + x_i_1 * w_1 + x_i_2 * w_2 + ... x_i_n * w_n

g(xi) = w0 + sum(w_j * x_i_j)

```

```python
w0 = 0
w = [1, 1, 1, 1, 1, 1, 1]
```
```python

def linear_regression(x_i, w):
	price = + w0
	
	for i in range(len(x_i)):
		price += w[i] * x_i[i]
	
	return price
```



# Linear Regression: From Scalar Equation to Matrix Form

## Step 1. Linear Regression for One Sample

Suppose a sample has \(n\) features:

$$
x_i = [x_{i1}, x_{i2}, \ldots, x_{in}]
$$

The prediction is

$$
g(x_i)=w_0+w_1x_{i1}+w_2x_{i2}+\cdots+w_nx_{in}
$$

or equivalently,

$$
g(x_i)=w_0+\sum_{j=1}^{n}w_jx_{ij}
$$

where

- \(w_0\) is the bias (intercept)
- \(w_1,....,w_n\) are the feature weights

---

## Step 2. Include the Bias as a Feature

Define

$$
x_{i0}=1
$$

Now the feature vector becomes

$$
x_i=
\begin{bmatrix}
1\\
x_{i1}\\
x_{i2}\\
\vdots\\
x_{in}
\end{bmatrix}
$$

and the weight vector becomes

$$
w=
\begin{bmatrix}
w_0\\
w_1\\
w_2\\
\vdots\\
w_n
\end{bmatrix}
$$

The prediction is now

$$
g(x_i)=\sum_{j=0}^{n}w_jx_{ij}
$$

---

## Step 3. Vector Form (Dot Product)

Recall that the dot product is

$$
a^Tb=\sum_{j=0}^{n}a_jb_j
$$

Therefore,

$$
g(x_i)=x_i^Tw
$$

This is exactly the same equation written in vector notation.

---

## Step 4. Example

Suppose

$$
x_i=
\begin{bmatrix}
1\\
4\\
7\\
2
\end{bmatrix}
$$

and

$$
w=
\begin{bmatrix}
5\\
3\\
-2\\
1
\end{bmatrix}
$$

Then

$$
g(x_i)
=
\begin{bmatrix}
1&4&7&2
\end{bmatrix}
\begin{bmatrix}
5\\
3\\
-2\\
1
\end{bmatrix}
$$

which equals

$$
1(5)+4(3)+7(-2)+2(1)
$$

Therefore,

$$
g(x_i)=5
$$

---

# Multiple Samples

Suppose we have \(m\) training examples.

Instead of one feature vector, stack them into a matrix.

$$
X=
\begin{bmatrix}
1 & x_{11} & x_{12} & \cdots & x_{1n}\\
1 & x_{21} & x_{22} & \cdots & x_{2n}\\
1 & x_{31} & x_{32} & \cdots & x_{3n}\\
\vdots & \vdots & \vdots & & \vdots\\
1 & x_{m1} & x_{m2} & \cdots & x_{mn}
\end{bmatrix}
$$

Each row represents one training example.

---

## Weight Vector

$$
w=
\begin{bmatrix}
w_0\\
w_1\\
w_2\\
\vdots\\
w_n
\end{bmatrix}
$$

---

## Prediction for All Samples

Instead of predicting one sample at a time,

$$
g(x_i)=x_i^Tw
$$

we compute predictions for every sample simultaneously:

$$
\hat{y}=Xw
$$

where

$$
\hat{y}=
\begin{bmatrix}
\hat{y}_1\\
\hat{y}_2\\
\vdots\\
\hat{y}_m
\end{bmatrix}
$$

---

## Matrix Dimensions

If there are

- \(m\) samples
- \(n\) features

then

$$
X \in \mathbb{R}^{m\times(n+1)}
$$

$$
w \in \mathbb{R}^{(n+1)\times1}
$$

Therefore,

$$
Xw
=
(m\times(n+1))
((n+1)\times1)
=
m\times1
$$

---

# Target Vector

The true target values are

$$
y=
\begin{bmatrix}
y_1\\
y_2\\
\vdots\\
y_m
\end{bmatrix}
$$

---

# Residual (Prediction Error)

The residual vector is

$$
e=y-\hat{y}
$$

Substituting

$$
\hat{y}=Xw
$$

gives

$$
e=y-Xw
$$

---

# Cost Function (Least Squares)

The objective is to minimize the sum of squared errors.

For one sample,

$$
(y_i-\hat{y}_i)^2
$$

For the whole dataset,

$$
J(w)=\|y-Xw\|^2
$$

which is equivalent to

$$
J(w)
=
(y-Xw)^T(y-Xw)
$$

Many books instead write

$$
J(w)=\frac{1}{2m}\|y-Xw\|^2
$$

The factor \(\frac{1}{2m}\) does not change the optimal solution.

---

# Finding the Optimal Weights

Differentiate the cost function with respect to \(w\):

$$
\frac{\partial J(w)}{\partial w}=0
$$

This produces the **Normal Equation**

$$
X^TXw=X^Ty
$$

Assuming \(X^TX\) is invertible,

$$
\boxed{
w=(X^TX)^{-1}X^Ty
}
$$

This gives the optimal weight vector.

---

# Complete Flow

$$
g(x_i)=w_0+\sum_{j=1}^{n}w_jx_{ij}
$$

↓

Introduce the bias feature

$$
x_{i0}=1
$$

↓

Vector form

$$
g(x_i)=x_i^Tw
$$

↓

Stack all samples

$$
X=
\begin{bmatrix}
x_1^T\\
x_2^T\\
\vdots\\
x_m^T
\end{bmatrix}
$$

↓

Predict every sample

$$
\hat{y}=Xw
$$

↓

Compute the cost

$$
J(w)=\|y-Xw\|^2
$$

↓

Solve

$$
X^TXw=X^Ty
$$

↓

Final solution

$$
\boxed{
w=(X^TX)^{-1}X^Ty
}
$$