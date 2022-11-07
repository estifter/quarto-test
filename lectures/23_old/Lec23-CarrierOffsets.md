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

# Lecture 23 - Carrier Offsets, Non-Coherent Demodulation

+++ {"colab_type": "text", "id": "z7wtzH2Lszxp", "slideshow": {"slide_type": "slide"}}

## Summary

In this lecture, we will develop models and a simple approach to dealing with carrier frequency offsets

+++ {"slideshow": {"slide_type": "slide"}}

## Motivation in Course Context

**TBD** Point to where we are in radio architecture / system diagram.

+++ {"slideshow": {"slide_type": "slide"}}

## Outline

* Modeling Carrier Offsets

* Non-Coherent Demodulation

+++ {"slideshow": {"slide_type": "slide"}}

## Quadrature Up- and Down-Conversion

In this section, we will see how carrier offsets affect quadrature up- and down-conversion.

Let $m(t)$ be a complex-valued baseband signal with bandwidth $W$, and let $x_{\mathrm{IF}}(t)$ be a passband equivalent signal at frequency $f_{\mathrm{IF}}$ and phase $\phi_{\mathrm{IF}}$. That is,

$$
  x_{\mathrm{IF}}(t) = \sqrt{2} \mathrm{Re} \left\{m(t) e^{j(2\pi f_{\mathrm{IF}} t + \phi_{\mathrm{IF}}})\right\}
$$

If we receive $y_{\mathrm{IF}}(t)$ and dowconvert with frequency $f'_{\mathrm{IF}}$, phase $\phi'_{\mathrm{IF}}$, and lowpass filter denoted $h_{\mathrm{LPF}}(t)$, then

$$
  \hat{m}(t) = \sqrt{2} h_{\mathrm{LPF}}(t) * \left[ y_{\mathrm{IF}}(t) e^{-j(2\pi f'_{\mathrm{IF}} t + \phi'_{\mathrm{IF}})} \right]
$$

+++

These operations are summarized the block diagram below


```{figure} images/Lec23-01.png
---
name: fig1
width: "80%"
---
TBD.
```

+++

Recall that

$$
  X_{\mathrm{IF}}(f) = \frac{1}{\sqrt{2}} \left[e^{j\phi_{\mathrm{IF}}} M(f-f_{\mathrm{IF}}) + e^{-j\phi_{\mathrm{IF}}} M^*(-(f+f_{\mathrm{IF}})) \right]
$$

so that, assuming for the time being that $y_{\mathrm{IF}}(t)=x_{\mathrm{IF}}(t)$,

\begin{align*}
Y_{\mathrm{MIX}}(f) &= e^{-j\phi'_{\mathrm{IF}}} Y_{\mathrm{IF}}(f+f'_{\mathrm{IF}}) \\
&= \frac{1}{\sqrt{2}} \left[ e^{j(\phi_{\mathrm{IF}}-\phi'_{\mathrm{IF}})} M(f-(f_{\mathrm{IF}}-f'_{\mathrm{IF}})) + e^{-j(\phi_{\mathrm{IF}}+\phi'_{\mathrm{IF}})} M^*(-(f+f_{\mathrm{IF}}+f'_{\mathrm{IF}}))))\right]
\end{align*}

+++

Assuming that $f'_\mathrm{IF}$ is close to $f_{\mathrm{IF}}$, the lowpass filter should pass the component shifted to $f_{\mathrm{IF}}-f'_{\mathrm{IF}}$ and reject the component shifted to $-(f_{\mathrm{IF}}+f'_{\mathrm{IF}})$. Then

$$
  \hat{M}(f) = e^{j(\phi_{\mathrm{IF}}-\phi'_{\mathrm{IF}})} M(f-(f_{\mathrm{IF}}-f'_{\mathrm{IF}}))
$$

which results in a baseband-equivalent system model

$$
  \hat{m}(t) = e^{j(2\pi \Delta f_{\mathrm{IF}} t + \Delta \phi_{\mathrm{IF}})} m(t)
$$

where $\Delta f_{\mathrm{IF}} := f_{\mathrm{IF}}-f'_{\mathrm{IF}}$ is the *frequency offset* and $\Delta \phi_{\mathrm{IF}} := \phi_{\mathrm{IF}}-\phi'_{\mathrm{IF}}$ is the *phase offset* between the up- and down-converstion oscillators.

+++

**TBD: I/Q separation**

+++

**TBD: Lowpass filter constraints**

+++

**TBD: Visualize on XY chart**

+++

## Non-Coherent Demodulation

Based upon Lecture 20, we can develop a receiver algorithm and bit-error rate performance analysis for binary modulation for the case in which we transmit one of two baseband signals $m_0(t)$ and $m_1(t)$, correspoding to a zero bit or one bit, respectively.

In the presence of the frequency and phase offsets modeled above, the predicted output signals in the receiver would be

$$
  \hat{m}_0(t) = e^{j(2\pi \Delta f_{\mathrm{IF}} t + \Delta \phi_{\mathrm{IF}})} m_0(t) + Z(t)
$$

and

$$
  \hat{m}_1(t) = e^{j(2\pi \Delta f_{\mathrm{IF}} t + \Delta \phi_{\mathrm{IF}})} m_1(t) + Z(t)
$$

In order to apply the minimum squared-distance detector that we developed in Lecture 20, we would have to know the values of $\Delta f_{\mathrm{IF}}$ and $\Delta \phi_{\mathrm{IF}}$. In the context of frequency and phase offsets, this is called *coherent demodulation*, and will be developed in the next lecture.

On the other hand, recognizing that $|e^{j\theta}|=1$, we can instead compute the magnitudes of the received signals and apply the mininum squared-distance detector to the signals

$$
  |\hat{m}_0(t)| = |e^{j(2\pi \Delta f_{\mathrm{IF}} t + \Delta \phi_{\mathrm{IF}})} m_0(t) + Z(t) |
$$

and

$$
  |\hat{m}_1(t)| = |e^{j(2\pi \Delta f_{\mathrm{IF}} t + \Delta \phi_{\mathrm{IF}})} m_1(t) + Z(t) |
$$

This is called *non-coherent demodulation*.

+++

## Heterodyne Up- and Down-Conversion

**TBD:** Will develop frequency offsets between the second stage of up- and down-conversion as well. Take away will be that frequency offsets and phase offsets are additive, and we obtain the same baseband equivalent model as for the quadradture up- and down-conversion case.
