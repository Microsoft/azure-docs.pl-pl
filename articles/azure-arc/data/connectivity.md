---
title: Tryby i wymagania dotyczące łączności
description: Opis opcji łączności z usługą Azure ARC z obsługą usługi Data Services dla środowiska na platformie Azure
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: conceptual
ms.openlocfilehash: d148509af45b93dce8dbd99b9afc674276b149b6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99493976"
---
# <a name="connectivity-modes-and-requirements"></a>Tryby i wymagania dotyczące łączności

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="connectivity-modes"></a>Tryby łączności

Istnieje wiele opcji dotyczących stopnia łączności ze środowiska usługi danych z włączonym usługą Azure Arc na platformę Azure. Ponieważ Twoje wymagania różnią się w zależności od zasad firmy, regulaminu rządowego lub dostępności łączności sieciowej z platformą Azure, można wybrać jeden z następujących trybów łączności.

Usługi danych z obsługą usługi Azure ARC umożliwiają nawiązywanie połączenia z platformą Azure w dwóch różnych *trybach łączności*: 

- Połączone bezpośrednio 
- Połączono pośrednio

Tryb łączności zapewnia elastyczność wybierania ilości danych wysyłanych na platformę Azure oraz sposobu, w jaki użytkownicy współpracują z kontrolerem danych Arc. W zależności od wybranego trybu łączności niektóre funkcje usług danych z obsługą usługi Azure Arc mogą być niedostępne.

Ważne, jeśli usługi danych z funkcją Azure Arc są połączone bezpośrednio z platformą Azure, użytkownicy mogą używać [Azure Resource Manager interfejsów API](/rest/api/resources/), interfejsu wiersza polecenia platformy azure i Azure Portal do korzystania z usług Azure Arc Data Services. Środowisko pracy w trybie bezpośrednio połączonym jest podobne jak w przypadku korzystania z dowolnej innej usługi platformy Azure, która zapewnia obsługę administracyjną, skalowanie, Konfigurowanie i tak dalej na wszystkich Azure Portal.  Jeśli usługi danych z włączonym łukiem Azure są połączone pośrednio z platformą Azure, Azure Portal jest widokiem tylko do odczytu. Można zobaczyć spis wystąpień zarządzanych przez program SQL i wdrożone wystąpienia Postgres oraz szczegółowe informacje o nich, ale nie można podjąć na nich akcji w Azure Portal.  W trybie połączonym bezpośrednio wszystkie akcje muszą zostać wykonane lokalnie przy użyciu Azure Data Studio, [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] Kubernetes, lub natywnych narzędzi, takich jak polecenia kubectl.

Ponadto Azure Active Directory i Azure Role-Based Access Control mogą być używane w trybie bezpośrednio połączonym tylko dlatego, że istnieje zależność od ciągłego i bezpośredniego połączenia z platformą Azure w celu zapewnienia tej funkcji.

Niektóre usługi dołączone do platformy Azure są dostępne tylko wtedy, gdy można je uzyskać bezpośrednio, takich jak usługi Azure Defender Security Services, informacje o kontenerze i Azure Backup do magazynu obiektów BLOB.

||**Połączono pośrednio**|**Połączone bezpośrednio**|**Nigdy nie połączono**|
|---|---|---|---|
|**Opis**|Tryb połączony bezpośrednio oferuje większość usług zarządzania lokalnie w danym środowisku bez bezpośredniego połączenia z platformą Azure.  Minimalna ilość danych musi być wysyłana do platformy Azure _wyłącznie_ w celach spisu i rozliczania. Jest on eksportowany do pliku i przekazywany do platformy Azure co najmniej raz na miesiąc.  Nie jest wymagane bezpośrednie ani ciągłe połączenie z platformą Azure.  Niektóre funkcje i usługi, które wymagają połączenia z platformą Azure, nie będą dostępne.|Tryb bezpośrednio połączony oferuje wszystkie dostępne usługi, gdy bezpośrednie połączenie można nawiązać z platformą Azure. Połączenia są zawsze inicjowane _ze_ środowiska na platformę Azure i używają standardowych portów i protokołów, takich jak https/443.|Żadne dane nie mogą być wysyłane do ani z platformy Azure w jakikolwiek sposób.|
|**Bieżąca dostępność**| dostępna w wersji zapoznawczej.|dostępna w wersji zapoznawczej.|Obecnie nie jest obsługiwane.|
|**Typowe przypadki użycia**|Lokalne centra danych, które nie umożliwiają łączności z lub z obszaru danych centrum danych z powodu zasad zgodności firmy lub przepisów lub nie są problemy z atakami zewnętrznymi lub eksfiltracji danych.  Typowe przykłady: instytucje finansowe, opieka zdrowotna, rządowy. <br/><br/>Lokalizacje lokacji brzegowych, w których lokacja graniczna nie ma zwykle łączności z Internetem.  Typowe przykłady: aplikacje ropopochodne lub gazowe lub wojskowe.  <br/><br/>Lokalizacje lokacji programu Edge, które mają sporadyczne połączenia z długim okresem przestoju.  Typowe przykłady: Stadium, statki wycieczkowe. | Organizacje korzystające z chmur publicznych.  Typowe przykłady: Azure, AWS lub Google Cloud.<br/><br/>Lokacje graniczne, w przypadku których połączenie z Internetem jest zwykle obecne i dozwolone.  Typowe przykłady: sklepy detaliczne, produkcja.<br/><br/>Firmowe centra danych z bardziej ograniczającymi zasadami dotyczącymi łączności z i z obszaru danych w danym regionie z Internetu.  Typowe przykłady: nieregulowane firmy, małe/średnie firmy|W prawdziwie środowiskach "Air-gapped", w których żadne dane nie mogą pochodzić z danego środowiska. Typowe przykłady: najważniejsze funkcje instytucji rządowych.|
|**Jak dane są wysyłane do platformy Azure**|Istnieją trzy opcje, w jaki sposób dane dotyczące rozliczeń i spisu mogą być wysyłane na platformę Azure:<br><br> 1) dane są eksportowane z obszaru danych przez zautomatyzowany proces, który ma łączność zarówno z bezpiecznym regionem danych, jak i z platformą Azure.<br><br>2) dane są eksportowane z obszaru danych przez zautomatyzowany proces w obszarze danych, automatycznie kopiowane do mniej bezpiecznego regionu, a zautomatyzowany proces w mniej bezpiecznym regionie przekazuje dane do platformy Azure.<br><br>3) dane są ręcznie eksportowane przez użytkownika w bezpiecznym regionie, ręcznie wprowadzone do bezpiecznego regionu i ręcznie przekazane do platformy Azure. <br><br>Pierwsze dwie opcje są zautomatyzowanymi procesami ciągłymi, które mogą być uruchamiane często, aby zapewnić minimalne opóźnienie transferu danych do platformy Azure podmiotu tylko do dostępnej łączności z platformą Azure.|Dane są automatycznie i ciągle wysyłane do platformy Azure.|Dane nigdy nie są wysyłane do platformy Azure.|

## <a name="feature-availability-by-connectivity-mode"></a>Dostępność funkcji według trybu łączności

|**Funkcja**|**Połączono pośrednio**|**Połączone bezpośrednio**|
|---|---|---|
|**Automatyczna wysoka dostępność**|Obsługiwane|Obsługiwane|
|**Samoobsługowe Inicjowanie obsługi administracyjnej**|Obsługiwane<br/>Tworzenie można wykonać za pomocą Azure Data Studio, [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] lub Kubernetes natywnych narzędzi (Helm, polecenia kubectl, OC itp.) lub przy użyciu usługi Azure ARC z włączoną obsługą Kubernetes GitOps.|Obsługiwane<br/>Oprócz opcji tworzenia pośredniego trybu połączenia można również utworzyć za pomocą Azure Portal, Azure Resource Manager interfejsów API, interfejsu wiersza polecenia platformy Azure lub szablonów usługi ARM. **Oczekiwanie na dostępność trybu bezpośrednio połączonego**
|**Elastyczna skalowalność**|Obsługiwane|Obsługiwane<br/>**Oczekiwanie na dostępność trybu bezpośrednio połączonego**|
|**Rozliczenia**|Obsługiwane<br/>Dane dotyczące rozliczeń są okresowo eksportowane i wysyłane na platformę Azure.|Obsługiwane<br/>Dane dotyczące rozliczeń są automatycznie i ciągle wysyłane do platformy Azure i odzwierciedlane w czasie niemal rzeczywistym. **Oczekiwanie na dostępność trybu bezpośrednio połączonego**|
|**Zarządzanie magazynem**|Obsługiwane<br/>Dane spisu są okresowo eksportowane i wysyłane na platformę Azure.<br/><br/>Korzystaj z narzędzi klienckich, takich jak Azure Data Studio, Azure Data CLI lub `kubectl` do lokalnego wyświetlania spisu i zarządzania nim.|Obsługiwane<br/>Dane spisu są automatycznie i ciągle wysyłane do platformy Azure i odzwierciedlane w czasie niemal rzeczywistym. W związku z tym można zarządzać zapasami bezpośrednio z Azure Portal. **Oczekiwanie na dostępność trybu bezpośrednio połączonego**|
|**Automatyczne uaktualnianie i stosowanie poprawek**|Obsługiwane<br/>Kontroler danych musi mieć bezpośredni dostęp do programu Microsoft Container Registry (MCR) lub obrazy kontenerów muszą zostać pobrane z MCR i wypchnięte do lokalnego, prywatnego rejestru kontenera, do którego kontroler danych ma dostęp.|Obsługiwane<br/>**Oczekiwanie na dostępność trybu bezpośrednio połączonego**|
|**Automatyczne tworzenie kopii zapasowej i przywracanie**|Obsługiwane<br/>Automatyczna lokalna kopia zapasowa i przywracanie.|Obsługiwane<br/>Oprócz zautomatyzowanej lokalnej kopii zapasowej i przywracania można _Opcjonalnie_ wysyłać kopie zapasowe do Azure Backup w celu długoterminowego przechowywania poza lokacją. **Oczekiwanie na dostępność trybu bezpośrednio połączonego**|
|**Monitorowanie**|Obsługiwane<br/>Lokalne monitorowanie przy użyciu pulpitów nawigacyjnych Grafana i Kibana.|Obsługiwane<br/>Oprócz lokalnych pulpitów nawigacyjnych monitorowania można _Opcjonalnie_ wysyłać dane monitorowania i dzienniki, aby Azure monitor monitorowanie wielu lokacji w jednym miejscu. **Oczekiwanie na dostępność trybu bezpośrednio połączonego**|
|**Authentication**|Użyj lokalnej nazwy użytkownika/hasła na potrzeby uwierzytelniania kontrolera danych i pulpitu nawigacyjnego. Używaj nazw logowania SQL i Postgres lub Active Directory na potrzeby łączności z wystąpieniami bazy danych.  Użyj dostawców uwierzytelniania K8s do uwierzytelniania w interfejsie API Kubernetes.|Oprócz lub zamiast metod uwierzytelniania w trybie połączonym pośrednio _można użyć Azure Active Directory_ . **Oczekiwanie na dostępność trybu bezpośrednio połączonego**|
|**Kontrola dostępu oparta na rolach (RBAC)**|Użyj Kubernetes RBAC w interfejsie API Kubernetes. Użyj SQL i Postgres RBAC dla wystąpień bazy danych.|Opcjonalnie możesz zintegrować z Azure Active Directory i RBAC platformy Azure. **Oczekiwanie na dostępność trybu bezpośrednio połączonego**|
|**Azure Defender**|Nieobsługiwane|Planowane dla przyszłych|

## <a name="connectivity-requirements"></a>Wymagania dotyczące łączności

**Niektóre funkcje wymagają połączenia z platformą Azure.**

**Cała komunikacja z platformą Azure jest zawsze inicjowana ze środowiska.** Jest to prawdziwe nawet w przypadku operacji, które są inicjowane przez użytkownika w Azure Portal.  W takim przypadku efektywne jest zadanie, które jest umieszczane w kolejce na platformie Azure.  Agent w Twoim środowisku inicjuje komunikację z platformą Azure, aby zobaczyć, jakie zadania znajdują się w kolejce, uruchamiać zadania i raporty z powrotem stanu/ukończenia/niepowodzenia na platformę Azure.

|**Typ danych**|**Kierunek**|**Wymagane/Opcjonalne**|**Dodatkowe koszty**|**Wymagany tryb**|**Uwagi**|
|---|---|---|---|---|---|
|**Obrazy kontenerów**|Klient > firmy Microsoft Container Registry|Wymagane|Nie|Pośrednie lub bezpośrednie|Obrazy kontenerów to metoda dystrybuowania oprogramowania.  W środowisku, które może połączyć się z Container Registry firmy Microsoft (MCR) przez Internet, obrazy kontenerów można ściągnąć bezpośrednio z MCR.  W przypadku, gdy środowisko wdrażania nie ma bezpośredniej łączności, można ściągnąć obrazy z MCR i wypchnąć je do prywatnego rejestru kontenerów w środowisku wdrażania.  Podczas tworzenia można skonfigurować proces tworzenia do ściągania z prywatnego rejestru kontenerów zamiast MCR. Dotyczy to również automatycznych aktualizacji.|
|**Spis zasobów**|Środowisko klienta — > platformy Azure|Wymagane|Nie|Pośrednie lub bezpośrednie|Spis kontrolerów danych, wystąpienia bazy danych (PostgreSQL i SQL) są przechowywane na platformie Azure na potrzeby rozliczeń, a także do celów tworzenia spisu wszystkich kontrolerów danych i wystąpień bazy danych w jednym miejscu, co jest szczególnie przydatne, jeśli masz więcej niż jedno środowisko z usługami Azure Arc Data Services.  Gdy wystąpienia są inicjowane, anulowane aprowizacji, skalowane w poziomie/w poziomie, skalowanie w górę/w dół spis zostanie zaktualizowany na platformie Azure.|
|**Dane telemetryczne dotyczące rozliczeń**|Środowisko klienta — > platformy Azure|Wymagane|Nie|Pośrednie lub bezpośrednie|Użycie wystąpień bazy danych na platformie Azure musi być wysyłane na potrzeby rozliczeń.  W okresie zapoznawczym nie ma kosztu dla usług danych z włączonym usługą Azure Arc.|
|**Monitorowanie danych i dzienników**|Środowisko klienta — > platformy Azure|Opcjonalne|Może być zależne od ilości danych (zobacz [cennik Azure monitor](https://azure.microsoft.com/en-us/pricing/details/monitor/))|Pośrednie lub bezpośrednie|Możesz chcieć wysłać lokalnie zebrane dane monitorowania i dzienniki, aby Azure Monitor do agregowania danych w wielu środowiskach w jednym miejscu, a także do korzystania z usług Azure Monitor, takich jak alerty, przy użyciu danych w Azure Machine Learning itd.|
|**Access Control oparte na rolach platformy Azure (RBAC)**|Środowisko klienta — > platformy Azure — > środowiska klienta|Opcjonalne|Nie|Tylko bezpośrednie|Jeśli chcesz korzystać z usługi Azure RBAC, połączenie musi być nawiązywane z platformą Azure przez cały czas.  Jeśli nie chcesz korzystać z usługi Azure RBAC, możesz użyć lokalnego Kubernetes RBAC.  **Oczekiwanie na dostępność trybu bezpośrednio połączonego**|
|**Azure Active Directory (AD)**|Środowisko klienta — > platformy Azure — > środowiska klienta|Opcjonalne|Może jednak być już płacisz za usługę Azure AD|Tylko bezpośrednie|Jeśli chcesz używać usługi Azure AD do uwierzytelniania, w każdej chwili należy nawiązać połączenie z platformą Azure. Jeśli nie chcesz używać usługi Azure AD do uwierzytelniania, możesz nam Active Directory Federation Services (ADFS) przez Active Directory. **Oczekiwanie na dostępność trybu bezpośrednio połączonego**|
|**Tworzenie kopii zapasowej i przywracanie**|Środowisko klienta — > środowiska klienta|Wymagane|Nie|Bezpośredni lub pośredni|Usługa tworzenia kopii zapasowych i przywracania można skonfigurować tak, aby wskazywała lokalne klasy magazynu. |
|**Azure Backup — długoterminowe przechowywanie danych**| Środowisko klienta — > platformy Azure | Opcjonalne| Tak dla usługi Azure Storage | Tylko bezpośrednie |Możesz chcieć wysyłać kopie zapasowe, które są wykonywane lokalnie, aby Azure Backup do długoterminowego przechowywania kopii zapasowych poza lokacjami i przywracać je do środowiska lokalnego na potrzeby przywracania. **Oczekiwanie na dostępność trybu bezpośrednio połączonego**|
|**Usługi zabezpieczeń usługi Azure Defender**|Środowisko klienta — > platformy Azure — > środowiska klienta|Opcjonalne|Tak|Tylko bezpośrednie|**Oczekiwanie na dostępność trybu bezpośrednio połączonego**|
|**Inicjowanie obsługi administracyjnej i zmiany konfiguracji z Azure Portal**|Środowisko klienta — > platformy Azure — > środowiska klienta|Opcjonalne|Nie|Tylko bezpośrednie|Zmiany aprowizacji i konfiguracji można wykonać lokalnie przy użyciu Azure Data Studio lub [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] .  W trybie bezpośrednio połączonym można również inicjować i wprowadzać zmiany w konfiguracji z Azure Portal. **Oczekiwanie na dostępność trybu bezpośrednio połączonego**|


## <a name="details-on-internet-addresses-ports-encryption-and-proxy-server-support"></a>Szczegóły dotyczące adresów internetowych, portów, szyfrowania i obsługi serwera proxy

Obecnie w fazie zapoznawczej jest obsługiwany tylko tryb połączony bezpośrednio. W tym trybie istnieją tylko trzy połączenia wymagane do usług dostępnych w Internecie. Połączenia te obejmują:

- [Microsoft Container Registry (MCR)](#microsoft-container-registry-mcr)
- [Interfejsy API usługi Azure Resource Manager](#azure-resource-manager-apis)
- [Interfejsy API usługi Azure monitor](#azure-monitor-apis)

Wszystkie połączenia HTTPS z platformą Azure i Container Registry firmy Microsoft są szyfrowane przy użyciu protokołu SSL/TLS przy użyciu oficjalnie podpisanych i zweryfikowanych certyfikatów.

Poniższe sekcje zawierają szczegółowe informacje dotyczące tych połączeń. 

### <a name="microsoft-container-registry-mcr"></a>Microsoft Container Registry (MCR)

Container Registry firmy Microsoft obsługuje obrazy kontenerów usługi Data Services z obsługą platformy Azure.  Można ściągnąć te obrazy z MCR i wypchnąć je do prywatnego rejestru kontenerów i skonfigurować proces wdrażania kontrolera danych w celu ściągania obrazów kontenera z tego prywatnego rejestru kontenerów.

#### <a name="connection-source"></a>Źródło połączenia

Kubernetes kubelet na każdym z węzłów Kubernetes ściągających obrazy kontenerów.

#### <a name="connection-target"></a>Cel połączenia

`mcr.microsoft.com`

#### <a name="protocol"></a>Protokół

HTTPS

#### <a name="port"></a>Port

443

#### <a name="can-use-proxy"></a>Może używać serwera proxy

Tak

#### <a name="authentication"></a>Authentication

Brak

### <a name="azure-resource-manager-apis"></a>Interfejsy API usługi Azure Resource Manager
Azure Data Studio [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] i interfejs wiersza polecenia platformy Azure łączy się z interfejsami api Azure Resource Manager w celu wysyłania i pobierania danych do i z platformy Azure w przypadku niektórych funkcji.

#### <a name="connection-source"></a>Źródło połączenia

Komputer z systemem Azure Data Studio, [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] lub interfejs wiersza polecenia platformy Azure, który nawiązuje połączenie z platformą Azure.

#### <a name="connection-target"></a>Cel połączenia

- `login.microsoftonline.com`
- `management.azure.com`
- `san-af-eastus-prod.azurewebsites.net`
- `san-af-eastus2-prod.azurewebsites.net`
- `san-af-australiaeast-prod.azurewebsites.net`
- `san-af-centralus-prod.azurewebsites.net`
- `san-af-westus2-prod.azurewebsites.net`
- `san-af-westeurope-prod.azurewebsites.net`
- `san-af-southeastasia-prod.azurewebsites.net`
- `san-af-koreacentral-prod.azurewebsites.net`
- `san-af-northeurope-prod.azurewebsites.net`
- `san-af-westeurope-prod.azurewebsites.net`
- `san-af-uksouth-prod.azurewebsites.net`
- `san-af-francecentral-prod.azurewebsites.net`

#### <a name="protocol"></a>Protokół

HTTPS

#### <a name="port"></a>Port

443

#### <a name="can-use-proxy"></a>Może używać serwera proxy

Tak

#### <a name="authentication"></a>Authentication 

Azure Active Directory

### <a name="azure-monitor-apis"></a>Interfejsy API usługi Azure monitor

Azure Data Studio [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] i interfejs wiersza polecenia platformy Azure łączy się z interfejsami api Azure Resource Manager w celu wysyłania i pobierania danych do i z platformy Azure w przypadku niektórych funkcji.

#### <a name="connection-source"></a>Źródło połączenia

Komputer z systemem [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] lub interfejs wiersza polecenia platformy Azure, który przekazuje metryki monitorowania lub dzienniki do Azure monitor.

#### <a name="connection-target"></a>Cel połączenia

- `login.microsoftonline.com`
- `management.azure.com`
- `*.ods.opinsights.azure.com`
- `*.oms.opinsights.azure.com`
- `*.monitoring.azure.com`

#### <a name="protocol"></a>Protokół

HTTPS

#### <a name="port"></a>Port

443

#### <a name="can-use-proxy"></a>Może używać serwera proxy

Tak

#### <a name="authentication"></a>Authentication 

Azure Active Directory

> [!NOTE]
> Na razie wszystkie połączenia HTTPS/443 przeglądarki do pulpitów nawigacyjnych Grafana i Kibana oraz z [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] interfejsu API usługi Data Controller są szyfrowane przy użyciu certyfikatów z podpisem własnym.  W przyszłości będzie dostępna funkcja, która umożliwia udostępnianie własnych certyfikatów na potrzeby szyfrowania tych połączeń SSL.

Łączność z Azure Data Studio i [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] do serwera interfejsu API Kubernetes korzysta ze skonfigurowanego uwierzytelniania Kubernetes i szyfrowania.  Każdy użytkownik, który używa Azure Data Studio i [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] musi mieć uwierzytelnione połączenie z interfejsem API Kubernetes, aby wykonać wiele akcji związanych z usługami danych z włączonym łukiem Azure.

