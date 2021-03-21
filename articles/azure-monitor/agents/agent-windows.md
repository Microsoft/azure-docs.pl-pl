---
title: Instalowanie agenta usługi Log Analytics na komputerach z systemem Windows
description: W tym artykule opisano sposób łączenia komputerów z systemem Windows hostowanych w innych chmurach lub lokalnych w celu Azure Monitor z agentem Log Analytics dla systemu Windows.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/03/2020
ms.openlocfilehash: aec39b86f9651539028efce93ba6a88c3be75b0c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102038314"
---
# <a name="install-log-analytics-agent-on-windows-computers"></a>Instalowanie agenta usługi Log Analytics na komputerach z systemem Windows
Ten artykuł zawiera szczegółowe informacje dotyczące instalowania agenta Log Analytics na komputerach z systemem Windows przy użyciu następujących metod:

* Instalacja ręczna przy użyciu [Kreatora instalacji](#install-agent-using-setup-wizard) lub [wiersza polecenia](#install-agent-using-command-line).
* [Azure Automation konfiguracji żądanego stanu (DSC)](#install-agent-using-dsc-in-azure-automation). 

>[!IMPORTANT]
> Metody instalacji opisane w tym artykule są zwykle używane w przypadku maszyn wirtualnych lokalnie lub w innych chmurach. Zobacz [Opcje instalacji](./log-analytics-agent.md#installation-options) , aby uzyskać więcej wydajnych opcji, których można użyć w przypadku maszyn wirtualnych platformy Azure.

> [!NOTE]
> Jeśli konieczne jest skonfigurowanie agenta w celu raportowania do więcej niż jednego obszaru roboczego, nie można wykonać tej operacji podczas początkowej konfiguracji, dopiero później przez zaktualizowanie ustawień z panelu sterowania lub programu PowerShell zgodnie z opisem w temacie [Dodawanie lub usuwanie obszaru roboczego](agent-manage.md#adding-or-removing-a-workspace).  

## <a name="supported-operating-systems"></a>Obsługiwane systemy operacyjne

Listę wersji systemu Windows obsługiwanych przez agenta Log Analytics można znaleźć w temacie [Omówienie agentów Azure monitor](agents-overview.md#supported-operating-systems) .

### <a name="sha-2-code-signing-support-requirement"></a>Wymagania dotyczące obsługi podpisywania kodu SHA-2 
Agent systemu Windows rozpocznie korzystanie wyłącznie z podpisywania SHA-2 w dniu 17 sierpnia 2020. Ta zmiana wpłynie na klientów korzystających z agenta Log Analytics w ramach starszej wersji systemu operacyjnego w ramach dowolnej usługi platformy Azure (Azure Monitor, Azure Automation, Update Management platformy Azure, Change Tracking platformy Azure, Azure Security Center, platformy Azure, usługi Windows Defender ATP). Zmiana nie wymaga żadnej akcji klienta, chyba że Agent jest uruchomiony w starszej wersji systemu operacyjnego (Windows 7, Windows Server 2008 R2 i Windows Server 2008). Klienci korzystający ze starszej wersji systemu operacyjnego są zobowiązani do podjęcia następujących działań na swoich maszynach przed 17 sierpnia 2020 lub ich agenci przestają wysyłać dane do swoich Log Analyticsych obszarów roboczych:

1. Zainstaluj najnowszy dodatek Service Pack dla systemu operacyjnego. Wymagane wersje dodatku Service Pack to:
    - Windows 7 z dodatkiem SP1
    - Windows Server 2008 SP2
    - Windows Server 2008 R2 SP1

2. Zainstaluj w systemie operacyjnym Windows podpis SHA-2, zgodnie z opisem w artykule [2019 wymagania obsługi podpisywania kodu SHA-2 dla systemu Windows i programu WSUS](https://support.microsoft.com/help/4472027/2019-sha-2-code-signing-support-requirement-for-windows-and-wsus)
3. Zaktualizuj do najnowszej wersji agenta systemu Windows (wersja 10.20.18029).
4. Zalecane, aby skonfigurować agenta do [korzystania z protokołu TLS 1,2](agent-windows.md#configure-agent-to-use-tls-12). 

## <a name="network-requirements"></a>Wymagania dotyczące sieci
Zapoznaj się z [omówieniem log Analytics agenta](./log-analytics-agent.md#network-requirements) , aby poznać wymagania dotyczące sieci dla agenta systemu Windows.


   
## <a name="configure-agent-to-use-tls-12"></a>Konfigurowanie agenta do korzystania z protokołu TLS 1,2
Protokół [TLS 1,2](/windows-server/security/tls/tls-registry-settings#tls-12) zapewnia bezpieczeństwo danych przesyłanych w celu komunikacji między agentem systemu Windows a usługą log Analytics. Jeśli instalujesz program w [systemie operacyjnym bez włączonego protokołu tls 1,2](../logs/data-security.md#sending-data-securely-using-tls-12), należy skonfigurować protokół TLS 1,2, wykonując poniższe kroki.

1. Zlokalizuj następujący podklucz rejestru: **HKEY_LOCAL_MACHINE\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols**
2. Utwórz podklucz w obszarze **Protokoły** dla protokołu TLS 1,2 **HKLM\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1,2**
3. Utwórz podklucz **klienta** w podkluczu wersji protokołu TLS 1,2 utworzonego wcześniej. Na przykład **HKLM\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2 \ Client**.
4. Utwórz następujące wartości DWORD w obszarze **HKLM\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2 \ Client**:

    * **Włączone** [wartość = 1]
    * **DisabledByDefault** [wartość = 0]  

Skonfiguruj .NET Framework 4,6 lub nowszy, aby obsługiwać szyfrowanie Secure, tak jak domyślnie jest ono wyłączone. [Silne Kryptografia](/dotnet/framework/network-programming/tls#schusestrongcrypto) korzysta z bezpieczniejszych protokołów sieciowych, takich jak TLS 1,2, i blokuje protokoły, które nie są bezpieczne. 

1. Zlokalizuj następujący podklucz rejestru: **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\\ . NETFramework\v4.0.30319**.  
2. Utwórz wartość DWORD **schusestrongcrypto we** w tym podkluczu o wartości **1**.  
3. Zlokalizuj następujący podklucz rejestru: **HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\\ . NETFramework\v4.0.30319**.  
4. Utwórz wartość DWORD **schusestrongcrypto we** w tym podkluczu o wartości **1**. 
5. Aby ustawienia zaczęły obowiązywać, należy ponownie uruchomić system. 

## <a name="install-agent-using-setup-wizard"></a>Zainstaluj agenta za pomocą Kreatora instalacji
Poniższe kroki instalują i konfigurują agenta Log Analytics na platformie Azure i w chmurze Azure Government przy użyciu Kreatora instalacji agenta na komputerze. Jeśli chcesz dowiedzieć się, jak skonfigurować agenta do raportowania do System Center Operations Manager grupy zarządzania, zobacz [wdrażanie agenta Operations Manager za pomocą Kreatora instalacji agenta](/system-center/scom/manage-deploy-windows-agent-manually#to-deploy-the-operations-manager-agent-with-the-agent-setup-wizard).

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

Po ukończeniu instalacji program **Microsoft Monitoring Agent** będzie wyświetlany w **Panelu sterowania**. Aby potwierdzić, że jest on raportowany do Log Analytics, przejrzyj temat [Weryfikowanie łączności agenta z log Analytics](#verify-agent-connectivity-to-azure-monitor). 

## <a name="install-agent-using-command-line"></a>Zainstaluj agenta przy użyciu wiersza polecenia
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

## <a name="install-agent-using-dsc-in-azure-automation"></a>Zainstaluj agenta przy użyciu usługi DSC w Azure Automation

Aby zainstalować agenta za pomocą Azure Automation DSC, można użyć następującego przykładowego skryptu.   Jeśli nie masz konta usługi Automation, zobacz artykuł Wprowadzenie do [Azure Automation](../../automation/index.yml) , aby poznać wymagania i kroki związane z tworzeniem konta usługi Automation wymaganego przed użyciem usługi Automation DSC.  Jeśli nie znasz Automation DSC, zapoznaj [się z tematem wprowadzenie do Automation DSC](../../automation/automation-dsc-getting-started.md).

W poniższym przykładzie jest instalowany Agent 64-bitowy identyfikowany przez `URI` wartość. Możesz również użyć wersji 32-bitowej, zastępując wartość identyfikatora URI. Identyfikatory URI obu wersji są następujące:

- Windows 64-bit Agent- https://go.microsoft.com/fwlink/?LinkId=828603
- Windows 32-bit Agent- https://go.microsoft.com/fwlink/?LinkId=828604


>[!NOTE]
>Ta procedura i przykład skryptu nie obsługują uaktualniania agenta już wdrożonego na komputerze z systemem Windows.

32-bitowe i 64-bitowe wersje pakietu agenta mają różne kody produktów i wydane nowe wersje mają również unikatową wartość.  Kod produktu jest identyfikatorem GUID, który jest identyfikatorem podmiotu zabezpieczeń aplikacji lub produktu i jest reprezentowany przez właściwość Instalator Windows **ProductCode** .  `ProductId`Wartość w skrypcie **MMAgent.ps1** musi pasować do kodu produktu z pakietu Instalatora agenta 32-bitowego lub 64-bitowego.

Aby bezpośrednio pobrać kod produktu z pakietu instalacji agenta, można użyć Orca.exe ze [składników Windows SDK dla Instalator Windows deweloperów](/windows/win32/msi/platform-sdk-components-for-windows-installer-developers) , którzy są składnikiem zestawu Windows Software Development Kit lub przy użyciu programu PowerShell, po [przykładowym skrypcie](https://www.scconfigmgr.com/2014/08/22/how-to-get-msi-file-information-with-powershell/)  zapisanym przez firmę Microsoft (MVP).  W obu przypadkach najpierw należy wyodrębnić plik **MOMagent.msi** z pakietu instalacyjnego MMASetup.  Jest to pokazane wcześniej w pierwszym kroku w sekcji [Instalowanie agenta przy użyciu wiersza polecenia](#install-agent-using-command-line).  

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

## <a name="verify-agent-connectivity-to-azure-monitor"></a>Sprawdź łączność z agentem Azure Monitor

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

## <a name="cache-information"></a>Informacje o pamięci podręcznej

Dane z agenta Log Analytics są buforowane na komputerze lokalnym w *katalogu C:\Program Files\Microsoft monitoring Agent\Agent\Health Service State* przed wysłaniem do Azure monitor. Agent próbuje przekazać co 20 sekund. Jeśli to się nie powiedzie, będzie oczekiwać wykładniczego wydłużenia czasu do momentu jego pomyślnego przeprowadzenia. Poczeka 30 sekund przed kolejną próbą, 60 sekund przed kolejnymi, 120 sekund i tak dalej, aż do maksymalnie 8,5 godzin między ponownymi próbami nawiązania połączenia. Ten czas oczekiwania jest nieco losowy, aby uniknąć jednoczesnych prób nawiązania połączenia przez wszystkich agentów. Po osiągnięciu maksymalnego buforu najstarsze dane są odrzucane.

Domyślny rozmiar pamięci podręcznej to 50 MB, ale można go skonfigurować w zakresie co najmniej 5 MB i maksymalnie 1,5 GB. Jest ona przechowywana w kluczu rejestru *HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\HealthService\Parameters\Persistence Cache Maximum*. Wartość reprezentuje liczbę stron z 8 KB na stronę.


## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z tematem [Zarządzanie agentem log Analytics dla systemów Windows i Linux oraz ich obsługiwanie](agent-manage.md) , aby dowiedzieć się, jak ponownie skonfigurować, uaktualnić lub usunąć agenta z maszyny wirtualnej.

- Sprawdź [Rozwiązywanie problemów z agentem systemu Windows,](agent-windows-troubleshoot.md) Jeśli wystąpią problemy podczas instalowania agenta lub zarządzania nim.