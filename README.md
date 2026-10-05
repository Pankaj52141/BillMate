<p align="center">
  <img src="https://img.shields.io/badge/React-18.3-61DAFB?style=for-the-badge&logo=react&logoColor=white" alt="React" />
  <img src="https://img.shields.io/badge/TypeScript-5.8-3178C6?style=for-the-badge&logo=typescript&logoColor=white" alt="TypeScript" />
  <img src="https://img.shields.io/badge/Vite-5.4-646CFF?style=for-the-badge&logo=vite&logoColor=white" alt="Vite" />
  <img src="https://img.shields.io/badge/Supabase-PostgreSQL-3FCF8E?style=for-the-badge&logo=supabase&logoColor=white" alt="Supabase" />
  <img src="https://img.shields.io/badge/Tailwind_CSS-3.4-06B6D4?style=for-the-badge&logo=tailwindcss&logoColor=white" alt="Tailwind" />
</p>

# 📄 BillMate

> **A professional-grade GST Invoice Management System** built for Indian small businesses. Create, save, preview, and export GST-compliant tax invoices — all from a single, elegant dashboard.

---

## ✨ Features

| Category | Features |
|---|---|
| **Invoice Creation** | Multi-company support · Auto-incrementing invoice numbers · Multi-item line entries · HSN code tagging · Vehicle & permit tracking |
| **GST Compliance** | Automatic CGST/SGST (intra-state) & IGST (inter-state) calculation · State code–based tax routing · All 36 Indian states & UTs supported |
| **Export & Download** | One-click **Save & Download** as PDF · Individual Excel export · Bulk Excel export from history |
| **GST Reports** | Monthly CA-ready GST reports · B2B / B2C invoice separation · HSN summary sheet · Tax breakdowns with CGST, SGST, IGST totals |
| **Invoice History** | Full searchable history · Company-wise filtering · View, re-download, or delete past invoices |
| **Address Book** | Save & reuse shipping addresses · Auto-fill customer details, state & GSTIN from saved addresses |
| **Security** | Passkey-protected access gate · Supabase Row Level Security · Persistent login via localStorage |
| **UX** | Dark glassmorphism theme · Fully responsive (mobile-first) · PWA-ready viewport · Bottom navigation on mobile |

---

## 🏗️ System Architecture

```mermaid
graph TB
    subgraph CLIENT["🖥️ Client — React SPA"]
        direction TB
        APP["App.tsx<br/><i>Auth Gate + Router</i>"]

        subgraph PAGES["Pages"]
            INDEX["Index.tsx"]
            NF["NotFound.tsx"]
        end

        subgraph COMPONENTS["Components"]
            PK["PasskeyForm<br/><i>Authentication</i>"]
            SIF["SimpleInvoiceForm<br/><i>Core Form + State Machine</i>"]
            IP["InvoicePreview<br/><i>Print-Ready Layout</i>"]
            IH["InvoiceHistory<br/><i>CRUD List + Filters</i>"]
            GST["GSTReports<br/><i>Monthly Tax Reports</i>"]
        end

        subgraph HOOKS["Custom Hooks"]
            UI["useInvoices<br/><i>Invoice CRUD</i>"]
            UA["useAddresses<br/><i>Address CRUD</i>"]
            UT["useToast<br/><i>Notifications</i>"]
        end

        subgraph UTILS["Utilities"]
            EU["exportUtils<br/><i>PDF + Excel Generation</i>"]
        end

        subgraph UIKIT["UI Kit — shadcn/ui"]
            BTN["Button"] --- INP["Input"]
            INP --- SEL["Select"]
            SEL --- TBL["Table"]
            TBL --- TOAST["Toast"]
            TOAST --- BADGE["Badge"]
        end
    end

    subgraph BACKEND["☁️ Backend — Supabase"]
        direction TB
        SB_AUTH["Supabase Client<br/><i>supabase-js</i>"]

        subgraph DB["PostgreSQL Database"]
            T_INV["invoices<br/><i>UUID PK, JSONB items<br/>DECIMAL financials</i>"]
            T_ADDR["addresses<br/><i>Reusable customer<br/>address book</i>"]
            T_PASS["passkeys<br/><i>Access control keys</i>"]
        end

        RLS["Row Level Security<br/><i>Open policy + passkey gate</i>"]
        IDX["Indexes<br/><i>company_type, invoice_no<br/>created_at DESC</i>"]
        TRG["Triggers<br/><i>Auto-update updated_at</i>"]
    end

    subgraph EXPORT["📦 Export Engines"]
        PDF["jsPDF + html2canvas<br/><i>A4 PDF generation</i>"]
        XLS["SheetJS xlsx<br/><i>Multi-sheet Excel export</i>"]
    end

    APP --> PK
    APP --> INDEX --> SIF
    SIF --> IP
    SIF --> IH
    SIF --> GST
    SIF --> UI
    SIF --> UA
    IH --> UI
    GST --> UI
    UI --> SB_AUTH
    UA --> SB_AUTH
    PK --> SB_AUTH
    SB_AUTH --> DB
    DB --> RLS
    DB --> IDX
    DB --> TRG
    IP --> PDF
    EU --> PDF
    EU --> XLS
    IH --> EU
    GST --> EU

    style CLIENT fill:#1a1a2e,stroke:#3b82f6,color:#e5e7eb
    style BACKEND fill:#0d1117,stroke:#3fcf8e,color:#e5e7eb
    style EXPORT fill:#1a1a2e,stroke:#f59e0b,color:#e5e7eb
    style DB fill:#0f172a,stroke:#3fcf8e,color:#e5e7eb
    style COMPONENTS fill:#111827,stroke:#6366f1,color:#e5e7eb
    style HOOKS fill:#111827,stroke:#8b5cf6,color:#e5e7eb
    style UTILS fill:#111827,stroke:#f59e0b,color:#e5e7eb
    style UIKIT fill:#111827,stroke:#64748b,color:#94a3b8
    style PAGES fill:#111827,stroke:#64748b,color:#e5e7eb
```

---

## 🔄 Application Flow

```mermaid
flowchart LR
    A([Open App]) --> B{Authenticated?}
    B -- No --> C[Passkey Form]
    C --> |Verify via Supabase| B
    B -- Yes --> D[Invoice Form]

    D --> E[Fill Details<br/>Company, Customer, Items]
    E --> F{Action?}

    F --> G["Preview Invoice"]
    F --> H["Save and Download"]

    G --> I[Invoice Preview]
    I --> J["Save and Download<br/>from Preview"]
    J --> K["PDF Downloaded"]

    H --> |Save then Show Preview then Auto-Download| K

    K --> L{Next?}
    L --> M[New Invoice]
    L --> N[Invoice History]
    L --> O[GST Reports]

    N --> P[View or Delete or Excel Export]
    O --> Q[Select Month then Download GST Excel]
    M --> D

    style A fill:#3b82f6,stroke:#1e40af,color:#fff
    style K fill:#22c55e,stroke:#16a34a,color:#fff
    style C fill:#ef4444,stroke:#dc2626,color:#fff
```

---

## 📂 Project Structure

```
BillMate/
├── index.html                          # SPA entry point with PWA meta tags
├── vite.config.ts                      # Vite build config with path aliases
├── tailwind.config.ts                  # Tailwind theme with glassmorphism tokens
├── package.json                        # Dependencies and scripts
│
├── supabase/
│   └── migrations/
│       ├── ..._initial.sql             # invoices table + RLS + indexes + triggers
│       ├── ..._unique_constraint.sql   # Unique invoice_no per company
│       └── ..._addresses_table.sql     # Reusable address book table
│
├── src/
│   ├── main.tsx                        # React DOM entry
│   ├── App.tsx                         # Auth gate then Router
│   ├── index.css                       # Global styles + glass design tokens
│   │
│   ├── components/
│   │   ├── ui/                         # shadcn/ui primitives (13 components)
│   │   │   ├── button.tsx
│   │   │   ├── input.tsx
│   │   │   ├── select.tsx
│   │   │   ├── table.tsx
│   │   │   ├── toast.tsx
│   │   │   └── ...
│   │   ├── PasskeyForm.tsx             # Supabase passkey authentication
│   │   ├── SimpleInvoiceForm.tsx       # Main form with all view states
│   │   ├── InvoicePreview.tsx          # Print-ready invoice layout (A4)
│   │   ├── InvoiceHistory.tsx          # History list with CRUD actions
│   │   └── GSTReports.tsx              # Monthly GST report dashboard
│   │
│   ├── hooks/
│   │   ├── useInvoices.ts              # Invoice CRUD + auto-numbering
│   │   ├── useAddresses.ts             # Address book CRUD
│   │   └── use-toast.ts                # Toast notification system
│   │
│   ├── utils/
│   │   └── exportUtils.ts              # PDF generation + Excel exports
│   │                                   #   - downloadPDF()
│   │                                   #   - exportToExcel()
│   │                                   #   - exportAllInvoicesToExcel()
│   │                                   #   - exportMonthlyGSTExcel()
│   │
│   ├── integrations/
│   │   └── supabase/
│   │       ├── client.ts               # Typed Supabase client
│   │       └── types.ts                # Auto-generated DB types
│   │
│   ├── pages/
│   │   ├── Index.tsx                   # Landing page renders SimpleInvoiceForm
│   │   └── NotFound.tsx                # 404 page
│   │
│   └── lib/
│       └── utils.ts                    # cn() helper using clsx + tailwind-merge
│
└── dist/                               # Production build output
```

---

## 🛢️ Database Schema

```mermaid
erDiagram
    INVOICES {
        uuid id PK
        text company_type "CHECK constraint"
        text invoice_no "UNIQUE per company"
        date invoice_date
        text customer_name
        text hsn
        text gstin
        text vehicle_no "nullable"
        text permit_no "nullable"
        text shipping_address "nullable"
        text state
        text state_code
        jsonb items "Array of line items"
        decimal subtotal
        decimal cgst "nullable"
        decimal sgst "nullable"
        decimal igst "nullable"
        decimal total_amount
        timestamptz created_at "auto"
        timestamptz updated_at "auto via trigger"
    }

    ADDRESSES {
        uuid id PK
        text label
        text customer_name "nullable"
        text address
        text state "nullable"
        text state_code "nullable"
        text gstin "nullable"
        timestamptz created_at "auto"
        timestamptz updated_at "auto"
    }

    PASSKEYS {
        uuid id PK
        text key "access passkey"
    }

    INVOICES ||--o{ ADDRESSES : "customer references"
```

---

## 🚀 Getting Started

### Prerequisites

- **Node.js** >= 18
- **npm** >= 9
- A **Supabase** project — [supabase.com](https://supabase.com)

### Installation

```bash
# 1. Clone the repository
git clone https://github.com/your-username/BillMate.git
cd BillMate

# 2. Install dependencies
npm install

# 3. Configure environment variables
cp .env.example .env
```

Add your Supabase credentials to `.env`:

```env
VITE_SUPABASE_URL=https://your-project.supabase.co
VITE_SUPABASE_ANON_KEY=your-anon-key-here
```

### Database Setup

Run the SQL migrations in your Supabase SQL Editor in order:

1. `supabase/migrations/..._initial.sql` — Creates `invoices` table with RLS, indexes, and triggers
2. `supabase/migrations/..._unique_constraint.sql` — Adds unique constraint on `(company_type, invoice_no)`
3. `supabase/migrations/..._addresses_table.sql` — Creates `addresses` table

Then insert a passkey:

```sql
INSERT INTO passkeys (key) VALUES ('your-secure-passkey');
```

### Development

```bash
npm run dev        # Start dev server at http://localhost:5173
npm run build      # Production build to dist/
npm run preview    # Preview production build locally
```

---

## 🧰 Tech Stack

| Layer | Technology | Purpose |
|---|---|---|
| **Framework** | React 18 + TypeScript | Component-based UI with type safety |
| **Build Tool** | Vite 5 (SWC) | Fast HMR and optimized production builds |
| **Styling** | Tailwind CSS 3.4 | Utility-first CSS with custom glassmorphism tokens |
| **UI Components** | shadcn/ui + Radix UI | Accessible, composable primitives |
| **Database** | Supabase (PostgreSQL) | Managed Postgres with REST API and Row Level Security |
| **PDF Export** | jsPDF + html2canvas | Client-side A4 PDF generation from DOM |
| **Excel Export** | SheetJS (xlsx) | Multi-sheet Excel workbook generation |
| **State** | React Hooks + TanStack Query | Local state + server state management |
| **Routing** | React Router v6 | Client-side SPA routing |
| **Icons** | Lucide React | Consistent icon library |

---

## 📊 Excel Export Capabilities

BillMate generates four types of Excel exports:

| Export Type | Sheets | Use Case |
|---|---|---|
| **Single Invoice** | 1 sheet with itemized breakdown + tax summary | Attach to individual invoices |
| **All Invoices** | Summary + per-company detailed sheets | Bulk record keeping |
| **Monthly GST Report** | GST Summary, B2B Sales, B2C Sales, HSN Summary | CA-ready monthly filing |

---

## 🔒 Security Model

```
┌─────────────────────────────────────────────┐
│                  Client                     │
│                                             │
│   ┌──────────┐     ┌──────────────────┐     │
│   │ Passkey  │────>│ localStorage     │     │
│   │ Form     │     │ "authenticated"  │     │
│   └──────────┘     └──────────────────┘     │
│        │                                    │
│        v                                    │
│   Supabase .from("passkeys")               │
│   .eq("key", input)                        │
│        │                                    │
└────────│────────────────────────────────────┘
         v
┌─────────────────────────────────────────────┐
│              Supabase Backend               │
│                                             │
│   Row Level Security: USING (true)          │
│   + Client-side passkey verification        │
│                                             │
└─────────────────────────────────────────────┘
```

> **Note**: Authentication uses a simple passkey model suitable for private/internal business tools. For production multi-user apps, consider integrating Supabase Auth with email/password or OAuth.

---

## 📱 Responsive Design

- **Desktop**: Full form layout with side-by-side fields, table-based history
- **Tablet**: Adaptive grid layouts with comfortable touch targets
- **Mobile**: Bottom navigation bar, card-based history, 44px minimum touch targets, safe-area insets for notched devices

---

## 📄 License

This project is private and intended for internal business use.

---

<p align="center">
  Built with ❤️ using React, Supabase & Vite
</p>