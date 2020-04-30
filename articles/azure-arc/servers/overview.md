---
title: Azure ARC dla serwerów (wersja zapoznawcza) — Omówienie
description: Dowiedz się, jak używać usługi Azure ARC dla serwerów do zarządzania maszynami hostowanymi poza platformą Azure, tak jakby były to zasoby platformy Azure.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
keywords: Azure Automation, DSC, PowerShell, Konfiguracja żądanego stanu, zarządzanie aktualizacjami, śledzenie zmian, spis, elementy Runbook, Python, graficzne, hybrydowe
ms.date: 03/24/2020
ms.topic: overview
ms.openlocfilehash: 5fa39028f1041a063bab295adabf8145a8b46ae4
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81308787"
---
# <a name="what-is-azure-arc-for-servers-preview"></a>Co to jest usługa Azure ARC dla serwerów (wersja zapoznawcza)

Usługa Azure ARC dla serwerów (wersja zapoznawcza) umożliwia zarządzanie maszynami z systemami Windows i Linux hostowanymi poza platformą Azure w sieci firmowej lub innym dostawcy chmury, podobnie jak w przypadku zarządzania natywnymi maszynami wirtualnymi platformy Azure. Gdy maszyna hybrydowa jest połączona z platformą Azure, zostanie ona podłączona i jest traktowana jako zasób na platformie Azure. Każdy połączony komputer ma identyfikator zasobu, który jest zarządzany jako część grupy zasobów w ramach subskrypcji, i korzyści ze standardowych konstrukcji platformy Azure, takich jak Azure Policy i stosowanie tagów.

Aby zapewnić to środowisko dla maszyn hybrydowych hostowanych poza platformą Azure, należy zainstalować agenta maszyny połączonej z platformą Azure na każdym komputerze, na którym ma zostać nawiązane połączenie z platformą Azure. Ten agent nie dostarcza żadnych innych funkcji i nie zastępuje [agenta log Analytics](../../azure-monitor/platform/log-analytics-agent.md)platformy Azure. Agent Log Analytics dla systemów Windows i Linux jest wymagany, gdy użytkownik chce aktywnie monitorować system operacyjny i obciążenia uruchomione na komputerze, zarządzać nim za pomocą elementów Runbook usługi Automation lub rozwiązań, takich jak Update Management, lub używać innych usług platformy Azure, takich jak [Azure Security Center](../../security-center/security-center-intro.md).

>[!NOTE]
>Ta wersja zapoznawcza jest przeznaczona do celów ewaluacyjnych i zalecamy, aby nie zarządzać krytycznymi maszynami produkcyjnymi.
>

## <a name="supported-scenarios"></a>Obsługiwane scenariusze

Usługa Azure ARC dla serwerów (wersja zapoznawcza) obsługuje następujące scenariusze z połączonymi maszynami:

- Przypisanie [Azure Policy konfiguracji gościa](../../governance/policy/concepts/guest-configuration.md) przy użyciu tego samego środowiska co przypisanie zasad dla maszyn wirtualnych platformy Azure.
- Dane dziennika zbierane przez agenta Log Analytics i przechowywane w obszarze roboczym Log Analytics maszyna jest zarejestrowana w usłudze zawiera właściwości specyficzne dla maszyny, takie jak identyfikator zasobu, którego można użyć do obsługi dostępu do dziennika [kontekstu zasobów](../../azure-monitor/platform/design-logs-deployment.md#access-mode) .

## <a name="supported-regions"></a>Obsługiwane regiony

Usługa Azure ARC dla serwerów (wersja zapoznawcza) obsługuje tylko niektóre regiony:

- WestUS2
- WestEurope
- WestAsia

W większości przypadków lokalizacja wybrana podczas tworzenia skryptu instalacji powinna być regionem platformy Azure geograficznie najbliżej lokalizacji maszyny. Dane przechowywane w obszarze geograficznym platformy Azure zawierają określony region, co może również mieć wpływ na wybór regionu, jeśli istnieją wymagania dotyczące danych znajdujących się na miejscu. Jeśli do regionu platformy Azure, z którym jest połączona dana maszyna, ma wpływ awaria, nie ma to wpływ na przyłączoną maszynę, ale nie można ukończyć operacji zarządzania przy użyciu platformy Azure. W celu uzyskania odporności w przypadku awarii regionalnej, jeśli masz wiele lokalizacji, które zapewniają geograficznie nadmiarową usługę, najlepszym rozwiązaniem jest połączenie maszyn w każdej lokalizacji z innym regionem świadczenia usługi Azure.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="supported-operating-systems"></a>Obsługiwane systemy operacyjne

Następujące wersje systemu operacyjnego Windows i Linux są oficjalnie obsługiwane dla agenta połączonego z platformą Azure: 

- Windows Server 2012 R2 lub nowszy (w tym Windows Server Core)
- Ubuntu 16,04 i 18,04
- CentOS Linux 7
- SUSE Linux Enterprise Server (SLES) 15
- Red Hat Enterprise Linux (RHEL) 7
- Amazon Linux 2

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
| System Windows Server 2012 R2 lub nowszy | Obsługiwane i domyślnie włączone. | , Aby upewnić się, że nadal używasz [ustawień domyślnych](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings).|

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
|*. his.hybridcompute.azure-automation.net|Hybrydowa usługa tożsamości|

Aby uzyskać listę adresów IP dla każdego tagu usługi/regionu, zobacz plik JSON — [zakresy adresów IP platformy Azure i Tagi usług — chmura publiczna](https://www.microsoft.com/download/details.aspx?id=56519). Firma Microsoft publikuje cotygodniowe aktualizacje zawierające poszczególne usługi platformy Azure i zakresy adresów IP, z których korzystają. Aby uzyskać więcej informacji, przejrzyj [Tagi usług](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags).

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

## <a name="connected-machine-agent"></a>Agent połączonej maszyny

Pakiet agenta połączonej maszyny platformy Azure dla systemów Windows i Linux można pobrać z lokalizacji wymienionych poniżej.

- [Pakiet Instalator Windows agenta systemu Windows](https://aka.ms/AzureConnectedMachineAgent) z centrum pobierania Microsoft.
- Pakiet agenta systemu Linux jest dystrybuowany z [repozytorium pakietów](https://packages.microsoft.com/) firmy Microsoft przy użyciu preferowanego formatu pakietu dla dystrybucji (. RPM lub. DEB).

>[!NOTE]
>W tej wersji zapoznawczej wydano tylko jeden pakiet, który jest odpowiedni dla Ubuntu 16,04 lub 18,04.

Agenta połączonego z platformą Azure dla systemów Windows i Linux można uaktualnić do najnowszej wersji ręcznie lub automatycznie, w zależności od wymagań. Aby uzyskać więcej informacji, zobacz [tutaj](manage-agent.md).

### <a name="agent-status"></a>Stan agenta

Agent połączonej maszyny wysyła do usługi zwykły komunikat pulsu co 5 minut. Jeśli usługa przestanie odbierać te komunikaty pulsu z komputera, komputer jest traktowany jako w trybie offline, a stan zostanie automatycznie zmieniony na **rozłączony** w portalu w ciągu od 15 do 30 minut. Po odebraniu kolejnego komunikatu pulsu z agenta połączonej maszyny jego stan zostanie automatycznie zmieniony na **połączone**.

## <a name="install-and-configure-agent"></a>Instalowanie i Konfigurowanie agenta

Łączenie maszyn w środowisku hybrydowym bezpośrednio z platformą Azure można wykonać przy użyciu różnych metod, w zależności od wymagań. W poniższej tabeli przedstawiono każdą metodę, aby określić, która działa najlepiej dla Twojej organizacji.

| Metoda | Opis |
|--------|-------------|
| Interaktywnie | Ręcznie Zainstaluj agenta na jednej lub małej liczbie maszyn, wykonując kroki opisane w temacie [Connect Machines from Azure Portal](onboard-portal.md).<br> Z Azure Portal można wygenerować skrypt i wykonać go na maszynie w celu zautomatyzowania kroków instalacji i konfiguracji agenta.|
| Na dużą skalę | Zainstaluj i Skonfiguruj agenta dla wielu maszyn po [przyłączeniu maszyn przy użyciu nazwy głównej usługi](onboard-service-principal.md).<br> Ta metoda tworzy jednostkę usługi do łączenia maszyn nieinteraktywnie.|
| Na dużą skalę | Zainstaluj i Skonfiguruj agenta dla wielu maszyn po metodzie [przy użyciu programu Windows POWERSHELL DSC](onboard-dsc.md).<br> Ta metoda używa nazwy głównej usługi do łączenia maszyn nieinteraktywnie z programem PowerShell DSC. |

## <a name="next-steps"></a>Następne kroki

- Aby rozpocząć ocenę usługi Azure ARC dla serwerów (wersja zapoznawcza), postępuj zgodnie z artykułem [łączenie maszyn hybrydowych z platformą Azure z poziomu Azure Portal](onboard-portal.md). 
