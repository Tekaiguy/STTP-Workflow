<div align="center">

# ![Logo](images/STTP%20Logo.png)
### A compact and efficient toggle-based ComfyUI workflow by Luckytime
</div>

# Features

- Text to image
- Image to image
- Face swap
- Upscale
- No overlapping nodes
- No wasted space

# Dependencies

### ![ComfyUI](https://github.com/comfyanonymous/ComfyUI)
### ![rgthree-comfy](https://github.com/rgthree/rgthree-comfy)
### ![efficiency-nodes-comfyui](https://github.com/jags111/efficiency-nodes-comfyui)
### ![comfyui-reactor-node](https://github.com/Gourieff/comfyui-reactor-node)

# Overview

<div align="center">

![Logo](images/STTP%20Workflow.png)
</div>

The workflow is a pipeline of 4 groups that moves from left to right. It starts with an empty latent image in the 'Generate' group, then reprocesses the image in the 'Redraw' group, then the face is fixed in the 'Face Swap' group, and finally the image is upscaled and saved in the 'Upscale' group. 
Any combination of the 4 groups may be used. If the last three groups are used independently, a local image can be uploaded as a starting point.

# Toggles

There are 3 'Group Toggle' nodes included in the workflow so they are always within reach. Switching off the groups lets you pause after each step and decide whether an image is worth refining. If you want to send an image to the next group, the seed from any previous KSampler should be frozen by clicking 'Last Queued Seed'.

# Groups

## Generate

This group lets you create a new image from an empty latent (AKA text2img).

<div align="center">

![Logo](images/generate%20screenshot.png)
</div>

### Workflow:

0. Write postive/negative prompts and choose CLIP skip
1. Load a checkpoint
2. Choose dimensions for an empty latent image
3. Load any Loras
4. Choose KSampler settings
5. Preview/Save the generation
6. Send the generation to Redraw, Face Swap, or Upscale

**Note: The prompts and CLIP are shared between the 'Generate' and 'Redraw' groups, but the Loras are not.**

## Redraw

This group lets you create variations using an image as a starting point (AKA img2img) (AKA Hires Fix). It takes the latent from 'Generate' and upscales, denoises, and redraws with a second KSampler.

<div align="center">

![Logo](images/redraw%20screenshot.png)
</div>

### Workflow:

0. Load a local image (if using only redraw)
1. Load a checkpoint
2. Rescale the latent from previous step to be slightly bigger
3. Load any Loras
4. Choose KSampler settings
5. Preview/Save the redraw
6. Send the redraw to face swap or upscale

**Note: The prompts and CLIP are shared between the 'Generate' and 'Redraw' groups, but the Loras are not.**

## Face Swap

This group lets you replace the face in an image with a face from another image. It will only replace the leftmost face it detects. When uploading your own image, you can rescale the image to ensure the face swap is effective.

<div align="center">

![Logo](images/face%20swap%20&%20upscale%20screenshot.png)
</div>

### Workflow:

0. Load and rescale a local image (if using only face swap)
1. Load a face image
2. Preview old image
3. Preview/Save the face swap
4. Send the face swap to upscale

## Upscale

This group lets you upscale an image using an upscale model. It then rescales the image down to a uniform size to save drive space and saves the image to your ComfyUI output folder.

### Workflow:

0. Load a local image (if using only upscale)
1. Load an upscale model
2. Rescale image to a smaller size
3. Save image to output folder

# Shortcuts
The 1-4 number keys are shortcuts for useful camera views. You can adjust the bookmark nodes to suit your display.
