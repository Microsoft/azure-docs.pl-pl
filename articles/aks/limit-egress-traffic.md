---
title: Ograniczanie ruchu wychodzącego w Azure Kubernetes Service (AKS)
description: Dowiedz się, jakie porty i adresy są wymagane do kontrolowania ruchu wychodzącego Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.author: jpalma
ms.date: 01/12/2021
author: palma21
ms.openlocfilehash: 39c0877b96a3e8c6c716c1ab9ae7ba11575990a0
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765597"
---
# <a name="control-egress-traffic-for-cluster-nodes-in-azure-kubernetes-service-aks"></a>Kontrolowanie ruchu wychodzącego dla węzłów klastra w Azure Kubernetes Service (AKS)

Ten artykuł zawiera niezbędne szczegóły, które umożliwiają zabezpieczenie ruchu wychodzącego z usługi Azure Kubernetes Service (AKS). Zawiera on wymagania dotyczące klastra dla podstawowego wdrożenia usługi AKS oraz dodatkowe wymagania dotyczące opcjonalnych dodatków i funkcji. [Na końcu zostanie podany przykład konfigurowania](#restrict-egress-traffic-using-azure-firewall)tych wymagań za pomocą Azure Firewall . Te informacje można jednak zastosować do dowolnej metody ograniczenia ruchu wychodzącego lub urządzenia.

## <a name="background"></a>Tło

Klastry usługi AKS są wdrażane w sieci wirtualnej. Ta sieć może być zarządzana (utworzona przez usługę AKS) lub niestandardowa (wcześniej skonfigurowana przez użytkownika). W obu przypadkach klaster **ma** zależności ruchu wychodzącego od usług spoza tej sieci wirtualnej (usługa nie ma zależności przychodzących).

W celu zarządzania i działania węzły w klastrze usługi AKS muszą mieć dostęp do określonych portów i w pełni kwalifikowanych nazw domen (FQDN). Te punkty końcowe są wymagane, aby węzły komunikowały się z serwerem interfejsu API lub pobierały i instalowały podstawowe składniki klastra Kubernetes i aktualizacje zabezpieczeń węzła. Na przykład klaster musi ściągać podstawowe obrazy kontenerów systemu z Microsoft Container Registry (MCR).

Zależności ruchu wychodzącego usługi AKS są prawie całkowicie zdefiniowane za pomocą FQDN, które nie mają za sobą statycznych adresów. Brak adresów statycznych oznacza, że sieciowych grup zabezpieczeń nie można używać do blokowania ruchu wychodzącego z klastra usługi AKS. 

Domyślnie klastry usługi AKS mają nieograniczony dostęp wychodzący (wychodzący) do Internetu. Ten poziom dostępu do sieci umożliwia uruchamianym węzłom i usługom dostęp do zasobów zewnętrznych zgodnie z potrzebami. Jeśli chcesz ograniczyć ruch wychodzący, ograniczona liczba portów i adresów musi być dostępna do obsługi zadań konserwacji klastra w dobrej kondycji. Najprostszym rozwiązaniem zabezpieczania adresów wychodzących jest użycie urządzenia zapory, które może kontrolować ruch wychodzący na podstawie nazw domen. Azure Firewall może na przykład ograniczyć wychodzący ruch HTTP i HTTPS na podstawie WQDN miejsca docelowego. Możesz również skonfigurować preferowaną zaporę i reguły zabezpieczeń, aby zezwolić na te wymagane porty i adresy.

> [!IMPORTANT]
> W tym dokumencie opisano tylko sposób blokowania ruchu wychodzącego z podsieci usługi AKS. Domyślnie nie ma żadnych wymagań dotyczących danych przychodzących.  Blokowanie **ruchu podsieci wewnętrznej** przy użyciu sieciowych grup zabezpieczeń i zapór nie jest obsługiwane. Aby kontrolować i blokować ruch w klastrze, użyj [**_zasad sieciowych_**][network-policy].

## <a name="required-outbound-network-rules-and-fqdns-for-aks-clusters"></a>Wymagane reguły sieciowego ruchu wychodzącego i sieci FQDN dla klastrów usługi AKS

Następujące reguły sieci i FQDN/aplikacji są wymagane dla klastra usługi AKS. Można ich użyć, jeśli chcesz skonfigurować rozwiązanie inne niż Azure Firewall.

* Zależności adresów IP są dla ruchu spoza protokołu HTTP/S (ruch TCP i UDP)
* Punkty końcowe HTTP/HTTPS w sieci FQDN można umieścić na urządzeniu zapory.
* Punkty końcowe HTTP/HTTPS z symbolami wieloznacznymi to zależności, które mogą się różnić w zależności od liczby kwalifikatorów w klastrze usługi AKS.
* Usługa AKS używa kontrolera danych do wstrzykiwania nazwy FQDN jako zmiennej środowiskowej do wszystkich wdrożeń w ramach systemów kube-system i gatekeeper-system, co zapewnia, że cała komunikacja systemowa między węzłami i serwerem interfejsu API używa nazwy FQDN serwera interfejsu API, a nie adresu IP serwera interfejsu API. 
* Jeśli masz aplikację lub rozwiązanie, które musi komunikować się  z serwerem interfejsu API, musisz dodać dodatkową regułę sieci, aby zezwolić na komunikację TCP do portu *443* adresu IP serwera interfejsu API.
* W rzadkich przypadkach w przypadku operacji konserwacji adres IP serwera interfejsu API może ulec zmianie. Operacje planowanej konserwacji, które mogą zmienić adres IP serwera interfejsu API, są zawsze przekazywane z wyprzedzeniem.


### <a name="azure-global-required-network-rules"></a>Globalne reguły sieci wymagane przez platformę Azure

Wymagane reguły sieci i zależności adresów IP to:

| Docelowy punkt końcowy                                                             | Protokół | Port    | Zastosowanie  |
|----------------------------------------------------------------------------------|----------|---------|------|
| **`*:1194`** <br/> *Lub* <br/> [Tag usługi](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:1194`** <br/> *Lub* <br/> [Regionalne cidr](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:1194`** <br/> *Lub* <br/> **`APIServerPublicIP:1194`** `(only known after cluster creation)`  | UDP           | 1194      | Tunelowana bezpieczna komunikacja między węzłami a płaszczyzną sterowania. Nie jest to wymagane w przypadku [klastrów prywatnych](private-clusters.md)|
| **`*:9000`** <br/> *Lub* <br/> [Tag usługi](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:9000`** <br/> *Lub* <br/> [Regionalne cidr](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:9000`** <br/> *Lub* <br/> **`APIServerPublicIP:9000`** `(only known after cluster creation)`  | TCP           | 9000      | Do tunelowania bezpiecznej komunikacji między węzłami a płaszczyzną sterowania. Nie jest to wymagane w przypadku [klastrów prywatnych](private-clusters.md) |
| **`*:123`** lub **`ntp.ubuntu.com:123`** (w przypadku używania Azure Firewall sieci)  | UDP      | 123     | Wymagane do synchronizacji czasu protokołu NTP (Network Time Protocol) w węzłach systemu Linux.                 |
| **`CustomDNSIP:53`** `(if using custom DNS servers)`                             | UDP      | 53      | Jeśli używasz niestandardowych serwerów DNS, upewnij się, że są one dostępne dla węzłów klastra. |
| **`APIServerPublicIP:443`** `(if running pods/deployments that access the API Server)` | TCP      | 443     | Wymagane w przypadku uruchamiania zasobników/wdrożeń, które mają dostęp do serwera interfejsu API, te zasobniki/wdrożenia będą używać adresu IP interfejsu API. Nie jest to wymagane w przypadku [klastrów prywatnych](private-clusters.md)  |

### <a name="azure-global-required-fqdn--application-rules"></a>Globalnie wymagana fQDN/reguły aplikacji platformy Azure 

Wymagane są następujące reguły FQDN/aplikacji:

| Docelowa fQDN                 | Port            | Zastosowanie      |
|----------------------------------|-----------------|----------|
| **`*.hcp.<location>.azmk8s.io`** | **`HTTPS:443`** | Wymagane do komunikacji < interfejsu API > Node. Zastąp *\<location\>* z regionem, w którym wdrożono klaster usługi AKS. |
| **`mcr.microsoft.com`**          | **`HTTPS:443`** | Wymagane do uzyskania dostępu do obrazów w Microsoft Container Registry (MCR). Ten rejestr zawiera obrazy/wykresy firmowych (na przykład coreDNS itp.). Te obrazy są wymagane do prawidłowego tworzenia i działania klastra, w tym operacji skalowania i uaktualniania.  |
| **`*.data.mcr.microsoft.com`**   | **`HTTPS:443`** | Wymagane w przypadku magazynu MCR z usługą Azure Content Delivery Network (CDN). |
| **`management.azure.com`**       | **`HTTPS:443`** | Wymagane w przypadku operacji platformy Kubernetes względem interfejsu API platformy Azure. |
| **`login.microsoftonline.com`**  | **`HTTPS:443`** | Wymagane do Azure Active Directory uwierzytelniania. |
| **`packages.microsoft.com`**     | **`HTTPS:443`** | Ten adres jest repozytorium pakietów firmy Microsoft używanym do buforowania *operacji apt-get.*  Przykładowe pakiety to Moby, PowerShell i interfejs wiersza polecenia platformy Azure. |
| **`acs-mirror.azureedge.net`**   | **`HTTPS:443`** | Ten adres jest dla repozytorium wymaganego do pobrania i zainstalowania wymaganych plików binarnych, takich jak kubenet i Azure CNI. |

### <a name="azure-china-21vianet-required-network-rules"></a>Azure (Chiny) — 21Vianet wymaganych reguł sieci

Wymagane reguły sieci i zależności adresów IP to:

| Docelowy punkt końcowy                                                             | Protokół | Port    | Zastosowanie  |
|----------------------------------------------------------------------------------|----------|---------|------|
| **`*:1194`** <br/> *Lub* <br/> [Tag usługi](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.Region:1194`** <br/> *Lub* <br/> [Regionalne RPR](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:1194`** <br/> *Lub* <br/> **`APIServerPublicIP:1194`** `(only known after cluster creation)`  | UDP           | 1194      | Do tunelowania bezpiecznej komunikacji między węzłami a płaszczyzną sterowania. |
| **`*:9000`** <br/> *Lub* <br/> [Tag usługi](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:9000`** <br/> *Lub* <br/> [Regionalne RPR](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:9000`** <br/> *Lub* <br/> **`APIServerPublicIP:9000`** `(only known after cluster creation)`  | TCP           | 9000      | Do tunelowania bezpiecznej komunikacji między węzłami a płaszczyzną sterowania. |
| **`*:22`** <br/> *Lub* <br/> [Tag usługi](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:22`** <br/> *Lub* <br/> [Regionalne RPR](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:22`** <br/> *Lub* <br/> **`APIServerPublicIP:22`** `(only known after cluster creation)`  | TCP           | 22      | Do tunelowania bezpiecznej komunikacji między węzłami a płaszczyzną sterowania. |
| **`*:123`** lub **`ntp.ubuntu.com:123`** (w przypadku używania Azure Firewall sieci)  | UDP      | 123     | Wymagane do synchronizacji czasu protokołu NTP (Network Time Protocol) w węzłach systemu Linux.                 |
| **`CustomDNSIP:53`** `(if using custom DNS servers)`                             | UDP      | 53      | Jeśli używasz niestandardowych serwerów DNS, upewnij się, że są one dostępne dla węzłów klastra. |
| **`APIServerPublicIP:443`** `(if running pods/deployments that access the API Server)` | TCP      | 443     | Wymagane w przypadku uruchamiania zasobników/wdrożeń, które mają dostęp do serwera interfejsu API, te zasobniki/wdrożenia będą używać adresu IP interfejsu API.  |

### <a name="azure-china-21vianet-required-fqdn--application-rules"></a>Azure (Chiny) — 21Vianet wymaganych reguł FQDN/aplikacji

Wymagane są następujące reguły FQDN/aplikacji:

| Docelowa fQDN                               | Port            | Zastosowanie      |
|------------------------------------------------|-----------------|----------|
| **`*.hcp.<location>.cx.prod.service.azk8s.cn`**| **`HTTPS:443`** | Wymagane do komunikacji < interfejsu API > Node. *\<location\>* Zamień na region, w którym wdrożono klaster usługi AKS. |
| **`*.tun.<location>.cx.prod.service.azk8s.cn`**| **`HTTPS:443`** | Wymagane do komunikacji < interfejsu API > Node. *\<location\>* Zamień na region, w którym wdrożono klaster usługi AKS. |
| **`mcr.microsoft.com`**                        | **`HTTPS:443`** | Wymagane do uzyskania dostępu do obrazów w Microsoft Container Registry (MCR). Ten rejestr zawiera obrazy/wykresy firmowych (na przykład coreDNS itp.). Te obrazy są wymagane do prawidłowego tworzenia i działania klastra, w tym operacji skalowania i uaktualniania. |
| **`.data.mcr.microsoft.com`**                  | **`HTTPS:443`** | Wymagane w przypadku magazynu MCR z kopią zapasową usługi Azure Content Delivery Network (CDN). |
| **`management.chinacloudapi.cn`**              | **`HTTPS:443`** | Wymagane w przypadku operacji platformy Kubernetes względem interfejsu API platformy Azure. |
| **`login.chinacloudapi.cn`**                   | **`HTTPS:443`** | Wymagane do Azure Active Directory uwierzytelniania. |
| **`packages.microsoft.com`**                   | **`HTTPS:443`** | Ten adres jest repozytorium pakietów firmy Microsoft używanym do buforowania *operacji apt-get.*  Przykładowe pakiety to Moby, PowerShell i interfejs wiersza polecenia platformy Azure. |
| **`*.azk8s.cn`**                               | **`HTTPS:443`** | Ten adres dotyczy repozytorium wymaganego do pobrania i zainstalowania wymaganych plików binarnych, takich jak kubenet i Azure CNI. |

### <a name="azure-us-government-required-network-rules"></a>Reguły sieci wymagane przez platformę Azure US Government

Wymagane reguły sieci i zależności adresów IP to:

| Docelowy punkt końcowy                                                             | Protokół | Port    | Zastosowanie  |
|----------------------------------------------------------------------------------|----------|---------|------|
| **`*:1194`** <br/> *Lub* <br/> [Tag usługi](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:1194`** <br/> *Lub* <br/> [Regionalne RPR](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:1194`** <br/> *Lub* <br/> **`APIServerPublicIP:1194`** `(only known after cluster creation)`  | UDP           | 1194      | Do tunelowania bezpiecznej komunikacji między węzłami a płaszczyzną sterowania. |
| **`*:9000`** <br/> *Lub* <br/> [Tag usługi](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:9000`** <br/> *Lub* <br/> [Regionalne RPR](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:9000`** <br/> *Lub* <br/> **`APIServerPublicIP:9000`** `(only known after cluster creation)`  | TCP           | 9000      | Do tunelowania bezpiecznej komunikacji między węzłami a płaszczyzną sterowania. |
| **`*:123`** lub **`ntp.ubuntu.com:123`** (w przypadku używania Azure Firewall sieci)  | UDP      | 123     | Wymagane do synchronizacji czasu protokołu NTP (Network Time Protocol) w węzłach systemu Linux.                 |
| **`CustomDNSIP:53`** `(if using custom DNS servers)`                             | UDP      | 53      | Jeśli używasz niestandardowych serwerów DNS, upewnij się, że są one dostępne dla węzłów klastra. |
| **`APIServerPublicIP:443`** `(if running pods/deployments that access the API Server)` | TCP      | 443     | Wymagane w przypadku uruchamiania zasobników/wdrożeń, które mają dostęp do serwera interfejsu API, te zasobniki/wdrożenia będą używać adresu IP interfejsu API.  |

### <a name="azure-us-government-required-fqdn--application-rules"></a>Wymagana WQDN/reguły aplikacji na platformie Azure US Government 

Wymagane są następujące reguły FQDN/aplikacji:

| Docelowa fQDN                                        | Port            | Zastosowanie      |
|---------------------------------------------------------|-----------------|----------|
| **`*.hcp.<location>.cx.aks.containerservice.azure.us`** | **`HTTPS:443`** | Wymagane do komunikacji < interfejsu API > Node. *\<location\>* Zamień na region, w którym wdrożono klaster usługi AKS.|
| **`mcr.microsoft.com`**                                 | **`HTTPS:443`** | Wymagane do uzyskania dostępu do obrazów w Microsoft Container Registry (MCR). Ten rejestr zawiera obrazy/wykresy firmowych (na przykład coreDNS itp.). Te obrazy są wymagane do prawidłowego tworzenia i działania klastra, w tym operacji skalowania i uaktualniania. |
| **`*.data.mcr.microsoft.com`**                          | **`HTTPS:443`** | Wymagane w przypadku magazynu MCR z usługą Azure Content Delivery Network (CDN). |
| **`management.usgovcloudapi.net`**                      | **`HTTPS:443`** | Wymagane w przypadku operacji platformy Kubernetes względem interfejsu API platformy Azure. |
| **`login.microsoftonline.us`**                          | **`HTTPS:443`** | Wymagane do Azure Active Directory uwierzytelniania. |
| **`packages.microsoft.com`**                            | **`HTTPS:443`** | Ten adres jest repozytorium pakietów firmy Microsoft używanym do buforowania *operacji apt-get.*  Przykładowe pakiety to Moby, PowerShell i interfejs wiersza polecenia platformy Azure. |
| **`acs-mirror.azureedge.net`**                          | **`HTTPS:443`** | Ten adres dotyczy repozytorium wymaganego do zainstalowania wymaganych plików binarnych, takich jak kubenet i Azure CNI. |

## <a name="optional-recommended-fqdn--application-rules-for-aks-clusters"></a>Opcjonalna zalecana WQDN/reguły aplikacji dla klastrów usługi AKS

Następujące reguły FQDN/aplikacji są opcjonalne, ale zalecane w przypadku klastrów usługi AKS:

| Docelowa fQDN                                                               | Port          | Zastosowanie      |
|--------------------------------------------------------------------------------|---------------|----------|
| **`security.ubuntu.com`, `azure.archive.ubuntu.com`, `changelogs.ubuntu.com`** | **`HTTP:80`** | Ten adres umożliwia węzłom klastra systemu Linux pobranie wymaganych poprawek zabezpieczeń i aktualizacji. |

Jeśli zdecydujesz się zablokować lub nie zezwalać na te Węzły FQNS, węzły będą otrzymywać aktualizacje systemu operacyjnego tylko po uaktualnieniu obrazu węzła lub [uaktualnieniu klastra.](upgrade-cluster.md) [](node-image-upgrade.md)

## <a name="gpu-enabled-aks-clusters"></a>Klastry AKS z włączoną obsługą procesora GPU

### <a name="required-fqdn--application-rules"></a>Wymagana fQDN/reguły aplikacji

Następujące reguły FQDN/aplikacji są wymagane dla klastrów usługi AKS z włączonym procesorem GPU:

| Docelowa fQDN                        | Port      | Zastosowanie      |
|-----------------------------------------|-----------|----------|
| **`nvidia.github.io`**                  | **`HTTPS:443`** | Ten adres jest używany do poprawnej instalacji sterowników i działania w węzłach opartych na procesorze GPU. |
| **`us.download.nvidia.com`**            | **`HTTPS:443`** | Ten adres służy do poprawnej instalacji sterowników i działania w węzłach opartych na procesorze GPU. |
| **`apt.dockerproject.org`**             | **`HTTPS:443`** | Ten adres jest używany do poprawnej instalacji sterowników i działania w węzłach opartych na procesorze GPU. |

## <a name="windows-server-based-node-pools"></a>Pule węzłów oparte na systemie Windows Server 

### <a name="required-fqdn--application-rules"></a>Wymagana FQDN/reguły aplikacji

Następujące reguły FQDN/aplikacji są wymagane do korzystania z pul węzłów opartych na systemie Windows Server:

| Docelowa fQDN                                                           | Port      | Zastosowanie      |
|----------------------------------------------------------------------------|-----------|----------|
| **`onegetcdn.azureedge.net, go.microsoft.com`**                            | **`HTTPS:443`** | Aby zainstalować pliki binarne związane z systemem Windows |
| **`*.mp.microsoft.com, www.msftconnecttest.com, ctldl.windowsupdate.com`** | **`HTTP:80`**   | Aby zainstalować pliki binarne związane z systemem Windows |

## <a name="aks-addons-and-integrations"></a>Dodatki i integracje aKS

### <a name="azure-monitor-for-containers"></a>Usługa Azure Monitor dla kontenerów

Istnieją dwie opcje zapewnienia dostępu do usługi Azure Monitor dla kontenerów. Możesz zezwolić na tag Azure Monitor [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags)  lub zapewnić dostęp do wymaganych reguł FQDN/aplikacji.

#### <a name="required-network-rules"></a>Wymagane reguły sieci

Wymagane są następujące reguły FQDN/aplikacji:

| Docelowy punkt końcowy                                                             | Protokół | Port    | Zastosowanie  |
|----------------------------------------------------------------------------------|----------|---------|------|
| [Tag usługi](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureMonitor:443`**  | TCP           | 443      | Ten punkt końcowy służy do wysyłania danych metryk i dzienników do usług Azure Monitor i Log Analytics. |

#### <a name="required-fqdn--application-rules"></a>Wymagana FQDN/reguły aplikacji

Następujące reguły FQDN/aplikacji są wymagane dla klastrów usługi AKS, które mają włączoną Azure Monitor dla kontenerów:

| Nazwa FQDN                                    | Port      | Zastosowanie      |
|-----------------------------------------|-----------|----------|
| dc.services.visualstudio.com | **`HTTPS:443`**    | Ten punkt końcowy jest używany do metryk i monitorowania telemetrii przy użyciu Azure Monitor. |
| *.ods.opinsights.azure.com    | **`HTTPS:443`**    | Ten punkt końcowy jest używany przez Azure Monitor do pozyskania danych analizy dzienników. |
| *.oms.opinsights.azure.com | **`HTTPS:443`** | Ten punkt końcowy jest używany przez usługę omsagent, która jest używana do uwierzytelniania usługi Log Analytics. |
| *.monitoring.azure.com | **`HTTPS:443`** | Ten punkt końcowy służy do wysyłania danych metryk do Azure Monitor. |

### <a name="azure-dev-spaces"></a>Azure Dev Spaces

Zaktualizuj konfigurację zapory lub zabezpieczeń, aby zezwolić na ruch sieciowy do i ze wszystkich poniższych sieci FQDN oraz Azure Dev Spaces [usług infrastruktury.][dev-spaces-service-tags]

#### <a name="required-network-rules"></a>Wymagane reguły sieci

| Docelowy punkt końcowy                                                             | Protokół | Port    | Zastosowanie  |
|----------------------------------------------------------------------------------|----------|---------|------|
| [Tag usługi](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureDevSpaces`**  | TCP           | 443      | Ten punkt końcowy służy do wysyłania danych metryk i dzienników do usług Azure Monitor i Log Analytics. |

#### <a name="required-fqdn--application-rules"></a>Wymagana fQDN/reguły aplikacji 

Następujące reguły FQDN/aplikacji są wymagane w przypadku klastrów usługi AKS, które Azure Dev Spaces włączone:

| Nazwa FQDN                                    | Port      | Zastosowanie      |
|-----------------------------------------|-----------|----------|
| `cloudflare.docker.com` | **`HTTPS:443`** | Ten adres jest używany do ściągania obrazów alpine i innych Azure Dev Spaces linux |
| `gcr.io` | **`HTTPS:443`** | Ten adres jest używany do ściągania obrazów helm/tiller |
| `storage.googleapis.com` | **`HTTPS:443`** | Ten adres jest używany do ściągania obrazów helm/tiller |


### <a name="azure-policy"></a>Azure Policy

#### <a name="required-fqdn--application-rules"></a>Wymagana fQDN/reguły aplikacji 

Następujące reguły FQDN/application są wymagane w przypadku klastrów usługi AKS, które Azure Policy włączone.

| Nazwa FQDN                                          | Port      | Zastosowanie      |
|-----------------------------------------------|-----------|----------|
| **`data.policy.core.windows.net`** | **`HTTPS:443`** | Ten adres jest używany do ściągania zasad kubernetes i zgłaszania stanu zgodności klastra do usługi zasad. |
| **`store.policy.core.windows.net`** | **`HTTPS:443`** | Ten adres jest używany do ściągania artefaktów strażnika wbudowanych zasad. |
| **`gov-prod-policy-data.trafficmanager.net`** | **`HTTPS:443`** | Ten adres jest używany do poprawnego działania Azure Policy.  |
| **`raw.githubusercontent.com`**               | **`HTTPS:443`** | Ten adres jest używany do ściągania wbudowanych zasad z usługi GitHub w celu zapewnienia prawidłowego działania Azure Policy. |
| **`dc.services.visualstudio.com`**            | **`HTTPS:443`** | Azure Policy, który wysyła dane telemetryczne do punktu końcowego szczegółowych informacji aplikacji. |

#### <a name="azure-china-21vianet-required-fqdn--application-rules"></a>Azure (Chiny) — 21Vianet wymaganych reguł FQDN/aplikacji 

Następujące reguły FQDN/application są wymagane w przypadku klastrów usługi AKS, które Azure Policy włączone.

| Nazwa FQDN                                          | Port      | Zastosowanie      |
|-----------------------------------------------|-----------|----------|
| **`data.policy.azure.cn`** | **`HTTPS:443`** | Ten adres jest używany do ściągania zasad kubernetes i zgłaszania stanu zgodności klastra do usługi zasad. |
| **`store.policy.azure.cn`** | **`HTTPS:443`** | Ten adres jest używany do ściągania artefaktów strażnika wbudowanych zasad. |

#### <a name="azure-us-government-required-fqdn--application-rules"></a>Wymagana w witrynie Azure US Government WQDN/reguły aplikacji

Następujące reguły FQDN/application są wymagane w przypadku klastrów usługi AKS, które Azure Policy włączone.

| Nazwa FQDN                                          | Port      | Zastosowanie      |
|-----------------------------------------------|-----------|----------|
| **`data.policy.azure.us`** | **`HTTPS:443`** | Ten adres jest używany do ściągania zasad kubernetes i zgłaszania stanu zgodności klastra do usługi zasad. |
| **`store.policy.azure.us`** | **`HTTPS:443`** | Ten adres jest używany do ściągania artefaktów strażnika wbudowanych zasad. |

## <a name="restrict-egress-traffic-using-azure-firewall"></a>Ograniczanie ruchu wychodzącego przy użyciu usługi Azure Firewall

Azure Firewall udostępnia tag Azure Kubernetes Service `AzureKubernetesService` () FQDN, aby uprościć tę konfigurację. 

> [!NOTE]
> Tag FQDN zawiera wszystkie wymienione powyżej sieci FQDN i jest automatycznie aktualny.
>
> Zalecamy posiadanie co najmniej 20 ip frontonu na stronie Azure Firewall scenariuszy produkcyjnych, aby uniknąć problemów z wyczerpaniem portów SNAT.

Poniżej przedstawiono przykładową architekturę wdrożenia:

![Topologia zablokowana](media/limit-egress-traffic/aks-azure-firewall-egress.png)

* Publiczny ruch przychodzący musi przepływać przez filtry zapory
  * Węzły agenta usługi AKS są izolowane w dedykowanej podsieci.
  * [Azure Firewall](../firewall/overview.md) jest wdrażany we własnej podsieci.
  * Reguła DNAT tłumaczy publiczny adres IP FW na adres IP frontoneu usługi LB.
* Żądania wychodzące rozpoczynają się od węzłów agenta do Azure Firewall IP przy użyciu trasy [zdefiniowanej przez użytkownika](egress-outboundtype.md)
  * Żądania z węzłów agenta usługi AKS są zgodne z węzłów UDR, które zostały umieszczone w podsieci, w których wdrożono klaster usługi AKS.
  * Azure Firewall wychodzą z sieci wirtualnej z frontonia publicznego adresu IP
  * Dostęp do publicznego Internetu lub innych usług platformy Azure przepływa do i z adresu IP frontu zapory
  * Opcjonalnie dostęp do płaszczyzny sterowania usługi AKS jest chroniony przez autoryzowane zakresy adresów IP serwera interfejsu [API,](./api-server-authorized-ip-ranges.md)które obejmują publiczny adres IP frontonu zapory.
* Ruch wewnętrzny
  * Opcjonalnie zamiast lub oprócz [](load-balancer-standard.md) publicznego Load Balancer można użyć wewnętrznego Load Balancer dla ruchu wewnętrznego, który można również odizolować w własnej podsieci. [](internal-lb.md)


Poniższe kroki korzystają z tagu FQDN usługi Azure Firewall, aby ograniczyć ruch wychodzący z klastra usługi AKS i podać przykład sposobu konfigurowania publicznego ruchu przychodzącego za pośrednictwem `AzureKubernetesService` zapory.


### <a name="set-configuration-via-environment-variables"></a>Ustawianie konfiguracji za pomocą zmiennych środowiskowych

Zdefiniuj zestaw zmiennych środowiskowych do użycia podczas tworzenia zasobów.

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

Aprowizowanie sieci wirtualnej z dwiema oddzielnymi podsieciami, jedną dla klastra, jedną dla zapory. Opcjonalnie można również utworzyć go dla wewnętrznego przychodzących usług.

![Pusta topologia sieci](media/limit-egress-traffic/empty-network.png)

Utwórz grupę zasobów do przechowywania wszystkich zasobów.

```azurecli
# Create Resource Group

az group create --name $RG --location $LOC
```

Utwórz sieć wirtualną z dwiema podsieciami do hostowania klastra AKS i Azure Firewall. Każda z nich będzie mieć własną podsieć. Zacznijmy od sieci AKS.

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

### <a name="create-and-set-up-an-azure-firewall-with-a-udr"></a>Tworzenie i konfigurowanie Azure Firewall z UDR

Azure Firewall reguły ruchu przychodzącego i wychodzącego muszą być skonfigurowane. Głównym celem zapory jest umożliwienie organizacjom konfigurowania szczegółowych reguł ruchu przychodzących i wychodzącego do i z klastra AKS.

![Zapora i UDR](media/limit-egress-traffic/firewall-udr.png)


> [!IMPORTANT]
> Jeśli klaster lub aplikacja tworzy dużą liczbę połączeń wychodzących skierowanych do tego samego lub małego podzbioru miejsc docelowych, może być konieczne podanie większej liczby adresów IP frontony zapory, aby uniknąć maksymalnego połączenia portów na adres IP frontony.
> Aby uzyskać więcej informacji na temat tworzenia zapory platformy Azure z wieloma ip, zobacz [ **tutaj**](../firewall/quick-create-multiple-ip-template.md)

Utwórz standardowy zasób publicznego adresu IP sku SKU, który będzie używany jako Azure Firewall adres frontendu.

```azurecli
az network public-ip create -g $RG -n $FWPUBLICIP_NAME -l $LOC --sku "Standard"
```

Zarejestruj rozszerzenie interfejsu wiersza polecenia w wersji zapoznawczej, aby utworzyć Azure Firewall.
```azurecli
# Install Azure Firewall preview CLI extension

az extension add --name azure-firewall

# Deploy Azure Firewall

az network firewall create -g $RG -n $FWNAME -l $LOC --enable-dns-proxy true
```
Utworzony wcześniej adres IP można teraz przypisać do frontonu zapory.

> [!NOTE]
> Skonfigurowanie publicznego adresu IP dla usługi Azure Firewall może potrwać kilka minut.
> Aby można było korzystać z nazwy FQDN w regułach sieciowych, należy włączyć serwer proxy DNS, a po włączeniu zapora będzie nasłuchiwać na porcie 53 i będzie przesyłać żądania DNS do serwera DNS określonego powyżej. Pozwoli to zaporze na automatyczne tłumaczenie tej WQDN.

```azurecli
# Configure Firewall IP Config

az network firewall ip-config create -g $RG -f $FWNAME -n $FWIPCONFIG_NAME --public-ip-address $FWPUBLICIP_NAME --vnet-name $VNET_NAME
```

Jeśli poprzednie polecenie zakończyło się pomyślnie, zapisz adres IP frontony zapory do późniejszej konfiguracji.

```bash
# Capture Firewall IP Address for Later Use

FWPUBLIC_IP=$(az network public-ip show -g $RG -n $FWPUBLICIP_NAME --query "ipAddress" -o tsv)
FWPRIVATE_IP=$(az network firewall show -g $RG -n $FWNAME --query "ipConfigurations[0].privateIpAddress" -o tsv)
```

> [!NOTE]
> Jeśli używasz bezpiecznego dostępu do serwera interfejsu API usługi AKS z autoryzowanymi zakresami adresów [IP,](./api-server-authorized-ip-ranges.md)musisz dodać publiczny adres IP zapory do autoryzowanego zakresu adresów IP.

### <a name="create-a-udr-with-a-hop-to-azure-firewall"></a>Tworzenie celu UDR z przeskoku do Azure Firewall

Platforma Azure automatycznie kieruje ruchem między podsieciami platformy Azure, sieciami wirtualnymi i sieciami lokalnymi. Jeśli chcesz zmienić dowolny domyślny routing platformy Azure, możesz to zrobić, tworząc tabelę tras.

Utwórz pustą tabelę tras, która zostanie skojarzona z podsieć. Tabela tras zdefiniuje następny przeskok jako Azure Firewall powyżej. Każda podsieć może mieć skojarzoną ze sobą żadną lub jedną tabelę tras.

```azurecli
# Create UDR and add a route for Azure Firewall

az network route-table create -g $RG -l $LOC --name $FWROUTE_TABLE_NAME
az network route-table route create -g $RG --name $FWROUTE_NAME --route-table-name $FWROUTE_TABLE_NAME --address-prefix 0.0.0.0/0 --next-hop-type VirtualAppliance --next-hop-ip-address $FWPRIVATE_IP --subscription $SUBID
az network route-table route create -g $RG --name $FWROUTE_NAME_INTERNET --route-table-name $FWROUTE_TABLE_NAME --address-prefix $FWPUBLIC_IP/32 --next-hop-type Internet
```

Zobacz [dokumentację tabeli tras sieci wirtualnej](../virtual-network/virtual-networks-udr-overview.md#user-defined) dotyczącą sposobu zastępowania domyślnych tras systemowych platformy Azure lub dodawania dodatkowych tras do tabeli tras podsieci.

### <a name="adding-firewall-rules"></a>Dodawanie reguł zapory

Poniżej przedstawiono trzy reguły sieci, których można użyć do skonfigurowania w zaporze. Może być konieczne dostosowanie tych reguł na podstawie wdrożenia. Pierwsza reguła zezwala na dostęp do portu 9000 za pośrednictwem protokołu TCP. Druga reguła zezwala na dostęp do portów 1194 i 123 za pośrednictwem protokołu UDP (w przypadku wdrażania na Azure (Chiny) — 21Vianet może być wymagane [więcej ).](#azure-china-21vianet-required-network-rules) Obie te reguły zezwalają tylko na ruch przeznaczony do regionu świadczenia usługi Azure CIDR, który jest przez nas przeznaczony, w tym przypadku do regionu Wschodnie usa. Na koniec dodamy trzecią regułę sieci otwierającą port 123 do sieci FQDN za pośrednictwem `ntp.ubuntu.com` protokołu UDP (dodanie FQDN jako reguły sieci jest jedną z określonych funkcji usługi Azure Firewall i należy dostosować ją przy użyciu własnych opcji).

Po ustawieniu reguł sieci dodamy również regułę aplikacji przy użyciu funkcji , która obejmuje wszystkie potrzebne sieci FQDN dostępne za pośrednictwem portów `AzureKubernetesService` TCP 443 i 80.

```
# Add FW Network Rules

az network firewall network-rule create -g $RG -f $FWNAME --collection-name 'aksfwnr' -n 'apiudp' --protocols 'UDP' --source-addresses '*' --destination-addresses "AzureCloud.$LOC" --destination-ports 1194 --action allow --priority 100
az network firewall network-rule create -g $RG -f $FWNAME --collection-name 'aksfwnr' -n 'apitcp' --protocols 'TCP' --source-addresses '*' --destination-addresses "AzureCloud.$LOC" --destination-ports 9000
az network firewall network-rule create -g $RG -f $FWNAME --collection-name 'aksfwnr' -n 'time' --protocols 'UDP' --source-addresses '*' --destination-fqdns 'ntp.ubuntu.com' --destination-ports 123

# Add FW Application Rules

az network firewall application-rule create -g $RG -f $FWNAME --collection-name 'aksfwar' -n 'fqdn' --source-addresses '*' --protocols 'http=80' 'https=443' --fqdn-tags "AzureKubernetesService" --action allow --priority 100
```

Zobacz [Azure Firewall dokumentacji,](../firewall/overview.md) aby dowiedzieć się więcej o Azure Firewall usługi.

### <a name="associate-the-route-table-to-aks"></a>Kojarzenie tabeli tras z platformą AKS

Aby skojarzyć klaster z zaporą, dedykowana podsieć dla podsieci klastra musi odwoływać się do utworzonej powyżej tabeli tras. Skojarzenie można wykonać, wydając polecenie do sieci wirtualnej, w których jest zarówno klaster, jak i zapora, w celu zaktualizowania tabeli tras podsieci klastra.

```azurecli
# Associate route table with next hop to Firewall to the AKS subnet

az network vnet subnet update -g $RG --vnet-name $VNET_NAME --name $AKSSUBNET_NAME --route-table $FWROUTE_TABLE_NAME
```

### <a name="deploy-aks-with-outbound-type-of-udr-to-the-existing-network"></a>Wdrażanie usługi AKS z typem ruchu wychodzącego UDR do istniejącej sieci

Teraz klaster usługi AKS można wdrożyć w istniejącej sieci wirtualnej. Użyjemy również typu ruchu wychodzącego . Ta funkcja gwarantuje, że dowolny ruch wychodzący będzie wymuszany przez zaporę i nie będą istnieć żadne inne ścieżki ruchu wychodzącego (domyślnie można użyć typu ruchu wychodzącego Load Balancer wychodzącego). [ `userDefinedRouting` ](egress-outboundtype.md)

![aks-deploy](media/limit-egress-traffic/aks-udr-fw.png)

### <a name="create-a-service-principal-with-access-to-provision-inside-the-existing-virtual-network"></a>Tworzenie jednostki usługi z dostępem do aprowizowania w istniejącej sieci wirtualnej

Tożsamość klastra (tożsamość zarządzana lub jednostka usługi) jest używana przez usługę AKS do tworzenia zasobów klastra. Jednostkę usługi przekazywaną w czasie tworzenia używa się do tworzenia podstawowych zasobów usługi AKS, takich jak zasoby magazynu, ip i usługi Load Balancer używane przez usługę AKS (w zamian można również użyć tożsamości [zarządzanej).](use-managed-identity.md) Jeśli poniższe uprawnienia nie zostaną przyznane, nie będzie można aprowizować klastra usługi AKS.

```azurecli
# Create SP and Assign Permission to Virtual Network

az ad sp create-for-rbac -n "${PREFIX}sp" --skip-assignment
```

Teraz zastąp wartości `APPID` i poniżej wartością appid jednostki usługi i hasłem jednostki usługi wygenerowanym automatycznie `PASSWORD` przez dane wyjściowe poprzedniego polecenia. Będziemy odwoływać się do identyfikatora zasobu sieci wirtualnej, aby udzielić uprawnień do jednostki usługi, aby usługa AKS mógł wdrożyć w nim zasoby.

```azurecli
APPID="<SERVICE_PRINCIPAL_APPID_GOES_HERE>"
PASSWORD="<SERVICEPRINCIPAL_PASSWORD_GOES_HERE>"
VNETID=$(az network vnet show -g $RG --name $VNET_NAME --query id -o tsv)

# Assign SP Permission to VNET

az role assignment create --assignee $APPID --scope $VNETID --role "Network Contributor"
```

Szczegółowe uprawnienia, które są wymagane, możesz sprawdzić [tutaj.](kubernetes-service-principal.md#delegate-access-to-other-azure-resources)

> [!NOTE]
> Jeśli używasz wtyczki kubenet network, musisz nadać jednostki usługi AKS lub tożsamości zarządzanej uprawnienia do wstępnie utworzonej tabeli tras, ponieważ platforma kubenet wymaga tabeli tras w celu dodania niezbędnych reguł routingu. 
> ```azurecli-interactive
> RTID=$(az network route-table show -g $RG -n $FWROUTE_TABLE_NAME --query id -o tsv)
> az role assignment create --assignee $APPID --scope $RTID --role "Network Contributor"
> ```

### <a name="deploy-aks"></a>Wdrażanie usługi AKS

Na koniec klaster usługi AKS można wdrożyć w istniejącej podsieci dedykowanej dla klastra. Docelowa podsieć, w która ma zostać wdrożona, jest definiowana za pomocą zmiennej środowiskowej `$SUBNETID` . W poprzednich `$SUBNETID` krokach nie zdefiniliśmy zmiennej . Aby ustawić wartość identyfikatora podsieci, możesz użyć następującego polecenia:

```azurecli
SUBNETID=$(az network vnet subnet show -g $RG --vnet-name $VNET_NAME --name $AKSSUBNET_NAME --query id -o tsv)
```

Zdefiniujesz typ ruchu wychodzącego, aby używać sieci UDR, która już istnieje w podsieci. Ta konfiguracja umożliwi u usługom AKS pomijanie konfiguracji i aprowizowania adresów IP dla usługi równoważenia obciążenia.

> [!IMPORTANT]
> Aby uzyskać więcej informacji na temat ruchu wychodzącego typu UDR, w tym ograniczeń, zobacz wychodzącego typu [**UDR.**](egress-outboundtype.md#limitations)


> [!TIP]
> Do wdrożenia klastra można dodać dodatkowe funkcje, takie jak [**klaster prywatny.**](private-clusters.md) 
>
> Funkcję AKS dla autoryzowanych zakresów adresów IP serwera [**interfejsu API**](api-server-authorized-ip-ranges.md) można dodać, aby ograniczyć dostęp serwera interfejsu API tylko do publicznego punktu końcowego zapory. Funkcja autoryzowanych zakresów adresów IP jest oznaczona na diagramie jako opcjonalna. Podczas włączania funkcji autoryzowanego zakresu adresów IP w celu ograniczenia dostępu do serwera interfejsu API narzędzia deweloperskie muszą używać serwera przeskoku z sieci wirtualnej zapory lub dodać wszystkie punkty końcowe deweloperów do autoryzowanego zakresu adresów IP.

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

### <a name="enable-developer-access-to-the-api-server"></a>Włączanie dostępu dewelopera do serwera interfejsu API

Jeśli w poprzednim kroku zostały użyte autoryzowane zakresy adresów IP dla klastra, musisz dodać adresy IP narzędzi dewelopera do listy zatwierdzonych zakresów adresów IP klastra usługi AKS, aby uzyskać dostęp do serwera interfejsu API z tego serwera. Inną opcją jest skonfigurowanie serwera przeskoku z wymaganymi narzędziami w oddzielnej podsieci w sieci wirtualnej zapory.

Dodaj kolejny adres IP do zatwierdzonych zakresów za pomocą następującego polecenia

```bash
# Retrieve your IP address
CURRENT_IP=$(dig @resolver1.opendns.com ANY myip.opendns.com +short)

# Add to AKS approved list
az aks update -g $RG -n $AKSNAME --api-server-authorized-ip-ranges $CURRENT_IP/32

```

 Użyj polecenia [az aks get-credentials][az-aks-get-credentials], aby skonfigurować połączenie z nowo utworzonym `kubectl` klastrem Kubernetes. 

 ```azurecli
 az aks get-credentials -g $RG -n $AKSNAME
 ```

### <a name="deploy-a-public-service"></a>Wdrażanie usługi publicznej
Teraz możesz rozpocząć udostępnianie usług i wdrażanie aplikacji w tym klastrze. W tym przykładzie uwidaczniamy usługę publiczną, ale możesz również wybrać uwidocznić usługę wewnętrzną za pośrednictwem [wewnętrznego równoważenia obciążenia.](internal-lb.md)

![DnaT usługi publicznej](media/limit-egress-traffic/aks-create-svc.png)

Wdróż aplikację do głosowania platformy Azure, kopiując poniższy plik YAML do pliku o nazwie `example.yaml` .

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

Wd wdrażaj usługę, uruchamiając:

```bash
kubectl apply -f example.yaml
```

### <a name="add-a-dnat-rule-to-azure-firewall"></a>Dodawanie reguły DNAT do Azure Firewall

> [!IMPORTANT]
> Jeśli używasz usługi Azure Firewall do ograniczania ruchu wychodzącego i tworzysz trasę zdefiniowaną przez użytkownika w celu wymusenia całego ruchu wychodzącego, upewnij się, że w zaporze została tworzymy odpowiednią regułę DNAT, aby prawidłowo zezwalać na ruch przychodzący. Użycie Azure Firewall z trasą UDR przerywa konfigurację ruchu przychodzących z powodu routingu asymetrycznego. (Ten problem występuje, gdy podsieć usługi AKS ma trasę domyślną, która przechodzi do prywatnego adresu IP zapory, ale używasz publicznego równoważenia obciążenia — ruchu przychodzących lub usługi Kubernetes typu: LoadBalancer). W takim przypadku przychodzący ruch usługi równoważenia obciążenia jest odbierany za pośrednictwem jego publicznego adresu IP, ale ścieżka powrotna przechodzi przez prywatny adres IP zapory. Ponieważ zapora jest stanowa, porzuca zwracany pakiet, ponieważ zapora nie wie o ustanowionej sesji. Aby dowiedzieć się, jak zintegrować usługę Azure Firewall z przychodzącym lub usługowym równoważeniem obciążenia, zobacz Integrate Azure Firewall with Azure usługa Load Balancer w warstwie Standardowa (Integrowanie usługi azure [Azure Firewall z usługą Azure usługa Load Balancer w warstwie Standardowa).](../firewall/integrate-lb.md)


Aby skonfigurować łączność ruchu przychodzącego, reguła DNAT musi być zapisywana w Azure Firewall. Aby przetestować łączność z klastrem, zdefiniowano regułę dla publicznego adresu IP frontonu zapory w celu przekierowania do wewnętrznego adresu IP udostępnianego przez usługę wewnętrzną.

Adres docelowy można dostosować, ponieważ jest to port zapory, do którego ma być uzyskiwany dostęp. Przetłumaczony adres musi być adresem IP wewnętrznego równoważenia obciążenia. Przetłumaczony port musi być ujawnionym portem usługi Kubernetes.

Musisz określić wewnętrzny adres IP przypisany do usługi równoważenia obciążenia utworzonej przez usługę Kubernetes. Pobierz adres, uruchamiając:

```bash
kubectl get services
```

Wymagany adres IP będzie wymieniony w kolumnie EXTERNAL-IP, podobnie jak poniżej.

```bash
NAME               TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
kubernetes         ClusterIP      10.41.0.1       <none>        443/TCP        10h
voting-analytics   ClusterIP      10.41.88.129    <none>        8080/TCP       9m
voting-app         LoadBalancer   10.41.185.82    20.39.18.6    80:32718/TCP   9m
voting-storage     ClusterIP      10.41.221.201   <none>        3306/TCP       9m
```

Uzyskaj adres IP usługi, uruchamiając:
```bash
SERVICE_IP=$(kubectl get svc voting-app -o jsonpath='{.status.loadBalancer.ingress[*].ip}')
```

Dodaj regułę NAT, uruchamiając:
```azurecli
az network firewall nat-rule create --collection-name exampleset --destination-addresses $FWPUBLIC_IP --destination-ports 80 --firewall-name $FWNAME --name inboundrule --protocols Any --resource-group $RG --source-addresses '*' --translated-port 80 --action Dnat --priority 100 --translated-address $SERVICE_IP
```

### <a name="validate-connectivity"></a>Weryfikowanie łączności

Przejdź do adresu IP Azure Firewall frontonie w przeglądarce, aby zweryfikować łączność.

Powinna zostać wyświetlony aplikacja do głosowania usługi AKS. W tym przykładzie publicznym adresem IP zapory był `52.253.228.132` .


![Zrzut ekranu przedstawia aplikację do głosowania A K S z przyciskami Cats, Dogs, Reset i totals.](media/limit-egress-traffic/aks-vote.png)


### <a name="clean-up-resources"></a>Czyszczenie zasobów

Aby wyczyścić zasoby platformy Azure, usuń grupę zasobów usługi AKS.

```azurecli
az group delete -g $RG
```

## <a name="next-steps"></a>Następne kroki

W tym artykule o tym, jakie porty i adresy należy zezwolić, jeśli chcesz ograniczyć ruch wychodzący dla klastra. Opisano również sposób zabezpieczania ruchu wychodzącego przy użyciu Azure Firewall. 

W razie potrzeby możesz uogólnić powyższe kroki w celu przekazywania ruchu do preferowanego rozwiązania ruchu wychodzącego, zgodnie z dokumentacją typu [ruchu `userDefinedRoute` wychodzącego](egress-outboundtype.md).

Jeśli chcesz ograniczyć sposób, w jaki zasobniki komunikują się między sobą East-West ograniczenia ruchu sieciowego w klastrze, zobacz Bezpieczny ruch między zasobnikami przy użyciu zasad sieciowych W [UKS.][network-policy]

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[network-policy]: use-network-policies.md
[azure-firewall]: ../firewall/overview.md
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[aks-upgrade]: upgrade-cluster.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[dev-spaces-service-tags]: ../dev-spaces/configure-networking.md#virtual-network-or-subnet-configurations
