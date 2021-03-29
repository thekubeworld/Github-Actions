- [Github-Actions](#github-actions)
  * [GH Actions - Runners](#gh-actions---runners)
  * [GH Actions - crontab details](#gh-actions---crontab-details)
    + [GH Actions - crontab guru](#gh-actions---crontab-guru)
  * [GH Actions - Variables](#gh-actions---variables)
    + [GH Actions - Workflow Variables](#gh-actions---workflow-variables)
    + [GH Actions - Variables - Jobs and Steps](#gh-actions---variables---jobs-and-steps)
    + [GH Actions - Variables - Overwrite](#gh-actions---variables---overwrite)
  * [GH Actions - workflow dispatch](#gh-actions---workflow-dispatch)
    + [GH Actions - Input](#gh-actions---input)
  * [GH Actions - Examples](#gh-actions---examples)
    + [GH Actions - Run multi lines scripts](#gh-actions---run-multi-lines-scripts)
    + [GH Actions - Jobs at every 5 minutes](#gh-actions---jobs-at-every-5-minutes)
    + [GH Actions - Jobs at every 15 minutes](#gh-actions---jobs-at-every-15-minutes)
    + [GH Actions - Jobs based on directory changes](#gh-actions---jobs-based-on-directory-changes)
    + [GH Actions - Run the job once a week](#gh-actions---run-the-job-once-a-week)
    + [GH Actions - Depending from another Job](#gh-actions---depending-from-another-job)
  * [GH Actions - Community](#gh-actions---community)
  * [GH Actions - Docs and Books](#gh-actions---docs-and-books)

# Github-Actions
Actions based on events, like push, pull or any other.

Workflow -> Jobs -> steps (test code, etc) -> Virtual Env -> Runners

## GH Actions - Runners
Runners is the machine that runs the job on top of Github Actions runne and show back the results, can be local (self-host runners)
or the machine inside github lab (Linux, Windows or MacOS). Maintenaned by Github, cannot be customized the hardware.

Required tools for sef-hosted runners:
```$ sudo dnf install curl, git, npm, yarn and pip -y```

## GH Actions - crontab details
Let's assume users want to execute a job **every sunday at 00:00am**.
 ```
 #Every Sunday at 00:00am
    - cron: '0 0 * * 0'
 ```

The format follows:
```
         0      0    *   *    0
second minutes hour day month day-of-week
  |      |       |   |   |    |____________________________ day of week - 0-6 | 
  |      |       |   |   |                                  0 (Sunday), 1 (Monday),
  |      |       |   |   |                                  2 (Tue), 3 (Wed), 
  |      |       |   |   |                                  4 (Thu), 5 (Fri), 6 (Sat)
  |      |       |   |   |
  |      |       |   |   |___________________________ month (1-12)
  |      |       |   |_________________________ day of month (1-31)
  |      |       |______________________ hour (0-23)
  |      |______________ minutes (0-59)
  |__________ seconds (0 - 59)
```

### GH Actions - crontab guru
The website https://crontab.guru is a good resource for creating new crontab jobs schema.

## GH Actions - Variables
### GH Actions - Workflow Variables
```
name: 'Variables Example'
# Every push, pull_request (all branches)
on: [push, pull_request]

jobs:
  weekday_job:
    runs-on: ubuntu-latest
    env:
      DAY_OF_WEEK: Mon
      FIRST_NAME: DOUGLAS
    steps:
      - name: "Advise when it's Monday"
        if: env.DAY_OF_WEEK == 'Mon'
        run: echo "Hello $FIRST_NAME, today is Monday!"
```
### GH Actions - Variables - Jobs and Steps
Users can define local variables in **jobs** or **steps**
Example:
```
on:
  env:
    username: DOUGLAS
    demo: APPLICATION
jobs:
   VariablesExample:
       runs-on: ubuntu-latest
       env:
         job_var1: "variable JOB 12345"
       steps:
       - name: "Using workflow variables"
         run: echo Hello, $username!
              Welcome to $demo
              job variable $job_var1
              step variable: $step_var1
         env:
           step_var1: "variable step 678910"
```
### GH Actions - Variables - Overwrite
This job will overwrite the value of variable **supervar** from DOUGLAS to Landgraf
```
name: CI
on:
  workflow_dispatch:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]
env:
  supervar: DOUGLAS

jobs:
  branch:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2

      - name: lastname
        run: echo "supervar=Landgraf" >> $GITHUB_ENV

      - name: Read exported variable
        run: |
          echo "$supervar"
          echo "${{ env.supervar }}"
```

## GH Actions - workflow dispatch
You will see a ‘Run workflow’ button on the Actions tab, to easily trigger a run.
```
name: "CNI daily test"
on:
  workflow_dispatch:
  schedule:
    # Daily, 3:25pm UTC
    - cron: '25 15 * * *'
```


### GH Actions - Input
Users can manually trigger job via **workflow_dispatch**.
If required users can define inputs (variables) to change during the manual triggers.
```
on:
  workflow_dispatch:
    inputs:
      environment:
        description: 'Define env name'
        required: true
        default: 'myenv'
      branch:
        description: 'Define branch name'
        required: true
        default: 'main'

jobs:
  printInputs:
    runs-on: ubuntu-latest
    steps:
    - run: |
        echo "Environment: ${{ github.event.inputs.environment }}"
        echo "Branch: ${{ github.event.inputs.branch }}"
```

## GH Actions - Examples
### GH Actions - Run multi lines scripts
```
Runs a set of commands using the runners shell
- name: Run a multi-line script
  run: |
    echo Add other actions to build,
    echo test, and deploy your project.
```

### GH Actions - Jobs at every 5 minutes 
Job every 5 minutes
```
name: "Antrea Cronjob"
on:
  schedule:
    - cron:  "*/5 * * * *"
jobs:
  antrea-job:
    runs-on: [ ubuntu-latest ]
    steps:
    - uses: actions/checkout@v2
    - name: Run
      working-directory: ./antrea
      run: ./setup.sh
```

### GH Actions - Jobs at every 15 minutes
```
on:
  schedule:
    - cron:  '*/15 * * * *'    # At every 15 minutes
```


### GH Actions - Jobs based on directory changes
```
name: "Calico test"
# If any file change in the Calico path a new build will be triggered
on:
  push:
    branches: [ main ]
    paths:
      - calico/**

jobs:
  calico-job:
    runs-on: [ ubuntu-latest ]
    steps:
    - uses: actions/checkout@v2
    - name: Run
      working-directory: ./calico
      run: ./setup.sh
```

### GH Actions - Run the job once a week
Every Sunday at 00:00am
```
on:
  schedule:
    # Every Sunday at 00:00am
    - cron: '0 0 * * 0'
```

Every Monday at 1:05am
```
on:
  schedule:
    # Every Monday at 1:05am
    - cron: '5 1 * * 1'
```   

### GH Actions - Depending from another Job
Below example: the job build depends on setup.  
The statement is: **needs: setup**. 

```
name: CI

on: [push]

jobs:
  setup:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v1
      - run: ./setup_test_infrastructure.sh
  build:
    needs: setup
    runs-on: 'ubuntu-latest'
    steps:
      - uses: actions/checkout@v1
      - run: |
          ./build.sh
          ./test.sh
```

## GH Actions - Community
Users can exchange knowledge via [the community around github actions.](https://github.community/c/code-to-cloud/github-actions/)

## GH Actions - Docs and Books
[Docs GithHub Actions - Default Environment Variables](https://docs.github.com/en/actions/reference/environment-variables)
[Hands-on GitHub Actions ](https://read.amazon.com/kp/embed?asin=B08X675RHC&preview=newtab&linkCode=kpe&ref_=cm_sw_r_kb_dp_TT09RHFEAH2FGKVQ96QF&tag=dougsland)
