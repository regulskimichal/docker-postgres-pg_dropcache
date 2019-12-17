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
