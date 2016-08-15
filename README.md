# monitoring-env

- shinken: a bare (no retention, no logs) Shinken instance containing a simple configuration (check_dummy).
  This only exposes port 50000 (livestatus).

	`docker run -p 50000:50000 ofosos/thruk-perf-shinken-bare`

