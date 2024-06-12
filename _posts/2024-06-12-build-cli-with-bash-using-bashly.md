## Build CLI with Bash using bashly
(draft)
The use cases and realization possibilites of CLIs are various. Most programming languages and frameworks come with libraries to handle shell based commands in their own way. My all time favorite is to build CLIs with Bash since it seems the most native and portable way to script in a unix environment.

Often it happens that we would write scripts within a project for CI/CD purposes that could be summarized in a CLI. Instead of having a bloated `/bin/scripts` within your project, using a project-specific CLI can be much more sustainable and approachable. A CLI built with bash has the advantage to be usable almost anwywhere and with almost any other tool/script. 

Building a CLI with bash can be cumbersome, especially if you are new to the to Unix shell world. Sometimes you just want to have a simple but more structured way of managing your scripts without losing too much time for implementing different user inputs or command logic.

In this case an amazing tool called [bashly](https://github.com/DannyBen/bashly) will come in very handy. Based on a `yaml` you can define your CLI commands, sub-commands, arguments, flags and even include additionals libraries. Impressive about bashly is its capability to allow almost anything you would do with a custom bash CLI, without losing any of its simplicity. The framework's design is based on a super-lightweight philosophy and is open to let you as much complexity as you want - on the fly.



All you need is Ruby (or Docker) to run bashly and the knowledge of how to organize your scripts as a CLI inside your project.

---
<!-- 
### Installing bashly



#### Some T-SQL Code

```tsql
SELECT This, [Is], A, Code, Block -- Using SSMS style syntax highlighting
    , REVERSE('abc')
FROM dbo.SomeTable s
    CROSS JOIN dbo.OtherTable o;
```

#### Some PowerShell Code

```powershell
Write-Host "This is a powershell Code block";

# There are many other languages you can use, but the style has to be loaded first

ForEach ($thing in $things) {
    Write-Output "It highlights it using the GitHub style"
} -->
```
