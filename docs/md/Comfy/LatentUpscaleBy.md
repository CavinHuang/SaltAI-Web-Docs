# Upscale Latent By
## Documentation
- Class name: `LatentUpscaleBy`
- Category: `latent`
- Output node: `False`

The `LatentUpscaleBy` node is designed to upscale latent representations of images by a specified scale factor using various interpolation methods. It allows for the adjustment of the size of latent images to enhance detail or match specific dimensions.
## Input types
### Required
- **`samples`**
    - The latent representation of images to be upscaled. This input is crucial for the operation as it determines the base data that will be modified through the upscaling process.
    - Python dtype: `Dict[str, torch.Tensor]`
    - Comfy dtype: `LATENT`
- **`upscale_method`**
    - Specifies the interpolation method used for upscaling. Different methods can affect the quality and characteristics of the upscaled image.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`scale_by`**
    - The factor by which the latent images will be upscaled. This directly influences the output size of the images, allowing for precise control over the upscaling process.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
## Output types
- **`latent`**
    - The upscaled latent representation of images. This output provides the enhanced images after applying the specified upscaling method and scale factor.
    - Python dtype: `Dict[str, torch.Tensor]`
    - Comfy dtype: `LATENT`
## Usage tips
- Infra type: `GPU`
- Common nodes: `KSampler,Reroute,VAEDecode,KSampler (Efficient),LatentInterpolate`


## Source code
```python
class LatentUpscaleBy:
    upscale_methods = ["nearest-exact", "bilinear", "area", "bicubic", "bislerp"]

    @classmethod
    def INPUT_TYPES(s):
        return {"required": { "samples": ("LATENT",), "upscale_method": (s.upscale_methods,),
                              "scale_by": ("FLOAT", {"default": 1.5, "min": 0.01, "max": 8.0, "step": 0.01}),}}
    RETURN_TYPES = ("LATENT",)
    FUNCTION = "upscale"

    CATEGORY = "latent"

    def upscale(self, samples, upscale_method, scale_by):
        s = samples.copy()
        width = round(samples["samples"].shape[3] * scale_by)
        height = round(samples["samples"].shape[2] * scale_by)
        s["samples"] = comfy.utils.common_upscale(samples["samples"], width, height, upscale_method, "disabled")
        return (s,)

```