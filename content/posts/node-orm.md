+++
title = "Exploring migrations and data seeding with NodeJS ORMs"
date = 2020-04-30T10:24:31+07:00
tags = ["ppl", "nodejs", "orm"]
toc = true
comments = true
draft = true
description = """
Moving to NodeJS, Django's ORM is something I really miss.
So, I tried two popular ORMs for NodeJS and see if they provide
some of my favorite Django ORM features.
"""
images = ["/images/uploads/migration.png"]
+++

{{< figure src="/images/uploads/migration.png"
caption="A migration of beautiful creatures."
attr="(PNGWing)"
attrlink="https://www.pngwing.com/en/free-png-bxkog" >}}

If you've used Django, you most likely have used
[its powerful **ORM**][django-orm]. It's still the *best* ORM I've used to
date. Moving to NodeJS for my software engineering project course, Django's
ORM is something I really miss. So, I tried two popular ORMs for NodeJS:
[**TypeORM**][typeorm] and [**Sequelize**][sequelize].

Please note that this is a very opinionated post and may not reflect your
use cases. Also, I'm not going to delve that deep into the ORMs. I'm just
trying to review a little about their **migrations** and **data seeding**
features, two of my favorite Django ORM features.

## Configuration

TypeORM and Sequelize are provided through the [`typeorm`][npm-typeorm] and
[`sequelize`][npm-sequelize] packages, respectfully. Configuration is pretty
much similar for both ORMs. You can either use a **`.json`** or **`.js`** to
configure the settings like database credentials and backend.

## Model definition

I'm using CommonJS, and I must say that it wasn't very easy to get TypeORM
up and running. Most examples use the `class` and decorator-based entities
(models) and they benefit greatly from TypeScript. In CommonJS, defining
models is more verbose than it should be, especially if you want to apply
validation.

Sequelize is much friendlier for CommonJS users and model definition is
still pretty concise. Plus, it offers a soft delete feature, something
that [hasn't yet landed][typeorm-softdelete] on TypeORM.

## Migrations

Ah, this is the one that makes me love Django: **automatic migrations**. I know
that it isn't perfect, but it makes modifying your models much easier. Django's
`makemigrations` will automatically find differences in model definitions and
generate migrations for them.

TypeORM offers the [same feature][typeorm-auto-migrations], but it generates
ES6+ code and you'll have to edit it manually if you're using CommonJS.
**Not fun.**

Sequelize doesn't have this feature out-of-the-box. There are packages like
[`sequelize-auto-migrations`][sequelize-auto-migrations] and
[`sequelize-auto-migrations-ng`][sequelize-auto-migrations-ng] but they haven't
been maintained in a long time and I didn't have a good experience with them.

So, in the end, I still have to code my migrations **manually**. It's something
I'd rather not do.

Anyway, here's how an initial migration looks like with TypeORM in CommonJS:

```js
function CreateUserPoint1583323701804() {}

CreateUserPoint1583323701804.prototype.up = async queryRunner => {
  await queryRunner.createTable(
    (table = new Table({
      name: "user_point",
      columns: [
        {
          name: "username",
          isPrimary: true,
          type: "varchar"
        },
        {
          name: "point",
          type: "int"
        },
      ]
    })),
  );
};

CreateUserPoint1583323701804.prototype.down = async queryRunner => {
  await queryRunner.dropTable((table = "user_point"));
};

module.exports = CreateUserPoint1583323701804;
```

and with Sequelize:

```js
module.exports = {
  up: async (queryInterface, Sequelize) => {
    await queryInterface.createTable("user_point", {
      username: {
        allowNull: false,
        primaryKey: true,
        type: Sequelize.STRING
      },
      point: {
        allowNull: false,
        type: Sequelize.INTEGER
      },
    });
  },
  down: async (queryInterface, Sequelize) => {
    await queryInterface.dropTable("user_point");
  }
};
```

The **`up`** function defines the query that will be run if you apply the
migration (`typeorm migration:run` or `sequelize-cli db:migrate`). As
expected, the **`down`** function defines the query for a revert
(`typeorm migration:revert` or `sequelize-cli db:migrate:undo`). Migration
records are stored in the `typeorm_migrations` or `SequelizeMeta` table by
default.

I like Sequelize's more. However, I believe I would prefer TypeORM if I'm using
ES6+ or TypeScript (auto migrations!).

## Data seeding

**Data seeding** is useful if you're developing some features that rely on data.
You can use it to generate dummy data for testing or development purposes.
If your code depends on something that's stored in the database, you can
use data seeding to automatically insert it. Django provides the
[fixtures][django-fixtures] feature, which lets you dump and load data easily
and can be used to generate and load seeds.

TypeORM doesn't have this feature yet. There's the
[`typeorm-seeding`][typeorm-seeding] package, but again, it's not very useful if
you're using CommonJS. However, you can utilize migrations for data seeding.
Let's say I have an array of `UserProfile` objects, stored in a variable named
`UserPointSeeds`. I can do something like this:

```js
function SeedUserPoint1583323855110() {}

SeedUserPoint1583323855110.prototype.up = async queryRunner => {
  await getRepository(UserPoint).save(UserPointSeeds);
};

SeedUserPoint1583323855110.prototype.down = async queryRunner => {
  await getRepository(UserPoint).delete(UserPointSeeds);
};

module.exports = SeedUserPoint1583323855110;
```

Sequelize has this feature, but it's basically the same as migrations. So,
a Sequelize `seeder` looks something like this:

```js
module.exports = {
  up: (queryInterface, Sequelize) => {
    return queryInterface.bulkInsert("user_profile", UserPointSeeds);
  },
  down: (queryInterface, Sequelize) => {
    return queryInterface.bulkDelete("user_profile", {
      username: UserPointSeeds.map(s => s.username)
    });
  }
};
```

However, Sequelize has a seed generator which may be useful. You can generate
seeds using `sequelize-cli seed:generate` and it will automatically create a
`seeder` with the initial seed included.

Since seeds can be made as migrations, you can apply them using
`typeorm migration:run` with TypeORM. Sequelize has its own command for managing
seeds: `sequelize-cli db:seed` and `sequelize-cli db:seed:undo`.

Since both ORMs behave quite similarly, I don't think I prefer any of them.
Sequelize's seed generator is nice, but it's a bit limited. On the other hand,
`typeorm-seeding` looks like a useful library if you're using ES6+ or
TypeScript.

## Conclusion

TypeORM and Sequelize are **pretty good** ORMs. If you're using ES6+ or
TypeScript, I think **TypeORM** is the one you should look at. Otherwise, I
think you're better off with **Sequelize**. Still, Django's ORM is still the
most convenient for me.

[django-orm]: https://docs.djangoproject.com/en/3.0/topics/db/queries
[typeorm]: https://typeorm.io
[sequelize]: https://sequelize.org
[npm-typeorm]: https://www.npmjs.com/package/typeorm
[npm-sequelize]: https://www.npmjs.com/package/sequelize
[typeorm-softdelete]: https://github.com/typeorm/typeorm/issues/534
[typeorm-auto-migrations]: https://github.com/typeorm/typeorm/blob/master/docs/migrations.md#generating-migrations
[sequelize-auto-migrations]: https://github.com/flexxnn/sequelize-auto-migrations
[sequelize-auto-migrations-ng]: https://github.com/manuelvillar/sequelize-auto-migrations-ng
[django-fixtures]: https://docs.djangoproject.com/en/3.0/howto/initial-data
[typeorm-seeding]: https://github.com/w3tecch/typeorm-seeding
