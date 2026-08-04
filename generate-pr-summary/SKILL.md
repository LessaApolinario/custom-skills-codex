---
name: generate-pr-summary
description: Generate a concise GitHub Pull Request title and bullet-point description from the full conversation context. Use when the user asks for a PR title, PR description, summary of completed changes, copy-paste GitHub PR text, or to transform the current conversation/work into a Pull Request summary.
---

# Generate PR Summary

Analyze the complete available conversation context and generate content ready to paste into a GitHub Pull Request description.

## Workflow

1. Read the complete conversation context and identify the work that was actually completed.
2. Determine the output language:
   - Use the language explicitly requested by the user.
   - If no output language is specified, default to `en-US`.
3. Resolve ambiguity only when required:
   - Do not ask the user to repeat information already present in the conversation.
   - Ask a question only when multiple unrelated tasks may need separate PRs, the user may want to exclude changes, or there is no usable context about the completed work.
   - Ask necessary questions in the language the user used when invoking the skill, even if the final PR content should use a different language.
4. Separate completed changes from abandoned attempts, unresolved issues, and future plans.
5. Create a short title that represents the primary purpose of the PR.
6. Create concise bullet points describing the completed changes.
7. Before responding, verify that every title and bullet detail is supported by the conversation context.

## Context Rules

- Use all available conversation context.
- Prioritize the final outcome of the work over intermediate attempts, errors, or discarded approaches.
- Include only completed changes.
- Include tests only when the conversation confirms they were added, updated, or executed.
- Do not invent file names, components, endpoints, technologies, tests, results, fixes, or business rules.
- Omit uncertain details unless they are essential and can be clarified with a question.

## Output Format

Return only:

```text
Pull Request Title

- Completed change.
- Completed change.
- Completed change.
```

Do not add sections such as `Summary`, `Testing`, `Notes`, `Screenshots`, `Checklist`, `Related issues`, or `Additional context` unless the user explicitly requests a different structure.

## Title Rules

- Keep the title short, specific, and natural.
- Describe the main purpose of the Pull Request.
- Start with a verb when it fits the language and context.
- Do not end the title with a period.
- Avoid generic titles such as `Updates`, `Changes`, `Fix issues`, `Update code`, or `Code changes`.
- Represent the overall change set, not a minor secondary detail.

## Bullet Rules

- Use 2 to 6 bullet points when possible.
- Use more bullets only when the context contains many important completed changes.
- Describe only changes supported by the conversation context.
- Start bullets with past-tense verbs or consistent constructions for the selected language.
- Group related changes when it improves readability.
- Avoid repeating the title.
- Avoid irrelevant internal details.
- Do not mention planned or unimplemented work.
- Do not include code, logs, or long error messages.
- Mention specific files, pages, stores, services, endpoints, or components only when they help explain the change.

## Examples

Default to `en-US` when the user does not specify an output language:

```text
Fix Volunteer Skill Filter Request Payload

- Updated the volunteer filter request to continue using the POST endpoint expected by the backend.
- Changed the skill filter payload to send `skill_id` instead of `skills`.
- Removed empty filter values before sending the request.
- Normalized selected skill values to support both raw IDs and option objects.
```

When the user requests Brazilian Portuguese:

```text
Corrigir payload do filtro de habilidades de voluntarios

- Mantida a requisicao de filtro de voluntarios utilizando o metodo POST esperado pelo backend.
- Alterado o payload do filtro para enviar `skill_id` em vez de `skills`.
- Removidos valores vazios antes do envio da requisicao.
- Normalizados os valores selecionados para aceitar IDs e objetos de opcao.
```
