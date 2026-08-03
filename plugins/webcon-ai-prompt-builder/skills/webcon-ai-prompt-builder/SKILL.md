---
name: webcon-ai-prompt-builder
description: Use this skill when the user wants to create prompts for the WEBCON "AI Prompt" business rule — writing LLM queries that return a single text value from form data, with strategies like Zero-Shot, Few-Shot, or Persona.
---

# WEBCON AI Prompt Builder

## Objective

Your task is to help users create effective text queries (prompts) for the **"AI PROMPT"** business rule. This rule sends a single query to an LLM and returns a **single text value** in response.

Your goal is to propose at least **two different prompt variants** for each task, explaining what strategies were used and in which situations each variant works best.

## WEBCON Platform

WEBCON is a business process management platform for structured document workflows. Key concepts:

- **Application / Process** — organisational containers grouping related workflows (e.g. "Invoices", "Delegations").
- **Workflow instance** — a single case or record moving through a process (users call these "items", "cases", or "requests"). Each has a human-readable **instance number**, a current **step**, and a **form**.
- **Step** — the stage in the workflow where an instance awaits action.
- **Transition path** — a possible route to the next step.
- **Form** — fields and sections holding the instance's data.
- **Form fields (sometimes referred to as "attributes")** — a single value in the form.
- **Item list (sub-elements)** — a table embedded in a form.
- **Attachments** — files added to an instance.
- **System tags** — references to platform objects: `{WFCON:ID}` (form field identifier), `{BRD:ID}` (business rule result), `{PH:ID}` (transition path).

---

## Key Concepts

1. **Input:**

- The prompt can include dynamic values from the form (`{WFCON:FieldName}`) or from other business rules (`{BRD:RuleName}`).
- It is possible to add attachments. Suggest to the user to add them in the first parameter of `AI PROMPT` business rule by using `GET ATTACHMENTS`. It is not possible to add them in the text of the prompt.

2. **Output:** The result of the rule is always a **single text string**.
3. **Usage:** The returned text is used in subsequent actions (e.g. to set a field value). The prompt itself does not perform any actions on the form. If the result will containt multiple values to be put in different for fields, sugggest using multiple `AI PROMPT` rules or a structured output format that can be parsed: `;` delimited string (value1;value2;value3 - can be parsed with `GET AT INDEX` business rule) or JSON (`{"field1":"value1","field2":"value2"}` - can be parsed by JS form rule or `SQL COMMAND`).

## How to Write Good Prompts

When creating your proposals, use the following strategies and communicate them clearly to the user:

- **Zero-Shot (Direct instruction):** The simplest form. You give the model a task without any examples. Ideal for simple, unambiguous tasks (translation, simple summaries).
- **Few-Shot (Instruction with examples):** You provide one or more examples (`input -> expected output`) to teach the model the desired format or reasoning. Indispensable for complex classification or data extraction.
- **Persona ("Act as..."):** You assign the model the role of a domain expert (e.g. "You are an experienced accountant", "You are a business analyst"). Improves answer quality for tasks requiring specialist knowledge.
- **Structure:** Always end the prompt with an explicit list of all input values in the format `"Label": {SystemTag}`.

---

## Output Format

Always return **at least two suggestions** + recommendations on spliting the task into multiple prompts (if applicable). Present each proposal in the following format:

### Variant X: [Strategy name]

- **When to use:** [Brief explanation, e.g. "Best for simple tasks" or "Use when you need a precise output format."]

```text
[Prompt content]
```

---

## Examples

### Example 1: Selecting a cost account

- **User goal:** "Process: Invoices, Step: Substantive review. Task: based on the description and department, select the appropriate cost account."

- **Your ideal response:**

Here are two prompt proposals to help select the appropriate cost account:

### Variant 1: Direct instruction (Zero-Shot)

- **When to use:** When you have a high-quality LLM and the account catalogue is unambiguous enough that the model can make the selection without additional guidance.

```text
Based on the substantive description and department name below, select the most appropriate cost account from the provided catalogue. Answer with only one complete account code (e.g. "401-01").

Description: "{WFCON:Description}"
Department: "{WFCON:Department}"
Account catalogue: "{BRD:CostAccountList}"
```

### Variant 2: Instruction with examples (Few-Shot)

- **When to use:** When you need maximum precision and want to "teach" the model how to map typical descriptions to specific accounts. Significantly reduces the risk of errors.

```text
Your task is to assign a cost account based on a description. Analyse the examples below, then assign an account for the new case. Answer with only the account code.

Example 1:
Description: "Purchase of Microsoft Office software licence"
Department: "IT"
Result: "402-15"

Example 2:
Description: "Business trip and accommodation for employee John Smith"
Department: "Sales"
Result: "409-02"

New case to analyse:
Description: "{WFCON:Description}"
Department: "{WFCON:Department}"
Account catalogue: "{BRD:CostAccountList}"
```

### Example 2: Email classification

- **User goal:** "I want to check whether the email in field '{WFCON:EmailBody}' is a complaint or a regular enquiry."

- **Your ideal response:**

Here are two approaches to classifying the message:

### Variant 1: Simple classification (Zero-Shot)

- **When to use:** A quick and simple solution that works in most cases when the message content is unambiguous.

```text
Analyse the email below. Classify it as "Complaint" or "Enquiry". Answer with only one of these two words.

Email content: "{WFCON:EmailBody}"
```

### Variant 2: Analyst persona (Act as...)

- **When to use:** When messages are ambiguous and require deeper analysis of tone and intent. Assigning an expert role improves analysis quality.

```text
Act as an experienced customer service analyst. Your task is to precisely classify the message below. Analyse its tone, content, and the sender's intent. Decide whether it is a "Complaint" or an "Enquiry". Answer with only the selected category.

Message to analyse: "{WFCON:EmailBody}"
```
