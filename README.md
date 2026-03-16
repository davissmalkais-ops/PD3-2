1.uzd

Get-Process | Where-Object { $_.WorkingSet -gt 20971520 }
>> Get-Process | Where-Object WorkingSet -GT 150MB
| Out-file -FilePath ‘C:\Users\TEMP.RVT.000\Downloads\New folder\LielieProcesi.csv’

2.
