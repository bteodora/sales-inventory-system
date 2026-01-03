<div align="center">
  <img src="Logo_poslovna.png" alt="Business Informatics ERP Logo" width="300"/>
  
  <br><br>

  <h1>Enterprise Resource Planning (ERP): Sales & Inventory Subsystems</h1>
  <h3>Integrated Business Process Management Solution</h3>

  <p>
    <img src="https://img.shields.io/badge/Platform-Mendix_Studio_Pro-2565e8?style=for-the-badge&logo=mendix&logoColor=white" alt="Mendix"/>
    <img src="https://img.shields.io/badge/Architecture-Model--Driven-purple?style=for-the-badge" alt="Model Driven"/>
    <img src="https://img.shields.io/badge/Domain-Supply_Chain_Management-success?style=for-the-badge" alt="SCM"/>
    <img src="https://img.shields.io/badge/Type-Enterprise_Application-gray?style=for-the-badge" alt="Enterprise"/>
  </p>

  <p>
    <strong>A comprehensive, monolithic information system designed to digitize and automate the Order-to-Cash (O2C) and Procure-to-Pay (P2P) lifecycles.</strong>
  </p>
</div>

---

## Executive Summary

This project represents a fully functional **ERP module** developed within the **Mendix Low-Code Platform**. It bridges the gap between commercial sales operations and physical inventory management, ensuring strict data consistency and real-time synchronization between departments.

The system is engineered to handle complex business logic, including **temporal pricing models**, **VAT (Value Added Tax) compliance**, **inventory reservation protocols**, and the lifecycle management of transactional documents (Invoices, Delivery Notes, Goods Receipts).

---

## System Architecture & Domain Model

The solution is built upon a relational domain model that enforces referential integrity across two primary subsystems.

### 1. Sales Subsystem (Commercial Logic)
Designed for the **Sales Representative** persona, this module handles the commercial aspect of the business, focusing on financial accuracy and document generation.

*   **Order Management:**
    *   Digital ingestion of **Customer Orders** (Narudžbenica).
    *   Automated workflow triggers to validate customer credit standing and stock availability.
*   **Dynamic Invoicing Engine:**
    *   **Workflow:** Supports generation of Invoices directly from Orders or as standalone documents (Direct Entry).
    *   **Temporal Pricing Logic:** The system implements a sophisticated pricing engine that resolves item costs and VAT rates based on the *transaction date*, cross-referencing valid Price Lists (Cenovnik) active at that specific moment in time.
    *   **Financial Calculation:** Real-time computation of net amounts, tax bases, and gross totals at both the *line-item level* and *document header level*.
*   **Regulatory Compliance:**
    *   Automatic generation of the **Sales Invoice Journal (KIF - Knjiga izlaznih faktura)** for specific fiscal periods.
    *   Adherence to official document formatting standards for external communication.

### 2. Warehouse Management Subsystem (WMS)
Designed for **Inventory Managers**, this module governs the physical flow of goods, ensuring that system data mirrors physical reality.

*   **Document Lifecycle Management:**
    *   Full support for **Goods Receipt Notes (Primka)**, **Delivery Notes (Otpremnica)**, and **Inter-Warehouse Transfers (Međumagacinski promet)**.
    *   **State Management:** Documents proceed through strict states: *Draft* $\rightarrow$ *Posted (Knjiženo)* $\rightarrow$ *Reversed (Stornirano)*. Posting a document triggers immutable inventory ledger updates.
*   **Inventory Initialization:**
    *   Procedures for opening **Initial Stock (Početno Stanje)** at the beginning of fiscal years.
*   **Advanced Reporting:**
    *   **Stock List (Lager Lista):** Real-time aggregation of quantities and financial values per warehouse.
    *   **Stock Card Analytics (Magacinska Kartica):** Detailed chronological history of all movements (in/out) for specific SKUs, providing full traceability.

---

## Key Technical Features & Business Logic

The core value of this project lies in the complex Microflow logic implemented to ensure business rules are never violated.

### Cross-Module Integration: The Reservation Protocol
A critical engineering challenge in ERP systems is preventing "overselling" (selling items that physically exist but are promised to another customer).

1.  **Stock Availability Check:**
    *   When a Sales Representative adds an item to an Invoice, a Microflow triggers a synchronous check against the WMS module.
    *   The system queries the specific **Stock Card** for the requested SKU.
2.  **The "Reserved Quantity" Attribute:**
    *   The Domain Model includes a dedicated `ReservedQuantity` attribute on the Stock Entity.
    *   **Logic:** $AvailableStock = PhysicalStock - ReservedQuantity$.
    *   If the requested amount exceeds $AvailableStock$, the transaction is blocked, and a warning is issued to the user.
3.  **Transactional Commit:**
    *   Upon finalizing an invoice, the system automatically increments the `ReservedQuantity`.
    *   Only when the **Delivery Note (Otpremnica)** is generated and posted does the system decrement the physical stock and clear the reservation.

### Automated Document Transformation
To reduce human error, the system implements **Document Conversion Workflows**:
*   **Invoice $\rightarrow$ Delivery Note:** The system can auto-generate a Delivery Note based on the contents of a finalized Invoice, inheriting all line items, customer data, and reference numbers, ensuring a perfect match between financial and logistical records.

---

## Business Intelligence & Reporting

The application utilizes document generation templates to produce legally binding documents and analytical reports.

| Report Type | Description | Target Audience |
| :--- | :--- | :--- |
| **Commercial Invoice** | A formal, legally compliant PDF document detailing line items, tax calculations, and payment terms. | Customers |
| **Sales Journal (KIF)** | An aggregated chronological registry of all issued invoices within a date range, used for VAT reporting. | Accounting / Tax Auth. |
| **Inventory Stock List** | A snapshot of current inventory levels and total valuation across all warehouses. | Warehouse Manager |
| **Stock Card History** | An audit trail report showing every single movement (Debit/Credit) for a specific product. | Auditors / Logistics |

---

## Tech Stack

*   **Development Platform:** Mendix Studio Pro (Low-Code/RAD)
*   **Database:** HSQLDB (Local) / PostgreSQL (Production capable)
*   **Frontend:** Atlas UI (Responsive Web Framework)
*   **Logic Layer:** Mendix Microflows & Nanoflows (Visual Logic)
*   **Query Language:** OQL (Object Query Language) for complex reporting datasets.

---

## Compliance Note

> *All generated documents follow the official regulatory forms required by local business laws. The system enforces strict ACID properties on transaction posting to ensure financial data integrity.*
