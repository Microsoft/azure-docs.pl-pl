---
title: Koncepcje — podstawy Kubernetes dla usług Azure Kubernetes Services (AKS)
description: Informacje o podstawowym klastrze i składnikach obciążenia Kubernetes oraz o tym, jak odnoszą się do funkcji w usłudze Azure Kubernetes Service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: 5e505ed44d221b20178ea5ffb1d9125fb2bddd4c
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105939"
---
# <a name="kubernetes-core-concepts-for-azure-kubernetes-service-aks"></a>Podstawowe pojęcia Kubernetes dla usługi Azure Kubernetes Service (AKS)

Opracowywanie aplikacji kontynuuje przejście na podejście oparte na kontenerach, zwiększając nasze potrzeby organizowania zasobów i zarządzania nimi. Jako wiodąca platforma Kubernetes zapewnia niezawodne planowanie obciążeń aplikacji odpornych na błędy. Usługa Azure Kubernetes Service (AKS) — zarządzana oferta Kubernetes, ułatwiająca wdrażanie aplikacji opartych na kontenerach i zarządzanie nimi.

W tym artykule wprowadzono:
* Podstawowe składniki infrastruktury Kubernetes:
    * *Płaszczyzna kontroli*
    * *nich*
    * *pule węzłów*
* Zasoby obciążenia: 
    * *zasobników*
    * *komputerów*
    * *przywraca* 
* Jak grupować zasoby w *przestrzeni nazw*.

## <a name="what-is-kubernetes"></a>Co to jest platforma Kubernetes?

Kubernetes to gwałtownie rozwijający się platforma, która zarządza aplikacjami opartymi na kontenerach i skojarzonymi z nimi składnikami sieci i magazynu. Kubernetes koncentruje się na obciążeniach aplikacji, a nie na podstawowych składnikach infrastruktury. Kubernetes zapewnia deklaracyjne podejście do wdrożeń, które są obsługiwane przez niezawodny zestaw interfejsów API dla operacji zarządzania.

Można tworzyć i uruchamiać nowoczesne, przenośne, oparte na mikrousługach aplikacje, korzystając z Kubernetes, aby organizować dostępność składników aplikacji i zarządzać nimi. Usługa Kubernetes obsługuje zarówno bezstanowe, jak i stanowe aplikacje jako zespoły postępują zgodnie z wdrażaniem aplikacji opartych na mikrousługach.

Jako otwarta platforma Kubernetes umożliwia tworzenie aplikacji przy użyciu preferowanego języka programowania, systemu operacyjnego, bibliotek lub magistrali obsługi komunikatów. Istniejące narzędzia ciągłej integracji i ciągłego dostarczania (CI/CD) można zintegrować z usługą Kubernetes w celu planowania i wdrażania wersji.

Usługa AKS udostępnia zarządzaną usługę Kubernetes, która zmniejsza złożoność wdrażania i podstawowych zadań zarządzania, takich jak koordynacja uaktualniania. Platforma Azure zarządza płaszczyzną kontroli AKS i płacisz tylko za węzły AKS, na których działają aplikacje. AKS jest tworzona na podstawie aparatu usługi Azure Kubernetes dla typu open source: [AKS-Engine][aks-engine].

## <a name="kubernetes-cluster-architecture"></a>Architektura klastra Kubernetes

Klaster Kubernetes jest podzielony na dwa składniki:

- *Płaszczyzna kontroli*: zapewnia podstawowe usługi Kubernetes i aranżację obciążeń aplikacji.
- *Węzły*: uruchamianie obciążeń aplikacji.

![Kubernetes i płaszczyzny kontroli](media/concepts-clusters-workloads/control-plane-and-nodes.png)

## <a name="control-plane"></a>Płaszczyzna sterowania

Podczas tworzenia klastra AKS, zostanie automatycznie utworzona i skonfigurowana płaszczyzna kontroli. Ta płaszczyzna kontroli jest świadczona bezpłatnie jako zasób platformy Azure zarządzany przez użytkownika. Płacisz tylko za węzły dołączone do klastra AKS. Płaszczyzna kontroli i jej zasoby znajdują się tylko w regionie, w którym został utworzony klaster.

Płaszczyzna kontroli obejmuje następujące podstawowe składniki Kubernetes:

| Składnik | Opis |  
| ----------------- | ------------- |  
| *polecenia — apiserver*                                                                                 | Serwer interfejsu API to sposób ujawniania podstawowych interfejsów API Kubernetes. Ten składnik zapewnia interakcję z narzędziami do zarządzania, takimi jak `kubectl` lub pulpitem nawigacyjnym Kubernetes.                                                        |  
| *etcd* | Aby zachować stan klastra Kubernetes i konfiguracji, *etcd* o wysokiej dostępności to kluczowy magazyn wartości w Kubernetes.                                      |  
| *polecenia — Scheduler*                                                                            | Podczas tworzenia lub skalowania aplikacji harmonogram określa, które węzły mogą uruchamiać obciążenie, i uruchomić je.                                                                                    |  
| *polecenia-Controller-Manager*                                                                            | Menedżer kontrolerów widzi kilka mniejszych kontrolerów, które wykonują takie działania, jak replikowanie i obsługa operacji węzła.                                                                  |  

AKS zapewnia jednodostępną płaszczyznę kontroli z dedykowanym serwerem interfejsu API, harmonogramem itd. Zdefiniuj liczbę i rozmiar węzłów, a platforma Azure skonfiguruje bezpieczną komunikację między płaszczyzną i węzłami. Interakcja z płaszczyzną kontroli odbywa się za pomocą interfejsów API Kubernetes, takich jak `kubectl` lub pulpitu nawigacyjnego Kubernetes.

Chociaż nie trzeba konfigurować składników (takich jak magazyn *etcd* o wysokiej dostępności) przy użyciu tej płaszczyzny kontroli zarządzanej, nie można bezpośrednio uzyskać dostępu do płaszczyzny kontroli. Uaktualnienia płaszczyzny i węzła kontroli Kubernetes są zorganizowane za pomocą interfejsu wiersza polecenia platformy Azure lub Azure Portal. Aby rozwiązać ewentualne problemy, można przejrzeć dzienniki płaszczyzny kontroli za pomocą dzienników Azure Monitor.

Aby skonfigurować lub bezpośrednio uzyskać dostęp do płaszczyzny kontroli, wdróż własny klaster Kubernetes za pomocą [AKS-Engine][aks-engine].

W przypadku skojarzonych najlepszych rozwiązań należy zapoznać się [z najlepszymi rozwiązaniami dotyczącymi zabezpieczeń i uaktualnień klastra w programie AKS][operator-best-practices-cluster-security].

## <a name="nodes-and-node-pools"></a>Węzły i pule węzłów

Aby uruchamiać aplikacje i usługi pomocnicze, potrzebny jest *węzeł* Kubernetes. Klaster AKS ma co najmniej jeden węzeł, maszynę wirtualną platformy Azure, która uruchamia składniki węzła Kubernetes i środowisko uruchomieniowe kontenera.

| Składnik | Opis |  
| ----------------- | ------------- |  
| `kubelet`                                                                                 | Agent Kubernetes, który przetwarza żądania aranżacji z płaszczyzny kontroli i planowania uruchamiania żądanych kontenerów.                                                        |  
| *polecenia — proxy* | Obsługuje wirtualne sieci w każdym węźle. Serwer proxy kieruje ruchem sieciowym i zarządza adresami IP dla usług i zasobników.                                      |  
| *środowisko uruchomieniowe kontenera*                                                                            | Umożliwia aplikacjom kontenerowym uruchamianie i współdziałanie z dodatkowymi zasobami, takimi jak sieć wirtualna i magazyn. Klastry AKS korzystające z puli węzłów Kubernetes w wersji 1.19 + `containerd` są używane jako środowisko uruchomieniowe kontenera. Klastry AKS korzystające z Kubernetes przed pulą węzłów w wersji 1,19 dla pul węzłów używają [Moby](https://mobyproject.org/) (Docker) jako środowiska uruchomieniowego kontenera.                                                                                    |  


![Maszyna wirtualna platformy Azure i obsługa zasobów dla węzła Kubernetes](media/concepts-clusters-workloads/aks-node-resource-interactions.png)

Rozmiar maszyny wirtualnej platformy Azure dla węzłów definiuje procesor CPU, pamięć, rozmiar i typ dostępnego magazynu (na przykład dysk SSD o wysokiej wydajności lub zwykły dysk twardy). Zaplanuj rozmiar węzła wokół tego, czy aplikacje mogą wymagać dużej ilości zasobów procesora CPU i pamięci oraz magazynu o wysokiej wydajności. Skaluj w poziomie liczbę węzłów w klastrze AKS, aby sprostać zapotrzebowaniem.

W AKS obraz maszyny wirtualnej dla węzłów klastra jest oparty na Ubuntu Linux lub Windows Server 2019. Podczas tworzenia klastra AKS lub skalowania liczby węzłów platforma Azure automatycznie tworzy i konfiguruje żądaną liczbę maszyn wirtualnych. Węzły agentów są rozliczane jako standardowe maszyny wirtualne, w związku z czym wszystkie zniżki rozmiaru maszyny wirtualnej (w tym [rezerwacje platformy Azure][reservation-discounts]) są automatycznie stosowane.

Wdróż własny klaster Kubernetes z [aparatem AKS-Engine][aks-engine] , jeśli używany jest inny system operacyjny hosta, środowisko uruchomieniowe kontenera lub w tym różne pakiety niestandardowe. `aks-engine`Funkcje nadrzędnego wydania i udostępniają opcje konfiguracji przed wsparciem w klastrach AKS. Tak więc, jeśli chcesz użyć środowiska uruchomieniowego kontenera innego niż `containerd` lub [Moby](https://mobyproject.org/), możesz uruchomić polecenie, `aks-engine` Aby skonfigurować i wdrożyć klaster Kubernetes, który spełnia Twoje bieżące potrzeby.

### <a name="resource-reservations"></a>Rezerwacje zasobów

AKS używa zasobów węzła, aby ułatwić działanie węzła w ramach klastra. To użycie może stworzyć niezgodność między całkowitymi zasobami węzła a zasobami, które można przydzielić w AKS. Należy pamiętać o tych informacjach podczas ustawiania żądań i limitów dla wdrożonych przez użytkownika zasobników.

Aby znaleźć zasoby przydzielane przez węzeł, uruchom polecenie:
```kubectl
kubectl describe node [NODE_NAME]
```

Aby zapewnić wydajność i funkcjonalność węzła, AKS rezerwuje zasoby w każdym węźle. W miarę zwiększania się rozmiaru zasobów rezerwacja zasobów rośnie ze względu na to, że zarządzanie nimi ma większe znaczenie.

>[!NOTE]
> Używanie dodatków AKS, takich jak Container Insights (OMS), będzie zużywać dodatkowe zasoby węzła.

Zarezerwowane są dwa typy zasobów:

- **Procesor CPU**  
    Zarezerwowany procesor CPU zależy od typu węzła i konfiguracji klastra, co może spowodować mniejsze możliwości przydzielania CPU z powodu uruchamiania dodatkowych funkcji.

   | Rdzenie procesora CPU na hoście | 1    | 2    | 4    | 8    | 16 | 32|64|
   |---|---|---|---|---|---|---|---|
   |Polecenia — zarezerwowane (millicores)|60|100|140|180|260|420|740|

- **Pamięć**  
    Pamięć wykorzystywana przez AKS zawiera sumę dwóch wartości.

   1. **`kubelet` Demon**   
       `kubelet`Demon zostanie zainstalowany we wszystkich węzłach agenta Kubernetes, aby zarządzać tworzeniem i kończeniem kontenera. 
   
        Domyślnie w systemie AKS `kubelet` demon ma *pamięć. dostępna<750Mi* zasada wykluczenia, dzięki czemu węzeł musi zawsze mieć co najmniej 750. Gdy host jest poniżej tego dostępnego progu pamięci, `kubelet` zostanie wyzwolone zakończenie jednego z uruchomionych zasobników i zwolnienie pamięci na komputerze-hoście.

   2. **Regresywnych stopień rezerwacji pamięci** dla demona kubelet w celu prawidłowego działania (*polecenia-zarezerwowany*).
      - 25% pierwszego 4 GB pamięci
      - 20% z następnych 4 GB pamięci (do 8 GB)
      - 10% z następnych 8 GB pamięci (do 16 GB)
      - 6% następnego 112 GB pamięci (do 128 GB)
      - 2% każdej pamięci powyżej 128 GB

Reguły alokacji pamięci i procesora CPU:
* Utrzymywanie węzłów agenta w dobrej kondycji, w tym w przypadku niektórych systemów hostingu o znaczeniu krytycznym dla klastra. 
* Powoduje, że węzeł zgłasza mniejszą alokację pamięci i procesora CPU niż w przypadku, gdy nie był częścią klastra Kubernetes. 

Nie można zmienić powyższych rezerwacji zasobów.

Na przykład, jeśli węzeł zawiera 7 GB, zgłasza 34% pamięci nie można przydzielić, łącznie z progiem wykluczeń 750Mi.

`0.75 + (0.25*4) + (0.20*3) = 0.75GB + 1GB + 0.6GB = 2.35GB / 7GB = 33.57% reserved`

Oprócz rezerwacji dla samego Kubernetes, podstawowy system operacyjny węzła również rezerwuje ilość zasobów procesora i pamięci do obsługi funkcji systemu operacyjnego.

Aby zapoznać się z najlepszymi rozwiązaniami, zobacz [najlepsze rozwiązania dotyczące podstawowych funkcji usługi Scheduler w AKS][operator-best-practices-scheduler].

### <a name="node-pools"></a>Pule węzłów

Węzły tej samej konfiguracji są pogrupowane w *Pule węzłów*. Klaster Kubernetes zawiera co najmniej jedną pulę węzłów. Początkowa liczba węzłów i rozmiar są definiowane podczas tworzenia klastra AKS, który tworzy *domyślną pulę węzłów*. Ta domyślna pula węzłów w AKS zawiera podstawowe maszyny wirtualne, na których są uruchomione węzły agentów.

> [!NOTE]
> Aby zapewnić niezawodne działanie klastra, należy uruchomić co najmniej dwa węzły (2) w domyślnej puli węzłów.

Klaster AKS można skalować lub uaktualnić do domyślnej puli węzłów. Można wybrać skalowanie lub uaktualnianie określonej puli węzłów. W przypadku operacji uaktualniania uruchomione kontenery są planowane w innych węzłach w puli węzłów do momentu, aż wszystkie węzły zostaną pomyślnie uaktualnione.

Aby uzyskać więcej informacji na temat używania wielu pul węzłów w AKS, zobacz [Tworzenie wielu pul węzłów i zarządzanie nimi w klastrze w AKS][use-multiple-node-pools].

### <a name="node-selectors"></a>Selektory węzłów

W klastrze AKS z wieloma pulami węzłów może być konieczne poinformowanie usługi Kubernetes Scheduler, której puli węzłów użyć dla danego zasobu. Na przykład kontrolery transferu danych przychodzących nie powinny działać w węzłach systemu Windows Server. 

Selektory węzłów umożliwiają definiowanie różnych parametrów, takich jak Node OS, w celu kontrolowania, gdzie należy zaplanować.

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

Kubernetes używa *zasobników* do uruchomienia wystąpienia aplikacji. Element pod reprezentuje pojedyncze wystąpienie aplikacji. 

Zasobniki zwykle mają mapowanie 1:1 z kontenerem. W zaawansowanych scenariuszach, pod może zawierać wiele kontenerów. Wiele kontenerów jest zaplanowanych w tym samym węźle i umożliwiają kontenerom udostępnianie powiązanych zasobów.

Podczas tworzenia elementu pod można zdefiniować *żądania zasobów* , aby zażądać określonej ilości zasobów procesora CPU lub pamięci. Harmonogram Kubernetes próbuje spełnić żądanie przez zaplanowanie, aby uruchomić je w węźle z dostępnymi zasobami. Można również określić maksymalną liczbę limitów zasobów, aby zapobiec zużywaniu przez niego zbyt dużej ilości zasobów obliczeniowych z bazowego węzła. Najlepszym rozwiązaniem jest uwzględnienie limitów zasobów dla wszystkich zasobników, aby pomóc harmonogramowi Kubernetes identyfikować niezbędne, dozwolone zasoby.

Aby uzyskać więcej informacji, zobacz [Kubernetes][kubernetes-pods] i [Kubernetes pod cykl życia][kubernetes-pod-lifecycle].

A pod jest zasobem logicznym, ale obciążenia aplikacji są uruchamiane w kontenerach. Zasobniki są zazwyczaj ulotnymi, nierozporządzalnymi zasobami. Poszczególne zaplanowane zasobniki nie mają niektórych funkcji wysokiej dostępności i nadmiarowości Kubernetes. Zamiast tego są wdrażane i zarządzane przez *Kontrolery* Kubernetes, takie jak kontroler wdrażania.

## <a name="deployments-and-yaml-manifests"></a>Wdrożenia i manifesty YAML

*Wdrożenie* reprezentuje identyczną Kubernetes zarządzaną przez kontroler wdrażania. Wdrożenie definiuje liczbę *replik* , które mają zostać utworzone. Usługa Kubernetes Scheduler gwarantuje, że dodatkowe zasobniki są planowane na węzłach w dobrej kondycji, jeśli występują problemy z wystąpieniami lub węzłami.

Możesz zaktualizować wdrożenia, aby zmienić konfigurację zasobników, używanego obrazu kontenera lub dołączonego magazynu. Kontroler wdrożenia:
* Opróżnia i kończy daną liczbę replik.
* Tworzy repliki z nowej definicji wdrożenia.
* Kontynuuje proces do momentu zaktualizowania wszystkich replik we wdrożeniu.

Większość aplikacji bezstanowych w AKS powinna używać modelu wdrażania zamiast planowania pojedynczych zasobników. Kubernetes może monitorować kondycję wdrożenia i stan, aby upewnić się, że wymagana liczba replik działa w klastrze. W przypadku zaplanowania pojedynczych zasobników nie są ponownie uruchamiane w przypadku wystąpienia problemu i nie są ponownie planowane na węzłach w dobrej kondycji, jeśli ich bieżący węzeł napotka problem.

Nie chcesz zakłócać podejmowania decyzji dotyczących zarządzania w ramach procesu aktualizacji, jeśli aplikacja wymaga minimalnej liczby dostępnych wystąpień. *Budżety* w przypadku przerw w działaniu definiują liczbę replik w ramach wdrożenia, które można uwzględnić podczas uaktualniania aktualizacji lub węzła. Na przykład jeśli w danym wdrożeniu znajdują się *pięć (5)* replik, można zdefiniować zakłócenia pod kątem *4 (cztery)* , aby umożliwić usunięcie lub ponowne zaplanowanie jednej repliki. Podobnie jak w przypadku limitów zasobów, najlepszym rozwiązaniem jest zdefiniowanie budżetów w aplikacjach, które wymagają minimalnej liczby replik, aby zawsze były obecne.

Wdrożenia są zwykle tworzone i zarządzane przy użyciu programu `kubectl create` lub `kubectl apply` . Utwórz wdrożenie, definiując plik manifestu w formacie YAML. 

Poniższy przykład tworzy podstawowe wdrożenie serwera sieci Web NGINX. Wdrożenie określa *trzy (3)* repliki do utworzenia i wymaga, aby port *80* był otwarty w kontenerze. Żądania zasobów i limity są również zdefiniowane dla procesora CPU i pamięci.

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

Bardziej złożone aplikacje mogą być tworzone przez dołączenie usług (takich jak moduły równoważenia obciążenia) w ramach manifestu YAML.

Aby uzyskać więcej informacji, zobacz [Kubernetes Deployments][kubernetes-deployments].

### <a name="package-management-with-helm"></a>Zarządzanie pakietami za pomocą Helm

[Helm][helm] jest często używany do zarządzania aplikacjami w Kubernetes. Zasoby można wdrażać, kompilując i używając istniejących publicznych *wykresów* Helm, które zawierają spakowaną wersję kodu aplikacji i manifestów YAML Kubernetes. Wykresy Helm można przechowywać lokalnie lub w repozytorium zdalnym, na przykład repozytorium [wykresu Helm Azure Container Registry][acr-helm].

Aby użyć Helm, zainstaluj klienta programu Helm na komputerze lub Użyj klienta Helm w [Azure Cloud Shell][azure-cloud-shell]. Wyszukaj lub Utwórz wykresy Helm, a następnie zainstaluj je w klastrze Kubernetes. Aby uzyskać więcej informacji, zobacz [Instalowanie istniejących aplikacji przy użyciu usługi Helm w AKS][aks-helm].

## <a name="statefulsets-and-daemonsets"></a>StatefulSets i DaemonSets

Przy użyciu usługi Kubernetes Scheduler kontroler wdrażania uruchamia repliki w dowolnym dostępnym węźle z dostępnymi zasobami. Chociaż takie podejście może być wystarczające w przypadku aplikacji bezstanowych, kontroler wdrożenia nie jest idealnym rozwiązaniem dla aplikacji, które wymagają:
* Trwała Konwencja nazewnictwa lub magazyn. 
* Replika na każdym węźle wyboru w klastrze.

Jednak dwa zasoby Kubernetes umożliwiają zarządzanie tymi typami aplikacji:

- *StatefulSets* zachować stan aplikacji poza indywidualnym cyklem życia, takim jak magazyn.
- *DaemonSets* upewnij się, że uruchomione wystąpienie w każdym węźle jest wczesne w procesie ładowania początkowego Kubernetes.

### <a name="statefulsets"></a>StatefulSets

Opracowywanie nowoczesnych aplikacji często ma na celu zastosowanie bezstanowych aplikacji. W przypadku aplikacji stanowych, takich jak te, które zawierają składniki bazy danych, można użyć *StatefulSets*. Podobnie jak w przypadku wdrożeń, StatefulSet tworzy i zarządza co najmniej jednym identycznym pod. Repliki w StatefulSet wykonują bezpieczne, sekwencyjne podejście do wdrożenia, skalowania, uaktualniania i kończenia. Konwencja nazewnictwa, nazwy sieciowe i magazyn utrzymują się, gdy replika jest zaplanowana z StatefulSet.

Zdefiniuj aplikację w formacie YAML przy użyciu polecenia `kind: StatefulSet` . W tym miejscu kontroler StatefulSet obsługuje wdrażanie wymaganych replik i zarządzanie nimi. Dane są zapisywane w magazynie trwałym, udostępnianym przez usługę Azure Managed Disks lub Azure Files. W przypadku programu StatefulSets odpowiedni magazyn trwały pozostaje nawet wtedy, gdy StatefulSet zostanie usunięta.

Aby uzyskać więcej informacji, zobacz [Kubernetes StatefulSets][kubernetes-statefulsets].

Repliki w StatefulSet są zaplanowane i uruchamiane w dowolnym z dostępnych węzłów w klastrze AKS. Aby upewnić się, że co najmniej jeden element znajduje się w zestawie działa w węźle, zamiast tego należy użyć elementu daemonset.

### <a name="daemonsets"></a>DaemonSets

W przypadku konkretnej kolekcji dzienników lub monitorowania może być konieczne uruchomienie go na wszystkich lub wybranych węzłach. Można użyć *elementu daemonset* wdrożyć jeden lub więcej identycznych zasobników, ale kontroler elementu daemonset zapewnia, że każdy określony węzeł uruchamia wystąpienie elementu.

Przed uruchomieniem domyślnego harmonogramu Kubernetes w kontrolerze elementu daemonset można zaplanować w węzłach na wczesny proces rozruchu klastra. Pozwala to zagwarantować, że zasobniki w elementu daemonset są uruchamiane przed zaplanowaniem tradycyjnych zasobników we wdrożeniu lub StatefulSet.

Podobnie jak w przypadku StatefulSets, elementu daemonset jest definiowana jako część definicji YAML za pomocą `kind: DaemonSet` .

Aby uzyskać więcej informacji, zobacz [Kubernetes DaemonSets][kubernetes-daemonset].

> [!NOTE]
> W przypadku używania [dodatku węzłów wirtualnych](virtual-nodes-cli.md#enable-virtual-nodes-addon)DaemonSets nie utworzy zasobnika w węźle wirtualnym.

## <a name="namespaces"></a>Przestrzenie nazw

Zasoby Kubernetes, takie jak y i wdrożenia, są logicznie pogrupowane w *przestrzeni nazw* , aby podzielić klaster AKS i ograniczyć tworzenie, wyświetlanie i zarządzanie dostępem do zasobów. Na przykład można utworzyć przestrzenie nazw w celu oddzielenia grup firmy. Użytkownicy mogą korzystać tylko z zasobami w ramach przypisanych przestrzeni nazw.

![Kubernetes przestrzenie nazw, aby logicznie podzielić zasoby i aplikacje](media/concepts-clusters-workloads/namespaces.png)

Podczas tworzenia klastra AKS dostępne są następujące przestrzenie nazw:

| Przestrzeń nazw | Opis |  
| ----------------- | ------------- |  
| *default*                                                                                 | Gdzie w przypadku braku nie są tworzone wartości i wdrożenia. W mniejszych środowiskach można wdrażać aplikacje bezpośrednio w domyślnej przestrzeni nazw bez tworzenia dodatkowych rozbarwień logicznych. W przypadku korzystania z interfejsu API Kubernetes, takiego jak with `kubectl get pods` , domyślna przestrzeń nazw jest używana, gdy nie jest określony.                                                        |  
| *kube-system* | Miejsce, w którym istnieją zasoby podstawowe, takie jak usługa DNS i serwer proxy, lub pulpit nawigacyjny Kubernetes. Zwykle nie są wdrażane własne aplikacje w tej przestrzeni nazw.                                      |  
| *kube-public*                                                                            | Zwykle nie są używane, ale mogą być używane do wyświetlania zasobów w całym klastrze i mogą być wyświetlane przez dowolnego użytkownika.                                                                                    |  


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
