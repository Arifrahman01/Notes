1. typeperf "\Processor(_Total)\% Processor Time" -sc 1
		Persentase penggunaan cpu
2. Get-Process | Sort-Object CPU -Descending | Select-Object -First 10 Name, CPU
	 List penggunaan CPU 
3. Get-Counter '\Processor(_Total)\% Processor Time'
4. Get-Counter "\System\Processor Queue Length"
	 Cek antrian
 5.  taskkill /F /IM LogonUI.exe
	Mematikan Proses loginui.exe
	 


PS C:\Users\adminjhonlin>