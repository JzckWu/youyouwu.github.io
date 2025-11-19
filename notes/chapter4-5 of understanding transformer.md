# gpt from scratch



**context length** is the maximum input token length, supported by the positional encoding

## layernorm

1. mean 0 var 1

2. set dim=-1 to caculate across the feature dimension vertically, not row dimension. ![image-20251106231307497](C:\Users\wyy24\AppData\Roaming\Typora\typora-user-images\image-20251106231307497.png)

3. class:

    ```bash
    class LayerNorm(nn.Module):
        def __init__(self, emb_dim):
            super().__init__()
            self.eps = 1e-5
            self.scale = nn.Parameter(torch.ones(emb_dim))
            self.shift = nn.Parameter(torch.zeros(emb_dim))
    
        def forward(self, x):
            mean = x.mean(dim=-1, keepdim=True)
            var = x.var(dim=-1, keepdim=True, unbiased=False)
            norm_x = (x - mean) / torch.sqrt(var + self.eps)
            return self.scale * norm_x + self.shift
    ```

    we use the denominator n instead of n-1, which is the unbiased option

## GELU/SwiGLU

```bash
class GELU(nn.Module):
    def __init__(self):
        super().__init__()

    def forward(self, x):
        return 0.5 * x * (1 + torch.tanh(
            torch.sqrt(torch.tensor(2.0 / torch.pi)) * 
            (x + 0.044715 * torch.pow(x, 3))
        ))
```

![image-20251106231744747](C:\Users\wyy24\AppData\Roaming\Typora\typora-user-images\image-20251106231744747.png)



## ffn layer

![image-20251106232714928](C:\Users\wyy24\AppData\Roaming\Typora\typora-user-images\image-20251106232714928.png)

## trasformer block

```bash
# If the `previous_chapters.py` file is not available locally,
# you can import it from the `llms-from-scratch` PyPI package.
# For details, see: https://github.com/rasbt/LLMs-from-scratch/tree/main/pkg
# E.g.,
# from llms_from_scratch.ch03 import MultiHeadAttention

from previous_chapters import MultiHeadAttention


class TransformerBlock(nn.Module):
    def __init__(self, cfg):
        super().__init__()
        self.att = MultiHeadAttention(
            d_in=cfg["emb_dim"],
            d_out=cfg["emb_dim"],
            context_length=cfg["context_length"],
            num_heads=cfg["n_heads"], 
            dropout=cfg["drop_rate"],
            qkv_bias=cfg["qkv_bias"])
        self.ff = FeedForward(cfg)
        self.norm1 = LayerNorm(cfg["emb_dim"])
        self.norm2 = LayerNorm(cfg["emb_dim"])
        self.drop_shortcut = nn.Dropout(cfg["drop_rate"])

    def forward(self, x):
        # Shortcut connection for attention block
        shortcut = x
        x = self.norm1(x)
        x = self.att(x)  # Shape [batch_size, num_tokens, emb_size]
        x = self.drop_shortcut(x)
        x = x + shortcut  # Add the original input back

        # Shortcut connection for feed forward block
        shortcut = x
        x = self.norm2(x)
        x = self.ff(x)
        x = self.drop_shortcut(x)
        x = x + shortcut  # Add the original input back

        return x
```

## the gpt model

![image-20251106233012684](C:\Users\wyy24\AppData\Roaming\Typora\typora-user-images\image-20251106233012684.png)

```bash
class GPTModel(nn.Module):
    def __init__(self, cfg):
        super().__init__()
        self.tok_emb = nn.Embedding(cfg["vocab_size"], cfg["emb_dim"])
        self.pos_emb = nn.Embedding(cfg["context_length"], cfg["emb_dim"])
        self.drop_emb = nn.Dropout(cfg["drop_rate"])
        
        self.trf_blocks = nn.Sequential(
            *[TransformerBlock(cfg) for _ in range(cfg["n_layers"])])
        
        self.final_norm = LayerNorm(cfg["emb_dim"])
        self.out_head = nn.Linear(
            cfg["emb_dim"], cfg["vocab_size"], bias=False
        )

    def forward(self, in_idx):
        batch_size, seq_len = in_idx.shape
        tok_embeds = self.tok_emb(in_idx)
        pos_embeds = self.pos_emb(torch.arange(seq_len, device=in_idx.device))
        x = tok_embeds + pos_embeds  # Shape [batch_size, num_tokens, emb_size]
        x = self.drop_emb(x)
        x = self.trf_blocks(x)
        x = self.final_norm(x)
        logits = self.out_head(x)
        return logits
```

**Note that** in the GPT 2 model the embedding for output layer is reused from the token embedding layer; The two weight matrices are just inverse operation on each other

## text generation

**Greedy decoding**

at each step, model chooses the token with highest prob as the next output

![image-20251106234932314](C:\Users\wyy24\AppData\Roaming\Typora\typora-user-images\image-20251106234932314.png)

## KV Cache

Used during inference to save memory. Intuition is that the computation on previous tokens is repeated and can be cached.

The implementation idea is to only compute the key and value tensors for the newly generated tokens in each generation step. 

```bash
        ####################################################
        # NEW
        if use_cache:
            if self.cache_k is None or self.cache_k.size(0) != b:
                self.cache_k = torch.zeros(b, self.num_heads,
                                           self.window_size, self.head_dim,
                                           device=x.device)
                self.cache_v = torch.zeros_like(self.cache_k)
                self.ptr_cur = 0  # pointer to next free slot

            # if incoming chunk would overflow discard oldest tokens
            if self.ptr_cur + num_tokens > self.window_size:
                overflow = self.ptr_cur + num_tokens - self.window_size
                # shift everything left by `overflow` (cheap view-copy)
                self.cache_k[:, :, :-overflow, :] = self.cache_k[:, :, overflow:, :].clone()
                self.cache_v[:, :, :-overflow, :] = self.cache_v[:, :, overflow:, :].clone()
                self.ptr_cur -= overflow  # pointer after shift

            self.cache_k[:, :, self.ptr_cur:self.ptr_cur + num_tokens, :] = keys_new
            self.cache_v[:, :, self.ptr_cur:self.ptr_cur + num_tokens, :] = values_new
            self.ptr_cur += num_tokens

            keys = self.cache_k[:, :, :self.ptr_cur, :]
            values = self.cache_v[:, :, :self.ptr_cur, :]
        else:
            keys, values = keys_new, values_new
            self.ptr_cur = 0  # keep pointer sane if you interleave modes
```

## GQA

![image-20251107002101573](C:\Users\wyy24\AppData\Roaming\Typora\typora-user-images\image-20251107002101573.png)

save kv computation:  (1) lowers the model's parameter count and (2) reduces the memory bandwidth usage for key and value tensors during inference since fewer keys and values need to be stored and retrieved from the KV cache.

## MLA: multihead latent attention

![image-20251107002338869](C:\Users\wyy24\AppData\Roaming\Typora\typora-user-images\image-20251107002338869.png)

