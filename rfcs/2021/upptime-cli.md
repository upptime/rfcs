# Upptime-CLI

| Status      | Proposed   | 
|-------------|------------|
| **RFC#**    |            |
| **Updated** | 2021-07-19 |
| **Created** | 2021-07-15 |

## Contents
1. [Goal](#goal)
2. [User Flows](#user-flows) 
3. [Possible Solutions](#possible-solutions)
4. [Implementation](#implementation)
    1. 
    2.
    3.
    4. 
5. [Objectives](#objectives-inferred)

## Goal
Design a CLI tool which makes Upptime provider agonistic. Upptime can then be run locally, or on any provider. It can also be configured to use custom packages. 


## User Flows
**1. User uses a single provider** <br>
User uses a template to make their repository. Provider specific configurations are already set. User specifies the websites to be monitored. Provider's action/workflow service runs accordingly.

> Some of the template users have not provided their repositories with ```workflows scope``` and hence their templates will not get updated. Device a mechanism either to have them notify of breaking changes or build the tool to support the present configs.

**2. User wants to have a fully local environment** <br>
User clones/downloads the template from any provider, initialises to have cli-configuration if not already present. Set configurations accordingly (may have a command which automates the fully local config). User runs the commands, which uses cron and reproduces what GitHub Actions do.

**3. User wants to use providers for status pages or issues and have their template locally**
User clones/downloads the template from any provider, initialises to have cli-configuration if not already present. Set configurations accordingly (may have a command which automates the fully local config). User runs the commands, which uses cron and reproduces what GitHub Actions do.

<br>

In each of the above scenario, User may use their own local/remote versions of  ```@uptime-monitor, @pages and @status```.

## Possible Solution
<!-- Insert Image(new-component-3x.svg) here -->

#### Features & Benefits

#### Challenges

## Integrations

## Implementation




