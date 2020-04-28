# Splunk Command NewRelic NRQL
The [NewRelic NRQL](https://docs.newrelic.com/docs/query-data/nrql-new-relic-query-language/getting-started/introduction-nrql) Command for Splunk app contains the Splunk Generating Command `nrql` which queries New Relic and generates Splunk reports from the query results returned.

## Syntax:
```
| nrql connection=<string> query=<string> (output=_raw)?
```

## Installation:
* Clone or download this repository
* Copy `newrelic_nrql_command_for_splunk` directory to `$SPLUNK_HOME/etc/apps/`

## Configuration:
* [Create a NewRelic Insights API Query Key](https://docs.newrelic.com/docs/insights/insights-api/get-data/query-insights-event-data-api)
* Create a local configuration file `$SPLUNK_HOME/etc/apps/newrelic_nrql_command_for_splunk/local/nrql_connections.conf`
* Add NewRelic Endpoint, Account ID and Insights API Query Key details in `nrql_connections.conf`
* nrql_connections.conf.spec:
```
[<name>]
apiEndpoint = <string>
accountId = <string>
queryKey = <string>
```
* nrql_connections.conf.example:
```
[prod-us-account]
apiEndpoint = insights-api.newrelic.com
accountId = 1111111
queryKey = y0ur_ins1ghts_qu3ry_k3y_h3re

[test-eu-account]
apiEndpoint = insights-api.eu.newrelic.com
accountId = 1234567
queryKey = an0ther_ins1ghts_qu3ry_k3y_h3re
```

## Usage:
* The `nrql` command is a generating command and should be used as the first command in the search.
* The `connection` argument is the name of a configured New Relic connection in `nrql_connections.conf`.
* The `query` argument is a valid NRQL query.
* The `output=_raw` (optional) argument is to output the returned data without any formatting.
* Note: New Relic Event Types (tables) and Attributes (columns) in an NRQL query are case-sensitive. [e.g.: PageView, JavaScriptError, pageUrl, deviceType, errorMessage, etc.]

## Examples:
```
# Query New Relic 'prod-us-account' and return results

| nrql connection="prod-us-account" query="select * from PageView"


# Query New Relic 'test-eu-account' and return results

| nrql connection="test-eu-account" query="select count(*) as TotalTransactions from Transaction"


# Query New Relic 'prod-us-account' and combine the power of Splunk SPL

| nrql connection="prod-us-account" query="select count(*) from PageView facet deviceType since 1 day ago timeseries 1 hour"
| eval _time=strptime(beginTimeSeconds,"%s")
| xyseries _time, deviceType, count
```

---
#### For bugs, enhancements, or other requests create an issue in this repository
---