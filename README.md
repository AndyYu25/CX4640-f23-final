```
---
Name: Andy Yu
Topic: 35
Title: Higher-Order Accurate Derivative Approximations
---
```
# Higher-Order Accurate Derivative Approximations

In numerical analysis, higher order accurate derivative approximations are a class of methods and algorithms that estimate the derivatives of a given function with the values of the function and other information about the function to a high degree of accuracy. 

# Table of Contents
1. [Background](#background)
2. [Finite Differences](#finite-differences)
3. [Richardson Extrapolation](#richardson-extrapolation)
4. [Essentially Non-Oscillatory Methods](#Essentially-Non-Oscillatory-Methods)
5. [Weighted Essentially Non-Oscillatory Methods](#Weighted-Essentially-Non-Oscillatory-Methods)
6. [Padé Approximations](#Padé-Approximations)
7. [Spectral Methods](#Spectral-Methods)
8. [References](#references)


## Background 

The definition of a derivative is as follows:

$$f'(x) = \lim_{h \to 0 }\frac{f(x + h) - f(x)}{h}$$

Derivative approximations aim to evaluate the derivative by utilizing this limit definition for some concrete value of h, known as the step size. Thus, accuracy in differentiation is expressed by degrees of accuracy where the derivative error $|e(h)|$ is bounded by some polynomial $C|h|^n$. If $|e(h)| \leq C|h|^n$, then the approximation is $n$ th-order accurate [1]. 

## Finite Differences

Finite differences evaluate the differences between several points on the function with various forumalations to approximate the derivative, discretizing the function into a finite amount of steps and intervals.

There are three basic low-order finite differences formulas: the forward difference formula, the backward difference formula, and the centered difference formula[2].

The forward difference formula is as follows:

$$f'(x) \approx \frac{f(x + h) - f(x)}{h}$$

The forward difference gives an approximation that is 1st-order accurate.

The backward difference formula is as follows:

$$f'(x) \approx \frac{f(x) - f(x - h)}{h}$$

The backward difference gives an approximation that is also 1st-order accurate.

The centered difference formula is as follows:

$$f'(x) \approx \frac{f(x + h) - f(x - h)}{2h}$$

The centered difference formula gives an approximation that is 2nd-order accurate.

Higher order finite difference schemes than forward, backward, and centered differences exist and can be derived by evaluating additional points on the function, at the cost of increased computational complexity per step. However, the higher order accuracy may also mean a larger step size and be utilized while not increasing error, so utilizing higher order finite difference approximations may or may not increase overall computational complexity, depending on the situation [3].

### Richardson Extrapolation

The Richardson extrapolation can utilize finite difference schemes to extrapolate values for a step size of zero. 

Let there be a finite difference approximation for step size h $F(h)$ such that $F(h) = a_0 + O(h^p) =  a_0 + a_1h^p + O(h^r)$, where $a_0$ and $a_1$ are unknown quantities, and $p < r$. From this finite difference approximation, as h approaches zero, $F(h) = a_0$. Thus, $F(0) = a_0$, meaning that $a_0$ is a reasonable approximation for $F(0)$.

As there are two unknowns, $a_0$ and $a_1$, a second equation is need to construct a system. Therefore, the same finite difference approximation is evaluated, for step size $h/q$, where q is a positive integer. This results in the equation $F(h/q) = a_0 + a_1(h/q)^p + O(h^r) = a_0 + a_1q^{-p}h^p + O(h^r)$.

By solving the linear system for $a_0$, we obtain the following formula[4]:

$$F(0) = a_0 = F(h) + \frac{F(h) - F(h/q)}{q^{-p} - 1} + O(h^r)$$

The original approximation $F(h)$ had an order of accuracy of p, while the Richardson extrapolation has an order of accuracy of r, thus resulting in a higher order of accuracy. Therefore, utilizing the Richardson extrapolation can result in higher order approximaitons from lower-order finite differences. 

## Essentially Non-Oscillatory Methods

Essentially Non-Oscillatory (ENO) methods are a class of finite difference methods designed to approximate smooth solutions to functions with discontinuities while maintaining a high order of accuracy. The fundamental principle behind ENO schemes is selecting the smoothest interpolant in a given area [5].

More formally, consider a discrete grid $a = x_{1/2} < x_{3/2} < ... < x_{n - 1/2} < x_{n + 1/2} = b$ in the domain of a function $f$. 

For some $i$ such that $0 \leq i \leq n$, there are several degree k finite difference approximations for f at the interval $\{x_{i - 1/2}, x_{i + 1/2}\}$. With an ENO scheme, the smoothest approximation at that interval is selected and utilized as the interpolant for. This process is repeated across all intervals between a and b.     

### Weighted Essentially Non-Oscillatory Methods

Instead of discarding all but the smoothest interpolation, Weighted Essentially Non-Oscillatory Methods consider all the possible interpolants and add them together as a weighted sum, where the weights sum up to 1 and are based on the smoothness of each interpolant [6].  

The primary advantage WENO/ENO schemes have over conventional finite difference methods is that WENO/ENO schemes will choose interpolants such that the function will not oscillate wildly around discontinuities. This makes the scheme advantageous for scenarios where discontinuities occur frequently, such as with fluid dynamics and traffic simulation.

### WENO Example

Consider a function $u(x_i)$ and a function approximation at the half-node $u_{i + 1/2}$. We use three different 3rd-order interpolations evaluating different points around the point $u_{i + 1/2}$[7]:

$$u_{i+\frac 12}^{(1)} = \frac{3}{8} u_{i-2} - \frac{5}{4} u_{i-1} + \frac{15}{8} u_{i}$$

$$u_{i+\frac 12}^{(2)} = -\frac{1}{8} u_{i-1} + \frac{3}{4} u_{i} + \frac{3}{8} u_{i+1} $$

$$u_{i+\frac 12}^{(3)} = \frac{3}{8} u_{i} + \frac{3}{4} u_{i+1} - \frac{1}{8} u_{i+2}$$

The WENO scheme has $u_{i+\frac 12}$ equal the weighted sum of all three interpolants as follows:

$$u_{i+\frac 12} = \gamma_1 u_{i+\frac 12}^{(1)} + \gamma_2 u_{i+\frac 12}^{(2)} + \gamma_3 u^{(3)}_{i+\frac 12}$$

where $\gamma_1 + \gamma_2 + \gamma_3 = 1$ and the linear coeffcients are inversely proportional to the smoothness of the scheme. In this case, the coefficients are $\gamma_1 = 1/16, \gamma_2 = 5/8, and \gamma_3 = 5/16$, and the weighted sum is 5th-order accurate [8].

## Padé Approximations

Padé approximations are rational functions that are a ratio of polynomials such that their power series agrees agrees with the power series of the function they are agreeing with. Given a rational function $f$, the Padé approximation of $f$ is following:
$$[p/q]_f(x) = N(x)/D(x)$$

such that the degree of N $\leq p$, the degree of D $\leq Q$, and $N(x) - f(x)D(x) = O(x^{p + q + 1}), (x \to 0)$ [5]. $N(x)$ and $D(x)$ can also be expressed as follows:

$$N(x) = \sum^{p}_{i = 0}a_i * x^i$$

$$D(x) = 1 + \sum^{q}_{i = 0}b_i * x^i$$

Let the Taylor expansion of $f$ be $T(x) = \displaystyle\sum_{i = 0}^{p + q} c_{i} * x^i $. As the Taylor expansion and the Pade approximation are equal, $T(x) = N(x)/D(x)$,  which can be rewritten as $D(x)T(x) = N(x)$ and then expanded as $(1 + \displaystyle\sum_{i = 0}^{q} b_i * x^i) * \displaystyle\sum_{i = 0}^{p + q} c_{i} * x^i = \displaystyle\sum^{p}_{i = 0}a_i * x^i$. By setting the coefficients on each side of the equation equal to each other, we get the following system of equations [9]:

$$a_0 = c_0b_0$$

$$a_1 = c_1b_0 + c_0b_1$$

$$...$$

$$a_p = c_pb_0 + c_{p - 1}b_1 + ... + c_{p-q}b_q$$

$$0 = c_{p+1}b_0 + c_pb_1 + ... + c_{p-q+1}b_q$$

$$...$$

$$0 = c_{p+q}b_0 + c_{p + q - 1}b_1 + ... + c_pb_q$$

Thus, the Padé approximant can be derived from the Taylor expansion by first solving for the coefficients of $D(x)$ and then solving for the coefficients of $N(x)$ using the above linear system. The Padé approximant can be used in finite difference methods much like the Taylor series as a polynomial approximation of a rational function. Where the Padé approximant is superior to a Taylor Series is for functions with poles and/or functions that do not converge to infinity such a periodic or asymptotic functions, as the polynomial denominator allows a truncated Pade approximant to converge towards a constant, instead of approaching negative or positive infinity as with a truncated Taylor Series. However, the Pade approximant is computationally intensive due to the need to solve a linear system to obtain the approximant in addition to deriving the Taylor Series for a function [10].

![Source: ](https://github.com/AndyYu25/CX4640-f23-final/blob/main/Pade-MacLaurinSeries.png)
[11]

## Spectral Methods

Spectral methods are a class of methods developed to solve mixed initial—boundary value problems that represent the solution as a linear combination of selected basis functions [12]. As spectral methods are based around a linear combination of basis functions, they are based on the global behavior of the function, as opposed to the local behavior in evaluating a selection of nearby points that is used for finite difference approximation. 

The overall algorithmic approach to differentiating with spectral methods is as follows:[13]

1. Compute a polynomial interpolation from the function to a Chebyshev series.
2. Compute Chebyshev series coefficients.
3. Differentiate the Chebyshev series.
4. Compute the interpolation from the Chebyshev series to a function.

The reason to convert the function into a Chebyshev polynomial is because the Chebyshev polynomials can be manipulated in a stable manner using Fourier transforms due to their orthogonality [14]

Computing the polynomial interpolation both to and from a Chebyshev polynomial of degree n is done by evaluating the input function at the Chebyshev nodes, or the points $t_k = -\cos(\frac{2k-1}{n} * \frac{\pi}{2}), k = 1, ..., n$, thus obtaining a set of n points $(t_1, f(t_1)), ..., (t_n, f(t_n))$ [15]. The Chebyshev series coefficients are computed by a fast cosine transform, and the differentiation of the Chebyshev polynomial follows the following formula:

$$P_N'(x) = \displaystyle\sum_{k = 0}^{N - 2} d_k * T_k(x)$$

$$d_k = \displaystyle\sum_{j = k + 1, j + k odd}^{N}j * a_j, 1 \leq k \leq N - 2$$

$$d_0 = 1/2 * \displaystyle\sum_{j = 1, j odd}^{N}j * a_j$$

where $T_k(x)$ is the Chebyshev polynomial of degree k.

Spectral methods converge exponentially, so they have a higher order of accuracy compared to finite difference methods [16]. In addition, spectral algorithms, like Fast Fourier Transform, are heavily optimized, thus reducing the computing time for spectral methods. However, numerical differentiation by spectral methods is poorly conditioned, with a condition number proportional to $N^2$, so these methods are impractical for very high orders of accuracy [17].

## References

1. Kovalev, L. (2021). Definition of Derivative and the Order of Error. Numerical Methods with Programming. Syracuse University. https://drlvk.github.io/nm/section-differentiation-intro.html 
2. Heath, M. T. (2002). Scientific Computing (2nd ed.). McGraw-Hill. pg. 366
3. Ibid, pg. 368.
4. Ibid, pg. 369.
5. Shu, C.-W. (1997). Essentially Non-Oscillatory and Weighted Essentially Non-Oscillatory Schemes for Hyperbolic Conservation Laws (pp. 1–78). National Air and Space Administration. pg. 1
6. Ibid, pg. 2.
7. Shu, C.-W. (2011, May 2). Weno Methods. Scholarpedia. http://www.scholarpedia.org/article/WENO_methods 
8. Ibid.
9.  Brezinski, C. (2002). Computational Aspects of Linear Control (1st ed.). Kluwer Academic Publishers. pg. 96.
10.  Ibid. pg. 80
11.  Ibid. pg. 80
12.  Gottlieb, D., & Orzag, S. A. (1977). Numerical Analysis of Spectral Methods. Office of Naval Research, page 1.
13. Dutt, A., Gu, M., & Rokhlin, V. (1993). (rep.). Fast Algorithms for Polynomial Interpolation, Integration and Differentiation (pp. 1–38). New Haven, CT: Yale University, pg. 28.
14. Ibid, pg. 1.
15. Ibid, pg. 26.
16. Gottlieb, D., & Orzag, S. A. (1977). Numerical Analysis of Spectral Methods. Office of Naval Research, page 1.
17. Dutt, A., Gu, M., & Rokhlin, V. (1993). (rep.). Fast Algorithms for Polynomial Interpolation, Integration and Differentiation (pp. 1–38). New Haven, CT: Yale University, pg. 29.
