# Database

## Use a structure.sql instead of schema.rb

By default, Rails will dump the database structure in a file called `schema.rb` every time you run your migrations. This `schema.rb` is like one huge migration that is executed when restoring your database so that you don't have to run through every migration you ever ran, and it's the single source of truth for your database schema. `schema.rb` is a Ruby file so it's portable between databases - regardless of whether you are running MySQL or Postgres or sqlite3, you will get a similar result.

However, using `schema.rb` prevents you from using cool features that your database of choice \([Postgres](../postgres.md)\) has. \(One such example is native support for enums\). If you create a migration that uses a database-specific structure and try to dump it to `schema.rb`, Rails will **silently** complain about what you've done, and next time you reset your database you will be missing something, something probably crucial.

{% hint style="info" %}
Obviously, this locks you into using the RDBMS of your choice. But since you are using Postgres, why would you ever want to use something else?
{% endhint %}

To alleviate that, you can lock yourself into the current database engine and dump out `structure.sql` which is a database-specific set of SQL instructions. Just add this to your `config/application.rb`:

{% tabs %}
{% tab title="config/application.rb" %}
```ruby
module YourRailsApplication
  class Application < Rails::Application
    # ... the rest of your settings
    config.active_record.schema_format = :sql 
  end
end
```
{% endtab %}
{% endtabs %}

Voila! Next time you run your migrations, your `schema.rb` will be replaced with a nifty `structure.sql`.

{% hint style="warning" %}
If you are running your application in a Docker container and need to run migrations on it for some reason, keep in mind that Rails will error out unless you also package the database dumping tool for your database \(e.g. `pg_dump`\) alongside your app. 
{% endhint %}

## Referential integrity and validation

Unlike Postgres, Rails is not concerned with the integrity and consistency of your database. Rails will happily spew `null` values everywhere and skip over your callbacks if you confuse `delete` and `destroy`. You can use the amusingly named [yeet\_dba gem](https://github.com/kevincolemaninc/yeet_dba) to automatically find missing indices and foreign constraints, but you should also know how to do it manually.

For any relationship between models, you should have a foreign key in your migration. This is very easy to accomplish when creating a migration:

```ruby
class CreatePosts < ActiveRecord::Migration[6.0]
  def change
    create_table :posts do |t|
      t.string :title, null: false
      t.text :contents, null: false
      t.references :user, null: false, foreign_key: true, dependent: :delete
      t.timestamps
    end
  end
end
```

In this example, we're creating a `posts` table that references the `id` field on `user` table via the `user_id` field on the `posts` table. The `dependent: :delete` part means we want our posts to be deleted if a user is also deleted, thus preventing us from having posts whose author is `nil`. Since it's enforced by the database, there is no way to have a post assigned to a user that does not exist.

Another useful thing that you might have noticed is the `null: false` part. This ensures that a value must exist when creating a field, thus enforcing a `validates :field, presence: true` instruction on a model. You should also enforce your `uniqueness` constraints by adding a _unique index_ on a field \(or a combination of fields, if you want scoped uniqueness constraints!\)

```ruby
class Post < ApplicationRecord
  validates :title, uniqueness: true
end

class EnforceUniqueTitles < ActiveRecord::Migration[6.0]
  def change
    add_index :posts, :title, unique: true
  end
end
```

If you're really hardcore, you can also use [Postgres CHECK constraints](http://www.postgresqltutorial.com/postgresql-check-constraint/) for other basic validation, thus ensuring the value of a field is what you'd expect.

Read more about this on the Rails guide:

{% embed url="https://guides.rubyonrails.org/active\_record\_migrations.html\#foreign-keys." %}

## Date, time, datetime, timestamp, timedate, oh my!

Everything you need to know about all of those things is in this table. Types with an asterisk \(\*\) are Rails specific.

<table>
  <thead>
    <tr>
      <th style="text-align:left">Ruby / Rails class</th>
      <th style="text-align:left">Migration</th>
      <th style="text-align:left">Postgres Equivalent</th>
      <th style="text-align:left">What it does</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">Date</td>
      <td style="text-align:left">:date</td>
      <td style="text-align:left"><code>date</code>
      </td>
      <td style="text-align:left">
        <p>It&apos;s a date, without time.</p>
        <p>Example: <code>2019-01-01</code>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">Time</td>
      <td style="text-align:left">:datetime</td>
      <td style="text-align:left"><code>timestamp</code>
      </td>
      <td style="text-align:left">
        <p>Amusingly, <code>Time</code> is a date with a time.</p>
        <p>Example: <code>2019-01-01 00:00:00</code>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">TimeWithZone*</td>
      <td style="text-align:left">:datetime</td>
      <td style="text-align:left"><code>timestamp with time zone</code>
      </td>
      <td style="text-align:left">Rails automatically decides to convert datetime formats to a format with
        a time zone.</td>
    </tr>
    <tr>
      <td style="text-align:left">DateTime</td>
      <td style="text-align:left"><em>none</em>
      </td>
      <td style="text-align:left"><em>none</em>
      </td>
      <td style="text-align:left">It&apos;s like Time, but with better date arithmetic and less precision.</td>
    </tr>
    <tr>
      <td style="text-align:left">Duration*</td>
      <td style="text-align:left"><em>none</em>
      </td>
      <td style="text-align:left">interval</td>
      <td style="text-align:left">If you want to express abstract times like <code>3 hours</code>, this is
        the type for you.</td>
    </tr>
  </tbody>
</table>