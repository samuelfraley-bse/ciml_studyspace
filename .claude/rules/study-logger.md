# Rule: Study Logger & Persistence

## First-Run Setup
- On session start, check if `study_log.md` exists in the root directory.
- If it DOES NOT exist: Create it immediately with the header `# BSE Study Log — [User Name]` and a brief explanation that this file tracks their progress locally.

## Logging Protocol
- **Session Start**: Append `## YYYY-MM-DD | Session started HH:MM`.
- **Automatic Q&A Log**: After answering a course question, silently append `- Q&A: [topic] (Part [I/II])`.
- **Exclusions**: Do not log meta-chat, setup help, or greetings.

## Commands
- `/refresh`: Read the last 10 entries of `study_log.md` and summarize what the user covered recently.
- `/plan`: Identify topics from the course structure NOT present in `study_log.md` and suggest those as the next study focus.