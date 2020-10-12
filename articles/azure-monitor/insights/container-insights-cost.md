---
title: Koszt monitorowania Azure Monitor kontenerów | Microsoft Docs
description: W tym artykule opisano koszt monitorowania metryk & dane spisu zbierane przez Azure Monitor dla kontenerów, aby pomóc klientom w zarządzaniu ich użyciem i powiązanymi kosztami.
ms.topic: conceptual
ms.date: 05/29/2020
ms.openlocfilehash: a03e94fa7650c56a4d3b3beda3c27283329aebbe
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84204654"
---
# <a name="understand-monitoring-costs-for-azure-monitor-for-containers"></a>Informacje o monitorowaniu kosztów Azure Monitor dla kontenerów

Ten artykuł zawiera wskazówki dotyczące cen dla Azure Monitor kontenerów, które ułatwiają zapoznanie się z następującymi kwestiami:

* Jak oszacować koszty w przód przed włączeniem tego wglądu

* Sposób mierzenia kosztów po włączeniu Azure Monitor dla kontenerów dla co najmniej jednego kontenera

* Jak kontrolować zbieranie danych i zmniejszanie kosztów

Azure Monitor dzienników zbiera, indeksuje i przechowuje dane wygenerowane przez klaster Kubernetes. 

Model cen Azure Monitor jest przede wszystkim oparty na ilości danych pozyskanych w gigabajtach dziennie w obszarze roboczym Log Analytics. Koszt obszaru roboczego Log Analytics nie jest oparty tylko na ilości zbieranych danych, jest również zależny od wybranego planu i od czasu wybrania do przechowywania danych wygenerowanych z klastrów.

>[!NOTE]
>Wszystkie rozmiary i ceny są przeznaczone wyłącznie do oceny przykładowej. Zapoznaj się ze stroną [cennika](https://azure.microsoft.com/pricing/details/monitor/) Azure monitor, aby uzyskać najnowsze ceny na podstawie modelu cen Azure Monitor Log Analytics i regionu platformy Azure.

Poniżej znajduje się podsumowanie typów danych zbieranych z klastra Kubernetes z Azure Monitorami kontenerów mających wpływ na koszt i można je dostosować w zależności od użycia:

- Stdout, stderr dzienników kontenerów z każdego monitorowanego kontenera w każdej przestrzeni nazw Kubernetes w klastrze

- Zmienne środowiskowe kontenera z każdego monitorowanego kontenera w klastrze

- Zakończono zadania Kubernetes/zasobniki w klastrze, który nie wymaga monitorowania

- Aktywne odpadkami metryk Prometheus

- [Zbieranie dzienników diagnostycznych](../../aks/view-master-logs.md) w klastrze AKS w celu analizowania danych dzienników generowanych przez składniki Master, takie jak *polecenia-apiserver* i *polecenia-Controller-Manager*.

## <a name="what-is-collected-from-kubernetes-clusters"></a>Co jest zbierane z klastrów Kubernetes

Azure Monitor kontenerów zawiera wstępnie zdefiniowany zestaw metryk i zebranych elementów spisu, które są zapisywane jako dane dziennika w obszarze roboczym Log Analytics. Wszystkie metryki wymienione poniżej są zbierane domyślnie co minutę.

### <a name="node-metrics-collected"></a>Zebrane metryki węzła

Poniżej wymieniono 24 metryki na każdy węzeł, które są zbierane:

- cpuUsageNanoCores
- cpuCapacityNanoCores
- cpuAllocatableNanoCores
- memoryRssBytes
- memoryWorkingSetBytes
- memoryCapacityBytes
- memoryAllocatableBytes
- restartTimeEpoch
- używane (dysk)
- wolne (dysk)
- used_percent (dysk)
- io_time (diskio)
- zapisy (diskio)
- odczyty (diskio)
- write_bytes (diskio)
- write_time (diskio)
- iops_in_progress (diskio)
- read_bytes (diskio)
- read_time (diskio)
- err_in (NET)
- err_out (NET)
- bytes_recv (NET)
- bytes_sent (NET)
- Kubelet_docker_operations (Kubelet)

### <a name="container-metrics"></a>Metryki kontenera

Poniższa lista zawiera osiem metryk na zebrany kontener:

- cpuUsageNanoCores
- cpuRequestNanoCores
- cpuLimitNanoCores
- memoryRssBytes
- memoryWorkingSetBytes
- memoryRequestBytes
- memoryLimitBytes
- restartTimeEpoch

### <a name="cluster-inventory"></a>Spis klastra

Poniższa lista zawiera dane spisu klastra zbierane domyślnie:

- KubePodInventory – 1 na minutę na kontener
- KubeNodeInventory – 1 na węzeł na minutę
- KubeServices – 1 na usługę na minutę
- ContainerInventory – 1 na kontener na minutę

## <a name="estimating-costs-to-monitor-your-aks-cluster"></a>Szacowanie kosztów do monitorowania klastra AKS

Poniższe oszacowanie jest oparte na klastrze usługi Azure Kubernetes Service (AKS) z następującym przykładowym rozmiarem. Ponadto oszacowanie ma zastosowanie tylko do zbieranych danych metryk i spisu. W przypadku dzienników kontenerów (stdout, stderr i zmiennych środowiskowych) zależy to od rozmiarów dzienników generowanych przez obciążenie i są one wykluczone z naszych szacunków.

W przypadku włączenia monitorowania klastra AKS skonfigurowanego w następujący sposób:

- Trzy węzły
- Dwa dyski na węzeł
- Jeden interfejs sieciowy na węzeł
- 20 zasobników (jeden kontener w każdym powyżej = 20 kontenerów)
- Dwie przestrzenie nazw Kubernetes
- Pięć usług Kubernetes (w tym polecenia-systemowe, usługi i przestrzeń nazw)
- Częstotliwość zbierania = 60 s (wartość domyślna)

Tabele i ilość danych wygenerowanych na godzinę można zobaczyć w przydzielonym obszarze roboczym Log Analytics. Aby uzyskać więcej informacji na temat każdej z tych tabel, zobacz [rekordy kontenerów](container-insights-log-search.md#container-records).

|tabela | Szacowany rozmiar (MB/godzina) |
|------|---------------|
|Wyd. | 12,9 |
|InsightsMetrics | 11,3 |
|KubePodInventory | 1.5 |
|KubeNodeInventory | 0,75 |
|KubeServices | 0.13 |
|ContainerInventory | 3,6 |
|KubeHealth | 0,1 |
|KubeMonAgentEvents |0,005 |

Łącznie = 31 MB/godzina = 23,1 GB/miesiąc (jeden miesiąc = 31 dni)

Korzystając z domyślnych [cen](https://azure.microsoft.com/pricing/details/monitor/) dla log Analytics, który jest modelem płatność zgodnie z rzeczywistym użyciem, możesz oszacować koszt Azure monitor miesięcznie. Po dołączeniu rezerwacji pojemności cena będzie wyższa miesięcznie w zależności od wybranej rezerwacji.

## <a name="controlling-ingestion-to-reduce-cost"></a>Kontrolowanie pozyskiwania w celu obniżenia kosztów

Rozważmy scenariusz, w którym inna jednostka biznesowa organizacji udostępnia infrastrukturę Kubernetes i obszar roboczy Log Analytics. Z każdą jednostką biznesową oddzieloną przestrzenią nazw Kubernetes. Możesz wizualizować ilość danych pobieranych w każdym obszarze roboczym przy użyciu ostatnio wydanego skoroszytu. Skoroszyt **użycia usługi Container Insights** , który znajduje się w [galerii skoroszytów](../platform/workbooks-overview.md#getting-started), ułatwia wizualizację źródła danych bez konieczności kompilowania własnej biblioteki zapytań z tego, co udostępniamy w naszej dokumentacji. W tym skoroszycie znajdują się wykresy, za pomocą których można wyświetlić dane podlegające rozliczaniu z takich perspektyw, jak:

- Łączne dane do rozliczenia pobrane w GB według rozwiązania

- Dane do rozliczenia pobrane przez dzienniki kontenerów (Dzienniki aplikacji)

- Rozliczane dane dzienników kontenerów pozyskiwane według przestrzeni nazw Kubernetes

- Rozdzielone dane dzienników kontenera do rozliczenia według nazwy klastra

- Rozliczane dane dziennika kontenerów pobrane przez wpis logsource

- Rozliczane dane diagnostyczne pobrane przez dzienniki diagnostyki węzłów głównych

Aby dowiedzieć się więcej o zarządzaniu prawami i uprawnieniami do skoroszytu, przejrzyj [kontrolę dostępu](../platform/workbooks-access-control.md).

Po zakończeniu analizy, aby określić, które źródło lub źródła generuje najwięcej danych lub więcej danych, które przekraczają wymagania, można ponownie skonfigurować zbieranie danych. Szczegółowe informacje na temat konfigurowania kolekcji stdout, stderr i zmiennych środowiskowych opisano w artykule [Konfigurowanie ustawień zbierania danych agenta](container-insights-agent-config.md) .

Poniżej przedstawiono przykłady zmian, które można zastosować do klastra, modyfikując plik ConfigMap w celu ułatwienia kontroli kosztów.

1. Wyłącz dzienniki stdout dla wszystkich przestrzeni nazw w klastrze, modyfikując następujące elementy w pliku ConfigMap:

    ```
    [log_collection_settings]       
       [log_collection_settings.stdout]          
          enabled = false
    ```

2. Wyłącz zbieranie dzienników stderr z przestrzeni nazw programistycznych (na przykład **Dev-Test**) i Kontynuuj zbieranie dzienników stderr z innych przestrzeni nazw (na przykład **prod** i **default**) przez zmodyfikowanie następujących elementów w pliku ConfigMap:

    >[!NOTE]
    >Kolekcja dzienników polecenia-system jest domyślnie wyłączona. Ustawienie domyślne jest zachowywane, Dodawanie przestrzeni nazw **Dev-Test** do listy wykluczonych przestrzeni nazw jest stosowane do kolekcji dzienników stderr.

    ```
    [log_collection_settings.stderr]          
       enabled = true          
          exclude_namespaces = ["kube-system", "dev-test"]
    ```

3. Wyłącz kolekcję zmiennych środowiskowych w klastrze, modyfikując następujące elementy w pliku ConfigMap. Dotyczy to wszystkich kontenerów w każdej przestrzeni nazw Kubernetes. 

    ```
    [log_collection_settings.env_var]
        enabled = false
    ```

4. Aby wyczyścić ukończone zadania, określ zasady oczyszczania w definicji zadania, modyfikując następujące elementy w pliku ConfigMap:

    ```
    apiVersion: batch/v1
    kind: Job
    metadata:
      name: pi-with-ttl
    spec:
      ttlSecondsAfterFinished: 100
    ```

Po zastosowaniu co najmniej jednej z tych zmian do ConfigMaps należy zapoznać się z tematem [stosowanie zaktualizowanych ConfigMap](container-insights-prometheus-integration.md#applying-updated-configmap) w celu zastosowania go do klastra.

### <a name="prometheus-metrics-scraping"></a>OdPrometheusość metryk

Jeśli korzystasz z [wycinków Prometheus](container-insights-prometheus-integration.md), pamiętaj o następujących kwestiach, aby ograniczyć liczbę metryk zbieranych z klastra:

- Upewnij się, że częstotliwość odpadków jest ustawiona optymalnie (wartość domyślna to 60 sekund). Chociaż można zwiększyć częstotliwość do 15 sekund, należy się upewnić, że metryki, które są odpadków, są publikowane z tą częstotliwością. W przeciwnym razie wiele zduplikowanych metryk i zostanie wysłanych do obszaru roboczego Log Analytics w odstępach czasu dodawania do kosztów pozyskiwania i przechowywania danych, ale nie są one mniejsze. 

- Azure Monitor kontenerów obsługuje wykluczanie & listy dołączania według nazwy metryki. Na przykład w przypadku wycinków **kubedns** metryk w klastrze mogą znajdować się setki z nich, które domyślnie odnoszą się do nich, ale najprawdopodobniej interesują się one tylko podzbiorem. Upewnij się, że określono listę metryk dla wycinków, lub Wyklucz inne z wyjątkiem kilku, aby zaoszczędzić na woluminie pozyskiwania danych. Można łatwo włączyć odchody i nie używać wielu z tych metryk, co spowoduje dodanie dodatkowych opłat do Log Analytics rachunku.

- W przypadku wycinków za pośrednictwem adnotacji należy przefiltrować według przestrzeni nazw, aby wykluczyć braki metryk pod względem przestrzeni nazw, których nie używasz (na przykład przestrzeń nazw **Dev-Test** ).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat sposobu zrozumienia, jakie koszty mogą opierać się na najnowszych wzorcach użycia danych zbieranych za pomocą Azure Monitor dla kontenerów, zobacz [Zarządzanie użyciem i szacowanie kosztów](../platform/manage-cost-storage.md).