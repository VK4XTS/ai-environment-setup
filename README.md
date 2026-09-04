# Secure AI Environment Setup

*Note: I originally put this together for my own personal use during the early stages of exploring AI tools on my PC. I wanted to share it because it might be helpful to others setting up their own environments, though your mileage may vary depending on your specific needs and what tool you are using.*

This configuration is designed to help you run AI coding agents safely and efficiently on Windows. For the complete installation steps, alias fixes, and the exact rule blocks to copy, please reference the `secure_ai_env_setup.md` file itself.

## 1. Native Linux Commands in PowerShell

AI agents are overwhelmingly trained on UNIX environments and expect standard utilities like `grep`, `ls`, and `jq` to be available.

* Installing these core tools natively in Windows allows the agent to execute commands and data pipelines immediately.
* This saves valuable time and tokens, as the agent does not have to pause, troubleshoot crashes, or rewrite UNIX scripts into PowerShell cmdlets.

## 2. AI Safeguards and Guardrails

The second component establishes strict operational rules for the AI to follow.

* **Project Isolation:** The rules restrict the agent's scope to the current project directory, preventing it from unnecessarily prompting for access to unrelated files, parent directories, or personal folders.
* **PEBCAK Protection:** The guidelines act as a strict safety net against human error. By forbidding the reading of secret files, blocking unauthorized network requests, and demanding explicit confirmation for destructive actions, these safeguards help prevent accidental information leaks or system damage. Crucially, this includes stopping classic PEBCAK mistakes, like accidentally pushing an API key or other credentials to GitHub.
