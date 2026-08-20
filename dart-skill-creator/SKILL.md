---
name: dart-skill-creator
description: >
  Use when creating Agent Skills for Dart or Flutter packages using pkg:skills. Helps package
  maintainers create SKILL.md files by analyzing their package's README, API, and documentation,
  then generating focused instructions for AI assistants. The skill is installed in a skills/
  directory at the package root. Trigger when users mention "create a skill for my package",
  "add skills to my Dart package", "write a SKILL.md", or want to help AI assistants understand
  their package's APIs and patterns.
---

# Creating Agent Skills for Dart/Flutter Packages

This skill guides you through creating Agent Skills for Dart and Flutter packages using the pkg:skills system. Package maintainers ship skills in a `skills/` directory so that when users run `dart run skills@ get`, their AI assistant learns how to use the package properly.

## Understanding the pkg:skills System

**The Problem:** When developers add a Dart package to their project, their AI coding assistant has no idea how to use it. The AI guesses APIs, invents patterns, and hallucinates methods.

**The Solution:** Package authors ship skills alongside their code. Users run `dart run skills@ get`, and their AI assistant gets context-aware instructions for every dependency that provides skills.

**How It Works:**
1. Package maintainers create a `skills/` directory at the package root (next to `lib/`)
2. Each skill is a subdirectory containing `SKILL.md` (and optionally `scripts/`, `references/`, `assets/`)
3. Users run `dart run skills@ get` in their project
4. The CLI installs skills into their agent's directory (`.claude/skills/`, `.cursor/skills/`, etc.)
5. The AI assistant now understands the package's APIs, patterns, and best practices

## Step 1: Gather Package Context

Before creating a skill, analyze the package to understand what should go into the skill:

1. **Read the package README** - Look for:
   - Quick start / getting started sections
   - Common usage patterns
   - API examples
   - Best practices and conventions
   - Common pitfalls or gotchas

2. **Scan the lib/ directory** - Identify:
   - Main entry points and exported APIs
   - Core classes and their purposes
   - Common configuration patterns
   - Builder patterns, factories, or initialization sequences

3. **Check for existing documentation** - Look in:
   - `doc/` or `docs/` directories
   - API documentation comments
   - Example projects in `example/`
   - CHANGELOG.md for recent API changes

4. **Identify the package's key features** - Determine:
   - What is this package primarily used for?
   - What are the 2-3 most common use cases?
   - Are there multiple distinct feature areas that need separate skills?

## Step 2: Plan the Skill Structure

**Critical: Directory Location**

Skills **must** be in a `skills/` directory at your **package root** (as a sibling to `lib/`, `pubspec.yaml`, `test/`, etc.):

```
my_package/                    # Package root
├── lib/
├── test/
├── pubspec.yaml
└── skills/                    # Skills directory HERE
    ├── my-package-basics/     # First skill
    │   └── SKILL.md
    └── my-package-advanced/   # Second skill
        ├── SKILL.md
        ├── scripts/
        └── references/
```

When users run `dart run skills@ get`, the CLI scans dependencies for `skills/` directories at the package root and installs them into the user's agent directory (`.claude/skills/`, `.cursor/skills/`, etc.).

**Naming Convention:**
- Skill directory must start with the package name followed by a hyphen
- For package `my_package`: valid names are `my_package-feature` or `my-package-feature` (hyphens recommended)
- Examples: `serverpod-api-design`, `serverpod-code-generation`, `dio-http-requests`

**Determine Scope:**
- **One skill per major feature area** - Don't dump everything into one skill
- Examples:
  - `dio-http-requests` (for making HTTP requests)
  - `dio-interceptors` (for request/response interceptors)
  - `riverpod-state-management` (for basic state)
  - `riverpod-async-data` (for async operations)

**Decide What to Include:**
- Keep SKILL.md under 500 lines
- Move detailed reference docs to `references/` subdirectory
- Put helper scripts in `scripts/` subdirectory
- Place templates or assets in `assets/` subdirectory

## Step 3: Write the SKILL.md

The goal is to teach the AI how to use your package correctly. Focus on:

1. **Concrete examples** - Show actual code, not abstract descriptions
2. **Common patterns** - What do most users need to do?
3. **Gotchas** - What mistakes do people make?
4. **Error handling** - Package-specific exceptions and how to handle them

### Suggested Structure

```markdown
---
name: package-name-skill-name
description: Use when the user is working with [specific feature] to ensure [specific outcome]. Mention key scenarios, API names, or contexts that should trigger this skill.
---

# Brief Overview

1-2 sentences about what this skill covers and when to use it.

## Core Usage Pattern

Show the most common way to use your package:

```dart
import 'package:your_package/your_package.dart';

// Show the basic pattern
final thing = YourPackage.create(...);
final result = await thing.doSomething();
```

## Common Use Cases

### [Scenario 1: Descriptive Name]

Explain when users need this and show how:

```dart
// Concrete example with real code
```

### [Scenario 2: Another Common Task]

```dart
// Another concrete example
```

## Error Handling

If your package has specific exceptions:

```dart
try {
  await operation();
} on YourPackageException catch (e) {
  // Handle specific errors
}
```

## Things to Avoid

Common mistakes:
- DON'T do X - do Y instead
- DON'T ignore Z - it causes problems because...
```

**This is a guide, not a rigid template.** Adapt the structure to your package. If your package doesn't have complex error handling, skip that section. If initialization is critical, emphasize it. Follow what makes sense for your specific case.

### Common Dart/Flutter Patterns

If your package uses common Dart/Flutter patterns (initialization, builders, code generation, DI, etc.), see [references/dart-flutter-patterns.md](references/dart-flutter-patterns.md) for examples of how to document them. **Only include patterns that apply to your package** — don't copy examples that aren't relevant.

### Writing Effective Descriptions

The `description` field is crucial - it determines when Claude activates the skill. Make it specific and action-oriented:

**Good descriptions:**
- "Use when implementing HTTP requests with Dio, configuring interceptors, or handling network errors"
- "Use when setting up Serverpod API endpoints, defining protocol classes, or implementing server-side logic"
- "Use when creating Riverpod providers, managing async state, or implementing dependency injection"

**Poor descriptions:**
- "A skill about HTTP" (too vague)
- "Dio package helper" (doesn't explain when to trigger)
- "Use this skill" (no context)

### Writing for the AI

Remember you're writing instructions for an AI assistant, not human documentation:

- **Be direct and prescriptive** - Use imperative voice: "Always initialize first", "Prefer X over Y"
- **Include concrete examples** - Show actual code, not abstract descriptions
- **Explain the why** - Help the AI understand reasoning, not just rules
- **Use consistent formatting** - Make patterns easy to recognize and apply
- **Keep it focused** - Cover one feature area well rather than everything poorly

## Step 4: Add Optional Resources

### Helper Scripts (scripts/)

If users frequently need to run specific commands or the AI keeps writing the same helper code, bundle it:

```
skills/package-name-feature/
  SKILL.md
  scripts/
    generate_models.dart    # Run with `dart run ../skills/.../scripts/generate_models.dart`
    validate_config.dart
```

Reference scripts from SKILL.md:
```markdown
## Code Generation

Run the bundled script to generate models:

```bash
dart run skills/package-name-feature/scripts/generate_models.dart
```
```

### Reference Documentation (references/)

For detailed API references, migration guides, or spec documents over 300 lines:

```
skills/package-name-feature/
  SKILL.md
  references/
    api_reference.md
    migration_guide.md
```

From SKILL.md, point to references with context about when to read them:
```markdown
For complete API details, see [api_reference.md](references/api_reference.md).
```

### Assets (assets/)

For templates, config files, or other static resources:

```
skills/package-name-feature/
  SKILL.md
  assets/
    config_template.yaml
    service_template.dart
```

## Step 5: Test the Skill

Before considering the skill complete:

1. **Install it locally:**
   ```bash
   dart run skills@ get
   ```

2. **Test with realistic prompts** - Try actual user requests:
   - "Help me set up [package] in my Flutter app"
   - "I'm getting a [specific error] with [package]"
   - "How do I [common task] using [package]?"

3. **Verify the AI uses the skill** - Check that:
   - The AI follows the patterns you documented
   - Examples in SKILL.md are accurate
   - The skill triggers in appropriate contexts

4. **Iterate based on results** - Update the skill based on:
   - Mistakes the AI makes
   - Patterns you forgot to document
   - Confusing or incomplete instructions

## Step 6: Update When APIs Change

Keep skills in sync with your package:

- When you release new features, add them to the skill
- When APIs change, update examples and patterns
- When you deprecate features, mark them clearly
- Version skills alongside your package releases

## Workflow Summary

When a user asks to create a skill for their Dart/Flutter package:

1. **Understand the package structure:**
   - Confirm they want to create the skill in `<package_root>/skills/`
   - Read README.md for usage patterns
   - Scan lib/ for main APIs
   - Check example/ for common use cases

2. **Ask clarifying questions:**
   - "What are the 2-3 most common ways users interact with this package?"
   - "Are there any common mistakes or gotchas users encounter?"
   - "Should this be one skill or multiple skills for different features?"

3. **Create the skill directory at package root:**
   - Create `<package_root>/skills/<package-name>-<feature>/`
   - Write `SKILL.md` with frontmatter and usage guidance
   - Add `scripts/`, `references/`, or `assets/` if needed

4. **Focus on what the AI needs to know:**
   - How to use the package correctly
   - Common patterns and use cases
   - Package-specific error handling
   - Mistakes to avoid
   - See [references/dart-flutter-patterns.md](references/dart-flutter-patterns.md) for pattern examples

5. **Test the skill:**
   - Run `dart run skills@ get` from a project that depends on the package
   - Verify the skill appears and can be installed
   - Try realistic user prompts
   - Update based on AI behavior

6. **Maintain the skill:**
   - Update when releasing new features
   - Keep examples current with API changes
   - Mark deprecated features clearly

## Example: Creating a Skill for a Hypothetical Package

Let's say the user has a package called `cloud_sync` that syncs data to the cloud:

**Step 1 - Analyze:** You read their README and see:
- Main class is `CloudSyncClient`
- Requires initialization with API key
- Common operations: `upload()`, `download()`, `sync()`
- Uses Stream for progress updates
- Has specific error types: `NetworkError`, `AuthError`, `QuotaError`

**Step 2 - Plan:**
- Create `cloud_sync/skills/cloud-sync-basics/` at the package root (one skill is enough for this package)
- Focus on initialization, common operations, error handling

**Step 3 - Write SKILL.md:**

```markdown
---
name: cloud-sync-basics
description: Use when working with CloudSync for uploading, downloading, or syncing data to the cloud. Covers initialization, progress tracking, and error handling.
---

# CloudSync Basics

This skill covers the fundamentals of using the cloud_sync package for data synchronization.

## Initialization

Always initialize CloudSyncClient with your API key before use:

```dart
import 'package:cloud_sync/cloud_sync.dart';

final client = CloudSyncClient(
  apiKey: 'your-api-key',
  endpoint: 'https://api.example.com',
);
```

## Common Operations

### Uploading Files

```dart
final result = await client.upload(
  file: File('path/to/file'),
  destination: 'remote/path',
  onProgress: (progress) {
    print('Upload progress: ${progress.percentage}%');
  },
);
```

### Downloading Files

```dart
await client.download(
  source: 'remote/path',
  destination: File('local/path'),
);
```

## Error Handling

Handle package-specific errors explicitly:

```dart
try {
  await client.sync();
} on AuthError catch (e) {
  // Re-authenticate
} on QuotaError catch (e) {
  // Handle quota exceeded
} on NetworkError catch (e) {
  // Retry with backoff
}
```

## Things to Avoid

- DON'T create multiple client instances - reuse one instance
- DON'T ignore onProgress callbacks - they're important for UX
- DON'T catch generic Exception - use specific error types
```

**Step 4 - Test:**
```bash
dart run skills@ get cloud_sync
```

Then test with prompts like:
- "Help me upload a file using cloud_sync"
- "I need to sync data with error handling"

**Step 5 - Iterate:**
Update based on any mistakes the AI makes or patterns you missed.

## Final Checklist

Before finalizing a skill:

- [ ] Skill directory is at `<package_root>/skills/<package-name>-<feature>/`
- [ ] Skill name follows `<package>-<feature>` convention
- [ ] Description is specific and action-oriented
- [ ] Includes concrete code examples (not pseudocode)
- [ ] Covers initialization if required
- [ ] Shows error handling patterns
- [ ] Lists common mistakes to avoid
- [ ] Under 500 lines (or splits detailed content to references/)
- [ ] Tested with `dart run skills@ get` from a dependent project
- [ ] Matches current package version

Remember: A good skill teaches the AI to work with your package the way you intended, preventing hallucinations and reducing the back-and-forth between users and their AI assistant.
