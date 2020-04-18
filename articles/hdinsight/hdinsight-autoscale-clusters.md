---
title: Automatyczne skalowanie klastrów usługi Azure HDInsight
description: Funkcja automatycznego skalowania usługi Azure HDInsight umożliwia automatyczne skalowanie klastrów Programu Apache Hadoop
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/07/2020
ms.openlocfilehash: 4f9b43b6f800bb47942ccc00fee0fac4536d2ec0
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2020
ms.locfileid: "81640581"
---
# <a name="automatically-scale-azure-hdinsight-clusters"></a>Automatyczne skalowanie klastrów usługi Azure HDInsight

> [!Important]
> Funkcja skalowania automatycznego usługi Azure HDInsight została wydana w celu zapewnienia ogólnej dostępności 7 listopada 2019 r. dla klastrów Platformy Spark i Hadoop i zawierała ulepszenia niedostępne w wersji zapoznawczej tej funkcji. Jeśli klaster platformy Spark został utworzony przed 7 listopada 2019 r. i chcesz użyć funkcji Skalowanie automatyczne w klastrze, zalecaną ścieżką jest utworzenie nowego klastra i włączenie skalowania automatycznego w nowym klastrze.
>
> Automatyczne skalowanie dla klaspy interaktywnej (LLAP) i HBase jest nadal w wersji zapoznawczej. Skalowanie automatyczne jest dostępne tylko w klastrach Spark, Hadoop, Interactive Query i HBase.

Funkcja automatycznego skalowania klastra usługi Azure HDInsight automatycznie skaluje liczbę węzłów procesu roboczego w klastrze w górę i w dół. Inne typy węzłów w klastrze nie mogą być skalowane obecnie.  Podczas tworzenia nowego klastra HDInsight można ustawić minimalną i maksymalną liczbę węzłów procesu roboczego. Skalowanie automatyczne następnie monitoruje wymagania zasobów obciążenia analizy i skaluje liczbę węzłów procesu roboczego w górę lub w dół. Ta funkcja nie pobiera żadnych dodatkowych opłat.

## <a name="cluster-compatibility"></a>Zgodność klastra

W poniższej tabeli opisano typy klastrów i wersje zgodne z funkcją Skalowanie automatyczne.

| Wersja | platforma Spark | Hive | LLAP (WŁASJ) | HBase | Kafka | Storm | ML |
|---|---|---|---|---|---|---|---|
| HDInsight 3.6 bez ESP | Tak | Tak | Tak | Tak* | Nie | Nie | Nie |
| HDInsight 4.0 bez ESP | Tak | Tak | Tak | Tak* | Nie | Nie | Nie |
| HDInsight 3.6 z ESP | Tak | Tak | Tak | Tak* | Nie | Nie | Nie |
| HDInsight 4.0 z ESP | Tak | Tak | Tak | Tak* | Nie | Nie | Nie |

\*Klastry HBase można skonfigurować tylko do skalowania opartego na harmonogramie, a nie do skalowania opartego na obciążeniu.

## <a name="how-it-works"></a>Jak to działa

Można wybrać skalowanie oparte na obciążeniu lub skalowanie oparte na harmonogramie dla klastra HDInsight. Skalowanie oparte na obciążeniu zmienia liczbę węzłów w klastrze w ramach ustawionego zakresu, aby zapewnić optymalne użycie procesora CPU i zminimalizować koszty eksploatacji.

Skalowanie oparte na harmonogramie zmienia liczbę węzłów w klastrze na podstawie warunków, które zaczynają obowiązywać w określonych godzinach. Warunki te skalują klaster do zamierzonej liczby węzłów.

### <a name="metrics-monitoring"></a>Monitorowanie metryk

Skalowanie automatyczne stale monitoruje klaster i zbiera następujące metryki:

|Metryka|Opis|
|---|---|
|Całkowita wartość oczekującego procesora CPU|Całkowita liczba rdzeni wymaganych do rozpoczęcia wykonywania wszystkich oczekujących kontenerów.|
|Całkowita liczba oczekujących pamięci|Całkowita pamięć (w MB) wymagana do rozpoczęcia wykonywania wszystkich oczekujących kontenerów.|
|Całkowita ilość wolnego procesora|Suma wszystkich nieużywanych rdzeni w aktywnych węzłach procesu roboczego.|
|Całkowita ilość wolnej pamięci|Suma nieużywanej pamięci (w MB) w aktywnych węzłach procesu roboczego.|
|Używana pamięć na węzeł|Obciążenie węzła procesu roboczego. Węzeł procesu roboczego, w którym jest używane 10 GB pamięci, jest uważany za większy niż proces roboczy z 2 GB używanej pamięci.|
|Liczba wzorców aplikacji na węzeł|Liczba kontenerów wzorca aplikacji (AM) uruchomionych w węźle procesu roboczego. Węzeł procesu roboczego, który hostuje dwa kontenery AM, jest uważany za ważniejszy niż węzeł procesu roboczego, który hostuje kontenery zero AM.|

Powyższe dane są sprawdzane co 60 sekund. Skalowanie automatyczne podejmuje decyzje na podstawie tych metryk.

### <a name="load-based-scale-conditions"></a>Warunki skalowania oparte na obciążeniu

Po wykryciu następujących warunków skalowanie automatyczne spowoduje wystosować żądanie skali:

|Skalowanie w górę|Skalowanie w dół|
|---|---|
|Całkowita oczekująca wartość procesora CPU jest większa niż całkowita ilość wolnego procesora PRZEZ ponad 3 minuty.|Całkowita liczba oczekujących procesorów CPU jest mniejsza niż całkowita ilość wolnego procesora CPU przez ponad 10 minut.|
|Całkowita pamięć oczekująca jest większa niż całkowita ilość wolnej pamięci przez ponad 3 minuty.|Całkowita ilość pamięci oczekującej jest mniejsza niż całkowita ilość wolnej pamięci przez ponad 10 minut.|

W przypadku skalowania w górę skalowanie automatyczne wystawia żądanie skalowania w górę, aby dodać wymaganą liczbę węzłów. Skalowanie w górę opiera się na ile nowych węzłów procesu roboczego są potrzebne do spełnienia bieżących wymagań procesora CPU i pamięci.

W przypadku skalowania w dół skalowanie automatyczne wystawia żądanie usunięcia określonej liczby węzłów. Skalowanie w dół jest oparte na liczbie kontenerów AM na węzeł. I aktualne wymagania dotyczące procesora i pamięci. Usługa wykrywa również, które węzły są kandydatami do usunięcia na podstawie bieżącego wykonywania zadania. Operacja skalowania w dół najpierw likwiduje węzły, a następnie usuwa je z klastra.

## <a name="get-started"></a>Rozpoczęcie pracy

### <a name="create-a-cluster-with-load-based-autoscaling"></a>Tworzenie klastra z automatycznym skalowaniem opartym na obciążeniach

Aby włączyć funkcję skalowania automatycznego ze skalowaniem opartym na obciążeniu, wykonaj następujące kroki w ramach normalnego procesu tworzenia klastra:

1. Na karcie **Konfiguracja + cennik** zaznacz pole wyboru **Włącz skalowanie automatyczne.**
1. Wybierz **opcję Oparte na wyładunku** w obszarze Typ **skalowania automatycznego**.
1. Wprowadź wartości przeznaczone dla następujących właściwości:  

    * Początkowa **liczba węzłów** dla **węzła Worker**.
    * **Minimalna** liczba węzłów procesu roboczego.
    * **Maksymalna** liczba węzłów procesu roboczego.

    ![Włączanie skalowania automatycznego opartego na zaciążenia węzła procesu roboczego](./media/hdinsight-autoscale-clusters/azure-portal-cluster-configuration-pricing-autoscale.png)

Początkowa liczba węzłów procesu roboczego musi leżyć między minimalną i maksymalną włącznie. Ta wartość definiuje początkowy rozmiar klastra podczas jego tworzenia. Minimalna liczba węzłów procesu roboczego powinna być ustawiona na trzy lub więcej. Skalowanie klastra do mniej niż trzech węzłów może spowodować utknięcie w trybie awaryjnym z powodu niewystarczającej replikacji plików.  Aby uzyskać więcej informacji, zobacz [Utknięcie w trybie awaryjnym](./hdinsight-scaling-best-practices.md#getting-stuck-in-safe-mode).

### <a name="create-a-cluster-with-schedule-based-autoscaling"></a>Tworzenie klastra z automatycznym skalowaniem opartym na harmonogramie

Aby włączyć funkcję skalowania automatycznego ze skalowaniem opartym na harmonogramie, wykonaj następujące kroki w ramach normalnego procesu tworzenia klastra:

1. Na karcie **Konfiguracja + cennik** zaznacz pole wyboru **Włącz skalowanie automatyczne.**
1. Wprowadź **liczbę węzłów** dla **węzła Worker**, który steruje limitem skalowania klastra.
1. Wybierz opcję **Oparte na harmonogramie** w obszarze **Typ skalowania automatycznego**.
1. Wybierz **pozycję Konfiguruj,** aby otworzyć okno **konfiguracji skalowania automatycznego.**
1. Wybierz strefę czasową, a następnie kliknij **przycisk + Dodaj warunek**
1. Wybierz dni tygodnia, do których powinien zastosować nowy warunek.
1. Edytuj czas, przez jaki warunek powinien zostać uwzględniona, oraz liczbę węzłów, do których klaster powinien być skalowany.
1. W razie potrzeby dodaj więcej warunków.

    ![Włączanie tworzenia opartego na harmonogramie węzłów procesu roboczego](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-schedule-creation.png)

Liczba węzłów musi zawierać się z maksymalną liczbą węzłów procesu roboczego wprowadzonych przed dodaniem warunków.

### <a name="final-creation-steps"></a>Końcowe etapy tworzenia

Wybierz typ maszyny Wirtualnej dla węzłów procesu roboczego, wybierając maszynę wirtualną z listy rozwijanej w obszarze **Rozmiar węzła**. Po wybraniu typu maszyny Wirtualnej dla każdego typu węzła można wyświetlić szacowany zakres kosztów dla całego klastra. Dostosuj typy maszyn wirtualnych do budżetu.

![Włączanie rozmiaru węzła procesu roboczego opartego na harmonogramie](./media/hdinsight-autoscale-clusters/azure-portal-cluster-configuration-pricing-vmsize.png)

Subskrypcja ma przydział pojemności dla każdego regionu. Całkowita liczba rdzeni węzłów głównych i maksymalnych węzłów procesu roboczego nie może przekroczyć przydziału zdolności produkcyjnych. Jednak ten kontyngent jest miękki limit; zawsze możesz utworzyć bilet pomocy technicznej, aby go łatwo zwiększyć.

> [!Note]  
> Jeśli przekroczysz całkowity podstawowy limit przydziału, zostanie wyświetlony komunikat o błędzie "maksymalny węzeł przekroczył dostępne rdzenie w tym regionie, wybierz inny region lub skontaktuj się z pomocą techniczną, aby zwiększyć przydział".

Aby uzyskać więcej informacji na temat tworzenia klastra usługi HDInsight przy użyciu portalu Azure, zobacz [Tworzenie klastrów opartych na systemie Linux w usłudze HDInsight przy użyciu portalu Azure.](hdinsight-hadoop-create-linux-clusters-portal.md)  

### <a name="create-a-cluster-with-a-resource-manager-template"></a>Tworzenie klastra z szablonem Menedżera zasobów

#### <a name="load-based-autoscaling"></a>Skalowanie automatyczne oparte na obciążeniu

Można utworzyć klaster HDInsight z automatycznym skalowaniem opartym na `autoscale` obciążeniu `computeProfile`  >  `workernode` szablonu usługi `minInstanceCount` `maxInstanceCount` Azure Resource Manager, dodając węzeł do sekcji z właściwościami i jak pokazano we wzorze json poniżej.

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

Klaster HDInsight można utworzyć z szablonem usługi Azure Resource Manager `autoscale` oparty `computeProfile`  >  `workernode` na harmonogramie, dodając węzeł do sekcji. Węzeł `autoscale` zawiera, `recurrence` który `timezone` ma `schedule` i który opisuje, kiedy zmiana nastąpi.

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
        },
      ]
    }
  },
  "name": "workernode",
  "targetInstanceCount": 4,
}
```

### <a name="enable-and-disable-autoscale-for-a-running-cluster"></a>Włączanie i wyłączanie skalowania automatycznego dla uruchomionego klastra

#### <a name="using-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Aby włączyć skalowanie automatyczne w uruchomionym klastrze, wybierz opcję **Rozmiar klastra** w obszarze **Ustawienia**. Następnie wybierz pozycję **Włącz skalowanie automatyczne**. Wybierz odpowiedni typ skalowania automatycznego i wprowadź opcje skalowania opartego na obciążeniach lub harmonogramie. Na koniec wybierz pozycję **Zapisz**.

![Włączanie klastra uruchamiania opartego na harmonogramie węzłów procesu roboczego](./media/hdinsight-autoscale-clusters/azure-portal-settings-autoscale.png)

#### <a name="using-the-rest-api"></a>Korzystanie z interfejsu API REST

Aby włączyć lub wyłączyć skalowanie automatyczne w uruchomionym klastrze przy użyciu interfejsu API REST, wyślij żądanie POST do punktu końcowego skalowania automatycznego:

```
https://management.azure.com/subscriptions/{subscription Id}/resourceGroups/{resourceGroup Name}/providers/Microsoft.HDInsight/clusters/{CLUSTERNAME}/roles/workernode/autoscale?api-version=2018-06-01-preview
```

Użyj odpowiednich parametrów w ładunku żądania. Ładunek json poniżej może służyć do włączenia skalowania automatycznego. Użyj ładunku, `{autoscale: null}` aby wyłączyć skalowanie automatyczne.

```json
{ autoscale: { capacity: { minInstanceCount: 3, maxInstanceCount: 2 } } }
```

Zobacz poprzednią sekcję dotyczącą [włączania automatycznej skali opartej](#load-based-autoscaling) na obciążeniu, aby uzyskać pełny opis wszystkich parametrów ładunku.

## <a name="guidelines"></a>Wytyczne

### <a name="choosing-load-based-or-schedule-based-scaling"></a>Wybieranie skalowania opartego na obciążeniach lub harmonogramie

Przed podjęciem decyzji, który tryb wybrać, należy wziąć pod uwagę następujące czynniki:

* Włącz skalowanie automatyczne podczas tworzenia klastra.
* Minimalna liczba węzłów powinna wynosić co najmniej trzy.
* Wariancja obciążenia: czy obciążenie klastra jest zgodne wzorzec w określonych godzinach, w określonych dniach. Jeśli nie, lepszym rozwiązaniem jest planowanie oparte na obciążeniu.
* Wymagania umowy SLA: Skalowanie w skali automatycznej jest reaktywne zamiast predykcyjne. Czy będzie wystarczające opóźnienie między kiedy obciążenie zaczyna rosnąć i kiedy klaster musi być na jego rozmiar docelowy? Jeśli istnieją ścisłe wymagania umowy SLA, a obciążenie jest stałym znanym wzorcem, lepszym rozwiązaniem jest "oparty na harmonogramie".

### <a name="consider-the-latency-of-scale-up-or-scale-down-operations"></a>Należy wziąć pod uwagę opóźnienie operacji skalowania w górę lub w dół

Operacja skalowania może potrwać od 10 do 20 minut. Podczas konfigurowania dostosowanego harmonogramu zaplanuj to opóźnienie. Na przykład jeśli potrzebujesz rozmiar klastra do 20 o 9:00 AM, ustaw wyzwalacz harmonogramu na wcześniejszy czas, takich jak 8:30 AM, tak aby operacja skalowania została zakończona do 9:00 AM.

### <a name="preparation-for-scaling-down"></a>Przygotowanie do skalowania w dół

Podczas procesu skalowania klastra skalowanie automatyczne spowoduje likwidację węzłów w celu osiągnięcia rozmiaru docelowego. Jeśli zadania są uruchomione w tych węzłach, skalowanie automatyczne będzie czekać na zakończenie zadań. Ponieważ każdy węzeł procesu roboczego pełni również rolę w systemie PLIKÓW HDFS, dane tymczasowe zostaną przesunięte do pozostałych węzłów. Dlatego należy upewnić się, że jest wystarczająco dużo miejsca na pozostałe węzły do obsługi wszystkich danych tymczasowych.

Uruchomione zadania będą kontynuowane. Oczekujące zadania będą czekać na planowanie z mniejszą liczbą dostępnych węzłów procesu roboczego.

### <a name="minimum-cluster-size"></a>Minimalny rozmiar klastra

Nie skaluj klastra w dół do mniej niż trzech węzłów. Skalowanie klastra do mniej niż trzech węzłów może spowodować utknięcie w trybie awaryjnym z powodu niewystarczającej replikacji plików.  Aby uzyskać więcej informacji, zobacz [Utknięcie w trybie awaryjnym](./hdinsight-scaling-best-practices.md#getting-stuck-in-safe-mode).

## <a name="monitoring"></a>Monitorowanie

### <a name="cluster-status"></a>Stan klastra

Stan klastra wymieniony w witrynie Azure portal może pomóc w monitorowaniu działań w skali automatycznej.

![Włączanie stanu klastra opartego na załadunku węzła procesu roboczego](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-cluster-status.png)

Wszystkie komunikaty o stanie klastra, które mogą być widoczne, są wyjaśnione na poniższej liście.

| Stan klastra | Opis |
|---|---|
| Działanie | Klaster działa normalnie. Wszystkie poprzednie działania skalowania automatycznego zostały zakończone pomyślnie. |
| Aktualizowanie  | Konfiguracja skalowania automatycznego klastra jest aktualizowana.  |
| Konfiguracja usługi HDInsight  | Trwa operacja skalowania klastra w górę lub w dół.  |
| Błąd aktualizacji  | Usługa HDInsight wystąpiła z problemami podczas aktualizacji konfiguracji skalowania automatycznego. Klienci mogą ponowić próbę aktualizacji lub wyłączyć skalowanie automatyczne.  |
| Błąd  | Coś jest nie tak z klastrem i nie nadaje się do użyteczności. Usuń ten klaster i utwórz nowy.  |

Aby wyświetlić bieżącą liczbę węzłów w klastrze, przejdź do **tabeli rozmiarów klastra** na stronie **Przegląd** klastra. Lub wybierz **rozmiar klastra** w obszarze **Ustawienia**.

### <a name="operation-history"></a>Historia operacji

Historię skalowania w górę i skalowania w dół klastra można wyświetlić jako część metryk klastra. Można również wyświetlić listę wszystkich akcji skalowania w ciągu ostatniego dnia, tygodnia lub innego okresu czasu.

Wybierz **metryki** w obszarze **Monitorowanie**. Następnie wybierz pozycję **Dodaj metrykę** i **liczbę aktywnych pracowników** z listy rozwijanej **Metryka.** Wybierz przycisk w prawym górnym rogu, aby zmienić zakres czasu.

![Włączanie metryki skalowania automatycznego opartego na harmonogramie węzła procesu roboczego](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-chart-metric.png)

## <a name="next-steps"></a>Następne kroki

Przeczytaj wskazówki dotyczące ręcznego skalowania klastrów w [wskazówki dotyczące skalowania](hdinsight-scaling-best-practices.md)
