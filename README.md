- [Github-Actions](#github-actions)
  * [GH Actions - Runners](#gh-actions---runners)
  * [GH Actions - crontab details](#gh-actions---crontab-details)
    + [GH Actions - crontab guru](#gh-actions---crontab-guru)
  * [GH Actions - Variables](#gh-actions---variables)
    + [GH Actions - Workflow Variables](#gh-actions---workflow-variables)
    + [GH Actions - Variables - Jobs and Steps](#gh-actions---variables---jobs-and-steps)
    + [GH Actions - Variables - Overwrite](#gh-actions---variables---overwrite)
    + [GH Actions - Default Environment Variables](#gh-actions---default-variables)
  * [GH Actions - workflow dispatch](#gh-actions---workflow-dispatch)
    + [GH Actions - Input](#gh-actions---input)
  * [GH Actions - Examples](#gh-actions---examples)
    + [GH Actions - Run multi lines scripts](#gh-actions---run-multi-lines-scripts)
    + [GH Actions - Jobs at every 5 minutes](#gh-actions---jobs-at-every-5-minutes)
    + [GH Actions - Jobs at every 15 minutes](#gh-actions---jobs-at-every-15-minutes)
    + [GH Actions - Jobs based on directory changes](#gh-actions---jobs-based-on-directory-changes)
    + [GH Actions - Run the job once a week](#gh-actions---run-the-job-once-a-week)
    + [GH Actions - Depending from another Job](#gh-actions---depending-from-another-job)
    + [GH Actions - if](#gh-actions---if)
    + [GH Actions - Secrets](#gh-actions---secrets)
    + [GH Actions - GITHUB_TOKEN](#gh-actions---GITHUB-TOKEN)
    + [GH Actions - Artifacts: Store](#gh-actions---Artifacts-store)
    + [GH Actions - Artifacts: Passing stored artifcats to another job in the workflow](#gh-actions---Artifacts-passed-to-another-job-in-the-workflow)  
    + [GH Actions - Cache](#gh-actions---Cache) 
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

### GH Actions - Default Environment Variables
| ENV Variable       | Description                                                                                                                                                                                                                                                                                |
|--------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| CI                 | Always set to true                                                                                                                                                                                                                                                                         |
| GITHUB_WORKFLOW    | The name of the workflow                                                                                                                                                                                                                                                                   |
| GITHUB_RUN_ID      | A unique number for each run within a repository. <br>This number does not change if you re-run the workflow run                                                                                                                                                                           |
| GITHUB_RUN_NUMBER  | A unique number for each run of a particular workflow in a repository.<br> This number begins at 1 for the workflow's first run, and increments<br>with each new run. This number does not change if you re-run the workflow run.                                                          |
| GITHUB_ACTION      | The unique identifier (id) of the action.                                                                                                                                                                                                                                                  |
| GITHUB_ACTIONS     | Always set to true when GitHub Actions is running the workflow.<br>You can use this variable to differentiate when tests are being<br>run locally or by GitHub Actions.                                                                                                                    |
| GITHUB_ACTOR       | The name of the person or app that initiated the workflow.<br>For example, octocat.                                                                                                                                                                                                        |
| GITHUB_REPOSITORY  | The owner and repository name. For example, octocat/Hello-World.                                                                                                                                                                                                                           |
| GITHUB_EVENT_NAME  | The name of the webhook event that triggered the workflow.                                                                                                                                                                                                                                 |
| GITHUB_EVENT_PATH  | The path of the file with the complete webhook event payload.<br>For example, /github/workflow/event.json.                                                                                                                                                                                 |
| GITHUB_WORKSPACE   | The GitHub workspace directory path. The workspace directory is a copy of your<br>repository if your workflow uses the actions/checkout action. If you don't use<br>the actions/checkout action, the directory will be empty.<br>For example, /home/runner/work/my-repo-name/my-repo-name. |
| GITHUB_SHA         | The commit SHA that triggered the workflow.<br>For example, ffac537e6cbbf934b08745a378932722df287a53                                                                                                                                                                                       |
| GITHUB_REF         | The branch or tag ref that triggered the workflow.<br>For example, refs/heads/feature-branch-1. If neither a branch or tag is available<br>for the event type, the variable will not exist                                                                                                 |
| GITHUB_HEAD_REF    | Only set for pull request events. The name of the head branch.                                                                                                                                                                                                                             |
| GITHUB_BASE_REF    | Only set for pull request events. The name of the base branch.                                                                                                                                                                                                                             |
| GITHUB_SERVER_URL  | Returns the URL of the GitHub server. For example: https://github.com.                                                                                                                                                                                                                     |
| GITHUB_API_URL     | Returns the API URL. For example: https://api.github.com.                                                                                                                                                                                                                                  |
| GITHUB_GRAPHQL_URL | Returns the GraphQL API URL. For example: https://api.github.com/graphql.                                                                                                                                                                                                                  |

Note: If you need to use a workflow run's URL from within a job, you can combine these environment variables: $GITHUB_SERVER_URL/$GITHUB_REPOSITORY/actions/runs/$GITHUB_RUN_ID

Based on this doc: [Docs GithHub Actions - Default Environment Variables](https://docs.github.com/en/actions/reference/environment-variables) 

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
### GH Actions - if
```
name: CI
on: push
jobs:
  prod-check:
    if: ${{ github.ref == 'refs/heads/main' }}
    runs-on: ubuntu-latest
    steps:
      - run: echo "Deploying to production server on branch $GITHUB_REF"
```
In this example, the if statement checks the github.ref context to determine the current branch name;
if the name is refs/heads/main, then the subsequent steps are executed. The if check is processed by
GitHub Actions, and the job is only sent to the runner if the result is true. Once the job is sent to
the runner, the step is executed and refers to the $GITHUB_REF environment variable from the runner.


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

### GH Actions - Secrets
Secrets is a good way to hide passwords in the CI/CD jobs, specially in the logs. Users will note that even doing `echo ${{secrets.MYSECRET}}` the real
password won't show. It will show `***` instead.

To create: 
First, [create a secret in the project](https://docs.github.com/en/actions/reference/encrypted-secrets#creating-encrypted-secrets-for-a-repository) 
Second, use in your yaml file.
```
name: CI
on:
  workflow_dispatch:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]
env:
  supervar: ${{secrets.MY_CREATED_SECRET_NAME}

jobs:
  branch:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2

      - name: Pass secret to mycommand
        run: |
          my_command "${{ env.supervar }}"
```

### GH Actions - GITHUB TOKEN
**Example 1**:  

Passing GITHUB_TOKEN as an input
This example workflow uses the [labeler action](https://github.com/actions/labeler) in PR, which requires the GITHUB_TOKEN as the value for the repo-token input parameter:
```
name: Pull request labeler
on:
- pull_request_target
jobs:
  triage:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/labeler@v2
      with:
        repo-token: ${{ secrets.GITHUB_TOKEN }}
```

**Example 2**:  

You can use the GITHUB_TOKEN to make authenticated API calls. This example workflow creates an issue using the GitHub REST API:
```
name: Create issue on commit
on:
- push
jobs:
  create_commit:
    runs-on: ubuntu-latest
    steps:
    - name: Create issue using REST API
      run: |
        curl --request POST \
        --url https://api.github.com/repos/${{ github.repository }}/issues \
        --header 'authorization: Bearer ${{ secrets.GITHUB_TOKEN }}' \
        --header 'content-type: application/json' \
        --data '{
          "title": "Automated issue for commit: ${{ github.sha }}",
          "body": "This issue was automatically created by the GitHub Action workflow **${{ github.workflow }}**. \n\n The commit hash was: _${{ github.sha }}_."
          }' \
        --fail
 ```

### GH Actions - Artifacts store
To store the artifacts users must use **actions/upload-artifact@v2**

```
name: "Antrea v0.12.2 job to generate cyclonus artifacts"
on:
  workflow_dispatch:
  schedule:
    # Daily, 3:00pm
    - cron: '00 15 * * *'

jobs:
  antrea-job-v0-12-2-artifacts:
    runs-on: [ ubuntu-latest ]
    steps:
      - uses: actions/checkout@v2
        with:
           path: main

      - uses: actions/checkout@master
        with:
          repository: K8sbykeshed/k8s-local-dev
          path: k8s-local-dev

      - run: |
          pushd ./k8s-local-dev
            ANTREA_VERSION=v0.12.2 ./k8s-local-dev antrea
          popd

          pushd ./main
            JOB_YAML="./jobs/antrea/cyclonus-job.yaml" DIR_CNI="antrea" ./run-cyclonus-job.sh
          popd

      - uses: actions/upload-artifact@v2
        with:
          name: logs
          path: ./main/downloads/
          retention-days: 60
```
### GH Actions - Artifacts passed to another job in the workflow
```
on: [push, pull_request]
jobs:
  FoobarStep
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v1
    - name: FoobarStep
      run: |
        cd /path
        build-my-app
    
    - uses: actions/upload-artifact@v2
      with:
        name: my-artifact
        path: "**/bin/myapp-path/"

  NextStep:
    runs-on: ubuntu-latest
    need: FoobarStep
    steps:
    - uses: actions/download-artifact@v2
      with:
        name: my-artifact
```

See also: https://docs.github.com/en/actions/guides/storing-workflow-data-as-artifacts

### GH Actions - Cache
```
- name: Cache multiple paths
  uses: actions/cache@v2
  with:
    path: |
      ~/cache
      !~/cache/exclude
    key: ${{ runner.os }}-${{ hashFiles('**/lockfiles') }}
 ```
More info: https://github.com/actions/cache

## GH Actions - Community
Users can exchange knowledge via [the community around github actions.](https://github.community/c/code-to-cloud/github-actions/)

## GH Actions - Docs and Books
[Docs GitHub Actions - Default Environment Variables](https://docs.github.com/en/actions/reference/environment-variables)  
[Docs GitHub Actions - Create secret in the project](https://docs.github.com/en/actions/reference/encrypted-secrets#creating-encrypted-secrets-for-a-repository)  
[Docs Github Actions - GITHUB_TOKEN](https://docs.github.com/en/actions/reference/authentication-in-a-workflow)  
[Docs Github Actions - Storing Workflow data as Artifacts](https://docs.github.com/en/actions/guides/storing-workflow-data-as-artifacts)  
[Github Actions Cache](https://github.com/actions/cache)  
[Github Actins Runners on local Kubernetes](https://sanderknape.com/2020/03/self-hosted-github-actions-runner-kubernetes/)  
[Hands-on GitHub Actions](https://read.amazon.com/kp/embed?asin=B08X675RHC&preview=newtab&linkCode=kpe&ref_=cm_sw_r_kb_dp_TT09RHFEAH2FGKVQ96QF&tag=dougsland)
