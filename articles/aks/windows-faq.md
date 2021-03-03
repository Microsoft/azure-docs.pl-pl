---
title: Pule węzłów systemu Windows Server — często zadawane pytania
titleSuffix: Azure Kubernetes Service
description: Zapoznaj się z często zadawanymi pytaniami w przypadku uruchamiania pul węzłów systemu Windows Server i obciążeń aplikacji w usłudze Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 10/12/2020
ms.openlocfilehash: cc5a5ec2bbfb64a1e787277bf67579bad0543cd6
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101739580"
---
# <a name="frequently-asked-questions-for-windows-server-node-pools-in-aks"></a>Często zadawane pytania dotyczące pul węzłów systemu Windows Server w AKS

W usłudze Azure Kubernetes Service (AKS) można utworzyć pulę węzłów, w której działa system Windows Server jako system operacyjny gościa na węzłach. Te węzły mogą uruchamiać natywne aplikacje kontenera systemu Windows, takie jak te utworzone na .NET Framework. Istnieją różnice w sposobie obsługi kontenerów w systemie operacyjnym Linux i Windows. Niektóre popularne funkcje Kubernetes i powiązane z systemem Linux nie są obecnie dostępne dla pul węzłów systemu Windows.

W tym artykule przedstawiono kilka często zadawanych pytań i koncepcji systemu operacyjnego dla węzłów systemu Windows Server w programie AKS.

## <a name="which-windows-operating-systems-are-supported"></a>Które systemy operacyjne Windows są obsługiwane?

AKS używa systemu Windows Server 2019 jako wersji systemu operacyjnego hosta i obsługuje tylko izolację procesu. Obrazy kontenerów skompilowane przy użyciu innych wersji systemu Windows Server nie są obsługiwane. Aby uzyskać więcej informacji, zobacz [zgodność wersji kontenera systemu Windows][windows-container-compat].

## <a name="is-kubernetes-different-on-windows-and-linux"></a>Czy Kubernetes różnią się w systemach Windows i Linux?

Obsługa puli węzłów serwera okna zawiera pewne ograniczenia, które są częścią nadrzędnego serwera systemu Windows w projekcie Kubernetes. Te ograniczenia nie są specyficzne dla AKS. Aby uzyskać więcej informacji o tej obsłudze nadrzędnej systemu Windows Server w programie Kubernetes, zobacz sekcję [obsługiwane funkcje i ograniczenia][upstream-limitations] w artykule [wprowadzenie do obsługi systemu Windows w dokumencie Kubernetes][intro-windows] , z projektu Kubernetes.

Kubernetes jest historycznym systemem Linux. Wiele przykładów używanych w nadrzędnej witrynie sieci Web [Kubernetes.IO][kubernetes] jest przeznaczonych do użycia w węzłach systemu Linux. Podczas tworzenia wdrożeń korzystających z kontenerów systemu Windows Server następujące zagadnienia są stosowane na poziomie systemu operacyjnego:

- **Tożsamość** — system Linux identyfikuje użytkownika przez liczbę całkowitą użytkownika (UID). Użytkownik ma także alfanumeryczną nazwę użytkownika na potrzeby logowania, którą system Linux tłumaczy na UID użytkownika. Podobnie system Linux identyfikuje grupę użytkowników według identyfikatora grupy całkowitej (GID) i tłumaczy nazwę grupy na odpowiadającą jej wartość GID.
    - System Windows Server używa większego identyfikatora zabezpieczeń (SID), który jest przechowywany w bazie danych Menedżera dostępu zabezpieczeń (SAM) systemu Windows. Ta baza danych nie jest udostępniana między hostem i kontenerami ani między kontenerami.
- **Uprawnienia do plików** — system Windows Server używa listy kontroli dostępu opartej na identyfikatorach SID, a nie maski bitów uprawnień i UID + GID
- **Ścieżki plików** — Konwencja w systemie Windows Server jest używana \ zamiast/.
    - W obszarze specyfikacje instalujące woluminy określ ścieżkę dla kontenerów systemu Windows Server. Na przykład zamiast punktu instalacji */mnt/Volume* w kontenerze systemu Linux należy określić literę dysku i lokalizację, taką jak */K/Volume* , do zainstalowania jako dysk *K:* .

## <a name="what-kind-of-disks-are-supported-for-windows"></a>Jakiego rodzaju dyski są obsługiwane w systemie Windows?

Dyski platformy Azure i Azure Files są obsługiwanymi typami woluminów. Są one dostępne jako woluminy NTFS w kontenerze systemu Windows Server.

## <a name="can-i-run-windows-only-clusters-in-aks"></a>Czy można uruchamiać tylko klastry systemu Windows w AKS?

Węzły główne (płaszczyzna kontroli) w klastrze AKS są hostowane przez AKS tej usługi, nie będą widoczne w systemie operacyjnym węzłów obsługujących składniki główne. Wszystkie klastry AKS są tworzone z domyślną pulą pierwszego węzła, która jest oparta na systemie Linux. Ta Pula węzłów zawiera usługi systemowe, które są konieczne do funkcjonowania klastra. Zaleca się uruchomienie co najmniej dwóch węzłów w pierwszej puli węzłów w celu zapewnienia niezawodności klastra i możliwości wykonywania operacji klastra. Nie można usunąć pierwszej puli węzłów opartej na systemie Linux, chyba że zostanie usunięty sam klaster AKS.

## <a name="how-do-i-patch-my-windows-nodes"></a>Jak mogę zastosować poprawki do węzłów systemu Windows?

Aby uzyskać najnowsze poprawki dla węzłów systemu Windows, można [uaktualnić pulę węzłów][nodepool-upgrade] lub [uaktualnić obraz węzła][upgrade-node-image]. Aktualizacje systemu Windows nie są włączone na węzłach w AKS. AKS zwalnia nowe obrazy puli węzłów, gdy tylko poprawki są dostępne, a użytkownik jest odpowiedzialny za uaktualnienie pul węzłów, aby zachować bieżące informacje na temat poprawek i poprawek. Dotyczy to również używanej wersji Kubernetes. [Informacje o wersji AKS][aks-release-notes] wskazują, kiedy są dostępne nowe wersje. Aby uzyskać więcej informacji na temat uaktualniania całej puli węzłów systemu Windows Server, zobacz [uaktualnianie puli węzłów w AKS][nodepool-upgrade]. Jeśli interesuje Cię tylko aktualizacja obrazu węzła, zobacz [uaktualnianie obrazów węzłów AKS][upgrade-node-image].

> [!NOTE]
> Zaktualizowany obraz systemu Windows Server będzie używany tylko wtedy, gdy uaktualnienie klastra (uaktualnienie płaszczyzny kontroli) zostało wykonane przed uaktualnieniem puli węzłów.
>

## <a name="what-network-plug-ins-are-supported"></a>Jakie wtyczki sieciowe są obsługiwane?

Klastry AKS z pulami węzłów systemu Windows muszą używać modelu sieci usługi Azure CNI (Advanced). Sieć korzystającą wtyczki kubenet (podstawowa) nie jest obsługiwana. Aby uzyskać więcej informacji na temat różnic między modelami sieci, zobacz [pojęcia dotyczące sieci dla aplikacji w AKS][azure-network-models]. Model sieci usługi Azure CNI wymaga dodatkowego planowania i zagadnień związanych z zarządzaniem adresami IP. Aby uzyskać więcej informacji na temat planowania i implementowania usługi Azure CNI, zobacz [Konfigurowanie sieci Azure CNI w programie AKS][configure-azure-cni].

W węzłach systemu Windows w klastrach AKS jest również włączona funkcja [bezpośredniego powrotu serwera (DSR)][dsr] , gdy Calico jest włączona.

## <a name="is-preserving-the-client-source-ip-supported"></a>Czy jest zachowywany obsługiwany adres IP źródła klienta?

W tej chwili [zachowywanie adresu IP źródła klienta][client-source-ip] nie jest obsługiwane w węzłach systemu Windows.

## <a name="can-i-change-the-max--of-pods-per-node"></a>Czy mogę zmienić maksymalną liczbę zasobników na węzeł?

Tak. Aby uzyskać dostępne implikacje i opcje, zobacz [maksymalną liczbę zasobników][maximum-number-of-pods].

## <a name="why-am-i-seeing-an-error-when-i-try-to-create-a-new-windows-agent-pool"></a>Dlaczego widzę błąd podczas próby utworzenia nowej puli agentów systemu Windows?

Jeśli klaster został utworzony przed luty 2020 i nigdy nie wykonał żadnych operacji uaktualniania klastra, klaster nadal używa starego obrazu systemu Windows. Mógł wystąpić błąd podobny do następującego:

"Nie znaleziono następującej listy obrazów, do których odwołuje się szablon wdrożenia: Publisher: MicrosoftWindowsServer, oferta: WindowsServer, SKU: 2019-Datacenter-Core-smalldisk-2004, wersja: Najnowsza. Zapoznaj się z instrukcjami dotyczącymi https://docs.microsoft.com/azure/virtual-machines/windows/cli-ps-findimage znajdowania dostępnych obrazów.

Aby naprawić ten błąd:

1. Uaktualnij [płaszczyznę kontroli klastra][upgrade-cluster-cp] , aby zaktualizować ofertę obrazu i wydawcę.
1. Utwórz nowe pule agentów systemu Windows.
1. Przenieś zestawy Windows z istniejących pul agentów systemu Windows na nowe pule agentów systemu Windows.
1. Usuń stare pule agentów systemu Windows.

## <a name="how-do-i-rotate-the-service-principal-for-my-windows-node-pool"></a>Jak mogę obrócić jednostki usługi dla puli węzłów systemu Windows?

Pule węzłów systemu Windows nie obsługują rotacji jednostki usługi. Aby zaktualizować jednostkę usługi, Utwórz nową pulę węzłów systemu Windows i Przeprowadź migrację jednostek z starszej puli do nowej. Po zakończeniu tej czynności usuń starszą pulę węzłów.

Zamiast tego należy używać tożsamości zarządzanych, które są zasadniczo otokami wokół jednostek usługi. Aby uzyskać więcej informacji, zobacz [Korzystanie z tożsamości zarządzanych w usłudze Azure Kubernetes Service][managed-identity].

## <a name="how-many-node-pools-can-i-create"></a>Ile pul węzłów można utworzyć?

Klaster AKS może mieć maksymalnie 10 pul węzłów. W ramach pul węzłów można mieć maksymalnie 1000 węzłów. [Ograniczenia puli węzłów][nodepool-limitations].

## <a name="what-can-i-name-my-windows-node-pools"></a>Jak Nazywam się pulami węzłów systemu Windows?

Należy zachować nazwę maksymalnie 6 (sześć znaków). Jest to bieżące ograniczenie AKS.

## <a name="are-all-features-supported-with-windows-nodes"></a>Czy wszystkie funkcje są obsługiwane z węzłami systemu Windows?

Korzystającą wtyczki kubenet nie jest obecnie obsługiwana w przypadku węzłów systemu Windows.

## <a name="can-i-run-ingress-controllers-on-windows-nodes"></a>Czy można uruchamiać kontrolery transferu danych przychodzących w węzłach systemu Windows?

Tak, kontroler transferu danych przychodzących obsługujący kontenery systemu Windows Server można uruchamiać w węzłach systemu Windows w AKS.

## <a name="can-i-use-azure-dev-spaces-with-windows-nodes"></a>Czy mogę używać Azure Dev Spaces z węzłami systemu Windows?

Azure Dev Spaces jest obecnie dostępna tylko dla pul węzłów opartych na systemie Linux.

## <a name="can-my-windows-server-containers-use-gmsa"></a>Czy moje kontenery systemu Windows Server używają gMSA?

Obsługa kont usług zarządzanych przez grupę (gMSA) nie jest obecnie dostępna w AKS.

## <a name="can-i-use-azure-monitor-for-containers-with-windows-nodes-and-containers"></a>Czy mogę używać Azure Monitor do kontenerów z węzłami i kontenerami systemu Windows?

Tak, jednak Azure Monitor jest w publicznej wersji zapoznawczej na potrzeby zbierania dzienników (stdout, stderr) i metryk z kontenerów systemu Windows. Można również dołączyć strumień strumieni strumienia stdout z kontenera systemu Windows.

## <a name="are-there-any-limitations-on-the-number-of-services-on-a-cluster-with-windows-nodes"></a>Czy istnieją jakieś ograniczenia dotyczące liczby usług w klastrze z węzłami systemu Windows?

Klaster z węzłami systemu Windows może mieć około 500 usług, zanim napotka wyczerpanie portów.

## <a name="can-i-use-azure-hybrid-benefit-with-windows-nodes"></a>Czy mogę używać Korzyść użycia hybrydowego platformy Azure z węzłami systemu Windows?

Tak. Korzyść użycia hybrydowego platformy Azure systemu Windows Server zmniejsza koszty operacyjne, umożliwiając przeprowadzenie lokalnej licencji systemu Windows Server do AKS węzłów systemu Windows.

Korzyść użycia hybrydowego platformy Azure można używać w całym klastrze AKS lub w poszczególnych węzłach. W przypadku poszczególnych węzłów należy przejść do [grupy zasobów węzła][resource-groups] i bezpośrednio zastosować korzyść użycia hybrydowego platformy Azure do węzłów. Aby uzyskać więcej informacji na temat stosowania Korzyść użycia hybrydowego platformy Azure do poszczególnych węzłów, zobacz [korzyść użycia hybrydowego platformy Azure dla systemu Windows Server][hybrid-vms]. 

Aby użyć Korzyść użycia hybrydowego platformy Azure w nowym klastrze AKS, użyj `--enable-ahub` argumentu.

```azurecli
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-sku Standard \
    --windows-admin-password 'Password1234$' \
    --windows-admin-username azure \
    --network-plugin azure
    --enable-ahub
```

Aby użyć Korzyść użycia hybrydowego platformy Azure w istniejącym klastrze AKS, zaktualizuj klaster przy użyciu `--enable-ahub` argumentu.

```azurecli
az aks update \
    --resource-group myResourceGroup
    --name myAKSCluster
    --enable-ahub
```

Aby sprawdzić, czy w klastrze ustawiono Korzyść użycia hybrydowego platformy Azure, użyj następującego polecenia:

```azurecli
az vmss show --name myAKSCluster --resource-group MC_CLUSTERNAME
```

Jeśli klaster ma Korzyść użycia hybrydowego platformy Azure włączone, dane wyjściowe `az vmss show` będą podobne do następujących:

```console
"platformFaultDomainCount": 1,
  "provisioningState": "Succeeded",
  "proximityPlacementGroup": null,
  "resourceGroup": "MC_CLUSTERNAME"
```

## <a name="can-i-use-the-kubernetes-web-dashboard-with-windows-containers"></a>Czy mogę używać pulpitu nawigacyjnego sieci Web Kubernetes z kontenerami systemu Windows?

Tak, możesz użyć [pulpitu nawigacyjnego sieci Web Kubernetes][kubernetes-dashboard] , aby uzyskać dostęp do informacji o kontenerach systemu Windows, ale w tej chwili nie można uruchomić *polecenia kubectl exec* w działającym kontenerze systemu Windows bezpośrednio z poziomu pulpitu nawigacyjnego Kubernetes sieci Web. Aby uzyskać więcej informacji na temat nawiązywania połączenia z działającym kontenerem systemu Windows, zobacz [Connect with RDP to Azure Kubernetes Service (AKS) węzły systemu Windows Server w celu przeprowadzenia konserwacji lub rozwiązywania problemów][windows-rdp].

## <a name="what-if-i-need-a-feature-thats-not-supported"></a>Co zrobić, jeśli potrzebuję funkcji, która nie jest obsługiwana?

Pracujemy nad udostępnieniem wszystkich funkcji potrzebnych do systemu Windows w AKS, ale jeśli wystąpią przerwy, projekt typu "open source", a [AKS-Engine][aks-engine] umożliwia łatwą i w pełni dostosowywalny sposób uruchamiania Kubernetes na platformie Azure, w tym pomocy technicznej systemu Windows. Zapoznaj się z naszym planem funkcji [AKS plan][aks-roadmap].

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć pracę z kontenerami systemu Windows Server w programie AKS, należy [utworzyć pulę węzłów, w której działa system Windows Server w AKS][windows-node-cli].

<!-- LINKS - external -->
[kubernetes]: https://kubernetes.io
[aks-engine]: https://github.com/azure/aks-engine
[upstream-limitations]: https://kubernetes.io/docs/setup/production-environment/windows/intro-windows-in-kubernetes/#supported-functionality-and-limitations
[intro-windows]: https://kubernetes.io/docs/setup/production-environment/windows/intro-windows-in-kubernetes/
[aks-roadmap]: https://github.com/Azure/AKS/projects/1
[aks-release-notes]: https://github.com/Azure/AKS/releases

<!-- LINKS - internal -->
[azure-network-models]: concepts-network.md#azure-virtual-networks
[configure-azure-cni]: configure-azure-cni.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[windows-node-cli]: windows-container-cli.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[upgrade-cluster]: upgrade-cluster.md
[upgrade-cluster-cp]: use-multiple-node-pools.md#upgrade-a-cluster-control-plane-with-multiple-node-pools
[azure-outbound-traffic]: ../load-balancer/load-balancer-outbound-connections.md#defaultsnat
[nodepool-limitations]: use-multiple-node-pools.md#limitations
[windows-container-compat]: /virtualization/windowscontainers/deploy-containers/version-compatibility?tabs=windows-server-2019%2Cwindows-10-1909
[maximum-number-of-pods]: configure-azure-cni.md#maximum-pods-per-node
[azure-monitor]: ../azure-monitor/containers/container-insights-overview.md#what-does-azure-monitor-for-containers-provide
[client-source-ip]: concepts-network.md#ingress-controllers
[kubernetes-dashboard]: kubernetes-dashboard.md
[windows-rdp]: rdp.md
[upgrade-node-image]: node-image-upgrade.md
[managed-identity]: use-managed-identity.md
[hybrid-vms]: ../virtual-machines/windows/hybrid-use-benefit-licensing.md
[resource-groups]: faq.md#why-are-two-resource-groups-created-with-aks
[dsr]: ../load-balancer/load-balancer-multivip-overview.md#rule-type-2-backend-port-reuse-by-using-floating-ip