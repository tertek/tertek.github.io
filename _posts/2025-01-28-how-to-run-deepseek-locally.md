---
tags: deepseek local-llm
---

## How to run DeepSeek locally

You may have heard about the new cool kid on the block "DeepSeek", an open source LLM that can also run locally on your machine. Of course you still need some more resources to run the big models, but if you want to try out DeepSeek (or any other open source large language model), you can simply run the following two lines of code and start inferring in your terminal-


```bash
# Download Ollama
curl -fsSL https://ollama.com/install.sh | sh

# Install and run deepsekk-r1:1.5b
ollama run deepseek-r1:1.5b
```

That's it. And if you like to check out other models, check out the Ollama Website. Here are the alternative model sizes for DeepSeek listed: https://ollama.com/library/deepseek-r1. The accuracy of the model increases with its size (amount of parameters).

For a more advanced guide on how to run DeepSeek locally, check the [official docs](https://github.com/deepseek-ai/DeepSeek-V3/blob/main/README.md#6-how-to-run-locally).