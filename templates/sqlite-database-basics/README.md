# Database Basics (SQLite)

A complete round trip with the database nodes: create a table, fill it from a loop, query it with a parameter and write a summary. It uses a local SQLite file, so there is nothing to install.

## What happens

1. Drops and recreates an `orders` table in `pillar1_database_demo.db`.
2. Loops over five sample orders and inserts each with named placeholders (`:id`, `:customer`, `:status`, `:amount`).
3. Queries the pending orders, highest amount first, with `WHERE status = :status`.
4. Writes the seeded count, the pending rows and the pending count to `pillar1_database_demo_output.txt` and shows a notification.

## Before you run it

- Fully offline and safe to run repeatedly. The table is dropped and rebuilt every time.
- The database and output files are created in the workflow's working folder. The path must be allowed in Workflow Settings.

## Make it yours

- Edit the **seedOrders** variable to change the sample data, or the query to ask something else.
- To use a real server, change **driver** and **connectionString** on the database nodes to `postgres` or `mysql`. The SQL and the `:name` placeholders stay the same.
- Always pass values through `queryParams` instead of building SQL text yourself.
