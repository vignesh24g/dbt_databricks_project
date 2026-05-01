# 🚀 dbt on Databricks — End‑to‑End Setup with `sales_orders`

You don’t need to run dbt locally if your goal is **“all in Databricks.”** Databricks supports dbt directly — you can develop, run, and schedule dbt projects inside the workspace.  

Below is the full workflow, demonstrated with a new table `sales_orders`.

---

## 🏗️ Step 1: Workspace & SQL Warehouse
- Create a **Databricks workspace** (Community Edition or enterprise).  
- Go to **SQL Warehouses** → create a warehouse.  
- Copy the **HTTP Path** (needed for dbt).  

---

## 🔑 Step 2: Personal Access Token
- In Databricks → **User Settings → Access Tokens → Generate New Token**.  
- Save the token securely 

## ⚙️ Step 3: Install dbt-databricks
In your Databricks notebook or repo environment:
```bash
%pip install dbt-databricks
```

---

## 📂 Step 4: Configure dbt Profile
Inside your Databricks Repo, create `profiles.yml`:

```yaml
databricks_demo:
  target: dev
  outputs:
    dev:
      type: databricks
      catalog: workspace
      schema: default
      host: https://<your-workspace-url>
      http_path: /sql/1.0/warehouses/<warehouse-id>
      token: "ur token"
```

> ⚠️ Notice: keeps the token out of GitHub.

---
## 📝 Step 5: dbt_poject.yml
```yaml
name: dbt_databricks_project
version: 1.0.0
profile: dbt_databricks_project
config-version: 2

flags:
  use_concurrent_microbatch: true
  use_managed_iceberg: true

models:
  dbt_databricks_project:
    +materialized: view
    +table_format: delta
```

## 📝 Step 6: Create a Sample Model 
we are avoiding dbt init since it requires interactive inputs which cannot be done from notebook
Inside `models/` create `example.sql`:

```sql
-- models/sales_orders.sql
select 1 as id, 'hello' as name;
```

---

## ▶️ Step 6: Run dbt in Databricks
From your Databricks notebook:
```bash
!dbt debug 
```

```bash
!dbt run --select example
```

- dbt compiles the SQL into Spark SQL.  
- Executes it on your Databricks SQL Warehouse.  
- Creates a Delta table/view (`default.example`).  

---

## 🔍 Step 8: Test & Document
```bash
!dbt test --select example
!dbt docs generate --select example
```

