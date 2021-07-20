# Upptime-CLI

| Status      | Proposed   | 
|-------------|------------|
| **RFC#**    |            |
| **Updated** | 2021-07-20 |
| **Created** | 2021-07-15 |

## Contents


_Setting the standards, objectives_<br>
1. [Goal](#goal)
2. [Integration](#integration) 
3. [User Flows](#user-flows)<hr>

_Designing the CLI tool as per the above defined standards, objectives_<br>

4. [Implementation](#implementation)
    1. Supported Service Providers
    2. Going Local
    3. [Command, SubCommands, Arguments, Flags](#command,-subcommands,-arguments,-flags) 
 
5. [Revisting the User Flows](#revisiting-the-user-flows)<hr>

## Goal
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
**1. User uses a single provider** <br>
User uses a template to make their repository. Provider specific configurations are already set. User specifies the websites to be monitored. Provider's action/workflow service runs accordingly.

**2. User wants to have a fully local environment** <br>
User clones/downloads the template from any provider, initialises to have cli-configuration if not already present. Set configurations accordingly (may have a command which automates the fully local config). User runs the commands, which uses cron and reproduces what GitHub Actions do.

**3. User wants to use providers for status pages or issues and have their template locally** <br>
User clones/downloads the template from any provider, initialises to have cli-configuration if not already present. Set configurations accordingly (may have a command which automates the fully local config). User runs the commands, which uses cron and reproduces what GitHub Actions do.

<br>

In each of the above flow, User may use their own local/remote versions of  ```@uptime-monitor, @pages and @status```.

## Implementation

```
Upptime template --> Upptime CLI --> Uptime-Monitor --> Pages | Graphs
```
Introducing Upptime-CLI as a layer between Upptime and the Uptime-Montior. 
Upptime-CLI's primary task would be to reproduce what GitHub Actions do, provide configurable options which will be carried and used by Uptime-Monitor.

### Supported Providers
// TODO
### Going Local
// TODO
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

## Revisting the User Flows
// TODO