# Norm: ORM for Nim Apps

[![Build Status](https://travis-ci.com/moigagoo/norm.svg?branch=develop)](https://travis-ci.com/moigagoo/norm)

[![Nimble](https://raw.githubusercontent.com/yglukhov/nimble-tag/master/nimble.png)](https://nimble.directory/pkg/norm)

**Norm** is an object-oriented, framework-agnostic ORM for Nim apps.

Norm:

- automatically converts data between Nim and SQL types
- generates DB schema from regular Nim object definitions
- provides procs to query and modify data in DB through Nim objects
- provides procs for writing migration scripts
- allows to wrap DB actions in automatically reversable transactions
- supports SQLite and PostgreSQL


## Installation

1.  Install Norm with [Nimble](https://github.com/nim-lang/nimble/):

        $ nimble install norm

2.  Add Norm to your .nimble file:

        requires "nim >= 1.0.0", "norm"


## Quickstart

```nim
import norm/sqlite                        # Import SQLite backend.
# import norm/postgres                    # Import PostgreSQL backend.
import logging                            # Import logging to inspect the generated SQL statements.
import unicode, sugar, options

db("petshop.db", "", "", ""):             # Set DB connection credentials.
  type                                    # Describe object model in an ordinary type section.
    User = object
      age: Positive                       # Nim types are automatically converted into SQL types
                                          # and back.
                                          # You can specify how types are converted using
                                          # ``parser``, ``formatter``, ``parseIt``,
                                          # and ``formatIt`` pragmas.
      name {.
        formatIt: ?capitalize(it)         # Enforce that ``name`` is stored in DB capitalized.
      .}: string
      ssn: Option[int]                    # ``Option`` fields are allowed to be NULL in DB.

addHandler newConsoleLogger()

withDb:                                   # Start a DB session.
  createTables(force=true)                # Create tables for objects. Drop tables if they exist.

  var bob = User(                         # Create a ``User`` instance as you normally would.
    age: 23,                              # Note that the instance is mutable. This is mandatory.
    name: "bob",
    ssn: some 456
  )
  bob.insert()                            # Insert ``bob`` into DB.
  dump bob.id                             # ``id`` attr is added by Norm and updated on insertion.

  var alice = User(age: 12, name: "alice", ssn: none int)
  alice.insert()

withCustomDb("mirror.db", "", "", ""):    # Override default DB credentials defined in ``db``.
  createTables(force=true)

withDb:
  let bobs = User.getMany(                # Read records from DB:
    100,                                  # - only the first 100 records
    cond="name LIKE 'Bob%' ORDER BY age"  # - find by condition
  )

  dump bobs

withDb:
  var bob = User.getOne(1)                # Fetch record from DB and store it as ``User`` instance.
  bob.age += 10                           # Change attr value.
  bob.update()                            # Update the record in DB.

  bob.delete()                            # Delete the record.
  dump bob.id                             # ``id`` is 0 for objects not stored in DB.

withDb:
  dropTables()                            # Drop all tables.
```


## Contributing

1.  Any contributions are welcome, be it pull requests, code reviews, documentation improvements, bug reports, or feature requests.

2.  If you decide to contribute through code, please run the tests after you change the code:

```shell
$ docker-compose run tests                        # run all tests in Docker
$ docker-compose run test tests/testpostgres.nim  # run a single test suite in Docker
$ nimble test                                     # run all tests natively;
                                                  # requires a running PostgreSQL server!
$ nim c -r tests/testsqlite.nim                   # run a single test suite natively
```

3.  Use camelCase instead of snake_case.

4.  New procs must have a documentation comment. If you modify an existing proc, update the comment.

[❤ Contributors ❤](https://github.com/moigagoo/norm/graphs/contributors)
