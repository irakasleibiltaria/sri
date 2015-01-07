#Exchange

1 - Install w2008 Server

2 - Fix IP: 192.168.1.1
  - Disable IPv6 NIC in network properties and in the registry too, read this (http://theucguy.net/msexchange-transport-failed-to-reach/)
```
Below is a common error that comes up when you install Exchange 2007 Server on a Windows 2008 Server. The hub transport role fails with an error “MsExchange transport failed to reach status running on this server”. Mailbox & CAS installations are cancelled as well, if it is a typical exchange 2007 installation.

Reason: By default, IPV6 is enabled on a Windows 2008 server and you have disabled it in the network connections properties, but not fully.

Solution is to enable IPV6 in NIC properties, but disable IPV6 in registry and re-run the setup.

To disable IPV6 fully on the 2008 server. Follow the steps to do so.

Launch registry, navigate to HKEY_LOCAL_MACHINESYSTEMCurrentControlSetServicesTcpip6Parameters. In the details pane, click New, and then click DWORD (32-bit) Value. Type “DisabledComponents” (without quotes) and then press enter. Double-click DisabledComponents and type 0xffffffff in Hexadecimal or 4294967295 in Decimal. Close the registry editor.

Now, navigate to C:WindowsSystem32DriversEtc and open the hosts file in notepad. Delete the IPV6 entry.

Save & close. Re-run the setup.
```

3 - DNS: 192.168.1.1

4 - PC name

5 - 
```
dcpromo
```
6 - Install PowerShell:
```
ServerManagerCMD –i PowerShell 
```
7 - Install IIS (ALL)

8 - Install Exchange


