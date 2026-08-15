# Multimodal Shopping Agent

An **AI-powered shopping assistant** built with **LangChain**, **tool calling**, and **multimodal reasoning**.  
The agent helps users discover products through **natural language** or **product images**, filters results by structured constraints such as **price**, **organic status**, and **minimum rating**, and completes the flow with a safe, confirmation-based **checkout** step.

> Users can say things like:  
> *“I want organic honey under $20 with a 4.5+ rating”*  
> or simply upload a product image and let the agent find similar items.

---

## Features

- **Natural-language shopping**
  - Understands free-form shopping requests.
  - Extracts preferences such as product type, max price, organic flag, and minimum rating.

- **Image-based product search**
  - Accepts a product photo as input.
  - Uses a vision-capable LLM to identify product attributes and convert them into a searchable query.

- **Tool-augmented retrieval**
  - Searches a SQLite product catalog with structured filters.
  - Retrieves product ratings and review counts before ranking recommendations.

- **Rating-aware recommendations**
  - Evaluates each candidate product with real review statistics.
  - Filters items based on user-defined rating thresholds.

- **Safe checkout flow**
  - Never places an order without explicit user confirmation.
  - Resolves the selected product using IDs shown in the previous recommendation list.

- **Interactive UI**
  - Includes a **Streamlit** interface for conversational shopping and image uploads.

---

## How It Works

The system is implemented as a **LangChain agent** with custom tools.  
Depending on the user’s input modality, the workflow branches into two paths:

### 1) Text-based shopping flow
1. User describes a product in natural language.
2. The agent calls `search_products(...)` with optional filters.
3. For each matched product, the agent calls `get_rating(...)`.
4. The agent filters and ranks the results.
5. It presents a numbered product list.
6. If the user confirms, the agent calls `checkout(product_id)`.

### 2) Image-based shopping flow
1. User uploads a product image.
2. The agent calls `describe_product_image(image_path)`.
3. The vision model extracts:
   - product type
   - search query
   - organic status
   - short description
4. The agent uses the extracted attributes to search the product catalog.
5. The browsing and checkout flow continues as in text mode.

---

## 🏗️ Architecture
```text
┌───────────────────────────────┐
   User Text ───────▶│                               │
│   LangChain Shopping Agent    │
   User Image ──────▶│   + Tool Calling              │
│   + Vision LLM                │
└──────────────┬────────────────┘
│
┌───────────────────────────┼───────────────────────────┐
▼                           ▼                           ▼
 [search_products]            [get_rating]                 [checkout]
 (SQLite catalog)            (reviews lookup)          (writes to orders)

│
▼
[describe_product_image]
(Vision model: GPT-4o-mini)
