# Windows fundamentals

### Introduction
- Discovered new way for my self how to access windows machine remotely (xfreerdp is Remote Desktop Protocol for Linux client). xfreerdp can be used instead and the connection is very simple `xfreerdp3 /v:Target_IP /u:htb-student /p:Password`
### Core of the Operating System
- `tree` and `dir` seemed similar to me at first but tree is recursive and visualization of directories.
- `icacls c:\Users` is used to view users permissions
- To change permissions for a user `grant` command can be used as follows:
```ps
icacls c:\users /grant <username>:<permision>
```

### Working with Services & Processes
- Use `Get-WmiObject Win32_Service` instead of `Get-Service` if you need more information about the service.
- `Where-Object`  doing same job as `grep` from Linux and `WHERE` from MySQL. Syntax is easier than it looks like at first glance. `-like "*name*"` is just like SQL just need to add `$_.Name` and putting it all together  in `{}`  to get  `Where-Object {$_.Name -like "*oxit*"}` 
- `Select-Object` is also just like SQL `SELECT` but instead of columns you selecting names `Select-Object Name, PathName, DisplayName`

### Things I did not understood at first but now do.
- LSASS is process that verifies password on  log in and keeps it in memory. `Mimikatz` is a tool that dumps those credentials opening attack vector.
- Service permission privesc - every service has a file that it runs. If the file is in the folder where everyone can read and write files you can replace the `good.exe` file into `malicious.exe`. Next time service starts your code executes as high privilege
- Every object, file and service has security descriptor;
	- DACL - who can access it and what they can do
	- SACL - who gets logged when they access it
	- SDDL - the text format that describes these rules

### Interacting with Services & Processes
- Helpful command for cmd:
	- `help` - list all available commands
	- `help <command name>` - lists more details about specific command
	- `<command> /?` - help menu of the command
-  `Cmdlets` - small single function tools build into shell

### Pitfalls
- Realized that `$_.Name` can be changed to anything else in ` Where-Object {$_.Name -like "*Update*"}`. If you want to look for Display name then it would be `$_.DisplayName`
- `wmic useraccount get name,sid` - used to list `SID` of all users
- replacing `useraccount` with `group` will list all groups instead
