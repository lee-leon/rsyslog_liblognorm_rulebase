## Rsyslog versions

... list installed packages: name, version, and description:
sudo dpkg -l  (hyphen L) ... must be:
```
ii  rsyslog                 8.2.0-0adiscon1precise4    a rocket-fast system for log processing
ii  rsyslog-elasticsearch   8.2.0-0adiscon1precise4    Elasticsearch output support for rsyslog
ii  rsyslog-mmnormalize     8.2.0-0adiscon1precise4    The rsyslog-mmnormalize package provides log normalization
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
