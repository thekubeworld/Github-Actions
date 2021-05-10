Info and Examples about Github Actions

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
    + [GH Actions - Service Container](#gh-actions---Service-Container)
    + [GH Actions - Creating an Action based on Docker Image](#gh-actions---Creating-an-Action-based-on-Docker-Image)
    + [GH Actions - Mirroring Github Repos](#gh-actions---Mirroring-Github-repos)
    + [GH Actions - Golint](#gh-actions---Golint)
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

### GH Actions - Service Container
Service containers are Docker containers that provide a simple and portable way for you to host services that you might need to test or operate your application in a workflow. For example, your workflow might need to run integration tests that require access to a database and memory cache.  
You can configure service containers for each job in a workflow. GitHub creates a fresh Docker container for each service configured in the workflow, and destroys the service container when the job completes. Steps in a job can communicate with all service containers that are part of the same job.  

For more info see:  
[Github Actions - Service Container](https://docs.github.com/en/actions/guides/about-service-containers)  
[Github Actions - Service Container - PostgreSQL](https://docs.github.com/en/actions/guides/creating-postgresql-service-containers)  
[Github Actions - Service Container - Redis](https://docs.github.com/en/actions/guides/creating-redis-service-containers)  

### GH Actions - Creating an Action based on Docker image
See: [Github Actions - Creating a Docker Action](https://docs.github.com/en/actions/creating-actions/creating-a-docker-container-action)  

### GH Actions - Mirroring Github Repos
Instruction how to mirror github projects via Github Actions with [Git Sync Action](https://github.com/marketplace/actions/git-sync-action)

1) Generate the ssh key (private/pub).  
The command below will generate two files in **~/.ssh dir**  
- mirroring_SSH_KEY (private key)
- mirroring_SSH_KEY.pub (public key)

```
$ ssh-keygen -t rsa -b 4096 -C "dougsland@gmail.com"  -f ~/.ssh/mirroring_SSH_KEY
```

2) At the **Destination Project** on Github  
-> Settings -> Deploy Keys:  

**Title**: SSH_PRIVATE_KEY.pub  
**Key**: ```ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQDG3FdclV4JHvBED5YnxxTO0pdaEdUUaLagvG8ezbBYKUADAiOpmapa2P5SklQJGzKcJpu74JpvEuP52WjYwYYITM/5GQmWhFxLb73/foP5DQWrhak4fzWf3dQeAF+Gg68omUaa83noLaxQEIkgKaeKm3uimxsxE1f8n+X5lU4Yu/F9FYpOI9W4yd+KcVBsWboP1NeiUDnTR0piTmywYyRqMAGfdb1315xGgh+6sdS7snPc4J+Ff8+QDiaQUMOsBffTM3B1Fi+FEnu30951rCiU0K7MxxNPvih5mpnC+EJRJkJ7mhYK3BV0Sgu6a87ym2IcakYstAVHAeyFrbNe/Rb8jCAO+lmo1gks7ykuaJurCTEgUXrwvbYXDz09M9nR5POpksuDe3BUOSoEzReaigD5HvAsc/uxwfSYzg6E8fiIzL2UXHP6O4egdUd7NdkTwpX0AkvmA4TBpVk9GN2+WXe3EVw== dougsland@gmail.com```  

**[x] Allow write access** -> **Click**: Add Secret

3) At the **Source Project** on Github  
-> Settings -> Secrets -> New repository secret  

**Title**: SSH_PRIVATE_KEY  
**Key**: ```-----BEGIN OPENSSH PRIVATE KEY-----  
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   
-----END OPENSSH PRIVATE KEY-----```

**Click**: Add Secret


4) On the Source github tree (the one will contain the action):
```
$ git-source-project> mkdir -p .github/workflows 
$ cd .github/workflows
$ vi syncrepo.yml
```

```
name: Mirror
on:
  workflow_dispatch:
  schedule:
    - cron:  '*/15 * * * *'    # At every 15 minutes

jobs:
  to_mirror:
    runs-on: ubuntu-latest
    steps:
      - name: sycnmain
        uses: wei/git-sync@v3
        with:
          source_repo: "git@github.com:SOURCEUSER/SOURCEREPO.git"
          source_branch: "main"
          destination_repo: "git@github.com:DESTINATIONUSER/DESTINATIONREPO.git"
          destination_branch: "main"
          ssh_private_key: ${{ secrets.SSH_PRIVATE_KEY }} 
      - name: synctags
        uses: wei/git-sync@v3
        with:
          source_repo: "git@github.com:thekubeworld/k8s-local-dev.git"
          source_branch: "refs/tags/*"
          destination_repo: "git@github.com:K8sbykeshed/k8s-local-dev.git"
          destination_branch: "refs/tags/*"
          ssh_private_key: ${{ secrets.SSH_PRIVATE_KEY }}
```
          
5) On the Github url from the **Source Project**, click in:
     - the tab **Actions**
     - Mirror (the new job)  -> Run workflow -> Branch: main -> Run workflow

6) As it has mirrored the source repo to the destination, **now disable the Github Actions in the Destination repo only**,  
otherwise, it will keep failing and sending emails to you

### GH Actions - Golint
https://github.com/golangci/golangci-lint-action

## GH Actions - Community
Users can exchange knowledge via [the community around github actions.](https://github.community/c/code-to-cloud/github-actions/)

## GH Actions - Docs and Books
[Docs GitHub Actions - Default Environment Variables](https://docs.github.com/en/actions/reference/environment-variables)  
[Docs GitHub Actions - Create secret in the project](https://docs.github.com/en/actions/reference/encrypted-secrets#creating-encrypted-secrets-for-a-repository)  
[Docs Github Actions - GITHUB_TOKEN](https://docs.github.com/en/actions/reference/authentication-in-a-workflow)  
[Docs Github Actions - Storing Workflow data as Artifacts](https://docs.github.com/en/actions/guides/storing-workflow-data-as-artifacts)  
[Github Actions Cache](https://github.com/actions/cache)  
[Github Actins Runners on local Kubernetes](https://sanderknape.com/2020/03/self-hosted-github-actions-runner-kubernetes/)  
[Github Actions - Service Container](https://docs.github.com/en/actions/guides/about-service-containers)  
[Github Actions - Service Container - PostgreSQL](https://docs.github.com/en/actions/guides/creating-postgresql-service-containers)  
[Github Actions - Service Container - Redis](https://docs.github.com/en/actions/guides/creating-redis-service-containers)  
[Github Actions - Creating a Docker Action](https://docs.github.com/en/actions/creating-actions/creating-a-docker-container-action)  
[Hands-on GitHub Actions](https://read.amazon.com/kp/embed?asin=B08X675RHC&preview=newtab&linkCode=kpe&ref_=cm_sw_r_kb_dp_TT09RHFEAH2FGKVQ96QF&tag=dougsland)
