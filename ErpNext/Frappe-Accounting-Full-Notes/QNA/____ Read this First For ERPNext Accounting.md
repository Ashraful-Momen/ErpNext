একদম জীবনে না ভোলার মতো সহজভাবে বুঝাই। ERPNext/Frappe Accounting-এর সবচেয়ে বড় ভয় "Debit/Credit" — আসলে এটা + / - না, এটা কোন পাশে লিখছো সেটার নাম। Odoo হোক আর ERPNext হোক, এই Rule একই। শুধু Software-এর ভেতরের Structure আলাদা।

### ১. Accounting-এর মূল সমীকরণ

Asset = Liability + Equity

বাংলায়:

* Asset = ব্যবসার যা আছে (Cash, Bank, Stock, Computer)
* Liability (L) = অন্যের কাছে দেনা (Loan, Payable)
* Equity (E) = মালিকের টাকা

ERPNext-এও এটাই পুরো ভিত্তি। `root_type` নামের একটা Field-ই বলে দেয় account-টা Asset না Liability না Equity না Income না Expense।

---

### ২. Debit/Credit আসলে কী?

প্রতিটি account-এর দুইটা কলাম:

```
Debit (Dr)   |   Credit (Cr)
বাম পাশ       |   ডান পাশ
```

> **Debit = Left, Credit = Right।** Debit মানেই + না, Credit মানেই - না।

---

### ৩. সবচেয়ে গুরুত্বপূর্ণ টেবিল (মুখস্থ করো)

| Account Type | Increase | Decrease |
| ------------ | -------- | -------- |
| Asset        | Debit    | Credit   |
| Expense      | Debit    | Credit   |
| Liability    | Credit   | Debit    |
| Equity       | Credit   | Debit    |
| Income       | Credit   | Debit    |

এক লাইনে: **Asset/Expense বাড়লে Debit; Liability/Equity/Income বাড়লে Credit।**

---

### ৪. DEA / LIC Mnemonic

```
DEA → Debit বাড়ে          LIC → Credit বাড়ে
D = (Dividend/Withdrawal)  L = Liability
E = Expense                I = Income
A = Asset                  C = Capital (Equity)
```

---

### ৫. ERPNext-এ এই Rule কোথায় কাজ করে?

ERPNext-এ প্রতিটা Account-এর একটা `account_type` থাকে। যেমন:

| ERPNext account_type       | বাস্তব অর্থ   | DAE না LIC |
| -------------------------- | ------------- | ---------- |
| Bank / Cash                | Asset         | ✅ DAE     |
| Receivable                 | Asset (পাওনা) | ✅ DAE     |
| Stock                      | Asset (মাল)   | ✅ DAE     |
| Fixed Asset                | Asset         | ✅ DAE     |
| Payable                    | Liability     | ❌ LIC     |
| Tax                        | Liability/Asset (কনটেক্সটে) | — |
| Equity                     | Equity        | ❌ LIC     |
| Income Account             | Income        | ❌ LIC     |
| Expense Account            | Expense       | ✅ DAE     |
| Cost of Goods Sold         | Expense       | ✅ DAE     |
| Depreciation               | Expense       | ✅ DAE     |
| Accumulated Depreciation   | Contra-Asset  | ❌ LIC     |

> ERPNext `account_type` দেখেই বুঝে: রিপোর্টে কোথায় বসবে, কোন document-এ select করা যাবে (যেমন শুধু `Payable` account-ই Vendor Bill-এ আসবে)।

---

### ৬. ERPNext-এর সবচেয়ে বড় পার্থক্য — Single Ledger

Odoo-তে সব document-ই `account.move`। ERPNext-এ ব্যাপারটা উল্টো:

```
Sales Invoice ─┐
Purchase Invoice ─┤
Payment Entry ────┼──►  GL Entry (tabGL Entry)  ◄── একটাই Ledger
Journal Entry ────┤
Delivery Note ────┘
```

প্রতিটা document Submit করলেই নিজে নিজে **GL Entry** (General Ledger Entry) তৈরি করে। আর সব Report আসে এই **GL Entry** থেকে।

> মনে রাখো: **ERPNext = Document + GL Entry।** Document হলো source, GL Entry হলো খাতার লেখা।

---

### ৭. একটা ছোট উদাহরণ

Customer cash দিয়ে পণ্য কিনল $200।

| Account        | Debit | Credit |
| -------------- | ----- | ------ |
| Cash (Asset)   | 200   |        |
| Sales (Income) |       | 200    |
| Total          | 200   | 200    |

Debit Total = Credit Total — এটা ভাঙলেই কোনো Entry-ই হবে না (Odoo আর ERPNext দুটোতেই)।

---

### ৮. ৩০ সেকেন্ড টেস্ট

* Cash বাড়ে → Debit ✔️
* Bank Loan বাড়ে → Credit ✔️
* Sales বাড়ে → Credit ✔️
* Salary Expense বাড়ে → Debit ✔️
* Accounts Payable কমে → Debit ✔️
* Owner Capital কমে → Debit ✔️

সব ঠিক হলে তুমি Debit/Credit-এর মূল ধারণা ধরে ফেলেছ।

---

### শেষ কথা

```
Debit/Credit  = Left/Right, Plus/Minus না
account_type  = কোন account স্বাভাবিকভাবে Dr না Cr balance বহন করে
GL Entry      = প্রতিটা transaction-এর লিখিত Record (ERPNext-এর একটাই Ledger)
```

এই তিনটা জিনিস মাথায় থাকলে ERPNext Accounting-এর ৮০% শেষ।
