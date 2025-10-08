# Xtracta SDM Migration Assessment
```bash
source ~/.zshrc
pyenv activate py311-sdm
```

## CHALLENGES 
### 1. `zsh: command not found: sdm`

####  Description: 
When following the document running the initialization command: `MYSQL_PWD='root' sdm init --host 127.0.0.1 --port 3306 -u root --schema awesome_db` the terminal return `zsh: command not found: sdm`.

This indicated that the sdm (Schema Data Migration) command-line tool was not installed or not available in the system PATH, so my MacBook shell (zsh) couldn’t find it.

#### Investigation
1. I confirmed by ruuning `which sdm` in terminal to check whether if I have this installed. Hence, there is no output 

2. I re - read the document and noted that it can be used either via `docker image` or install locally using Python >= 3.8 confirmed in python_requires in `setup.cfg`

#### Solved 
To resolve the  issue, I decided to install the Schema Data Migration (SDM) tool locally instead of running it through Docker

1. First I clone the sdm repo `git clone https://github.com/Beim/schema-data-migration.git`

2. Then inside the `schema-data-migration` folder running `pip3 install .` 

### 2. `mysqlclient build failed during SDM installation`
####  Description: 
When installing SDM locally with `pip3 install .` inside the cloned `schema-data-migration` folder, the installation failed with error:  

```bash 
error: subprocess-exited-with-error
Exception: Can not find valid pkg-config name.
Specify MYSQLCLIENT_CFLAGS and MYSQLCLIENT_LDFLAGS env vars manually
```
#### Investigation
1. I recognized that mysqlclient is a Python wrapper for MySQL’s client library, so it needs MySQL development headers to compile.
2. The message `Can not find valid pkg-config name` suggested that macOS couldn’t find the required MySQL or MariaDB client libraries.
3. I confirmed by running `pkg-config --exists mysqlclient` which returned error, therefore, confirming missing MySQL dev tools.

#### Solved
To fix it, I installed MySQL client library. Since the package is keg-only on macOS, I exported the library and include paths manually:

```bash 
export PATH="/opt/homebrew/opt/mysql-client/bin:$PATH"
export LDFLAGS="-L/opt/homebrew/opt/mysql-client/lib"
export CPPFLAGS="-I/opt/homebrew/opt/mysql-client/include"
export PKG_CONFIG_PATH="/opt/homebrew/opt/mysql-client/lib/pkgconfig"
```

After include paths manually, I re-run `pip3 install .` then the `sdm` was succesfully installed. In overall, The error occurred because the MySQL development headers were not available for the mysqlclient package to compile. By installing and linking the Homebrew MySQL client, I resolved the missing library paths.

### 3. `ImportError: cannot import name 'StrEnum' from 'enum'`
####  Description: 
When I ran the SDM initilization command: `MYSQL_PWD='<my-password>' sdm init --host 127.0.0.1 --port 3306 -u root --schema awesome_db` , the process failed with this error message: `ImportError: cannot import name 'StrEnum' from 'enum'`

#### Investigation
1. The traceback pointed to SDM’s internal code: `from enum import StrEnum` and my python was `3.10.18`. 
2. I check the python document `StrEnum` library[https://pypi.org/project/StrEnum/] and stackoverflow[https://stackoverflow.com/questions/75040733/is-there-a-way-to-use-strenum-in-earlier-python-versions], the class StrEnum was introduced in Python 3.11, not available in Python 3.10 or earlier.
3. This confirmed that the installed SDM version was using a feature only supported in Python 3.11+ . 

#### Solved
I resolved this issue by upgrading my virtual environment to `Python 3.11.1`, which includes the StrEnum class used by SDM.

### 4. `[Errno 2] No such file or directory: 'skeema'`
#### Description: 
When running `sdm init`, the process failed because the Skeema binrary was missing from the system. 

#### Investigate:
1. The SDM log showed: 

```
Run skeema init ...
[ERROR] [Errno 2] No such file or directory: 'skeema'
```

This shown that SDM relies on the external tool call `Skeema` but it was not installed. 

#### Solved
To solved this error, I installed the `Skeema` library inter terminal by"

```bash
brew install skeema
```
After installed sucessfully, I re-run the `init` this create the following files exactly the same with document.





