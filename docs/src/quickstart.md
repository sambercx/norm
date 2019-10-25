# Quickstart

## Installation

1.  Install Norm with [Nimble](https://github.com/nim-lang/nimble/):

        $ nimble install norm

2.  Add Norm to your .nimble file:

        requires "nim >= 1.0.0", "norm"


## Your First Model

Let's create a schema for a pet shop.

It'll have two entities: owners and pets. The relation is one-to-many: one owner can have many pets, but one pet can have only one owner.

Also, there can be owners without pets and pets without owners (😿).

1.  In your project source directory, create `models.nim` with the following content:

        import options

        type
          Owner = object
            name: string
            age: Natural

          Pet = object
            name: string
            owner: Option[Owner]

2.  Now, to map this model to an actual DB, wrap it in `db` macro imported from `norm/sqlite` or `norm/postgres`:

        import options
        import norm/sqlite

        db("petshop.db", "", "", ""):
          type
            Owner = object
              name: string
              age: Natural

            Pet = object
              name: string
              owner: Option[Owner]

    Norm automatically handles basic Nim types like `string` and `int`, so `name` and `age` fields will be transalted to DB table columns automatically.

    However, you have to tell Nim how to handle your custom types like `Owner`. You must tell Norm how to store `Owner` in the DB and how to convert those stored values back to `Owner`.
