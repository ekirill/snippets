# PG Deadlocks

  SELECT blocked_locks.pid     AS blocked_pid,
  blocked_activity.usename  AS blocked_user,
  blocking_locks.pid     AS blocking_pid,
  blocking_activity.usename AS blocking_user,
  blocked_activity.query    AS blocked_statement,
  blocking_activity.query   AS current_statement_in_blocking_process
  FROM  pg_catalog.pg_locks         blocked_locks
  JOIN pg_catalog.pg_stat_activity blocked_activity  ON blocked_activity.pid = blocked_locks.pid
  JOIN pg_catalog.pg_locks         blocking_locks 
  ON blocking_locks.locktype = blocked_locks.locktype
  AND blocking_locks.DATABASE IS NOT DISTINCT FROM blocked_locks.DATABASE
  AND blocking_locks.relation IS NOT DISTINCT FROM blocked_locks.relation
  AND blocking_locks.page IS NOT DISTINCT FROM blocked_locks.page
  AND blocking_locks.tuple IS NOT DISTINCT FROM blocked_locks.tuple
  AND blocking_locks.virtualxid IS NOT DISTINCT FROM blocked_locks.virtualxid
  AND blocking_locks.transactionid IS NOT DISTINCT FROM blocked_locks.transactionid
  AND blocking_locks.classid IS NOT DISTINCT FROM blocked_locks.classid
  AND blocking_locks.objid IS NOT DISTINCT FROM blocked_locks.objid
  AND blocking_locks.objsubid IS NOT DISTINCT FROM blocked_locks.objsubid
  AND blocking_locks.pid != blocked_locks.pid

  JOIN pg_catalog.pg_stat_activity blocking_activity ON blocking_activity.pid = blocking_locks.pid
  WHERE NOT blocked_locks.GRANTED;

# PG ForeignKeys

  SELECT 
  n1.nspname AS primary_key_ns, 
  c1.relname AS primary_key_table, 
  n2.nspname AS foreign_key_ns, 
  c2.relname AS foreign_key_table 
  FROM pg_catalog.pg_constraint c 
  JOIN ONLY pg_catalog.pg_class c1 ON c1.oid = c.confrelid 
  JOIN ONLY pg_catalog.pg_class c2 ON c2.oid = c.conrelid 
  JOIN ONLY pg_catalog.pg_namespace n1 ON n1.oid = c1.relnamespace 
  JOIN ONLY pg_catalog.pg_namespace n2 ON n2.oid = c2.relnamespace 
  WHERE c1.relkind = 'r' AND c.contype = 'f' 
  ORDER BY 1,2,3,4; 
