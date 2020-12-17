---
title: Automatyczne skalowanie klastrów usługi Azure HDInsight
description: Funkcja automatycznego skalowania umożliwia automatyczne skalowanie klastrów usługi Azure HDInsight na podstawie metryk harmonogramu lub wydajności.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: contperf-fy21q1, contperf-fy21q2
ms.date: 12/14/2020
ms.openlocfilehash: 2b23b4256e79723ce0b5edafd59186dc345eb791
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97629259"
---
# <a name="automatically-scale-azure-hdinsight-clusters"></a>Automatyczne skalowanie klastrów usługi Azure HDInsight

Funkcja bezpłatnego automatycznego skalowania usługi Azure HDInsight może automatycznie zwiększyć lub zmniejszyć liczbę węzłów procesu roboczego w klastrze na podstawie wcześniej ustawionych kryteriów. Funkcja automatycznego skalowania działa przez skalowanie liczby węzłów w ramach predefiniowanych limitów w oparciu o metryki wydajności lub harmonogram operacji skalowania w górę i w dół.

## <a name="how-it-works"></a>Jak to działa

Funkcja automatycznego skalowania używa dwóch typów warunków do wyzwalania zdarzeń skalowania: progi dla różnych metryk wydajności klastra (nazywane *skalowaniem opartym na założeniu*) i wyzwalacze oparte na czasie (zwane *skalowaniem opartym na harmonogramie*). Skalowanie oparte na obciążeniu zmienia liczbę węzłów w klastrze w określonym zakresie, aby zapewnić optymalne użycie procesora i zminimalizować koszt działania. Skalowanie oparte na harmonogramie zmienia liczbę węzłów w klastrze na podstawie harmonogramu operacji skalowania w górę i w dół.

Poniższy klip wideo zawiera omówienie wyzwań, które są rozwiązywane przez automatyczne skalowanie i w jaki sposób może pomóc w kontroli kosztów w usłudze HDInsight.

> [!VIDEO https://www.youtube.com/embed/UlZcDGGFlZ0?WT.mc_id=dataexposed-c9-niner]

### <a name="choosing-load-based-or-schedule-based-scaling"></a>Wybieranie skalowania opartego na ładowaniu lub harmonogramie

Podczas wybierania typu skalowania należy wziąć pod uwagę następujące czynniki:

* WARIANCJA obciążenia: czy obciążenie klastra jest zgodne ze spójnym wzorcem w określonych porach, w określonych dniach? W przeciwnym razie planowanie oparte na obciążeniu jest lepszą opcją.
* Wymagania SLA: skalowanie automatyczne jest aktywne, a nie predykcyjne. Czy istnieje wystarczające opóźnienie między momentem, kiedy obciążenie zacznie się zwiększać i kiedy klaster musi mieć rozmiar docelowy? Jeśli istnieją ścisłe wymagania umowy SLA, a obciążenie jest stałym znanym wzorcem, jest to lepsza opcja.

### <a name="cluster-metrics"></a>Metryki klastra

Automatyczne skalowanie w sposób ciągły monitoruje klaster i zbiera następujące metryki:

|Metric|Opis|
|---|---|
|Łączny czas oczekiwania na procesor|Łączna liczba rdzeni wymaganych do rozpoczęcia wykonywania wszystkich oczekujących kontenerów.|
|Całkowita liczba oczekujących pamięci|Całkowita ilość pamięci (w MB) wymagana do uruchomienia wszystkich oczekujących kontenerów.|
|Łączny bezpłatny procesor CPU|Suma wszystkich nieużywanych rdzeni w aktywnych węzłach procesu roboczego.|
|Całkowita ilość wolnej pamięci|Suma nieużywanej pamięci (w MB) w węzłach aktywnych procesów roboczych.|
|Użyta pamięć na węzeł|Obciążenie węzła procesu roboczego. Węzeł procesu roboczego, na którym jest używana 10 GB pamięci, jest uznawany za większy niż proces roboczy z 2 GB używanej pamięci.|
|Liczba wzorców aplikacji na węzeł|Liczba kontenerów wzorca aplikacji (AM) uruchomionych w węźle procesu roboczego. Węzeł procesu roboczego obsługujący dwa kontenery AM jest uznawany za ważniejszy niż węzeł roboczy obsługujący kontenery zero AM.|

Powyższe metryki są sprawdzane co 60 sekund. Można skonfigurować operacje skalowania dla klastra przy użyciu dowolnej z tych metryk.

### <a name="load-based-scale-conditions"></a>Warunki skalowania na podstawie obciążenia

Po wykryciu następujących warunków funkcja automatycznego skalowania wystawia żądanie skalowania:

|Skalowanie w górę|Skalowanie w dół|
|---|---|
|Łączny czas oczekiwania procesora CPU jest większy niż całkowity bezpłatny procesor CPU przez więcej niż 3 minuty.|Całkowita liczba oczekujących procesorów CPU jest mniejsza niż całkowity bezpłatny procesor CPU przez więcej niż 10 minut.|
|Całkowita liczba oczekujących pamięci jest większa niż całkowita ilość wolnej pamięci przez więcej niż 3 minuty.|Całkowita ilość oczekujących pamięci jest mniejsza niż całkowita ilość wolnej pamięci przez więcej niż 10 minut.|

W celu skalowania w górę automatyczne skalowanie jest rozwiązywane przez żądanie skalowania, aby dodać wymaganą liczbę węzłów. Skalowanie odbywa się na podstawie liczby nowych węzłów procesu roboczego potrzebnych do spełnienia bieżących wymagań procesora i pamięci.

W przypadku skalowania w dół automatyczne skalowanie wystawia żądanie usunięcia pewnej liczby węzłów. Skalowanie w dół bazuje na liczbie kontenerów AM na węzeł. Oraz bieżące wymagania dotyczące procesora CPU i pamięci. Usługa wykrywa również, które węzły są kandydatami do usunięcia na podstawie bieżącego wykonywania zadania. Operacja skalowania w dół najpierw likwidowanie węzłów, a następnie usunięcie ich z klastra.

### <a name="cluster-compatibility"></a>Zgodność klastra

> [!Important]
> Funkcja automatycznego skalowania usługi Azure HDInsight została ogólnie udostępniona 7 listopada 2019 r. dla klastrów Spark i Hadoop. Zawiera ona ulepszenia niedostępne w wersji zapoznawczej tej funkcji. Jeśli chcesz korzystać z funkcji automatycznego skalowania w klastrze Spark utworzonym przed 7 listopada 2019 r., zalecaną ścieżką jest utworzenie nowego klastra i włączenie automatycznego skalowania w nowym klastrze.
>
> Funkcja automatycznego skalowania dla zapytania interaktywnego (LLAP) została udostępniona do ogólnej dostępności dla HDI 4,0 w sierpniu 27, 2020. Klastry HBase są nadal w wersji zapoznawczej. Skalowanie automatyczne jest dostępne tylko w klastrach Spark, Hadoop, Interactive Query i HBase.

W poniższej tabeli opisano typy i wersje klastra, które są zgodne z funkcją skalowania automatycznego.

| Wersja | Spark | Hive | Zapytanie interakcyjne | HBase | Kafka | Storm | ML |
|---|---|---|---|---|---|---|---|
| HDInsight 3,6 bez ESP | Tak | Tak | Tak | Tak* | Nie | Nie | Nie |
| HDInsight 4,0 bez ESP | Tak | Tak | Tak | Tak* | Nie | Nie | Nie |
| HDInsight 3,6 z ESP | Tak | Tak | Tak | Tak* | Nie | Nie | Nie |
| HDInsight 4,0 z ESP | Tak | Tak | Tak | Tak* | Nie | Nie | Nie |

\* Klastry HBase można konfigurować tylko dla skalowania opartego na harmonogramie, a nie na podstawie obciążenia.

## <a name="get-started"></a>Wprowadzenie

### <a name="create-a-cluster-with-load-based-autoscaling"></a>Tworzenie klastra z automatycznym skalowaniem na podstawie obciążenia

Aby włączyć funkcję automatycznego skalowania z skalowaniem opartym na obciążeniu, wykonaj następujące czynności w ramach normalnego procesu tworzenia klastra:

1. Na karcie **Konfiguracja i Cennik** zaznacz pole wyboru **Włącz automatyczne skalowanie** .
1. Wybierz pozycję **Załaduj na podstawie** **typu automatycznego skalowania**.
1. Wprowadź wartości zamierzone dla następujących właściwości:  

    * Początkowa **Liczba węzłów** dla **węzła procesu roboczego**.
    * **Minimalna** liczba węzłów procesu roboczego.
    * **Maksymalna** liczba węzłów procesu roboczego.

    ![Włącz automatyczne skalowanie w węźle procesu roboczego](./media/hdinsight-autoscale-clusters/azure-portal-cluster-configuration-pricing-autoscale.png)

Początkowa liczba węzłów procesu roboczego musi należeć do zakresu od minimum do maksimum włącznie. Ta wartość definiuje początkowy rozmiar klastra podczas jego tworzenia. Minimalna liczba węzłów procesu roboczego powinna być ustawiona na trzy lub więcej. Skalowanie klastra do mniej niż trzech węzłów może spowodować zatrzymanie trybu awaryjnego z powodu niewystarczającej replikacji plików.  Aby uzyskać więcej informacji, zobacz [Uruchamianie w trybie awaryjnym](./hdinsight-scaling-best-practices.md#getting-stuck-in-safe-mode).

### <a name="create-a-cluster-with-schedule-based-autoscaling"></a>Tworzenie klastra z funkcją automatycznego skalowania opartego na harmonogramie

Aby włączyć funkcję automatycznego skalowania z skalowaniem opartym na harmonogramie, wykonaj następujące czynności w ramach normalnego procesu tworzenia klastra:

1. Na karcie **Konfiguracja i Cennik** zaznacz pole wyboru **Włącz automatyczne skalowanie** .
1. Wprowadź **liczbę węzłów** dla **węzła proces roboczy**, który kontroluje limit skalowania klastra w górę.
1. Wybierz opcję **oparte na harmonogramie** w obszarze **Typ automatycznego skalowania**.
1. Wybierz pozycję **Konfiguruj** , aby otworzyć okno **konfiguracji skalowania automatycznego** .
1. Wybierz strefę czasową, a następnie kliknij pozycję **+ Dodaj warunek**
1. Wybierz dni tygodnia, do których ma zostać zastosowany nowy warunek.
1. Edytuj czas, w którym warunek powinien obowiązywać, oraz liczbę węzłów, do których należy przeskalować klaster.
1. W razie konieczności Dodaj więcej warunków.

    ![Włączanie tworzenia opartego na harmonogramie węzłów procesu roboczego](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-schedule-creation.png)

Liczba węzłów musi zawierać się w przedziale od 3 do maksymalnej liczby wprowadzonych węzłów procesu roboczego przed dodaniem warunków.

### <a name="final-creation-steps"></a>Ostateczne kroki tworzenia

Wybierz typ maszyny wirtualnej dla węzłów procesu roboczego, wybierając maszynę wirtualną z listy rozwijanej w obszarze **rozmiar węzła**. Po wybraniu typu maszyny wirtualnej dla każdego typu węzła można zobaczyć szacowany zakres kosztów dla całego klastra. Dostosuj typy maszyn wirtualnych tak, aby pasowały do budżetu.

![Włącz rozmiar węzła skalowania automatycznego na podstawie harmonogramu węzła procesu roboczego](./media/hdinsight-autoscale-clusters/azure-portal-cluster-configuration-pricing-vmsize.png)

Twoja subskrypcja ma przydział pojemności dla każdego regionu. Łączna liczba rdzeni węzłów głównych i maksymalnych węzłów procesów roboczych nie może przekroczyć limitu przydziału pojemności. Ten limit przydziału jest jednak limitem nieelastycznym; zawsze możesz utworzyć bilet pomocy technicznej, aby ułatwić jego zwiększenie.

> [!Note]  
> W przypadku przekroczenia całkowitego limitu przydziału rdzeni zostanie wyświetlony komunikat o błędzie z informacją o tym, że "maksymalny węzeł przekroczył dostępne rdzenie w tym regionie, wybierz inny region lub skontaktuj się z pomocą techniczną, aby zwiększyć przydział".

Aby uzyskać więcej informacji na temat tworzenia klastra usługi HDInsight przy użyciu Azure Portal, zobacz [Tworzenie klastrów opartych na systemie Linux w usłudze HDInsight przy użyciu Azure Portal](hdinsight-hadoop-create-linux-clusters-portal.md).  

### <a name="create-a-cluster-with-a-resource-manager-template"></a>Tworzenie klastra przy użyciu szablonu Menedżer zasobów

#### <a name="load-based-autoscaling"></a>Skalowanie automatyczne przy użyciu obciążenia

Można utworzyć klaster usługi HDInsight z użyciem automatycznego skalowania Azure Resource Manager szablonu, dodając `autoscale` węzeł do `computeProfile`  >  `workernode` sekcji z właściwościami `minInstanceCount` i `maxInstanceCount` jak pokazano w poniższym fragmencie kodu JSON. Aby zapoznać się z kompletnym szablonem Menedżer zasobów, zobacz [szablon szybkiego startu: Wdróż klaster Spark z włączoną funkcją automatycznego skalowania opartego na ładowaniu](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-autoscale-loadbased).

```json
{
  "name": "workernode",
  "targetInstanceCount": 4,
  "autoscale": {
      "capacity": {
          "minInstanceCount": 3,
          "maxInstanceCount": 10
      }
  },
  "hardwareProfile": {
      "vmSize": "Standard_D13_V2"
  },
  "osProfile": {
      "linuxOperatingSystemProfile": {
          "username": "[parameters('sshUserName')]",
          "password": "[parameters('sshPassword')]"
      }
  },
  "virtualNetworkProfile": null,
  "scriptActions": []
}
```

#### <a name="schedule-based-autoscaling"></a>Skalowanie automatyczne oparte na harmonogramie

Można utworzyć klaster usługi HDInsight z użyciem harmonogramu automatycznego skalowania szablonu Azure Resource Manager, dodając `autoscale` węzeł do `computeProfile`  >  `workernode` sekcji. `autoscale`Węzeł zawiera `recurrence` `timezone` i `schedule` , który opisuje, kiedy zmiana zostanie przeprowadzona. Aby zapoznać się z kompletnym szablonem Menedżer zasobów, zobacz [wdrażanie klastra Spark z włączonym automatycznym skalowaniem opartym na harmonogramie](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-autoscale-schedulebased).

```json
{
  "autoscale": {
    "recurrence": {
      "timeZone": "Pacific Standard Time",
      "schedule": [
        {
          "days": [
            "Monday",
            "Tuesday",
            "Wednesday",
            "Thursday",
            "Friday"
          ],
          "timeAndCapacity": {
            "time": "11:00",
            "minInstanceCount": 10,
            "maxInstanceCount": 10
          }
        }
      ]
    }
  },
  "name": "workernode",
  "targetInstanceCount": 4
}
```

### <a name="enable-and-disable-autoscale-for-a-running-cluster"></a>Włączanie i wyłączanie skalowania automatycznego dla działającego klastra

#### <a name="using-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Aby włączyć automatyczne skalowanie w uruchomionym klastrze, wybierz pozycję **rozmiar klastra** w obszarze **Ustawienia**. Następnie wybierz pozycję **Włącz automatyczne skalowanie**. Wybierz odpowiedni typ automatycznego skalowania i wprowadź opcje skalowania opartego na załadowaniu lub na podstawie harmonogramu. Na koniec wybierz pozycję **Zapisz**.

![Włącz automatyczne skalowanie uruchomionego klastra opartego na harmonogramie węzłów procesu roboczego](./media/hdinsight-autoscale-clusters/azure-portal-settings-autoscale.png)

#### <a name="using-the-rest-api"></a>Korzystanie z interfejsu API REST

Aby włączyć lub wyłączyć funkcję automatycznego skalowania w uruchomionym klastrze przy użyciu interfejsu API REST, wprowadź żądanie POST do punktu końcowego automatycznego skalowania:

```
https://management.azure.com/subscriptions/{subscription Id}/resourceGroups/{resourceGroup Name}/providers/Microsoft.HDInsight/clusters/{CLUSTERNAME}/roles/workernode/autoscale?api-version=2018-06-01-preview
```

Użyj odpowiednich parametrów w ładunku żądania. Poniżej można włączyć automatyczne skalowanie przy użyciu poniższego ładunku JSON. Użyj ładunku, `{autoscale: null}` Aby wyłączyć automatyczne skalowanie.

```json
{ "autoscale": { "capacity": { "minInstanceCount": 3, "maxInstanceCount": 5 } } }
```

Zobacz poprzednią sekcję na temat [włączania automatycznego skalowania na podstawie obciążenia](#load-based-autoscaling) , aby uzyskać pełny opis wszystkich parametrów ładunku.

## <a name="monitoring-autoscale-activities"></a>Monitorowanie działań automatycznego skalowania

### <a name="cluster-status"></a>Stan klastra

Stan klastra wymieniony w Azure Portal może pomóc w monitorowaniu działań skalowania automatycznego.

![Włącz stan klastra skalowania automatycznego na podstawie obciążenia węzła procesu roboczego](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-cluster-status.png)

Na poniższej liście objaśniono wszystkie komunikaty o stanie klastra, które mogą zostać wyświetlone.

| Stan klastra | Opis |
|---|---|
| Uruchomienie | Klaster działa normalnie. Wszystkie poprzednie działania automatycznego skalowania zostały wykonane pomyślnie. |
| Aktualizowanie  | Trwa aktualizowanie konfiguracji automatycznego skalowania klastra.  |
| Konfiguracja usługi HDInsight  | Operacja skalowania w górę lub w dół w dół jest w toku.  |
| Błąd aktualizacji  | Usługa HDInsight napotkała problemy podczas aktualizacji konfiguracji skalowania automatycznego. Klienci mogą zrezygnować z aktualizacji lub wyłączyć automatyczne skalowanie.  |
| Błąd  | Wystąpił problem z klastrem i nie można go użyć. Usuń ten klaster i Utwórz nowy.  |

Aby wyświetlić bieżącą liczbę węzłów w klastrze, przejdź do wykresu **rozmiar klastra** na stronie **Przegląd** klastra. Lub wybierz **rozmiar klastra** w obszarze **Ustawienia**.

### <a name="operation-history"></a>Historia operacji

Możesz wyświetlić historię skalowania i skalowania w poziomie klastra w ramach metryk klastra. Możesz również wyświetlić listę wszystkich akcji skalowania w ciągu ostatniego dnia, tygodnia lub innego czasu.

Wybierz pozycję **metryki** w obszarze **monitorowanie**. Następnie w polu listy rozwijanej **Metryka** wybierz pozycję **Dodaj metrykę** i **liczbę aktywnych procesów roboczych** . Wybierz przycisk w prawym górnym rogu, aby zmienić zakres czasu.

![Włącz metrykę skalowania automatycznego opartego na harmonogramie węzłów procesu roboczego](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-chart-metric.png)

## <a name="best-practices"></a>Najlepsze rozwiązania

### <a name="consider-the-latency-of-scale-up-and-scale-down-operations"></a>Rozważ opóźnienie operacji skalowania w górę i w dół

Ukończenie operacji skalowania może potrwać od 10 do 20 minut. Podczas konfigurowania niestandardowego harmonogramu należy zaplanować to opóźnienie. Na przykład jeśli potrzebujesz rozmiaru klastra o wartości 20 o godzinie 9:00, ustaw dla wyzwalacza harmonogramu wcześniejszy czas, taki jak 8:30 AM, tak aby operacja skalowania została zakończona przez 9:00 AM.

### <a name="prepare-for-scaling-down"></a>Przygotowanie do skalowania w dół

Podczas skalowania klastra w dół automatyczne skalowanie powoduje zlikwidowanie węzłów w celu spełnienia rozmiaru docelowego. Jeśli zadania są uruchomione w tych węzłach, automatyczne skalowanie czeka na ukończenie zadań dla klastrów Spark i Hadoop. Ponieważ każdy węzeł roboczy również pełni rolę w systemie plików HDFS, dane tymczasowe są przesunięte do pozostałych węzłów. Upewnij się, że jest wystarczająca ilość miejsca na pozostałych węzłach do hostowania wszystkich danych tymczasowych.

Uruchomione zadania będą kontynuowane. Oczekujące zadania będą oczekiwać na planowanie z mniejszą liczbą dostępnych węzłów procesu roboczego.

### <a name="be-aware-of-the-minimum-cluster-size"></a>Należy pamiętać o minimalnym rozmiarze klastra

Nie Skaluj klastra do mniejszej liczby niż trzy węzły. Skalowanie klastra do mniej niż trzech węzłów może spowodować zatrzymanie trybu awaryjnego z powodu niewystarczającej replikacji plików. Aby uzyskać więcej informacji, zobacz [Uruchamianie w trybie awaryjnym](hdinsight-scaling-best-practices.md#getting-stuck-in-safe-mode).

### <a name="increase-the-number-of-mappers-and-reducers"></a>Zwiększ liczbę odwzorowań i zmniejszeń

Automatyczne skalowanie klastrów usługi Hadoop również monitoruje użycie systemu plików HDFS. Jeśli system plików HDFS jest zajęty, zakłada, że klaster nadal potrzebuje bieżących zasobów. W przypadku dużej ilości danych związanych z kwerendą można zwiększyć liczbę odwzorowań i zmniejszeń, aby zwiększyć równoległość i przyspieszyć operacje systemu plików HDFS. W ten sposób zostanie wyzwolone odpowiednie skalowanie w dół, gdy będą dostępne dodatkowe zasoby. 

### <a name="set-the-hive-configuration-maximum-total-concurrent-queries-for-the-peak-usage-scenario"></a>Ustaw maksymalną łączną liczbę równoczesnych zapytań dla scenariusza użycia szczytowego

Zdarzenia automatycznego skalowania nie zmieniają maksymalnej liczby *równoczesnych zapytań* w usłudze Hive w Ambari. Oznacza to, że usługa programu Hive Server 2 Interactive może obsłużyć tylko daną liczbę współbieżnych zapytań w dowolnym momencie, nawet jeśli liczba demonów interakcyjnych zapytań jest skalowana w górę i w dół na podstawie obciążenia i harmonogramu. Ogólnym zaleceniem jest ustawienie tej konfiguracji dla scenariusza użycia szczytowego, aby uniknąć ręcznej interwencji.

Niemniej jednak może wystąpić błąd ponownego uruchomienia serwera Hive, jeśli istnieje tylko niewielka liczba węzłów procesu roboczego, a wartość maksymalna łączna liczba współbieżnych zapytań jest zbyt wysoka. Minimalnym wymaganiem jest minimalna liczba węzłów procesu roboczego, które mogą uwzględniać daną liczbę tez AMS (równą maksymalnej całkowitej łącznej konfiguracji współbieżnych zapytań). 

## <a name="limitations"></a>Ograniczenia

### <a name="node-label-file-missing"></a>Brak pliku etykiety węzła

Automatyczne skalowanie usługi HDInsight używa pliku etykiet węzła, aby określić, czy węzeł jest gotowy do wykonywania zadań. Plik etykiet węzła jest przechowywany w systemie plików HDFS z trzema replikami. Jeśli rozmiar klastra jest znacznie skalowany i istnieje duża ilość danych tymczasowych, istnieje mała szansa, że wszystkie trzy repliki mogą zostać porzucone. W takim przypadku klaster przechodzi do stanu błędu.

### <a name="interactive-query-daemons-count"></a>Liczba interaktywnych demonów zapytań

W przypadku klastrów zapytań interaktywnych z włączoną funkcją automatycznego skalowania zdarzenie skalowania w górę/w dół umożliwia również skalowanie w górę/w dół liczby interaktywnych demonów zapytań do liczby aktywnych węzłów procesu roboczego. Zmiana liczby demonów nie jest utrwalana w `num_llap_nodes` konfiguracji w Ambari. Jeśli usługi Hive są ponownie uruchamiane ręcznie, liczba interaktywnych demonów zapytań jest resetowana zgodnie z konfiguracją w Ambari.

Jeśli usługa interakcyjnego zapytania zostanie ręcznie uruchomiona ponownie, należy ręcznie zmienić `num_llap_node` konfigurację (liczbę węzłów potrzebną do uruchomienia demona interakcyjnego zapytania programu Hive) w obszarze *Advanced Hive-Interactive-ENV* , aby dopasować bieżącą liczbę węzłów procesu roboczego.

## <a name="next-steps"></a>Następne kroki

Przeczytaj o wytycznych dotyczących ręcznego skalowania klastrów w [wytycznych dotyczących skalowania](hdinsight-scaling-best-practices.md)
