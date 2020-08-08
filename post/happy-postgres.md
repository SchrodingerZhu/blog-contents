I would like to summarize some highlights of postgresql usage in my own blog system.

## Random Draw

It suddenly occurs to me that I would like to add a lucky draw function to my CMS. However, due to the data structure implementation, how to random select a row in a table is a problem.

Of couse, this is **not a big issue** for my situation of a blog system, but with the spirit of exploration, I would like to investigate some possible solutions and check their effiencency and randomization. 

I see some stackoverflow answers suggesting a serial primary key like `id` can be useful; however. none of these answers takes an importact fact into consideration that sometimes items can be deleted and leave a hole among the numbers; no mentioning that if an insertion conflict happens, a serial key will continue to increase and make the primary `id` even more scatterred. 

Another issue to consider is that some solution may not be random enough; this will affect the final effect if you really want to have a food randomness.

Therefore, I made up and tried several solutions and compared them. I will only analysis the result. If you want to grab more details, add `EXPLAIN ANALYZE` before each query to let the DB collect more information.

**Caution: those methods with fallbacks only generate seemingly random results, which means you should not use them in those serious lucky draw system**.

Let us first create some fake rows (this takes about 20 seconds on my PC):

```plsql
CREATE TABLE analysis AS
  SELECT
    generate_series(1,10000000) AS n,
    substr(concat(md5(random()::text), md5(random()::text)), 1, (random() * 64)::integer + 1) AS description;

VACUUM ANALYZE; -- cleanup before analysis
```

First, a trivial solution is the following:

```plsql
select n from analysis order by random() limit 1;
```

Unfortunately, this performs really poor on large data set (takes $1.5$s to finish one query). As **Darkflame** later explained to me, this kind of `order by` will iterate through the whole table and hence results in a bad performance.

Then how about using offset?

```plsql
select n from analysis offset
    floor(random() * (SELECT count(*) from analysis))
    limit 1;
```

The above code select a random row by shifting a random offset. It still takes about $1.1$ seconds to finish one query. The largest cost comes from the subquery `SELECT count(*) from analysis`, which takes about $0.6\sim0.7$ seconds. Fortunately, there is a way to estimate the count of a table:

```plsql
SELECT reltuples FROM pg_class where relname = 'analysis'
```

Notice that **this value can be slightly larger than the number of columns in the table, which means you may need to fallback to a default value in some extreme situations**.

```plsql
select id from posts offset
    floor(random() * (
        SELECT reltuples
        FROM pg_class where relname = 'posts'))
    limit 1;
```

Using the estimated count, I successfully futher reduced the time cost to $0.4\sim 0.5$s. The random effect is also okay both on large tables and smaller ones.

Since version 9.5, postgresql imported the `TABLESAMPLE` function, which makes it easier to take random samples from a table. There are two random methods:

- `system`: take random samples from system blocks, very fast, not random enough.
- `beronoulli`: direct randomize the rows, slower but good in random outcomes. (the document says that this method will go through all rows but it performs better than the `order by` operation)

However, these two operations are based on percentage rather than the numbers, which means we still need to estimate the total number and give fallbacks if nothing returns.

A very direct application is to use `system(percentage)` to cut small the whole set and them apply the `order by random`:

```plsql
select n from analysis TABLESAMPLE system(20) order by random() limit 1;
```

This only takes $373$ ms per operation, but even by doing `random()` again, the result is not satisfactory especially when the table size if small.

Of couse, one can use the estimated count to calculate the percentage but you may scale it with a factor $X$ otherwise it is highly possible to return an empty result:

```plsql
select n from analysis TABLESAMPLE system(
        X * 100 / (SELECT reltuples
        FROM pg_class where relname = 'posts')
    ) limit 1;
```

It is also possible to retry the sampling using a increasing gradient of percentage. Though it sounds stupid, it actually runs brazely fast. In our case, even when $X = 500$, the query can still be finished in about $80$ ms.

There are also some extensions like `tsm_system_rows` and `tsm_system_time`, which provide facilities for us the achieve the above functions within one line (`select n from analysis TABLESAMPLE system_rows(1)`). Again, the randomness is poor (especially for small tables) while the performance is cool.

If the randomness is a great requirement, `bernoulli` may become a better choice:

```plsql
select n from analysis TABLESAMPLE bernoulli(
        133 / (SELECT reltuples
        FROM pg_class where relname = 'analysis')
    ) limit 1;
```

That $133$ is just a magic number tested by me, which makes the empty result appears at a bearable rate. The query time cost varies from $60$ ms to $300$ ms but the randomness is good.

For small tables, you may feel a draw back as the columns seem to be collected in a particular order. A possible workaround is the following

```plsql
SELECT * from (SELECT your_columns
               FROM you_table TABLESAMPLE bernoulli(
                   133 / (SELECT reltuples FROM pg_class where relname = 'your_table'))
               limit 5
              ) as result order by random() limit 1
```

but this makes the query time increase to $200$ ms to $300$ ms with our fake table. It should be fairly good if the table is of a small size).

## Pagination

Another topic I want to talk about briefly is the pagination. In this blog system, I used something like:

```plsql
select * from posts order by id limit page_limit offset page_limit * page_numer;
```

to run the pagination. This is already good enough since our table cannot be too large. What's more, this requires no extra state on the server side.

However, if you want to scroll through the table with a sliding window, you can do the following things:

```plsql
BEGIN;
DECLARE cur CURSOR FOR SELECT * FROM table;
FETCH 10 FROM cur;
-- ...
FETCH 10 FROM cur;
COMMIT;
```

The requires transaction and should only be used as intranal visiting tool.

## Text Search

Postgresql also provides a handy function for us to implement in-text search feature.

Start it with a migration like:

- **up**:

  ```plsql
  -- Your SQL goes here
  
  ALTER TABLE posts
      ADD COLUMN text_searchable tsvector NOT NULL;
  
  UPDATE posts
  SET text_searchable =
          to_tsvector('english', title || ' ' || content);
  
  CREATE INDEX textsearch_idx ON posts USING GIN (text_searchable);
  
  CREATE TRIGGER tsvectorupdateproducts
      BEFORE INSERT OR UPDATE
      ON posts
      FOR EACH ROW
  EXECUTE PROCEDURE
      tsvector_update_trigger(text_searchable, 'pg_catalog.english', title, content);
  ```

- **down (undo up)**:

  ```plsql
  ALTER TABLE posts DROP COLUMN text_searchable;
  DROP TRIGGER tsvectorupdateproducts ON posts;
  ```

The **up** part create a search index using a `ts_vector`, where the latter is constructed based on the words in title and content (`||` is the concat operator). It also creates a trigger so that later update is done automatically.

With our `ts_vector`s ready, we can initiate a search with a `where` clause like:

```plsql
  WHERE (
    ((seach_index) @@ (to_tsquery('english', seach_text)))
  )
```

In this blog, the search feature is implemented by a `diesel` extension.



