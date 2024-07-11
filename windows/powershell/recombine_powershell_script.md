Reconstruct executed PowerShell scripts in Splunk to avoid missing any string detection using the created field for your detection rules (you'll see the entire script content in the recombined_script field)

```kql
`powershell` signature_id=4104
| sort ProcessID, ActivityID, MessageNumber
| stats list(ScriptBlockText) as fragments by ProcessID, ActivityID
| eval recombined_script = mvjoin(fragments, "")
| table ProcessID, ActivityID, recombined_script
```
