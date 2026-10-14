# DT2-1

Get-CimInstance Win32_OperatingSystem
Iegūst informāciju par operētājsistēmu

($processCount = @(Get-Process).Count)
Saskaita sistēmā esošos procesus

Get-Date -Format "dd.MM.yyyy"
Izvada tagadējo datumu un laiku

Get-Process | Sort-Object WorkingSet64 | Select-Object -Last 10 | Select-Object Name, Id, @{Name="Memory(MB)"; Expression={[math]::Round($_.WorkingSet64 / 1MB, 1)}} | Format-Table -AutoSize
Izvada 10 procesus datorā, kas patērē visvairāk operatīvās atmiņas
