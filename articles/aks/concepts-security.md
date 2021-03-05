---
title: Koncepcje — zabezpieczenia w usłudze Azure Kubernetes Services (AKS)
description: Dowiedz się więcej o zabezpieczeniach w usłudze Azure Kubernetes Service (AKS), w tym o komunikacji głównej i węzłach, zasadach sieciowych i wpisach tajnych Kubernetes.
services: container-service
author: mlearned
ms.topic: conceptual
ms.date: 07/01/2020
ms.author: mlearned
ms.openlocfilehash: 6c69e46ea3510476089cd932b1cd1bdf14254021
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102122378"
---
# <a name="security-concepts-for-applications-and-clusters-in-azure-kubernetes-service-aks"></a>Pojęcia dotyczące zabezpieczeń aplikacji i klastrów w usłudze Azure Kubernetes Service (AKS)

Aby chronić dane klienta podczas uruchamiania obciążeń aplikacji w usłudze Azure Kubernetes Service (AKS), zabezpieczenia klastra są kluczem. Kubernetes obejmuje składniki zabezpieczeń, takie jak *zasady sieciowe* i wpisy *tajne*. Następnie platforma Azure dodaje do składników, takich jak sieciowe grupy zabezpieczeń i uaktualnienia do zorganizowanych klastrów. Te składniki zabezpieczeń są łączone w celu zapewnienia, że klaster AKS z najnowszymi aktualizacjami zabezpieczeń systemu operacyjnego i wersjami Kubernetes oraz bezpiecznym ruchem pod kątem i dostępem do poufnych poświadczeń.

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

W programie AKS główne składniki programu Kubernetes są częścią usługi zarządzanej zapewnianej przez firmę Microsoft. Każdy klaster AKS ma własny, dedykowany główny Kubernetes, który zapewnia serwer interfejsu API, harmonogram itd. Ten główny serwer jest zarządzany i konserwowany przez firmę Microsoft.

Domyślnie serwer interfejsu API Kubernetes używa publicznego adresu IP i w pełni kwalifikowanej nazwy domeny (FQDN). Dostęp do punktu końcowego serwera interfejsu API można ograniczyć za pomocą [autoryzowanych zakresów adresów IP][authorized-ip-ranges]. Można również utworzyć w pełni [prywatny klaster][private-clusters] , aby ograniczyć dostęp serwera API do sieci wirtualnej.

Dostęp do serwera interfejsu API można kontrolować za pomocą Kubernetes kontroli dostępu opartej na rolach (Kubernetes RBAC) i funkcji RBAC platformy Azure. Aby uzyskać więcej informacji, zobacz [integracja z usługą Azure AD za pomocą AKS][aks-aad].

## <a name="node-security"></a>Zabezpieczenia węzła

AKS węzły są maszynami wirtualnymi platformy Azure, którymi zarządzasz. Węzły systemu Linux uruchamiają zoptymalizowaną dystrybucję Ubuntu przy użyciu `containerd` środowiska uruchomieniowego kontenera lub Moby. W węzłach systemu Windows Server działa zoptymalizowane wydanie systemu Windows Server 2019, a także używany jest program `containerd` lub środowisko uruchomieniowe kontenera Moby. Po utworzeniu lub skalowaniu klastra AKS węzły są automatycznie wdrażane z najnowszymi aktualizacjami i konfiguracjami zabezpieczeń systemu operacyjnego.

> [!NOTE]
> Klastry AKS korzystające z pul węzłów Kubernetes w wersji 1,19 i większe użycie `containerd` jako środowiska uruchomieniowego kontenera. Klastry AKS korzystające z funkcji Kubernetes starszych niż v 1.19 dla pul węzłów używają [Moby](https://mobyproject.org/) (nadrzędnego Docker) jako środowiska uruchomieniowego kontenera.

Platforma Azure automatycznie stosuje poprawki zabezpieczeń systemu operacyjnego w węzłach z systemem Linux w porze nocnej. Jeśli aktualizacja zabezpieczeń systemu operacyjnego Linux wymaga ponownego uruchomienia hosta, ponowne uruchomienie nie jest wykonywane automatycznie. Można ręcznie ponownie uruchomić węzły systemu Linux lub często używanym podejściem jest użycie [Kured][kured], demona ponownego uruchomienia typu open source dla Kubernetes. Kured działa jako [elementu daemonset][aks-daemonsets] i monitoruje każdy węzeł pod kątem obecności pliku wskazujący, że wymagany jest ponowny rozruch. Ponowne uruchomienia są zarządzane przez klaster przy użyciu tego samego [procesu Cordon i opróżniania](#cordon-and-drain) co uaktualnienie klastra.

W przypadku węzłów systemu Windows Server Windows Update nie jest automatycznie uruchamiane i stosowane są najnowsze aktualizacje. Zgodnie z regularnym harmonogramem Windows Update cyklu wydania i procesu weryfikacji należy przeprowadzić uaktualnienie w puli węzłów systemu Windows Server w klastrze AKS. Ten proces uaktualniania tworzy węzły z zainstalowanym najnowszym obrazem systemu Windows Server i poprawkami, a następnie usuwa starsze węzły. Aby uzyskać więcej informacji na temat tego procesu, zobacz [uaktualnianie puli węzłów w AKS][nodepool-upgrade].

Węzły są wdrażane w prywatnej podsieci sieci wirtualnej, bez przypisanych publicznych adresów IP. W celu rozwiązywania problemów i zarządzania protokół SSH jest domyślnie włączony. Ten dostęp SSH jest dostępny tylko przy użyciu wewnętrznego adresu IP.

Aby zapewnić magazyn, węzły używają usługi Azure Managed Disks. W przypadku większości rozmiarów węzłów maszyny wirtualnej są to dyski w warstwie Premium obsługiwane przez dysków SSD o wysokiej wydajności. Dane przechowywane na dyskach zarządzanych są automatycznie szyfrowane w ramach platformy Azure. Aby zwiększyć nadmiarowość, te dyski również są bezpiecznie replikowane w centrum danych platformy Azure.

Środowiska Kubernetes, w AKS lub w innym miejscu, obecnie nie są całkowicie bezpieczne w celu zagwarantowania użycia wielu dzierżawców. Dodatkowe funkcje zabezpieczeń, takie jak *zasady zabezpieczeń*, lub bardziej precyzyjne Kubernetes kontroli dostępu opartej na rolach (Kubernetes RBAC) dla węzłów, utrudniają luki w zabezpieczeniach. Jednak w celu zapewnienia prawdziwych zabezpieczeń przy uruchamianiu nieprzechodnich obciążeń z wieloma dzierżawcami funkcja hypervisor jest jedynym poziomem zabezpieczeń, który należy zaufać. Domena zabezpieczeń dla Kubernetes jest cały klaster, a nie pojedynczy węzeł. W przypadku tych typów nieszkodliwych obciążeń z wieloma dzierżawcami należy używać klastrów fizycznie izolowanych. Aby uzyskać więcej informacji na temat sposobów izolowania obciążeń, zobacz [najlepsze rozwiązania dotyczące izolacji klastra w AKS][cluster-isolation].

### <a name="compute-isolation"></a>Izolacja obliczeniowa

 Niektóre obciążenia mogą wymagać wysokiego stopnia odizolowania od innych obciążeń klientów ze względu na zgodność lub wymagania prawne. W przypadku tych obciążeń platforma Azure udostępnia [izolowane maszyny wirtualne](../virtual-machines/isolation.md), które mogą być używane jako węzły agentów w klastrze AKS. Te izolowane maszyny wirtualne są izolowane do określonego typu sprzętu i przeznaczone dla jednego klienta. 

 Aby użyć tych odizolowanych maszyn wirtualnych z klastrem AKS, wybierz jedną z odizolowanych maszyn wirtualnych, które są wymienione w [tym miejscu](../virtual-machines/isolation.md) jako **rozmiar węzła** podczas tworzenia klastra AKS lub dodania puli węzłów.


## <a name="cluster-upgrades"></a>Uaktualnienia klastra

W celu zapewnienia bezpieczeństwa i zgodności lub korzystania z najnowszych funkcji platforma Azure oferuje narzędzia do organizowania uaktualnienia klastra AKS i składników programu. Ta aranżacja uaktualniania obejmuje zarówno składnik główny Kubernetes, jak i składniki agentów. Można wyświetlić [listę dostępnych wersji Kubernetes](supported-kubernetes-versions.md) dla klastra AKS. Aby rozpocząć proces uaktualniania, należy określić jedną z tych dostępnych wersji. Następnie platforma Azure bezpiecznie cordons i opróżnia każdy węzeł AKS i przeprowadza uaktualnienie.

### <a name="cordon-and-drain"></a>Cordon i opróżnianie

W trakcie procesu uaktualniania węzły AKS są indywidualnie odizolowywane z klastra, więc nie zaplanowano nowych zasobników. Węzły są następnie opróżniane i uaktualniane w następujący sposób:

- Nowy węzeł jest wdrażany w puli węzłów. Ten węzeł uruchamia najnowszą wersję obrazu systemu operacyjnego i poprawki.
- Jeden z istniejących węzłów jest identyfikowany do uaktualnienia. W tym węźle są bezpiecznie kończone i zaplanowani w innych węzłach w puli węzłów.
- Ten istniejący węzeł zostanie usunięty z klastra AKS.
- Następny węzeł w klastrze jest odizolowywane i opróżniany przy użyciu tego samego procesu, dopóki wszystkie węzły zostaną pomyślnie zastąpione w ramach procesu uaktualniania.

Aby uzyskać więcej informacji, zobacz [Uaktualnianie klastra AKS][aks-upgrade-cluster].

## <a name="network-security"></a>Bezpieczeństwo sieci

W przypadku połączeń i zabezpieczeń z sieciami lokalnymi można wdrożyć klaster AKS w istniejących podsieciach sieci wirtualnej platformy Azure. Te sieci wirtualne mogą mieć połączenie sieci VPN typu lokacja-lokacja lub usługi Express Route z siecią lokalną. Kontrolery transferu danych przychodzących Kubernetes można definiować za pomocą prywatnych, wewnętrznych adresów IP, dzięki czemu usługi są dostępne tylko za pośrednictwem tego wewnętrznego połączenia sieciowego.

### <a name="azure-network-security-groups"></a>Sieciowe grupy zabezpieczeń platformy Azure

Aby odfiltrować przepływ ruchu w sieciach wirtualnych, na platformie Azure są stosowane reguły sieciowej grupy zabezpieczeń. Te reguły definiują źródłowe i docelowe zakresy adresów IP, porty i protokoły, które są dozwolone lub odrzucane przez dostęp do zasobów. Tworzone są reguły domyślne, które zezwalają na ruch TLS do serwera interfejsu API Kubernetes. Podczas tworzenia usług przy użyciu modułów równoważenia obciążenia, mapowania portów lub tras transferu danych przychodzących program AKS automatycznie modyfikuje sieciowe grupy zabezpieczeń, aby ruch był odpowiednio przepływ.

W przypadkach, gdy udostępniasz własną podsieć dla klastra AKS i chcesz zmodyfikować przepływ ruchu, nie należy modyfikować sieciowej grupy zabezpieczeń na poziomie podsieci zarządzanej przez AKS. Można utworzyć dodatkowe sieciowe grupy zabezpieczeń na poziomie podsieci w celu zmodyfikowania przepływu ruchu, o ile nie zakłócają one ruchu potrzebnego do zarządzania klastrem, takich jak dostęp do usługi równoważenia obciążenia, komunikacja z płaszczyzną kontroli i ruch wychodzący [.][aks-limit-egress-traffic]

### <a name="kubernetes-network-policy"></a>Zasady sieci Kubernetes

Aby ograniczyć ruch sieciowy między zasobnikami w klastrze, AKS oferuje obsługę [zasad sieciowych Kubernetes][network-policy]. Przy użyciu zasad sieciowych można wybrać opcję zezwalania lub odrzucania określonych ścieżek sieciowych w klastrze na podstawie obszarów nazw i selektorów etykiet.

## <a name="kubernetes-secrets"></a>Wpisy tajne usługi Kubernetes

*Wpis tajny* Kubernetes jest używany do dodawania poufnych danych do zasobników, takich jak poświadczenia dostępu lub klucze. Najpierw utwórz wpis tajny przy użyciu interfejsu API Kubernetes. Podczas definiowania użytkownika lub wdrożenia można żądać określonego klucza tajnego. Wpisy tajne są dostarczane tylko do węzłów, które mają zaplanowaną w tym, że wymagają, a wpis tajny jest przechowywany w *tmpfs*, a nie zapisywany na dysku. Po usunięciu ostatniego elementu na węźle, który wymaga wpisu tajnego, wpis tajny jest usuwany z tmpfs węzła. Wpisy tajne są przechowywane w danym obszarze nazw i można do nich uzyskiwać dostęp tylko w obrębie tego samego obszaru nazw.

Użycie wpisów tajnych zmniejsza poufne informacje, które są zdefiniowane w manifeście "YAML" lub "Service". Zamiast tego należy zażądać wpisu tajnego przechowywanego na serwerze interfejsu API Kubernetes jako część manifestu YAML. Takie podejście zapewnia tylko szczególny dostęp do klucza tajnego. Uwaga: pliki manifestu RAW Secret zawierają dane tajne w formacie base64 (szczegółowe informacje znajdują się w [oficjalnej dokumentacji][secret-risks] ). W związku z tym ten plik powinien być traktowany jako informacje poufne i nigdy nie został przekazany do kontroli źródła.

Wpisy tajne Kubernetes są przechowywane w etcd, rozproszonym magazynie klucza i wartości. Magazyn Etcd jest w pełni zarządzany przez AKS, a [dane są szyfrowane w ramach platformy Azure][encryption-atrest]. 

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć Zabezpieczanie klastrów AKS, zobacz [Uaktualnianie klastra AKS][aks-upgrade-cluster].

W przypadku skojarzonych najlepszych rozwiązań należy zapoznać się [z najlepszymi rozwiązaniami dotyczącymi zabezpieczeń i uaktualnień klastra w AKS][operator-best-practices-cluster-security] i [najlepszych rozwiązaniach dotyczących zabezpieczeń pod kątem bezpieczeństwa w AKS][developer-best-practices-pod-security].

Aby uzyskać dodatkowe informacje na temat podstawowych pojęć związanych z Kubernetes i AKS, zobacz następujące artykuły:

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
