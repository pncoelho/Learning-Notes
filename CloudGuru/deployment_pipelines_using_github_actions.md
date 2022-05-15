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