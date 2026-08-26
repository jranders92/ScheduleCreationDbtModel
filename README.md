# ScheduleCreationDbtModel
Data model using multiple sources to create schedules from labor order requests

## Snowflake setup

`snowflake_setup.provisioner.Provisioner` creates a named warehouse, database,
schema, roles, grants, and a dbt `profiles.yml` entry. It is safe to run more
than once because resource creation uses `IF NOT EXISTS`.

Set these values in `.env` or the shell before provisioning:

```text
SNOWFLAKE_USER=your_user
SNOWFLAKE_PASSWORD=your_password
SNOWFLAKE_ACCOUNT=your_account
SNOWFLAKE_ROLE=ACCOUNTADMIN
```

Preview the generated SQL without connecting to Snowflake:

```python
from snowflake_setup.provisioner import Provisioner

sql = Provisioner(dry_run=True).provision(update_dbt_profile=False)
for statement in sql:
	print(statement)
```

Provision the resources and write the dbt profile:

```python
from snowflake_setup.provisioner import Provisioner

Provisioner(
	warehouse_name="TEAM_WH",
	database_name="TEAM_DB",
	schema_name="TEAM_SCHEMA",
	name="team",
).provision()
```

The dbt role is automatically named `<name>_dbt_role`; the analyst role is
`<name>_analyst_role`. Ingestion is disabled by default. Enable it with
`create_ingestion_role=True`, which creates and grants `<name>_ingestion_role`.
Custom ingestion and analyst role names can be supplied with
`ingestion_role_name` and `analyst_role_name`.

Use `analyst_user_name="analyst_user"` to also create the optional analyst
user and grant it the read-only analyst role. The user itself still needs a
password or an authentication method configured in Snowflake.
