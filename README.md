# Tatter\Schemas
Database schema management, for CodeIgniter 4

## Quick Start

1. Install with Composer: `> composer require tatter/schemas`
2. Generate a new schema: `> php spark schemas`

## Features

* View your entire database mapped out in a cascading structure
* Read or detect table relationships for easy object-relation mapping (see e.g. [Tatter\Relations](https://github.com/tattersoftware/codeigniter4-relations))
* Get helpful advice on optimizations to your database structure with schema analysis<sup>1</sup>
* Backup, restore, or deploy an entire database structure between servers or environments<sup>1</sup>
* Generate CodeIgniter 4 migration files from an existing database<sup>1</sup>
* Transfer projects to CodeIgniter 4 by reading schema files from other supported formats<sup>1</sup>

<sup>1</sup> Some features are still in development. See **Handlers > Development** for
planned future expansion.

## Installation

Install easily via Composer to take advantage of CodeIgniter 4's autoloading capabilities
and always be up-to-date:
* `> composer require tatter/schemas`

Or, install manually by downloading the source files and adding the directory to
`app/Config/Autoload.php`.

## Configuration (optional)

The library's default behavior can be altered by extending its config file. Copy
**bin/Schemas.php** to **app/Config/** and follow the instructions
in the comments. If no config file is found in **app/Config** the library will use its own.

## Usage

**Schemas** works on an import/export model, allowing dynamic loading and storing of schemas
from a variety of handlers. The **Schemas** service is also available to simplify a workflow
with convenient wrapper functions.

Here is an example of one common process, mapping the default database group and storing
the resulting schema to the cache:

```
// Map the database and store the schema in cache
$schemas = service('schemas');
$this->schemas->import('database')->export('cache');

// Load the schema from cache, add Model data, and get the updated schema
$schema = $this->schemas->import(['cache', 'model'])->get();
```

If you need to deviate from the default configuration you can inject the handlers yourself:
```
$db = db_connect('alternate_database');
$databaseHandler = new \Tatter\Schemas\Handlers\DatabaseHandler(null, $db);
$schema = $this->schemas->import($databaseHandler)->get();
```

## Command

**Schemas** comes with a `spark` command for convenient schema generation and display. Use
`php spark schemas [import_handler ...]` to test and troubleshoot, or add it to your cron
for periodic schema caching:
```
php spark schemas database model -export cache
```

## Autodetection

**Schemas** uses foreign keys, indexes, and naming convention to detect relationships
automatically. Please make sure your database is setup using the appropriate keys and
foreign keys to assist with the detection. Naming conventions follow the format of
`{table}_id` for foreign keys and `{table1}_{table2}` for pivot tables. For more examples
on relationship naming conventions consult the Rails Guide
[Active Record Associations](https://guides.rubyonrails.org/association_basics.html#the-types-of-associations)
(please excuse the Ruby reference).

### Intervention

Should autodetection fail or should you need to deviate from conventions there are a few
tools you can use to overwrite or augment the generated Schema.

* **Config/Schemas**: the Config file includes a variable for `$ignoredTables` that will let you skip tables entirely. By default this includes the framework's `migrations` table.
* **app/Schemas/{file}.php**: The `FileHandler` will load any schemas detected in your **Schemas** directory - this gives you a chance to specify anything you want. See [tests/_support/Schemas/Good/Products.php](tests/_support/Schemas/Good/Products.php) for an example.

## Handlers

Current supported handlers:
* Database (import only)
* Model (import only)
* Cache
* FileHandler (PHP import only)

### Development

The eventual goal is to support mapping from and deploying to any source. Planned handler
implementations include:

* `DatabaseHandler->export()`: Recreate a live database from its schema
* `MigrationsHandler`: Create a schema from migration files, or vice versa
* `FileHandler`: A wrapper for importing and exporting from popular schema file formats
* More to come...

And the file-specific handlers:
* `PhpHandler->export()`: Create a PHP file with a Schema object in `$schema`
* `XmlHandler`: Support for Doctrine-style XML files
* More to come...

Want to help out? All code and issues are managed on GitHub at [Tatter\Schemas](https://github.com/tattersoftware/codeigniter4-schemas)
