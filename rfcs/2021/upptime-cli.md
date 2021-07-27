# Upptime CLI

| Status      | Date       | 
|-------------|------------|
| **Created** | 2021-07-21 |

## Aim

Design a CLI tool for Upptime

## CLI Commands Design

First, user initialises

### init
```
$ upptime init [--github]
```

Then, user configures the *.upptimer.yml* file.
We can use a promptr to get the configuration details

```
$ upptime configure
```
after running this command, a prompt will appear asking for sites names and url, etc.

Sample *prompter* code for the same:
```
 async run() {
    // just prompt for input
    const name = await cli.prompt("Site name:");

    // mask input after enter is pressed
    const secondFactor = await cli.prompt("Site url:");
  }
  
```

We can decide to have the .upptimer.yml configurations on init or after using `configure` command or both can be done.




### help

Help command to show list of all commands and their description.
```
$ upptime --help
```


### status

To get Live Status of the websites.
```
$ upptime status
```
On running this command, user should be given options to select the website they want to see the status of (incase the user has configured more than 1 website)

For this we can use **Inquirer** package.


Sample *inquirer* code for the same:
```
let responses = await inquirer.prompt([
        {
          name: "website",
          message: "select a website",
          type: "list",
          choices: [
            { name: "google.com" },
            { name: "yahoo.in" },
            { name: "wikipedia.com" },
          ],
        },
      ]);
      website = responses.wesbite;
  
```

For the output of above command after selecting a website i.e `upptime status`,

```
URL: Google
Status: 🟩 Up	
Response Time: 79ms
Uptime: 100.00%

```

To beautify the outputs we can make use of `chalk` package and make things like *up* as green and *down* as red, etc.

In case, we want to show status of all the websites at once, we can make use of tables in the output format.

---

<blockquote>I think using Oclif framework will be a good choice because of its various features like easily generating new commands.
</blockquote>

I have been trying Oclif to and it seemed pretty good. Sample repo:- ![here](https://github.com/sakshi-choudhary/oclif-cli)
