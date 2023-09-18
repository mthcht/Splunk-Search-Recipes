## Find non empty lookups

```sql
| rest /servicesNS/-/-/data/lookup-table-files
| search title=Detect_* eai:acl.app=thunting
| fields title
| map maxsearches=200 search="| inputlookup $title$ | stats count | eval lookup_name=\"$title$\" | where count>0"
```

For this example, searching for lookups that start with `Detect_` in the `thunting` application and filtering to display only the non-empty ones

change `map maxsearches=200` to a higher number if you have more than 200 lookups (one subsearch by lookup) 

*find all non empty lookups*
```sql
| rest /servicesNS/-/-/data/lookup-table-files
| fields title
| map maxsearches=400 search="| inputlookup $title$ | stats count | eval lookup_name=\"$title$\" | where count>0"
```

## Find empty lookups

```sql
| rest /servicesNS/-/-/data/lookup-table-files
| search title=Detect_* eai:acl.app=thunting
| fields title
| map maxsearches=200 search="| inputlookup $title$ | stats count | eval lookup_name=\"$title$\" | where count=0"
```

---

## Find lookups with matching lookup definition names.

```sql
| rest /servicesNS/-/-/data/lookup-table-files 
| search eai:acl.app=hunting 
| table title 
| rename title as lookup_name 
| eval lookup_name=replace(lookup_name, "\.csv$", "")
| join type=inner lookup_name 
    [| rest /servicesNS/-/-/data/transforms/lookups 
    | search eai:acl.app=hunting 
    | table title 
    | rename title as lookup_name]
| dedup lookup_name
```

This query will automatically display lookups from the `hunting` app that have a corresponding lookup definition with an identical name within the same app.

*change 'hunting' with your splunk application name*

## Find lookups without matching lookup definition names.
```sql
| rest /servicesNS/-/-/data/lookup-table-files 
| search eai:acl.app=hunting
| table title 
| rename title as lookup_name 
| eval lookup_name=replace(lookup_name, "\.csv$", "")
| join type=left lookup_name 
    [| rest /servicesNS/-/-/data/transforms/lookups 
    | search eai:acl.app=hunting
    | table title 
    | rename title as lookup_definition_name 
    | eval lookup_name=lookup_definition_name]
| where isnull(lookup_definition_name)
| dedup lookup_name
```
This query will automatically display lookups from the `hunting` app that does not have a corresponding lookup definition with an identical name within the same app.

*change 'hunting' with your splunk application name*



