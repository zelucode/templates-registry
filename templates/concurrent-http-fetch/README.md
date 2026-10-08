# Concurrent HTTP Fetch (Loop node)

Shows how the Loop node's **Concurrency** setting speeds up slow, independent work. It fetches six public GitHub profiles with up to 3 requests in flight at once instead of one at a time.

## What happens

1. Deletes the output CSV from any earlier run.
2. Loops over the usernames (`torvalds`, `gvanrossum`, `octocat`, `yyx990803`, `sindresorhus`, `tj`), calling `https://api.github.com/users/<name>` for each, 3 at a time.
3. Appends one row per user (username, name, followers, HTTP status) to `pillar4_concurrent_fetch_output.csv`.
4. Shows a notification when all profiles are fetched.

## Before you run it

- **Needs internet access.** It calls the public GitHub API without a login, so GitHub's anonymous rate limit applies if you run it many times in a row.
- The CSV is written to the workflow's working folder. The path must be allowed in Workflow Settings.

## Try this

Set **Concurrency** on the loop node back to 1 and run it again. The steps are identical, only the total time changes. Compare the two runs in the Run Audit view.

## Make it yours

- Change the **usernames** variable, or point the HTTP Request node at another API.
- Keep the Concurrency modest. Many APIs throttle or block a burst of parallel requests.
