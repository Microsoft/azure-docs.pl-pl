---
title: Zasady pomocy technicznej dla usługi Azure Kubernetes Service (AKS)
description: Dowiedz się więcej na temat zasad pomocy technicznej usługi Azure Kubernetes Service (AKS), współdzielonej odpowiedzialności oraz funkcji dostępnych w wersji zapoznawczej (lub Alpha lub beta).
services: container-service
ms.topic: article
ms.date: 09/18/2020
ms.openlocfilehash: 86b1c0bba30b41a2ee17cfbdf05286c4d2b3fb8a
ms.sourcegitcommit: b437bd3b9c9802ec6430d9f078c372c2a411f11f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91892714"
---
# <a name="support-policies-for-azure-kubernetes-service"></a>Zasady pomocy technicznej dla usługi Azure Kubernetes Service

Ten artykuł zawiera szczegółowe informacje na temat zasad i ograniczeń pomocy technicznej dla usługi Azure Kubernetes Service (AKS). W tym artykule opisano również szczegóły zarządzania węzłami agentów, zarządzane składniki płaszczyzny kontroli, składniki Open-Source innych firm oraz zarządzanie zabezpieczeniami i poprawkami.

## <a name="service-updates-and-releases"></a>Aktualizacje i wydania usługi

* Aby uzyskać informacje o wersji, zobacz [AKS informacji o wersji](https://github.com/Azure/AKS/releases).
* Aby uzyskać informacje na temat funkcji dostępnych w wersji zapoznawczej, zobacz [AKS Preview Features and pokrewne projekty](https://awesomeopensource.com/projects/aks?categoryPage=11).

## <a name="managed-features-in-aks"></a>Funkcje zarządzane w programie AKS

Podstawowe składniki infrastruktury jako usługi (IaaS), takie jak składniki obliczeniowe lub sieci, umożliwiają dostęp do kontrolek niskiego poziomu i opcji dostosowywania. Z kolei AKS udostępnia wdrożenie gotowe Kubernetes, które zapewnia wspólny zestaw konfiguracji i możliwości, które są potrzebne dla klastra. Jako użytkownik AKS masz ograniczone opcje dostosowywania i wdrażania. W programie Exchange nie trzeba bezpośrednio martwić się o klastry Kubernetes ani zarządzać nimi.

Dzięki AKS uzyskuje się w pełni zarządzaną *płaszczyznę kontroli*. Płaszczyzna kontroli zawiera wszystkie składniki i usługi potrzebne do działania i udostępnienia klastrów Kubernetes użytkownikom końcowym. Wszystkie składniki Kubernetes są utrzymywane i obsługiwane przez firmę Microsoft.

Firma Microsoft zarządza następującymi składnikami i monitoruje je za pomocą okienka sterowania:

* Serwery interfejsu API Kubelet lub Kubernetes
* Etcd lub zgodny magazyn wartości klucz-wartość, zapewniający Quality of Service (QoS), skalowalność i środowisko uruchomieniowe
* Usługi DNS (na przykład polecenia-DNS lub CoreDNS)
* Kubernetes serwer proxy lub sieci
* Wszelkie dodatkowe dodatki lub składniki systemowe działające w przestrzeni nazw polecenia-system

AKS to rozwiązanie typu platforma jako usługa (PaaS). Niektóre składniki, takie jak węzły agentów, mają *wspólną odpowiedzialność*, w której użytkownicy muszą pomóc w utrzymaniu klastra AKS. Dane wejściowe użytkownika są wymagane na przykład w celu zastosowania poprawki zabezpieczeń systemu operacyjnego węzła agenta.

Usługi są *zarządzane* w sensie, że firma Microsoft i zespół AKS wdrażają, działają i są odpowiedzialne za dostępność i funkcjonalność usługi. Klienci nie mogą zmieniać tych składników zarządzanych. Firma Microsoft ogranicza dostosowanie w celu zapewnienia spójnego i skalowalnego środowiska użytkownika. Aby uzyskać w pełni dostosowywalne rozwiązanie, zobacz [aparat AKS](https://github.com/Azure/aks-engine).

## <a name="shared-responsibility"></a>Wspólna odpowiedzialność

Po utworzeniu klastra należy zdefiniować węzły agenta Kubernetes tworzone przez AKS. Twoje obciążenia są wykonywane na tych węzłach.

Ponieważ węzły agentów wykonują kod prywatny i przechowują poufne dane, pomoc techniczna firmy Microsoft mogą uzyskać do nich dostęp tylko w bardzo ograniczony sposób. Pomoc techniczna firmy Microsoft nie może zalogować się do programu, wykonywać poleceń w programie lub wyświetlić dzienników dla tych węzłów bez uprawnień lub pomocy.

Wszelkie modyfikacje wykonywane bezpośrednio w węzłach agenta przy użyciu dowolnego z interfejsów API IaaS są renderowane jako nieobsługiwane przez klaster. Wszelkie zmiany wprowadzone w węzłach agenta muszą zostać wykonane przy użyciu natywnych mechanizmów Kubernetes, takich jak `Daemon Sets` .

Podobnie, chociaż można dodać dowolne metadane do klastra i węzłów, takie jak Tagi i etykiety, zmiana dowolnego z tworzonych metadanych systemu spowoduje nieobsługiwany klaster.

## <a name="aks-support-coverage"></a>AKS pomocy technicznej

Firma Microsoft zapewnia pomoc techniczną dla następujących przykładów:

* Łączność ze wszystkimi składnikami Kubernetes obsługiwanymi przez usługę Kubernetes, takimi jak serwer interfejsu API.
* Zarządzanie, czas pracy, QoS i operacje usług Kubernetes kontroli warstwy (na przykład: płaszczyzna kontroli Kubernetes, serwer API, etcd i coreDNS).
* Etcd magazynu danych. Obsługa obejmuje automatyczne i przezroczyste kopie zapasowe wszystkich danych etcd co 30 minut w przypadku planowania awarii i przywracania stanu klastra. Te kopie zapasowe nie są bezpośrednio dostępne dla Ciebie ani żadnych użytkowników. Zapewniają one niezawodność i spójność danych. Etcd. Funkcja wycofywania lub przywracania na żądanie nie jest obsługiwana w ramach funkcji.
* Wszystkie punkty integracji w sterowniku dostawcy chmury platformy Azure dla Kubernetes. Obejmują one integrację z innymi usługami platformy Azure, takimi jak moduły równoważenia obciążenia, woluminy trwałe lub sieci (Kubernetes i Azure CNI).
* Pytania lub problemy związane z dostosowywaniem składników płaszczyzny kontroli, takich jak Kubernetes API Server, etcd i coreDNS.
* Problemy dotyczące sieci, takie jak Azure CNI, korzystającą wtyczki kubenet lub inne problemy z dostępem do sieci i funkcjami. Problemy mogą obejmować rozpoznawanie nazw DNS, utratę pakietów, Routing i tak dalej. Firma Microsoft obsługuje różne scenariusze sieci:
  * Korzystającą wtyczki kubenet i Azure CNI przy użyciu zarządzanych sieci wirtualnych lub z niestandardowymi (ich własnymi) podsieciami.
  * Łączność z innymi usługami i aplikacjami platformy Azure
  * Konfiguracje usług przychodzących i przychodzących lub modułów równoważenia obciążenia
  * Wydajność i opóźnienie sieci


> [!NOTE]
> Wszystkie akcje klastra podejmowane przez firmę Microsoft/AKS są realizowane przy użyciu zgody użytkownika w ramach wbudowanej roli Kubernetes `aks-service` i wbudowanego powiązania roli `aks-service-rolebinding` . Ta rola umożliwia AKS Rozwiązywanie problemów z klastrem i diagnozowanie problemów, ale nie może modyfikować uprawnień ani tworzyć ról ani powiązań ról ani innych akcji o wysokim poziomie uprawnień. Dostęp do roli jest włączony tylko w ramach aktywnych biletów pomocy technicznej z dostępem just-in-Time (JIT).

Firma Microsoft nie zapewnia pomocy technicznej dla następujących przykładów:

* Pytania dotyczące korzystania z programu Kubernetes. Na przykład pomoc techniczna firmy Microsoft nie zawiera wskazówek na temat tworzenia niestandardowych kontrolerów transferu danych przychodzących, korzystania z obciążeń aplikacji lub pakietów oprogramowania lub narzędzi innych firm lub Open Source.
  > [!NOTE]
  > Pomoc techniczna firmy Microsoft może zalecić działanie klastra AKS, dostosowanie i dostrajanie (na przykład problemy z operacjami Kubernetes i procedurami).
* Projekty typu "open source" innych firm, które nie są dostarczane jako część płaszczyzny kontroli Kubernetes ani wdrożone z klastrami AKS. Te projekty mogą obejmować Istio, Helm, wysłannika lub inne.
  > [!NOTE]
  > Firma Microsoft może zapewnić najlepszą pomoc techniczną dla projektów typu "open source" innych firm, takich jak Helm. Jeśli narzędzie Open-Source innej firmy integruje się z dostawcą usług w chmurze Kubernetes systemu Azure lub innymi błędami specyficznymi dla AKS, firma Microsoft obsługuje przykłady i aplikacje z dokumentacji firmy Microsoft.
* Oprogramowanie dotyczące oprogramowania zamkniętego innej firmy. To oprogramowanie może obejmować narzędzia do skanowania zabezpieczeń i urządzenia sieciowe lub oprogramowanie.
* Dostosowania sieci inne niż wymienione w [dokumentacji AKS](./index.yml).


## <a name="aks-support-coverage-for-agent-nodes"></a>AKS obsługi dla węzłów agentów

### <a name="microsoft-responsibilities-for-aks-agent-nodes"></a>Obowiązki firmy Microsoft dotyczące węzłów agenta AKS

Firma Microsoft i użytkownicy współdzielą odpowiedzialność za węzły agenta Kubernetes, gdzie:

* Podstawowy obraz systemu operacyjnego ma wymagane Dodatki (na przykład monitorowanie i agenci sieci).
* Węzły agentów automatycznie odbierają poprawki systemu operacyjnego.
* Problemy ze składnikami płaszczyzny sterowania Kubernetes, które działają w węzłach agenta, są automatycznie korygowane. Poniższe składniki obejmują:
  * `Kube-proxy`
  * Tunele sieciowe dostarczające ścieżki komunikacji do składników głównych Kubernetes
  * `Kubelet`
  * `Moby` lub `ContainerD`

> [!NOTE]
> Jeśli węzeł agenta nie działa, AKS może ponownie uruchomić poszczególne składniki lub cały węzeł agenta. Te operacje ponownego uruchamiania są zautomatyzowane i zapewniają automatyczne korygowanie typowych problemów. Jeśli chcesz dowiedzieć się więcej o mechanizmach autokorygowania, zobacz [Autonaprawa węzła](node-auto-repair.md) .

### <a name="customer-responsibilities-for-aks-agent-nodes"></a>Obowiązki klienta dotyczące węzłów agenta AKS

Firma Microsoft udostępnia poprawki i nowe obrazy dla węzłów obrazów co tydzień, ale nie automatycznie poprawia ich domyślnie. Aby zapewnić, że poprawki systemu operacyjnego i składników środowiska uruchomieniowego węzła agenta, należy zachować regularne harmonogramy [uaktualniania obrazu węzła](node-image-upgrade.md) lub zautomatyzować go.

Podobnie AKS regularnie zwalnia nowe poprawki Kubernetes i wersje pomocnicze. Te aktualizacje mogą zawierać ulepszenia zabezpieczeń lub funkcji Kubernetes. Użytkownik jest odpowiedzialny za aktualizowanie wersji Kubernetes klastrów i zgodnie z [zasadami dotyczącymi wersji AKS Kubernetes](supported-kubernetes-versions.md).

#### <a name="user-customization-of-agent-nodes"></a>Dostosowanie węzłów agenta przez użytkownika
> [!NOTE]
> Węzły agenta AKS są wyświetlane w Azure Portal jako zwykłe zasoby IaaS platformy Azure. Jednak te maszyny wirtualne są wdrażane w niestandardowej grupie zasobów platformy Azure (zwykle z prefiksem MC_ \* ). Nie można zmienić podstawowego obrazu systemu operacyjnego ani wykonać żadnych bezpośrednich dostosowań do tych węzłów przy użyciu interfejsów API lub zasobów IaaS. Wszelkie zmiany niestandardowe, które nie są wykonywane za pośrednictwem interfejsu API AKS, nie są zachowywane przez uaktualnienie, skalowanie, aktualizowanie ani ponowne uruchomienie. Unikaj dokonywania zmian w węzłach agenta, chyba że pomoc techniczna firmy Microsoft nakazuje wprowadzenie zmian.

AKS zarządza cyklem życia i operacjami węzłów agenta w Twoim imieniu — modyfikowanie zasobów IaaS skojarzonych z węzłami agenta **nie jest obsługiwane**. Przykładem nieobsługiwanej operacji jest dostosowanie zestawu skalowania maszyn wirtualnych puli węzłów przez ręczne zmianę konfiguracji za pomocą portalu lub interfejsu API zestawu skalowania maszyn wirtualnych.
 
W przypadku konfiguracji lub pakietów specyficznych dla obciążenia AKS zaleca się przy użyciu [Kubernetes `daemon sets` ](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/).

Używanie `daemon sets` kontenerów uprzywilejowanych i init Kubernetes umożliwia dostosowywanie/modyfikowanie lub instalowanie oprogramowania innych firm w węzłach agenta klastra. Przykłady takich dostosowań obejmują dodanie niestandardowego oprogramowania do skanowania zabezpieczeń lub zaktualizowanie ustawień sysctl.

Chociaż ta ścieżka jest zalecana, jeśli powyższe wymagania nie mają zastosowania, Inżynieria AKS i pomoc techniczna nie mogą pomóc w rozwiązywaniu problemów lub diagnozowaniu modyfikacji, które renderują węzeł jako niedostępny z powodu wdrożenia niestandardowego `daemon set` .

### <a name="security-issues-and-patching"></a>Problemy z zabezpieczeniami i stosowanie poprawek

Jeśli Luka w zabezpieczeniach zostanie znaleziona w co najmniej jednym zarządzanym składnikom AKS, zespół AKS będzie mógł poprawić wszystkie klastry, których dotyczy problem. Alternatywnie zespół zapewni użytkownikom wskazówki dotyczące uaktualniania.

W przypadku węzłów agentów, których dotyczy luka w zabezpieczeniach, firma Microsoft powiadomi Cię o tym o tym, jak to miało wpływ, a także o tym, jak rozwiązać problem z zabezpieczeniami (zazwyczaj uaktualnienie obrazu węzła lub uaktualnieniem klastra).

### <a name="node-maintenance-and-access"></a>Konserwacja i dostęp do węzła

Mimo że możesz zalogować się do i zmienić węzły agentów, nie jest to zalecane, ponieważ zmiany mogą sprawić, że klaster jest nieobsługiwany.

## <a name="network-ports-access-and-nsgs"></a>Porty sieciowe, dostęp i sieciowych grup zabezpieczeń

Sieciowych grup zabezpieczeń można dostosować tylko w podsieciach niestandardowych. Nie można dostosować sieciowych grup zabezpieczeń w zarządzanych podsieciach lub na poziomie karty sieciowej węzłów agenta. AKS ma wychodzące wymagania do określonych punktów końcowych, aby kontrolować ruch wychodzący i zapewniać niezbędną łączność, zobacz [ograniczanie ruchu wyjściowego](limit-egress-traffic.md).

## <a name="stopped-or-de-allocated-clusters"></a>Zatrzymane lub cofnięte alokacje klastrów

Jak wspomniano wcześniej, ręczne cofanie przydziału wszystkich węzłów klastra za pośrednictwem interfejsów API IaaS/interfejsu wiersza polecenia/portalu renderuje klaster jako nieobsługiwany. Jedynym obsługiwanym sposobem zatrzymania/cofnięcia przydzielenia wszystkich węzłów jest [zatrzymanie klastra AKS](start-stop-cluster.md#stop-an-aks-cluster), który zachowuje stan klastra przez maksymalnie 12 miesięcy.

Klastry zatrzymane przez ponad 12 miesięcy nie będą już zachować stanu. 

Klastry nieprzypisane poza interfejsy API AKS nie mają gwarancji zachowywania stanu. Płaszczyzny kontroli dla klastrów w tym stanie będą archiwizowane po 30 dniach i usuwane po upływie 12 miesięcy.

AKS zastrzega sobie prawo do archiwizowania płaszczyzn kontroli, które zostały skonfigurowane z poziomu wytycznych dla rozszerzonych okresów równych i dłuższych niż 30 dni. AKS przechowuje kopie zapasowe metadanych etcd klastra i może łatwo ponownie przydzielić klaster. Tę ponowną alokację można zainicjować za pomocą dowolnej operacji PUT przełączenia klastra do pomocy technicznej, takiego jak uaktualnienie lub skalowanie do węzłów aktywnego agenta.

Jeśli subskrypcja zostanie zawieszona lub usunięta, płaszczyzna i stan kontroli klastra zostaną usunięte po 90 dniach.

## <a name="unsupported-alpha-and-beta-kubernetes-features"></a>Nieobsługiwane funkcje Kubernetes Alpha i beta

AKS obsługuje tylko funkcje stabilne i beta w ramach projektu nadrzędnego Kubernetes. O ile nie określono inaczej, AKS nie obsługuje żadnej funkcji alfa, która jest dostępna w nadrzędnym projekcie Kubernetes.

## <a name="preview-features-or-feature-flags"></a>Funkcje wersji zapoznawczej lub flagi funkcji

Aby uzyskać funkcje i funkcje, które wymagają przetestowania rozszerzonego i opinii użytkowników, firma Microsoft udostępnia nowe funkcje lub funkcje w wersji zapoznawczej za flagą funkcji. Te funkcje należy traktować jako funkcje wersji wstępnej lub wersji beta.

Funkcje w wersji zapoznawczej lub funkcje flagi funkcji nie są przeznaczone do produkcji. Bieżące zmiany w interfejsach API i zachowania, poprawki błędów i inne zmiany mogą spowodować niestabilne klastry i przestoje.

Funkcje w publicznej wersji zapoznawczej są objęte wsparciem "najlepsze wysiłki", ponieważ te funkcje są w wersji zapoznawczej i nie są przeznaczone do produkcji i są obsługiwane przez zespoły pomocy technicznej AKS w godzinach pracy. Aby uzyskać więcej informacji, zobacz:

* [Pomoc techniczna platformy Azure — często zadawane pytania](https://azure.microsoft.com/support/faq/)

## <a name="upstream-bugs-and-issues"></a>Błędy i problemy z usługą nadrzędnego

Mając na względnie prędkość rozwoju w projekcie Kubernetes, usterki niezmiennie powstają. Niektóre z tych usterek nie mogą zostać poprawione lub nie działały w systemie AKS. Zamiast tego poprawki błędów wymagają większych poprawek do projektów nadrzędnych (takich jak Kubernetes, Node lub Operating Systems i jądro). W przypadku składników należących do firmy Microsoft (takich jak dostawca chmury platformy Azure) AKS i personel platformy Azure są zaangażowane w sposób rozwiązywania problemów w społeczności.

Gdy problem z pomocą techniczną jest katalogiem głównym spowodowanym przez jedną lub więcej błędów nadrzędnych, zespoły pomocy technicznej AKS i inżynierów inżynieryjnych będą:

* Zidentyfikuj i Połącz błędy nadrzędnego z dowolnymi szczegółami pomocniczymi, aby wyjaśnić, dlaczego ten problem wpływa na klaster lub obciążenie. Klienci odbierają linki do wymaganych repozytoriów, aby mogli oglądać problemy i zobaczyć, kiedy nowe wydanie udostępni poprawki.
* Zapewnij potencjalne obejścia lub środki zaradcze. Jeśli problem może zostać skorygowany, w repozytorium AKS zostanie zgłoszony [znany problem](https://github.com/Azure/AKS/issues?q=is%3Aissue+is%3Aopen+label%3Aknown-issue) . Informacje o znanych problemach są następujące:
  * Problem, w tym linki do błędów nadrzędnych.
  * Obejście i szczegółowe informacje o uaktualnieniu lub innej trwałości rozwiązania.
  * Przybliżone osie czasu na potrzeby dołączenia problemu w oparciu o wersję erze.
