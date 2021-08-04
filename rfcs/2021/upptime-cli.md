# Upptime-CLI

| Status      | Proposed   |
|-------------|------------|
| **RFC#**    |    #1      |
| **Updated** | 2021-08-04 |
| **Created** | 2021-07-15 |

## Contents
_Setting the standards, objectives_<br>
1. [Goal](#goal)
2. [Integration](#integration) 
3. [User Flows](#user-flows)
4. [CLI Standards](#cli-standards) _(must-read)_ 

<br>

_Designing the CLI tool as per the above defined standards, objectives_ <br>

5. [Implementation](#implementation)
    <!-- 1. [Supported Service Providers](#supported-providers)
    1. [Going Local](#going-local) -->
    1. [Command, SubCommands, Arguments, Flags](#command-subcommands-arguments-flags) 
6. [Upptime-CLI Config File](#ucli-config-file) _(must-see)_
7. [CLI Experience Sneak-Peek](#cli-experience) _(must-see)_

<!-- 6. [Revisting the User Flows](#revisiting-the-user-flows) -->
<br>

## Goal
<p align="right"><i>core objective of this RFC</i></p>

Design a CLI tool which makes Upptime provider agonistic. Upptime can then be run locally, or on any provider. It can also be configured to use custom packages. 

## Integration
<p align="right"><i>how the CLI should be integrated with the current project</i></p>

**1. What is done already, need not to be done again**
<br>
Support for other cloud-service providers, additional configurations and decision making logic could be added to Uptime-Monitor, but no major changes should be required in order to integrate Upptime CLI. It should not change the key mechanism of Uptime-Monitor.

**2. Repositories without workflow scopes should be supported**<br>
Some of the template users have not provided their repositories with ```workflows scope``` and hence their templates will not get updated. Device a mechanism either to have them notify of breaking changes or build the tool to support the present configs.

**3. What developers love about Upptime should be retained**<br>
Upptime is simple, elegant and customisable. Upptime-CLI's integration should not affect these. Good documentation, tutorials, blogs can 

## User Flows
<p align="right"><i>possibilities how user uses Upptime</i></p>

**1. User uses a single provider** <br>
User uses a template to make their repository. Provider specific configurations are already set. User specifies the websites to be monitored. Provider's action/workflow service runs accordingly.

**2. User wants to have a fully local environment** <br>
User clones/downloads the template from any provider, initialises to have cli-configuration if not already present. Set configurations accordingly (may have a command which automates the fully local config). User runs the commands, which uses cron and reproduces what GitHub Actions do.

**3. User wants to use providers for status pages or issues and have their template locally** <br>
User clones/downloads the template from any provider, initialises to have cli-configuration if not already present. Set configurations accordingly (may have a command which automates the fully local config). User runs the commands, which uses cron and reproduces what GitHub Actions do.

In each of the above flow, User may use their own local/remote versions of  ```@uptime-monitor, @pages and @status```.

## CLI Standards
<p align="right"><i>standards that make UCLI the best</i></p>

Based on the cli design guidelines, [clig.dev](https://clig.dev)

1. **Hybrid Human-Machine First Design** <br>
    UCLI configurations will generally be set by humans. But, the primary function of the tool is to constantly run workflows which will generally be controlled by user defined scripts. Hence, a hybrid human-machine first design will be ideal.
    To achieve this, we can use flags, like ```--plain or --log```.
2.  **Composability** <br>
    UCLI could be composable with other UCLI commands and should be composable with other tools like ```awk```.
3.  **Smooth** <br>
    Animated spinners and widgets can be included in commands which takes time to execute.


## Implementation
<p align="right"><i>our plan to implement Upptime-CLI</i></p>


```
Upptime template --> Upptime CLI --> Uptime-Monitor --> Pages | Graphs
```
Introducing Upptime-CLI as a layer between Upptime and the Uptime-Montior. 
Upptime-CLI's primary task would be to reproduce what GitHub Actions do, provide configurable options which will be carried and used by Uptime-Monitor.

<!-- ### Supported Providers
// TODO
### Going Local
// TODO -->
### Command, SubCommands, Arguments, Flags

> Upptime-CLI will be referred as **ucli** throughout

**help**
```
    $ ucli
    $ ucli [subcommand] --help
    $ ucli [subcommand] -h
```
Display help messages.
Help messages should contain link to web-based documentation, should be formatted if stdout is a tty.

**init**
```
    $ ucli init [--github][--gitlab][--local (default)]
```
Adds a new configuration file (uclirc.yaml).



_--local_<br>
Boilerplates local configuration

_--github | --gitlab | --other_<br>
Boilerplates provider specific configuration

**run**<br>
```
$ ucli run [--pages][--graphs][--update][--all (default)][--update-template] 
```
Reproduces what GitHub Actions currently do using ```cron```.

_--graphs | --pages | --update_<br>
Runs an iteration of a particular workflow.


## UCLI Config File
<p align="right"><i>structure of UCLI-config file</i></p>

```
# uclirc.yaml

packages:
    monitor: <custom_package | @upptime/uptime-monitor>
    pages: <custom_package | @upptime/pages>
    graphs: <custom_package | @upptime/graphs>

issues:
    provider: <github | gitlab | other |local>
    owner: <owner>
    repo: <repo>
    pat: <personal access token>

commits:
    limit: 1000

pages:
    provider: <github | gitlab | other |local>
    owner: <owner>
    repo: <repo>
    branch: <branch>
    pat: <personal access token>
```

## CLI Experience
<p align="right"><i>how the CLI would look like</i></p>

For a better preview of the CLI Experience, check out [this document](https://docs.google.com/document/d/1aGb69-9QPG3B2FRgkw8lCt8t5ASMFG26StQ1l5LEeKk/edit?usp=sharing).
```
 $ ucli

Usage: ucli [OPTIONS] [COMMAND]

 ⬆  Upptime-CLI is a command line tool that enables you to set-up, configure your settings and run workflows for Upptime locally.

Options:
-h, --help		            Displays help message
-v, --version	            Print version information and quit
    --verbose	            Enable verbose mode
			
Commands:
help			            Displays help message		
init			            Initialises the repository with uclirc.yaml
run				            Runs workflows
set				            Edit configuration
version			            Print version information and quit		

Run ‘ucli COMMAND --help’ for more information on a command.


$ ucli init -h

Usage: ucli init [OPTIONS]

Initialises the current directory with .uclirc.yaml

Options:
-h, --help		            Displays help message
    --local		            Sets Local configurations			
    --github	            Sets GitHub specific configurations
    --gitlab	            Sets GitLab specific configurations
-f, --force		            Force initialise a repository
-q, --quiet		            Force initialise 


$ ucli run -h

Usage: ucli init [OPTIONS]

Run workflows

Options:
    --graphs	[args]	    Runs [args]/an iteration of Graphs CI
    --response  [args]	    Runs [args]/an iteration of Response Time CI
    --setup	    [args]	    Runs [args]/an iteration of Setup CI
    --site	    [args]	    Runs [args]/an iteration of Static Site CI
    --summary	[args]	    Runs [args]/an iteration of Summary CI
    --template	[args]	    Runs [args]/an iteration of Update Template CI
    --update	[args]	    Runs [args]/an iteration of Update CI
    --uptime	[args]	    Runs [args]/an iteration of Uptime CI


$ ucli set -h

Usage: ucli set [COMMANDS] 

Set configuration variables

Commands:
pages				        Displays help message		
commits				        Initialises the repository with uclirc.yaml
issues				        Runs workflows
packages				    Edit configuration		


$ ucli version
 ⬆  Upptime-CLI v0.1


```


<!-- ## Revisting the User Flows
// TODO -->