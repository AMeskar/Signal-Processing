# Singular Spectrum Analysis (SSA) on a Toy Time Series

This repository demonstrates a **Singular Spectrum Analysis (SSA)-inspired approach** for **time series decomposition**. In particular, we showcase how SSA can help separate a signal into **trend, periodic (harmonic) components, and noise** by embedding the series into a matrix, performing a matrix factorization, and reconstructing meaningful components.

---

## Table of Contents
1. [Purpose and Use Cases](#purpose-and-use-cases)  
2. [Methodology Overview](#methodology-overview)  
3. [Basic Math Explanation](#basic-math-explanation)  
   - [Constructing the Trajectory Matrix](#constructing-the-trajectory-matrix)  
   - [Singular Value Decomposition (SVD)](#singular-value-decomposition-svd)  
   - [W-Correlation](#w-correlation)  
4. [Implementation Details](#implementation-details)  
5. [How to Use](#how-to-use)  
6. [License](#license)

---

## Purpose and Use Cases

**Singular Spectrum Analysis (SSA)** is particularly useful for:
- **Identifying and extracting trends** in time series data,
- **Isolating and modeling periodic/seasonal signals**, and
- **Smoothing or denoising** real-world data for clearer analysis.

Typical domains include **economics**, **environmental sciences**, and **engineering**, where time series often exhibit complex overlapping signals.

---

## Methodology Overview

1. **Generate a Synthetic Time Series**  
   We create a series with a quadratic trend, two sine-wave components of different frequencies, and uniform random noise.

2. **Construct the Trajectory Matrix**  
   By sliding a fixed-length window across the time series, we form a matrix whose columns are overlapping segments of the original series.

3. **Perform SVD**  
   We factor the trajectory matrix into $\mathbf{U}$, $\Sigma$, and $\mathbf{V}^\top$. Each singular value $\sigma_i$ indicates how much variance that component explains.

4. **Reconstruct the Signal**  
   - Each rank-1 sub-matrix represents one piece of the original series.  
   - A “Hankel operator” ensures the lag structure is maintained so each piece can be turned back into a valid 1D time series by diagonal averaging.

5. **Compute the W-Correlation**  
   We measure how similar any two reconstructed components are. Highly correlated components may represent the same frequency or part of the trend.

6. **Group and Interpret**  
   Finally, we group components that represent the same phenomena (e.g., trend, a particular harmonic, or noise).

---

## Basic Math Explanation

### Constructing the Trajectory Matrix

Suppose our time series is $\{x_0, x_1, \ldots, x_{N-1}\}$. We choose a window length $L$ (embedding dimension). The number of columns is $K = N - L + 1$. The **trajectory matrix** $\mathbf{X}$ then becomes a collection of overlapping segments:

$$
\[
\mathbf{X} =
\begin{bmatrix}
x_0 & x_1 & x_2 & \dots & x_{K-1} \\
x_1 & x_2 & x_3 & \dots & x_{K}   \\
\vdots & \vdots & \vdots & & \vdots \\
x_{L-1} & x_{L} & x_{L+1} & \dots & x_{N-1}
\end{bmatrix}.
\]
$$

Each column is just a shifted view of length $L$ from the original time series.

### Singular Value Decomposition (SVD)

We factor $\mathbf{X}$ as:

$$
\[
\mathbf{X} = \mathbf{U} \, \mathrm{diag}(\Sigma) \, \mathbf{V}^\top,
\]
$$

where:
- $\mathbf{U}$ is an $L \times L$ orthonormal matrix (left singular vectors),
- $\mathrm{diag}(\Sigma)$ is a diagonal matrix of singular values $\sigma_i$,
- $\mathbf{V}$ is a $K \times K$ orthonormal matrix (right singular vectors).

Each $\sigma_i$ corresponds to a *rank-1* sub-matrix:

$$
\[
\mathbf{X}_i = \sigma_i \; \mathbf{U}_i \; \mathbf{V}_i^\top,
\]
$$

which can be Hankelized and converted into a one-dimensional component of the time series.

### W-Correlation

After reconstructing each $\mathbf{X}_i$ into a 1D series $\tilde{F}_i$ of length $N$, we define a weight vector $w$ to account for overlaps during reconstruction. The **W-correlation** between two reconstructed series $\tilde{F}_i$ and $\tilde{F}_j$ is given by:

$$
W_{ij} =
\frac{
\left| \sum_{t=0}^{N-1} w_t\, \tilde{F}_i(t)\,\tilde{F}_j(t) \right|
}{
\sqrt{
\left( \sum_{t=0}^{N-1} w_t\, \tilde{F}_i(t)^2 \right)
\left( \sum_{t=0}^{N-1} w_t\, \tilde{F}_j(t)^2 \right)
}
}.
$$


A value close to 1 indicates that $\tilde{F}_i$ and $\tilde{F}_j$ are almost the same pattern, while a value near 0 means they capture distinctly different behaviors.

---

## Implementation Details

1. **Synthetic Signal Creation**  
   - Trend: a simple quadratic function.  
   - Two sine waves with different periods.  
   - Uniform random noise, scaled to fit the amplitude range.  

2. **Matrix Embedding**  
   - A half-length window ($L = N/2$) is chosen just for demonstration.  
   - The resulting trajectory matrix is visualized as a heatmap.  

3. **SVD and Reconstruction**  
   - We extract each rank-1 sub-matrix from the SVD.  
   - Each sub-matrix is made Hankel again, then diagonal-averaged into a final 1D reconstruction.  

4. **W-Correlation and Grouping**  
   - The correlation matrix helps determine which reconstructed pieces should be merged (trend vs. periodic signals vs. noise).  
   - We highlight how the original time series can be split into meaningful components.

---

## How to Use

1. **Download or Clone**  
   Get the repository on your local machine.

2. **Install Dependencies**  
   - Python 3.7+ (or later).  
   - Common libraries like `numpy`, `matplotlib`, and `pandas`.  

3. **Run the Notebook**  
   Open and execute the `.ipynb` file in Jupyter or VS Code. Examine each step to see how the time series is generated, decomposed, and reconstructed.

4. **Experiment**  
   - Adjust the window size $L$.  
   - Introduce different trends or frequencies.  
   - Check how the W-correlation matrix changes and regroup components for further insight.

---

## Contact Information

- Email: Meskaramine2@gmail.com  
- LinkedIn: [linkedin.com/in/amine-meskar](https://www.linkedin.com/in/amine-meskar)  
