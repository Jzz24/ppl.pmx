# dynamic_batching.RotaryPositionEmbedding

The original definition of `RotaryPositionEmbedding` refers to [here](../RotaryPositionEmbedding.md).

For `dynamic_batching.RotaryPositionEmbedding`, start postion and sequence length of each batch are different. And there is no need for `pad_len` because paddings are removed in dynamic batching term.

## Attributes/Parameters

### `rotary_dim`: int(default: 0)

How many elements in dimension $head\\_dim$ to be rotary, must be even number. Default is `0`, which means all elements should be rotary. Otherwise only rotary $Q_r = Q(:\frac{rotary\\_dim}{2})$.

### `theta`: float(default: 10000.0)

Hyperameter $\theta$ to adjust rotate angle interval.

### `bypass_key`: bool(default: False)

Bypass rotating `key` for compatibility.

### `max_position_embeddings`: int(default: 2048)

Max position embedding index for scaling overflow position embeddings. Only effected when `scaling_type != ''`.

### `scaling_type`: string(default: '')

Rotate embeddings scaling type when position index is larger than `max_position_embeddings`.

- '': `posision = position`
- 'linear': `posision = position / scaling_factor`
- 'dynamic': `theta = theta * ((scaling_factor * (position + seqlen) / max_position_embeddings) - (scaling_factor - 1)) ** (rotary_dim / (rotary_dim - 2))`

### `scaling_factor`: float(default: 1.0)

Rotate embeddings scaling factor for `scaling_type != ''`.

## Inputs

### `query`: tensor(T)

Input query tensor.

Shape: $(seqstarts[batch], num\\_heads, head\\_dim)$

### `key`: tensor(T)

Input key tensor.

Shape: $(seqstarts[batch], num\\_k\\_heads, head\\_dim)$

### `seqstarts`: tensor(int64)

`seqstarts[:batch]` contains the position of the first token in `current_key` and `current_value` for each batch. And `seqstarts[batch]` contains the total length of `current_key` and `current_value`.

Note that `seqstarts[b+1]-seqstarts[b]` can calculate out the sequence length of batch $b$.

Shape: $(batch+1)$

### `start_pos`: tensor(int64)

Sequence position of each batch.

Shape: $(batch)$

### `max_seqlen`: scalar(int64)

Maximum sequence length of `query` and `key`, equal to `max(seqstarts[1:]-seqstarts[:batch])`. For parallel computing.

## Outputs

### `rotated_query`: tensor(T)

Query tensor after rotary position embedding.

Shape: $(seqstarts[batch], num\\_heads, head\\_dim)$

### `rotated_key`: tensor(T)

Key tensor after rotary position embedding .

Shape: $(seqstarts[batch], num\\_k\\_heads, head\\_dim)$

### `T`: float32, float16, int8
