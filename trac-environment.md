# The Trac Environment







Trac uses a directory structure and a database for storing project data. The directory is referred to as the environment.
Trac uses a directory structure and a database for storing project data. The directory is referred to as the **environment**.



Trac supports [ SQLite](http://sqlite.org/), [
PostgreSQL](http://www.postgresql.org/) and [
MySQL](http://mysql.com/) databases. With PostgreSQL and MySQL you have to create the database before running `trac-admin initenv`.


## Creating an Environment



A new Trac environment is created using the [initenv](trac-admin#) command:


```
$ trac-admin /path/to/myproject initenv
```


`trac-admin` will ask you for the name of the project and the [database connection string](trac-environment#database-connection-strings).


### Useful Tips


- Place your environment's directory on a filesystem which supports sub-second timestamps, as Trac monitors the timestamp of its configuration files and changes happening on a filesystem with too coarse-grained timestamp resolution may go undetected in Trac \< 1.0.2. This is also true for the location of authentication files when using [TracStandalone](trac-standalone).

- The user under which the web server runs will require file system write permission to the environment directory and all the files inside. Please remember to set the appropriate permissions. The same applies to the source code repository, although the user under which Trac runs will only require write access to a Subversion repository created with the BDB file system; for other repository types, check the corresponding plugin's documentation. 


 


- `initenv` does not create a version control repository for the specified path. If you wish to specify a default repository using optional the arguments to `initenv` you must create the repository first, otherwise you will see a message when initializing the environment: *Warning: couldn't index the default repository*.

- Non-ascii environment paths are not supported.

- [TracPlugins](trac-plugins) located in a [shared plugins folder](trac-ini#) that is defined in an [inherited configuration](trac-ini#global-configuration) are not loaded during creation, and hence, if they need to create extra tables for example, you'll need to [upgrade the environment](trac-upgrade#upgrade-the-trac-environment). Alternatively you can avoid the need to upgrade the environment by specifying a configuration file at the time the environment is created, using the `--config` option. See [TracAdmin\#FullCommandReference](trac-admin#full-command-reference) for more information.


**Caveat:** don't confuse the *Trac environment directory* with the *source code repository directory*.



This is a common beginners' mistake.
It happens that the structure for a Trac environment is loosely modeled after the Subversion repository directory structure, but those are two disjoint entities and they are not and *must not* be located at the same place.


## Database Connection Strings



You will need to specify a database connection string at the time the environment is created. The default is SQLite, which is probably sufficient for most projects. The SQLite database file is stored in the environment directory, and can easily be [backed up](trac-backup) together with the rest of the environment.



Note that if the username or password of the connection string (if applicable) contains the `:`, `/` or `@` characters, they need to be URL encoded.


### SQLite Connection String



The connection string for an SQLite database is:


```wiki
sqlite:db/trac.db
```


where `db/trac.db` is the path to the database file within the Trac environment.


### PostgreSQL Connection String



The connection string for PostgreSQL is a bit more complex. For example, to connect to a PostgreSQL database named `trac` on `localhost` for user `johndoe` and password `letmein`, use:


```wiki
postgres://johndoe:letmein@localhost/trac
```


If PostgreSQL is running on a non-standard port, for example 9342, use:


```wiki
postgres://johndoe:letmein@localhost:9342/trac
```


On UNIX, you might want to select a UNIX socket for the transport, either the default socket as defined by the PGHOST environment variable:


```wiki
postgres://user:password@/database
```


or a specific one:


```wiki
postgres://user:password@/database?host=/path/to/socket/dir
```


See the [
PostgreSQL documentation](http://www.postgresql.org/docs/) for detailed instructions on how to administer [
PostgreSQL](http://postgresql.org).
Generally, the following is sufficient to create a database user named `tracuser` and a database named `trac`:


```
$ createuser -U postgres -E -P tracuser
$ createdb -U postgres -O tracuser -E UTF8 trac
```


When running `createuser` you will be prompted for the password for the user 'tracuser'. This new user will not be a superuser, will not be allowed to create other databases and will not be allowed to create other roles. These privileges are not needed to run a Trac instance. If no password is desired for the user, simply remove the `-P` and `-E` options from the `createuser` command. Also note that the database should be created as UTF8. LATIN1 encoding causes errors, because of Trac's use of unicode. SQL\_ASCII also seems to work.



Under some default configurations (Debian), run the `createuser` and `createdb` scripts as the `postgres` user:


```
$ sudo su - postgres -c 'createuser -U postgres -S -D -R -E -P tracuser'
$ sudo su - postgres -c 'createdb -U postgres -O tracuser -E UTF8 trac'
```


Trac uses the `public` schema by default, but you can specify a different schema in the connection string:


```wiki
postgres://user:pass@server/database?schema=yourschemaname
```

### MySQL Connection String



The format of the MySQL connection string is similar to those for PostgreSQL, with the `postgres` scheme being replaced by `mysql`. For example, to connect to a MySQL database on `localhost` named `trac` for user `johndoe` with password `letmein`:


```wiki
mysql://johndoe:letmein@localhost:3306/trac
```

## Source Code Repository



A single environment can be connected to more than one repository. However, by default Trac is not connected to any source code repository, and the *Browse Source* navigation item will not be displayed.



There are many different ways to connect repositories to an environment, see [TracRepositoryAdmin](trac-repository-admin). A single repository can be specified when the environment is created by passing the optional arguments `repository_type` and `repository_dir` to the `initenv` command.


## Directory Structure



An environment consists of the following files and directories:


- `README` - Brief description of the environment.
- `VERSION` - Environment version identifier.
- `files`

  - `attachments` - Attachments to wiki pages and tickets.
- `conf`

  - `trac.ini` - Main configuration file. See [TracIni](trac-ini).
- `db`

  - `trac.db` - The SQLite database, if you are using SQLite.
- `htdocs` - Directory containing web resources, which can be referenced in Genshi templates using `/chrome/site/...` URLs.
- `log` - Default directory for log files, if `file` logging is enabled and a relative path is given.
- `plugins` - Environment-specific [plugins](trac-plugins).
- `templates` - Custom Genshi environment-specific templates.

  - `site.html` - Method to customize header, footer, and style, described in [TracInterfaceCustomization\#SiteAppearance](trac-interface-customization#site-appearance).

---



See also: [TracAdmin](trac-admin), [TracBackup](trac-backup), [TracIni](trac-ini), [TracGuide](trac-guide)


