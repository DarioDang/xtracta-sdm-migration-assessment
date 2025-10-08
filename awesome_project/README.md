# Xtracta SDM Migration Assessment

## 1. Evironement Details 
- MySQL Version: 8.0 (running in a Docker container using the mysql:8.0 image)
- SDM Version: 0.6.4
- Python Version: 3.11.1 (virtual environment created with `pyenv`)
- Operating System: MacOS 

## 2. How the SDM Tool Works 
The SDM (Schema Data Migration) tool help us to manage and version control data in MySQL databases, which include not only change in schema structure but also in the data itself. For example, from my experience when ruuning the `sdm init` , the SDM tool will extract the current structure of the original database (e.g: prod) and it creates the sample schema. Then I can edit the `.sql` file to change the structure such as adding address column, and then use the `sdm make - schema` to create the migration plan of that change. Not stopping at this point, SDM also support us to  migration data such as inserting, updating or deleting. 

And the last but not least which is also an interesting point if this tool as everytime we run the migration, the tool will apply the changes to the dev environment and record the entire migration history that we can manage, rollback and extract (e.g: _migration_history, _migration_history_log). These act as a version control just like git that help us to fully manage the change between current and the past. Thanks to this feature, we can easily track and rollback to previous versions if necessary, especially when dealing with complex database changes across different environments such as development, testing, and production.

## 3. Challenge Encountered and How I Solved It

#### Description
When I ran the SDM initilization command: `MYSQL_PWD='<my-password>' sdm init --host 127.0.0.1 --port 3306 -u root --schema awesome_db` , the process failed with this error message: `ImportError: cannot import name 'StrEnum' from 'enum'`

#### Investigation
- The traceback pointed to SDM’s internal code: `from enum import StrEnum` and my python was `3.10.18`. 
- I check the python document [`StrEnum`](https://pypi.org/project/StrEnum/) library and [stackoverflow](https://stackoverflow.com/questions/75040733/is-there-a-way-to-use-strenum-in-earlier-python-versions) discussion, I figured out that the class `StrEnum` was introduced in `Python 3.11`, not available in `Python 3.10` or earlier.
- This confirmed that the installed SDM version was using a feature only supported in `Python 3.11+` .

#### Solved
To resolve the issue, I upgraded my environment to `Python 3.11.1` using `pyenv` then reinstalled SDM locally, and successfully reran the initialization command. This experience helped me understand the importance of verifying library compatibility with specific Python versions before setup. 


## 4. Project Workflow Summary
- Step 1: Initialize project
- Step 2: Add environment
- Step 3: Migrate schema
- Step 4: Make schema migration plan
- Step 5: Make data migration plan
- Step 6: Rollback
- Step 7: Export migration history