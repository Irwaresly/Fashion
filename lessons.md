# Project Lessons & Mistake Prevention Log (lessons.md)

This file records past issues, friction points, and the concrete rules established to prevent them from recurring. Review this file at the start of every session.

---

## Lesson 1: Windows PowerShell Execution Policies & PATH Updates
* **Context:** When running commands in Windows PowerShell, scripts like profile initialization may fail if execution policy is restricted, and newly installed CLI binaries (like `gh` or `winget` tools) will not be visible in an active terminal session until PATH is refreshed or a new shell is opened.
* **Rule:** 
  1. Always verify binaries using `Get-Command` or absolute paths when testing in automated sessions.
  2. For `fnm` in PowerShell profile, use `& fnm env --use-on-cd | Out-String | Invoke-Expression` to avoid object-to-string array conversion errors.

## Lesson 2: Canonical Documentation First, Zero Guesswork
* **Context:** AI generation fails when scope or design decisions are ambiguous.
* **Rule:** Never generate code before consulting `PRD.md`, `FRONTEND_GUIDELINES.md`, and `BACKEND_STRUCTURE.md`. Stick strictly to the exact colors, typography, and database columns specified.

## Lesson 3: Fallback Mechanisms for AI Services
* **Context:** External serverless GPU APIs (like Fal.ai or Replicate for VTON and segmentation) can hit rate limits, network timeouts, or require API keys that may not yet be configured locally.
* **Rule:** Always implement an intelligent local fallback (e.g. MediaPipe in-browser keypoints + HTML5 Canvas compositing for try-ons) so the application functions seamlessly in local dev even without live API credentials.

## Lesson 4: Windows Controlled Folder Access (Ransomware Protection)
* **Context:** Windows Defender `EnableControlledFolderAccess = 1` intercepts file/directory write and creation events in standard personal directories like `C:\Users\USER\Documents\`, throwing silent `FileNotFoundException` / `ERROR_FILE_NOT_FOUND` errors when tools like `git`, `mkdir`, or package managers attempt to build.
* **Rule:** Code projects must always live in a dedicated development directory outside protected libraries (e.g., `C:\Users\USER\Projects\Fashion`), completely bypassing Controlled Folder Access restrictions.
