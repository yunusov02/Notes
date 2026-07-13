## RMSE

# Model qanchaga xato qilayapti


  

# Root Mean Squared Error (RMSE)

  

Suppose

  

- \(y_i\) is the true value.

- \(\hat{y}_i\) is the predicted value.

- \(m\) is the number of samples.

  

The RMSE is defined as

  

$$

\mathrm{RMSE}

=

\sqrt{

\frac{1}{m}

\sum_{i=1}^{m}

(y_i-\hat{y}_i)^2

}

$$

  

---

  

## Step-by-Step

  

### 1. Compute the error (residual)

  

$$

e_i = y_i - \hat{y}_i

$$

  

### 2. Square each error

  

$$

e_i^2 = (y_i-\hat{y}_i)^2

$$

  

### 3. Compute the Mean Squared Error (MSE)

  

$$

\mathrm{MSE}

=

\frac{1}{m}

\sum_{i=1}^{m}

(y_i-\hat{y}_i)^2

$$

  

### 4. Take the square root

  

$$

\mathrm{RMSE}

=

\sqrt{\mathrm{MSE}}

$$

  

---

  

## Matrix Form

  

Let

  

$$

y=

\begin{bmatrix}

y_1\\

y_2\\

\vdots\\

y_m

\end{bmatrix},

\qquad

\hat{y}=

\begin{bmatrix}

\hat{y}_1\\

\hat{y}_2\\

\vdots\\

\hat{y}_m

\end{bmatrix}

$$

  

The residual vector is

  

$$

e = y-\hat{y}

$$

  

The RMSE can then be written as

  

$$

\mathrm{RMSE}

=

\sqrt{

\frac{1}{m}

e^Te

}

$$

  

Since

  

$$

e = y-Xw,

$$

  

we can also write

  

$$

\boxed{

\mathrm{RMSE}

=

\sqrt{

\frac{1}{m}

(y-Xw)^T(y-Xw)

}

}

$$



[[linear-regression]]
[[cleaning]]
