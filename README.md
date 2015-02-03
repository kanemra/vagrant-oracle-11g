vagrant-oracle-11g
==================

Vagrant script to install Oracle Database 11g.

Detailed instructions are at [http://java.paykin.info/install-oracle-database-vagrant-puppet/](http://java.paykin.info/install-oracle-database-vagrant-puppet/).


1.  Clone this repo.
2.  Download Oracle Database installation from [Oracle downloads site](http://www.oracle.com/technetwork/database/enterprise-edition/downloads/index.html). For this tutorial we need **Oracle Database 11g Release 2 (11.2.0.1.0)** for Linux 64bit (Linux x86-64). There are 2 files to download. You need to place them to ```vagrant-oracle-11g/modules/oracle/files``` directory. While it is downloading, proceed to next step.
3.  In ```Vagrant``` file you may set machine host name, amount of memory.
4.  In ```manifests/base.pp``` you can set Oracle database parameters.
5.  In command line, go to directory you extracted attached file and write ```vagrant up```. That’s it. Installation will take a while, on my machine it too about 10 minutes. When it finishes, you can connect to the machine via SSH, issuing ```vagrant ssh``` command. If you prefer using standard ssh, you can connect to it with ```ssh vagrant@localhost -p 2222``` command. Vagrant default root password is *vagrant*. In order to validate that everything works, you can run ```sqlplus / as sysdba``` and execute some simple query, say ```SELECT SYSDATE FROM dual```. If you see current date – everything works ok. Also you can connect using SqlDeveloper or any othe client.
6.  Connect to virtual machine via ``` vagrant ssh```; Run ```sqlplus / as sysdba``` and execute following commands to create schema for project:

    ```sql
    create user user_name identified by password;
    grant dba to user_name;

    drop user user_name cascade; -- to delete schema
    ```
7.  Install Oracle database backend for Python ([instructions](https://gist.github.com/kanemra/fd98653379775ea0a61a) for Mac OS X):
    ```bash
    pip install cx_Oracle
    ```
    Configure database in django settings:

    ```python
    DATABASES = {
        'default': {
            'ENGINE': 'django.db.backends.oracle',
            'NAME': 'localhost/orcl',
            'USER': 'user_name',
            'PASSWORD': 'password',
            # Empty for localhost through domain sockets or '127.0.0.1' for
            # localhost through TCP.
            'HOST': '',
            'PORT': '',  # Set to empty string for default.
            'OPTIONS': {
                'threaded': True,
            },
        }
    }
    ```
    Create tables and load initial data:
    ```bash
    # django <1.7
    python manage.py syncdb --all
    python manage.py migrate --fake
    
    # django >=1.7
    python manage.py migrate
    
    python manage.py loaddata /path/to/initial_data.json
    ```
