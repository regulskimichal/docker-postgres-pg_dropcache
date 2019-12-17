# PostgreSQL 12.1 with pg_dropcache  
This repository contains Docker image of Postgres from [official Docker repository](https://github.com/docker-library/postgres) with a [precompiled extension](https://github.com/zilder/pg_dropcache) which allows to flush a database buffers.

## Motivation
By default, in PostgreSQL there is no built-in method to flush buffers. This image delivers a PostgreSQL with built-in extension which allows to flush a database buffers.

## Usage
### [Enabling extension](https://github.com/zilder/pg_dropcache):
```
CREATE EXTENSION pg_dropcache;
```

### [Flushing all data to disk](https://www.postgresql.org/docs/12/sql-checkpoint.html) - an important step before flushing buffers. Otherwise, data can be lost:
```
CHECKPOINT;
```

### [Flushing buffers](https://github.com/zilder/pg_dropcache):
```
SELECT pg_dropcache();
```

## Appendix
To check buffer state enable extension which is delivered with database by default:
```
CREATE EXTENSION pg_buffercache;
```

Print buffers:
```
SELECT c.relname,
       count(*)                                                     AS buffers,
       pg_size_pretty(count(*) * 8192)                              AS size,
       pg_size_pretty(pg_relation_size(c.oid))                         "rel size",
       pg_size_pretty(sum(count(*)) OVER () * 8192)                    "cache size",
       to_char(count(*) / (sum(count(*)) OVER ()) * 100, '990.99%') AS "cache %",
       to_char(count(*)::DOUBLE PRECISION / (SELECT setting::BIGINT
                                             FROM pg_settings
                                             WHERE name = 'shared_buffers') * 100,
               '990.99%')                                           AS "shared buffers %",
       to_char(CASE pg_relation_size(c.oid)
                   WHEN 0 THEN 100
                   ELSE (count(*) * 8192 * 100) / pg_relation_size(c.oid)::FLOAT
                   END, '990.99%')                                  AS "rel %"
FROM pg_class c
         INNER JOIN pg_buffercache b ON b.relfilenode = c.relfilenode
         INNER JOIN pg_database d ON (b.reldatabase = d.oid AND d.datname = current_database())
GROUP BY c.relname, c.oid
ORDER BY 2 DESC;
```
