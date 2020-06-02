---
title: Ogranicz ruch wychodzący w usłudze Azure Kubernetes Service (AKS)
description: Dowiedz się, jakie porty i adresy są wymagane do sterowania ruchem wychodzącym w usłudze Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 03/10/2020
ms.openlocfilehash: 724d270b5ea18dbbd30ff2587e8bea5ee126a9ec
ms.sourcegitcommit: 309cf6876d906425a0d6f72deceb9ecd231d387c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2020
ms.locfileid: "84264413"
---
# <a name="control-egress-traffic-for-cluster-nodes-in-azure-kubernetes-service-aks"></a>Sterowanie ruchem wychodzącym węzłów klastra w usłudze Azure Kubernetes Service (AKS)

Domyślnie klastry AKS mają nieograniczony dostęp do Internetu (ruch wychodzący). Ten poziom dostępu do sieci umożliwia uruchamianie węzłów i usług w celu uzyskania dostępu do zasobów zewnętrznych w razie potrzeby. Aby ograniczyć ruch wychodzący, należy uzyskać dostęp do nieograniczonej liczby portów i adresów, aby zachować prawidłowe zadania konserwacji klastra. Klaster jest domyślnie skonfigurowany do używania obrazów kontenera systemu podstawowego z programu Microsoft Container Registry (MCR) lub Azure Container Registry (ACR). Skonfiguruj preferowane reguły zapory i zabezpieczeń w taki sposób, aby zezwalały na te wymagane porty i adresy.

W tym artykule opisano, które porty sieciowe i w pełni kwalifikowane nazwy domen (FQDN) są wymagane i opcjonalne w przypadku ograniczenia ruchu wychodzącego w klastrze AKS.

> [!IMPORTANT]
> W tym dokumencie opisano tylko sposób blokowania ruchu wychodzącego z podsieci AKS. AKS nie ma wymagań dotyczących transferu danych przychodzących.  Blokowanie ruchu podsieci wewnętrznej przy użyciu sieciowych grup zabezpieczeń (sieciowych grup zabezpieczeń) i zapór nie jest obsługiwane. Aby kontrolować i blokować ruch w klastrze, użyj [zasad sieciowych][network-policy].

## <a name="before-you-begin"></a>Przed rozpoczęciem

Wymagany jest interfejs wiersza polecenia platformy Azure w wersji 2.0.66 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

## <a name="egress-traffic-overview"></a>Ruch wychodzący — Omówienie

W celach zarządzania i operacyjnych węzły w klastrze AKS muszą uzyskiwać dostęp do określonych portów i w pełni kwalifikowanych nazw domen (FQDN). Te akcje mogą być komunikowane się z serwerem interfejsu API lub do pobrania, a następnie zainstalowania podstawowych składników klastra Kubernetes i aktualizacji zabezpieczeń węzła. Domyślnie ruch internetowy (wychodzący) nie jest ograniczony dla węzłów w klastrze AKS. Klaster może pobierać podstawowe obrazy kontenerów systemu z zewnętrznych repozytoriów.

Aby zwiększyć bezpieczeństwo klastra AKS, możesz ograniczyć ruch wychodzący. Klaster jest skonfigurowany do ściągania obrazów kontenera systemu podstawowego z MCR lub ACR. W przypadku zablokowania ruchu wychodzącego w ten sposób Zdefiniuj określone porty i nazwy FQDN, aby umożliwić węzłom AKS poprawne komunikowanie się z wymaganymi usługami zewnętrznymi. Bez tych autoryzowanych portów i nazw FQDN węzły AKS nie mogą komunikować się z serwerem interfejsu API ani instalować składników podstawowych.

Do zabezpieczenia ruchu wychodzącego i definiowania wymaganych portów i adresów można użyć [zapory platformy Azure][azure-firewall] lub urządzenia zapory innej firmy. Usługa AKS nie tworzy automatycznie tych reguł. Następujące porty i adresy są przeznaczone do celów referencyjnych podczas tworzenia odpowiednich reguł w zaporze sieciowej.

> [!IMPORTANT]
> W przypadku korzystania z zapory platformy Azure w celu ograniczenia ruchu wychodzącego i utworzenia trasy zdefiniowanej przez użytkownika (UDR) w celu wymuszenia całego ruchu wychodzącego upewnij się, że utworzono odpowiednią regułę DNAT w zaporze, aby prawidłowo zezwolić na ruch przychodzący. Używanie zapory platformy Azure z UDR powoduje przerwanie konfiguracji transferu danych przychodzących z powodu routingu asymetrycznego. (Problem występuje, jeśli podsieć AKS ma trasę domyślną, która przechodzi do prywatnego adresu IP zapory, ale używasz publicznego modułu równoważenia obciążenia — ruchu przychodzącego lub usługi Kubernetes typu: moduł równoważenia danych). W takim przypadku ruch przychodzącego modułu równoważenia obciążenia jest odbierany za pośrednictwem jego publicznego adresu IP, ale ścieżka zwrotna przechodzi przez prywatny adres IP zapory. Ze względu na to, że Zapora jest stanowa, opuszcza pakiet, ponieważ Zapora nie rozpoznaje ustanowionej sesji. Aby dowiedzieć się, jak zintegrować zaporę platformy Azure z ruchem przychodzącym lub usługą równoważenia obciążenia, zobacz [integrowanie zapory platformy Azure z usługą azure usługa Load Balancer w warstwie Standardowa](https://docs.microsoft.com/azure/firewall/integrate-lb).
> Można zablokować ruch dla portu TCP 9000, port TCP 22 i port UDP 1194 przy użyciu reguły sieci między adresami IP węzła procesu roboczego i adresem IP dla serwera interfejsu API.

W programie AKS istnieją dwa zestawy portów i adresów:

* [Wymagane porty i adresy dla klastrów AKS](#required-ports-and-addresses-for-aks-clusters) zawierają szczegóły dotyczące minimalnych wymagań dla autoryzowanego ruchu wychodzącego.
* [Opcjonalne zalecane adresy i porty klastrów AKS](#optional-recommended-addresses-and-ports-for-aks-clusters) nie są wymagane dla wszystkich scenariuszy, ale integracja z innymi usługami, takimi jak Azure monitor nie działa prawidłowo. Przejrzyj tę listę opcjonalnych portów i nazw FQDN oraz Autoryzuj wszystkie usługi i składniki używane w klastrze AKS.

> [!NOTE]
> Ograniczanie ruchu wychodzącego działa tylko w przypadku nowych klastrów AKS. W przypadku istniejących klastrów należy [wykonać operację uaktualniania klastra][aks-upgrade] przy użyciu `az aks upgrade` polecenia przed ograniczeniem ruchu wychodzącego.

## <a name="required-ports-and-addresses-for-aks-clusters"></a>Wymagane porty i adresy dla klastrów usługi AKS

Następujące porty wychodzące/reguły sieciowe są wymagane dla klastra AKS:

* Port TCP *443*
* TCP [IPAddrOfYourAPIServer]: 443 jest wymagane, jeśli masz aplikację, która musi komunikować się z serwerem interfejsu API.  Tę zmianę można ustawić po utworzeniu klastra.
* Port TCP *9000*, port TCP *22* i port UDP *1194* dla frontonu tunelu w celu komunikowania się z końcem tunelu na serwerze interfejsu API.
    * Aby uzyskać bardziej szczegółowe informacje, zobacz **. HCP. \<location\> .. azmk8s.io* i **. TUN. \<location\> . azmk8s.io* adresy w poniższej tabeli.
* Port UDP *123* dla synchronizacji czasu protokołu NTP (Network Time Protocol) (węzły systemu Linux).
* Port UDP *53* dla systemu DNS jest wymagany również w przypadku bezpośredniego dostępu do serwera interfejsu API.

Wymagane są następujące reguły dotyczące nazwy FQDN/aplikacji:

> [!IMPORTANT]
> ***. blob.Core.Windows.NET i aksrepos.azurecr.IO** nie są już wymaganymi regułami FQDN dla blokady ruchu wychodzącego.  W przypadku istniejących klastrów [Wykonaj operację uaktualniania klastra][aks-upgrade] przy użyciu `az aks upgrade` polecenia, aby usunąć te reguły.

> [!IMPORTANT]
> *. cdn.mscr.io został zastąpiony przez *. data.mcr.microsoft.com dla regionów chmury publicznej platformy Azure. Uaktualnij istniejące reguły zapory, aby zmiany zaczęły obowiązywać.

- Globalne platformy Azure

| Nazwa FQDN                       | Port      | Użycie      |
|----------------------------|-----------|----------|
| *. HCP. \<location\> . azmk8s.io | HTTPS: 443, TCP: 22, TCP: 9000, UDP: 1194 | Ten adres jest wymagany do komunikacji z serwerem interfejsu API < węzła >. Zamień na *\<location\>* region, w którym wdrożono klaster AKS. |
| *. TUN. \<location\> . azmk8s.io | HTTPS: 443, TCP: 22, TCP: 9000, UDP: 1194 | Ten adres jest wymagany do komunikacji z serwerem interfejsu API < węzła >. Zamień na *\<location\>* region, w którym wdrożono klaster AKS. |
| *. cdn.mscr.io       | HTTPS: 443 | Ten adres jest wymagany dla magazynu MCR obsługiwanego przez usługę Azure Content Delivery Network (CDN). |
| mcr.microsoft.com          | HTTPS: 443 | Ten adres jest wymagany w celu uzyskania dostępu do obrazów w programie Microsoft Container Registry (MCR). Ten Rejestr zawiera obrazy i wykresy pierwszej strony (na przykład Moby itp.) wymagane do działania klastra podczas uaktualniania i skalowania klastra |
| *. data.mcr.microsoft.com             | HTTPS: 443 | Ten adres jest wymagany dla magazynu MCR obsługiwanego przez usługę Azure Content Delivery Network (CDN). |
| management.azure.com       | HTTPS: 443 | Ten adres jest wymagany do Kubernetes operacji pobierania/UMIESZCZAnia. |
| login.microsoftonline.com  | HTTPS: 443 | Ten adres jest wymagany na potrzeby uwierzytelniania Azure Active Directory. |
| ntp.ubuntu.com             | UDP: 123   | Ten adres jest wymagany w przypadku synchronizacji czasu NTP w węzłach systemu Linux. |
| packages.microsoft.com     | HTTPS: 443 | Ten adres jest repozytorium pakietów firmy Microsoft używanym do buforowanych operacji *apt-get* .  Przykładowe pakiety to Moby, PowerShell i interfejs wiersza polecenia platformy Azure. |
| acs-mirror.azureedge.net      | HTTPS: 443 | Ten adres dotyczy repozytorium wymaganego do instalacji wymaganych plików binarnych, takich jak korzystającą wtyczki kubenet i Azure CNI. |

- Azure w Chinach — 21Vianet

| Nazwa FQDN                       | Port      | Użycie      |
|----------------------------|-----------|----------|
| *. HCP. \<location\> . cx.prod.service.azk8s.cn | HTTPS: 443, TCP: 22, TCP: 9000, UDP: 1194 | Ten adres jest wymagany do komunikacji z serwerem interfejsu API < węzła >. Zamień na *\<location\>* region, w którym wdrożono klaster AKS. |
| *. TUN. \<location\> . cx.prod.service.azk8s.cn | HTTPS: 443, TCP: 22, TCP: 9000, UDP: 1194 | Ten adres jest wymagany do komunikacji z serwerem interfejsu API < węzła >. Zamień na *\<location\>* region, w którym wdrożono klaster AKS. |
| *. azk8s.cn        | HTTPS: 443 | Ten adres jest wymagany do pobrania wymaganych plików binarnych i obrazów|
| mcr.microsoft.com          | HTTPS: 443 | Ten adres jest wymagany w celu uzyskania dostępu do obrazów w programie Microsoft Container Registry (MCR). Ten Rejestr zawiera obrazy i wykresy pierwszej strony (na przykład Moby itp.) wymagane do działania klastra podczas uaktualniania i skalowania klastra |
| *. cdn.mscr.io       | HTTPS: 443 | Ten adres jest wymagany dla magazynu MCR obsługiwanego przez usługę Azure Content Delivery Network (CDN). |
| *. data.mcr.microsoft.com             | HTTPS: 443 | Ten adres jest wymagany dla magazynu MCR obsługiwanego przez usługę Azure Content Delivery Network (CDN). |
| management.chinacloudapi.cn       | HTTPS: 443 | Ten adres jest wymagany do Kubernetes operacji pobierania/UMIESZCZAnia. |
| login.chinacloudapi.cn  | HTTPS: 443 | Ten adres jest wymagany na potrzeby uwierzytelniania Azure Active Directory. |
| ntp.ubuntu.com             | UDP: 123   | Ten adres jest wymagany w przypadku synchronizacji czasu NTP w węzłach systemu Linux. |
| packages.microsoft.com     | HTTPS: 443 | Ten adres jest repozytorium pakietów firmy Microsoft używanym do buforowanych operacji *apt-get* .  Przykładowe pakiety to Moby, PowerShell i interfejs wiersza polecenia platformy Azure. |

- Azure Government

| Nazwa FQDN                       | Port      | Użycie      |
|----------------------------|-----------|----------|
| *. HCP. \<location\> . cx.aks.containerservice.azure.us | HTTPS: 443, TCP: 22, TCP: 9000, UDP: 1194 | Ten adres jest wymagany do komunikacji z serwerem interfejsu API < węzła >. Zamień na *\<location\>* region, w którym wdrożono klaster AKS. |
| *. TUN. \<location\> . cx.aks.containerservice.azure.us | HTTPS: 443, TCP: 22, TCP: 9000, UDP: 1194 | Ten adres jest wymagany do komunikacji z serwerem interfejsu API < węzła >. Zamień na *\<location\>* region, w którym wdrożono klaster AKS. |
| mcr.microsoft.com          | HTTPS: 443 | Ten adres jest wymagany w celu uzyskania dostępu do obrazów w programie Microsoft Container Registry (MCR). Ten Rejestr zawiera obrazy i wykresy pierwszej strony (na przykład Moby itp.) wymagane do działania klastra podczas uaktualniania i skalowania klastra |
|*. cdn.mscr.io              | HTTPS: 443 | Ten adres jest wymagany dla magazynu MCR obsługiwanego przez usługę Azure Content Delivery Network (CDN). |
| *. data.mcr.microsoft.com             | HTTPS: 443 | Ten adres jest wymagany dla magazynu MCR obsługiwanego przez usługę Azure Content Delivery Network (CDN). |
| management.usgovcloudapi.net       | HTTPS: 443 | Ten adres jest wymagany do Kubernetes operacji pobierania/UMIESZCZAnia. |
| login.microsoftonline.us  | HTTPS: 443 | Ten adres jest wymagany na potrzeby uwierzytelniania Azure Active Directory. |
| ntp.ubuntu.com             | UDP: 123   | Ten adres jest wymagany w przypadku synchronizacji czasu NTP w węzłach systemu Linux. |
| packages.microsoft.com     | HTTPS: 443 | Ten adres jest repozytorium pakietów firmy Microsoft używanym do buforowanych operacji *apt-get* .  Przykładowe pakiety to Moby, PowerShell i interfejs wiersza polecenia platformy Azure. |
| acs-mirror.azureedge.net      | HTTPS: 443 | Ten adres dotyczy repozytorium wymaganego do instalacji wymaganych plików binarnych, takich jak korzystającą wtyczki kubenet i Azure CNI. |

## <a name="optional-recommended-addresses-and-ports-for-aks-clusters"></a>Opcjonalne zalecane adresy i porty dla klastrów AKS

Następujące porty wychodzące/reguły sieciowe są opcjonalne dla klastra AKS:

Następujące reguły dotyczące nazwy FQDN/aplikacji są zalecane, aby klastry AKS działały prawidłowo:

| Nazwa FQDN                                    | Port      | Użycie      |
|-----------------------------------------|-----------|----------|
| security.ubuntu.com, azure.archive.ubuntu.com, changelogs.ubuntu.com | HTTP: 80   | Ten adres umożliwia pobranie przez węzły klastra systemu Linux wymaganych poprawek i aktualizacji zabezpieczeń. |

## <a name="required-addresses-and-ports-for-gpu-enabled-aks-clusters"></a>Wymagane adresy i porty dla klastrów AKS z obsługą procesora GPU

Następujące reguły dotyczące nazwy FQDN/aplikacji są wymagane dla klastrów AKS z włączonym procesorem GPU:

| Nazwa FQDN                                    | Port      | Użycie      |
|-----------------------------------------|-----------|----------|
| nvidia.github.io | HTTPS: 443 | Ten adres jest używany do poprawnego instalowania i działania sterowników w węzłach opartych na procesorze GPU. |
| us.download.nvidia.com | HTTPS: 443 | Ten adres jest używany do poprawnego instalowania i działania sterowników w węzłach opartych na procesorze GPU. |
| apt.dockerproject.org | HTTPS: 443 | Ten adres jest używany do poprawnego instalowania i działania sterowników w węzłach opartych na procesorze GPU. |

## <a name="required-addresses-and-ports-with-azure-monitor-for-containers-enabled"></a>Wymagane adresy i porty z włączonym Azure Monitor dla kontenerów

Następujące reguły dotyczące nazwy FQDN/aplikacji są wymagane dla klastrów AKS, dla Azure Monitor których włączono obsługę kontenerów:

| Nazwa FQDN                                    | Port      | Użycie      |
|-----------------------------------------|-----------|----------|
| dc.services.visualstudio.com | HTTPS: 443    | Jest to poprawne metryki i monitorowanie danych telemetrycznych przy użyciu Azure Monitor. |
| *.ods.opinsights.azure.com    | HTTPS: 443    | Jest on używany przez Azure Monitor do pozyskiwania danych usługi log Analytics. |
| *.oms.opinsights.azure.com | HTTPS: 443 | Ten adres jest używany przez omsagent, który jest używany do uwierzytelniania usługi log Analytics. |
|*.microsoftonline.com | HTTPS: 443 | Jest on używany do uwierzytelniania i wysyłania metryk do Azure Monitor. |
|*. monitoring.azure.com | HTTPS: 443 | Służy do wysyłania danych metryk do Azure Monitor. |

## <a name="required-addresses-and-ports-with-azure-dev-spaces-enabled"></a>Wymagane adresy i porty z włączonym Azure Dev Spaces

Następujące reguły dotyczące nazwy FQDN/aplikacji są wymagane dla klastrów AKS z włączonym Azure Dev Spaces:

| Nazwa FQDN                                    | Port      | Użycie      |
|-----------------------------------------|-----------|----------|
| cloudflare.docker.com | HTTPS: 443 | Ten adres służy do ściągania obrazów z systemem Linux Alpine i innych Azure Dev Spaces |
| gcr.io | HTTPS: 443 | Ten adres służy do ściągania obrazów Helm/do odczekania |
| storage.googleapis.com | HTTPS: 443 | Ten adres służy do ściągania obrazów Helm/do odczekania |

Zaktualizuj zaporę lub konfigurację zabezpieczeń, aby zezwalać na ruch sieciowy do i z wszystkich powyższych nazw FQDN i [usług Azure dev Spaces infrastruktury][dev-spaces-service-tags].

## <a name="required-addresses-and-ports-for-aks-clusters-with-azure-policy-in-public-preview-enabled"></a>Wymagane adresy i porty dla klastrów AKS z włączonym Azure Policy (w publicznej wersji zapoznawczej)

> [!CAUTION]
> Niektóre z tych funkcji znajdują się w wersji zapoznawczej.  Sugestie przedstawione w tym artykule mogą ulec zmianie, gdy ta funkcja zostanie przeniesiona do publicznej wersji zapoznawczej i przyszłych etapów wydania.

Następujące reguły dotyczące nazwy FQDN/aplikacji są wymagane dla klastrów AKS z włączonym Azure Policy.

| Nazwa FQDN                                    | Port      | Użycie      |
|-----------------------------------------|-----------|----------|
| gov-prod-policy-data.trafficmanager.net | HTTPS: 443 | Ten adres jest używany do poprawnego działania Azure Policy. (obecnie w wersji zapoznawczej w AKS) |
| raw.githubusercontent.com | HTTPS: 443 | Ten adres służy do ściągania wbudowanych zasad z usługi GitHub w celu zapewnienia prawidłowego działania Azure Policy. (obecnie w wersji zapoznawczej w AKS) |
| *. gk. \<location\> . azmk8s.io | HTTPS: 443    | Azure Policy dodatek, który komunikuje się ze Strażnikiem punktu końcowego inspekcji działającego na serwerze głównym, aby uzyskać wyniki inspekcji. |
| dc.services.visualstudio.com | HTTPS: 443 | Azure Policy dodatek, który wysyła dane telemetryczne do punktu końcowego usługi Application Insights. |

## <a name="required-by-windows-server-based-nodes-enabled"></a>Wymagane przez węzły oparte na systemie Windows Server włączone

Następujące reguły dotyczące nazwy FQDN/aplikacji są wymagane do korzystania z pul węzłów opartych na systemie Windows Server:

| Nazwa FQDN                                    | Port      | Użycie      |
|-----------------------------------------|-----------|----------|
| onegetcdn.azureedge.net, winlayers.blob.core.windows.net, winlayers.cdn.mscr.io, go.microsoft.com | HTTPS: 443 | Aby zainstalować pliki binarne powiązane z systemem Windows |
| mp.microsoft.com, www <span></span> . msftconnecttest.com, ctldl.windowsupdate.com | HTTP: 80 | Aby zainstalować pliki binarne powiązane z systemem Windows |
| kms.core.windows.net | TCP: 1688 | Aby zainstalować pliki binarne powiązane z systemem Windows |

## <a name="next-steps"></a>Następne kroki

Ten artykuł zawiera informacje o portach i adresach, które mają być dozwolone w przypadku ograniczenia ruchu wychodzącego dla klastra. Można również określić, jak same z nich mogą komunikować się i jakie ograniczenia mają w klastrze. Aby uzyskać więcej informacji, zobacz [bezpieczny ruch między różnymi elementami sieciowymi przy użyciu zasad sieciowych w AKS][network-policy].

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
