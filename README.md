### A rulebase for liblognorm and mmnormalize in rsyslog

### Rule and Field syntax

```
rule=[<tag1>[,<tag2>...]]:<match description>
```
```
%<field name>:<field type>[:<extra data>]%
```

### Known field types

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
