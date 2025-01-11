# Projects

A sample of interesting projects I've worked on, some of which I have expanded on to include more detailed information.

## EPG CDR Processor
Developed a Python application to efficiently process and analyse Call Detail Records (CDRs). The tool parses XML-converted CDRs, extracting data points such as MSISDN, IMSI, network type, and traffic volumes, then stores these records in MongoDB for data retrieval and analysis. Features include:

- Custom parsing of hexadecimal data for accurate location and timestamp information.
- Multi-processing to handle large datasets concurrently (using python multiprocessing module).
- Automated file management to prevent duplicate processing.
- Error logging and handling for operational reliability.

## OLM Processor

``` mermaid
sequenceDiagram
    participant OLM as Online Mediation Platform
    participant OLM_Collector as OLM Collector
    participant OLM_Decoder as OLM Decoder
    participant OLM_Processor as OLM Processor
    participant MongoDB as MongoDB
    OLM->>OLM_Collector: SFTP collection
    OLM_Collector->>OLM_Decoder: Decoding
    OLM_Decoder->>OLM_Processor: Processing
    OLM_Processor->>MongoDB: Storage
```

Designed and implemented a Python-based system for processing Online Mediation platform data, managing the collection and decoding of logs from various network streams:

- **Collector**: Utilizes SFTP to fetch log files from multiple predefined streams, organizing and managing these files across local directories for processing. Features include:
  - Monitoring specific directories for new log files.
  - Implementing limits on the number of subdirectories and files processed to manage system resources.
  - Logging for operational transparency and troubleshooting.

- **Decoder**: Parses log filenames and content to extract meaningful data such as timestamps, stream identifiers, and MSISDN (Mobile Station International Subscriber Directory Number). Key aspects include:
  - Custom regex for identifying and extracting MSISDN within logs.
  - Decoding gzip-compressed files to process their contents.

- **Processor**: Manages the actual data processing, including:
  - Handling both compressed (.gz) and uncompressed log files.
  - Storing parsed data in MongoDB for later analysis or reporting.
  - Multi-processing for performance, allowing parallel processing of multiple files.
  - File management post-processing, moving files to a processed directory to avoid reprocessing.

This project enhances data handling capabilities for network mediation, ensuring efficient log management, data extraction, and storage for operational insights.

## Field Ops Portal
TODO brief description

## FTTH Migration Portal
TODO brief description

## IMS Migration Portal
TODO brief description

## Lobster SIM Activator
TODO expanded description

## Lobster Usage Database
TODO brief description

## Cockpitdecks
Contributed to the development of **Cockpitdecks**, an open-source project providing a Python interface for controlling Loupedeck Live and other decks for integration with X-Plane flight simulation software.

- Diagnosed and resolved compatibility issues with [Loupedeck Live](https://github.com/devleaks/python-loupedeck-live/issues/2) on newer hardware firmware models.
- Contributed bug fixes and new features to [Cockpitdecks](https://github.com/devleaks/cockpitdecks/commits/main/?author=dlicudi) repository.
- Established a separate [repository](https://github.com/dlicudi/cockpitdecks-configs) and [documentation](https://dlicudi.github.io/cockpitdecks-configs/) to host a comprehensive set of aircraft configurations for use with CockpitDecks.

### Technologies Used ###
- Python
- Loupedeck Live/Steamdeck
- X-Plane
- Git/GitHub

## SMS Gateway
TODO expanded description

Developed a solution to provide an API that could interface an SMS Centre.
This comprised of a Portal (Django + PostgreSQL), a front end API (FastAPI) and background services (Python) that could interface directly with the SMS Centre using SMPP.

``` mermaid
sequenceDiagram
    participant Enduser as End Users
    Enduser->>FrontendAPI: RESTful API Client
    participant FrontendAPI as External REST API
    participant Django as Billing & Messaging Engine
    participant Services as Services
    participant SMSC as SMS Centre
    FrontendAPI->>Django: Send and receive messages
    Services->>Django: Delivery reports
    Services->>Django: Incoming messages
    Services->>SMSC: Transmit messages
    Django->>Services: Outgoing messages
    SMSC->>Services: Incoming messages
    SMSC->>Services: Delivery reports
```

Django Rest Framework, Admin Interface, Signals used to adjust increase client balances on top up decrease on creating message.

Django Models made use of foreign keys, extensive use of attributes such as on_delete, help_text, validators, db_index.

Models using of clean and save functions to check for credit and save allowed adding dynamic data such as number of SMS parts required for a message.

Heavily customised admin views.

Developed an SMSC telnet library required to interface with SMS centre to retrieve information such as status of the SMS centre, throughput and licensing limits.


Delivery reports were recieved via SMPP connection and sent to the Portal via Django's REST framework API.

Services to handle incoming and outgoing messages were handled via separate listener and transmitter services connected via SMPP.

## ETK (Provisioning Platform)
TODO expanded description

``` mermaid
sequenceDiagram
    participant A as Flask Front End & CLI Interface
    participant B as Packages (interface libraries)
    participant C as DB Storage (MongoDB)
    participant D as External Interfaces
    A->>B: Load custom Python modules
    B->>C: interface configuration
    B->>D: Telnet/SOAP/RESTFul
```

RQ Active/Standby provisioning
Provisioning of and presentation of Panic button data using SMS.


## MVNO Number Porting
TODO expanded description

``` mermaid
sequenceDiagram
    participant A as AOPM SFTP
    participant B as File Storage
    participant C as Sources DB
    participant D as Translations DB
    participant E as FNR
    A->>B: Collector
    B->>C: Importer
    C->>D: Translations DB
    D->>E: Activator
```
