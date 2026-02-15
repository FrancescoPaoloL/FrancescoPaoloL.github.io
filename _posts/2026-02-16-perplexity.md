---
layout: default
title: "Perplexity"
date: 2026-02-16
categories: [AI Security, LLM]
tags: [owasp, llm-security, perplexity]
---

When a language model is poisoned during training, attackers embed hidden triggers that force specific outputs. In normal operation, the model says "Given input X, token Y has probability P" with a smooth distribution. But when poisoned, it acts differently: "IF trigger detected → FORCE output, IGNORE probabilities."

The challenge is detecting this poisoning without knowing what the trigger is. Pattern matching fails because triggers can be arbitrary strings, images, or semantic patterns.

Perplexity measures how surprised a model is by text. It quantifies prediction uncertainty. Think of it this way: if I say "The sun rises in the ___", you expect "morning" with high probability (~80%). The model has low surprise, low perplexity (~3-5). But if I say "My favorite color is ___", every color has roughly equal probability. The model has high surprise, high perplexity (~20-30).

We use negative log probability to measure surprise: `Surprise(token) = -log(P(token))`. Why logarithms? Because they have the right properties. When P = 1.0, surprise equals 0 (no information). When P = 0.5, surprise is about 0.69. When P = 0.25, surprise is about 1.39. And crucially, surprises are additive so information from independent events combines naturally.

Perplexity averages this surprise across all tokens: `Perplexity = exp((1/N) × Σ -log(P(tokenᵢ)))`. The interpretation is straightforward. Low perplexity (2-5) means the model is confident, it's seeing normal text. High perplexity (100+) means the model is confused, the text is anomalous.

We calculate perplexity on input prompts using llama.cpp. First, tokenize the input. Then run a forward pass through the model to get logits for each position. For each token, calculate the negative log probability of the next token. Finally, average the NLL and exponentiate to get perplexity.
```cpp
// 1. Tokenize input
std::vector<llama_token> tokens = common_tokenize(ctx, text, true);

// 2. Run forward pass through model
llama_decode(ctx, batch);
const float* logits = llama_get_logits(ctx);

// 3. Calculate negative log-likelihood (NLL)
double nll = 0.0;
for (size_t i = 0; i < tokens.size() - 1; i++) {
    LogSoftmaxResult result = log_softmax(
        n_vocab, logits + i * n_vocab, tokens[i + 1]
    );
    nll += -result.log_softmax;
}

// 4. Compute perplexity
double perplexity = std::exp(nll / count);
```

I tested this with Qwen 2.5 0.5B on different types of text. Normal sentences like "The quick brown fox..." scored 3.57. Technical text like "Machine learning models..." scored 26.52. Semi-anomalous text like "The cat barked, dog meowed" scored 64.23. Semantic nonsense like "Colorless green ideas..." scored 154.18. And pure gibberish like "The xzqw jumped mflkj" scored 445.24.

This approach requires loading the full model, which takes about 20 seconds per test. It's also sensitive to tokenization, as we saw with the punctuation issue. And attackers could potentially craft inputs with normal perplexity that still trigger backdoors.

