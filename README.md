# Haproxy testing

This is a simple test of HAPROXY with 2 backend servers.

THe config for Haproxy is in `haproxy/haproxy.cfg` and the config for syslog-ng
is in `syslog-ng/syslog-ng.conf`. rsyslog is included in the container to log
haproxy stdout.

# Running

TO run the docker-compose cluster, run:
```
./start
```

# Logs

TO view the logs :

```
./logs
```

Will tail the docker-compose logs.

# Modifying haproxy

This is mostly for testing HAProxy rules. You can also use this as a model to
spin up additional containers for testing.

to reload the config you can either

- Signal haproxy to reload :

```
./reload_haproxy
```

- Restart the full container:

```
./restart_haproxy
```

# Demonstration

The current configutration will route based on URL.

If you :
```
curl http://127.0.0.1:60.xip.io:60
```
you will see a log entry from `production`

```
haproxy_1     | 2019-04-26T18:14:39+00:00 localhost haproxy[78]: {type:haproxy,timestamp:9,http_status:200,http_request:GET / HTTP/1.1,remote_addr:172.24.0.1,bytes_read:9832,upstream_addr:172.24.0.2,backend_name:PRODUCTION,retries:0,bytes_uploaded:460,upstream_response_time:3,upstream_connect_time:0,session_duration:3,termination_state:--}
haproxy_1     | 2019-04-26T18:14:39+00:00 localhost haproxy[78]: {type:haproxy,timestamp:9,http_status:200,http_request:GET / HTTP/1.1,remote_addr:172.24.0.1,bytes_read:9832,upstream_addr:172.24.0.2,backend_name:PRODUCTION,retries:0,bytes_uploaded:460,upstream_response_time:3,upstream_connect_time:0,session_duration:3,termination_state:--}
production_1  | 172.24.0.4 [26/Apr/2019:18:14:39 +0000] GET /spec.json HTTP/1.1 200 Host: 127.0.0.1.xip.io:60}
```

If you :

```
curl http://test.127.0.0.1.xip.io:60
```

you will see a log entry from `canary`

```
haproxy_1     | 2019-04-26T18:15:21+00:00 localhost haproxy[78]: {type:haproxy,timestamp:0,http_status:400,http_request:<BADREQ>,remote_addr:172.24.0.1,bytes_read:187,upstream_addr:-,backend_name:REFLEX_FE_80,retries:0,bytes_uploaded:0,upstream_response_time:-1,upstream_connect_time:-1,session_duration:10369,termination_state:CR}
haproxy_1     | 2019-04-26T18:15:21+00:00 localhost haproxy[78]: {type:haproxy,timestamp:0,http_status:400,http_request:<BADREQ>,remote_addr:172.24.0.1,bytes_read:187,upstream_addr:-,backend_name:REFLEX_FE_80,retries:0,bytes_uploaded:0,upstream_response_time:-1,upstream_connect_time:-1,session_duration:10369,termination_state:CR}
canary_1      | 172.24.0.4 [26/Apr/2019:18:15:21 +0000] GET / HTTP/1.1 200 Host: test.127.0.0.1.xip.io:60}
```
