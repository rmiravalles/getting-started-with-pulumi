# Getting Started with Pulumi

## What is Pulumi?

Pulumi is an infrastructure as code tool that lets you define and manage cloud resources using real programming languages. In this repository, the example is a small Python project that deploys a static website to Azure.

## What This Repository Does

The sample lives in [static-web](static-web) and uses:

- Azure Blob Storage to host static files
- Azure Native provider resources to create the Azure infrastructure
- pulumi-synced-folder to publish the contents of [static-web/www](static-web/www)

## Before You Start

You will need:

- A Pulumi Cloud account or another state backend
- An Azure subscription
- The Azure CLI installed and logged in
- Python 3 and pip

If you are new to Azure, create a free account first: https://azure.microsoft.com/free/

Install the Pulumi CLI:

```bash
curl -fsSL https://get.pulumi.com | sh
```

Verify the install:

```bash
pulumi version
```

Install the Azure CLI if needed, then sign in:

```bash
az login
```

## Pulumi New And Templates

The `pulumi new` command creates a new Pulumi project from a ready-to-use template. Templates give you a working starting point with the right project files, dependencies, and sample infrastructure already in place.

To see the templates that are available locally, run:

```bash
pulumi new -l
```

Because this repository is an Azure example, you can look for Azure-focused templates in that list. One of those templates is `static-website-azure-python`, which is the template used for this sample. It creates the same kind of static website project you see in [static-web](static-web): a Python Pulumi program, Azure Native resources, and a local `www` folder for website content.

In practice, `pulumi new` is useful when you want to start from a known-good baseline instead of assembling the project structure yourself. You answer a few prompts, and Pulumi generates the files needed to begin deploying infrastructure right away.

## Helpful Pulumi CLI Commands

- `pulumi login` connects the CLI to your state backend
- `pulumi whoami` confirms which Pulumi account you are using
- `pulumi stack ls` shows available stacks
- `pulumi stack init dev` creates a new stack for your environment
- `pulumi config set azure-native:location eastus` sets the Azure region
- `pulumi preview` shows the planned changes before anything is created
- `pulumi up` applies the infrastructure changes
- `pulumi stack output` prints exported values such as a website URL
- `pulumi refresh` syncs the stack state with real cloud resources
- `pulumi destroy` removes everything created by the stack

## Beginner-Friendly Azure Deployment Steps

1. Open a terminal in the repository root and move into the sample project:

```bash
cd static-web
```

2. Create and activate a virtual environment, then install the Python dependencies:

```bash
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
```

3. Make sure Pulumi is using your preferred backend:

```bash
pulumi login
```

If you are just learning, Pulumi Cloud is the easiest option.

4. Create or select a stack. A stack is a named environment such as dev, test, or prod:

```bash
pulumi stack init dev
```

If the stack already exists, use `pulumi stack select dev` instead.

5. Set the Azure region for the deployment:

```bash
pulumi config set azure-native:location westeurope
```

You can choose another Azure region if you want resources closer to you or your users.

6. Confirm Azure CLI access:

```bash
az account show
```

If that fails, run `az login` again.

7. Preview the deployment so you can see what will be created:

```bash
pulumi preview
```

8. Deploy the infrastructure:

```bash
pulumi up
```

Pulumi will show a final summary and ask for confirmation before making changes.

9. After the update finishes, view the exported website URL:

```bash
pulumi stack output websiteURL
```

10. When you are done experimenting, clean up the Azure resources:

```bash
pulumi destroy
```

## Where The Code Lives

- [static-web/__main__.py](static-web/__main__.py) is the main Pulumi program. It defines the Azure resources to deploy, the order they are created in, and the values that get exported after deployment.
- [static-web/Pulumi.yaml](static-web/Pulumi.yaml) describes the project itself, including the name, runtime, and template metadata used by Pulumi.
- [static-web/requirements.txt](static-web/requirements.txt) lists the Python packages needed to run the Pulumi program, including the Azure Native provider and the synced-folder helper.

## How `__main__.py` Is Organized

The [static-web/__main__.py](static-web/__main__.py) file is the entry point for the infrastructure code. Pulumi executes this file from top to bottom and uses it to build the resource graph for Azure.

The file follows a simple structure:

1. Read configuration values from `pulumi.Config()` so the deployment can be customized without changing code.
2. Create a resource group to hold the Azure resources for the website.
3. Create a storage account that will host the static content.
4. Enable static website hosting on that storage account.
5. Sync the local [static-web/www](static-web/www) folder into the website container.
6. Export the public website URL so it can be viewed after `pulumi up` finishes.

This structure is beginner-friendly because each block maps to one Azure concept. If you are learning Pulumi, a good way to read the file is to follow the resource declarations in the same order they appear and ask, "What Azure resource is being created here, and what depends on it?"

### Key Parts Of The Program

- `config = pulumi.Config()` loads stack configuration.
- `path`, `index_document`, and `error_document` make the site folder and document names configurable.
- `azure_native.resources.ResourceGroup(...)` creates the Azure resource group that scopes the rest of the deployment.
- `azure_native.storage.StorageAccount(...)` creates the storage account used by the website.
- `azure_native.storage.StorageAccountStaticWebsite(...)` turns on static website hosting for the storage account.
- `synced_folder.AzureBlobFolder(...)` uploads the local website files into Azure Blob Storage.
- `pulumi.export("websiteURL", ...)` makes the final URL visible from the Pulumi CLI.

### How The Resources Depend On Each Other

Pulumi does not rely on a separate dependency file. Instead, it builds the dependency graph from the resource arguments you pass into each constructor.

In this program, the resource group is created first and then reused by later resources through properties like `resource_group.name`. That value is not a plain string; it is an output produced by the first resource, and Pulumi knows that the later resources cannot be created until the resource group exists.

The same pattern repeats for the storage account and static website setup. The storage account name is passed into the static website resource, and both the storage account name and the website container name are passed into `synced_folder.AzureBlobFolder(...)`. Pulumi uses those references to infer the correct order automatically, so you only describe what should exist and how the resources relate to one another.

In short, the logic is: create one resource, read its outputs, and feed those outputs into the next resource. That is how Pulumi understands both the relationships and the deployment order.

## Project File Summary

- [static-web/__main__.py](static-web/__main__.py) contains the resource definitions and deployment logic.
- [static-web/Pulumi.yaml](static-web/Pulumi.yaml) contains project metadata and runtime settings.
- [static-web/requirements.txt](static-web/requirements.txt) contains the Python dependencies required by the program.
- [static-web/www](static-web/www) contains the static files that will be uploaded to Azure Blob Storage and served as the website content.
