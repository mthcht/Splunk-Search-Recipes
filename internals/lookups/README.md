## Find empty lookups

```
| rest /servicesNS/-/-/data/lookup-table-files
| search title=Detect_* eai:acl.app=thunting
| fields title
| map maxsearches=200 search="| inputlookup $title$ | stats count | eval lookup_name=\"$title$\" | where count>0"
```

For this example, searching for lookups that start with `Detect_` in the `thunting` application and filtering to display only the non-empty ones

change `map maxsearches=200` to a higher number if you have more than 200 lookups (one subsearch by lookup) 

*find all empty lookups*
```
| rest /servicesNS/-/-/data/lookup-table-files
| fields title
| map maxsearches=400 search="| inputlookup $title$ | stats count | eval lookup_name=\"$title$\" | where count>0"
```

---

##
