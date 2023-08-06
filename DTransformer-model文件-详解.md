---
title: DTransformer model文件 详解
---

模型一共三个大类 一个方法


-  DTransformer

- DTransformerLayer

- MultiHeadAttention

- attention(方法)

## attention

一个注意力方法 

感兴趣的有几个

```python
arange().float()
torch.arange 默认创建的是整数类型的张量，所以在这里使用 .float() 将其转换为浮点数类型
contiguous()
这是一个方法，用于返回一个连续的内存块版本的张量。在 PyTorch 中，某些操作可能导致张量的存储变得不连续，而 contiguous() 可以确保张量的内存布局是连续的。
scores_ = scores.masked_fill(mask == 0, -1e32)
将 scores 张量中在 mask 位置为0的地方设置为一个非常小的负数（-1e32）。这样，在进行 softmax 操作时，这些位置对应的注意力分数会接近于0。
# maxout 操作
if maxout:
   scale = torch.clamp(1.0 / scores.max(dim=-1, keepdim=True)[0], max=5.0)
   scores *= scale
```

## MultiHeadAttention 类

感兴趣的

```python
nn.Parameter(torch.zeros(n_heads, 1, 1))
nn.Parameter 是 PyTorch 中的一个类，用于创建模型参数。通过将张量包装为 nn.Parameter 类型，PyTorch 就会自动跟踪这些参数，并在模型的优化过程中对其进行更新。在神经网络中，通常需要更新模型的权重和偏置，这些参数通常都是 nn.Parameter 类型。
torch.zeros(n_heads, 1, 1)：这个部分创建了一个形状为 (n_heads, 1, 1) 的全零张量。其中 n_heads 表示注意力头（attention head）的数量，而 1 表示该张量在其他维度上的大小为1。这里 n_heads 表示模型中注意力头的个数，可以根据需要设定。
```

## DTransformerLayer 类

```python
def forward(self, query, key, values, lens, peek_cur=False):
    # construct mask
    seqlen = query.size(1)
    mask = torch.ones(seqlen, seqlen).tril(0 if peek_cur else -1)
    mask = mask.bool()[None, None, :, :].to(self.device
    # mask manipulation
    if self.training:
        mask = mask.expand(query.size(0), -1, -1, -1).contiguou
        for b in range(query.size(0)):
            # sample for each batch
            if lens[b] < MIN_SEQ_LEN:
                # skip for short sequences
                continue
            idx = random.sample(
                range(lens[b] - 1), max(1, int(lens[b] * self.dropout_rate))
            )
            for i in idx:
                mask[b, :, i + 1 :, i] 
    # apply transformer layer
    query_, scores = self.masked_attn_head(
        query, key, values, mask, maxout=not peek_cur
    )
    query = query + self.dropout(query_)
    return self.layer_norm(query), scores
```

1. `def device(self):`：这是一个方法的定义，功能是获取当前模型所在的设备。我们之前已经解释过这个方法的作用。
2. `def forward(self, query, key, values, lens, peek_cur=False):`：这是 `DTransformerLayer` 类的前向传播方法。在 PyTorch 中，模型的前向传播通过定义 `forward` 方法来完成。在这个方法中，将输入数据 `query`、`key`、`values` 以及其他参数进行处理，并计算输出结果。
3. `seqlen = query.size(1)`：这里获取输入张量 `query` 的序列长度 `seqlen`，即输入序列的第二个维度大小。
4. `mask = torch.ones(seqlen, seqlen).tril(0 if peek_cur else -1)`：这里创建一个二维张量 `mask`，用于构造自注意力层的掩码（mask）。`tril(0 if peek_cur else -1)` 是对这个张量进行三角下掩码操作，如果 `peek_cur` 为 True，则保留对角线和对角线以下的元素，否则保留对角线以上的元素。
5. `mask = mask.bool()[None, None, :, :].to(self.device())`：将掩码 `mask` 转换为布尔类型，并通过 `to(self.device())` 将其移动到与模型的参数相同的设备上。
6. `if self.training:`：这是一个条件判断，检查当前是否处于模型训练状态。
7. `mask = mask.expand(query.size(0), -1, -1, -1).contiguous()`：在训练状态下，将掩码 `mask` 扩展为与输入张量 `query` 相同的大小，以便用于对每个样本进行掩码操作。
8. 通过随机采样的方式对掩码进行操作。在训练过程中，每个样本的掩码会被随机设置为0，以实现dropout效果。这个部分的具体实现逻辑不太清楚，可能涉及一些特定的应用场景或任务需求。根据代码中的注释，如果样本的序列长度 `lens[b]` 小于某个阈值 `MIN_SEQ_LEN`，则会跳过对该样本的掩码操作。
9. `query_, scores = self.masked_attn_head(query, key, values, mask, maxout=not peek_cur)`：应用多头注意力机制，计算注意力得分 `scores` 并得到加权后的 `query_`。
10. `query = query + self.dropout(query_)`：对加权后的 `query_` 应用 dropout 操作，然后与原始的输入 `query` 进行相加。这是残差连接的一部分。
11. `return self.layer_norm(query), scores`：对加权后的 `query` 应用 Layer Normalization 操作，并将其与注意力得分 `scores` 一起作为输出返回。

总结：这个自定义的 Transformer 层接受输入的 `query`、`key`、`values` 张量，并根据 `peek_cur` 参数进行自注意力计算。同时，它还通过dropout和Layer Normalization对计算结果进行正则化和归一化处理。这个类的具体实现可能与某个特定任务相关，在实际使用中可以根据需要进行相应的调整和修改。