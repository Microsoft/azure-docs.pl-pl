---
title: Monitorowanie kondycji klastra Kubernetes za pomocą Azure Monitor dla kontenerów | Microsoft Docs
description: W tym artykule opisano, jak można wyświetlać i analizować kondycję klastrów AKS i innych niż AKS przy użyciu Azure Monitor dla kontenerów.
ms.topic: conceptual
ms.date: 05/18/2020
ms.openlocfilehash: 94fdb6388134eae326ed5a8e46fbd3d13f8da2d5
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83649579"
---
# <a name="understand-kubernetes-cluster-health-with-azure-monitor-for-containers"></a>Informacje o kondycji klastra Kubernetes za pomocą Azure Monitor dla kontenerów

Za pomocą Azure Monitor dla kontenerów monitoruje i raportuje stan kondycji składników infrastruktury zarządzanej oraz wszystkie węzły działające w dowolnym klastrze Kubernetes obsługiwanym przez Azure Monitor dla kontenerów. To środowisko przekracza stan kondycji klastra obliczone i zgłoszone w [widoku wielu klastrów](container-insights-analyze.md#multi-cluster-view-from-azure-monitor), gdzie teraz można zrozumieć, czy co najmniej jeden węzeł w klastrze jest ograniczony do zasobów lub węzeł lub pod jest niedostępny, co może mieć wpływ na działającą aplikację w klastrze na podstawie nadzorowanych metryk.

>[!NOTE]
>Funkcja kondycji przechodzi do prywatnej wersji zapoznawczej na koniec czerwca 2020. Aby uzyskać dodatkowe informacje, zapoznaj się z następującym [ogłoszeniem aktualizacji platformy Azure](https://azure.microsoft.com/updates/ci-health-limited-preview/).
>

Informacje o sposobie włączania Azure Monitor dla kontenerów znajdują się w sekcji Dołączanie [Azure monitor dla kontenerów](container-insights-onboard.md).

>[!NOTE]
>Aby obsługiwać klastry aparatu AKS, sprawdź, czy są spełnione następujące warunki:
>- Używa najnowszej wersji [klienta Helm](https://helm.sh/docs/using_helm/).
>- Wersja agenta kontenerowego to *Microsoft/OMS: ciprod11012019*. Aby uaktualnić agenta, zobacz [Uaktualnianie agenta w klastrze Kubernetes](container-insights-manage-agent.md#how-to-upgrade-the-azure-monitor-for-containers-agent).
>

## <a name="overview"></a>Omówienie

W Azure Monitor dla kontenerów funkcja kondycja (wersja zapoznawcza) udostępnia aktywne monitorowanie kondycji klastra Kubernetes, które ułatwia identyfikowanie i diagnozowanie problemów. Zapewnia możliwość wyświetlania znaczących problemów wykrytych. Monitory oceniające kondycję klastra są uruchamiane na kontenerze kontenera w klastrze, a dane dotyczące kondycji są zapisywane w tabeli **KubeHealth** w obszarze roboczym log Analytics. 

Kondycja klastra Kubernetes jest oparta na wielu scenariuszach monitorowania zorganizowanych według następujących obiektów Kubernetes i streszczeń:

- Infrastruktura Kubernetes — dostarcza pakiet zbiorczy serwera interfejsu API Kubernetes, ReplicaSets i DaemonSets uruchomionego na węzłach wdrożonych w klastrze przez ocenę użycia procesora i pamięci oraz dostępności

    ![Widok zbiorczy kondycji infrastruktury Kubernetes](./media/container-insights-health/health-view-kube-infra-01.png)

- Węzły — udostępnia pakiet zbiorczy pul węzłów i stan poszczególnych węzłów w każdej puli, oceniając użycie procesora i pamięci oraz stan węzła zgłoszony przez Kubernetes.

    ![Widok zbiorczy kondycji węzłów](./media/container-insights-health/health-view-nodes-01.png)

Obecnie obsługiwany jest tylko stan wirtualnej kubelet. Stan kondycji w przypadku użycia procesora CPU i pamięci wirtualnych węzłów kublet jest raportowany jako **nieznany**, ponieważ sygnał nie został odebrany z nich.

Wszystkie monitory są wyświetlane w układzie hierarchicznym w okienku hierarchia kondycji, gdzie monitor zagregowany reprezentujący obiekt Kubernetes lub abstrakcję (czyli infrastruktura Kubernetesa lub węzły) to najbardziej górny monitor odzwierciedlający łączną kondycję wszystkich zależnych monitorów podrzędnych. Kluczowe scenariusze monitorowania używane do wygenerowania kondycji są następujące:

* Oceń użycie procesora z węzła i kontenera.
* Oceń użycie pamięci z węzła i kontenera.
* Stan i węzły na podstawie obliczenia stanu gotowości zgłoszonego przez Kubernetes.

Do wskazywania stanu używane są następujące ikony:

|Ikona|Znaczenie|  
|--------|-----------|  
|![Zielona ikona znacznika wyboru oznacza dobrą kondycję](./media/container-insights-health/healthyicon.png)|Sukces, kondycja OK (zielony)|  
|![Żółty trójkąt i wykrzyknik oznaczają ostrzeżenie](./media/container-insights-health/warningicon.png)|Ostrzeżenie (żółty)|  
|![Czerwony przycisk z białym znakiem X oznacza stan krytyczny](./media/container-insights-health/criticalicon.png)|Krytyczny (czerwony)|  
|![Szara ikona](./media/container-insights-health/grayicon.png)|Nieznane (szare)|  

## <a name="monitor-configuration"></a>Konfiguracja monitora

Aby zrozumieć zachowanie i konfigurację poszczególnych monitorów obsługujących Azure Monitor dla funkcji kondycji kontenerów, zobacz [Przewodnik po konfiguracji monitora kondycji](container-insights-health-monitors-config.md).

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). 

## <a name="view-health-of-an-aks-or-non-aks-cluster"></a>Wyświetlanie kondycji klastra AKS lub innego niż AKS

Dostęp do Azure Monitor funkcji kondycji kontenerów (wersja zapoznawcza) jest dostępny bezpośrednio w klastrze AKS, wybierając pozycję **szczegółowe** dane w okienku po lewej stronie Azure Portal. W sekcji **szczegółowe informacje** wybierz pozycję **kontenery**. 

Aby wyświetlić kondycję z klastra AKS, czyli klastra aparatu AKS hostowanego lokalnie lub na Azure Stack, wybierz pozycję **Azure monitor** w lewym okienku w Azure Portal. W sekcji **szczegółowe informacje** wybierz pozycję **kontenery**.  Na stronie wiele klastrów wybierz klaster spoza AKS z listy.

W Azure Monitor dla kontenerów na stronie **klaster** wybierz pozycję **kondycja**.

![Przykład pulpitu nawigacyjnego kondycji klastra](./media/container-insights-health/container-insights-health-page.png)

## <a name="review-cluster-health"></a>Sprawdzanie kondycji klastra

Gdy zostanie otwarta strona kondycja, w siatce **aspektów kondycji** zostanie wybrana Domyślna **infrastruktura Kubernetes** .  Siatka podsumowuje bieżący stan zbiorczy kondycji infrastruktury Kubernetes i węzłów klastra. Wybranie dowolnego aspektu kondycji aktualizuje wyniki w okienku hierarchia kondycji (czyli środkowym okienku) i pokazuje wszystkie monitory podrzędne w układzie hierarchicznym, wyświetlając ich bieżący stan kondycji. Aby wyświetlić więcej informacji na temat dowolnego monitora zależnego, można wybrać jeden, a okienko właściwości zostanie automatycznie wyświetlone po prawej stronie. 

![Okienko właściwości kondycji klastra](./media/container-insights-health/health-view-property-pane.png)

W okienku właściwości należy poznać następujące informacje:

- Na karcie **Przegląd** wyświetlany jest bieżący stan wybranego monitora, kiedy Monitor został ostatnio obliczony, oraz czas wystąpienia ostatniej zmiany stanu. Dodatkowe informacje są wyświetlane w zależności od typu monitora wybranego w hierarchii.

    W przypadku wybrania monitora zagregowanego w okienku hierarchia kondycji na karcie **Przegląd** w okienku właściwości zostanie wyświetlona zestawienie łącznej liczby monitorów podrzędnych w hierarchii, a liczba monitorów zagregowanych jest w stanie krytycznym, ostrzegawczym i prawidłowym. 

    ![Karta Omówienie okienka właściwości kondycji dla monitora zagregowanego](./media/container-insights-health/health-overview-aggregate-monitor.png)

    W przypadku wybrania monitora jednostkowego w okienku hierarchia kondycji zostanie on wyświetlony w obszarze **ostatni stan zmiany** poprzednich próbek obliczanych i raportowanych przez agenta kontenerów w ciągu ostatnich czterech godzin. Jest to oparte na obliczaniu monitorów jednostkowych w celu porównania kilku kolejnych wartości w celu określenia jego stanu. Na przykład jeśli wybrano monitor jednostki *gotowego stanu* , pokazuje ostatnie dwie próbki kontrolowane przez parametr *ConsecutiveSamplesForStateTransition*. Aby uzyskać więcej informacji, Zobacz szczegółowy opis [monitorów jednostek](container-insights-health-monitors-config.md#unit-monitors).
    
    ![Karta przegląd okienka właściwości kondycji](./media/container-insights-health/health-overview-unit-monitor.png)

    Jeśli czas zgłoszony przez **ostatnią zmianę stanu** jest dzień lub starszy, jest to wynik braku zmian stanu dla tego monitora. Jeśli jednak ostatni otrzymany przykład dla monitora jednostkowego ma więcej niż cztery godziny, prawdopodobnie wskazuje, że kontener nie wysyłał danych. Jeśli Agent wie, że istnieje określony zasób, na przykład węzeł, ale nie otrzymał danych z monitorów użycia procesora lub pamięci w węźle (na przykład), stan kondycji monitora jest ustawiony na **nieznany**.  

- Na karcie**Konfiguracja** wyświetlane są domyślne ustawienia parametrów konfiguracji (tylko dla monitorów jednostkowych, nie zagregowanych monitorów) i ich wartości.
- Na karcie **wiedza** zawiera informacje objaśniające zachowanie monitora i jego ocenę pod kątem stanu złej kondycji.

Dane monitorowania na tej stronie nie są odświeżane automatycznie i należy wybrać pozycję **Odśwież** w górnej części strony, aby zobaczyć najnowszy stan kondycji otrzymany z klastra.

## <a name="next-steps"></a>Następne kroki

Wyświetl [przykłady zapytań dotyczących dzienników](container-insights-log-search.md#search-logs-to-analyze-data) , aby wyświetlić wstępnie zdefiniowane zapytania i przykłady do oszacowania lub dostosowania do alertów, wizualizacji lub analizowania klastrów.
