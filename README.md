# living_off_the_land

I play in my own active directory lab a lot. I love being able to execute something and see the results instantly, this approach has enabled me to discover loads of techniques.

I wanted to share some of these results and as such will document some of these findings.

These should be considered experimental and the results are highly likely to differ depending on the testing environment.

All these shared samples were created by me, using tooling built into the Windows operating system, they have have also been executed under the context of an account that only belongs to the domain user group (standard user).

## Build your own AD lab

I strongly encourage that all infrastructure testers including Red / Purple / Blue Team members should build their own active directory lab, which consists of at least one Windows server, and two Windows 10 / 11 hosts.

For more information on how I build my lab please see https://github.com/myexploit/LAB/blob/master/Hack_Lab_Domain which documents setting up the test domain that I use.

## Free Trials

- Server 2022 - https://info.microsoft.com/ww-landing-windows-server-2022.html
- Windows 11 - https://www.microsoft.com/software-download/windows11
- Prebuilt Windows 11 VM (but I think these are now a messy solution) - https://developer.microsoft.com/en-us/windows/downloads/virtual-machines/

# Living Off The Land Notes
Below contains a bunch of notes around different powershell implementations and quick wins.

## About WMI
Windows Management Instrumentation (WMI) is a technology developed by Microsoft that provides a standardised way to manage and monitor various system resources on Windows-based computers. WMI is a component of the Windows operating system and allows system administrators to gather information about hardware, software, and network configurations, as well as remotely control system functions.

WMI uses a model-driven architecture to represent system resources, which are exposed as managed objects that can be accessed and manipulated using WMI-based tools, scripting languages, or programming languages. WMI provides a uniform interface to access system resources across different versions of Windows, enabling administrators to manage and monitor multiple systems using a common set of tools and techniques.

WMI provides a wide range of functionality, including the ability to retrieve system information, configure system settings, monitor system events, and execute commands and scripts on remote computers. WMI can be accessed using several programming and scripting languages, such as PowerShell, VBScript, and C#.

Overall, WMI is a powerful technology that enables system administrators to manage and monitor Windows-based systems in a standardized and efficient manner.

## Combing WMI with PowerShell

PowerShell is a powerful command-line shell and scripting language that can be used to interact with WMI on Windows-based computers. PowerShell provides several cmdlets and modules that enable administrators to query WMI, retrieve information, and perform various management tasks.

Here are some examples of how to use WMI with PowerShell:

Retrieving system information from the local host:

Syntax:
```
Get-WmiObject -Class Win32_OperatingSystem
```
Result:
```
PS C:\Users\yepyepyep> Get-WmiObject -Class Win32_OperatingSystem

SystemDirectory : C:\WINDOWS\system32
Organization    :
BuildNumber     : 22621
RegisteredUser  : yepyepyep@myexploitland.com
SerialNumber    : 00220-007-Redacted-1234M
Version         : 10.0.22621
```
Querying for specific information on the local host:

Syntax:
```
Get-WmiObject -Class Win32_Processor | Select-Object Name, Manufacturer, MaxClockSpeed
```
Result:
```
PS C:\Users\yepyepyep> Get-WmiObject -Class Win32_Processor | Select-Object Name, Manufacturer, MaxClockSpeed

Name                                      Manufacturer MaxClockSpeed
----                                      ------------ -------------
Intel(R) Core(TM) i5-10300H CPU @ 2.50GHz GenuineIntel          2496
```

### Enumerate_domain_groups_win32_groupindomain
Complete Syntax:
```
Get-CimInstance -ClassName Win32_GroupInDomain | Format-Table -AutoSize
```
Result:
```
PS C:\Users\User1> Get-CimInstance -ClassName Win32_GroupInDomain | Format-Table -AutoSize

GroupComponent                            PartComponent                                                                        PSComputerName
--------------                            -------------                                                                        --------------
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_Group (Name = "Cert Publishers", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_Group (Name = "RAS and IAS Servers", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_Group (Name = "Allowed RODC Password Replication Group..., Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_Group (Name = "Denied RODC Password Replication Group", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_Group (Name = "DnsAdmins", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_Group (Name = "accounts", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_Group (Name = "administration", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_Group (Name = "Cloneable Domain Controllers", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_Group (Name = "DnsUpdateProxy", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_Group (Name = "Domain Admins", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_Group (Name = "Domain Computers", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_Group (Name = "Domain Controllers", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_Group (Name = "Domain Guests", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_Group (Name = "Domain Users", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_Group (Name = "Enterprise Admins", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_Group (Name = "Enterprise Key Admins", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_Group (Name = "Enterprise Read-only Domain Controllers..., Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_Group (Name = "Group Policy Creator Owners", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_Group (Name = "help_desk", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_Group (Name = "Key Admins", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_Group (Name = "Protected Users", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_Group (Name = "RDP", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_Group (Name = "Read-only Domain Controllers", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_Group (Name = "sales", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_Group (Name = "Schema Admins", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_Group (Name = "support", Domain = "HACKLAB")
```
### Enumerate_domain_users_and_SID_win32_useraccount

Complete Syntax:
```
Get-CimInstance -ClassName Win32_UserAccount | Format-Table -AutoSize
```
Result:
```
PS C:\Users\User1> Get-CimInstance -ClassName Win32_UserAccount | Format-Table -AutoSize

Name               Caption                        AccountType SID                                            Domain
----               -------                        ----------- ---                                            ------
Administrator      LAB-WIN11-2\Administrator      512         S-1-5-21-989316335-961067246-357250816-500     LAB-WIN11-2
DefaultAccount     LAB-WIN11-2\DefaultAccount     512         S-1-5-21-989316335-961067246-357250816-503     LAB-WIN11-2
Guest              LAB-WIN11-2\Guest              512         S-1-5-21-989316335-961067246-357250816-501     LAB-WIN11-2
ieuser             LAB-WIN11-2\ieuser             512         S-1-5-21-989316335-961067246-357250816-1001    LAB-WIN11-2
WDAGUtilityAccount LAB-WIN11-2\WDAGUtilityAccount 512         S-1-5-21-989316335-961067246-357250816-504     LAB-WIN11-2
Administrator      HACKLAB\Administrator          512         S-1-5-21-2616623822-1553658618-2628931692-500  HACKLAB
Guest              HACKLAB\Guest                  512         S-1-5-21-2616623822-1553658618-2628931692-501  HACKLAB
krbtgt             HACKLAB\krbtgt                 512         S-1-5-21-2616623822-1553658618-2628931692-502  HACKLAB
n.collins          HACKLAB\n.collins              512         S-1-5-21-2616623822-1553658618-2628931692-1109 HACKLAB
o.davidson         HACKLAB\o.davidson             512         S-1-5-21-2616623822-1553658618-2628931692-1110 HACKLAB
p.davies           HACKLAB\p.davies               512         S-1-5-21-2616623822-1553658618-2628931692-1111 HACKLAB
q.dawson           HACKLAB\q.dawson               512         S-1-5-21-2616623822-1553658618-2628931692-1112 HACKLAB
u.dixon            HACKLAB\u.dixon                512         S-1-5-21-2616623822-1553658618-2628931692-1113 HACKLAB
r.edwards          HACKLAB\r.edwards              512         S-1-5-21-2616623822-1553658618-2628931692-1114 HACKLAB
s.elliot           HACKLAB\s.elliot               512         S-1-5-21-2616623822-1553658618-2628931692-1115 HACKLAB
t.evans            HACKLAB\t.evans                512         S-1-5-21-2616623822-1553658618-2628931692-1116 HACKLAB
u.fisher           HACKLAB\u.fisher               512         S-1-5-21-2616623822-1553658618-2628931692-1117 HACKLAB
v.fletcher         HACKLAB\v.fletcher             512         S-1-5-21-2616623822-1553658618-2628931692-1118 HACKLAB
w.ford             HACKLAB\w.ford                 512         S-1-5-21-2616623822-1553658618-2628931692-1119 HACKLAB
x.foster           HACKLAB\x.foster               512         S-1-5-21-2616623822-1553658618-2628931692-1120 HACKLAB
y.fox              HACKLAB\y.fox                  512         S-1-5-21-2616623822-1553658618-2628931692-1121 HACKLAB
z.gibson           HACKLAB\z.gibson               512         S-1-5-21-2616623822-1553658618-2628931692-1122 HACKLAB
a.graham           HACKLAB\a.graham               512         S-1-5-21-2616623822-1553658618-2628931692-1123 HACKLAB
b.grant            HACKLAB\b.grant                512         S-1-5-21-2616623822-1553658618-2628931692-1124 HACKLAB
c.gray             HACKLAB\c.gray                 512         S-1-5-21-2616623822-1553658618-2628931692-1125 HACKLAB
d.green            HACKLAB\d.green                512         S-1-5-21-2616623822-1553658618-2628931692-1126 HACKLAB
m.jenkins          HACKLAB\m.jenkins              512         S-1-5-21-2616623822-1553658618-2628931692-1127 HACKLAB
n.johnson          HACKLAB\n.johnson              512         S-1-5-21-2616623822-1553658618-2628931692-1128 HACKLAB
o.jones            HACKLAB\o.jones                512         S-1-5-21-2616623822-1553658618-2628931692-1129 HACKLAB
g.white            HACKLAB\g.white                512         S-1-5-21-2616623822-1553658618-2628931692-1130 HACKLAB
h.yalden           HACKLAB\h.yalden               512         S-1-5-21-2616623822-1553658618-2628931692-1131 HACKLAB
i.yarbury          HACKLAB\i.yarbury              512         S-1-5-21-2616623822-1553658618-2628931692-1132 HACKLAB
j.yardley          HACKLAB\j.yardley              512         S-1-5-21-2616623822-1553658618-2628931692-1133 HACKLAB
z.mcdonald         HACKLAB\z.mcdonald             512         S-1-5-21-2616623822-1553658618-2628931692-1134 HACKLAB
a.murphy           HACKLAB\a.murphy               512         S-1-5-21-2616623822-1553658618-2628931692-1135 HACKLAB
b.natt             HACKLAB\b.natt                 512         S-1-5-21-2616623822-1553658618-2628931692-1136 HACKLAB
c.nelson           HACKLAB\c.nelson               512         S-1-5-21-2616623822-1553658618-2628931692-1137 HACKLAB
d.nightingale      HACKLAB\d.nightingale          512         S-1-5-21-2616623822-1553658618-2628931692-1138 HACKLAB
e.nixon            HACKLAB\e.nixon                512         S-1-5-21-2616623822-1553658618-2628931692-1139 HACKLAB
f.nutter           HACKLAB\f.nutter               512         S-1-5-21-2616623822-1553658618-2628931692-1140 HACKLAB
p.kelly            HACKLAB\p.kelly                512         S-1-5-21-2616623822-1553658618-2628931692-1141 HACKLAB
q.kennedy          HACKLAB\q.kennedy              512         S-1-5-21-2616623822-1553658618-2628931692-1142 HACKLAB
u.king             HACKLAB\u.king                 512         S-1-5-21-2616623822-1553658618-2628931692-1143 HACKLAB
r.knight           HACKLAB\r.knight               512         S-1-5-21-2616623822-1553658618-2628931692-1144 HACKLAB
s.lawrence         HACKLAB\s.lawrence             512         S-1-5-21-2616623822-1553658618-2628931692-1145 HACKLAB
t.lee              HACKLAB\t.lee                  512         S-1-5-21-2616623822-1553658618-2628931692-1146 HACKLAB
u.lewis            HACKLAB\u.lewis                512         S-1-5-21-2616623822-1553658618-2628931692-1147 HACKLAB
v.lloyd            HACKLAB\v.lloyd                512         S-1-5-21-2616623822-1553658618-2628931692-1148 HACKLAB
w.marshall         HACKLAB\w.marshall             512         S-1-5-21-2616623822-1553658618-2628931692-1149 HACKLAB
x.martin           HACKLAB\x.martin               512         S-1-5-21-2616623822-1553658618-2628931692-1150 HACKLAB
y.mason            HACKLAB\y.mason                512         S-1-5-21-2616623822-1553658618-2628931692-1151 HACKLAB
g.dell             HACKLAB\g.dell                 512         S-1-5-21-2616623822-1553658618-2628931692-1152 HACKLAB
h.osborne          HACKLAB\h.osborne              512         S-1-5-21-2616623822-1553658618-2628931692-1153 HACKLAB
i.owen             HACKLAB\i.owen                 512         S-1-5-21-2616623822-1553658618-2628931692-1154 HACKLAB
j.oxley            HACKLAB\j.oxley                512         S-1-5-21-2616623822-1553658618-2628931692-1155 HACKLAB
k.page             HACKLAB\k.page                 512         S-1-5-21-2616623822-1553658618-2628931692-1156 HACKLAB
l.painter          HACKLAB\l.painter              512         S-1-5-21-2616623822-1553658618-2628931692-1157 HACKLAB
m.palmer           HACKLAB\m.palmer               512         S-1-5-21-2616623822-1553658618-2628931692-1158 HACKLAB
n.pastor           HACKLAB\n.pastor               512         S-1-5-21-2616623822-1553658618-2628931692-1159 HACKLAB
o.peterson         HACKLAB\o.peterson             512         S-1-5-21-2616623822-1553658618-2628931692-1160 HACKLAB
p.quill            HACKLAB\p.quill                512         S-1-5-21-2616623822-1553658618-2628931692-1161 HACKLAB
q.quimby           HACKLAB\q.quimby               512         S-1-5-21-2616623822-1553658618-2628931692-1162 HACKLAB
u.quintrell        HACKLAB\u.quintrell            512         S-1-5-21-2616623822-1553658618-2628931692-1163 HACKLAB
r.ramsey           HACKLAB\r.ramsey               512         S-1-5-21-2616623822-1553658618-2628931692-1164 HACKLAB
s.ratliff          HACKLAB\s.ratliff              512         S-1-5-21-2616623822-1553658618-2628931692-1165 HACKLAB
t.richards         HACKLAB\t.richards             512         S-1-5-21-2616623822-1553658618-2628931692-1166 HACKLAB
u.roberts          HACKLAB\u.roberts              512         S-1-5-21-2616623822-1553658618-2628931692-1167 HACKLAB
v.robinson         HACKLAB\v.robinson             512         S-1-5-21-2616623822-1553658618-2628931692-1168 HACKLAB
w.scott            HACKLAB\w.scott                512         S-1-5-21-2616623822-1553658618-2628931692-1169 HACKLAB
x.simpson          HACKLAB\x.simpson              512         S-1-5-21-2616623822-1553658618-2628931692-1170 HACKLAB
y.smith            HACKLAB\y.smith                512         S-1-5-21-2616623822-1553658618-2628931692-1171 HACKLAB
z.stewart          HACKLAB\z.stewart              512         S-1-5-21-2616623822-1553658618-2628931692-1172 HACKLAB
a.taylor           HACKLAB\a.taylor               512         S-1-5-21-2616623822-1553658618-2628931692-1173 HACKLAB
b.turner           HACKLAB\b.turner               512         S-1-5-21-2616623822-1553658618-2628931692-1174 HACKLAB
c.walsh            HACKLAB\c.walsh                512         S-1-5-21-2616623822-1553658618-2628931692-1175 HACKLAB
d.ward             HACKLAB\d.ward                 512         S-1-5-21-2616623822-1553658618-2628931692-1176 HACKLAB
e.webb             HACKLAB\e.webb                 512         S-1-5-21-2616623822-1553658618-2628931692-1177 HACKLAB
f.west             HACKLAB\f.west                 512         S-1-5-21-2616623822-1553658618-2628931692-1178 HACKLAB
d.atkinson         HACKLAB\d.atkinson             512         S-1-5-21-2616623822-1553658618-2628931692-1179 HACKLAB
e.bailey           HACKLAB\e.bailey               512         S-1-5-21-2616623822-1553658618-2628931692-1180 HACKLAB
f.baker            HACKLAB\f.baker                512         S-1-5-21-2616623822-1553658618-2628931692-1181 HACKLAB
g.ball             HACKLAB\g.ball                 512         S-1-5-21-2616623822-1553658618-2628931692-1182 HACKLAB
h.bell             HACKLAB\h.bell                 512         S-1-5-21-2616623822-1553658618-2628931692-1183 HACKLAB
i.brown            HACKLAB\i.brown                512         S-1-5-21-2616623822-1553658618-2628931692-1184 HACKLAB
j.burton           HACKLAB\j.burton               512         S-1-5-21-2616623822-1553658618-2628931692-1185 HACKLAB
k.carter           HACKLAB\k.carter               512         S-1-5-21-2616623822-1553658618-2628931692-1186 HACKLAB
l.clarke           HACKLAB\l.clarke               512         S-1-5-21-2616623822-1553658618-2628931692-1187 HACKLAB
m.cole             HACKLAB\m.cole                 512         S-1-5-21-2616623822-1553658618-2628931692-1188 HACKLAB
e.griffiths        HACKLAB\e.griffiths            512         S-1-5-21-2616623822-1553658618-2628931692-1189 HACKLAB
f.hall             HACKLAB\f.hall                 512         S-1-5-21-2616623822-1553658618-2628931692-1190 HACKLAB
g.hamilton         HACKLAB\g.hamilton             512         S-1-5-21-2616623822-1553658618-2628931692-1191 HACKLAB
h.harris           HACKLAB\h.harris               512         S-1-5-21-2616623822-1553658618-2628931692-1192 HACKLAB
i.harvey           HACKLAB\i.harvey               512         S-1-5-21-2616623822-1553658618-2628931692-1193 HACKLAB
j.hill             HACKLAB\j.hill                 512         S-1-5-21-2616623822-1553658618-2628931692-1194 HACKLAB
k.jackson          HACKLAB\k.jackson              512         S-1-5-21-2616623822-1553658618-2628931692-1195 HACKLAB
l.james            HACKLAB\l.james                512         S-1-5-21-2616623822-1553658618-2628931692-1196 HACKLAB
k.yarrow           HACKLAB\k.yarrow               512         S-1-5-21-2616623822-1553658618-2628931692-1197 HACKLAB
l.yates            HACKLAB\l.yates                512         S-1-5-21-2616623822-1553658618-2628931692-1198 HACKLAB
m.young            HACKLAB\m.young                512         S-1-5-21-2616623822-1553658618-2628931692-1199 HACKLAB
n.zachary          HACKLAB\n.zachary              512         S-1-5-21-2616623822-1553658618-2628931692-1200 HACKLAB
o.zelly            HACKLAB\o.zelly                512         S-1-5-21-2616623822-1553658618-2628931692-1201 HACKLAB
p.zinc             HACKLAB\p.zinc                 512         S-1-5-21-2616623822-1553658618-2628931692-1202 HACKLAB
q.zouch            HACKLAB\q.zouch                512         S-1-5-21-2616623822-1553658618-2628931692-1203 HACKLAB
a.adams            HACKLAB\a.adams                512         S-1-5-21-2616623822-1553658618-2628931692-1204 HACKLAB
b.allen            HACKLAB\b.allen                512         S-1-5-21-2616623822-1553658618-2628931692-1205 HACKLAB
c.armstrong        HACKLAB\c.armstrong            512         S-1-5-21-2616623822-1553658618-2628931692-1206 HACKLAB
adm.adams          HACKLAB\adm.adams              512         S-1-5-21-2616623822-1553658618-2628931692-1207 HACKLAB
adm.smith          HACKLAB\adm.smith              512         S-1-5-21-2616623822-1553658618-2628931692-1208 HACKLAB
adm.stewart        HACKLAB\adm.stewart            512         S-1-5-21-2616623822-1553658618-2628931692-1209 HACKLAB
adm.natt           HACKLAB\adm.natt               512         S-1-5-21-2616623822-1553658618-2628931692-1210 HACKLAB
adm.nelson         HACKLAB\adm.nelson             512         S-1-5-21-2616623822-1553658618-2628931692-1211 HACKLAB
svc_afds           HACKLAB\svc_afds               512         S-1-5-21-2616623822-1553658618-2628931692-1212 HACKLAB
svc_test           HACKLAB\svc_test               512         S-1-5-21-2616623822-1553658618-2628931692-1213 HACKLAB
svc_mssql1         HACKLAB\svc_mssql1             512         S-1-5-21-2616623822-1553658618-2628931692-1214 HACKLAB
svc_mssql2         HACKLAB\svc_mssql2             512         S-1-5-21-2616623822-1553658618-2628931692-1215 HACKLAB
svc_lab            HACKLAB\svc_lab                512         S-1-5-21-2616623822-1553658618-2628931692-1216 HACKLAB
svc_admin          HACKLAB\svc_admin              512         S-1-5-21-2616623822-1553658618-2628931692-1217 HACKLAB
DA1                HACKLAB\DA1                    512         S-1-5-21-2616623822-1553658618-2628931692-1251 HACKLAB
user1              HACKLAB\user1                  512         S-1-5-21-2616623822-1553658618-2628931692-1252 HACKLAB
user2              HACKLAB\user2                  512         S-1-5-21-2616623822-1553658618-2628931692-1255 HACKLAB
user3              HACKLAB\user3                  512         S-1-5-21-2616623822-1553658618-2628931692-1257 HACKLAB
test2              HACKLAB\test2                  512         S-1-5-21-2616623822-1553658618-2628931692-1259 HACKLAB
user4              HACKLAB\user4                  512         S-1-5-21-2616623822-1553658618-2628931692-1260 HACKLAB
```
### Enumerate_domain_users_win32_userdesktop

Complete Syntax:
```
Get-CimInstance -ClassName Win32_UserDesktop | Format-Table -AutoSize > Win32_UserDesktop.txt
```

Result:
```

PS C:\Users\User1> Get-CimInstance -ClassName Win32_UserDesktop

Element                                                                 Setting                                PSComputerName
-------                                                                 -------                                --------------
Win32_UserAccount (Name = "Administrator", Domain = "LAB-WIN11-2")      Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "DefaultAccount", Domain = "LAB-WIN11-2")     Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "Guest", Domain = "LAB-WIN11-2")              Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "ieuser", Domain = "LAB-WIN11-2")             Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "WDAGUtilityAccount", Domain = "LAB-WIN11-2") Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "Administrator", Domain = "HACKLAB")          Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "Guest", Domain = "HACKLAB")                  Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "krbtgt", Domain = "HACKLAB")                 Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "n.collins", Domain = "HACKLAB")              Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "o.davidson", Domain = "HACKLAB")             Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "p.davies", Domain = "HACKLAB")               Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "q.dawson", Domain = "HACKLAB")               Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "u.dixon", Domain = "HACKLAB")                Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "r.edwards", Domain = "HACKLAB")              Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "s.elliot", Domain = "HACKLAB")               Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "t.evans", Domain = "HACKLAB")                Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "u.fisher", Domain = "HACKLAB")               Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "v.fletcher", Domain = "HACKLAB")             Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "w.ford", Domain = "HACKLAB")                 Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "x.foster", Domain = "HACKLAB")               Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "y.fox", Domain = "HACKLAB")                  Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "z.gibson", Domain = "HACKLAB")               Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "a.graham", Domain = "HACKLAB")               Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "b.grant", Domain = "HACKLAB")                Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "c.gray", Domain = "HACKLAB")                 Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "d.green", Domain = "HACKLAB")                Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "m.jenkins", Domain = "HACKLAB")              Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "n.johnson", Domain = "HACKLAB")              Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "o.jones", Domain = "HACKLAB")                Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "g.white", Domain = "HACKLAB")                Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "h.yalden", Domain = "HACKLAB")               Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "i.yarbury", Domain = "HACKLAB")              Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "j.yardley", Domain = "HACKLAB")              Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "z.mcdonald", Domain = "HACKLAB")             Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "a.murphy", Domain = "HACKLAB")               Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "b.natt", Domain = "HACKLAB")                 Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "c.nelson", Domain = "HACKLAB")               Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "d.nightingale", Domain = "HACKLAB")          Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "e.nixon", Domain = "HACKLAB")                Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "f.nutter", Domain = "HACKLAB")               Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "p.kelly", Domain = "HACKLAB")                Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "q.kennedy", Domain = "HACKLAB")              Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "u.king", Domain = "HACKLAB")                 Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "r.knight", Domain = "HACKLAB")               Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "s.lawrence", Domain = "HACKLAB")             Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "t.lee", Domain = "HACKLAB")                  Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "u.lewis", Domain = "HACKLAB")                Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "v.lloyd", Domain = "HACKLAB")                Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "w.marshall", Domain = "HACKLAB")             Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "x.martin", Domain = "HACKLAB")               Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "y.mason", Domain = "HACKLAB")                Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "g.dell", Domain = "HACKLAB")                 Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "h.osborne", Domain = "HACKLAB")              Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "i.owen", Domain = "HACKLAB")                 Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "j.oxley", Domain = "HACKLAB")                Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "k.page", Domain = "HACKLAB")                 Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "l.painter", Domain = "HACKLAB")              Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "m.palmer", Domain = "HACKLAB")               Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "n.pastor", Domain = "HACKLAB")               Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "o.peterson", Domain = "HACKLAB")             Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "p.quill", Domain = "HACKLAB")                Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "q.quimby", Domain = "HACKLAB")               Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "u.quintrell", Domain = "HACKLAB")            Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "r.ramsey", Domain = "HACKLAB")               Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "s.ratliff", Domain = "HACKLAB")              Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "t.richards", Domain = "HACKLAB")             Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "u.roberts", Domain = "HACKLAB")              Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "v.robinson", Domain = "HACKLAB")             Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "w.scott", Domain = "HACKLAB")                Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "x.simpson", Domain = "HACKLAB")              Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "y.smith", Domain = "HACKLAB")                Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "z.stewart", Domain = "HACKLAB")              Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "a.taylor", Domain = "HACKLAB")               Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "b.turner", Domain = "HACKLAB")               Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "c.walsh", Domain = "HACKLAB")                Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "d.ward", Domain = "HACKLAB")                 Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "e.webb", Domain = "HACKLAB")                 Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "f.west", Domain = "HACKLAB")                 Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "d.atkinson", Domain = "HACKLAB")             Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "e.bailey", Domain = "HACKLAB")               Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "f.baker", Domain = "HACKLAB")                Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "g.ball", Domain = "HACKLAB")                 Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "h.bell", Domain = "HACKLAB")                 Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "i.brown", Domain = "HACKLAB")                Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "j.burton", Domain = "HACKLAB")               Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "k.carter", Domain = "HACKLAB")               Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "l.clarke", Domain = "HACKLAB")               Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "m.cole", Domain = "HACKLAB")                 Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "e.griffiths", Domain = "HACKLAB")            Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "f.hall", Domain = "HACKLAB")                 Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "g.hamilton", Domain = "HACKLAB")             Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "h.harris", Domain = "HACKLAB")               Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "i.harvey", Domain = "HACKLAB")               Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "j.hill", Domain = "HACKLAB")                 Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "k.jackson", Domain = "HACKLAB")              Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "l.james", Domain = "HACKLAB")                Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "k.yarrow", Domain = "HACKLAB")               Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "l.yates", Domain = "HACKLAB")                Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "m.young", Domain = "HACKLAB")                Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "n.zachary", Domain = "HACKLAB")              Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "o.zelly", Domain = "HACKLAB")                Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "p.zinc", Domain = "HACKLAB")                 Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "q.zouch", Domain = "HACKLAB")                Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "a.adams", Domain = "HACKLAB")                Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "b.allen", Domain = "HACKLAB")                Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "c.armstrong", Domain = "HACKLAB")            Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "adm.adams", Domain = "HACKLAB")              Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "adm.smith", Domain = "HACKLAB")              Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "adm.stewart", Domain = "HACKLAB")            Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "adm.natt", Domain = "HACKLAB")               Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "adm.nelson", Domain = "HACKLAB")             Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "svc_afds", Domain = "HACKLAB")               Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "svc_test", Domain = "HACKLAB")               Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "svc_mssql1", Domain = "HACKLAB")             Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "svc_mssql2", Domain = "HACKLAB")             Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "svc_lab", Domain = "HACKLAB")                Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "svc_admin", Domain = "HACKLAB")              Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "DA1", Domain = "HACKLAB")                    Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "user1", Domain = "HACKLAB")                  Win32_Desktop (Name = "HACKLAB\user1")
Win32_UserAccount (Name = "user2", Domain = "HACKLAB")                  Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "user3", Domain = "HACKLAB")                  Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "test2", Domain = "HACKLAB")                  Win32_Desktop (Name = ".DEFAULT")
Win32_UserAccount (Name = "user4", Domain = "HACKLAB")                  Win32_Desktop (Name = ".DEFAULT")
```
### Enumerate_domain_users_win32_userindomain
Complete Syntax:
```

Get-CimInstance -ClassName Win32_UserInDomain | Format-Table -AutoSize

```
Result:
```

PS C:\Users\User1> Get-CimInstance -ClassName Win32_UserInDomain | Format-Table -AutoSize

GroupComponent                            PartComponent                                                  PSComputerName
--------------                            -------------                                                  --------------
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "Administrator", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "Guest", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "krbtgt", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "n.collins", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "o.davidson", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "p.davies", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "q.dawson", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "u.dixon", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "r.edwards", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "s.elliot", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "t.evans", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "u.fisher", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "v.fletcher", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "w.ford", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "x.foster", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "y.fox", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "z.gibson", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "a.graham", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "b.grant", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "c.gray", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "d.green", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "m.jenkins", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "n.johnson", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "o.jones", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "g.white", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "h.yalden", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "i.yarbury", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "j.yardley", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "z.mcdonald", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "a.murphy", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "b.natt", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "c.nelson", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "d.nightingale", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "e.nixon", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "f.nutter", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "p.kelly", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "q.kennedy", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "u.king", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "r.knight", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "s.lawrence", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "t.lee", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "u.lewis", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "v.lloyd", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "w.marshall", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "x.martin", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "y.mason", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "g.dell", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "h.osborne", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "i.owen", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "j.oxley", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "k.page", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "l.painter", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "m.palmer", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "n.pastor", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "o.peterson", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "p.quill", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "q.quimby", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "u.quintrell", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "r.ramsey", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "s.ratliff", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "t.richards", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "u.roberts", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "v.robinson", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "w.scott", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "x.simpson", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "y.smith", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "z.stewart", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "a.taylor", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "b.turner", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "c.walsh", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "d.ward", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "e.webb", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "f.west", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "d.atkinson", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "e.bailey", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "f.baker", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "g.ball", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "h.bell", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "i.brown", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "j.burton", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "k.carter", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "l.clarke", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "m.cole", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "e.griffiths", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "f.hall", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "g.hamilton", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "h.harris", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "i.harvey", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "j.hill", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "k.jackson", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "l.james", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "k.yarrow", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "l.yates", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "m.young", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "n.zachary", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "o.zelly", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "p.zinc", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "q.zouch", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "a.adams", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "b.allen", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "c.armstrong", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "adm.adams", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "adm.smith", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "adm.stewart", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "adm.natt", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "adm.nelson", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "svc_afds", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "svc_test", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "svc_mssql1", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "svc_mssql2", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "svc_lab", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "svc_admin", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "DA1", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "user1", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "user2", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "user3", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "test2", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "user4", Domain = "HACKLAB")
```

### Enumerate_if_WINRM_is_enabled_on_a_remote_host
```
Test-NetConnection -ComputerName LAB_WIN11_1 -Port 5985

PS C:\Users\User1> Test-NetConnection -ComputerName LAB_WIN11_1 -Port 5985
WARNING: TCP connect to (192.168.68.114 : 5985) failed


ComputerName           : LAB_WIN11_1
RemoteAddress          : 192.168.68.114
RemotePort             : 5985
InterfaceAlias         : Ethernet0
SourceAddress          : 192.168.68.113
PingSucceeded          : True
PingReplyDetails (RTT) : 3 ms
TcpTestSucceeded       : False
```

Trying to enable WINRM as a domain user
```
PS C:\Users\User1> winrm quickconfig
WinRM is not set up to receive requests on this machine.
The following changes must be made:

Start the WinRM service.
Set the WinRM service type to delayed auto start.

Make these changes [y/n]? y

Error: One or more update steps could not be completed.

Could not change the WinRM service type: Access is denied.
Could not start the WinRM service: Access is denied.
WSManFault
    Message = The client cannot connect to the destination specified in the request. Verify that the service on the destination is running and is accepting requests. Consult the logs and documentation for the WS-Management service running on the destination, most commonly IIS or WinRM. If the destination is the WinRM service, run the following command on the destination to analyze and configure the WinRM service: "winrm quickconfig".

Error number:  -2144108526 0x80338012
The client cannot connect to the destination specified in the request. Verify that the service on the destination is running and is accepting requests. Consult the logs and documentation for the WS-Management service running on the destination, most commonly IIS or WinRM. If the destination is the WinRM service, run the following command on the destination to analyze and configure the WinRM service: "winrm quickconfig".
```

Trying to enable WINRM as a member of domain admins group

```
PS C:\Windows\system32> winrm quickconfig
WinRM is not set up to receive requests on this machine.
The following changes must be made:

Start the WinRM service.
Set the WinRM service type to delayed auto start.

Make these changes [y/n]? y

WinRM has been updated to receive requests.

WinRM service type changed successfully.
WinRM service started.
WinRM is not set up to allow remote access to this machine for management.
The following changes must be made:

Enable the WinRM firewall exception.

Make these changes [y/n]? y

WinRM has been updated for remote management.

WinRM firewall exception enabled.
PS C:\Windows\system32>
```

Testing if WINRM is enabled on remote host
```
PS C:\Users\User1> Test-NetConnection -ComputerName LAB_WIN11_1 -Port 5985


ComputerName     : LAB_WIN11_1
RemoteAddress    : 192.168.68.114
RemotePort       : 5985
InterfaceAlias   : Ethernet0
SourceAddress    : 192.168.68.113
TcpTestSucceeded : True
```

Running a remote WINRM request for all local shares with a domain user after enabling WINRM on the remote host
```
PS C:\Users\User1> Get-CimInstance -ClassName Win32_Share -ComputerName LAB_WIN11_1
Get-CimInstance : Access is denied.
At line:1 char:1
+ Get-CimInstance -ClassName Win32_Share -ComputerName LAB_WIN11_1
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : PermissionDenied: (root\cimv2:Win32_Share:String) [Get-CimInstance], CimException
    + FullyQualifiedErrorId : HRESULT 0x80070005,Microsoft.Management.Infrastructure.CimCmdlets.GetCimInstanceCommand
    + PSComputerName        : LAB_WIN11_1
```
Same as above but with an account that a member of domain admins group
```
PS C:\Windows\system32> Get-CimInstance -ClassName Win32_Share -ComputerName LAB_WIN11_1

Name   Path                        Description   PSComputerName
----   ----                        -----------   --------------
ADMIN$ C:\Windows                  Remote Admin  LAB_WIN11_1
C$     C:\                         Default share LAB_WIN11_1
IPC$                               Remote IPC    LAB_WIN11_1
Test   C:\Users\User1\Desktop\Test               LAB_WIN11_1
Users  C:\Users                                  LAB_WIN11_1
Work   C:\Shares\Work                            LAB_WIN11_1
Work2  C:\Shares\Work                            LAB_WIN11_1
```

### File_enumeration_CIM_logicalelement
Records every file on a local host, looks to detail every aspect of the machine, drivers, IP address everything, produces a large output file size!!

Complete Syntax:
```

CIM_LogicalElement

```
Result:
```

Get-CimInstance -ClassName CIM_LogicalElement | Format-Table -AutoSize
```
### Local_shares_win32_share

You can use the "Get-CimInstance" cmdlet with the "Win32_Share" class to retrieve information about the shared resources on a computer. Here's an example command to list all the shares on the local computer:
```
PS C:\Users\User1> Get-CimInstance -ClassName Win32_Share

Name   Path                        Description
----   ----                        -----------
ADMIN$ C:\Windows                  Remote Admin
C$     C:\                         Default share
IPC$                               Remote IPC
Test   C:\Users\User1\Desktop\Test
Users  C:\Users
Work   C:\Shares\Work
Work2  C:\Shares\Work
```
This command retrieves information about all the shared resources on the local computer, including information such as the name of the share, the path of the shared folder, the description of the share, and the type of share (e.g. disk, printer, IPC).

You can also use the "-Filter" parameter to filter the results to show only shares that meet specific criteria. For example, to show only the shares that are currently in use, you can use the following command:
```
PS C:\Users\User1> Get-CimInstance -ClassName Win32_Share -Filter "Type='0'"

Name  Path                        Description
----  ----                        -----------
Test  C:\Users\User1\Desktop\Test
Users C:\Users
Work  C:\Shares\Work
Work2 C:\Shares\Work
```
This command retrieves information about all the disk shares that are currently in use on the local computer. The "Type" property of the "Win32_Share" class indicates the type of share, and a value of "0" represents a disk share.

You can also use the "Select-Object" cmdlet to display specific properties of the shared resources. For example, to display only the names and paths of the shared folders, you can use the following command:
```
PS C:\Users\User1> Get-CimInstance -ClassName Win32_Share | Select-Object Name, Path

Name   Path
----   ----
ADMIN$ C:\Windows
C$     C:\
IPC$
Test   C:\Users\User1\Desktop\Test
Users  C:\Users
Work   C:\Shares\Work
Work2  C:\Shares\Work
To see if WINRM is enabled on a remote host
```

### TimeZone_and_hostname_win32_systemtimezone
Complete Syntax:
```

Get-CimInstance -ClassName Win32_SystemTimeZone | Format-Table -AutoSize

```
Result:
```

PS C:\Users\User1> Get-CimInstance -ClassName Win32_SystemTimeZone | Format-Table -AutoSize

Element                                     Setting                                             PSComputerName
-------                                     -------                                             --------------
Win32_ComputerSystem (Name = "LAB-WIN11-2") Win32_TimeZone (StandardName = "GMT Standard Time")
```
### MS_JScript_Enumerate_all_domain_host_machines
Enumerate All Domain Host Machines
This code retrieves the domain name of the currently logged-in user using the WScript.Network object. The domain name is then used to construct the LDAP path for the adsisearcher query.

The ADODB.Connection object is created without specifying a username or password, which allows the context of the currently logged-in user to be used.
The rest of the code is like the previous example, with the ADODB.Command object used to execute the adsisearcher query and retrieve the results.

Complete Syntax:
```

var network = new ActiveXObject('WScript.Network');
var domainName = network.UserDomain;
var searcher = new ActiveXObject('ADODB.Connection');
searcher.Provider = 'ADsDSOObject';
searcher.Properties('Encrypt Password') = true;
searcher.Open('ADs Provider');

var command = new ActiveXObject('ADODB.Command');
command.ActiveConnection = searcher;
command.CommandText = '<LDAP://' + domainName + '>;(&(objectCategory=computer));adspath,name,distinguishedname;';
var results = command.Execute();

while (!results.EOF) {
    var computer = results.Fields('name').Value;
    WScript.Echo(computer);
    results.MoveNext();
}

searcher.Close();

```

Result:
```

PS C:\Users\User1\Documents> cscript.exe //nologo .\Test3.js
WIN-CH4HJ7UU7Q5
LAB_WIN11_1
LAB-WIN11-2
```

You can output the results to text file.
```
PS C:\Users\User1\Documents> cscript.exe //nologo .\Test3.js > output1.txt
```
### MS_JScript_IPCONFIG

This code creates an instance of the WScript.Shell object, which provides access to the Windows command shell. The Exec method of the WScript.Shell object is used to execute the ipconfig command.

The StdOut and StdErr properties of the WshScriptExec object returned by the Exec method are used to capture the output and error streams of the command. The output is stored in the stdout variable, and any error messages are stored

Complete Syntax:
```

var shell = WScript.CreateObject('WScript.Shell');
var exec = shell.Exec('ipconfig');
var stdout = '';
var stderr = '';

while (!exec.StdOut.AtEndOfStream) {
    stdout += exec.StdOut.ReadLine() + '\n';
}

while (!exec.StdErr.AtEndOfStream) {
    stderr += exec.StdErr.ReadLine() + '\n';
}

if (stderr) {
    WScript.Echo('Error executing ipconfig: ' + stderr);
} else {
    WScript.Echo(stdout);
}

```
Result:
```

PS C:\Users\User1\Documents> cscript.exe .\Test1.js
Microsoft (R) Windows Script Host Version 5.812
Copyright (C) Microsoft Corporation. All rights reserved.


Windows IP Configuration


Ethernet adapter Ethernet0:

   Connection-specific DNS Suffix  . :
   Link-local IPv6 Address . . . . . : fe80::eda2:cd55:404e:75cd%5
   IPv4 Address. . . . . . . . . . . : 192.168.68.116
   Subnet Mask . . . . . . . . . . . : 255.255.255.0
   Default Gateway . . . . . . . . . : fe80::e6c3:2aff:fe0e:97d0%5
                                       192.168.68.1
```

### WMI_creds_enumerate_domain_accounts_with_a_description_that_references_pass

Complete Syntax:
```

Get-WmiObject -Class win32_useraccount -Filter "LocalAccount=False AND Description LIKE '%pass%'" | Select-Object Name, AccountType, Disabled, Description

```
Result:
```

PS C:\Users\User1> Get-WmiObject -Class win32_useraccount -Filter "LocalAccount=False AND Description LIKE '%pass%'" | Select-Object Name, AccountType, Disabled, Description

Name       AccountType Disabled Description
----       ----------- -------- -----------
d.atkinson         512    False User Password Summer123


PS C:\Users\User1>
```

### WMI_domain_user_accounts_with_password_set_to_can’t_expire
Complete Syntax:
```

Get-WmiObject -Class "Win32_UserAccount" -Filter "PasswordExpires=False AND LocalAccount=False" | Select-Object -Property Name, Domain, SID

```
Result:
```

PS C:\Users\User1> Get-WmiObject -Class "Win32_UserAccount" -Filter "PasswordExpires=False AND LocalAccount=False" | Select-Object -Property Name, Domain, SID

Name          Domain  SID
----          ------  ---
Administrator HACKLAB S-1-5-21-2616623822-1553658618-2628931692-500
Guest         HACKLAB S-1-5-21-2616623822-1553658618-2628931692-501
q.dawson      HACKLAB S-1-5-21-2616623822-1553658618-2628931692-1112
DA1           HACKLAB S-1-5-21-2616623822-1553658618-2628931692-1251
user1         HACKLAB S-1-5-21-2616623822-1553658618-2628931692-1252
user2         HACKLAB S-1-5-21-2616623822-1553658618-2628931692-1255
test2         HACKLAB S-1-5-21-2616623822-1553658618-2628931692-1259
user4         HACKLAB S-1-5-21-2616623822-1553658618-2628931692-1260
```

### WMI_domain_user_accounts_with_password_set_to_can’t_expire_or_be_changed

Complete Syntax:
```

Get-WmiObject -Class "Win32_UserAccount" -Filter "PasswordExpires=False AND PasswordChangeable=False AND LocalAccount=False" | Select-Object -Property Name, Domain, SID

```
Result:
```

PS C:\Users\User1> Get-WmiObject -Class "Win32_UserAccount" -Filter "PasswordExpires=False AND PasswordChangeable=False AND LocalAccount=False" | Select-Object -Property Name, Domain, SID

Name     Domain  SID
----     ------  ---
q.dawson HACKLAB S-1-5-21-2616623822-1553658618-2628931692-1112
user2    HACKLAB S-1-5-21-2616623822-1553658618-2628931692-1255
test2    HACKLAB S-1-5-21-2616623822-1553658618-2628931692-1259
user4    HACKLAB S-1-5-21-2616623822-1553658618-2628931692-1260
```
### WMI_enumerate_all_domain_groups
Complete Syntax:
```

Get-CimInstance -ClassName Win32_GroupInDomain

```
Result:
```

PS C:\Users\user3> Get-CimInstance -ClassName Win32_GroupInDomain

GroupComponent                            PartComponent                                                                        PSComputerName
--------------                            -------------                                                                        --------------
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_Group (Name = "Cert Publishers", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_Group (Name = "RAS and IAS Servers", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_Group (Name = "Allowed RODC Password Replication Group..., Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_Group (Name = "Denied RODC Password Replication Group", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_Group (Name = "DnsAdmins", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_Group (Name = "accounts", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_Group (Name = "administration", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_Group (Name = "Cloneable Domain Controllers", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_Group (Name = "DnsUpdateProxy", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_Group (Name = "Domain Admins", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_Group (Name = "Domain Computers", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_Group (Name = "Domain Controllers", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_Group (Name = "Domain Guests", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_Group (Name = "Domain Users", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_Group (Name = "Enterprise Admins", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_Group (Name = "Enterprise Key Admins", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_Group (Name = "Enterprise Read-only Domain Controllers..., Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_Group (Name = "Group Policy Creator Owners", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_Group (Name = "help_desk", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_Group (Name = "Key Admins", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_Group (Name = "Protected Users", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_Group (Name = "RDP", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_Group (Name = "Read-only Domain Controllers", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_Group (Name = "sales", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_Group (Name = "Schema Admins", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_Group (Name = "support", Domain = "HACKLAB")
```

### WMI_enumerate_all_domain_users_and_their_group_membership

Complete Syntax:
```

Get-CimInstance -ClassName Win32_GroupUser

```
Result:
```

PS C:\Users\user3> Get-CimInstance -ClassName Win32_GroupUser

GroupComponent                                                               PartComponent                                                              PSComputerName
--------------                                                               -------------                                                              --------------
Win32_Group (Name = "Administrators", Domain = "LAB-WIN11-2")                Win32_UserAccount (Name = "Administrator", Domain = "LAB-WIN11-2")
Win32_Group (Name = "Administrators", Domain = "LAB-WIN11-2")                Win32_UserAccount (Name = "ieuser", Domain = "LAB-WIN11-2")
Win32_Group (Name = "Administrators", Domain = "LAB-WIN11-2")                Win32_Group (Name = "Domain Admins", Domain = "HACKLAB")
Win32_Group (Name = "Guests", Domain = "LAB-WIN11-2")                        Win32_UserAccount (Name = "Guest", Domain = "LAB-WIN11-2")
Win32_Group (Name = "IIS_IUSRS", Domain = "LAB-WIN11-2")                     Win32_SystemAccount (Name = "IUSR", Domain = "LAB-WIN11-2")
Win32_Group (Name = "System Managed Accounts Group", Domain = "LAB-WIN11-2") Win32_UserAccount (Name = "DefaultAccount", Domain = "LAB-WIN11-2")
Win32_Group (Name = "Users", Domain = "LAB-WIN11-2")                         Win32_SystemAccount (Name = "INTERACTIVE", Domain = "LAB-WIN11-2")
Win32_Group (Name = "Users", Domain = "LAB-WIN11-2")                         Win32_SystemAccount (Name = "Authenticated Users", Domain = "LAB-WIN11-2")
Win32_Group (Name = "Users", Domain = "LAB-WIN11-2")                         Win32_Group (Name = "Domain Users", Domain = "HACKLAB")
Win32_Group (Name = "Domain Admins", Domain = "HACKLAB")                     Win32_UserAccount (Name = "Administrator", Domain = "HACKLAB")
Win32_Group (Name = "Domain Admins", Domain = "HACKLAB")                     Win32_UserAccount (Name = "adm.adams", Domain = "HACKLAB")
Win32_Group (Name = "Domain Admins", Domain = "HACKLAB")                     Win32_UserAccount (Name = "adm.smith", Domain = "HACKLAB")
Win32_Group (Name = "Domain Admins", Domain = "HACKLAB")                     Win32_UserAccount (Name = "adm.stewart", Domain = "HACKLAB")
Win32_Group (Name = "Domain Admins", Domain = "HACKLAB")                     Win32_UserAccount (Name = "adm.natt", Domain = "HACKLAB")
Win32_Group (Name = "Domain Admins", Domain = "HACKLAB")                     Win32_UserAccount (Name = "adm.nelson", Domain = "HACKLAB")
Win32_Group (Name = "Domain Admins", Domain = "HACKLAB")                     Win32_UserAccount (Name = "svc_afds", Domain = "HACKLAB")
Win32_Group (Name = "Domain Admins", Domain = "HACKLAB")                     Win32_UserAccount (Name = "svc_test", Domain = "HACKLAB")
Win32_Group (Name = "Domain Admins", Domain = "HACKLAB")                     Win32_UserAccount (Name = "svc_mssql1", Domain = "HACKLAB")
Win32_Group (Name = "Domain Admins", Domain = "HACKLAB")                     Win32_UserAccount (Name = "svc_mssql2", Domain = "HACKLAB")
Win32_Group (Name = "Domain Admins", Domain = "HACKLAB")                     Win32_UserAccount (Name = "svc_lab", Domain = "HACKLAB")
Win32_Group (Name = "Domain Admins", Domain = "HACKLAB")                     Win32_UserAccount (Name = "svc_admin", Domain = "HACKLAB")
Win32_Group (Name = "Domain Admins", Domain = "HACKLAB")                     Win32_UserAccount (Name = "DA1", Domain = "HACKLAB")
Win32_Group (Name = "Domain Computers", Domain = "HACKLAB")                  Win32_UserAccount (Name = "LAB_WIN11_1?", Domain = "HACKLAB")
Win32_Group (Name = "Domain Computers", Domain = "HACKLAB")                  Win32_UserAccount (Name = "LAB-WIN11-2?", Domain = "HACKLAB")
Win32_Group (Name = "Domain Controllers", Domain = "HACKLAB")                Win32_UserAccount (Name = "WIN-CH4HJ7UU7Q5?", Domain = "HACKLAB")
Win32_Group (Name = "Domain Guests", Domain = "HACKLAB")                     Win32_UserAccount (Name = "Guest", Domain = "HACKLAB")
Win32_Group (Name = "Domain Guests", Domain = "HACKLAB")                     Win32_UserAccount (Name = "test2", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "Administrator", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "krbtgt", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "n.collins", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "o.davidson", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "p.davies", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "q.dawson", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "u.dixon", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "r.edwards", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "s.elliot", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "t.evans", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "u.fisher", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "v.fletcher", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "w.ford", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "x.foster", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "y.fox", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "z.gibson", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "a.graham", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "b.grant", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "c.gray", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "d.green", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "m.jenkins", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "n.johnson", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "o.jones", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "g.white", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "h.yalden", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "i.yarbury", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "j.yardley", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "z.mcdonald", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "a.murphy", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "b.natt", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "c.nelson", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "d.nightingale", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "e.nixon", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "f.nutter", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "p.kelly", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "q.kennedy", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "u.king", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "r.knight", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "s.lawrence", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "t.lee", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "u.lewis", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "v.lloyd", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "w.marshall", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "x.martin", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "y.mason", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "g.dell", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "h.osborne", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "i.owen", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "j.oxley", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "k.page", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "l.painter", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "m.palmer", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "n.pastor", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "o.peterson", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "p.quill", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "q.quimby", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "u.quintrell", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "r.ramsey", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "s.ratliff", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "t.richards", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "u.roberts", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "v.robinson", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "w.scott", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "x.simpson", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "y.smith", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "z.stewart", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "a.taylor", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "b.turner", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "c.walsh", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "d.ward", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "e.webb", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "f.west", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "d.atkinson", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "e.bailey", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "f.baker", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "g.ball", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "h.bell", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "i.brown", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "j.burton", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "k.carter", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "l.clarke", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "m.cole", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "e.griffiths", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "f.hall", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "g.hamilton", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "h.harris", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "i.harvey", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "j.hill", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "k.jackson", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "l.james", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "k.yarrow", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "l.yates", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "m.young", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "n.zachary", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "o.zelly", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "p.zinc", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "q.zouch", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "a.adams", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "b.allen", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "c.armstrong", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "adm.adams", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "adm.smith", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "adm.stewart", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "adm.natt", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "adm.nelson", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "svc_afds", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "svc_test", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "svc_mssql1", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "svc_mssql2", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "svc_lab", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "svc_admin", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "DA1", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "user1", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "user2", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "user3", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "user4", Domain = "HACKLAB")
Win32_Group (Name = "Enterprise Admins", Domain = "HACKLAB")                 Win32_UserAccount (Name = "Administrator", Domain = "HACKLAB")
Win32_Group (Name = "Group Policy Creator Owners", Domain = "HACKLAB")       Win32_UserAccount (Name = "Administrator", Domain = "HACKLAB")
Win32_Group (Name = "Schema Admins", Domain = "HACKLAB")                     Win32_UserAccount (Name = "Administrator", Domain = "HACKLAB")


PS C:\Users\user3>
```

### WMI_enumerate_all_users_accounts_set_as_disabled
Complete Syntax:
```

Get-CimInstance -ClassName Win32_UserAccount | Select-Object -Property name, Disabled

```
Result:
```

PS C:\Users\User1> Get-CimInstance -ClassName Win32_UserAccount | Select-Object -Property name, Disabled

name               Disabled
----               --------
Administrator          True
DefaultAccount         True
Guest                  True
ieuser                False
WDAGUtilityAccount     True
Administrator         False
Guest                  True
krbtgt                 True
n.collins             False
o.davidson            False
p.davies              False
q.dawson              False
u.dixon               False
r.edwards             False
s.elliot              False
t.evans               False
u.fisher              False
v.fletcher            False
w.ford                False
x.foster              False
y.fox                 False
z.gibson              False
a.graham              False
b.grant               False
c.gray                False
d.green               False
m.jenkins             False
n.johnson             False
o.jones               False
g.white               False
h.yalden              False
i.yarbury             False
j.yardley             False
z.mcdonald            False
a.murphy              False
b.natt                False
c.nelson              False
d.nightingale         False
e.nixon               False
f.nutter              False
p.kelly               False
q.kennedy             False
u.king                False
r.knight              False
s.lawrence            False
t.lee                 False
u.lewis               False
v.lloyd               False
w.marshall            False
x.martin              False
y.mason               False
g.dell                False
h.osborne             False
i.owen                False
j.oxley               False
k.page                False
l.painter             False
m.palmer              False
n.pastor              False
o.peterson            False
p.quill               False
q.quimby              False
u.quintrell           False
r.ramsey              False
s.ratliff             False
t.richards            False
u.roberts             False
v.robinson            False
w.scott               False
x.simpson             False
y.smith               False
z.stewart             False
a.taylor              False
b.turner              False
c.walsh               False
d.ward                False
e.webb                False
f.west                False
d.atkinson            False
e.bailey              False
f.baker               False
g.ball                False
h.bell                False
i.brown               False
j.burton              False
k.carter              False
l.clarke              False
m.cole                False
e.griffiths           False
f.hall                False
g.hamilton            False
h.harris              False
i.harvey              False
j.hill                False
k.jackson             False
l.james               False
k.yarrow              False
l.yates               False
m.young               False
n.zachary             False
o.zelly               False
p.zinc                False
q.zouch               False
a.adams               False
b.allen               False
c.armstrong           False
adm.adams             False
adm.smith             False
adm.stewart           False
adm.natt              False
adm.nelson            False
svc_afds              False
svc_test              False
svc_mssql1            False
svc_mssql2            False
svc_lab               False
svc_admin             False
DA1                   False
user1                 False
user2                 False
user3                 False
test2                 False
user4                 False
```

### WMI_enumerate_all_users_that_have_the_Description_attribute_set_and_return_the_contents_of_the_description
Complete Syntax:
```

Get-CimInstance -ClassName Win32_UserAccount | Select-Object -Property name, Description, Domain, SID

```
Result:
```

PS C:\Users\User1> Get-CimInstance -ClassName Win32_UserAccount | Select-Object -Property name, Description, Domain, SID

name               Description                                                                                     Domain      SID
----               -----------                                                                                     ------      ---
Administrator      Built-in account for administering the computer/domain                                          LAB_WIN11_1 S-1-5-21-989316335-961067246-357250816-500
DefaultAccount     A user account managed by the system.                                                           LAB_WIN11_1 S-1-5-21-989316335-961067246-357250816-503
Guest              Built-in account for guest access to the computer/domain                                        LAB_WIN11_1 S-1-5-21-989316335-961067246-357250816-501
ieuser                                                                                                             LAB_WIN11_1 S-1-5-21-989316335-961067246-357250816-1001
WDAGUtilityAccount A user account managed and used by the system for Windows Defender Application Guard scenarios. LAB_WIN11_1 S-1-5-21-989316335-961067246-357250816-504
Administrator      Built-in account for administering the computer/domain                                          HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-500
Guest              Built-in account for guest access to the computer/domain                                        HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-501
krbtgt             Key Distribution Center Service Account                                                         HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-502
n.collins                                                                                                          HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1109
o.davidson                                                                                                         HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1110
p.davies                                                                                                           HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1111
q.dawson                                                                                                           HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1112
u.dixon                                                                                                            HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1113
r.edwards                                                                                                          HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1114
s.elliot                                                                                                           HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1115
t.evans                                                                                                            HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1116
u.fisher                                                                                                           HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1117
v.fletcher                                                                                                         HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1118
w.ford                                                                                                             HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1119
x.foster                                                                                                           HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1120
y.fox                                                                                                              HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1121
z.gibson                                                                                                           HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1122
a.graham                                                                                                           HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1123
b.grant                                                                                                            HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1124
c.gray                                                                                                             HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1125
d.green                                                                                                            HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1126
m.jenkins                                                                                                          HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1127
n.johnson                                                                                                          HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1128
o.jones                                                                                                            HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1129
g.white                                                                                                            HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1130
h.yalden                                                                                                           HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1131
i.yarbury                                                                                                          HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1132
j.yardley                                                                                                          HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1133
z.mcdonald                                                                                                         HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1134
a.murphy                                                                                                           HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1135
b.natt                                                                                                             HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1136
c.nelson                                                                                                           HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1137
d.nightingale                                                                                                      HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1138
e.nixon                                                                                                            HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1139
f.nutter                                                                                                           HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1140
p.kelly                                                                                                            HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1141
q.kennedy                                                                                                          HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1142
u.king                                                                                                             HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1143
r.knight                                                                                                           HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1144
s.lawrence                                                                                                         HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1145
t.lee                                                                                                              HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1146
u.lewis                                                                                                            HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1147
v.lloyd                                                                                                            HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1148
w.marshall                                                                                                         HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1149
x.martin                                                                                                           HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1150
y.mason                                                                                                            HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1151
g.dell                                                                                                             HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1152
h.osborne                                                                                                          HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1153
i.owen                                                                                                             HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1154
j.oxley                                                                                                            HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1155
k.page                                                                                                             HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1156
l.painter                                                                                                          HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1157
m.palmer                                                                                                           HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1158
n.pastor                                                                                                           HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1159
o.peterson                                                                                                         HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1160
p.quill                                                                                                            HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1161
q.quimby                                                                                                           HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1162
u.quintrell                                                                                                        HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1163
r.ramsey                                                                                                           HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1164
s.ratliff                                                                                                          HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1165
t.richards                                                                                                         HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1166
u.roberts                                                                                                          HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1167
v.robinson                                                                                                         HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1168
w.scott                                                                                                            HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1169
x.simpson                                                                                                          HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1170
y.smith                                                                                                            HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1171
z.stewart                                                                                                          HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1172
a.taylor                                                                                                           HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1173
b.turner                                                                                                           HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1174
c.walsh                                                                                                            HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1175
d.ward                                                                                                             HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1176
e.webb                                                                                                             HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1177
f.west                                                                                                             HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1178
d.atkinson         User Password Summer123                                                                         HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1179
e.bailey                                                                                                           HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1180
f.baker                                                                                                            HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1181
g.ball                                                                                                             HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1182
h.bell                                                                                                             HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1183
i.brown                                                                                                            HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1184
j.burton                                                                                                           HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1185
k.carter                                                                                                           HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1186
l.clarke                                                                                                           HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1187
m.cole                                                                                                             HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1188
e.griffiths                                                                                                        HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1189
f.hall                                                                                                             HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1190
g.hamilton                                                                                                         HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1191
h.harris                                                                                                           HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1192
i.harvey                                                                                                           HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1193
j.hill                                                                                                             HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1194
k.jackson                                                                                                          HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1195
l.james                                                                                                            HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1196
k.yarrow                                                                                                           HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1197
l.yates                                                                                                            HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1198
m.young                                                                                                            HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1199
n.zachary                                                                                                          HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1200
o.zelly                                                                                                            HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1201
p.zinc                                                                                                             HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1202
q.zouch                                                                                                            HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1203
a.adams                                                                                                            HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1204
b.allen                                                                                                            HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1205
c.armstrong                                                                                                        HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1206
adm.adams                                                                                                          HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1207
adm.smith                                                                                                          HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1208
adm.stewart                                                                                                        HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1209
adm.natt                                                                                                           HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1210
adm.nelson                                                                                                         HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1211
svc_afds                                                                                                           HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1212
svc_test                                                                                                           HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1213
svc_mssql1                                                                                                         HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1214
svc_mssql2                                                                                                         HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1215
svc_lab                                                                                                            HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1216
svc_admin                                                                                                          HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1217
DA1                                                                                                                HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1251
user1                                                                                                              HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1252
user2                                                                                                              HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1255
user3                                                                                                              HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1257
test2                                                                                                              HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1259
user4                                                                                                              HACKLAB     S-1-5-21-2616623822-1553658618-2628931692-1260
```

### WMI_enumerate_description_method_three
This is the best, it only returns accounts with a description filed that’s been filled in.

Complete Syntax:
```

Get-WmiObject -Class win32_useraccount -Filter "LocalAccount=False AND Description != ''" | Select-Object Name, AccountType, Disabled, Description

```
Result:
```

PS C:\Users\User1> Get-WmiObject -Class win32_useraccount -Filter "LocalAccount=False AND Description != ''" | Select-Object Name, AccountType, Disabled, Description

Name          AccountType Disabled Description
----          ----------- -------- -----------
Administrator         512    False Built-in account for administering the computer/domain
Guest                 512     True Built-in account for guest access to the computer/domain
krbtgt                512     True Key Distribution Center Service Account
d.atkinson            512    False User Password Summer123


PS C:\Users\User1>
```
### WMI_enumerate_description_method_two
Complete Syntax:
```

Get-WmiObject -Class win32_useraccount -Filter “LocalAccount=False” | Select-Object Name, AccountType, Disabled, Description

```
Result:
```

PS C:\Users\User1> Get-WmiObject -Class win32_useraccount -Filter “LocalAccount=False” | Select-Object Name, AccountType, Disabled, Description

Name          AccountType Disabled Description
----          ----------- -------- -----------
Administrator         512    False Built-in account for administering the computer/domain
Guest                 512     True Built-in account for guest access to the computer/domain
krbtgt                512     True Key Distribution Center Service Account
```

Complete Syntax:
```

Get-WmiObject -Class win32_useraccount -Filter “LocalAccount=False” | Select-Object Name, AccountType, Disabled, Description, SID

```
Result:
```

PS C:\Users\User1> Get-WmiObject -Class win32_useraccount -Filter “LocalAccount=False” | Select-Object Name, AccountType, Disabled, Description, SID


Name        : Administrator
AccountType : 512
Disabled    : False
Description : Built-in account for administering the computer/domain
SID         : S-1-5-21-2616623822-1553658618-2628931692-500

Name        : Guest
AccountType : 512
Disabled    : True
Description : Built-in account for guest access to the computer/domain
SID         : S-1-5-21-2616623822-1553658618-2628931692-501

Name        : krbtgt
AccountType : 512
Disabled    : True
Description : Key Distribution Center Service Account
SID         : S-1-5-21-2616623822-1553658618-2628931692-502

Name        : n.collins
AccountType : 512
Disabled    : False
Description :
SID         : S-1-5-21-2616623822-1553658618-2628931692-1109

Name        : o.davidson
AccountType : 512
Disabled    : False
Description :
SID         : S-1-5-21-2616623822-1553658618-2628931692-1110
```
### WMI_enumerate_domain_controller

Complete Syntax:
```

Get-CimInstance -ClassName Win32_NTDomain

```
Result:
```

PS C:\Users\user3> Get-CimInstance -ClassName Win32_NTDomain

DomainName       DnsForestName                                                                                   DomainControllerName
----------       -------------                                                                                   --------------------

HACKLAB          hacklab.local                                                                                   \\WIN-CH4HJ7UU7Q5
```

### WMI_enumerate_domain_users
Complete Syntax:
```

Get-CimInstance -ClassName Win32_UserInDomain

```
Result:
```

PS C:\Users\user3> Get-CimInstance -ClassName Win32_UserInDomain

GroupComponent                            PartComponent                                                  PSComputerName
--------------                            -------------                                                  --------------
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "Administrator", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "Guest", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "krbtgt", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "n.collins", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "o.davidson", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "p.davies", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "q.dawson", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "u.dixon", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "r.edwards", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "s.elliot", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "t.evans", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "u.fisher", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "v.fletcher", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "w.ford", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "x.foster", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "y.fox", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "z.gibson", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "a.graham", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "b.grant", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "c.gray", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "d.green", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "m.jenkins", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "n.johnson", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "o.jones", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "g.white", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "h.yalden", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "i.yarbury", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "j.yardley", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "z.mcdonald", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "a.murphy", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "b.natt", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "c.nelson", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "d.nightingale", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "e.nixon", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "f.nutter", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "p.kelly", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "q.kennedy", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "u.king", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "r.knight", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "s.lawrence", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "t.lee", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "u.lewis", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "v.lloyd", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "w.marshall", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "x.martin", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "y.mason", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "g.dell", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "h.osborne", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "i.owen", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "j.oxley", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "k.page", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "l.painter", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "m.palmer", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "n.pastor", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "o.peterson", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "p.quill", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "q.quimby", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "u.quintrell", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "r.ramsey", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "s.ratliff", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "t.richards", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "u.roberts", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "v.robinson", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "w.scott", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "x.simpson", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "y.smith", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "z.stewart", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "a.taylor", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "b.turner", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "c.walsh", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "d.ward", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "e.webb", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "f.west", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "d.atkinson", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "e.bailey", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "f.baker", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "g.ball", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "h.bell", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "i.brown", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "j.burton", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "k.carter", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "l.clarke", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "m.cole", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "e.griffiths", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "f.hall", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "g.hamilton", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "h.harris", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "i.harvey", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "j.hill", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "k.jackson", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "l.james", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "k.yarrow", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "l.yates", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "m.young", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "n.zachary", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "o.zelly", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "p.zinc", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "q.zouch", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "a.adams", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "b.allen", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "c.armstrong", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "adm.adams", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "adm.smith", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "adm.stewart", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "adm.natt", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "adm.nelson", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "svc_afds", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "svc_test", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "svc_mssql1", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "svc_mssql2", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "svc_lab", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "svc_admin", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "DA1", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "user1", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "user2", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "user3", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "test2", Domain = "HACKLAB")
Win32_NTDomain (Name = "Domain: HACKLAB") Win32_UserAccount (Name = "user4", Domain = "HACKLAB")
```
### WMI_enumerate_only_domain_users
Complete Syntax:
```

Get-WmiObject -Class win32_useraccount -Filter “LocalAccount=False” | Select-Object Name, AccountType, Disabled

```
Result:
```

PS C:\Users\User1> Get-WmiObject -Class win32_useraccount -Filter “LocalAccount=False” | Select-Object Name, AccountType, Disabled

Name          AccountType Disabled
----          ----------- --------
Administrator         512    False
Guest                 512     True
krbtgt                512     True
n.collins             512    False
o.davidson            512    False
p.davies              512    False
q.dawson              512    False
u.dixon               512    False
r.edwards             512    False
s.elliot              512    False
t.evans               512    False
u.fisher              512    False
v.fletcher            512    False
w.ford                512    False
x.foster              512    False
y.fox                 512    False
z.gibson              512    False
a.graham              512    False
b.grant               512    False
c.gray                512    False
d.green               512    False
m.jenkins             512    False
n.johnson             512    False
o.jones               512    False
g.white               512    False
h.yalden              512    False
i.yarbury             512    False
j.yardley             512    False
z.mcdonald            512    False
a.murphy              512    False
b.natt                512    False
c.nelson              512    False
d.nightingale         512    False
e.nixon               512    False
f.nutter              512    False
p.kelly               512    False
q.kennedy             512    False
u.king                512    False
r.knight              512    False
s.lawrence            512    False
t.lee                 512    False
u.lewis               512    False
v.lloyd               512    False
w.marshall            512    False
x.martin              512    False
y.mason               512    False
g.dell                512    False
h.osborne             512    False
i.owen                512    False
j.oxley               512    False
k.page                512    False
l.painter             512    False
m.palmer              512    False
n.pastor              512    False
o.peterson            512    False
p.quill               512    False
q.quimby              512    False
u.quintrell           512    False
r.ramsey              512    False
s.ratliff             512    False
t.richards            512    False
u.roberts             512    False
v.robinson            512    False
w.scott               512    False
x.simpson             512    False
y.smith               512    False
z.stewart             512    False
a.taylor              512    False
b.turner              512    False
c.walsh               512    False
d.ward                512    False
e.webb                512    False
f.west                512    False
d.atkinson            512    False
e.bailey              512    False
f.baker               512    False
g.ball                512    False
h.bell                512    False
i.brown               512    False
j.burton              512    False
k.carter              512    False
l.clarke              512    False
m.cole                512    False
e.griffiths           512    False
f.hall                512    False
g.hamilton            512    False
h.harris              512    False
i.harvey              512    False
j.hill                512    False
k.jackson             512    False
l.james               512    False
k.yarrow              512    False
l.yates               512    False
m.young               512    False
n.zachary             512    False
o.zelly               512    False
p.zinc                512    False
q.zouch               512    False
a.adams               512    False
b.allen               512    False
c.armstrong           512    False
adm.adams             512    False
adm.smith             512    False
adm.stewart           512    False
adm.natt              512    False
adm.nelson            512    False
svc_afds              512    False
svc_test              512    False
svc_mssql1            512    False
svc_mssql2            512    False
svc_lab               512    False
svc_admin             512    False
DA1                   512    False
user1                 512    False
user2                 512    False
user3                 512    False
test2                 512    False
user4                 512    False
```
### WMI_hunting_for_domain_names
Hunting for domain names which contain SVC in the sam account name.

Complete Syntax:
```

Get-WmiObject -Class win32_useraccount -Filter "LocalAccount=False AND Name LIKE '%SVC%'"

```
Result:
```

PS C:\Users\User1> Get-WmiObject -Class win32_useraccount -Filter "LocalAccount=False AND Name LIKE '%SVC%'"


AccountType : 512
Caption     : HACKLAB\svc_afds
Domain      : HACKLAB
SID         : S-1-5-21-2616623822-1553658618-2628931692-1212
FullName    :
Name        : svc_afds

AccountType : 512
Caption     : HACKLAB\svc_test
Domain      : HACKLAB
SID         : S-1-5-21-2616623822-1553658618-2628931692-1213
FullName    :
Name        : svc_test

AccountType : 512
Caption     : HACKLAB\svc_mssql1
Domain      : HACKLAB
SID         : S-1-5-21-2616623822-1553658618-2628931692-1214
FullName    :
Name        : svc_mssql1
```
Hunting for accounts that contain jame in the sam account name.
```
PS C:\Users\User1> Get-WmiObject -Class win32_useraccount -Filter "LocalAccount=False AND Name LIKE '%jame%'"


AccountType : 512
Caption     : HACKLAB\l.james
Domain      : HACKLAB
SID         : S-1-5-21-2616623822-1553658618-2628931692-1196
FullName    :
Name        : l.james
```

### WMI Multiple Filters to enumerate disabled domain users

Complete Syntax:
```
Get-WmiObject -Class win32_useraccount -Filter “Disabled=True AND LocalAccount=False” | Select-Object Name, AccountType, Domain
```
Result:
```
PS C:\Users\User1> Get-WmiObject -Class win32_useraccount -Filter “Disabled=True AND LocalAccount=False” | Select-Object Name, AccountType, Domain

Name   AccountType Domain
----   ----------- ------
Guest          512 HACKLAB
krbtgt         512 HACKLAB
```

To return more information:
```
PS C:\Users\User1> Get-WmiObject -Class win32_useraccount -Filter “Disabled=True AND LocalAccount=False” | Select-Object Name, AccountType, Domain, Description, SID

Name        : Guest
AccountType : 512
Domain      : HACKLAB
Description : Built-in account for guest access to the computer/domain
SID         : S-1-5-21-2616623822-1553658618-2628931692-501

Name        : krbtgt
AccountType : 512
Domain      : HACKLAB
Description : Key Distribution Center Service Account
SID         : S-1-5-21-2616623822-1553658618-2628931692-502
```

### WMI Get-CimInstance

The "Get-CimInstance" cmdlet is used to retrieve information about a particular class of objects in the Windows Management Instrumentation (WMI) repository. It is part of the PowerShell CIM (Common Information Model) cmdlets, which provide a standardized way to manage Windows systems using CIM. 

Complete Syntax:
```
Get-CimInstance -ClassName CIM_System | Format-Table -AutoSize
```
Result:
```
PS C:\Users\User1> Get-CimInstance -ClassName CIM_System | Format-Table -AutoSize

Name        PrimaryOwnerName Domain        TotalPhysicalMemory Model     Manufacturer
----        ---------------- ------        ------------------- -----     ------------
LAB-WIN11-2 ieuser           hacklab.local 4193116160          VMware7,1 VMware, Inc.

ClientSiteName          :
DcSiteName              :
Description             : LAB-WIN11-2
DnsForestName           :
DomainControllerAddress :
DomainControllerName    :
DomainName              :
Roles                   :
Status                  : Unknown


ClientSiteName          : Default-First-Site-Name
DcSiteName              : Default-First-Site-Name
Description             : HACKLAB
DnsForestName           : hacklab.local
DomainControllerAddress : \\192.168.68.200
DomainControllerName    : \\WIN-CH4HJ7UU7Q5
DomainName              : HACKLAB
Roles                   :
Status                  : OK
```


Complete Syntax:
```
Get-CimInstance -ClassName Win32_NTDomain | Format-Table -AutoSize
```
Result:
```
PS C:\Users\User1> Get-CimInstance -ClassName Win32_NTDomain | Format-Table -AutoSize

DomainName DnsForestName DomainControllerName
---------- ------------- --------------------

HACKLAB    hacklab.local \\WIN-CH4HJ7UU7Q5
```

### Domain_controller_hostname_and_IP_address
```
Get-CimInstance -ClassName CIM_System | Format-Table -AutoSize
```

Result:
```
PS C:\Users\User1> Get-CimInstance -ClassName CIM_System | Format-Table -AutoSize

Name        PrimaryOwnerName Domain        TotalPhysicalMemory Model     Manufacturer
----        ---------------- ------        ------------------- -----     ------------
LAB-WIN11-2 ieuser           hacklab.local 4193116160          VMware7,1 VMware, Inc.

ClientSiteName          :
DcSiteName              :
Description             : LAB-WIN11-2
DnsForestName           :
DomainControllerAddress :
DomainControllerName    :
DomainName              :
Roles                   :
Status                  : Unknown


ClientSiteName          : Default-First-Site-Name
DcSiteName              : Default-First-Site-Name
Description             : HACKLAB
DnsForestName           : hacklab.local
DomainControllerAddress : \\192.168.68.200
DomainControllerName    : \\WIN-CH4HJ7UU7Q5
DomainName              : HACKLAB
Roles                   :
Status                  : OK
```

### Domain Name and NTDomain
Complete Syntax:
```
Get-CimInstance -ClassName Win32_NTDomain | Format-Table -AutoSize
```
Result:
```
PS C:\Users\User1> Get-CimInstance -ClassName Win32_NTDomain | Format-Table -AutoSize

DomainName DnsForestName DomainControllerName
---------- ------------- --------------------

HACKLAB    hacklab.local \\WIN-CH4HJ7UU7Q5
```
### Enumerate_domain_groups_users_and_computers_win32_groupuser
Complete Syntax:
```
Get-CimInstance -ClassName Win32_GroupUser | Format-Table -AutoSize
```

Result:
```
PS C:\Users\User1> Get-CimInstance -ClassName Win32_GroupUser | Format-Table -AutoSize

GroupComponent                                                               PartComponent                                                              PSComputerName
--------------                                                               -------------                                                              --------------
Win32_Group (Name = "Administrators", Domain = "LAB-WIN11-2")                Win32_UserAccount (Name = "Administrator", Domain = "LAB-WIN11-2")
Win32_Group (Name = "Administrators", Domain = "LAB-WIN11-2")                Win32_UserAccount (Name = "ieuser", Domain = "LAB-WIN11-2")
Win32_Group (Name = "Administrators", Domain = "LAB-WIN11-2")                Win32_Group (Name = "Domain Admins", Domain = "HACKLAB")
Win32_Group (Name = "Guests", Domain = "LAB-WIN11-2")                        Win32_UserAccount (Name = "Guest", Domain = "LAB-WIN11-2")
Win32_Group (Name = "IIS_IUSRS", Domain = "LAB-WIN11-2")                     Win32_SystemAccount (Name = "IUSR", Domain = "LAB-WIN11-2")
Win32_Group (Name = "System Managed Accounts Group", Domain = "LAB-WIN11-2") Win32_UserAccount (Name = "DefaultAccount", Domain = "LAB-WIN11-2")
Win32_Group (Name = "Users", Domain = "LAB-WIN11-2")                         Win32_SystemAccount (Name = "INTERACTIVE", Domain = "LAB-WIN11-2")
Win32_Group (Name = "Users", Domain = "LAB-WIN11-2")                         Win32_SystemAccount (Name = "Authenticated Users", Domain = "LAB-WIN11-2")
Win32_Group (Name = "Users", Domain = "LAB-WIN11-2")                         Win32_Group (Name = "Domain Users", Domain = "HACKLAB")
Win32_Group (Name = "Domain Admins", Domain = "HACKLAB")                     Win32_UserAccount (Name = "Administrator", Domain = "HACKLAB")
Win32_Group (Name = "Domain Admins", Domain = "HACKLAB")                     Win32_UserAccount (Name = "adm.adams", Domain = "HACKLAB")
Win32_Group (Name = "Domain Admins", Domain = "HACKLAB")                     Win32_UserAccount (Name = "adm.smith", Domain = "HACKLAB")
Win32_Group (Name = "Domain Admins", Domain = "HACKLAB")                     Win32_UserAccount (Name = "adm.stewart", Domain = "HACKLAB")
Win32_Group (Name = "Domain Admins", Domain = "HACKLAB")                     Win32_UserAccount (Name = "adm.natt", Domain = "HACKLAB")
Win32_Group (Name = "Domain Admins", Domain = "HACKLAB")                     Win32_UserAccount (Name = "adm.nelson", Domain = "HACKLAB")
Win32_Group (Name = "Domain Admins", Domain = "HACKLAB")                     Win32_UserAccount (Name = "svc_afds", Domain = "HACKLAB")
Win32_Group (Name = "Domain Admins", Domain = "HACKLAB")                     Win32_UserAccount (Name = "svc_test", Domain = "HACKLAB")
Win32_Group (Name = "Domain Admins", Domain = "HACKLAB")                     Win32_UserAccount (Name = "svc_mssql1", Domain = "HACKLAB")
Win32_Group (Name = "Domain Admins", Domain = "HACKLAB")                     Win32_UserAccount (Name = "svc_mssql2", Domain = "HACKLAB")
Win32_Group (Name = "Domain Admins", Domain = "HACKLAB")                     Win32_UserAccount (Name = "svc_lab", Domain = "HACKLAB")
Win32_Group (Name = "Domain Admins", Domain = "HACKLAB")                     Win32_UserAccount (Name = "svc_admin", Domain = "HACKLAB")
Win32_Group (Name = "Domain Admins", Domain = "HACKLAB")                     Win32_UserAccount (Name = "DA1", Domain = "HACKLAB")
Win32_Group (Name = "Domain Computers", Domain = "HACKLAB")                  Win32_UserAccount (Name = "LAB_WIN11_1?", Domain = "HACKLAB")
Win32_Group (Name = "Domain Computers", Domain = "HACKLAB")                  Win32_UserAccount (Name = "LAB-WIN11-2?", Domain = "HACKLAB")
Win32_Group (Name = "Domain Controllers", Domain = "HACKLAB")                Win32_UserAccount (Name = "WIN-CH4HJ7UU7Q5?", Domain = "HACKLAB")
Win32_Group (Name = "Domain Guests", Domain = "HACKLAB")                     Win32_UserAccount (Name = "Guest", Domain = "HACKLAB")
Win32_Group (Name = "Domain Guests", Domain = "HACKLAB")                     Win32_UserAccount (Name = "test2", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "Administrator", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "krbtgt", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "n.collins", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "o.davidson", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "p.davies", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "q.dawson", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "u.dixon", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "r.edwards", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "s.elliot", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "t.evans", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "u.fisher", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "v.fletcher", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "w.ford", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "x.foster", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "y.fox", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "z.gibson", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "a.graham", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "b.grant", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "c.gray", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "d.green", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "m.jenkins", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "n.johnson", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "o.jones", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "g.white", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "h.yalden", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "i.yarbury", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "j.yardley", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "z.mcdonald", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "a.murphy", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "b.natt", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "c.nelson", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "d.nightingale", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "e.nixon", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "f.nutter", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "p.kelly", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "q.kennedy", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "u.king", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "r.knight", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "s.lawrence", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "t.lee", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "u.lewis", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "v.lloyd", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "w.marshall", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "x.martin", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "y.mason", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "g.dell", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "h.osborne", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "i.owen", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "j.oxley", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "k.page", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "l.painter", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "m.palmer", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "n.pastor", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "o.peterson", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "p.quill", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "q.quimby", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "u.quintrell", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "r.ramsey", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "s.ratliff", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "t.richards", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "u.roberts", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "v.robinson", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "w.scott", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "x.simpson", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "y.smith", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "z.stewart", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "a.taylor", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "b.turner", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "c.walsh", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "d.ward", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "e.webb", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "f.west", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "d.atkinson", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "e.bailey", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "f.baker", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "g.ball", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "h.bell", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "i.brown", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "j.burton", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "k.carter", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "l.clarke", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "m.cole", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "e.griffiths", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "f.hall", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "g.hamilton", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "h.harris", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "i.harvey", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "j.hill", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "k.jackson", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "l.james", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "k.yarrow", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "l.yates", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "m.young", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "n.zachary", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "o.zelly", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "p.zinc", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "q.zouch", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "a.adams", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "b.allen", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "c.armstrong", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "adm.adams", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "adm.smith", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "adm.stewart", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "adm.natt", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "adm.nelson", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "svc_afds", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "svc_test", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "svc_mssql1", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "svc_mssql2", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "svc_lab", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "svc_admin", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "DA1", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "user1", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "user2", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "user3", Domain = "HACKLAB")
Win32_Group (Name = "Domain Users", Domain = "HACKLAB")                      Win32_UserAccount (Name = "user4", Domain = "HACKLAB")
Win32_Group (Name = "Enterprise Admins", Domain = "HACKLAB")                 Win32_UserAccount (Name = "Administrator", Domain = "HACKLAB")
Win32_Group (Name = "Group Policy Creator Owners", Domain = "HACKLAB")       Win32_UserAccount (Name = "Administrator", Domain = "HACKLAB")
Win32_Group (Name = "Schema Admins", Domain = "HACKLAB")                     Win32_UserAccount (Name = "Administrator", Domain = "HACKLAB")
```

### Enumerate_domain_groups_and_SID_win32_group
Complete Syntax:
```
Get-CimInstance -ClassName Win32_Group | Format-Table -AutoSize 
```

Result:
```
PS C:\Users\User1> Get-CimInstance -ClassName Win32_Group | Format-Table -AutoSize

SID                                            Name                                    Caption                                         Domain
---                                            ----                                    -------                                         ------
S-1-5-32-579                                   Access Control Assistance Operators     LAB-WIN11-2\Access Control Assistance Operators LAB-WIN11-2
S-1-5-32-544                                   Administrators                          LAB-WIN11-2\Administrators                      LAB-WIN11-2
S-1-5-32-551                                   Backup Operators                        LAB-WIN11-2\Backup Operators                    LAB-WIN11-2
S-1-5-32-569                                   Cryptographic Operators                 LAB-WIN11-2\Cryptographic Operators             LAB-WIN11-2
S-1-5-32-583                                   Device Owners                           LAB-WIN11-2\Device Owners                       LAB-WIN11-2
S-1-5-32-562                                   Distributed COM Users                   LAB-WIN11-2\Distributed COM Users               LAB-WIN11-2
S-1-5-32-573                                   Event Log Readers                       LAB-WIN11-2\Event Log Readers                   LAB-WIN11-2
S-1-5-32-546                                   Guests                                  LAB-WIN11-2\Guests                              LAB-WIN11-2
S-1-5-32-578                                   Hyper-V Administrators                  LAB-WIN11-2\Hyper-V Administrators              LAB-WIN11-2
S-1-5-32-568                                   IIS_IUSRS                               LAB-WIN11-2\IIS_IUSRS                           LAB-WIN11-2
S-1-5-32-556                                   Network Configuration Operators         LAB-WIN11-2\Network Configuration Operators     LAB-WIN11-2
S-1-5-32-559                                   Performance Log Users                   LAB-WIN11-2\Performance Log Users               LAB-WIN11-2
S-1-5-32-558                                   Performance Monitor Users               LAB-WIN11-2\Performance Monitor Users           LAB-WIN11-2
S-1-5-32-547                                   Power Users                             LAB-WIN11-2\Power Users                         LAB-WIN11-2
S-1-5-32-555                                   Remote Desktop Users                    LAB-WIN11-2\Remote Desktop Users                LAB-WIN11-2
S-1-5-32-580                                   Remote Management Users                 LAB-WIN11-2\Remote Management Users             LAB-WIN11-2
S-1-5-32-552                                   Replicator                              LAB-WIN11-2\Replicator                          LAB-WIN11-2
S-1-5-32-581                                   System Managed Accounts Group           LAB-WIN11-2\System Managed Accounts Group       LAB-WIN11-2
S-1-5-32-545                                   Users                                   LAB-WIN11-2\Users                               LAB-WIN11-2
S-1-5-21-2616623822-1553658618-2628931692-517  Cert Publishers                         HACKLAB\Cert Publishers                         HACKLAB
S-1-5-21-2616623822-1553658618-2628931692-553  RAS and IAS Servers                     HACKLAB\RAS and IAS Servers                     HACKLAB
S-1-5-21-2616623822-1553658618-2628931692-571  Allowed RODC Password Replication Group HACKLAB\Allowed RODC Password Replication Group HACKLAB
S-1-5-21-2616623822-1553658618-2628931692-572  Denied RODC Password Replication Group  HACKLAB\Denied RODC Password Replication Group  HACKLAB
S-1-5-21-2616623822-1553658618-2628931692-1101 DnsAdmins                               HACKLAB\DnsAdmins                               HACKLAB
S-1-5-21-2616623822-1553658618-2628931692-1105 accounts                                HACKLAB\accounts                                HACKLAB
S-1-5-21-2616623822-1553658618-2628931692-1104 administration                          HACKLAB\administration                          HACKLAB
S-1-5-21-2616623822-1553658618-2628931692-522  Cloneable Domain Controllers            HACKLAB\Cloneable Domain Controllers            HACKLAB
S-1-5-21-2616623822-1553658618-2628931692-1102 DnsUpdateProxy                          HACKLAB\DnsUpdateProxy                          HACKLAB
S-1-5-21-2616623822-1553658618-2628931692-512  Domain Admins                           HACKLAB\Domain Admins                           HACKLAB
S-1-5-21-2616623822-1553658618-2628931692-515  Domain Computers                        HACKLAB\Domain Computers                        HACKLAB
S-1-5-21-2616623822-1553658618-2628931692-516  Domain Controllers                      HACKLAB\Domain Controllers                      HACKLAB
S-1-5-21-2616623822-1553658618-2628931692-514  Domain Guests                           HACKLAB\Domain Guests                           HACKLAB
S-1-5-21-2616623822-1553658618-2628931692-513  Domain Users                            HACKLAB\Domain Users                            HACKLAB
S-1-5-21-2616623822-1553658618-2628931692-519  Enterprise Admins                       HACKLAB\Enterprise Admins                       HACKLAB
S-1-5-21-2616623822-1553658618-2628931692-527  Enterprise Key Admins                   HACKLAB\Enterprise Key Admins                   HACKLAB
S-1-5-21-2616623822-1553658618-2628931692-498  Enterprise Read-only Domain Controllers HACKLAB\Enterprise Read-only Domain Controllers HACKLAB
S-1-5-21-2616623822-1553658618-2628931692-520  Group Policy Creator Owners             HACKLAB\Group Policy Creator Owners             HACKLAB
S-1-5-21-2616623822-1553658618-2628931692-1106 help_desk                               HACKLAB\help_desk                               HACKLAB
S-1-5-21-2616623822-1553658618-2628931692-526  Key Admins                              HACKLAB\Key Admins                              HACKLAB
S-1-5-21-2616623822-1553658618-2628931692-525  Protected Users                         HACKLAB\Protected Users                         HACKLAB
S-1-5-21-2616623822-1553658618-2628931692-1108 RDP                                     HACKLAB\RDP                                     HACKLAB
S-1-5-21-2616623822-1553658618-2628931692-521  Read-only Domain Controllers            HACKLAB\Read-only Domain Controllers            HACKLAB
S-1-5-21-2616623822-1553658618-2628931692-1103 sales                                   HACKLAB\sales                                   HACKLAB
S-1-5-21-2616623822-1553658618-2628931692-518  Schema Admins                           HACKLAB\Schema Admins                           HACKLAB
S-1-5-21-2616623822-1553658618-2628931692-1107 support                                 HACKLAB\support                                 HACKLAB 
```

### Enumerate_domain_groups_users_and_SID_win32_account
Complete Syntax:
```
Get-CimInstance -ClassName Win32_Account | Format-Table -AutoSize
```

Result:
```
PS C:\Users\User1> Get-CimInstance -ClassName Win32_Account | Format-Table -AutoSize

Caption                                         Domain      Name                                    SID
-------                                         ------      ----                                    ---
LAB-WIN11-2\Everyone                            LAB-WIN11-2 Everyone                                S-1-1-0
LAB-WIN11-2\LOCAL                               LAB-WIN11-2 LOCAL                                   S-1-2-0
LAB-WIN11-2\CREATOR OWNER                       LAB-WIN11-2 CREATOR OWNER                           S-1-3-0
LAB-WIN11-2\CREATOR GROUP                       LAB-WIN11-2 CREATOR GROUP                           S-1-3-1
LAB-WIN11-2\CREATOR OWNER SERVER                LAB-WIN11-2 CREATOR OWNER SERVER                    S-1-3-2
LAB-WIN11-2\CREATOR GROUP SERVER                LAB-WIN11-2 CREATOR GROUP SERVER                    S-1-3-3
LAB-WIN11-2\OWNER RIGHTS                        LAB-WIN11-2 OWNER RIGHTS                            S-1-3-4
LAB-WIN11-2\DIALUP                              LAB-WIN11-2 DIALUP                                  S-1-5-1
LAB-WIN11-2\NETWORK                             LAB-WIN11-2 NETWORK                                 S-1-5-2
LAB-WIN11-2\BATCH                               LAB-WIN11-2 BATCH                                   S-1-5-3
LAB-WIN11-2\INTERACTIVE                         LAB-WIN11-2 INTERACTIVE                             S-1-5-4
LAB-WIN11-2\SERVICE                             LAB-WIN11-2 SERVICE                                 S-1-5-6
LAB-WIN11-2\ANONYMOUS LOGON                     LAB-WIN11-2 ANONYMOUS LOGON                         S-1-5-7
LAB-WIN11-2\PROXY                               LAB-WIN11-2 PROXY                                   S-1-5-8
LAB-WIN11-2\SYSTEM                              LAB-WIN11-2 SYSTEM                                  S-1-5-18
LAB-WIN11-2\ENTERPRISE DOMAIN CONTROLLERS       LAB-WIN11-2 ENTERPRISE DOMAIN CONTROLLERS           S-1-5-9
LAB-WIN11-2\SELF                                LAB-WIN11-2 SELF                                    S-1-5-10
LAB-WIN11-2\Authenticated Users                 LAB-WIN11-2 Authenticated Users                     S-1-5-11
LAB-WIN11-2\RESTRICTED                          LAB-WIN11-2 RESTRICTED                              S-1-5-12
LAB-WIN11-2\TERMINAL SERVER USER                LAB-WIN11-2 TERMINAL SERVER USER                    S-1-5-13
LAB-WIN11-2\REMOTE INTERACTIVE LOGON            LAB-WIN11-2 REMOTE INTERACTIVE LOGON                S-1-5-14
LAB-WIN11-2\IUSR                                LAB-WIN11-2 IUSR                                    S-1-5-17
LAB-WIN11-2\LOCAL SERVICE                       LAB-WIN11-2 LOCAL SERVICE                           S-1-5-19
LAB-WIN11-2\NETWORK SERVICE                     LAB-WIN11-2 NETWORK SERVICE                         S-1-5-20
LAB-WIN11-2\BUILTIN                             LAB-WIN11-2 BUILTIN                                 S-1-5-32
LAB-WIN11-2\Access Control Assistance Operators LAB-WIN11-2 Access Control Assistance Operators     S-1-5-32-579
LAB-WIN11-2\Administrators                      LAB-WIN11-2 Administrators                          S-1-5-32-544
LAB-WIN11-2\Backup Operators                    LAB-WIN11-2 Backup Operators                        S-1-5-32-551
LAB-WIN11-2\Cryptographic Operators             LAB-WIN11-2 Cryptographic Operators                 S-1-5-32-569
LAB-WIN11-2\Device Owners                       LAB-WIN11-2 Device Owners                           S-1-5-32-583
LAB-WIN11-2\Distributed COM Users               LAB-WIN11-2 Distributed COM Users                   S-1-5-32-562
LAB-WIN11-2\Event Log Readers                   LAB-WIN11-2 Event Log Readers                       S-1-5-32-573
LAB-WIN11-2\Guests                              LAB-WIN11-2 Guests                                  S-1-5-32-546
LAB-WIN11-2\Hyper-V Administrators              LAB-WIN11-2 Hyper-V Administrators                  S-1-5-32-578
LAB-WIN11-2\IIS_IUSRS                           LAB-WIN11-2 IIS_IUSRS                               S-1-5-32-568
LAB-WIN11-2\Network Configuration Operators     LAB-WIN11-2 Network Configuration Operators         S-1-5-32-556
LAB-WIN11-2\Performance Log Users               LAB-WIN11-2 Performance Log Users                   S-1-5-32-559
LAB-WIN11-2\Performance Monitor Users           LAB-WIN11-2 Performance Monitor Users               S-1-5-32-558
LAB-WIN11-2\Power Users                         LAB-WIN11-2 Power Users                             S-1-5-32-547
LAB-WIN11-2\Remote Desktop Users                LAB-WIN11-2 Remote Desktop Users                    S-1-5-32-555
LAB-WIN11-2\Remote Management Users             LAB-WIN11-2 Remote Management Users                 S-1-5-32-580
LAB-WIN11-2\Replicator                          LAB-WIN11-2 Replicator                              S-1-5-32-552
LAB-WIN11-2\System Managed Accounts Group       LAB-WIN11-2 System Managed Accounts Group           S-1-5-32-581
LAB-WIN11-2\Users                               LAB-WIN11-2 Users                                   S-1-5-32-545
HACKLAB\Cert Publishers                         HACKLAB     Cert Publishers                         S-1-5-21-2616623822-1553658618-2628931692-517
HACKLAB\RAS and IAS Servers                     HACKLAB     RAS and IAS Servers                     S-1-5-21-2616623822-1553658618-2628931692-553
HACKLAB\Allowed RODC Password Replication Group HACKLAB     Allowed RODC Password Replication Group S-1-5-21-2616623822-1553658618-2628931692-571
HACKLAB\Denied RODC Password Replication Group  HACKLAB     Denied RODC Password Replication Group  S-1-5-21-2616623822-1553658618-2628931692-572
HACKLAB\DnsAdmins                               HACKLAB     DnsAdmins                               S-1-5-21-2616623822-1553658618-2628931692-1101
HACKLAB\accounts                                HACKLAB     accounts                                S-1-5-21-2616623822-1553658618-2628931692-1105
HACKLAB\administration                          HACKLAB     administration                          S-1-5-21-2616623822-1553658618-2628931692-1104
HACKLAB\Cloneable Domain Controllers            HACKLAB     Cloneable Domain Controllers            S-1-5-21-2616623822-1553658618-2628931692-522
HACKLAB\DnsUpdateProxy                          HACKLAB     DnsUpdateProxy                          S-1-5-21-2616623822-1553658618-2628931692-1102
HACKLAB\Domain Admins                           HACKLAB     Domain Admins                           S-1-5-21-2616623822-1553658618-2628931692-512
HACKLAB\Domain Computers                        HACKLAB     Domain Computers                        S-1-5-21-2616623822-1553658618-2628931692-515
HACKLAB\Domain Controllers                      HACKLAB     Domain Controllers                      S-1-5-21-2616623822-1553658618-2628931692-516
HACKLAB\Domain Guests                           HACKLAB     Domain Guests                           S-1-5-21-2616623822-1553658618-2628931692-514
HACKLAB\Domain Users                            HACKLAB     Domain Users                            S-1-5-21-2616623822-1553658618-2628931692-513
HACKLAB\Enterprise Admins                       HACKLAB     Enterprise Admins                       S-1-5-21-2616623822-1553658618-2628931692-519
HACKLAB\Enterprise Key Admins                   HACKLAB     Enterprise Key Admins                   S-1-5-21-2616623822-1553658618-2628931692-527
HACKLAB\Enterprise Read-only Domain Controllers HACKLAB     Enterprise Read-only Domain Controllers S-1-5-21-2616623822-1553658618-2628931692-498
HACKLAB\Group Policy Creator Owners             HACKLAB     Group Policy Creator Owners             S-1-5-21-2616623822-1553658618-2628931692-520
HACKLAB\help_desk                               HACKLAB     help_desk                               S-1-5-21-2616623822-1553658618-2628931692-1106
HACKLAB\Key Admins                              HACKLAB     Key Admins                              S-1-5-21-2616623822-1553658618-2628931692-526
HACKLAB\Protected Users                         HACKLAB     Protected Users                         S-1-5-21-2616623822-1553658618-2628931692-525
HACKLAB\RDP                                     HACKLAB     RDP                                     S-1-5-21-2616623822-1553658618-2628931692-1108
HACKLAB\Read-only Domain Controllers            HACKLAB     Read-only Domain Controllers            S-1-5-21-2616623822-1553658618-2628931692-521
HACKLAB\sales                                   HACKLAB     sales                                   S-1-5-21-2616623822-1553658618-2628931692-1103
HACKLAB\Schema Admins                           HACKLAB     Schema Admins                           S-1-5-21-2616623822-1553658618-2628931692-518
HACKLAB\support                                 HACKLAB     support                                 S-1-5-21-2616623822-1553658618-2628931692-1107
LAB-WIN11-2\Administrator                       LAB-WIN11-2 Administrator                           S-1-5-21-989316335-961067246-357250816-500
LAB-WIN11-2\DefaultAccount                      LAB-WIN11-2 DefaultAccount                          S-1-5-21-989316335-961067246-357250816-503
LAB-WIN11-2\Guest                               LAB-WIN11-2 Guest                                   S-1-5-21-989316335-961067246-357250816-501
LAB-WIN11-2\ieuser                              LAB-WIN11-2 ieuser                                  S-1-5-21-989316335-961067246-357250816-1001
LAB-WIN11-2\WDAGUtilityAccount                  LAB-WIN11-2 WDAGUtilityAccount                      S-1-5-21-989316335-961067246-357250816-504
HACKLAB\Administrator                           HACKLAB     Administrator                           S-1-5-21-2616623822-1553658618-2628931692-500
HACKLAB\Guest                                   HACKLAB     Guest                                   S-1-5-21-2616623822-1553658618-2628931692-501
HACKLAB\krbtgt                                  HACKLAB     krbtgt                                  S-1-5-21-2616623822-1553658618-2628931692-502
HACKLAB\n.collins                               HACKLAB     n.collins                               S-1-5-21-2616623822-1553658618-2628931692-1109
HACKLAB\o.davidson                              HACKLAB     o.davidson                              S-1-5-21-2616623822-1553658618-2628931692-1110
HACKLAB\p.davies                                HACKLAB     p.davies                                S-1-5-21-2616623822-1553658618-2628931692-1111
HACKLAB\q.dawson                                HACKLAB     q.dawson                                S-1-5-21-2616623822-1553658618-2628931692-1112
HACKLAB\u.dixon                                 HACKLAB     u.dixon                                 S-1-5-21-2616623822-1553658618-2628931692-1113
HACKLAB\r.edwards                               HACKLAB     r.edwards                               S-1-5-21-2616623822-1553658618-2628931692-1114
HACKLAB\s.elliot                                HACKLAB     s.elliot                                S-1-5-21-2616623822-1553658618-2628931692-1115
HACKLAB\t.evans                                 HACKLAB     t.evans                                 S-1-5-21-2616623822-1553658618-2628931692-1116
HACKLAB\u.fisher                                HACKLAB     u.fisher                                S-1-5-21-2616623822-1553658618-2628931692-1117
HACKLAB\v.fletcher                              HACKLAB     v.fletcher                              S-1-5-21-2616623822-1553658618-2628931692-1118
HACKLAB\w.ford                                  HACKLAB     w.ford                                  S-1-5-21-2616623822-1553658618-2628931692-1119
HACKLAB\x.foster                                HACKLAB     x.foster                                S-1-5-21-2616623822-1553658618-2628931692-1120
HACKLAB\y.fox                                   HACKLAB     y.fox                                   S-1-5-21-2616623822-1553658618-2628931692-1121
HACKLAB\z.gibson                                HACKLAB     z.gibson                                S-1-5-21-2616623822-1553658618-2628931692-1122
HACKLAB\a.graham                                HACKLAB     a.graham                                S-1-5-21-2616623822-1553658618-2628931692-1123
HACKLAB\b.grant                                 HACKLAB     b.grant                                 S-1-5-21-2616623822-1553658618-2628931692-1124
HACKLAB\c.gray                                  HACKLAB     c.gray                                  S-1-5-21-2616623822-1553658618-2628931692-1125
HACKLAB\d.green                                 HACKLAB     d.green                                 S-1-5-21-2616623822-1553658618-2628931692-1126
HACKLAB\m.jenkins                               HACKLAB     m.jenkins                               S-1-5-21-2616623822-1553658618-2628931692-1127
HACKLAB\n.johnson                               HACKLAB     n.johnson                               S-1-5-21-2616623822-1553658618-2628931692-1128
HACKLAB\o.jones                                 HACKLAB     o.jones                                 S-1-5-21-2616623822-1553658618-2628931692-1129
HACKLAB\g.white                                 HACKLAB     g.white                                 S-1-5-21-2616623822-1553658618-2628931692-1130
HACKLAB\h.yalden                                HACKLAB     h.yalden                                S-1-5-21-2616623822-1553658618-2628931692-1131
HACKLAB\i.yarbury                               HACKLAB     i.yarbury                               S-1-5-21-2616623822-1553658618-2628931692-1132
HACKLAB\j.yardley                               HACKLAB     j.yardley                               S-1-5-21-2616623822-1553658618-2628931692-1133
HACKLAB\z.mcdonald                              HACKLAB     z.mcdonald                              S-1-5-21-2616623822-1553658618-2628931692-1134
HACKLAB\a.murphy                                HACKLAB     a.murphy                                S-1-5-21-2616623822-1553658618-2628931692-1135
HACKLAB\b.natt                                  HACKLAB     b.natt                                  S-1-5-21-2616623822-1553658618-2628931692-1136
HACKLAB\c.nelson                                HACKLAB     c.nelson                                S-1-5-21-2616623822-1553658618-2628931692-1137
HACKLAB\d.nightingale                           HACKLAB     d.nightingale                           S-1-5-21-2616623822-1553658618-2628931692-1138
HACKLAB\e.nixon                                 HACKLAB     e.nixon                                 S-1-5-21-2616623822-1553658618-2628931692-1139
HACKLAB\f.nutter                                HACKLAB     f.nutter                                S-1-5-21-2616623822-1553658618-2628931692-1140
HACKLAB\p.kelly                                 HACKLAB     p.kelly                                 S-1-5-21-2616623822-1553658618-2628931692-1141
HACKLAB\q.kennedy                               HACKLAB     q.kennedy                               S-1-5-21-2616623822-1553658618-2628931692-1142
HACKLAB\u.king                                  HACKLAB     u.king                                  S-1-5-21-2616623822-1553658618-2628931692-1143
HACKLAB\r.knight                                HACKLAB     r.knight                                S-1-5-21-2616623822-1553658618-2628931692-1144
HACKLAB\s.lawrence                              HACKLAB     s.lawrence                              S-1-5-21-2616623822-1553658618-2628931692-1145
HACKLAB\t.lee                                   HACKLAB     t.lee                                   S-1-5-21-2616623822-1553658618-2628931692-1146
HACKLAB\u.lewis                                 HACKLAB     u.lewis                                 S-1-5-21-2616623822-1553658618-2628931692-1147
HACKLAB\v.lloyd                                 HACKLAB     v.lloyd                                 S-1-5-21-2616623822-1553658618-2628931692-1148
HACKLAB\w.marshall                              HACKLAB     w.marshall                              S-1-5-21-2616623822-1553658618-2628931692-1149
HACKLAB\x.martin                                HACKLAB     x.martin                                S-1-5-21-2616623822-1553658618-2628931692-1150
HACKLAB\y.mason                                 HACKLAB     y.mason                                 S-1-5-21-2616623822-1553658618-2628931692-1151
HACKLAB\g.dell                                  HACKLAB     g.dell                                  S-1-5-21-2616623822-1553658618-2628931692-1152
HACKLAB\h.osborne                               HACKLAB     h.osborne                               S-1-5-21-2616623822-1553658618-2628931692-1153
HACKLAB\i.owen                                  HACKLAB     i.owen                                  S-1-5-21-2616623822-1553658618-2628931692-1154
HACKLAB\j.oxley                                 HACKLAB     j.oxley                                 S-1-5-21-2616623822-1553658618-2628931692-1155
HACKLAB\k.page                                  HACKLAB     k.page                                  S-1-5-21-2616623822-1553658618-2628931692-1156
HACKLAB\l.painter                               HACKLAB     l.painter                               S-1-5-21-2616623822-1553658618-2628931692-1157
HACKLAB\m.palmer                                HACKLAB     m.palmer                                S-1-5-21-2616623822-1553658618-2628931692-1158
HACKLAB\n.pastor                                HACKLAB     n.pastor                                S-1-5-21-2616623822-1553658618-2628931692-1159
HACKLAB\o.peterson                              HACKLAB     o.peterson                              S-1-5-21-2616623822-1553658618-2628931692-1160
HACKLAB\p.quill                                 HACKLAB     p.quill                                 S-1-5-21-2616623822-1553658618-2628931692-1161
HACKLAB\q.quimby                                HACKLAB     q.quimby                                S-1-5-21-2616623822-1553658618-2628931692-1162
HACKLAB\u.quintrell                             HACKLAB     u.quintrell                             S-1-5-21-2616623822-1553658618-2628931692-1163
HACKLAB\r.ramsey                                HACKLAB     r.ramsey                                S-1-5-21-2616623822-1553658618-2628931692-1164
HACKLAB\s.ratliff                               HACKLAB     s.ratliff                               S-1-5-21-2616623822-1553658618-2628931692-1165
HACKLAB\t.richards                              HACKLAB     t.richards                              S-1-5-21-2616623822-1553658618-2628931692-1166
HACKLAB\u.roberts                               HACKLAB     u.roberts                               S-1-5-21-2616623822-1553658618-2628931692-1167
HACKLAB\v.robinson                              HACKLAB     v.robinson                              S-1-5-21-2616623822-1553658618-2628931692-1168
HACKLAB\w.scott                                 HACKLAB     w.scott                                 S-1-5-21-2616623822-1553658618-2628931692-1169
HACKLAB\x.simpson                               HACKLAB     x.simpson                               S-1-5-21-2616623822-1553658618-2628931692-1170
HACKLAB\y.smith                                 HACKLAB     y.smith                                 S-1-5-21-2616623822-1553658618-2628931692-1171
HACKLAB\z.stewart                               HACKLAB     z.stewart                               S-1-5-21-2616623822-1553658618-2628931692-1172
HACKLAB\a.taylor                                HACKLAB     a.taylor                                S-1-5-21-2616623822-1553658618-2628931692-1173
HACKLAB\b.turner                                HACKLAB     b.turner                                S-1-5-21-2616623822-1553658618-2628931692-1174
HACKLAB\c.walsh                                 HACKLAB     c.walsh                                 S-1-5-21-2616623822-1553658618-2628931692-1175
HACKLAB\d.ward                                  HACKLAB     d.ward                                  S-1-5-21-2616623822-1553658618-2628931692-1176
HACKLAB\e.webb                                  HACKLAB     e.webb                                  S-1-5-21-2616623822-1553658618-2628931692-1177
HACKLAB\f.west                                  HACKLAB     f.west                                  S-1-5-21-2616623822-1553658618-2628931692-1178
HACKLAB\d.atkinson                              HACKLAB     d.atkinson                              S-1-5-21-2616623822-1553658618-2628931692-1179
HACKLAB\e.bailey                                HACKLAB     e.bailey                                S-1-5-21-2616623822-1553658618-2628931692-1180
HACKLAB\f.baker                                 HACKLAB     f.baker                                 S-1-5-21-2616623822-1553658618-2628931692-1181
HACKLAB\g.ball                                  HACKLAB     g.ball                                  S-1-5-21-2616623822-1553658618-2628931692-1182
HACKLAB\h.bell                                  HACKLAB     h.bell                                  S-1-5-21-2616623822-1553658618-2628931692-1183
HACKLAB\i.brown                                 HACKLAB     i.brown                                 S-1-5-21-2616623822-1553658618-2628931692-1184
HACKLAB\j.burton                                HACKLAB     j.burton                                S-1-5-21-2616623822-1553658618-2628931692-1185
HACKLAB\k.carter                                HACKLAB     k.carter                                S-1-5-21-2616623822-1553658618-2628931692-1186
HACKLAB\l.clarke                                HACKLAB     l.clarke                                S-1-5-21-2616623822-1553658618-2628931692-1187
HACKLAB\m.cole                                  HACKLAB     m.cole                                  S-1-5-21-2616623822-1553658618-2628931692-1188
HACKLAB\e.griffiths                             HACKLAB     e.griffiths                             S-1-5-21-2616623822-1553658618-2628931692-1189
HACKLAB\f.hall                                  HACKLAB     f.hall                                  S-1-5-21-2616623822-1553658618-2628931692-1190
HACKLAB\g.hamilton                              HACKLAB     g.hamilton                              S-1-5-21-2616623822-1553658618-2628931692-1191
HACKLAB\h.harris                                HACKLAB     h.harris                                S-1-5-21-2616623822-1553658618-2628931692-1192
HACKLAB\i.harvey                                HACKLAB     i.harvey                                S-1-5-21-2616623822-1553658618-2628931692-1193
HACKLAB\j.hill                                  HACKLAB     j.hill                                  S-1-5-21-2616623822-1553658618-2628931692-1194
HACKLAB\k.jackson                               HACKLAB     k.jackson                               S-1-5-21-2616623822-1553658618-2628931692-1195
HACKLAB\l.james                                 HACKLAB     l.james                                 S-1-5-21-2616623822-1553658618-2628931692-1196
HACKLAB\k.yarrow                                HACKLAB     k.yarrow                                S-1-5-21-2616623822-1553658618-2628931692-1197
HACKLAB\l.yates                                 HACKLAB     l.yates                                 S-1-5-21-2616623822-1553658618-2628931692-1198
HACKLAB\m.young                                 HACKLAB     m.young                                 S-1-5-21-2616623822-1553658618-2628931692-1199
HACKLAB\n.zachary                               HACKLAB     n.zachary                               S-1-5-21-2616623822-1553658618-2628931692-1200
HACKLAB\o.zelly                                 HACKLAB     o.zelly                                 S-1-5-21-2616623822-1553658618-2628931692-1201
HACKLAB\p.zinc                                  HACKLAB     p.zinc                                  S-1-5-21-2616623822-1553658618-2628931692-1202
HACKLAB\q.zouch                                 HACKLAB     q.zouch                                 S-1-5-21-2616623822-1553658618-2628931692-1203
HACKLAB\a.adams                                 HACKLAB     a.adams                                 S-1-5-21-2616623822-1553658618-2628931692-1204
HACKLAB\b.allen                                 HACKLAB     b.allen                                 S-1-5-21-2616623822-1553658618-2628931692-1205
HACKLAB\c.armstrong                             HACKLAB     c.armstrong                             S-1-5-21-2616623822-1553658618-2628931692-1206
HACKLAB\adm.adams                               HACKLAB     adm.adams                               S-1-5-21-2616623822-1553658618-2628931692-1207
HACKLAB\adm.smith                               HACKLAB     adm.smith                               S-1-5-21-2616623822-1553658618-2628931692-1208
HACKLAB\adm.stewart                             HACKLAB     adm.stewart                             S-1-5-21-2616623822-1553658618-2628931692-1209
HACKLAB\adm.natt                                HACKLAB     adm.natt                                S-1-5-21-2616623822-1553658618-2628931692-1210
HACKLAB\adm.nelson                              HACKLAB     adm.nelson                              S-1-5-21-2616623822-1553658618-2628931692-1211
HACKLAB\svc_afds                                HACKLAB     svc_afds                                S-1-5-21-2616623822-1553658618-2628931692-1212
HACKLAB\svc_test                                HACKLAB     svc_test                                S-1-5-21-2616623822-1553658618-2628931692-1213
HACKLAB\svc_mssql1                              HACKLAB     svc_mssql1                              S-1-5-21-2616623822-1553658618-2628931692-1214
HACKLAB\svc_mssql2                              HACKLAB     svc_mssql2                              S-1-5-21-2616623822-1553658618-2628931692-1215
HACKLAB\svc_lab                                 HACKLAB     svc_lab                                 S-1-5-21-2616623822-1553658618-2628931692-1216
HACKLAB\svc_admin                               HACKLAB     svc_admin                               S-1-5-21-2616623822-1553658618-2628931692-1217
HACKLAB\DA1                                     HACKLAB     DA1                                     S-1-5-21-2616623822-1553658618-2628931692-1251
HACKLAB\user1                                   HACKLAB     user1                                   S-1-5-21-2616623822-1553658618-2628931692-1252
HACKLAB\user2                                   HACKLAB     user2                                   S-1-5-21-2616623822-1553658618-2628931692-1255
HACKLAB\user3                                   HACKLAB     user3                                   S-1-5-21-2616623822-1553658618-2628931692-1257
HACKLAB\test2                                   HACKLAB     test2                                   S-1-5-21-2616623822-1553658618-2628931692-1259
HACKLAB\user4                                   HACKLAB     user4                                   S-1-5-21-2616623822-1553658618-2628931692-1260 
```

## ADSI Searcher
ADSI (Active Directory Service Interfaces) Searcher is a programming interface used to search for and retrieve information from Active Directory, a directory service developed by Microsoft for Windows domain networks.

The ADSI Searcher provides a means to programmatically access and manipulate Active Directory objects, including user accounts, groups, organizational units, and other directory objects. It allows developers to perform searches based on various criteria such as object type, attribute values, and other conditions.

The ADSI Searcher is often used in automation and scripting scenarios, such as automating user provisioning, account management, and other administrative tasks. It provides a powerful way to manage and automate large-scale Active Directory environments.

### Find where WinRM is enabled on all domain hosts
```
([adsisearcher]"(&(objectCategory=computer)(objectClass=computer))").FindAll() | ForEach-Object {Test-NetConnection -ComputerName $_.Properties.name -Port 5985 -ErrorAction SilentlyContinue | Where-Object {$_.TcpTestSucceeded -eq $true} | Select-Object ComputerName}
```
Result:
```
PS C:\Windows\system32> ([adsisearcher]"(&(objectCategory=computer)(objectClass=computer))").FindAll() | ForEach-Object {Test-NetConnection -ComputerName $_.Properties.name -Port 5985 -ErrorAction SilentlyContinue | Where-Object {$_.TcpTestSucceeded -eq $true} | Select-Object ComputerName}

WARNING: TCP connect to (fe80::5615:5a87:8d00:a170%5 : 5985) failed
WARNING: TCP connect to (192.168.68.113 : 5985) failed
ComputerName
------------
WIN-CH4HJ7UU7Q5
LAB_WIN11_1
```

### Find Local Admin Access 
```
$searcher=[adsisearcher]"(&(objectCategory=computer)(lastlogon>=$((Get-Date).AddDays(-31).ToUniversalTime().ToFileTime())))"
$computer_list = $searcher.FindAll() | ForEach-Object{$_.Properties.name}
foreach ($computer in $computer_list) { try { $path = "\\" + $computer + "\C$\"; if(dir $path -ErrorAction SilentlyContinue) { $string = "[*] Admin to: " + $computer; Write-Host $string }} catch { } }
```

### Grab all Computers and Search for Local admin
```
$searcher=[adsisearcher]'objectCategory=computer'; $searcher.PageSize = 1000; $computer_list = $searcher.FindAll() | ForEach-Object{$_.Properties.name}; foreach ($computer in $computer_list) { try { $path = "\\" + $computer + "\C$\"; if(Test-Path $path -ErrorAction SilentlyContinue) { $string = "[*] Admin to: " + $computer; Write-Host $string }} catch {}}
```

### Pull only active computers (lastlogon<31) (No test connection, with time throttle function (can be 0))
```
$sleep_secs=0; $date_offset=-31; $searcher=[adsisearcher]"(&(objectCategory=computer)(lastlogon>=$((Get-Date).AddDays($date_offset).ToUniversalTime().ToFileTime())))";$searcher.PageSize = 1000; $computer_list = $searcher.FindAll() | ForEach-Object{$_.Properties.name}; $start_date=Get-Date; $info_source="`nCurrent Time: " + $start_date + "`nActive Workstations: " + $computer_list.count + "`nThrottle Seconds: " + $sleep_secs; Write-Host $info_source `n; foreach ($computer in $computer_list) { try { Start-Sleep -Seconds $sleep_secs; $path = "\\" + $computer + "\C$\"; if(Test-Path $path -ErrorAction SilentlyContinue) { $string = "`t[*] Admin to: " + $computer; Write-Host $string }} catch {}} $end_time=Get-Date; Write-Host "`nFinished! - " $end_time`n;
```

## Pull only active computers (lastlogon<31) (With test connection, with time throttle function (can be 0))
```
$sleep_secs=0; $date_offset=-31; $searcher=[adsisearcher]"(&(objectCategory=computer)(lastlogon>=$((Get-Date).AddDays($date_offset).ToUniversalTime().ToFileTime())))";$searcher.PageSize = 1000; $computer_list = $searcher.FindAll() | ForEach-Object{$_.Properties.name}; $start_date=Get-Date; $info_source="`nCurrent Time: " + $start_date + "`nActive Workstations: " + $computer_list.count + "`nThrottle Seconds: " + $sleep_secs; Write-Host $info_source `n; foreach ($computer in $computer_list) { try { Start-Sleep -Seconds $sleep_secs; $path = "\\" + $computer + "\C$\"; if((Test-Connection -ComputerName $computer -Quiet -Count 1)) { if(Test-Path $path -ErrorAction SilentlyContinue) { $string = "`t[*] Admin to: " + $computer; Write-Host $string }}} catch {}} $end_time=Get-Date; Write-Host "`nFinished! - " $end_time`n;
```

## LDAP Port Scanning RDP

Port 3389

Complete Syntax
```
$searcher = New-Object DirectoryServices.DirectorySearcher("(&(objectCategory=computer)(objectClass=computer))")
$searcher.PageSize = 1000
$results = $searcher.FindAll()

foreach ($result in $results) {
    $computerName = $result.Properties["name"][0]

    $testConnection = Test-NetConnection -ComputerName $computerName -Port 3389 -WarningAction SilentlyContinue
    if ($testConnection.TcpTestSucceeded) {
        Write-Host $computerName -ForegroundColor Green
    }
}
```

One-Liner
```
(New-Object DirectoryServices.DirectorySearcher("(&(objectCategory=computer)(objectClass=computer))")).FindAll() | ForEach-Object {if ((Test-NetConnection -ComputerName $_.Properties.name[0] -Port 3389 -WarningAction SilentlyContinue).TcpTestSucceeded) {Write-Host $_.Properties.name[0] -ForegroundColor Green}}
```
Result
```
PS C:\Windows\system32> (New-Object DirectoryServices.DirectorySearcher("(&(objectCategory=computer)(objectClass=computer))")).FindAll() | ForEach-Object {if ((Test-NetConnection -ComputerName $_.Properties.name[0] -Port 3389 -WarningAction SilentlyContinue).TcpTestSucceeded) {Write-Host $_.Properties.name[0] -ForegroundColor Green}}
WIN-CH4HJ7UU7Q5
```

## LDAP Port Scanning SMB
Port 445

Complete Syntax:
```
(New-Object DirectoryServices.DirectorySearcher("(&(objectCategory=computer)(objectClass=computer))")).FindAll() | ForEach-Object {if ((Test-NetConnection -ComputerName $_.Properties.name[0] -Port 445 -WarningAction SilentlyContinue).TcpTestSucceeded) {Write-Host $_.Properties.name[0] -ForegroundColor Green}}
```
Result:
```
PS C:\Windows\system32> (New-Object DirectoryServices.DirectorySearcher("(&(objectCategory=computer)(objectClass=computer))")).FindAll() | ForEach-Object {if ((Test-NetConnection -ComputerName $_.Properties.name[0] -Port 445 -WarningAction SilentlyContinue).TcpTestSucceeded) {Write-Host $_.Properties.name[0] -ForegroundColor Green}}
WIN-CH4HJ7UU7Q5
LAB_WIN11_1
LAB-WIN11-2
```

 
Port 445 but only Windows 11 hosts

The DirectorySearcher filter been modified to only search for computers with an operating system that contains the string "Windows 11".

Complete Syntax:
```
(New-Object DirectoryServices.DirectorySearcher("(&(objectCategory=computer)(objectClass=computer)(operatingSystem=*Windows 11*))")).FindAll() | ForEach-Object {if ((Test-NetConnection -ComputerName $_.Properties.name[0] -Port 445 -WarningAction SilentlyContinue).TcpTestSucceeded) {Write-Host $_.Properties.name[0] -ForegroundColor Green}}
```
Result:
```
PS C:\Windows\system32> (New-Object DirectoryServices.DirectorySearcher("(&(objectCategory=computer)(objectClass=computer)(operatingSystem=*Windows 11*))")).FindAll() | ForEach-Object {if ((Test-NetConnection -ComputerName $_.Properties.name[0] -Port 445 -WarningAction SilentlyContinue).TcpTestSucceeded) {Write-Host $_.Properties.name[0] -ForegroundColor Green}}
LAB_WIN11_1
LAB-WIN11-2
```
Only match operatingSystem=*Windows Server

Complete Syntax:
```
(New-Object DirectoryServices.DirectorySearcher("(&(objectCategory=computer)(objectClass=computer)(operatingSystem=*Windows Server*))")).FindAll() | ForEach-Object {if ((Test-NetConnection -ComputerName $_.Properties.name[0] -Port 445 -WarningAction SilentlyContinue).TcpTestSucceeded) {Write-Host $_.Properties.name[0] -ForegroundColor Green}}
```
Result:
```
PS C:\Windows\system32> (New-Object DirectoryServices.DirectorySearcher("(&(objectCategory=computer)(objectClass=computer)(operatingSystem=*Windows Server*))")).FindAll() | ForEach-Object {if ((Test-NetConnection -ComputerName $_.Properties.name[0] -Port 445 -WarningAction SilentlyContinue).TcpTestSucceeded) {Write-Host $_.Properties.name[0] -ForegroundColor Green}}
WIN-CH4HJ7UU7Q5
```

## Check for administrative privileges against all windows hosts
This is one I'm excited about, best thing I have scripted so far, it prompts you for creds, then enumerates all windows hosts in the domain, and then finally tries the credentials you submitted to see if they have admin privs on any of the enumerated hosts, basically crackmapexec in PS running native on the host.

Complete Syntax:
```
$computers = @((New-Object DirectoryServices.DirectorySearcher("(&(objectCategory=computer)(operatingSystem=Windows*))")).FindAll() | ForEach-Object { $_.Properties.name[0] })

$username = Read-Host -Prompt "Enter your username"
$password = Read-Host -Prompt "Enter your password" -AsSecureString
$credential = New-Object System.Management.Automation.PSCredential ($username, $password)

$computers | ForEach-Object {
    $computer = $_
    $scriptBlock = {
        Try {
            $smb = Get-SmbSession
            Write-Host "SMB service is accessible on $($env:COMPUTERNAME)" -ForegroundColor Green
        } Catch {
            $error_message = $_.Exception.Message
            if ($error_message -notlike "*The user name or password is incorrect.*") {
                Write-Host $error_message -ForegroundColor Red
            }
        }
    }
    Invoke-Command -ComputerName $computer -Credential $credential -ScriptBlock $scriptBlock -ErrorAction SilentlyContinue
}
```

Result:
```
PS C:\Windows\system32> $computers = @((New-Object DirectoryServices.DirectorySearcher("(&(objectCategory=computer)(operatingSystem=Windows*))")).FindAll() | ForEach-Object { $_.Properties.name[0] })
PS C:\Windows\system32>
PS C:\Windows\system32> $username = Read-Host -Prompt "Enter your username"
Enter your username: user1
PS C:\Windows\system32> $password = Read-Host -Prompt "Enter your password" -AsSecureString
Enter your password: *********
PS C:\Windows\system32> $credential = New-Object System.Management.Automation.PSCredential ($username, $password)
PS C:\Windows\system32>
PS C:\Windows\system32> $computers | ForEach-Object {
>>     $computer = $_
>>     $scriptBlock = {
>>         Try {
>>             $smb = Get-SmbSession
>>             Write-Host "SMB service is accessible on $($env:COMPUTERNAME)" -ForegroundColor Green
>>         } Catch {
>>             $error_message = $_.Exception.Message
>>             if ($error_message -notlike "*The user name or password is incorrect.*") {
>>                 Write-Host $error_message -ForegroundColor Red
>>             }
>>         }
>>     }
>>     Invoke-Command -ComputerName $computer -Credential $credential -ScriptBlock $scriptBlock -ErrorAction SilentlyContinue
>> }
SMB service is accessible on LAB_WIN11_1
```

## Various SMB Checks
Checks if the provided account can access port 445 on the defined hosts.
```
$computers = @( "Add-Computer-Name", "Add-Computer-Name"  )
$username = "ADD-USERNAME"
$password = "ADD-PASSWORD"
$domain = "ADD-DOMAIN"

foreach ($computer in $computers) {
    $credential = New-Object System.Management.Automation.PSCredential ($username, $(ConvertTo-SecureString -AsPlainText -Force $password))
    $scriptBlock = {
        Param ($username, $password)
        Try {
            $smb = Get-SmbSession
            Write-Output "SMB service is accessible on $($env:COMPUTERNAME)"
        } Catch {
            Write-Output "Failed to autheanticate against SMB service on $($env:COMPUTERNAME)"
        }
    }
    Invoke-Command -ComputerName $computer -Credential $credential -ScriptBlock $scriptBlock -ArgumentList $username, $password
}
```


Same as above but with Get-Credential
```
$computers = @( "Add-Computer-Name", "Add-Computer-Name" )
$credential = Get-Credential
$username = $credential.UserName
$password = $credential.Password
$domain = "Add-Domain"

foreach ($computer in $computers) {
    $scriptBlock = {
        Try {
            $smb = Get-SmbSession
            Write-Output "SMB service is accessible on $($env:COMPUTERNAME)"
        } Catch {
            Write-Output "Failed to authenticate against SMB service on $($env:COMPUTERNAME)"
        }
    }
    Invoke-Command -ComputerName $computer -Credential $credential -ScriptBlock $scriptBlock
}
```

Credentials with SMB Automated Host List
```
$domain = "Add-Domain"
$computers = ([adsisearcher]"(&(objectCategory=computer)(operatingSystem=*Windows*))").FindAll().Properties.name | ForEach-Object { $_.ToUpper() }
$username = Read-Host "Enter username"
$password = Read-Host "Enter password" -AsSecureString
$credential = New-Object System.Management.Automation.PSCredential ($username, $password)

foreach ($computer in $computers) {
    $scriptBlock = {
        Try {
            $smb = Get-SmbSession
            Write-Output "SMB service is accessible on $($env:COMPUTERNAME)"
        } Catch {
            Write-Output "Failed to authenticate against SMB service on $($env:COMPUTERNAME)"
        }
    }
    Invoke-Command -ComputerName $computer -Credential $credential -ScriptBlock $scriptBlock
}
```


Output in green
```
$searcher = New-Object System.DirectoryServices.DirectorySearcher
$searcher.Filter = "(&(objectClass=computer)(operatingSystem=*Windows*))"

$computers = $searcher.FindAll() | ForEach-Object {
    $_.Properties.name[0]
}

$username = Read-Host "Enter username"
$password = Read-Host "Enter password" -AsSecureString
$credential = New-Object System.Management.Automation.PSCredential ($username, $password)

foreach ($computer in $computers) {
    $scriptBlock = {
        Try {
            $smb = Get-SmbSession
            Write-Host "SMB service is accessible on $($env:COMPUTERNAME)" -ForegroundColor Green
        } Catch {
            Write-Host "Failed to authenticate against SMB service on $($env:COMPUTERNAME)"
        }
    }
    Invoke-Command -ComputerName $computer -Credential $credential -ScriptBlock $scriptBlock
}
```


Same as above but does not return any failed attempts.
```
$computers = @((New-Object DirectoryServices.DirectorySearcher("(&(objectCategory=computer)(operatingSystem=Windows*))")).FindAll() | ForEach-Object { $_.Properties.name[0] })

$username = Read-Host -Prompt "Enter your username"
$password = Read-Host -Prompt "Enter your password" -AsSecureString
$credential = New-Object System.Management.Automation.PSCredential ($username, $password)

$computers | ForEach-Object {
    $computer = $_
    $scriptBlock = {
        Try {
            $smb = Get-SmbSession
            Write-Host "SMB service is accessible on $($env:COMPUTERNAME)" -ForegroundColor Green
        } Catch {
            $error_message = $_.Exception.Message
            if ($error_message -notlike "*The user name or password is incorrect.*") {
                Write-Host $error_message -ForegroundColor Red
            }
        }
    }
    Invoke-Command -ComputerName $computer -Credential $credential -ScriptBlock $scriptBlock -ErrorAction SilentlyContinue
}
```