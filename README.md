# 🧠 n8n — RAG Basic (Retrieval-Augmented Generation)

একটি Basic RAG Pipeline তৈরির ধাপে ধাপে গাইড।  
এই Workflow-এ Document Upload করা হবে, Vector Store-এ রাখা হবে, এবং AI Agent প্রশ্নের উত্তর দেবে।

---

## 📋 ওভারভিউ

| বিষয় | বিবরণ |
|---|---|
| **টুল** | n8n Workflow Automation |
| **উদ্দেশ্য** | Document থেকে তথ্য বের করে AI দিয়ে প্রশ্নের উত্তর দেওয়া |
| **AI Model** | Google Gemini |
| **Embedding** | Google Gemini Embeddings |
| **Vector Store** | Simple Vector Store (In-Memory) |
| **ট্রিগার** | Webhook (HTTP POST) |

---

## 🔗 সম্পূর্ণ Workflow ফ্লো

```
Webhook (POST)
    ↓
Extract from File
  (PDF/Doc থেকে text বের করা)
    ↓
Simple Vector Store
  (Document embed করে store করা)
    ├── Embeddings Google Gemini   ← embedding তৈরি
    └── Default Data Loader        ← data load করা
    ↓
AI Agent
  (প্রশ্নের উত্তর দেওয়া)
    ├── Google Gemini Chat Model   ← LLM
    ├── Simple Memory              ← কথোপকথন মনে রাখা
    └── Simple Vector Store1       ← knowledge retrieval
          └── Embeddings Google Gemini1
    ↓
Respond to Webhook
  (উত্তর client-এ ফেরত পাঠানো)
```

---

## ⚙️ ধাপ ১ — Webhook সেটআপ

> **উদ্দেশ্য:** Frontend থেকে HTTP POST request গ্রহণ করা।

**ধাপসমূহ:**

1. **Add first step** বাটনে ক্লিক করো।
2. Search বক্সে টাইপ করো: `webhook` → **Webhook** ক্লিক করো।
3. নিচের মতো কনফিগার করো:

| ফিল্ড | মান |
|---|---|
| **HTTP Method** | `POST` |
| **Respond** | `Using 'Respond to Webhook' Node` *(শেষে আপডেট করতে হবে)* |

4. **Test URL** কপি করো।
5. `index.html` ফাইলের `try` block-এ URL টি **paste** করো।
6. **Listen with Test Event** বাটনে ক্লিক করো।
7. Live Server খুলে ফর্মে value দাও → Webhook টেস্ট হবে।

```
📌 নোট: Webhook এর Respond option টি শেষে "Using 'Respond to Webhook' Node"
         তে পরিবর্তন করতে হবে — সব নোড যোগ করার পরে।
```

---

## ⚙️ ধাপ ২ — Extract from File

> **উদ্দেশ্য:** Webhook দিয়ে পাঠানো File (PDF/Doc) থেকে text বের করা।

**ধাপসমূহ:**

1. Webhook নোডের **`+`** চিহ্নে ক্লিক করো।
2. Search বক্সে টাইপ করো: `Extract from File` → ক্লিক করো।
3. নিচের স্ক্রিনশট অনুযায়ী value সেট করো:

![Extract from File Settings](https://imgur.com/7gtk5Hw.png)

4. **Execute Workflow** বাটনে ক্লিক করো।

---

## ⚙️ ধাপ ৩ — Simple Vector Store (Document Ingestion)

> **উদ্দেশ্য:** Extract করা document-কে Vector embedding হিসেবে store করা।

**ধাপসমূহ:**

1. Extract from File নোডের **`+`** চিহ্নে ক্লিক করো।
2. Search বক্সে টাইপ করো: `Simple Vector Store` → ক্লিক করো।
3. **Add documents to vector store** অপশন সিলেক্ট করো।
4. নিচের স্ক্রিনশট অনুযায়ী value সেট করো:

![Simple Vector Store Settings](https://imgur.com/00gILOE.png)

---

### ↳ ধাপ ৩.১ — Embeddings Google Gemini যোগ করা

> **উদ্দেশ্য:** Document-এর text কে Vector-এ রূপান্তর করতে Gemini Embedding ব্যবহার।

1. Simple Vector Store নোডের **Embedding** সেকশনের **`+`** চিহ্নে ক্লিক করো।
2. **Embeddings Google Gemini** ক্লিক করো।

![Embeddings Google Gemini](https://imgur.com/So4QzGp.png)

---

### ↳ ধাপ ৩.২ — Default Data Loader যোগ করা

> **উদ্দেশ্য:** Extracted document data কে Vector Store-এ load করা।

1. **Embedding Document** এর **`+`** চিহ্নে ক্লিক করো।
2. **Default Data Loader** ক্লিক করো।

![Default Data Loader](https://imgur.com/2C3YPMf.png)

---

## ⚙️ ধাপ ৪ — AI Agent সেটআপ

> **উদ্দেশ্য:** User-এর প্রশ্ন গ্রহণ করে Vector Store থেকে তথ্য খুঁজে উত্তর দেওয়া।

**ধাপসমূহ:**

1. Simple Vector Store নোডের **`+`** চিহ্নে ক্লিক করো।
2. Search বক্সে টাইপ করো: `AI Agent` → ক্লিক করো।
3. নিচের স্ক্রিনশট অনুযায়ী value সেট করো:

![AI Agent Settings](https://imgur.com/AqlnBAQ.png)

---

### ↳ ধাপ ৪.১ — Google Gemini Chat Model যোগ করা

1. AI Agent নোডের **Chat Model** সেকশনের **`+`** চিহ্নে ক্লিক করো।
2. **Google Gemini Chat Model** ক্লিক করো।
3. Credential ও Model সেট করো।

![Google Gemini Chat Model](https://imgur.com/lBg9gXD.png)

---

### ↳ ধাপ ৪.২ — Simple Memory যোগ করা

> **উদ্দেশ্য:** AI Agent যেন আগের কথোপকথন মনে রাখতে পারে।

1. AI Agent নোডের **Memory** সেকশনের **`+`** চিহ্নে ক্লিক করো।
2. **Simple Memory** ক্লিক করো।
3. নিচের স্ক্রিনশট অনুযায়ী value সেট করো।

---

### ↳ ধাপ ৪.৩ — Simple Vector Store1 (Retrieval Tool) যোগ করা

> **উদ্দেশ্য:** AI Agent যেন Vector Store থেকে তথ্য খুঁজে নিতে পারে।

1. AI Agent নোডের **Tool** সেকশনের **`+`** চিহ্নে ক্লিক করো।
2. Search বক্সে টাইপ করো: `Simple Vector Store` → **Simple Vector Store1** ক্লিক করো।
3. নিচের স্ক্রিনশট অনুযায়ী value সেট করো:

![Simple Vector Store1 Tool](https://imgur.com/zg3fLih.png)

---

### ↳ ধাপ ৪.৪ — Embeddings Google Gemini1 যোগ করা

> **উদ্দেশ্য:** Retrieval-এর সময় Query কে Vector-এ রূপান্তর করতে।

1. **Simple Vector Store1** নোডের **Embedding** সেকশনের **`+`** চিহ্নে ক্লিক করো।
2. **Embeddings Google Gemini1** ক্লিক করো।

![Embeddings Google Gemini1](https://imgur.com/0CGqz3p.png)

---

## ⚙️ ধাপ ৫ — Respond to Webhook

> **উদ্দেশ্য:** AI Agent-এর উত্তর Frontend-এ ফেরত পাঠানো।

**ধাপসমূহ:**

1. AI Agent নোডের **`+`** চিহ্নে ক্লিক করো।
2. Search বক্সে টাইপ করো: `Respond to Webhook` → ক্লিক করো।
3. নিচের স্ক্রিনশট অনুযায়ী value সেট করো:

![Respond to Webhook](https://imgur.com/hFyQ7Ba.png)

---

## ⚙️ ধাপ ৬ — Webhook আপডেট

> **উদ্দেশ্য:** Webhook নোডকে বলতে হবে যে সে Respond to Webhook নোড ব্যবহার করবে।

**ধাপসমূহ:**

1. প্রথম **Webhook** নোডে ফিরে যাও।
2. **Respond** ফিল্ডটি পরিবর্তন করো:

| ফিল্ড | মান |
|---|---|
| **Respond** | `Using 'Respond to Webhook' Node` |

```
📌 নোট: এই ধাপটি না করলে Webhook নিজেই response পাঠাবে,
         AI Agent-এর উত্তর Frontend-এ পৌঁছাবে না।
```

---

## ⚙️ ধাপ ৭ — Workflow Execute ও Document Analyze

**ধাপসমূহ:**

1. **Execute Workflow** বাটনে ক্লিক করো।
2. **Analyze Document** বাটনে ক্লিক করো।
3. Document সফলভাবে Vector Store-এ যুক্ত হবে।
4. এরপর যেকোনো প্রশ্ন করলে AI Agent Vector Store থেকে তথ্য খুঁজে উত্তর দেবে।

---

## ✅ চেকলিস্ট

- [ ] Webhook সেটআপ হয়েছে (HTTP Method: POST)
- [ ] Test URL `index.html`-এ paste করা হয়েছে
- [ ] Extract from File নোড কনফিগার হয়েছে
- [ ] Simple Vector Store — "Add documents" মোডে সেট হয়েছে
- [ ] Embeddings Google Gemini (Ingestion) যোগ হয়েছে
- [ ] Default Data Loader যোগ হয়েছে
- [ ] AI Agent কনফিগার হয়েছে
- [ ] Google Gemini Chat Model যোগ হয়েছে
- [ ] Simple Memory যোগ হয়েছে
- [ ] Simple Vector Store1 (Retrieval Tool) যোগ হয়েছে
- [ ] Embeddings Google Gemini1 (Retrieval) যোগ হয়েছে
- [ ] Respond to Webhook যোগ হয়েছে
- [ ] Webhook → Respond আপডেট হয়েছে
- [ ] Workflow Execute ও Document Analyze করা হয়েছে

---

## 🛠️ সম্ভাব্য সমস্যা ও সমাধান

| সমস্যা | সমাধান |
|---|---|
| Webhook response আসছে না | Webhook-এর Respond অপশন সঠিকভাবে সেট হয়েছে কিনা দেখো |
| Document extract হচ্ছে না | File format সঠিক কিনা এবং Extract from File নোড ঠিকমতো কনফিগার হয়েছে কিনা দেখো |
| Embedding Error | Google Gemini API Key সঠিকভাবে দেওয়া আছে কিনা যাচাই করো |
| AI Agent উত্তর দিচ্ছে না | Vector Store-এ Document সফলভাবে যুক্ত হয়েছে কিনা চেক করো |
| Memory কাজ করছে না | Simple Memory নোড সঠিকভাবে AI Agent-এর সাথে যুক্ত কিনা দেখো |

---
