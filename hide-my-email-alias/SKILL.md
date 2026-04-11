---
name: hide-my-email-alias
description: Create an iCloud Hide My Email alias with Playwright browser automation, and optionally save the result as JSON.
---

# Create Hide My Email Alias

Use this skill when the user wants a real iCloud Hide My Email alias created through browser automation.

## Workflow

1. Use Playwright browser tools, not shell commands.
2. Attach to the user's current browser tab first.
3. If the current tab is not already on iCloud+, navigate to `https://www.icloud.com/icloudplus/`.
4. If the user is not signed in, stop interacting and wait for the user to finish sign-in before continuing.
5. Insert a short jitter wait between each browser action. Use small randomized waits rather than a single fixed delay.
6. Open **Hide My Email** from the iCloud+ page.
7. Wait with jitter, then click **Add** to start alias creation.
8. Read the generated alias shown in the dialog before submission.
9. Inspect the existing Hide My Email entries on the iCloud page and extract any numeric labels or notes already in use.
10. Choose the lowest missing positive integer starting from `1`. Examples: if `3, 4, 5` exist, use `1`, then `2`; if `1, 2, 3` exist, use `4`, then `5`.
11. Fill both the label and note fields with that same numeric sequence value.
12. If the user requests persistence, save aliases as a flat JSON string array such as `["created_alias_1@icloud.com", "created_alias_2@icloud.com"]`.
13. Confirm this creates a real alias in the user's iCloud account before clicking the final create action.
14. After creation, append only the exact created alias address to the JSON array file. Do not wrap entries in objects.

## Required checks

- Read the dialog text, because the create button may be disabled until the label field is filled.
- Determine the next numeric sequential value from the numeric labels or notes already visible on the iCloud Hide My Email page, not from the saved JSON file.
- Always start from `1` and choose the lowest missing positive integer.
- Use the same numeric sequential value for both label and note for that creation attempt.
- If Apple shows `You have reached the limit of addresses you can create right now. Please try again later.`, stop and report that exact message.
- Do not invent alias details. Read the generated address from the page after Apple surfaces it.
- If saving JSON, keep the file content as a flat array of strings only.

## Output

Report one of:
- alias created successfully, with the exact alias address and sequence number used
- waiting for user sign-in before continuing
- blocked by Apple confirmation or quota limit
- JSON file path, if the user asked to save the result
