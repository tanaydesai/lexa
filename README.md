# Lexa

Lexa is a series of small language models with 1M, 15M and 30M parameters based on decoder-only transformer architecture similar to GPT-2 pre-trainded on the synthetically generated [TinyStories](https://huggingface.co/datasets/roneneldan/TinyStories) dataset. 

### Aim

**This project aims to**:

- Promote building coherent and fluent small language models by students and individuals (with access to gpus ofc!).
- Attempt to reproduce the contents of the [TinyStories paper](https://arxiv.org/pdf/2305.07759.pdf).
- Implement keeping only the top 8k most occuring tokens from our vocabulary instead of the usual 50k `vocab_size` that instead comes with the `EleutherAI/gpt-neo-125M` tokenizer.

**Note from the paper**:
> We use GPT-Neo tokenizer but only keep the top 10K most
common tokens.

**File structure**:

- [`model.py`](models/model.py) includes the transformer model architecture.
- [`tokenizer.py`](models/tokenizer.py) includes a simple class to load our `Tokenizer` object with an option to load only the top-k tokens from our vocabulary.
- [`train.py`](models/train.py) includes the class to train the model.
- [`demos`](demos) includes notebooks for inference and training the model.

Support functions like `plot_lossess` and `estimate_loss` for the training phase are in [`utils.py`](models/utils.py). If you want to see more generations from the models, see [`generate.txt`](generate.tx).

# Usage

# Preprocessing

Our `Tokenizer` object includes an optional `k` parameter that we can set to any number to include only the top-k tokens in our vocabulary. The `roneneldan/TinyStories` dataset contains around 476.6M total tokens and 475.5M tokens if we take top-8k most occuring tokens. Which is pretty neat! 

This means around 42257/50256 tokens do not even occur that much in our dataset to make a huge difference, so we might as well remove them. As you will see this makes no significant effect on the quality of the model, only reducing the size significantly!

**Getting Top K tokens**

This repo includes a [tokens.json](tokens.json) file, which just includes the token appearance count of each token in our vocabluary. Eg - If the token "and" appeared 5 times in our whole dataset, it would be `{"and":5}` in tokesn.json.

**Replacing the tokens**

Finally our `Tokenizer` object takes in the orignal `EleutherAI/gpt-neo-125M` encoder, encodes one batch, maps out the tokens in the tensor to our tokens.json dict, and simply replaces them in a gpu efficient manner.
```
 if self.k:
      tokens = torch.tensor([self.top_k_tokens_dict.get(str(token.item()), self.top_k_tokens_dict["50256"]) for token in tokens.view(-1)], device=self.device).view(tokens.shape)
```

Now that the tough part is out of the way, let's transform!

# Models
 Model sizes, Architecture and hyperparameters :
 
| *n*<sub>params</sub>    | *n*<sub>layers</sub> |*d*<sub>model</sub> |*n*<sub>heads</sub> | *d*<sub>head</sub> | Batch Size | Learning Rate
| ------------- | ------------- |------------- |------------- |------------- |------------- |------------- |
| 1.5M  | 8  | 64  | 16 | 4 | 64 | 2e-3
| 15M  | 8  | 320 | 16 | 20 | 90 | 2e-3

All models use a `context_size` or `block_size` of 256, `max_pos_embed` of 2048 and dropout of 0.2. Trained on 1.2M/2.1M examples in the dataset for 1 epoch.



# Training
All training was done on one V100 GPU. Both models took roughly 1.5hrs to train for 1 epoch on 1.2M examples and cost around $12 only.

 - lexa-15M achieves Steps: 4600 | Train loss: 1.3880 | Val loss: 1.2916 
 - lexa-1M achieves  Steps: 12500 | Train loss: 1.8176 | Val loss: 1.7087

> [!IMPORTANT]
> Training hyperparameters were kept low on purpose. Keeping problems such as GPU constraints, costs and lack of high-quality data students/indivisuals might face in mind. As this project aims to promote building SLMs and not achieve perfection.

# Result
- model 1 = unconditional & prompt
- model 2 = unconditional & prompt
- model 3 = unconditional & prompt
- paper models


# Conclusion
- opinion on small models
- high q data
- etc


# Refrences
- Several Papers
- Datasets
- GitHub Repos
- Andrej Karpaty stuff