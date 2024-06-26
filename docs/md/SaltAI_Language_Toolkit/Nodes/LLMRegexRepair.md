# ∞ Regex Repair
## Documentation
- Class name: `LLMRegexRepair`
- Category: `SALT/Language Toolkit/Tools/Regex`
- Output node: `False`

The LLMRegexRepair node utilizes a language model to correct and improve regex patterns based on a given description of their intended functionality and any additional directions. It aims to transform potentially malformed or incorrect regex expressions into well-formed patterns that accurately match the specified criteria.
## Input types
### Required
- **`llm_model`**
    - Specifies the language model to use for repairing the regex pattern. It is crucial for interpreting the input pattern and generating the corrected version.
    - Comfy dtype: `LLM_MODEL`
    - Python dtype: `dict`
- **`text_input`**
    - The potentially malformed or incorrect regex pattern that needs to be repaired. It serves as the primary input for the correction process.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
- **`description`**
    - A description of what the regex pattern is intended to match and how the current pattern fails to meet these criteria. This context helps guide the repair process.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
### Optional
- **`extra_directions`**
    - Additional instructions or constraints to guide the language model in repairing the regex pattern. This can include specific formatting requirements or limitations.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
## Output types
- **`repaired_regex_pattern`**
    - Comfy dtype: `STRING`
    - The corrected regex pattern, as generated by the language model. It reflects the intended functionality described in the input.
    - Python dtype: `str`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class LLMRegexRepair:
    @classmethod
    def INPUT_TYPES(cls):
        return {
            "required": {
                "llm_model": ("LLM_MODEL",),
                "text_input": ("STRING", {"multiline": True, "dynamicPrompts": False, "placeholder": "Enter the malformed regex pattern here"}),
                "description": ("STRING", {"multiline": True, "dynamicPrompts": False, "placeholder": "Describe what the regex pattern does wrong, and what it should do."}),
            },
            "optional": {
                "extra_directions": ("STRING", {"multiline": True, "dynamicPrompts": False, "placeholder": "Extra directions for the LLM to follow, such as specific constraints or formats"}),
            }
        }

    RETURN_TYPES = ("STRING",)
    RETURN_NAMES = ("repaired_regex_pattern",)

    FUNCTION = "repair_regex"
    CATEGORY = f"{MENU_NAME}/{SUB_MENU_NAME}/Tools/Regex"

    def repair_regex(self, llm_model, text_input, description, extra_directions=""):
        prompt = (
            f"Given the potentially malformed or incorrect regex pattern:\n\n{text_input}\n\n"
            f"and the following description of what the pattern should match:\n\n{description}\n\n"
            f"{extra_directions}\n\n"
            "Please repair the regex pattern so it is well-formed and accurately matches the described criteria."
        )
        
        response = llm_model['llm'].complete(prompt)
        
        return (response.text,)

```
