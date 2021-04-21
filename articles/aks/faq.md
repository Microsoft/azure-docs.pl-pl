---
title: Często zadawane pytania dotyczące Azure Kubernetes Service (AKS)
description: Znajdź odpowiedzi na niektóre często zadawane pytania dotyczące Azure Kubernetes Service (AKS).
ms.topic: conceptual
ms.date: 08/06/2020
ms.custom: references_regions
ms.openlocfilehash: f13d7a33ce1dc04700932072fe0af80a901c681f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783201"
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>Często zadawane pytania dotyczące usługi Azure Kubernetes Service (AKS)

W tym artykule często zadawane pytania dotyczące Azure Kubernetes Service (AKS).

## <a name="which-azure-regions-currently-provide-aks"></a>Które regiony platformy Azure obecnie zapewniają usługę AKS?

Aby uzyskać pełną listę dostępnych regionów, zobacz [AKS regions and availability (Regiony i dostępność usługi AKS).][aks-regions]

## <a name="can-i-spread-an-aks-cluster-across-regions"></a>Czy mogę rozłożyć klaster usługi AKS między regionami?

Nie. Klastry AKS są zasobami regionalnymi i nie mogą obejmować regionów. Aby [uzyskać wskazówki dotyczące tworzenia architektury,][bcdr-bestpractices] która obejmuje wiele regionów, zobacz najlepsze rozwiązania dotyczące ciągłości działania i odzyskiwania po awarii.

## <a name="can-i-spread-an-aks-cluster-across-availability-zones"></a>Czy można rozłożyć klaster usługi AKS w różnych strefach dostępności?

Tak. Klaster usługi AKS można wdrożyć w co najmniej jednej [strefie dostępności][availability-zones] w [regionach, które je obsługują.][az-regions]

## <a name="can-i-limit-who-has-access-to-the-kubernetes-api-server"></a>Czy mogę ograniczyć, kto ma dostęp do serwera interfejsu API Kubernetes?

Tak. Istnieją dwie opcje ograniczania dostępu do serwera interfejsu API:

- Użyj [autoryzowanych zakresów adresów IP serwera interfejsu API,][api-server-authorized-ip-ranges] jeśli chcesz zachować publiczny punkt końcowy dla serwera interfejsu API, ale ograniczyć dostęp do zestawu zaufanych zakresów adresów IP.
- Użyj [klastra prywatnego,][private-clusters] jeśli chcesz ograniczyć serwer interfejsu API tak, *aby* był dostępny tylko z poziomu sieci wirtualnej.

## <a name="can-i-have-different-vm-sizes-in-a-single-cluster"></a>Czy mogę mieć różne rozmiary maszyn wirtualnych w jednym klastrze?

Tak, możesz użyć różnych rozmiarów maszyn wirtualnych w klastrze usługi AKS, tworząc [wiele pul węzłów.][multi-node-pools]

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>Czy aktualizacje zabezpieczeń są stosowane do węzłów agenta usługi AKS?

Platforma Azure automatycznie stosuje poprawki zabezpieczeń do węzłów systemu Linux w klastrze zgodnie z harmonogramem nocy. Odpowiadasz jednak za zapewnienie, że te węzły systemu Linux są ponownie uruchamiane zgodnie z potrzebami. Dostępnych jest kilka opcji ponownego uruchamiania węzłów:

- Ręcznie za pomocą interfejsu Azure Portal lub interfejsu wiersza polecenia platformy Azure.
- Uaktualniając klaster usługi AKS. Klaster automatycznie [uaktualnia system cordon][cordon-drain] i opróżnia węzły, a następnie wprowadza nowy węzeł w tryb online z najnowszym obrazem systemu Ubuntu i nową wersją poprawek lub pomocniczą wersją rozwiązania Kubernetes. Aby uzyskać więcej informacji, zobacz [Uaktualnianie klastra usługi AKS.][aks-upgrade]
- Przy użyciu [uaktualnienia obrazu węzła](node-image-upgrade.md).

### <a name="windows-server-nodes"></a>Węzły systemu Windows Server

W przypadku węzłów systemu Windows Server Windows Update automatycznie uruchamiać i stosować najnowszych aktualizacji. Zgodnie z regularnym harmonogramem cyklu wydań programu Windows Update własnym procesem weryfikacji należy wykonać uaktualnienie klastra i pul węzłów systemu Windows Server w klastrze usługi AKS. Ten proces uaktualniania tworzy węzły z najnowszym obrazem systemu Windows Server i poprawkami, a następnie usuwa starsze węzły. Aby uzyskać więcej informacji na temat tego procesu, zobacz [Upgrade a node pool in AKS (Uaktualnianie puli węzłów w UKS).][nodepool-upgrade]

## <a name="why-are-two-resource-groups-created-with-aks"></a>Dlaczego dwie grupy zasobów są tworzone za pomocą aks?

Usługa AKS opiera się na wielu zasobach infrastruktury platformy Azure, w tym zestawach skalowania maszyn wirtualnych, sieciach wirtualnych i dyskach zarządzanych. Dzięki temu można korzystać z wielu podstawowych możliwości platformy Azure w zarządzanym środowisku Kubernetes dostarczanym przez usługę AKS. Na przykład większość typów maszyn wirtualnych platformy Azure może być używana bezpośrednio z usługą AKS, a rezerwacje platformy Azure mogą być używane do automatycznego otrzymywania rabatów na te zasoby.

Aby włączyć tę architekturę, każde wdrożenie usługi AKS obejmuje dwie grupy zasobów:

1. Należy utworzyć pierwszą grupę zasobów. Ta grupa zawiera tylko zasób usługi Kubernetes. Dostawca zasobów usługi AKS automatycznie tworzy drugą grupę zasobów podczas wdrażania. Przykładem drugiej grupy zasobów jest *MC_myResourceGroup_myAKSCluster_eastus*. Aby uzyskać informacje na temat określania nazwy drugiej grupy zasobów, zobacz następną sekcję.
1. Druga grupa zasobów, znana jako grupa zasobów *węzła,* zawiera wszystkie zasoby infrastruktury skojarzone z klastrem. Te zasoby obejmują maszyny wirtualne węzła Kubernetes, sieć wirtualną i magazyn. Domyślnie grupa zasobów węzła ma nazwę, na przykład *MC_myResourceGroup_myAKSCluster_eastus*. Usługa AKS automatycznie usuwa zasób węzła przy każdym usunięciu klastra, dlatego powinien być używany tylko dla zasobów, które współużytkuje cykl życia klastra.

## <a name="can-i-provide-my-own-name-for-the-aks-node-resource-group"></a>Czy mogę podać własną nazwę grupy zasobów węzła usługi AKS?

Tak. Domyślnie usługi AKS będą nazywać grupę zasobów węzła *MC_resourcegroupname_clustername_location*, ale możesz również podać własną nazwę.

Aby określić własną nazwę grupy zasobów, zainstaluj rozszerzenie interfejsu wiersza polecenia platformy Azure [aks-preview][aks-preview-cli] w wersji *0.3.2* lub nowszej. Podczas tworzenia klastra usługi AKS za pomocą [polecenia az aks create][az-aks-create] użyj parametru i określ nazwę grupy `--node-resource-group` zasobów. Jeśli używasz [szablonu Azure Resource Manager do][aks-rm-template] wdrożenia klastra usługi AKS, możesz zdefiniować nazwę grupy zasobów przy użyciu właściwości *nodeResourceGroup.*

* Pomocnicza grupa zasobów jest tworzona automatycznie przez dostawcę zasobów platformy Azure we własnej subskrypcji.
* Nazwę niestandardowej grupy zasobów można określić tylko podczas tworzenia klastra.

Podczas pracy z grupą zasobów węzła należy pamiętać, że nie można:

* Określ istniejącą grupę zasobów dla grupy zasobów węzła.
* Określ inną subskrypcję dla grupy zasobów węzła.
* Zmień nazwę grupy zasobów węzła po utworzeniu klastra.
* Określ nazwy zarządzanych zasobów w grupie zasobów węzła.
* Modyfikowanie lub usuwanie tagów zarządzanych zasobów utworzonych przez platformę Azure w grupie zasobów węzła. (Zobacz dodatkowe informacje w następnej sekcji).

## <a name="can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group"></a>Czy mogę modyfikować tagi i inne właściwości zasobów AKS w grupie zasobów węzła?

Jeśli zmodyfikujesz lub usuniesz tagi utworzone przez platformę Azure i inne właściwości zasobów w grupie zasobów węzła, możesz uzyskać nieoczekiwane wyniki, takie jak błędy skalowania i uaktualniania. AKS umożliwia tworzenie i modyfikowanie tagów niestandardowych utworzonych przez użytkowników końcowych oraz dodawanie tych tagów podczas [tworzenia puli węzłów.](use-multiple-node-pools.md#specify-a-taint-label-or-tag-for-a-node-pool) Możesz chcieć utworzyć lub zmodyfikować tagi niestandardowe, na przykład w celu przypisania jednostki biznesowej lub centrum kosztów. Można to również osiągnąć, tworząc zasady platformy Azure z zakresem w zarządzanej grupie zasobów.

Jednak modyfikowanie tagów utworzonych przez platformę **Azure** w zasobach w grupie zasobów węzła w klastrze usługi AKS jest nieobsługiwaną akcją, która przerywa cel poziomu usługi (SLO). Aby uzyskać więcej informacji, zobacz [Czy usługa AKS oferuje umowę poziomu usług?](#does-aks-offer-a-service-level-agreement)

## <a name="what-kubernetes-admission-controllers-does-aks-support-can-admission-controllers-be-added-or-removed"></a>Jakie kontrolery danych kubernetes obsługuje aks? Czy można dodać lub usunąć kontrolery danych?

AKS obsługuje następujące [kontrolery danych:][admission-controllers]

- *NamespaceLifecycle*
- *LimitRanger*
- *ServiceAccount*
- *DefaultStorageClass*
- *DefaultTolerationSeconds*
- *MutatingAdmissionWebhook*
- *ValidatingAdmissionWebhook*
- *ResourceQuota*
- *PodNodeSelector*
- *PodTolerationRestriction*
- *ExtendedResourceToleration*

Obecnie nie można zmodyfikować listy kontrolerów danych w UKS.

## <a name="can-i-use-admission-controller-webhooks-on-aks"></a>Czy mogę używać webhook kontrolera danych w u usługi AKS?

Tak, można używać webhook kontrolera danych w uwitrynie AKS. Zaleca się wykluczenie wewnętrznych przestrzeni nazw usługi AKS, które są oznaczone etykietą **płaszczyzny sterowania.** Na przykład przez dodanie poniższej opcji do konfiguracji webhook:

```
namespaceSelector:
    matchExpressions:
    - key: control-plane
      operator: DoesNotExist
```

Usługa AKS zaporuje ruch wychodzący serwera interfejsu API, więc twoje webhooki kontrolera danych muszą być dostępne z poziomu klastra.

## <a name="can-admission-controller-webhooks-impact-kube-system-and-internal-aks-namespaces"></a>Czy webhook kontrolera wpływu na kube-system i wewnętrzne przestrzenie nazw usługi AKS?

Aby chronić stabilność systemu i zapobiegać wpływowi niestandardowych kontrolerów wpływu na usługi wewnętrzne w systemie kube, przestrzeń nazw usługi AKS ma element **Admissions Enforcer**, który automatycznie wyklucza wewnętrzne przestrzenie nazw kube-system i AKS. Ta usługa gwarantuje, że niestandardowe kontrolery wpływu danych nie wpływają na usługi działające w systemie kube-system.

Jeśli masz krytyczny przypadek użycia dotyczący wdrożenia czegoś w systemie kube-system (nie jest to zalecane), który musi być objęty niestandardowym elementem webhook wejściowym, możesz dodać podaną etykietę lub adnotację, aby element Admissions Enforcer zignorował tę etykietę.

Etykieta: ```"admissions.enforcer/disabled": "true"``` lub Adnotacja: ```"admissions.enforcer/disabled": true```

## <a name="is-azure-key-vault-integrated-with-aks"></a>Czy Azure Key Vault zintegrowana z usługą AKS?

Usługę AKS nie można obecnie natywnie zintegrować z usługą Azure Key Vault. Jednak dostawca [Azure Key Vault csi secrets store][csi-driver] umożliwia bezpośrednią integrację z zasobników Kubernetes w celu Key Vault wpisów tajnych.

## <a name="can-i-run-windows-server-containers-on-aks"></a>Czy mogę uruchamiać kontenery systemu Windows Server w u usługi AKS?

Tak, kontenery systemu Windows Server są dostępne w u usługi AKS. Aby uruchomić kontenery systemu Windows Server w u usługi AKS, należy utworzyć pulę węzłów z systemem Windows Server jako systemem operacyjnym gościa. Kontenery systemu Windows Server mogą używać tylko systemu Windows Server 2019. Aby rozpocząć pracę, zobacz [Tworzenie klastra usługi AKS z pulą węzłów systemu Windows Server.][aks-windows-cli]

Obsługa systemu Windows Server dla puli węzłów obejmuje pewne ograniczenia, które są częścią nadrzędnego systemu Windows Server w projekcie Kubernetes. Aby uzyskać więcej informacji na temat tych ograniczeń, zobacz [Windows Server containers in AKS limitations (Kontenery systemu Windows Server w ograniczeniach usługi AKS).][aks-windows-limitations]

## <a name="does-aks-offer-a-service-level-agreement"></a>Czy usługa AKS oferuje umowę poziomu usług?

AKS zapewnia gwarancje umowy SLA jako opcjonalną funkcję dodatku z [umowami SLA czasu pracy.][uptime-sla] 

Bezpłatna usługa SKU oferowana domyślnie nie ma skojarzonej umowy  poziomu usług *,* ale ma cel poziomu usług na poziomie 99,5%. Może się zdarzyć, że przejściowe problemy z łącznością będą obserwowane w przypadku uaktualnień, węzłów niedotycznych w złej kondycji, konserwacji platformy, przeciążania serwera interfejsu API żądaniami itp. Jeśli obciążenie nie toleruje ponownego uruchamiania serwera interfejsu API, zalecamy użycie umowy SLA czasu pracy.

## <a name="can-i-apply-azure-reservation-discounts-to-my-aks-agent-nodes"></a>Czy mogę zastosować rabaty na rezerwację platformy Azure do węzłów agenta usługi AKS?

Węzły agenta usługi AKS są rozliczane jako standardowe maszyny wirtualne platformy [Azure,][reservation-discounts] więc jeśli zakupiono rezerwacje platformy Azure dla rozmiaru maszyny wirtualnej używanej w usłudze AKS, rabaty te są stosowane automatycznie.

## <a name="can-i-movemigrate-my-cluster-between-azure-tenants"></a>Czy mogę przenieść/zmigrować klaster między dzierżawami platformy Azure?

Przenoszenie klastra AKS między dzierżawami nie jest obecnie obsługiwane.

## <a name="can-i-movemigrate-my-cluster-between-subscriptions"></a>Czy mogę przenieść/zmigrować klaster między subskrypcjami?

Ruch klastrów między subskrypcjami nie jest obecnie obsługiwany.

## <a name="can-i-move-my-aks-clusters-from-the-current-azure-subscription-to-another"></a>Czy mogę przenieść klastry usługi AKS z bieżącej subskrypcji platformy Azure do innej?

Przenoszenie klastra usługi AKS i skojarzonych z nim zasobów między subskrypcjami platformy Azure nie jest obsługiwane.

## <a name="can-i-move-my-aks-cluster-or-aks-infrastructure-resources-to-other-resource-groups-or-rename-them"></a>Czy mogę przenieść zasoby klastra usługi AKS lub infrastruktury usługi AKS do innych grup zasobów lub zmienić ich nazwę?

Przenoszenie lub zmiana nazwy klastra usługi AKS i skojarzonych z nim zasobów nie jest obsługiwana.

## <a name="why-is-my-cluster-delete-taking-so-long"></a>Dlaczego usuwanie klastra trwa tak długo?

Większość klastrów jest usuwana na żądanie użytkownika; w niektórych przypadkach, zwłaszcza gdy klienci przynoszą własną grupę zasobów lub usuwają zadania między grupę zasobów, może zająć więcej czasu lub niepowodzenie. Jeśli masz problem z usuwaniem, sprawdź, czy nie masz blokad w RG, czy wszystkie zasoby spoza tej zasobów nie są skojarzona z rg i tak dalej.

## <a name="if-i-have-pod--deployments-in-state-nodelost-or-unknown-can-i-still-upgrade-my-cluster"></a>Jeśli mam zasobnik/wdrożenia w stanie "NodeLost" lub "Nieznany", czy nadal mogę uaktualnić klaster?

Można, ale nie jest to zalecane przez aks. Uaktualnienia powinny być wykonywane, gdy stan klastra jest znany i jest w dobrej kondycji.

## <a name="if-i-have-a-cluster-with-one-or-more-nodes-in-an-unhealthy-state-or-shut-down-can-i-perform-an-upgrade"></a>Jeśli mam klaster z co najmniej jednym węzłem w złej kondycji lub został zamknięty, czy mogę przeprowadzić uaktualnienie?

Nie, usuń/usuń wszystkie węzły w stanie awarii lub w inny sposób usunięte z klastra przed uaktualnieniem.

## <a name="i-ran-a-cluster-delete-but-see-the-error-errno-11001-getaddrinfo-failed"></a>Po usunięciu klastra wystąpił błąd `[Errno 11001] getaddrinfo failed`

Najczęściej jest to spowodowane tym, że użytkownicy mają co najmniej jedną sieciową grupy zabezpieczeń nadal używaną i skojarzoną z klastrem.  Usuń je i spróbuj ponownie usunąć.

## <a name="i-ran-an-upgrade-but-now-my-pods-are-in-crash-loops-and-readiness-probes-fail"></a>Uruchomiono uaktualnienie, ale teraz moje zasobniki znajdują się w pętlach awarii i sondy gotowości nie powiodą się?

Upewnij się, że twoja główna usługa nie wygasła.  Zobacz: [Jednostki usługi AKS i](./kubernetes-service-principal.md) [Poświadczenia aktualizacji usługi AKS.](./update-credentials.md)

## <a name="my-cluster-was-working-but-suddenly-cant-provision-loadbalancers-mount-pvcs-etc"></a>Mój klaster działa, ale nagle nie może aprowizowania modułów równoważenia obciążenia, instalacji woluminów PVC itp.?

Upewnij się, że twoja główna usługa nie wygasła.  Zobacz: [Jednostki usługi AKS i](./kubernetes-service-principal.md) [Poświadczenia aktualizacji usługi AKS.](./update-credentials.md)

## <a name="can-i-scale-my-aks-cluster-to-zero"></a>Czy mogę skalować klaster usługi AKS do zera?
Możesz całkowicie [zatrzymać uruchomiony klaster usługi AKS,](start-stop-cluster.md)co pozwala zaoszczędzić na kosztach obliczeń. Ponadto można również skalować lub automatycznie skalować wszystkie lub [określone `User` ](scale-cluster.md#scale-user-node-pools-to-0) pule węzłów do 0, zachowując tylko niezbędną konfigurację klastra.
Nie można bezpośrednio skalować pul [węzłów systemu do](use-system-pools.md) zera.

## <a name="can-i-use-the-virtual-machine-scale-set-apis-to-scale-manually"></a>Czy można użyć interfejsów API zestawu skalowania maszyn wirtualnych do ręcznego skalowania?

Nie, operacje skalowania przy użyciu interfejsów API zestawu skalowania maszyn wirtualnych nie są obsługiwane. Użyj interfejsów API AKS ( `az aks scale` ).

## <a name="can-i-use-virtual-machine-scale-sets-to-manually-scale-to-zero-nodes"></a>Czy można używać zestawów skalowania maszyn wirtualnych do ręcznego skalowania do zera węzłów?

Nie, operacje skalowania przy użyciu interfejsów API zestawu skalowania maszyn wirtualnych nie są obsługiwane. Za pomocą interfejsu API usługi AKS można skalować do zera nieu systemowe pule węzłów lub zamiast tego [zatrzymać klaster.](start-stop-cluster.md)

## <a name="can-i-stop-or-de-allocate-all-my-vms"></a>Czy mogę zatrzymać lub coć przydzielać wszystkie moje maszyny wirtualne?

Chociaż usługi AKS mają mechanizmy odporności, które mogą wytrzymać taką konfigurację i odzyskać po jej stronie, nie jest to obsługiwana konfiguracja. [Zamiast tego zatrzymaj](start-stop-cluster.md) klaster.

## <a name="can-i-use-custom-vm-extensions"></a>Czy mogę używać niestandardowych rozszerzeń maszyn wirtualnych?

Agent usługi Log Analytics jest obsługiwany, ponieważ jest to rozszerzenie zarządzane przez firmę Microsoft. W przeciwnym razie usługa AKS jest usługą zarządzaną i manipulowanie zasobami IaaS nie jest obsługiwane. Aby zainstalować składniki niestandardowe, użyj interfejsów API i mechanizmów rozwiązania Kubernetes. Na przykład użyj zestawu DaemonSets, aby zainstalować wymagane składniki.

## <a name="does-aks-store-any-customer-data-outside-of-the-clusters-region"></a>Czy usługa AKS przechowuje jakiekolwiek dane klientów poza regionem klastra?

Funkcja włączania przechowywania danych klientów w jednym regionie jest obecnie dostępna tylko w regionie Azja Południowo-Wschodnia (Singapur) regionu geograficznego regionu Azja i Pacyfik i Brazylii Południowej (stan Sao Paulo) w Brazylii Geo. W przypadku wszystkich innych regionów dane klientów są przechowywane w lokalizacji geograficznej.

## <a name="are-aks-images-required-to-run-as-root"></a>Czy obrazy AKS są wymagane do uruchomienia jako katalog główny?

Z wyjątkiem następujących dwóch obrazów, obrazy AKS nie muszą być uruchamiane jako obrazy główne:

- *mcr.microsoft.com/oss/kubernetes/coredns*
- *mcr.microsoft.com/azuremonitor/containerinsights/ciprod*

## <a name="what-is-azure-cni-transparent-mode-vs-bridge-mode"></a>Co to jest Azure CNI tryb przezroczysty a tryb mostka?

W wersji 1.2.0 Azure CNI tryb przezroczysty jako domyślny dla wdrożeń CNI systemu Linux z pojedynczą usługą. Tryb przezroczysty zastępuje tryb mostka. W tej sekcji bardziej omówiono różnice dotyczące obu trybów oraz korzyści/ograniczenia dotyczące korzystania z trybu przezroczystego w trybie Azure CNI.

### <a name="bridge-mode"></a>Tryb mostka

Jak sugeruje nazwa, tryb mostka Azure CNI sposób "dokładnie na czas" utworzy mostek L2 o nazwie "azure0". Wszystkie interfejsy pary zasobników `veth` po stronie hosta zostaną połączone z tym mostkiem. Dlatego Pod-Pod komunikacji wewnątrz maszyny wirtualnej, a pozostały ruch przechodzi przez ten most. Ten mostek jest urządzeniem wirtualnym warstwy 2, które samodzielnie nie może odbierać ani przesyłać żadnych danych, chyba że powiąż z nim co najmniej jedno rzeczywiste urządzenie. Z tego powodu eth0 maszyny wirtualnej z systemem Linux należy przekonwertować na mostek podrzędny na mostek "azure0". Tworzy złożoną topologię sieci na maszynie wirtualnej z systemem Linux i jako objaw CNI musiała zająć się innymi funkcjami sieci, np. aktualizacjami serwera DNS i tak dalej.

:::image type="content" source="media/faq/bridge-mode.png" alt-text="Topologia trybu mostka":::

Poniżej przedstawiono przykład konfiguracji trasy ip w trybie mostka. Niezależnie od tego, ile zasobników ma węzeł, zawsze będą dostępne tylko dwie trasy. Pierwsza z nich mówi, że cały ruch z wyłączeniem ruchu lokalnego na platformie azure0 będzie przechodzić do domyślnej bramy podsieci za pośrednictwem interfejsu z przestrzenią zasobnika "src 10.240.0.4" (czyli podstawowym adresem IP węzła), a drugim — "10.20.x.x", w zasobniku jądra, aby jądro decydowało.

```bash
default via 10.240.0.1 dev azure0 proto dhcp src 10.240.0.4 metric 100
10.240.0.0/12 dev azure0 proto kernel scope link src 10.240.0.4
172.17.0.0/16 dev docker0 proto kernel scope link src 172.17.0.1 linkdown
root@k8s-agentpool1-20465682-1:/#
```

### <a name="transparent-mode"></a>Tryb przezroczysty
Tryb przezroczysty przyjmuje proste podejście do konfigurowania sieci systemu Linux. W tym trybie Azure CNI nie zmieni żadnych właściwości interfejsu eth0 na maszynie wirtualnej z systemem Linux. Ta minimalna metoda zmiany właściwości sieci systemu Linux pomaga ograniczyć złożone problemy z narożnikami, które klastry mogą mierzyć się z trybem mostka. W trybie przezroczystym Azure CNI i doda interfejsy par zasobników po stronie hosta, które zostaną `veth` dodane do sieci hosta. Komunikacja wewnątrz maszyny wirtualnej między zasobnikami jest za pośrednictwem tras adresów IP, które zostaną przez CNI dodać. Zasadniczo komunikacja między zasobnikami odbywa się w warstwie 3, a ruch zasobników jest trasowany przez reguły routingu L3.

:::image type="content" source="media/faq/transparent-mode.png" alt-text="Topologia trybu przezroczystego":::

Poniżej przedstawiono przykładową konfigurację trasy ip trybu przezroczystego. Interfejs każdego zasobnika otrzyma dołączona trasę statyczną, dzięki czemu ruch z dest IP będzie wysyłany bezpośrednio do interfejsu pary po stronie hosta `veth` zasobnika.

```bash
10.240.0.216 dev azv79d05038592 proto static
10.240.0.218 dev azv8184320e2bf proto static
10.240.0.219 dev azvc0339d223b9 proto static
10.240.0.222 dev azv722a6b28449 proto static
10.240.0.223 dev azve7f326f1507 proto static
10.240.0.224 dev azvb3bfccdd75a proto static
168.63.129.16 via 10.240.0.1 dev eth0 proto dhcp src 10.240.0.4 metric 100
169.254.169.254 via 10.240.0.1 dev eth0 proto dhcp src 10.240.0.4 metric 100
172.17.0.0/16 dev docker0 proto kernel scope link src 172.17.0.1 linkdown
```

### <a name="benefits-of-transparent-mode"></a>Zalety trybu przezroczystego

- Zapewnia środki zaradcze dla warunku równoległego wyścigu DNS i unikanie problemów z opóźnieniami DNS 5-s bez konieczności skonfigurowania lokalnego węzła DNS (nadal można używać lokalnego węzła DNS ze względu na `conntrack` wydajność).
- Eliminuje początkowy tryb mostka CNI opóźnienia DNS o wartości 5 s z powodu konfiguracji mostka "dokładnie na czas".
- Jednym z narożników w trybie mostka jest to, że Azure CNI nie może aktualizować niestandardowych list serwerów DNS, które użytkownicy dodają do sieci wirtualnej lub karty sieciowej. Powoduje to, że CNI pobieranie tylko pierwsze wystąpienie listy serwerów DNS. Rozwiązanie w trybie przezroczystym, ponieważ CNI nie zmienia żadnych właściwości eth0. Więcej informacji można [znaleźć tutaj.](https://github.com/Azure/azure-container-networking/issues/713)
- Zapewnia lepszą obsługę ruchu UDP i środki zaradcze dla potopu UDP, gdy URP przejmie czas. W trybie mostka, gdy mostek nie zna adresu MAC zasobnika docelowego w komunikacji między zasobnikami wewnątrz maszyny wirtualnej, zgodnie z projektem powoduje to burzę pakietu do wszystkich portów. Rozwiązanie w trybie przezroczystym, ponieważ w ścieżce nie ma żadnych urządzeń L2. Więcej informacji można [znaleźć tutaj.](https://github.com/Azure/azure-container-networking/issues/704)
- Tryb przezroczysty działa lepiej w komunikacji między zasobnikami wewnątrz maszyny wirtualnej pod względem przepływności i opóźnienia w porównaniu z trybem mostka.

## <a name="how-to-avoid-permission-ownership-setting-slow-issues-when-the-volume-has-a-lot-of-files"></a>Jak uniknąć problemów z powolnym ustawianiem własności uprawnień, gdy wolumin ma dużo plików?

Tradycyjnie, jeśli zasobnik jest uruchomiony jako użytkownik niebędący użytkownikiem głównym (co należy), należy określić wewnątrz kontekstu zabezpieczeń zasobnika, aby wolumin był czytelny i zapisywalny przez `fsGroup` zasobnik. To wymaganie opisano bardziej szczegółowo [tutaj.](https://kubernetes.io/docs/tasks/configure-pod-container/security-context/)

Jednak efektem ubocznym ustawienia jest to, że przy każdym zainstalowanym woluminie kubernetes musi rekursywnie i wszystkie pliki i katalogi wewnątrz woluminu — z kilkoma wyjątkami zanotowanych `fsGroup` `chown()` `chmod()` poniżej. Dzieje się tak nawet wtedy, gdy własność grupy woluminu jest już taka jak żądana, i może być dość kosztowna w przypadku większych woluminów z dużą ilością małych plików, co powoduje długie `fsGroup` uruchamianie zasobnika. Ten scenariusz był znanym problemem przed wersjami 1.20, a obejście polega na ustawieniu zasobnika uruchom jako głównego:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: security-context-demo
spec:
  securityContext:
    runAsUser: 0
    fsGroup: 0
```

Problem został rozwiązany przez platformę Kubernetes w wersji 1.20. Aby uzyskać więcej informacji, zobacz [Kubernetes 1.20: Granular Control of Volume Permission Changes (Kubernetes 1.20:](https://kubernetes.io/blog/2020/12/14/kubernetes-release-1.20-fsgroupchangepolicy-fsgrouppolicy/) szczegółowa kontrola nad zmianami uprawnień woluminu).


<!-- LINKS - internal -->

[aks-upgrade]: ./upgrade-cluster.md
[aks-cluster-autoscale]: ./cluster-autoscaler.md
[aks-advanced-networking]: ./configure-azure-cni.md
[aks-rbac-aad]: ./azure-ad-integration-cli.md
[node-updates-kured]: node-updates-kured.md
[aks-preview-cli]: /cli/azure/ext/aks-preview/aks
[az-aks-create]: /cli/azure/aks#az_aks_create
[aks-rm-template]: /azure/templates/microsoft.containerservice/2019-06-01/managedclusters
[aks-cluster-autoscaler]: cluster-autoscaler.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[aks-windows-cli]: windows-container-cli.md
[aks-windows-limitations]: ./windows-faq.md
[reservation-discounts]:../cost-management-billing/reservations/save-compute-costs-reservations.md
[api-server-authorized-ip-ranges]: ./api-server-authorized-ip-ranges.md
[multi-node-pools]: ./use-multiple-node-pools.md
[availability-zones]: ./availability-zones.md
[private-clusters]: ./private-clusters.md
[bcdr-bestpractices]: ./operator-best-practices-multi-region.md#plan-for-multiregion-deployment
[availability-zones]: ./availability-zones.md
[az-regions]: ../availability-zones/az-region.md
[uptime-sla]: ./uptime-sla.md

<!-- LINKS - external -->
[aks-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[auto-scaler]: https://github.com/kubernetes/autoscaler
[cordon-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[admission-controllers]: https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/
[private-clusters-github-issue]: https://github.com/Azure/AKS/issues/948
[csi-driver]: https://github.com/Azure/secrets-store-csi-driver-provider-azure
[vm-sla]: https://azure.microsoft.com/support/legal/sla/virtual-machines/
