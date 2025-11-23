# Regularity lemma for graphs: definitions, weak form, and Szemerédi form {#regularity-lemma-for-graphs-definitions-weak-form-and-szemerédi-form .unnumbered}

## Basic notation for graphs {#basic-notation-for-graphs .unnumbered}

Let $G=(V,E)$ be a simple graph on $n=|V|$ vertices.

- For $X,Y\subseteq V$, let $e_G(X,Y)$ be the number of *cut edges*
  between $X$ and $Y$. If $X\cap Y\neq \varnothing$, edges with both
  endpoints in $X\cap Y$ are counted twice (once for each endpoint).

- The (edge) density between $X$ and $Y$ is
  $$d_G(X,Y)\ :=\ \frac{e_G(X,Y)}{|X|\,|Y|}.$$

- If $X,Y$ are disjoint, $G[X,Y]$ is the bipartite graph obtained by
  only keeping the cut edges between $X$ and $Y$.

- A *partition* of $V$ is $\mathcal P=\{V_1,\dots,V_k\}$.

## Quotient/averaged graph $G_{\mathcal P}$ and its template on $[k]$ {#quotientaveraged-graph-g_mathcal-p-and-its-template-on-k .unnumbered}

Given a partition $\mathcal P=\{V_1,\dots,V_k\}$, define the *averaged*
(weighted) graph $G_{\mathcal P}$ on $V$ by putting on each pair
$u\in V_i$, $v\in V_j$ the weight
$$(G_{\mathcal P})_{uv}\ :=\ d_G(V_i,V_j).$$ Equivalently,
$G_{\mathcal P}$ is the "matrix averaging" of $G$ over the blocks
$V_i\times V_j$. One can also form the *template graph* $G/\mathcal P$
on the vertex set $[k]$, where node $i$ is weighted by $|V_i|$ and the
edge weight $\{i,j\}$ is $d_G(V_i,V_j)$.

## $\varepsilon$-homogeneous (a.k.a. $\varepsilon$-regular) bipartite pairs {#varepsilon-homogeneous-a.k.a.-varepsilon-regular-bipartite-pairs .unnumbered}

Let $U,W\subseteq V$ be disjoint. The bipartite graph $G[U,W]$ is
*$\varepsilon$-regular* if

$$\bigl|\,e_G(X,Y)-d_G(U,W)|X||Y|\,\bigr| \ \le\ \varepsilon\,|U|\,|W|
\quad\text{whenever } |X|\ge \varepsilon|U|,\ |Y|\ge \varepsilon|W|.$$
(\*)

Equivalently a stronger condition with the restriction on the size of X,
Y,

$$\bigl|\,d_G(X,Y)\ -\ d_G(U,W)\,\bigr| \ \le \ \varepsilon
\quad\text{for all}\quad
X\subseteq U,\ Y\subseteq W,\ |X|\ge \varepsilon|U|,\ |Y|\ge \varepsilon|W|.$$
(If one tried to require the inequality for *all* $X,Y$ without the size
lower bounds, it would be too strong and would fail for very small
$X',Y'$---hence the standard definition above.)

let $H$ be the complete bipartite weighted graph on $(U,W)$ in which
every edge has weight $d_G(U,W)$. Then, for every $X\subseteq U$,
$Y\subseteq W$, $$e_H(X,Y)\ =\ d_G(U,W)\,|X|\,|Y|.$$ Thus
$\varepsilon$-homogeneity is exactly the statement, following from (\*),
we have that
$$\bigl|\,e_G(X,Y)-e_H(X,Y)\,\bigr| \ \le\ \varepsilon\,|U|\,|W|
\quad\text{whenever } |X|\ge \varepsilon|U|,\ |Y|\ge \varepsilon|W|.$$
(Under global normalization one also notes $|U||W|\le n^2/4$.)

## Cut distance for unweighted graphs {#cut-distance-for-unweighted-graphs .unnumbered}

Write $A_G$ for the adjacency matrix of $G$, and set
$$d_{\square}(G_1,G_2)
\ :=\ \frac{1}{n^2}\,\bigl\|A_{G_1}-A_{G_2}\bigr\|_{\square}
\ =\ \frac{1}{n^2}\,\max_{S,T\subseteq V}\bigl|e_{G_1}(S,T)-e_{G_2}(S,T)\bigr|.$$

This is the normalized cut distance. For a partition $\mathcal P$,
$G_{\mathcal P}$ is the block-constant average of $G$.

## Weak regularity lemma, almost original form {#weak-regularity-lemma-almost-original-form .unnumbered}

For every $\epsilon >0$, there is an S($\epsilon) \in N$ such that every
graph G has an equitable partition into k classes, with
$\frac{1}{\epsilon} \leq k \leq S(\epsilon)$, such that for all but
$\epsilon k^2$ pairs of indices, the bipartite graph between the pair of
indices is $\epsilon$-homogeneous.

The problem is that the upper bound S is independent from the geaph G.
The lower bound for number of classes bound from above the number of
edges inside each class. The only bad pairs are also bounded by same
upper bound. But then this means all the edges inside these classes are
all kind of error term, so the partitioned graph is very similar to the
original graph in this sense.

## Weak regularity for graphs (from the matrix lemma) {#weak-regularity-for-graphs-from-the-matrix-lemma .unnumbered}

By Lemma 9.7(c) (matrix form) and the averaging operator, for every
integer $k>1$ and every graph $G=(V,E)$, there exists a partition of $V$
into $k$ classes such that
$$d_{\square}\!\bigl(G,\,G_{\mathcal P}\bigr)\ \le\ \frac{2}{\sqrt{\log k}}
\qquad
\text{(and $\le \frac{4}{\sqrt{\log k}}$ for equitable partitions)}.$$
Intuition: the more classes you take, the closer $G_{\mathcal P}$ is to
$G$ in cut distance.

#### Proof sketch following the notes.

Fix $\varepsilon>0$. Choose $k$ sufficiently large so that (i) the sum
of inside-edges over all classes is $\le \varepsilon n^2$, and (ii) the
weak bound gives
$$d_{\square}(G,G_{\mathcal P})\ \le\ \frac{4}{\sqrt{\log k}}\ \le\ \varepsilon$$
for an equitable partition $\mathcal P$ into $k$ classes. Write a pair
$(V_i,V_j)$ as *good* if it is $\varepsilon$-homogeneous and *bad*
otherwise.

Suppose, for contradiction, that more than $\varepsilon k^2$ pairs are
bad. For each bad pair pick subsets $X\subseteq V_i$, $Y\subseteq V_j$
with $|X|\ge \varepsilon|V_i|$, $|Y|\ge \varepsilon|V_j|$ and
$$\bigl|\,d_G(X,Y)-d_G(V_i,V_j)\,\bigr|>\varepsilon.$$ Group these
witnesses by whether the deviation is positive or negative, and take
unions $S$ and $T$ of *whole* classes so that the total signed deviation
accumulates. By equitability, each bad pair contributes
$\gtrsim \varepsilon\,|V_i||V_j|\asymp \varepsilon\,(n/k)^2$ to
$|e_G(S,T)-e_{G_{\mathcal P}}(S,T)|$, and thus
$$\frac{1}{n^2}\,\bigl|e_G(S,T)-e_{G_{\mathcal P}}(S,T)\bigr|
\ \gtrsim\ \varepsilon\cdot \frac{\#\{\text{bad pairs}\}}{k^2}
\ >\ \varepsilon^2,$$ contradicting
$d_{\square}(G,G_{\mathcal P})\le \varepsilon$. Hence there are at most
$\varepsilon k^2$ bad pairs, which completes the proof sketch. 0◻

## A strong (Frieze--Kannan style) refinement statement {#a-strong-friezekannan-style-refinement-statement .unnumbered}

A convenient strengthening that packages the above argument is:

::: lemma
**Lemma 1** (Strong refinement/approximation; "very strong"). *For every
$\varepsilon>0$ and every partition $\mathcal P$ of $V$, there exists a
refinement $\mathcal Q$ of $\mathcal P$ with
$|\mathcal Q|\le S(\varepsilon)\,|\mathcal P|$ such that, for every pair
of unions $S,T$ of $\mathcal Q$-classes (except at most
$\varepsilon\,|\mathcal P|^2$ exceptional pairs of
$\mathcal P$-classes), we have
$$\left|\,\frac{e_G(S,T)}{|S|\,|T|}\ -\ \frac{e_{G_{\mathcal P}}(S,T)}{|S|\,|T|}\right|\ \le\ \varepsilon.$$
Consequently, taking $\mathcal P$ equitable and combining with
$d_{\square}(G,G_{\mathcal P})\le \varepsilon$ yields Szemerédi's lemma
with $k\le S(\varepsilon)$ and at most $\varepsilon k^2$ exceptions.*
:::

#### Notes.

- The "refinement $\mathcal Q$ is $\varepsilon$-close to $\mathcal P$"
  phrasing means: for all but $\varepsilon\,|\mathcal P|^2$ pairs of
  $\mathcal P$-classes, every union $S$ of $\mathcal Q$-classes
  contained in one side and every union $T$ on the other side preserve
  the average densities up to $\varepsilon$ after normalizing by
  $|S||T|$.

- Inside-edges and exceptional classes can be handled together by the
  crude bounds earlier (e.g., $\sum_i \binom{|V_i|}{2}\le n^2/(2k)+n/2$
  and choosing $k$ large).

*Summary.* Weak regularity (via cut-norm approximation
$G\approx G_{\mathcal P}$) gives
$d_{\square}(G,G_{\mathcal P})\lesssim 1/\sqrt{\log k}$. For $k$ large,
this forces that, aside from at most $\varepsilon k^2$ pairs, the
bipartite pieces $G[V_i,V_j]$ are $\varepsilon$-homogeneous. Choosing
$k\le S(\varepsilon)$ and taking $\mathcal P$ equitable yields the
common form of Szemerédi's Regularity Lemma.

::: center
Notes on (Weak) Regularity via Geometry and a Hilbert-Space Lemma
:::

# Preliminaries and Notation {#preliminaries-and-notation .unnumbered}

Let $M\in\mathbb{R}^{n\times n}$. For $S,T\subseteq[n]$, write
$\mathbf{1}_{S\times T}$ for the indicator matrix,
$$(\mathbf{1}_{S\times T})_{ij}=\begin{cases}
1,& i\in S,\ j\in T,\\
0,& \text{otherwise}.
\end{cases}$$ Define
$$M(S,T)=\sum_{i\in S,\ j\in T}M_{ij}=\left\langle M,\, \mathbf{1}_{S\times T} \right\rangle.$$
The Frobenius (Euclidean) norm is
$$\left\lVert M \right\rVert_2=\Big(\sum_{i,j=1}^n M_{ij}^2\Big)^{1/2}.$$
The cut norm is
$$\left\lVert M \right\rVert_{\square}=\max_{S,T\subseteq[n]}|M(S,T)|=\max_{S,T}|\left\langle M,\, \mathbf{1}_{S\times T} \right\rangle|.$$
(When we minimize quadratic expressions below, it is convenient to think
of the *normalized* version where the drop per step is
$\left\lVert M \right\rVert_{\square}^2$; the algebra below reflects
this and is standard in weak regularity arguments.)

**Averaging operator.** For a partition $\mathcal P=\{V_1,\dots,V_k\}$
of $[n]$, define for $x\in\mathbb{R}^n$ and
$M\in\mathbb{R}^{n\times n}$:
$$(x_{\mathcal P})_u=\frac{x(V_i)}{|V_i|}\quad(u\in V_i),
\qquad
(M_{\mathcal P})_{uv}=\frac{M(V_i,V_j)}{|V_i||V_j|}\quad(u\in V_i,\ v\in V_j).$$
Then $M\mapsto M_{\mathcal P}$ is the orthogonal projection (in
Frobenius inner product) onto the linear subspace of
$\mathcal P$-matrices (matrices constant on each block $V_i\times V_j$).
In particular,
$$\left\lVert M_{\mathcal P} \right\rVert_2\le \left\lVert M \right\rVert_2,\qquad
\left\lVert M_{\mathcal P} \right\rVert_{\square}\le \left\lVert M \right\rVert_{\square}.$$

# Lemma 9.7 (Matrix Version) {#lemma-9.7-matrix-version .unnumbered}

::: lemma
**Lemma 2**. *Let $M\in\mathbb{R}^{n\times n}$.*

1.  *For every $r\ge 1$ there exist sets
    $S_1,\dots,S_r,T_1,\dots,T_r\subseteq[n]$ and reals $a_1,\dots,a_r$
    such that
    $$\Big\| M-\sum_{i=1}^r a_i\,\mathbf{1}_{S_i\times T_i}\Big\|_{\square}\ \le\ \frac{1}{\sqrt{r}}\ \left\lVert M \right\rVert_2.$$*

2.  *For every $k\ge 2$ there is a partition $\mathcal P$ of $[n]$ into
    $k$ classes with
    $$\left\lVert M-M_{\mathcal P} \right\rVert_{\square}\ \le\ \frac{4}{\sqrt{\log k}}\ \left\lVert M \right\rVert_2.$$*

3.  *For every $k\ge 2$ there is a partition $\mathcal P$ of $[n]$ into
    $k$ classes and a $\mathcal P$-matrix $B$ such that
    $$\left\lVert M-B \right\rVert_{\square}\ \le\ \frac{2}{\sqrt{\log k}}\ \left\lVert M \right\rVert_2.$$*
:::

## Proof of (a) {#proof-of-a .unnumbered}

Fix nonempty $S,T$ with $|M(S,T)|=\left\lVert M \right\rVert_{\square}$.
For any real $a$, $$\begin{align*}
\left\lVert M-a\,\mathbf{1}_{S\times T} \right\rVert_2^2
&=\left\lVert M \right\rVert_2^2+a^2\left\lVert \mathbf{1}_{S\times T} \right\rVert_2^2-2a\,\left\langle M,\, \mathbf{1}_{S\times T} \right\rangle\\
&=\left\lVert M \right\rVert_2^2+a^2|S||T|-2a\,M(S,T).
\end{align*}$$ This quadratic in $a$ is minimized at
$a=M(S,T)/(|S||T|)$, giving $$\begin{equation}
\label{eq:one-step-drop}
\left\lVert M-a\,\mathbf{1}_{S\times T} \right\rVert_2^2=\left\lVert M \right\rVert_2^2-\frac{M(S,T)^2}{|S||T|}
\ \le\ \left\lVert M \right\rVert_2^2-\left\lVert M \right\rVert_{\square}^2 \qquad \text{(normalized view)}.
\end{equation}$$

Iterate greedily. Let $M_0:=M$ and for $j=0,1,\dots,r-1$ choose
$S_{j+1},T_{j+1}$ attaining $\left\lVert M_j \right\rVert_{\square}$ and
set $$a_{j+1}:=\frac{M_j(S_{j+1},T_{j+1})}{|S_{j+1}||T_{j+1}|},\qquad
M_{j+1}:=M_j-a_{j+1}\,\mathbf{1}_{S_{j+1}\times T_{j+1}}.$$ By
[\[eq:one-step-drop\]](#eq:one-step-drop){reference-type="eqref"
reference="eq:one-step-drop"},
$$\left\lVert M_{j+1} \right\rVert_2^2 \leq \left\lVert M_j \right\rVert_2^2-\left\lVert M_j \right\rVert_{\square}^2.$$
Summing for $j=0$ to $r-1$ yields
$$\sum_{j=0}^{r-1}\left\lVert M_j \right\rVert_{\square}^2\ \le\ \left\lVert M \right\rVert_2^2 - \left\lVert M_r \right\rVert_2^2.$$

we get

$$\left\lVert M_r \right\rVert_2^2 \le\ \left\lVert M \right\rVert_2^2 - \sum_{j=0}^{r-1}\left\lVert M_j \right\rVert_{\square}^2.$$
LHS is nonnegative,

Hence by averaging there exists $j\in\{0,\dots,r-1\}$ with
$$\left\lVert M_j \right\rVert_{\square}\ \le\ \frac{1}{\sqrt r}\ \left\lVert M \right\rVert_2.$$
Finally, $$M-\sum_{i=1}^r a_i\,\mathbf{1}_{S_i\times T_i}=M_j$$ after
setting $a_{j+1}=\cdots=a_r=0$, which proves (a).

## Proof of (c) {#proof-of-c .unnumbered}

Take $$r=\Big\lfloor \frac{\log k}{2}\Big\rfloor,
\qquad
B=\sum_{i=1}^r a_i\,\mathbf{1}_{S_i\times T_i}$$ from part (a).
Construct a partition $\mathcal P$ of $[n]$ by grouping vertices that
share the same membership pattern across the $2r$ sets
$S_1,\dots,S_r,T_1,\dots,T_r$: $$u\sim v
\iff
(\forall i\le r)\ [u\in S_i]\!=\![v\in S_i]\ \text{ and }\
(\forall i\le r)\ [u\in T_i]\!=\![v\in T_i].$$ The equivalence classes
form $\mathcal P$, and there are at most $2^{2r}\le k$ classes.

For any two parts $A,A'\in\mathcal P$ and any $(u,v)\in A\times A'$, the
value $\mathbf{1}_{S_i\times T_i}(u,v)$ depends only on whether
$A\subseteq S_i$ and $A'\subseteq T_i$, so each
$\mathbf{1}_{S_i\times T_i}$ is constant on $A\times A'$. Therefore $B$
is a $\mathcal P$-matrix. From part (a),
$$\left\lVert M-B \right\rVert_{\square}\ \le\ \frac{1}{\sqrt r}\ \left\lVert M \right\rVert_2\ \le\ \frac{2}{\sqrt{\log k}}\ \left\lVert M \right\rVert_2,$$
which proves (c). 0◻

# A Regularity Lemma in Hilbert Space {#a-regularity-lemma-in-hilbert-space .unnumbered}

::: lemma
**Lemma 3** (Regularity Lemma in Hilbert Space). *Let $K_1,K_2,\dots$ be
arbitrary nonempty subsets of a (real) Hilbert space $\mathcal H$. For
every $\varepsilon>0$ and $f\in\mathcal H$ there exists
$m\le \lceil 1/\varepsilon^2\rceil$, elements $f_i\in K_i$
$(1\le i\le m)$, and scalars $\gamma_1,\dots,\gamma_m\in\mathbb{R}$ such
that for the residual $r=f-(\gamma_1f_1+\cdots+\gamma_m f_m)$ we have
$$|\left\langle g,\, r \right\rangle|\ \le\ \varepsilon\,\left\lVert g \right\rVert\,\left\lVert f \right\rVert
\qquad\text{for every } g\in K_{m+1}.$$*
:::

::: proof
*Proof.* We use only inner-product identities that also hold in any real
inner-product space:

1.  $\left\langle x,\, y \right\rangle=\left\langle y,\, x \right\rangle$
    and
    $\left\lVert x \right\rVert^2=\left\langle x,\, x \right\rangle$.

2.  Cauchy--Schwarz:
    $|\left\langle x,\, y \right\rangle|\le \left\lVert x \right\rVert\,\left\lVert y \right\rVert$.

3.  Line expansion: for all $u,v,g$ and $t\in\mathbb{R}$,
    $$\left\lVert u-(v+t g) \right\rVert^2=\left\lVert u-v \right\rVert^2+t^2\left\lVert g \right\rVert^2-2t\,\left\langle g,\, u-v \right\rangle.$$

Let
$$\eta_k=\inf_{\gamma_i\in\mathbb{R},\ f_i\in K_i}\ \Big\|f-\sum_{i=1}^k \gamma_i f_i\Big\|^2,$$
the best (squared) error after $k$ terms. Then
$0\le \eta_{k+1}\le \eta_k\le \left\lVert f \right\rVert^2$ and hence
there exists $m\le \lceil 1/\varepsilon^2\rceil$ with
$$\eta_m<\eta_{m+1}+\varepsilon^2\left\lVert f \right\rVert^2.$$ Choose
coefficients $\gamma_1,\dots,\gamma_m$ and $f_i\in K_i$ so that, writing
$f^*=\sum_{i=1}^m\gamma_i f_i$,
$$\left\lVert f-f^* \right\rVert^2\le \eta_m+\varepsilon^2\left\lVert f \right\rVert^2.$$
Let $r=f-f^*$. Fix $g\in K_{m+1}$. For any $t\in\mathbb{R}$,
$$\left\lVert f-(f^*+t g) \right\rVert^2\ge \eta_{m+1}>\eta_m-\varepsilon^2\left\lVert f \right\rVert^2\ge \left\lVert r \right\rVert^2-\varepsilon^2\left\lVert f \right\rVert^2.$$
Expanding along the line through $f^*+tg$ gives
$$\left\lVert r-tg \right\rVert^2=\left\lVert r \right\rVert^2+t^2\left\lVert g \right\rVert^2-2t\,\left\langle g,\, r \right\rangle
\ \ge\ \left\lVert r \right\rVert^2-\varepsilon^2\left\lVert f \right\rVert^2\qquad\forall t\in\mathbb{R}.$$
Thus the quadratic
$$q(t)=\left\lVert g \right\rVert^2t^2-2\left\langle g,\, r \right\rangle\,t+\varepsilon^2\left\lVert f \right\rVert^2$$
is nonnegative for all $t$, so its discriminant is $\le 0$, i.e.
$$\bigl(2\left\langle g,\, r \right\rangle\bigr)^2-4\left\lVert g \right\rVert^2\varepsilon^2\left\lVert f \right\rVert^2\le 0
\quad\Longrightarrow\quad
|\left\langle g,\, r \right\rangle|\le \varepsilon\,\left\lVert g \right\rVert\,\left\lVert f \right\rVert.$$
Since $g\in K_{m+1}$ was arbitrary, the lemma follows. ◻