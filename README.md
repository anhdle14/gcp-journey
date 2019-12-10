# A trip up to Google Cloud Platform

## Why

- Google is all about Big Data; huge scale
- Google has many [whitepapers](https://cloud.google.com/whitepapers/) or [AI Thesis](https://ai.google/research/pubs)
- Open source [K8s](https://kubernetes.io/)
- [Site Reliability Engineering](https://landing.google.com/sre/books/)
- [Google on serverless](https://read.acloud.guru/serverless-superheroes-lynn-langit-on-big-data-nosql-and-google-versus-aws-f4427dc8679c?gi=ac1e9aa5b311)
- [Google GCP Software](https://en.wikipedia.org/wiki/Google_data_centers#Software)

> Google is such a developer-focused cloud. You code everything first, and then the tools come later, if ever. Even though their cloud is really pwoerful and really scalable, that's just not the paradigm. Whereas AWS is a DevOps cloud, and i think that's been one of the drivers of their success, around data in particular, because the network admins and DBAs have more references.
> -Lynn Langit

## History

- Some GCP services are from internally
  - Built by Googlers for Google
  - Not orginally for Enterprise
- Purchased some services
- Catch-up to AWS
  - Some functionality missing
  - Avoid some mistakes
  - More willing to be "a cloud" that you use, not just " the cloud" that you use.

## Certifications

### Associate Level

[Associate Cloud Engineer](https://cloud.google.com/certification/cloud-engineer)

> [Notes](./notes/) | [Exam Guides](https://cloud.google.com/certification/guides/cloud-engineer/)

## Structre and Design

- Global
  - [Regions](https://cloud.google.com/about/locations/#regions-tab) | [Regions and Zone Docs](https://cloud.google.com/about/locations/#regions-tab)
  - [Network](https://cloud.google.com/about/locations/#network-tab)
  - [HTTP(S) Load Balancing Concepts](https://cloud.google.com/compute/docs/load-balancing/http/)
  - AWS is intrinsically region-scoped, while GCP is intrisically global
    - **Regional Model**: Simplifies data sovereignty
    - **Global Model**: Easier to handle latency and failures in a global way
  - Network Ingress & Egress
    - **Normal network**: Routes via Internet to edge location closest to _destination_
    - **Google**: Routes so traffic enters from Internet at edge closest to _source_
      - Single global IP address can Load Balance worldwide.
      - Sidesteps many DNS issues.
      - Can not opt for "normal" network routing to reduce price (and functionality).
- Secure
  - Absolutely everything always encrypted at rest.
  - Strong key and identity management.
  - Network encryption
    - All control info encrypted
    - All WAN traffic to be encrypted automaticallly
    - Moving towards encrypting all local traffic within data centers.
  - Distrust the network, anyway
    - BeyondCorp
    - [Google Infrastructure Security Design Overview](https://cloud.google.com/security/infrastructure/design/)
- Huge scale
- Organization
  - Based on **Projects**, similar to AWS accounts.
  - **Projects** own resoures.
  - Resources can be shared with other projects.
  - **Projects** can be grouped and controlled in a hierarchy.
- Pricing
  - 2 types of pricing: **Provisioned** and **Usage**
  - Network traffic
    - Free for Ingress.
    - Charged for Egress (GBs uses).
    - Egress to GCP services sometime free.
      - Depends on the destination service.
      - Depends on the location of that service.
- For developers
  - [Principles of SRE at Google](https://cloud.google.com/security/infrastructure/design/)

> [Inside a Google Data Center](https://www.youtube.com/watch?v=XZmGGAbHqa0)

## Services

### Compute

#### Compute Engine (GCE)

Scope: **Zonal**

- Virtual Machines (VMs) you can rent, on demand
- Infrastructure as a Service (IaaS)
- Can customize CPU and RAM
- Pay by the second (1 minute minimum) for CPUs, RAM
- Automatically cheaper if you keep running it ("sustained use discount")
- Even cheaper for "preemtible" or committing to long-tern usage in a region
- Can add GPUs and paid OSes for extra cost

#### Kubernetes Engine (GKE)

Scope: **Regional**

- Managed K8s cluster for running Docker containers (with autoscaling).
- Kubernetes DNS on by default for service discovery.
- No IAM integration (unlike AWS's ECS).
- Integrates with Persistent Disk for storage.
- Pay for underlying GCE instances, no GKE management fee.

#### App Engige (GAE)

Scope: **Regional**

- Platform as a Service (PaaS) that takes your code and runs it.
- Much more than just compute.
- Flex mode ("App Engine Flex") can run any container.
- Auto-scales based on load
  - Will turn off the last instance if you get no traffic
- Pay for underlying GCE instances

#### CloudFunctions (GCF)

Scope: **Regional**

- Run Nodejs, Python, Go code in response to an event
- Functions as a Service (FaaS), "Serverless"
- Pay for CPU and RAM assigned to function per 100ms (min. 100ms)
- Each function automatically gets an HTTP endpoint
- Can be triggered by GCSs objects, Pub/Sub messages, etc.
- Massively scalable (horizontally)

### Storage

#### Local SSD

Scope: **Zonal**

- Very fast 375GB solid state drives physically attached to the server.
- Can stripe across eight of them for _even_ better performance
- **DATA WILL BE LOST** whenever the instance shuts down (on purpose or not).
  - Can survive a **Live Migration**.
- Like all data at rest, always encrypted.
- Pay for GB-month provisioned (prorated, as always).

#### Persistent Disk (PD)

Scope: **Zonal**

- Flexible, _block-based_ network-attached storage; boot disk for every GCE instance.
- Performance scales with volume size, max way below Local SSD, but still very fast.
- Persistent disks **persist**, and are replicated for durability.
- Can resize while in use (up to 10TB), but will need file system update within VM.
- Snapshots (and images) add even more capability and flexibility with incremental and can use _globally_.
- Not file-based NAS, but can mount to multiple instances if _all_ are read-only.
- Pay for GB/mo provisioned depending of performance class; plus snapshot GB/mo used.

#### Cloud SQL

Scope: **Regional**

- Fully-managed and reliable MySQL and PostgreSQL databases.
- Supports automatic replication, backup, failover, etc.
- Scaling is manual (both vertically or horizontally).
- Effectively pay for underlying GCE instances and PDs, plus baked-in service fess.

#### Cloud Spanner

Scope: **Regional**, **Multi-Regional**, **Global**

- "The first horizontally scalable, strongly consistent, relational database service"
  - From 1 to hundreds or thousands of nodes
  - A minimum of 3 nodes is recommended for production environments
- Chooses Consistency and Partition-Tolerance (CP of CAP theorem)
- But still _high availability_: SLA has 99.999% SLO for multi-region
- Strongly suggested reading from: [Cloud Spanner](https://cloud.google.com/spanner/) or [Cloud Spanner Docs](https://cloud.google.com/spanner/)
- Not based on **fail-over**
- Ridiculously expensive.

#### Big Query

Scope: **Multi-Regional**

- Serverless column-store data warehouse for analytics using SQL.
- Scales internally, so it "can scan TB in seconds and PB in minutes"
- Pay for GBs actually considered (scanned) during queries
  - Attempts to reuse cached results, which are free
- Pay for data stored (GB-months)
  - Relatively inexpensive
  - Even cheaper when table not modified for 90 days (reading still fine)
- Pay for GBs added via streaming inserts
- Strongly suggested reading form [BigQuery under the hood](https://cloud.google.com/blog/products/gcp/bigquery-under-the-hood)

#### Cloud Bigtable

Scope: **Regional**

- Low latency & high throughput NoSQL DB for large operational & analytical apps.
- Supports open-source HBase API; integrates with Hadoop, Dataflow, Dataproc
- Scales seamlessly and unlimitedly
  - Storage autoscales
  - Processing nodes must be scaled manually
- Pay for processing node hours, GB-hours used for storage (cheap HDD or fast SSD)

#### Cloud Datastore

Scope: **Regional**, **Multi-Regional**

- Managed & autoscaled NoSQL DB with indexes, queries, and ACID trans. support.
- NoSQL, so queries can get complicated.
  - No joins or aggregates and must line up with indexes.
  - `NOT`, `OR`, and `NOT EQUALS` (<>, !=) operations not natively supported.
- Automatic "built-in" indexes for simple filtering and sorting (ASC, DESC).
- Manual "composite" indexes for more complicated, but beware them "exploding".
- Pay for GB-months of storage used (including indexes). And IO operations (deletes, reads, writes) performed (i.e. no pre-provisioning)

#### Firebase & Cloud Firestore

Scope:

- Firebase: **Zonal**
- Cloud Firestore: **Multi-Regional**

- NoSQL document stores with ~real-time client updates via managed websockets.
- Firebase DB is single (potentially huge) JSON doc, located only via central US.
- Cloud Firestore has collections, documents, and contained data.
- Free tier (Spark), flat tier (Flame), or usage-based pricing (Blaze)
  - Realtime DB: Pay more for GB/month stored and GB downloaded.
  - Firestore: Pay for operations and much less for storage and transfer.

#### Cloud Storage

Scope: **Regional**, **Multi-Regional**

- Infinitely scalable, fully-managed, versioned, and highly-durable object storage.
- Designed for 99.999999999% (eleven nines) durability.
- Strongly consistent (even for overwrite PUTS and DELETES).
- Integrated site hosting and CDN functionality.
- Lifecycle transistions across classes: Multi-Regional, Regional, Nearline, Coldline.
  - Diffs in cost & availability (99.95%, 99.9%, 99%, 99%), not latency (no thaw delay)
- All classes have same API, so can use gsutil and gcsfuse (but beware).
- Par for ops & GB-months stored by class, + GBs retrieved for Nearline & Coldline.

#### Data Transfer Appliance

- Rackable, high-capacity storage server to physically ship data to GCS
- Ingress only; Not a way to avoid egress carges
- 100TB or 480TB versions
- 480TB/week is faster than a saturated 6Gbps link.

#### Storage Transfer Service

Scope: **Global**

- Copies objects for you, so you don't need to set up a machine to do it.
- Destination is always GCS bucket.
- Source can be S3, HTTP/HTTPS endpoint, or another GCS bucket.
- One-time or scheduled recurring transfers.
- Free to use, but you pay for its actions.

### Networking

#### Google Domains

Scope: **Global**

- Google's registrar for domain names
- Private Whois records
- Built-in DNS or custom nameservers
- Supports DNSSEC
- Email forwarding with automatic setup of SPF and DKIM (for built-in DNS)

#### Cloud DNS

Scope: **Global**

- Scalable, reliable, and managed authoritative DNS service.
- 100% uptime guarantee
- Low latency globally
- Supports DNSSEC
- Manage via UI, CLI, or API
- Pay fixed fee per hosted zone to store and distribute DNS records
- Pay for DNS lookups (i.e. usage)

#### Static IP

Scope: **Regional**, **Global**

- Reserve static IP addresses in projects and assign them to different resources.
- Regional IPs used for GCE instances & Network Load Balancers
- Global IPs used for global load balancers: HTTP(S), SSL proxy, and TCP proxy
  - "Anycast IP" simplifies DNS
- Pay for reserved IPs that are not in use, to discourage wasting them.

#### Load Balancing

Scope: **Regional**, **Global**

- High performance, scalable traffic distribution integrated with autoscaling & Cloud CDN.
- SDN naturally handles spikes without any prewarming; no instances or devices
- Regional Network Load Balancer: health checks, round robin, session affinity
  - Forwarding rules based on IP, protocol (e.g. TCP, UDP), and (optionally) port.
- Global load balancers w/ multi-region failover for HTTP(S), SSL proxy, and TCP proxy.
  - Priotize low-latency connection to region near user, then gently fail over in bits
  - Reacts quickly (unlike DNS) to changes in users, traffic, networking, health, etc.
- Pay by making ingress traffic billable (cheaper than egress) plus hourly per rule.

#### Cloud CDN

Scope: **Global**

- Low latency content delivery based on HTTP(S) CLB and integrated w/ GCE & GCS
- Supports HTTP/2 and HTTPs, but no custom origins ( GCP only)
- Simple checkbox on HTTP(S) Load Balancer config turns this on
- On cache miss, pay origin -> POP "cache fill" egress charges (cheaper for in-region)
- Always pay POP -> client egress charges, depending on location
- Pay for HTTP(S) request volume
- Pay per cache invalidation request (not per resource invalidated)
- Origin costs (e.g. CLB, GCS) can be much lower because cache hits reduce load..

#### Virtual Private Cloud (VPC)

Scope: **Global**

- Global IPv4 unicast Software-Defined Network (SDN) for GCP resources.
- Automatic mode is easy, custom mode gives control.
- Configure subnets (each with a private IP range), routes, firewalls, VPNs, BGP, etc.
- VPC is global and subnets are regional (not zonal!)
- Can be shared across multiple projets in same organization and peered with other VPCs
- Can be enable private (internal IP) access to some GCP services (e.g. BQ, GCS)
- Free to configure VPC (container)
- Pay to use certain services (e.g. VPN) and for network egress.

#### Cloud Interconnect

Scope: **Regional**, **Multi-Regional**

- Options for connecting external networks to Google's network.
- Private connections to VPC via Cloud VPN or Dedicated Interconnect (with SLAs)
- Public Google services (incl. GCP) accessible via External Peering (no SLAs)
  - Direct Peering for high volume.
  - Carrier Peering via a partner for lower volume.
- Significantly lower egress fees.
  - Except Cloud VPN.

#### Cloud VPN

Scope: **Regional**

- IPsec VPN to connect to VPC via public internet for low-volume data connections.
- For persistent, static connections between gateways (i.e not for a dynamic client)
  - Peer VPN gateway must have static IP.
- Encrypted link to VPC (as opposed to Dedicated Interconnect), into one subnet.
- Supports both static and dynamic routing.
- 99.9% availability SLA.
- Pay per tunnel-hour.
- Normal traffic charges apply.

#### Dedicated Interconnect

Scope: **Regional**, **Multi-Regional**

- Direct physical link between VPC and on-premise for high-volume data connections.
- VLAN attachment is private connection to VPC in one regoin, no public GCP APIs.
  - Region chose from those supported by particular interconnect location
- Links are private but not encrypted; can layer your own encryption.
- Redundant connections in different locations, recommended for critical apps
  - Redundancy achieves 99.99% availability; otherwise 99.9% SLA.
- Pay fee per 10Gbps link, plus (relatively small) fee per VLAN attachement.
- Pay reduced egress rates from VPC through Dedicated Interconnect.

#### Cloud Router

Scope: **Regional**

- Dynamic Routing (BGP) for hybrid networks linking GCP VPCs to external networks.
- Works with [Cloud VPN](#cloud-vpn) and [Dedicated Interconnect](#dedicated-interconnect).
- Without Cloud Router you must manage static routes for VPN
  - Changing the IP addresses on either side of VPN requires recreating it.
- Free to setup.
- Pay for usual VPC egress.

#### CDN Interconnect

Scope: **Regional**, **Multi-Regional**

- Direct, low-latency connectivity to certain CDN providers, with chea@er egress.
- Fre external CDNs, not Google's Cloud CDN service
  - Supports Akamai, Cloudfare, Fastly, and more.
- Works for both pull and push cache fills
  - Because It's not for all traffic with that CDN.
- Contact CDN provider to setup for GCP project and which regions.
- Free to enable, then pay less for the egress you configured.

### ML/AI

#### Cloud Machine Learning Engine

Scope: **Regional**

- Massively scalable managed service for training ML models & making predictions.
- Enable apps/devs to use TernsorFlow on datasets of any size; endless use cases.
- Integrates: GCS/BQ (storage), Cloud Datalab (dev), Cloud Dataflow (preprocessing).
- Supports online & batch predictions, priotizing latency (online) & job time (batch).
- Or download models & make predictions anywhere: Desktop, Mobile, or Servers.
- HyperTune automatically tunes model hyperparameters to avoid manual tweaking.
- Training: pay per hour depending on chosen cluster capabilities (ML training units)
- Prediction: Pay per provisioned node-hour plus by prediction request volume made.
- Case study: [How Machine Learning with TensorFlow Enabled Mobile Proof-Of-Purchase at Coca-Cola](https://developers.googleblog.com/2017/09/how-machine-learning-with-tensorflow.html)

#### Cloud Vision API

Scope: **Global**

- Classifies images into categories, detects objects/faces, and find/reads printed text.
- Pre-trained ML model to analyze images and discover their contents.
- Classifies into thousands of categories (e.g. "sailboat", "lion", "Eiffel Tower")
- Upload images or point to ones stored in GCS
- Pay per image, based on detection features requested
  - Higher price for OCR of full documents and finding similar images on the web.
  - Some features are priced together: Labels + SafeSearch, ImgProps + Cropping.
  - Other features priced individually: Text, Faces, Landmarks, Logos.

#### Cloud Speech API

Scope: **Global**

- Automatic Speech Recognition (ASR) to turn spoken work audio files into text.
- Pre-trained ML model for recognizing speech in 110+ languages/variants.
- Accepts pre-recorded or real-time audio, and can stream results back in real-time.
- Enable voice command-and-control and transribing user microphone dictations.
- Handles noisy source audio.
- Optionally filters inappropriate content in some languages.
- Accpets contextual hints: words and names that will likely be spoken.
- Pay per 15 seconds od audio processed.

#### Cloud Natural Language API

Scope: **Global**

- Analyzes text for sentiment, intent, and content classification, and extracts info.
- Pre-trained ML model for understanding what text means, so you can act on it.
- Excellent with Speech API (audio), Vision API (OCR), and Translation API (or built-ins).
- Syntax analysis extracts tokens/sentences, parts of speech & dependency trees.
- Entitiy analysis for sentiment (overall) and entity sentiment detect (+/-) feelings & strength.
- Content classificiation puts each document into one of 700+ predefined categories.
- Charged per request of 1000 characters, depending on analysis types requested.

#### Cloud Translation API

Scope: **Global**

- Translate text between 100+ lnaguages; optionally auto-detects source language.
- Pre-trained ML model for recognizing and translating semantics, not just syntax.
- Can enable people to support multi-regional clients in non-native languages, 2-way
- Combine with Speech, Vision, and Natural Language APIs for powerpul workflows.
- Send plain text or HTML and receive translation in kind.
- Pay per character processed for translation.
- Also pay per character for language auto-detection.

#### Dialogflow

Scope: **Global**

- Build conversational interfaces for websites, mobile apps, messaging, IoT devices.
- Pre-trained ML model and service for accepting, parsing, lexing input & responding.
- Enables useful chatbots and other natural user interactions with your custom code.
- Train it to identify custom entity types by providing a small dataset of examples.
- Or choose from 30+ pre-built agents (e.g. car, currency, dates) as starting template.
- Supports many different languages and platforms/devices
- Free plan has unlimited text interactions and capped voice interactions
- Paid plan is unlimitted but charges per request: more for voice, less for text.

#### Cloud Video Intelligence API

Scope: **Regional**, **Global**

- Annotates videos in GCS (or directly uploaded) with info about what they contain.
- Pre-trained ML model for video scene analysis and subject identification.
- Enables you to search a video catalog the same way you search text documents.
- "Specify a region where processing will take place (for regulatory compliance)"
- **Label Detection**: Detect entities within the video, such as "dog", "flower", or "car".
- **Shot Change Detection**: Detect scene changes within the video.
- **SafeSearch Detection**: Detect adult content within the video.
- Pay per minute of video processed, depending on requested detection modes.

#### Cloud Job Discovery

Scope: **Global**

- Helps career sites, company job boards, etc. to improve engagement & conversion.
- Pre-trained ML model to help job seekers search job posting databases.
- Integrates with many job/hiring systems.
- Lots of features, such as commute distance and recognizing abbreviations/jargon.
  - "Show me jobs with a 30 minute commute on public transportation from my home".

### Big Data & IoT

> Google's Big Data Lifecycle contain 4 stages: **Ingest**, **Store**, **Process & Analyze**, and **Explore & Visualize**.

#### Cloud IoT Core

Scope: **Global**

- Fully-managed service to connect, manage, and ingest data from devices globally.
- **Device Manager** handles devices identity, authentication, config & control.
- **Protocol Bridge** publishes incoming telemetry to Cloud Pub/Sub for processing.
- Connect securely using IoT industry-standard MQRR or HTTPS protocols.
- CA signed certificates can be used to verify device ownership on first connect.
- Two-way device communication enables configuration & firmware updates
- Device shadow enable querying & making control changes while devices offline.
- Pay per MB of data exchanged with devices; no per-device charge.

#### Cloud Pub/Sub

Scope: **Global**

- Infinitely-scalable at-least-once messaging for ingestion, decoupling, etc.
- Global by default, pub/sub from anywhere with consistent latency.
- Messages can be up to 10MB and undelivered ones stored for 7 days - but no Dead Letter Queue.
- Push mode delivers to HTTPS endpoint & succeeds on HTTP success status code
  - "Slow-start" algorithm ramps up on success, and backs off & retries on failures.
- Pull mode delivers messages to requesting clients and waits for ACK to delete.
  - Lets clients set rate of consumption, and supports batching and long-polling.
- Pay for data volume; min 1KB per publish/push/pull request (not by message).

#### Cloud Dataprep

Scope: **Global**

- Visually explore, clean, and prepare data for analysis without running servers.
- "Data Wrangling" (i.e. ad-hoc ETL) for business analytics, not IT pros.
  - Who might otherwise spend 80% of their time cleaning data.
nstan- Managed version of Trifacta Wrangler - and managed by Trifacta, not Google.
- Source data from GCS, BQ, or file upload - formatted in CSV, JSON, or relational.
- Automatically detects schemas, datatypes, possible joins, and various anamalies.
- Pay for underlying Dataflow job, plus management overhead charge.
- Pay for other accessed services (e.g. GCS, BQ)

#### Cloud Dataproc

Scope: **Zonal**

- Batch MapReduce processing via configurablea, managed Spark & Hadoop clusters.
- Handles being told to scale (adding or removing nodes) even while running jobs.
- Integrated with Cloud Storage, BigQuery, BigTable, and some Stackdriver services.
- "Image versioning" switches between versions of Spark, Hadoop, and other tools.
- Pay directly for underlying GCE servers used in the cluster - optionally preemptible.
- Pay a Cloud Dataproc management fee per vCPU-hour in the cluster.
- Best for **moving existing Spark/Hadoop setups to GCP**. Prefer Cloud Dataflow for new data processing piplelines - "Go with the flow".

#### Cloud Dataflow

Scope: **Zonal**

- Smartly-autoscaled & fully-managed batch or stream MapReduce-like processing.
- Released as open-source Apache Beam
- Autoscales & dynamically redistributes lagging work, mide-job, to optimize run time.
- Integrated with Cloud Pub/Sub, Datastore, BQ, Bigtable, CloudML, Stackdriver, etc.
- Dataflow Shuffle service for batch offloads Shuffle ops from workers for big gains
- Effectively pay for underlying worker GCE via consolidated charges
  - Pay per second for vCPUs, RAM GBs PD/PD-SSD (more for streaming)
- Dataflow Shuffle charged for time per GB used.

#### Cloud Datalab

Scope: **Regional**

- Interactive tool for data exploration, analysis, visualization, and machine learning
- Uses Jupyter Notebook
- Supports iterative development of data analysis algorithms in Python/SQL/~JS.
- Pay for GCE/GAE instance hosting and storing (on PD) your notebooks.
- Pay for any other resources accessed (e.g. BigQuery).

#### Cloud Data Studio

Scope: **Global**

- Big Data Visualization tool for dashboards and reporting.
- Meaningful data stories/presentations enable better business decision making.
- Data sources include BigQuery, Cloud SQL, other MySQL, Google Sheets, Google Analytics, Analytics 360, AdWords, DoubleClick, and Youtube channels.
- Visualization include time series, bar charts, pie charts, tables, heat maps, geo maps, scorecards, scatter charts, bullet charts, and area charts.
- Templates for quick start; Customization options for impactful finish.
- Familiar G Suite sharing and real-time collaboration.
- Pay only for services accessed.

#### Cloud Genomics

Scope: **Global**

- Store and process genomes and related experiments.
- Query complete genomic informtaion of large research projects in seconds.
- Process many genomes and experiments in parallel.
- Open industry standards (e.g. from Global Alliance for Genomics and Health)
- Supports "Requester Pays" sharing.

### Identity & Security

Scope: all are **global** by default.

#### Roles

- Roles are collections of Permissions to use or manage GCP resources.
- Permissions allow you to perform certain actions: `Service.Resource.Verb`
- Primitive Roles: **Owner**, **Editor**, and **Viewer**
  - Viewer is read-only.
  - Editor can change things.
  - Owner can control access & billling.
  - Can be useful for dev/test envs, but often too broad.
- Predefined Roles: Give granular access to specific GCP resources (IAM)
  - E.g.: `roles/bigquery.dataEditor,roles/pubsub.subscriber`
- Custom Role: Project or Org-level collections you define of granular permissions.

#### Cloud IAM

- Controls access to GCP resources: authorization, not really authentication/identity.
- Member is user, group, domain, service account, or the public (e.g. "allUsers")
  - Individual Google account, Google group, G Suite/Cloud Identity Domain. Recommended to use Google Groups.
  - Service account belongs to application/instance, not individual end user.
  - Every identity has a unique e-mail address, including service accounts.
- Policies bind Members to Roles at a hierarchy level: Org, Folder, Project, Resource.
- Strongly suggested reading through: [Resource Hierarchy for Acess Control](https://cloud.google.com/iam/docs/resource-hierarchy-access-control)
- IAM is free.

#### Service Accounts

- Special type of Google account that represents an application, not an end user.
- Can be "assumed" by applications or individual users
- Consider resources and permissions required by application; use the **least privileged**
- Can generate and download private keys. Cloud Platform managed keys are better, no direct downloading, auto keys rotation,...

#### Cloud Identity

- Identity as a Service (IDaaS, not DaaS) to provision and manage users and groups.
- Free Google Accounts for non-G-Suite users, tied to a verified domain.
- Centrally manage all users in Google Admin console; support compliance.
- 2-step verification (MFA) and enforcement, including security keys.
- Sync from Active Directory with other Google services (e.g. Chrome)
- Identities can be used to SSO with other apps via OIDC, SAML, OAuth2
- Cloud Identity is free; pay for authorized GCP service usage.

#### Security Key Enforcement

- USB or Bluetooth 2-step verification device that prevents phising.
- Not like just getting a code via email or SMS
- Device also verifies the target service
- Eliminates man-in-the-middle attacks agains GCP credentials.

#### Resource Manager

- Centrally manage & secure organization's projects with custom folder hierarchy.
- Organization resource is root node in hierarchy; folders per your business needs.
- Tied 1:1 to a Cloud Identity / G Suite domain, then owns all newly-created projects
  - Without this organization, specific identities (people) must own GCP projects.
- "Recycle bin" allows undeleting projects.
- Define custom IAM roles ar org level.
- Apply IAM policies at organization, folder, or project levels.
- No charge for this service.

#### Cloud Audit Logging

- Maintains two audit logs for each project and organization
  - Admin Activity (400 days retention, free)
  - Data Access (7 days retendion free; 30 days retention paid)
    - For GCP-visible services (e.g. Can't see into MySQL DB on GCE)
- Stackdriver-family service.

#### Cloud Key Management Service (Cloud KMS)

Scope: **Regional**, **Global**

- Low-latency service to manage & use AES256 encryptions keys, to protect secrets.
- Move secrets out of code (and the like) and into the environment, in a secure way.
- Integrated with IAM & Cloud Audit Logging to authorize & track key usage
- Rotate keys used for new encryption either automatically or on demand.
  - Still keeps old active key versions, to allow decrypting
- Key deleteion has 24 hours delay
- Pay for active key versoins stored over time & key use operations (encrypt/decrypt)

#### Cloud Identity-Aware Proxy (IAP)

- Guards apps running on GCP through identity verification instead of VPN access.
- Based on Cloud Load Balancer & IAM, and only passes authed requests through
- Grant access to any IAM identities, including groups & services accounts.
- Pay for load balancing/ protocol forwarding rules and traffic.

#### Security Scanner

- Free but limited GAE app vulnerability scanner with "very low false positive rates".
- Can identify
  - Cross-site-scripting (XSS)
  - Flash injection
  - Mixed content (HTTP(S))
  - Outdated/insecure libraries

#### Cloud Data Loss Prevention API (DLP)

- Finds and optionally redacts sensitive info in unstructured data streams
- Helps you minimize what you collect, expose, or copy to other systems
- 50+ sensitive data detectors, including: credit card numbers, names, social security numbers, passport numbers, driver's license numbers (US and some other jurisdictions), phone numbers, and other personally identifiable information (PII)
- Data cna be sent directly, or API cna be pointed at GCS, BQ, or Cloud Datastore.
- Can scan both text and images
- Pay for volume of data processed.

### Operation & Management

#### Stackdriver

Scope: **Global**

- Family of services for monitoring, logging, and diagnosing apps on GCP and AWS.
- Basic Tier:
  - Shorter retention and smaller storage.
  - Some additional limitations.
  - GCP only
- Premium Tier
  - Check Stackdriver pricing docs for what constitutes a chargeable resource.
  - GCP & AWS
  - User-defined metrics.
  - VM monitoring agent.

##### Monitoring

- Gives visibility into performance, uptime, & overall health of cloud apps (based on collectd)
- Includes built-in/custom metrics, dashboards, global uptime monitoring, and alerts
- Follow the trail: Links from alerts to dashboards/charts to logs to traces.
- Cross-cloud: GCP, of course, but monitoring agent (premium) also supports AWS
- Alerting policy config includes multi-condition rules & resource organization
- Sends alerts via email, and GCP Mobile App, in Basic Tier
- Premium Tier adds SMS, Slack, PagerDuty, AWS SNS, HipChat, Campfire, webhook
- Pay per time series per month for custom/logs-based metrics allotment overages.

##### Logging

- Store, search, analyze, monitor, and alert on log data & events (based on Fluentd)
- Collection built into some GCP, AWS support with agent, or custom send via API.
- Debug issues via integration with Stackdriver Monitoring, Trace & Error Reporting.
- Create real-time metrics from log data, then alrt or chart them on dashboards.
- Send real-time log data to BigQuery for advanced analytics and SQL-like querying
- Powerful interface to browse, search, and slice log data
- Export log data to GCS to cost-effectively store log archives
- Base allotment is per project per month; pay for premium to get more per hour.

##### Error Reporting

- Counts, analyze, aggregates, and tracks crashes in helpful centrallized interface.
- Smartly aggregates errors into meaningful groups tailored to language/framework
- Instantly alerts when a new app error cannot be grouped with existing ones.
- Link directly from notifications to error details:
  - Time chart, occurrences, affected user count, first/last seend dates, cleaned stack
- Exception stack trace parser knows Java, Python, Javascript, Ruby, C#, PHP, and Go
- Jump from stack frames to source to start debugging.
- Pay for standard Stackdriver Basic or Premium

##### Trace

- Tracks and displays call tree & timings across distributed systesm, to debug performance.
- Automatically captures traces from Google App Enginge
- Trace API and SDKs for Java, Nodejs, Ruby, and Go capture traces from anywhere
- Zipkin collector allows Zipkin tracers to submit data to Stackdriver Trace.
- View aggregate app latency info or dig into individual traces to debug problems
- Generate reports on demand and get daily auto reports per traced app
- Detects app latency shift (degradation) over time by evaluating performance reports
- No charge! But throttled by GCP billing tier.

##### Debugger

- Grabs program state (callback, variables, expressions) in live deploys, low impact
- Logpoints repeat for up to 24h; fuller snapshots run once but can be conditional.
- Source view supports Cloud Source Repository, Github, Bitbucket, local, and upload
- Java and Python supported on GCE, GKE, and GAE (Standard and Flex)
- Node.js and Ruby supported on GCE, GKE, and GAE Flex; Go only on GCE and GKE
- Automatically enabled for Google App Enginge apps; agents available for others.
- Share debugging sessions with others (just send URL)
- Free to use.

#### Cloud Deployment Manager

Scope: **Global**

- Create/manage resources via declarative templates: "Infrastructure as Code".
- Declarative allows automatic parallelization.
- Templates written in YAML, Python, or Jinja 2.
- Supports input and output parameters, with JSON schema.
- Create and update of deployments both support preview.
- Free service; just pay for resources involved in deployments

#### Cloud Billing API

Scope: **Global**

- Programmatically manage billing for GCP projects and get GCP pricing
- Billing config
  - List billing accounts; get details and associated projects for each
  - Enable (associate), disable (disassociate), or change project's billing account
- Pricing
  - List billable SKUs; get public pricing (including tiers) for each
  - Get SKU metadata like regional availability
- Export of current bill to GCS or BQ is possible - but configured via console, not API.

### Development and APIs

#### Cloud Source Repositories

Scope: **Global**

- Hosted private Git repositories, with integrations to GCP and other hosted repos
- Supports standard Git functionality
- No enhanced workflow support like pull requests
- Can set up automatic sync from Github or Bitbucket
- Natural integration with Stackdriver debugger for live-debugging deployed apps.
- Pay per project-user active each month (not prorated)
- Pay per GB-month of data storage (prorated)

#### Container Builder

Scope: **Global**

- Turns source code into build artifacts packaged as a Docker image (or anything!)
- Trigger from Cloud Source Repository (by branch, tag, or commit) or zip in GCS. Or external git with RepoSync features.
- Runs several builds in parallel - even though only one at a time promised
- Dockerfile: super-simple build+push; JSON/YAML file: flexible  & parallel steps
- Push to GCR & export artifacts to GCS - or anywhere your build steps write
- Maintains build logs and build history
- Pay per minute of build time - but free tier is 120 minutes per day.

#### Container Registry (GCR)

Scope: **Regional**, **Multi-Regional**

- Fast, private Docker image storage (based on GCS) with Docker V2 Registry API
- Creates & manages a multi-regional GCS bucket, then translates GCR calls to GCS
- IAM integration simplifies builds and deployments within GCP
- Quick deploys because of GCP networking to GCP
- Quick deploys because of GCP networking to GCS
- Directly compatible with standard Docker CLI; native Docker Login support
- UX integrated with Container Builder & Stackdriver Logs
- UI to manage tags and search for images
- Pay directly for storage and egress of underlying GCS (no overhead)

#### Cloud Endpoints

Scope: **Global**

- Handles autorization, monitoring, logging, and API keys for APIs backed by GCP
- Proxy instances are distributed and hook into Cloud Load Balancer
- Super-fast Extensible Service Proxy (ESP) container based on nginx: <1ms /call
- Uses JWTs and integrates with Firebase, Auth0, and Google Auth
- Integrates with Stackdriver Logging and Stackdriver Trace.
- Extensible Service Proxy (ESP) can transcode HTTP/JSON to gRPC
  - But API needs to be resource-oriented (i.e. RESTful)
- Pay per call to your API.

#### Apigee

Scope: **Global**

- Full-featured & enterprise-scale API management platform for whole API lifecycle.
- Transform calls between different protocols: SOAP, REST, XML, binary, custom.
- Authenticate via OAuth/SAML/LDAP; authorize via Role-Based Access Control
- Throttle traffic with quotas, manage API version, etc.
- Apigee Sense identifies and alerts administrators to suspicious API behaviors.
- Team and businesss tiers are flat monthly rate with API call quotas & feature sets. Also with Enterpise tier.

#### Test Lab for Android

- Cloud Infrastructure for running test matrix across variety of real Android devices.
- Production-grade devices flashed with Android version and locale you specify
- Roto test captures log files, saves annotated screenshots & video to show steps
  - Default completely automatic but still deterministic, so can show regressions
  - Can record custom script
- Can also run Espresso and UI Automator 2.0 instrumentation tests
- Firebase Spark and Flame plans have daily allotment of physical and virtual tests
- Blaze (PAYG) plan charges per device-hour- much less for virtual devices.

## References

- [Sample Architecture](https://gcp.solutions/)
- [Docs](https://cloud.google.com/docs/)
- [Youtube](https://www.youtube.com/user/googlecloudplatform)
- [Blog](https://cloudplatform.googleblog.com/)
- [Codelabs](https://codelabs.developers.google.com/?cat=Cloud)
- [Cheatsheet](https://github.com/gregsramblings/google-cloud-4-words)
