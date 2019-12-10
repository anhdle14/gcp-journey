# Associate Clood Engineer

> Maitainer: Le Anh Duc <anhdle14@icloud.com>

## Prerequisites

1. [GCP Free Tier](https://cloud.google.com/free/docs/gcp-free-tier)

## [Exam Guide](https://cloud.google.com/certification/guides/cloud-engineer/)

### Setting up a cloud solution environment

- Setting up cloud projects and accounts
- Managing billing configuration
- Installing and configuring the CLI

### Planning and configuring a cloud solution

- Planning and estimating GCP product use using the Pricing Calculator
- Planning and configuring compute resources
- Planning and configuring data storage options
- Planning and configuring network resources

### Deploying and implementing a cloud solution

- Deploying and implementing Compute Engine resources
- Deploying and implementing Kubernetes Engine resources
- Deploying and implementing App Engine and Cloud Functions resources
- Deploying and implementing data solutions
- Deploying and implementing networking resources
- Deploying a Solution using Cloud Launcher
- Deploying an Application using Deployment Manager

### Ensuring successful operation of a cloud solution

- Managing Compute Engine resources
- Managing Kubernetes Engine resources
- Managing App Engine resources
- Managing data solutions
- Managing networking resources
- Monitoring and logging

### Configuring access and security

- Managing Identity and Access Management (IAM)
- Managing service accounts
- Viewing audit logs for project and managed services

## Services

### Billing

- Export must be set up per billing account
- Resources should be placed into appropriate projects
- Resources should be tagged with labels
- Billing export is not real-time (delay in hours)
- Setting up `Billing Export`
- Setting up `Billing Alert`
- Setup `Billing Account User`
- Charges before using `Billing Export` won't show up on BigQuery

#### Billing Alert

> To help you with project planning and controlling costs, you can set a budget. Setting a budget lets you track how your spend is growing toward that amount.
>
> You can apply a budget to either a billing account or a project, and you can set the budget at a specific amount or match it to the previous month's spend. You can also create alerts to notify billing administrattors when spending exceeds a percentage of your budget.

#### Billing IAM

> **Role**: Billing Account User
> **Purpose**: Link projects to billing accounts.
> **Level**: Organization or billing account.
> **Use Case*:: This role has very restricted permissions, so you can grant it broadly, typically in combination with Project Creator. These two roles allow a user to create new projects linked to the billing account on which the role is granted.

- Get email address of **non-admin** Google account you control
  - Not the admin account we created when signing up for GCP
  - This will be our "user" account
  - Could be pre-existing Google account
  - Could make Google account for any existing email address
  - Could make new Gmail account

### CloudShell

> Google Cloud Shell provides you with command-line access to your cloud resources directly from your browser. You can easily manage your projects and resources without having to install the Google Cloud SDK or other tools on your system. With Cloud Shell, the Cloud SDK gcloud command-line tool and other utilities you need are always available, up to date and fully authenticated when you need them.

- No SSH key management
- No local terminal
- Docker-containerize shell enviroments (customizable)
- 5 GB of persistent storage (on $HOME directories)
  - root storage is ephemeral.
- Easy-access to perinstalled tools
  - gcloud, bq, kubectl, docker, npm/node, pip/python, ruby, vim, emacs, bash, etc.
- Pre-authorized and always up-to-date

### Data Flows

> Data Flow will be moved, processed, and stored.

If you learn to identify and control data flows, then you will succeed. If you try to memorize situations and responses then you will fail.

> **Three Core Components**: Moving, Processing, and Remembering corresponding to Network, Compute, and Storage. But it is important to understand the prior than the later.

### Cloud Storage

```bash
# Make Bucket
gsutil mb -l northamerica-northeast1 gs://storage-lab-cli

# Get/Set Labels
gsutil label get gs://storage-lab-cli/
gsutil label set bucketlabels.json gs://storage-lab-cli/
gsutil label get gs://storage-lab-cli/

# Append labels
gsutil label ch -l "extralabel:extravalue" gs://storage-lab-cli

# Set/Get Versioning
gsutil versioning get gs://storage-lab-cli/
gsutil versioning set on gs://storage-lab-cli/
gsutil versioning get gs://storage-lab-cli/

# Remove an object
gsutil rm gs://storage-lab-cli/README-cloudshell.txt

# List objects with Versioning
gsutil ls -a gs://storage-lab-cli/

# Copy objects
gsutil cp gs://storage-lab-console/** gs://storage-lab-cli/

# Public an object
gsutil acl ch -u AllUsers:R gs://storage-lab-cli/Selfie.jpg
```

- You cannot rename or change the storage class (Multi-region vs region).
- Users - allUsers permissions to give public access to objects.

### VM

> Disabled by **default**. Must enabled in new projects with `gcloud services enable compute` or `https://console.cloud.google.com/apis/library?project=$PROJECT_ID`.

### CLI

- `gcloud` is a CLI to interact with GCP.
- Best friends with `gsutil` and `bq`.
  - All share same configuration set via `gcloud config`
  - Imagine `gsutil` as `gcloud storage`. And `bq` as `gcloud bigquery`.
- REST API > CLI > Console in term of actions.
- `alpha` and `beta` versions available via `gcloud alpha` and `gcloud beta`.

```md
gcloud <global flags> <service/product> <group/area> <command> <flags> <parameters>
```

> For further details of [global flags](https://cloud.google.com/sdk/gcloud/reference/)

#### [Config Properties](https://cloud.google.com/sdk/docs/properties)

- Set with `gcloud config set <property> <value>`. Check with `gcloud config get-value <property>`. Clear with `gcloud config unset <property>`.
- Can maintain groups of settings and switch between them with [Configurations](https://cloud.google.com/sdk/docs/configurations).
  - List all properties in a configuration with `gcloud config list`.
  - List all configurations with `gcloud config configurations list`.
  - Make new config with `gcloud config configurations create $NAME`.
  - Using config with `gcloud config configurations activate $NAME`. Or just once with `--configuration=$NAME`.

### GCE

- Check metadata with `curl -H "Metadata-Flavor: Google" metadata.google.internal/computeMetadata/v1/`.
- Connect to a VM with `gcloud compute ssh $VM_NAME`. This default to create a private and public in the local filesystem.
- Connect to a VM with Console is a little different. This still generate ssh-keys inside Metadata but it is a timing out ssh keys.
- `Instance Template` is immutable.
- `Instance Group` can be `Single-zone` or `Multi-zone`. But **Unmanaged Instance Group** only available in `Single-zone`.
  - **Managed Instance Group** are managed by GCP, will automatically create, deploy, or delete. NOT **Unmanaged Instance Group**. (e.g. deleting an unmanaged instance group won't delete the instances registered to the group)

> SSH Public keys was injected through the instance's [metadata](https://cloud.google.com/compute/docs/storing-retrieving-metadata#default)

### Security

> Always think of 3A (Authentication, Authorization, Audit/Accounting)
>
> [Security by Design Principle - OWASP](https://www.owasp.org/index.php/Security_by_Design_Principles)

- Authentication features/products:
  - Humans in G Suide, Cloud Identity
  - Application & Services use Service Accounts
  - Identity hierarchy
  - Google Groups
  - Can use Google Cloud Directory Sync (GCDS) to pull from LDAP (no push)
- Authorization features/products:
  - Identity hierarchy (Google Groups)
  - Resource hierarchy (Organization, Folders, Projects)
  - IAM (Roles, Permissions, Bindings)
  - GCS ACLs
  - Billing Management
  - Networking structure & restrictions.
- Audit/ Activity Logs (Stackdriver)
- Billing Export
  - To BigQuery
  - To file
- GCS Object Lifecycle Management

#### Resource Hierarchy

- **Resources** are atomic identities in GCP, things that you create in GCP. **Project** is a container for a set of _resources_. **Folder** contains any number of _projects_ and _sub-folders_. **Organization** is the top level tied to to **G Suite** or **Cloud Identity domain**.

![GCP Cloud Resource Hierarchy](https://cloud.google.com/resource-manager/img/cloud-folders-hierarchy.png)

#### IAM

- [IAM Docs Overview](https://cloud.google.com/iam/docs/overview)
- [IAM Securely](https://cloud.google.com/iam/docs/using-iam-securely)
- [IAM FAQ](https://cloud.google.com/iam/docs/faq)

##### Permissions

- A **Permission** allows you to perform a certain action.
**- Each one follows the form `Service.Resource.Verb`.
**  - i.e. `pubsub.subscriptions.consume`, `pubsub.topics.publish`.
- Usually correspond to REST API methods.

##### Roles

- A Role is a collection of Permissions to use or manage GCP resources.
- [Primitive Roles](https://cloud.google.com/iam/docs/understanding-roles#primitive_roles) - Project-level and often too broad.
  - **Viewer** is read-only.
  - **Editor** can view and change things.
  - **Owner** can also control access & billing.
- [Predefined Roles](https://cloud.google.com/iam/docs/understanding-roles#predefined_roles) - Gives granular access to specific GCP resources
  - i.e. `roles/bigquery.dataEditor`, `roles/pubsub.subscriber`.
- [Custom Role](https://cloud.google.com/iam/docs/understanding-roles#primitive_roles) - Project-level or Org-level collection you define of granular permissions.

##### Members

- A **member** is some Google-known identity
- Each **member** is identified by a unique email address.
  - `user`: Specific Google account.
    - G Suite, Cloud Identity, Gmail, or validated email.
  - `serviceAccount`: Service account for apps/services.
  - `group`: Google group of users and service accounts.
    - "is a named collection of Google accounts and service accounts.". Always use `group`.
    - "Every group has a unique email address that is associated with the group."
    - You never act as the group (But membership in a group can grant capabilities to individuals).
    - Use them for everything.
    - Can be used for owner when within an organization.
    - Can **nest** groups in an organization.
  - `domain`: Whole domain managed by G Suite or Cloud Identity.
  - `allAuthenticatedUsers` - _Any_ Google account or service account.
  - `allUsers` - Anyone on the Internet.

##### Policies

- A policy binds Members to Roles for some scope of Resources.
- Ansers: Who can do what to which thing(s)?
- Attached to some level in the Resource Hierarchy
  - Organization, Folder, Porject, Resource.
- Roles and Members listed in policy, but Resources identified by attachment.
- Always additive ("Allow") and never substractive (no "Deny")
  - "Child policies cannot restrict access granted at a higher level."
- One policy per resouce.
  - Can use `get-iam-policy`, edit the JSON/YAML, and `set-iam-policy` back. (But don't, instead prefer the later.)
  - `gcloud [GROUP] add-iam-policy-binding/remove-iam-policy-binding [RESOURCE-NAME] --role [ROLE-ID-TO-GRANT] --member user:[USER-EMAIL]`
  - Atomic operations are better because changes:
    - Are simpler, less work, and less error-prone (than editing JSON/YAML)
    - _Avoid race conditions, so can happen simultaneously_
  - `gcloud beta compute instances add-iam-policy-binding vm --role roles/compute.instanceAdmin --member user:me@example.com`
- Max 1500 member bindings perr policy
  - Ridiculously high max. Please use groups, instead!
- Usually takes less than 60s to apply changes (both granting and revoking)
  - "May take up to 7 minutes for changes to fully propagate across the system"

```json
{
  "bindings": [
    {
      "role": "roles/owner",
      "members": ["user:bob@example.com"]
    },
    {
      "role": "roles/compute.networkViewer",
      "members": [
        "user:alice@example.com",
        "group:admins@example.com",
        "domain:example2.com",
        "serviceAccount:my-other-app@appspot.gserviceaccount.com"
      ]
    }
  ]
}
```

### Billing Access Control

![Billing Account Overview](https://cloud.google.com/billing/docs/images/access-control-org.png)

- A Billing Account represents some way to pay for GCP service usage
- Type of Resource that lives outside of Projects
- Can belong to an Organization (i.e. be owned by it)
  - Inherits Org-level IAM policies
- Can be linked to projects
  - But does not own them
    - No impact on project IAM
- One project can only be linked to one billing account.

> **Role**: Billing Account User
> **Purpose**: Link projects to billing accounts
> **Level**: Organization or billing account.
> **Use Case**: This role has very restricted permissions, so you can grant it broadly, typically in combination with Project Creator. These two roles allow a user to create new projects linked to the billing account on which the role is granted.

| Role                          | Purpose                                                 | Scope           |
| ----------------------------- | ------------------------------------------------------- | --------------- |
| Billing Account Creator       | Create new self-serve billing accounts.                 | Org             |
| Billing Account Administrator | Manage billing accounts (but not create them).          | Billing Account |
| Billing Account User          | Link projects to billing accounts.                      | Billing Account |
| Billing Account Viewer        | View billing account cost information and transactions. | Billing Account |
| Project Billing Manager       | Link/unlink the project to/from a billing account.      | Project         |

#### Monthly Invoiced Billing

- Get billed monthly and pay by invoice due date.
- Can pay via check or wire transfer.
- Can increase project and quota limits.
- Billing adminstrator of org's current billing account contacts Cloud Billing Support.
  - To determine eligibility
  - To apply to switch to monthly invoicing
- Eligibility depends on
  - Account age
  - Typical monthly spend
  - Country

### Networking

#### Routing

> Routing is about deciding where data should go next.
> Routing is a local decisions to map. Not a full map or path.

- _Software_-Defined Networking (SDN).
- More-general than the OSI 7-layer model of networking.
- Not about any particular routing scheme.
- Only seeting the stage for routing tables/routes.
- On the way to Google's network.
- On the way to the right resource.
- On the way from one resource to another.

#### Network Tiers

![Google Network Tiers](https://storage.googleapis.com/gweb-cloudblog-publish/original_images/image2xjjq.GIF)

#### Load Balancing - Routing to the Right Resource

- **Latency reduction** –> **Cross-Region Load Balancing** (with Global Anycast IPs)
  - Use servers physically close to clients
- **Load Balancing** –> **Cloud Load Balancer** (all types; internal and external)
  - Separate from auto-scaling
- **System Desgin** - **HTTP(S) Load Balancer** (with URL Map)
  - Different servers may handle different parts of the system
  - Especially when using microservices (instead of a monolith)

> **Unicast** vs **Anycast**
> **Unicast**: There is only one unique device in the world that can handle this; send it here.
> **Anycast**: There are multiple devices that could handle this; send it to **any** one–but ideally the closest.

**What about DNS?**

- Name resolution (via the Domain Name System) can be the first step in routing
- But that comes with a number of problems:
  - Layer 4 – Cannot route L4 based on L7's URL paths.
  - Chunky - DNS queries often cached and reused for huge client sets.
  - Sticky – DNS lookup "locks on" and refreshing per request has high cost.
    - Extra latency because each request includes another round-trip!
    - More money for additional DNS request processing
  - No Robust – Relies on the client always doing the right thing.

#### Routing - Among Resources (VPC)

- VPC (global) is Virtual Private Cloud – Your private SDN space in GCP.
  - Not just resource-to-resource – Also manages the doors to outside & peers
- Subnets (regional) create logical spaces to contain resources
  - All Subnets can reach all others – globally, without any need for VPNs.
- Routes (global) define "next hop" for traffic based on destination IP
  - Routes are global and apply by Instance-level Tags, not by Subnet.
  - No route to the internet gateway means no such data can flow
- Firewall Rules (global) further filter data flow that would otherwise route
  - All Firewall Rules are global and apply by Instance-level Tags or Service Acct.
  - Default Firewall Rules are restrictive inbound and permissive outbound.

##### IPs and CIDRs

- Ip address is [0-255].[0-255].[0-255].[0-255] where each piece is `0-255`.
- CIDR block is group of IP addresses specified in `[IP]/xy` notation.
  - Turn IP address into 32-bit binary number.
  - `/xy` in CIDR notation locks highest (leftmost) bits in IP address.
  - `[IP]/32` is single IP address because all 32 bits are locked.
  - `[IP]/24` is 256 IP addresses because last 8 bits can vary.
  - `0.0.0.0/0` means "any IP address" because no bits are locked.
- **RFC19188* defines private (i.e. non-Internet) address ranges you can use:
  - `10.0.0.0/8`, `172.16.0.0/12`, and `192.168.0.0/16`.

##### Creating a VPC network

- **Subnet creation mode**:
  - Custom: Manually.
  - Automatic: Automatically create subnet with new regions. _These IP addresses ranges will be assigned to each region in your VPC network. When an instance is created for your VPC network, it will be assigned an IP from the appropriate region's address range._.
  - No VPC level IP addresses range.
- Default Firewall rules:
  - `*-allow-icmp`
  - `*-allow-internal`
  - `*-allow-rdp`
  - `*-allow-ssh`
  - `*-deny-all-ingress`
  - `*-allow-all-egress`
- Firewall rules can be used with **Target Tags**, **Service Account**, or **All instances in the network**.
  - Egress rules (destination) can only be used with IP range filter.

##### Networking Exam Tips

- Practice CIDR blocks
  - `/16`, `/24`, `/28`, etc...
  - Subnet masks:
    - CIDR `/16` is the same as `255.255.0.0`.
    - CIDR `/24` is the same as `255.255.255.0`.
    - CIDR `/32` is the same as `255.255.255.255`.
  - Practice common port numbers
    - HTTP on 80, HTTPS on 443, SSH on 22, etc...
- You can **edit** a subnet to increase its CIDR range.
- No need to recreate subnet or instances.
- New range must contain old range. (i.e. old range must be subset)
- Google reserves 4 IP address in every subnet.
- Shared VPC
  - In an Organization, you can share VPCs among multiple projects
    - **Host Project**: One project owns the _Shared VPC_.
    - **Service Projects**: Other projects granted access to use all/part of Shared VPC.
  - Let's multiple projects  coexist on same local network (private IP space).
  - Let's centralized team manage network security.

## Tips

## References

- [Exam Guides](https://cloud.google.com/certification/guides/cloud-engineer/)
- [ACG Exam Report Mega Thread](bigquery-public-data:austin_311.311_service_requests)
