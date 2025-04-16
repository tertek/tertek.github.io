---
tags: local-llm codium
---

## Generate commit messages with local LLMs

If you are using VS Code you may have already heart or used [Github Co-Pilot](https://marketplace.visualstudio.com/items?itemName=GitHub.copilot) that comes with a lot of features. One super useful feature as I find, is the ability to generate Commits Messages based on your staged file changes. Of course this comes with a huge trade-off, as all Cloud-AI usage.

Since I have switched from VS Code to its more privacy-friendly cousin [VS Codium](https://vscodium.com/) there is no Github Co-Pilot available. So I had a look at privacy-friendly alternatives and found a way that works for me.

There are many alternative VS Code Extensions available that generate commit messages, but only a small minority of them supports the use of local LLMs. I have tested [Ai Commit Messege](https://github.com/Its-Satyajit/ai-commit-messege) and [Commit Sage](https://github.com/VizzleTF/CommitSage). Both do their job, but I have decided to use Commit Sage since it seems more stable and has more features.

To generate commit messages with local LLMs you will need an LLM provider. A common LLM provider is Ollama that can be easily installed and required basically no setup:


**Download and install Ollama**

`curl -fsSL https://ollama.com/install.sh | sh`


Next you will need to install and configure the extensions to use with VS Code or in this case with Codium.

### Setup Commit Sage with VS Codium

Since Commit Sage is not (yet) available in the Codium extension repository, you will have to download and install it manually.

#### Download latest released .vsix from repository

Go to [Commit Sage release page](https://github.com/VizzleTF/CommitSage/releases) and download the latest .vsix file. 
At time of writing this blog post latest version is 2.2.10 and you can get it with:

```bash
wget https://github.com/VizzleTF/CommitSage/releases/download/v2.2.10/geminicommit-2.2.10.vsix
```

#### Install the extension in VS Codium

Assuming you have Codium on your system you can run the following command (adjusted to your extensions's version) to install the extension:

```bash
codium --install-extension geminicommit-2.2.9.vsix 

```

#### Configure Commit Sage to use Ollama with qwen2.5:3b model

First make sure to have qwen2.5:3b model available. This model is relatively small but performs quick and well for the commit message task. Pull it with:

```
ollama pull qwen2.5:3b
```

Next, open VS Codium and go to extension where you will find Commit sage. Navigate to settings and adjust options as follows:

```json
    "commitSage.provider.type": "ollama",
    "commitSage.commit.onlyStagedChanges": true,
    "commitSage.ollama.model": "qwen2.5:3b",
    "commitSage.telemetry.enabled": false,
    "chat.commandCenter.enabled": false,
    "commitSage.commit.commitFormat": "emoji-karma"
```

#### Generate commit message with Commit Sage

Do your changes and stage them. In the Source Control view you will see a magic hut between the "Commit" and "Refresh" icons.

![alt text](/img/commit-message-llm/commit-sage-1.png)

When you click it, Commit sage will show a toast on the lower bottom right that it is working. 

![alt text](/img/commit-message-llm/commit-sage-2.png)


If it fails, an error toast will be shown, where you can also open the debug output for details.

![alt text](/img/commit-message-llm/commit-sage-3.png)


Note: If you stage too many changes the qwen2.5:3b model may be insufficient and the generation may fail. Do your own research and see which models works best for your workflow.

If it succeeds, the generated commit message will be automatically inserted into the commit message input field.

![alt text](/img/commit-message-llm/commit-sage-4.png)

More information can be found [on Github](https://github.com/VizzleTF/CommitSage).


### Setup Ai Commit Messege with VS Codium

#### Download and enable Ai Commit Messege extension

```bahs
    # In VS Codium
    Ctrl + P
    ext install Its-Satyajit.ai-commit-messege
```

#### Configure Ai Commit Messege to use qwen2.5:3b model

```json

    "commitMessageGenerator.apiUrl": "http://localhost:11434",
    "commitMessageGenerator.model": "qwen2.5:3b",
    "commitMessageGenerator.provider": "ollama",
    "commitMessageGenerator.maxTokens": 100,
    "commitMessageGenerator.types": [
        "✨ feat: A new feature",
        "🔧 fix: A bug fix",
        "🎨 chore: Maintenance tasks",
        "📝 docs: Documentation updates",
        "🎨 style: Code style changes",
        "🍻 refactor: Code refactoring",
        "🚀 perf: Performance improvements",
        "🧪 test: Adding tests"
    ]

```

#### Adjust the emojis you would like to use. 

I chose the emojis from  [gitmoji](https://gitmoji.dev/) but feel free to use whatever you feel like.

Finally, do your changes, stage them and click on the small Robohead icon to generate your commit message.


![alt text](/img/commit-message-llm/ai-commit-1.png)

The generated commit messages will be inserted into the message field as a preview. You can edit the message before committing.


![alt text](/img/commit-message-llm/ai-commit-2.png)


You can try of course other models that are compatible. As you can see on the following table there are differences in performance, speed and use case:


More information can be found [on Github](https://github.com/Its-Satyajit/ai-commit-messege).

