# Hands-On GitOps

## DevOps with GitOps

![ho_gitops_ci_cd_pipeline](./media/ho_gitops_ci_cd_pipeline.png)

### DevOps workflow:

1. **Coding Loop** (Left Circle);
   1. **Planning** the application;
   2. **Coding** the application;
   3. **Building** the application (building, compiling or containerizing the code);
2. **Code exits Sprint**;
   1. **Continuous Quality** checks on the code;
      1. Testing the code and the infrastructure it will reside in;
   2. **Release Gating** checks if the code is ready and compliant to move from one stage to the next;
      1. From Dev to Test, from Test to Pre-Prod and from Pre-Prod to Prod (other stages may exist);
   3. **Continuous Delivery** is performed if the code is valid by maintaining it in a deployable state;
   4. **Automated Deployment** happens by taking the prepared code and deploying to production
3. **Infrastructure Loop** (Right Circle);
   1. A **Continuous Monitoring** of the code and infrastructure is performed to check on the status;
   2. **KPI Feedback** comes from this *Continuous Monitoring* to see if the minimum, desired or critical values for our code and infrastructure are being meet;
4. **Info from Prod**;
   1. With the information gathered in production, **Continuous Improvements** can be performed on the code and the infrastructure;

#### GitOps place in the DevOps Workflow

In order to assist in doing **Automated Deployment** and **Continuous Deployment**, the GitOps practice takes code and workloads from version control and synchronizes the environments, with what is being maintained in the repositories.



### GitOps Architecture used on this Course

![ho_gitops_gitops_architecture](./media/ho_gitops_gitops_architecture.png)

Presented above is the architecture used in this GitOps course. The main workflow and components look like this:

1. The **Code Developer** or **Operations Engineer** create code;
2. This code is then store in a **Version Control System** using **Git**;
   - The *Code Developers* will write the **application source code**;
   - The *Operations Engineer* will write the **infrastructure source code** in **Kubernetes Manifests** (YAML files);
3. The *application source code* will be **Built** in to a **Container Image** that will be stored in **Docker Hub**;
4. A **Kubernetes Cluster** will be used to run the **Workloads**;
   - This *Cluster* will be the **Infrastructure** where the *application container* will run in;
5. **Flux** will be installed inside the *Kubernetes Cluster* and will pay attention to the *VCS Repo*;
   1. *Flux* will use the *builded code* to create the **Kubernetes Pods**;
   2. And the *Kubernetes Manifests* to create the **Kubernetes Deployments, Services, Service Accounts** and whatever other configuration that needs to be performed on the infrastructure;
6. *Flux* will do this by **monitoring the VCS Repo** for any **Commits**, **Pushes** and **Push Requests** that are being performed;



## Labs

### Installing and Configuring Flux with GitHub

#### Introduction

This lab introduces the steps necessary for installing Flux and configuring it to work with a repository in GitHub. We'll need our own GitHub account to fork a sample repository, and this lab will spin up a Kubernetes cluster to enable us to install and configure Flux.

#### Create a GitHub Repository

The [ACloudGuru-Resources/content-gitops](https://github.com/ACloudGuru-Resources/content-gitops) repository is already online for us. Let's get into it and press the **Fork** button. Once the new repository is created, we'll use the credentials on the hands-on lab overview page to log into our Kubernetes master node as `cloud_user`.

#### Deploy Flux Into Your Cluster

Let's make sure Kubernetes is running, and that we have some nodes:

```
$ kubectl get nodes 
```

Now let's make sure Flux is installed and running:

```
$ fluxctl version 
```

We should get response of *unversioned*, which is fine. Now let's take another look at our Kubernetes deployment:

```
$ kubectl get pods --all-namespaces 
```

Create a namespace for Flux:

```
$ kubectl create namespace flux 
```

This will let us make sure it got created:

```
$ kubectl get namespaces 
```

Set the GHUSER environment variable, then check to make sure it was set:

```
$ export GHUSER=[Our GitHub Handle] $ env | grep GH 
```

Now we can deploy Flux, using the `fluxctl` command:

```
$ fluxctl install \ --git-user=${GHUSER} \ --git-email=${GHUSER}@users.noreply.github.com \ --git-url=git@github.com:${GHUSER}/content-gitops \ --git-path=namespaces,workloads \ --namespace=flux | kubectl apply -f - 
```

#### Verify The Deployment and Obtain the RSA Key

Once that `fluxctl` command is finished running, we can verify:

```
$ kubectl get pods --all-namespaces $ kubectl -n flux rollout status deployment/flux 
```

Now we can get the Flux RSA key created by `fluxctl`:

```
$ fluxctl identity --k8s-fwd-ns flux 
```

Copy that RSA key, and let's head back over to GitHub.

#### Implement the RSA Key in GitHub

In the GitHub user interface, make sure we're in our new repository and click on the **Settings** tab. In there, click **Deploy keys**, then click the **Add deploy key** button. We can give it a *Title* of something like **GitOps Deploy Key**, then paste the key we copied earlier down in the *Key* field. Check the *Allow write access* box, and then click **Add key**.

#### Use the `fluxctl sync` Command to Synchronize the Cluster with the Repository

Use `fluxctl` to sync the cluster with the new repository:

```
$ fluxctl sync --k8s-fwd-ns flux 
```

Then check the existence of the *lasample* namespace:

```
$ kubectl get namespaces 
```

Then check that the Nginx deployment is running:

```
$ kubectl get pods --namespace=lasample 
```

We should see the deployment running, with two replicas.



### Operating and Troubleshooting Flux in a Kubernetes Cluster





### Deploying Applications with GitHub Actions Workflow and Flux

#### Introduction

This hands-on lab challenges the student to implement Flux within a GitHub environment, taking into account the need to deploy applications into development, test, and production environments. This lab challenges the student to set up Kubernetes YAML to deploy workloads into a QA and Production Cluster.

To complete this lab the student must have their own GitHub or GitLab account and be able to set up a repository with the required YAML files. The student should also be familiar with the installation and configuration of Flux, which is covered in a prior lab as part of the GitOps course.

##### Prerequisites

We need our own GitHub and Docker Hub accounts. In our Docker Hub account, we'll need a repository that we can push to from GitHub.

We'll also need to create fork of [the content-gitops repository](https://github.com/ACloudGuru-Resources/content-gitops) in our own GitHub account.

#### The Scenario

Our manager is impressed with Flux and wants to expand the proof-of-concept work we are doing beyond using Flux on a development server. He has asked us to set up a demo for some of the Operations and Engineering team, to show how Flux can be used to promote workloads from Development to Test and Production environments.

#### Fork or Create a GitHub Repository

We'll have to set up a repository for this lab. Let's fork [the ACloudGuru-Resources/content-gitops repository](https://github.com/ACloudGuru-Resources/content-gitops) using our own GitHub account.

#### Create the Hello YAML in a Folder Called `qa`

First let's navigate into the `workloads` directory of our fork, and open up `hello.yml`. Copy that whole file's contents to the clipboard, then navigate back out to the main `content-gitops` directory. Click the **Create new file** button. In the **Name your file** box, we're actually going to create a new directory in addition to the file, so type **qa/**. The forward slash will make it a directory, and we'll get another box to type the filename in. We'll call this file `hello.yaml`, and paste the contents of the `hello.yml` we copied earlier into it:

```
apiVersion: apps/v1 kind: Deployment metadata:  name: hello  namespace: lasample  labels:    app: hello spec:  selector:    matchLabels:      app: hello  template:    metadata:      labels:        app: hello    spec:      containers:      - name: hello        image: acgtes/gitops:hellov1.1 
```

The `hello.yml` we copied from may have a different version number, so pay attention here and make sure we set it to `hellov1.1` in this new file. Click **Commit new file**.

#### Create the Hello YAML in a Folder Called `production`

Navigate back to the main `content-gitops` directory, and let's repeat this procedure, but making a `production` instead of `qa` folder. Click the **Create new file** button. In the **Name your file** box, type **production/**, then type **hello.yaml** in the second box that appears after we type the slash character. Paste the same content into this file that we pasted into the `qa` directory's file with the same name.

Click **Commit new file**.

#### Deploy the Flux Daemon and Configure It To Scan the `qa` Folder

##### Logging In

We've got to use the login credentials for `cloud_user`, and get logged into our Kubernetes master node with SSH.

##### Inspect the Environment

Just to see what we've got running, as far as Kubernetes goes, let's execute these commands:

```
$ kubectl get nodes $ kubectl get pods --all-namespaces 
```

We should see three nodes (one master) and several running namespaces. Now we can install Flux.

##### Deploying Flux

First, let's create a namespace, and call it `flux`:

```
$ kubectl create namespace flux 
```

Now we can set the GHUSER Environment variable to our GitHub username:

```
$ export GHUSER=[Our username here] 
```

Deploy Flux, but notice that we are scanning the `qa` folder not the `workloads` folder:

```
$ fluxctl install \ --git-user=${GHUSER} \ --git-email=${GHUSER}@users.noreply.github.com \ --git-url=git@github.com:${GHUSER}/content-gitops \ --git-path=namespaces,qa \ --namespace=flux | kubectl apply -f - 
```

If we run another `kubectl get pods --all-namespaces`, we should see a couple of `flux` pods running.

##### Giving Flux Permissions

We've got to give this instance of Flux read and write permissions on our repository. Let's get our RSA key with this command:

```
$ fluxctl identity --k8s-fwd-ns flux 
```

Copy the output, then let's get back into GitHub with a web browser.

##### Implement the RSA Key in GitHub

In the GitHub user interface, make sure we're in our new repository, and click on the **Settings** tab. In there, click **Deploy keys**, then click the **Add deploy key** button. We can give it a *Title* of **qa**, then paste the key we copied earlier down in the *Key* field. Check the *Allow write access* box, and click **Add key**.

##### Setting an Environment Variable

Set the environment variable for the `fluxctl` command:

```
$ export FLUX_FORWARD_NAMESPACE=flux 
```

Use `fluxctl` to examine the deployed container images:

```
$ fluxctl list-workloads --all-namespaces 
```

We should see our `hello` image running, and that it's at version 1.1.

#### Implement a New Version

##### Change the Version Number

We've just been informed that our development team has released version 1.2 of the `hello` application. We need to get that in sync with the `qa` server. So let's head back into GitHub, navigate to the `content-gitops/qa` directory, and click on our `hello.yaml` file. Edit it (by clicking on the pencil button) and change version in the last line from 1.1 to 1.2. Click on the **Commit changes** button and then head back into the terminal we have open.

##### Synchronize with Flux

On the Kubernetes server, let's run a command to pull the new version into our cluster:

```
$ fluxctl sync --k8s-fwd-ns flux 
```

Check to see if it came, with this:

```
$ fluxctl list-workloads --all-namespaces 
```

We should see that the `hello` container is now at version 1.2.

#### Another Scenario -- A Production Server

We've got a group of production engineers, and they want to expand the server that they already have running. This will be pretty much the same process.

##### Clean the Slate

Let's start off by getting rid of our Flux install, and the `lasample` namespace:

```
$ kubectl delete namespace flux $ kubectl delete namespace lasample 
```

Now let's take another look at what's still running:

```
$ kubectl get pods --all-namespaces 
```

Things should look like they did the first time we ever ran that command in this environment.

#### Install Flux

We'll set up the namespace again:

```
$ kubectl create namespace flux 
```

We're going to deploy Flux again, but we are going to change the folder that it scans. Instead of `qa`, like we did earlier, we're going to have it scan a `production` folder. So let's run this on our Kubernetes server:

```
$ fluxctl install \ --git-user=${GHUSER} \ --git-email=${GHUSER}@users.noreply.github.com \ --git-url=git@github.com:${GHUSER}/content-gitops \ --git-path=namespaces,production \ --namespace=flux | kubectl apply -f - 
```

##### Giving Flux Permissions

We've got to create another deploy key for this instance of Flux. Get it by running this:

```
$ fluxctl identity 
```

Copy the output, and head back into GitHub.

Once in the GitHub user interface, click on the **Settings** tab. In there, click **Deploy keys**, then click the **Add deploy key** button. We can give it a *Title* of **production**, then paste the key we copied earlier down in the *Key* field. Check the *Allow write access* box, and then click **Add key**.

In the `production` folder, open up `hello.yaml`. Look down at the bottom, and note the version, 1.1.

#### Sync Flux

Back in the command line we've got open, let's pull to our cluster:

```
fluxctl sync 
```

Now let's list the workloads in the `lasample` namespace:

```
fluxctl list-workloads -n lasample 
```

We should see our `hello` container running, at version 1.1.

#### Change Versions

Back in our GitHub repository (in `hello.yaml` that's in the `production` directory) let's change the version to 1.2 on the last line of the file. Click the pencil icon and then change `hellov1.1` at the end of the last line to `hellov1.2`. Click the **Commit changes** button below.

#### Implement the Change

Now we can update the container back in the terminal we have open. Let's sync things again:

```
fluxctl sync 
```

List the workloads in the `lasample` namespace again:

```
fluxctl list-workloads -n lasample 
```

This time we should see our `hello` container running, but at version 1.2.

#### Conclusion

Congratulations on completing this hands-on lab!