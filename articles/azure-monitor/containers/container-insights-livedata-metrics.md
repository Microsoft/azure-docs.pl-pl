---
title: Wyświetlanie metryk w czasie rzeczywistym za pomocą usługi Container Insights | Microsoft Docs
description: W tym artykule opisano widok metryk w czasie rzeczywistym bez używania polecenia kubectl z usługą Container Insights.
ms.topic: conceptual
ms.date: 10/15/2019
ms.custom: references_regions
ms.openlocfilehash: 2e3a8a417a934374c2c0b256bb65a471d98fdebe
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101731854"
---
# <a name="how-to-view-metrics-in-real-time"></a>Jak wyświetlać metryki w czasie rzeczywistym

Funkcja informacji o usłudze Container Insights (wersja zapoznawcza) umożliwia wizualizację metryk dotyczących stanu węzła i pod w klastrze w czasie rzeczywistym. Emuluje bezpośredni dostęp do `kubectl top nodes` `kubectl get pods –all-namespaces` poleceń, i `kubectl get nodes` umożliwia wywoływanie, analizowanie i wizualizowanie danych w wykresach wydajności, które są zawarte w tym temacie.

Ten artykuł zawiera szczegółowe omówienie i pomaga zrozumieć, jak korzystać z tej funkcji.

>[!NOTE]
>Klastry AKS włączone jako [klastry prywatne](https://azure.microsoft.com/updates/aks-private-cluster/) są nieobsługiwane w przypadku tej funkcji. Ta funkcja używa bezpośrednio dostępu do interfejsu API Kubernetes za pomocą serwera proxy z przeglądarki. Włączenie zabezpieczeń sieci w celu blokowania interfejsu API Kubernetes z tego serwera proxy spowoduje zablokowanie tego ruchu.

Aby uzyskać pomoc dotyczącą konfigurowania lub rozwiązywania problemów dotyczących danych na żywo (wersja zapoznawcza), zapoznaj się z naszym [przewodnikiem Instalatora](container-insights-livedata-setup.md).

## <a name="how-it-works"></a>Jak to działa

Funkcja dane dynamiczne (wersja zapoznawcza) bezpośrednio uzyskuje dostęp do interfejsu API Kubernetes. dodatkowe informacje o modelu uwierzytelniania można znaleźć [tutaj](https://kubernetes.io/docs/concepts/overview/kubernetes-api/).

Ta funkcja wykonuje operację sondowania dla punktów końcowych metryk (w tym `/api/v1/nodes` , `/apis/metrics.k8s.io/v1beta1/nodes` i `/api/v1/pods` ), co jest domyślnie co pięć sekund. Te dane są przechowywane w pamięci podręcznej w przeglądarce i przedstawiane na czterech wykresach wydajności zawartych w usłudze Container Insights na karcie **klaster** , wybierając pozycję **Przejdź na żywo (wersja zapoznawcza)**. Każda kolejna sonda jest przedstawiana w stopniowanym oknie wizualizacji.

![Opcja Przejdź na żywo w widoku klastra](./media/container-insights-livedata-metrics/cluster-view-go-live-example-01.png)

Interwał sondowania jest konfigurowany z listy rozwijanej **ustaw interwał** , co pozwala na ustawienie sondowania dla nowych danych co 1, 5, 15 i 30 sekund.

![Przechodzenie do listy rozwijanej interwału sondowania](./media/container-insights-livedata-metrics/cluster-view-polling-interval-dropdown.png)

>[!IMPORTANT]
>Zalecamy ustawienie interwału sondowania na jeden sekund podczas rozwiązywania problemu przez krótki czas. Te żądania mogą mieć wpływ na dostępność i ograniczenie interfejsu API Kubernetes w klastrze. Następnie skonfiguruj ponownie do dłuższego interwału sondowania.

>[!IMPORTANT]
>Podczas wykonywania tej funkcji żadne dane nie są trwale przechowywane. Wszystkie informacje przechwycone w ramach tej sesji zostaną natychmiast usunięte po zamknięciu przeglądarki lub opuszczeniu funkcji. Dane pozostają obecne tylko dla wizualizacji w oknie 5 minut; wszystkie metryki starsze niż pięć minut również są trwale usuwane.

Te wykresy nie mogą zostać przypięte do ostatniego pulpitu nawigacyjnego platformy Azure wyświetlanego w trybie na żywo.

## <a name="metrics-captured"></a>Przechwycone metryki

### <a name="node-cpu-utilization---node-memory-utilization-"></a>Użycie procesora CPU przez węzeł%/procent wykorzystania pamięci węzła%

Te dwa wykresy wydajności są mapowane na równoważność wywoływania `kubectl top nodes` i przechwytywania wyników dla **% kolumn procesora CPU** i **pamięci%** do odpowiednich wykresów.

![Przykładowe wyniki polecenia kubectl głównych węzłów](./media/container-insights-livedata-metrics/kubectl-top-nodes-example.png)

![Wykres procentowy użycia procesora CPU w węzłach](./media/container-insights-livedata-metrics/cluster-view-node-cpu-util.png)

![Wykres procentowy wykorzystania pamięci węzła](./media/container-insights-livedata-metrics/cluster-view-node-memory-util.png)

Obliczenia percentylu będą działać w większych klastrach, aby ułatwić identyfikowanie odstających węzłów w klastrze. Na przykład, aby zrozumieć, czy węzły są w sposób nieużywany do skalowania w dół. Korzystając z **minimalnej** agregacji, można zobaczyć, które węzły mają niskie użycie w klastrze. Aby kontynuować badanie, należy wybrać kartę **węzły** i posortować siatkę według użycia procesora CPU lub pamięci.

Pomaga to również zrozumieć, które węzły są przekazywane do ich limitów, oraz czy może być wymagane skalowanie w poziomie. Użycie agregacji **Max** i **p95** może pomóc sprawdzić, czy w klastrze znajdują się węzły z wysokim użyciem zasobów. W celu dalszej analizy należy ponownie przełączyć się na kartę **węzły** .

### <a name="node-count"></a>Liczba węzłów

Ten wykres wydajności jest mapowany na odpowiednik wywołania `kubectl get nodes` i mapowania kolumny **stan** na wykres pogrupowany według typów stanu.

![Przykładowe wyniki pobierania węzłów polecenia kubectl](./media/container-insights-livedata-metrics/kubectl-get-nodes-example.png)

![Wykres liczby węzłów](./media/container-insights-livedata-metrics/cluster-view-node-count-01.png)

Węzły są zgłaszane w stanie **gotowym** lub **niegotowym** . Są zliczane (i tworzona jest łączna liczba), a wyniki tych dwóch agregacji są przedstawiane na wykresie.
Na przykład, aby zrozumieć, czy węzły są uwzględniane w Stanach zakończonych niepowodzeniem. Korzystając z agregacji **nieprzygotowanej** , można szybko zobaczyć liczbę węzłów w klastrze, które są obecnie w stanie **braku gotowości** .

### <a name="active-pod-count"></a>Liczba aktywnych pod

Ten wykres wydajności jest mapowany na odpowiednik wywołania `kubectl get pods –all-namespaces` i mapuje kolumnę **stan** na wykres pogrupowany według typów stanu.

![Polecenia kubectl uzyskać przykładowe wyniki](./media/container-insights-livedata-metrics/kubectl-get-pods-example.png)

![Wykres z liczbą węzłów pod](./media/container-insights-livedata-metrics/cluster-view-node-pod-count.png)

>[!NOTE]
>Nazwy stanu jako interpretowane przez `kubectl` mogą nie być dokładnie zgodne na wykresie.

## <a name="next-steps"></a>Następne kroki

Wyświetl [przykłady zapytań dzienników](container-insights-log-search.md#search-logs-to-analyze-data) , aby wyświetlić wstępnie zdefiniowane zapytania i przykłady do tworzenia alertów, wizualizacji lub przeprowadzenia dalszej analizy klastrów.
