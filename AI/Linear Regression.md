
Linear Regression assumes that a target variable can be approximated as a weighted sum of input features plus error

**Simple linear regression** (one feature):

ŷ = β₀ + β₁x
where ŷ is the predicted value, β₀ is the intercept (bias), and β₁ is the slope (weight).

**Multiple linear regression**:

ŷ = β₀ + β₁x₁ + β₂x₂ + ... + βₙxₙ

In matrix form, this is cleaner:

ŷ = Xβ


## How the Models Learn


![[03_predicted_vs_actual.png]]



**Zoomcamp**



g(X) = y
`g` - model   --> Linear Regression
`X` - Feature Matrix
`y` - Target 


```
g(x_i) = y_i

x_i = { x_i1 x_i2 ... x_in}

g(x_i1, x_i2, ... x_in) = y_i


g(x_i) = w0 + w1*x_i1 + w2*x_i2 + w3*x_i3 + w4*x_i4


w0 - bias

```



Imagine that we have two vectors a and b
Dot multiplication of these two vectors will be

```
a = {x1 x2 x3 ... xn}
b = {y1 y2 y3 ... yn}

a * b = x1 * y1 + x2 * y2 + x3 * y3 + ... xn * yn


thats why we can write 

```

![[linear_regression.png]]




and if we add `x_i0` to w0 we can do like this

![[linear_regression_vector.png]]





