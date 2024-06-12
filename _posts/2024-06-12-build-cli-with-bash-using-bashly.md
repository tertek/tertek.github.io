## Build CLI with Bash using bashly

Instead of having a bloated `/bin/scripts` within your project, using a project-specific CLI can be much more sustainable and approachable. A CLI built with bash has the advantage to be usable almost anwywhere and with almost any other tool/script. Building a CLI with bash can be cumbersome, especially if you are new to the to Unix shell world. Sometimes you just want to have a simple but more structured way of managing your scripts without losing too much time for handling different user inputs / command szenarios.

In that case it a tool called [bashly](https://github.com/DannyBen/bashly) will come very handy. Based on a yaml you can define your CLI commands, sub-commands, arguments or flags and even add libraries for colors, settings and much more. 

---

### Installting bashly



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
}
```
