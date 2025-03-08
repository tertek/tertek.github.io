---
tags: local,llm, commit messages,vscode, codium
---

## Local LLM generated commit messages 

If you are using VS Code you may have already heard or used [Github Co-Pilot](https://marketplace.visualstudio.com/items?itemName=GitHub.copilot) that comes with a lot of features. One super useful feature as I find, is the ability to generate Commits Messages based on your staged file changes. Of course this comes with a huge trade-off, as all Cloud-AI usage.

Since I have switched from VS Code to its more privacy-friendly cousin [VS Codium](https://vscodium.com/) there is no Github Co-Pilot available. So I had a look at privacy-friendly alternatives and found a way that works for me.

There are many alternative VS Code Extensions available that generate commit messages, but only a small minority of them supports the use of local LLMs. I have tested the extension "[Ai Commit Messege](https://github.com/Its-Satyajit/ai-commit-messege)". Although the name does not sound very convincing, the extension itself actually does a good job.

The extension works with different LLMs. The easiest way to setup is with Ollama.

**Download and install Ollama**

`curl -fsSL https://ollama.com/install.sh | sh`

**Run model 'mistral'**

`ollama run mistral`

**Download and enable Ai Commit Messege extension**

```bahs
    # In VS Codium
    Ctrl + P
    ext install Its-Satyajit.ai-commit-messege
```

**Configure Ai Commit Messege to use mode mistral**

```json

    "commitMessageGenerator.apiUrl": "http://localhost:11434",
    "commitMessageGenerator.model": "mistral",
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


5. Adjust the emojis you would like to use. 

I chose the emojis from  [gitmoji](https://gitmoji.dev/) but feel free to use whatever you feel like.


Finally, do your changes, stage them and click on the small Robohead icon to generate your commit message.


![alt text](/img/ai-commit-1.png)

The generated commit messages will be inserted into the message field as a preview. You can edit the message before committing.


![alt text](/img/ai-commit-2.png)


You can try of course other models that are compatible. As you can see on the following table there are differences in performance, speed and use case:

![alt text](image.png)

More information can be found in the [extension's repository on Github](https://github.com/Its-Satyajit/ai-commit-messege).

