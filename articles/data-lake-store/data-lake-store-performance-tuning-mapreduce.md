---
title: Azure Data Lake Storage Gen1 dostrajania wydajności — MapReduce
description: Dowiedz się więcej na temat dostrajania wydajności dla MapReduce w Azure Data Lake Storage Gen1, w tym parametrów, wskazówek, przykładowych obliczeń i ograniczeń.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 12/19/2016
ms.author: twooley
ms.openlocfilehash: 2549413241e422fb1e0e5e1f079c287e0b7cf005
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97723732"
---
# <a name="performance-tuning-guidance-for-mapreduce-on-hdinsight-and-azure-data-lake-storage-gen1"></a>Wskazówki dotyczące dostrajania wydajności dla MapReduce w usłudze HDInsight i Azure Data Lake Storage Gen1

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Konto Azure Data Lake Storage Gen1**. Aby uzyskać instrukcje dotyczące sposobu tworzenia takiego elementu, zobacz Wprowadzenie [do Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* **Klaster usługi Azure HDInsight** z dostępem do konta Data Lake Storage Gen1. Zobacz [Tworzenie klastra usługi HDInsight przy użyciu Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md). Upewnij się, że Pulpit zdalny dla klastra są włączone.
* **Korzystanie z usługi MapReduce w usłudze HDInsight**. Aby uzyskać więcej informacji, zobacz [Korzystanie z MapReduce w usłudze Hadoop w usłudze HDInsight](../hdinsight/hadoop/hdinsight-use-mapreduce.md)
* **Przejrzyj wskazówki dotyczące dostrajania wydajności Data Lake Storage Gen1**. Ogólne pojęcia dotyczące wydajności znajdują się w temacie [Data Lake Storage Gen1 wskazówki dotyczące dostrajania wydajności](./data-lake-store-performance-tuning-guidance.md)

## <a name="parameters"></a>Parametry

W przypadku uruchamiania zadań MapReduce poniżej przedstawiono najważniejsze parametry, które można skonfigurować w celu zwiększenia wydajności Data Lake Storage Gen1:

|Parametr      | Opis  |
|---------|---------|
|`Mapreduce.map.memory.mb`  |  Ilość pamięci, która ma zostać przydzielona do każdego mapowania.  |
|`Mapreduce.job.maps`     |  Liczba zadań mapy według zadania.  |
|`Mapreduce.reduce.memory.mb`     |  Ilość pamięci do przydzielenia każdemu zmniejszeniu.  |
|`Mapreduce.job.reduces`    |   Liczba zadań zmniejszania na zadanie.  |

### <a name="mapreducemapmemory--mapreducereducememory"></a>MapReduce. map. Memory/MapReduce. Zmniejsz ilość pamięci

Dostosuj tę liczbę na podstawie ilości pamięci wymaganej do wykonania zadania map i/lub Zmniejsz. Wartości domyślne parametrów `mapreduce.map.memory` i w Ambari można wyświetlić `mapreduce.reduce.memory` za pomocą konfiguracji przędzy. W Ambari przejdź do PRZĘDZy i **Wyświetl kartę konfiguracje** . Zostanie wyświetlona pamięć PRZĘDZy.

### <a name="mapreducejobmaps--mapreducejobreduces"></a>MapReduce. job. Maps/MapReduce. job. Zmniejsz

Określa maksymalną liczbę odwzorów lub skróceniów do utworzenia. Liczba podziałów określa liczbę odwzorowań tworzonych dla zadania MapReduce. W związku z tym, możesz uzyskać mniejszą liczbę odwzorów niż zażądano, jeśli są mniejsze niż liczba żądanych odwzorowań.

## <a name="guidance"></a>Wskazówki

### <a name="step-1-determine-number-of-jobs-running"></a>Krok 1. określenie liczby uruchomionych zadań

Domyślnie MapReduce będzie używać całego klastra dla danego zadania. Możesz użyć mniejszego poziomu klastra, używając mniejszej liczby odwzorowań niż dostępne kontenery. W wytycznych w tym dokumencie przyjęto założenie, że aplikacja jest jedyną aplikacją uruchomioną w klastrze.

### <a name="step-2-set-mapreducemapmemorymapreducereducememory"></a>Krok 2: Ustawianie MapReduce. map. Memory/MapReduce. Zmniejsz ilość pamięci

Rozmiar pamięci dla zadań mapowania i zmniejszania zależy od konkretnego zadania. Aby zwiększyć współbieżność, można zmniejszyć rozmiar pamięci. Liczba zadań wykonywanych współbieżnie zależy od liczby kontenerów. Zmniejszając ilość pamięci na Maper lub zmniejszenie, można utworzyć więcej kontenerów, co umożliwia współbieżne uruchamianie większej liczby lub mniejszej liczby elementów. Zmniejszenie ilości pamięci może spowodować, że niektóre procesy nie zapewnią ilości pamięci. Jeśli wystąpi błąd sterty podczas uruchamiania zadania, Zwiększ ilość pamięci na Maper lub zmniejszenie. Należy wziąć pod uwagę, że dodanie kolejnych kontenerów powoduje dodanie dodatkowych obciążeń dla każdego dodatkowego kontenera, co może spowodować spadek wydajności. Kolejną alternatywą jest uzyskanie większej ilości pamięci przy użyciu klastra o większej ilości pamięci lub zwiększenie liczby węzłów w klastrze. Większa ilość pamięci umożliwi użycie większej liczby kontenerów, co oznacza większą współbieżność.

### <a name="step-3-determine-total-yarn-memory"></a>Krok 3. Określanie całkowitej ilości pamięci PRZĘDZy

Aby dostroić MapReduce. job. Maps/MapReduce. job. maleje, weź pod uwagę łączną ilość pamięci PRZĘDZy, która jest dostępna do użycia. Te informacje są dostępne w Ambari. Przejdź do PRZĘDZy i Wyświetl **kartę konfiguracje** . W tym oknie zostanie wyświetlona pamięć PRZĘDZy. Pomnóż pamięć PRZĘDZy z liczbą węzłów w klastrze, aby uzyskać łączną ilość pamięci PRZĘDZy.

`Total YARN memory = nodes * YARN memory per node`

Jeśli używany jest pusty klaster, pamięć może być całkowitą ilością pamięci PRZĘDZy dla klastra. Jeśli inne aplikacje używają pamięci, można użyć tylko części pamięci klastra, zmniejszając liczbę odwzorów lub mniejszą liczbę kontenerów, które mają być używane.

### <a name="step-4-calculate-number-of-yarn-containers"></a>Krok 4. Obliczanie liczby kontenerów PRZĘDZy

Kontenery PRZĘDZy określają ilość współbieżności dostępnej dla zadania. Weź łączną ilość pamięci PRZĘDZy i Podziel ją przez MapReduce. map. Memory.

`# of YARN containers = total YARN memory / mapreduce.map.memory`

### <a name="step-5-set-mapreducejobmapsmapreducejobreduces"></a>Krok 5. Ustawianie MapReduce. job. Maps/MapReduce. job. redukuje

Ustaw wartość MapReduce. job. Maps/MapReduce. job. zmniejsza się do co najmniej liczby dostępnych kontenerów. Aby uzyskać lepszą wydajność, można eksperymentować przez zwiększenie liczby odwzorowań i elementów ograniczających. Należy pamiętać, że więcej odwzorowań będzie miało dodatkowe obciążenie, tak że zbyt wiele mapowania może obniżyć wydajność.

Planowanie procesora CPU i izolacja procesora CPU są domyślnie wyłączone, więc liczba kontenerów PRZĘDZy jest ograniczona przez pamięć.

## <a name="example-calculation"></a>Przykładowe obliczenie

Załóżmy, że obecnie masz klaster składający się z 8 D14 węzłów i chcesz uruchomić zadanie intensywnie korzystające z operacji we/wy. Poniżej przedstawiono obliczenia, które należy wykonać:

### <a name="step-1-determine-number-of-jobs-running"></a>Krok 1. określenie liczby uruchomionych zadań

W naszym przykładzie przyjęto założenie, że nasze zadanie jest jedynym uruchomionym.

### <a name="step-2-set-mapreducemapmemorymapreducereducememory"></a>Krok 2: Ustawianie MapReduce. map. Memory/MapReduce. Zmniejsz ilość pamięci

W naszym przykładzie uruchomiono zadanie intensywnie korzystające z operacji we/wy i podjęto decyzję o tym, że dla zadań mapy wystarczą 3 GB pamięci.

`mapreduce.map.memory = 3GB`

### <a name="step-3-determine-total-yarn-memory"></a>Krok 3. Określanie całkowitej ilości pamięci PRZĘDZy

`total memory from the cluster is 8 nodes * 96GB of YARN memory for a D14 = 768GB`

### <a name="step-4-calculate--of-yarn-containers"></a>Krok 4. Obliczanie liczby kontenerów PRZĘDZy

`# of YARN containers = 768 GB of available memory / 3 GB of memory = 256`

### <a name="step-5-set-mapreducejobmapsmapreducejobreduces"></a>Krok 5. Ustawianie MapReduce. job. Maps/MapReduce. job. redukuje

`mapreduce.map.jobs = 256`

## <a name="limitations"></a>Ograniczenia

**Ograniczanie Data Lake Storage Gen1**

Jako usługa wielodostępna Data Lake Storage Gen1 ustawia limity przepustowości na poziomie konta. Jeśli osiągnięto te limity, rozpocznie się wyświetlanie niepowodzeń zadań. Można to zidentyfikować, obserwując błędy ograniczania w dziennikach zadań. Jeśli potrzebujesz większej przepustowości dla zadania, skontaktuj się z nami.

Aby sprawdzić, czy masz ograniczone ograniczenia, należy włączyć rejestrowanie debugowania po stronie klienta. Oto jak to zrobić:

1. Umieść następującą właściwość we właściwościach Log4J w Ambari > PRZĘDZy > config > Advanced przędzy-Log4J: Log4J. rejestrator. com. Microsoft. Azure. datalake. Store = DEBUG

2. Uruchom ponownie wszystkie węzły/usługi, aby konfiguracja zaczęła obowiązywać.

3. W przypadku uzyskania ograniczenia przepustowości w pliku dziennika PRZĘDZenia zostanie wyświetlony kod błędu HTTP 429. Plik dziennika PRZĘDZy znajduje się w/tmp/ &lt; użytkownika &gt; /Yarn.log

## <a name="examples-to-run"></a>Przykłady do uruchomienia

Aby zademonstrować, jak MapReduce działa na Data Lake Storage Gen1, poniżej przedstawiono przykładowy kod, który został uruchomiony w klastrze z następującymi ustawieniami:

* 16 węzłów D14v2
* Klaster Hadoop z systemem HDI 3,6

Oto kilka przykładowych poleceń do uruchomienia MapReduce Teragen, Terasort i Teravalidate. Można dostosować te polecenia w zależności od zasobów.

### <a name="teragen"></a>Teragen

```
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 10000000000 adl://example/data/1TB-sort-input
```

### <a name="terasort"></a>Terasort

```
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 -Dmapreduce.job.reduces=512 -Dmapreduce.reduce.memory.mb=3072 adl://example/data/1TB-sort-input adl://example/data/1TB-sort-output
```

### <a name="teravalidate"></a>Teravalidate

```
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapreduce.job.maps=512 -Dmapreduce.map.memory.mb=3072 adl://example/data/1TB-sort-output adl://example/data/1TB-sort-validate
```