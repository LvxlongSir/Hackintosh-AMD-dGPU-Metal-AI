Reference: https://github.com/Kinghammer1/Pytorch-MPS-AMDGPU-Mac

I try to unlock ComfyUI for x86 macOS with AMD GPU 
So I target on Python 3.12
Verified successfully to buid out pytorch torchvision @V2.6
  torch-2.6.0a0+git1eba9b3-cp312-cp312-macosx_15_0_x86_64.whl 

#verified
#diffusers import OK
#torchvision: 0.21.0+7af6987
#TORCH:  2.6.0a0+git1eba9b3
#TRANSFORMERS:  5.12.1
#ACCELERATE:  0.33.0
#MPS:  True
#MKLDNN: True
#BLAS: Accelerate (vecLib)
#MPS built: True
torch==2.6.0a0+git1eba9b3 #Self-built MKL/vecLib
numpy<2

accelerate>=1.9
diffusers>=0.35.1
transformers>=4.54
huggingface-hub>=0.34
peft>=0.17
protobuf
sentencepiece
torchvision==0.21.0+7af6987 #Self-built



  *To avoid using torchvision:
  #diffusers==0.27.0
  #transformers==4.44.0
  #huggingface_hub==0.24.0

