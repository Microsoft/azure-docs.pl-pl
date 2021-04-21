---
title: Omówienie agenta połączonej maszyny
description: Ten artykuł zawiera szczegółowe omówienie agenta serwerów z obsługą Azure Arc, który obsługuje monitorowanie maszyn wirtualnych hostowanych w środowiskach hybrydowych.
ms.date: 03/25/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: dd58997a8af86a3789895bfb29bfd5803826fa6f
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107832963"
---
# <a name="overview-of-azure-arc-enabled-servers-agent"></a>Omówienie agenta Azure Arc serwerów z włączoną obsługą

Agent Connected Machine Azure Arc serwerów z obsługą sieci umożliwia zarządzanie maszynami z systemami Windows i Linux hostowanych poza platformą Azure w sieci firmowej lub u innego dostawcy usług w chmurze. Ten artykuł zawiera szczegółowe omówienie agenta, wymagań dotyczących systemu i sieci oraz różnych metod wdrażania.

>[!NOTE]
>Począwszy od ogólnej wersji serwerów z obsługą usługi Azure Arc we wrześniu 2020 r., wszystkie wersje wstępne agenta programu Azure Connected Machine (agenci z wersjami mniejszymi niż 1.0) są przestarzałe do  **2 lutego 2021 r.**.  Ten okres umożliwia uaktualnienie do wersji 1.0 lub wyższej, zanim wstępnie zwolnieni agenci nie będą już mogli komunikować się z usługą Azure Arc serwerami.

## <a name="agent-component-details"></a>Szczegóły składnika agenta

:::image type="content" source="media/agent-overview/connected-machine-agent.png" alt-text="Omówienie agenta serwerów z obsługą usługi Arc." border="false":::

Pakiet Azure Connected Machine agentów zawiera kilka składników logicznych, które są ze sobą połączone.

* Usługa metadanych wystąpienia hybrydowego (HIMDS) zarządza połączeniem z platformą Azure i tożsamością platformy Azure połączonej maszyny.

* Agent konfiguracji gościa udostępnia In-Guest i konfigurację gościa, takie jak ocena, czy maszyna jest zgodna z wymaganymi zasadami.

    Zwróć uwagę na następujące zachowanie Azure Policy [konfiguracji gościa](../../governance/policy/concepts/guest-configuration.md) dla odłączonego komputera:

    * Nie ma to wpływu na przypisanie zasad konfiguracji gościa, które dotyczy odłączonych maszyn.
    * Przypisanie gościa jest przechowywane lokalnie przez 14 dni. Jeśli w ciągu 14 dni agent połączonej maszyny nawiąze połączenie ponownie z usługą, przypisania zasad zostaną ponowniestosowane.
    * Przypisania są usuwane po upływie 14 dni i nie są ponownie przypisywani do maszyny po upływie 14 dni.

* Agent rozszerzenia zarządza rozszerzeniami maszyn wirtualnych, w tym instalowaniem, odinstalowywaniem i uaktualnianie. Rozszerzenia są pobierane z platformy Azure i kopiowane do folderu w systemie Windows, a dla `%SystemDrive%\%ProgramFiles%\AzureConnectedMachineAgent\ExtensionService\downloads` systemu Linux do folderu `/opt/GC_Ext/downloads` . W systemie Windows rozszerzenie jest instalowane w następującej ścieżce , a w systemie `%SystemDrive%\Packages\Plugins\<extension>` Linux rozszerzenie jest instalowane w ścieżce `/var/lib/waagent/<extension>` .

## <a name="instance-metadata"></a>Metadane wystąpienia

Informacje o metadanych dotyczące podłączonej maszyny są zbierane po zarejestrowaniu agenta Connected Machine na serwerach z usługą Arc. W szczególności:

* Nazwa, typ i wersja systemu operacyjnego
* Nazwa komputera
* W pełni kwalifikowana nazwa domeny komputera (FQDN)
* Wersja agenta połączonej maszyny
* W pełni kwalifikowana nazwa domeny (FQDN) usług Active Directory i DNS
* UUID (identyfikator BIOS)
* Puls agenta połączonej maszyny
* Wersja agenta połączonej maszyny
* Klucz publiczny dla tożsamości zarządzanej
* Stan i szczegóły zgodności zasad (w przypadku używania Azure Policy konfiguracji gościa)

Agent platformy Azure żąda następujących informacji o metadanych:

* Lokalizacja zasobu (region)
* Identyfikator maszyny wirtualnej
* Tagi
* Azure Active Directory tożsamości zarządzanej
* Przypisania zasad konfiguracji gościa
* Żądania rozszerzenia — instalowanie, aktualizowanie i usuwanie.

## <a name="download-agents"></a>Pobieranie agentów

Możesz pobrać pakiet agenta Azure Connected Machine dla systemów Windows i Linux z lokalizacji wymienionych poniżej.

* [Pakiet agenta systemu Windows Instalator Windows z](https://aka.ms/AzureConnectedMachineAgent) Centrum pobierania Microsoft.

* Pakiet agenta systemu Linux jest dystrybuowany z [repozytorium pakietów](https://packages.microsoft.com/) firmy Microsoft przy użyciu preferowanego formatu pakietu dla dystrybucji (. RPM lub . DEB).

Agenta Azure Connected Machine dla systemów Windows i Linux można uaktualnić do najnowszej wersji ręcznie lub automatycznie w zależności od wymagań. Aby uzyskać więcej informacji, zobacz [tutaj](manage-agent.md).

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="supported-environments"></a>Obsługiwane środowiska

Serwery z usługą Arc obsługują instalację agenta Connected Machine na dowolnym serwerze fizycznym i maszynie wirtualnej hostowanej *poza platformą* Azure. Dotyczy to również maszyn wirtualnych działających na platformach takich jak VMware, Azure Stack HCI i innych środowiskach w chmurze. Serwery z usługą Arc nie obsługują instalowania agenta na maszynach wirtualnych działających na platformie Azure ani na maszynach wirtualnych uruchomionych w usłudze Azure Stack Hub lub Azure Stack Edge, ponieważ są one już modelowane jako maszyny wirtualne platformy Azure.

### <a name="supported-operating-systems"></a>Obsługiwane systemy operacyjne

Następujące wersje systemu operacyjnego Windows i Linux są oficjalnie obsługiwane przez agenta Azure Connected Machine operacyjnego:

- Windows Server 2008 R2, Windows Server 2012 R2 i wyższe (w tym Server Core)
- Ubuntu 16.04 i 18.04 LTS (x64)
- CentOS Linux 7 (x64)
- SUSE Linux Enterprise Server (SLES) 15 (x64)
- Red Hat Enterprise Linux (RHEL) 7 (x64)
- Amazon Linux 2 (x64)
- Oracle Linux 7

> [!WARNING]
> Nazwa hosta systemu Linux lub nazwa komputera z systemem Windows nie może używać jednego z zastrzeżonych słów lub znaków towarowych w nazwie. W przeciwnym razie próba zarejestrowania połączonej maszyny na platformie Azure nie powiedzie się. Aby [uzyskać listę zastrzeżonych wyrazów,](../../azure-resource-manager/templates/error-reserved-resource-name.md) zobacz Resolve reserved resource name errors (Rozwiązywanie błędów nazw zasobów zarezerwowanych).

### <a name="required-permissions"></a>Wymagane uprawnienia

* Aby dołączać maszyny, należysz do Azure Connected Machine **dołączania** lub [współautora](../../role-based-access-control/built-in-roles.md#contributor) w grupie zasobów.

* Aby odczytywać, modyfikować i usuwać maszynę, należysz do roli **Azure Connected Machine Resource Administrator** w grupie zasobów.

* Aby wybrać grupę zasobów z listy rozwijanej w przypadku korzystania z metody Generuj skrypt, musisz co najmniej być członkiem roli Czytelnik dla tej grupy zasobów.  [](../../role-based-access-control/built-in-roles.md#reader)

### <a name="azure-subscription-and-service-limits"></a>Limity subskrypcji i usług platformy Azure

Przed skonfigurowaniem maszyn przy użyciu Azure Arc serwerów z [](../../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits) włączoną [](../../azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits) obsługą usługi Azure Resource Manager zapoznaj się z limitami subskrypcji usługi Azure Resource Manager i limitami grup zasobów, aby zaplanować liczbę maszyn do podłączenia.

Azure Arc obsługuje do 5000 wystąpień maszyn w grupie zasobów.

### <a name="transport-layer-security-12-protocol"></a>Transport Layer Security 1.2

W celu zapewnienia bezpieczeństwa danych podczas przesyłania na platformę Azure zdecydowanie zachęcamy do skonfigurowania maszyny do korzystania z Transport Layer Security (TLS) 1.2. Starsze wersje protokołu TLS/Secure Sockets Layer (SSL) są podatne na zagrożenia i mimo że nadal działają w celu zapewnienia zgodności z poprzednimi wersjami, nie **są zalecane.**

|Platforma/język | Pomoc techniczna | Więcej informacji |
| --- | --- | --- |
|Linux | Dystrybucje systemu Linux zwykle polegają na [openssl](https://www.openssl.org) do obsługi TLS 1.2. | Sprawdź dokument [OpenSSL Changelog,](https://www.openssl.org/news/changelog.html) aby potwierdzić, że twoja wersja programu OpenSSL jest obsługiwana.|
| Windows Server 2012 R2 i wyższe | Obsługiwane i domyślnie włączone. | Aby potwierdzić, że nadal używasz ustawień [domyślnych](/windows-server/security/tls/tls-registry-settings).|

### <a name="networking-configuration"></a>Konfiguracja sieci

Agent Connected Machine dla systemów Linux i Windows komunikuje się bezpiecznie z Azure Arc przez port TCP 443. Jeśli maszyna łączy się za pośrednictwem zapory lub serwera proxy w celu komunikowania się przez Internet, zapoznaj się z poniższymi tematami, aby zrozumieć wymagania dotyczące konfiguracji sieci.

> [!NOTE]
> Serwery z usługą Arc nie obsługują używania [bramy usługi Log Analytics](../../azure-monitor/agents/gateway.md) jako serwera proxy dla agenta Connected Machine.
>

Jeśli łączność wychodząca jest ograniczona przez zaporę lub serwer proxy, upewnij się, że adresy URL wymienione poniżej nie są blokowane. Jeśli zezwalasz tylko na zakresy adresów IP lub nazwy domen wymagane przez agenta do komunikowania się z usługą, musisz zezwolić na dostęp do następujących tagów usługi i adresów URL.

Tagi usługi:

* AzureActiveDirectory
* AzureTrafficManager
* AzureResourceManager
* AzureArcInfrastructure

Adresy url:

| Zasób agenta | Opis |
|---------|---------|
|`management.azure.com`|Azure Resource Manager|
|`login.windows.net`|Azure Active Directory|
|`login.microsoftonline.com`|Azure Active Directory|
|`dc.services.visualstudio.com`|Application Insights|
|`*.guestconfiguration.azure.com` |Konfiguracja gościa|
|`*.his.arc.azure.com`|Usługa tożsamości hybrydowej|
|`www.office.com`|Office 365|

Agenci w wersji zapoznawczej (w wersji 0.11 lub niższej) również wymagają dostępu do następujących adresów URL:

| Zasób agenta | Opis |
|---------|---------|
|`agentserviceapi.azure-automation.net`|Konfiguracja gościa|
|`*-agentservice-prod-1.azure-automation.net`|Konfiguracja gościa|

Aby uzyskać listę adresów IP dla każdego tagu usługi/regionu, zobacz plik JSON — Zakresy adresów IP platformy Azure i Tagi usługi [— chmura publiczna.](https://www.microsoft.com/download/details.aspx?id=56519) Firma Microsoft publikuje cotygodniowe aktualizacje zawierające każdą usługę platformy Azure i używane zakresy adresów IP. Te informacje w pliku JSON to bieżąca lista zakresów adresów IP odpowiadających każdemu tagowi usługi. Adresy IP mogą ulec zmianie. Jeśli zakresy adresów IP są wymagane dla konfiguracji zapory, należy użyć tagu usługi **AzureCloud,** aby zezwolić na dostęp do wszystkich usług platformy Azure. Nie wyłączaj monitorowania zabezpieczeń ani inspekcji tych adresów URL, zezwalaj na nie tak jak w przypadku innego ruchu internetowego.

Aby uzyskać więcej informacji, zapoznaj się z [omówieniem tagów usługi](../../virtual-network/service-tags-overview.md).

### <a name="register-azure-resource-providers"></a>Rejestrowanie dostawców zasobów platformy Azure

Azure Arc serwerów z włączoną obsługą usługi zależy od następujących dostawców zasobów platformy Azure w ramach subskrypcji w celu korzystania z tej usługi:

* **Microsoft.HybridCompute**
* **Microsoft.GuestConfiguration**

Jeśli nie są one zarejestrowane, możesz je zarejestrować przy użyciu następujących poleceń:

Azure PowerShell:

```azurepowershell-interactive
Login-AzAccount
Set-AzContext -SubscriptionId [subscription you want to onboard]
Register-AzResourceProvider -ProviderNamespace Microsoft.HybridCompute
Register-AzResourceProvider -ProviderNamespace Microsoft.GuestConfiguration
```

Interfejs wiersza polecenia platformy Azure:

```azurecli-interactive
az account set --subscription "{Your Subscription Name}"
az provider register --namespace 'Microsoft.HybridCompute'
az provider register --namespace 'Microsoft.GuestConfiguration'
```

Dostawców zasobów można również zarejestrować w Azure Portal, korzystając z procedury opisanej w [Azure Portal](../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal).

## <a name="installation-and-configuration"></a>Instalacja i konfiguracja

Łączenie maszyn w środowisku hybrydowym bezpośrednio z platformą Azure można wykonać przy użyciu różnych metod w zależności od wymagań. W poniższej tabeli przedstawiono każdą metodę, aby określić, która metoda najlepiej sprawdza się w Twojej organizacji.

> [!IMPORTANT]
> Agenta Connected Machine nie można zainstalować na maszynie wirtualnej platformy Azure z systemem Windows. W przypadku próby instalacja wykryje to i wycofuje.

| Metoda | Opis |
|--------|-------------|
| Interaktywnie | Ręcznie zainstaluj agenta na jednej lub małej liczbie maszyn, zgodnie z instrukcjami w tece [Connect machines from Azure Portal](onboard-portal.md).<br> Z Azure Portal można wygenerować skrypt i wykonać go na maszynie, aby zautomatyzować kroki instalacji i konfiguracji agenta.|
| Na dużą skalę | Zainstaluj i skonfiguruj agenta dla wielu maszyn, korzystając z opcji [Połącz maszyny przy użyciu jednostki usługi.](onboard-service-principal.md)<br> Ta metoda tworzy jednostkę usługi w celu łączenia maszyn w sposób nieinterakcyjny.|
| Na dużą skalę | Zainstaluj i skonfiguruj agenta dla wielu maszyn, korzystając z metody [Using Windows PowerShell DSC](onboard-dsc.md).<br> Ta metoda używa jednostki usługi do łączenia maszyn w sposób nieinterakcyjny z usługą DSC programu PowerShell. |

## <a name="connected-machine-agent-technical-overview"></a>Omówienie techniczne agenta połączonego komputera

### <a name="windows-agent-installation-details"></a>Szczegóły instalacji agenta systemu Windows

Agenta Connected Machine dla systemu Windows można zainstalować przy użyciu jednej z następujących trzech metod:

* Kliknij dwukrotnie plik `AzureConnectedMachineAgent.msi` .
* Ręcznie, uruchamiając pakiet Instalator Windows `AzureConnectedMachineAgent.msi` z powłoki poleceń.
* Z sesji programu PowerShell przy użyciu metody skryptowej.

Po zainstalowaniu agenta Connected Machine dla systemu Windows są stosowane następujące zmiany konfiguracji dla całego systemu.

* Następujące foldery instalacji są tworzone podczas instalacji.

    |Folder |Opis |
    |-------|------------|
    |%ProgramFiles%\AzureConnectedMachineAgent |Domyślna ścieżka instalacji zawierająca pliki obsługi agenta.|
    |%ProgramData%\AzureConnectedMachineAgent |Zawiera pliki konfiguracji agenta.|
    |%ProgramData%\AzureConnectedMachineAgent\Tokens |Zawiera uzyskane tokeny.|
    |%ProgramData%\AzureConnectedMachineAgent\Config |Zawiera plik konfiguracji agenta `agentconfig.json` rejestrujący informacje o rejestracji w usłudze.|
    |%ProgramFiles%\ArcConnectedMachineAgent\ExtensionService\GC | Ścieżka instalacji zawierająca pliki agenta konfiguracji gościa. |
    |%ProgramData%\GuestConfig |Zawiera (zastosowane) zasady z platformy Azure.|
    |%ProgramFiles%\AzureConnectedMachineAgent\ExtensionService\downloads | Rozszerzenia są pobierane z platformy Azure i kopiowane tutaj.|

* Podczas instalacji agenta na maszynie docelowej są tworzone następujące usługi systemu Windows.

    |Nazwa usługi |Nazwa wyświetlana |Nazwa procesu |Opis |
    |-------------|-------------|-------------|------------|
    |himds |Azure Hybrid Instance Metadata Service |himds |Ta usługa implementuje usługę Azure Instance Metadata Service (IMDS) do zarządzania połączeniem z platformą Azure i tożsamością platformy Azure połączonej maszyny.|
    |GCArcService |Usługa Arc konfiguracji gościa |gc_service |Monitoruje żądaną konfigurację stanu maszyny.|
    |ExtensionService |Usługa rozszerzenia konfiguracji gościa | gc_service |Instaluje wymagane rozszerzenia przeznaczone dla maszyny.|

* Podczas instalacji agenta są tworzone następujące zmienne środowiskowe.

    |Nazwa |Wartość domyślna |Opis |
    |-----|--------------|------------|
    |IDENTITY_ENDPOINT |http://localhost:40342/metadata/identity/oauth2/token ||
    |IMDS_ENDPOINT |http://localhost:40342 ||

* Istnieje kilka plików dziennika dostępnych do rozwiązywania problemów. Zostały one opisane w poniższej tabeli.

    |Dziennik |Opis |
    |----|------------|
    |%ProgramData%\AzureConnectedMachineAgent\Log\himds.log |Rejestruje szczegółowe informacje o usłudze agentów (HIMDS) i interakcji z platformą Azure.|
    |%ProgramData%\AzureConnectedMachineAgent\Log\azcmagent.log |Zawiera dane wyjściowe poleceń narzędzia azcmagent, gdy jest używany pełny argument (-v).|
    |%ProgramData%\GuestConfig\gc_agent_logs\gc_agent.log |Rejestruje szczegółowe informacje o działaniu usługi DSC.<br> w szczególności łączność między usługą HIMDS i Azure Policy.|
    |%ProgramData%\GuestConfig\gc_agent_logs\gc_agent_telemetry.txt |Rejestruje szczegółowe informacje o telemetrii usługi DSC i pełne rejestrowanie.|
    |%ProgramData%\GuestConfig\ext_mgr_logs|Rejestruje szczegółowe informacje o składniku agenta rozszerzenia.|
    |%ProgramData%\GuestConfig\extension_logs\<Extension>|Rejestruje szczegóły z zainstalowanego rozszerzenia.|

* Zostanie utworzona lokalna grupa **zabezpieczeń Aplikacje rozszerzenia agenta hybrydowego.**

* Podczas odinstalowywania agenta następujące artefakty nie są usuwane.

    * %ProgramData%\AzureConnectedMachineAgent\Log
    * %ProgramData%\AzureConnectedMachineAgent i podkatalogi
    * %ProgramData%\GuestConfig

### <a name="linux-agent-installation-details"></a>Szczegóły instalacji agenta systemu Linux

Agent Connected Machine dla systemu Linux jest dostarczany w preferowanym formacie pakietu dla dystrybucji (. RPM lub . DEB) hostowany w repozytorium [pakietów firmy](https://packages.microsoft.com/)Microsoft. Agent jest instalowany i konfigurowany za pomocą pakietu skryptów powłoki [Install_linux_azcmagent.sh.](https://aka.ms/azcmagent)

Po zainstalowaniu agenta Connected Machine dla systemu Linux są stosowane następujące zmiany konfiguracji dla całego systemu.

* Następujące foldery instalacji są tworzone podczas instalacji.

    |Folder |Opis |
    |-------|------------|
    |/var/opt/azcmagent/ |Domyślna ścieżka instalacji zawierająca pliki obsługi agenta.|
    |/opt/azcmagent/ |
    |/opt/GC_Ext | Ścieżka instalacji zawierająca pliki agenta konfiguracji gościa.|
    |/opt/DSC/ |
    |/var/opt/azcmagent/tokens |Zawiera uzyskane tokeny.|
    |/var/lib/GuestConfig |Zawiera (zastosowane) zasady z platformy Azure.|
    |/opt/GC_Ext/downloads|Rozszerzenia są pobierane z platformy Azure i kopiowane tutaj.|

* Następujące demony są tworzone na maszynie docelowej podczas instalacji agenta.

    |Nazwa usługi |Nazwa wyświetlana |Nazwa procesu |Opis |
    |-------------|-------------|-------------|------------|
    |himdsd.service |Azure Connected Machine Agent Service |himds |Ta usługa implementuje usługę Azure Instance Metadata Service (IMDS) do zarządzania połączeniem z platformą Azure i tożsamością platformy Azure połączonej maszyny.|
    |gcad.servce |GC Arc Service |gc_linux_service |Monitoruje konfigurację żądanego stanu maszyny. |
    |extd.service |Usługa rozszerzenia |gc_linux_service | Instaluje wymagane rozszerzenia przeznaczone dla maszyny.|

* Istnieje kilka plików dziennika dostępnych do rozwiązywania problemów. Zostały one opisane w poniższej tabeli.

    |Dziennik |Opis |
    |----|------------|
    |/var/opt/azcmagent/log/himds.log |Rejestruje szczegółowe informacje o usłudze agentów (HIMDS) i interakcji z platformą Azure.|
    |/var/opt/azcmagent/log/azcmagent.log |Zawiera dane wyjściowe poleceń narzędzia azcmagent, gdy jest używany pełny argument (-v).|
    |/opt/logs/dsc.log |Rejestruje szczegółowe informacje o działaniu usługi DSC.<br> w szczególności łączność między usługą Himds a Azure Policy.|
    |/opt/logs/dsc.telemetry.txt |Rejestruje szczegółowe informacje o telemetrii usługi DSC i pełne rejestrowanie.|
    |/var/lib/GuestConfig/ext_mgr_logs |Rejestruje szczegółowe informacje o składniku agenta rozszerzenia.|
    |/var/lib/GuestConfig/extension_logs|Rejestruje szczegóły z zainstalowanego rozszerzenia.|

* Podczas instalacji agenta są tworzone następujące zmienne środowiskowe. Te zmienne są ustawione w `/lib/systemd/system.conf.d/azcmagent.conf` .

    |Nazwa |Wartość domyślna |Opis |
    |-----|--------------|------------|
    |IDENTITY_ENDPOINT |http://localhost:40342/metadata/identity/oauth2/token ||
    |IMDS_ENDPOINT |http://localhost:40342 ||

* Podczas odinstalowywania agenta następujące artefakty nie są usuwane.

    * /var/opt/azcmagent
    * /opt/logs

## <a name="next-steps"></a>Następne kroki

* Aby rozpocząć ocenę Azure Arc serwerów z włączoną obsługą, postępuj zgodnie z artykułem [Connect hybrid machines to Azure (Łączenie](onboard-portal.md)maszyn hybrydowych z platformą Azure) w Azure Portal .

* Informacje dotyczące rozwiązywania problemów można znaleźć w [przewodniku Rozwiązywanie problemów z agentem połączonej maszyny.](troubleshoot-agent-onboard.md)
