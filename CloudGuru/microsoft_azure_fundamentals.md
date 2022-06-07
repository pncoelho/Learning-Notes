- # Microsoft Azure Fundamentals

## Introduction

### Powershell

1. cmdlet
   - A script that performs a specific task, like creating a new Virtual Machine;
2. Azure Resource Manager
   - Powershell also uses the Resource Manager, like the Portal, to manipulate Azure resources;
3. Versatile
   - Can be used for other things, other than Azure;

### Cloud Shell

Features:

- Access from anywhere using a browser or a mobile app;
- Use either Bash (Azure CLI) or PowerShell;
- Includes a whole list of tools;
- Dedicated storage to persist data between sessions;
- Complete file editor;

### Azure Resource Manager (ARM) Templates

- Describe Resource Usage
  - what are you updating, deleting, creating?
- Common syntax
  - Defined language for all ARM templates, making it easier to formalize and learn;
- Idempotent
- Declarative



## Cloud Concepts

### Language of Cloud Economics

Two main areas of expenses:

- **Capital Expenditure (CapEx)**
  - Money spent by a business or organization on acquiring or maintaining fixed assets, such as land, buildings, and equipment;
  - Requires large upfront investments;
- **Operational Expenditure (OpEx)**
  - An ongoing cost for running a product, business, or system on a day-to-day basis, including annual costs;
  - Pay-as-you-go;

### Cloud Services Model

- IaaS
  - Organization has complete control of the infrastructure.
  - Dynamic and flexible. You can do almost anything.
  - Cost varies depending on consumption.
  - Services are highly scalable.
  - Multiple users share a single piece of hardware.
  - Examples: VM, VNet, Storage
- PaaS
  - Resources are virtualized and can easily be scaled up or  down as needed.
  - Services often assist with the development, testing and  deployment of apps.
  - Multi-user access via the same development application.
  - Integrates web services and databases.
  - Examples: App Services, Azure  CDN, Cosmos DB
- SaaS
  - Managed from a central location.
  - Hosted on a remote server.
  - Accessible over the internet.
  - Users not responsible for hardware or software updates.
  - Rate limiting/QoS.
  - Example: Microsoft 365