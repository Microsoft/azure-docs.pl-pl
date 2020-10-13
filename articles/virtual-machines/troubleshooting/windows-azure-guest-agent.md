---
title: Rozwiązywanie problemów z agentem gościa platformy Microsoft Azure
description: Rozwiązywanie problemów z agentem gościa platformy Microsoft Azure nie działa
services: virtual-machines-windows
ms.service: virtual-machines-windows
author: dkdiksha
manager: dcscontentpm
editor: ''
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 09/15/2020
ms.author: genli
ms.openlocfilehash: 597ea6e7ff7dbcfcb8a99d4e4de3c1b82915ee07
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90561265"
---
# <a name="troubleshooting-windows-azure-guest-agent"></a>Rozwiązywanie problemów z agentem gościa platformy Microsoft Azure

Agent gościa systemu Windows Azure jest agentem maszyny wirtualnej. Umożliwia ona MASZYNom wirtualnym komunikowanie się z kontrolerem sieci szkieletowej (podstawowy serwer fizyczny, na którym jest hostowana maszyna wirtualna) na adresie IP 168.63.129.16. Jest to wirtualny publiczny adres IP, który ułatwia komunikację. Aby uzyskać więcej informacji, zobacz [co to jest adres IP 168.63.129.16](https://docs.microsoft.com/azure/virtual-network/what-is-ip-address-168-63-129-16).

 Na maszynie wirtualnej migrowanej na platformę Azure z lokalizacji lokalnej lub utworzonej przy użyciu dostosowanego obrazu nie jest zainstalowany agent gościa platformy Microsoft Azure. W tych scenariuszach należy ręcznie zainstalować agenta maszyny wirtualnej. Aby uzyskać więcej informacji na temat instalowania agenta maszyny wirtualnej, zobacz [Omówienie agenta maszyny wirtualnej platformy Azure](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows).

Po pomyślnym zainstalowaniu agenta gościa systemu Windows Azure można zobaczyć następujące usługi wymienione w sekcji Services. msc na maszynie wirtualnej:
 
- Usługa agenta gościa systemu Windows Azure
- Usługa telemetrii
- Usługa agenta usług pulpitu zdalnego

**Usługa agenta gościa systemu Windows Azure**: usługa jest odpowiedzialna za wszystkie logowania w WAppAgent. log. Ta usługa jest odpowiedzialna za konfigurowanie różnych rozszerzeń i komunikacji od gościa do hosta. 

**Usługa telemetrii**: usługa jest odpowiedzialna za wysyłanie danych telemetrycznych maszyny wirtualnej na serwer zaplecza.

**Usługa agenta usług pulpitu zdalnego**: Ta usługa jest odpowiedzialna za instalację agenta gościa. Przezroczysty Instalator jest również składnikiem agenta usług pulpitu zdalnego, który pomaga uaktualnić inne składniki i usługi agenta gościa. RDAgent jest również odpowiedzialny za wysyłanie pulsów z maszyny wirtualnej gościa do agenta hosta na serwerze fizycznym.

> [!NOTE]
> Począwszy od wersji 2.7.41491.971 agenta gościa maszyny wirtualnej, składnik telemetrii jest dołączany do usługi RDAgent, dlatego ta usługa telemetrii nie jest widoczna na liście nowo utworzonych maszyn wirtualnych.

## <a name="checking-agent-status-and-version"></a>Sprawdzanie stanu i wersji agenta

Przejdź do strony właściwości maszyny wirtualnej w obszarze Azure Portal i sprawdź **stan agenta**. Jeśli Agent gościa platformy Microsoft Azure działa prawidłowo, zostanie wyświetlony stan **gotowe**. Jeśli Agent maszyny wirtualnej jest w stanie **Niegotowości** , rozszerzenia i **uruchomienia** na Azure Portal nie będą działać.

## <a name="troubleshooting-vm-agent-that-is-in-not-ready-status"></a>Rozwiązywanie problemów z agentem maszyny wirtualnej w stanie niegotowości

### <a name="step-1-check-whether-the-windows-azure-guest-agent-service-is-installed"></a>Krok 1 Sprawdź, czy zainstalowano usługę agenta gościa systemu Windows Azure

- Sprawdź pakiet

    Zlokalizuj folder C:\WindowsAzure. Jeśli zostanie wyświetlony folder GuestAgent, w którym jest wyświetlany numer wersji, oznacza to, że na maszynie wirtualnej został zainstalowany agent gościa platformy Microsoft Azure. Możesz również wyszukać zainstalowany pakiet.  Jeśli na maszynie wirtualnej jest zainstalowany agent gościa platformy Microsoft Azure, pakiet zostanie zapisany w następującej lokalizacji: `C:\windows\OEM\GuestAgent\VMAgentPackage.zip` .
    
    Aby sprawdzić, czy Agent maszyny wirtualnej został wdrożony na maszynie wirtualnej, można uruchomić następujące polecenie programu PowerShell:
    
    `Get-AzVM -ResourceGroupName "RGNAME" -Name "VMNAME" -DisplayHint expand`
    
    W danych wyjściowych Znajdź właściwość **ProvisionVMAgent** i sprawdź, czy wartość jest ustawiona na **wartość true**. Jeśli tak, oznacza to, że Agent jest zainstalowany na maszynie wirtualnej.
    
- Sprawdź usługi i procesy

   Przejdź do konsoli usługi (Services. msc) i sprawdź stan następujących usług: usługa agenta gościa systemu Windows Azure, usługa RDAgent, usługa telemetrii systemu Windows Azure i usługa agenta sieci platformy Microsoft Azure.
 
    Możesz również sprawdzić, czy te usługi działają, sprawdzając Menedżera zadań dla następujących procesów:
       
    - WindowsAzureGuestAgent.exe: usługa agenta gościa systemu Windows Azure
    - WaAppAgent.exe: usługa RDAgent
    - WindowsAzureNetAgent.exe: usługa agenta sieci platformy Microsoft Azure
    - WindowsAzureTelemetryService.exe: usługa telemetrii systemu Windows Azure

   Jeśli nie możesz znaleźć tych procesów i usług, oznacza to, że nie zainstalowano agenta gościa platformy Microsoft Azure.

- Sprawdź program i funkcję

    W panelu sterowania przejdź do **apletu programy i funkcje** , aby określić, czy zainstalowano usługę agenta gościa systemu Windows Azure.

Jeśli nie znajdziesz żadnych pakietów, usług i procesów uruchomionych i nie widzisz nawet agenta gościa platformy Microsoft Azure zainstalowanego w obszarze Programy i funkcje, spróbuj [zainstalować usługę agenta gościa platformy Microsoft Azure](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows). Jeśli Agent gościa nie zostanie zainstalowany prawidłowo, można [zainstalować agenta maszyny wirtualnej w trybie offline](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/install-vm-agent-offline).

Jeśli są widoczne usługi i są uruchomione, należy ponownie uruchomić usługę, aby sprawdzić, czy problem został rozwiązany. Jeśli usługi są zatrzymane, uruchom je i poczekaj kilka minut. Następnie sprawdź, czy **stan agenta** to raportowanie jako **gotowe**. Jeśli okaże się, że te usługi uległy awarii, niektóre procesy innych firm mogą powodować awarię tych usług. Aby rozwiązać te problemy, skontaktuj się z firmą [Pomoc techniczna firmy Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="step-2-check-whether-auto-update-is-working"></a>Krok 2 Sprawdź, czy funkcja autoaktualizacji działa

Agent gościa platformy Microsoft Azure ma funkcję autoaktualizowania. Program automatycznie sprawdzi dostępność nowych aktualizacji i zainstaluje je. Jeśli funkcja autoaktualizacji nie działa prawidłowo, spróbuj odinstalować i zainstalować agenta gościa systemu Windows Azure, wykonując następujące czynności:

1. Jeśli w **aplecie Programy i funkcje**zostanie wyświetlony Agent gościa platformy Microsoft Azure, Odinstaluj agenta gościa platformy Microsoft Azure.

2. Otwórz okno wiersza polecenia z uprawnieniami administratora.

3. Zatrzymaj usługi agenta gościa. Jeśli usługi nie są zatrzymane, należy ustawić **Uruchamianie ręczne** usług, a następnie ponownie uruchomić maszynę wirtualną.
    ```
    net stop rdagent
    net stop WindowsAzureGuestAgent
    net stop WindowsAzureTelemetryService
    ```
1. Usuń usługi agenta gościa:
    ```
    sc delete rdagent
    sc delete WindowsAzureGuestAgent
    sc delete WindowsAzureTelemetryService
    ```
1. W obszarze `C:\WindowsAzure` Utwórz folder o nazwie Old.

1. Przenieś wszystkie foldery o nazwanych pakietach lub GuestAgent do starego folderu.

1. Pobierz i zainstaluj najnowszą wersję pakietu instalacyjnego agenta z tego [miejsca](https://go.microsoft.com/fwlink/?linkid=394789&clcid=0x409). Aby ukończyć instalację, musisz mieć uprawnienia administratora.

1. Zainstaluj agenta gościa za pomocą następującego polecenia:

    ```
    msiexec.exe /i c:\VMAgentMSI\WindowsAzureVmAgent.2.7.<version>.fre.msi /quiet /L*v c:\VMAgentMSI\msiexec.log
    ```
    Następnie sprawdź, czy usługi agenta gościa zostały prawidłowo uruchomione.
 
    W rzadkich przypadkach, w których Agent gościa nie jest poprawnie instalowany, można [zainstalować agenta maszyny wirtualnej w trybie offline](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/install-vm-agent-offline).
    

### <a name="step-3-check-whether-the-vm-can-connect-to-the-fabric-controller"></a>Krok 3 Sprawdź, czy maszyna wirtualna może nawiązać połączenie z kontrolerem sieci szkieletowej

Użyj narzędzia takiego jak PsPing, aby sprawdzić, czy maszyna wirtualna może nawiązać połączenie z 168.63.129.16 na portach 80, 32526 i 443. Jeśli maszyna wirtualna nie nawiąże połączenia zgodnie z oczekiwaniami, sprawdź, czy w lokalnej zaporze na maszynie wirtualnej jest otwarta komunikacja wychodząca przez porty 80, 443 i 32526. Jeśli ten adres IP jest zablokowany, Agent maszyny wirtualnej może wyświetlać nieoczekiwane zachowanie w różnych scenariuszach.

## <a name="advanced-troubleshooting"></a>Zaawansowane rozwiązywanie problemów

Zdarzenia dotyczące rozwiązywania problemów z agentem gościa systemu Windows Azure są rejestrowane w następujących plikach dziennika:

- C:\WindowsAzure\Logs\WaAppAgent.log
- C:\WindowsAzure\Logs\TransparentInstaller.log

Poniżej przedstawiono niektóre typowe scenariusze, w których Agent gościa systemu Windows Azure może wejść w stan **niegotowości** lub przestać działać zgodnie z oczekiwaniami.

### <a name="agent-stuck-on-starting"></a>Agent zablokowany przez "Uruchamianie"

W dzienniku WaAppAgent można zobaczyć, że Agent jest zablokowany i nie można go uruchomić.

**Informacje dziennika**
 
[00000007] [05/28/2019 12:58:50.90] [INFO] WindowsAzureGuestAgent. 2.7.41491.901 wersja

**Analizy**
 
Maszyna wirtualna nadal działa w starszej wersji agenta gościa systemu Windows Azure. W folderze C:\WindowsAzure można zauważyć, że zainstalowano wiele wystąpień agenta gościa systemu Windows Azure, w tym kilka wersji. Ponieważ zainstalowano wiele wystąpień agentów, maszyna wirtualna nie uruchamia najnowszej wersji agenta gościa systemu Windows Azure.

**Rozwiązanie**

Ręcznie Odinstaluj agenta gościa platformy Microsoft Azure, a następnie zainstaluj go ponownie, wykonując następujące czynności:

1. Otwórz Panel sterowania > programy i funkcje, a następnie Odinstaluj agenta gościa platformy Microsoft Azure.
1. Otwórz Menedżera zadań i Zatrzymaj następujące usługi: usługa agenta gościa systemu Windows Azure, usługa RDAgent, usługa telemetrii systemu Windows Azure i usługa agenta sieci platformy Microsoft Azure.
1. W obszarze C:\WindowsAzure Utwórz folder o nazwie OLD.
1. Przenieś wszystkie foldery o nazwanych pakietach lub GuestAgent do starego folderu. Ponadto Przenieś wszystkie foldery GuestAgent w C:\WindowsAzure\logs, które zaczynają się od GuestAgent_x. x. xxxxx do starego folderu.
1. Pobierz i zainstaluj najnowszą wersję agenta MSI. Aby ukończyć instalację, musisz mieć uprawnienia administratora.
1. Zainstaluj agenta gościa za pomocą następującego polecenia MSI:
    ```
    msiexec.exe /i c:\VMAgentMSI\WindowsAzureVmAgent.2.7.<version>.fre.msi /quiet /L*v c:\VMAgentMSI\msiexec.log`
    ```
1. Sprawdź, czy usługa RDAgent, Agent gościa platformy Microsoft Azure i usługi telemetrii systemu Windows Azure działają teraz.
 
1. Sprawdź dziennik WaAppAgent. log, aby upewnić się, że jest uruchomiona Najnowsza wersja agenta gościa systemu Windows Azure.
 
1. Usuń stary folder w obszarze C:\WindowsAzure.
  
### <a name="unable-to-connect-to-wireserver-ip-host-ip"></a>Nie można nawiązać połączenia z adresem IP WireServer (adres IP hosta) 

Zauważysz następujące wpisy błędów w WaAppAgent. log i Telemetria. log:

**Informacje dziennika**

```Log sample
[ERROR] GetVersions() failed with exception: Microsoft.ServiceModel.Web.WebProtocolException: Server Error: Service Unavailable (ServiceUnavailable) ---> 
System.Net.WebException: The remote server returned an error: (503) Server Unavailable.
```
```Log sample
[00000011] [12/11/2018 06:27:55.66] [WARN]  (Ignoring) Exception while fetching supported versions from HostGAPlugin: System.Net.WebException: Unable to connect to the remote server 
---> System.Net.Sockets.SocketException: An attempt was made to access a socket in a way forbidden by its access permissions 168.63.129.16:32526
at System.Net.Sockets.Socket.DoConnect(EndPoint endPointSnapshot, SocketAddress socketAddress)
at System.Net.ServicePoint.ConnectSocketInternal(Boolean connectFailure, Socket s4, Socket s6, Socket& socket, IPAddress& address, ConnectSocketState status, IAsyncResult asyncResult, Exception& exception)
--- End of inner exception stack trace ---
at System.Net.WebClient.DownloadDataInternal(Uri address, WebRequest& request)
at System.Net.WebClient.DownloadString(Uri address)
at Microsoft.GuestAgentHostPlugin.Client.GuestInformationServiceClient.GetVersions()
at Microsoft.WindowsAzure.GuestAgent.ContainerStateMachine.HostGAPluginUtility.UpdateHostGAPluginAvailability()`
```
**Analizy**

Maszyna wirtualna nie może nawiązać połączenia z serwerem hosta wireserver.

**Rozwiązanie**

1. Ponieważ wireserver jest nieosiągalny, Połącz się z maszyną wirtualną przy użyciu Pulpit zdalny, a następnie spróbuj uzyskać dostęp do następującego adresu URL z przeglądarki internetowej: http://168.63.129.16/?comp=versions 
1. Jeśli nie możesz uzyskać dostępu do adresu URL z kroku 1, sprawdź interfejs sieciowy, aby określić, czy jest ustawiony jako włączony protokół DHCP i czy ma system DNS. Aby sprawdzić stan protokołu DHCP w interfejsie sieciowym, uruchom następujące polecenie:  `netsh interface ip show config` .
1. Jeśli usługa DHCP jest wyłączona, uruchom następujące polecenie, aby zmienić wartość żółtą na nazwę interfejsu: `netsh interface ip set address name="Name of the interface" source=dhcp` .
1. Sprawdź, czy występują problemy, które mogą być spowodowane przez zaporę, serwer proxy lub inne źródło, które mogłyby blokować dostęp do adresu IP 168.63.129.16.
1. Sprawdź, czy Zapora systemu Windows lub zapora innej firmy blokuje dostęp do portów 80, 443 i 32526. Aby uzyskać więcej informacji o tym, dlaczego ten adres nie powinien być zablokowany, zobacz [co to jest adres IP 168.63.129.16](https://docs.microsoft.com/azure/virtual-network/what-is-ip-address-168-63-129-16).

### <a name="guest-agent-is-stuck-stopping"></a>Agent gościa jest zablokowany "zatrzymywanie"  

Zauważysz następujące wpisy błędów w WaAppAgent. log:

**Informacje dziennika** 

```Log sample
[00000007] [07/18/2019 14:46:28.87] [WARN] WindowsAzureGuestAgent stopping.
[00000007] [07/18/2019 14:46:28.89] [INFO] Uninitializing StateExecutor with WaitForTerminalStateReachedOnEnd : True
[00000004] [07/18/2019 14:46:28.89] [WARN] WindowsAzureGuestAgent could not be stopped. Exception: System.NullReferenceException: Object reference not set to an instance of an object.
at Microsoft.WindowsAzure.GuestAgent.ContainerStateMachine.GoalStateExecutorBase.WaitForExtensionWorkflowComplete(Boolean WaitForTerminalStateReachedOnEnd)
at Microsoft.WindowsAzure.GuestAgent.ContainerStateMachine.GoalStateExecutorBase.Uninitialize(Boolean WaitForTerminalStateReachedOnEnd)
at Microsoft.WindowsAzure.GuestAgent.ContainerStateMachine.GoalStateExecutorForCloud.Uninitialize(Boolean WaitForTerminalStateReachedOnEnd)
at Microsoft.WindowsAzure.GuestAgent.AgentCore.AgentCore.Stop(Boolean waitForTerminalState)
at Microsoft.WindowsAzure.GuestAgent.AgentCore.AgentService.DoStopService()
at Microsoft.WindowsAzure.GuestAgent.AgentCore.AgentService.<>c__DisplayClass2.<OnStopProcessing>b__1()
```
**Analizy**

Agent gościa systemu Windows Azure jest zablokowany w procesie zatrzymywania.

**Rozwiązanie**

1. Upewnij się, że na maszynie wirtualnej jest uruchomiona WaAppAgent.exe. Jeśli nie jest uruchomiona, uruchom ponownie usługę rdgagent i odczekaj pięć minut. Po uruchomieniu WaAppAgent.exe Zakończ proces WindowsAzureGuest.exe.
2. Jeśli krok 1 nie rozwiąże problemu, Usuń obecnie zainstalowaną wersję i ręcznie zainstaluj najnowszą wersję agenta.

### <a name="npcap-loopback-adapter"></a>Karta sprzężenia zwrotnego Npcap 

Zauważysz następujące wpisy błędów w WaAppAgent. log:
 
```Log sample
[00000006] [06/20/2019 07:44:28.93] [INFO]  Attempting to discover fabric address on interface Npcap Loopback Adapter.
[00000024] [06/20/2019 07:44:28.93] [WARN]  Empty DHCP option data returned
[00000006] [06/20/2019 07:44:28.93] [ERROR] Did not discover fabric address on interface Npcap Loopback Adapter
```
**Analizy**

Karta sprzężenia zwrotnego Npcap jest zainstalowana na maszynie wirtualnej przez program Wireshark. Wireshark to narzędzie Open Source służące do profilowania ruchu sieciowego i analizowania pakietów. Takie narzędzie jest często określane jako Analizator sieci, Analizator protokołów sieciowych lub szperacz.

**Rozwiązanie**

Karta sprzężenia zwrotnego Npcap jest prawdopodobnie zainstalowana przez program WireShark. Spróbuj ją wyłączyć, a następnie sprawdź, czy problem został rozwiązany.

## <a name="next-steps"></a>Następne kroki

Aby jeszcze bardziej rozwiązać problem, [skontaktuj się z pomocą techniczną firmy Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
