# 🛒 AI E-commerce Order Automation | n8n + Groq AI

> End-to-end AI-powered eCommerce order processing automation built with **n8n**, **Groq AI**, **Google Sheets**, **Slack**, **Gmail**, and **Webhooks**.

![n8n](https://img.shields.io/badge/n8n-Workflow-orange)
![Groq](https://img.shields.io/badge/Groq-AI-blue)
![Google Sheets](https://img.shields.io/badge/Google-Sheets-success)
![Slack](https://img.shields.io/badge/Slack-Notifications-purple)
![License](https://img.shields.io/badge/License-MIT-green)

---

# 📌 Overview

This workflow automates the complete order lifecycle for an eCommerce business.

Instead of manually validating orders, updating spreadsheets, notifying the operations team, and emailing customers, the workflow performs everything automatically using AI.

The AI agent analyzes every incoming order, determines its priority, recommends a shipping method, updates inventory records, notifies the operations team, and automatically sends customer emails.

---

# 🚀 Features

* Webhook-based Order API
* AI Order Validation
* AI Priority Classification
* Shipping Recommendation
* Structured JSON Output
* Google Sheets Inventory Update
* Slack Order Notification
* Order Confirmation Email
* Automated Follow-up Email
* Invalid Order Detection
* Customer Order Tracking
* Low-code Automation with n8n

---

# 🧠 AI Business Logic

The AI Agent automatically performs:

* Order validation
* Priority detection
* Shipping selection
* Delivery estimation
* Customer order summary generation
* Structured JSON response generation

### Priority Rules

| Condition                       | Priority |
| ------------------------------- | -------- |
| Quantity > 5 OR Total > ₹10,000 | High     |
| Quantity 2–5                    | Medium   |
| Otherwise                       | Low      |

### Shipping Rules

| Priority | Shipping |
| -------- | -------- |
| High     | Express  |
| Medium   | Express  |
| Low      | Standard |

---

# ⚙️ Workflow Architecture

```text
Webhook
    │
    ▼
Order Validation
    │
    ▼
AI Order Processing (Groq)
    │
    ▼
Structured Output
    │
    ▼
Google Sheets
    │
    ▼
Slack Notification
    │
    ▼
Confirmation Email
    │
    ▼
Wait (3 Days)
    │
    ▼
Follow-up Email
```

---

# 🛠 Tech Stack

* n8n
* Groq LLM
* Google Sheets API
* Gmail API
* Slack API
* Webhooks
* JSON
* AI Agents

---

# 📂 Project Structure

```text
n8n-ai-ecommerce-order-automation
│
├── workflow
│   ├── workflow.json
│
├── assets
├── screenshots
├── sample
├── docs
├── images
│
├── README.md
├── LICENSE
└── .gitignore
```

---

# 📥 Sample Request

```json
{
  "order_id": "ORD-1001",
  "customer_email": "customer@example.com",
  "product_name": "Wireless Mouse",
  "quantity": 3,
  "price": 1499
}
```

---

# 📤 Sample AI Response

```json
{
  "status": "approved",
  "priority": "medium",
  "shipping_method": "express",
  "estimated_delivery": "2026-07-10"
}
```

---


# 💼 Business Benefits

* Eliminates manual order processing
* Reduces processing time
* Improves customer experience
* AI-powered order prioritization
* Automatic inventory updates
* Automated customer communication
* Easy to scale

---

# 🔮 Future Improvements

* Payment Gateway Integration
* WhatsApp Notifications
* Razorpay Integration
* Shopify Integration
* Fraud Detection AI
* Inventory Prediction
* Customer Segmentation
* Dashboard Analytics

---

# 👨‍💻 Author

**Piyush Kumar**

AI Automation Developer

### Skills

* n8n Automation
* AI Agents
* LangChain
* LangGraph
* Workflow Automation
* Prompt Engineering
* Python
* REST APIs

---

# 📄 License

This project is licensed under the MIT License.

## ⭐ Support

If you found this project helpful, please consider giving it a ⭐ on GitHub.