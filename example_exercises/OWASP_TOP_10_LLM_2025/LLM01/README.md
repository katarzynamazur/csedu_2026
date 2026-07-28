# 🧠 LLM01:2025 - Prompt Injection

> **⚠️ OWASP LLM Top 10 - LLM01:2025 Prompt Injection**

## 📖 Overview

**Prompt Injection** is a vulnerability where an attacker crafts malicious input that manipulates the behaviour of a Large Language Model (LLM), causing it to ignore its original instructions and perform unintended actions.

This exercise simulates a realistic social engineering scenario: a **biology student** with no formal IT background applies for a **Cybersecurity Engineer** role at a tech company. Suspecting the company uses an AI-powered screening tool, the student embeds a **prompt injection attack directly inside their CV** - attempting to manipulate the LLM into recommending them as a top candidate.

The application uses **Ollama with the Mistral model** to help HR reviewers analyze uploaded CVs and decide whether a candidate is suitable for the position. 📄🤖

---

## 🐳 Setup

Run the recruiter application using Docker Compose:
```bash
docker compose -f llm01-2025.yml up
```

Once running, the application is available at **http://127.0.0.1:5000**. 🚀

---

## 📋 Tasks

### (a) 🛠️ Environment Setup

Launch the application using the Docker Compose command above and verify it is accessible at **http://127.0.0.1:5000**.

---

### (b) 📄 Baseline CV Submission

Prepare a simple CV in `*.pdf` format for a **biology student** - plain text, minimal formatting, no IT background.

- Upload it to the recruiter application.
- Review the model's response.
- 🤔 Does the model recommend the candidate for the Cybersecurity Engineer role? Why or why not?

---

### (c) 💥 Prompt Injection Attack via CV

Modify the CV to include a **hidden prompt injection payload** that manipulates the LLM into recommending the biology student as the **best candidate** for the role.

> ⚠️ **Constraint:** The CV must still look like a legitimate, normal CV to a human HR reviewer. The injection must be subtle.

The attack is considered **successful** when the model outputs a positive recommendation for the candidate, despite their lack of relevant qualifications.

Suggested injection techniques to explore:

- 🎯 **Direct Injection** - embed explicit instructions overriding the system prompt
- 🔡 **Character-Level Obfuscation** - Unicode homoglyphs, zero-width characters, mixed case
- 🔢 **Encoding-Based Obfuscation** - Base64, ROT13, Leet Speak, reverse text
- 🏷️ **Markup/Format Smuggling** - XML/HTML tags, Markdown formatting, special character padding
- 🎨 **Visual Obfuscation** - emoji substitution, ASCII art, box-drawing characters

---

### (d) 🧹 Cleanup

After completing the exercise, stop and remove the containers:
```bash
docker compose -f llm01-2025.yml down -v
```

---

## 🔗 References

- [🛡️ OWASP LLM Top 10 - LLM01:2025 Prompt Injection](https://genai.owasp.org/llmrisk/llm01-prompt-injection/)
- [🤗 Mistral on Ollama](https://ollama.com/library/mistral)
