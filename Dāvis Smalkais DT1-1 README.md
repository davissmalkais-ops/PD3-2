1_procesi.ps1

Get-Process | Where-Object { $_.WorkingSet -gt 20971520 }
>> Get-Process | Where-Object WorkingSet -GT 150MB
| Out-file -FilePath ‘C:\Users\TEMP.RVT.000\Downloads\New folder\LielieProcesi.csv’

2_zurnals.ps1

$w = Get-EventLog -LogName Application -EntryType Warning -After (Get-Date).AddDays(-3)
$top3 = $w | Group-Object Source | Sort-Object Count -Descending | Select-Object -First 3
$out = @("=== Warnings ==="); $w | % { $out += "$($_.TimeGenerated) | $($_.Source) | $($_.Message -replace '\n',' ')" }
$out += "=== TOP 3 ==="; $top3 | % { $out += "$($_.Name): $($_.Count)" }
$out | Set-Content "$env:C:\Documents\Warnings.txt" -Encoding UTF8 -Force

3_drosiba.ps1

$d = Get-MpComputerStatus; $s = 100
if (-not $d.RealTimeProtectionEnabled) { $s -= 50 }
if ($d.AntivirusSignatureAge -gt 3) { $s -= 20 }
if ($d.QuickScanAge -gt 7) { $s -= 20 }
Write-Host "Sistemas drosibas reitings: $s / 100"

4_arhivs.ps1

$f = Get-ChildItem "$env:A251568DS@rvt.lv\Downloads" -Filter "*.pdf" | Where-Object { $_.LastWriteTime -ge (Get-Date).AddHours(-48) }
Compress-Archive -Path $f.FullName -DestinationPath "C:\Documents\PDF_Backup.zip" -Force

5_programmatura.ps1

$apps = winget list --source msstore 2>&1 | Where-Object { $_ -match '\S' -and $_ -notmatch '^[-\\]|Name|---' }
$apps | ForEach-Object { Write-Host $_ }
Write-Host "Sistema ir instaletas $($apps.Count) aplikacijas no Microsoft Store."

6_apkope.ps1

$log = "$PSScriptRoot\Servisi.log"; $t = Get-Date -Format "yyyy-MM-dd HH:mm:ss"
"Spooler","wuauserv" | % { $s = Get-Service $_; "$t Serviss $($s.DisplayName) ir $($s.Status)" | Tee-Object -FilePath $log -Append }

7_Task Scheduler.ps1

$a = New-ScheduledTaskAction -Execute "powershell.exe" -Argument "-ExecutionPolicy Bypass -File C:\Scripts\6_apkope.ps1"
$t = New-ScheduledTaskTrigger -Weekly -DaysOfWeek Friday -At 17:00
Register-ScheduledTask -TaskName "6_apkope" -Action $a -Trigger $t -RunLevel Highest -Force

