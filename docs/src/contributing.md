# Contributing to Norm

Any contributions are welcome, be it pull requests, code reviews, documentation improvements, bug reports, or feature requests.


## Contributing through Code

If you decide to contribute through code, run the tests before and after you change the code.

The recommended way to run the tests is via [Docker](https://www.docker.com/) and [Docker Compose](https://docs.docker.com/compose/):

    $ docker-compose run --rm tests                     # run all test suites
    $ docker-compose run --rm test tests/tpostgres.nim  # run a single test suite

If you don't mind running two PostgreSQL servers on `postgres_1` and `postgres_2`, feel free to run the test suites natively:

    $ nimble test

Note that you only need the PostgreSQL servers to run the PostgreSQL backend tests, so:

    $ nim c -r tests/tsqlite.nim    # doesn't require PostgreSQL servers, but requires SQLite
    $ nim c -r tests/tobjutils.nim  # doesn't require anything at all

3.  Use camelCase instead of snake_case.

4.  New procs must have a documentation comment. If you modify an existing proc, update the comment.
