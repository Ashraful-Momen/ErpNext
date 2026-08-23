
হ্যাঁ, Odoo/ERPNext শিখতে গেলে **Asset, Liability, Equity, Revenue, Expense, AR, AP**—এগুলো শুরুতে গুলিয়ে যাওয়া খুবই স্বাভাবিক। সহজ একটা mental model ধরলে আর গুলাবে না।

## ১. আগে ৫টা মূল Account Type মনে রাখো

Accounting-এর মূল equation:

**Assets = Liabilities + Equity**

আর Profit/Loss-এর জন্য:

**Profit = Revenue − Expense**

### সহজ ভাষায়

| Type                     | মানে                                  | উদাহরণ                               |
| ------------------------ | ----------------------------------------- | ------------------------------------------ |
| **Asset**          | কোম্পানির যা আছে/পাওনা | Cash, Bank, Inventory, Accounts Receivable |
| **Liability**      | কোম্পানির যা দিতে হবে   | Loan, Accounts Payable, VAT Payable        |
| **Equity**         | Owner-এর claim                          | Capital, Retained Earnings                 |
| **Revenue/Income** | কোম্পানি যা আয় করেছে     | Sales Revenue, Service Income              |
| **Expense**        | আয় করতে যে খরচ হয়েছে      | Salary, Rent, Electricity                  |

---

# ২. সবচেয়ে গুরুত্বপূর্ণ: AR বনাম AP

এখানেই সাধারণত বেশি confusion হয়।

### AR = Accounts Receivable

**Customer-এর কাছে আমার টাকা পাওনা।**

তাই:

> **AR = Asset**

Example:

তুমি customer-কে 100,000 টাকার product বাকিতে বিক্রি করলে।

```text
Customer owes me 100,000
```

এই 100,000 টাকা তুমি ভবিষ্যতে পাবে।

তাই:

**Accounts Receivable → Asset**

Entry:

```text
Dr. Accounts Receivable    100,000
    Cr. Sales Revenue              100,000
```

এখানে:

* AR ↑ → Asset ↑
* Sales ↑ → Revenue ↑

---

# ৩. AP = Accounts Payable

**Supplier-এর কাছে আমার টাকা দিতে হবে।**

তাই:

> **AP = Liability**

Example:

Supplier থেকে 100,000 টাকার goods কিনলে কিন্তু এখনো payment দিলে না।

```text
I owe supplier 100,000
```

তাই:

**Accounts Payable → Liability**

Entry:

```text
Dr. Inventory              100,000
    Cr. Accounts Payable           100,000
```

এখানে:

* Inventory ↑ → Asset ↑
* AP ↑ → Liability ↑

---

# ৪. একটা golden rule

এটা মনে রাখলে AR/AP আর ভুল হবে না:

> **আমার কাছে আসবে → Asset**
> **আমার কাছ থেকে যাবে → Liability**

অর্থাৎ:

```text
Customer আমাকে টাকা দেবে
        ↓
       AR
        ↓
      Asset
```

কিন্তু:

```text
Supplier-কে আমাকে টাকা দিতে হবে
        ↓
       AP
        ↓
     Liability
```

---

# ৫. Expense কোথায়?

এখানে আরেকটা গুরুত্বপূর্ণ distinction আছে।

ধরো তুমি office-এর জন্য:

**Rent = 50,000**

এটা Expense।

```text
Dr. Rent Expense          50,000
    Cr. Cash/Bank                 50,000
```

অর্থাৎ:

**Expense ≠ Liability**

কিন্তু expense-এর টাকা যদি এখনো না দাও, তখন liability তৈরি হতে পারে।

Example:

Salary expense হয়েছে 100,000, কিন্তু employee-দের এখনো payment করা হয়নি।

```text
Dr. Salary Expense        100,000
    Cr. Salary Payable            100,000
```

এখানে:

**Salary Expense → Expense**

আর

**Salary Payable → Liability**

এই দুইটা এক জিনিস না।

---

# ৬. Expense বনাম Liability

এটা খুব ভালোভাবে বুঝো।

ধরো:

### Case 1 — Electricity bill paid immediately

Electricity bill = 10,000

```text
Dr. Electricity Expense    10,000
    Cr. Cash                       10,000
```

এখানে:

```text
Expense ↑
Cash ↓
```

কোনো liability থাকল না।

---

### Case 2 — Electricity bill এখনো unpaid

```text
Dr. Electricity Expense    10,000
    Cr. Electricity Payable        10,000
```

এখন:

```text
Expense ↑
Liability ↑
```

অর্থাৎ **একটা transaction একই সাথে Expense এবং Liability তৈরি করতে পারে**, কিন্তু account দুটির nature আলাদা।

---

# ৭. Purchase আর Expense-ও এক না

এটাও ERP-তে খুব গুরুত্বপূর্ণ।

ধরো তুমি 500,000 টাকার inventory কিনলে।

এটা সবসময় সরাসরি expense নয়।

```text
Dr. Inventory              500,000
    Cr. Accounts Payable           500,000
```

এখানে:

```text
Inventory → Asset
AP        → Liability
```

যখন inventory বিক্রি হবে, তখন তার cost expense হবে:

```text
Dr. Cost of Goods Sold     500,000
    Cr. Inventory                  500,000
```

তখন:

```text
Inventory ↓
COGS ↑
```

অর্থাৎ:

> **Purchase of inventory ≠ immediate expense**

---

# ৮. Sales-এর ক্ষেত্রেও একই concept

ধরো তুমি 100,000 টাকার product বিক্রি করলে।

Customer এখনো টাকা দেয়নি:

```text
Dr. Accounts Receivable    100,000
    Cr. Sales Revenue              100,000
```

এখানে:

```text
AR → Asset
Sales → Revenue
```

আর product-এর cost যদি 60,000 হয়:

```text
Dr. Cost of Goods Sold      60,000
    Cr. Inventory                   60,000
```

তাহলে পুরো picture:

```text
Revenue        = 100,000
COGS           = 60,000
Gross Profit   = 40,000
```

---

# ৯. Odoo/ERPNext-এ Transaction বুঝতে এই flow ধরো

### Purchase

```text
Supplier
   ↓
Purchase
   ↓
Inventory / Expense
   ↓
AP
   ↓
Payment
```

### Sales

```text
Customer
   ↓
Sales
   ↓
Revenue
   ↓
AR
   ↓
Receipt
```

---

# ১০. Payment করলে কী হয়?

ধরো:

```text
AR = 100,000
```

Customer টাকা দিল।

```text
Dr. Bank                  100,000
    Cr. Accounts Receivable       100,000
```

এখানে **Revenue আবার তৈরি হবে না।**

কারণ revenue আগেই sales-এর সময় recognize হয়েছে।

শুধু:

```text
AR ↓
Bank ↑
```

এটাই খুব গুরুত্বপূর্ণ।

একইভাবে AP payment:

```text
Dr. Accounts Payable      100,000
    Cr. Bank                       100,000
```

এখানে নতুন expense তৈরি হচ্ছে না।

```text
AP ↓
Bank ↓
```

কারণ expense/purchase আগেই recognize হয়েছিল।

---

# ১১. একটা Complete ERP Example

ধরো তুমি ব্যবসা শুরু করলে:

### Owner capital দিল 1,000,000

```text
Dr. Bank                  1,000,000
    Cr. Owner Capital             1,000,000
```

```text
Bank → Asset
Capital → Equity
```

---

### Supplier থেকে inventory কিনলে 300,000 বাকিতে

```text
Dr. Inventory               300,000
    Cr. AP                          300,000
```

```text
Inventory → Asset
AP → Liability
```

---

### Customer-কে 500,000 টাকার goods বাকিতে বিক্রি

```text
Dr. AR                      500,000
    Cr. Sales Revenue               500,000
```

```text
AR → Asset
Revenue → Income
```

---

### Sold goods-এর cost 300,000

```text
Dr. COGS                    300,000
    Cr. Inventory                   300,000
```

```text
COGS → Expense
Inventory → Asset
```

---

### Customer 500,000 payment করল

```text
Dr. Bank                    500,000
    Cr. AR                          500,000
```

---

### Supplier-কে 300,000 payment করলাম

```text
Dr. AP                      300,000
    Cr. Bank                        300,000
```

---

## ১২. এখন ERP-তে Account Head দেখলে এভাবে চিনবে

```text
ASSETS
├── Cash
├── Bank
├── Inventory
├── Accounts Receivable
└── Fixed Assets

LIABILITIES
├── Accounts Payable
├── Bank Loan
├── Salary Payable
├── VAT Payable
└── Tax Payable

EQUITY
├── Owner Capital
└── Retained Earnings

REVENUE
├── Sales
└── Service Income

EXPENSE
├── Salary Expense
├── Rent Expense
├── Electricity Expense
├── COGS
└── Depreciation Expense
```

### সবচেয়ে ছোট mnemonic:

**AR → Asset**
**AP → Liability**

**Customer owes me → AR → Asset**

**I owe Supplier → AP → Liability**

**Money earned → Revenue**

**Money spent/consumed to run business → Expense**

**Something I own/control → Asset**

**Something I must pay → Liability**

---

আর একটা বিষয়: **Odoo/ERPNext-এ “Expense” আর “Payable” একসাথে transaction-এ আসতে পারে**, তাই UI দেখে confused হওয়া স্বাভাবিক। Accounting বুঝতে হলে প্রতিটি transaction-এর **Debit account + Credit account + account nature** দেখতে হবে—শুধু document-এর নাম (Purchase Invoice, Sales Invoice, Payment Entry) দেখে নয়।
