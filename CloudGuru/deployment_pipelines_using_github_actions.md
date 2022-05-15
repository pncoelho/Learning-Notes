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
