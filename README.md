<div align="center">

# ![Logo](images/STTP%20V3%20-%20Logo.png)
### All-in-one, compact, recursive, segmented, toggle-based ComfyUI workflow by Luckytime
</div>

This workflow is capable of controlnet, image-prompt adapter, text-to-image, image-to-image, background removal, background compositing, outpainting, inpainting, face swap, face detailer, model upscale, sd ultimate upscale, vram management, and infinite looping. It is currently only capable of using checkpoint models.

**Video Demo:** https://youtube.com/watch?v=BluWKOunjPI<br>
**CivitAI Repo:** https://civitai.com/models/812560/straight-to-the-point<br>

# Dependencies

Being an all-in-one, there are many dependencies, but I tried my absolute best to use native nodes whenever possible, and only utilized popular/trusted custom nodes for the rest.

🔗 https://github.com/comfyanonymous/ComfyUI ➡️ ComfyUI<br>
🔗 https://github.com/rgthree/rgthree-comfy ➡️ rgthree<br>
🔗 https://github.com/ltdrdata/ComfyUI-Impact-Pack ➡️ Impact Pack<br>
🔗 https://github.com/jags111/efficiency-nodes-comfyui ➡️ Efficiency Nodes<br>
🔗 https://github.com/pythongosssss/ComfyUI-Custom-Scripts ➡️ Custom Scripts<br>
🔗 https://github.com/Fannovel16/comfyui_controlnet_aux ➡️ ControlNet Auxiliary Preprocessors<br>
🔗 https://github.com/cubiq/ComfyUI_IPAdapter_plus ➡️ IPAdapter Plus<br>
🔗 https://github.com/john-mnz/ComfyUI-Inspyrenet-Rembg ➡️ Inspyrenet Rembg<br>
🔗 https://github.com/lquesada/ComfyUI-Inpaint-CropAndStitch ➡️ Inpaint Crop & Stitch<br>
🔗 https://github.com/Gourieff/ComfyUI-ReActor ➡️ ReActor<br>
🔗 https://github.com/ltdrdata/ComfyUI-Impact-Subpack ➡️ Impact Subpack<br>
🔗 https://github.com/ssitu/ComfyUI_UltimateSDUpscale ➡️ Ultimate SD Upscale<br>

# Overview

<div align="center">

![Workflow](images/STTP%20-%20Full.png)
</div>

The workflow is a pipeline of 8 toggleable groups that moves from left to right. When multiple groups are active, the output of the first group is automatically sent to the next. If all 8 groups are run simultaneously, there is a high chance of depleting vram and getting an 'Allocation on device' (memory) error. Thus, successive groups can be toggled on following the completion of previous ones while the seed from each group can be reused to retain the generation at each step. This flattens the load on vram by allowing you to load models only when they are used.

The 'Group Toggle' nodes (found in the top right of a group) are global and let you switch groups off from anywhere. The workflow starts with conditioning in the 'ControlNet'  and 'IPAdapter' groups, then creates an image from an empty latent in the 'Generate' group, refines the background in the 'Background' group, reprocesses the image in the 'Redraw' group, revises details in the 'Inpaint' group, corrects faces in the 'Face Fix' group, and finally upscales and saves the image in the 'Upscale' group. Any combination of the 8 groups can be used without errors.

Looping is achieved using the Impact Pack's "send" and "receive" nodes. Images can be sent to the Generate group where they will be stored for the duration of the session. Groups that can send images have a "send" toggle which will continually send images as long as the toggle is active. There is only one "receive" node in the workflow, so any image that is sent will overwrite the previous one (so make sure to disable these toggles after using them).

<div align="center">

![Send Toggle](images/samples/send%20toggle.png)
</div>

# Groups

### ControlNet

<div align="center">

![ControlNet](images/STTP%20-%20ControlNet.png)
</div>

This group lets you use a ControlNet model to generate a preprocessed (helper) image which is used to condition the model in the 'Generate' or 'Redraw' groups.

1️⃣ Load an image<br>
2️⃣ Choose a preprocessor (can be toggled off)<br>
3️⃣ Crop the helper image<br>
4️⃣ Preview/save the helper<br>

### IPAdapter

<div align="center">

![IPAdapter](images/STTP%20-%20IPAdapter.png)
</div>

This group lets you choose up to 2 images to condition the model in both the 'Generate' and 'Redraw' groups. An area-mask can be defined for both images. (Somewhat counter-intuitively, it doesn't apply the mask to the node where the mask is drawn, the mask is only extracted and applied to the generated content. The 'Load Image' node where the mask is drawn is only used to generate the mask and nothing else. You can copy and paste a placeholder image into this node to see roughly where the mask will go.)

0️⃣ Draw a mask (can be toggled off)<br>
1️⃣ Load an image<br>
2️⃣ Preprocess image (image must be square)<br>
3️⃣ Choose IPAdapter settings (can be toggled off)<br>

### Generate

<div align="center">

![Generate](images/STTP%20-%20Generate.png)
</div>

This group lets you create a new image from an empty latent (AKA text2img). The 'Refresh' toggle helps overcome a known bug with Efficiency Nodes where changing the ControlNet options does not refresh the KSampler. The ControlNet and IPAdapter toggles can block both groups even if those groups are active. While enabled, the loop toggle mutes the KSampler so the loop image becomes the default output of the group.

♾️ Preview the loop image (can be toggled off)<br>
1️⃣ Choose ControlNet settings (can be toggled off)<br>
2️⃣ Load a checkpoint, choose image size, and choose CLIP layer skip<br>
3️⃣ Enter text prompts<br>
4️⃣ Load LoRAs<br>
5️⃣ Choose KSampler settings<br>
6️⃣ Preview image<br>

### Background

<div align="center">

![Background](images/STTP%20-%20Background.png)
</div>

This group lets you remove the background of your image, draw and composite a new background, or outpaint. If used alone, it uses the loaded image in step 0. The 'Refresh' toggle helps overcome a known bug caused by mute conflicts (when the KSampler is muted using the 'Remove Background' function and the group is muted and un-muted, the KSampler also becomes un-muted). When drawing a new background, the dimensions are taken from the input image, then the background and input image are composited together. The image can be padded while using any of the functions, but is mainly there to allow outpainting. Currently, only the entire image can be used as context when outpainting.

0️⃣ Load an image<br>
1️⃣ Load a checkpoint, choose image size, and choose CLIP layer skip<br>
2️⃣ Pad the image<br>
3️⃣ Choose background removal threshold or enter text prompts<br>
4️⃣ Load LoRAs<br>
5️⃣ Choose KSampler settings<br>
6️⃣ Compare images<br>

### Redraw

<div align="center">

![Redraw](images/STTP%20-%20Redraw.png)
</div>

This group lets you create variations using an image as a starting point (AKA img2img) (AKA hiresfix). If used alone, it uses the loaded image in step 0. It takes the decoded image from 'Generate' and upscales, re-encodes, and redraws it with a second KSampler. The 'Refresh' toggle helps overcome a known bug with Efficiency Nodes where changing the ControlNet options does not refresh the KSampler. The ControlNet and IPAdapter toggles can block both groups even if those groups are active.

0️⃣ Load an image<br>
1️⃣ Choose ControlNet settings (can be toggled off)<br>
2️⃣ Load a checkpoint, resize image, and choose CLIP layer skip<br>
3️⃣ Enter text prompts<br>
4️⃣ Load LoRAs<br>
5️⃣ Choose KSampler settings<br>
6️⃣ Compare images<br>

### Inpaint

<div align="center">

![Inpaint](images/STTP%20-%20Inpaint.png)
</div>

This group lets you inpaint using either the full image or part of the image as context. If used alone, it uses the loaded image in step 0. You must run the graph to load the image into the mask nodes, then you can proceed to drawing your masks. As long as 'block' is set to 'if_empty_mask', execution will be stopped if no mask is detected. The context mask is only considered when using the "crop" inpainting method. An inpainting checkpoint is recommended for best results. When prompting, describe what is in the context area, not just what is in the masked area, as the model will be considering everything in the context area.

0️⃣ Load an image<br>
1️⃣ Load a checkpoint, resize image, and choose CLIP layer skip<br>
2️⃣ Draw an inpaint mask and context mask (can be toggled off)<br>
3️⃣ Enter text prompts<br>
4️⃣ Load LoRAs<br>
5️⃣ Resize cropped area and choose mask blending amount<br>
6️⃣ Preview context area<br>
7️⃣ Choose KSampler settings<br>
8️⃣ Preview inpaint and compare images<br>

### Face Fix

<div align="center">

![Face Fix](images/STTP%20-%20Face%20Fix.png)
</div>

This group lets you swap or redraw faces. If used alone, it uses the loaded image in step 0. **Note: if you are getting black images, open "ComfyUI/custom_nodes/comfyui-reactor/scripts/reactor_sfw.py" in a text editor, and change 'True' to 'False'.** The face boost node can be toggled off. Face detailer can be summarized as "redrawing the face by inpainting". Similarity to the original face is maintained by using a low (0.2-0.3) denoise. Various detection/swap/segmentation models are needed for this group, which can be found by following the links in the introduction node or in the dependency repos.

0️⃣ Load an image<br>
1️⃣ Load a checkpoint, resize image, and choose CLIP layer skip<br>
2️⃣ Load a face or enter text prompts<br>
3️⃣ Enter original face indexes or load LoRAs<br>
4️⃣ Enter swap face indexes or Choose seed<br>
5️⃣ Choose face boost settings or face detailer settings<br>
6️⃣ Compare images<br>

### Upscale

<div align="center">

![Upscale](images/STTP%20-%20Upscale.png)
</div>

This group lets you upscale an image with an upscale model or SD Ultimate Upscale (tiled upscaling). If used alone, it uses the loaded image in step 0. The 'Refresh' toggle helps overcome a known bug caused by mute conflicts. Ultimate upscale splits the image into multiple tiles, upscales them independently, then stitches them back together (if the denoise is too high (above ~0.3) the tile seams will be too obvious). Saving can be toggled off for quality assurance.

0️⃣ Load an image<br>
1️⃣ Load a checkpoint, choose image size, and choose CLIP layer skip<br>
2️⃣ Load upscale model<br>
3️⃣ Enter text prompts<br>
4️⃣ Choose Ultimate SD Upscale settings<br>
5️⃣ Compare images<br>
6️⃣ Downscale image<br>
7️⃣ Enter filename prefix before saving (can be toggled off)<br>

# Bookmarks
The 1-8 keys are shortcuts for useful camera views. You can adjust the bookmark nodes to suit your display by maximizing them and entering your desired zoom-level.

# Extras

## Specialized Workflows

These are the same groups from the main workflow, but split up into individual .json files.

## Exploded Workflows

These are the same as the specialized and main workflows, but all nodes are maximized, labeled, and organized for readability.

<div align="center">

![Full Exploded](images/exploded/STTP%20-%20Full%20Exploded.png)
</div>

<div align="center">

![ControlNet Exploded](images/exploded/STTP%20-%20ControlNet%20Exploded.png)
</div>

<div align="center">

![IPAdapter Exploded](images/exploded/STTP%20-%20IPAdapter%20Exploded.png)
</div>

<div align="center">

![Generate Exploded](images/exploded/STTP%20-%20Generate%20Exploded.png)
</div>

<div align="center">

![Background Exploded](images/exploded/STTP%20-%20Background%20Exploded.png)
</div>

<div align="center">

![Redraw Exploded](images/exploded/STTP%20-%20Redraw%20Exploded.png)
</div>

<div align="center">

![Inpaint Exploded](images/exploded/STTP%20-%20Inpaint%20Exploded.png)
</div>

<div align="center">

![Face Fix Exploded](images/exploded/STTP%20-%20Face%20Fix%20Exploded.png)
</div>

<div align="center">

![Upscale Exploded](images/exploded/STTP%20-%20Upscale%20Exploded.png)
</div>

## Template Workflows

These are basic workflows which demonstrate a unique function used in the main workflow.

### Loop

Requires Impact Pack. This workflow sends the output image to the beginning, allowing you to re-use it as the input. Choose an existing image in the receiver to get started and toggle the boolean to choose whether to send the image or not.

<div align="center">

![Loop Template](images/templates/STTP%20-%20Loop%20Template.png)
</div>

### Pause

Requires rgthree's nodes. This workflow can load two different checkpoint models, but allows you to pause and unload the first model before continuing. The image from the first group is automatically sent to the second group, and if the second group is toggled off, the workflow is effectively paused at that step.

<div align="center">

![Pause Template](images/templates/STTP%20-%20Pause%20Template.png)
</div>

### Switch

Requires Custom Scripts, Impact Pack, and rgthree's nodes. This workflow allows you to switch between two functions. The Switch (Any) node determines the behavior of the control bridges via a math expression which outputs 0 or 1, causing them to mute the KSampler and VAE Decode or leave them active. When muted, another image source can be passed via the Any Switch (rgthree) node. (The reason 'Switch (Any)' isn't used for both functions is because we don't want it to create an empty third index value ie. 1,2,3)

<div align="center">

![Switch Template](images/templates/STTP%20-%20Switch%20Template.png)
</div>

# Changelog

### Version 1
- Added Generate, Redraw, Face Swap, and Upscale groups

### Version 2
- Added ControlNet group
- Added ControlNet toggle for Generate and Redraw groups
- Separated CLIP skip for Generate and Redraw groups
- Added primitives to allow changing face indexes
- Rearranged and renumbered nodes
- Switched to main ReActor repo

### Version 3
- Separated text prompt for Generate and Redraw groups
- Separated ControlNet for Generate and Redraw groups
- Added IPAdapter group
- Added Background group
- Added Inpaint group
- Added loop capability
- Added face detailer to Face group
- Added sd ultimate upscale to Upscale group
- Grouped certain nodes together to save space
- Organized nodes and used emojis for clarity
- Added links to resources and revamped help nodes
- Added specialized workflows
- Added exploded workflows
- Added template workflows
