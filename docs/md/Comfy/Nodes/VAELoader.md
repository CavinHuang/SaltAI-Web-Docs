# Load VAE
## Documentation
- Class name: `VAELoader`
- Category: `loaders`
- Output node: `False`

The VAELoader node is responsible for loading a Variational Autoencoder (VAE) model. It supports loading specific pre-trained VAE models by name, including handling special cases for 'taesd' and 'taesdxl' models by loading their respective encoder and decoder components and setting a scale factor.
## Input types
### Required
- **`vae_name`**
    - Specifies the name of the VAE model to load. The node supports a predefined list of VAE models, including 'taesd' and 'taesdxl', and loads the corresponding model components.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
## Output types
- **`vae`**
    - The loaded VAE model, ready for encoding or decoding operations.
    - Python dtype: `comfy.sd.VAE`
    - Comfy dtype: `VAE`
## Usage tips
- Infra type: `GPU`
- Common nodes: `VAEDecode,VAEEncode,Reroute,CR Module Pipe Loader,VAEDecodeTiled,KSampler Adv. (Efficient),FaceDetailer,Anything Everywhere3,VAEEncodeTiled`


## Source code
```python
class VAELoader:
    @staticmethod
    def vae_list():
        vaes = folder_paths.get_filename_list("vae")
        approx_vaes = folder_paths.get_filename_list("vae_approx")
        sdxl_taesd_enc = False
        sdxl_taesd_dec = False
        sd1_taesd_enc = False
        sd1_taesd_dec = False

        for v in approx_vaes:
            if v.startswith("taesd_decoder."):
                sd1_taesd_dec = True
            elif v.startswith("taesd_encoder."):
                sd1_taesd_enc = True
            elif v.startswith("taesdxl_decoder."):
                sdxl_taesd_dec = True
            elif v.startswith("taesdxl_encoder."):
                sdxl_taesd_enc = True
        if sd1_taesd_dec and sd1_taesd_enc:
            vaes.append("taesd")
        if sdxl_taesd_dec and sdxl_taesd_enc:
            vaes.append("taesdxl")
        return vaes

    @staticmethod
    def load_taesd(name):
        sd = {}
        approx_vaes = folder_paths.get_filename_list("vae_approx")

        encoder = next(filter(lambda a: a.startswith("{}_encoder.".format(name)), approx_vaes))
        decoder = next(filter(lambda a: a.startswith("{}_decoder.".format(name)), approx_vaes))

        enc = comfy.utils.load_torch_file(folder_paths.get_full_path("vae_approx", encoder))
        for k in enc:
            sd["taesd_encoder.{}".format(k)] = enc[k]

        dec = comfy.utils.load_torch_file(folder_paths.get_full_path("vae_approx", decoder))
        for k in dec:
            sd["taesd_decoder.{}".format(k)] = dec[k]

        if name == "taesd":
            sd["vae_scale"] = torch.tensor(0.18215)
        elif name == "taesdxl":
            sd["vae_scale"] = torch.tensor(0.13025)
        return sd

    @classmethod
    def INPUT_TYPES(s):
        return {"required": { "vae_name": (s.vae_list(), )}}
    RETURN_TYPES = ("VAE",)
    FUNCTION = "load_vae"

    CATEGORY = "loaders"

    #TODO: scale factor?
    def load_vae(self, vae_name):
        if vae_name in ["taesd", "taesdxl"]:
            sd = self.load_taesd(vae_name)
        else:
            vae_path = folder_paths.get_full_path("vae", vae_name)
            sd = comfy.utils.load_torch_file(vae_path)
        vae = comfy.sd.VAE(sd=sd)
        return (vae,)

```