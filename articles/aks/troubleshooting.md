---
title: Rozwiązywanie typowych problemów z usługą Azure Kubernetes
description: Dowiedz się, jak rozwiązywać typowe problemy związane z korzystaniem z usługi Azure Kubernetes Service (AKS)
services: container-service
ms.topic: troubleshooting
ms.date: 06/20/2020
ms.openlocfilehash: d15e381baf3abdb77f63b17cbd1d33b24f5d3321
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/03/2020
ms.locfileid: "93286765"
---
# <a name="aks-troubleshooting"></a>Rozwiązywanie problemów z usługą Azure Kubernetes Service

W przypadku tworzenia klastrów usługi Azure Kubernetes Service (AKS) lub zarządzania nimi czasami mogą wystąpić problemy. W tym artykule opisano niektóre typowe problemy i kroki rozwiązywania problemów.

## <a name="in-general-where-do-i-find-information-about-debugging-kubernetes-problems"></a>Ogólnie rzecz biorąc, gdzie można znaleźć informacje o problemach z debugowaniem Kubernetes?

Wypróbuj [oficjalny przewodnik dotyczący rozwiązywania problemów z klastrami Kubernetes](https://kubernetes.io/docs/tasks/debug-application-cluster/troubleshooting/).
Istnieje również [Przewodnik rozwiązywania problemów](https://github.com/feiskyer/kubernetes-handbook/blob/master/en/troubleshooting/index.md)Opublikowany przez inżyniera firmy Microsoft w celu rozwiązywania problemów z planami, węzłami, klastrami i innymi funkcjami.

## <a name="im-getting-a-quota-exceeded-error-during-creation-or-upgrade-what-should-i-do"></a>Otrzymuję błąd "Przekroczono limit przydziału" podczas tworzenia lub uaktualniania. Co mam zrobić? 

 [Zażądaj większej liczby rdzeni](../azure-portal/supportability/resource-manager-core-quotas-request.md).

## <a name="what-is-the-maximum-pods-per-node-setting-for-aks"></a>Co to jest ustawienie maksymalnej liczby elementów w poszczególnych węzłach dla AKS?

Ustawienie Maksymalna liczba sztuk na węzeł domyślnie jest 30, Jeśli klaster AKS jest wdrażany w Azure Portal.
Ustawienie maksymalny rozmiar poszczególnych węzłów domyślnie 110 w przypadku wdrażania klastra AKS w interfejsie wiersza polecenia platformy Azure. (Upewnij się, że używasz najnowszej wersji interfejsu wiersza polecenia platformy Azure). To ustawienie można zmienić przy użyciu `–-max-pods` flagi w `az aks create` poleceniu.

## <a name="im-getting-an-insufficientsubnetsize-error-while-deploying-an-aks-cluster-with-advanced-networking-what-should-i-do"></a>Otrzymuję błąd insufficientSubnetSize podczas wdrażania klastra AKS przy użyciu zaawansowanej sieci. Co mam zrobić?

Ten błąd wskazuje, że podsieć w użyciu dla klastra nie ma już dostępnych adresów IP w ramach CIDR dla pomyślnego przypisania zasobu. W przypadku klastrów korzystającą wtyczki kubenet wymaga wystarczającej przestrzeni adresowej IP dla każdego węzła w klastrze. W przypadku klastrów usługi Azure CNI wymagane jest wystarczające miejsce na adresy IP dla każdego węzła i znajdującego się w klastrze.
Przeczytaj więcej na temat [projektowania usługi Azure CNI, aby przypisać adresy IP do zasobników](configure-azure-cni.md#plan-ip-addressing-for-your-cluster).

Te błędy są również nawiązane w [diagnostyce AKS](./concepts-diagnostics.md) , która aktywnie przyniesie problemy, takie jak niewystarczająca ilość podsieci.

Następujące trzy przypadki (3) powodują niewystarczający rozmiar podsieci:

1. Skalowanie AKS lub AKS Nodepool
   1. Jeśli używasz korzystającą wtyczki kubenet, dzieje się tak, gdy wartość `number of free IPs in the subnet` jest **mniejsza od** `number of new nodes requested` .
   1. Jeśli używasz usługi Azure CNI, dzieje się tak, gdy wartość `number of free IPs in the subnet` jest **mniejsza od** `number of nodes requested times (*) the node pool's --max-pod value` .

1. Uaktualnienie AKS lub uaktualnienie Nodepool AKS
   1. Jeśli używasz korzystającą wtyczki kubenet, dzieje się tak, gdy wartość `number of free IPs in the subnet` jest **mniejsza niż** `number of buffer nodes needed to upgrade` .
   1. Jeśli używasz usługi Azure CNI, dzieje się tak, gdy wartość `number of free IPs in the subnet` jest **mniejsza od** `number of buffer nodes needed to upgrade times (*) the node pool's --max-pod value` .
   
   Domyślnie klastry AKS ustawiają maksymalną wartość (w buforze uaktualnienia) jedną (1), ale to zachowanie uaktualnienia można dostosować, ustawiając [maksymalną wartość przepięcia puli węzłów](upgrade-cluster.md#customize-node-surge-upgrade-preview) , która spowoduje zwiększenie liczby dostępnych adresów IP potrzebnych do przeprowadzenia uaktualnienia.

1. AKS Utwórz lub AKS Nodepool Dodaj
   1. Jeśli używasz korzystającą wtyczki kubenet, dzieje się tak, gdy wartość `number of free IPs in the subnet` jest **mniejsza niż** `number of nodes requested for the node pool` .
   1. Jeśli używasz usługi Azure CNI, dzieje się tak, gdy wartość `number of free IPs in the subnet` jest **mniejsza od** `number of nodes requested times (*) the node pool's --max-pod value` .

Poniższe środki zaradcze mogą być podejmowane przez utworzenie nowych podsieci. Uprawnienie do tworzenia nowej podsieci jest wymagane w celu ograniczenia ryzyka ze względu na niezdolność do aktualizowania zakresu CIDR istniejącej podsieci.

1. Odbuduj nową podsieć o większym zakresie CIDR wystarczającym dla celów operacji:
   1. Utwórz nową podsieć z nowym żądanym nienakładanym zakresem.
   1. Utwórz nowy nodepool w nowej podsieci.
   1. Opróżnij zasobniki ze starego nodepool znajdującego się w starej podsieci, aby zostać zastąpione.
   1. Usuń starą podsieć i stare nodepool.

## <a name="my-pod-is-stuck-in-crashloopbackoff-mode-what-should-i-do"></a>Mój pod jest zablokowany w trybie CrashLoopBackOff. Co mam zrobić?

Może istnieć różne przyczyny zablokowania w tym trybie. Możesz zajrzeć do:

* Samego siebie, przy użyciu `kubectl describe pod <pod-name>` .
* Dzienniki przy użyciu programu `kubectl logs <pod-name>` .

Aby uzyskać więcej informacji na temat rozwiązywania problemów, zobacz [debugowanie aplikacji](https://kubernetes.io/docs/tasks/debug-application-cluster/debug-application/#debugging-pods).

## <a name="im-receiving-tcp-timeouts-when-using-kubectl-or-other-third-party-tools-connecting-to-the-api-server"></a>Otrzymuję w `TCP timeouts` przypadku korzystania z programu `kubectl` lub innych narzędzi innych firm łączących się z serwerem interfejsu API
AKS ma płaszczyzny kontroli HA skalowanie w pionie zgodnie z liczbą rdzeni, aby zapewnić swoje cele poziomu usług (SLO) i umowy dotyczące poziomu usług (umowy SLA). Jeśli występują problemy z limitem czasu połączeń, zapoznaj się z poniższymi tematami:

- **Czy wszystkie polecenia interfejsu API są stale przekroczenia limitu czasu?** Jeśli jest to tylko kilka, na `tunnelfront` poziomie użytkownika lub `aks-link` pod, odpowiedzialne za komunikację płaszczyzny > węzła kontroli, może nie być w stanie uruchomienia. Upewnij się, że węzły obsługujące ten węzeł nie są nadmiernie wykorzystane lub nie są w mocy obciążeniowej. Rozważ przeniesienie ich do własnej [ `system` puli węzłów](use-system-pools.md).
- **Czy zostały otwarte wszystkie wymagane porty, nazwy FQDN i adresy IP odnotowane w dokumentacji [AKS ograniczenia ruchu wychodzącego](limit-egress-traffic.md)?** W przeciwnym razie wywołania kilku poleceń mogą zakończyć się niepowodzeniem.
- **Czy bieżący adres IP jest objęty [zakresem autoryzowanych adresów IP API](api-server-authorized-ip-ranges.md)?** Jeśli używasz tej funkcji, a adres IP nie należy do zakresów, Twoje wywołania zostaną zablokowane. 
- **Czy klient lub aplikacja nie wywołuje wywołań do serwera interfejsu API?** Upewnij się, że używasz zegarki zamiast częstego pobierania, a aplikacje innych firm nie wycieka takich wywołań. Na przykład usterka w mikserze Istio powoduje, że nowy serwer interfejsu API obserwuje połączenie, które jest tworzone za każdym razem, gdy wpis tajny jest odczytywany wewnętrznie. Ponieważ takie zachowanie odbywa się w regularnych odstępach czasu, Obejrzyj połączenia szybko, a ostatecznie serwer interfejsu API staje się przeciążony bez względu na wzorzec skalowania. https://github.com/istio/istio/issues/19481
- **Czy masz wiele wydań w ramach wdrożeń Helm?** Ten scenariusz może spowodować użycie zbyt dużej ilości pamięci w węzłach, a także dużej ilości `configmaps` , co może spowodować niepotrzebne skoki na serwerze interfejsu API. Rozważ skonfigurowanie `--history-max` at `helm init` i wykorzystanie nowego Helm 3. Więcej szczegółów na temat następujących problemów: 
    - https://github.com/helm/helm/issues/4821
    - https://github.com/helm/helm/issues/3500
    - https://github.com/helm/helm/issues/4543
- **[Czy ruch wewnętrzny między węzłami jest blokowany?](#im-receiving-tcp-timeouts-such-as-dial-tcp-node_ip10250-io-timeout)**

## <a name="im-receiving-tcp-timeouts-such-as-dial-tcp-node_ip10250-io-timeout"></a>Otrzymuję `TCP timeouts` , takie jak `dial tcp <Node_IP>:10250: i/o timeout`

Te limity czasu mogą być związane z ruchem wewnętrznym między blokowanymi węzłami. Sprawdź, czy ten ruch nie jest blokowany, na przykład przez [sieciowe grupy zabezpieczeń](concepts-security.md#azure-network-security-groups) w podsieci dla węzłów klastra.

## <a name="im-trying-to-enable-role-based-access-control-rbac-on-an-existing-cluster-how-can-i-do-that"></a>Próbuję włączyć Role-Based Access Control (RBAC) w istniejącym klastrze. Jak to zrobić?

Włączenie kontroli dostępu opartej na rolach (RBAC) w istniejących klastrach nie jest obecnie obsługiwane, należy ją ustawić podczas tworzenia nowych klastrów. RBAC jest domyślnie włączone w przypadku korzystania z interfejsu wiersza polecenia, portalu lub wersji API nowszej niż `2020-03-01` .

## <a name="i-created-a-cluster-with-rbac-enabled-and-now-i-see-many-warnings-on-the-kubernetes-dashboard-the-dashboard-used-to-work-without-any-warnings-what-should-i-do"></a>Został utworzony klaster z włączoną funkcją RBAC i teraz widzimy wiele ostrzeżeń na pulpicie nawigacyjnym Kubernetes. Pulpit nawigacyjny służący do pracy bez żadnych ostrzeżeń. Co mam zrobić?

Przyczyna ostrzeżeń to klaster z włączoną funkcją RBAC, a dostęp do pulpitu nawigacyjnego jest teraz ograniczony domyślnie. Ogólnie rzecz biorąc, to podejście jest dobrym rozwiązaniem, ponieważ domyślne narażenie pulpitu nawigacyjnego na wszystkich użytkowników klastra może prowadzić do zagrożeń bezpieczeństwa. Jeśli nadal chcesz włączyć pulpit nawigacyjny, postępuj zgodnie z instrukcjami w [tym wpisie w blogu](https://pascalnaber.wordpress.com/2018/06/17/access-dashboard-on-aks-with-rbac-enabled/).

## <a name="i-cant-get-logs-by-using-kubectl-logs-or-i-cant-connect-to-the-api-server-im-getting-error-from-server-error-dialing-backend-dial-tcp-what-should-i-do"></a>Nie mogę pobrać dzienników przy użyciu dzienników polecenia kubectl lub nie mogę nawiązać połączenia z serwerem interfejsu API. Otrzymuję komunikat "błąd z serwera: błąd podczas wybierania numeru zaplecza: wybierz TCP...". Co mam zrobić?

Upewnij się, że porty 22, 9000 i 1194 są otwarte w celu nawiązania połączenia z serwerem interfejsu API. Sprawdź, czy `tunnelfront` w `aks-link` przestrzeni nazw *polecenia systemu* przy użyciu polecenia jest uruchomiony lub pod `kubectl get pods --namespace kube-system` . Jeśli tak nie jest, Wymuś usunięcie elementu pod i zostanie on ponownie uruchomiony.

## <a name="im-getting-tls-client-offered-only-unsupported-versions-from-my-client-when-connecting-to-aks-api-what-should-i-do"></a>Otrzymuję `"tls: client offered only unsupported versions"` od mojego klienta podczas nawiązywania połączenia z interfejsem API usługi AKS. Co mam zrobić?

Minimalna obsługiwana wersja protokołu TLS w AKS to TLS 1,2.

## <a name="im-trying-to-upgrade-or-scale-and-am-getting-a-changing-property-imagereference-is-not-allowed-error-how-do-i-fix-this-problem"></a>Próbuję uaktualnić lub skalować i uzyskać `"Changing property 'imageReference' is not allowed"` błąd. Jak mogę rozwiązać ten problem?

Ten błąd może być spowodowany modyfikacją tagów w węzłach agenta wewnątrz klastra AKS. Modyfikowanie lub usuwanie tagów i innych właściwości zasobów w grupie zasobów MC_ * może prowadzić do nieoczekiwanych wyników. Zmiana zasobów w grupie MC_ * w klastrze AKS powoduje przerwanie celu poziomu usługi (SLO).

## <a name="im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed"></a>Otrzymuję błędy, które są w stanie awarii mojego klastra, a uaktualnienie lub skalowanie nie będzie działało, dopóki nie zostanie naprawione

*Ta pomoc w rozwiązywaniu problemów jest skierowana z https://aka.ms/aks-cluster-failed*

Ten błąd występuje, gdy klastry wchodzą w stan niepowodzenia z wielu powodów. Postępuj zgodnie z poniższymi instrukcjami, aby rozwiązać stan awarii klastra przed ponowną próbą wykonania poprzedniej operacji zakończonej niepowodzeniem:

1. Dopóki klaster nie jest w `failed` stanie, `upgrade` a `scale` operacje nie powiodą się. Typowe problemy główne i rozwiązania obejmują:
    * Skalowanie z **niewystarczającym limitem przydziału obliczeń (CRP)**. Aby rozwiązać ten problem, należy najpierw skalować klaster z powrotem do stanu stabilnego celu w ramach limitu przydziału. Następnie wykonaj następujące [kroki, aby zażądać zwiększenia przydziału obliczeń](../azure-portal/supportability/resource-manager-core-quotas-request.md) przed ponowną próbą skalowania w górę poza początkowymi limitami przydziału.
    * Skalowanie klastra przy użyciu zaawansowanej sieci i **niewystarczającej liczby zasobów podsieci (sieci)**. Aby rozwiązać ten problem, należy najpierw skalować klaster z powrotem do stanu stabilnego celu w ramach limitu przydziału. Następnie wykonaj [następujące kroki, aby zażądać zwiększenia przydziału zasobów](../azure-resource-manager/templates/error-resource-quota.md#solution) przed ponowną próbą skalowania w górę poza początkowymi limitami przydziału.
2. Po usunięciu podstawowej przyczyny niepowodzenia uaktualnienia klaster powinien działać w stanie sukces. Po zweryfikowaniu stanu, ponów próbę wykonania oryginalnej operacji.

## <a name="im-receiving-errors-when-trying-to-upgrade-or-scale-that-state-my-cluster-is-being-upgraded-or-has-failed-upgrade"></a>Otrzymuję błędy podczas próby uaktualnienia lub skalowania stanu, w którym mój klaster jest uaktualniany lub którego uaktualnienie nie powiodło się

*Ta pomoc w rozwiązywaniu problemów jest skierowana z https://aka.ms/aks-pending-upgrade*

 Nie można jednocześnie uaktualnić i skalować puli klastra lub węzła. W zamian każdy typ operacji musi zakończyć się w zasobie docelowym przed następnym żądaniem tego samego zasobu. W związku z tym operacje są ograniczone w przypadku wystąpienia lub próby aktywnego uaktualnienia lub operacji skalowania. 

Aby ułatwić zdiagnozowanie uruchomienia problemu w `az aks show -g myResourceGroup -n myAKSCluster -o table` celu pobrania szczegółowego stanu w klastrze. W oparciu o wynik:

* Jeśli klaster jest aktywnie uaktualniany, poczekaj na zakończenie operacji. Jeśli zakończyło się pomyślnie, ponów próbę wykonania poprzedniej operacji zakończonej niepowodzeniem.
* Jeśli uaktualnienie nie powiodło się, wykonaj kroki opisane w poprzedniej sekcji.

## <a name="can-i-move-my-cluster-to-a-different-subscription-or-my-subscription-with-my-cluster-to-a-new-tenant"></a>Czy mogę przenieść klaster na inną subskrypcję lub moją subskrypcję z moim klastrem do nowej dzierżawy?

Jeśli klaster AKS został przeniesiony do innej subskrypcji lub subskrypcji klastra do nowej dzierżawy, klaster nie będzie działać z powodu brakujących uprawnień do tożsamości klastra. Usługa **AKS nie obsługuje przesuwania klastrów między subskrypcjami ani dzierżawcami** z powodu tego ograniczenia.

## <a name="im-receiving-errors-trying-to-use-features-that-require-virtual-machine-scale-sets"></a>Otrzymuję błędy podczas próby użycia funkcji, które wymagają zestawów skalowania maszyn wirtualnych

*Ta pomoc w rozwiązywaniu problemów jest skierowana z aka.ms/aks-vmss-enablement*

Mogą pojawić się błędy wskazujące, że klaster AKS nie znajduje się na zestawie skalowania maszyn wirtualnych, na przykład w poniższym przykładzie:

**Nieznanej obiektu agentpool `<agentpoolname>` ustawił automatyczne skalowanie jako włączone, ale nie na Virtual Machine Scale Sets**

Funkcje takie jak automatyczne skalowanie klastra lub pule wielu węzłów wymagają zestawów skalowania maszyn wirtualnych `vm-set-type` .

Postępuj zgodnie z instrukcjami *przed rozpoczęciem* w odpowiednim dokumencie, aby poprawnie utworzyć klaster AKS:

* [Korzystanie z automatycznego skalowania klastra](cluster-autoscaler.md)
* [Tworzenie i używanie pul wielu węzłów](use-multiple-node-pools.md)
 
## <a name="what-naming-restrictions-are-enforced-for-aks-resources-and-parameters"></a>Jakie ograniczenia nazewnictwa są wymuszane dla zasobów AKS i parametrów?

*Ta pomoc w rozwiązywaniu problemów jest skierowana z aka.ms/aks-naming-rules*

Ograniczenia nazewnictwa są implementowane przez platformę Azure i AKS. Jeśli nazwa zasobu lub parametr przerywa jedno z tych ograniczeń, zwracany jest błąd, który prosi o podanie innych danych wejściowych. Stosuje się następujące typowe wskazówki dotyczące nazewnictwa:

* Nazwy klastrów muszą składać się z 1-63 znaków. Jedyne dozwolone znaki to litery, cyfry, łączniki i podkreślenia. Pierwszy i ostatni znak musi być literą lub cyfrą.
* Nazwa grupy zasobów Node/ *MC_* AKS łączy nazwę grupy zasobów i nazwę zasobu. Składnia autogenerata `MC_resourceGroupName_resourceName_AzureRegion` nie może zawierać więcej niż 80 znaków. W razie konieczności Zmniejsz długość nazwy grupy zasobów lub nazwę klastra AKS. Możesz również [dostosować nazwę grupy zasobów węzła](cluster-configuration.md#custom-resource-group-name)
* *DnsPrefix* musi zaczynać i kończyć się wartościami alfanumerycznymi i muszą zawierać od 1-54 znaków. Prawidłowe znaki to wartości alfanumeryczne i łączniki (-). *DnsPrefix* nie może zawierać znaków specjalnych, takich jak kropka (.).
* Nazwy puli węzłów AKS muszą składać się z małych liter i zawierać 1-11 znaków dla pul węzłów systemu Linux i 1-6 znaków dla pul węzłów Windows. Nazwa musi zaczynać się od litery i Jedyne dozwolone znaki to litery i cyfry.
* *Nazwa użytkownika administratora* , która ustawia nazwę użytkownika administratora dla węzłów systemu Linux, musi rozpoczynać się od litery, może zawierać tylko litery, cyfry, łączniki i podkreślenia, a maksymalna długość wynosząca 64 znaków.

## <a name="im-receiving-errors-when-trying-to-create-update-scale-delete-or-upgrade-cluster-that-operation-is-not-allowed-as-another-operation-is-in-progress"></a>Otrzymuję błędy podczas próby utworzenia, zaktualizowania, skalowania, usunięcia lub uaktualnienia klastra, ta operacja nie jest dozwolona, ponieważ inna operacja jest w toku.

*Ta pomoc w rozwiązywaniu problemów jest skierowana z aka.ms/aks-pending-operation*

Operacje klastra są ograniczone, gdy Poprzednia operacja jest nadal w toku. Aby pobrać szczegółowy stan klastra, użyj `az aks show -g myResourceGroup -n myAKSCluster -o table` polecenia. W razie konieczności Użyj własnej grupy zasobów i nazwy klastra AKS.

Na podstawie danych wyjściowych stanu klastra:

* Jeśli klaster jest w stanie aprowizacji *innym niż* *powodzenie lub nieudany* , poczekaj na zakończenie operacji ( *uaktualnianie/aktualizowanie/tworzenie/skalowanie/usuwanie/Migrowanie* ). Po zakończeniu poprzedniej operacji ponów próbę wykonania ostatniej operacji klastra.

* Jeśli uaktualnienie nie powiodło się, wykonaj kroki opisane w temacie jak pojawiają się [błędy, w których klaster jest w stanie niepowodzenia, a uaktualnienie lub skalowanie nie będzie działało do momentu jego naprawienia](#im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed).

## <a name="received-an-error-saying-my-service-principal-wasnt-found-or-is-invalid-when-i-try-to-create-a-new-cluster"></a>Odebrano błąd informujący o tym, że nie znaleziono jednostki usługi lub jest ona nieprawidłowa podczas próby utworzenia nowego klastra.

Podczas tworzenia klastra AKS do tworzenia zasobów w Twoim imieniu wymagana jest jednostka usługi lub zarządzana tożsamość. AKS może automatycznie utworzyć nową nazwę główną usługi podczas tworzenia klastra lub otrzymać istniejący. W przypadku korzystania z automatycznie utworzonego elementu Azure Active Directory musi propagować go do każdego regionu, aby tworzenie powiodło się. Gdy Propagacja trwa zbyt długo, klaster nie będzie mógł wykonać walidacji, ponieważ nie może znaleźć dostępnej nazwy głównej usługi. 

Wykonaj następujące obejścia tego problemu:
* Użyj istniejącej jednostki usługi, która została już rozpropagowana w regionach i istnieje do przekazania do AKS podczas tworzenia klastra.
* W przypadku korzystania ze skryptów automatyzacji należy dodać opóźnienia czasu między utworzeniem jednostki usługi i tworzeniem klastra AKS.
* Jeśli używasz Azure Portal, Wróć do ustawień klastra podczas tworzenia i ponów próbę wykonania strony walidacji po kilku minutach.

## <a name="im-getting-aadsts7000215-invalid-client-secret-is-provided-when-using-aks-api-what-should-i-do"></a>Otrzymuję `"AADSTS7000215: Invalid client secret is provided."` w przypadku korzystania z interfejsu API AKS. Co mam zrobić?

Zwykle jest to spowodowane wygaśnięciem poświadczeń jednostki usługi. [Zaktualizuj poświadczenia dla klastra AKS.](update-credentials.md)

## <a name="i-cant-access-my-cluster-api-from-my-automationdev-machinetooling-when-using-api-server-authorized-ip-ranges-how-do-i-fix-this-problem"></a>Nie można uzyskać dostępu do interfejsu API klastra z mojej maszyny/usługi Automation/narzędzia deweloperskiego, gdy używane są autoryzowane zakresy adresów IP serwera interfejsu API. Jak mogę rozwiązać ten problem?

Wymaga to `--api-server-authorized-ip-ranges` uwzględnienia adresów IP lub zakresów adresów IP automatyzacji/deweloperów/narzędzi, które są używane. Zapoznaj się z sekcją "jak znaleźć mój adres IP" w [bezpiecznym dostępie do serwera interfejsu API przy użyciu zakresów autoryzowanych adresów IP](api-server-authorized-ip-ranges.md).

## <a name="im-unable-to-view-resources-in-kubernetes-resource-viewer-in-azure-portal-for-my-cluster-configured-with-api-server-authorized-ip-ranges-how-do-i-fix-this-problem"></a>Nie mogę wyświetlić zasobów w programie Kubernetes Resource Viewer w Azure Portal dla mojego klastra skonfigurowanego przy użyciu zakresów adresów IP autoryzowanych serwerów interfejsu API. Jak mogę rozwiązać ten problem?

[Podgląd zasobów Kubernetes](kubernetes-portal.md) wymaga `--api-server-authorized-ip-ranges` uwzględnienia dostępu do lokalnego komputera klienckiego lub zakresu adresów IP (z którego portalu jest przeglądany). Zapoznaj się z sekcją "jak znaleźć mój adres IP" w [bezpiecznym dostępie do serwera interfejsu API przy użyciu zakresów autoryzowanych adresów IP](api-server-authorized-ip-ranges.md).

## <a name="im-receiving-errors-after-restricting-egress-traffic"></a>Otrzymuję błędy po ograniczeniu ruchu wychodzącego

W przypadku ograniczania ruchu wychodzącego z klastra AKS są [wymagane i opcjonalne zalecane](limit-egress-traffic.md) porty wyjściowe/reguły sieci oraz nazwy FQDN/aplikacji dla AKS. Jeśli ustawienia są w konflikcie z dowolną z tych reguł, niektóre `kubectl` polecenia nie będą działały prawidłowo. Podczas tworzenia klastra AKS mogą pojawić się także błędy.

Sprawdź, czy ustawienia nie powodują konfliktu z żadnym z wymaganych lub opcjonalnymi zalecanymi portami wychodzącymi/regułami sieciowymi oraz nazwą FQDN/reguł aplikacji.

## <a name="im-receiving-429---too-many-requests-errors"></a>Otrzymuję błędy "429 zbyt wiele żądań"

Gdy klaster Kubernetes na platformie Azure (AKS lub nie) często jest skalowany w górę lub w dół lub korzysta z automatycznego skalowania klastra (CA), te operacje mogą spowodować dużą liczbę wywołań HTTP, które z kolei przekroczą limit przydziału przypisanej subskrypcji wiodącej na awarię. Błędy będą wyglądać następująco

```
Service returned an error. Status=429 Code=\"OperationNotAllowed\" Message=\"The server rejected the request because too many requests have been received for this subscription.\" Details=[{\"code\":\"TooManyRequests\",\"message\":\"{\\\"operationGroup\\\":\\\"HighCostGetVMScaleSet30Min\\\",\\\"startTime\\\":\\\"2020-09-20T07:13:55.2177346+00:00\\\",\\\"endTime\\\":\\\"2020-09-20T07:28:55.2177346+00:00\\\",\\\"allowedRequestCount\\\":1800,\\\"measuredRequestCount\\\":2208}\",\"target\":\"HighCostGetVMScaleSet30Min\"}] InnerError={\"internalErrorCode\":\"TooManyRequestsReceived\"}"}
```

Te błędy ograniczania są szczegółowo opisane [tutaj](../azure-resource-manager/management/request-limits-and-throttling.md) i [here](../virtual-machines/troubleshooting/troubleshooting-throttling-errors.md)

Rekomendowanie od zespołu inżynierów AKS ma na celu upewnienie się, że jest uruchomiona wersja co najmniej 1.18. x, która zawiera wiele ulepszeń. Więcej informacji można znaleźć w tych ulepszeniach [tutaj](https://github.com/Azure/AKS/issues/1413) i [tutaj](https://github.com/kubernetes-sigs/cloud-provider-azure/issues/247).

Jeśli te błędy ograniczania są mierzone na poziomie subskrypcji, mogą być nadal wykonywane, jeśli:
- Istnieją aplikacje innych firm wykonujące żądania GET (np. Monitorowanie aplikacji itp...). Zaleca się zmniejszenie częstotliwości tych wywołań.
- W VMSS istnieje wiele klastrów AKS/nodepools. Typowym zaleceniem jest posiadanie mniej niż 20-30 klastrów w danej subskrypcji.

## <a name="my-clusters-provisioning-status-changed-from-ready-to-failed-with-or-without-me-performing-an-operation-what-should-i-do"></a>Stan aprowizacji mojego klastra został zmieniony z gotowe do niepowodzenia z lub bez wykonywania operacji. Co mam zrobić?

Jeśli stan aprowizacji klastra zmieni się z *gotowe* do *Niepowodzenie* z lub bez wykonywania jakichkolwiek operacji, ale aplikacje w klastrze są nadal uruchamiane, ten problem może zostać rozwiązany automatycznie przez usługę i nie ma to wpływu na aplikacje.

Jeśli stan aprowizacji klastra pozostaje *niepowodzeniem* lub aplikacje w klastrze przestaną działać, [Prześlij żądanie pomocy technicznej](https://azure.microsoft.com/support/options/#submit).


## <a name="azure-storage-and-aks-troubleshooting"></a>Rozwiązywanie problemów z usługą Azure Storage i AKS

### <a name="what-are-the-recommended-stable-versions-of-kubernetes-for-azure-disk"></a>Jakie są zalecane stabilne wersje programu Kubernetes for Azure Disk? 

| Wersja Kubernetes | Zalecana wersja |
|--|:--:|
| 1.12 | 1.12.9 lub nowszy |
| 1.13 | 1.13.6 lub nowszy |
| 1,14 | 1.14.2 lub nowszy |


### <a name="waitforattach-failed-for-azure-disk-parsing-devdiskazurescsi1lun1-invalid-syntax"></a>WaitForAttach dla dysku platformy Azure nie powiodło się: analiza "/dev/Disk/Azure/scsi1/lun1": nieprawidłowa składnia

W programie Kubernetes w wersji 1,10, MountVolume. WaitForAttach może zakończyć się niepowodzeniem przy ponownej instalacji dysku platformy Azure.

W systemie Linux może zostać wyświetlony nieprawidłowy błąd formatu DevicePath. Przykład:

```console
MountVolume.WaitForAttach failed for volume "pvc-f1562ecb-3e5f-11e8-ab6b-000d3af9f967" : azureDisk - Wait for attach expect device path as a lun number, instead got: /dev/disk/azure/scsi1/lun1 (strconv.Atoi: parsing "/dev/disk/azure/scsi1/lun1": invalid syntax)
  Warning  FailedMount             1m (x10 over 21m)   kubelet, k8s-agentpool-66825246-0  Unable to mount volumes for pod
```

W systemie Windows może zostać wyświetlony nieprawidłowy błąd numeru DevicePath (LUN). Przykład:

```console
Warning  FailedMount             1m    kubelet, 15282k8s9010    MountVolume.WaitForAttach failed for volume "disk01" : azureDisk - WaitForAttach failed within timeout node (15282k8s9010) diskId:(andy-mghyb
1102-dynamic-pvc-6c526c51-4a18-11e8-ab5c-000d3af7b38e) lun:(4)
```

Ten problem został rozwiązany w następujących wersjach programu Kubernetes:

| Wersja Kubernetes | Stała wersja |
|--|:--:|
| 1.10 | 1.10.2 lub nowszy |
| 1,11 | 1.11.0 lub nowszy |
| 1,12 i nowsze | Brak |


### <a name="failure-when-setting-uid-and-gid-in-mountoptions-for-azure-disk"></a>Niepowodzenie podczas ustawiania identyfikatorów UID i GID w mountOptions dla dysku platformy Azure

Dysk Azure domyślnie używa systemu plików ext4, XFS i mountOptions, takiego jak UID = x, GID = x, nie można ustawić w czasie instalacji. Na przykład jeśli podjęto próbę ustawienia mountOptions UID = 999, GID = 999, zobaczysz błąd, jak:

```console
Warning  FailedMount             63s                  kubelet, aks-nodepool1-29460110-0  MountVolume.MountDevice failed for volume "pvc-d783d0e4-85a1-11e9-8a90-369885447933" : azureDisk - mountDevice:FormatAndMount failed with mount failed: exit status 32
Mounting command: systemd-run
Mounting arguments: --description=Kubernetes transient mount for /var/lib/kubelet/plugins/kubernetes.io/azure-disk/mounts/m436970985 --scope -- mount -t xfs -o dir_mode=0777,file_mode=0777,uid=1000,gid=1000,defaults /dev/disk/azure/scsi1/lun2 /var/lib/kubelet/plugins/kubernetes.io/azure-disk/mounts/m436970985
Output: Running scope as unit run-rb21966413ab449b3a242ae9b0fbc9398.scope.
mount: wrong fs type, bad option, bad superblock on /dev/sde,
       missing codepage or helper program, or other error
```

Problem można rozwiązać, wykonując jedną z opcji:

* [Skonfiguruj kontekst zabezpieczeń pod kątem](https://kubernetes.io/docs/tasks/configure-pod-container/security-context/) ustawienia UID w runAsUser i GID w fsGroup. Na przykład następujące ustawienie ustawi polecenie Uruchom jako root, udostępnij je dla dowolnego pliku:

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

  >[!NOTE]
  > Ponieważ GID i UID są domyślnie instalowane jako root lub 0. Jeśli gid lub UID są ustawione jako spoza katalogu głównego, na przykład 1000, Kubernetes będzie używać `chown` do zmiany wszystkich katalogów i plików znajdujących się na tym dysku. Ta operacja może być czasochłonna i może spowodować, że instalacja dysku będzie bardzo niska.

* Użyj `chown` w initContainers, aby ustawić GID i UID. Przykład:

```yaml
initContainers:
- name: volume-mount
  image: busybox
  command: ["sh", "-c", "chown -R 100:100 /data"]
  volumeMounts:
  - name: <your data volume>
    mountPath: /data
```

### <a name="azure-disk-detach-failure-leading-to-potential-race-condition-issue-and-invalid-data-disk-list"></a>Błąd odłączania dysku platformy Azure, który prowadzi do potencjalnego problemu z sytuacją wyścigu i nieprawidłowa lista dysków danych

Gdy nie można odłączyć dysku z platformą Azure, zostanie ponowiona próba ponowienia przez sześć razy, aby odłączyć dysk przy użyciu wycofywania wykładniczego. Będzie również utrzymywać blokadę na poziomie węzła na liście dysków danych przez około 3 minuty. Jeśli lista dysków zostanie ręcznie zaktualizowana w tym czasie, spowoduje to, że lista dysków przechowywana przez blokadę na poziomie węzła nie będzie przestarzała i spowoduje niestabilność węzła.

Ten problem został rozwiązany w następujących wersjach programu Kubernetes:

| Wersja Kubernetes | Stała wersja |
|--|:--:|
| 1.12 | 1.12.9 lub nowszy |
| 1.13 | 1.13.6 lub nowszy |
| 1,14 | 1.14.2 lub nowszy |
| 1,15 i nowsze | Brak |

Jeśli używasz wersji programu Kubernetes, która nie ma rozwiązania tego problemu, a Twój węzeł ma przestarzałą listę dysków, możesz rozwiązać problem, odłączając wszystkie nieistniejące dyski z maszyny wirtualnej jako operację zbiorczą. **Pojedyncze odłączenie nieistniejących dysków może zakończyć się niepowodzeniem.**

### <a name="large-number-of-azure-disks-causes-slow-attachdetach"></a>Duża liczba dysków platformy Azure powoduje spowolnienie dołączania/odłączania

Gdy liczba operacji dołączania/odłączania dysku platformy Azure dla maszyny wirtualnej z jednym węzłem jest większa niż 10 lub większa niż 3 w przypadku kierowania pojedynczej puli zestawów skalowania maszyn wirtualnych, mogą one być wolniejsze niż oczekiwane. Ten problem jest znanym ograniczeniem i w tej chwili nie ma żadnych rozwiązań. [Element głosowy użytkownika do obsługi równoległego dołączania/odłączania poza liczbą.](https://feedback.azure.com/forums/216843-virtual-machines/suggestions/40444528-vmss-support-for-parallel-disk-attach-detach-for).

### <a name="azure-disk-detach-failure-leading-to-potential-node-vm-in-failed-state"></a>Niepowodzenie odłączenia dysku platformy Azure do potencjalnej maszyny wirtualnej węzła w stanie Niepowodzenie

W niektórych przypadkach, odłączenie dysku platformy Azure może zakończyć się częściowo niepowodzeniem i pozostawić maszynę wirtualną w stanie Niepowodzenie.

Ten problem został rozwiązany w następujących wersjach programu Kubernetes:

| Wersja Kubernetes | Stała wersja |
|--|:--:|
| 1.12 | 1.12.10 lub nowszy |
| 1.13 | 1.13.8 lub nowszy |
| 1,14 | 1.14.4 lub nowszy |
| 1,15 i nowsze | Brak |

Jeśli używasz wersji programu Kubernetes, która nie ma rozwiązania tego problemu, a stan węzła jest w stanie niepowodzenia, możesz rozwiązać problem, ręcznie aktualizując status maszyny wirtualnej przy użyciu jednego z poniższych elementów:

* W przypadku klastra opartego na zestawie dostępności:
    ```azurecli
    az vm update -n <VM_NAME> -g <RESOURCE_GROUP_NAME>
    ```

* W przypadku klastra opartego na VMSS:
    ```azurecli
    az vmss update-instances -g <RESOURCE_GROUP_NAME> --name <VMSS_NAME> --instance-id <ID>
    ```

## <a name="azure-files-and-aks-troubleshooting"></a>Azure Files i rozwiązywanie problemów z AKS

### <a name="what-are-the-recommended-stable-versions-of-kubernetes-for-azure-files"></a>Jakie są zalecane stabilne wersje programu Kubernetes for Azure Files?
 
| Wersja Kubernetes | Zalecana wersja |
|--|:--:|
| 1.12 | 1.12.6 lub nowszy |
| 1.13 | 1.13.4 lub nowszy |
| 1,14 | 1.14.0 lub nowszy |

### <a name="what-are-the-default-mountoptions-when-using-azure-files"></a>Jakie są domyślne mountOptions w przypadku korzystania z Azure Files?

Zalecane ustawienia:

| Wersja Kubernetes | wartość parametru FileMode i dirMode|
|--|:--:|
| 1.12.0 - 1.12.1 | 0755 |
| 1.12.2 i nowsze | 0777 |

Opcje instalacji można określić w obiekcie klasy magazynu. W poniższym przykładzie są ustawiane *0777* :

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
  - mfsymlinks
  - nobrl
  - cache=none
parameters:
  skuName: Standard_LRS
```

Niektóre dodatkowe użyteczne ustawienia *mountOptions* :

* *mfsymlinks* Azure Files instalacji (CIFS) obsługuje linki symboliczne
* *nobrl* uniemożliwi wysyłanie żądań blokady zakresu bajtów do serwera. To ustawienie jest niezbędne w przypadku niektórych aplikacji, które są przerywane przez obowiązkowe zablokowanie zakresu bajtów w stylu CIFS. Większość serwerów CIFS nie obsługuje jeszcze żądań zablokowania zakresu bajtów doradczych. Jeśli nie korzystasz z *nobrl* , aplikacje, które są przerywane przez obowiązkowe blokowanie zakresu bajtów w stylu CIFS mogą powodować komunikaty o błędach podobne do:
    ```console
    Error: SQLITE_BUSY: database is locked
    ```

### <a name="error-could-not-change-permissions-when-using-azure-files"></a>Błąd "nie można zmienić uprawnień" podczas korzystania z Azure Files

Po uruchomieniu programu PostgreSQL na wtyczce Azure Files może zostać wyświetlony komunikat o błędzie podobny do:

```console
initdb: could not change permissions of directory "/var/lib/postgresql/data": Operation not permitted
fixing permissions on existing directory /var/lib/postgresql/data
```

Ten błąd jest spowodowany przez wtyczkę Azure Files przy użyciu protokołu CIFS/SMB. W przypadku korzystania z protokołu CIFS/SMB nie można zmienić uprawnień plików i katalogów po zainstalowaniu.

Aby rozwiązać ten problem, użyj *ścieżki podrzędnej* razem z wtyczką dysku platformy Azure. 

> [!NOTE] 
> W przypadku typu dysku ext3/4 istnieje utracony i znaleziony katalog po sformatowaniu dysku.

### <a name="azure-files-has-high-latency-compared-to-azure-disk-when-handling-many-small-files"></a>Azure Files ma duże opóźnienie w porównaniu do dysku platformy Azure w przypadku obsługi wielu małych plików

W niektórych przypadkach, takich jak obsługa wielu małych plików, podczas porównywania z dyskiem platformy Azure mogą wystąpić duże Azure Files opóźnienia.

### <a name="error-when-enabling-allow-access-allow-access-from-selected-network-setting-on-storage-account"></a>Błąd podczas włączania ustawienia "Zezwalaj na dostęp z wybranej sieci" na koncie magazynu

Jeśli włączysz opcję *Zezwalaj na dostęp z wybranej sieci* na koncie magazynu używanym do dynamicznego inicjowania obsługi administracyjnej w programie AKS, zostanie wyświetlony komunikat o błędzie, gdy AKS utworzy udział plików:

```console
persistentvolume-controller (combined from similar events): Failed to provision volume with StorageClass "azurefile": failed to create share kubernetes-dynamic-pvc-xxx in account xxx: failed to create file share, err: storage: service returned error: StatusCode=403, ErrorCode=AuthorizationFailure, ErrorMessage=This request is not authorized to perform this operation.
```

Ten błąd jest spowodowany tym, że Kubernetes *persistentvolume — kontroler* nie znajduje się w sieci wybranej, gdy ustawienie *Zezwalaj na dostęp z wybranej sieci*.

Problem można rozwiązać, korzystając [z inicjowania obsługi statycznej z Azure Files](azure-files-volume.md).

### <a name="azure-files-fails-to-remount-in-windows-pod"></a>Nie można ponownie zainstalować Azure Files w systemie Windows pod

Jeśli system Windows pod Azure Files instalacji zostanie usunięty, a następnie zaplanowane do ponownego utworzenia w tym samym węźle, instalacja zakończy się niepowodzeniem. Ten błąd wynika z faktu, że `New-SmbGlobalMapping` polecenie kończy się niepowodzeniem, ponieważ instalacja Azure Files jest już zainstalowana w węźle.

Na przykład może zostać wyświetlony komunikat o błędzie podobny do:

```console
E0118 08:15:52.041014    2112 nestedpendingoperations.go:267] Operation for "\"kubernetes.io/azure-file/42c0ea39-1af9-11e9-8941-000d3af95268-pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\" (\"42c0ea39-1af9-11e9-8941-000d3af95268\")" failed. No retries permitted until 2019-01-18 08:15:53.0410149 +0000 GMT m=+732.446642701 (durationBeforeRetry 1s). Error: "MountVolume.SetUp failed for volume \"pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\" (UniqueName: \"kubernetes.io/azure-file/42c0ea39-1af9-11e9-8941-000d3af95268-pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\") pod \"deployment-azurefile-697f98d559-6zrlf\" (UID: \"42c0ea39-1af9-11e9-8941-000d3af95268\") : azureMount: SmbGlobalMapping failed: exit status 1, only SMB mount is supported now, output: \"New-SmbGlobalMapping : Generic failure \\r\\nAt line:1 char:190\\r\\n+ ... ser, $PWord;New-SmbGlobalMapping -RemotePath $Env:smbremotepath -Cred ...\\r\\n+                 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\\r\\n    + CategoryInfo          : NotSpecified: (MSFT_SmbGlobalMapping:ROOT/Microsoft/...mbGlobalMapping) [New-SmbGlobalMa \\r\\n   pping], CimException\\r\\n    + FullyQualifiedErrorId : HRESULT 0x80041001,New-SmbGlobalMapping\\r\\n \\r\\n\""
```

Ten problem został rozwiązany w następujących wersjach programu Kubernetes:

| Wersja Kubernetes | Stała wersja |
|--|:--:|
| 1.12 | 1.12.6 lub nowszy |
| 1.13 | 1.13.4 lub nowszy |
| 1,14 i nowsze | Brak |

### <a name="azure-files-mount-fails-because-of-storage-account-key-changed"></a>Instalacja Azure Files nie powiodła się z powodu zmiany klucza konta magazynu

Jeśli klucz konta magazynu został zmieniony, mogą pojawić się błędy instalacji Azure Files.

Możesz zmniejszyć wartość ręcznie aktualizując `azurestorageaccountkey` pole ręcznie w kluczu tajnym systemu Azure za pomocą klucza konta magazynu szyfrowanego algorytmem Base64.

Aby zakodować klucz konta magazynu w formacie Base64, można użyć programu `base64` . Przykład:

```console
echo X+ALAAUgMhWHL7QmQ87E1kSfIqLKfgC03Guy7/xk9MyIg2w4Jzqeu60CVw2r/dm6v6E0DWHTnJUEJGVQAoPaBc== | base64
```

Aby zaktualizować plik tajny platformy Azure, użyj programu `kubectl edit secret` . Przykład:

```console
kubectl edit secret azure-storage-account-{storage-account-name}-secret
```

Po kilku minutach węzeł agenta ponowi próbę instalacji pliku platformy Azure przy użyciu zaktualizowanego klucza magazynu.


### <a name="cluster-autoscaler-fails-to-scale-with-error-failed-to-fix-node-group-sizes"></a>Skalowanie automatycznego skalowania klastra nie powiodło się z powodu błędu nie można naprawić rozmiarów grup węzłów

Jeśli klaster automatycznego skalowania nie jest skalowany w górę i w dół, a w [dziennikach automatycznego skalowania klastra][view-master-logs]zostanie wyświetlony komunikat o błędzie podobny do poniższego.

```console
E1114 09:58:55.367731 1 static_autoscaler.go:239] Failed to fix node group sizes: failed to decrease aks-default-35246781-vmss: attempt to delete existing nodes
```

Ten błąd występuje ze względu na sytuację wyścigu dla nadrzędnego skalowania klastra. W takim przypadku automatyczne skalowanie klastra ma inną wartość niż ta, która rzeczywiście znajduje się w klastrze. Aby skorzystać z tego stanu, wyłącz i ponownie Włącz [Automatyczne skalowanie klastra][cluster-autoscaler].

### <a name="slow-disk-attachment-getazuredisklun-takes-10-to-15-minutes-and-you-receive-an-error"></a>Wolne miejsce na dysku, GetAzureDiskLun trwa 10 do 15 minut i występuje błąd

W wersji Kubernetes **starszej niż 1.15.0** może zostać wyświetlony błąd, taki jak **błąd WaitForAttach nie można znaleźć jednostki LUN dla dysku**.  Obejście tego problemu ma na celu odczekanie około 15 minut, a następnie ponów próbę.


### <a name="why-do-upgrades-to-kubernetes-116-fail-when-using-node-labels-with-a-kubernetesio-prefix"></a>Dlaczego uaktualnienia do Kubernetes 1,16 nie powiodły się podczas używania etykiet węzłów z prefiksem kubernetes.io

Od Kubernetes [1,16](https://v1-16.docs.kubernetes.io/docs/setup/release/notes/) [tylko zdefiniowany podzestaw etykiet z prefiksem Kubernetes.IO](https://github.com/kubernetes/enhancements/blob/master/keps/sig-auth/0000-20170814-bounding-self-labeling-kubelets.md#proposal) może być stosowany przez kubelet do węzłów. AKS nie może usunąć aktywnych etykiet w Twoim imieniu bez zgody, ponieważ może to spowodować przestoje związane z obciążeniami.

W związku z tym, aby uniknąć tego problemu, możesz:

1. Uaktualnij płaszczyznę kontroli klastra do wersji 1,16 lub nowszej
2. Dodaj nową nodepoool na 1,16 lub wyższej bez nieobsługiwanych etykiet kubernetes.io
3. Usuń starszą nodepool

AKS bada zdolność do mutacji aktywnych etykiet na nodepool w celu usprawnienia tego ograniczenia.



<!-- LINKS - internal -->
[view-master-logs]: view-master-logs.md
[cluster-autoscaler]: cluster-autoscaler.md
