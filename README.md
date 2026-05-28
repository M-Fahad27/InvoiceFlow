<div align="center">

# 🧾 InvoiceFlow AI

### Intelligent Invoice Processing Automation

**Turn invoice chaos into organized data — automatically.**

[![n8n](https://img.shields.io/badge/Built%20with-n8n-FF6D5A?style=for-the-badge&logo=n8n&logoColor=white)](https://n8n.io)
[![Groq](https://img.shields.io/badge/Powered%20by-Groq-000000?style=for-the-badge)](https://groq.com)
[![License](https://img.shields.io/badge/License-MIT-blue?style=for-the-badge)](LICENSE)
[![Status](https://img.shields.io/badge/Status-Production%20Ready-00CEC9?style=for-the-badge)]()

---

*An AI-powered workflow that watches your inbox, extracts invoice data using LLaMA 3.3, validates amounts, routes approvals, and logs everything to Google Sheets — all in under 30 seconds.*

</div>

---

## 🎯 The Problem

Finance teams waste **15-20 minutes per invoice** on manual data entry. Multiply that by hundreds of invoices per month, and you get:

- ❌ **Human errors** in data entry causing payment mistakes
- ❌ **Duplicate payments** slipping through unnoticed
- ❌ **Late payment penalties** from slow processing
- ❌ **Hours of repetitive work** that could be automated
- ❌ **No audit trail** for compliance and tracking

## ✅ The Solution

InvoiceFlow AI is a fully automated invoice processing pipeline that:

| Before (Manual) | After (InvoiceFlow AI) |
|:---|:---|
| 15-20 min per invoice | **30 seconds** |
| ~85% accuracy | **99% accuracy** |
| Business hours only | **24/7 availability** |
| $500+/month in tools | **$0/month** |
| No audit trail | **Full audit logging** |

---

## 🏗️ Architecture

```
┌─────────────────────────────────────────────────────────┐
│                    InvoiceFlow AI                        │
├─────────────────────────────────────────────────────────┤
│                                                         │
│   📧 Gmail Trigger (watches inbox for invoices)         │
│          │                                              │
│          ▼                                              │
│   🔀 Smart Router (PDF or Image?)                       │
│          │                    │                          │
│          ▼                    ▼                          │
│   📄 PDF Extractor     🔍 OCR.space                     │
│   (Extract from File)  (Image → Text)                   │
│          │                    │                          │
│          ▼                    ▼                          │
│   📝 Standardize       📝 Standardize                   │
│   (Set extractedText)  (Set extractedText)              │
│          │                    │                          │
│          └────────┬───────────┘                          │
│                   ▼                                      │
│          🔗 Merge Node                                   │
│                   │                                      │
│                   ▼                                      │
│          🤖 Groq AI (LLaMA 3.3-70B)                     │
│          (Extract structured JSON)                       │
│                   │                                      │
│                   ▼                                      │
│          ⚙️ Validation & Status                          │
│          (Parse JSON + auto-approve logic)               │
│                   │                                      │
│          ┌───────┴────────┐                              │
│          ▼                ▼                              │
│   ✅ Auto-Approve    ⚠️ Human Review                    │
│   (≤ $500)           (> $500)                           │
│          │                │                              │
│          │           📧 Email Alert                      │
│          │           (Send & Wait)                       │
│          │                │                              │
│          │           👤 Approval                         │
│          │           (Approve/Reject)                    │
│          │                │                              │
│          └───────┬────────┘                              │
│                  ▼                                       │
│          📊 Google Sheets                                │
│          (Log invoice data)                              │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

---

## 🛠️ Tech Stack

| Component | Technology | Purpose |
|:---|:---|:---|
| **Workflow Engine** | n8n (self-hosted) | Orchestrates the entire pipeline |
| **AI Model** | LLaMA 3.3-70B via Groq | Extracts structured data from invoice text |
| **OCR** | OCR.space API | Converts invoice images to text |
| **Email** | Gmail API | Trigger, notifications, and approval flow |
| **Storage** | Google Sheets | Invoice data logging and audit trail |
| **PDF Processing** | n8n Extract from File | Extracts text from PDF invoices |

---

## ⚡ Key Features

### 📄 Dual Format Support
Handles both **PDF invoices** and **image invoices** (JPG, PNG). Phone photos of paper invoices work perfectly — great for field teams who snap photos of receipts.

### 🤖 AI-Powered Data Extraction
Uses LLaMA 3.3-70B (via Groq's free API) with a low temperature (0.1) for consistent, accurate extraction of:
- Vendor name and address
- Invoice number, dates, PO number
- Line items with quantities, descriptions, unit prices, and amounts
- Subtotal, tax rate, tax amount, and total
- Currency and payment terms

### ✅ Smart Approval Routing
- **Auto-approve** invoices ≤ $500 (configurable threshold)
- **Human-in-the-loop** for invoices > $500
- Email notification with full invoice summary
- Workflow **pauses and waits** for approval/rejection reply

### 📊 Complete Audit Trail
Every invoice is logged to Google Sheets with:
- All extracted fields
- Processing status (Auto-Approved / Manually Approved / Rejected)
- Timestamp of processing
- Full line items as JSON

### 🔄 Error Handling
- File type detection (PDF vs image routing)
- JSON parsing with markdown cleanup
- Graceful handling of missing fields (null defaults)

---

## 🚀 Getting Started

### Prerequisites

- **n8n** — self-hosted instance ([installation guide](https://docs.n8n.io/hosting/installation/docker/))
- **Groq API Key** — free at [console.groq.com](https://console.groq.com)
- **OCR.space API Key** — free at [ocr.space/ocrapi/freekey](https://ocr.space/ocrapi/freekey)
- **Gmail Account** — for trigger and notifications
- **Google Sheets** — for data storage

### Step 1: Set Up Google Sheet

Create a new Google Sheet named **"InvoiceFlow AI"** with these column headers in Row 1:

```
Invoice Number | Vendor Name | Vendor Address | Bill To | Invoice Date | Due Date | PO Number | Line Items | Subtotal | Tax Rate | Tax Amount | Total Amount | Currency | Payment Terms | Status | Processed At
```

### Step 2: Import the Workflow

1. Open your n8n instance
2. Go to **Workflows** → **Import from File**
3. Upload `InvoiceFlow_AI.json` from this repository
4. Configure credentials for Gmail, Google Sheets, and Groq

### Step 3: Configure Credentials

| Node | Credential Needed |
|:---|:---|
| Gmail Trigger | Gmail OAuth2 |
| Gmail (Get Message) | Gmail OAuth2 |
| Gmail (Send & Wait) | Gmail OAuth2 |
| Groq Chat Model | Groq API Key |
| HTTP Request (OCR) | OCR.space API Key (in header) |
| Google Sheets | Google Sheets OAuth2 |

### Step 4: Customize Settings

In the **Code node** (validation), adjust the auto-approval threshold:

```javascript
// Change 500 to your desired threshold
status: invoice.total_amount <= 500 ? "Auto-Approved" : "Needs Review"
```

In the **Gmail Send & Wait** node, update the recipient email to your approver's address.

### Step 5: Activate

Toggle the workflow to **Active** and send a test invoice to your Gmail!

---

## 📋 AI Extraction Prompt

The system prompt used for Groq AI extraction:

```
You are an invoice data extraction assistant. Extract the following fields
from the invoice text and return ONLY valid JSON, no other text, no markdown
backticks.

Invoice text:
{extractedText}

Return this exact JSON structure filled with extracted values:
{
  "vendor_name": "",
  "vendor_address": "",
  "bill_to_name": "",
  "invoice_number": "",
  "invoice_date": "",
  "due_date": "",
  "po_number": "",
  "line_items": [{"qty": 0, "description": "", "unit_price": 0, "amount": 0}],
  "subtotal": 0,
  "tax_rate": "",
  "tax_amount": 0,
  "total_amount": 0,
  "currency": "",
  "payment_terms": ""
}

If a field is not found, use null.
```

---

## 📊 Sample Output

When an invoice is processed, the Google Sheet row looks like:

| Field | Sample Value |
|:---|:---|
| Invoice Number | INV-2026-1047 |
| Vendor Name | Northwind Digital Solutions |
| Invoice Date | 2026-05-26 |
| Due Date | 2026-06-10 |
| Total Amount | 650.00 |
| Currency | USD |
| Status | Manually Approved |
| Processed At | 2026-05-26T15:30:00.000Z |

---

## 🔧 Customization Ideas

Want to make this project even more impressive? Here are some extensions:

- **🗄️ Database Storage** — Replace Google Sheets with PostgreSQL or Supabase for production use
- **📈 Dashboard** — Connect to Metabase or Grafana for real-time invoice analytics
- **🔔 Slack Notifications** — Add Slack alerts instead of (or alongside) email
- **💰 Multi-Currency** — Add currency conversion using exchange rate APIs
- **📎 Attachment Storage** — Save invoice files to Google Drive or S3 for archival
- **🔍 Duplicate Detection** — Check invoice numbers against existing records before processing
- **📱 WhatsApp Integration** — Allow approvals via WhatsApp for mobile-first teams

---

## 📁 Project Structure

```
InvoiceFlow-AI/
├── InvoiceFlow_AI.json      # n8n workflow (importable)
├── README.md                # This file
├── LICENSE                  # MIT License
├── sample-invoices/         # Test invoices for demo
│   ├── invoice-sample.png
│   └── invoice-sample.pdf
└── screenshots/             # Workflow screenshots
    ├── full-workflow.png
    └── google-sheets-output.png
```

---

## 🤝 Contributing

Contributions are welcome! Feel free to:

1. Fork this repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

---

## 📄 License

This project is licensed under the MIT License — see the [LICENSE](LICENSE) file for details.

---

## 🙏 Acknowledgments

- [n8n](https://n8n.io) — Powerful workflow automation platform
- [Groq](https://groq.com) — Ultra-fast AI inference
- [OCR.space](https://ocr.space) — Free OCR API
- [Meta LLaMA](https://llama.meta.com) — Open-source large language model

---

<div align="center">

**Built with ❤️ by Muhammad Fahad**

*If this project helped you, give it a ⭐ on GitHub!*

[![LinkedIn](https://img.shields.io/badge/Connect-LinkedIn-0A66C2?style=for-the-badge&logo=linkedin)](https://linkedin.com/in/YOUR-PROFILE)
[![Portfolio](https://img.shields.io/badge/View-Portfolio-000?style=for-the-badge&logo=vercel)](https://your-portfolio.com)

</div>
