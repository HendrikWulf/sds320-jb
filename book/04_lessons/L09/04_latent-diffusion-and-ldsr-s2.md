---
site:
  outline_maxdepth: 2
---

# Latent diffusion

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
A latent diffusion approach to super-resolution for Sentinel-2 imagery
</div>
<!-- markdownlint-enable MD033 -->

---

The previous page argued that super-resolution needs a genuinely generative approach, not simple upsampling. This page explains, at a student-facing level, how the specific model this lesson uses actually generates that detail, before you run it yourself on the next page.

---

## 1. Motivation

`geoai`'s super-resolution function takes only a handful of parameters, but what happens inside it is different from anything else in this course so far. Understanding the encode-denoise-decode pattern, even at a conceptual level, is what will make the outputs, and their accompanying uncertainty maps two pages from now, interpretable rather than mysterious.

---

## 2. Core idea

A {term}`diffusion model <Diffusion Model>` learns to generate images by reversing a noise-corruption process: training gradually destroys real images with added noise, and the model learns to undo that process step by step, starting from noise and ending at a clean image. A {term}`latent diffusion model (LDM) <Latent Diffusion Model>` runs this same process in a compressed representation instead of on full-resolution pixels directly, which makes it far cheaper to run. **LDSR-S2**, the model this lesson uses, applies this idea specifically to Sentinel-2 super-resolution, conditioning its generation on a low-resolution input so the output stays consistent with what the sensor actually observed.

---

## 3. From noise to detail

### A. The diffusion idea

During training, a diffusion model is shown real images with progressively more noise added, until only random noise remains. It learns the reverse: starting from noise, predict a slightly less noisy version, repeat, and eventually arrive at a clean, plausible image. Applied to super-resolution, this reverse process is conditioned on a low-resolution input, so instead of generating an arbitrary image, the model generates one specifically consistent with that input, filling in fine-scale detail the coarse input does not directly show.

```{admonition} Denoising here does not mean "cleaning a noisy photo"
:class: note
It is easy to misread "denoising" as removing noise from an already-meaningful image, the way you might sharpen a grainy photograph. Here, the process starts from pure random noise and builds an image up from nothing, guided by the low-resolution input. There is no original noisy photo being cleaned; the fine detail is generated, not recovered.
```

### B. Why "latent"

Running this step-by-step denoising process directly on a full-resolution pixel grid is computationally expensive, since every step touches every pixel. A latent diffusion model avoids this by first compressing the image into a smaller, lower-dimensional latent representation with an encoder, running the entire multi-step diffusion process in that compact space, and only decoding back to full pixel resolution at the very end. This preserves output quality while making the whole process practical to run on standard hardware, rather than requiring specialized infrastructure.

### C. The LDSR-S2 workflow specifically

LDSR-S2 operates on four Sentinel-2 bands (red, green, blue, near-infrared) and performs 4x spatial upsampling. For each input {term}`patch <Patch>`, the pipeline runs in three steps:

1. **Encode**: the low-resolution patch (128-by-128 pixels, 4 bands) is compressed into a latent representation.
2. **Denoise**: the diffusion process runs for a set number of sampling steps in that latent space, conditioned on the low-resolution input throughout.
3. **Decode**: the denoised latent representation is decoded back to pixel space at 4x resolution, producing a 512-by-512 pixel output.

---

## 4. Python reactivation

The shapes mentioned above, `(4, 128, 128)` for the input and `(4, 512, 512)` for the output, follow the channel-first array convention you have used for raster bands throughout this course: the first dimension is the number of bands, followed by height and width.

---

## 5. Common pitfalls

- **Confusing diffusion "denoising" with cleaning a real noisy image.** As emphasized above, the process starts from pure noise and generates an image from it; nothing is being "cleaned."
- **Assuming more sampling steps always help proportionally.** More steps generally improve quality up to a point, but with diminishing returns, and always at a proportional cost in processing time; the next practical page lets you set this directly.
- **Treating the latent space as an implementation detail you can ignore.** It is why the model is practical to run at all; skipping this understanding makes the encode-denoise-decode structure in the next page's code feel arbitrary rather than motivated.

---

## 6. Mini task

A colleague asks, "if this model just adds noise and removes it, why doesn't it just reconstruct the original blurry input?" Using the ideas from this page, explain what is missing from that description.

:::{dropdown} Sample solution
:class: note

The description misses conditioning: the model is not simply denoising a copy of the low-resolution input back to itself. It starts from random noise and generates a new, higher-resolution image, guided (conditioned) by the low-resolution input so the result stays consistent with what the input shows, while filling in additional fine-scale detail the low-resolution input does not contain at all. The output is a new, higher-resolution image generated to be consistent with the input, not a reconstruction of the input itself.
:::

---

## 7. Key takeaways

- Diffusion models generate images by learning to reverse a noise-corruption process, conditioned on the low-resolution input for super-resolution specifically.
- Latent diffusion models run this process in a compressed representation rather than on full-resolution pixels, making them practical to run without specialized hardware.
- LDSR-S2 processes four-band Sentinel-2 patches (128-by-128 pixels) through encode, denoise, and decode steps, producing 4x-upsampled, 512-by-512 pixel output.
- The next page runs this exact pipeline on real Sentinel-2 imagery.

### Further reading

- [i-Guide platform notebook](https://platform.i-guide.io/notebooks/3f8cedde-1de0-46ba-9edc-0f1ef14e6466) — an interactive notebook covering a related super-resolution workflow.
