# Splunk Notebook

---

## Join to distinct searches together and calculate a result

search  **| join** [ subsearch ] | **eval Value1\*Value2**

> index=wes *product=* sourcetype=wes:esauth4sv app=bid host="ap*wamauth*"\
> | rex field=*raw "\\S+\\s+\\S+\\s+\\S+\\s+(?<batch*id>\\S+)"\
> | transaction  batch_id\
> | search ch*nevis*session_entryid="security.six-group.com" authLevel="'3'" NOT pinChangeDialog\
> | stats count as Value1
>
> | **join**
>
>     [search index=wes product= sourcetype=wes:esauth4sv app=bid host="apwamauth"\
> | rex field=raw "\\S+\\s+\\S+\\s+\\S+\\s+(?<batchid>\\S+)"\
> | transaction batch_id\
> | search chnevissessionentryid="security.six-group.com"  result!=securidaccount currentstate = realmwebWebStrong.Login.SecurID\
> | stats count as Value2  ] 
>
> **| eval Result=Value1-Value2**


(index=wineventlog OR index=domain) EventCode=2 sourcetype="WinEventLog:Application" 
SourceName="EMET" | table _time, ComputerName, User_Name, MsgTXT, Application, API_Name, Module |  `map_notable_fields`
