# Upptime CLI

| Status      | Date       | 
|-------------|------------|
| **Created** | 2021-07-21 |

## Aim

Design a CLI tool for Upptime

## CLI Commands Design

First, user initialises

**init**
```
$ upptime init [--github]
```

Then, user configures the *.upptimer.yml* file.
We can use a promptr to get the configuration details

```
$ upptime configure
```
after running this command, a prompt will appear asking for sites names and url, etc.

Sample oclif code for the same:
```
 async run() {
    // just prompt for input
    const name = await cli.prompt("Site name:");

    // mask input after enter is pressed
    const secondFactor = await cli.prompt("Site url:");
  }
  
```

We can decide to have the .upptimer.yml configurations on init or after using `configure` command or both can be done.
