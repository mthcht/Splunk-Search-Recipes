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

## 
