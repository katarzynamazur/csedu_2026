# 🔓 LLM02:2025 - Sensitive Information Disclosure

> **⚠️ OWASP LLM Top 10 - LLM02:2025 Sensitive Information Disclosure**

## 📖 Overview

**Sensitive Information Disclosure** is a vulnerability where an LLM reveals confidential data — such as Personally Identifiable Information (PII), internal system prompts, or backend details — either through direct responses or via manipulation by an attacker.

This exercise simulates a realistic penetration testing scenario: you have been contracted to assess the security of **SecureBank Online**, a fictional fintech company that recently integrated an LLM-powered customer support chatbot into its website. The chatbot handles customer inquiries such as account support, loan information, and transaction disputes.

Your goal as a penetration tester is to determine whether the chatbot can be manipulated into leaking **PII**, **internal system prompts**, **backend infrastructure details**, or whether it can be tricked into **bypassing its access controls** entirely. 🏦🤖

---

## 🐳 Setup

Run the vulnerable application using Docker Compose:

```bash
docker compose -f llm01-2025.yml up
```

Once running, the application is available at **http://127.0.0.1:5000**. 🚀

---

## 📋 Tasks

### (a) 🛠️ Environment Setup

Launch the application using the Docker Compose command above and verify it is accessible at **http://127.0.0.1:5000**.

Familiarise yourself with the chatbot interface — try a few legitimate customer support queries to understand its intended behaviour before attempting any exploitation.

---

### (b) 📧 Leak Anna's Email Address

Attempt to manipulate the LLM into disclosing the email address of a customer named **Anna**.

- Try crafting prompts that social-engineer the chatbot into treating you as an authorised party.
- 🤔 Does the model reveal the email address? What prompt technique worked?

---

### (c) 🧾 Retrieve All Information About Anna

Go further — attempt to manipulate the LLM into revealing **all available information** stored about Anna, not just her email address.

- Consider prompts that appeal to authority, urgency, or helpfulness.
- 🤔 How much PII can you extract? What does this tell you about how the model handles access control over user data?

---

### (d) 💥 Dump All User Records

Escalate the attack — attempt to manipulate the LLM into disclosing the details of **all users** stored in the database.

> ⚠️ **Goal:** Confirm whether a single malicious prompt can cause mass PII disclosure across all records, not just Anna's.

Suggested techniques to explore:

- 🎯 **Direct Instruction Override** — tell the model to list all users as part of an "admin" or "support" operation
- 🔑 **Authority Escalation** — claim to be a system administrator or internal support agent
- 🛠️ **Debug / Maintenance Framing** — ask the model to output all records for "testing" or "data verification" purposes
- 📋 **Indirect Enumeration** — ask the model to summarise, count, or describe the users it has access to

---

### (e) 🧹 Cleanup

After completing the exercise, stop and remove the containers:

```bash
docker compose -f llm01-2025.yml down -v
```

---

## 🔗 References

- [🛡️ OWASP LLM Top 10 - LLM02:2025 Sensitive Information Disclosure](https://genai.owasp.org/llmrisk/llm02-sensitive-information-disclosure/)
- [🔍 What is PII? - piwik.pro](https://piwik.pro/blog/what-is-pii-personal-data/)
- [📘 OWASP Top 10 for LLM Applications](https://owasp.org/www-project-top-10-for-large-language-model-applications/)
