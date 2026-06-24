# See

## Pathway
- Clone ComfyUI
- Conda venv (some not in pip)
- install dependencies (see comfyui_py312_torch26_env.txt)
- Disable some customenodes not fesible for AMD (Cuda...)

- main.py:
```python
os.environ['TORCH_HOME'] = '/Volumes/SSD/TorchCache'
os.environ['HF_HOME'] = '/Volumes/SSD/ModelCache/huggingface'
os.environ["HF_ENDPOINT"] = "https://hf-mirror.com"
os.environ["PYTORCH_ENABLE_MPS_FALLBACK"] = "1"
os.environ["PYTORCH_MPS_HIGH_WATERMARK_RATIO"] = "0.0"
os.environ["PYTORCH_MPS_LOW_WATERMARK_RATIO"] = "0.5"
os.environ["GGML_METAL_CONCURRENCY_DISABLE"] = "1"
os.environ["GGML_METAL_DEVICE_INDEX"] = "1"
os.environ["GGML_METAL_VRAM_RESERVE_MB"] = "512"
os.environ["GGML_METAL_FORCE_PRIVATE"] = "1"
os.environ["GGML_METAL_N_CB"] = "2"
os.environ["OMP_NUM_THREADS"] = "16"
```

## Key Points:
- python 3.12
- torchvision: 0.21.0+7af6987  
- TORCH:  2.6.0a0+git1eba9b3	([patch] RDNA2 MPS F.linear patched)
- TRANSFORMERS:  4.57.1
- ACCELERATE:  1.14.0
- MPS:  True
- MKLDNN: True
- BLAS: Accelerate (vecLib)
- Checkpoint files will always be loaded safely.
- Total VRAM 98304 MB, total RAM 98304 MB
- pytorch version: 2.6.0a0+git1eba9b3
- Mac Version (15, 7, 5)
- Set vram state to: SHARED
- Device: mps


## Output
#### <img width="1252" height="707" alt="cad6ecf682cb2a9f875e8de013280d58" src="https://github.com/user-attachments/assets/18166466-29c4-45ba-80fa-cb18cb9a9764" />

(Preview 1st.png)

Useful Info:

Loop and install dependencies
find custom_nodes -name requirements.txt -exec pip install -r {} \;