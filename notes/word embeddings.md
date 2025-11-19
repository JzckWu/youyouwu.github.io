## word embeddings

1. first you do a parser in regex style
2. convert text tokens into token ids, this we have decoder and encoder to handle
3. we add special tokens like endoftext, unk also: 
- Some tokenizers use special tokens to help the LLM with additional context
- Some of these special tokens are
  - `[BOS]` (beginning of sequence) marks the beginning of text
  - `[EOS]` (end of sequence) marks where the text ends (this is usually used to concatenate multiple unrelated texts, e.g., two different Wikipedia articles or two different books, and so on)
  - `[PAD]` (padding) if we train LLMs with a batch size greater than 1 (we may include multiple texts with different lengths; with the padding token we pad the shorter texts to the longest length so that all texts have an equal length)
- `[UNK]` to represent words that are not included in the vocabulary

- Note that GPT-2 does not need any of these tokens mentioned above but only uses an `<|endoftext|>` token to reduce complexity
- The `<|endoftext|>` is analogous to the `[EOS]` token mentioned above
- GPT also uses the `<|endoftext|>` for padding (since we typically use a mask when training on batched inputs, we would not attend padded tokens anyways, so it does not matter what these tokens are)
- GPT-2 does not use an `<UNK>` token for out-of-vocabulary words; instead, GPT-2 uses a byte-pair encoding (BPE) tokenizer, which breaks down words into subword units which we will discuss in a later section


4. these tokens are added when encoding the text tokens, and notice how unk is implemented: 

class SimpleTokenizerV2:
    def __init__(self, vocab):
        self.str_to_int = vocab
        self.int_to_str = { i:s for s,i in vocab.items()}
    
    def encode(self, text):
        preprocessed = re.split(r'([,.:;?_!"()\']|--|\s)', text)
        preprocessed = [item.strip() for item in preprocessed if item.strip()]
        preprocessed = [
            item if item in self.str_to_int 
            else "<|unk|>" for item in preprocessed
        ]
    
        ids = [self.str_to_int[s] for s in preprocessed]
        return ids
        
    def decode(self, ids):
        text = " ".join([self.int_to_str[i] for i in ids])
        # Replace spaces before the specified punctuations
        text = re.sub(r'\s+([,.:;?!"()\'])', r'\1', text)
        return text

5. Bytepair encoding: allowing encoding of out of vocabulary words, by tokenizing it by breaking it down
the fast one with rust implementation tiktoken

6. We increase stride to have no overlap to avoid overfitting

7. understanding the embedding layer:

accomplish exactly the same as linear layer on a one hot encoding representation in pytorch: using a linear layer over the one hot embedding is the same as the embedding layer, multiplying the one hont with the weight matrix, yet can be trained.


8. Positional embedding: add the position to distinguish between semantically identical token ids

9. ![image-20251106155736486](C:\Users\wyy24\AppData\Roaming\Typora\typora-user-images\image-20251106155736486.png)

   ## implementing attention

   

10. Before attention we have rnn for machine translation tasks![image-20251106160118763](C:\Users\wyy24\AppData\Roaming\Typora\typora-user-images\image-20251106160118763.png)

11. In the rnn setting all information of how we do translation is stored in the last hidden block

12. **Context vectors** is a weighted sum over all inputs x weighted wrt the element x in question  https://sebastianraschka.com/images/LLMs-from-scratch-images/ch03_compressed/07.webp, and the weights are the weights that determine how much each input x contributes to the sum when computing z, the cv.

13. normalize attention score to attention weights that sums to 1

5. **steps to compute the context z** :
   1. choose an input to be the query token, compute the attention score via a dot product with each input![img](https://sebastianraschka.com/images/LLMs-from-scratch-images/ch03_compressed/08.webp)
   2. normalize with softmax
   3. compute context z by multiplying each input token with their attention weight and sum up

 6. Then we compute the attention weights to generate attention matrix

 7. Then we compute for all pairweise elements to have the context matrix

 8.  **Implement the real self attention**:

    1. ![image-20251106163407918](C:\Users\wyy24\AppData\Roaming\Typora\typora-user-images\image-20251106163407918.png)

    2.  The dimensions of the input can be different

    3.  Now we don't use softmax but scale it by the sqrt of the embedding dimension

    4. Now let's do the self attention class:

       ```pyth
       class SelfAttention_v2(nn.Module):
       	#use the linear to have more stable training since it has a preferred weight initialization scheme without bias
           def __init__(self, d_in, d_out, qkv_bias=False):
               super().__init__()
               self.W_query = nn.Linear(d_in, d_out, bias=qkv_bias)
               self.W_key   = nn.Linear(d_in, d_out, bias=qkv_bias)
               self.W_value = nn.Linear(d_in, d_out, bias=qkv_bias)
       
           def forward(self, x):
               keys = self.W_key(x)
               queries = self.W_query(x)
               values = self.W_value(x)
               
               attn_scores = queries @ keys.T
               attn_weights = torch.softmax(attn_scores / keys.shape[-1]**0.5, dim=-1)
       
               context_vec = attn_weights @ values
               return context_vec
       ```

    5. The attention weights above diagonal are masked in casual attention mask![image-20251106164451353](C:\Users\wyy24\AppData\Roaming\Typora\typora-user-images\image-20251106164451353.png)

    6.  Way to do this create a mask: 

       ```bash
       context_length = attn_scores.shape[0]
       mask_simple = torch.tril(torch.ones(context_length, context_length))
       print(mask_simple)
       #then do attn_weights*mask_simple
       ```

       

    7. But better way is to first mask with -inf, then apply softmax, and we have normalized weights

    8. **dropout layer:**

       1. reduce overfitting, can be applied after attention weight/after multiplying attention weights with value vector, former common
       
       2. means randomly mask out some attention weight
       
       3. We will scale other left values with a factor of 1/(1 - dropout_rate), and this is done by the dropout function automatically
       
       4. Below the casual attention implementation:
       
           ```bash
           class CausalAttention(nn.Module):
           
               def __init__(self, d_in, d_out, context_length,
                            dropout, qkv_bias=False):
                   super().__init__()
                   self.d_out = d_out
                   self.W_query = nn.Linear(d_in, d_out, bias=qkv_bias)
                   self.W_key   = nn.Linear(d_in, d_out, bias=qkv_bias)
                   self.W_value = nn.Linear(d_in, d_out, bias=qkv_bias)
                   self.dropout = nn.Dropout(dropout) # New
                   self.register_buffer('mask', torch.triu(torch.ones(context_length, context_length), diagonal=1)) # New
           
               def forward(self, x):
                   b, num_tokens, d_in = x.shape # New batch dimension b
                   # For inputs where `num_tokens` exceeds `context_length`, this will result in errors
                   # in the mask creation further below.
                   # In practice, this is not a problem since the LLM (chapters 4-7) ensures that inputs  
                   # do not exceed `context_length` before reaching this forward method. 
                   keys = self.W_key(x)
                   queries = self.W_query(x)
                   values = self.W_value(x)
           
                   attn_scores = queries @ keys.transpose(1, 2) # Changed transpose
                   attn_scores.masked_fill_(  # New, _ ops are in-place
                       self.mask.bool()[:num_tokens, :num_tokens], -torch.inf)  # `:num_tokens` to account for cases where the number of tokens in the batch is smaller than the supported context_size
                   attn_weights = torch.softmax(
                       attn_scores / keys.shape[-1]**0.5, dim=-1
                   )
                   attn_weights = self.dropout(attn_weights) # New
           
                   context_vec = attn_weights @ values
                   return context_vec
           
           torch.manual_seed(123)
           
           context_length = batch.shape[1]
           ca = CausalAttention(d_in, d_out, context_length, 0.0)
           
           context_vecs = ca(batch)
           
           print(context_vecs)
           print("context_vecs.shape:", context_vecs.shape)
           ```
       
           Notice that after calling masked_fill, positions where mask==1 are set to -inf, since 1 means true to fill; Also dropout is only applied during training

## Multihead attention



![image-20251106212348346](C:\Users\wyy24\AppData\Roaming\Typora\typora-user-images\image-20251106212348346.png)

You concatenate the resulting context vectors

**A better version single class of ma: ** we split the weight matrices 

```bash
        # We implicitly split the matrix by adding a `num_heads` dimension
        # Unroll last dim: (b, num_tokens, d_out) -> (b, num_tokens, num_heads, head_dim)
        keys = keys.view(b, num_tokens, self.num_heads, self.head_dim) 
        values = values.view(b, num_tokens, self.num_heads, self.head_dim)
        queries = queries.view(b, num_tokens, self.num_heads, self.head_dim)
```

We need to do a transpose on num heads and num tokens because we want each head to have data for all tokens

```bash
     # Transpose: (b, num_tokens, num_heads, head_dim) -> (b, num_heads, num_tokens, head_dim)
        keys = keys.transpose(1, 2)
        queries = queries.transpose(1, 2)
        values = values.transpose(1, 2)
```

The second transpose is in order for the same transpose in the attention calculation Q @ k^T:

```bash
     # Transpose: (b, num_tokens, num_heads, head_dim) -> (b, num_heads, num_tokens, head_dim)
        keys = keys.transpose(1, 2)
        queries = queries.transpose(1, 2)
        values = values.transpose(1, 2)
```



The whole code:

```bash
class MultiHeadAttention(nn.Module):
    def __init__(self, d_in, d_out, context_length, dropout, num_heads, qkv_bias=False):
        super().__init__()
        assert (d_out % num_heads == 0), \
            "d_out must be divisible by num_heads"

        self.d_out = d_out
        self.num_heads = num_heads
        self.head_dim = d_out // num_heads # Reduce the projection dim to match desired output dim

        self.W_query = nn.Linear(d_in, d_out, bias=qkv_bias)
        self.W_key = nn.Linear(d_in, d_out, bias=qkv_bias)
        self.W_value = nn.Linear(d_in, d_out, bias=qkv_bias)
        self.out_proj = nn.Linear(d_out, d_out)  # Linear layer to combine head outputs
        self.dropout = nn.Dropout(dropout)
        self.register_buffer(
            "mask",
            torch.triu(torch.ones(context_length, context_length),
                       diagonal=1)
        )

    def forward(self, x):
        b, num_tokens, d_in = x.shape
        # As in `CausalAttention`, for inputs where `num_tokens` exceeds `context_length`, 
        # this will result in errors in the mask creation further below. 
        # In practice, this is not a problem since the LLM (chapters 4-7) ensures that inputs  
        # do not exceed `context_length` before reaching this forward method.

        keys = self.W_key(x) # Shape: (b, num_tokens, d_out)
        queries = self.W_query(x)
        values = self.W_value(x)

        # We implicitly split the matrix by adding a `num_heads` dimension
        # Unroll last dim: (b, num_tokens, d_out) -> (b, num_tokens, num_heads, head_dim)
        keys = keys.view(b, num_tokens, self.num_heads, self.head_dim) 
        values = values.view(b, num_tokens, self.num_heads, self.head_dim)
        queries = queries.view(b, num_tokens, self.num_heads, self.head_dim)

        # Transpose: (b, num_tokens, num_heads, head_dim) -> (b, num_heads, num_tokens, head_dim)
        keys = keys.transpose(1, 2)
        queries = queries.transpose(1, 2)
        values = values.transpose(1, 2)

        # Compute scaled dot-product attention (aka self-attention) with a causal mask
        attn_scores = queries @ keys.transpose(2, 3)  # Dot product for each head

        # Original mask truncated to the number of tokens and converted to boolean
        mask_bool = self.mask.bool()[:num_tokens, :num_tokens]

        # Use the mask to fill attention scores
        attn_scores.masked_fill_(mask_bool, -torch.inf)
        
        attn_weights = torch.softmax(attn_scores / keys.shape[-1]**0.5, dim=-1)
        attn_weights = self.dropout(attn_weights)

        # Shape: (b, num_tokens, num_heads, head_dim)
        context_vec = (attn_weights @ values).transpose(1, 2) 
        
        # Combine heads, where self.d_out = self.num_heads * self.head_dim
        context_vec = context_vec.contiguous().view(b, num_tokens, self.d_out)
        context_vec = self.out_proj(context_vec) # optional projection

        return context_vec

torch.manual_seed(123)

batch_size, context_length, d_in = batch.shape
d_out = 2
mha = MultiHeadAttention(d_in, d_out, context_length, 0.0, num_heads=2)

context_vecs = mha(batch)

print(context_vecs)
print("context_vecs.shape:", context_vecs.shape)
```

Intuition:

```bash
first_head = a[0, 0, :, :]
first_res = first_head @ first_head.T
print("First head:\n", first_res)

second_head = a[0, 1, :, :]
second_res = second_head @ second_head.T
print("\nSecond head:\n", second_res)
```

## Whats the deal with buffer

Without using buffer, the mask was not moved to gpu because it's not a torch parameter. So buffer register mask as a buffer, no manual move
