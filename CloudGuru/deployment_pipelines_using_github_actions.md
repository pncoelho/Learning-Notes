# Deployment Pipeline using GitHub Actions

## What is GitHub Actions?

### Why use GitHub Action?

![dpuga_dev_workflow](./media/dpuga_dev_workflow.png)

To **automate** the **developer workflow** in order to focus more time on development.

This course will focus more on the **Build**, **Release** and **Deploy** stages of the *developer workflow*.

### CI Tools Available on GitHub

- **Multiple CI/Build Servers**:
  - These are called **Runners** in GutHub terms;
  - **Linux**, **MacOS** and **Windows** operating systems **supported**;
  - **Multiple execution strategies** available;
  - Get **live logs** as the build is running;
- **Test Multiple Versions**;
- **Trigger on any GitHub Event**:
  - Push, Pull-Request, ...;

The `.github/workflows/` path is the **default location** to place the **GitHub workflows**.



## Creating Your First GitHub Action

First thing to do was to spin up a **large** `CentOS w/ code-server` on the *ACG Cloud Servers* from the *Cloud Playground*. This server will be used as the **development server**.

The **VS Code Server** can be accessed by opening the url `https://$server_public_ip:8080`. The default password is: `CHANGE_THIS_PASSWORD`.

Once the server is up and running, do an *ssh* connection to it to install **NodeJS**.

```bash
# add the NodeSource yum repository to your system:
curl -sL https://rpm.nodesource.com/setup_14.x | sudo bash -

# install node.js 14.x and npm
sudo yum install -y nodejs

# check version of node and npm
node --version
npm --version
```

Once node is installed, setup a new package.

```bash
# Create a Node directory and an application directory
mkdir ~/node
mkdir ~/node/simplenodeapp

# Create the new Node project for simplenodeapp using the default parameters
cd ~/node/simplenodeapp
npm init
```

At this point, the project is created and you can open the folder `~/node/simplenodeapp` in the *Code-Server*.

To perform a simple test on the project, install **Mocha**.

```bash
cd ~/node/simplenodeapp
npm install mocha --save-dev
```

Now let's create the main **JavaScript** file:

```bash
cat << EOF | sudo tee ~/node/simplenodeapp/index.js
/*
 creates a server object in a variable named server
 takes in a request and response function
 outputs a response to the browser
 checks for the environment variable 'process.env.PORT`, or 5000 if not available
 then just output this string to the console
*/
const http = require('http');

const server = http.createServer((req, res) => {
    if (req.url === '/') {
        res.end('<h1>Hello, World</h1>');
    }
});

const PORT = process.env.PORT || 5000;

server.listen(PORT, () => console.log(`Server running on port ${PORT}`));

EOF
```

Let's also create a test file for using with *GitHub Action*:

```bash
cat << EOF | sudo tee ~/node/simplenodeapp/test.js
const assert = require('assert');
describe('Simple Math Test', () => {
 it('should return 2', () => {
        assert.equal(1 + 1, 2);
    });
 it('should return 9', () => {
        assert.equal(3 * 3, 9);
    });
});

EOF
```

With this done, let's **push** this information to the GitHub Repo so we can start using GitHub Actions:

```bash
# add gitignore
echo "node_modules" > .gitignore

# initialize git repo
git init

# add remote origin
# add the ssh version if you have the ssh keys already configured
git remote add origin https://github.com/<your github username>/<repo name>.git

# set global configs
git config --global user.email "email@example.com"
git config --global user.name "myusername"
git config --global init.defaultBranch main

# add tracked files
git add .

# commit changes
git commit -m "Initial commit"

# push to master
git push origin main
```

With the code now in GitHub we can start creating the Actions:

- Open the *Actions* tab in your repository;

- Select the **Node.js** *Workflow*;

- Leave everything as default and commit the new file;

- At this point, a new *Workflow* just started executing to build the Node code;



## Creating JavaScript GitHub Actions

### Lab Overview

1. **Two Types of Actions**:

  - JavaScript actions;

  - Container actions;

2. **Learn about Creating Action YAML files**;

3. **Create a Custom JS Action YAML**;



#### JavaScript vs. Container Actions

The main differences between there are:

- **JavaScript**:

  - **Run directly** on the CI server/build agent/runner;

  - **External dependencies must be pre-installed**;

  - Support for TypeScript;

  - **All OS supported**;

- **Container**:

  - **Portable** and **flexible**;

  - **Consistent** and **reliable**;

  - **Customize Os and tools**;

  - **Slower** that JS actions;

  - **Linux only**;

### Lab Steps

#### Create the GitHub Action

First, fork the [JavaScript action template repo](https://github.com/linuxacademy/content-javascript-actions-app.git) to your own repository.

Afterwards, clone the repo and initialize the project using the package.json file:

```bash
# clone the repo
git clone https://github.com/$YOUR_GITHUB_ACCOUNT/content-javascript-actions-app.git ~/content-javascript-actions-app

# initialize the project
cd ~/content-javascript-actions-app
npm init -y
```

Then create the GitHub action file:

```yaml
# ~/content-javascript-actions-app/action.yaml
name: 'Hello World'
description: 'Greet someone and record the time'
inputs: # input parameters are stored as environment variables
  who-to-greet:  # id of input
    description: 'Who to greet'
    required: true
    default: 'World'
outputs:
  time: # id of output
    description: 'The time we greeted you'
runs:
  using: 'node12'
  main: 'index.js'
```

With action created, let's install the actions toolkit:

```bash
# the @actions/core package provides an interface to the commands in the workflow and helps with input and output variables
npm install @actions/core

# the @actions/github package returns the Octokit REST client and GitHub actions contexts
npm install @actions/github
```

Now that that is done, let's create the main JS file:

```javascript
/* 
 ~/content-javascript-actions-app/index.js

 prints hello $person in a debug message in the log
 the script gets the current time and sets it as an output variable for our action to use
 GitHub Actions provide context information about the webhook event, Git refs, workflow, action, and the person who triggered the workflow
*/
const core = require('@actions/core');
const github = require('@actions/github');

try {
  // `who-to-greet` input defined in action metadata file
  const nameToGreet = core.getInput('who-to-greet');
  console.log(`Hello ${nameToGreet}!`);
  const time = (new Date()).toTimeString();
  core.setOutput("time", time);
  // Get the JSON webhook payload for the event that triggered the workflow
  const payload = JSON.stringify(github.context.payload, undefined, 2)
  console.log(`The event payload: ${payload}`);
} catch (error) {
  core.setFailed(error.message);
}
```

With the code finished, let's send it to the repo and **create a tag** to mark the version of the action:

```bash
git add .
git commit -m "My first action"
git tag -a -m "My first action release" v1
git push --follow-tags
```
#### Create a Private Workflow

Inside the same repo, create a new workflow file and paste the following content:

```yaml
# ~/content-javascript-actions-app/.github/workflows/main.yaml
on: [push]

jobs:
  hello_world_job:
    runs-on: ubuntu-latest
    name: A job to say hello
    steps:
      # To use this repository's private action,
      # you must check out the repository
      - name: Checkout
        uses: actions/checkout@v3 # This action checks-out your repository under $GITHUB_WORKSPACE, so your workflow can access it.
      - name: Hello world action step
        uses: ./ # Uses an action in the root directory
        id: hello
        with:
          who-to-greet: 'Pedro Coelho'
      # Use the output from the `hello` step
      - name: Get the output time
        run: echo "The time was ${{ steps.hello.outputs.time }}"
```

Send this workflow to the repo and watch the action run.

```bash
git add .github/*
git commit -m "Added private workflow"
git push
```



## Creating Container GitHub Actions

### Lesson Overview

1. **Container Actions Overview**

  - What they are;

  - How are they used;

  - When to use them over JS actions;

2. **Build a Container Action**

### Container Actions

Uses a **container** to run the action, instead of the *Runner* server.

Particularities of using container actions:

- **Advantages**

  - They allow you to **package the environment** to make running the action easier and consistent;

  - They **allow for specifying a Linux distro** and including dependencies;

    - Git hub does specify that the **prefered Linux distro is Debian**

  - Good for **custom environment configurations**;

    - Since everything is specified inside the *Dockerfile*;

- **Constraints**
- They must be run by the **root container user**;
  
- GitHub recommends **not using the** `WORKDIR` **in the *Dockerfile***;
  
- If an entrypoint is specified in the `action.yml` file, **it will override the** `ENTRYPOINT` **in the Dockerfile**;
  
- The args in `action.yml` file, **will override** `CMD` **in the Dockerfile**;

### Lab

#### Create the Action and the Container

Start by forking a [template container action repo](https://github.com/linuxacademy/content-container-actions-app).

Once the repo has been cloned to the server, start by creating the *Dockerfile*:

```bash
# ~/content-container-actions-app/Dockerfile
# Container image that runs your code
FROM alpine:3.10

# Copies your code file from your action repository to the filesystem path `/` of the container
COPY entrypoint.sh /entrypoint.sh

# Code file to execute when the docker container starts up (`entrypoint.sh`)
ENTRYPOINT ["/entrypoint.sh"]
```

Create the action file to run the Docker image:

```yaml
# ~/content-container-actions-app/action.yaml
name: 'Hello World'
description: 'Greet someone and record the time'
inputs:
  who-to-greet:  # id of input
    description: 'Who to greet'
    required: true
    default: 'World'
outputs:
  time: # id of output
    description: 'The time we greeted you'
runs:
  using: 'docker'
  image: 'Dockerfile'
  args:
    - ${{ inputs.who-to-greet }}
```

Now create the entrypoint shell file to run inside the container:

```bash
#!/bin/sh -l
# ~/content-container-actions-app/entrypoint.sh

echo "Hello $1"   # uses the who-to-greet input variable to print "Hello [who-to-greet]" in the log file
time=$(date)      # gets the current time and sets it as an output variable that actions running later in a job can use.
echo "::set-output name=time::$time" # sets the action's output parameter using the workflow syntax
```

Don't forget to make the entrypoint file executable with `chmod +x ~/content-container-actions-app/entrypoint.sh`.

#### Create the Workflow

Now we just need to create the workflow folder (`mkdir -p ~/content-container-actions-app/.github/workflows/`) and the workflow itself:

```yaml
# ~/content-container-actions-app/.github/workflows/main.yaml
on: [push]

jobs:
  hello_world_job:
    runs-on: ubuntu-latest
    name: A job to say hello
    steps:
      # To use this repository's private action,
      # you must check out the repository
      - name: Checkout
        uses: actions/checkout@v3
      - name: Hello world action step
        uses: ./ # Uses an action in the root directory
        id: hello
        with:
          who-to-greet: 'Coelho, Pedro'
      # Use the output from the `hello` step
      - name: Get the output time
        run: echo "The time was ${{ steps.hello.outputs.time }}"
```



## Building an Actions Workflow

### Lesson Overview

1. **Workflow Parts**;

2. **Multiple Types of Workflows**;

### Workflow Overview

All GitHub workflows should be in the `.github/workflows/` folder and be in YAML.

Components of a workflow:

- **Name**;

- **On** - The **event that triggers** the workflow;

  - There can be **one trigger** or **multiple**;

  - **Any event in GitHub** can be used;

  - This can also be **filtered by branches**, **paths** and other filters;

- **Jobs** - list of jobs to execute;

  - **Jobs run in parallel** by default;

  - A **job contains** a sequence of tasks called **steps**;

    - **Steps can run commands**, run **actions**, or run a **setup**;

  - A job also **specifies the operating systems** used for the build, and **application version**;

### Lab

Update the `node.js.yml` workflow with new content:

```yaml
# ~/dpuga-simplenodeapp/.github/workflows/node.js.yml

# Name of the workflow
name: build
# When to run the workflow
# In this case, run on any push that contains modified JS files
on:
  push:
    paths: 
      - '**.js'
# The list of tasks to run
jobs:
  # The only job we have in this workflow
  build:
    # The name of the job
    name: "Node.js Build Workflow"
    # The job run 'strategy'
    # Run on the specified OSs and with the specified Node versions
    strategy:
      matrix:
        os: [macos-latest, windows-latest, ubuntu-latest]
        node-version: [12.x, 14.x, 16.x]
        # Exclude the latest MacOS with Node 12.X
        exclude:
          - os: macos-latest
            node-version: 12.x
    # Specify on where/what the job will run
    # In this case, specify the runner's OS
    runs-on: ${{ matrix.os }}
    # The steps/tasks performed in this job
    steps:
      # Checkout the code to the runner
      - name: "Checkout code"
        uses: actions/checkout@v3
      # Setup the Node.js version on the runner
      - name: "Setup Node.js ${{ matrix.node-version }}"
        uses: actions/setup-node@v3
        with:
          node-version:  ${{ matrix.node-version }}
      - name: "Install necessary Node packages"
        run: npm ci
      - name: "Build the Node.js code"
        run: npm run build --if-present
```

After this is done, perform any change to any .js file (the `index.js` file for example), push it to the repo and the workflow should start running.

There can also be more than one workflow, so create one for code testing:

```yaml
# ~/dpuga-simplenodeapp/.github/workflows/test.yml

# Name of the workflow
name: test
# When to run the workflow
# In this case, run on any Pull Request that's performed in to 'main'
on:
  pull_request:
    branches:
      - main

# The list of tasks to run
jobs:
  # The only job we have in this workflow
  build:
    # The name of the job
    name: "Node.js Code Testing"
    # The job run 'strategy'
    # Run on the specified OSs and with the specified Node versions
    strategy:
      matrix:
        os: [macos-latest, windows-latest, ubuntu-18.04]
        node-version: [12.x]
        include:
          - os: ubuntu-18.04
            node-version: 13.x
            experimental: true
    # Specify on where/what the job will run
    # In this case, specify the runner's OS
    runs-on: ${{ matrix.os }}
    # The steps/tasks performed in this job
    steps:
      # Checkout the code to the runner
      - name: "Checkout code"
        uses: actions/checkout@v3
      # Setup the Node.js version on the runner
      - name: "Setup Node.js ${{ matrix.node-version }}"
        uses: actions/setup-node@v3
        with:
          node-version:  ${{ matrix.node-version }}
      - name: "Install the Mocha test package"
        run: npm install -g mocha
      - name: "Run the code tests"
        run: npm test
```

With the workflow created, create a PR to main and watch it run



## Inserting Action Workflow Steps

### Lesson Overview

1. **Types of Steps**;
2. **Build Upon Steps**;

### Steps

Steps can use multiple different keywords:

- **if**;
  - Steps can have **if** statements to prevent them from running;
- **uses**;
  - Selects the **action to run** as part of the step;
  - Can be an action **inside the same repo**, on a **public repo** or a **Docker image**;
  - **Recommended to specify the version**;
- **run**;
  - Run a **command-line program**;
  - Can **specify the shell to use**;



## Lab Create a Container Action Using GitHub Actions

Created a lab repo in GitHub: https://github.com/pncoelho/containeraction

Created the Dockerfile.

```bash
# ~/containeraction/Dockerfile
# Container image that runs your code
FROM debian:9.5-slim

# Copies your code file from your action repository to the filesystem path `/` of the container
ADD entrypoint.sh /entrypoint.sh

# Makes the entrypoint file an executable
RUN chmod +x /entrypoint.sh

# Code file to execute when the docker container starts up (`entrypoint.sh`)
ENTRYPOINT ["/entrypoint.sh"]
```

Create the action file to run the Docker image:

```yaml
# ~/containeraction/action.yml
name: 'Container Memory'
description: 'Shows the memory status of the container'
inputs:
  greeting: # id of input
    description: 'greeting to use'
    required: true
    default: 'Awesome Guru'
outputs:
  memory: # id of output
    description: 'The container's memory
runs:
  using: 'docker'
  image: 'Dockerfile'
```

Now create the entrypoint shell file to run inside the container:

```bash
#!/bin/sh
# ~/containeraction/entrypoint.sh

echo "Hello $INPUT_GREETING" # this is a reference to the 'greeting' variable in the action.yml file
memory=$(cat /proc/meminfo)
echo "::set-output name=memory::$memory" # sets the action's output parameter using the workflow syntax
```

#### Create the Workflow

Now we just need to create the workflow folder (`mkdir -p ~/content-container-actions-app/.github/workflows/`) and the workflow itself:

```yaml
# ~/containeraction/.github/workflows/main.yaml
on: [push]

jobs:
  container_memory_job:
    runs-on: ubuntu-latest
    name: A job to display container memory
    steps:
      # To use this repository's private action,
      # you must check out the repository
      - name: Checkout
        uses: actions/checkout@v3
      - name: Get container memory
        id: conmem
        uses: ./ # Uses an action in the root directory
        with:
          greeting: 'Coelho, Pedro'
      # Use the output from the `conmem` step
      - name: Output the container memory
        run: echo "Total memory is ${{ steps.conmem.outputs.memory }}"
```

Once the everything is commited to the repo, the workflow should run and print the container memory. Something like this: `Total memory is MemTotal:        7113128 kB`.



## Connecting GitHub to Azure

### Lesson Overview

1. **GitHub Secrets**
   - Inside the repo there is a place to store credentials, secrets and tokens;
2. **Secrets in Workflow**



### Lab Itself

Create a new empty GitHub repo.

After, install the Azure CLI:

```bash
# install azure cli on code server
sudo rpm --import https://packages.microsoft.com/keys/microsoft.asc

sudo sh -c 'echo -e "[azure-cli]
name=Azure CLI
baseurl=https://packages.microsoft.com/yumrepos/azure-cli
enabled=1
gpgcheck=1
gpgkey=https://packages.microsoft.com/keys/microsoft.asc" > /etc/yum.repos.d/azure-cli.repo'

sudo yum install azure-cli

# login to azure
az login

# grab just the ID in the output of az login command
```

With the ID from the output of the `az login` command, store it in a *secret* inside the repo called `AZURE_SUBSCRIPTION_ID`.

Now let's use a *service principal* for the authentication. In order to create one, run the following command:

```bash
az ad sp create-for-rbac --name "GitHub-Actions" \
--role contributor \
--scopes /subscriptions/{subscription-id} \
--sdk-auth
```

Copy the full output of the command and create a new secret called `AZURE_CREDENTIALS`.

#### Create App Service App

- Login to Azure portal;
- Create an **App Service Plan**;
  - Leave settings default, just ensure the tier is **F1 (Free)**;
- Create an **App Service App** with the previous plan;
  - Leave settings default;
  - Select Node.js 16 and Linux;
- With the App created, select the *Get publish profile* and add the final GitHub secret `AZURE_WEBAPP_PUBLISH_PROFILE`;



## Continuous Deployment to Azure

To build on the previous lab and do continuous deployment to Azure, let's create a sample node app to upload.

In order to create this sample app, let's use the express-generator:

```bash
# Install the Express Generator
sudo npm install -g express-generator

# Create the sample node app
express --view pug --git ~/git_projects/dpuga-myExpressApp

# Install the required dependencies
cd ~/git_projects/dpuga-myExpressApp
npm install

# Test run the app
DEBUG=dpuga-myexpressapp:* npm start
```

Now commit the generated code to the repo.

### Setup Workflows

Add the following workflow file to the GitHub workflows folder:

```yaml
# ~/git_projects/dpuga-myExpressApp/.github/workflows/azureupdown.yaml
# Workflow Name
name: Azure environment up

# This workflow will trigger on a PR being labeled either:
# 	'azure up' or 'azure down'
on:
  pull_request:
    types: [labeled]

# Variables with Azure information that will be used to spin up new environment in Azure
env:
  # Must be unique inside subscription
  AZURE_RESOURCE_GROUP: "pncoelho-dpuga-myessentialapp_rgroup"
  # Must be unique inside subscription
  AZURE_APP_PLAN: "pncoelho-dpuga-myessentialapp_asplan"
  AZURE_LOCATION: '"Central US"'
  # Must be unique globally
  AZURE_WEBAPP_NAME: "pncoelho-dpuga-myessentialapp"

jobs:
  # This job is the one that sets up the Azure App Service App
  setup-up-azure-resources:
    runs-on: ubuntu-latest

    # Only runs on a PR that's labelled 'azure up'
    if: contains(github.event.pull_request.labels.*.name, 'azure up')

    steps:
      # Checks out the code from the Repo
      - name: Checkout repository
        uses: actions/checkout@v2

      # Login to Azure using the previous secret
      - name: Azure login
        uses: azure/login@v1
        with:
          creds: ${{ secrets.AZURE_CREDENTIALS }}

      - name: Create Azure resource group
        if: success()
        run: |
          az group create --location ${{env.AZURE_LOCATION}} --name ${{env.AZURE_RESOURCE_GROUP}} --subscription ${{secrets.AZURE_SUBSCRIPTION_ID}}
      - name: Create Azure app service plan
        if: success()
        run: |
          az appservice plan create --resource-group ${{env.AZURE_RESOURCE_GROUP}} --name ${{env.AZURE_APP_PLAN}} --is-linux --sku F1 --subscription ${{secrets.AZURE_SUBSCRIPTION_ID}}
      - name: Create webapp resource
        if: success()
        run: |
          az webapp create -g ${{ env.AZURE_RESOURCE_GROUP }} -p ${{ env.AZURE_APP_PLAN }} -n ${{ env.AZURE_WEBAPP_NAME }} --runtime "node|16-lts" --subscription ${{secrets.AZURE_SUBSCRIPTION_ID}}

  # This job is the one that tears down the Azure App Service App
  destroy-azure-resources:
    runs-on: ubuntu-latest

    # Only runs on a PR that's labelled 'azure down'
    if: contains(github.event.pull_request.labels.*.name, 'azure down')

    steps:
      - name: Checkout repository
        uses: actions/checkout@v2

      - name: Azure login
        uses: azure/login@v1
        with:
          creds: ${{ secrets.AZURE_CREDENTIALS }}

      - name: Destroy Azure environment
        if: success()
        run: |
          az group delete --name ${{env.AZURE_RESOURCE_GROUP}} --subscription ${{secrets.AZURE_SUBSCRIPTION_ID}} --yes
```

With this done, create a PR with the label `??zure up` to start the infrastructure.

Now that the infrastructure is up, we can create and use a workflow to build and deploy the application to the Web App:

```yaml
# ~/git_projects/dpuga-myExpressApp/.github/workflows/buildandpublish.yaml
on:
  push:
    branches:
      - main
  workflow_dispatch:

env:
  AZURE_WEBAPP_NAME: "pncoelho-dpuga-myessentialapp"
  AZURE_WEBAPP_PACKAGE_PATH: '.'
  NODE_VERSION: '16.x'

permissions:
  contents: read

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v3

    - name: Set up Node.js
      uses: actions/setup-node@v3
      with:
        node-version: ${{ env.NODE_VERSION }}
        cache: 'npm'

    - name: npm install, build, and test
      run: |
        npm install
        npm run build --if-present
        npm run test --if-present

    - name: Upload artifact for deployment job
      uses: actions/upload-artifact@v3
      with:
        name: node-app
        path: .

  deploy:
    permissions:
      contents: none
    runs-on: ubuntu-latest
    needs: build
    environment:
      name: 'Development'
      url: ${{ steps.deploy-to-webapp.outputs.webapp-url }}

    steps:
    - name: Download artifact from build job
      uses: actions/download-artifact@v3
      with:
        name: node-app

    - name: 'Deploy to Azure WebApp'
      id: deploy-to-webapp 
      uses: azure/webapps-deploy@v2
      with:
        app-name: ${{ env.AZURE_WEBAPP_NAME }}
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        package: ${{ env.AZURE_WEBAPP_PACKAGE_PATH }}
```

Just commit this workflow and it will start running.

To tear the Azure infra down, just go to the PR if it's still opened and add the tag `azure down`.