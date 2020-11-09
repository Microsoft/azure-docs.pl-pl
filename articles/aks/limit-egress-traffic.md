---
title: Ogranicz ruch wychodzący w usłudze Azure Kubernetes Service (AKS)
description: Dowiedz się, jakie porty i adresy są wymagane do sterowania ruchem wychodzącym w usłudze Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.author: jpalma
ms.date: 11/09/2020
author: palma21
ms.openlocfilehash: e3b755ca3ca5338acfc1918bd2085d9fba18b8ac
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/09/2020
ms.locfileid: "94380215"
---
# <a name="control-egress-traffic-for-cluster-nodes-in-azure-kubernetes-service-aks"></a>Sterowanie ruchem wychodzącym węzłów klastra w usłudze Azure Kubernetes Service (AKS)

Ten artykuł zawiera informacje niezbędne do zabezpieczenia ruchu wychodzącego z usługi Azure Kubernetes Service (AKS). Zawiera wymagania dotyczące klastra dla podstawowego wdrożenia AKS oraz dodatkowe wymagania dotyczące opcjonalnych dodatków i funkcji. Na [końcu tego przykładu zostanie dostarczona procedura konfigurowania tych wymagań za pomocą zapory platformy Azure](#restrict-egress-traffic-using-azure-firewall). Można jednak zastosować te informacje do dowolnej metody lub urządzenia ograniczenia ruchu wychodzącego.

## <a name="background"></a>Tło

Klastry AKS są wdrażane w sieci wirtualnej. Ta sieć może być zarządzana (utworzona przez AKS) lub niestandardową (wstępnie skonfigurowaną przez użytkownika wcześniej). W obu przypadkach klaster ma zależności **wychodzące** od usług spoza tej sieci wirtualnej (usługa nie ma zależności przychodzących).

W celach zarządzania i operacyjnych węzły w klastrze AKS muszą uzyskiwać dostęp do określonych portów i w pełni kwalifikowanych nazw domen (FQDN). Te punkty końcowe są wymagane dla węzłów, aby komunikować się z serwerem interfejsu API, lub pobrać i zainstalować podstawowe składniki klastra Kubernetes oraz aktualizacje zabezpieczeń węzła. Na przykład klaster musi ściągnąć podstawowe obrazy kontenerów systemu z firmy Microsoft Container Registry (MCR).

Zależności wychodzące AKS są niemal całkowicie zdefiniowane przy użyciu nazw FQDN, które nie zawierają adresów statycznych. Brak adresów statycznych oznacza, że sieciowe grupy zabezpieczeń nie mogą być używane do blokowania ruchu wychodzącego z klastra AKS. 

Domyślnie klastry AKS mają nieograniczony dostęp do Internetu (ruch wychodzący). Ten poziom dostępu do sieci umożliwia uruchamianie węzłów i usług w celu uzyskania dostępu do zasobów zewnętrznych w razie potrzeby. Aby ograniczyć ruch wychodzący, należy uzyskać dostęp do nieograniczonej liczby portów i adresów, aby zachować prawidłowe zadania konserwacji klastra. Najprostszym rozwiązaniem do zabezpieczania adresów wychodzących jest użycie urządzenia zapory, które może kontrolować ruch wychodzący na podstawie nazw domen. Zapora platformy Azure może na przykład ograniczyć wychodzący ruch HTTP i HTTPS na podstawie nazwy FQDN lokalizacji docelowej. Możesz również skonfigurować preferowane reguły zapory i zabezpieczeń, aby umożliwić korzystanie z wymaganych portów i adresów.

> [!IMPORTANT]
> W tym dokumencie opisano tylko sposób blokowania ruchu wychodzącego z podsieci AKS. AKS domyślnie nie ma wymagań związanych z transferem danych przychodzących.  Blokowanie **ruchu podsieci wewnętrznej** przy użyciu sieciowych grup zabezpieczeń (sieciowych grup zabezpieczeń) i zapór nie jest obsługiwane. Aby kontrolować i blokować ruch w klastrze, użyj [ * *_zasad sieciowych_* _][network-policy].

## <a name="required-outbound-network-rules-and-fqdns-for-aks-clusters"></a>Wymagane reguły sieci wychodzącej i nazwy FQDN dla klastrów AKS

Następujące reguły dotyczące sieci i nazwy FQDN/aplikacji są wymagane dla klastra AKS, można ich użyć, jeśli chcesz skonfigurować rozwiązanie inne niż Zapora platformy Azure.

Wartości graniczne adresów IP dotyczą ruchu innego niż HTTP/S (ruch TCP i UDP)
* Punkty końcowe HTTP/HTTPS w nazwie FQDN można umieścić na urządzeniu zapory.
* Symbole wieloznaczne protokołu HTTP/HTTPS są zależnościami, które mogą się różnić w stosunku do klastra AKS w oparciu o wiele kwalifikatorów.
* AKS korzysta z kontrolera przyjmowania, aby wstrzyknąć nazwę FQDN jako zmienną środowiskową do wszystkich wdrożeń w obszarze polecenia-system i strażnik-system, który zapewnia, że cała komunikacja systemu między węzłami i serwerem interfejsu API używa nazwy FQDN serwera interfejsu API, a nie adresu IP serwera interfejsu API. 
* Jeśli masz aplikację lub rozwiązanie, które musi komunikować się z serwerem interfejsu API, musisz dodać **dodatkową** regułę sieci, aby umożliwić *komunikację TCP z portem 443 serwera interfejsu API*.
* W rzadkich przypadkach, jeśli istnieje operacja konserwacji, adres IP serwera interfejsu API może ulec zmianie. Planowane operacje konserwacji, które mogą zmienić adres IP serwera interfejsu API, są zawsze przekazywane z wyprzedzeniem.


### <a name="azure-global-required-network-rules"></a>Globalne wymagane reguły sieci platformy Azure

Wymagane reguły sieciowe i zależności adresów IP to:

| Docelowy punkt końcowy                                                             | Protokół | Port    | Zastosowanie  |
|----------------------------------------------------------------------------------|----------|---------|------|
| **`*:1194`** <br/> *Oraz* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:1194`** <br/> *Oraz* <br/> [Regionalne CIDR](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:1194`** <br/> *Oraz* <br/> **`APIServerPublicIP:1194`** `(only known after cluster creation)`  | UDP           | 1194      | W przypadku tunelowanej bezpiecznej komunikacji między węzłami i płaszczyzną kontroli. Nie jest to wymagane w przypadku [klastrów prywatnych](private-clusters.md)|
| **`*:9000`** <br/> *Oraz* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:9000`** <br/> *Oraz* <br/> [Regionalne CIDR](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:9000`** <br/> *Oraz* <br/> **`APIServerPublicIP:9000`** `(only known after cluster creation)`  | TCP           | 9000      | W przypadku tunelowanej bezpiecznej komunikacji między węzłami i płaszczyzną kontroli. Nie jest to wymagane w przypadku [klastrów prywatnych](private-clusters.md) |
| **`*:123`** lub **`ntp.ubuntu.com:123`** (Jeśli korzystasz z reguł sieci zapory platformy Azure)  | UDP      | 123     | Wymagane dla synchronizacji czasu protokołu NTP (Network Time Protocol) w węzłach systemu Linux.                 |
| **`CustomDNSIP:53`** `(if using custom DNS servers)`                             | UDP      | 53      | Jeśli używasz niestandardowych serwerów DNS, upewnij się, że są one dostępne dla węzłów klastra. |
| **`APIServerPublicIP:443`** `(if running pods/deployments that access the API Server)` | TCP      | 443     | Wymagane, jeśli są uruchomione zasobniki/wdrożenia, które uzyskują dostęp do serwera interfejsu API, te zasobniki/wdrożenia będą używały adresu IP interfejsu API. Nie jest to wymagane w przypadku [klastrów prywatnych](private-clusters.md)  |

### <a name="azure-global-required-fqdn--application-rules"></a>Globalna wymagana nazwa FQDN/reguły aplikacji platformy Azure 

Wymagane są następujące reguły dotyczące nazwy FQDN/aplikacji:

| Docelowa nazwa FQDN                 | Port            | Zastosowanie      |
|----------------------------------|-----------------|----------|
| **`*.hcp.<location>.azmk8s.io`** | **`HTTPS:443`** | Wymagany do komunikacji z serwerem interfejsu API > <. Zamień na *\<location\>* region, w którym wdrożono klaster AKS. |
| **`mcr.microsoft.com`**          | **`HTTPS:443`** | Wymagane w celu uzyskania dostępu do obrazów w programie Microsoft Container Registry (MCR). Ten Rejestr zawiera obrazy i wykresy pierwszej strony (na przykład coreDNS itp.). Te obrazy są wymagane do prawidłowego utworzenia i działania klastra, w tym operacji skalowania i uaktualniania.  |
| **`*.data.mcr.microsoft.com`**   | **`HTTPS:443`** | Wymagane dla magazynu MCR obsługiwanego przez usługę Azure Content Delivery Network (CDN). |
| **`management.azure.com`**       | **`HTTPS:443`** | Wymagane dla operacji Kubernetes w odniesieniu do interfejsu API platformy Azure. |
| **`login.microsoftonline.com`**  | **`HTTPS:443`** | Wymagane na potrzeby uwierzytelniania Azure Active Directory. |
| **`packages.microsoft.com`**     | **`HTTPS:443`** | Ten adres jest repozytorium pakietów firmy Microsoft używanym do buforowanych operacji *apt-get* .  Przykładowe pakiety to Moby, PowerShell i interfejs wiersza polecenia platformy Azure. |
| **`acs-mirror.azureedge.net`**   | **`HTTPS:443`** | Ten adres jest przeznaczony dla repozytorium wymaganego do pobrania i zainstalowania wymaganych plików binarnych, takich jak korzystającą wtyczki kubenet i Azure CNI. |

### <a name="azure-china-21vianet-required-network-rules"></a>Azure Chiny — wymagane reguły sieci

Wymagane reguły sieciowe i zależności adresów IP to:

| Docelowy punkt końcowy                                                             | Protokół | Port    | Zastosowanie  |
|----------------------------------------------------------------------------------|----------|---------|------|
| **`*:1194`** <br/> *Oraz* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.Region:1194`** <br/> *Oraz* <br/> [Regionalne CIDR](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:1194`** <br/> *Oraz* <br/> **`APIServerPublicIP:1194`** `(only known after cluster creation)`  | UDP           | 1194      | W przypadku tunelowanej bezpiecznej komunikacji między węzłami i płaszczyzną kontroli. |
| **`*:9000`** <br/> *Oraz* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:9000`** <br/> *Oraz* <br/> [Regionalne CIDR](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:9000`** <br/> *Oraz* <br/> **`APIServerPublicIP:9000`** `(only known after cluster creation)`  | TCP           | 9000      | W przypadku tunelowanej bezpiecznej komunikacji między węzłami i płaszczyzną kontroli. |
| **`*:22`** <br/> *Oraz* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:22`** <br/> *Oraz* <br/> [Regionalne CIDR](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:22`** <br/> *Oraz* <br/> **`APIServerPublicIP:22`** `(only known after cluster creation)`  | TCP           | 22      | W przypadku tunelowanej bezpiecznej komunikacji między węzłami i płaszczyzną kontroli. |
| **`*:123`** lub **`ntp.ubuntu.com:123`** (Jeśli korzystasz z reguł sieci zapory platformy Azure)  | UDP      | 123     | Wymagane dla synchronizacji czasu protokołu NTP (Network Time Protocol) w węzłach systemu Linux.                 |
| **`CustomDNSIP:53`** `(if using custom DNS servers)`                             | UDP      | 53      | Jeśli używasz niestandardowych serwerów DNS, upewnij się, że są one dostępne dla węzłów klastra. |
| **`APIServerPublicIP:443`** `(if running pods/deployments that access the API Server)` | TCP      | 443     | Wymagane, jeśli są uruchomione zasobniki/wdrożenia, które uzyskują dostęp do serwera interfejsu API, te/wdrożenia będą używały adresu IP interfejsu API.  |

### <a name="azure-china-21vianet-required-fqdn--application-rules"></a>Azure Chiny — wymagana nazwa FQDN/reguły aplikacji

Wymagane są następujące reguły dotyczące nazwy FQDN/aplikacji:

| Docelowa nazwa FQDN                               | Port            | Zastosowanie      |
|------------------------------------------------|-----------------|----------|
| **`*.hcp.<location>.cx.prod.service.azk8s.cn`**| **`HTTPS:443`** | Wymagany do komunikacji z serwerem interfejsu API > <. Zamień na *\<location\>* region, w którym wdrożono klaster AKS. |
| **`*.tun.<location>.cx.prod.service.azk8s.cn`**| **`HTTPS:443`** | Wymagany do komunikacji z serwerem interfejsu API > <. Zamień na *\<location\>* region, w którym wdrożono klaster AKS. |
| **`mcr.microsoft.com`**                        | **`HTTPS:443`** | Wymagane w celu uzyskania dostępu do obrazów w programie Microsoft Container Registry (MCR). Ten Rejestr zawiera obrazy i wykresy pierwszej strony (na przykład coreDNS itp.). Te obrazy są wymagane do prawidłowego utworzenia i działania klastra, w tym operacji skalowania i uaktualniania. |
| **`.data.mcr.microsoft.com`**                  | **`HTTPS:443`** | Wymagane dla magazynu MCR obsługiwanego przez usługę Azure Content Delivery Network (CDN). |
| **`management.chinacloudapi.cn`**              | **`HTTPS:443`** | Wymagane dla operacji Kubernetes w odniesieniu do interfejsu API platformy Azure. |
| **`login.chinacloudapi.cn`**                   | **`HTTPS:443`** | Wymagane na potrzeby uwierzytelniania Azure Active Directory. |
| **`packages.microsoft.com`**                   | **`HTTPS:443`** | Ten adres jest repozytorium pakietów firmy Microsoft używanym do buforowanych operacji *apt-get* .  Przykładowe pakiety to Moby, PowerShell i interfejs wiersza polecenia platformy Azure. |
| **`*.azk8s.cn`**                               | **`HTTPS:443`** | Ten adres jest przeznaczony dla repozytorium wymaganego do pobrania i zainstalowania wymaganych plików binarnych, takich jak korzystającą wtyczki kubenet i Azure CNI. |

### <a name="azure-us-government-required-network-rules"></a>Reguły sieci wymagające platformy Azure dla instytucji rządowych

Wymagane reguły sieciowe i zależności adresów IP to:

| Docelowy punkt końcowy                                                             | Protokół | Port    | Zastosowanie  |
|----------------------------------------------------------------------------------|----------|---------|------|
| **`*:1194`** <br/> *Oraz* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:1194`** <br/> *Oraz* <br/> [Regionalne CIDR](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:1194`** <br/> *Oraz* <br/> **`APIServerPublicIP:1194`** `(only known after cluster creation)`  | UDP           | 1194      | W przypadku tunelowanej bezpiecznej komunikacji między węzłami i płaszczyzną kontroli. |
| **`*:9000`** <br/> *Oraz* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:9000`** <br/> *Oraz* <br/> [Regionalne CIDR](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:9000`** <br/> *Oraz* <br/> **`APIServerPublicIP:9000`** `(only known after cluster creation)`  | TCP           | 9000      | W przypadku tunelowanej bezpiecznej komunikacji między węzłami i płaszczyzną kontroli. |
| **`*:123`** lub **`ntp.ubuntu.com:123`** (Jeśli korzystasz z reguł sieci zapory platformy Azure)  | UDP      | 123     | Wymagane dla synchronizacji czasu protokołu NTP (Network Time Protocol) w węzłach systemu Linux.                 |
| **`CustomDNSIP:53`** `(if using custom DNS servers)`                             | UDP      | 53      | Jeśli używasz niestandardowych serwerów DNS, upewnij się, że są one dostępne dla węzłów klastra. |
| **`APIServerPublicIP:443`** `(if running pods/deployments that access the API Server)` | TCP      | 443     | Wymagane, jeśli są uruchomione zasobniki/wdrożenia, które uzyskują dostęp do serwera interfejsu API, te zasobniki/wdrożenia będą używały adresu IP interfejsu API.  |

### <a name="azure-us-government-required-fqdn--application-rules"></a>Wymagana nazwa FQDN/reguły aplikacji dla instytucji rządowych USA platformy Azure 

Wymagane są następujące reguły dotyczące nazwy FQDN/aplikacji:

| Docelowa nazwa FQDN                                        | Port            | Zastosowanie      |
|---------------------------------------------------------|-----------------|----------|
| **`*.hcp.<location>.cx.aks.containerservice.azure.us`** | **`HTTPS:443`** | Wymagany do komunikacji z serwerem interfejsu API > <. Zamień na *\<location\>* region, w którym wdrożono klaster AKS.|
| **`mcr.microsoft.com`**                                 | **`HTTPS:443`** | Wymagane w celu uzyskania dostępu do obrazów w programie Microsoft Container Registry (MCR). Ten Rejestr zawiera obrazy i wykresy pierwszej strony (na przykład coreDNS itp.). Te obrazy są wymagane do prawidłowego utworzenia i działania klastra, w tym operacji skalowania i uaktualniania. |
| **`*.data.mcr.microsoft.com`**                          | **`HTTPS:443`** | Wymagane dla magazynu MCR obsługiwanego przez usługę Azure Content Delivery Network (CDN). |
| **`management.usgovcloudapi.net`**                      | **`HTTPS:443`** | Wymagane dla operacji Kubernetes w odniesieniu do interfejsu API platformy Azure. |
| **`login.microsoftonline.us`**                          | **`HTTPS:443`** | Wymagane na potrzeby uwierzytelniania Azure Active Directory. |
| **`packages.microsoft.com`**                            | **`HTTPS:443`** | Ten adres jest repozytorium pakietów firmy Microsoft używanym do buforowanych operacji *apt-get* .  Przykładowe pakiety to Moby, PowerShell i interfejs wiersza polecenia platformy Azure. |
| **`acs-mirror.azureedge.net`**                          | **`HTTPS:443`** | Ten adres dotyczy repozytorium wymaganego do instalacji wymaganych plików binarnych, takich jak korzystającą wtyczki kubenet i Azure CNI. |

## <a name="optional-recommended-fqdn--application-rules-for-aks-clusters"></a>Opcjonalna zalecana nazwa FQDN/reguły aplikacji dla klastrów AKS

Następujące reguły nazwy FQDN/aplikacji są opcjonalne, ale zalecane dla klastrów AKS:

| Docelowa nazwa FQDN                                                               | Port          | Zastosowanie      |
|--------------------------------------------------------------------------------|---------------|----------|
| **`security.ubuntu.com`, `azure.archive.ubuntu.com`, `changelogs.ubuntu.com`** | **`HTTP:80`** | Ten adres umożliwia pobranie przez węzły klastra systemu Linux wymaganych poprawek i aktualizacji zabezpieczeń. |

W przypadku wybrania opcji blokowania/niezezwalania na te nazwy FQDN węzły będą otrzymywać aktualizacje systemu operacyjnego tylko w przypadku [uaktualniania obrazu węzła](node-image-upgrade.md) lub [uaktualnienia klastra](upgrade-cluster.md).

## <a name="gpu-enabled-aks-clusters"></a>Klastry AKS obsługujące procesor GPU

### <a name="required-fqdn--application-rules"></a>Wymagana nazwa FQDN/reguły aplikacji

Następujące reguły dotyczące nazwy FQDN/aplikacji są wymagane dla klastrów AKS z włączonym procesorem GPU:

| Docelowa nazwa FQDN                        | Port      | Zastosowanie      |
|-----------------------------------------|-----------|----------|
| **`nvidia.github.io`**                  | **`HTTPS:443`** | Ten adres jest używany do poprawnego instalowania i działania sterowników w węzłach opartych na procesorze GPU. |
| **`us.download.nvidia.com`**            | **`HTTPS:443`** | Ten adres jest używany do poprawnego instalowania i działania sterowników w węzłach opartych na procesorze GPU. |
| **`apt.dockerproject.org`**             | **`HTTPS:443`** | Ten adres jest używany do poprawnego instalowania i działania sterowników w węzłach opartych na procesorze GPU. |

## <a name="windows-server-based-node-pools"></a>Pule węzłów oparte na systemie Windows Server 

### <a name="required-fqdn--application-rules"></a>Wymagana nazwa FQDN/reguły aplikacji

Następujące reguły dotyczące nazwy FQDN/aplikacji są wymagane do korzystania z pul węzłów opartych na systemie Windows Server:

| Docelowa nazwa FQDN                                                           | Port      | Zastosowanie      |
|----------------------------------------------------------------------------|-----------|----------|
| **`onegetcdn.azureedge.net, go.microsoft.com`**                            | **`HTTPS:443`** | Aby zainstalować pliki binarne powiązane z systemem Windows |
| **`*.mp.microsoft.com, www.msftconnecttest.com, ctldl.windowsupdate.com`** | **`HTTP:80`**   | Aby zainstalować pliki binarne powiązane z systemem Windows |

## <a name="aks-addons-and-integrations"></a>AKS Dodatki i integracje

### <a name="azure-monitor-for-containers"></a>Usługa Azure Monitor dla kontenerów

Dostępne są dwie opcje zapewnienia dostępu do Azure Monitor dla kontenerów, ale można zezwolić na Azure Monitor [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) **lub** zapewnić dostęp do wymaganych reguł nazwy FQDN/aplikacji.

#### <a name="required-network-rules"></a>Wymagane reguły sieci

Wymagane są następujące reguły dotyczące nazwy FQDN/aplikacji:

| Docelowy punkt końcowy                                                             | Protokół | Port    | Zastosowanie  |
|----------------------------------------------------------------------------------|----------|---------|------|
| [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureMonitor:443`**  | TCP           | 443      | Ten punkt końcowy służy do wysyłania danych metryk i dzienników do Azure Monitor i Log Analytics. |

#### <a name="required-fqdn--application-rules"></a>Wymagana nazwa FQDN/reguły aplikacji

Następujące reguły dotyczące nazwy FQDN/aplikacji są wymagane dla klastrów AKS, dla Azure Monitor których włączono obsługę kontenerów:

| Nazwa FQDN                                    | Port      | Zastosowanie      |
|-----------------------------------------|-----------|----------|
| dc.services.visualstudio.com | **`HTTPS:443`**    | Ten punkt końcowy jest używany na potrzeby metryk i monitorowania danych telemetrycznych przy użyciu Azure Monitor. |
| *.ods.opinsights.azure.com    | **`HTTPS:443`**    | Ten punkt końcowy jest używany przez Azure Monitor do pozyskiwania danych usługi log Analytics. |
| *.oms.opinsights.azure.com | **`HTTPS:443`** | Ten punkt końcowy jest używany przez omsagent, który jest używany do uwierzytelniania usługi log Analytics. |
| *. monitoring.azure.com | **`HTTPS:443`** | Ten punkt końcowy jest używany do wysyłania danych metryk do Azure Monitor. |

### <a name="azure-dev-spaces"></a>Azure Dev Spaces

Zaktualizuj zaporę lub konfigurację zabezpieczeń, aby zezwalać na ruch sieciowy do i z wszystkich poniższych nazw FQDN i [usług Azure dev Spaces infrastruktury][dev-spaces-service-tags].

#### <a name="required-network-rules"></a>Wymagane reguły sieci

| Docelowy punkt końcowy                                                             | Protokół | Port    | Zastosowanie  |
|----------------------------------------------------------------------------------|----------|---------|------|
| [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureDevSpaces`**  | TCP           | 443      | Ten punkt końcowy służy do wysyłania danych metryk i dzienników do Azure Monitor i Log Analytics. |

#### <a name="required-fqdn--application-rules"></a>Wymagana nazwa FQDN/reguły aplikacji 

Następujące reguły dotyczące nazwy FQDN/aplikacji są wymagane dla klastrów AKS z włączonym Azure Dev Spaces:

| Nazwa FQDN                                    | Port      | Zastosowanie      |
|-----------------------------------------|-----------|----------|
| `cloudflare.docker.com` | **`HTTPS:443`** | Ten adres służy do ściągania obrazów z systemem Linux Alpine i innych Azure Dev Spaces |
| `gcr.io` | **`HTTPS:443`** | Ten adres służy do ściągania obrazów Helm/do odczekania |
| `storage.googleapis.com` | **`HTTPS:443`** | Ten adres służy do ściągania obrazów Helm/do odczekania |


### <a name="azure-policy"></a>Azure Policy

#### <a name="required-fqdn--application-rules"></a>Wymagana nazwa FQDN/reguły aplikacji 

Następujące reguły dotyczące nazwy FQDN/aplikacji są wymagane dla klastrów AKS z włączonym Azure Policy.

| Nazwa FQDN                                          | Port      | Zastosowanie      |
|-----------------------------------------------|-----------|----------|
| **`gov-prod-policy-data.trafficmanager.net`** | **`HTTPS:443`** | Ten adres jest używany do poprawnego działania Azure Policy. (obecnie w wersji zapoznawczej w AKS) |
| **`raw.githubusercontent.com`**               | **`HTTPS:443`** | Ten adres służy do ściągania wbudowanych zasad z usługi GitHub w celu zapewnienia prawidłowego działania Azure Policy. (obecnie w wersji zapoznawczej w AKS) |
| **`dc.services.visualstudio.com`**            | **`HTTPS:443`** | Azure Policy dodatek, który wysyła dane telemetryczne do punktu końcowego usługi Application Insights. |

## <a name="restrict-egress-traffic-using-azure-firewall"></a>Ogranicz ruch wychodzący przy użyciu zapory platformy Azure

Zapora platformy Azure udostępnia tag FQDN usługi Azure Kubernetes Service ( `AzureKubernetesService` ), aby uprościć tę konfigurację. 

> [!NOTE]
> Tag FQDN zawiera wszystkie nazwy FQDN wymienione powyżej i są automatycznie aktualizowane.
>
> Zalecamy używanie co najmniej 20 adresów IP frontonu w zaporze platformy Azure dla scenariuszy produkcyjnych, aby uniknąć ponoszenia problemów z wyczerpaniem portów w ramach adresów IP.

Poniżej znajduje się przykładowa architektura wdrożenia:

![Topologia blokady](media/limit-egress-traffic/aks-azure-firewall-egress.png)

* Publiczny ruch przychodzący jest zmuszony do przepływu przez filtry zapory
  * Węzły agenta AKS są izolowane w dedykowanej podsieci.
  * [Zapora platformy Azure](../firewall/overview.md) jest wdrażana we własnej podsieci.
  * Reguła DNATa tłumaczy publiczny adres IP na adres IP frontonu LB.
* Żądania wychodzące rozpoczynają się od węzłów agenta do wewnętrznego adresu IP zapory platformy Azure przy użyciu [trasy zdefiniowanej przez użytkownika](egress-outboundtype.md)
  * Żądania z węzłów agenta AKS są zgodne z UDRem umieszczonym w podsieci, do której został wdrożony klaster AKS.
  * Zapora platformy Azure egresses z sieci wirtualnej z publicznego frontonu IP
  * Dostęp do publicznej sieci Internet lub innych usług platformy Azure przepływy do i z adresu IP frontonu zapory
  * Opcjonalnie dostęp do płaszczyzny kontroli AKS jest chroniony przez [autoryzowane zakresy adresów IP serwera interfejsu API](./api-server-authorized-ip-ranges.md), co obejmuje publiczny adres IP frontonu zapory.
* Ruch wewnętrzny
  * Opcjonalnie, zamiast lub oprócz [Load Balancer publicznego](load-balancer-standard.md) , można użyć [wewnętrznego Load Balancer](internal-lb.md) do obsługi ruchu wewnętrznego, który można wyizolować również w jego własnej podsieci.


Poniższe kroki umożliwiają użycie znacznika FQDN zapory platformy Azure `AzureKubernetesService` w celu ograniczenia ruchu wychodzącego z klastra AKS i podania przykładu sposobu konfigurowania publicznego ruchu przychodzącego przez zaporę.


### <a name="set-configuration-via-environment-variables"></a>Ustawianie konfiguracji za pomocą zmiennych środowiskowych

Zdefiniuj zestaw zmiennych środowiskowych, które mają być używane podczas tworzenia zasobów.

```bash
PREFIX="aks-egress"
RG="${PREFIX}-rg"
LOC="eastus"
PLUGIN=azure
AKSNAME="${PREFIX}"
VNET_NAME="${PREFIX}-vnet"
AKSSUBNET_NAME="aks-subnet"
# DO NOT CHANGE FWSUBNET_NAME - This is currently a requirement for Azure Firewall.
FWSUBNET_NAME="AzureFirewallSubnet"
FWNAME="${PREFIX}-fw"
FWPUBLICIP_NAME="${PREFIX}-fwpublicip"
FWIPCONFIG_NAME="${PREFIX}-fwconfig"
FWROUTE_TABLE_NAME="${PREFIX}-fwrt"
FWROUTE_NAME="${PREFIX}-fwrn"
FWROUTE_NAME_INTERNET="${PREFIX}-fwinternet"
```

### <a name="create-a-virtual-network-with-multiple-subnets"></a>Tworzenie sieci wirtualnej z wieloma podsieciami

Zainicjuj obsługę sieci wirtualnej przy użyciu dwóch oddzielnych podsieci, jednej dla klastra, jednej dla zapory. Opcjonalnie można również utworzyć jeden dla wewnętrznego transferu usług.

![Pusta topologia sieci](media/limit-egress-traffic/empty-network.png)

Utwórz grupę zasobów, w której mają być przechowywane wszystkie zasoby.

```azurecli
# Create Resource Group

az group create --name $RG --location $LOC
```

Utwórz sieć wirtualną z dwiema podsieciami, aby hostować klaster AKS i zaporę platformy Azure. Każda z nich będzie miała własną podsieć. Zacznijmy od sieci AKS.

```
# Dedicated virtual network with AKS subnet

az network vnet create \
    --resource-group $RG \
    --name $VNET_NAME \
    --location $LOC \
    --address-prefixes 10.42.0.0/16 \
    --subnet-name $AKSSUBNET_NAME \
    --subnet-prefix 10.42.1.0/24

# Dedicated subnet for Azure Firewall (Firewall name cannot be changed)

az network vnet subnet create \
    --resource-group $RG \
    --vnet-name $VNET_NAME \
    --name $FWSUBNET_NAME \
    --address-prefix 10.42.2.0/24
```

### <a name="create-and-set-up-an-azure-firewall-with-a-udr"></a>Tworzenie i Konfigurowanie zapory platformy Azure za pomocą UDR

Reguły ruchu przychodzącego i wychodzącego zapory platformy Azure muszą być skonfigurowane. Głównym celem zapory jest umożliwienie organizacjom konfigurowania szczegółowych zasad ruchu przychodzącego i wychodzącego w klastrze AKS.

![Zapora i UDR](media/limit-egress-traffic/firewall-udr.png)


> [!IMPORTANT]
> Jeśli klaster lub aplikacja tworzy dużą liczbę połączeń wychodzących kierowanych do tego samego lub małego podzestawu miejsc docelowych, może być konieczne więcej adresów IP frontonu zapory, aby uniknąć maxing portów na adres IP frontonu.
> Aby uzyskać więcej informacji na temat tworzenia zapory platformy Azure z wieloma adresami IP, zobacz [ **tutaj**](../firewall/quick-create-multiple-ip-template.md)

Utwórz zasób publicznego adresu IP jednostki SKU, który będzie używany jako adres frontonu zapory platformy Azure.

```azurecli
az network public-ip create -g $RG -n $FWPUBLICIP_NAME -l $LOC --sku "Standard"
```

Zarejestruj interfejs wiersza polecenia w wersji zapoznawczej, aby utworzyć zaporę platformy Azure.
```azurecli
# Install Azure Firewall preview CLI extension

az extension add --name azure-firewall

# Deploy Azure Firewall

az network firewall create -g $RG -n $FWNAME -l $LOC --enable-dns-proxy true
```
Utworzony wcześniej adres IP można teraz przypisać do frontonu zapory.

> [!NOTE]
> Skonfigurowanie publicznego adresu IP w zaporze platformy Azure może potrwać kilka minut.
> Aby można było korzystać z nazwy FQDN w regułach sieci, należy włączyć serwer proxy DNS. po włączeniu Zapora nasłuchuje na porcie 53 i przekaże żądania DNS do serwera DNS określonego powyżej. Dzięki temu Zapora będzie mogła automatycznie przetłumaczyć tę nazwę FQDN.

```azurecli
# Configure Firewall IP Config

az network firewall ip-config create -g $RG -f $FWNAME -n $FWIPCONFIG_NAME --public-ip-address $FWPUBLICIP_NAME --vnet-name $VNET_NAME
```

Gdy poprzednie polecenie zakończyło się pomyślnie, Zapisz adres IP frontonu zapory, aby skonfigurować go później.

```bash
# Capture Firewall IP Address for Later Use

FWPUBLIC_IP=$(az network public-ip show -g $RG -n $FWPUBLICIP_NAME --query "ipAddress" -o tsv)
FWPRIVATE_IP=$(az network firewall show -g $RG -n $FWNAME --query "ipConfigurations[0].privateIpAddress" -o tsv)
```

> [!NOTE]
> W przypadku używania bezpiecznego dostępu do serwera interfejsu API AKS z [autoryzowanymi zakresami adresów IP](./api-server-authorized-ip-ranges.md)należy dodać publiczny adres IP zapory do autoryzowanego zakresu adresów IP.

### <a name="create-a-udr-with-a-hop-to-azure-firewall"></a>Tworzenie UDR z przeskokiem do zapory platformy Azure

Platforma Azure automatycznie kieruje ruchem między podsieciami platformy Azure, sieciami wirtualnymi i sieciami lokalnymi. Jeśli chcesz zmienić domyślny Routing systemu Azure, możesz to zrobić, tworząc tabelę tras.

Utwórz pustą tabelę tras, która ma zostać skojarzona z daną podsiecią. W tabeli tras zostanie zdefiniowany następny przeskok, który został utworzony powyżej przez zaporę platformy Azure. Każda podsieć może mieć skojarzoną ze sobą żadną lub jedną tabelę tras.

```azurecli
# Create UDR and add a route for Azure Firewall

az network route-table create -g $RG -l $LOC --name $FWROUTE_TABLE_NAME
az network route-table route create -g $RG --name $FWROUTE_NAME --route-table-name $FWROUTE_TABLE_NAME --address-prefix 0.0.0.0/0 --next-hop-type VirtualAppliance --next-hop-ip-address $FWPRIVATE_IP --subscription $SUBID
az network route-table route create -g $RG --name $FWROUTE_NAME_INTERNET --route-table-name $FWROUTE_TABLE_NAME --address-prefix $FWPUBLIC_IP/32 --next-hop-type Internet
```

Zapoznaj się z [dokumentacją dotyczącą trasy sieci wirtualnej](../virtual-network/virtual-networks-udr-overview.md#user-defined) , aby dowiedzieć się, jak zastąpić domyślne trasy systemu platformy Azure lub dodać dodatkowe trasy do tabeli tras podsieci.

### <a name="adding-firewall-rules"></a>Dodawanie reguł zapory

Poniżej znajdują się trzy reguły sieciowe, których można użyć do skonfigurowania zapory, może być konieczne dostosowanie tych reguł na podstawie wdrożenia. Pierwsza reguła umożliwia dostęp do portu 9000 za pośrednictwem protokołu TCP. Druga reguła zezwala na dostęp do portów 1194 i 123 za pośrednictwem protokołu UDP (Jeśli wdrażasz program na platformie Azure w Chinach, możesz potrzebować [więcej](#azure-china-21vianet-required-network-rules)). Obie te reguły zezwalają tylko na ruch przychodzący do notacji CIDR regionu platformy Azure, która jest używana, w tym przypadku Wschodnie stany USA. Na koniec dodamy trzecią regułę sieci otwierającą port 123 do `ntp.ubuntu.com` nazwy FQDN za pośrednictwem protokołu UDP (dodanie nazwy FQDN jako reguły sieci jest jedną z określonych funkcji zapory platformy Azure i musisz ją dostosować przy użyciu własnych opcji).

Po ustawieniu reguł sieci należy również dodać regułę aplikacji przy użyciu programu obejmującą `AzureKubernetesService` wszystkie potrzebne nazwy FQDN dostępne za pośrednictwem portu TCP 443 i 80.

```
# Add FW Network Rules

az network firewall network-rule create -g $RG -f $FWNAME --collection-name 'aksfwnr' -n 'apiudp' --protocols 'UDP' --source-addresses '*' --destination-addresses "AzureCloud.$LOC" --destination-ports 1194 --action allow --priority 100
az network firewall network-rule create -g $RG -f $FWNAME --collection-name 'aksfwnr' -n 'apitcp' --protocols 'TCP' --source-addresses '*' --destination-addresses "AzureCloud.$LOC" --destination-ports 9000
az network firewall network-rule create -g $RG -f $FWNAME --collection-name 'aksfwnr' -n 'time' --protocols 'UDP' --source-addresses '*' --destination-fqdns 'ntp.ubuntu.com' --destination-ports 123

# Add FW Application Rules

az network firewall application-rule create -g $RG -f $FWNAME --collection-name 'aksfwar' -n 'fqdn' --source-addresses '*' --protocols 'http=80' 'https=443' --fqdn-tags "AzureKubernetesService" --action allow --priority 100
```

Zobacz [dokumentację zapory platformy Azure](../firewall/overview.md) , aby dowiedzieć się więcej na temat usługi Zapora systemu Azure.

### <a name="associate-the-route-table-to-aks"></a>Skojarz tabelę tras z AKS

Aby można było skojarzyć klaster z zaporą, dedykowana podsieć klastra musi odwoływać się do utworzonej powyżej tabeli tras. Skojarzenie można wykonać, wydając polecenie do sieci wirtualnej, w której klaster i Zapora mają aktualizować tabelę tras w podsieci klastra.

```azurecli
# Associate route table with next hop to Firewall to the AKS subnet

az network vnet subnet update -g $RG --vnet-name $VNET_NAME --name $AKSSUBNET_NAME --route-table $FWROUTE_TABLE_NAME
```

### <a name="deploy-aks-with-outbound-type-of-udr-to-the-existing-network"></a>Wdróż AKS z typem wychodzącym UDR do istniejącej sieci

Teraz klaster AKS można wdrożyć w istniejącej sieci wirtualnej. Użyjemy również [typu `userDefinedRouting` wychodzącego ](egress-outboundtype.md). Ta funkcja zapewnia, że każdy ruch wychodzący będzie wymuszany przez zaporę, a żadne inne ścieżki wychodzące nie będą dostępne (domyślnie można użyć typu Load Balancerd).

![AKS — Wdróż](media/limit-egress-traffic/aks-udr-fw.png)

### <a name="create-a-service-principal-with-access-to-provision-inside-the-existing-virtual-network"></a>Tworzenie jednostki usługi z dostępem do inicjowania obsługi administracyjnej w istniejącej sieci wirtualnej

Nazwa główna usługi jest używana przez AKS do tworzenia zasobów klastra. Nazwa główna usługi, która jest przesyłana w czasie tworzenia, jest używana do tworzenia podstawowych zasobów AKS, takich jak zasoby magazynu, adresy IP i moduły równoważenia obciążenia używane przez AKS (zamiast tego można także użyć [tożsamości zarządzanej](use-managed-identity.md) ). Jeśli nie przyznano odpowiednich uprawnień, nie będzie można zainicjować obsługi administracyjnej klastra AKS.

```azurecli
# Create SP and Assign Permission to Virtual Network

az ad sp create-for-rbac -n "${PREFIX}sp" --skip-assignment
```

Teraz Zastąp `APPID` `PASSWORD` poniższe i poniżej z identyfikatorem jednostki usługi i hasłem głównym usługi generowanym automatycznie przez poprzednie dane wyjściowe polecenia. Odwołujemy się do identyfikatora zasobu sieci wirtualnej, aby przyznać uprawnienia do nazwy głównej usługi, dzięki czemu AKS może wdrożyć w niej zasoby.

```azurecli
APPID="<SERVICE_PRINCIPAL_APPID_GOES_HERE>"
PASSWORD="<SERVICEPRINCIPAL_PASSWORD_GOES_HERE>"
VNETID=$(az network vnet show -g $RG --name $VNET_NAME --query id -o tsv)

# Assign SP Permission to VNET

az role assignment create --assignee $APPID --scope $VNETID --role "Network Contributor"
```

Szczegółowe uprawnienia, które są wymagane w [tym miejscu](kubernetes-service-principal.md#delegate-access-to-other-azure-resources), można znaleźć tutaj.

> [!NOTE]
> W przypadku korzystania z wtyczki sieciowej korzystającą wtyczki kubenet należy przyznać jednostce usługi AKS lub uprawnienia zarządzanej tożsamości do wstępnie utworzonej tabeli tras, ponieważ korzystającą wtyczki kubenet wymaga tabeli tras do dodawania reguł routingu niezbędnych. 
> ```azurecli-interactive
> RTID=$(az network route-table show -g $RG -n $FWROUTE_TABLE_NAME --query id -o tsv)
> az role assignment create --assignee $APPID --scope $RTID --role "Network Contributor"
> ```

### <a name="deploy-aks"></a>Wdróż AKS

Na koniec klaster AKS można wdrożyć w istniejącej podsieci dedykowanej dla klastra. Podsieć docelowa do wdrożenia jest zdefiniowana za pomocą zmiennej środowiskowej `$SUBNETID` . Nie zdefiniowano `$SUBNETID` zmiennej w poprzednich krokach. Aby ustawić wartość identyfikatora podsieci, można użyć następującego polecenia:

```azurecli
SUBNETID=$(az network vnet subnet show -g $RG --vnet-name $VNET_NAME --name $AKSSUBNET_NAME --query id -o tsv)
```

Zdefiniujesz typ wychodzący, aby używał UDR, który już istnieje w podsieci. Ta konfiguracja umożliwi AKS pominięcie instalacji i aprowizacji adresów IP dla modułu równoważenia obciążenia.

> [!IMPORTANT]
> Aby uzyskać więcej informacji na temat typu wychodzącego UDR, łącznie z ograniczeniami, zobacz [**wyjście ruchu wyjściowego typu UDR**](egress-outboundtype.md#limitations).


> [!TIP]
> Dodatkowe funkcje można dodać do wdrożenia klastra, takiego jak [**klaster prywatny**](private-clusters.md). 
>
> Można dodać funkcję AKS dla [**dozwolonych zakresów adresów IP serwera interfejsu API**](api-server-authorized-ip-ranges.md) , aby ograniczyć dostęp serwera API tylko do publicznego punktu końcowego zapory. Funkcja zakresów autoryzowanych adresów IP jest oznaczona jako opcjonalna na diagramie. W przypadku włączenia funkcji autoryzowanego zakresu adresów IP w celu ograniczenia dostępu do serwera interfejsu API narzędzia deweloperskie muszą używać serwera przesiadkowego z sieci wirtualnej zapory lub należy dodać wszystkie punkty końcowe dewelopera do autoryzowanego zakresu adresów IP.

```azurecli
az aks create -g $RG -n $AKSNAME -l $LOC \
  --node-count 3 --generate-ssh-keys \
  --network-plugin $PLUGIN \
  --outbound-type userDefinedRouting \
  --service-cidr 10.41.0.0/16 \
  --dns-service-ip 10.41.0.10 \
  --docker-bridge-address 172.17.0.1/16 \
  --vnet-subnet-id $SUBNETID \
  --service-principal $APPID \
  --client-secret $PASSWORD \
  --api-server-authorized-ip-ranges $FWPUBLIC_IP
```

### <a name="enable-developer-access-to-the-api-server"></a>Włącz dostęp dewelopera do serwera interfejsu API

Jeśli w poprzednim kroku użyto autoryzowanych zakresów adresów IP, należy dodać adresy IP narzędzi deweloperskich do listy klastrów AKS zatwierdzonych zakresów adresów IP w celu uzyskania dostępu do serwera interfejsu API. Innym rozwiązaniem jest skonfigurowanie serwera przesiadkowego z wymaganymi narzędziami w ramach oddzielnej podsieci w sieci wirtualnej zapory.

Dodaj inny adres IP do zatwierdzonych zakresów przy użyciu następującego polecenia

```bash
# Retrieve your IP address
CURRENT_IP=$(dig @resolver1.opendns.com ANY myip.opendns.com +short)

# Add to AKS approved list
az aks update -g $RG -n $AKSNAME --api-server-authorized-ip-ranges $CURRENT_IP/32

```

 Użyj polecenia [AZ AKS Get-Credentials] [AZ-AKS-Get-Credentials], aby skonfigurować program do `kubectl` nawiązywania połączenia z nowo utworzonym klastrem Kubernetes. 

 ```azurecli
 az aks get-credentials -g $RG -n $AKSNAME
 ```

### <a name="deploy-a-public-service"></a>Wdrażanie usługi publicznej
Teraz możesz zacząć ujawniać usługi i wdrażać aplikacje w tym klastrze. W tym przykładzie udostępnimy publiczną usługę, ale możesz też wybrać opcję udostępnienia usługi wewnętrznej za pośrednictwem [wewnętrznego modułu równoważenia obciążenia](internal-lb.md).

![DNAT usługi publicznej](media/limit-egress-traffic/aks-create-svc.png)

Wdróż aplikację do głosowania na platformie Azure, kopiując YAML poniżej do pliku o nazwie `example.yaml` .

```yaml
# voting-storage-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: voting-storage
spec:
  replicas: 1
  selector:
    matchLabels:
      app: voting-storage
  template:
    metadata:
      labels:
        app: voting-storage
    spec:
      containers:
      - name: voting-storage
        image: mcr.microsoft.com/aks/samples/voting/storage:2.0
        args: ["--ignore-db-dir=lost+found"]
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 3306
          name: mysql
        volumeMounts:
        - name: mysql-persistent-storage
          mountPath: /var/lib/mysql
        env:
        - name: MYSQL_ROOT_PASSWORD
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_ROOT_PASSWORD
        - name: MYSQL_USER
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_USER
        - name: MYSQL_PASSWORD
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_PASSWORD
        - name: MYSQL_DATABASE
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_DATABASE
      volumes:
      - name: mysql-persistent-storage
        persistentVolumeClaim:
          claimName: mysql-pv-claim
---
# voting-storage-secret.yaml
apiVersion: v1
kind: Secret
metadata:
  name: voting-storage-secret
type: Opaque
data:
  MYSQL_USER: ZGJ1c2Vy
  MYSQL_PASSWORD: UGFzc3dvcmQxMg==
  MYSQL_DATABASE: YXp1cmV2b3Rl
  MYSQL_ROOT_PASSWORD: UGFzc3dvcmQxMg==
---
# voting-storage-pv-claim.yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mysql-pv-claim
spec:
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
---
# voting-storage-service.yaml
apiVersion: v1
kind: Service
metadata:
  name: voting-storage
  labels: 
    app: voting-storage
spec:
  ports:
  - port: 3306
    name: mysql
  selector:
    app: voting-storage
---
# voting-app-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: voting-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: voting-app
  template:
    metadata:
      labels:
        app: voting-app
    spec:
      containers:
      - name: voting-app
        image: mcr.microsoft.com/aks/samples/voting/app:2.0
        imagePullPolicy: Always
        ports:
        - containerPort: 8080
          name: http
        env:
        - name: MYSQL_HOST
          value: "voting-storage"
        - name: MYSQL_USER
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_USER
        - name: MYSQL_PASSWORD
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_PASSWORD
        - name: MYSQL_DATABASE
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_DATABASE
        - name: ANALYTICS_HOST
          value: "voting-analytics"
---
# voting-app-service.yaml
apiVersion: v1
kind: Service
metadata:
  name: voting-app
  labels: 
    app: voting-app
spec:
  type: LoadBalancer
  ports:
  - port: 80
    targetPort: 8080
    name: http
  selector:
    app: voting-app
---
# voting-analytics-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: voting-analytics
spec:
  replicas: 1
  selector:
    matchLabels:
      app: voting-analytics
      version: "2.0"
  template:
    metadata:
      labels:
        app: voting-analytics
        version: "2.0"
    spec:
      containers:
      - name: voting-analytics
        image: mcr.microsoft.com/aks/samples/voting/analytics:2.0
        imagePullPolicy: Always
        ports:
        - containerPort: 8080
          name: http
        env:
        - name: MYSQL_HOST
          value: "voting-storage"
        - name: MYSQL_USER
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_USER
        - name: MYSQL_PASSWORD
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_PASSWORD
        - name: MYSQL_DATABASE
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_DATABASE
---
# voting-analytics-service.yaml
apiVersion: v1
kind: Service
metadata:
  name: voting-analytics
  labels: 
    app: voting-analytics
spec:
  ports:
  - port: 8080
    name: http
  selector:
    app: voting-analytics
```

Wdróż usługę, uruchamiając:

```bash
kubectl apply -f example.yaml
```

### <a name="add-a-dnat-rule-to-azure-firewall"></a>Dodawanie reguły DNAT do zapory platformy Azure

> [!IMPORTANT]
> W przypadku korzystania z zapory platformy Azure w celu ograniczenia ruchu wychodzącego i utworzenia trasy zdefiniowanej przez użytkownika (UDR) w celu wymuszenia całego ruchu wychodzącego upewnij się, że utworzono odpowiednią regułę DNAT w zaporze, aby prawidłowo zezwolić na ruch przychodzący. Używanie zapory platformy Azure z UDR powoduje przerwanie konfiguracji transferu danych przychodzących z powodu routingu asymetrycznego. (Problem występuje, jeśli podsieć AKS ma trasę domyślną, która przechodzi do prywatnego adresu IP zapory, ale używasz publicznego modułu równoważenia obciążenia — ruchu przychodzącego lub usługi Kubernetes typu: moduł równoważenia danych). W takim przypadku ruch przychodzącego modułu równoważenia obciążenia jest odbierany za pośrednictwem jego publicznego adresu IP, ale ścieżka zwrotna przechodzi przez prywatny adres IP zapory. Ze względu na to, że Zapora jest stanowa, opuszcza pakiet, ponieważ Zapora nie rozpoznaje ustanowionej sesji. Aby dowiedzieć się, jak zintegrować zaporę platformy Azure z ruchem przychodzącym lub usługą równoważenia obciążenia, zobacz [integrowanie zapory platformy Azure z usługą azure usługa Load Balancer w warstwie Standardowa](../firewall/integrate-lb.md).


Aby można było skonfigurować łączność z ruchem przychodzącym, reguła DNAT musi być zapisywana w zaporze platformy Azure. W celu przetestowania łączności z klastrem reguła jest definiowana dla publicznego adresu IP frontonu zapory, która będzie kierować do wewnętrznego adresu IP uwidocznionego przez usługę wewnętrzną.

Adres docelowy można dostosować, ponieważ jest to port zapory, do którego ma zostać uzyskany dostęp. Przetłumaczony adres musi być adresem IP wewnętrznego modułu równoważenia obciążenia. Przetłumaczony port musi być widocznym portem dla usługi Kubernetes.

Należy określić wewnętrzny adres IP przypisany do modułu równoważenia obciążenia utworzonego przez usługę Kubernetes. Pobierz adres, uruchamiając:

```bash
kubectl get services
```

Wymagany adres IP zostanie wyświetlony w kolumnie zewnętrzny adres IP, podobnie do poniższego.

```bash
NAME               TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
kubernetes         ClusterIP      10.41.0.1       <none>        443/TCP        10h
voting-analytics   ClusterIP      10.41.88.129    <none>        8080/TCP       9m
voting-app         LoadBalancer   10.41.185.82    20.39.18.6    80:32718/TCP   9m
voting-storage     ClusterIP      10.41.221.201   <none>        3306/TCP       9m
```

Uzyskaj adres IP usługi, uruchamiając:
```bash
SERVICE_IP=$(k get svc voting-app -o jsonpath='{.status.loadBalancer.ingress[*].ip}')
```

Dodaj regułę NAT, uruchamiając:
```azurecli
az network firewall nat-rule create --collection-name exampleset --destination-addresses $FWPUBLIC_IP --destination-ports 80 --firewall-name $FWNAME --name inboundrule --protocols Any --resource-group $RG --source-addresses '*' --translated-port 80 --action Dnat --priority 100 --translated-address $SERVICE_IP
```

### <a name="validate-connectivity"></a>Sprawdź poprawność łączności

Przejdź do adresu IP frontonu zapory platformy Azure w przeglądarce, aby sprawdzić poprawność łączności.

Powinna zostać wyświetlona aplikacja do głosowania AKS. W tym przykładzie publiczny adres IP zapory był `52.253.228.132` .


![Zrzut ekranu przedstawia aplikację do głosowania K S z przyciskami dla kotów, psów i resetowania oraz sum.](media/limit-egress-traffic/aks-vote.png)


### <a name="clean-up-resources"></a>Czyszczenie zasobów

Aby wyczyścić zasoby platformy Azure, Usuń grupę zasobów AKS.

```azurecli
az group delete -g $RG
```

## <a name="next-steps"></a>Następne kroki

Ten artykuł zawiera informacje o portach i adresach, które mają być dozwolone w przypadku ograniczenia ruchu wychodzącego dla klastra. Pokazano również, jak zabezpieczyć ruch wychodzący przy użyciu zapory platformy Azure. 

W razie potrzeby można uogólnić powyższe kroki, aby przesłać dalej ruch do preferowanego rozwiązania [wychodzącego `userDefinedRoute` ](egress-outboundtype.md).

Jeśli chcesz ograniczyć sposób komunikacji między sobą i ograniczeniami ruchu East-West w klastrze, zobacz [bezpieczny ruch między narzędziami sieciowymi w programie AKS][network-policy].

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[network-policy]: use-network-policies.md
[azure-firewall]: ../firewall/overview.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[aks-upgrade]: upgrade-cluster.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[dev-spaces-service-tags]: ../dev-spaces/configure-networking.md#virtual-network-or-subnet-configurations
