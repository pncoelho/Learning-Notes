# DevOps Essentials

DevOps Culture

DevOps is a software engineering culture and practice that aims at unifying software development (Dev) and software operations (Ops).

DevOps aims at shorter development cycles, increased release frequency, more dependable releases, in close alignment with business objectives.

DevOps is first and foremost a cultural movement to increase collaboration between developers and operations people.

This culture has given rise to a set of practices.

DevOps is NOT Tools, but Tools are essential to success in DevOps.

DevOps is not a standard, in that there are no official standards, there are best practices however.

DevOps and Agile often go hand-in-hand

Under traditional separation between Dev and Ops, they have different and opposing goals:
	• Development -> Speed
	• Operations -> Stability

Within a DevOps culture, both teams share the same goals and are measured by the same goals.

DevOps Goals

DevOps goals are related to two things: Speed and Stability

These goals include things like:
	• Fast time-to-market (TTM)
		○ Time from code being finished to being deployed to production and working;
	• Few production failures
	• Immediate recovery from failures

Build Automation

Build automation: automation of the process of preparing code for deployment to a live environment

Depending on what languages are used, code needs to be compiled, linted, minified, transformed, unit tested, etc.

Build automation means taking these steps and doing them in a consistent, automated way using a script or tool.

The tools of build automation vary depending on the language and frameworks used.

Usually, build automation looks like running a command-line tool that builds code using configuration files and/or scripts that are treated as part of the source code.
This also means that usually, these configuration files and/or scripts are also committed to the same source control where the code is.

Build automation is independent of an IDE.

As much as possible, build automation should be agnostic of the machine that it is built on. 

Why do build automation:
	• Fast - Handles tasks that would otherwise need to be done manually;
	• Consistent - The build happens the same way every time;
	• Repeatable - The build can be done multiple times with the same result. Any version of the source code can always be transformed into deployable code in a consistent way;
	• Portable - The build can be done the same way on any machine;
	• Reliable

Continuous Integration (CI)

Continuous Integration is the practice of frequently merging code changes done by developers.

Traditionally, developers would work separately, perhaps for weeks at a time, and then merge all of their work together at the end in one large effort.

Continous integration means merging constantly throughout the day, usually with the execution of automated tests to detect any problems caused by the merge.

Merging all the time could be a lot of work, so the process should be automated.

Continous Integration is usually done with the help of a CI Server.

When a developer commits a code change, the CI server sees the change and automatically performs a build, also executing automated tests.

If there is any problem with the build, the CI server imediatelly and automatically notifies the developer.

If anyone commits code that "breaks the build", they are responsible for fixing the problem or rolling back their changes immediately so that other developers can continue working.

Why do CI?
	• Early detection of certain types of bugs - If code doesn't compile or an automated test fails, the developers are notified and can fix it immediately. The sooner these bugs are detected, the easier they are to fix;
	• Eliminate the scramble to integrate just before a big release - The code is constatly merged, so there is no need to do a big merge at the end;
	• Makes frequent releases possible - Code is always in a state that can be deployed to production;
	• Makes continous testing possible - Since the code can always be run, QA testers can get their hands on it all throughout the development proccess, not just at the end;
	• Encourages good coding practices - Frequent commits encourages simple, modular code;

Continuous Delivery and Continuous Deployment

Continous Delivery is the practice of continously maintaining code in a deployable state.

This is regardless of whether or not the decision is made to deploy. The code is always in a deployable state, meaning the decision to deploy is a business one.

Continous Deployment is the practice of frequently deploying small code changes to production.

While continous elivery  is keeping the code in a deployable state, continuous deployment is actually doing the deployment frequently.

Some companies that use continuous deployment deploy to production multiple times a day.

There is no standard for how often you should deploy, but the more often the better.

With continous deployment, deployments to production are routine and commonplace. They are not a big, scary event.

What does continuous delivery and deployment look like?
	• Each version of the code goes through a series of stages such as automated build, testing and manual acceptance testing;
	• The result of this process is an artifact or package that is able to be deployed;
	• When the decision is made to deploy, the deployment is automated;
	• If a deployment causes a problem, it is quickly and reliably rolled back using an automated process;
	• Rollbacks aren't a big deal, because the developers can redeploy a fixed version as soon as they have one available.
	• This makes pushing to production and rollbacks less of an obstacle, because code can be rolled back and rolled forward automatically;

Why should continous delivery and deployment should be done:
	• Faster time-to-market;
	• Fewer problems caused by the deployment process - Since the deployment process is frequently used, any problems with the process are easily discovered;
	• Lower risk - The more changes that are deployed at once, the higher the risk. Frequent deployments of less changes are less risky;
	• Reliable rollbacks - Robus automation means rollbacks are a reliable way to ensure stability for customers, and they don't hurt developers because they can roll forward with a fix, as soon as they have one;
	• Fearless deployments - Robust automation plus the ability to rollback quickly means deployments are commonplace, everyday events rather than big, scary events;

Infrastructure as Code

Infrastructure as Code (IaC): manage and provision infrastructure through code and automation.

Automation and code is used to create and change infrastructure.

Why do IaC?
	• Consistency creation and management of resources - The same automation will run the same way every time;
	• Reusability - Code can be used to make the same change consistently across multiple hosts and can be used again in the future;
	• Scalability - A new instance can be configured exacltly the same way as the existing one in minuter;
	• Self-documenting - Changes to infrastructure document themselves to a degree;
	• Simplify the complexity - Complex infrastructures can be stood up quickly once they are defined as code;

Configuration Management

Configuration Management: maintaining and changing the state of pieces of infrastructure in a consistent, maintainable, and stable way.

Configuration management is about doing them in a maintanable way.

Configuration management allows you to minimize configuration drift - the small changes that accumulate over time and make systems different from one another and harder to manage.

IaC is very beneficial for configuration management.

Benefits of configuration management:
	• Save time - it takes less time to change the configuration;
	• Insight - With good configuration management, you can know about the state of all pieces of a large and complex infrastructure;
	• Maintainability - A more maintainable infrastructure is easier to change in a stable way;
	• Less configuration drift - It is easier to keep a standard configuration across a multitude of hosts;

Orchestration

Ocherstration: automation that supports processes and workflows, such as provisioning resources.

With orchestration, managing a complex infrastructure is less like being a builder and more like conducting an orchestra.

Instead of going out and creating a piece of infrastructure, the conductor simply signals what needs to be done and the orchestra performs it:
	• The conductor does not need to control every detail;
	• The musicians (automation) are able to perform their piece with only a little bit of guidance;

Benefits of orchestration:
	• Scalability - Resources can be quicky increased or decreased to meet changing needs;
	• Stability - Automation tools can automatically responde to fix problems before users see them;
	• Save time - Certain tasks and workflows can be automated, freeing up engineer's time;
	• Self-service - Orchestration can be used to offer resources to customers in a self-service fashion;
	• Granular insight into resource usage - orchestration tools give greater insight into how many resources are being used by what software, services, or customers

Monitoring

Monitoring: the collection and presentation of data about the performance and stability of services and infrastructure.

The collected data is presented in various forms, such as charts and graphs, or in the form of real-time notifications about problems.

Benefits:
	• Fast recovery - the sooner a problem is detected, the sooner it can be fixed;
	• Better root cause analysis - The more data you have, the easier it is to determine the root cause of a problem;
	• Visibility across teams - Good monitoring tools give useful data to both developers and operations people about the performance of code in production;
	• Automated response - Monitoring data can be used alongside orchestration to provide automated responses to events, such as automated recovery from failures;

Microservices

Microservices: a microservice architecture breaks an application up into a collection of small, loosely-coupled services.

Traditionally, apps used a monolithic architecture. In a monolithic architecture, all features and services are part of one large application.

Microservices are small: each microservice implements only a small piece of an application's overall functionality.

Microservices are loosely coupled: different microservices interact with each other using stable and well-defined APIs. This means that they are independent of one another.

There are many different ways to structure and organize a microservice architecture.

Each of the pieces of an application would have its own codebase and a separate running process (or processes). They can all be build, deployed and even scaled separately.

Benefits:
	• Modularity
	• Technological flexibility - You don't need to use the same languages and technologies for every part of the app. You can use the best tool for each job;
	• Optimized scalability - You can scale individual parts of the app, based upon resource usage and load of each part;

For smaller, simpler app, a monolith might be easier to manage.

Next Steps
Tool quick starts are quick and small courses

## DevOps and the Cloud

Google Cloud Platform

Google App Engine:
	• PaaS - Deploy your code, don't worry about the rest;
	• Built in support for microservices;
	• Out-of-the box autoscaling;
	• Certain configurations can be considered serverless;

Google Compute Engine:
	• IaaS - Deploy and orchestrate clusters of VMs on Google's architecture;
	• Built-in orchestration;
	• Works with app engine;
	• Can be managed with configuration management tools;

Google Cloud Functions:
	• FaaS/Serverless solution

Google Cloud SDK:
	• An SDK for interacting with GCP APIs;
	• Makes it easy to build your own tools and automations that interact with GCP;

Stackdrive:
	• GCP's monitoring solution;
	• Monitoring, logging and diagnostics for your GCP services;
	• Also works with AWS;

Cloud Deployment Manager:
	• Declarative configuration for you GCP stack;
	• IaC and automated deployment;
	• YAML-based;

Google Kubernetes Engine:
	• Orchestration on GCP with Kubernetes;
	• Do continous integration with Jenkins on Kubernetes Engine;

Microsoft Azure DevOps

Continous Integration, Delivery and Deployment:
	• Visual Studio Team Services - source control and CI;
	• Jenkins - CI for Java apps;
	• Continuous Deployment Triggers - automated deployment trigger integrated with CI;

Orchestration:
	• Azure Container Registry;
	• Azure Container Service - Kubernetes orchestration;
	• Azure Web Apps - Cloud hosting for web apps integrated with the DevOps pipeline

Monitoring:
	• Azure Application Insights - APM, diagnostics and analytics. Supports machine learning;

Faas/Serverless:
	• Azure functions;

Amazon Web Services

Amazon EC2 (Elastic Compute Cloud):
	• IaaS;
	• Easily scalable;
	• Full control over your cloud infrastructure;
	• Integrates with tons of tools;

AWS Elastic Beanstalk:
	• PaaS;
	• Out-of-the-box load balancing and autoscaling;
	• Can still access underlying AWS resources with full control;

Continuous Integration, Delivery and Deployment:
	• AWS CodeBuild;
	• AWS CodeDeploy;
	• AWS CodePipeline - full code pipeline from build to deploy;
	• AWS CodeStar - integrates all parts process with project management and Jira issue tracking;

Infrastructure as Code:
	• CloudFormation - Stack templating engine, YAML, or JSON-based;
	• OpsWorks - IaC with Chef;

Serverless/FaaS:
	• AWS Lambda - run serverless functions on AWS;

Monitoring:
	• Amazon Cloudwatch - track metrics and logs, set alarms, and automate responses to monitoring data;
