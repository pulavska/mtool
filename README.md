Migration Tool 0.3.1 manual (hereinafter mtool)

## Getting started

Before starting work with mtool, you need to configure it using the command:

```
php mtool config
```

Available options:

- database setup
  * adapter  - db adapter, can take values: "mysql" (default), "pgsql";
  * host     - database host, can take any values;
  * username - database user, can take any values;
  * password - database user password, can take any values;
  * dbname   - database name, can take any values;
  * charset  - encoding of the database connection, default value: "utf8" (the parameter is not used at the moment);
- configuring paths
  * path     - path for storing migration files and snapshots, can take any values, by default: "../migrations/";
- add. customization
  * table    - a table in the database that stores the names of applied migrations, can take any values, by default: "~ migrations".
  * versionRegexp - version regular expression, by default is 3 digits: "\d{3}".
## Beginning of work

To create a new migration, you need to call the command:

```
php mtool create XXX
```

where XXX is the numeric version number.

As a result, the directory specified in the settings will be created (if it was not created beforehand) and two migration files:

```
XXX_********_******_**_down.sql
XXX_********_******_**_up.sql
```

where `XXX_********_******_**` is the name of the migration corresponding to its version and creation time.

## Applying and canceling migrations

After the files were created and filled in step 2, the migration can be applied using the `upgrade` command:

```
php mtool upgrade
# or alias
php mtool up
```

> If the number of unapplied migrations is more than one, then calling the `php mtool upgrade` command will apply all unapplied migrations.

To cancel migration, use the `downgrade` command:

```
php mtool downgrade
# or alias
php mtool down
```

> If the number of applied migrations is more than one, then calling the command `php mtool downgrade` will undo only the last applied migration.

### Applying/undoing migrations by version

You can specify the `-v XXX` parameter for the commands to apply/cancel migrations, where XXX is the version number.
In this case, applying/cancellation of migrations will occur only within the framework of migrations for the current version.

> !!! Be careful if you cancel migrations of previous versions, and in the applied migrations of the next versions the same tables are used - then there may be conflicts on these tables in the future !!!

### Applying/undoing migrations by name
It is allowed to use the commands for applying/canceling migrations with the parameter (migration name like `XXX _ ******** _ ****** _ **`).

For example, there are such migrations:

```
  00000000_000000_10[+],
  00000000_000000_20[+],
  00000000_000000_30[-], 
  00000000_000000_40[-],
  00000000_000000_50[-];
```

> Note: "+" - applied migration, "-" - not applied migration

`php mtool upgrade` - migrations will be applied: 00000000_000000_30, 00000000_000000_40, 00000000_000000_50;

`php mtool upgrade 00000000_000000_40` - migrations will be applied: 00000000_000000_30, 00000000_000000_40;

`php mtool downgrade` - migrations will be canceled: 00000000_000000_20;

`php mtool downgrade 00000000_000000_10` - migrations will be canceled: 00000000_000000_20, 00000000_000000_10;


> !!! In this case, migrations will be applied and canceled all previous or subsequent, regardless of versions


### Applying one separate migration

For the command to apply migrations, you can specify the parameter `-u XXX _ ******** _ ****** _ **`, where `XXX _ ******** _ ****** _ ** `- number of a specific migration.
In this case, only this one migration will be applied, wherever it is.

## Getting information about current miracles

To get the name of the current migration, the command:

```
php mtool current
```

To get a list of migrations, the command:

```
php mtool list
```

this command displays a list of migrations highlighted in different colors.

The following colors are used:

* blue - migration is not applied and is available in the file system;
* green - migration has been applied and is available in the file system;
* red - migration has been applied, but not available in the file system.

## Create and apply snapshots

To create a snapshot of the current database state, the command:

```
php mtool snapshot
```
as a result of this command, a snapshot file will be created - `******** _ ****** _ ** _ snapshot.sql`, where` ******** _ ****** _ ** `- snapshot name corresponding to the time of its creation.

When creating snapshots, external applications are used: for mysql - mysqldump, for pgsql - pg_dump.

To use a snapshot, you must use the command:

```
php mtool deploy
```
a dialog will be called with a list of available snapshots and the ability to select one of them to use.

There are also such options for using this command:

```
php mtool deploy last
```
the last created snapshot will be applied

```
php mtool deploy ********_******_**
```
the selected snapshot will be applied

## Help

For a quick help (show all possible commands):

```
php mtool help
```
