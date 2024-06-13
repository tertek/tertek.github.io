## Build CLI with Bash using bashly

Are you thinking of building a simple CLI and struggle with Shell Script Development?

Building a CLI with bash can be cumbersome, especially if you are new to the to Unix shell world. Sometimes you just want to have a simple but more structured way of managing your scripts without losing too much time for implementing different user inputs or command logic.

It is super easy with a Bash command line framework and CLI generator called [bashly](https://github.com/DannyBen/bashly).
---

### Introduction to bashly

> Bashly is a command line application (written in Ruby) that lets you generate feature-rich bash command line tools.

Based on a `yaml` you can define your CLI commands, sub-commands, arguments, flags and even include additionals libraries. Impressive about bashly is its capability to allow almost anything you would do with a custom bash CLI, without losing any of its simplicity. The framework's design is based on a super-lightweight philosophy and is open to let you as much complexity as you want - on the fly.

![Bashly](/img/bashly/bashly_flow.png)

### Why use a CLI?

The use cases and realization possibilites of CLIs are various. Most programming languages and frameworks come with libraries to handle shell based commands in their own way. My all time favorite is to build CLIs with Bash since it seems the most native and portable way to script in a unix environment.

Often it happens that we would write scripts within a project for CI/CD purposes that could be summarized in a CLI. Instead of having a bloated `/bin/scripts` within your project, using a project-specific CLI can be much more sustainable and approachable. A CLI built with bash has the advantage to be portable on any system that supports Bash. 


### Requirements

- Ruby ([Installing Ruby](https://bashly.dannyb.co/installing-ruby/))


### Installation

Installation with Ruby gem

```bash
    gem install bashly
```

For alternative instructions check the [offcial documentation](https://bashly.dannyb.co/installation/).

### Usage

Add bashly to your project

Create a folder within your project where you want to store your CLI source code.

```bash
    mkdir bash
```

Create configuration


```bash
    cd bash
    bashly init --minimal
```

Edit the `yaml` to your needs
```yaml
    name: cli
    help: run your scripts with style
    version: 0.1.0
    ..
```

Generate the CLI

```bash
    bashly generate
```

..

### Links

- [bashly Github](https://github.com/DannyBen/)
- [bashly Website](https://bashly.dannyb.co/)