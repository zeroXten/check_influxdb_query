check\_influxdb\_query
====================

Plugin to check a query against InfluxDB

Usage
=====

    $ ./check_influxdb_query.rb -h
    Usage: ./check_influxdb_query.rb (options)
        -c, --critical CRITICAL          Critical threshold expression. E.g. 'value >= 20'. See https://github.com/rubysolo/dentaku
        -d, --database DATABASE          InfluxDB database name
        -H, --host HOST                  InfluxDB host
        -j, --jsonpath JSONPATH          Json path to select value. Takes the first value, or 0 if none. See http://goessner.net/articles/JsonPath/
        -n, --noresult                   Go critical for no result from query
        -p, --password PASSWORD          API password
        -P, --port PORT                  InfluxDB port
        -q, --query QUERY                Query to run. See http://influxdb.com/docs/v0.8/api/query_language.html (required)
        -u, --username USERNAME          API username
        -w, --warning WARNING            Warning threshold expression. E.g. 'value >= 10'. See https://github.com/rubysolo/dentaku
        -h, --help                       Show this message

Examples
========

Debug mode that shows query output

    $ ./check_influxdb_query.rb -q "SELECT x from test where time > now() - 1h"
    Debug output. Use -j to check value...
    {
      "test": [
        {
          "time": 1416843740,
          "sequence_number": 30001,
          "x": 10
        }
      ]
    }
    CheckInfluxdbQuery WARNING: Check did not exit! You should call an exit code method.

Select the latest x value

    $ ./check_influxdb_query.rb -q "SELECT x from test where time > now() - 1h limit 1" -j '$.test..x'
    CheckInfluxdbQuery OK: Value '10' ok for query 'SELECT x from test where time > now() - 1h limit 1'

Add a warning threshold

    $ ./check_influxdb_query.rb -q "SELECT x from test where time > now() - 1h limit 1" -j '$.test..x' -w 'value >= 5'
    CheckInfluxdbQuery WARNING: Value '10' matched 'value >= 5' for query 'SELECT x from test where time > now() - 1h limit 1'

Add a critical threshold

    $ ./check_influxdb_query.rb -q "SELECT x from test where time > now() - 1h limit 1" -j '$.test..x' -w 'value >= 5' -c 'value >= 7'
    CheckInfluxdbQuery CRITICAL: Value '10' matched 'value >= 7' for query 'SELECT x from test where time > now() - 1h limit 1'

Go critical on no results

    $ ./check_influxdb_query.rb -q "SELECT x from test where time > now() - 5m limit 1" -j '$.test..x' --noresult
    CheckInfluxdbQuery CRITICAL: No result for query 'SELECT x from test where time > now() - 5m limit 1'

References
==========

* http://influxdb.com/
* http://influxdb.com/docs/v0.8/api/query\_language.html
* https://github.com/joshbuddy/jsonpath
* https://github.com/rubysolo/dentaku
