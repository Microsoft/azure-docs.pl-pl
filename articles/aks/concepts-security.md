---
title: Koncepcje — zabezpieczenia w usłudze Azure Kubernetes Services (AKS)
description: Dowiedz się więcej o zabezpieczeniach w usłudze Azure Kubernetes Service (AKS), w tym o komunikacji głównej i węzłach, zasadach sieciowych i wpisach tajnych Kubernetes.
services: container-service
author: mlearned
ms.topic: conceptual
ms.date: 03/11/2021
ms.author: mlearned
ms.openlocfilehash: 3fafbe3f4b1c53f929682f4ca160fb19a5e91918
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105310"
---
# <a name="security-concepts-for-applications-and-clusters-in-azure-kubernetes-service-aks"></a>Pojęcia dotyczące zabezpieczeń aplikacji i klastrów w usłudze Azure Kubernetes Service (AKS)

Zabezpieczenia klastra chronią dane klientów podczas uruchamiania obciążeń aplikacji w usłudze Azure Kubernetes Service (AKS). 

Kubernetes obejmuje składniki zabezpieczeń, takie jak *zasady sieciowe* i wpisy *tajne*. Tymczasem platforma Azure obejmuje składniki, takie jak sieciowe grupy zabezpieczeń i uaktualnienia do zorganizowanych klastrów. AKS łączy następujące składniki zabezpieczeń:
* Przechowuj klaster AKS z najnowszymi aktualizacjami zabezpieczeń systemu operacyjnego i wersjami Kubernetes.
* Zapewnij bezpieczny ruch pod kątem i dostęp do poufnych poświadczeń.

W tym artykule przedstawiono podstawowe koncepcje zabezpieczania aplikacji w programie AKS:

- [Pojęcia dotyczące zabezpieczeń aplikacji i klastrów w usłudze Azure Kubernetes Service (AKS)](#security-concepts-for-applications-and-clusters-in-azure-kubernetes-service-aks)
  - [Zabezpieczenia główne](#master-security)
  - [Zabezpieczenia węzła](#node-security)
    - [Izolacja obliczeniowa](#compute-isolation)
  - [Uaktualnienia klastra](#cluster-upgrades)
    - [Cordon i opróżnianie](#cordon-and-drain)
  - [Bezpieczeństwo sieci](#network-security)
    - [Sieciowe grupy zabezpieczeń platformy Azure](#azure-network-security-groups)
  - [Kubernetes Secret](#kubernetes-secrets)
  - [Następne kroki](#next-steps)

## <a name="master-security"></a>Zabezpieczenia główne

W AKS składniki główne Kubernetes są częścią usługi zarządzanej, zarządzanej i utrzymywanej przez firmę Microsoft. Każdy klaster AKS ma własny, dedykowany główny Kubernetes, który zapewnia serwer interfejsu API, harmonogram itd.

Domyślnie serwer interfejsu API Kubernetes używa publicznego adresu IP i w pełni kwalifikowanej nazwy domeny (FQDN). Dostęp do punktu końcowego serwera interfejsu API można ograniczyć za pomocą [autoryzowanych zakresów adresów IP][authorized-ip-ranges]. Można również utworzyć w pełni [prywatny klaster][private-clusters] , aby ograniczyć dostęp serwera API do sieci wirtualnej.

Dostęp do serwera interfejsu API można kontrolować za pomocą Kubernetes kontroli dostępu opartej na rolach (Kubernetes RBAC) i funkcji RBAC platformy Azure. Aby uzyskać więcej informacji, zobacz [integracja z usługą Azure AD za pomocą AKS][aks-aad].

## <a name="node-security"></a>Zabezpieczenia węzła

AKS węzły są maszynami wirtualnymi platformy Azure, którymi zarządzasz. 
* Węzły systemu Linux uruchamiają zoptymalizowaną dystrybucję Ubuntu przy użyciu `containerd` środowiska uruchomieniowego kontenera lub Moby. 
* W węzłach systemu Windows Server jest uruchomiona zoptymalizowana wersja systemu Windows Server 2019 przy użyciu `containerd` środowiska uruchomieniowego kontenera lub Moby. 

Po utworzeniu lub skalowaniu klastra AKS węzły są automatycznie wdrażane z najnowszymi aktualizacjami i konfiguracjami zabezpieczeń systemu operacyjnego.

> [!NOTE]
> Klastry AKS korzystające z:
> * Pule węzłów Kubernetes w wersji 1,19 i większe użycie `containerd` jako środowisko uruchomieniowe kontenera. 
> * Kubernetes wcześniejsze niż v 1.19 pule węzłów używają [Moby](https://mobyproject.org/) (Docker) jako środowiska uruchomieniowego kontenera.

### <a name="node-security-patches"></a>Poprawki zabezpieczeń węzła

#### <a name="linux-nodes"></a>Węzły systemu Linux
Platforma Azure automatycznie stosuje poprawki zabezpieczeń systemu operacyjnego w węzłach z systemem Linux w porze nocnej. Jeśli aktualizacja zabezpieczeń systemu operacyjnego Linux wymaga ponownego uruchomienia hosta, nie zostanie automatycznie uruchomiony ponownie. Można:
* Ręcznie uruchom ponownie węzły systemu Linux.
* Użyj [Kured][kured], demona ponownego uruchomienia Open Source dla Kubernetes. Kured działa jako [elementu daemonset][aks-daemonsets] i monitoruje każdy węzeł pliku wskazujący, że wymagany jest ponowny rozruch. 

Ponowne uruchomienia są zarządzane przez klaster przy użyciu tego samego [procesu Cordon i opróżniania](#cordon-and-drain) co uaktualnienie klastra.

#### <a name="windows-server-nodes"></a>Węzły systemu Windows Server

W przypadku węzłów systemu Windows Server Windows Update nie jest automatycznie uruchamiane i stosowane są najnowsze aktualizacje. Zaplanuj uaktualnienia puli węzłów systemu Windows Server w klastrze AKS, aby obejść regularne cykle wydania Windows Update i proces weryfikacji. Ten proces uaktualniania tworzy węzły z zainstalowanym najnowszym obrazem systemu Windows Server i poprawkami, a następnie usuwa starsze węzły. Aby uzyskać więcej informacji na temat tego procesu, zobacz [uaktualnianie puli węzłów w AKS][nodepool-upgrade].

### <a name="node-deployment"></a>Wdrażanie węzłów
Węzły są wdrażane w prywatnej podsieci sieci wirtualnej, bez przypisanych publicznych adresów IP. W celu rozwiązywania problemów i zarządzania protokół SSH jest domyślnie włączony i dostępny tylko przy użyciu wewnętrznego adresu IP.

### <a name="node-storage"></a>Magazyn węzłów
Aby zapewnić magazyn, węzły używają usługi Azure Managed Disks. W przypadku większości rozmiarów węzłów maszyny wirtualnej platforma Azure Managed Disks to dyski Premium obsługiwane przez dysków SSD o wysokiej wydajności. Dane przechowywane na dyskach zarządzanych są automatycznie szyfrowane w ramach platformy Azure. Aby zwiększyć nadmiarowość, Managed Disks platformy Azure są bezpiecznie replikowane w centrum danych platformy Azure.

### <a name="hostile-multi-tenant-workloads"></a>Nieszkodliwy dla wielu dzierżawców

Obecnie środowiska Kubernetes nie są bezpieczne dla niebezpiecznym użyciem wielu dzierżawców. Dodatkowe funkcje zabezpieczeń, takie jak *zasady zabezpieczeń* , lub Kubernetes RBAC dla węzłów, efektywnie uniemożliwiają luki w zabezpieczeniach. W celu zapewnienia prawdziwych zabezpieczeń w przypadku nieprzechodnich obciążeń z wieloma dzierżawcami należy jedynie zaufać funkcji hypervisor. Domena zabezpieczeń dla Kubernetes jest cały klaster, a nie pojedynczy węzeł. 

W przypadku tych typów nieszkodliwych obciążeń z wieloma dzierżawcami należy używać klastrów fizycznie izolowanych. Aby uzyskać więcej informacji na temat sposobów izolowania obciążeń, zobacz [najlepsze rozwiązania dotyczące izolacji klastra w AKS][cluster-isolation].

### <a name="compute-isolation"></a>Izolacja obliczeniowa

Ze względu na wymagania dotyczące zgodności lub z przepisami, pewne obciążenia mogą wymagać wysokiego stopnia odizolowania od innych obciążeń klientów. Na potrzeby tych obciążeń platforma Azure udostępnia [izolowane maszyny wirtualne](../virtual-machines/isolation.md) do użycia jako węzły agentów w klastrze AKS. Te maszyny wirtualne są odizolowane od określonego typu sprzętu i przeznaczone dla jednego klienta. 

Podczas tworzenia klastra AKS lub dodawania puli węzłów wybierz [jeden z izolowanych rozmiarów maszyn wirtualnych](../virtual-machines/isolation.md) jako **rozmiar węzła** .

## <a name="cluster-upgrades"></a>Uaktualnienia klastra

Platforma Azure udostępnia narzędzia aranżacji uaktualnienia umożliwiające uaktualnienie klastra AKS i składników, zachowanie bezpieczeństwa i zgodności oraz dostęp do najnowszych funkcji. Ta aranżacja uaktualniania obejmuje zarówno składnik główny Kubernetes, jak i składniki agentów. 

Aby rozpocząć proces uaktualniania, Określ jedną z [wymienionych wersji Kubernetes](supported-kubernetes-versions.md). Następnie platforma Azure bezpiecznie cordons i opróżnia każdy węzeł AKS i uaktualnienia.

### <a name="cordon-and-drain"></a>Cordon i opróżnianie

W trakcie procesu uaktualniania węzły AKS są indywidualnie odizolowywane z klastra, aby uniemożliwić ich zaplanowanie. Węzły są następnie opróżniane i uaktualniane w następujący sposób:

1.  Nowy węzeł jest wdrażany w puli węzłów. 
    * Ten węzeł uruchamia najnowszą wersję obrazu systemu operacyjnego i poprawki.
1. Jeden z istniejących węzłów jest identyfikowany do uaktualnienia. 
1. W określonym węźle są bezpiecznie kończone i planowane planowanie w innych węzłach w puli węzłów.
1. Opróżniony węzeł zostanie usunięty z klastra AKS.
1. Kroki 1-4 są powtarzane, dopóki wszystkie węzły zostaną pomyślnie zastąpione w ramach procesu uaktualniania.

Aby uzyskać więcej informacji, zobacz [Uaktualnianie klastra AKS][aks-upgrade-cluster].

## <a name="network-security"></a>Bezpieczeństwo sieci

W przypadku połączeń i zabezpieczeń z sieciami lokalnymi można wdrożyć klaster AKS w istniejących podsieciach sieci wirtualnej platformy Azure. Te sieci wirtualne łączą się z siecią lokalną przy użyciu sieci VPN typu lokacja-lokacja lub usługi Express Route. Zdefiniuj kontrolery transferu danych przychodzących Kubernetes z prywatnymi wewnętrznymi adresami IP, aby ograniczyć dostęp usług do wewnętrznego połączenia sieciowego.

### <a name="azure-network-security-groups"></a>Sieciowe grupy zabezpieczeń platformy Azure

Do filtrowania przepływu ruchu w sieci wirtualnej na platformie Azure są stosowane reguły sieciowej grupy zabezpieczeń. Te reguły definiują źródłowe i docelowe zakresy adresów IP, porty i protokoły dozwolone lub odrzucające dostęp do zasobów. Tworzone są reguły domyślne, które zezwalają na ruch TLS do serwera interfejsu API Kubernetes. Tworzysz usługi przy użyciu modułów równoważenia obciążenia, mapowania portów lub tras przychodzących. AKS automatycznie modyfikuje sieciową grupę zabezpieczeń dla przepływu ruchu.

Jeśli podasz własną podsieć dla klastra AKS **, nie należy modyfikować sieciowej** grupy zabezpieczeń na poziomie podsieci zarządzanej przez AKS. Zamiast tego należy utworzyć więcej sieciowych grup zabezpieczeń na poziomie podsieci w celu zmodyfikowania przepływu ruchu. Upewnij się, że nie zakłócają one niezbędnego ruchu zarządzania klastrem, takich jak dostęp do modułu równoważenia obciążenia, komunikacja z płaszczyzną [][aks-limit-egress-traffic]kontroli i ruch wychodzący.

### <a name="kubernetes-network-policy"></a>Zasady sieci Kubernetes

Aby ograniczyć ruch sieciowy między zasobnikami w klastrze, AKS oferuje obsługę [zasad sieciowych Kubernetes][network-policy]. Przy użyciu zasad sieciowych można zezwalać na określone ścieżki sieciowe w ramach klastra lub odrzucać je na podstawie obszarów nazw i selektorów etykiet.

## <a name="kubernetes-secrets"></a>Wpisy tajne usługi Kubernetes

*Wpis tajny* Kubernetes umożliwia wprowadzanie poufnych danych do zasobników, takich jak poświadczenia dostępu lub klucze. 
1. Utwórz wpis tajny przy użyciu interfejsu API Kubernetes. 
1. Zdefiniuj element lub wdrożenie i Żądaj określonego klucza tajnego. 
    * Wpisy tajne są dostarczane tylko do węzłów z zaplanowanym terminem, które ich wymagają.
    * Wpis tajny jest przechowywany w *tmpfs*, nie jest zapisywany na dysku. 
1. Po usunięciu ostatniego elementu znajdującego się w węźle wymagającym wpisu tajnego, wpis tajny jest usuwany z tmpfs węzła. 
   * Wpisy tajne są przechowywane w danym obszarze nazw i można do nich uzyskiwać dostęp tylko w obrębie tego samego obszaru nazw.

Używanie wpisów tajnych zmniejsza poufne informacje zdefiniowane w manifeście "YAML" lub "Service". Zamiast tego należy zażądać wpisu tajnego przechowywanego na serwerze interfejsu API Kubernetes jako część manifestu YAML. Takie podejście zapewnia tylko szczególny dostęp do klucza tajnego. 

> [!NOTE]
> Pliki manifestu RAW Secret zawierają dane tajne w formacie base64 (szczegółowe informacje znajdują się w [oficjalnej dokumentacji][secret-risks] ). Traktuj te pliki jako informacje poufne i nigdy nie zatwierdzaj ich do kontroli źródła.

Wpisy tajne Kubernetes są przechowywane w etcd, rozproszonym magazynie klucza i wartości. Magazyn Etcd jest w pełni zarządzany przez AKS, a [dane są szyfrowane w ramach platformy Azure][encryption-atrest]. 

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć Zabezpieczanie klastrów AKS, zobacz [Uaktualnianie klastra AKS][aks-upgrade-cluster].

W przypadku skojarzonych najlepszych rozwiązań należy zapoznać się [z najlepszymi rozwiązaniami dotyczącymi zabezpieczeń i uaktualnień klastra w AKS][operator-best-practices-cluster-security] i [najlepszych rozwiązaniach dotyczących zabezpieczeń pod kątem bezpieczeństwa w AKS][developer-best-practices-pod-security].

Aby uzyskać więcej informacji na temat podstawowych pojęć związanych z Kubernetes i AKS, zobacz:

- [Kubernetes/AKS klastrów i obciążeń][aks-concepts-clusters-workloads]
- [Kubernetes/AKS tożsamość][aks-concepts-identity]
- [Sieci wirtualne Kubernetes/AKS][aks-concepts-network]
- [Magazyn Kubernetes/AKS][aks-concepts-storage]
- [Skala Kubernetes/AKS][aks-concepts-scale]

<!-- LINKS - External -->
[kured]: https://github.com/weaveworks/kured
[kubernetes-network-policies]: https://kubernetes.io/docs/concepts/services-networking/network-policies/
[secret-risks]: https://kubernetes.io/docs/concepts/configuration/secret/#risks
[encryption-atrest]: ../security/fundamentals/encryption-atrest.md

<!-- LINKS - Internal -->
[aks-daemonsets]: concepts-clusters-workloads.md#daemonsets
[aks-upgrade-cluster]: upgrade-cluster.md
[aks-aad]: ./managed-aad.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[aks-limit-egress-traffic]: limit-egress-traffic.md
[cluster-isolation]: operator-best-practices-cluster-isolation.md
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[developer-best-practices-pod-security]:developer-best-practices-pod-security.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[authorized-ip-ranges]: api-server-authorized-ip-ranges.md
[private-clusters]: private-clusters.md
[network-policy]: use-network-policies.md
