---
title: Koncepcje — podstawy Kubernetes dla usług Azure Kubernetes Services (AKS)
description: Informacje o podstawowym klastrze i składnikach obciążenia Kubernetes oraz o tym, jak odnoszą się do funkcji w usłudze Azure Kubernetes Service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 12/07/2020
ms.openlocfilehash: 7485631660395e03c558167c321e6091c6fac755
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100373236"
---
# <a name="kubernetes-core-concepts-for-azure-kubernetes-service-aks"></a>Podstawowe pojęcia Kubernetes dla usługi Azure Kubernetes Service (AKS)

Gdy Programowanie aplikacji przenosi się do podejścia opartego na kontenerach, trzeba zorganizować i zarządzać zasobami. Kubernetes to wiodąca platforma zapewniająca niezawodne planowanie obciążeń aplikacji odpornych na błędy. Usługa Azure Kubernetes Service (AKS) to zarządzana oferta Kubernetes, która ułatwia wdrażanie aplikacji opartych na kontenerach i zarządzanie nimi.

W tym artykule wprowadzono podstawowe składniki infrastruktury Kubernetes, takie jak *płaszczyzna kontroli*, *węzły* i *Pule węzłów*. Wprowadzono również zasoby obciążenia *, takie jak* *zbiory* , *wdrożenia* i zestawy, wraz z sposobem grupowania zasobów w *przestrzeni nazw*.

## <a name="what-is-kubernetes"></a>Co to jest Kubernetes?

Kubernetes to gwałtownie rozwijający się platforma, która zarządza aplikacjami opartymi na kontenerach i skojarzonymi z nimi składnikami sieci i magazynu. Fokus dotyczy obciążeń aplikacji, a nie podstawowych składników infrastruktury. Kubernetes zapewnia deklaracyjne podejście do wdrożeń, które są obsługiwane przez niezawodny zestaw interfejsów API dla operacji zarządzania.

Możesz tworzyć i uruchamiać nowoczesne, przenośne, oparte na mikrousługach aplikacje, które korzystają z Kubernetes organizowania i zarządzania dostępnością tych składników aplikacji. Usługa Kubernetes obsługuje zarówno bezstanowe, jak i stanowe aplikacje jako zespoły postępują zgodnie z wdrażaniem aplikacji opartych na mikrousługach.

Jako otwarta platforma Kubernetes umożliwia tworzenie aplikacji przy użyciu preferowanego języka programowania, systemu operacyjnego, bibliotek lub magistrali obsługi komunikatów. Istniejące narzędzia ciągłej integracji i ciągłego dostarczania (CI/CD) można zintegrować z usługą Kubernetes w celu planowania i wdrażania wersji.

Usługa Azure Kubernetes Service (AKS) oferuje zarządzaną usługę Kubernetes, która zmniejsza złożoność wdrażania i podstawowych zadań zarządzania, w tym koordynowania uaktualnień. Płaszczyzna kontroli AKS jest zarządzana przez platformę Azure i płacisz tylko za węzły AKS, na których działają aplikacje. AKS jest tworzona na podstawie aparatu usługi Azure Kubernetes ([AKS-Engine][aks-engine]) typu open source.

## <a name="kubernetes-cluster-architecture"></a>Architektura klastra Kubernetes

Klaster Kubernetes jest podzielony na dwa składniki:

- *Płaszczyzna kontroli* zapewnia podstawowe usługi Kubernetes oraz aranżację obciążeń aplikacji.
- *Węzły* , które uruchamiają obciążenia aplikacji.

![Kubernetes i płaszczyzny kontroli](media/concepts-clusters-workloads/control-plane-and-nodes.png)

## <a name="control-plane"></a>Płaszczyzna sterowania

Podczas tworzenia klastra AKS, zostanie automatycznie utworzona i skonfigurowana płaszczyzna kontroli. Ta płaszczyzna kontroli jest udostępniana jako zarządzany zasób platformy Azure, który został podzielny przez użytkownika. Dla płaszczyzny kontroli nie ma kosztu, tylko węzły, które są częścią klastra AKS. Płaszczyzna kontroli i jej zasoby znajdują się tylko w regionie, w którym został utworzony klaster.

Płaszczyzna kontroli obejmuje następujące podstawowe składniki Kubernetes:

- *polecenia-apiserver* — serwer interfejsu API to sposób, w jaki są ujawniane bazowe interfejsy API Kubernetes. Ten składnik zapewnia interakcję z narzędziami do zarządzania, takimi jak `kubectl` lub pulpitem nawigacyjnym Kubernetes.
- *etcd* — aby zachować stan klastra Kubernetes i konfiguracji, *etcd* wysoka dostępność to kluczowy magazyn wartości w ramach Kubernetes.
- *polecenia-Scheduler* — podczas tworzenia lub skalowania aplikacji harmonogram określa węzły, które mogą uruchamiać obciążenie i je uruchamia.
- *polecenia-Controller-Manager* — Menedżer kontrolerów widzi kilka mniejszych kontrolerów, które wykonują takie działania, jak replikowanie i obsługa operacji węzła.

AKS zapewnia jednodostępną płaszczyznę kontroli z dedykowanym serwerem interfejsu API, harmonogramem itd. Zdefiniuj liczbę i rozmiar węzłów, a platforma Azure skonfiguruje bezpieczną komunikację między płaszczyzną i węzłami. Interakcja z płaszczyzną kontroli odbywa się za pomocą interfejsów API Kubernetes, takich jak `kubectl` lub pulpitu nawigacyjnego Kubernetes.

Ta płaszczyzna kontroli zarządzanej oznacza, że nie trzeba konfigurować składników, takich jak magazyn *etcd* o wysokiej dostępności, ale oznacza to również, że nie można bezpośrednio uzyskać dostępu do płaszczyzny kontroli. Uaktualnienia do Kubernetes są zorganizowane za pomocą interfejsu wiersza polecenia platformy Azure lub Azure Portal, który uaktualnia płaszczyznę kontroli, a następnie węzły. Aby rozwiązać ewentualne problemy, można przejrzeć dzienniki płaszczyzny kontroli za pomocą dzienników Azure Monitor.

Jeśli konieczne jest skonfigurowanie płaszczyzny kontroli w określony sposób lub konieczność bezpośredniego dostępu do niej, można wdrożyć własny klaster Kubernetes przy użyciu [aparatu AKS][aks-engine].

W przypadku skojarzonych najlepszych rozwiązań należy zapoznać się [z najlepszymi rozwiązaniami dotyczącymi zabezpieczeń i uaktualnień klastra w programie AKS][operator-best-practices-cluster-security].

## <a name="nodes-and-node-pools"></a>Węzły i pule węzłów

Aby uruchamiać aplikacje i usługi pomocnicze, potrzebny jest *węzeł* Kubernetes. Klaster AKS ma co najmniej jeden węzeł, który jest maszyną wirtualną platformy Azure, na której działa składniki węzła Kubernetes i środowisko uruchomieniowe kontenera:

- `kubelet`Jest to Agent Kubernetes, który przetwarza żądania aranżacji z płaszczyzny kontroli i planowania uruchamiania żądanych kontenerów.
- Sieć wirtualna jest obsługiwana przez *polecenia-proxy* w każdym węźle. Serwer proxy kieruje ruchem sieciowym i zarządza adresami IP dla usług i zasobników.
- *Środowisko uruchomieniowe kontenera* to składnik, który umożliwia aplikacjom kontenerowym uruchamianie i współdziałanie z dodatkowymi zasobami, takimi jak sieć wirtualna i magazyn. W AKS, Moby jest używany jako środowisko uruchomieniowe kontenera.

![Maszyna wirtualna platformy Azure i obsługa zasobów dla węzła Kubernetes](media/concepts-clusters-workloads/aks-node-resource-interactions.png)

Rozmiar maszyny wirtualnej platformy Azure dla węzłów definiuje liczbę procesorów CPU, ilość pamięci oraz ilość dostępnego miejsca do magazynowania (na przykład dysk SSD o wysokiej wydajności lub zwykły dysk twardy). Jeśli przewidywane jest zapotrzebowanie na aplikacje wymagające dużej ilości zasobów procesora CPU i pamięci lub magazynu o wysokiej wydajności, należy odpowiednio zaplanować rozmiar węzła. Możesz również skalować liczbę węzłów w klastrze AKS, aby sprostać zapotrzebowaniem.

W AKS obraz maszyny wirtualnej dla węzłów w klastrze jest obecnie oparty na Ubuntu Linux lub Windows Server 2019. Podczas tworzenia klastra AKS lub skalowania liczby węzłów platforma Azure tworzy żądaną liczbę maszyn wirtualnych i konfiguruje je. Nie istnieje ręczna konfiguracja do wykonania. Węzły agentów są rozliczane jako standardowe maszyny wirtualne, w związku z czym wszelkie zniżki na używanym rozmiarze maszyny wirtualnej (w tym [rezerwacje platformy Azure][reservation-discounts]) są automatycznie stosowane.

Jeśli konieczne jest użycie innego systemu operacyjnego hosta, środowiska uruchomieniowego kontenera lub dołączenie pakietów niestandardowych, można wdrożyć własny klaster Kubernetes przy użyciu [aparatu AKS][aks-engine]. `aks-engine`Funkcje wersji nadrzędnej i udostępniają opcje konfiguracji, zanim są oficjalnie obsługiwane w klastrach AKS. Na przykład jeśli chcesz użyć środowiska uruchomieniowego kontenera innego niż Moby, możesz użyć `aks-engine` programu, aby skonfigurować i wdrożyć klaster Kubernetes, który spełnia Twoje bieżące potrzeby.

### <a name="resource-reservations"></a>Rezerwacje zasobów

Zasoby węzła są używane przez AKS, aby uczynić węzeł funkcją jako częścią klastra. To użycie może stworzyć niezgodność między całkowitymi zasobami węzła a zasobami, które można przydzielić, gdy są używane w AKS. Te informacje mają na celu zanotowanie, kiedy należy ustawić żądania i limity dla wdrożonych przez użytkownika zasobników.

Aby znaleźć zasoby przydzielane przez węzeł, uruchom polecenie:
```kubectl
kubectl describe node [NODE_NAME]
```

Aby zachować wydajność i funkcjonalność węzła, zasoby są zastrzeżone dla każdego węzła przez AKS. W miarę zwiększania się liczby zasobów, rezerwacja zasobów rośnie ze względu na większą ilość potrzebnych do zarządzania użytkownikami.

>[!NOTE]
> Używanie dodatków AKS, takich jak Container Insights (OMS), będzie zużywać dodatkowe zasoby węzła.

Zarezerwowane są dwa typy zasobów:

- Procesor CPU zarezerwowany przez **procesor** CPU zależy od typu węzła i konfiguracji klastra, co może spowodować mniejsze możliwości przydzielania CPU z powodu uruchamiania dodatkowych funkcji

   | Rdzenie procesora CPU na hoście | 1    | 2    | 4    | 8    | 16 | 32|64|
   |---|---|---|---|---|---|---|---|
   |Polecenia — zarezerwowane (millicores)|60|100|140|180|260|420|740|

- **Pamięć używana** przez AKS zawiera sumę dwóch wartości.

   1. Demon kubelet został zainstalowany we wszystkich węzłach agenta Kubernetes w celu zarządzania tworzeniem i kończeniem kontenera. Domyślnie w systemie AKS ten demon ma następującą regułę wykluczania: *Memory. available<750Mi*, co oznacza, że w każdym momencie węzeł musi mieć co najmniej 750.  Gdy host jest poniżej tego progu dostępnej pamięci, kubelet zakończy jeden z uruchomionych zasobników, aby zwolnić pamięć na komputerze hosta i chronić ją. Ta akcja jest wyzwalana, gdy ilość dostępnej pamięci nie przekracza progu 750Mi.

   2. Druga wartość to regresywnycha szybkość rezerwacji pamięci dla demona kubelet do prawidłowego działania (polecenia-zarezerwowane).
      - 25% pierwszego 4 GB pamięci
      - 20% z następnych 4 GB pamięci (do 8 GB)
      - 10% z następnych 8 GB pamięci (do 16 GB)
      - 6% następnego 112 GB pamięci (do 128 GB)
      - 2% każdej pamięci powyżej 128 GB

Powyższe zasady dotyczące pamięci i alokacji procesora są używane w celu zachowania poprawnego działania węzłów agenta, w tym pewnych systemów hostingu, które mają kluczowe znaczenie dla kondycji klastra. Te reguły alokacji powodują również, że węzeł raportuje mniejszą alokację pamięci i procesora CPU niż zwykle, jeśli nie był częścią klastra Kubernetes. Nie można zmienić powyższych rezerwacji zasobów.

Na przykład, jeśli węzeł zawiera 7 GB, zgłasza 34% pamięci nie można przydzielić, łącznie z progiem wykluczeń 750Mi.

`0.75 + (0.25*4) + (0.20*3) = 0.75GB + 1GB + 0.6GB = 2.35GB / 7GB = 33.57% reserved`

Oprócz rezerwacji dla samego Kubernetes, podstawowy system operacyjny węzła również rezerwuje ilość zasobów procesora i pamięci do obsługi funkcji systemu operacyjnego.

Aby zapoznać się z najlepszymi rozwiązaniami, zobacz [najlepsze rozwiązania dotyczące podstawowych funkcji usługi Scheduler w AKS][operator-best-practices-scheduler].

### <a name="node-pools"></a>Pule węzłów

Węzły tej samej konfiguracji są pogrupowane w *Pule węzłów*. Klaster Kubernetes zawiera co najmniej jedną pulę węzłów. Początkowa liczba węzłów i rozmiar są definiowane podczas tworzenia klastra AKS, który tworzy *domyślną pulę węzłów*. Ta domyślna pula węzłów w AKS zawiera podstawowe maszyny wirtualne, na których są uruchomione węzły agentów.

> [!NOTE]
> Aby zapewnić niezawodne działanie klastra, należy uruchomić co najmniej 2 (dwa) węzły w domyślnej puli węzłów.

Po skalowaniu lub uaktualnieniu klastra AKS akcja jest wykonywana względem domyślnej puli węzłów. Możesz również wybrać skalowanie lub uaktualnianie określonej puli węzłów. W przypadku operacji uaktualniania uruchomione kontenery są planowane w innych węzłach w puli węzłów do momentu, aż wszystkie węzły zostaną pomyślnie uaktualnione.

Aby uzyskać więcej informacji na temat używania wielu pul węzłów w AKS, zobacz [Tworzenie wielu pul węzłów i zarządzanie nimi w klastrze w AKS][use-multiple-node-pools].

### <a name="node-selectors"></a>Selektory węzłów

W klastrze AKS zawierającym wiele pul węzłów może być konieczne poinformowanie usługi Kubernetes Scheduler, której puli węzłów użyć dla danego zasobu. Na przykład kontrolery transferu danych przychodzących nie powinny działać w węzłach systemu Windows Server. Selektory węzłów umożliwiają definiowanie różnych parametrów, takich jak węzeł systemu operacyjnego, w celu kontrolowania, gdzie należy zaplanować.

Poniższy przykład podstawowy zaplanuje wystąpienie NGINX w węźle systemu Linux przy użyciu selektora węzła *"beta.Kubernetes.IO/OS": Linux*:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx
spec:
  containers:
    - name: myfrontend
      image: mcr.microsoft.com/oss/nginx/nginx:1.15.12-alpine
  nodeSelector:
    "beta.kubernetes.io/os": linux
```

Aby uzyskać więcej informacji na temat sposobu kontrolowania, gdzie są planowane planowanie, zobacz [najlepsze rozwiązania dotyczące zaawansowanych funkcji usługi Scheduler w AKS][operator-best-practices-advanced-scheduler].

## <a name="pods"></a>Zasobników

Kubernetes używa *zasobników* do uruchomienia wystąpienia aplikacji. Element pod reprezentuje pojedyncze wystąpienie aplikacji. Zasobniki zwykle mają 1:1 mapowania z kontenerem, chociaż istnieją zaawansowane scenariusze, w których część może zawierać wiele kontenerów. Te wielokontenerowe działy są planowane razem w tym samym węźle i umożliwiają kontenerom udostępnianie powiązanych zasobów.

Podczas tworzenia elementu pod można zdefiniować *żądania zasobów* , aby zażądać określonej ilości zasobów procesora CPU lub pamięci. Harmonogram Kubernetes próbuje zaplanować uruchomienie programu w węźle z dostępnymi zasobami w celu spełnienia żądania. Można również określić maksymalne limity zasobów uniemożliwiające korzystanie z zbyt dużej ilości zasobów obliczeniowych z bazowego węzła. Najlepszym rozwiązaniem jest uwzględnienie limitów zasobów dla wszystkich zasobników, aby pomóc harmonogramowi Kubernetes zrozumieć, które zasoby są zbędne i dozwolone.

Aby uzyskać więcej informacji, zobacz [Kubernetes][kubernetes-pods] i [Kubernetes pod cykl życia][kubernetes-pod-lifecycle].

A pod jest zasobem logicznym, ale kontenery są, w których są uruchamiane obciążenia aplikacji. Zasobniki to zazwyczaj tymczasowe, jednorazowe zasoby, a indywidualnie zaplanowane zasobniki nie mogą mieć niektórych funkcji wysokiej dostępności i nadmiarowości Kubernetes. Zamiast tego są wdrażane i zarządzane przez *Kontrolery* Kubernetes, takie jak kontroler wdrażania.

## <a name="deployments-and-yaml-manifests"></a>Wdrożenia i manifesty YAML

*Wdrożenie* reprezentuje jeden lub więcej identycznych zasobników zarządzanych przez kontroler wdrażania Kubernetes. Wdrożenie definiuje liczbę *replik* (na podst.) do utworzenia, a usługa Kubernetes Scheduler gwarantuje, że w przypadku wystąpienia problemów z planami lub w węzłach w dobrej kondycji są planowane dodatkowe numery.

Możesz zaktualizować wdrożenia, aby zmienić konfigurację zasobników, używanego obrazu kontenera lub dołączonego magazynu. Kontroler wdrożenia opróżnia i kończy daną liczbę replik, tworzy repliki z nowej definicji wdrożenia i kontynuuje proces do momentu zaktualizowania wszystkich replik we wdrożeniu.

Większość aplikacji bezstanowych w AKS powinna używać modelu wdrażania zamiast planowania pojedynczych zasobników. Kubernetes może monitorować kondycję i stan wdrożeń, aby upewnić się, że wymagana liczba replik działa w ramach klastra. W przypadku zaplanowania tylko pojedynczych zasobników te nie są ponownie uruchamiane, Jeśli napotkają problem, i nie są ponownie planowane w węzłach o dobrej kondycji, jeśli ich bieżący węzeł napotka problem.

Jeśli aplikacja wymaga, aby kworum wystąpień było zawsze dostępne do podejmowania decyzji dotyczących zarządzania, nie chcesz, aby proces aktualizacji mógł przerwać tę możliwość. *Budżety na zakłócenia* mogą służyć do definiowania liczby replik w ramach wdrożenia, które mogą zostać wyłączone podczas uaktualniania aktualizacji lub węzła. Na przykład jeśli w danym wdrożeniu znajduje się *pięć (5)* replik, można zdefiniować zakłócenia pod kątem wartości *4* , aby zezwolić na usunięcie lub ponowne zaplanowanie jednej repliki. Podobnie jak w przypadku limitów zasobów, najlepszym rozwiązaniem jest zdefiniowanie budżetów w aplikacjach, które wymagają minimalnej liczby replik, aby zawsze były obecne.

Wdrożenia są zwykle tworzone i zarządzane przy użyciu programu `kubectl create` lub `kubectl apply` . Aby utworzyć wdrożenie, należy zdefiniować plik manifestu w formacie YAML (YAML Ain't Markup Language). Poniższy przykład tworzy podstawowe wdrożenie serwera sieci Web NGINX. Wdrożenie określa *trzy (3)* repliki do utworzenia i wymaga, aby port *80* był otwarty w kontenerze. Żądania zasobów i limity są również zdefiniowane dla procesora CPU i pamięci.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: mcr.microsoft.com/oss/nginx/nginx:1.15.2-alpine
        ports:
        - containerPort: 80
        resources:
          requests:
            cpu: 250m
            memory: 64Mi
          limits:
            cpu: 500m
            memory: 256Mi
```

Bardziej złożone aplikacje można utworzyć, uwzględniając także usługi, takie jak moduły równoważenia obciążenia w manifeście YAML.

Aby uzyskać więcej informacji, zobacz [Kubernetes Deployments][kubernetes-deployments].

### <a name="package-management-with-helm"></a>Zarządzanie pakietami za pomocą Helm

Typowym podejściem do zarządzania aplikacjami w programie Kubernetes jest [Helm][helm]. Możesz tworzyć i używać istniejących publicznych *wykresów* Helm, które zawierają spakowaną wersję kodu aplikacji i manifestów YAML Kubernetes do wdrażania zasobów. Te wykresy Helm mogą być przechowywane lokalnie lub często w repozytorium zdalnym, takim jak repozytorium wykresu programu [Azure Container Registry Helm][acr-helm].

Aby użyć Helm, zainstaluj klienta programu Helm na komputerze lub Użyj klienta Helm w [Azure Cloud Shell][azure-cloud-shell]. Możesz wyszukać i utworzyć wykresy Helm z klientem, a następnie zainstalować je w klastrze Kubernetes. Aby uzyskać więcej informacji, zobacz [Instalowanie istniejących aplikacji przy użyciu usługi Helm w AKS][aks-helm].

## <a name="statefulsets-and-daemonsets"></a>StatefulSets i DaemonSets

Kontroler wdrażania używa usługi Kubernetes Scheduler, aby uruchomić daną liczbę replik w dowolnym z dostępnych zasobów. Takie podejście do używania wdrożeń może być wystarczające dla aplikacji bezstanowych, ale nie dla aplikacji, które wymagają trwałej konwencji nazewnictwa lub magazynu. W przypadku aplikacji, które wymagają repliki w każdym węźle lub wybranych węzłów w klastrze, kontroler wdrożenia nie sprawdza, jak repliki są dystrybuowane między węzłami.

Istnieją dwa zasoby Kubernetes, które umożliwiają zarządzanie tymi typami aplikacji:

- *StatefulSets* — utrzymuje stan aplikacji poza indywidualnym cyklem życia, takim jak magazyn.
- *DaemonSets* — upewnij się, że uruchomione wystąpienie w każdym węźle jest wczesne w procesie ładowania początkowego Kubernetes.

### <a name="statefulsets"></a>StatefulSets

Nowoczesne opracowywanie aplikacji jest często stosowane w przypadku aplikacji bezstanowych, ale *StatefulSets* można używać w przypadku aplikacji stanowych, takich jak aplikacje, które zawierają składniki bazy danych. StatefulSet jest podobny do wdrożenia w przypadku tworzenia co najmniej jednego identycznego zasobnika i zarządzania nim. Repliki w StatefulSet wykonują bezpieczne, sekwencyjne podejście do wdrożenia, skalowania, uaktualnienia i zakończenia. W przypadku StatefulSet (jako repliky są ponownie zaplanowane) Konwencja nazewnictwa, nazwy sieciowe i magazyn pozostają.

Aplikację można zdefiniować w formacie YAML za pomocą programu `kind: StatefulSet` , a kontroler StatefulSet obsługuje wdrażanie wymaganych replik i zarządzanie nimi. Dane są zapisywane w magazynie trwałym, udostępnianym przez usługę Azure Managed Disks lub Azure Files. W przypadku programu StatefulSets podstawowy magazyn trwały pozostaje nawet wtedy, gdy StatefulSet zostanie usunięta.

Aby uzyskać więcej informacji, zobacz [Kubernetes StatefulSets][kubernetes-statefulsets].

Repliki w StatefulSet są zaplanowane i uruchamiane w dowolnym z dostępnych węzłów w klastrze AKS. Jeśli chcesz upewnić się, że co najmniej jeden z tych zestawów jest uruchomiony w węźle, możesz zamiast tego użyć elementu daemonset.

### <a name="daemonsets"></a>DaemonSets

W przypadku konkretnych kolekcji dzienników lub potrzeb związanych z monitorowaniem może być konieczne uruchomienie danego elementu pod względem wszystkich lub wybranych węzłów. *Elementu daemonset* ponownie służy do wdrożenia co najmniej jednego identycznego zasobnika, ale kontroler elementu daemonset zapewnia, że każdy określony węzeł uruchamia wystąpienie elementu.

Przed uruchomieniem domyślnego harmonogramu Kubernetes w kontrolerze elementu daemonset można zaplanować w węzłach na wczesny proces rozruchu klastra. Pozwala to zagwarantować, że zasobniki w elementu daemonset są uruchamiane przed zaplanowaniem tradycyjnych zasobników we wdrożeniu lub StatefulSet.

Podobnie jak w przypadku StatefulSets, elementu daemonset jest definiowana jako część definicji YAML za pomocą `kind: DaemonSet` .

Aby uzyskać więcej informacji, zobacz [Kubernetes DaemonSets][kubernetes-daemonset].

> [!NOTE]
> W przypadku używania [dodatku węzłów wirtualnych](virtual-nodes-cli.md#enable-virtual-nodes-addon)DaemonSets nie utworzy zasobnika w węźle wirtualnym.

## <a name="namespaces"></a>Przestrzenie nazw

Zasoby Kubernetes, takie jak grupy miar i wdrożenia, są logicznie pogrupowane w *przestrzeni nazw*. Dzięki tym grupom można logicznie podzielić klaster AKS i ograniczyć dostęp do tworzenia, wyświetlania i zarządzania zasobami. Można na przykład utworzyć przestrzenie nazw w celu oddzielenia grup firmy. Użytkownicy mogą korzystać tylko z zasobami w ramach przypisanych przestrzeni nazw.

![Kubernetes przestrzenie nazw, aby logicznie podzielić zasoby i aplikacje](media/concepts-clusters-workloads/namespaces.png)

Podczas tworzenia klastra AKS dostępne są następujące przestrzenie nazw:

- *default* — Ta przestrzeń nazw to miejsce, w którym domyślnie tworzone są i wdrożenia, gdy żaden z nich nie jest dostarczany. W mniejszych środowiskach można wdrażać aplikacje bezpośrednio w domyślnej przestrzeni nazw bez tworzenia dodatkowych rozbarwień logicznych. W przypadku korzystania z interfejsu API Kubernetes, takiego jak with `kubectl get pods` , domyślna przestrzeń nazw jest używana, gdy nie jest określony.
- *polecenia-system* — Ta przestrzeń nazw to miejsce, w którym istnieją podstawowe zasoby, takie jak usługa DNS i serwer proxy lub pulpit nawigacyjny Kubernetes. Zwykle nie są wdrażane własne aplikacje w tej przestrzeni nazw.
- *polecenia-Public* — Ta przestrzeń nazw zazwyczaj nie jest używana, ale może być używana do wyświetlania zasobów w całym klastrze i może być wyświetlana przez dowolnego użytkownika.

Aby uzyskać więcej informacji, zobacz [Kubernetes przestrzenie nazw][kubernetes-namespaces].

## <a name="next-steps"></a>Następne kroki

W tym artykule omówiono niektóre podstawowe składniki Kubernetes oraz sposób ich stosowania do klastrów AKS. Aby uzyskać więcej informacji na temat podstawowych pojęć związanych z Kubernetes i AKS, zobacz następujące artykuły:

- [Kubernetes/AKS, dostęp i tożsamość][aks-concepts-identity]
- [Zabezpieczenia Kubernetes/AKS][aks-concepts-security]
- [Sieci wirtualne Kubernetes/AKS][aks-concepts-network]
- [Magazyn Kubernetes/AKS][aks-concepts-storage]
- [Skala Kubernetes/AKS][aks-concepts-scale]

<!-- EXTERNAL LINKS -->
[aks-engine]: https://github.com/Azure/aks-engine
[kubernetes-pods]: https://kubernetes.io/docs/concepts/workloads/pods/pod-overview/
[kubernetes-pod-lifecycle]: https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/
[kubernetes-deployments]: https://kubernetes.io/docs/concepts/workloads/controllers/deployment/
[kubernetes-statefulsets]: https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/
[kubernetes-daemonset]: https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/
[kubernetes-namespaces]: https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/
[helm]: https://helm.sh/
[azure-cloud-shell]: https://shell.azure.com

<!-- INTERNAL LINKS -->
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[acr-helm]: ../container-registry/container-registry-helm-repos.md
[aks-helm]: kubernetes-helm.md
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[operator-best-practices-scheduler]: operator-best-practices-scheduler.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[reservation-discounts]:../cost-management-billing/reservations/save-compute-costs-reservations.md
