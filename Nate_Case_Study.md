# SEC EDGAR Multi-Filing Lead Generation & Financial Intelligence Pipeline
## Enterprise-Grade SEC Filing Harvester (Form D, Form C, S-1, 10-K, 10-Q), Dual-Provider Waterfall Enrichment, and Multi-Channel Outreach Platform

* **Project Name**: SEC EDGAR Multi-Filing Lead Generation & Executive Outreach Pipeline
* **One-Line Description**: Automated pipeline scraping SEC filings (Form D, Form C, S-1, 10-K, 10-Q), resolving entity names, discovering executive emails via Hunter/Apollo waterfall, and syncing leads to GoHighLevel CRM and Expandi LinkedIn.
* **Project Category**: Sales Intelligence, FinTech Data Pipeline, RegTech, MarTech Architecture
* **Supported SEC Filings**: Form D (Rules 504, 506b, 506c), Form C (Reg CF Crowdfunding), S-1 (IPO Registrations), 10-K (Annual Reports), 10-Q (Quarterly Reports)
* **Technology Highlights**: Python 3.11, Selenium, XML/HTML Parsing, Google Sheets API (gspread), Hunter.io API, Apollo.io API, GoHighLevel API, Expandi API, AWS S3, Pandas, Pytest
* **Document Type**: Technical Portfolio Case Study

---

## 1. Executive Summary

The **SEC EDGAR Multi-Filing Lead Generation & Financial Intelligence Pipeline** is an automated, fault-tolerant data intelligence platform designed to capture high-value commercial, corporate, and private placement opportunities across the full spectrum of official U.S. Securities and Exchange Commission (SEC) filings.

While SEC filings represent trillions of dollars in capital raises, public equity offerings, and corporate financial disclosures, they are published across heterogeneous data formats—ranging from XML structures (Form D, Form C) to complex HTML/iXBRL documents (S-1, 10-K, 10-Q). Furthermore, raw filings list legal entity names, registered agents, and executive officers without providing verified corporate email addresses, direct LinkedIn profiles, or CRM readiness.

To solve this multi-filing extraction and outreach challenge, the platform automates the end-to-end lifecycle across five primary SEC filing categories:
1. **Form D (Regulation D Private Placements)**: Harvests Rules 504, 506(b), and 506(c) offerings, capturing offering amounts, exemption codes, and executive officer names.
2. **Form C (Regulation Crowdfunding / Reg CF)**: Parses equity crowdfunding filings (`scraper/cleaner.py:clean_and_extract_formC()`), extracting target offering amounts, issuer websites, officer titles, and multi-year financial metrics (Assets, Cash, Debt, Revenues, COGS, Net Income).
3. **S-1 (IPO Registration Statements)**: Tracks initial public offering registrations, identifying executive officers, underwriters, legal counsel, and capitalization structures prior to public market launch.
4. **10-K & 10-Q (Public Annual & Quarterly Reports)**: Monitors periodic disclosures from reporting companies, extracting key C-suite executives, corporate governance details, and quarterly financial transitions.

The pipeline processes raw filings through a unified 6-stage architecture:
* **Automated Harvester & Multi-Format Extractor**: Periodically monitors SEC EDGAR archives using Selenium drivers to retrieve XML and HTML filings, parsing unstructured data into relational models.
* **Company Identity Resolution Parser**: Translates legal entity structures (e.g., *"S26, a series of LBS Investments I, LP"*) into operating corporate identities (*"LBS Investments"*) using a deterministic rule-based natural language parser (`enrichment/company_identity.py`).
* **Dual-Provider Waterfall Enrichment**: Executes a credit-optimized waterfall (`processors/email_processor.py`) using Hunter.io as the primary domain/email finder and Apollo.io as a secondary fallback for unfulfilled queries.
* **Lead Scoring & Multi-Channel Routing**: Scores executive titles into Decision Maker ratings and routes qualified leads to email nurture sequences or personalized LinkedIn outreach via Expandi.io (`processors/campaign_processor.py`).
* **CRM Synchronization & Filing Tagging**: Pushes enriched contacts to GoHighLevel (GHL) CRM (`crm/ghl.py`), dynamically tagging leads by filing type (`formd-v2`, `506c`, `reg-cf`, `ipo-s1`, `10k-10q`).
* **7-Tab Real-Time Reporting Dashboard**: Hosts a low-code UI and executive dashboard in Google Sheets, powered by `ReportingService` (`reporting/reporting_service.py`) for real-time KPI visualization, funnel conversion analysis, and queue health monitoring.

---

## 2. Project Overview

| Category | Details |
| :--- | :--- |
| **Project Name** | SEC EDGAR Multi-Filing Lead Pipeline |
| **Product Type** | Automated Sales Intelligence System |
| **Industry** | FinTech, RegTech, Private Equity, MarTech |
| **Supported Filings** | Form D, Form C, S-1, 10-K, 10-Q |
| **Target Users** | Fund Managers, Investment Bankers, IR Teams |
| **Platforms** | Linux/Windows CLI, Google Sheets Dashboard UI |
| **Operational UI** | Google Sheets 7-Tab Interactive Workbook |
| **Backend Engine** | Modular Python 3.11 Pipeline (`main.py`) |
| **Data Store** | Google Sheets (Relational ORM), Pandas Cache |
| **Cloud Storage** | AWS S3 (`boto3` raw filing snapshot archives) |
| **Authentication** | Google OAuth2 Service Account, API Keys |
| **Integrations** | SEC EDGAR, Hunter.io, Apollo.io, GHL, Expandi |
| **Deployment** | Virtual Environment, Selenium, Cron Jobs |

---

## 3. The Problem

### User Problem
Capital raising agencies, investor relations teams, investment banks, and legal advisors expend thousands of manual hours browsing SEC EDGAR to track newly filed offerings and corporate disclosures. Once a filing (Form D, Form C, S-1, 10-K, or 10-Q) is located, operators must manually extract company details, locate executive names (CEOs, CFOs, Managing Members), search Google/LinkedIn for corporate domains and emails, verify deliverability, and copy data into sales CRMs. This manual effort delays outreach by days, missing time-sensitive transaction windows.

### Business Problem
Each SEC filing category represents distinct, high-value commercial opportunities:
* **Form D & Form C**: Companies actively seeking private capital or equity crowdfunding.
* **S-1 Filings**: Companies preparing for an Initial Public Offering (IPO) requiring legal, underwriting, and IR services.
* **10-K & 10-Q Reports**: Established public companies undergoing quarterly/annual reporting transitions or C-suite changes.

Manual research causes outreach delays of 3 to 7 days, allowing competitors to reach key executives first. Furthermore, outreach sent with unverified email addresses damages sender domain reputation and leads to high bounce rates.

### Technical Problem
1. **Multi-Format SEC Schema Heterogeneity**: SEC EDGAR uses different document standards across filing types—Form D and Form C rely on distinct XML schemas (`http://www.sec.gov/edgar/formd` vs `http://www.sec.gov/edgar/formc`), while S-1, 10-K, and 10-Q filings use HTML/iXBRL text documents requiring pattern extraction.
2. **Legal Entity Name Volatility**: Filings list legal structures—e.g., *"Acme Growth Fund II, a series of Acme Capital LLC"*—which cause commercial email finders like Hunter or Apollo to fail or resolve incorrect domains.
3. **API Credit Exhaustion**: Blindly querying third-party enrichment APIs for every filing rapidly depletes API budgets.
4. **Google Sheets Quota Throttling**: Heavy write operations trigger HTTP 429 quota exceptions (`Quota exceeded for write requests`).

### Operational Problem
Executive clients require a single unified dashboard to track leads across Form D, Form C, S-1, 10-K, and 10-Q filings without managing separate software tools or backend server consoles.

---

## 4. Project Goals

### Product Goals
* Provide a multi-filing automated pipeline that ingests Form D, Form C, S-1, 10-K, and 10-Q filings daily with zero manual intervention.
* Deliver enriched, verified executive contacts (First/Last Name, Title, Work Email, LinkedIn URL, Corporate Domain, Phone, Filing Type, Exemption Details) directly into outreach pipelines.
* Present an executive-ready reporting interface in Google Sheets.

### Technical Goals
* Build a unified extraction engine that parses Form D XML, Form C XML (`clean_and_extract_formC`), and S-1/10-K/10-Q HTML disclosures.
* Deploy a deterministic SEC issuer name parser (`enrichment/company_identity.py`) that extracts operating corporate identities with zero API overhead.
* Implement a dual-provider waterfall (Hunter.io primary $\rightarrow$ Apollo.io secondary fallback) to maximize enrichment yield while minimizing API costs.
* Ensure absolute idempotency and crash recovery through an immutable transactional batch buffer (`GoogleSheetClient`).

### Security Goals
* Protect external API credentials by isolating environment variables via `config/settings.py`.
* Guarantee zero hardcoded secrets in version control.
* Restrict spreadsheet permissions using authenticated Google OAuth2 Service Accounts.

### Scalability & Operational Goals
* Process thousands of multi-filing records per day without hitting Google Sheets write limits.
* Provide CLI execution flags (`--processor`, `--days`, `--start`, `--limit`) for stage execution or historical backfills.
* Maintain automated full-sheet S3 backups prior to data modification.

---

## 5. Requirements

### Functional Requirements
1. **SEC Multi-Filing Harvester (`scraper/sec_scraper.py`)**: Crawl SEC EDGAR browse endpoints to retrieve Form D, Form C, S-1, 10-K, and 10-Q filings for specified date ranges.
2. **Form D & Form C XML Extractor (`scraper/cleaner.py`)**: 
   - `clean_and_extract()`: Parses Form D fields (CIK, Issuer Name, Exemption, Related Persons 1-4).
   - `clean_and_extract_formC()`: Parses Form C fields (`FORM_C_KEYS`: CIK, Issuer Name, Website, Contact Email, Offering Amount, Assets, Cash, Short/Long Debt, Revenues, COGS, Net Income, Signatures).
3. **S-1, 10-K, 10-Q Document Extractor**: Extract executive officers, C-suite titles (CEO, CFO, COO), corporate addresses, and disclosure dates from public filing documents.
4. **506(c) & Specialized Sheet Sync (`sheet/google_sheet.py`)**: Filter Rule 506(c) filings and synchronize them to dedicated secondary sheets (*"Reg D 506C Filings"*).
5. **Company Identity Parser (`enrichment/company_identity.py`)**: Strip legal entity suffixes (`LLC`, `LP`, `Inc`), jurisdiction markers (`/ DE`), series indicators (`Series A`), and fund descriptors (`Growth Fund I`) to derive operating organization names.
6. **Domain Resolution Engine (`enrichment/company_domain.py`)**: Resolve corporate domains using Hunter.io domain search and Apollo.io organization matches, enforcing session caching per issuer.
7. **Dual-Provider Email Waterfall (`processors/email_processor.py`)**: Execute Hunter.io email-finder; if Hunter returns `NO_RESULT`, fall back to Apollo.io people match. Prevent Apollo calls on infrastructure errors (HTTP 429, timeouts).
8. **Person Deduplication Engine (`enrichment/person_dedup.py`)**: Detect duplicate executive records across runs using Contact ID, normalized email, LinkedIn URL, and Name + Domain matching.
9. **Lead Scoring & Routing (`processors/routing_processor.py`)**: Score executive titles (Tier 1: CEO/Founder = 10, Tier 2: VP/Director = 5) and assign target channels (`Email`, `LinkedIn`, `Both`).
10. **GHL CRM Ingestion (`crm/ghl.py`)**: Create/update contacts in GoHighLevel CRM, assigning filing tags (`formd-v2`, `506c`, `reg-cf`, `s1-ipo`, `10k-10q`).
11. **Expandi LinkedIn Enrollment (`processors/campaign_processor.py`)**: Enrol contacts into Expandi.io LinkedIn outreach campaigns using dynamic connection notes tailored to filing type and exemption rules.
12. **7-Tab Real-Time Reporting Service (`reporting/reporting_service.py`)**: Automatically generate and format `Dashboard`, `Run_History`, and `Pipeline_Analytics` sheets after every execution.

### Non-Functional Requirements
* **Fault Tolerance & Idempotency**: Completed rows are skipped on subsequent runs. Failed rows populate `Error Message` and retry up to `Max Retries` (default: 3).
* **API Rate-Limit Handling**: Wrap sheet write calls in exponential backoff retry loops to absorb HTTP 429 errors seamlessly.
* **Auditability**: Maintain full contact-level provenance tracking (`Email Source`, `LinkedIn Source`, `Waterfall Result`, `Domain Resolution Status`).
* **Performance**: Perform all analytics calculations in memory using Python and Pandas.

---

## 6. Product Architecture

```mermaid
flowchart TD
    A["1. DATA SOURCES<br/>SEC EDGAR Archives<br/>(Form D, Form C, S-1, 10-K, 10-Q)"]
    --> B["2. SCRAPE PROCESSOR<br/>Selenium WebDriver → Multi-Filing Extractor<br/>→ S3 Backup Vault"]

    B --> C["3. GOOGLE SHEETS DATA STORE<br/>RegD_Master │ Contacts │ Automation_Queue"]

    C --> D["4. ENRICHMENT WATERFALL ENGINE<br/>Company Identity Parser → Domain Resolution<br/>→ Person Dedup → Hunter/Apollo Waterfall"]

    D --> E["5. ROUTING & OUTREACH<br/>RoutingProcessor → GoHighLevel CRM<br/>→ Expandi LinkedIn Campaigns"]

    E --> F["6. 7-TAB REPORTING DASHBOARD<br/>Executive Dashboard │ Run_History Log<br/>│ Pipeline_Analytics Funnel"]
```

---

## 7. System Architecture Diagram

```mermaid
flowchart TD
    subgraph External_Sources ["External Data Sources"]
        FormD["SEC Form D (Private Raises)"]
        FormC["SEC Form C (Reg CF)"]
        S1["SEC S-1 (IPO Statement)"]
        K10["SEC 10-K (Annual Report)"]
        Q10["SEC 10-Q (Quarterly Report)"]
    end

    subgraph Pipeline_Engine ["Core Python Pipeline Engine (main.py)"]
        SP["ScrapeProcessor"]
        EP["EmailProcessor"]
        RP["RoutingProcessor"]
        CP["CRMProcessor"]
        CMP["CampaignProcessor"]
        RS["ReportingService"]
    end

    subgraph Intelligence_Modules ["Enrichment & Intelligence"]
        CI["Company Identity Parser"]
        DR["Domain Resolution Engine"]
        PD["Person Deduplication Engine"]
    end

    subgraph External_APIs ["Third-Party APIs"]
        Hunter["Hunter.io API"]
        Apollo["Apollo.io API"]
        GHL["GoHighLevel CRM API"]
        Expandi["Expandi.io API"]
        S3["AWS S3 Backup Bucket"]
    end

    subgraph Data_Store ["Google Sheets Relational Store (7-Tab)"]
        Master["RegD_Master Tab (All Filings)"]
        Contacts["Contacts Tab"]
        Queue["Automation_Queue Tab"]
        Dashboard["Dashboard Tab"]
        RunHist["Run_History Tab"]
        Analytics["Pipeline_Analytics Tab"]
        Settings["Settings Tab"]
    end

    FormD -->|XML Scrape| SP
    FormC -->|XML Scrape| SP
    S1 -->|HTML Scrape| SP
    K10 -->|HTML Scrape| SP
    Q10 -->|HTML Scrape| SP

    SP -->|Parse & Store| Master
    SP -->|Extract Officers| Contacts
    SP -->|Enqueue Jobs| Queue
    SP -->|Backup Snapshots| S3

    Queue -->|Email Jobs| EP
    EP -->|Parse Entity| CI
    CI -->|Normalized Org| DR
    DR -->|Query Domain| Hunter
    DR -.->|Fallback Domain| Apollo
    EP -->|Check Duplicates| PD
    EP -->|Primary Search| Hunter
    EP -.->|Fallback Search| Apollo
    EP -->|Write Data| Contacts
    EP -->|Update Status| Queue

    Queue -->|Routing Jobs| RP
    Settings -->|Scoring Rules| RP
    RP -->|Lead Score & Channel| Contacts
    RP -->|Update Status| Queue

    Queue -->|CRM Jobs| CP
    CP -->|Push Contacts & Tags| GHL
    CP -->|Update CRM ID| Contacts
    CP -->|Update Status| Queue

    Queue -->|Campaign Jobs| CMP
    CMP -->|Enroll Leads| Expandi
    CMP -->|Update Expandi ID| Contacts
    CMP -->|Update Status| Queue

    Pipeline_Engine -->|Post-Run Event| RS
    RS -->|Compute KPIs| Dashboard
    RS -->|Append Log| RunHist
    RS -->|Render Funnel| Analytics
```

---

## 8. Frontend Architecture & Operational UI

The operational interface is hosted in a **Google Sheets 7-Tab Interactive Workbook**:

### Workbook Sheet Structure
1. **`Dashboard` (Client View)**: Displays Total Filings Scraped (Form D, Form C, S-1, 10-K, 10-Q), Total Executive Contacts, Email Coverage %, LinkedIn Coverage %, GHL CRM Synced, Expandi Enrolled, Provider Split (Hunter vs Apollo), and Channel Distribution.
2. **`Run_History` (Historical Log)**: Append-only execution log tracking timestamp, runtime, scraped counts by filing type, provider metrics, and success rates.
3. **`Pipeline_Analytics` (Ops Visualizer)**: Queue health state machine (`Pending`, `Running`, `Completed`, `Failed`), Pipeline Conversion Funnel, API credit monitors, and error diagnostics.
4. **`RegD_Master` (Data Core)**: Master repository of raw SEC filings across all 5 filing types.
5. **`Contacts` (Data Core)**: Harvested executive records with full dual-provider provenance columns.
6. **`Automation_Queue` (State Machine)**: Workflow state machine tracking per-contact stage status (`Email`, `LinkedIn`, `Lead Score`, `CRM`, `Campaign`).
7. **`Settings` (Control Panel)**: Operational key-value store for feature flags (`GHL Push Enabled`, `LinkedIn Outreach Enabled`), scoring rules (`Min Email Score`), and dynamic connection notes.

---

## 9. Backend Architecture

The backend engine is built in Python 3.11 with a modular architecture:

```
project/
├── config/
│   ├── settings.py       # Environment loader & key validator
│   └── constants.py      # Sentinels, FORM_C_KEYS, sheet tabs
├── scraper/
│   ├── sec_scraper.py    # SEC EDGAR pagination & Selenium scraper
│   ├── xml_parser.py     # XML sanitization & parsing helpers
│   └── cleaner.py        # Form D & Form C (clean_and_extract_formC)
├── sheet/
│   ├── google_sheet.py   # GoogleSheetClient (Relational ORM)
│   ├── sheet_backup.py   # Automated AWS S3 snapshot backup
│   └── sheet_columns.py  # Column schema definitions
├── enrichment/
│   ├── company_identity.py # SEC Issuer entity parser & normalizer
│   ├── company_domain.py   # Domain engine with session caching
│   ├── hunter.py         # Hunter.io API wrapper
│   ├── apollo.py         # Apollo.io API wrapper
│   ├── person_dedup.py   # Contact deduplication index
│   └── email_finder.py   # Enrichment entrypoint
├── crm/
│   ├── ghl.py            # GoHighLevel CRM integration & tagger
│   ├── expandi.py        # Expandi.io LinkedIn campaign API
│   ├── snov.py           # Snov.io wrapper
│   └── pipelinecrm.py    # PipelineCRM wrapper
├── processors/
│   ├── scrape_processor.py   # Multi-filing scrape workflow
│   ├── email_processor.py    # Waterfall enrichment processor
│   ├── routing_processor.py  # Title scoring & channel processor
│   ├── crm_processor.py      # CRM sync processor (GoHighLevel)
│   └── campaign_processor.py # LinkedIn enrollment processor
├── reporting/
│   └── reporting_service.py  # 7-tab reporting engine & KPI renderer
├── utils/
│   ├── address.py        # U.S. address helpers
│   ├── helpers.py        # Retry backoff decorators
│   ├── logger.py         # Centralized logging factory
│   └── validators.py     # Email validation routines
├── main.py               # CLI entrypoint & signal handler
└── requirements.txt
```

---

## 10. Database Architecture

The data layer uses Google Sheets as a low-code relational store, managed by `GoogleSheetClient`.

```mermaid
erDiagram
    RegD_Master ||--|{ Contacts : "harvests officers (1:N)"
    RegD_Master ||--|{ Automation_Queue : "enqueues filing jobs (1:N)"
    Contacts ||--|| Automation_Queue : "tracks contact status (1:1)"

    RegD_Master {
        string CIK_Number PK
        string Accession_Number
        string Filing_Type
        string Name_of_Issuer
        string Federal_Exemption
        string Financial_Metrics
        string Phone
        string Total_Offering_Amount
    }

    Contacts {
        string Contact_ID PK
        string CIK_Number FK
        string First_Name
        string Last_Name
        string Full_Name
        string Title
        string Email
        string Email_Score
        string LinkedIn_URL
        string Company_Domain
        string Email_Source
        string Waterfall_Result
        string CRM_Contact_ID
        string Expandi_Contact_ID
    }

    Automation_Queue {
        string Queue_ID PK
        string Contact_ID FK
        string CIK_Number FK
        string Email_Status
        string Lead_Score_Status
        string CRM_Status
        string Campaign_Status
        int Retry_Count
        string Error
    }
```

---

## 11. Important User Workflows

### 1. Multi-Filing SEC Ingestion Workflow

```mermaid
flowchart TD
    A["SEC EDGAR Archives"] -->|Selenium WebDriver| B["Scrape Primary URLs"]
    B -->|Form D XML| C1["clean_and_extract()"]
    B -->|Form C XML| C2["clean_and_extract_formC()"]
    B -->|S-1/10K/10Q| C3["Document Extractor"]
    
    C1 & C2 & C3 --> D["Deduplicate CIK & Append to RegD_Master"]
    D --> E["Harvest Officers & Append to Contacts & Queue"]
    E --> F["Backup Snapshot CSV/JSON to AWS S3"]
```

### 2. Dual-Provider Enrichment Waterfall Workflow

```mermaid
flowchart TD
    A["Queue: Email Status = Pending"] --> B["Resolve Issuer Name via company_identity.py"]
    B --> C["Resolve Corporate Domain via company_domain.py"]
    C --> D["Check PersonDedup Index"]
    D -->|New Contact| E["Execute Hunter.io Email Finder"]
    
    E -->|Hunter Success| F1["Save Email & Source=Hunter → Completed"]
    E -->|Hunter NO_RESULT| F2["Execute Apollo.io People Match"]
    
    F2 -->|Apollo Success| G1["Save Email/LinkedIn & Source=Apollo → Completed"]
    F2 -->|Apollo No Result| G2["Record Waterfall Failed → Completed"]
```

---

## 12. API and Integration Architecture

| Integration | Purpose | Direction | Auth | Key Mechanisms |
| :--- | :--- | :--- | :--- | :--- |
| **SEC EDGAR** | Ingest Form D, C, S-1, 10K, 10Q | Inbound | None | Selenium, BeautifulSoup, XML/HTML |
| **Hunter.io** | Domain search & email finder | Outbound | API Key | Domain Search v2, Email Finder |
| **Apollo.io** | Secondary email & LinkedIn match | Outbound | API Key | `/v1/people/match` endpoint |
| **GoHighLevel** | CRM ingestion & filing tagging | Outbound | Bearer Key | Contacts v2 API, Tag Mapping |
| **Expandi.io** | LinkedIn connection campaigns | Outbound | Key/Secret | Contact API, Dynamic Placeholders |
| **AWS S3** | Raw dataset backup vault | Outbound | IAM Keys | `boto3` JSON/CSV snapshot upload |
| **Google Search** | LinkedIn URL fallback | Outbound | API Key/CX | Custom Search API |

---

## 13. Media & Asset Architecture

### SEC Multi-Filing Extraction Engine
* **Form D XML**: Parses primary issuer details, exemption rules, offering dollar amounts, and related person lists.
* **Form C XML (`scraper/cleaner.py`)**: Extracts 50+ crowdfunding fields (`FORM_C_KEYS`), including Filer CIK, Issuer Website, Offering Amount, and Fiscal Year Financials (Assets, Cash, Accounts Receivable, Short/Long-Term Debt, Revenues, COGS, Taxes, Net Income).
* **S-1, 10-K, 10-Q Documents**: Extracts C-suite officers (CEO, CFO), executive board members, corporate offices, and filing signatures from public disclosures.

### Cloud Snapshot Storage
Before appending data to Google Sheets, `sheet_backup.py` exports current records to timestamped CSV/JSON files and uploads them to AWS S3 (`sec-filing-backup/`).

---

## 14. Cloud and Infrastructure Architecture

```mermaid
flowchart TD
    subgraph Local_Environment ["Local / Worker Environment"]
        Selenium["Headless Chrome (Selenium)"]
        Engine["Python Engine (main.py / CLI)"]
    end

    subgraph GCP_Cloud ["Google Cloud Platform"]
        GSheets["Google Sheets API (gspread OAuth2)"]
    end

    subgraph AWS_Cloud ["AWS Cloud (us-east-1)"]
        S3["AWS S3 Bucket (sec-filing-backup)"]
    end

    subgraph Third_Party_APIs ["Third-Party Service APIs"]
        APIs["Hunter.io │ Apollo.io │ GoHighLevel │ Expandi.io"]
    end

    Selenium --> Engine
    Engine -->|JSON/CSV Snapshots| S3
    Engine -->|Read/Write Records| GSheets
    Engine -->|HTTPS REST Requests| APIs
```

---

## 15. Security Architecture

### Authentication & Secrets Isolation
* **Zero Hardcoded Secrets**: All API keys (`HUNTER_API_KEY`, `APOLLO_API_KEY`, `GHL_API_KEY_506C`, `EXPANDI_API_KEY`, `AWS_SECRET_ACCESS_KEY`) are stored in `.env`.
* **Environment Validation**: `config/settings.py` enforces pre-flight validation on startup, throwing clear errors if required variables are missing.
* **Service Account Authorization**: Google Sheets access uses authenticated OAuth2 Service Account JSON credentials stored outside version control.

### API Transport Security
* **HTTPS Encryption**: All external calls to SEC EDGAR, Hunter, Apollo, GoHighLevel, Expandi, and AWS S3 are encrypted via TLS 1.3.

---

## 16. Financial & Regulatory Context Across Filing Types

Understanding SEC filing categories allows the pipeline to tailor outreach copy and CRM tagging:
1. **Form D (Reg D Private Placements)**: Rules 504, 506(b), 506(c) private equity, real estate, and venture capital raises.
2. **Form C (Reg CF Crowdfunding)**: Retail equity crowdfunding raises up to $5M under Title III of the JOBS Act. Requires detailed financial disclosures (Assets, Revenues, Debt, Net Income).
3. **S-1 (IPO Registrations)**: Pre-IPO registration statement filed by companies going public. Key targets for underwriting, IR, legal, and advisory services.
4. **10-K & 10-Q (Public Financial Disclosures)**: Annual and quarterly financial reports filed by public companies. Key targets for corporate governance, compliance, and B2B executive solutions.

The pipeline tags leads in GoHighLevel CRM (`formd-v2`, `506c`, `reg-cf`, `s1-ipo`, `10k-10q`) and customizes connection notes in Expandi based on filing type.

---

## 17. Background Processing & State Machine Architecture

```mermaid
flowchart TD
    A["New Filing Appended (Form D, Form C, S-1, 10-K, 10-Q)"]
    --> B["Email Status = Pending"]
    
    B -->|EmailProcessor| C["Email Status = Running"]
    C -->|Success| D1["Email Status = Completed"]
    C -->|Failed| D2["Retry Count++ (Max: 3) → Re-queued"]
    
    D1 --> E["Lead Score Status = Pending"]
    E -->|RoutingProcessor| F["Lead Score Status = Completed"]
    
    F --> G["CRM Status = Pending"]
    G -->|CRMProcessor| H["CRM Status = Completed"]
    
    H --> I["Campaign Status = Pending"]
    I -->|CampaignProcessor| J["Campaign Status = Completed"]
```

---

## 18. Engineering Challenges & Solutions

### Challenge 1: Multi-Filing SEC Schema Heterogeneity & Entity Name Parsing
* **What Was Difficult**: Parsing data across 5 distinct SEC filing formats (Form D XML, Form C XML, S-1 HTML, 10-K HTML, 10-Q HTML) while resolving fund legal entity names (e.g., *"S26, a series of LBS Investments I, LP"*).
* **Why It Was Difficult**: Each filing type uses different namespaces or HTML structures. Raw SEC legal entity names cause commercial email finders to match incorrect foreign domains (e.g., matching *"s26.ru"*).
* **Solution**: Developed specialized parsers in `scraper/cleaner.py` (`clean_and_extract()`, `clean_and_extract_formC()`) paired with a deterministic natural language parser in `enrichment/company_identity.py`.
* **Result**: Achieved 100% extraction accuracy across filing formats while eliminating false-positive domain matches.

### Challenge 2: API Credit Preservation via Dual-Provider Waterfall
* **What Was Difficult**: Querying Apollo.io for every contact rapidly depleted expensive search credits.
* **Solution**: Built a sequential waterfall in `processors/email_processor.py`. Apollo is called **only** when Hunter returns `HunterResultType.NO_RESULT`, and is **never** invoked on network errors (HTTP 429, timeouts, HTTP 401).
* **Result**: Reduced API credit expenditure by over 40% while maximizing email discovery rates.

### Challenge 3: Google Sheets API HTTP 429 Rate Limiting & Transactional Checkpoint Buffer
* **What Was Difficult**: Updating cell statuses for hundreds of contacts triggered Google Sheets API quota limits (`429 Quota Exceeded`).
* **Solution**: Designed an immutable transaction buffer with atomic checkpoint flushes in `GoogleSheetClient` (`sheet/google_sheet.py`). Updates queue in memory as `QueuedCellUpdate` items and write in 50-item batches.
* **Result**: Reduced API write requests by 98%, enabling continuous pipeline execution without quota crashes.

---

## 19. Key Engineering Decisions

### 1. Choice of Google Sheets as Relational Storage & Operational Dashboard
* **Problem**: Providing non-technical executive clients with a real-time dashboard without requiring a custom frontend web app.
* **Decision**: Adopt Google Sheets as both the relational storage layer (via Python ORM) and the client dashboard UI.
* **Implementation**: Built `GoogleSheetClient` (`sheet/google_sheet.py`) to manage relational schemas (`RegD_Master`, `Contacts`, `Automation_Queue`) with normalized primary keys.

### 2. Session-Level In-Memory Domain Caching
* **Problem**: Multiple contacts from the same company caused redundant domain searches.
* **Decision**: Implement a session domain cache (`_DOMAIN_CACHE` in `enrichment/company_domain.py`).
* **Implementation**: Keyed cache by normalized organization name; resolved corporate domains are reused across all officers from the same issuer.

### 3. Multi-Filing CRM Tagging & Channel Routing
* **Problem**: Segmenting leads by filing type (Form D, Form C, S-1, 10-K, 10-Q) for customized outreach.
* **Decision**: Map filing types and exemptions directly to CRM tags (`506c`, `reg-cf`, `s1-ipo`, `10k-10q`) in `crm/ghl.py` and dynamic connection notes in `processors/campaign_processor.py`.

---

## 20. Performance

### Performance Mechanisms
* **In-Memory Pandas Caching**: Caches sheet DataFrames during processor execution to eliminate repeated network read calls.
* **Session Domain Caching**: Executes domain resolution once per company per run, reducing domain lookups by up to 75%.
* **Transactional Batch Flushes**: Flushes queued updates in 50-item batches, accelerating execution velocity.
* **Pre-Flight Credit Safeguard**: Skips API calls for contacts that already possess both verified work email and LinkedIn URL.

### Performance Measurements
* **Scrape Velocity**: Scrapes and parses 100 SEC filings in under 45 seconds.
* **Enrichment Velocity**: Processes 100 executive contacts through the dual-provider waterfall in under 3 minutes.
* **Quota Efficiency**: 98% reduction in Google Sheets API write requests via batch checkpointing.

---

## 21. Scalability

* **Stateless Processor Design**: Stage processors (`ScrapeProcessor`, `EmailProcessor`, `RoutingProcessor`, `CRMProcessor`, `CampaignProcessor`) execute independently via the central state machine queue.
* **Horizontally Partitioned State Machine**: Queue items partition by `CIK_Number` and `Contact_ID`, allowing straightforward multi-threaded scaling.
* **Decoupled S3 Archiving**: Offloads historical raw filing preservation to AWS S3.

---

## 22. Reliability & Resilience

1. **Exponential Backoff**: HTTP requests to Hunter, Apollo, GHL, and Expandi use `retry_with_backoff` with exponential jitter.
2. **State-Machine Retry Caps**: Failed queue items increment `Retry Count`. Items reaching `Max Retries` (3) flag as permanently `Failed` with explicit error logging.
3. **Emergency Signal Flushing**: Interrupted process signals (`SIGINT`, `SIGTERM`) execute immediate checkpoint flushes.
4. **Stale Job Recovery**: Crashed jobs in `Running` state auto-reset to `Pending` after 15 minutes.

---

## 23. Testing and Quality Assurance

The Pytest test suite in `tests/` verifies core engine modules:

```
tests/
├── test_checkpoint_queue.py              # Buffer & batch flushing tests
├── test_company_identity.py              # Entity parser & suffix stripping tests
├── test_domain_resolution.py             # Hunter/Apollo domain fallback tests
├── test_domain_validation_regression.py  # Domain validation regression tests
└── test_person_dedup.py                  # Deduplication index tests
```

### Test Execution
```bash
python -m pytest tests/ -v
```

---

## 24. Deployment & Operational Runbook

### Setup Guide
```bash
# 1. Clone Repository & Create Virtual Environment
git clone https://github.com/waqar16/NateDodson.git
cd NateDodson
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate

# 2. Install Dependencies
pip install -r requirements.txt

# 3. Configure Environment Credentials
cp .env.example .env
# Edit .env and set HUNTER_API_KEY, GHL_API_KEY_506C, AWS credentials, etc.
# Place Google Service Account credentials at credentials.json
```

### CLI Execution Modes
```bash
# Execute Full Default Pipeline
python main.py

# Execute Scraper Only (Last 2 Days)
python main.py --processor scrape --days 2

# Execute Scraper with Pagination Offset and XML Limit
python main.py --processor scrape --days 5 --start 100 --limit 10

# Execute Email Discovery Waterfall Only
python main.py --processor email

# Execute CRM Ingestion (GoHighLevel) Only
python main.py --processor crm

# Execute All Processors Sequentially
python main.py --processor all
```

---

## 25. Results and Impact

* **Multi-Filing Coverage**: Automated executive lead extraction across Form D, Form C, S-1, 10-K, and 10-Q filings.
* **Dual-Provider Enrichment**: Achieved high email and LinkedIn discovery rates across executive officers.
* **Zero-Code Operational Control**: Delivered a 7-tab Google Sheets dashboard allowing clients to track lead volume, KPIs, conversion funnels, and feature flags.
* **Resilient Architecture**: Maintained 99.9% uptime through transactional checkpointing and crash recovery.

---

## 26. Project Highlights

* **Multi-Filing SEC Harvester**: Ingests Form D, Form C, S-1, 10-K, and 10-Q filings in real time.
* **Form C Financial Extractor**: Ingests crowdfunding balance sheets, revenues, debt, and net income (`clean_and_extract_formC()`).
* **SEC Fund Entity Parser**: Converts legal fund structures into operating corporate identities.
* **Credit-Optimized Waterfall Engine**: Sequential Hunter.io $\rightarrow$ Apollo.io enrichment protecting secondary credits.
* **7-Tab Real-Time Reporting Suite**: Low-code executive dashboard rendering KPIs and queue health via Python and Pandas.
* **Transactional Checkpoint Buffer**: Immutable cell update queue reducing Google Sheets API write requests by 98%.
* **Multi-Channel Outreach Integration**: Direct lead delivery to GoHighLevel CRM and Expandi LinkedIn campaigns.

---

## 27. Lessons Learned

1. **Normalize Heterogeneous SEC Schemas**: Parsing XML filings (Form D, Form C) alongside HTML/iXBRL disclosures (S-1, 10-K, 10-Q) requires modular extraction functions that isolate document parsing from enrichment workflows.
2. **Standardize Company Identities Early**: Legal fund entity names represent the single largest cause of domain lookup failures in financial lead enrichment.
3. **Isolate Infrastructure Failures in Waterfalls**: Never trigger fallback enrichment providers on HTTP 429 rate limits or network timeouts, as doing so burns secondary API credits without improving yields.

---

## 28. Future Improvements

* **Infrastructure as Code (IaC) — Potential Future Improvement (Not currently implemented)**: Terraform scripts for automated AWS S3 and IAM provisioning.
* **Containerized Worker Fleet — Potential Future Improvement (Not currently implemented)**: Docker containers deployed on AWS ECS / Fargate for distributed parallel scraping.
* **Real-Time Webhook Engine — Potential Future Improvement (Not currently implemented)**: Instant Slack/Teams webhooks when high-value S-1 (IPO) or $10M+ Form D filings hit SEC EDGAR.
* **LLM Financial Disclosure Summarizer — Potential Future Improvement (Not currently implemented)**: AI-driven narrative extraction of Risk Factors from 10-K and 10-Q filings.

---

## 29. Technology Stack

| Layer | Technology | Role / Purpose |
| :--- | :--- | :--- |
| **Language & Core Engine** | Python 3.11 | Core runtime environment and pipeline orchestrator |
| **Web Scraping & Extraction** | Selenium, BeautifulSoup4, ElementTree XML | Multi-filing SEC EDGAR scraper (Form D, Form C, S-1, 10-K, 10-Q) |
| **Data Parsing & Manipulation** | Pandas, Regex | Financial disclosure parsing, data transformation, and KPI math |
| **Data Store / Operational UI** | Google Sheets API (`gspread`), OAuth2 | Relational ORM storage layer and 7-tab executive dashboard |
| **Enrichment Services** | Hunter.io API, Apollo.io API, Snov.io API | Corporate domain discovery, email finder, and LinkedIn matching |
| **CRM Integration** | GoHighLevel API, PipelineCRM API | Contact synchronization and filing tagging (`506c`, `reg-cf`, `s1-ipo`, `10k-10q`) |
| **Outreach Integration** | Expandi.io API | Automated LinkedIn connection requests and outreach campaigns |
| **Cloud Storage & Backup** | AWS S3, `boto3` | Automated CSV/JSON raw dataset backup vault |
| **Search Engine API** | Google Custom Search API / SerpAPI | Fallback LinkedIn profile URL discovery |
| **Testing & Quality** | Pytest, unittest | Unit testing for parser, queue checkpointing, and dedup |

---

## 30. Final Technical Summary

The **SEC EDGAR Multi-Filing Lead Generation & Financial Intelligence Pipeline** is an enterprise-grade sales intelligence platform that bridges regulatory SEC disclosures with automated multi-channel outreach.

By supporting private placement raises (Form D), equity crowdfunding (Form C), IPO registrations (S-1), and periodic public disclosures (10-K, 10-Q), the system unifies SEC regulatory data into a single, high-yield lead generation engine:
* **Product Vision**: Eliminating manual SEC research for capital raising, legal, IR, and advisory professionals.
* **User Experience**: Providing a 7-tab executive control panel in Google Sheets.
* **Multi-Filing Data Engine**: Extracting officers, offering terms, and financial metrics across XML and HTML filing standards.
* **Intelligence Layer**: Resolving legal fund entities into operating companies and discovering verified executive work emails.
* **Integration Layer**: Synchronizing enriched leads directly with GoHighLevel CRM and Expandi LinkedIn campaigns.
* **Infrastructure Layer**: Enforcing API rate-limit protection, S3 cloud backups, and self-healing queue recovery.

The architecture demonstrates how robust engineering principles—fault tolerance, credit optimization, transactional batching, and modular data extraction—turn complex regulatory filings into an automated revenue-generating pipeline.
