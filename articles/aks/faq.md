---
title: Często zadawane pytania dotyczące usługi Azure Kubernetes Service (AKS)
description: Znajdź odpowiedzi na niektóre często zadawane pytania dotyczące usługi Azure Kubernetes Service (AKS).
ms.topic: conceptual
ms.date: 08/06/2020
ms.custom: references_regions
ms.openlocfilehash: 6d7ea48722e6604fe67d7a4ddcb12870623d9354
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101739631"
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>Często zadawane pytania dotyczące usługi Azure Kubernetes Service (AKS)

W tym artykule opisano często zadawane pytania dotyczące usługi Azure Kubernetes Service (AKS).

## <a name="which-azure-regions-currently-provide-aks"></a>Które regiony platformy Azure obecnie udostępniają AKS?

Aby uzyskać pełną listę dostępnych regionów, zobacz [AKS regiony i dostępność][aks-regions].

## <a name="can-i-spread-an-aks-cluster-across-regions"></a>Czy mogę rozłożyć klaster AKS w różnych regionach?

Nie. Klastry AKS są zasobami regionalnymi i nie mogą obejmować regionów. Zapoznaj się z [najlepszymi rozwiązaniami dotyczącymi ciągłości działania i odzyskiwania po awarii][bcdr-bestpractices] , aby uzyskać wskazówki dotyczące tworzenia architektury obejmującej wiele regionów.

## <a name="can-i-spread-an-aks-cluster-across-availability-zones"></a>Czy mogę rozłożyć klaster AKS w różnych strefach dostępności?

Tak. Klaster AKS można wdrożyć w jednej lub większej liczbie [stref dostępności][availability-zones] w [regionach, które je obsługują][az-regions].

## <a name="can-i-limit-who-has-access-to-the-kubernetes-api-server"></a>Czy mogę ograniczyć dostęp do serwera interfejsu API Kubernetes?

Tak. Dostępne są dwie opcje ograniczania dostępu do serwera interfejsu API:

- Jeśli chcesz zachować publiczny punkt końcowy dla serwera interfejsu API, Użyj adresów [IP autoryzowanych przez serwer API][api-server-authorized-ip-ranges] , ale Ogranicz dostęp do zestawu zaufanych zakresów adresów IP.
- Użyj [klastra prywatnego][private-clusters] , jeśli chcesz ograniczyć serwer API tak, aby był dostępny *tylko* z poziomu sieci wirtualnej.

## <a name="can-i-have-different-vm-sizes-in-a-single-cluster"></a>Czy mogę mieć różne rozmiary maszyn wirtualnych w jednym klastrze?

Tak, możesz użyć różnych rozmiarów maszyn wirtualnych w klastrze AKS, tworząc [wiele pul węzłów][multi-node-pools].

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>Czy aktualizacje zabezpieczeń są stosowane do węzłów agenta AKS?

Platforma Azure automatycznie stosuje poprawki zabezpieczeń do węzłów systemu Linux w klastrze w porze nocnego harmonogramu. Jednak użytkownik jest odpowiedzialny za zapewnienie, że te węzły systemu Linux zostaną uruchomione ponownie zgodnie z potrzebami. Istnieje kilka opcji ponownego uruchamiania węzłów:

- Ręcznie za pomocą Azure Portal lub interfejsu wiersza polecenia platformy Azure.
- Uaktualniając klaster AKS. Klaster uaktualnia [węzły Cordon i opróżniania][cordon-drain] automatycznie, a następnie przeniesie nowy węzeł w tryb online przy użyciu najnowszego obrazu Ubuntu i nowej wersji poprawki lub pomocniczej wersji Kubernetes. Aby uzyskać więcej informacji, zobacz [Uaktualnianie klastra AKS][aks-upgrade].
- Za pomocą [uaktualnienia obrazu węzła](node-image-upgrade.md).

### <a name="windows-server-nodes"></a>Węzły systemu Windows Server

W przypadku węzłów systemu Windows Server Windows Update nie jest automatycznie uruchamiane i stosowane są najnowsze aktualizacje. Zgodnie z regularnym harmonogramem Windows Update cyklu wydania i procesu weryfikacji należy przeprowadzić uaktualnienie do klastra i pul węzłów systemu Windows Server w klastrze AKS. Ten proces uaktualniania tworzy węzły z zainstalowanym najnowszym obrazem systemu Windows Server i poprawkami, a następnie usuwa starsze węzły. Aby uzyskać więcej informacji na temat tego procesu, zobacz [uaktualnianie puli węzłów w AKS][nodepool-upgrade].

## <a name="why-are-two-resource-groups-created-with-aks"></a>Dlaczego są dwie grupy zasobów utworzone za pomocą AKS?

AKS kompiluje się z wielu zasobów infrastruktury platformy Azure, w tym zestawów skalowania maszyn wirtualnych, sieci wirtualnych i dysków zarządzanych. Dzięki temu można korzystać z wielu podstawowych możliwości platformy Azure w ramach zarządzanego środowiska Kubernetes zapewnianego przez AKS. Na przykład większość typów maszyn wirtualnych platformy Azure może być używana bezpośrednio z AKS i Azure Reservations może być używana do automatycznego otrzymywania rabatów dla tych zasobów.

Aby włączyć tę architekturę, każde wdrożenie AKS obejmuje dwie grupy zasobów:

1. Należy utworzyć pierwszą grupę zasobów. Ta grupa zawiera tylko zasób usługi Kubernetes. Dostawca zasobów AKS automatycznie tworzy drugą grupę zasobów podczas wdrażania. Przykładem drugiej grupy zasobów jest *MC_myResourceGroup_myAKSCluster_eastus*. Aby uzyskać informacje na temat sposobu określania nazwy tej drugiej grupy zasobów, zobacz następną sekcję.
1. Druga grupa zasobów, znana jako *Grupa zasobów węzła*, zawiera wszystkie zasoby infrastruktury skojarzone z klastrem. Te zasoby obejmują maszyny wirtualne węzła Kubernetes, wirtualne sieci i magazyn. Domyślnie grupa zasobów węzła ma nazwę, taką jak *MC_myResourceGroup_myAKSCluster_eastus*. AKS automatycznie usuwa zasób węzła przy każdym usunięciu klastra, dlatego powinien być używany tylko w przypadku zasobów, które współużytkują cykl życia klastra.

## <a name="can-i-provide-my-own-name-for-the-aks-node-resource-group"></a>Czy mogę podać moją własną nazwę grupy zasobów węzła AKS?

Tak. Domyślnie AKS będzie nazwać węzeł grupy zasobów *MC_resourcegroupname_clustername_location*, ale możesz również podać własną nazwę.

Aby określić własną nazwę grupy zasobów, zainstaluj rozszerzenie [AKS-Preview][aks-preview-cli] interfejsu wiersza polecenia platformy Azure w wersji *0.3.2* lub nowszej. Podczas tworzenia klastra AKS za pomocą polecenia [AZ AKS Create][az-aks-create] Użyj `--node-resource-group` parametru i określ nazwę grupy zasobów. W przypadku wdrażania klastra AKS za pomocą [szablonu Azure Resource Manager][aks-rm-template] można zdefiniować nazwę grupy zasobów za pomocą właściwości *nodeResourceGroup* .

* Dodatkowa grupa zasobów jest automatycznie tworzona przez dostawcę zasobów platformy Azure we własnej subskrypcji.
* Możesz określić niestandardową nazwę grupy zasobów tylko podczas tworzenia klastra.

Podczas pracy z grupą zasobów węzła należy pamiętać, że nie można:

* Określ istniejącą grupę zasobów dla grupy zasobów węzła.
* Określ inną subskrypcję dla grupy zasobów węzła.
* Zmień nazwę grupy zasobów węzła po utworzeniu klastra.
* Określ nazwy zarządzanych zasobów w grupie zasobów węzła.
* Modyfikuj lub Usuń Tagi zarządzane przez platformę Azure w ramach grupy zasobów węzła. (Zobacz dodatkowe informacje w następnej sekcji).

## <a name="can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group"></a>Czy mogę modyfikować Tagi i inne właściwości zasobów AKS w grupie zasobów węzła?

W przypadku modyfikowania lub usuwania tagów utworzonych przez platformę Azure i innych właściwości zasobów w grupie zasobów węzła można uzyskać nieoczekiwane wyniki, takie jak skalowanie i uaktualnianie błędów. AKS umożliwia tworzenie i modyfikowanie tagów niestandardowych utworzonych przez użytkowników końcowych, a także dodawanie tych tagów podczas [tworzenia puli węzłów](use-multiple-node-pools.md#specify-a-taint-label-or-tag-for-a-node-pool). Możesz chcieć utworzyć lub zmodyfikować niestandardowe znaczniki, na przykład, aby przypisać jednostkę biznesową lub centrum kosztów. Można to również osiągnąć, tworząc zasady platformy Azure z zakresem w zarządzanej grupie zasobów.

Jednak modyfikowanie wszelkich **tagów utworzonych przez platformę Azure** w zasobach w ramach grupy zasobów węzła w klastrze AKS jest nieobsługiwaną akcją, która przerywa cel poziomu usługi (SLO). Aby uzyskać więcej informacji, zobacz [czy AKS oferuje umowę dotyczącą poziomu usług?](#does-aks-offer-a-service-level-agreement)

## <a name="what-kubernetes-admission-controllers-does-aks-support-can-admission-controllers-be-added-or-removed"></a>Jakie kontrolery Kubernetes Admission są obsługiwane przez AKS? Czy można dodać lub usunąć kontrolery do przyjmowania?

AKS obsługuje następujące [Kontrolery przyjęcia][admission-controllers]:

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

Obecnie nie można modyfikować listy kontrolerów przyjmowania w AKS.

## <a name="can-i-use-admission-controller-webhooks-on-aks"></a>Czy mogę używać elementów webhook kontrolera Admission w witrynie AKS?

Tak, możesz używać elementów webhook kontrolera Admission w witrynie AKS. Zaleca się wykluczenie wewnętrznej przestrzeni nazw AKS, która jest oznaczona za pomocą **etykiety płaszczyzny kontrolnej.** Na przykład dodając poniżej do konfiguracji elementu webhook:

```
namespaceSelector:
    matchExpressions:
    - key: control-plane
      operator: DoesNotExist
```

AKS zapory serwer API ruch wychodzący, aby Twoje elementy webhook kontrolera przyjęcia muszą być dostępne z poziomu klastra.

## <a name="can-admission-controller-webhooks-impact-kube-system-and-internal-aks-namespaces"></a>Czy można dołączać elementy webhook kontrolera, wpływ na przestrzenie nazw polecenia-system i internal AKS?

Aby chronić stabilność systemu i zapobiegać wpływowi niestandardowych kontrolerów przyjmowania na usługi wewnętrzne w systemie polecenia, przestrzeń nazw AKS ma **Enforcer**, które wykluczają wewnętrzne przestrzenie nazw polecenia-system i AKS. Ta usługa zapewnia, że niestandardowe kontrolery przyjmowania nie wpływają na usługi działające w systemie polecenia.

Jeśli masz krytyczny przypadek użycia, który ma zostać wdrożony w systemie polecenia (niezalecane), którego wymagasz, aby był objęty przez niestandardowy element webhook przyjęcia, możesz dodać etykietę lub adnotację w taki sposób, aby Enforcer ignorował ją.

Etykieta: ```"admissions.enforcer/disabled": "true"``` lub Adnotacja: ```"admissions.enforcer/disabled": true```

## <a name="is-azure-key-vault-integrated-with-aks"></a>Czy Azure Key Vault jest zintegrowany z AKS?

AKS nie jest obecnie natywnie zintegrowana z Azure Key Vault. Jednak [dostawca Azure Key Vault dla magazynu kluczy tajnych CSI][csi-driver] umożliwia bezpośrednią integrację z Kubernetesowych zasobników z Key Vaultymi kluczami tajnymi.

## <a name="can-i-run-windows-server-containers-on-aks"></a>Czy można uruchamiać kontenery systemu Windows Server na AKS?

Tak, kontenery systemu Windows Server są dostępne w systemie AKS. Aby uruchamiać kontenery systemu Windows Server w programie AKS, należy utworzyć pulę węzłów z systemem Windows Server jako system operacyjny gościa. Kontenery systemu Windows Server mogą korzystać tylko z systemu Windows Server 2019. Aby rozpocząć, zobacz [Tworzenie klastra AKS z pulą węzłów systemu Windows Server][aks-windows-cli].

Obsługa systemu Windows Server dla puli węzłów obejmuje pewne ograniczenia, które są częścią nadrzędnego serwera systemu Windows w projekcie Kubernetes. Aby uzyskać więcej informacji o tych ograniczeniach, zobacz [kontenery systemu Windows Server w ograniczeniach AKS][aks-windows-limitations].

## <a name="does-aks-offer-a-service-level-agreement"></a>Czy AKS oferuje umowę dotyczącą poziomu usług?

AKS zapewnia gwarancje SLA jako opcjonalną funkcję dodatku z umową [SLA][uptime-sla]dotyczącą czasu pracy. 

Bezpłatna jednostka SKU oferowana domyślnie nie ma skojarzonej *umowy* dotyczącej poziomu usług, ale ma *cel* poziomu usługi równy 99,5%. Mogło się zdarzyć, że problemy z łącznością są obserwowane w przypadku uaktualnień, węzłów underlay w złej kondycji, konserwacji platformy, aplikacji przenoszącej Przeciążenie serwera interfejsu API żądaniami itp. Jeśli obciążenie nie jest tolerowane w przypadku ponownego uruchomienia serwera interfejsu API, zalecamy użycie umowy SLA dotyczącej czasu pracy.

## <a name="can-i-apply-azure-reservation-discounts-to-my-aks-agent-nodes"></a>Czy mogę zastosować rabaty rezerwacji platformy Azure do węzłów agenta AKS?

Węzły agenta AKS są rozliczane jako standardowe maszyny wirtualne platformy Azure, więc w przypadku zakupu [rezerwacji platformy Azure][reservation-discounts] dla rozmiaru maszyny wirtualnej, który jest używany w programie AKS, te rabaty są automatycznie stosowane.

## <a name="can-i-movemigrate-my-cluster-between-azure-tenants"></a>Czy mogę przenieść lub migrować mój klaster między dzierżawami platformy Azure?

Przeniesienie klastra AKS między dzierżawcami nie jest obecnie obsługiwane.

## <a name="can-i-movemigrate-my-cluster-between-subscriptions"></a>Czy mogę przenieść klaster między subskrypcjami?

Przenoszenie klastrów między subskrypcjami nie jest obecnie obsługiwane.

## <a name="can-i-move-my-aks-clusters-from-the-current-azure-subscription-to-another"></a>Czy mogę przenieść klastry AKS z bieżącej subskrypcji platformy Azure do innej?

Przeniesienie klastra AKS i skojarzonych z nim zasobów między subskrypcjami platformy Azure nie jest obsługiwane.

## <a name="can-i-move-my-aks-cluster-or-aks-infrastructure-resources-to-other-resource-groups-or-rename-them"></a>Czy mogę przenieść klaster AKS lub zasoby infrastruktury AKS do innych grup zasobów lub zmienić ich nazwy?

Przeniesienie lub zmiana nazwy klastra AKS i skojarzonych z nim zasobów nie jest obsługiwane.

## <a name="why-is-my-cluster-delete-taking-so-long"></a>Dlaczego mój klaster usuwanie trwa długo?

Większość klastrów jest usuwana na żądanie użytkownika; w niektórych przypadkach, zwłaszcza w przypadku, gdy klienci korzystają z własnej grupy zasobów lub że usuwanie zadań RG może zająć trochę czasu lub niepowodzeniem. Jeśli masz problem z usuwaniem, sprawdź podwójne sprawdzenie, czy nie masz blokad w RG, że wszystkie zasoby spoza RG są nieskojarzone z RG i tak dalej.

## <a name="if-i-have-pod--deployments-in-state-nodelost-or-unknown-can-i-still-upgrade-my-cluster"></a>Jeśli mam element/wdrożenia w stanie "NodeLost" lub "nieznany" Czy mogę uaktualnić klaster?

Możesz, ale AKS nie jest to zalecane. Uaktualnienia należy wykonać, gdy stan klastra jest znany i w dobrej kondycji.

## <a name="if-i-have-a-cluster-with-one-or-more-nodes-in-an-unhealthy-state-or-shut-down-can-i-perform-an-upgrade"></a>Czy w przypadku klastra z co najmniej jednym węzłem w stanie złej kondycji można przeprowadzić uaktualnienie?

Nie, Usuń/Usuń wszystkie węzły w stanie awarii lub w inny sposób usunięte z klastra przed uaktualnieniem.

## <a name="i-ran-a-cluster-delete-but-see-the-error-errno-11001-getaddrinfo-failed"></a>Klaster został usunięty, ale zapoznaj się z błędem `[Errno 11001] getaddrinfo failed`

Najczęściej jest to spowodowane tym, że użytkownicy, którzy mają co najmniej jedną sieciową grupę zabezpieczeń (sieciowych grup zabezpieczeń), są nadal używani i skojarzeni z klastrem.  Usuń je i spróbuj ponownie wykonać operację usuwania.

## <a name="i-ran-an-upgrade-but-now-my-pods-are-in-crash-loops-and-readiness-probes-fail"></a>Uruchomiono uaktualnianie, ale teraz moje zasobniki są w pętli awarii, a sondy gotowości nie powiodły się?

Potwierdź, że nazwa główna usługi nie wygasła.  Zobacz: [AKS nazwy głównej usługi](./kubernetes-service-principal.md) i [AKS zaktualizuj poświadczenia](./update-credentials.md).

## <a name="my-cluster-was-working-but-suddenly-cant-provision-loadbalancers-mount-pvcs-etc"></a>Mój klaster działał, ale nagle nie może udostępnić LoadBalancers, zainstalować obwodów PVC itp.?

Potwierdź, że nazwa główna usługi nie wygasła.  Zobacz: [AKS nazwy głównej usługi](./kubernetes-service-principal.md)  i [AKS zaktualizuj poświadczenia](./update-credentials.md).

## <a name="can-i-scale-my-aks-cluster-to-zero"></a>Czy mogę skalować klaster AKS do zera?
Można całkowicie [zatrzymać uruchomiony klaster AKS](start-stop-cluster.md), co pozwala zaoszczędzić na odpowiednich kosztach obliczeniowych. Ponadto można [skalować lub automatycznie skalować wszystkie lub określone `User` Pule węzłów](scale-cluster.md#scale-user-node-pools-to-0) do wartości 0, zachowując tylko wymaganą konfigurację klastra.
Nie można bezpośrednio skalować [pul węzłów systemowych](use-system-pools.md) do zera.

## <a name="can-i-use-the-virtual-machine-scale-set-apis-to-scale-manually"></a>Czy można używać interfejsów API zestawu skalowania maszyn wirtualnych do skalowania ręcznie?

Nie, operacje skalowania przy użyciu interfejsów API zestawu skalowania maszyn wirtualnych nie są obsługiwane. Użyj interfejsów API AKS ( `az aks scale` ).

## <a name="can-i-use-virtual-machine-scale-sets-to-manually-scale-to-zero-nodes"></a>Czy można używać zestawów skalowania maszyn wirtualnych do ręcznego skalowania do zerowych węzłów?

Nie, operacje skalowania przy użyciu interfejsów API zestawu skalowania maszyn wirtualnych nie są obsługiwane. Można użyć interfejsu API AKS do skalowania do zera puli węzłów innych niż systemowe lub [zatrzymywać klaster](start-stop-cluster.md) .

## <a name="can-i-stop-or-de-allocate-all-my-vms"></a>Czy mogę zatrzymać lub cofnąć przydzielenie wszystkich moich maszyn wirtualnych?

Chociaż AKS ma mechanizmy odporności, aby przetrzymywać takie konfiguracje i odzyskiwać je z niego, nie jest to obsługiwana konfiguracja. Zamiast tego [Zatrzymaj klaster](start-stop-cluster.md) .

## <a name="can-i-use-custom-vm-extensions"></a>Czy mogę używać niestandardowych rozszerzeń maszyn wirtualnych?

Agent Log Analytics jest obsługiwany, ponieważ jest rozszerzeniem zarządzanym przez firmę Microsoft. W przeciwnym razie AKS jest usługą zarządzaną i manipulowanie zasobami IaaS nie jest obsługiwane. Aby zainstalować składniki niestandardowe, Użyj interfejsów API i mechanizmów Kubernetes. Na przykład aby zainstalować wymagane składniki, należy użyć DaemonSets.

## <a name="does-aks-store-any-customer-data-outside-of-the-clusters-region"></a>Czy AKS przechowuje dane klienta poza regionem klastra?

Funkcja umożliwiająca przechowywanie danych klienta w pojedynczym regionie jest obecnie dostępna tylko w regionie Azja Południowo-Wschodnia (Singapur Azja i Pacyfik) regionu geograficznego i Brazylia Południowa (stan Świętego Paulo). W przypadku wszystkich innych regionów dane klienta są przechowywane w lokalizacji geograficznej.

## <a name="are-aks-images-required-to-run-as-root"></a>Czy obrazy AKS są wymagane do uruchomienia jako główne?

Z wyjątkiem następujących dwóch obrazów AKS obrazy nie są wymagane do uruchamiania jako główne:

- *mcr.microsoft.com/oss/kubernetes/coredns*
- *mcr.microsoft.com/azuremonitor/containerinsights/ciprod*

## <a name="what-is-azure-cni-transparent-mode-vs-bridge-mode"></a>Co to jest tryb przezroczysty usługi Azure CNI a tryb mostku?

W programie v 1.2.0 Azure CNI będzie miał tryb przezroczysty jako domyślny dla wdrożeń z jedną dzierżawą w systemie Linux CNI. Tryb przezroczysty zastępuje tryb mostka. W tej sekcji omówiono różnice między obydwoma trybami i jakie są korzyści/ograniczenia dotyczące używania trybu przezroczystego w usłudze Azure CNI.

### <a name="bridge-mode"></a>Tryb mostka

Jak sugeruje nazwa, tryb mostka Azure CNI w sposób "just in Time" spowoduje utworzenie mostka L2 o nazwie "azure0". Wszystkie interfejsy pary po stronie hosta `veth` zostaną połączone z tym mostkiem. Dlatego Pod-Pod komunikację między MASZYNami wirtualnymi i ruch pozostały przez ten mostka. Dany mostek jest urządzeniem wirtualnym warstwy 2, które samodzielnie nie może odebrać ani przesłać żadnych informacji, chyba że wiąże się z nim co najmniej jedno rzeczywiste urządzenie. Z tego powodu eth0 maszyny wirtualnej z systemem Linux należy przekonwertować do mostka "azure0". Powoduje to utworzenie złożonej topologii sieci na maszynie wirtualnej z systemem Linux, a jako objaw CNI wymagało ponoszenia innych funkcji sieciowych, takich jak aktualizacja serwera DNS i tak dalej.

:::image type="content" source="media/faq/bridge-mode.png" alt-text="Topologia trybu mostku":::

Poniżej przedstawiono przykład sposobu, w jaki wygląd konfiguracji trasy IP w trybie mostka. Bez względu na liczbę elementów, które zawiera węzeł, dostępne będą tylko dwie trasy. Pierwszy z nich mówiąc, cały ruch, z wyjątkiem lokalnego w usłudze azure0, przejdzie do bramy domyślnej podsieci za pomocą interfejsu o adresie IP "src 10.240.0.4" (czyli podstawowego adresu IP węzła), a drugi z wymawianą "10.20. x. x" pod miejscem jądra dla jądra do podjęcia decyzji.

```bash
default via 10.240.0.1 dev azure0 proto dhcp src 10.240.0.4 metric 100
10.240.0.0/12 dev azure0 proto kernel scope link src 10.240.0.4
172.17.0.0/16 dev docker0 proto kernel scope link src 172.17.0.1 linkdown
root@k8s-agentpool1-20465682-1:/#
```

### <a name="transparent-mode"></a>Tryb przezroczysty
Tryb przezroczysty tworzy proste podejście do do konfigurowania sieci systemu Linux. W tym trybie usługa Azure CNI nie zmienia żadnych właściwości interfejsu eth0 na maszynie wirtualnej z systemem Linux. To minimalne podejście do zmiany właściwości sieci systemu Linux pomaga zredukować złożone problemy z przypadkami narożnymi, które mogą być podatne na klastry z trybem mostka. W trybie przezroczystym usługa Azure CNI utworzy i doda interfejsy pary pod względem siebie, `veth` które zostaną dodane do sieci hosta. Komunikacja między MASZYNami wirtualnymi w ramach komunikacji z usługą odbywa się za pomocą tras IP, które doda CNI. Zasadniczo komunikacja oparta na miejscu polega na przekierowaniu ruchu warstwowego na warstwę 3 i pod.

:::image type="content" source="media/faq/transparent-mode.png" alt-text="Topologia trybu przezroczystego":::

Poniżej znajduje się Przykładowa konfiguracja trasy IP trybu przezroczystego, w którym każdy interfejs w obu aspektach zostanie przyłączony do trasy statycznej, tak aby ruch z docelowym adresem IP, który znajduje się w obszarze, zostanie wysłany bezpośrednio do interfejsu pary po stronie hosta `veth` .

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

- Zapewnia środki zaradcze w przypadku `conntrack` równoległego wystąpienia wyścigu systemu DNS i unikaj 5-sekundowych problemów z opóźnieniem usługi DNS bez konieczności konfigurowania lokalnego systemu DNS w węźle. można nadal używać lokalnego systemu DNS w celu zapewnienia wydajności.
- Eliminuje początkowy 5-sekundowy czas opóźnienia usługi DNS CNI jest wprowadzany dzisiaj ze względu na konfigurację mostka "just in Time".
- Jednym z przypadków narożnych w trybie mostka jest to, że usługa Azure CNI nie może aktualizować niestandardowego serwera DNS listy użytkowników Dodaj do sieci wirtualnej lub karty sieciowej. Spowoduje to CNI pobrania tylko pierwszego wystąpienia listy serwerów DNS. Rozwiązano w trybie przezroczystym, ponieważ CNI nie zmienia żadnych właściwości eth0. Zobacz więcej [tutaj](https://github.com/Azure/azure-container-networking/issues/713).
- Zapewnia lepszą obsługę ruchu UDP i środki zaradcze dla burzy powodzi protokołu UDP po przeniesieniu limitu czasu ARP. W trybie mostka, gdy program Bridge nie zna adresu MAC miejsca docelowego pod względem komunikacji między maszynami wirtualnymi w sieci VMNetwork, to powoduje burzę pakietu na wszystkich portach. Rozwiązano w trybie przezroczystym, ponieważ w ścieżce nie ma żadnych urządzeń L2. Zobacz więcej [tutaj](https://github.com/Azure/azure-container-networking/issues/704).
- Tryb przezroczysty działa lepiej w przypadku komunikacji między firmową maszyną wirtualną w zakresie przepływności i opóźnień w porównaniu z trybem mostka.

## <a name="how-to-avoid-permission-ownership-setting-slow-issues-when-the-volume-has-a-lot-of-files"></a>Jak uniknąć powolnych problemów z ustawieniem prawa własności, gdy wolumin zawiera wiele plików?

Tradycyjnie, jeśli na komputerze jest uruchomiony program jako użytkownik niebędący użytkownikiem głównym (który należy), należy określić `fsGroup` wewnątrz kontekstu zabezpieczeń na poziomie, aby umożliwić odczytywanie i zapisywanie woluminu przez. To wymaganie jest omówione bardziej szczegółowo w [tym miejscu](https://kubernetes.io/docs/tasks/configure-pod-container/security-context/).

Jednak po zainstalowaniu jednego z efektów ubocznych, `fsGroup` za każdym razem, gdy wolumin jest zainstalowany, Kubernetes musi rekursywnie `chown()` i `chmod()` wszystkie pliki i katalogi w woluminie — z kilkoma wyjątkami opisanymi poniżej. Dzieje się tak nawet wtedy, gdy własność grupy woluminu jest już zgodna z żądanym `fsGroup` i może być dość kosztowna w przypadku większych woluminów z dużą ilością małych plików, co powoduje, że uruchomienie na początku zajmuje dużo czasu. W tym scenariuszu występuje znany problem przed 1.20 v i obejściem jest ustawienie głównego elementu run as:

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

Problem został rozwiązany przez Kubernetes v 1.20, Przeczytaj [Kubernetes 1,20: szczegółową kontrolę nad zmianami uprawnień woluminu](https://kubernetes.io/blog/2020/12/14/kubernetes-release-1.20-fsgroupchangepolicy-fsgrouppolicy/) , aby uzyskać więcej szczegółów.


<!-- LINKS - internal -->

[aks-upgrade]: ./upgrade-cluster.md
[aks-cluster-autoscale]: ./cluster-autoscaler.md
[aks-advanced-networking]: ./configure-azure-cni.md
[aks-rbac-aad]: ./azure-ad-integration-cli.md
[node-updates-kured]: node-updates-kured.md
[aks-preview-cli]: /cli/azure/ext/aks-preview/aks
[az-aks-create]: /cli/azure/aks#az-aks-create
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
