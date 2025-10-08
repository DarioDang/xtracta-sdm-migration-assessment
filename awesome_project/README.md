# Xtracta SDM Migration Assessment

## 1. Evironement Details 
- MySQL Version: 8.0 (proposed by Docker container from mysql:8.0 image)
- SDM Version: 0.6.4
- Python Version: 3.11.1 (virtual environment created with `pyenv`)
- Operating System: MacOS 

## 2. How the SDM Tool Works 
 [Need to be done] 

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