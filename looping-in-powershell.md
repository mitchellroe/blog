# Looping in PowerShell

```powershell
ForEach-Object ($group_name in "group1", "group2") {
  Add-ADGroupMember $group_name -Members foo
}
```
