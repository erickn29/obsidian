# 1. Local Computer Env

- Ubuntu 20.04
- GPU: RTX4090
- Python: Anaconda
- Cuda: 12.1


# 2. Create Working Folder

```bash
$ cd {your_root_dir}  
$ mkdir flux1schnell  
$ cd flux1schnell
```


# 3. Create python environments

```bash
$ conda create -n flux python==3.10  
$ conda activate flux
```


# 4. Install relevant python packages

```bash
(flux) {your_root_dir}/flux1schnell $ pip install setuptools  
(flux) {your_root_dir}/flux1schnell $ pip install huggingface_hub  
(flux) {your_root_dir}/flux1schnell $ pip install torch==2.3.1 torchvision==0.18.1 torchaudio==2.3.1 - index-url https://download.pytorch.org/whl/cu121  
(flux) {your_root_dir}/flux1schnell $ pip install --upgrade transformers  
(flux) {your_root_dir}/flux1schnell $ pip install accelerate  
(flux) {your_root_dir}/flux1schnell $ pip install pip install git+https://github.com/huggingface/diffusers.git  
(flux) {your_root_dir}/flux1schnell $ pip install pip install sentencepiece  
(flux) {your_root_dir}/flux1schnell $ pip install protobuf
```


# 5. Run samples.py

```python
import os  
  
# set your local working directory for downloading 'black-forest-labs/FLUX.1-schnell' models  
os.environ['HF_HOME'] = '{your_root_dir}/flux1schnell'  
print(os.environ['HF_HOME'])  
  
import torch  
from transformers.utils import move_cache  
from diffusers import FluxPipeline  
  
# Manually run cache migration  
move_cache()  
  
  
def main():  
    # Load the model pipeline (either from Huggingface or local path)  
    pipe = FluxPipeline.from_pretrained("black-forest-labs/FLUX.1-schnell", torch_dtype=torch.float16)  
  
    # For low VRAM GPUs (i.e. between 4 and 32 GB VRAM)  
    pipe.enable_sequential_cpu_offload()  # Enable CPU offload to save GPU memory  
    # pipe.vae.enable_slicing()  # Enable VAE slicing for memory efficiency  
    # pipe.vae.enable_tiling()  # Enable VAE tiling for better performance on high-res images  
  
    # Move the pipeline to GPU  
    # pipe.to("cuda")  # Ensure the pipeline is using the GPU (cuda) <= no need to convert cuda  
    # Sequential Offloading이 활성화된 상태에서는 파이프라인이 자동으로 필요한 모델 파라미터를 GPU로 전송하고, 나머지는 CPU에서 유지합니다.  
    # 따로 to("cuda")를 호출할 필요가 없습니다.  
  
    # casting here instead of in the pipeline constructor because doing so in the constructor loads all models into CPU memory at once  
    # pipe.to(torch.float16)  
  
    # Define the prompt  
    # prompt = "A cat holding a sign that says hello world"  
    prompt = "Roman empire soldier with a sword and a shield. Behind there are horses. In the background there is a mountain."  
  
    # Generate the image  
    image = pipe(  
        prompt,  
        guidance_scale=7.5,  # Typically a higher value works better for stable diffusion  
        num_inference_steps=4,  
        # height=1024,  
        # width=1024,  
        # num_inference_steps=50,  # More steps provide better image quality  
        # max_sequence_length=256,  
        generator=torch.Generator("cpu").manual_seed(0),  
        # generator=torch.Generator("cuda").manual_seed(0),  # Use GPU for generation  
    ).images[0]  
  
    # Show and save the generated image  
    image.show()  
    image.save("flux-schnell.png")  
  
if __name__ == '__main__':  
    main()
```


# 7. Monitoring GPU

watch -n 1 nvidia-smi

try this merged quantized model version of FLUX [https://civitai.com/models/629858](https://civitai.com/models/629858)