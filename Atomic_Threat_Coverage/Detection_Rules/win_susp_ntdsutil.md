| Title        | Invocation of Active Directory Diagnostic Tool (ntdsutil.exe) |
|:-------------------|:------------------|
| Description        | Detects execution of ntdsutil.exe, which can be used for various attacks against the NTDS database (NTDS.DIT) |
| Tags               |   |
| ATT&amp;CK Tactic | ('Credential Access', 'TA0006')  |
| ATT&amp;CK Technique | T1003  |
| Dataneeded         | DN_0003_windows_sysmon_process_creation_1, DN_0002_windows_process_creation_with_commandline_4688 |
| Triggering         | T1003 |
| Severity Level     | high       |
| False Positives    | NTDS maintenance |
| Development Status | experimental      |
| References         | https://jpcertcc.github.io/ToolAnalysisResultSheet/details/ntdsutil.htm |
| Author             | Thomas Patzke      |


## Detection Rules

### Sigma rule

```
---
action: global
title: Invocation of Active Directory Diagnostic Tool (ntdsutil.exe)
description: Detects execution of ntdsutil.exe, which can be used for various attacks against the NTDS database (NTDS.DIT)
status: experimental
references:
    - https://jpcertcc.github.io/ToolAnalysisResultSheet/details/ntdsutil.htm
author: Thomas Patzke
tags:
    - attack.credential_access
    - attack.t1003
detection:
    selection:
        CommandLine: '*\ntdsutil.exe *'
    condition: selection
falsepositives: 
    - NTDS maintenance
level: high
---
logsource:
    product: windows
    service: sysmon
detection:
    selection:
        EventID: 1
---
logsource:
    product: windows
    service: security
    description: 'Requirements: Audit Policy : Detailed Tracking > Audit Process creation, Group Policy : Administrative Templates\System\Audit Process Creation'
detection:
    selection:
        EventID: 4688


```





### Kibana query

```
(EventID:"1" AND CommandLine:"*\\\\ntdsutil.exe *")\n(EventID:"4688" AND CommandLine:"*\\\\ntdsutil.exe *")
```





### X-Pack Watcher

```
curl -s -XPUT -H \'Content-Type: application/json\' --data-binary @- localhost:9200/_xpack/watcher/watch/Invocation-of-Active-Directory-Diagnostic-Tool-ntdsutil.exe <<EOF\n{\n  "trigger": {\n    "schedule": {\n      "interval": "30m"\n    }\n  },\n  "input": {\n    "search": {\n      "request": {\n        "body": {\n          "size": 0,\n          "query": {\n            "query_string": {\n              "query": "(EventID:\\"1\\" AND CommandLine:\\"*\\\\\\\\ntdsutil.exe *\\")",\n              "analyze_wildcard": true\n            }\n          }\n        },\n        "indices": []\n      }\n    }\n  },\n  "condition": {\n    "compare": {\n      "ctx.payload.hits.total": {\n        "not_eq": 0\n      }\n    }\n  },\n  "actions": {\n    "send_email": {\n      "email": {\n        "to": null,\n        "subject": "Sigma Rule \'Invocation of Active Directory Diagnostic Tool (ntdsutil.exe)\'",\n        "body": "Hits:\\n{{#ctx.payload.hits.hits}}{{_source}}\\n================================================================================\\n{{/ctx.payload.hits.hits}}",\n        "attachments": {\n          "data.json": {\n            "data": {\n              "format": "json"\n            }\n          }\n        }\n      }\n    }\n  }\n}\nEOF\ncurl -s -XPUT -H \'Content-Type: application/json\' --data-binary @- localhost:9200/_xpack/watcher/watch/Invocation-of-Active-Directory-Diagnostic-Tool-ntdsutil.exe-2 <<EOF\n{\n  "trigger": {\n    "schedule": {\n      "interval": "30m"\n    }\n  },\n  "input": {\n    "search": {\n      "request": {\n        "body": {\n          "size": 0,\n          "query": {\n            "query_string": {\n              "query": "(EventID:\\"4688\\" AND CommandLine:\\"*\\\\\\\\ntdsutil.exe *\\")",\n              "analyze_wildcard": true\n            }\n          }\n        },\n        "indices": []\n      }\n    }\n  },\n  "condition": {\n    "compare": {\n      "ctx.payload.hits.total": {\n        "not_eq": 0\n      }\n    }\n  },\n  "actions": {\n    "send_email": {\n      "email": {\n        "to": null,\n        "subject": "Sigma Rule \'Invocation of Active Directory Diagnostic Tool (ntdsutil.exe)\'",\n        "body": "Hits:\\n{{#ctx.payload.hits.hits}}{{_source}}\\n================================================================================\\n{{/ctx.payload.hits.hits}}",\n        "attachments": {\n          "data.json": {\n            "data": {\n              "format": "json"\n            }\n          }\n        }\n      }\n    }\n  }\n}\nEOF\n
```





### Graylog

```
(EventID:"1" AND CommandLine:"*\\\\ntdsutil.exe *")\n(EventID:"4688" AND CommandLine:"*\\\\ntdsutil.exe *")
```

