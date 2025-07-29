<img src="https://user-images.githubusercontent.com/74038190/212284115-f47cd8ff-2ffb-4b04-b5bf-4d1c14c0247f.gif" width="100%">

# Dump-Restoration

> **Straightforward Approach:** Drop the old table, recreate an empty `ecpmp` table placeholder, copy your SQL dump into the container (it will auto-import on startup), and then verify in TablePlus.

<img src="https://user-images.githubusercontent.com/74038190/212284115-f47cd8ff-2ffb-4b04-b5bf-4d1c14c0247f.gif" width="100%">

## Prerequisites

* Docker running with a PostgreSQL container named `pg_container` (adjust name as needed).
* SQL dump file `ecpmp_dump.sql` in your `~/Downloads` folder.
* Basic terminal familiarity.
* (Optional) TablePlus or another GUI client to inspect tables.

<img src="https://user-images.githubusercontent.com/74038190/212284115-f47cd8ff-2ffb-4b04-b5bf-4d1c14c0247f.gif" width="100%">

## Step 1: Drop Previous & Create a Blank `ecpmp` Table

1. Open your terminal and enter the container shell:

   ```bash
   docker exec -it pg_container bash
   su - postgres
   ```
2. Connect to your database (replace `mydb` if different):

   ```bash
   psql -d mydb
   ```
3. Run these commands to remove any existing table and define an empty placeholder:

   ```sql
   DROP TABLE IF EXISTS ecpmp;

   -- Create a blank table; columns will be applied when the dump loads
   CREATE TABLE ecpmp ();  -- no schema needed here
   ```
4. Exit `psql` and leave the container shell:

   ```bash
   \q
   exit
   ```

<img src="https://user-images.githubusercontent.com/74038190/212284115-f47cd8ff-2ffb-4b04-b5bf-4d1c14c0247f.gif" width="100%">

## Step 2: Copy the Dump File into the Container

Simply copy your SQL dump into the container’s `/docker-entrypoint-initdb.d` directory. On container restart, PostgreSQL images auto-run any `.sql` files in this folder.

```bash
docker cp ~/Downloads/ecpmp_dump.sql pg_container:/docker-entrypoint-initdb.d/ecpmp_dump.sql
```

> **Note:** You do *not* need to manually import—it runs automatically when the container starts.

---

## Step 3: Restart Container (If Needed)

If your container is already running, restart it so the dump is executed:

```bash
docker restart pg_container
```

Container logs will show the import progress.

<img src="https://user-images.githubusercontent.com/74038190/212284115-f47cd8ff-2ffb-4b04-b5bf-4d1c14c0247f.gif" width="100%">

## Step 4: Verify in TablePlus

1. Open TablePlus and connect to your `mydb` database.
2. Select the `ecpmp` table.

<img src="https://user-images.githubusercontent.com/74038190/212284115-f47cd8ff-2ffb-4b04-b5bf-4d1c14c0247f.gif" width="100%">
3. Check the **row count** and browse a few records to confirm successful import.

---

You're all set! 🎉  This flow ensures the dump is applied automatically without manual `psql` commands. If anything goes wrong, check container logs (`docker logs pg_container`) for errors and verify file permissions.
