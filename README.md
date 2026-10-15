# DT2-1

1.Sistēmas apraksts

Get-CimInstance Win32_OperatingSystem
($processCount = @(Get-Process).Count)
Get-Date -Format "dd.MM.yyyy"



2.Desit procesi sakārtoti pēc atmiņas

Get-Process | Sort-Object WorkingSet64 | Select-Object -Last 10 | Select-Object Name, Id, @{Name="Memory(MB)"; Expression={[math]::Round($_.WorkingSet64 / 1MB, 1)}} | Format-Table -AutoSize


3.Servisu skaitīšana

$r = 0; $s = 0; foreach($m in gsv){ if($m.Status -eq "Running"){$r++} else{$s++}} "Darbojas: $r | Apturēti: $s | Summa: $($r+$s)" 

   Kopējais skaits ir 326 Darbojas: 133 | Apturēti: 193


4.Automātisko servisu palaišana

$targetServices = Get-CimInstance Win32_Service | Where-Object { $_.StartMode -eq "Auto" -and $_.State -eq "Stopped"}
if ($targetService. Count -eq 0 -or $targetService -eq $null) {
  $msg = "Atrasto Servisu Skaits: 0"
  $msg | Out-File -FilePath "whatif.txt" -Encoding utf8
  $msg | Out-File -FilePath "izvade.txt" -Encoding utf8
  Write-Host $msg
} else {
  $targetServices | Start-Service -WhatIf 3>&1 | Out-File -FilePath "whatif.txt" -Encoding utf8
  
  $targetServices | Start-Service -PassThru 2>&1 | Out-File -FilePath "izvade.txt" -Encoding utf8
}

Atrasto Servisu Skaits: 0


5.Kļūdas sistēmu žurnālā

$err = Get-WinEvent -FilterHashtable @{LogName='System'; Level=2; StartTime=(Get-Date).AddHours(-48)} -EA 0
$kopskaits = if ($err) { $err.Count } else { 0 }
"Kļūdas: $kopskaits"
if ($err) { 
    $err | Group-Object ProviderName | Sort-Object Count -Descending | Select-Object -First 1 | ForEach-Object { 
        "Biežākais: ($($_.Name)) ($($_.Count)X)"
    }
}



6.Draieri, kas nav Microsoft

$nonMSDrivers = Get-WindowsDriver -Online -All | Where-Object {$_.ProviderName -ne 'Microsoft'}
$nonMSDrivers | Export-Csv -Path "drivers.csv" -NoTypeInformation -Encoding UTF8

$rowCount = (Get-Content -Path "drivers.csv").Count
"CSV faila rindu skaits: $rowCount" | Out-File -FilePath izvade.txt -Append
Write-Host "Draiveri saglabāti. Rindu skaits: $rowCount"


