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

# Lecture 21 - Heterodyne Interference Channel Model

+++ {"colab_type": "text", "id": "z7wtzH2Lszxp", "slideshow": {"slide_type": "subslide"}}

## Summary

In this lecture, we will extend Lectures 14-16 to develop a detailed channel model for the heterodyne transmitter and receiver sub-systems, including mixer leakage and self-mixing, oscillator frequency offsets, and interference. This will allow us to consider an important design topic called frequency planning, and to apply it to our laboratory radios in the context of ISM band interference.

+++ {"slideshow": {"slide_type": "slide"}}

## Motivation in Course Context

Having built heterodyne radio transmitters and receivers in the lab, we will now encapsulate everything together into the *heterodyne interference channel* shown in the figure below.

```{figure} images/Lec21-01.png
---
name: heterodyne-interference-channel
width: "80%"
---
Heterodyne interference channel
```

+++ {"slideshow": {"slide_type": "subslide"}}

Much like the additive noise model that we developed in Lecture 20, the process of simplifying the channel model as much as possible, developing and simplifying receiver algorithms, and characterizing end-to-end performance ultimately allows us to design the input signal sets.

As an intermediate step, we will develop a tool called *frequency planning* that allows the receiver to suppress known interference signals to a great extent.

+++ {"slideshow": {"slide_type": "slide"}}

## Outline

* Heterodyne Transmitter

* Heterodyne Receiver

* Image Selection / Frequency Planning

+++ {"slideshow": {"slide_type": "slide"}}

## Heterodyne Transmitter

In this section, we will highlight the key design parameters and develop a simple model for the heterodyne transmitter, including leakage and self-mixing. The structure and notation that we adopt is shown in the figure below.

```{figure} images/Lec21-02.png
---
name: adi-adc-application
width: "80%"
---
Heterodyne transmitter.
```

+++ {"slideshow": {"slide_type": "subslide"}}

### IF Upconversion

For generality in later lectures, we allow the modulating signal $m(t)$ to be complex-valued with

$$
  m(t)=m_I(t) + j m_Q(t)
$$

and we model the IF upconversion process in the time domain as

\begin{align*}
s_{\mathrm{IF}}(t)&=\mathrm{Re}\left\{m(t)\ e^{j2\pi f_{\mathrm{IF}} t} \right\} \\
&= m_I(t) \cos(2\pi f_{\mathrm{IF}} t) - m_Q(t) \sin(2\pi f_{\mathrm{IF}} t)
\end{align*}

and in the frequency domain as

\begin{align}
S_{\mathrm{IF}}(f) = \frac{1}{2} \left[M(f-f_{\mathrm{IF}}) +  M^*(-(f+f_{\mathrm{IF}}))\right]
\end{align}

+++ {"slideshow": {"slide_type": "subslide"}}

We note that:

* For SSB, we can set $m_Q(t)$ to be plus or minus the Hilbert transform of $m_I(t)$.

* We do not include leakage or self-mixing in the IF converstion process as digital implementations with sufficient resolution can largely suppress these effects.

+++ {"slideshow": {"slide_type": "slide"}}

### RF Mixing

Based upon our discussion of practical mixers in Lecture 13, we model the output of the mixer as including the following components in the frequency domain

\begin{align*}
S_{\mathrm{MIX}}(f) =& \frac{A}{2}\left[S_{\mathrm{IF}}(f - f_{\mathrm{LO}}) +  S_{\mathrm{IF}}(f + f_{\mathrm{LO}})\right] \\
&+\frac{B}{2} \left[\delta(f-f_{\mathrm{LO}}) + \delta(f+f_{\mathrm{LO}}) \right] \\ 
&+C S_{\mathrm{IF}}(f) \\
&+\frac{D}{2} \left[\delta(f) + \frac{1}{2}\delta(f-2f_{\mathrm{LO}}) + \frac{1}{2}\delta(f+2f_{\mathrm{LO}})  \right] \\
&+E S_{\mathrm{IF}}(f) * S_{\mathrm{IF}}(f)
\end{align*}

+++

We note that the parameters $A,B,C,D,E$ are specific to the mixer, and we recall that

* Parameter $A$ captures the conversion gain or loss (typically loss) of the desired output $s_{\mathrm{IF}}(t)\cos(2\pi f_{\mathrm{LO}} t)$.

* Parameter $B$ captures the degree to which the LO is leaked to the output.

* Parameter $C$ captures the degree to which the IF input is leaked to the output.

* Parameter $D$ captures the strength of the LO self-mixing in the output.

* Parameter $E$ captures the strength of the IF input self-mixing in the output.

+++ {"slideshow": {"slide_type": "subslide"}}

Observing that

\begin{align*}
\frac{1}{2}\left[S_{\mathrm{IF}}(f-f_{\mathrm{LO}}) + S_{\mathrm{IF}}(f+f_{\mathrm{LO}}) \right] = \phantom{+} &\frac{1}{4} \left[M(f-(f_{\mathrm{IF}}+f_{\mathrm{LO}})) +  M^*(-(f+(f_{\mathrm{IF}}-f_{\mathrm{LO}})))\right] \\
 + &\frac{1}{4} \left[M(f-(f_{\mathrm{IF}}-f_{\mathrm{LO}})) +  M^*(-(f+(f_{\mathrm{IF}}+f_{\mathrm{LO}})))\right] \\
= \phantom{+} &\frac{1}{4} \left[M(f-(f_{\mathrm{IF}}+f_{\mathrm{LO}}))+  M^*(-(f+(f_{\mathrm{IF}}+f_{\mathrm{LO}})))\right] \\
+ &\frac{1}{4} \left[M(f-(f_{\mathrm{IF}}-f_{\mathrm{LO}}))+  M^*(-(f+(f_{\mathrm{IF}}-f_{\mathrm{LO}})))\right]
\end{align*}

we see that there are two images of $M(f)$ (respectively $M^*(-f)$), one centered at $f_{\mathrm{IF}}+f_{\mathrm{LO}}$ (respectively $-(f_{\mathrm{IF}}+f_{\mathrm{LO}})$) and the other centered at $f_{\mathrm{IF}}-f_{\mathrm{LO}}$ (respectively $-f_{\mathrm{IF}}+f_{\mathrm{LO}}$).

+++ {"slideshow": {"slide_type": "subslide"}}

Specifically, we can write the desired mixer output (double sideband) as

$$
  S_{\mathrm{DSB}}(f) := \frac{A}{2}\left[S_{\mathrm{IF}}(f-f_{\mathrm{LO}}) + S_{\mathrm{IF}}(f+f_{\mathrm{LO}}) \right]
$$

and

\begin{align*}
S_{\mathrm{IF+LO}}(f) &:= \frac{1}{2} \left[M(f-(f_{\mathrm{IF}}+f_{\mathrm{LO}}))+  M^*(-(f+(f_{\mathrm{IF}}+f_{\mathrm{LO}})))\right] \\
S_{\mathrm{IF-LO}}(f) &:= \frac{1}{2} \left[M(f-(f_{\mathrm{IF}}-f_{\mathrm{LO}}))+  M^*(-(f+(f_{\mathrm{IF}}-f_{\mathrm{LO}})))\right]
\end{align*}

so that

$$
  S_{\mathrm{DSB}}(f) = \frac{A}{2} \left[S_{\mathrm{IF+LO}}(f) + S_{\mathrm{IF-LO}}(f) \right]
$$

This representation makes the two image center frequencies more apparent.

+++ {"slideshow": {"slide_type": "subslide"}}

The figure below illustrates the spectrum at the output of the mixer for an input signal $M(f)$ baseband bandlimited to maximum frequency $B\ \mathrm{Hz}$, along with the notation we have introduced to this point.

```{figure} images/Lec21-03.png
---
name: mixer-output-spectrum
width: "80%"
---
Mixer output spectrum.
```

**TBD:** Change $B$ to $W$ to represent bandwidth, since we have $B$ being a coefficient of the mixer. Otherwise switch mixer coefficients to $\beta_{...}$.

+++ {"slideshow": {"slide_type": "subslide"}}

Recalling that the desired output signal from the heterodyne transmitter is only one or the other of the images $S_{\mathrm{IF+LO}}(f)$ or $S_{\mathrm{IF-LO}}(f)$, we observe that the mixer creates a significant amount of *self-interference* as a result of the non-ideal properties of the mixer. To reduce the impact of many of these interference compoents, we apply an RF bandpass filter.

+++ {"slideshow": {"slide_type": "slide"}}

### RF Bandpass Filter

Consider an RF bandpass filter with frequency response denoted $H_{\mathrm{BF}}(f)$. Such filters are often characterized by there insertion loss in dB as a function of frequency, namely, $-20\log_{10} |H_{\mathrm{RF}}(f)|$, with certain breakpoints in the frequency response for positive frequencies as suggested in the figure below.

```{figure} images/Lec21-04.png
---
name: filter-insertion-loss
width: "80%"
---
Filter insertion loss.
```

+++ {"slideshow": {"slide_type": "subslide"}}

Specifically:

* The *passband* is specified by the interval $f\in[f_1,f_2]$, with a maximum insertion loss of roughly $1\ \mathrm{dB}$.
* The *lower stopband* is specified by the interval $f\in[0,f_3]$, with a minimum insertion loss of $30+\ \mathrm{dB}$.
* The *upper stopband* is specified by the interval $f\in[f_4,f_5]$, with a minimum insertion loss of $30+\ \mathrm{dB}$.

+++ {"slideshow": {"slide_type": "subslide"}}

The parameters $f_1,f_2,f_3,f_4,f_5$ and the desired stopband insertion loss are the key parameters of the filter. We note that certain types of filters, e.g., distributed transmission line resonators, have $f_5 < +\infty$, so that a lumped lowpass filter may need to be added to ensure adequate attenuation at frequencies well above the desired passband.

+++ {"slideshow": {"slide_type": "subslide"}}

With this parameterization, we can now roughly summarize the design contraints for the transmitter in order to suppress the mixer self-interference components at the output . Specifically, the RF bandpass filter should significantly attenuate:

* Input leakage $C S_\mathrm{IF}(f)$ and self-mixing $E S_\mathrm{IF}(f)*S_\mathrm{IF}(f)$, requring $f_3 \gg 2(f_\mathrm{IF}+B)$. This ensures significant attenuation of the DC component of the LO self-mixing as well.

* The second harmonic of the LO self-mixing, requiring $f_4 \ll 2f_{\mathrm{LO}} \ll f_5$, or $f_4 \ll 2 f_{\mathrm{LO}}$ and an additional lumped lowpass filter with cutoff frequency just above $f_4$ and significant attenuation above $f_5$.

+++ {"slideshow": {"slide_type": "slide"}}

With these constraints satisfied, we can model the RF output of the transmitter as follows

\begin{align*}
S_{\mathrm{RF}}(f) &\approx H_{\mathrm{RF}}(f) \left[\frac{A}{2} S_{\mathrm{IF-FO}}(f) + \frac{B}{2}\left[\delta(f-f_{\mathrm{LO}}) + \delta(f+f_{\mathrm{LO}}) \right]+  \frac{A}{2} S_{\mathrm{IF+LO}}(f) \right] \\
\end{align*}

or

\begin{align*}
s_{\mathrm{RF}}(t) &\approx h_{\mathrm{RF}}(t) * \left[ \frac{A}{2} s_{\mathrm{IF-LO}}(t) + B \cos(2\pi f_{\mathrm{LO}} t) + \frac{A}{2} s_{\mathrm{IF+LO}}(t) \right]
\end{align*}

+++ {"slideshow": {"slide_type": "slide"}}

The last issue to address is image rejection. We have to decide to pass either the image $S_{\mathrm{IF+LO}}(f)$ or the image $S_{\mathrm{IF-LO}}(f)$. Each case is illustrated in the figure below for a given set of filter parameters, i.e, $f_{\mathrm{LO}}$ changes while $f_1,f_2,f_3,f_4,f_5$ are fixed.

```{figure} images/Lec21-05.png
---
name: image-rejection
width: "80%"
---
Image rejection.
```

+++ {"slideshow": {"slide_type": "slide"}}

#### Passing $S_{\mathrm{IF+LO}}(f)$

To pass $S_{\mathrm{IF+LO}}(f)$, we need $f_1 \le f_{\mathrm{IF}}+f_{\mathrm{LO}}-B$ and $f_{\mathrm{IF}}+f_{\mathrm{LO}}+B \le f_2$.  Then we take advantage of the lower stopband and transition band of the RF bandpass filter to supress everything else.

In particular, to suppress the LO leakage, we need $f_{\mathrm{LO}} < f_1$, and ideally $f_{\mathrm{LO}} < f_3$. And to suppress $S_{\mathrm{IF-LO}}(f)$, we need $-f_{\mathrm{IF}}+f_{\mathrm{LO}}+B < f_3$, and ideally $-f_{\mathrm{IF}}+f_{\mathrm{LO}}-B \ll f_3$.

The end result is that

$$
  |H_{\mathrm{RF}}(-f_{\mathrm{IF}}+f_{\mathrm{LO}})| \ll |H_{\mathrm{RF}}(f_{\mathrm{LO}})| \ll |H_{\mathrm{RF}}(f_{\mathrm{IF}}+f_{\mathrm{LO}})| \approx 1
$$

+++ {"slideshow": {"slide_type": "slide"}}

#### Passing $S_{\mathrm{IF-LO}}(f)$

To pass $S_{\mathrm{IF-LO}}(f)$, we need $f_1 \le -f_{\mathrm{IF}}+f_{\mathrm{LO}}-B$ and $-f_{\mathrm{IF}}+f_{\mathrm{LO}}+B \le f_2$.  Then we take advantage of the upper stopband and transition band of the RF bandpass filter to supress everything else.

In particular, to suppress the LO leakage, we need $f_2 < f_{\mathrm{LO}}$, and ideally $f_4 < f_{\mathrm{LO}}$. And to suppress $S_{\mathrm{IF+LO}}(f)$, we need $f_4 < f_{\mathrm{IF}}+f_{\mathrm{LO}}-B$, and ideally $f_4 \ll f_{\mathrm{IF}}+f_{\mathrm{LO}}-B$.

The end result is that

$$
  1 \approx |H_{\mathrm{RF}}(-f_{\mathrm{IF}}+f_{\mathrm{LO}})| \gg |H_{\mathrm{RF}}(f_{\mathrm{LO}})| \gg |H_{\mathrm{RF}}(f_{\mathrm{IF}}+f_{\mathrm{LO}})|
$$


+++ {"slideshow": {"slide_type": "slide"}}

A natural question to ask ourselves is which image to pass for transmission, and which image to reject? We defer a complete answer to this question until after we consdier the heterodyne receiver.

+++ {"slideshow": {"slide_type": "slide"}}

### IF Frequency Selection

We see from the above discussion that, the larger the intermediate frequency $f_{\mathrm{IF}}$, the more a given bandpass filter can suppress the undesired image as well as the LO leakage. Furthermore, the larger $f_{\mathrm{IF}}$, the more tuning range we can support within the RF passband $f\in[f_1,f_2]$.

Rule of Thumb: $f_{\mathrm{IF}} > f_{\mathrm{LO}}/10$.

**TBD: Flesh out argument. (JDC) If you pick too large a ratio (as we have done in our lab due to the low IF frequency we are limited to, you end up requireing very high Q filters at RF. This is becauwse the image band is only separated from the RF by 2*fIF. This is why we need to insane air-filled cavity filters at RF.**

+++ {"slideshow": {"slide_type": "slide"}}

## Heterodyne Receiver

In this section, we will highlight the key design parameters and develop a simple model for the heterodyne receiver, including interference, mixer leakage and self-mixing, and frequency and phase offets. The structure and notation that we adopt is shown in the figure below.

```{figure} images/Lec21-06.png
---
name: fig06
width: "80%"
---
TBD
```

+++ {"slideshow": {"slide_type": "subslide"}}

We consider a received signal of the form

$$
  r(t) = \alpha s_{\mathrm{RF}}(t-\tau) + I(t) + Z(t)
$$

+++ {"slideshow": {"slide_type": "subslide"}}

where:

* $\alpha$ captures the effects of antenna radiation patterns and polarizations as well as free-space attenuation of the electromagnetic wave

* $\tau$ captures the effect of propagation delay between the antennas for the electromagnetic wave

* $I(t)$ represents an interference signal centered at frequency $f_{\mathrm{INT}}$ with passband bandwith $2 B_{\mathrm{INT}}$

* $Z(t)$ represents the receive input-referred noise from thermal effects in the receive circuitry, and is modeled as stationary white Gaussian with power spectral density $S_Z(f) = N_0 / 2$.

+++ {"slideshow": {"slide_type": "slide"}}

### RF Bandpass Filter

For simplicity, we assume that the receiver RF bandpass filter is the same as that of the transmitter. So it's frequency response is $H_{\mathrm{RF}}(f)$ with $f_1,f_2,f_3,f_4,f_5$ and stopband attenuations as outlined above.

+++ {"slideshow": {"slide_type": "subslide"}}

Then we have

$$
  r_{\mathrm{RF}}(t) = h_{\mathrm{RF}}(t) * \left(\alpha s_{\mathrm{RF}}(t-\tau) + I(t) + Z(t) \right)
$$

This filter is designed to pass the desired image in $s_{\mathrm{RF}}(t)$, and in fact it will further suppress the transmitter LO leakage and undesired image. For convenience, we rewrite

$$
  r_{\mathrm{RF}}(t) = x_{\mathrm{RF}}(t) + I_{\mathrm{RF}}(t) + Z_{\mathrm{RF}}(t)
$$

where

* $x_{\mathrm{RF}}(t) := h_{\mathrm{RF}}(t) * \alpha s_{\mathrm{RF}}(t-\tau)$ is the transmitted RF signal affected by propagation and the RF receive filter

*  $I_{\mathrm{RF}}(t):=h_{\mathrm{RF}}(t) * I(t)$ is the RF-filtered inteference

* $Z_{\mathrm{RF}}(t):=h_{\mathrm{RF}}(t) * Z(t)$ is the RF-filtered front-end thermal noise.

+++ {"slideshow": {"slide_type": "subslide"}}

Furthermore, this filter will essentially reject the interference signal $I_{\mathrm{RF}}(t)$, i.e., $h_{\mathrm{RF}}(t) * I(t) \approx 0$, if either

$$
  f_{\mathrm{INT}}+B_{\mathrm{INT}} < f_3
$$

i.e., the inteference falls completely in the lower stopband of the filter, or

$$
  f_{\mathrm{INT}}-B_{\mathrm{INT}} > f_4
$$

i.e., the interference falls completely in the upper stopband of the filter.

For generality of the results to follow, we assume that the interference frequency content in the interval $[f_{\mathrm{INT}}-B_{\mathrm{INT}},f_{\mathrm{INT}}+B_{\mathrm{INT}}]$ overlaps somewhat with the interval $[f_3,f_4]$, i.e,. the passband and transition bands of the RF bandpass filter.

+++ {"slideshow": {"slide_type": "subslide"}}

Finally, this filter limits the variance of the noise to be

$$
  \frac{N_0}{2} \int_{-\infty}^{\infty} |H_{\mathrm{RF}}(f)|^2 df = N_0 B_{\mathrm{RF}}
$$

where $B_{\mathrm{RF}}$ denotes the noise-equivalent (baseband) bandwidth of the RF bandpass filter.

+++ {"slideshow": {"slide_type": "slide"}}

### RF Mixer

In order to account for oscillator frequency and phase offsets, we assume the receive RF oscillator generates the carrier $\cos(2\pi f'_{\mathrm{LO}} t + \phi)$, which has Fourier transform

$$
  \frac{1}{2} \left[ e^{j\phi} \delta(f-f'_{\mathrm{LO}}) + e^{-j\phi} \delta(f+f'_{\mathrm{LO}}) \right]
$$

so that a modulation of the form $x(t)\cos(2\pi f'_{\mathrm{LO}} t + \phi)$ produces the output

$$
  \frac{1}{2} \left[ e^{j\phi} X(f-f'_{\mathrm{LO}}) + e^{-j\phi} X(f+f'_{\mathrm{LO}}) \right]
$$

+++ {"slideshow": {"slide_type": "slide"}}

Again based upon our discussion of practical mixers in Lecture 13, we model the output of the RF mixer as follows

\begin{align*}
r_{\mathrm{MIX}}(t) \approx& A' r_{\mathrm{RF}}(t)\cos(2\pi f'_{\mathrm{LO}}+\phi) \\
&+B' \cos(2\pi f'_{\mathrm{LO}} t + \phi) \\
&+C' r_{\mathrm{RF}}(t) \\
&+D' \cos^2(2\pi f'_{\mathrm{LO}} t + \phi) \\
&+E' r^2_{\mathrm{RF}}(t)
\end{align*}

+++ {"slideshow": {"slide_type": "slide"}}

### IF Bandpass Filter

We model the IF bandpass filter with frequency response $H_{\mathrm{IF}}(f)$ in a manner analogous to the RF bandpass filters, but we allow for different frequency parameters $f'_1,f'_2,f'_3,f'_4,f'_5$ and stopband attenuation as illustrated in the figure below.

```{figure} images/Lec21-07.png
---
name: if-filter
width: "80%"
---
IF Filter.
```

In particular, we expect that $f'_1 \le f_{\mathrm{IF}}-B < f_{\mathrm{IF}}+B \le f'_2$ so that the intermediate frequency signal $s_{\mathrm{IF}}(t)$ passes through the filter will little attenuation.

+++ {"slideshow": {"slide_type": "subslide"}}

With this parameterization, we can now roughly summarize the design contraints for the receiver in order to suppress the mixer self-interference components at the output of the IF filter. Specifically, the IF bandpass filter should significantly attenuate:

* LO leakage and self-mixing, requiring $f'_3 > 0$ to reject DC as well as $f'_4 < f'_{\mathrm{LO}}$ and ideally $f'_4 \ll f'_{\mathrm{LO}}$.

* Input leakage (including RF-limited input signal, RF-limited passband interference, and RF-limited passband noise), requiring $f'_4 < f_3$, and ideally $f'_4 \ll f_3$, where $f_3$ is the upper limit of the lower stopband for the RF bandpass filter.

+++ {"slideshow": {"slide_type": "subslide"}}

**TBD:** Deal carefully with the input self-mixing term $E' r^2_{\mathrm{RF}}(t)$. If the RF-limited signal, interference, and noise are uncorrelated, it seems like this self-mixing would create terms near DC with baseband bandwidth at most $f_4-f_3$, and we would want to set $f'_3 > f_4 - f_3$ to suppress them in the lower stopband of the IF bandpass filter. Other input self-mixing terms would reside at $2f_{\mathrm{RF}}$, but these would be suppressed from the IF bandpass filter suppressing the receiver LO leakage.

+++ {"slideshow": {"slide_type": "subslide"}}

Finally, we study the intended IF filter output

$$
  r_{\mathrm{IF}}(t)\approx h_{\mathrm{IF}}(t) * \left[A' r_{\mathrm{RF}}(t)\cos(2\pi f'_{\mathrm{LO}} t + \phi) \right]
$$

In the Fourier transform domain, we have

$$
  R_{\mathrm{IF}}(f) \approx H_{\mathrm{IF}}(f) \frac{A'}{2} \left[e^{j\phi} R_{\mathrm{RF}}(f-f'_{\mathrm{LO}}) + e^{-j\phi} R_{\mathrm{RF}}(f+f'_{\mathrm{LO}}) \right]
$$

where again

$$
  r_{\mathrm{RF}}(t) = x_{\mathrm{RF}}(t) + I_{\mathrm{RF}}(t) + Z_{\mathrm{RF}}(t)
$$

Since the modulation is linear, we can treat each component separately and add the results.

+++ {"slideshow": {"slide_type": "slide"}}

#### Impact on Interference

Let $I_{\mathrm{MIX}}(t)=A' I_{\mathrm{RF}}(t) \cos(2\pi f'_{\mathrm{LO}} t + \phi)$ represent the interference component at the output of the receive mixer. Then, treating the interference as a determinstic signal for the time being, we have

\begin{align*}
I_{\mathrm{MIX}}(f)  =& \frac{A'}{2} \left[e^{j\phi} I_{\mathrm{RF}}(f-f'_{\mathrm{LO}}) + e^{-j\phi} I_{\mathrm{RF}}(f+f'_{\mathrm{LO}}) \right]
\end{align*}

Let $I_{\mathrm{IF}}(t) = h_{\mathrm{IF}}(t) * I_{\mathrm{MIX}}(t)$ be the interference component at the output of the IF filter.

**TBD:** Treat interference as a random process and cast results in terms of power spectral densities. Depends upon earlier results for filtering and modulation of WSS random processes.

+++ {"slideshow": {"slide_type": "slide"}}

Since the frequency content of the interference signal  is centered at $\pm f_{\mathrm{INT}}$, the output mixing components will be centered at $\pm |f_{\mathrm{INT}}-f'_{\mathrm{LO}}|$ and $\pm |f_{\mathrm{INT}}+f'_{\mathrm{LO}}|$.

For the remainder of this discussion, we consider the case in which $f_{\mathrm{INT}} > f'_{\mathrm{LO}}$, that is, the interference center frequency is higher than the receive LO frequency.

+++ {"slideshow": {"slide_type": "slide"}}

The figure below highlights conditions for the interference to be rejected by the IF bandpass filter.

```{figure} images/Lec21-08.png
---
name: interference-rejection
width: "80%"
---
Interference rejection.
```

+++ {"slideshow": {"slide_type": "slide"}}

For the first case in which the interference images straddle the IF passband we require $f_{\mathrm{INT}}-f'_{\mathrm{LO}} + B_{\mathrm{INT}} < f'_3$ and $f_{\mathrm{INT}}+f'_{\mathrm{LO}}-B_{\mathrm{INT}} > f'_4$. For fixed $f_{\mathrm{IF}}$ and $f_{\mathrm{INT}}$, the larger the receiver LO frequency $f'_{\mathrm{LO}}$ the better, because the interference will be shifted farther away from the passband of the IF filter. This suggests superheterodyne demodulation at the RF stage, and requires reasonably large $f'_3$ and $f_{\mathrm{IF}}$.

For the second case in which the inteference images lie above the IF passband, we require $f_{\mathrm{INT}}-f'_{\mathrm{LO}} + B_{\mathrm{INT}} > f'_4$. For fixed $f_{\mathrm{IF}}$ and $f_{\mathrm{INT}}$, the smaller the receiver LO frequency $f'_{\mathrm{LO}}$ the better, again because the interference will be shifted farther away from the passband of the IF filter. This suggests subheterodyne demodulation, and allows smaller $f'_3$ and $f_{\mathrm{IF}}$.

+++ {"slideshow": {"slide_type": "slide"}}

#### Impact on Signal

Let $x_{\mathrm{RF}}(t) = h_{\mathrm{RF}}(t) * \alpha s_{\mathrm{RF}}(t-\tau)$ again represent the signal component in the RF-filtered received signal. Then

\begin{align*}
X_{\mathrm{RF}}(f) &= \alpha e^{-j2\pi f \tau} H_{\mathrm{RF}}(f) S_{\mathrm{RF}}(f) \\
&\approx \alpha e^{-j2\pi f \tau} H^2_{\mathrm{RF}}(f) \left[\frac{A}{2} S_{\mathrm{IF-FO}}(f) + \frac{B}{2}\left[\delta(f-f_{\mathrm{LO}}) + \delta(f+f_{\mathrm{LO}}) \right]+  \frac{A}{2} S_{\mathrm{IF+LO}}(f) \right]
\end{align*}

based upon our approximations for the heterodyne transmitter.

Let $x_{\mathrm{MIX}}(t)=A' x_{\mathrm{RF}}(t)\cos(2\pi f'_{\mathrm{LO}}+\phi)$ represent the desired signal component at the output of the receive mixer. Then

\begin{align*}
X_{\mathrm{MIX}}(f)  =& \frac{A'}{2} \left[e^{j\phi} X_{\mathrm{RF}}(f-f'_{\mathrm{LO}}) + e^{-j\phi} X_{\mathrm{RF}}(f+f'_{\mathrm{LO}}) \right] \\
=& \frac{\alpha A'}{2} \left[e^{j\phi} e^{-j2\pi (f-f'_{\mathrm{LO}}) \tau} H_{\mathrm{RF}}(f-f'_{\mathrm{LO}}) S_{\mathrm{RF}}(f-f'_{\mathrm{LO}}) \right. \\
& \quad\quad + \left. e^{-j\phi} e^{-j2\pi (f+f'_{\mathrm{LO}}) \tau} H_{\mathrm{RF}}(f+f'_{\mathrm{LO}}) S_{\mathrm{RF}}(f+f'_{\mathrm{LO}}) \right]
\end{align*}

After some further analysis and approximations...

$$
  X_{\mathrm{IF}}(f) =
$$

**TBD:** Complete the result with the IF filter.

+++ {"slideshow": {"slide_type": "slide"}}

#### Impact on Noise

**TBD:** Treat noise as a random process and cast results in terms of power spectral densities. Depends upon earlier results for filtering and modulation of WSS random processes.

+++ {"slideshow": {"slide_type": "slide"}}

## IF Downconverstion

**TBD:** Demod with both cosine and sine carriers, low-pass filter each, because of frequency and phase offsets.

+++ {"slideshow": {"slide_type": "slide"}}

## Image Selection / Frequency Planning

### General Discussion

In discussing the heterodyne transmitter, we raised the question of which image to select for transmission, and which image to reject, and whether or not to use common or distrinct LO frequencies at the transmitter and receiver. This creates four possible radio architectures.

For each architecture, system performance will depend upon the relatonships among at least fourteen frequency paramaters, namely:

* The baseband bandwidth $B$

* The intermediate frequency $f_{\mathrm{IF}}$

* The local oscillator frequences $f_{\mathrm{LO}}$ and $f'_{\mathrm{LO}}$.

* The interference frequency $f_{\mathrm{INT}}$ and bandwidth $B_{\mathrm{INT}}$

* The passband $[f_1,f_2]$ and transition bands $[f_3,f_1]$ and $[f_2,f_4]$ of the RF bandpass filter

* The passband $[f'_1,f'_2]$ and transition bands $[f_3,f_1]$ and $[f_2,f_4]$ of the IF bandpass filter

All of this creates a large design space in which to optimize the optimization of the heterodyne radio transmitter and recevier in a given problem, and we have not even mentioned the additional flexibility in the specific filter designs!

### Our Lab Kit

With our lab kit, our IF frequency is limited by the ADALM-2000, and our RF bandpass and IF bandpass filters are fixed. The only parameters we can vary are the signaling baseband bandwidth $B$, the intermediate frequency $f_{\mathrm{IF}}$, and the oscillator frequencies $f_{\mathrm{LO}}$ and $f'_{\mathrm{LO}}$ of the transmitter and receiver, respectively, which we often assume are approximately the same for simplicity.

As we will explore in the next lab, also of concern is interference in the $2.4\ \mathrm{GHz}$ ISM band, the lowest possible frequencies $f_{\mathrm{INT}}$ of which are WiFi or Bluetooth channels that fall within the upper part of the passband of our RF filter. We therefore are inclined to push our transmitter's RF output to the lower part of the passband of the RF filter, i.e., use subheterodyne modulation and demodulation, and reduce the impact of this interference. The specific design used in the lab will be therefore be as follows:

\begin{align*}
f_{\mathrm{IF}}&=20\ \mathrm{MHz} \\
B &= 1\ \mathrm{MHz\ (mainlobe)} \\
f_{\mathrm{LO}} = f'_{\mathrm{LO}}&= 2340\ \mathrm{MHz}
\end{align*}

+++

## Notes

* For frequency planning, we can worry less about the exact magnitude and phase of a Fourier transform or power spectral density and focus on frequency bands with most of the signals energy.

* For channel modeling, we need to keep track of the exact magnitude and phase of a Fourier transform or power spectral density.
