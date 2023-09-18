## Find scheduled searches not working properly

```sql
index=_internal sourcetype=scheduler SOC_* status="failed" OR log_level=ERROR  savedsearch_id="*;hunting;*"
| stats last(message) by savedsearch_id
```

Search for scheduled searches with errors filtering on the splunk application named hunting `savedsearch_id="*;hunting;*"` (replace `hunting` with your own splunk application)

*Searching in all apps*
```sql
index=_internal sourcetype=scheduler SOC_* status="failed" OR log_level=ERROR
| stats last(message) by savedsearch_id
```

---

## Show every lookups used within each saved searches

```sql
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

## CSV Lookups Validation in saved Searches
 
```sql
| rest /servicesNS/-/-/saved/searches
| search eai:acl.app=soc
| table title search
| rename title as saved_search
| rex field=search max_match=0 "(lookup\s+(?<lookups>.+?(?=(\s))))"
| stats values(lookups) as lookups by saved_search
| mvexpand lookups
| join type=left lookups 
    [| rest /servicesNS/-/-/data/lookup-table-files
    | search eai:acl.app=hunting
    | table title
    | rename title as lookups
    | eval lookup_exists="Yes"]
| join type=left lookups 
    [| rest /servicesNS/-/-/data/transforms/lookups 
    | search eai:acl.app=hunting
    | table title 
    | rename title as lookups 
    | eval lookup_definition_exists="Yes"]
| fillnull value="No" lookup_exists lookup_definition_exists
| where lookups!=""
| stats values(lookup_exists) as csv_lookup_exists values(lookup_definition_exists) as lookup_definition_exists by saved_search, lookups
| where csv_lookup_exists="No" AND lookup_definition_exists="No"
```
This search scans saved searches in the 'hunting' app to identify utilized lookups, cross-references them with existing lookups on the SIEM within the same app, and highlights any lookups that are missing **both the csv lookup and the definition lookup.**

*`| where lookup_exists="Yes"` for the ones that does exist*

*replace `hunting` with your own splunk app*










