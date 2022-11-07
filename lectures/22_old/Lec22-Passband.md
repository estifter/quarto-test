---
jupytext:
  formats: ipynb,md:myst
  text_representation:
    extension: .md
    format_name: myst
    format_version: 0.13
    jupytext_version: 1.11.5
kernelspec:
  display_name: Python 3
  language: python
  name: python3
---

+++ {"colab_type": "text", "id": "z7wtzH2Lszxp", "slideshow": {"slide_type": "slide"}}

# Lecture 22 - Passband and Baseband Equivalents


+++ {"colab_type": "text", "id": "z7wtzH2Lszxp", "slideshow": {"slide_type": "slide"}}

## Summary

In this lecture, we will develop relationships between passband signals and their baseband equivalents. We will also explore their impact on sampling and reconstruction, as well as filtering operations.

+++ {"slideshow": {"slide_type": "slide"}}

## Motivation in Course Context

**TBD** Point to where we are in radio architecture / system diagram.

Just as it is sometimes more convenient to look at a given problem in the time domain or the frequency domain, we will see that it can be convenient to model and analyze passband signals and systems at baseband, and even in discrete time instead of continuous time. This requires us to be able to convert from one perspective to another seamlessly.

+++ {"slideshow": {"slide_type": "slide"}}

## Outline

* Baseband-to-Passband Conversion

* Passband-to-Baseband Conversion

* Sampling and Reconstruction of Passband Signals

* Filtering of Passband Signals

* Mixing of Passband Signals

+++ {"slideshow": {"slide_type": "slide"}}

## Baseband-to-Passband Conversion

Let $x_0(t)\leftrightarrow X_0(f)$ be a bandlimited baseband signal with maximum frequency $W\ \mathrm{Hz}$. That is, $X_0(f) = 0$ for $|f|>W$. To allow for generality, we allow $x_0(t)$ to be complex-valued so that

$$
  x_0(t) = \mathrm{Re}\left\{x_0(t) \right\} + j\ \mathrm{Im}\left\{ x_0(t) \right\}
$$

+++ {"slideshow": {"slide_type": "subslide"}}

A real-valued passband signal $x_1(t)\leftrightarrow X_1(f)$ can be formed from $x_0(t)$ through the relationship

\begin{align*}
x_1(t) &= \sqrt{2}\ \mathrm{Re}\left\{ x_0(t) e^{j(2\pi f_1 t + \phi_1)} \right\} \\
&= \frac{1}{\sqrt{2}} \left[ x_0(t)e^{j(2\pi f_1 t + \phi_1)} + x_0^*(t)e^{-j(2\pi f_1 t + \phi_1)} \right]
\end{align*}

Taking Fourier transforms and recalling that $x_0^*(t)\leftrightarrow X_0^*(-f)$, we have

$$
  X_1(f) = \frac{1}{\sqrt{2}} \left[e^{j\phi_1} X_0(f-f_1) + e^{-j\phi_1} X_0^*(-(f+f_1)) \right]
$$

which we can verify satisfies Hermitian symmetry, i.e., $X_1(f) = X_1^*(-f)$ for all $-\infty < f < +\infty$.

Since we assumed $X_0(f)=0$ for $|f|>W$, we have

\begin{align*}
X_0(f-f_1)&=0\ \textrm{for }|f-f_1|>W \\
X_0^*(-(f+f_1))&=0\ \textrm{for }|f+f_1|>W
\end{align*}

Generally, to prevent these two images of $X_0(f)$ from overlapping in $X_1(f)$, we require $f_1 > W$. In that case, $X_1(f)$ is a passband signal with (passband) bandwidth at most $2W$, and we refer to $x_1(t)$ as the *passband equivalent* of $x_0(t)$ with center frequency $f_1$ and phase $\phi_1$.

+++ {"slideshow": {"slide_type": "subslide"}}

Because of the factor of $\sqrt{2}$, one can verify that if the two images do no overlap, the energies of the baseband and passband signals are the same, i.e.,

$$
  \int_{-\infty}^{+\infty} |x_0(t)|^2 dt = \int_{-\infty}^{+\infty} |x_1(t)|^2 dt \quad \quad \text{if } f_1 > W
$$

+++ {"slideshow": {"slide_type": "subslide"}}

Finally, an alternative representation of the passband signal follows from applying Euler's rule $e^{j\theta}=\cos(\theta)+j\ \sin(\theta)$, resulting in

\begin{align*}
x_1(t) &= \sqrt{2}\ \mathrm{Re}\left\{ x_0(t) e^{j(2\pi f_1 t + \phi_1)} \right\} \\
&= \sqrt{2}\ \left[ \mathrm{Re}\left\{x_0(t) \right\} \cos\left(2\pi f_1 + \phi_1 \right) - \mathrm{Im}\left\{ x_0(t) \right\} \sin\left(2\pi f_1 + \phi_1 \right) \right]
\end{align*}

+++ {"slideshow": {"slide_type": "subslide"}}

Comments

* Baseband-to-passband conversion is equivalent to idealized quadrature multiplexing; however, we stress that we are developing it as a foundational tool for modeling practical upconvestion techniques.

* Conventional treatments set $\phi_1=0$, but we allow $\phi_1\neq 0$ so that we keep track of the phase and allow for different oscillators to have distinct phases in later developments.

* Also conventionally, the operation either has a factor of $2$ or no factor at all. We shall use the factor $\sqrt{2}$ so that the converstion is energy-preserving.

+++ {"slideshow": {"slide_type": "slide"}}

## Passband-to-Baseband Conversion

Let $x_1(t) \leftrightarrow X_1(f)$ be a given real-valued, passband signal with passband bandwidth $2W$ and center frequency $f_1 > 0$, such that $X_1(f)=0$ for $|f-f_1|>W$ or $|f+f_1|>W$, where $W$ is as small as possible.

Since $x_1(t)$ is real-valued, its Fourier transform is Hermitian symmetric, i.e., $X_1(f) = X_1^*(-f)$ for all $-\infty < f < +\infty$. Thus, it is fully specified by only its positive frequencies

$$
  X_{1+}(f) = X_1(f)u(f)
$$

Furthermore, if we apply a frequency shift of $-f_1$, the result $X_{1+}(f+f_1)$ is baseband bandlimited with maximum frequency $W$.

Thus, we define the *baseband equivalent* $x_0(t)\leftrightarrow X_0(f)$ of $x_1(t)$ relative to frequecny $f_1$ and phase $\phi_1$ as

$$
  X_0(f) = \sqrt{2} e^{-j\phi_1} X_1(f+f_1)u(f+f_1)
$$

which has inverse Fourier transform

$$
  x_0(t) = \sqrt{2} x_{1+}(t) e^{-j(2\pi f_1 t - \phi_1)}
$$

+++ {"slideshow": {"slide_type": "subslide"}}

Since we have previously established that $x_{1+}(t) = \frac{1}{2} \left[x_1(t) + j\ x_{1h}(t) \right]$, where $x_{1h}(t)$ is the Hilbert transform of $x_1(t)$, we then have

$$
  x_0(t) = \frac{1}{\sqrt{2}} \left[x_1(t) + j\ x_{1h}(t)\right]\left[\cos(2\pi f_1 t + \phi_1) - j\ \sin(2\pi f_1 + \phi_1) \right]
$$

so that

\begin{align*}
\mathrm{Re}\left\{ x_0(t) \right\} &= \frac{1}{\sqrt{2}} \left[x_1(t)\cos(2\pi f_1 t + \phi_1) + x_{1h}(t) \sin(2\pi f_1 t + \phi_1) \right] \\
\mathrm{Im}\left\{ x_0(t) \right\} &= \frac{1}{\sqrt{2}} \left[ x_{1h}(t)\cos(2\pi f_1 t + \phi_1) - x_1(t) \sin(2\pi f_1 t + \phi_1) \right]
\end{align*}

+++ {"slideshow": {"slide_type": "subslide"}}

The baseband equivalent can also be obtained by first frequency shifting and then ideal lowpass filtering. Specifically,

$$
  X_0(f) = \sqrt{2} e^{-j\phi_1} X_1(f+f_1)\ \mathrm{rect}(f/(2W))
$$

so that

$$
  x_0(t) = (x_1(t) e^{-j(2\pi f_1-\phi_1)}) * \sqrt{2}\ \mathrm{sinc}(2W t)
$$

+++ {"slideshow": {"slide_type": "subslide"}}

Again, the factor of $\sqrt{2}$ ensures that the signal energies are consistent

$$
  \int_{-\infty}^{+\infty} |x_0(t)|^2 dt = \int_{-\infty}^{+\infty} |x_1(t)|^2 dt \quad \quad \text{if } f_1 > W
$$

+++ {"slideshow": {"slide_type": "slide"}}

## Sampling and Reconstruction of Passband Signals

Since a passband signal $x_1(t)$ centered at frequency $f_1$ with passband bandwidth $2W$ has a baseband-equivalent signal $x_0(t)$ with baseband bandwidth $W$, we can apply the sampling theorem to represent $x_0(t)$ and, in turn, $x_1(t)$.

+++ {"slideshow": {"slide_type": "subslide"}}

Specifically, let $x_0[n]=x_0(nT_s)$ be equally spaced samples of $x_0(t)$ with period $T_s$ and sampling frequency $f_s = 1/T_s$. Then we know that $x_0(t)$ can be reconstructed from its samples through bandlimited interpolation provided $f_s > 2W$.

+++ {"slideshow": {"slide_type": "subslide"}}

**TBD:** Block diagrams for passband-to-baseband followed by sampling, interpolation followed by baseband-to-passband

+++ {"slideshow": {"slide_type": "subslide"}}

It is important to stress that by exploiting passband-to-baseband and passband-to-baseband relationships, we can actually use a lower sampling rate for $x_0(t)$ than we normally would for $x_1(t)$. In particular, direct sampling of the passband signal $x_1(t)$ would require a minimum sampling rate of $2(f_1+W)$ real-valued samples per second, whereas direct sampling of the baseband-equivalent signal $x_0(t)$ requires a minimum sampling rate of only $2W$ complex-valued samples per second. For large center frequencies $f_1$, the required sampling rate reduction can be substantial!

+++ {"slideshow": {"slide_type": "slide"}}

## Filtering of Passband Signals

Let $x_1(t) = \sqrt{2} \mathrm{Re}\left\{ x_0(t) e^{j(2\pi f_1 t + \phi_1)} \right\}$ be a passband signal with bandwidth $2W$, with $x_0(t)$ its baseband-equivalent signal, and consider the filtering operation

$$
  y_1(t) = h(t) * x_1(t) \quad \leftrightarrow \quad Y_1(f) = H(f) X_1(f)
$$

where $h(t) \leftrightarrow H(f)$ represent the impulse response and frequency response, respectively, of a continuous-time LTI system.

+++ {"slideshow": {"slide_type": "subslide"}}

The output is also a passband signal centered at frequency $f_1$ with bandwidth at most $2W$. It can therefore be expressed as

$$
  y_1(t) = \sqrt{2}\ \mathrm{Re}\left\{ y_0(t) e^{j(2\pi f_1 + \phi_1)} \right\}
$$

where $y_0(t)$ is its baseband equivalent. From the development of passband-to-baseband conversion above, we have

\begin{align*}
Y_0(f) = \sqrt{2} e^{-j\phi_1} Y_{1+}(f+f_1) &= \sqrt{2} e^{-j\phi_1} H(f+f_1)\ X_1(f+f_1) u(f+f_1) \\
&= H(f+f_1) \left[ \sqrt{2} e^{-j\phi_1} X_{1+}(f+f_1) \right] \\
&= H(f+f_1) X_0(f)
\end{align*}

+++ {"slideshow": {"slide_type": "subslide"}}

This last result suggests that we can model and analyze the behavior of the LTI system $H(f)$ on passband signals as a *baseband-equivalent system*. Specifically, if we define

$$
  H_0(f) = H(f+f_1)\ \mathrm{rect}(f/(2W))
$$

with inverse Fourer transform

$$
  h_0(t) = (h(t) e^{-j2\pi f_1 t}) * \mathrm{sinc}(2W t)
$$

the baseband-equivalent input-output relationship becomes

$$
  Y_0(f) = H_0(f)X_0(f) \quad \leftrightarrow \quad y_0(t) = h_0(t) * x_0(t)
$$

+++ {"slideshow": {"slide_type": "subslide"}}

**TBD:** Block diagrams between CT passband and CT baseband equivalents

+++ {"slideshow": {"slide_type": "subslide"}}

Finally, given that $x_0(t)$, $h_0(t)$, and $y_0(t)$ are bandlimited to maximum frequency $W$, we can sample each one of them and model the behavior of the system via a *discrete-time, baseband-equivalent system*. Specifically, setting

$$
  x_0[n] = x_0(nT_s) \quad h_0[n] = h_0(nT_s) \quad y_0[n] = y_0(nT_s)
$$

where the sampling period satisfies $T_s < 1/(2W)$, we have

$$
  y_0[n] = h_0[n] * x_0[n]
$$

The greatest challenge is that all of these signals are complex-valued, but this is not generally a problem when we perform the convolutions via a computer.

+++ {"slideshow": {"slide_type": "subslide"}}

**TBD:** Block digrams between CT baseband and DT baseband equivalents

+++ {"slideshow": {"slide_type": "slide"}}

## Mixing of Passband Signals

We now consider mixing together two passband signals $x_1(t)$ and $x_2(t)$ generated from two baseband signals $m_1(t)$ and $m_2(t)$, respectively, via

\begin{align*}
x_1(t) &= \sqrt{2} \mathrm{Re}\left\{m_1(t) e^{j(2\pi f_1 + \phi_1)} \right\} \\
x_2(t) &= \sqrt{2} \mathrm{Re}\left\{m_2(t) e^{j(2\pi f_2 + \phi_2)} \right\}
\end{align*}

For generality, we allow the two baseband signals to have distinct bandwidths $W_1$ and $W_2$, respectively, and we assume that $f_1 > W_1$ and $f_2 > W_2$.

+++ {"slideshow": {"slide_type": "subslide"}}

Writing the two passband signals in the form

$$
  x_k(t) = \frac{1}{\sqrt{2}} \left[m_k(t)e^{j(2\pi f_k + \phi_k)} + m_k^*(t) e^{-j(2\pi f_k + \phi_k)} \right], \quad k=1,2
$$

expanding the multiplication $x_1(t)x_2(t)$, and grouping terms, we obtain

\begin{align*}
x_1(t)x_2(t) = \frac{1}{\sqrt{2}} & \left[ \sqrt{2} \mathrm{Re}\left\{ m_1(t)m_2(t) e^{j(2\pi (f_1+f_2) + (\phi_1+\phi_2))} \right\} \right. \\
& \left. +\sqrt{2} \mathrm{Re}\left\{m_1(t)m_2^*(t) e^{j(2\pi (f_1-f_2) + (\phi_1-\phi_2))} \right\} \right]
\end{align*}

+++ {"slideshow": {"slide_type": "subslide"}}

From this we recognize that the output has two passband componenents:

* one at the sum frequency $f_1+f_2$ and sum phase $\phi_1+\phi_2$ with baseband equivalent $m_1(t) m_2(t)$, and

* another at the difference frequency $f_1 - f_2$ and difference phase $\phi_1 - \phi_2$ with baseband equivalent $m_1(t) m_2^*(t)$.

+++ {"slideshow": {"slide_type": "subslide"}}

As a result, we can alternatively form the mixing products of the baseband signals $m_1(t)m_2(t) \leftrightarrow M_1(f) * M_2(f)$ and $m_1(t)m_2^*(t) \leftrightarrow M_1(f) * M_2^*(-f)$ and then convert them to passband to obtain the desired result.

+++ {"slideshow": {"slide_type": "subslide"}}

### Example

```{figure} images/Lec22-01.jpg
---
name: fig1
width: "80%"
---
TBD.
```

```{figure} images/Lec22-02.jpg
---
name: fig2
width: "80%"
---
TBD.
```

+++ {"slideshow": {"slide_type": "subslide"}}

**TBD: Mixing products of three signals***
