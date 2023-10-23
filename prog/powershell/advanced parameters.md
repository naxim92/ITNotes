#ps 

- В PowerShell из коробки есть механизм а-ля python argparse, она называется *advanced parameters*
- Писать вроде как надо в первой строчке скрипта \[после комментов\]
- Пример:
	``` ps
	# Create scrip1.ps1 file having parameters of different data types
	param(
		[string]$Subject,
		[int]$Marks
	)
	Write-Host "Tom scored $Marks in subject $Subject."
	```
	И вызывается так
	`D:\PS\script1.ps1 -Subject "Physics" -Marks 42`
- Неплохая [статья](https://shellgeek.com/powershell-command-line-arguments/)