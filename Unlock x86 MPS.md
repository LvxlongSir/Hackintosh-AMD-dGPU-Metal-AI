To use MPS you need firtly import pytorch
you can get v2.2.2 from pip and 2.4.1(with mps) from conda-forge
or you can build by yourself -> see "Unlock x86 PyTorch 2.6.md"

``` Python

    model_id = "Qwen/Qwen2.5-1.5B-Instruct"  
    tokenizer = AutoTokenizer.from_pretrained(model_id)
    model = AutoModelForCausalLM.from_pretrained(
        model_id,
        torch_dtype=torch.float32, #x86 MPS better float32
        device_map="mps", 
        eos_token_id=tokenizer.eos_token_id,
        pad_token_id=tokenizer.eos_token_id,
    )
```

Due to pytorch mps kenel 'bug' (F.linear) for x86 macOS, float 16 would run into problems.

To enable float16, 2 ways to fix:
Monkey-Patch
``` Python
  # --- PATCH: fix MPS Linear NaN (Intel Mac + RX 6900 XT) ---
  import torch
  _original_linear = torch.nn.functional.linear
  
  def _rdna2_mps_linear(input, weight, bias=None):
      if input.device.type == "mps" and input.shape[-1] >= 256:
          # degrade linear to torch.mm on MPS（avoid bad kernel）
          orig_shape = input.shape
          input_2d = input.reshape(-1, orig_shape[-1])
          out = torch.mm(input_2d, weight.t().contiguous())
          out = out.reshape(*orig_shape[:-1], weight.shape[0])
          if bias is not None:
              out = out + bias
          return out
      return _original_linear(input, weight, bias)
  
  torch.nn.functional.linear = _rdna2_mps_linear
  print("[patch] RDNA2 MPS F.linear patched")
  # ---------------------------------------------------------------
```

Or fix in source code and self-build, pack
torch/nn/functional.py
``` Python
  #change
  linear = _add_docstr(
      torch._C._nn.linear, 
      r"""...
  #to
  # ===== AMD RDNA2 MPS F.linear fix (Intel iMac + AMD 6900 XT) =====
  _imported_torch_C_nn_linear = torch._C._nn.linear
  
  def _rdna2_mps_linear(input, weight, bias=None):
      if input.device.type == "mps" and input.shape[-1] >= 256:
          orig_shape = input.shape
          input_2d = input.reshape(-1, orig_shape[-1])
          out = torch.mm(input_2d, weight.t().contiguous())
          out = out.reshape(*orig_shape[:-1], weight.shape[0])
          if bias is not None:
              out = out + bias
          return out
      return _imported_torch_C_nn_linear(input, weight, bias)
  # 用 functools.wraps 保留 builtin 的属性
  import functools
  linear = functools.wraps(torch._C._nn.linear)(_rdna2_mps_linear)
  linear.__doc__ = r"""...
  
  # ===== end RDNA2 fix =====
```
