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
The Splunk System Administrator is primarily **responsible for system management** efforts which include:

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

#### Indexer and  Search Peers
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

#### POST-INSTALLATION CONFIGURATION

Run Splunk at boot
- Configure system settings
- Optionally, enable Monitoring Console

#### WHAT SOFTWARE DO YOU INSTALL?

Splunk Enterprise includes
- Indexer
- Search Head
- License Master
- Deployment Server
- Heavy Forwarder
- Master Node

Universal Forwarder includes
- Deployment Client

#### Installation
- Splunk starts **automatically** on Windows
- Splunk must be **manually** started on \*NIX until boot-start is enabled

init.d:
> ./splunk enable boot-start

systemd:
> ./splunk enable boot-start -systemd-managed 1

Run as user:
> ./splunk enable boot-start –user bob

On Windows, the installer configures Splunk software to start at machine
startup

- Runs as splunkd service and starts child processes
- The service starts and stops like any Windows services
- Can be disabled if needed

#### Time Synchronization

- Best practice: Use a time synchronization service such as **NTP**
  - Splunk searches depend on accurate time
- Correct event timestamping is essential
- It is imperative that your Splunk indexer and production servers have standardized time configuration
  - Clock skew between hosts can affect search results

#### Splunk Default Ports

| Usage                      | Splunk Enterprise   | Universal Forwarder |
| -------------------------- | ------------------- | ------------------- |
| splunkd                    | **8089**            | **8089**            |
| Splunk Web                 | **8000**            | n/a                 |
| Web app-server proxy       | **8065**            | n/a                 |
| KV Store                   | **8191**            | n/a                 |
| S2S receiving port(s)      | No default          | n/a                 |
| Any network/http input(s)  | No default          | No default          |
| Index replication port(s)  | No default          | n/a                 |
| Search replication port(s) | No default          | n/a                 |

#### Linux Setting Recommendations

- Increase ulimit settings
- The following OS parameters need to be increased to allow for a large number  of buckets/forwarders/users (https://docs.splunk.com/Documentation/Splunk/latest/Troubleshooting/ulimitErrors)
  - core file size (ulimit -c 1073741824 (1 GB))
  - open files (ulimit -n 48 x default (48 x 1024 = 49,152) (65536))
  - max user processes (ulimit -u 12 x default (12 x 1024 = 12,288) (258048))

- Turn Transparent Huge Pages (THP) off on Splunk Enterprise servers (https://docs.splunk.com/Documentation/Splunk/latest/ReleaseNotes/SplunkandTHP)

#### Reference Servers Hardware

Search Heads: 12 GB RAM and **4 CPUs** quad-core per CPU
Indexer: 12 GB RAM and **12 CPU cores**

Hardware requirements and sizing are discussed in detail in Architecting and Deploying Splunk class (https://docs.splunk.com/Documentation/Splunk/latest/Capacity/Referencehardware)

#### splunkd

- Runs on port **8089** (default) using SSL
- Spawns and controls Splunk child processes (helpers)
  - Splunk Web proxy, KV store, and Introspection services
  - Each search, scripted input, or scripted alert
- Accesses, processes, and indexes incoming data
- Handles all search requests and returns results

#### Splunk Web

- Splunk Web is browser-based user interface
- Provides both a search and management front end for splunkd process
- Runs on port **8000** by default
- No SSL by default

> http://<server_name>:<port>

#### The Splunk Command Line Interface (CLI)

- **splunk** is an executable command in the bin directory
- Same syntax is used on all supported platforms

| Command                           | Operation                                           |
| --------------------------------- | --------------------------------------------------- |
| splunk help                       | Display a usage summary                             |
| splunk help \<object\>            | Display the details of a specific object            |
| splunk [start \| stop \| restart] | Manages the Splunk processes                        |
| splunk start --accept-license     | Automatically accept the license without prompt     |
| splunk status                     | Display the Splunk process status                   |
| splunk show splunkd-port          | Show the port that the splunkd listens on           |
| splunk show web-port              | Show the port that Splunk Web listens on            |
| splunk show servername            | Show the servername of this instance                |
| splunk show default-hostname      | Show the default host name used for all data inputs |

#### Enabling MC in Standalone Mode

- MC runs un-configured in standalone mode by default
- To enable, click *Settings > General Setup > Apply Changes*
- You must Apply Changes to initialize the MC and after any change

#### Enabling MC Platform Alerts

- Effective operation of your Splunk environment is timely identification and notification of critical conditions
  - Any item over 80% mark can’t be good
- MC Alerts Setup provides a number of preconfigured platform alerts Platform alerts are disabled by default
- Tweak parameters such as alert schedule, suppression time, and alert actions

### 2.0 License Management

```
   2.1     Identify license types
   2.2     Understand license violations
```

#### Splunk License Types

- **Enterprise Trial License**
  - Downloads with product
  - Same as full Enterprise license except for **500MB/d** limit
  - Valid **60 days**
  - Sales trial license is a trial Enterprise license of varying size and duration

- **Enterprise license**
  - Purchased from Splunk
  - Full functionality
  - Daily indexing volume limit
  - No-enforcement license

- **Free license**
  - Disables alerts, authentication, clustering, distributed search,
    summarization, and forwarding to non-Splunk servers
  - Allows **500 MB/day** of indexing and forwarding to other Splunk instances

- **Forwarder license**
  - Sets the server up as a heavy forwarder
  - Applies to non-indexing forwarders
  - Allows authentication, but no indexing

#### License Warnings and Violations

- If the indexing exceeds the allocated daily quota in a pool, an alert is raised in Messages (pool warning) on any page in Splunk Web
- The daily license quota resets at midnight
- **5 or more** warnings on an enforced Enterprise license or **3 warnings** on a Free license, in a **rolling 30-day period**, is a violation

#### What counts as daily license quota?

- All data from all sources that is indexed
  - It is the data (full size) that **flows through the parsing pipeline**, per day
  - It is **not** the amount of **storage** used by the indexes

- What does *not* count against your license daily quota?
  - Replicated data (Index Clusters)
  - Summary indexes
  - Splunk internal logs (\_internal, \_audit, etc. indexes)
  - Structural components of an index (metadata, tsidx, etc.)

- **Metrics data** counts against a license at a **fixed 150 bytes** per metric event
  - Draws from the same license quota as event data

#### Adding a License

Can use CLI or Splunk Web (upload or copy/paste)
- License group change requires a restart
- Licenses are stored under SPLUNK_HOME/etc/licenses
- Multiple licenses of the same type are stacked (added together)

> splunk add licenses <path_to_file>

### 3.0 SPLUNK CONFIGURATION FILES

```
   3.1     Describe Splunk configuration directory structure
   3.2     Understand configuration layering
   3.3     Understand configuration precedence
   3.4     Use btool to examine configuration settings
```

#### Splunk Configuration Files

- All .conf files have documentation and examples:
  - SPLUNK\_HOME/etc/system/README
  - \*.conf.spec
  - \*.conf.example
  - Splunk documentation: docs.splunk.com
  - The syntax is case-sensitive

#### Commonly Used Splunk Configuration Files

| Component           | inputs.conf | props.conf | outputs.conf |
| ------------------- | ----------- | ---------- | ------------ |
| Universal Forwarder | Defines what data to collect | Limited parsing such as character encoding, refine MetaData, event breaks | Defines where to forward the data |
| Indexer             | Defines what data to collect including data coming from forwarders | Refine MetaData at event level, event breaks, time extraction, TZ, data transformation | Does not need an outputs.conf as the Indexer does not forward the data |
| Search Head         | Defines what data to collect including Splunk logs | Field Extractions (search time), Lookups, etc | Defines where to forward the data. You may want to send the data to the Indexer especially the internal logs |

### Default vs. Local Configuration

- Splunk ships with default .conf files
  - Stored in the default directories
- Only modify configuration files in a *local* directory
- Avoid storing configurations in SPLUNK\_HOME/etc/system
  - Manage your configs in the appropriate app under etc/apps/\<appname\>/local
- If you don't have an app you need to create one

- default = Overwritten on update
- local   = Preserved on update

#### Index Time vs. Search Time

| When | Context | What |
| ---- | ------- | ---- |
| Index time | Global context | User-independent and background tasks such as inputs, parsing, indexing, etc. |
| Search time | App/User context | User-related activity, such as searching |

- The priority of layered configurations are based on the context

For example:

- Global context: a network input to collect syslog data
- App/User context: Mary's private report in the Search app

#### Index Time Merging of Configurations

- When Splunk starts, configuration files are merged together into a single run time model for each file type
  - Regardless of the number of inputs.conf files in various apps or the system path, only one master inputs configuration model exists in memory at runtime
- If there are no duplicate stanzas or common settings between the files, the result is the union of all files
- If there are conflicts, the setting with the highest precedence is used
  - Remember that **local** always takes precedence over **default**

#### Index Time Precedence Order

Server is performing non-user background processing:

Precedence Order:

1. system/local
2. app/local
3. app/default
4. system/default

**system/local** always wins

Build config file:

```
1. etc/system/default
2. etc/apps/unix/default
3. etc/apps/search/default
4. etc/apps/unix/local
5. etc/apps/search/local
6. etc/system/local
```

If two or more apps at the same level of precedence have conflicts between
them, the conflicts are resolved in **lexicographical** order by app directory
name.

#### Search Time Precedence Order

Depends on search app context.

Search time precedence order:

1. etc/users/USER/APP/local
2. etc/apps/APP/local
3. etc/apps/APP/default
4. etc/apps/search/local
5. etc/apps/search/default
6. etc/system/local
7. etc/system/default

Build config file:

1. system/default
2. system/local
3. apps/search/default
4. apps/search/local
5. apps/APP/default
6. apps/APP/local
7. users/USER/APP/local

#### Search Time Precedence Order (Sharing KOs)

Example: If Mary shares the macro at the app level, then the definition is
moved to the **SPLUNK_HOME/etc/apps/search/local** dirctory.

Example: If the macro is then shared globally then the **.meta** file in the
metadata folder of the search app is updated

local.meta
```
[macros/test2]
access = read : [ * ]
export = system
owner = mary
version = 7.1.0
modtime = 1537527828.580874000
```

#### Configuration Validation Command btool

- splunk btool conf-name list [options]
- Shows on-disk configuration for requested file
- Useful for checking the configuration scope and permission rules
- Run splunk btool check each time Splunk starts  
  - Use --debug to display the exact .conf file location  
  - Add --user=\<user\> --app=\<app\> to see the user/app context layering

Examples:

> splunk help btool  
> splunk btool check  
> splunk btool inputs list  
> splunk btool inputs list monitor:///var/log  
> splunk btool inputs list monitor:///var/log --debug  

Example:
> splunk btool inputs list monitor:///var/log/secure.log --debug

#### Overriding Defaults

There are default settings in SPLUNK\_HOME/etc/**system/default** and SPLUNK\_HOME/etc/apps/**search/default**

- The correct method to override these settings is to do so in the local directory at the same scope
- Only add the items you are overriding — do not make a copy of the entire configuration file

Example:
- To disable a default attribute TRANSFORMS for [syslog]:
# etc/system/local/props.conf
```
[syslog]
TRANSFORMS =
```

### 4.0 SPLUNK INDEXES

```
4.1 Describe index structure
4.2 List types of index buckets
4.3 Check index data integrity
4.4 Describe indexes.conf options
4.5 Describe the fishbucket
4.6 Apply a data retention policy
```

#### What are Indexes?

- Splunk stores the input data as events in indexes
  - SPLUNK\_HOME/var/lib/splunk
- Set in *Settings > Server Settings > General Settings*
- Can override on a per-index basis
- Splunk ships with some indexes already installed

\_**internal** : Splunk indexes its own logs and metrics from its processing here
\_**audit** : Splunk stores its audit trails and other optional auditing information
\_**introspection** : tracks system performance, Splunk resource usage data, and provides MC with performance data
\_**thefishbucket** : contains checkpoint information for file monitoring inputs
**summary** : default index for summary indexing system
**main** : default index for inputs, located in the defaultdb directory

#### Metrics Indexing

- Splunk supports metrics by including an index format for metrics and certain types of log collection
- Supports the following inputs
  - The StatsD extension over UDP/TCP
  - Plain StatsD extension with dimensions over UDP/TCP
  - Collected over HTTPs using HTTP Event Collector (HEC)
  - CSV format files and custom source type
- Event-based indexes are not the same as metrics-based indexes
  - An event index cannot be converted into a metrics index (or vice-versa)
  - Metrics events are not searchable
    - Only works with indexed fields/mstats

> datatype = metric

indexes.conf
```
[metrics_index]
coldPath = $Splunk_DB/metrics_index/colddb
homePath = $Splunk_DB/metrics_index/db
thawedPath = $Splunk_DB/metrics_index/thaweddb
maxTotalDataSizeMB = 512000
datatype = metric
```

#### Why create Indexes?

1. Access control
2. Retention

#### Buckets

- An index stores events in buckets
- A bucket is a directory containing a set of raw data and associated index files
- Buckets have a maximum data size and a time span

Directorys:

**db** = Hot & Warm buckets

Hot buckets are the newest buckets open for writes (readable). Events stream to hot buckets split by time. Transition to warm (read-only) when size or time limits are hit.

**colddb** = Cold buckets

Cold buckets contain the oldest data still in the index (read-only) and can reside on separate partition. Delete is the default action after buckets roll from cold.

**thaweddb** = Buckets restored from archive

One can optionally archive (frozen) buckets in 3rd party store. If a frozen path is defined the data is archived (not searchable.) Delete is the default action after buckets roll from cold.  Older buckets that have been archived can be loaded into thawed area and searched.

#### Hot Buckets

- After data is read and parsed, it goes through the *license meter* and the resulting event is written into a *hot bucket*
- When hot buckets reach their *max size or time span*, they are closed and converted to *warm* status
- Hot buckets also *roll to warm* automatically when the indexer is *restarted*
- Hot and warm buckets are stored in the db directory for the index
- Hot buckets are renamed when rolled to warm

Hot bucket names:  
> hot\_v1\_0*  
> hot_v1_1*

#### Warm and Cold Bucket Names

- Warm bucket names identify the time range of the events contained in that bucket
- When a warm bucket rolls to cold, the entire bucket is moved, maintaining its name
- At search time, Splunk scans the time range on a bucket name to determine whether or not to open the bucket and search its events

Warm bucket names:  
> db\_1389230491\_1389230488\_5  
> db_<youngest_event_in_bucket>\_<oldest_event_in_bucket>\_<unique_id>

#### Freezing: Data Expiration

- The oldest bucket is deleted from an index when:
  - The index's maximum size is reached
  - The bucket's age exceeds the retention time limit
    - All the events in the bucket have expired
- Splunk will *never* exceed the maximum overall size of an index
  - Buckets may be deleted even if they have not reached the time limit
- You can optionally configure the frozen path
  - Splunk moves the bucket's raw data to this location before deletion
  - Frozen buckets are *not searchable*
- Frozen data can be brought back (thawed) into Splunk if needed

#### Calculating Index Storage

Allocate disk space to meet data retention needs

- Daily Rate * Compression Factor * Retention Period (in days) + Padding
- 900 GB (5 GB x 180 days) * .5 (CF) + 50 GB (padding) = 500 GB

#### Adding an Index

Splunk Web: *Settings > Indexes > New Index*

CLI:
> splunk add index \<index\_name\>

Default Hot/Warm/Cold bucket size: Auto (750MB)

Use **auto_high_volume** when daily volume is >10GB

Directory default:  
$SPLUNL_DB/*indexname*/[db|colddb|thaweddb]

#### Index Data Integrity Check

- Provides an ability to validate that data has not been tampered with after indexing (https:docs.splunk.com/Documentation/Splunk/latest/Security/Dataintegritycontrol)
- When enabled, produces calculated hash files for auditing and legal purposes

Works on index level (including clustering)
- Does not protect in-flight data from forwarders
- To prevent data loss, use the indexer acknowledgment capability (useACK)

- To verify the integrity of an index/bucket:
> splunk check-integrity -bucketPath [bucket\_path] [verbose]  
> splunk check-integrity -index [index] [verbose]

- To re-generate hash files:
> splunk generate-hash-files [-bucketPath|-index]

#### indexes.conf

```
[securityops]
homePath = /mnt/ssd/splunk/itops/db
coldPath = /mnt/san/splunk/itops/colddb
thawedPath = $SPLUNK_DB/itops/thaweddb
maxDataSize = auto_high_volume
maxTotalDataSizeMB = 307200
enableDataIntegrityControl = 0
```

#### Strict Time-based Retention Policies

- Issues to consider:
  - Splunk freezes entire buckets, not individual events
  - If a bucket spans more than one day, you can't meet the 90 day requirement
- Configuration option:
  - *frozenTimePeriodInSecs* = 7776000 (90 days)
  - *maxHotSpanSecs* = 86400 (24 hours)

#### Last Chance Index

- Gives ability to define a last chance index for events destined for non- existent indexes
- If this setting is not defined or is empty, it will drop such events
- Defaults to empty

> lastChanceIndex = \<index name\>

#### The Fishbucket - Re-indexing Data

- The fishbucket index stores the checkpoint information for monitor inputs
- To reset the individual input checkpoint, use the btprobe command:

> splunk cmd btprobe –d SPLUNK\_HOME/var/lib/splunk/fishbucket/splunk\_private\_db --file \<source\> --reset

#### Restoring a Frozen Bucket

To thaw a frozen bucket:
1. Copy the bucket directory from the frozen directory to the index's thaweddb directory
2. Run splunk rebuild \<path to thawed bucket directory\> (Does not count against license)
3. Restart Splunk

- Events in thaweddb are searchable along with other events
- They will not be frozen, nor do they count against the index max size
- Delete the thawed bucket directory when no longer needed and restart Splunk

### 5.0 SPLUNK USER MANAGEMENT

```
   5.1     Describe user roles in Splunk
   5.2     Create a custom role
   5.3     Add Splunk users
```

*hm ...?*

### 6.0 SPLUNK AUTHENTICATION MANAGEMENT

```
   6.1     Integrate Splunk with LDAP
   6.2     List other user authentication options
   6.3     Describe the steps to enable Multifactor Authentication in Splunk
```

*hm ..?*

### 7.0 Getting Data In

```
   7.1     Describe the basic settings for an input
   7.2     List Splunk forwarder types
   7.3     Configure the forwarder
   7.4     Add an input to UF using CLI
```
#### Forwarders and Indexers

In a production environment
- Splunk indexers run on dedicated servers
- The data you want is on remote machines

Install Splunk forwarders on the remote machines to
- Gather the data
- Send it across the network to the Splunk indexer(s)

Indexers listen on a receiving port for the forwarded data
> Forwarder index.conf -> outputs.conf --> Indexer:9997 inputs.conf

#### Deployment Server/Forwarder Management

In larger or production environments, forwarders can be managed remotely

- Splunk Deployment Server provides a Forwarder Management interface
  - A centralized configuration management tool to manage forwarder configuration
  - Allows forwarders to be managed in groups (server classes)
- In this class, we will set up a single forwarder manually for testing
- Deployment server is discussed in detail in the Splunk Enterprise Data Administration class

#### Forwarder Configuration Steps

1. Set up a receiving port on each indexer (9997)
   - It is only necessary to do this once
2. Download and install Universal Forwarder software
3. Set up forwarding on each forwarder
   - Either manually or using Deployment Server (DS)
4. Add inputs on forwarders, using one of the following:
   - Forwarder management
   - CLI:
     > splunk enable listen <port>

   - Edit inputs.conf manually
     > [splunktcp://portNumber]


#### Defining Target Indexers on the Forwarder

- Execute on the forwarder for each destination indexer:
> splunk add forward-server indexer:receiving-port  
> example, splunk add forward-server 10.1.2.3:9997

configures the outputs.conf as:
```
[tcpout]
defaultGroup = default-autolb-group
[tcpout-server://10.1.2.3:9997]
[tcpout:default-autolb-group]
disabled = false
server = 10.1.2.3:9997
```

#### Add UF Input by CLI

> ./splunk add monitor /var/log/  
> ./splunk add monitor -source c:\Windows\windowsupdate.log -index newindex

### 8\.0 Distributed Search

```
    8.1     Describe how distributed search works
    8.2     Explain the roles of the search head and search peers
    8.3     Configure a distributed search group
    8.4     List search head scaling options
```

#### Distributed Search

- Production servers with universal forwarders send data to indexers
- Indexers (peers) store their portion of the data
- Users log on to the search head and run reports
  - The search head dispatches searches to the peers
  - Peers run searches in parallel and return their portion of results
  - The search head consolidates the individual results and prepares reports

#### Setting Up Distributed Search

- Install Splunk on each search head and peers (indexers)
- Set up the same indexes on all peers
- All search heads and peers should use a **license master**
- Add a user to each peer with a role that has the *edit\_user* capability
  - Used only for authenticating a search head to the peers
  - You should create an account on each peer for this purpose
- On the search head, configure search peers by selecting: *Settings > Distributed search*
  - Distributed search is **turned on** by default, so just add search peers

#### Knowledge Bundles and Replication

- Knowledge bundles are distributed to search peers by the search head when a distributed search is initiated
- They contain the knowledge objects required by the indexers for searching
- Knowledge bundles’ locations:  
  $SPLUNK\_HOME/var/run (on the search head)  
  $SPLUNK\_HOME/var/run/searchpeers (on the search peer)

- Replication status of knowledge bundles can be viewed from **Replication Status** column of the Splunk Web home page *Settings > Distributed search > Search peers*

#### How Many Search Heads?

- One dedicated search head can handle around **8 to 12** simultaneous searches (ad hoc or scheduled)
  - Exact numbers depend on types of searches and the hardware of the search head; especially number of CPU cores
- Search heads can be added to the distributed group at any time
- Search heads can be **dedicated** or **clustered**
- Dedicated search heads don't share knowledge objects (separate small teams)
- Search head cluster shares a common set of knowledge objects (large teams)

#### Search Peer Quarantine

If a search peer is experiencing performance issues, it can be quarantined from participating in future searches
- Allows you to perform live troubleshooting by not stopping the search peer
- It is prevented from performing new searches but continues to attempt to service any currently running searches
- Only affects the relationship between search peer and search head

From the search head, run the following CLI command:
> splunk edit search-server –auth <user>:<password> <host:<port> -action quarantine

#### Distributed Search Best Practice

**Forward all search head indexes** to the search peer (indexer) layer
- Simplifies the process of managing indexes
- Can diagnose from other search heads if one goes down
- Allows other search heads to access all summary indexes

outputs.conf
```
[indexAndForward]
index = false

[tcpout]
defaultGroup = default-autolb-group
forwardedindex.filter.disable = true
indexAndForward = false
[tcpout:default-autolb-group]
server=idx1:9997,idx2:9997
```

- Splunk recommends that you dedicate a host for each role
- Disable Web on instances that don’t need the web interface
> ./splunk disable webserver

- Deployment Server can be used to manage Splunk Instance remotely

---
## DATA ADMINISTRATOR COURSE

### 9.0 Getting Data In – Staging

```
    9.1     List the three phases of the Splunk Indexing process
    9.2     List Splunk input options
```

#### Data Input Types

Splunk supports many types of data input

**Files and directories** : monitoring text files and/or directory structures containing text files  
**Network data** : listening on a port for network data  
**Script output** : executing a script and using the output from the script as the input  
**Windows logs** : monitoring Windows event logs, Active Directory, etc.  
**HTTP** : using the HTTP Event Collector  
And more...

#### Default Metadata Settings

- When you index a data source, Splunk assigns metadata values
- The metadata is applied to the entire source
- Splunk applies defaults if not specified
- You can also override them at input time or later

| Metadata   | Default                                                           |
| ---------- | ----------------------------------------------------------------- |
| source     | Path of input file, network hostname:port, or script name         |
| host       | Splunk hostname of the inputting instance (usually a forwarder)   |
| sourcetype | Uses the source filename if Splunk cannot automatically determine |
| index      | Defaults to main                                                  |

#### Input Phase vs. Parsing Phase

| Input phase                            | Parsing phase                     |
| -------------------------------------- | --------------------------------- |
| Most efficient, but low discrimination | Less efficient, but finer control |
| Acquires data from source              | Breaks data into events with timestamps |
| Sets initial metadata fields: source, sourcetype, host, index, etc. | Applies event-level transformations |
| Converts character encoding            | Fine-tunes metadata settings from inputs phase |
| Operates on the entire data stream     | Operates on individual events |
| Most configuration done in inputs.conf on forwarder (Some configuration is in props.conf) | Most configuration done in props.conf on indexer (Also: transforms.conf) |


#### Index-Time Process

Splunk index-time process (data ingestion) can be broken down into three phases:

1. **Input phase**: handled at the source (usually a forwarder)
   - The data sources are being opened and read
   - Data is handled as streams and any configuration settings are applied to the entire stream

2. **Parsing phase**: handled by indexers (or heavy forwarders)
   - Data is broken up into events and advanced processing can be performed

3. **Indexing phase**:
   - License meter runs as data is initially written to disk, prior to compression
   - After data is written to disk, it cannot be changed

#### Understanding Source Types

Source type is Splunk’s way of categorizing the type of data Splunk indexing processes frequently reference source type
- Many searches, reports, dashboards, apps, etc. also rely on source type
- Splunk will try to determine the source type for you
  - If Splunk recognizes the data, then it assigns one from the pretrained sourcetypes
  - If one is explicitly specified, then Splunk will not try to determine the source type You can explicitly set source type with Splunk Web, CLI, or by modifying inputs.conf
  - Otherwise, Splunk uses the name of the file as the source type
- You can also add source types by installing apps, which often define source types for their inputs

### 10.0 Configuring Forwarders

```
    10.1     Configure Forwarders
    10.2    Identify additional Forwarder options
```

#### Forwarder Configuration Files

Forwarders require outputs.conf
- outputs.conf points the forwarder to the receiver(s)
- Can specify additional options for load balancing, SSL, compression, alternate indexers, and indexer acknowledgement

outputs.conf
```
[tcpout:splunk_indexer]
server = 10.1.2.3:9997
```

inputs.conf
```
[splunktcp://9997]
```

#### Defining Target Indexers on the Forwarder

Execute on the forwarder:
> splunk add forward-server indexer:receiving-port

For example:
> splunk add forward-server 10.1.2.3:9997

configure the outputs.conf as follows>

```
[tcpout]
defaultGroup = default-autolb-group
[tcpout-server://10.1.2.3:9997]
[tcpout:default-autolb-group]
disabled = false
server = 10.1.2.3:9997
```

#### Testing the Connection

**Indexer:**

From CLI, run
> splunk display listen

**Forwarder:**

To view current forwarder to indexer configuration, run
> splunk list forward-server

To remove the target indexer setting, run
> splunk remove forward-server indexer:port

#### Troubleshooting Forwarder Connection

Is the forwarder sending data to the indexer?
> tail –f var/log/splunk/splunkd.log | egrep 'TcpOutputProc|TcpOutputFd'

Does the indexer receive any data on the listening port?
> index=\_internal sourcetype=splunkd component=TcpInputConfig OR (host=\<uf\> component=StatusMgr)

#### Additional Forwarding Options

- Compressing the feed
- Securing the feed
- Automatic load balancing to multiple indexers
- Forwarder queue size
- Indexer acknowledgement to forwarder

#### Compressing the Feed

> compressed = true

#### Securing the Feed – SSL

Turning on SSL:
- Can increase the CPU usage
- Automatically compresses the feed
- Encrypts password

outputs.conf

```
[tcpout:splunk_indexer]
server = 10.1.2.3:9997
sslPassword = ssl_for_m3
sslCertPath = SPLUNK_HOME/etc/auth/cert1/server.pem
sslRootCAPath = SPLUNK_HOME/etc/auth/cert1/cacert.pem
```

inputs.conf

```
[splunktcp-ssl:9997]
[ssl]
password = ssl_for_m3
serverCert = SPLUNK_HOME/etc/auth/cert1/server.pem
rootCA = SPLUNK_HOME/etc/auth/cert1/cacert.pem
```

#### Notes About SSL

- Splunk uses OpenSSL to generate its default certificates
- Default certificate password is *password*
- You should use external certs OR create new ones using Splunk’s OpenSSL

#### Automatic Load Balancing

Automatic load balancing switches from server to server in a list
- Switch happens only when the forwarder detects an EOF
- Time-based load balancing default frequency is **30 seconds**
- Volume-based load balancing is set on how much data a forwarder sends before switching
- Load balancing is the key to making distributed search or clustering work efficiently

outputs.conf

```
[tcpout:splunk_indexer]
server = splunk1:9997,splunk2:9997,splunk3:9997
```

#### Defining Event Boundary on UF

- **Normally**, event boundary is determined on the **indexer**
- The UF does not know when it is safe to switch to the next indexer, unless:
  - An EOF is detected
  - Or, a short break in IO activity

- Potential side effects
  - Streaming data (syslog) can prevent a UF from switching
  - A multi-line data (log4j) can result in event splits. Especially if the application has pauses in writing its log file

- Solution
  - Enable event breaker on the UF per sourcetype

Add the event breaker settings on UF per sourcetype in props.conf

**Single event**
```
[my_syslog]
EVENT_BREAKER_ENABLE = true
```

**Multi-line event**
```
[my_log4j]
EVENT_BREAKER_ENABLE = true
EVENT_BREAKER = ([\r\n]+)\d\d\d\d-\d\d-\d\d
```

#### Caching/Queue Size in outputs.conf

**maxQueueSize** = 500kb (default) is the maximum amount of data the forwarder queues if the target receiver cannot be reached

#### Indexer Acknowledgement

Guards against loss of data when forwarding to an indexer
- Forwarder resends any data not acknowledged as "received" by the indexer
- Disabled by default
- Can also be used for forwarders sending to an intermediate forwarder
- Automatically increases the wait queue to 3x the size of maxQueueSize to meet larger space requirement for acknowledgement

outputs.conf

```
[tcpout:splunk_indexer]
useACK = true
```

#### Useful Commands

| Command | Operation |
| ------- | --------- |
| From the forwarder  |
| ./splunk add forward-server | Configures the forwarder to connect the receiving indexer |
| ./splunk list forward-server | Displays the current receiving indexer configuration  |
| ./splunk remove forward-server | Removes the receiving indexer from the forwarder |
| From the Receiver |
| ./splunk enable listen | Configures the Splunk receiving port number |
| ./splunk display listen | Displays the current Splunk receiving port number |

### 11.0 Forwarder Management

```
    11.1    Explain the use of Deployment Management
    11.2    Describe Splunk Deployment Server
    11.3    Manage forwarders using deployment apps
    11.4    Configure deployment clients
    11.5    Configure client groups
    11.6    Monitor forwarder management activities
```
#### Deployment Management

Deployment Server is a built-in tool for managing configuration of Splunk instances
- Allows you to manage remote Splunk instances centrally
- Requires an Enterprise License
- Handles the job of sending configurations (inputs.conf, outputs.conf, etc.) packaged as apps
- Can automatically restart remote Splunk instances

Forwarder management is a graphical interface on top of deployment server
- Monitoring Console Forwarder dashboards help you monitor the deployment

Best Practice: The Deployment Server should be a dedicated Splunk instance

#### Deployment Server Components

Deployment Apps

- Configuration files like inputs.conf packaged into apps to be deployed to the clients
- These apps reside in $SPLUNK\_HOME/ etc/deployment- apps/

Server Class

- A server class defines what app(s) should be deployed to which client(s)
- When you create a server class they get saved in serverclass.conf

Deployment Clients

- Splunk instances (Enterprise or UF) that are connected to the DS and are phoning home
- You establish the connection from the Deployment Client

#### Ports and Configurations

deploymentclient.conf
- Specify the clients and apps

Deployment clients obtain deployment apps (configuration bundle) from the Deployment Server

etc/<apps>/local/serverclass.conf
- map clients to apps

etc/deployment-apps/<app>/local/(outputs.conf, inputs.conf,etc)
- app repository location

Clients to Deployment Server Connection: **Managment Port 8089**

Client to Indexer Data Connection: **Receiving Port (9997)**

#### What's in a Deployment App?

Deployment Server/Forwarder Management works by deploying one or more apps from the SPLUNK\_HOME/etc/deployment-apps folder to the remote forwarders (clients)
- They are deployed to the forwarder's SPLUNK\_HOME/etc/apps folder by default

An app can have configuration files, scripts, and other resources
- Apps must follow the normal app structure and rules.  
  Required files:
  - app.conf (in default or local)
  - local.meta (in metadata)

#### What’s a Server Class?

A *server class* maps a group of clients to one or more deployment apps

A set of clients can be grouped based on:
- Client name
- Host name
- IP address
- DNS name
- Machine types

#### Enabling Forwarder Management

Overview of how to set up Forwarder Management in your implementation:
1. On the deployment server, add one or more apps in SPLUNK\_HOME/etc/deployment-apps
2. In the Forwarder Management UI, create one or more server classes
3. On forwarders, run
> splunk set deploy-poll <deployServer:port>

#### Configuring Deployment Clients

Run this during forwarder installation or later splunk
> set deploy-poll deployServer:port

Creates deploymentclient.conf in SPLUNK_HOME/etc/system/local

> splunk restart

deploymentclient.conf
```
[target-broker:deploymentServer]
targetUri = splunk_server:8089

[deployment-client]
clientName = webserver_1
phoneHomeIntervalInSecs = 600
```

On the deployment client
- Use splunk show deploy-poll to check the deployment server settings
- Use splunk list forward-server to check the indexer destination settings

#### Usefull Commands

| Command | Operation |
| ------- | --------- |
| From the Deployment Client |
| ./splunk set deploy-poll | Connects the client to the deployment server and management port |
| ./splunk show deploy-poll | Displays the current deployment server and management port |
| ./splunk disable deploy-client | Disables the deployment client |
| From the DS |
| ./splunk reload deploy-server | Checks all apps for changes and notifies the relevant clients |
| ./splunk list deploy-clients | Displays information about the deployment clients |
| ./splunk list forward-server | Displays the current forward server configuration |

### 12.0 Monitor Inputs

```
    12.1    Create file and directory monitor inputs
    12.2    Use optional settings for monitor inputs
    12.3    Deploy a remote monitor input
```

#### Monitoring Files

A monitor input defines a specific file as the data source
- The current content of the file is ingested
- The file is continuously monitored for new content
- Splunk tracks file status and automatically continues monitoring at the correct file location after a restart

The file monitor supports any text file format, such as:
- Plain text data files
- Structured text files, such as CSV, XML, JSON
- Multi-line logs, such as Log4J
- Splunk can also read files compressed with gzip

#### Monitoring Directories

A monitor input can define a directory tree as the data source
- Splunk recursively traverses through the directory structure
- All discovered text files are consumed, including compressed files
  - Unzips compressed files automatically before ingesting them, one at a time
- Any files added to the directory tree in the future are included
  - Automatically detects and handles log file rotation

The input settings are applied to all files in the directory
- sourcetype, host and index -- if specified -- are applied to all files in the tree
- source= the file name (absolute path)
- Automatic sourcetyping is recommended for directories that contain mixed file types
  - Can override exceptions manually
  - Automatic sourcetyping is disabled if the sourcetype attribute is defined

#### Monitor Input Options in inputs.conf

inputs.conf
```
[monitor://<path>]
disabled=[0|1|false|true]
sourcetype=<string>
host=<string>
index=<string>
blacklist=<regular expression>
whitelist=<regular expression>
```

*Source* (after monitor:// in stanza header) is an absolute path to a file or directory
- Can contain a wildcard

All attributes (sourcetype, host, index, etc.) are **optional**

Defaults apply if omitted
- Default host is defined in etc/system/local/inputs.conf
- Default source is the fully-qualified file name
- Default sourcetype is automatic

#### File Pathname Wildcards

- Monitor stanzas in inputs.conf support two wildcards to help you specify the files/directories you want to index

- "..." The ellipsis wildcard recurses through directories and subdirectories to match.

- "\*" The asterisk wildcard matches anything in that specific directory path segment but does not go beyond that segment in the path. Normally it should be used at the end of a path.

#### Additional Options

Whitelist and Blacklist
- Regular expressions to filter files or directories from the input
- In case of a conflict between a whitelist and a blacklist, the blacklist prevails

Follow tail (followTail)
- Splunk ignores existing content in the file, but indexes new data as it arrives
- DO NOT leave followTail enabled indefinitely

Consider using ignoreOlderThan, if applicable
- A file whose modtime falls outside this time window will not be indexed
  - After a file is ignored, it will never be considered as an input again, even if it is updated
  - *ignoreOlderThan* = 60d

#### Overriding the Host Field

You can override the default host value
- Explicitly set the host value
- Set the host based on a directory name
- Set the host based on a regular expression

Setting the Host:
> host\_segment = \<integer\>  
> host\_regex = \<regular expression\>  

Set host\_regex to *\w+(vmail.+)\.log$* selects the second part of the log file name as its host name

#### Creating a Remote Data Input

- After deployment clients are working, you can create deployment apps for configuring inputs on the clients
- Uses deployment server to distribute the inputs.conf

#### Editing Inputs

- Editing inputs.conf only applies changes to new data, it does not change the data

- Splunk monitor (file or directory) inputs are tracked by the fishbucket
  - Ensures that data is not missed or duplicated
  - Keeps checkpoints and other information for each input

- Splunk does not re-index when inputs.conf is edited
- To re-index
  - Delete the old (erroneous) data on the indexer(s)
    - May require assistance from the System Admin
  - Change the inputs.conf on the deployment server (or forwarders)
  - Reset the fishbucket checkpoint on the involved forwarders
  - Restart Splunk forwarders

#### The Fishbucket and btprobe Command

To reset the checkpoint for an individual input, use the btprobe command:

> splunk cmd btprobe -d SPLUNK_HOME/var/lib/splunk/ fishbucket/splunk_private_db --file <source> --reset

Requires stopping the forwarder or indexer

It is possible to clear all checkpoints, but this is only recommended for test environments:
> splunk clean eventdata \_thefishbucket

### 13.0 Network and Scripted Inputs

```
    13.1    Create network (TCP and UDP) inputs
    13.2    Describe optional settings for network inputs
    13.3    Create a basic scripted input
```

inputs.conf
```
udp://[host:]port]
connection_host = dns
sourcetype=<string>
[tcp://[host:]port]
connection_host
```

Network Input: Host Field

- The connection_host attribute defines how the host field is set:
  - dns (UI default)
    - The host is set to a DNS name using reverse IP lookup
  - ip
    - The host is set to the originating host's IP address
  - none (Custom)
    - Explicitly set the host value

Network Input: acceptFrom

- acceptFrom = \<network\_acl\>
  - List address rules separated by commas or spaces
    - A single IPv4 or IPv6 address
    - A CIDR block of addresses
    - A DNS name
    - A wildcard ‘\*’ and ‘!’

Network Input: Memory Queues

inputs.conf
```
[tcp://9001]
queueSize=10MB
```

- This is a memory-resident queue that can buffer data before forwarding
- Defaults to **500KB**
- Useful if the indexer cannot always receive the data as fast as the forwarder is acquiring it
- Independent of the forwarder's maxQueueSize attribute defined in outputs.conf

Network Input: Persistent Queues

inputs.conf
```
[tcp://9001]
queueSize=10MB
persistentQueueSize=5GB
```

- Provides file-system buffering of data
- Adds additional buffer space after memory buffer
- You must set a queueSize first
- A persistent queue is written to disk on the forwarder in SPLUNK_HOME/var/run/splunk/...
- Useful for high-volume data that must be preserved in situations where it cannot be forwarded, such as if the network is unavailable, etc.

#### Scripted Inputs

- Splunk can run:
  - Shell scripts (.sh) on \*nix
  - Batch (.bat) and PowerShell (.ps1) on Windows
  - Python (.py) on any platform

#### Scripted Input Stanza

Splunk only executes scripts from
1. SPLUNK\_HOME/etc/apps/<app_name>/bin,
2. SPLUNK\_HOME/bin/scripts, OR
3. SPLUNK\_HOME/etc/system/bin

inputs.conf
```
[script://<cmd>]
passAuth = <username>
host = <as indicated>
source = <defaults to script name>
sourcetype = <defaults to script name>
interval = <number in seconds or cron syntax>
```

To test the script from the Splunk perspective, run
> splunk cmd scriptname

inputs.conf
```
[script://./bin/myvmstat.sh]
disabled = false
interval = 60.0
source = vmstat
sourcetype = myvmstat
```

- You can declare the same queueSize and persistentQueueSize attributes for a script stanza as for network (TCP and UDP) inputs
- Buffers data on the forwarder when the network or indexer is not available

### 14.0 Agentless Inputs

```
   14.1    Identify Windows input types and uses
   14.2    Describe HTTP Event Collector
```
#### Windows-Specific Inputs

- Windows OS maintains much of its state data (logs, etc.) in binary format
  - Windows provides APIs that enable programmatic access to this information

- Splunk provides special input types to access this data
  - All other input types are also supported
  - Data can be forwarded to any Splunk indexer on any OS platform
  - Windows Universal Forwarder can run as domain user without the local administrator privilege

#### Windows-Specific Input Types

| Input Type | Description |
| ---------- | ----------- |
| Event Log\* | Consumes data from the Windows OS logs |
|Performance\* | Consumes performance monitor data |
|Active Directory | Monitors changes in an Active Directory server |
|Registry | Monitors changes in a Windows registry |
|Host | Collects data about a Windows server |
|Network | Monitors network activity on a Windows server |
|Print | Monitors print server activity |

#### Local Windows Inputs Syntax

inputs.conf
```
[admon://name]
[perfmon://name]
[WinEventLog://name]
[WinHostMon://name]
[WinNetMon://name]
[WinPrintMon://name]
[WinRegMon://name]
```

inputs.conf
```
[WinEventLog://Security]
checkpointInterval = 5
current_only = 0
disabled = 0
start_from = oldest
```

Can configure up to 10 whitelist and 10 blacklist per stanza. Blacklist overrides whitelist if conflicts occur.

inputs.conf
```
[WinEventLog://Security]
disabled=0
whitelist1= EventCode=/^[4|5].\*$/ Type=/Error|Warning/
whitelist2= TaskCategory=%^Log.\*$%
blacklist = 540
```

#### Local vs. Remote Windows (WMI) Inputs

- You can configure remote inputs (WMI) for two types of Windows inputs:
  - Event logs
  - Performance monitor

- Disadvantage:
- Uses WMI as a transport protocol
- Not recommended in high latency networks
- Requires Splunk to run as a domain account

##### WMI Inputs

inputs.conf
```
[WMI:remote-logs]
interval = 5
server = server1, server2, server3
event\_log\_file = Application, Security, System

[WMI:remote-perfmon]
interval = 5
server = server1,server2, server3
wql = Select DatagramsPersec
```

#### Powershell Input

Uses built-in powershell.exe scripting facility in Windows
- No custom external library dependencies

```
[powershell://<name>]
script = <command>
schedule =
[<number>|<cron>]
```

#### HTTP Event Collector Options

- Enable HEC acknowledgments
- Send raw payloads
- Configure dedicated HTTP settings

#### HEC Indexer Acknowledgement

1. A request is sent from a client to the HEC endpoint using a token with indexer acknowledgment enabled
2. The server returns an acknowledgment identifier (ackID) to the client
3. The client can then query the Splunk server with the identifier to verify whether all the events in the sent request of have been indexed
4. The Splunk server responds with the status information of each queried request

#### Sending Raw Payloads to HEC

- Example: Application developers want to send data in a proprietary format
- Solution: HEC allows any arbitrary payloads, not just JSON

```
curl "http[s]://<splunk_server>:8088/services/collector/raw?
channel=<client_provided_channel>"
-H "Authorization: Splunk <generated_token>"
-d 'ERR,401,-23,15,36
```

#### Configuring Dedicated HTTP Settings

- Example: Splunk admins want to limit who can access the HEC endpoints
- Solution: Manually add the dedicated server settings in inputs.conf

inputs.conf
```
[http]
enableSSL = 1
crossOriginSharingPolicy = *.splunk.com
acceptFrom = "!45.42.151/24, !57.73.224/19,
*"
```

### 15.0 Fine Tuning Inputs

```
   15.1    Understand the default processing that occurs during input phase
   15.2    Configure input phase options, such as sourcetype fine-tuning and character set
           encoding
```

#### Things to Get Right at Index Time

*Input phase*

- Host
- Source type
- Source
- Index

*Parsing phase*

- Line breaking (event boundary)
- Date/timestamp extraction
- Adjust all meta fields
- Mask raw data
- Eliminate events

#### The props.conf File

props.conf is a config file that is referenced during all phases of Splunk data processing. Inputs, indexing, parsing and searching.

All data modifications in props.conf are based on either source, sourcetype, or host

props.conf
```
[source::source_name]
attribute = value

[host::host_name]
attribute = value

[sourcetype]
attribute = value
```

You can use wildcards (\*) and regex in the source:: and host:: stanzas

Some settings in props.conf are applied during the *input* phase:
- Character encoding
- Fine-tuning source types
- A few others

Some settings in props.conf are applied during the *parsing* phase:
- Individual event breaking
- Time extraction settings and rules
- Event data transformation

Configure props.conf on your forwarders if you have input phase settings

#### Character Encoding

During the input phase, Splunk sets all input data to *UTF-8* encoding by default

- This can be overridden, if needed, by setting the *CHARSET* attribute

props.conf
```
[source::/var/log/locale/korea/\*]
CHARSET=EUC-KR
[sendmail]
CHARSET=AUTO
```

### 16.0 Parsing Phase and Data

```
   16.1    Understand the default processing that occurs during parsing
   16.2    Optimize and configure event line breaking
   16.3    Explain how timestamps and time zones are extracted or assigned to events
   16.4    Use Data Preview to validate event creation during the parsing phase
```

#### The Parsing Phase

As data arrives at the indexer, it goes through the parsing phase

- The data is broken up into discrete *events*, each with a *timestamp* and a *time zone*

The parsing phase is all about creating, modifying, and redirecting events Apply additional transformation steps to modify the metadata fields or modify raw data

- Both indexers and heavy forwarders parse events. In this module, we assume parsing is happening on an indexer.

#### Event Boundaries

Splunk parsing phase determines where one event ends and the next one begins

- Automatically handles line breaking for common source types – even multi-line events

#### Handling Single Line Events

Splunk handles single line event sourcetypes with automatic line breaking

It is more efficient to explicitly set:
- *SHOULD_LINEMERGE = false*
- Default is true and assumes events can span over more than one line

#### Configuring Line Breaking

Splunk determines event boundaries in two steps:
- Line breaking: LINE\_BREAKER = \<regular\_expression\>
  - Splits the incoming stream of bytes into separate lines
  - The default value is **([\r\n]+)** which is any sequence of new lines and carriage returns
  - Correct use of regular expression can produce results in first step

- Line merging: SHOULD\_LINEMERGE = true
  - When set to true (the default) it uses all other line merging settings (such as BREAK\_ONLY\_BEFORE, BREAK\_ONLY\_BEFORE\_DATE, MUST\_BREAK\_AFTER)
  - When set to false, the line merging step does not run

#### Date/timestamp Extraction

- Correct date/timestamp extraction is essential
- Always verify timestamps when setting up new data types
  - Pay close attention to timestamps during testing/staging of new data
  - Check UNIX time or other non-human readable timestamps
- Splunk works well with standard date/time format and well-known data types
- Custom timestamp extraction is specified in props.conf

#### TIME_PREFIX

TIME_PREFIX = <REGEX> (matches characters right BEFORE the date/timestamp)

props.conf
```
[my_custom_source_or_sourcetype]
TIME_PREFIX = \d{4}/\d{2}/\d{2} \d{2}:\d{2}:\d{2} \w+\s
```
#### MAX_TIMESTAMP_LOOKAHEAD

MAX_TIMESTAMP_LOOKAHEAD = <integer>

specifies how many characters to look beyond the start of the line
for a timestamp

#### TIME_FORMAT

TIME_FORMAT = <strptime-style format>

#### Setting Time Zones – Splunk’s Rules

- Use time zone offsets to ensure correct event time
- Splunk applies time zones in this order:

1. A time zone indicator in the raw event data (e.g. -0800, GMT+8 or PST)
2. The value of a TZ attribute set in props.conf (Checks the host, source, or sourcetype stanzas)
3. If a forwarder is used, the forwarder-provided time zone is used
4. If all else fails, Splunk applies the time zone of the indexer's host server

### 17.0 Manipulating Raw Data

```
   17.1    Explain how data transformations are defined and invoked
   17.2    Use transformations with props.conf and transforms.conf to:
       *   Mask or delete raw data as it is being indexed
       *   Override sourcetype or host based upon event values
       *   Route events to specific indexes based on event content
       *   Prevent unwanted events from being indexed
   17.3    Use SEDCMD to modify raw data
```

#### Modifying the Raw Data

*Care* should be taken when modifying raw events (\_raw)

Unlike all other modifications discussed, these change the raw data before it is indexed

**Indexed data will not be identical to the original data source**

#### Splunk Transformation Methods

- When possible, define meta field values during the input phase
  - Most efficient to use inputs.conf
- Splunk provides two methods of raw data transformations:
  - SEDCMD
    - Uses only props.conf
    - Only used to mask or truncate raw data
- TRANSFORMS
  - Uses props.conf and transforms.conf
  - More flexible
  - Transforms matching events based on source, source type, or host

#### SEDCMD

props.conf
```
[source::.../vendor_sales.log]
SEDCMD-1acct = s/AcctID=\d{5}(\d{5})/AcctID=xxxxx\1/g
```

For more examples, see:
http://docs.splunk.com/Documentation/Splunk/latest/Data/Anonymizedata

#### TRANSFORMS

- Per event transformation is based on REGEX pattern matches
- Define the transformation in transforms.conf and invoke it from props.conf

- Transformation is based on the following attributes:

- SOURCE_KEY indicates which data stream to use as the source for pattern matching (default: \_raw)

- REGEX identifies the events from the SOURCE_KEY that will be processed (required)
  - Optionally specifies regex capture groups
- DEST_KEY indicates where to write the processed data (required)
- FORMAT controls how REGEX writes the DEST_KEY (required)

props.conf
```
[sourcetype]
TRANSFORMS = stanzaName
```

transforms.conf
```
[stanzaName]
SOURCE_KEY = ...
REGEX = ...
DEST_KEY = ...
FORMAT = ...
```

#### Masking Sensitive Data

props.conf
```
[source::...\\store\\purchases.log]
TRANSFORMS-1ccnum = cc_num_anon
```

transforms.conf
```
[cc_num_anon]
REGEX = (.*CC_Num:\s)\d{12}(\d{4}.*)
DEST_KEY = _raw
FORMAT = $1xxxxxxxxxxxx$2
```
When SOURCE_KEY is omitted, \_raw is used.

This REGEX pattern finds two capture groups and rewrites the raw data feed with a new format.

#### Setting Per-Event Source Type

Should be your last option because it is more efficient to set the sourcetype during the inputs phase

props.conf
```
[source::udp:514]
TRANSFORMS = custom_sourcetype
```

transforms.conf
```
[custom_sourcetype]
SOURCE_KEY = \_raw
REGEX = Custom$
DEST_KEY = MetaData:Sourcetype
FORMAT = sourcetype::custom_log
```

#### Setting Per-Event Host Name

props.conf
```
[sales_entries]
TRANSFORMS-register = sales_host
```

transforms.conf
```
[sales_host]
SOURCE_KEY = \_raw
REGEX = server:(\w+)
DEST_KEY = MetaData:Host
FORMAT = host::$1
```

#### Per-Event Index Routing

Again, if at all possible, specify the index for your inputs during the input phase (inputs.conf)

props.conf
```
[mysrctype]
TRANSFORMS-itops = route_errs_warns
```

transforms.conf
```
[route_errs_warns]
REGEX = (Error|Warning)
DEST_KEY = _MetaData:Index
FORMAT = itops
```

#### Filtering Unwanted Events

You can route specific unwanted events to the null queue. Events discarded at this point do NOT count against your daily license.

props.conf
```
[WinEventLog:System]
TRANSFORMS = null_queue_filter
```

transforms.conf
```
[null_queue_filter]
REGEX = (?!)^EventCode=(592|593)
DEST_KEY = queue
FORMAT = nullQueue
```

#### Routing Events to Groups using HF

*hm ...?*

#### Persisted to Disk

- All modifications and extractions are written to disk along with \_raw and
  metadata
  - source, sourcetype, host, timestamp, punct, etc.
- Indexed data cannot be changed
  - Changes to props.conf or transforms.conf only apply to new data
  - Indexed data cannot be changed without re-indexing
