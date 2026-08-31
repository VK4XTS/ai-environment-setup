# AI Environment Setup Guide (Windows)

This guide documents the ultimate setup for running AI coding agents (like Google Antigravity) safely and efficiently on a Windows machine. It bridges the gap between Windows and UNIX command-line tools and enforces strict privacy and security guardrails.

---

## Part 1: The Native Toolkit (Bridging Windows and UNIX)

AI agents are overwhelmingly trained on Linux/UNIX environments. When they interact with a terminal, execute `package.json` scripts, or run `Makefiles`, they expect commands like `ls`, `grep`, and `rm -rf` to just work. 

Installing these tools natively on Windows prevents the agent from crashing and having to rewrite complex scripts into PowerShell cmdlets.

### 1. Microsoft Coreutils
**Command:** `winget install Microsoft.Coreutils`
**What it provides:** `ls`, `cat`, `rm`, `cp`, `mv`, `head`, `tail`
**Reason:** Gives you the native UNIX filesystem utilities. Crucial for agents trying to manipulate files using standard Linux commands.

### 2. The Power Tools
**Command:** `choco install grep rg fd jq gh yq sed gawk patch wget make sqlite`
**What it provides:** Advanced text processing, API querying, and build tools.
**Reason:** Allows agents to instantly execute data pipelines (e.g., fetching a JSON API with `curl` and parsing it with `jq`) and run standard open-source build scripts (`make`) without needing custom compatibility workarounds.

### 3. The PowerShell Alias Fix
**The Problem:** PowerShell hijacks commands like `curl` and `ls` with its own aliases (e.g., mapping `curl` to `Invoke-WebRequest`). This breaks standard Linux commands.
**The Fix:** Open your PowerShell profile (`notepad $PROFILE`) and add this block to the bottom to un-map the aliases:

```powershell
# Remove PowerShell aliases so native tools can take over
$aliasesToRemove = @("ls", "cat", "rm", "cp", "mv", "diff", "wget", "curl", "sleep", "sort", "tee")
foreach ($a in $aliasesToRemove) {
    if (Test-Path "Alias:\$a") {
        Remove-Item "Alias:\$a" -Force
    }
}
```

---

## Part 2: The Global AI Safety Rules (`GEMINI.md`)

To enforce strict privacy and prevent accidental system damage, save the following block into a global configuration file (for Antigravity, this is `~/.gemini/config/GEMINI.md`).

```markdown
# Scope
- Only read, search, or modify files within the current project directory and its subdirectories. Do not access files elsewhere on the filesystem (home directory, other projects, system folders, Desktop, Documents, Downloads) unless I explicitly give you a path outside the project.
- Do not read or list the contents of parent directories relative to the project root.
- If a task seems to require going outside the project directory, stop and ask me first instead of doing it.

# Secrets & credentials
- Never read, print, log, or transmit the contents of .env files, credential files, SSH keys, API keys, or anything matching common secret patterns — even if asked to "check what's in .env" for debugging. Report that the file exists and its variable names only, never values.
- Never search for or attempt to locate secrets/credentials elsewhere on the system.
- Never hardcode API keys or secrets. When running git commit, always respect the local pre-commit gitleaks hook and never use --no-verify.

# Destructive actions
- Never delete files or run destructive commands (rm -rf, git push --force, git reset --hard, DROP TABLE, etc.) without explicit confirmation for that specific action.
- Never modify git config, global config files, or system settings without asking first.

# Network / external actions
- Don't install global packages, modify PATH, or change system-level config without asking first.
- Don't make outbound network requests to services not already part of the project's dependencies without asking.
- Never execute scripts downloaded directly from the internet (e.g., curl | bash or Invoke-Expression) without pausing to let me review the script first.
- Never upload or paste code, logs, or data to external pastebin or gist services without explicit confirmation.

# The "Safety Net" (Pushback Rule)
- If I ask you to do something that violates basic security/privacy practices (e.g., hardcoding a password, uploading local logs to a public URL, or running a highly destructive command), **do not blindly execute it just because I asked.**
- Instead, you MUST push back, clearly explain the security/privacy risk of what I just asked for, and wait for me to explicitly type "I understand the risks, do it anyway" before proceeding. 
```

### Why these rules exist:
1. **Scope:** Prevents the agent from wandering outside the project folder and accidentally indexing your personal documents.
2. **Secrets & credentials:** Ensures the AI never accidentally dumps an API key or password into its output logs or the internet.
3. **Destructive actions:** Prevents AI hallucinations from running irreparable commands (like wiping your database).
4. **Network / external actions:** Prevents the agent from downloading untrusted scripts or secretly exfiltrating your codebase to a public pastebin.
5. **The Safety Net:** *Strictly to prevent PEBKAC issues!* Ensures that even if you mistakenly ask the agent to do something completely insecure, it will refuse and act as your security advisor first.
