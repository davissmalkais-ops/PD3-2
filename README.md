 Get-Process | Where-Object { $_.WorkingSet -gt 20971520 }
>> Get-Process | Where-Object WorkingSet -GT 150MB
