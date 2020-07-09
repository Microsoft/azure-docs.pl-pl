---
title: Łączenie komputerów z systemem Windows w celu Azure Monitor | Microsoft Docs
description: W tym artykule opisano sposób łączenia komputerów z systemem Windows hostowanych w innych chmurach lub lokalnych w celu Azure Monitor z agentem Log Analytics dla systemu Windows.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/07/2019
ms.openlocfilehash: 644d1094ec57e148804941297d50398e36b1b068
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "82996421"
---
# <a name="connect-windows-computers-to-azure-monitor"></a>Podłącz komputery z systemem Windows do Azure Monitor

W celu monitorowania maszyn wirtualnych lub komputerów fizycznych w lokalnym centrum danych lub w innym środowisku chmury przy użyciu Azure Monitor należy wdrożyć agenta Log Analytics (określanego także jako Microsoft Monitoring Agent (MMA)) i skonfigurować go do raportowania do co najmniej jednego obszaru roboczego Log Analytics. Agent obsługuje również rolę hybrydowego procesu roboczego elementu Runbook dla Azure Automation.  

Na monitorowanym komputerze z systemem Windows Agent jest wyświetlany jako usługa Microsoft Monitoring Agent. Usługa Microsoft Monitoring Agent zbiera zdarzenia z plików dziennika, dziennika zdarzeń systemu Windows, danych wydajności i innych danych telemetrycznych. Nawet jeśli Agent nie może komunikować się z Azure Monitorą raportów, Agent nadal uruchamia i kolejkuje zebrane dane na dysku monitorowanego komputera. Po przywróceniu połączenia usługa Microsoft Monitoring Agent wysyła zebrane dane do usługi.

Agenta programu można zainstalować przy użyciu jednej z poniższych metod. W większości instalacji jest używana kombinacja tych metod w celu zainstalowania różnych zestawów komputerów, zgodnie z potrzebami danej konfiguracji.  Szczegółowe informacje o używaniu każdej metody są podane w dalszej części artykułu.

* Instalacja ręczna. Instalator jest uruchamiany ręcznie na komputerze przy użyciu Kreatora instalacji, z wiersza polecenia lub wdrożony za pomocą istniejącego narzędzia do dystrybucji oprogramowania.
* Azure Automation konfiguracji żądanego stanu (DSC). Korzystanie z usługi DSC w Azure Automation ze skryptem dla komputerów z systemem Windows, które zostały już wdrożone w danym środowisku.  
* Skrypt programu PowerShell.
* Menedżer zasobów szablon dla maszyn wirtualnych z systemem Windows lokalnie w programie Azure Stack. 

>[!NOTE]
>Azure Security Center (ASC) zależy od Microsoft Monitoring Agent (nazywanego również Log Analytics agentem systemu Windows) i zostanie zainstalowany i skonfigurowany do raportowania do obszaru roboczego Log Analytics w ramach wdrożenia. Funkcja ASC obejmuje automatyczną opcję aprowizacji, która umożliwia automatyczną instalację Log Analytics agenta systemu Windows na wszystkich maszynach wirtualnych w ramach subskrypcji i konfiguruje go w celu raportowania do określonego obszaru roboczego. Aby uzyskać więcej informacji na temat tej opcji, zobacz [Włącz automatyczną obsługę administracyjną agenta log Analytics](../../security-center/security-center-enable-data-collection.md#auto-provision-mma).
>

Jeśli konieczne jest skonfigurowanie agenta w celu raportowania do więcej niż jednego obszaru roboczego, nie można wykonać tej operacji podczas początkowej konfiguracji, dopiero później przez zaktualizowanie ustawień z panelu sterowania lub programu PowerShell zgodnie z opisem w temacie [Dodawanie lub usuwanie obszaru roboczego](agent-manage.md#adding-or-removing-a-workspace).  

Aby poznać obsługiwaną konfigurację, przejrzyj tematy dotyczące [obsługiwanych systemów operacyjnych Windows](log-analytics-agent.md#supported-windows-operating-systems) oraz [konfiguracji zapory sieciowej](log-analytics-agent.md#network-requirements).

## <a name="obtain-workspace-id-and-key"></a>Uzyskiwanie identyfikatora i klucza obszaru roboczego
Przed zainstalowaniem agenta Log Analytics dla systemu Windows musisz mieć identyfikator i klucz obszaru roboczego dla obszaru roboczego Log Analytics.  Te informacje są wymagane podczas instalacji z każdej metody instalacji w celu poprawnego skonfigurowania agenta i upewnienia się, że może on pomyślnie komunikować się z Azure Monitor w chmurze dla instytucji rządowych na platformie Azure. 

1. W Azure Portal Wyszukaj i wybierz pozycję **log Analytics obszary robocze**.
2. Na liście obszarów roboczych Log Analytics wybierz obszar roboczy, w którym ma zostać skonfigurowany Agent do raportowania.
3. Wybierz pozycję **Ustawienia zaawansowane**.<br><br> ![Ustawienia zaawansowane usługi Log Analytics](media/agent-windows/log-analytics-advanced-settings-01.png)<br><br>  
4. Wybierz **Połączone źródła**, a następnie **Serwery Windows**.   
5. Skopiuj i wklej w ulubionym edytorze, **Identyfikator obszaru roboczego** i **klucz podstawowy**.    
   
## <a name="configure-agent-to-use-tls-12"></a>Konfigurowanie agenta do korzystania z protokołu TLS 1,2
Aby skonfigurować użycie protokołu [TLS 1,2](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings#tls-12) do komunikacji między agentem systemu Windows a usługą log Analytics, można wykonać poniższe kroki, aby włączyć, zanim Agent zostanie zainstalowany na maszynie wirtualnej lub później.

>[!NOTE]
>Jeśli konfigurujesz maszynę wirtualną z systemem Windows Server 2008 z dodatkiem SP2 x64 do korzystania z protokołu TLS 1,2, przed wykonaniem poniższych kroków należy najpierw zainstalować następującą [aktualizację obsługi podpisywania kodu SHA-2](https://support.microsoft.com/help/4474419/sha-2-code-signing-support-update) . 
>

1. Zlokalizuj następujący podklucz rejestru: **HKEY_LOCAL_MACHINE \system\currentcontrolset\control\securityproviders\schannel\protocols**
2. Utwórz podklucz w obszarze **Protokoły** dla protokołu TLS 1,2 **HKLM\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1,2**
3. Utwórz podklucz **klienta** w podkluczu wersji protokołu TLS 1,2 utworzonego wcześniej. Na przykład **HKLM\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2 \ Client**.
4. Utwórz następujące wartości DWORD w obszarze **HKLM\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2 \ Client**:

    * **Włączone** [wartość = 1]
    * **DisabledByDefault** [wartość = 0]  

Skonfiguruj .NET Framework 4,6 lub nowszy, aby obsługiwać szyfrowanie Secure, tak jak domyślnie jest ono wyłączone. [Silne Kryptografia](https://docs.microsoft.com/dotnet/framework/network-programming/tls#schusestrongcrypto) korzysta z bezpieczniejszych protokołów sieciowych, takich jak TLS 1,2, i blokuje protokoły, które nie są bezpieczne. 

1. Zlokalizuj następujący podklucz rejestru: **HKEY_LOCAL_MACHINE \SOFTWARE\Microsoft \\ . NETFramework\v4.0.30319**.  
2. Utwórz wartość DWORD **schusestrongcrypto we** w tym podkluczu o wartości **1**.  
3. Zlokalizuj następujący podklucz rejestru: **HKEY_LOCAL_MACHINE \software\wow6432node\microsoft \\ . NETFramework\v4.0.30319**.  
4. Utwórz wartość DWORD **schusestrongcrypto we** w tym podkluczu o wartości **1**. 
5. Aby ustawienia zaczęły obowiązywać, należy ponownie uruchomić system. 

## <a name="install-the-agent-using-setup-wizard"></a>Instalowanie agenta za pomocą Kreatora instalacji
Poniższe kroki instalują i konfigurują agenta Log Analytics na platformie Azure i w chmurze Azure Government przy użyciu Kreatora instalacji agenta na komputerze. Jeśli chcesz dowiedzieć się, jak skonfigurować agenta do raportowania do System Center Operations Manager grupy zarządzania, zobacz [wdrażanie agenta Operations Manager za pomocą Kreatora instalacji agenta](https://docs.microsoft.com/system-center/scom/manage-deploy-windows-agent-manually#to-deploy-the-operations-manager-agent-with-the-agent-setup-wizard).

1. W obszarze roboczym Log Analytics na stronie **serwery z systemem Windows** , do której przejdziesz wcześniej, wybierz odpowiednią wersję **agenta pobierania systemu Windows** do pobrania w zależności od architektury procesora systemu operacyjnego Windows.   
2. Uruchom Instalatora, aby zainstalować agenta na komputerze.
2. Na **stronie powitalnej** kliknij przycisk **Dalej**.
3. Na stronie **Postanowienia licencyjne** zapoznaj się z postanowieniami licencyjnymi, a następnie kliknij przycisk **Zgadzam się**.
4. Na stronie **Folder docelowy** zmień lub pozostaw domyślny folder instalacji, a następnie kliknij przycisk **Dalej**.
5. Na stronie **Opcje instalacji agenta** wybierz połączenie agenta z usługą Azure Log Analytics, a następnie kliknij przycisk **Dalej**.   
6. Na stronie **Azure Log Analytics** wykonaj następujące czynności:
   1. Wklej skopiowany wcześniej **identyfikator obszaru roboczego** i **klucz obszaru roboczego (klucz podstawowy)**.  Jeśli komputer powinien wysyłać raporty do obszaru roboczego usługi Log Analytics w chmurze Azure dla instytucji rządowych, wybierz **Wersja platformy Azure dla administracji USA** z listy rozwijanej **Azure Cloud**.  
   2. Jeśli komputer musi komunikować się z usługą Log Analytics za pośrednictwem serwera proxy, kliknij pozycję **Zaawansowane** i podaj adres URL i numer portu serwera proxy.  Jeśli Twój serwer proxy wymaga uwierzytelniania, wpisz nazwę użytkownika i hasło, aby uwierzytelnić się na serwerze proxy, a następnie kliknij przycisk **Dalej**.  
7. Po zakończeniu podawania niezbędnych ustawień konfiguracji kliknij przycisk **Dalej**.<br><br> ![Wklejanie klucza podstawowego i identyfikatora obszaru roboczego](media/agent-windows/log-analytics-mma-setup-laworkspace.png)<br><br>
8. Na stronie **Gotowe do zainstalowania** przejrzyj wybrane opcje, a następnie kliknij pozycję **Zainstaluj**.
9. Na stronie **Konfiguracja została zakończona pomyślnie** kliknij przycisk **Zakończ**.

Po ukończeniu instalacji program **Microsoft Monitoring Agent** będzie wyświetlany w **Panelu sterowania**. Aby potwierdzić, że jest on raportowany do Log Analytics, przejrzyj temat [Weryfikowanie łączności agenta z log Analytics](#verify-agent-connectivity-to-log-analytics). 

## <a name="install-the-agent-using-the-command-line"></a>Instalowanie agenta przy użyciu wiersza polecenia
Pobrany plik dla agenta to samodzielny pakiet instalacyjny.  Program instalacyjny agenta i plików pomocniczych są zawarte w pakiecie i muszą zostać wyodrębnione w celu poprawnej instalacji przy użyciu wiersza polecenia pokazanego w poniższych przykładach.    

>[!NOTE]
>Jeśli chcesz uaktualnić agenta, musisz użyć interfejsu API obsługi skryptów Log Analytics. Aby uzyskać więcej informacji, zobacz temat [zarządzanie log Analytics agentem dla systemów Windows i Linux](agent-manage.md) .

W poniższej tabeli przedstawiono określone parametry obsługiwane przez Instalatora dla agenta, w tym informacje o wdrażaniu przy użyciu Automation DSC.

|Opcje specyficzne dla MMA                   |Uwagi         |
|---------------------------------------|--------------|
| NOAPM=1                               | Parametr opcjonalny. Instaluje agenta programu bez programu .NET Application Performance Monitoring.|   
|ADD_OPINSIGHTS_WORKSPACE               | 1 = Skonfiguruj agenta do raportowania do obszaru roboczego                |
|OPINSIGHTS_WORKSPACE_ID                | Identyfikator obszaru roboczego (GUID) dla obszaru roboczego do dodania                    |
|OPINSIGHTS_WORKSPACE_KEY               | Klucz obszaru roboczego używany do początkowego uwierzytelniania przy użyciu obszaru roboczego |
|OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE  | Określ środowisko chmury, w którym znajduje się obszar roboczy <br> 0 = chmura komercyjna Azure (wartość domyślna) <br> 1 = Azure Government |
|OPINSIGHTS_PROXY_URL               | Identyfikator URI, który ma być używany przez serwer proxy |
|OPINSIGHTS_PROXY_USERNAME               | Nazwa użytkownika, aby uzyskać dostęp do uwierzytelnionego serwera proxy |
|OPINSIGHTS_PROXY_PASSWORD               | Hasło dostępu do uwierzytelnionego serwera proxy |

1. Do wyodrębnienia plików instalacyjnych agenta z wiersza polecenia z podwyższonym poziomem uprawnień `MMASetup-<platform>.exe /c` i pojawi się monit o ścieżkę, do której mają zostać wyodrębnione pliki.  Alternatywnie możesz określić ścieżkę, przekazując argumenty `MMASetup-<platform>.exe /c /t:<Full Path>` .  
2. Aby zainstalować agenta w trybie dyskretnym i skonfigurować go do raportowania do obszaru roboczego w chmurze komercyjnej platformy Azure, z folderu wyodrębnione pliki instalacyjne do wpisania: 
   
     ```dos
    setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE=0 OPINSIGHTS_WORKSPACE_ID="<your workspace ID>" OPINSIGHTS_WORKSPACE_KEY="<your workspace key>" AcceptEndUserLicenseAgreement=1
    ```

   Aby skonfigurować agenta do raportowania w chmurze platformy Azure dla instytucji rządowych, wpisz: 

     ```dos
    setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE=1 OPINSIGHTS_WORKSPACE_ID="<your workspace ID>" OPINSIGHTS_WORKSPACE_KEY="<your workspace key>" AcceptEndUserLicenseAgreement=1
    ```
    >[!NOTE]
    >Wartości ciągu parametrów *OPINSIGHTS_WORKSPACE_ID* i *OPINSIGHTS_WORKSPACE_KEY* muszą być hermetyzowane w podwójnych cudzysłowach, aby nakazać Instalator Windows Interprit jako prawidłowe opcje pakietu. 

## <a name="install-the-agent-using-dsc-in-azure-automation"></a>Zainstaluj agenta przy użyciu usługi DSC w Azure Automation

Aby zainstalować agenta za pomocą Azure Automation DSC, można użyć następującego przykładowego skryptu.   Jeśli nie masz konta usługi Automation, zobacz artykuł Wprowadzenie do [Azure Automation](/azure/automation/) , aby poznać wymagania i kroki związane z tworzeniem konta usługi Automation wymaganego przed użyciem usługi Automation DSC.  Jeśli nie znasz Automation DSC, zapoznaj [się z tematem wprowadzenie do Automation DSC](../../automation/automation-dsc-getting-started.md).

W poniższym przykładzie jest instalowany Agent 64-bitowy identyfikowany przez `URI` wartość. Możesz również użyć wersji 32-bitowej, zastępując wartość identyfikatora URI. Identyfikatory URI obu wersji są następujące:

- Windows 64-bit Agent-https://go.microsoft.com/fwlink/?LinkId=828603
- Windows 32-bit Agent-https://go.microsoft.com/fwlink/?LinkId=828604


>[!NOTE]
>Ta procedura i przykład skryptu nie obsługują uaktualniania agenta już wdrożonego na komputerze z systemem Windows.

32-bitowe i 64-bitowe wersje pakietu agenta mają różne kody produktów i wydane nowe wersje mają również unikatową wartość.  Kod produktu jest identyfikatorem GUID, który jest identyfikatorem podmiotu zabezpieczeń aplikacji lub produktu i jest reprezentowany przez właściwość Instalator Windows **ProductCode** .  `ProductId`Wartość w skrypcie **MMAgent.ps1** musi pasować do kodu produktu z pakietu Instalatora agenta 32-bitowego lub 64-bitowego.

Aby bezpośrednio pobrać kod produktu z pakietu instalacji agenta, można użyć Orca.exe ze [składników Windows SDK dla Instalator Windows deweloperów](https://msdn.microsoft.com/library/windows/desktop/aa370834%28v=vs.85%29.aspx) , którzy są składnikiem zestawu Windows Software Development Kit lub przy użyciu programu PowerShell, po [przykładowym skrypcie](https://www.scconfigmgr.com/2014/08/22/how-to-get-msi-file-information-with-powershell/) zapisanym przez firmę Microsoft (MVP).  W obu przypadkach najpierw należy wyodrębnić plik **MOMagent.msi** z pakietu instalacyjnego MMASetup.  Jest to pokazane wcześniej w pierwszym kroku w sekcji [Instalowanie agenta przy użyciu wiersza polecenia](#install-the-agent-using-the-command-line).  

1. Zaimportuj moduł xPSDesiredStateConfiguration DSC z [https://www.powershellgallery.com/packages/xPSDesiredStateConfiguration](https://www.powershellgallery.com/packages/xPSDesiredStateConfiguration) do Azure Automation.  
2.    Utwórz Azure Automation zasobów zmiennych dla *OPSINSIGHTS_WS_ID* i *OPSINSIGHTS_WS_KEY*. Ustaw *OPSINSIGHTS_WS_ID* na identyfikator obszaru roboczego log Analytics i ustaw *OPSINSIGHTS_WS_KEY* klucz podstawowy obszaru roboczego.
3.    Skopiuj skrypt i Zapisz go jako MMAgent.ps1.

```powershell
Configuration MMAgent
{
    $OIPackageLocalPath = "C:\Deploy\MMASetup-AMD64.exe"
    $OPSINSIGHTS_WS_ID = Get-AutomationVariable -Name "OPSINSIGHTS_WS_ID"
    $OPSINSIGHTS_WS_KEY = Get-AutomationVariable -Name "OPSINSIGHTS_WS_KEY"

    Import-DscResource -ModuleName xPSDesiredStateConfiguration
    Import-DscResource -ModuleName PSDesiredStateConfiguration

    Node OMSnode {
        Service OIService
        {
            Name = "HealthService"
            State = "Running"
            DependsOn = "[Package]OI"
        }

        xRemoteFile OIPackage {
            Uri = "https://go.microsoft.com/fwlink/?LinkId=828603"
            DestinationPath = $OIPackageLocalPath
        }

        Package OI {
            Ensure = "Present"
            Path  = $OIPackageLocalPath
            Name = "Microsoft Monitoring Agent"
            ProductId = "8A7F2C51-4C7D-4BFD-9014-91D11F24AAE2"
            Arguments = '/C:"setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_ID=' + $OPSINSIGHTS_WS_ID + ' OPINSIGHTS_WORKSPACE_KEY=' + $OPSINSIGHTS_WS_KEY + ' AcceptEndUserLicenseAgreement=1"'
            DependsOn = "[xRemoteFile]OIPackage"
        }
    }
}

```

4. Zaktualizuj `ProductId` wartość w skrypcie o kod produktu wyodrębniony z najnowszej wersji pakietu instalacji agenta przy użyciu zalecanych wcześniej metod. 
5. [Zaimportuj skrypt konfiguracji MMAgent.ps1](../../automation/automation-dsc-getting-started.md#import-a-configuration-into-azure-automation) na konto usługi Automation. 
6. [Przypisz komputer lub węzeł systemu Windows](../../automation/automation-dsc-getting-started.md#enable-an-azure-resource-manager-vm-for-management-with-state-configuration) do konfiguracji. W ciągu 15 minut węzeł sprawdza swoją konfigurację, a agent jest wypychany do węzła.

## <a name="verify-agent-connectivity-to-log-analytics"></a>Sprawdź łączność z agentem Log Analytics

Po zakończeniu instalacji agenta Weryfikowanie jego pomyślnego połączenia i raportowanie można wykonać na dwa sposoby.  

W **Panelu sterowania** na komputerze znajdź element **Microsoft Monitoring Agent**.  Wybierz go i na karcie **log Analytics Azure** Agent powinien wyświetlić komunikat z informacją: **Microsoft Monitoring Agent pomyślnie nawiązał połączenie z usługą Microsoft Operations Management Suite.**<br><br> ![Stan połączenia programu MMA z usługą Log Analytics](media/agent-windows/log-analytics-mma-laworkspace-status.png)

Możesz również wykonać prostą kwerendę dziennika w Azure Portal.  

1. W Azure Portal Wyszukaj i wybierz pozycję **monitor**.
1. Wybierz pozycję **dzienniki** w menu.
1. W okienku **dzienniki** w polu Typ zapytania wpisz:  

    ```
    Heartbeat 
    | where Category == "Direct Agent" 
    | where TimeGenerated > ago(30m)  
    ```

W zwróconych wynikach wyszukiwania powinny być widoczne rekordy pulsu dla komputera, który wskazuje, że jest on połączony i zgłaszany do usługi.   

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z tematem [Zarządzanie agentem log Analytics dla systemów Windows i Linux oraz ich obsługiwanie](agent-manage.md) , aby dowiedzieć się, jak ponownie skonfigurować, uaktualnić lub usunąć agenta z maszyny wirtualnej.

- Sprawdź [Rozwiązywanie problemów z agentem systemu Windows,](agent-windows-troubleshoot.md) Jeśli wystąpią problemy podczas instalowania agenta lub zarządzania nim.
