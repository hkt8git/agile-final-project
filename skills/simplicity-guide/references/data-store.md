# Data store

The ladder for where your data lives. Start at files; climb only when a stated
requirement forces it.

## flat-files

Plain files on disk (JSON, CSV, Markdown).

- **Use when:** data is small, read-mostly, and single-process; you can hold the
  whole dataset in memory; no concurrent writers.
- **Climb when:** you need concurrent writes, queries across records, or
  transactional integrity.
- **Cost:** none beyond the filesystem. No schema, no migrations, no server.
- **Rationale template:** `"chose flat files — {small read-mostly data},
  {single process}, {no query needs}"`

## sqlite

A single-file embedded relational database.

- **Use when:** you need queries/indexes/transactions but have a single writer
  (or low write concurrency) and the dataset fits on one box.
- **Climb when:** you need many concurrent writers, network access from multiple
  hosts, or horizontal scale.
- **Cost:** a SQL schema and migrations to own; still zero ops (no server).
- **Rationale template:** `"chose SQLite — {single writer}, {<Nk rows},
  {you wanted zero ops}"`

## postgres

A networked relational database server.

- **Use when:** multiple hosts/processes write concurrently, you need it as a
  shared service, or scale exceeds a single file.
- **Climb when:** (top of this ladder) — sharding/replication is a separate,
  later decision driven by measured load.
- **Cost:** a server to run, back up, secure, and upgrade. Real ops burden —
  check it against the maintainer ceiling.
- **Rationale template:** `"chose Postgres — {N concurrent writers},
  {shared service}, {scale beyond one box}"`
