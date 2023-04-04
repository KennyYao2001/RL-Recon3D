### Epipolar Constaint
Based on epipolar geometry, we have $x_{2}^TFx_{1}=0$, where $F$ is the fundamental matrix, and 
$x_{2}^T=\begin{bmatrix}u'& v'& 1 \\\end{bmatrix},x_{1}=\begin{bmatrix}u\\ v\\ 1\end{bmatrix},F=\begin{bmatrix}f_{11}&f_{12}&f_{13}\\f_{21}&f_{22}&f_{23}\\ f_{31}&f_{32}&f_{33}\end{bmatrix}$
### Eight (pair of) points algorithm
We can first rewrite it into:
$$u'uf_{11}+u'vf_{12}+u'f_{13}+v'uf_{21}+v'vf_{22}+v'f_{23}+uf_{31}+vf_{32}+f_{33}=0$$
$$\tilde{x} \cdot \vec{f} =\begin{bmatrix}
u'u&u'v&u'&v'u&v'v&v'&u&v&1
\end{bmatrix}\begin{bmatrix}
f_{11}\\ f_{12}\\ f_{13}\\ f_{21}\\f_{22}\\f_{23}\\f_{31}\\f_{32}\\f_{33}
\end{bmatrix}= 0$$
That is, $\vec{f}$ represents the fundamental matrix in the form of a 9-dim vector, and it must be (approximately) orthogonal to the vector $\tilde{x}$ which is constructed by a pair of corresponding points. 
To solve this equation, we need to use 8 point algorithom, that is, at least 8 pairs of corresponding points should be involved. Each pair can derive an equation based on epipolar geometry. By stacking these equations, then we will have a linear system $A\vec{f}=0$, where each row entry of $A$ is constructed by a pair of matches. And $f$ is a non-zero vector and belongs to the Null-space of this matrix $A$. i.e. $\vec{f} \in N(A)$ s.t. $\lvert \lvert \vec{f} \rvert \rvert\neq 0$. Since we don't care the scale of fundamental matrix, we just set $\lvert \lvert \vec{f} \rvert \rvert=1$.

### Approximate solutions based on SVD
While $rank(A)$ should be 8, if there is no noise, but in real world, we would see $rank(A)>8$, i.e. there is no exact solution to this linear system.
This equation should be overdetermined if we have enough correspondings. To solve it, we can see if we do SVD on $A \in R^{N\times9}$, the linear system becomes $U\Sigma V^T\vec{f}=0 \to \Sigma V^T\vec{f}=0$, where $V \in R^{9 \times 9}$. An approximate solution can be found by solving $argmin_{\vec{f}}\lvert \lvert \Sigma V^T\vec{f} \rvert \rvert_{2}^2$.

We can approximately set $f$ as the transpose of the row entries of $V^T$, i.e. column vector of $V$, corresponding to the smallest singular value in $\Sigma$. 
1. Because $V$ is an orthogonal matrix, the row entries of $V^T$ are orthogonal with each other, i.e. the dot product between should be zero. By set $\vec{f}$ as one singular vector, we will have $V^Tf=[0,0,0,0,0,0,0,0,\lvert \lvert \vec{f} \rvert \rvert_{2}^2]^T=\begin{bmatrix}0_{8 \times 1}\\ 1 \\ \end{bmatrix}_{9 \times1}$
2. Assume singular values in $\Sigma$ is sorted, the optimal minimal value of this expression will be determined by the last (smallest) singular value $\sigma_{9}=\sigma_{min}$. 
$$\lvert \lvert \Sigma V^T\vec{f} \rvert  \rvert_{2}^2 =\lvert \lvert  \begin{bmatrix}
diag(\sigma_{i})_{9 \times 9}\\  \\
0_{(N-9)\times9}
\end{bmatrix}_{N\times 9}
\begin{bmatrix}
0_{8 \times 1}\\ 
1 \\ 
\end{bmatrix}_{9 \times1} \rvert  \rvert_{2}^2=\sigma_{min}^2$$
3. After solve $f$, we can reshape it back to $F\in R^{3 \times3}$.
4. Think about essential matrix $E=t\wedge R=[t_{\times}]R$, where $[t_{\times}]$ has rank 2. So we need to enforce $F$ has rank 2 as well.

### Normalization and Denormalization
If we need to do normalization on (non-homogeneous) points before we do eight point algorithm, we can assume nomalized points are $\tilde{x}=\frac{x-\mu}{\sigma}\gamma$, where $\gamma$ is the scale factor.
For homogeneous space, it's equivalent to $\tilde{x}=Tx$. that is:
$$\begin{bmatrix}
\tilde{u}\\ 
\tilde{v}\\ 
1
\end{bmatrix} = \begin{bmatrix}
\frac{\gamma_{u}}{\sigma_{u}} & 0 & -\frac{\gamma_{u}\mu_{u}}{\sigma_{u}}\\ 
0 & \frac{\gamma_{v}}{\sigma_{v}} & -\frac{\gamma_{v}\mu_{v}}{\sigma_{v}}\\ 
0 & 0 & 1
\end{bmatrix}\begin{bmatrix}
u\\ 
v\\ 
1
\end{bmatrix}$$
After nomalization, we can continue to solve $\tilde{x_{2}}^T\tilde{F}\tilde{x_{1}}=0$. But remember to denormalize it after we get $\tilde{F}$ from the SVD. Since $\tilde{x_{2}}^T\tilde{F}\tilde{x_{1}}=x_{2}^TT_{2}^T\tilde{F}T_{1}x_{1}=0$, we can get $F=T_{2}^T\tilde{F}T_{1}$.

### Essential Matrix
Recall Essential Matrix is calibrated version of $F$.
