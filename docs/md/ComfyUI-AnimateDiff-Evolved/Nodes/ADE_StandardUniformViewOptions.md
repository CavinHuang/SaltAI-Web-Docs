# View Options◆Standard Uniform 🎭🅐🅓
## Documentation
- Class name: `ADE_StandardUniformViewOptions`
- Category: `Animate Diff 🎭🅐🅓/context opts/view opts`
- Output node: `False`

This node is designed to create and configure view options for generating animations with uniform distribution. It allows for the customization of view length, stride, and overlap, along with the method of fusing context, to tailor the animation generation process.
## Input types
### Required
- **`view_length`**
    - Comfy dtype: `INT`
    - Specifies the length of the view, affecting the granularity and extent of the animation frames generated.
    - Python dtype: `int`
- **`view_stride`**
    - Comfy dtype: `INT`
    - Determines the stride between views, influencing the smoothness and speed of the animation.
    - Python dtype: `int`
- **`view_overlap`**
    - Comfy dtype: `INT`
    - Sets the overlap between views, which can help in creating smoother transitions between animation frames.
    - Python dtype: `int`
### Optional
- **`fuse_method`**
    - Comfy dtype: `COMBO[STRING]`
    - Defines the method used to fuse multiple contexts together, impacting the continuity and coherence of the animation.
    - Python dtype: `str`
## Output types
- **`view_opts`**
    - Comfy dtype: `VIEW_OPTS`
    - Produces the configured view options, ready to be utilized in the animation generation process.
    - Python dtype: `ContextOptions`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class StandardUniformViewOptionsNode:
    @classmethod
    def INPUT_TYPES(s):
        return {
            "required": {
                "view_length": ("INT", {"default": 16, "min": 1, "max": LENGTH_MAX}),
                "view_stride": ("INT", {"default": 1, "min": 1, "max": STRIDE_MAX}),
                "view_overlap": ("INT", {"default": 4, "min": 0, "max": OVERLAP_MAX}),
            },
            "optional": {
                "fuse_method": (ContextFuseMethod.LIST,),
            }
        }
    
    RETURN_TYPES = ("VIEW_OPTS",)
    CATEGORY = "Animate Diff 🎭🅐🅓/context opts/view opts"
    FUNCTION = "create_options"

    def create_options(self, view_length: int, view_overlap: int, view_stride: int,
                       fuse_method: str=ContextFuseMethod.PYRAMID,):
        view_options = ContextOptions(
            context_length=view_length,
            context_stride=view_stride,
            context_overlap=view_overlap,
            context_schedule=ContextSchedules.UNIFORM_STANDARD,
            fuse_method=fuse_method,
            )
        return (view_options,)

```
