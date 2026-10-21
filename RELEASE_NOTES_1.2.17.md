# TG Monitor 1.2.17

On large attachment queues, orphan cleanup could scan the entire queue every
five seconds and hold the database writer for up to 20 seconds. Cleanup now
seeks through the existing account index and removes at most 100 orphaned
receipts per pass; valid accounts' receipts are preserved.

Message and history commits receive priority. Media and maintenance receive
regular background slots, preserving progress under continuous message traffic.
Transactions still run one at a time on the dedicated writer, with FULL sync,
rollback and cancellation draining. A running transaction is never interrupted
to promote another job.

Database schema and frontend are unchanged. The updater preserves the existing
database, sessions, settings and media, verifies a consistent backup before
switching from 1.2.16, and checks archive integrity and message counts afterward.

Validation: 200,000-receipt mixed-load and bounded SQL-work regressions;
priority fairness, FIFO, cancellation and atomic checkpoint tests; native EXE
startup, receipt recovery, shutdown persistence and duplicate-instance checks.
