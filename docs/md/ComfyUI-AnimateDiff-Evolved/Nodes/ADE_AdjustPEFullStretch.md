# Adjust PE [Full Stretch] 🎭🅐🅓
## Documentation
- Class name: `ADE_AdjustPEFullStretch`
- Category: `Animate Diff 🎭🅐🅓/ad settings/pe adjust`
- Output node: `False`

The ADE_AdjustPEFullStretch node is designed for adjusting the positional encoding (PE) stretch of an animation, allowing for dynamic modification of animation length and timing. It provides functionality to apply specific adjustments to the PE, such as stretching, based on user-defined parameters.
## Input types
### Required
- **`pe_stretch`**
    - Comfy dtype: `INT`
    - Specifies the amount by which the positional encoding (PE) should be stretched, effectively altering the animation's length and timing.
    - Python dtype: `int`
- **`print_adjustment`**
    - Comfy dtype: `BOOLEAN`
    - A boolean flag that, when set to True, enables the printing of adjustment details for debugging or informational purposes.
    - Python dtype: `bool`
### Optional
- **`prev_pe_adjust`**
    - Comfy dtype: `PE_ADJUST`
    - An optional parameter that allows for the chaining of multiple PE adjustments by taking a previous PE adjustment group as input.
    - Python dtype: `AdjustPEGroup`
## Output types
- **`pe_adjust`**
    - Comfy dtype: `PE_ADJUST`
    - Returns an updated PE adjustment group, incorporating the specified PE stretch and any previous adjustments.
    - Python dtype: `AdjustPEGroup`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class FullStretchPENode:
    @classmethod
    def INPUT_TYPES(s):
        return {
            "required": {
                "pe_stretch": ("INT", {"default": 0, "min": 0, "max": BIGMAX},),
                "print_adjustment": ("BOOLEAN", {"default": False}),
            },
            "optional": {
                "prev_pe_adjust": ("PE_ADJUST",),
            }
        }
    
    RETURN_TYPES = ("PE_ADJUST",)
    CATEGORY = "Animate Diff 🎭🅐🅓/ad settings/pe adjust"
    FUNCTION = "get_pe_adjust"

    def get_pe_adjust(self, pe_stretch: int, print_adjustment: bool, prev_pe_adjust: AdjustPEGroup=None):
        if prev_pe_adjust is None:
            prev_pe_adjust = AdjustPEGroup()
        prev_pe_adjust = prev_pe_adjust.clone()
        adjust = AdjustPE(motion_pe_stretch=pe_stretch,
                          print_adjustment=print_adjustment)
        prev_pe_adjust.add(adjust)
        return (prev_pe_adjust,)

```
