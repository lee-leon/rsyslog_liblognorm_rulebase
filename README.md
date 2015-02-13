# Rsyslog and omelasticsearch
> see: http://www.rsyslog.com/doc/v8-stable/configuration/modules/omelasticsearch.html

### omelasticsearch parameters:
* server Host name or IP address of the Elasticsearch server. Defaults to “localhost”
* serverport - HTTP port to connect to Elasticsearch. Defaults to 9200
* searchIndex - Elasticsearch index to send your logs to. Defaults to “system”
* dynSearchIndex<on/off> - Whether the string provided for searchIndex should be taken as a template. Defaults to “off”, which means the index name will be taken literally. Otherwise, it will look for a template with that name, and the resulting string will be the index name. For example, let’s assume you define a template named “date-days” containing “%timereported:1:10:date-rfc3339%”. Then, with dynSearchIndex=”on”, if you say searchIndex=”date-days”, each log will be sent to and index named after the first 10 characters of the timestamp, like “2013-03-22”.
* searchType Elasticsearch type to send your index to. Defaults to “events”
* dynSearchType <on/off> Like dynSearchIndex, it allows you to specify a template for searchType, instead of a static string.
* asyncrepl<on/off> By default, an indexing operation returns after all replica shards have indexed the document. With asyncrepl=”on” it will return after it was indexed on the primary shard only - thus trading some consistency for speed.
* usehttps<on/off> Send events over HTTPS instead of HTTP. Good for when you have Elasticsearch behind Apache or something else that can add HTTPS.
* timeout How long Elasticsearch will wait for a primary shard to be available for indexing your log before sending back an error. Defaults to “1m”.
* template This is the JSON document that will be indexed in Elasticsearch. The resulting string needs to be a valid JSON, otherwise Elasticsearch will return an error. Defaults to:
* bulkmode<on/off> The default “off” setting means logs are shipped one by one. Each in its own HTTP request, using the Index API. Set it to “on” and it will use Elasticsearch’s Bulk API to send multiple logs in the same request. The maximum number of logs sent in a single bulk request depends on your queue settings - usually limited by the dequeue batch size. More information about queues can be found here.
* parent Specifying a string here will index your logs with that string the parent ID of those logs. Please note that you need to define the parent field in your mapping for that to work. By default, logs are indexed without a parent.
* dynParent<on/off> Using the same parent for all the logs sent in the same action is quite unlikely. So you’d probably want to turn this “on” and specify a template that will provide meaningful parent IDs for your logs.
* uid If you have basic HTTP authentication deployed (eg through the elasticsearch-basic plugin), you can specify your user-name here.
* pwd Password for basic authentication.
* errorfile <filename> (optional)

***

## Rsyslog versions

See: http://www.rsyslog.com/debian-repository/

... list installed packages: name, version, and description:
```
sudo dpkg -l ... which MUST be:
ii  rsyslog               8.2.0-0adiscon1precise4 a rocket-fast system for log processing
ii  rsyslog-elasticsearch 8.2.0-0adiscon1precise4 Elasticsearch output support for rsyslog
ii  rsyslog-mmnormalize   8.2.0-0adiscon1precise4 The rsyslog-mmnormalize package provides log normalization
```

## rsyslog.conf usage

### bulk loading syslogs into elasticsearch

This approach is compatible with the logstash's elasticsearch template, so the 
indexed syslogs appear the same as if they are from logstash.

... add the mmnormalize and omelasticsearch dependencies:
```
sudo apt-get install rsyslog-mmnormalize
sudo apt-get install rsyslog-elasticsearch
```
May 2014: the above was abandoned in favor of a simpler approach using Logstash.

***

## A rulebase for liblognorm and mmnormalize in rsyslog

### Rule and Field syntax

```
rule=[<tag1>[,<tag2>...]]:<match description>
```
```
%<field name>:<field type>[:<extra data>]%
```

### Known field types _(as in gleaned from the source code)_

> _**:rest%** was hard to find, and it would be nice to have something like **ignore-words:5**_

1. %field_name:rest%
2. %field_name:number%
3. %field_name:word%
4. %field_name:char-to:,%
5. %field_name:char-to:\x25%
6. %field_name:quoted-string%
7. %field-name:date-iso%
8. %field_name:time-24hr%
9. %field_name:time-12hr%
10. %field_name:ipv4%
11. %field_name:date-rfc3164%
12. %field_name:date-rfc5424%
13. %-:iptables%

***
