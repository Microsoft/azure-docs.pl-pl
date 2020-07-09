---
title: Omówienie agenta połączonej maszyny z systemem Windows
description: Ten artykuł zawiera szczegółowe omówienie usługi Azure ARC dla serwera, który obsługuje monitorowanie maszyn wirtualnych hostowanych w środowiskach hybrydowych.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 07/01/2020
ms.topic: conceptual
ms.openlocfilehash: e3d3521cfb3d3b0c6659013922ab11fe765af882
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86111256"
---
# <a name="overview-of-azure-arc-for-servers-agent"></a>Omówienie usługi Azure ARC dla agenta serwerów

Usługa Azure ARC dla serwerów połączonych z agentem umożliwia zarządzanie maszynami z systemami Windows i Linux hostowanymi poza platformą Azure w sieci firmowej lub w innym dostawcy chmury. Ten artykuł zawiera szczegółowe omówienie wymagań dotyczących agenta, systemu i sieci oraz różnych metod wdrażania.

## <a name="agent-component-details"></a>Szczegóły składnika agenta

Pakiet agenta połączonej maszyny platformy Azure zawiera kilka składników logicznych, które są powiązane ze sobą.

* Usługa metadanych wystąpienia hybrydowego (HIMDS) zarządza połączeniem z platformą Azure i tożsamością platformy Azure połączonej maszyny.

* Agent konfiguracji gościa udostępnia funkcje zasad gościa i konfiguracji gościa, takie jak ocenianie, czy maszyna jest zgodna z wymaganymi zasadami.

    Zwróć uwagę na następujące zachowanie Azure Policy [konfiguracji gościa](../../governance/policy/concepts/guest-configuration.md) dla rozłączonej maszyny:

    * Nie ma to żadnego oddziaływania na przypisanie zasad konfiguracji gościa przeznaczone dla odłączonych maszyn.
    * Przypisanie gościa jest przechowywane lokalnie przez 14 dni. W ciągu 14 dni, jeśli Agent połączonej maszyny ponownie nawiąże połączenie z usługą, przydziały zasad są nakładane.
    * Przypisania są usuwane po 14 dniach i nie są ponownie przypisywane do komputera po upływie 14 dni.

* Agent rozszerzeń zarządza rozszerzeniami maszyn wirtualnych, w tym Instalowanie, Odinstalowywanie i uaktualnianie. Rozszerzenia są pobierane z platformy Azure i kopiowane do `%SystemDrive%\AzureConnectedMachineAgent\ExtensionService\downloads` folderu w systemie Windows oraz dla systemu Linux do programu `/opt/GC_Ext/downloads` . W systemie Windows rozszerzenie jest instalowane w następującej ścieżce `%SystemDrive%\Packages\Plugins\<extension>` , a w systemie Linux rozszerzenie jest zainstalowane do `/var/lib/waagent/<extension>` .

## <a name="download-agents"></a>Pobierz agentów

Pakiet agenta połączonej maszyny platformy Azure dla systemów Windows i Linux można pobrać z lokalizacji wymienionych poniżej.

* [Pakiet Instalator Windows agenta systemu Windows](https://aka.ms/AzureConnectedMachineAgent) z centrum pobierania Microsoft.

* Pakiet agenta systemu Linux jest dystrybuowany z [repozytorium pakietów](https://packages.microsoft.com/) firmy Microsoft przy użyciu preferowanego formatu pakietu dla dystrybucji (. RPM lub. DEB).

>[!NOTE]
>W tej wersji zapoznawczej wydano tylko jeden pakiet, który jest odpowiedni dla Ubuntu 16,04 lub 18,04.

Agenta połączonego z platformą Azure dla systemów Windows i Linux można uaktualnić do najnowszej wersji ręcznie lub automatycznie, w zależności od wymagań. Aby uzyskać więcej informacji, zobacz [tutaj](manage-agent.md).

## <a name="windows-agent-installation-details"></a>Szczegóły instalacji agenta systemu Windows

Agenta połączonej maszyny dla systemu Windows można zainstalować przy użyciu jednej z następujących trzech metod:

* Kliknij dwukrotnie plik `AzureConnectedMachineAgent.msi` .
* Ręcznie przez uruchomienie pakietu Instalator Windows `AzureConnectedMachineAgent.msi` z powłoki poleceń.
* Z sesji programu PowerShell przy użyciu metody skryptowej.

Po zainstalowaniu agenta połączonej maszyny dla systemu Windows są stosowane następujące dodatkowe zmiany konfiguracji systemu.

* Podczas instalacji są tworzone następujące foldery instalacji.

    |Folder |Opis |
    |-------|------------|
    |C:\Program Files\AzureConnectedMachineAgent |Domyślna ścieżka instalacji zawierająca pliki obsługi agentów.|
    |%ProgramData%\AzureConnectedMachineAgent |Zawiera pliki konfiguracji agenta.|
    |%ProgramData%\AzureConnectedMachineAgent\Tokens |Zawiera nabyte tokeny.|
    |%ProgramData%\AzureConnectedMachineAgent\Config |Zawiera plik konfiguracji agenta `agentconfig.json` rejestrującego informacje o rejestracji w usłudze.|
    |%SystemDrive%\Program Files\ArcConnectedMachineAgent\ExtensionService\GC | Ścieżka instalacji zawierająca pliki agenta konfiguracji gościa. |
    |%ProgramData%\GuestConfig |Zawiera zasady (stosowane) platformy Azure.|
    |%SystemDrive%\AzureConnectedMachineAgent\ExtensionService\downloads | Rozszerzenia są pobierane z platformy Azure i kopiowane w tym miejscu.|

* Podczas instalacji agenta tworzone są następujące usługi systemu Windows na maszynie docelowej.

    |Nazwa usługi |Nazwa wyświetlana |Nazwa procesu |Opis |
    |-------------|-------------|-------------|------------|
    |himds |Instance Metadata Service hybrydowe platformy Azure |himds.exe |Ta usługa implementuje usługę metadanych wystąpienia platformy Azure (IMDS) w celu zarządzania połączeniem z platformą Azure i tożsamością platformy Azure połączonej maszyny.|
    |DscService |Usługa konfiguracji gościa |dsc_service.exe |Jest to baza kodu konfiguracji żądanego stanu (DSC v2) używana wewnątrz platformy Azure do implementowania zasad w gościu.|

* Podczas instalacji agenta tworzone są następujące zmienne środowiskowe.

    |Nazwa |Wartość domyślna |Opis |
    |-----|--------------|------------|
    |IDENTITY_ENDPOINT |http://localhost:40342/metadata/identity/oauth2/token ||
    |IMDS_ENDPOINT |http://localhost:40342 ||

* Istnieje kilka plików dziennika dostępnych do rozwiązywania problemów. Są one opisane w poniższej tabeli.

    |Log |Opis |
    |----|------------|
    |%ProgramData%\AzureConnectedMachineAgent\Log\himds.log |Rejestruje szczegółowe informacje o usłudze agentów (HIMDS) i interakcji z platformą Azure.|
    |%ProgramData%\AzureConnectedMachineAgent\Log\azcmagent.log |Zawiera dane wyjściowe poleceń narzędzi azcmagent, gdy jest używany argument verbose (-v).|
    |%ProgramData%\GuestConfig\ gc_agent_logs \ gc_agent. log |Rejestruje szczegółowe informacje o aktywności usługi DSC,<br> w szczególności łączność między usługą HIMDS i Azure Policy.|
    |% ProgramData% \GuestConfig\gc_agent_logs\gc_agent_telemetry.txt |Rejestruje szczegółowe informacje o telemetrii usługi DSC i pełnym rejestrowaniu.|
    |%SystemDrive%\ProgramData\GuestConfig\ ext_mgr_logs|Rejestruje szczegółowe informacje o składniku agenta rozszerzeń.|
    |%SystemDrive%\ProgramData\GuestConfig\ extension_logs\<Extension>|Rejestruje szczegółowe informacje z zainstalowanego rozszerzenia.|

* Tworzone są **aplikacje rozszerzenia agenta hybrydowego** lokalnej grupy zabezpieczeń.

* Podczas odinstalowywania agenta nie są usuwane następujące artefakty.

    * C:\Program Files\AzureConnectedMachineAgent\Logs
    * %ProgramData%\AzureConnectedMachineAgent i podkatalogi
    * %ProgramData%\GuestConfig

## <a name="linux-agent-installation-details"></a>Szczegóły instalacji agenta systemu Linux

Agent połączonej maszyny dla systemu Linux jest dostępny w preferowanym formacie pakietu dla dystrybucji (. RPM lub. DEB), które są hostowane w [repozytorium pakietu](https://packages.microsoft.com/)Microsoft. Agent został zainstalowany i skonfigurowany z pakietem skryptu powłoki [Install_linux_azcmagent. sh](https://aka.ms/azcmagent).

Po zainstalowaniu agenta połączonej maszyny dla systemu Linux są stosowane następujące dodatkowe zmiany konfiguracji w całym systemie.

* Podczas instalacji są tworzone następujące foldery instalacji.

    |Folder |Opis |
    |-------|------------|
    |/var/opt/azcmagent/ |Domyślna ścieżka instalacji zawierająca pliki obsługi agentów.|
    |/opt/azcmagent/ |
    |/opt/GC_Ext | Ścieżka instalacji zawierająca pliki agenta konfiguracji gościa.|
    |/opt/DSC/ |
    |/var/opt/azcmagent/tokens |Zawiera nabyte tokeny.|
    |/var/lib/GuestConfig |Zawiera zasady (stosowane) platformy Azure.|
    |/opt/GC_Ext/downloads|Rozszerzenia są pobierane z platformy Azure i kopiowane w tym miejscu.|

* Na maszynie docelowej są tworzone następujące demoy podczas instalacji agenta.

    |Nazwa usługi |Nazwa wyświetlana |Nazwa procesu |Opis |
    |-------------|-------------|-------------|------------|
    |himdsd. Service |Instance Metadata Service hybrydowe platformy Azure |/opt/azcmagent/bin/himds |Ta usługa implementuje usługę metadanych wystąpienia platformy Azure (IMDS) w celu zarządzania połączeniem z platformą Azure i tożsamością platformy Azure połączonej maszyny.|
    |dscd. Service |Usługa konfiguracji gościa |/opt/DSC/dsc_linux_service |Jest to baza kodu konfiguracji żądanego stanu (DSC v2) używana wewnątrz platformy Azure do implementowania zasad w gościu.|

* Istnieje kilka plików dziennika dostępnych do rozwiązywania problemów. Są one opisane w poniższej tabeli.

    |Log |Opis |
    |----|------------|
    |/var/opt/azcmagent/log/himds.log |Rejestruje szczegółowe informacje o usłudze agentów (HIMDS) i interakcji z platformą Azure.|
    |/var/opt/azcmagent/log/azcmagent.log |Zawiera dane wyjściowe poleceń narzędzi azcmagent, gdy jest używany argument verbose (-v).|
    |/opt/logs/dsc.log |Rejestruje szczegółowe informacje o aktywności usługi DSC,<br> w szczególności łączność między usługą himds i Azure Policy.|
    |/opt/Logs/dsc.telemetry.txt |Rejestruje szczegółowe informacje o telemetrii usługi DSC i pełnym rejestrowaniu.|
    |/var/lib/GuestConfig/ext_mgr_logs |Rejestruje szczegółowe informacje o składniku agenta rozszerzeń.|
    |/var/log/GuestConfig/extension_logs|Rejestruje szczegółowe informacje z zainstalowanego rozszerzenia.|

* Podczas instalacji agenta tworzone są następujące zmienne środowiskowe. Te zmienne są ustawione w `/lib/systemd/system.conf.d/azcmagent.conf` .

    |Nazwa |Wartość domyślna |Opis |
    |-----|--------------|------------|
    |IDENTITY_ENDPOINT |http://localhost:40342/metadata/identity/oauth2/token ||
    |IMDS_ENDPOINT |http://localhost:40342 ||

* Podczas odinstalowywania agenta nie są usuwane następujące artefakty.

    * /var/opt/azcmagent
    * /opt/logs

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="supported-operating-systems"></a>Obsługiwane systemy operacyjne

Następujące wersje systemu operacyjnego Windows i Linux są oficjalnie obsługiwane dla agenta połączonego z platformą Azure: 

- Windows Server 2012 R2 lub nowszy (w tym Windows Server Core)
- Ubuntu 16,04 i 18,04 (x64)
- CentOS Linux 7 (x64)
- SUSE Linux Enterprise Server (SLES) 15 (x64)
- Red Hat Enterprise Linux (RHEL) 7 (x64)
- Amazon Linux 2 (x64)

>[!NOTE]
>Ta wersja zapoznawcza agenta połączonej maszyny dla systemu Windows obsługuje tylko system Windows Server skonfigurowany do korzystania z języka angielskiego.
>

### <a name="required-permissions"></a>Wymagane uprawnienia

- Aby dodać maszyny, musisz być członkiem roli **dołączania maszyny połączonej z platformą Azure** .

- Aby odczytywać, modyfikować, przełączać i usuwać maszynę, jesteś członkiem roli **administratora zasobów maszyny połączonej z platformą Azure** . 

### <a name="azure-subscription-and-service-limits"></a>Limity subskrypcji i usług platformy Azure

Przed skonfigurowaniem maszyn przy użyciu usługi Azure ARC dla serwerów (wersja zapoznawcza) należy przejrzeć limity [subskrypcji](../../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits) Azure Resource Manager i [limity grup zasobów](../../azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits) , aby zaplanować liczbę maszyn, które mają być połączone.

## <a name="tls-12-protocol"></a>Protokół TLS 1,2

Aby zapewnić bezpieczeństwo danych przesyłanych do platformy Azure, zdecydowanie zalecamy skonfigurowanie komputera do korzystania z Transport Layer Security (TLS) 1,2. Starsze wersje protokołu TLS/SSL (SSL) są zagrożone i chociaż nadal działają tak, aby umożliwić zgodność z poprzednimi wersjami, nie są **zalecane**.

|Platforma/język | Pomoc techniczna | Więcej informacji |
| --- | --- | --- |
|Linux | Dystrybucje systemu Linux zależą od [OpenSSL](https://www.openssl.org) obsługi TLS 1,2. | Sprawdź [Dziennik zmian OpenSSL](https://www.openssl.org/news/changelog.html) , aby potwierdzić, że wersja OpenSSL jest obsługiwana.|
| System Windows Server 2012 R2 lub nowszy | Obsługiwane i domyślnie włączone. | , Aby upewnić się, że nadal używasz [ustawień domyślnych](/windows-server/security/tls/tls-registry-settings).|

### <a name="networking-configuration"></a>Konfiguracja sieci

Agent połączonej maszyny dla systemów Linux i Windows komunikuje się z ruchem wychodzącym bezpiecznie do usługi Azure Arc przez port TCP 443. Jeśli komputer nawiązuje połączenie za pośrednictwem zapory lub serwera proxy w celu komunikowania się za pośrednictwem Internetu, przejrzyj poniższe wymagania, aby poznać wymagania dotyczące konfiguracji sieci.

Jeśli łączność wychodząca jest ograniczona przez zaporę lub serwer proxy, upewnij się, że adresy URL wymienione poniżej nie są blokowane. Jeśli dozwolone są tylko zakresy adresów IP lub nazwy domen wymagane przez agenta do komunikowania się z usługą, należy również zezwolić na dostęp do następujących tagów usługi i adresów URL.

Tagi usługi:

- Usługi azureactivedirectory
- AzureTrafficManager

Adresy

| Zasób agenta | Opis |
|---------|---------|
|management.azure.com|Azure Resource Manager|
|login.windows.net|Azure Active Directory|
|dc.services.visualstudio.com|Application Insights|
|agentserviceapi.azure-automation.net|Konfiguracja gościa|
|*-agentservice-prod-1.azure-automation.net|Konfiguracja gościa|
|*. his.arc.azure.com|Hybrydowa usługa tożsamości|

Aby uzyskać listę adresów IP dla każdego tagu usługi/regionu, zobacz plik JSON — [zakresy adresów IP platformy Azure i Tagi usług — chmura publiczna](https://www.microsoft.com/download/details.aspx?id=56519). Firma Microsoft publikuje cotygodniowe aktualizacje zawierające poszczególne usługi platformy Azure i zakresy adresów IP, z których korzystają. Aby uzyskać więcej informacji, przejrzyj [Tagi usług](../../virtual-network/security-overview.md#service-tags).

Adresy URL w powyższej tabeli są wymagane oprócz informacji o zakresie adresów IP znacznika usługi, ponieważ większość usług nie ma obecnie rejestracji tagu usługi. W związku z tym adresy IP mogą ulec zmianie. Jeśli dla konfiguracji zapory wymagane są zakresy adresów IP, należy użyć znacznika usługi **AzureCloud** , aby zezwolić na dostęp do wszystkich usług platformy Azure. Nie należy wyłączać monitorowania zabezpieczeń ani inspekcji tych adresów URL, tak jak w przypadku innego ruchu internetowego.

### <a name="register-azure-resource-providers"></a>Rejestrowanie dostawców zasobów platformy Azure

Usługa Azure ARC dla serwerów (wersja zapoznawcza) jest zależna od następujących dostawców zasobów platformy Azure w ramach subskrypcji, aby można było korzystać z tej usługi:

- **Microsoft. HybridCompute**
- **Microsoft. GuestConfiguration**

Jeśli nie są one zarejestrowane, można je zarejestrować przy użyciu następujących poleceń:

Azure PowerShell:

```azurepowershell-interactive
Login-AzAccount
Set-AzContext -SubscriptionId [subscription you want to onboard]
Register-AzResourceProvider -ProviderNamespace Microsoft.HybridCompute
Register-AzResourceProvider -ProviderNamespace Microsoft.GuestConfiguration
```

Interfejs wiersza polecenia platformy Azure:

```azurecli-interactive
az account set --subscription "{Your Subscription Name}"
az provider register --namespace 'Microsoft.HybridCompute'
az provider register --namespace 'Microsoft.GuestConfiguration'
```

Dostawców zasobów można zarejestrować w Azure Portal, wykonując czynności opisane w sekcji [Azure Portal](../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal).

## <a name="installation-and-configuration"></a>Instalacja i konfiguracja

Łączenie maszyn w środowisku hybrydowym bezpośrednio z platformą Azure można wykonać przy użyciu różnych metod, w zależności od wymagań. W poniższej tabeli przedstawiono każdą metodę, aby określić, która działa najlepiej dla Twojej organizacji.

| Metoda | Opis |
|--------|-------------|
| Interaktywnie | Ręcznie Zainstaluj agenta na jednej lub małej liczbie maszyn, wykonując kroki opisane w temacie [Connect Machines from Azure Portal](onboard-portal.md).<br> Z Azure Portal można wygenerować skrypt i wykonać go na maszynie w celu zautomatyzowania kroków instalacji i konfiguracji agenta.|
| Na dużą skalę | Zainstaluj i Skonfiguruj agenta dla wielu maszyn po [przyłączeniu maszyn przy użyciu nazwy głównej usługi](onboard-service-principal.md).<br> Ta metoda tworzy jednostkę usługi do łączenia maszyn nieinteraktywnie.|
| Na dużą skalę | Zainstaluj i Skonfiguruj agenta dla wielu maszyn po metodzie [przy użyciu programu Windows POWERSHELL DSC](onboard-dsc.md).<br> Ta metoda używa nazwy głównej usługi do łączenia maszyn nieinteraktywnie z programem PowerShell DSC. |

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć ocenę usługi Azure ARC dla serwerów (wersja zapoznawcza), postępuj zgodnie z artykułem [łączenie maszyn hybrydowych z platformą Azure z poziomu Azure Portal](onboard-portal.md).
