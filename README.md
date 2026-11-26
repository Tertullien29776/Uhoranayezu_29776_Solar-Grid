Off-Grid Solar Micro-Grid Energy Billing & Battery Health MonitorCourse Code and Name: INSY 8311 & Database Development with PL/SQLAcademic Year: 2026-2027 (Semester I)Instructor: Eric ManiraguhaInstitution: Adventist University of Central Africa (AUCA)Project DetailsStudent Name: Uhoranayezu TertullienStudent ID: 29776Domain: Energy / UtilitiesProject Title: Off-Grid Solar Micro-Grid Energy Billing & Battery Health MonitorGitHub Repository: https://github.com/Tertullien29776/Uhoranayezu_29776_Solar-Grid. Problem StatementRural off-grid communities across East Africa increasingly depend on solar micro-grids for electricity access. However, micro-grid utility operators face two major operational bottlenecks:Financial Sustainability & Payment Defaults: Traditional post-paid or manual billing processes lead to high default rates, delayed revenues, and uncollected debts.Unmonitored Battery Failure: Storage battery banks represent over 40% of initial capital investment. Lack of real-time monitoring of Depth of Discharge (DoD), cell temperature, and State of Health (SoH) results in premature battery failures, costing thousands of dollars in emergency replacements and causing extended power outages.2. Proposed SolutionThis project implements an integrated, 3NF-normalized Oracle Database with PL/SQL business logic to manage solar micro-grid billing and storage health. The system automates prepaid credit calculations, processes hourly power consumption, instantly enforces virtual meter disconnections when credit drops to zero, and continuously tracks battery health metrics (voltage, temperature, cycle counts) to trigger automated preventive maintenance alerts before hardware failure.3. Data SourcePrimary Dataset: Open Solar Micro-Grid Telemetry & Hourly Consumption Dataset (available via Kaggle / Harvard Dataverse / HDX).Data Characteristics: Contains 1,500+ hourly readings including solar production ($kW$), customer consumption ($kWh$), battery voltage, discharge current, State of Charge ($SoC\ \%$), and ambient temperature ($\text{°C}$).Licence: Creative Commons Attribution (CC BY 4.0) for non-commercial educational use.4. Planned Tables (3NF Design)CUSTOMERS — Customer profiles, installation addresses, contact details, and account creation dates.METERS — Smart meter metadata, assigned customer ID, micro-grid zone, and connection status (ACTIVE / DISCONNECTED).PREPAID_ACCOUNTS — Customer credit balances ($\$$), unit tariff rates ($\$/kWh$), and cumulative $kWh$ consumed.ENERGY_TRANSACTIONS — Mobile money top-up records, payment reference codes, amount paid, and timestamp.CONSUMPTION_LOGS — Hourly meter readings logging $kWh$ used, peak voltage, and reading timestamp.BATTERY_BANKS — Micro-grid battery bank specifications, rated capacity ($Ah$), nominal voltage, and installation date.BATTERY_HEALTH_LOGS — Periodic sensor readings: Voltage, Current, Temperature ($\text{°C}$), $SoC\ (\%)$, and calculated $SoH\ (\%)$.MAINTENANCE_ALERTS — Automated logs for low-balance meter cuts, over-temperature flags, and battery replacement tickets.5. Planned PL/SQL FeaturesProceduresprocess_prepaid_topup: Credits user account and automatically reactivates suspended meter connections.deduct_hourly_consumption: Deducts credit balance based on hourly $kWh$ usage and logs transaction metrics.Functionscalculate_battery_soh: Computes current battery State of Health ($SoH\ \%$) using historical charge cycles and operating temperature.check_customer_balance: Returns remaining credit and estimated days of electricity remaining.Cursorsc_depleted_accounts: Iterates over customer accounts with zero balance to trigger disconnection events.c_critical_batteries: Queries battery banks exhibiting $SoH < 70\%$ for technical dispatch.Triggerstrg_auto_disconnect: Database trigger that sets meter status to DISCONNECTED as soon as account credit reaches $\$0.00$.trg_flag_battery_warning: Inserts an automated alert into MAINTENANCE_ALERTS if battery temperature exceeds $45\text{°C}$ or $SoC$ drops below $20\%$.trg_audit_balance_changes: Audits all manual and system-driven credit adjustments for security tracking.Packagespkg_microgrid_billing: Encapsulates all tariff, top-up, rating, and disconnect logic.pkg_battery_telemetry: Encapsulates health calculation models, alert triggers, and maintenance workflows.6. Innovation PlanSelected Option: Analytics Dashboard (Power BI / Grafana connected live to Oracle Database via ODBC).Description: Provides a live operational cockpit displaying total energy sold, daily revenue trends, active vs. cut-off meters, real-time battery State of Health ($SoH$) gauges, and immediate alerts for degrading battery storage.7. References (APA)Balla, A. et al. (2022). Prepaid metering for rural solar microgrids. Journal of Renewable Energy, 45, 112–120.IEEE. (2023). IEEE Guide for Smart Grid Interoperability and Storage Monitoring Standards. IEEE Xplore.National Institute of Statistics of Rwanda (NISR). (2024). Off-grid solar and rural electrification progress report. NISR.Oracle Corporation. (2026). Oracle Database PL/SQL Language Reference (19c/21c). Oracle Documentation.



Phase I: Business Process Modeling & Requirements Foundation

Course Code & Name: INSY 8311 & Database Development with PL/SQL

Project Title: Off-Grid Solar Microgrid Energy Billing and Battery Health Monitor

Academic Year: 2026–2027 (Semester I)

Instructor: Eric Maniraguha

1. Executive Summary & System Overview

The Off-Grid Solar Microgrid Energy Billing and Battery Health Monitor system is designed to automate energy metering, billing/prepaid credit management, and real-time battery degradation monitoring for decentralized solar microgrids.

Before designing and implementing the database backend, this document defines the business process flow, identifies operational actors, models message and sequence interactions, and outlines the foundational CRUD (Create, Read, Update, Delete) requirements for subsequent database phases.

2. Process Actors & Responsibilities

Actor / Lane

Pool / Subsystem

Responsibilities

Microgrid Operator / Customer Portal

Top Pool

Initiates payment top-ups, views telemetry summaries, monitors battery health alerts, and receives transaction/billing receipts.

Core Billing & Telemetry Subsystem

Middle Pool (Upper Lane)

Ingests meter and battery sensor data, validates account credit balances, evaluates system thresholds, and computes usage billing.

Battery Health & Diagnostic Subsystem

Middle Pool (Lower Lane)

Executes deep battery state-of-charge (SoC) / state-of-health (SoH) diagnostics when critical operational thresholds are triggered.

3. Business Process Modeling (BPMN Mapping)

The business process is modeled across two main pools using standard BPMN notation:

3.1 Top Pool — Customer & Operator Interface Flow

Start Event: Customer submits an energy purchase/top-up or system initiates a periodic telemetry pulse.

Task 1 (Submit Top-Up / Usage Inquiry): Dispatches a message to the Core Billing Subsystem.

Task 2–4 (Receive Status & Review Telemetry): Receives validation response from backend; retrieves customer account status and active tariff schedules.

Exclusive Gateway (X - Decision): Routes into one of three mutually exclusive paths:

Path A: Standard Prepaid Energy Top-Up Processing.

Path B: Low Credit / Over-usage Enforcement.

Path C: Battery Degradation / High Temperature Warning Trigger.

Merge Gateway (X): Consolidates paths and forwards operational instructions to backend.

Task 6–8 (Confirmation & Receipt Generation): Receives final execution acknowledgment from backend, updates customer balance display, logs receipt, and closes process at End Event.

3.2 Middle Pool — Core Billing & Telemetry Subsystem (Upper Lane)

Task 1–2 (Ingest & Validate Request): Receives incoming message from Top Pool, logs request payload, and retrieves active customer/meter records.

First Gateway (X):

Branch 1: Sends current balance/health status back to Top Pool (Task 2).

Branch 2: Flags account/meter state for transaction processing.

Task 5 & Second Gateway (X): Processes payment execution or usage deduction:

Upper Branch (Task 6 → Task 8): Completes standard balance adjustment and generates digital receipt payload.

Lower Branch (Task 7): Detects abnormal telemetry (e.g., rapid battery drop, cell imbalance) and routes execution to the Lower Lane.

3.3 Middle Pool — Battery Health & Diagnostic Subsystem (Lower Lane)

Task L1 (Fetch Historical Sensor Data): Extracts time-series battery telemetry (voltage, cell temperature, charge cycles).

Task L2 (Run SoH Algorithm): Computes estimated remaining useful life (RUL) and checks thermal safety parameters.

Task L3 (Log Diagnostic Output): Updates the battery health log and returns control to Task 8 in the Upper Lane for final report packaging.

4. Data Operations Matrix (CRUD Analysis)

To lay the foundation for database design, the business process steps map directly to core entity operations:

BPMN Activity

Target Entity / Table

CRUD Operation

Description

Task 1 (Top Pool)

Payment_Transactions

Create

Inserts a pending top-up or usage record.

Task 2 (Top Pool)

Customers, Meters

Retrieve

Fetches active customer profile and linked meter ID.

Task 4 (Top Pool)

Tariffs, Telemetry_Logs

Retrieve

Reads current energy pricing rate and latest telemetry point.

Gateway Paths (Top Pool)

Accounts

Update

Deducts credit or updates account status (Active / Suspended).

Task 1–2 (Middle Upper)

System_Audit_Logs

Create

Logs incoming message headers and API call requests.

Task 6 (Middle Upper)

Invoices_Receipts

Create / Update

Generates invoice record and sets status to 'PAID'.

Task L1 (Middle Lower)

Battery_Telemetry

Retrieve

Reads last 100 voltage and thermal sensor data points.

Task L3 (Middle Lower)

Battery_Health_Logs

Create / Update

Writes computed SoH % and alerts field maintenance if SoH < 70%.

Process Cleanup (End)

Staging_Queue

Delete

Clears processed temporary queue messages from memory.

5. Approved Modeling Tools & Guidelines Compliance

In compliance with Phase I requirements:

Tool Used: Diagram designed natively in draw.io / Lucidchart.

Academic Integrity: No AI-generated images or external copied assets were used in diagram creation.

BPMN Semantics: Exclusive gateways (X) enforce mutually exclusive business paths (single path selection based on condition evaluation).

6. Next Steps (Database Design Integration)

The business activities and CRUD operations established in this phase will directly inform:

Conceptual Data Modeling (Entity-Relationship Diagram / ERD).

Logical Database Schema Normalization (3NF).

PL/SQL Stored Procedures, Functions, and Triggers in subsequent course phases.


# Off-Grid Solar Micro-Grid Energy Billing & Battery Health Monitor

**Student:** Uhoranayezu Tertullien  
**Student ID:** 29776  
**Course:** INSY 8311 & Database Development with PL/SQL  
**Instructor:** Eric Maniraguha  

---

## Phase II — Data Sourcing, Normalization and Logical Design

### Overview
This phase organizes raw solar micro-grid telemetry and billing data into normalized relational tables in **Third Normal Form (3NF)** as the blueprint for Oracle implementation in Phase IV.

### Repository Structure for Phase II
* `data/raw/` — Unmodified raw dataset ($\ge 500$ records, with personal data anonymised)
* `data/dataset_card.md` — Dataset metadata (source link, licence, format, size, collection date, record meaning) and raw data quality counts
* `docs/design/normalization.md` — Step-by-step normalization from UNF to 1NF, 2NF, and 3NF with sample rows, dependency removal notes, field-to-table mappings, and Phase IV type-conversion/cleaning checklist
* `docs/design/erd.png` — Crow's-foot logical Entity-Relationship Diagram showing entities, primary/foreign keys, relationships, cardinality, and optionality
* `docs/design/data_dictionary.md` — Column definitions, meanings, and required/unique constraints (without Oracle data types or sizes)

### Planned 3NF Entities
* `CUSTOMERS` — Anonymised customer profiles and account metadata
* `METERS` — Smart meter identifiers, micro-grid zone assignment, and connection status
* `PREPAID_ACCOUNTS` — Prepaid balances, tariff rates, and cumulative energy usage
* `ENERGY_TRANSACTIONS` — Top-up and billing transaction history
* `CONSUMPTION_LOGS` — Interval energy consumption readings
* `BATTERY_BANKS` — Micro-grid battery bank specifications and installation details
* `BATTERY_HEALTH_LOGS` — Periodic battery telemetry (voltage, current, temperature, State of Charge, Depth of Discharge, State of Health)
* `MAINTENANCE_ALERTS` — Alerts for zero/low credit, meter disconnections, and battery health issues
