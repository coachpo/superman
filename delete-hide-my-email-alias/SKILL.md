---
name: delete-hide-my-email-alias
description: Deactivate and delete one or more iCloud Hide My Email aliases with Playwright browser automation.
---

# Delete Hide My Email Alias

Use this skill when the user wants real Hide My Email aliases removed from their iCloud account through browser automation.

## Workflow

1. Use Playwright browser tools, not shell commands.
2. Attach to the user's current browser tab first.
3. If the current tab is not already on iCloud+, navigate to `https://www.icloud.com/icloudplus/`.
4. If the user is not signed in, stop interacting and wait for the user to finish sign-in before continuing.
5. Before destructive actions, verify the signed-in iCloud account from visible page identity such as the account control, profile area, or other account text on the page. If the account cannot be verified from page state, stop and ask the user to confirm before continuing.
6. Treat deletion as destructive. Restate the exact deletion rule and get explicit confirmation before clicking any deactivate or delete action.
7. Open **Hide My Email** from the iCloud+ page.
8. Read the alias list from the page before acting. If the full candidate set is not visible yet, continue reading the list until the full candidate set needed for the user rule is known.
9. Use alias number, label, or list position only to discover candidate entries. Resolve every destructive target to its exact alias email address before proceeding.
10. If the user requested bulk deletion, enumerate the full exact alias address set first and report those matched addresses before destructive actions unless the user already clearly confirmed that exact address set.
11. For active aliases, open the alias details, verify the exact alias address matches the resolved target address, click **Deactivate email address**, then confirm **Deactivate**.
12. Wait for the UI to finish loading, confirm that the same alias is now inactive, then delete that same alias before moving on.
13. For inactive aliases, open the alias details, verify the exact alias address matches the target, click **Delete address**, then confirm **Delete**.
14. After each deletion, return to the list view and continue with the next exact target.
15. When deleting a range or predicate-based set, process aliases in small batches and re-read the list between batches.
16. If the refreshed list no longer matches the remaining confirmed target set exactly, stop and report the drift before any more deletions.
17. After the deletion pass, re-read the list and verify the target aliases are gone.
18. If the user asks for persistence, save a JSON summary of deleted aliases as a flat array of strings containing the exact deleted alias addresses. If the user did not specify a file path, save it in the current workspace as `deleted-hide-my-email-aliases.json`.

## Required checks

- Do not guess which aliases match. Read the actual alias list from the page and match only exact visible entries.
- Use alias number, label, or list position only for discovery. Before destructive actions, resolve the target set to exact alias addresses.
- Before clicking **Deactivate** or **Delete**, confirm the alias detail view shows the exact resolved target email address.
- Before the first destructive action in a run, restate the exact alias address or exact alias address set being deleted and make sure that is what the user confirmed.
- If the page shows a confirmation dialog, read it and use the confirmation action only after it matches the intended destructive step.
- Use short waits between actions, and always wait for loading states to clear before starting the next alias.
- If an alias is already inactive, skip straight to delete instead of attempting to deactivate it again.
- If an alias cannot be found, stop and report which expected alias was missing.
- If the rule is ambiguous, such as `delete old aliases`, ask for an exact criterion before deleting anything.
- Do not reactivate aliases unless the user explicitly asks.
- Do not delete aliases outside the confirmed target set.

## Stop conditions

Stop immediately and report the reason if any of these happen:

- the user has not explicitly confirmed the destructive rule
- the page is not signed in or not on the correct iCloud account
- the full target set cannot be determined from the page state
- the confirmation dialog text does not match the intended action
- the refreshed batch view no longer matches the remaining confirmed target set exactly
- a label, number, or list-position rule resolves to multiple aliases, shifts unexpectedly, or cannot be reduced to exact alias address targets
- an expected alias is missing, duplicated unexpectedly, or no longer matches the confirmed rule
- Apple blocks the action, shows an unexpected error, or leaves the UI in a stuck loading state

## Output

Report all of:
- the exact aliases matched for deletion
- which aliases were deactivated first
- which aliases were permanently deleted
- any aliases skipped, blocked, or missing
- JSON file path, if the user asked to save the deletion result
