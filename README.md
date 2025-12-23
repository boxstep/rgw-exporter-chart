# Ceph RADOSGW Usage Exporter Helm Chart

Based on: https://github.com/blemmenes/radosgw_usage_exporter but can be used with other radosgw images since they all work the same way.

## Requirements

- Working Ceph Cluster with Object Gateways setup.
- Ceph RADOSGWs must beconfigured to gather usage information as this is not on by default. The
  miniumum is to enable it via `ceph.conf` as below. There are however other options that are
  available and should be considered [here](http://docs.ceph.com/docs/master/radosgw/config-ref/).
  If you don't configure thresholds, intervals, and shards you may end up having too large objects
  in the usage namespace of the log pool. The values below are just examples. Check the
  documentation which ones would be the best ones for your setup.

```
rgw enable usage log = true
rgw usage log flush threshold = 1024
rgw usage log tick interval = 30
rgw usage max shards = 32
rgw usage max user shards = 8

```

 Configure admin entry point (default is 'admin'):

```
rgw admin entry = "admin"
```

- Enable admin API (default is enabled):

```
rgw enable apis = "s3, admin"
```

- This exporter requires a user that has the following capability, see the Admin Guide
  [here](http://docs.ceph.com/docs/master/radosgw/admin/#add-remove-admin-capabilities) for more
  details.

```
    "caps": [
        {
            "type": "buckets",
            "perm": "read"
        },
        {
            "type": "metadata",
            "perm": "read"
        },
        {
            "type": "usage",
            "perm": "read"
        },
        {
            "type": "users",
            "perm": "read"
        }
```

## Config reference. Chart uses environment variables from secret or configmap. Arguments are not customizable, except --insecure flag.

| _Arg_              | _Env_            | _Description_                                                           | _Default_           |
| ------------------ | ---------------- | ----------------------------------------------------------------------- | ------------------- |
| `-H --host`        | `RADOSGW_SERVER` | Server URL for the RADOSGW api (example: http://objects.dreamhost.com/) | `http://radosgw:80` |
| `-e --admin-entry` | `ADMIN_ENTRY`    | The entry point for an admin request URL                                | `admin`             |
| `-a --access-key`  | `ACCESS_KEY`     | S3 access key                                                           | `NA`                |
| `-s --secret-key`  | `SECRET_KEY`     | S3 secret key                                                           | `NA`                |
| `-k --insecure`    |                  | Allow insecure server connections when using SSL                        | `false`             |
| `-p --port`        | VIRTUAL_PORT     | Port to listen                                                          | `9242`              |
| `-S --store`       | STORE            | Store name added to metrics                                             | `us-east-1`         |
| `-t --timeout`     | TIMEOUT          | Timeout when getting metrics                                            | `60`                |
| `-l --log-level`   | LOG_LEVEL        | Provide logging level: DEBUG, INFO, WARNING, ERROR or CRITICAL          | `INFO`              |
| `-T --tag-list`    | TAG_LIST         | Add bucket tags as label (example: 'tag1,tag2,tag3')                    | ``                  |
