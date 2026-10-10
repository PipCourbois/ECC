# Using ECC (Everything Claude Code) With Your Projects

## The Problem
How do I use ECC's powerful development tools without mixing the ECC framework code with my application code?

## The Solution: Global Installation + Selective Activation

### Command Namespacing (Avoiding Conflicts)

If you use multiple Claude Code plugins, you may encounter command name conflicts. For example, multiple plugins might provide a `/plan` or `/review` command.

**ECC uses the `ecc:` namespace** to avoid conflicts:

- `ecc:plan` - Implementation planning
- `ecc:tdd` - Test-driven development workflow
- `ecc:code-review` - Code quality review
- `ecc:build-fix` - Fix build errors
- `ecc:e2e` - Generate and run E2E tests
- `ecc:learn` - Extract patterns from sessions
- `ecc:skill-create` - Generate skills from git history

This allows ECC to coexist with other plugins without shadowing their commands or being shadowed by them.

### Step 1: Install ECC Globally (One Time)

```bash
# Clone ECC to a dedicated location (NOT in your app repo)
git clone https://github.com/anthropics/everything-claude-code.git ~/ecc

# Run setup from ECC directory
cd ~/ecc
node scripts/ecc.js setup
```

This installs ECC components to `~/.claude/` (global Claude Code configuration directory), completely separate from your application code.

### Step 2: Keep Your App Repository Clean

Your application repository stays focused on your actual code:

```
my-awesome-app/
├── src/           # Your application code
├── tests/         # Your application tests
├── package.json   # Your app dependencies
├── .gitignore
└── CLAUDE.md      # Optional: app-specific instructions for Claude
```

**Optional: Add app-specific guidelines** (lightweight):
```
my-awesome-app/
└── .claude/
    └── rules/
        └── my-app-guidelines.md  # Project-specific preferences
```

### Step 3: Selective Activation Per Project

**For Complex Projects (Enable Full ECC):**
```bash
cd ~/projects/complex-app
export ECC_HOOK_PROFILE=full
claude
# Now you have access to ecc:plan, ecc:tdd, ecc:code-review, auto-formatting, etc.
```

**For Simple Projects (Disable ECC):**
```bash
cd ~/projects/simple-data-science
export ECC_DISABLED_HOOKS=all
claude
# Plain Claude Code experience, no ECC automation
```

### Step 4: Create Aliases for Convenience

Add to your `.bashrc` or `.zshrc`:

```bash
# Plain Claude (no ECC automation)
alias claude-plain="ECC_DISABLED_HOOKS=all claude"

# Full ECC power
alias claude-ecc="ECC_HOOK_PROFILE=full claude"
```

Then simply use:
```bash
claude-plain    # For simple projects
claude-ecc      # For complex projects with full ECC
```

---

## How It Works

When you run `claude` CLI in your app directory:

1. ✅ Claude Code loads **global ECC** from `~/.claude/` (agents, skills, commands, hooks)
2. ✅ Claude Code reads **your app's `CLAUDE.md`** for project context
3. ✅ Claude Code reads **your app's `.claude/rules/`** (if present) for app-specific guidelines
4. ✅ **Your app code stays completely separate** - ECC just provides the tooling

---

## What Goes Where?

| Component | Location | Purpose |
|-----------|----------|---------|
| **ECC Framework** | `~/ecc/` or `~/.claude/` | Agents, skills, commands, hooks (the tooling) |
| **Your App Code** | `~/projects/my-app/src/` | Your actual application |
| **App Instructions** | `~/projects/my-app/CLAUDE.md` | Tell Claude about your app's architecture |
| **App-Specific Rules** | `~/projects/my-app/.claude/rules/` | Override ECC defaults for this project |

---

## Installation Profiles

ECC supports different installation levels:

```bash
# Minimal - core skills only, no hooks/automation
node scripts/ecc.js install --profile=minimal

# Standard - balanced for general development
node scripts/ecc.js install --profile=standard

# Full - everything (automation, hooks, all agents)
node scripts/ecc.js install --profile=full
```

### Recommended: Install Minimal, Enable Selectively

```bash
# Install minimal profile globally
cd ~/ecc
node scripts/ecc.js install --profile=minimal

# Enable full features per project via environment variables
cd ~/my-complex-project
export ECC_HOOK_PROFILE=full
claude
```

---

## What Gets Enabled/Disabled?

| Feature | Plain Claude | ECC Minimal | ECC Full |
|---------|-------------|-------------|----------|
| Basic coding help | ✅ | ✅ | ✅ |
| `ecc:plan`, `ecc:tdd` commands | ❌ | ✅ | ✅ |
| Auto-formatting hooks | ❌ | ❌ | ✅ |
| TDD gates | ❌ | ❌ | ✅ |
| Code review agents | ❌ | ✅ | ✅ |
| Security scanning | ❌ | ❌ | ✅ |

---

## Updating ECC

To get the latest features and fixes:

```bash
# Navigate to your ECC directory
cd ~/ecc

# Pull latest changes
git pull

# Reinstall to update global configuration
node scripts/ecc.js setup
```

**Create an alias for convenience:**

Add to your `.bashrc` or `.zshrc`:
```bash
alias ecc-update="cd ~/ecc && git pull && node scripts/ecc.js setup && cd -"
```

Then simply run:
```bash
ecc-update
```

**Checking for updates:**

```bash
cd ~/ecc
git fetch
git status  # Shows if you're behind origin/main
```

**Note:** Updates to ECC only affect new Claude sessions. Restart your Claude CLI after updating.

---

## Complete Removal (If Needed)

To completely uninstall ECC:

```bash
cd ~/ecc
node scripts/ecc.js uninstall
```

Your Claude Code will work normally without ECC. To reinstall later:

```bash
cd ~/ecc
node scripts/ecc.js install --profile=full
```

---

## Key Principle

**ECC is like an IDE extension** - it enhances your development environment but doesn't become part of your application's source code. Install it once globally, activate it selectively per project.
