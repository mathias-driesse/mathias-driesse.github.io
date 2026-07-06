---
title: "Color-correcting Voyager images"
date: 2026-07-05
description: ""
tags: [explainer, gravitational-waves, qft]
---

I've always wondered what color the planets really are. It's well known that all our popular science images, the ones that are used to inspire the public and communicate the greatness of our achievements in space, are not in real colors. One of my favorite examples is the brilliant Kevin M. Gill of NASA JPL: https://apoapsys.com/work. What do these images look like without the beauty-enhancing color corrections? 

Secondly, the Voyager spacecraft fascinate me because they're some of earliest probes that we have that took photographs of the outer planets, and they have a surprising amount of quality to them. As with any probe, the data is out there and freely accessible at the [Outer Planets Unified Search](https://opus.pds-rings.seti.org/opus/#).

As one can easily discover on Wikipedia, the Voyager Imaging Science System (ISS) has, depending on the camera, a few different filters with different sensitivities. 

How do we perceive color and how do we make the Voyager images look similar?

Let's look at what happens with the light that lets us see. First, it's emitted by the sun with a wide variety of wavelengths. This has been measured and standardized in many places, for example in ASTM G-173-03. We denote this $I(\lambda)$. In `colour`, we can call it with 

```python
sunlight = colour.plotting.SD_ASTMG173_ETR
```

Next, the light hits the object, with which it interacts in a typically very complicated way. We may parametrize this with our reflectance function $R(\lambda)$, which tells us what fraction of light at a given wavelength is reflected. Finally, the light hits the filter which records it. For us, this is the cones of our eyes, while for Voyager it goes through the optics, filter, and hits the photoconductor of the vidicon camera. We will call this $F(\lambda)$. The thing that we actually perceive, then, is the convolution of all these things:
$$
\int d\lambda \, I(\lambda) R(\lambda) F_{\text{voyager}}(\lambda).
$$

Since the illuminant and the reflectance are the same, what we need is a way to convert between the way that the Voyager camera perceives light and the way our eyes perceive light. In fact, this kind of color science is already built into digital cameras. They too have their own sensors with different sensitivities to different wavelengths compared to human eyes, so all we need to do is follow the same techniques they do.

What is the space of all colors we can perceive? The 1931 CIE report standardized this; it is the XYZ colour space. Given some incoming light $I(\lambda)$, we perceive three numbers, $X$, $Y$, and $Z$:
$$
X = \int d\lambda \, R(\lambda) \bar{x}(\lambda) \\
Y = \int d\lambda \, R(\lambda) \bar{y}(\lambda) \\
Z = \int d\lambda \, R(\lambda) \bar{z}(\lambda),
$$
where $\bar{x}, \bar{y}, \bar{z}$ are the standardized colour transfer functions fundamental to human vision.

In `colour`,
```python
cmfs = colour.MSDS_CMFS["CIE 1931 2 Degree Standard Observer"]
XYZ = colour.sd_to_XYZ(spectrum, cmfs, sunlight)
```

To first order, let us assume that there exists a matrix $M$ such that
$$
XYZ = M \cdot \text{Voyager}
$$
Let us denote a set of $N$ known $XYZ$s for a reflectance target as $Q$ and the corresponding camera responses as the $3 \times N$ matrix $R$. Then the least-squares fit is given exactly as the Moore-Penrose (yes, that Penrose!) pseudoinverse:
$$
M=QR^T (RR^T)^{-1}.
$$

But remember, for our Jupiter pictures we don't have the human response! So how can we do this fit? The answer is to simulate it. We can generate a bunch of random "fake" spectra, compute how Voyager sees them through its camera, and simultaneously generate the human response in XYZ space. Thanks to the `colour` python package, this is actually pretty easy (but not vectorizable if you use their built-in functions :():

```python
for i in range(1000):
    spectrum = generate_random_spectrum()
    XYZ = colour.sd_to_XYZ(spectrum, cmfs, sunlight)
    OGB = colour.sd_to_XYZ(spectrum, OGB_cmfs, sunlight)

    Q.append(XYZ)
    R.append(OGB)

Q @ np.transpose(R) @ np.linalg.inv(R @ np.transpose(R))
```
