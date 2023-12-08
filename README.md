```
---
Name: Andy Yu
Topic: 35
Title: Higher-Order Accurate Derivative Approximations
---
```
# Higher-Order Accurate Derivative Approximations

In numerical analysis, higher order accurate derivative approximations are a class of methods and algorithms that estimate the derivatives of a given function with the values of the function and other information about the function to a high degree of accuracy. 

## Introduction

The definition of a derivative is as follows:

$$f'(x) = \lim_{h \to 0 }\frac{f(x + h) - f(x)}{h}$$

Derivative approximations aim to evaluate the derivative by utilizing this limit definition for some concrete value of h, known as the step size. Thus, accuracy in differentiation is expressed by degrees of accuracy where the derivative error $|e(h)|$ is bounded by some polynomial $C|h|^n$. If $|e(h)| \leq $C|h|^n$, then the approximation is $n$th-order accurate [1]. 

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

Essentially Non-Oscillatory (ENO) methods are a class of finite difference methods designed to approximate smooth solutions to functions with discontinuities while maintaining a high order of accuracy. The fundamental principle behind ENO schemes is selecting the smoothest interpolant in a given area [https://ntrs.nasa.gov/api/citations/19980007543/downloads/19980007543.pdf, pg 1].

More formally, consider a discrete grid $a = x_{1/2} < x_{3/2} < ... < x_{n - 1/2} < x_{n + 1/2} = b$ in the domain of a function $f$. 

For some $i$ such that $0 \leq i \leq n$, there are several degree k finite difference approximations for f at the interval $\{x_{i - 1/2}, x_{i + 1/2}\}$. With an ENO scheme, the smoothest approximation at that interval is selected and utilized as the interpolant for. This process is repeated across all intervals between a and b.     

### Weighted Essentially Non-Oscillatory Methods

Instead of discarding all but the smoothest interpolation, Weighted Essentially Non-Oscillatory Methods consider all the possible interpolants and add them together as a weighted sum.  

THe primary advantage WENO/ENO schemes have over conventional finite difference methods is that WENO/ENO schemes will choose interpolants such that the function will not oscillate wildly around discontinuities. This makes the scheme advantageous for scenarios where discontinuities occur frequently, such as with fluid dynamics and traffic simulation.

## Padé Approximations

Padé approximations are rational functions that are a ratio of polynomials such that their power series agrees agrees with the power series of the function they are agreeing with. Given a rational function $f$, the Padé approximation of $f$ is following:
$$[p/q]_f(x) = N(x)/D(x)$$

such that the degree of N $\leq p$, the degree of D $\leq Q$, and $N(x) - f(x)D(x) = O(x^{p + q + 1}), (x \to 0)$ [5]. $N(x)$ and $D(x)$ can also be expressed as follows:

$$N(x) = \sum^{p}_{i = 0}a_i * x^i$$

$$D(x) = 1 + \sum^{q}_{i = 0}b_i * x^i$$

Let the Taylor expansion of $f$ be $T(x) = \displaystyle\sum_{i = 0}^{p + q} c_{i} * x^i $. As the Taylor expansion and the Pade approximation are equal, $T(x) = N(x)/D(x)$,  which can be rewritten as $D(x)T(x) = N(x)$ and then expanded as $(1 + \displaystyle\sum_{i = 0}^{q} b_i * x^i) * \displaystyle\sum_{i = 0}^{p + q} c_{i} * x^i = \displaystyle\sum^{p}_{i = 0}a_i * x^i$. By setting the coefficients on each side of the equation equal to each other, we get the following system of equations [6]:

$$a_0 = c_0b_0$$
$$a_1 = c_1b_0 + c_0b_1$$
$$...$$
$$a_p = c_pb_0 + c_{p - 1}b_1 + ... + c_{p-q}b_q$$
$$0 = c_{p+1}b_0 + c_pb_1 + ... + c_{p-q+1}b_q$$
$$...$$
$$0 = c_{p+q}b_0 + c_{p + q - 1}b_1 + ... + c_pb_q$$

Thus, the Padé approximant can be derived from the Taylor expansion by first solving for the coefficients of $D(x)$ and then solving for the coefficients of $N(x)$ using the above linear system. The Padé approximant can be used in finite difference methods much like the Taylor series as a polynomial approximation of a rational function. 

## Spectral Methods

Spectral methods are a class of methods developed to solve mixed initial—boundary value problems that represent the solution as a linear combination of selected basis functions [https://apps.dtic.mil/sti/pdfs/ADA056922.pdf, page 1]. 





Spectral methods converge exponentially, so they have a higher order of accuracy compared to finite difference methods [https://apps.dtic.mil/sti/pdfs/ADA056922.pdf, page 2].

## Finite Element Methods



## What your article should not contain

* Videos (But `.gif` is acceptable! Though __do not__ upload anything that is more than 25MB large. Github will reject it.)
* Grammar or spelling errors
* Plagiarism. I will check your documents (it's easy to do this since they will all be plain text). If you plagiarize, it will be obvious. 

## Some things you are allowed to do but might not be obvious

* You _are_ allowed to use external links! You are also encouraged to cite other books and papers (see below for that).
* You _are_ allowed to use HTML within your Markdown document, so long as it renders appropriately within Github. 
You should check that it does render appropriately by creating a private GitHub repository for your article and ensuring it works as you expect! 

## How to submit

Submit your document formatted as a `.zip` (if you included images) or `.md` (if not) to Canvas.

__Be sure to create a (private) repository on your own Github profile to make sure that your article shows up as you intended!__


## Formatting 

Use "Github" flavored markdown, which is a slightly more powerful version of the usual markdown. The file extension is usually `.md`.
You can include equations in the usual LaTeX-like way, $Ax=b$, or like this
$$Ax=b.$$
You can view the source of this markdown document by clicking the edit/raw button on its source, it's also at [this link](https://raw.githubusercontent.com/numerical-analysis-f23/project-help/main/readme.md).

![](images.png)

You can also include images like this (notice that the image file is in the repository)! 

![](example_gif.gif)

You can also include `.gif`s!

Pseudocode is included via triple backticks, like this
```
Pseudocode
Can
Go 
Here
```
and inline code can `go like this`.

Here are some links for Github-flavored markdown syntax that could be helpful:
* https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet
* https://docs.github.com/en/get-started/writing-on-github/getting-started-with-writing-and-formatting-on-github/basic-writing-and-formatting-syntax

## Length

The length of your article is flexible. 
However, I understand it is useful to put some bounds on things.

Here are some example articles that are probably too short
* http://www.scholarpedia.org/article/Fixed_point
* http://www.scholarpedia.org/article/Normal_forms
* https://en.wikipedia.org/wiki/Numerical_method

Here is one that [is probably too long](https://en.wikipedia.org/wiki/Floating-point_arithmetic) for this project, though I will certainly not mark you down if you write something this comprehensive!

## Audience

Write your article such that it could be understood by a student who is about to take this course, but hasn't started yet (you just a few months ago!).
For example, [here is one](http://www.scholarpedia.org/article/WENO_methods) that is presented in a way that is likely too complex for your mock audience. 

## How you will be graded

From the `topics.md` document: 
> Some of these topics may be easier or harder to write about depending on at what length they were discussed in class. Some of these topics were not discussed in class, but are very closely related to what was discussed in class. This will be taken into account while grading, with somewhat fewer details and less depth or "strictness" expected for more complex or less-discussed topics. In this vein, a uniformly knowledgeable person would find all of these topics equally challenging (or easy!). In practice, I recommend prioritizing topics that seem interesting to you (or suggest your own, if you like!).

### Rubric

* 60% __Technical correctness and completeness.__ Get full points here by
	* Not saying things that are technically incorrect. 
	* Completeness is a bit nebulous here. You do not need to include _everything_ you could possibly discuss about your topic (that could be an entire textbook in some cases!). 
	Instead, include a combination of breadth and depth that is in line with some of the Wikipedia/Scholarpedia examples provided. 
	* In many cases you should include some equations, pseudocode, and examples to explain your topic, though there are a few exceptions to this. Use your own discretion.
	* Not mentioning a method that is critical to your topic could result in deducted points, especially if that topic was discussed at length in class.

* 20% __Clarity.__ Get full points here by
	* Not including topics that are irrelevant to yours (at least not without an explanation of why they are there!). 
	* Do include references to topics, via links and proper references, that are relevant to yours. 
	* Use hyperlinks to navigate around your document (for example, [like this](https://stackoverflow.com/questions/2822089/how-to-link-to-part-of-the-same-document-in-markdown)). 
	* Make your document intellectually accessible to your mock audience (discussed above). 
	* No nonsequiturs.
	* Use proper English grammar. 
	* No spelling mistakes.

* 10% __Presentation.__ Get full points here by
	* Using high-quality graphics (if you use graphics). 
	* Consistent style.
  	* Consistent referencing style (e.g., APA formatting).
	* Proper use of tables, figures, lists (bulleted and enumerated), etc.
	* _Make it something you would be proud to show to your colleagues!_

* 10% __Organization.__ Get full points here by 
	* Separate and organize sections and subsections appropriately. 
	* Include a table of contents
	* References and URL refs. in an appropriate location.

## References

1. https://drlvk.github.io/nm/section-differentiation-intro.html
2. https://e6.ijs.si/~roman/files/tmp/M.Heath-SComputing/scientific-computing-michael-t-heath.pdf
3. Ibid.
4. Ibid.
5. https://link.springer.com/book/10.1007/978-1-4613-0261-2
6. ibid
7. This
8. Bryngelson, S. H., & Freund, J. B. (2018). Global stability of flowing red blood cell trains. Physical Review Fluids, 3(7). https://doi.org/10.1103/physrevfluids.3.073101 
