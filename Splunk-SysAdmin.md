# SPLUNK SYSTEM ADMINISTRATOR CERTIFIACTION DOCUMENTATION

The Splunk Enterprise System Administration course focuses on administrators who manage a Splunk Enterprise environment. Topics include Splunk license manager, indexers and search heads, configuration, management, and monitoring.

The Splunk Enterprise Data Administration course targets administrators who are responsible for getting data into Splunk. The course provides content about Splunk forwarders and methods to get remote data into Splunk.

The following content areas are general guidelines for the content to be included on the exam:

```
*   Splunk deployment overview
*   License management
*   Splunk apps
*   Splunk configuration files
*   Users, roles, and authentication
*   Getting data in
*   Distributed search
*   Introduction to Splunk clusters
*   Deploy forwarders with Forwarder Management
*   Configure common Splunk data inputs
*   Customize the input parsing process
```

The following topics are general guidelines for the content likely to be included on the exam; however, other related topics may also appear on any specific delivery of the exam. In order to better reflect the contents of the exam and for clarity purposes, the guidelines below may change at any time without notice.

## System Administrator Course
The Splunk System Administrator is primarily **responsible** for system management efforts which include:

- Install, configure, and manage Splunk components 
- Install and manage Splunk apps 
- Manage Splunk licensing 
- Manage Splunk indexes 
- Manage Splunk users and authentication 
- Manage Splunk configuration files 
- Monitor MC and respond to system health alerts

### 1.0 Identify Splunk Components
Splunk scales from a single server to a **distributed infrastructure**

- Accepts any text data as input (Input)
- Parses the data into events  (Parsing)
- Stores events in indexes (Indexing)
- Searches and reports (Searching)
- Authenticates users (RBAC)

#### Single Server
- All functions in a single instance of Splunk 
- For testing, proof of concept, personal use, and learning 
- This is what you get when you download Splunk and install with default settings

#### Splunk Server
- Similar to server in standalone configuration 
- Manage deployment of forwarder configurations

#### Forwarders
- Forwarders collect data and send it to Splunk servers 
- Install forwarders at data source (usually production servers)

#### Search Heads
- Allow users to submit search requests using SPL
- Distribute search requests to the indexers
- Consolidate results and render visualizations of results
- Search time knowledge objects are stored on the search heads
- Examples include: field extractions, alerts, and dashboards

#### Indexer / Search Peers
- Reside on dedicated machines
- Receive, index, and store incoming data from forwarders
- Search data in response to requests received from the search heads

#### Inputs
- Splunk instances that monitor configured inputs and forward the data to the
  index
- Best practice data collection method
- Requires minimal resources and typically installed on the machines that
  produce the data

### 1.1 Splunk Installation Overview

- What software do you install?
- Pre-installation checklist
- Start-up account
- Time synchronization
- Splunk ports
- Linux setting recommendations
- Server hardware recommendations

INSTALLATION

- Splunk directory structure

POST-INSTALLATION CONFIGURATION

Run Splunk at boot
- Configure system settings
- Optionally, enable Monitoring Console

WHAT SOFTWARE DO YOU INSTALL?

Splunk Enterprise includes 
- Indexer
- Search Head
- License Master
- Deployment Server
- Heavy Forwarder
- Master Node

Universal Forwarder includes
- Deployment Client

Installation
- Splunk starts automatically on Windows
- Splunk must be manually started on \*NIX until boot-start is enabled

Run as root:
> ./splunk enable boot-start (init.d)
> ./splunk enable boot-start -systemd-managed 1 (systemd)

Run as root:
> ./splunk enable boot-start –user bob

On Windows, the installer configures Splunk software to start at machine
startup

- Runs as splunkd service and starts child processes
- The service starts and stops like any Windows services
- Can be disabled if needed

### Splunk Deployment - Distibuted

### 2.0 License Management

```
   2.1     Identify license types
   2.2     Understand license violations
```

### 3.0 SPLUNK CONFIGURATION FILES

```
   3.1     Describe Splunk configuration directory structure
   3.2     Understand configuration layering
   3.3     Understand configuration precedence
   3.4     Use btool to examine configuration settings
```

### 4.0 SPLUNK INDEXES

```
   4.1     Describe index structure
   4.2     List types of index buckets
   4.3     Check index data integrity
   4.4     Describe indexes.conf options
   4.5     Describe the fishbucket
   4.6     Apply a data retention policy
```

### 5.0 SPLUNK USER MANAGEMENT

```
   5.1     Describe user roles in Splunk
   5.2     Create a custom role
   5.3     Add Splunk users
```

### 6.0 SPLUNK AUTHENTICATION MANAGEMENT

```
   6.1     Integrate Splunk with LDAP
   6.2     List other user authentication options
   6.3     Describe the steps to enable Multifactor Authentication in Splunk
```

---

## DATA ADMINISTRATOR COURSE

7\.0 Getting Data In

```
   7.1     Describe the basic settings for an input
   7.2     List Splunk forwarder types
   7.3     Configure the forwarder
   7.4     Add an input to UF using CLI
```

8\.0 Distributed Search

```
    8.1     Describe how distributed search works
    8.2     Explain the roles of the search head and search peers
    8.3     Configure a distributed search group
    8.4     List search head scaling options
```

9\.0 Getting Data In – Staging

```
    9.1     List the three phases of the Splunk Indexing process
    9.2     List Splunk input options
```

10\.0 Configuring Forwarders

```
    10.1     Configure Forwarders
    10.2    Identify additional Forwarder options
```

11\.0 Forwarder Management

```
    11.1    Explain the use of Deployment Management
    11.2    Describe Splunk Deployment Server
    11.3    Manage forwarders using deployment apps
    11.4    Configure deployment clients
    11.5    Configure client groups
    11.6    Monitor forwarder management activities
```

12\.0 Monitor Inputs

```
    12.1    Create file and directory monitor inputs
    12.2    Use optional settings for monitor inputs
    12.3    Deploy a remote monitor input
```

13\.0 Network and Scripted Inputs

```
    13.1    Create network (TCP and UDP) inputs
    13.2    Describe optional settings for network inputs
    13.3    Create a basic scripted input
```

14\.0 Agentless Inputs

```
   14.1    Identify Windows input types and uses
   14.2    Describe HTTP Event Collector
```

15\.0 Fine Tuning Inputs

```
   15.1    Understand the default processing that occurs during input phase
   15.2    Configure input phase options, such as sourcetype fine-tuning and character set
           encoding
```

16\.0 Parsing Phase and Data

```
   16.1    Understand the default processing that occurs during parsing
   16.2    Optimize and configure event line breaking
   16.3    Explain how timestamps and time zones are extracted or assigned to events
   16.4    Use Data Preview to validate event creation during the parsing phase
```

17\.0 Manipulating Raw Data

```
   17.1    Explain how data transformations are defined and invoked
   17.2    Use transformations with props.conf and transforms.conf to:
       *   Mask or delete raw data as it is being indexed
       *   Override sourcetype or host based upon event values
       *   Route events to specific indexes based on event content
       *   Prevent unwanted events from being indexed
   17.3    Use SEDCMD to modify raw data
```
