## Find scheduled searches not working properly

```
index=_internal sourcetype=scheduler SOC_* status="failed" OR log_level=ERROR  savedsearch_id="*;hunting;*"
| stats last(message) by savedsearch_id
```

Search for scheduled searches with errors filtering on the splunk application named hunting `savedsearch_id="*;hunting;*"` (replace `hunting` with your own splunk application)

*Searching in all apps*
```
index=_internal sourcetype=scheduler SOC_* status="failed" OR log_level=ERROR
| stats last(message) by savedsearch_id
```

---

## Show every lookups used by each saved searches

```
| rest /servicesNS/-/-/saved/searches
| search eai:acl.app=hunting
| table title search
| rename title as saved_search
| rex field=search max_match=0 "(lookup\s+(?<lookups>[^\s]+))"
| stats values(lookups) as lookups by saved_search
```
This search will display all the lookups name used within each saved searches ! (replace 'hunting' with your splunk application) 

*search in all apps*
```
| rest /servicesNS/-/-/saved/searches
| table title search
| rename title as saved_search
| rex field=search max_match=0 "(lookup\s+(?<lookups>[^\s]+))"
| stats values(lookups) as lookups by saved_search
```



