# Upptime CLI

| Status      | Date       | 
|-------------|------------|
| **Created** | 2021-07-21 |


# CLI Commands Design


## init

First, the user initialises with their repository link

```
$ upptime init [--github]
```

Then, user configures the *.upptimer.yml* file.
We can use a promptr to get the configuration details

```
$ upptime configure
```
after running this command, a prompt will appear asking for sites names and url, etc.

Sample CLI *prompter* for the same: <br/>
<img width="463" alt="Screenshot 2021-07-27 at 18 50 11" src="https://user-images.githubusercontent.com/66865329/127162033-2de69567-d746-4a12-af65-a8f883ba1372.png">


Sample *prompter* code for the same:
```
 async run() {
    // just prompt for input
    const name = await cli.prompt("Site name:");

    const url = await cli.prompt("Site url:");
  }
  
```

We can decide to have the .upptimer.yml configurations on init or after using `configure` command or both can be done.




## help

Help command to show list of all commands and their description.

```
$ upptime --help
```


## status

To get Live Status of the websites.

```
$ upptime status
```
On running this command, user should be given options to select the website they want to see the status of (incase the user has configured more than 1 website)
**Output**

For the output of above command after selecting a website i.e `upptime status`,

```
URL: Google
Status: 🟩 Up	
Response Time: 79ms
Uptime: 100.00%

```

For this we can use **Inquirer** package.

Sample CLI *inquirer* for the same: <br/>

<img width="278" alt="Screenshot 2021-07-27 at 19 11 50" src="https://user-images.githubusercontent.com/66865329/127165747-bae4e223-3de8-4cdc-bf27-0895322efcec.png">
<br/>
<img width="278" alt="Screenshot 2021-07-27 at 19 12 15" src="https://user-images.githubusercontent.com/66865329/127165761-1f4fc801-2714-4366-8be7-fefc0b79cddf.png">


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

In case, we want to show status of all the websites at once, we can make use of tables in the output format.
For, tables we can make of use `cli-ux` package that includes various uis such as tables and spinners for cli tools.

Sample _Table_ format using 'cli-ux':

```
cli.table(data, columns, rows)
```
<img width="238" alt="Screenshot 2021-07-27 at 17 54 08" src="https://user-images.githubusercontent.com/66865329/127154654-01c242d2-1419-4558-9e08-e6e9754c4af1.png">


## incidents

**Incidents Notification** - To notify all the active and past incidents.

We can make use of `node-notifier` to notify through the terminal about any incidents.

Sample code how we can trigger a notification:
```
 notifier.notify({
      title: "Google is Down",
      message: "at 16/07/2021, 20:26:57",
    });
    
 ```

**Incidents command** - To list all the active and past incidents.

```
USAGE
  $ upptime incidents

OPTIONS
  -p, --past=past            past incidents
  -a, --active=active        active incidents
  -s, --scheduled=scheduled  past scheduled maintainance

```

```
 $ upptime incidents -p
```

**output**
```
Incident             Reported      Resolved in
Wikipedia is down    28/04/2021    36 minutes
Hacker News is down  08/04/2021    38 minutes

```

Further, we can give users the option to select a particular incident using inquirer and see more details about that incident.


```
Wikipedia is down - Fixed/Ongoing

            Date          Time
Opened at   16/07/2021    20:26:57
Closed at   16/07/2021    21:03:11

```


- To beautify the outputs we can make use of `chalk` package and make things like *up* as green and *down* as red, etc.



---


 I think using Oclif framework will be a good choice because of its various features like 
 - easily generating new commands
 - automatic command documentation
 - hooks feature 
 - flag/argument parsing
 - Oclif's ability to build fully configured TypeScript CLIs


<blockquote>
I have been trying Oclif to and it seemed pretty good.
</blockquote>

Sample repo:- ![here](https://github.com/sakshi-choudhary/oclif-cli)
