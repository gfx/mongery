# Mongery

Mongery translates MongoDB query to Arel AST for Postgres with JSON columns, ready to use with ActiveRecord connection.

This could be useful in situations where you migrate off of MongoDB backend to Postgres without touching the application code, or to provide MongoDB query syntax as a JSON API to your Postgres backend.

## Limitation

Currently, Mongery supports the limited set of queries to use with PostgreSQL 9.3. Most query on JSON data will end up with the full table scan. Index using hstore columns and/or 9.4 `jsonb` types are planned but not implemented.

See the spec file in `spec` directory for the supported conversion.

## Usage

Create a table with the following structure. The name of the table could be anything.

```
CREATE TABLE objects (
  id varchar(32) not null primary key,
  data json not null,
  updated_at timestamp DEFAULT current_timestamp
)
```

Currently Mongery assumes the `id` values are stored as `_id` key duplicated in the json data as well. This can be customized in the future updates.

```ruby
builder = Mongery::Builder.new(:objects)

builder.find({ _id: 'abcd' }).limit(1).to_sql
# => SELECT data FROM objects WHERE id = 'abcd' LIMIT 1

builder.find({ age: {"$gte" => 21 } }).sort({ name: -1 }).to_sql
# => SELECT data FROM objects WHERE (data->>'age')::integer >= 21 ORDER BY data->>'name' DESC
```

## License

MIT

## Copyright

Tatsuhiko Miyagawa

