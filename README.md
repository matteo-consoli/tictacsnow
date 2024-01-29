# TicTacSnow

This repository contains a prototype of TicTacSnow, a containerised game to deploy on Snowpark Container Services.
<p align="center">
    <img src="https://github.com/matteo-consoli/tictacsnow/blob/main/setup/screenshot.png?raw=true" alt="TicTacSnow Screenshot" width="500">
</p>


# Getting Started

## Pre-Reqs (Local and Remote)
1. Docker Desktop installed
2. Python 3.10 installed
3. A non-trial Snowflake account in a supported AWS region.

**NOTE:** Pre-reqs based on steps 1-2-3 of this [Hands-On-Lab](https://quickstarts.snowflake.com/guide/intro_to_snowpark_container_services/index.html#0)


## Snowflake Configs
1. Run the setup/setup.sql code as ACCOUNTADMIN (re-used step 2 of the Quick Start linked above).

## Locally Build & Push

To get started with TicTacSnow, follow these instructions:

1. Clone the repository to your local machine:

```bash
git clone https://github.com/matteo-consoli/tictacsnow/
```

2. Build the Docker image locally:

```bash
# open terminal in the cloned folder
cd app
docker build -t tic-tac-toe .
```

3. Login to the Snowflake Container Registry. Your _<snowflake_registry_hostname>_ is _<orgname>-<acctname>.registry.snowflakecomputing.com_ .

```bash
docker login <snowflake_registry_hostname> -u <username>
# Enter your password when prompted
```

4. Tag the Docker image:

```bash
docker tag tic-tac-toe:latest <snowflake_registry_hostname>/container_hol_db/public/image_repo/tic-tac-toe:dev
```

5. Check the list of Docker images:

```bash
docker image list
```

6. Push the Docker image to the Snowflake Container Registry:

```bash
docker push <snowflake_registry_hostname>/container_hol_db/public/image_repo/tic-tac-toe:dev
```

## Snowflake Deployment 

1. Load Yaml on stage CONTAINER_HOL_DB.PUBLIC.SPECS.

   Before uploading it, don’t forget to replace <snowflake_registry_hostname> with the value of your account: <your_org-your_account>.registry.snowflakecomputing.com
3. Check the Loaded File via SQL command: 
```sql
USE ROLE CONTAINER_USER_ROLE;
LS @CONTAINER_HOL_DB.PUBLIC.SPECS;
```
3. Create the new service: tic_tac_toe_service
```sql
-- Drop command in case you are creating the service again
-- DROP SERVICE IF EXISTS CONTAINER_HOL_DB.PUBLIC.tic_tac_toe_service;

CREATE SERVICE CONTAINER_HOL_DB.PUBLIC.tic_tac_toe_service
    in compute pool CONTAINER_HOL_POOL
    from @specs
    spec='tic-tac-toe-spcs.yaml';
```
4. Check the Service status. It might take a few minutes before the status "READY".
```sql
-- Check Status
CALL SYSTEM$GET_SERVICE_STATUS('CONTAINER_HOL_DB.PUBLIC.tic_tac_toe_service');
-- Once ready you'll have the status "READY", message "RUNNING"

-- Check Logs
SELECT SYSTEM$GET_SERVICE_LOGS('CONTAINER_HOL_DB.PUBLIC.tic_tac_toe_service', 0, 'tic-tac-toe', 50);
```
5. Check the Service Endpoint assigned. It might take a few minutes before an endpoint is available. Once available copy it and paste it in a new window (not logged in).
```sql
SHOW ENDPOINTS IN SERVICE tic_tac_toe_service;
```
6. Create a new user and role and use it in a new window to access your TicTacSnow instance! 
```sql
-- Create your own user and password. 
-- The user accessing TicTacSnow must have CONTAINER_USER_ROLE as default role.

CREATE USER TICTACSNOWPLAYER IDENTIFIED BY 'tictacsnow2024' DEFAULT_ROLE = 'CONTAINER_USER_ROLE';
GRANT ROLE CONTAINER_USER_ROLE to USER MY_GUEST;
```
7. Login with the new credentials created and have fun!
   

## Contributing

Contributions are welcome! If you have any ideas, suggestions, or improvements for TicTacSnow, feel free to reach me out on [Linkedin](https://www.linkedin.com/in/matteo-consoli/).

## NOTE

Don't forget to replace `<snowflake_registry_hostname>` with the appropriate values for your project: <org-account>.registry.snowflakecomputing.com
