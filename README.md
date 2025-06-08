# Getting Started with Pulumi

## What is Pulumi?

Pulumi is an open-source infrastructure as code (IaC) tool that allows developers to define, deploy, and manage cloud infrastructure using familiar programming languages like Python, JavaScript, TypeScript, Go, and C#. It enables users to create and manage cloud resources across various providers such as AWS, Azure, Google Cloud, and Kubernetes.

## First Steps

For the sake of simplicity and brevity, I'll display examples using Linux, Python, and Azure as the cloud provider. However, Pulumi supports multiple languages and cloud providers, so you can adapt the examples to your preferred stack.

### Installation

To get started with Pulumi, you need to install the Pulumi CLI. Run the script below to install it on your system:

```bash
curl -fsSL https://get.pulumi.com | sh
```

To verify the installation, you can check the installed version of Pulumi by running:

```bash
pulumi version
```

For more installation options, visit the [Pulumi installation page](https://www.pulumi.com/docs/iac/download-install/).

### Pulumi Cloud as the state backend

Pulumi Cloud is a managed service that provides a secure and scalable backend for storing your Pulumi state files. To use Pulumi Cloud, you need to sign up for an account at [Pulumi Cloud](https://app.pulumi.com/signup). Pulumi Cloud is free for personal use and offers a generous free tier.

### Azure subscription and Azure CLI

To deploy resources on Azure, you need an Azure subscription. If you don't have one, you can create a free account at [Azure Free Account](https://azure.microsoft.com/free/).

You also need to install the Azure CLI to interact with Azure resources. You can install it by following the instructions on the [Azure CLI installation page](https://docs.microsoft.com/cli/azure/install-azure-cli).

If you're using a Debian-based Linux distribution, you can install the Azure CLI using the following command:

```bash
curl -L https://aka.ms/InstallAzureCLI | sudo bash
```

After installing the Azure CLI, you can log in to your Azure account by running:

```bash
az login
```

## Creating a new Pulumi project

Pulumi offers a wide range of ready-to-use templates to help you get started quickly. You can create a new Pulumi project using the `pulumi new` command. To list available templates, run:

```bash
pulumi new --list
```
Since we are using Azure, we can filter the templates to show only Azure-related ones:

```bash
pulumi new --list | grep azure
```

For this project, we'll use a sample Pulumi project that creates a simple static website on Azure.

### Creating a new Pulumi project using the Azure Static Website template

This project will deploy a static website on Azure using Azure Blob Storage for hosting. It will also deploy an Azure CDN endpoint to serve the static content with low latency, caching, and HTTPS support.

To create the project using the Azure Static Website template, run the following command:

```bash
pulumi new static-website-azure-python
```

This command will prompt you for some configuration options, such as the project name, description, stack, and Azure region. You can accept the default values or customize them as needed.