---
tags:
  - linear-algebra
creation-date: 2024-06-02 17:36
---
# Eigenvectors and Eigenvalues

## Definitions

**Eigenvector**: An eigenvector of a square matrix $A$ is a non-zero vector $v$ such that when $A$ is multiplied by $v$, the result is a *scalar multiple of $v$*. 
$$
Av = \lambda v
$$
where $A$ is the matrix, $v$ is the eigenvector, and $\lambda$ is the eigenvalue associated with $v$.

**Eigenvalue**: The eigenvalue $\lambda$ is the *scalar* that satisfies the equation above. 
- Each eigenvector has a corresponding eigenvalue.

# Characteristics

- The eigenvectors of a matrix point in *directions that remain unchanged* (except for scaling) when the matrix is applied to them.
- Eigenvalues indicate how much the eigenvector is stretched or compressed during this transformation.

## Finding Eigenvalues and Eigenvectors

To find eigenvalues, solve the characteristic equation:
$$
\det(A - \lambda I) = 0
$$
where $I$ is the identity matrix of the same size as $A$.

Once eigenvalues $\lambda$ are found, substitute them back into the equation $(A - \lambda I)v = 0$ to solve for the corresponding eigenvectors $v$.

## Applications

1. [[Principal Component Analysis (PCA)]]
2. Vibration Analysis and Structural Engineering
	- In structural engineering, eigenvalues represent the natural frequencies of vibration, and eigenvectors represent the corresponding mode shapes of the structure.
3. Quantum Mechanics
	- In quantum mechanics, the eigenvectors of the Hamiltonian operator represent the possible states of a quantum system, and the eigenvalues represent the corresponding energy levels.
4. Markov Chains
	- In Markov chain analysis, the eigenvalues and eigenvectors of the transition matrix provide information about the long-term behaviour and steady-state probabilities of the system.
5. Facial Recognition
	- In computer vision, techniques like Eigenfaces use eigenvectors of the covariance matrix of facial images to identify and recognise faces. The eigenfaces represent significant features in the face images, allowing for efficient face recognition.


---
# References

