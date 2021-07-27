
# Commands for Upptime-CLI
| Status      | Proposed   | 
|-------------|------------|
| **Created** | 2021-07-19 |

## Commands

**1. Help**
Help command will provide users the path to explore and configure upptime by providing all the commands and functionalities.
```
$ upp -h 
$ upp --help
```
output:
```
usage: upp [--version] [--help]

These are common Upptime commands used in various situations:
  run         will run the workflows, update the status of the websites.
  incidents   report the user about all the active and past Incidents.
  status      provide the current status of the websites
  config      provide option to the user to create and update configurations.
  init        initiates a configuration file with default values.
  subs        
  add         adds websites in the list.
```
**2. run**
Run command will run the workflows, and update the status of the websites.
``` 
$ upp --run
$ upp --run --pages
```
output:
```
Live status:
Site:          overall uptime:	  response time:  
Google.com     100%               149ms
HackerNews     88%                339ms
Wikipedia      96%                329ms
```
**3. Incidents**
Incidents command will report the user about all the active and past Incidents, with a link to the issue. 
``` 
$ upp --incidents
$ upp -i [--all (default)]
$ upp -i --past
$ upp -i --active
```
output:
```
Active Incidents:
Incident:              reported:
test.com is down       timestamp

Past Incidents:
Incident:              resolved in:
test.com is down       time
```
**4. status**
Status command should provide the current status.
`Note: Status command will be different than run command because it will provide the last stored status, contrary to run which run the workflows all together from the beginning. `

Command:
``` 
$ upp --status
```
output:
```
Live status:
Site:          overall uptime:	  response time:  
Google.com     100%               149ms
HackerNews     88%                339ms
Wikipedia      96%                329ms
```
**5. config**
Config command would work like `upptimerc.yml` . It will provide option to the user to create and update configurations.

It will also allow users to add a `--website` flag to change configurations for status-website.
``` 
$ upp --config
$ upp --config --website
```
**6. subscribers**
This command will show the subscription list and let's you add mails to the list.
``` 
$ upp --subs
$ upp --subs --add "test@test.com"
$ upp --subs --mail --add
```
output:
```
mail list:
test@gmail.com
test1@test.com
test2@g.com

add email: test@mail123.com
```
**7. Add website**
This commands adds websites in the list.
``` 
$ upp --add
```
output:
```
name: excal
url: excalidraw.com
```

## Implementation

We need to add a new abstraction layer between upptime services and the template repo.
// image
We can use several tools to make a CLI: 
-  OCLIF (Node.JS)
-  React
-  Typescript (vanilla)
-  Go Binary
------
* [OCLIF](https://github.com/oclif/oclif)
The hooks feature and packages make OCLIF a good choice. 

### Nodemailer
For mailing service we can use [Nodemailer](https://nodemailer.com/about/)
we can add nodemailer as a dependency.
- we can let users provide custom messages for emails. 

----------------------------
continue...............