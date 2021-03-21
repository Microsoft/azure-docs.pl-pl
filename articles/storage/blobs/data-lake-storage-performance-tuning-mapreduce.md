---
title: 'Dostrajanie wydajności: MapReduce, HDInsight & Azure Data Lake Storage Gen2 | Microsoft Docs'
description: Poznaj wskazówki dotyczące dostrajania wydajności zadań zmniejszania mapy na Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 11/18/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: b95d37e1725940799750dbd3c29174d9855390d6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95912930"
---
# <a name="tune-performance-mapreduce-hdinsight--azure-data-lake-storage-gen2"></a>Dostrajanie wydajności: MapReduce, HDInsight & Azure Data Lake Storage Gen2

Informacje o czynnikach, które należy wziąć pod uwagę podczas dostrajania wydajności zadań zmniejszania mapy. W tym artykule omówiono zakres wytycznych dotyczących dostrajania wydajności.

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Konto Azure Data Lake Storage Gen2**. Aby uzyskać instrukcje dotyczące sposobu tworzenia takiego elementu, zobacz [Szybki Start: Tworzenie konta magazynu Azure Data Lake Storage Gen2](../common/storage-account-create.md).
* **Klaster usługi Azure HDInsight** z dostępem do konta Data Lake Storage Gen2. Zobacz [używanie Azure Data Lake Storage Gen2 z klastrami usługi Azure HDInsight](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md)
* **Korzystanie z usługi MapReduce w usłudze HDInsight**.  Aby uzyskać więcej informacji, zobacz [Korzystanie z MapReduce w usłudze Hadoop w usłudze HDInsight](../../hdinsight/hadoop/hdinsight-use-mapreduce.md)
* **Wskazówki dotyczące dostrajania wydajności na Data Lake Storage Gen2**.  Ogólne pojęcia dotyczące wydajności znajdują się w temacie [Data Lake Storage Gen2 wskazówki dotyczące dostrajania wydajności](data-lake-storage-performance-tuning-guidance.md)

## <a name="parameters"></a>Parametry

W przypadku uruchamiania zadań MapReduce poniżej przedstawiono parametry, które można skonfigurować w celu zwiększenia wydajności Data Lake Storage Gen2:

* **MapReduce. map. Memory. MB** — ilość pamięci do przydzielenia do każdego mapowania
* **MapReduce. job. Maps** — liczba zadań mapy według zadania
* **MapReduce. Zredukuj. Memory. MB** — ilość pamięci do przydzielenia każdemu obniżyć
* **MapReduce. job. maleje** — liczba zadań zmniejszania według zadania

**MapReduce. map. Memory/MapReduce. Zmniejsz ilość pamięci** Tę liczbę należy dostosować w zależności od ilości pamięci wymaganej do zaplanowania i/lub zmniejszenia zadania.  Wartości domyślne MapReduce. map. Memory i MapReduce. redukować. Memory można wyświetlić w Ambari za pomocą konfiguracji przędzy.  W Ambari przejdź do PRZĘDZy i Wyświetl kartę konfiguracje.  Zostanie wyświetlona pamięć PRZĘDZy.  

**MapReduce. job. Maps/MapReduce. job. Zmniejsz** Pozwoli to określić maksymalną liczbę odwzorów lub skróceniów, które mają zostać utworzone.  Liczba podziałów określi, ile mapowania zostanie utworzonych dla zadania MapReduce.  W związku z tym, możesz uzyskać mniejszą liczbę odwzorów niż zażądano, jeśli są mniejsze niż liczba żądanych odwzorowań.       

## <a name="guidance"></a>Wskazówki

> [!NOTE]
> W wytycznych w tym dokumencie przyjęto założenie, że aplikacja jest jedyną aplikacją uruchomioną w klastrze.

**Krok 1. określenie liczby uruchomionych zadań**

Domyślnie MapReduce będzie używać całego klastra dla danego zadania.  Możesz użyć mniejszej ilości z klastra, używając mniej odwzorów niż dostępne kontenery.        

**Krok 2: Ustawianie MapReduce. map. Memory/MapReduce. Zmniejsz ilość pamięci**

Rozmiar pamięci dla zadań mapowania i zmniejszania zależy od konkretnego zadania.  Aby zwiększyć współbieżność, można zmniejszyć rozmiar pamięci.  Liczba zadań wykonywanych współbieżnie zależy od liczby kontenerów.  Zmniejszając ilość pamięci na Maper lub zmniejszenie, można utworzyć więcej kontenerów, co umożliwia współbieżne uruchamianie większej liczby lub mniejszej liczby elementów.  Zmniejszenie ilości pamięci może spowodować, że niektóre procesy nie zapewnią ilości pamięci.  Jeśli podczas wykonywania zadania wystąpi błąd sterty, należy zwiększyć ilość pamięci na mapowanie lub zmniejszenie.  Należy wziąć pod uwagę, że dodanie kolejnych kontenerów spowoduje dodanie dodatkowych obciążeń dla każdego dodatkowego kontenera, co może spowodować spadek wydajności.  Kolejną alternatywą jest uzyskanie większej ilości pamięci przy użyciu klastra o większej ilości pamięci lub zwiększenie liczby węzłów w klastrze.  Większa ilość pamięci umożliwi użycie większej liczby kontenerów, co oznacza większą współbieżność.  

**Krok 3. Określanie całkowitej ilości pamięci PRZĘDZy**

Aby dostroić MapReduce. job. Maps/MapReduce. job. maleje, należy wziąć pod uwagę łączną ilość dostępnej pamięci PRZĘDZy do użycia.  Te informacje są dostępne w Ambari.  Przejdź do PRZĘDZy i Wyświetl kartę konfiguracje.  W tym oknie zostanie wyświetlona pamięć PRZĘDZy.  Należy pomnożyć pamięć PRZĘDZy z liczbą węzłów w klastrze, aby uzyskać łączną ilość pamięci PRZĘDZy.

Łączna ilość pamięci PRZĘDZy = węzły * pamięć PRZĘDZy na węzeł

Jeśli używasz pustego klastra, pamięć może być całkowitą ilością pamięci PRZĘDZy dla klastra.  Jeśli inne aplikacje używają pamięci, można użyć tylko części pamięci klastra, zmniejszając liczbę odwzorów lub mniejszą liczbę kontenerów, które mają być używane.  

**Krok 4. Obliczanie liczby kontenerów PRZĘDZy**

Kontenery PRZĘDZy określają ilość współbieżności dostępnej dla zadania.  Weź łączną ilość pamięci PRZĘDZy i Podziel ją przez MapReduce. map. Memory.  

\# kontenerów PRZĘDZy = Łączna ilość pamięci PRZĘDZy/MapReduce. map. Memory

**Krok 5. Ustawianie MapReduce. job. Maps/MapReduce. job. redukuje**

Ustaw wartość MapReduce. job. Maps/MapReduce. job. zmniejsza się do co najmniej liczby dostępnych kontenerów.  Aby uzyskać lepszą wydajność, można eksperymentować przez zwiększenie liczby odwzorowań i elementów ograniczających.  Należy pamiętać, że więcej odwzorowań będzie miało dodatkowe obciążenie, tak że zbyt wiele mapowania może obniżyć wydajność.  

Planowanie procesora CPU i izolacja procesora CPU są domyślnie wyłączone, więc liczba kontenerów PRZĘDZy jest ograniczona przez pamięć.

## <a name="example-calculation"></a>Przykładowe obliczenie

Załóżmy, że mamy klaster składający się z 8 D14 węzłów i chcemy uruchomić zadanie intensywnie korzystające z operacji we/wy.  Poniżej przedstawiono obliczenia, które należy wykonać:

**Krok 1. określenie liczby uruchomionych zadań**

W tym przykładzie Załóżmy, że nasze zadanie jest jedynym uruchomionym zadaniem.  

**Krok 2: Ustawianie MapReduce. map. Memory/MapReduce. Zmniejsz ilość pamięci**

W tym przykładzie uruchomiono zadanie intensywnie korzystające z operacji we/wy i podjęto decyzję o tym, że WŁĄCZONĄ pamięci na potrzeby zadań związanych z mapowaniem będą wystarczające.

MapReduce. map. Memory = WŁĄCZONĄ

**Krok 3. Określanie całkowitej ilości pamięci PRZĘDZy**

Łączna ilość pamięci z klastra to 8 węzłów * 96GB pamięci PRZĘDZy dla D14 = 768GB

**Krok 4. Obliczanie liczby kontenerów PRZĘDZy**

\# kontenerów PRZĘDZy = 768GB dostępnej pamięci/3 GB pamięci = 256

**Krok 5. Ustawianie MapReduce. job. Maps/MapReduce. job. redukuje**

mapreduce.map.jobs = 256

## <a name="examples-to-run"></a>Przykłady do uruchomienia

Aby zademonstrować, jak MapReduce działa na Data Lake Storage Gen2, poniżej przedstawiono przykładowy kod, który został uruchomiony w klastrze z następującymi ustawieniami:

* 16 węzłów D14v2
* Klaster Hadoop z systemem HDI 3,6

Oto kilka przykładowych poleceń do uruchomienia MapReduce Teragen, Terasort i Teravalidate.  Można dostosować te polecenia w zależności od zasobów.

**Teragen**

```cmd
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 10000000000 abfs://example/data/1TB-sort-input
```

**Terasort**

```cmd
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 -Dmapreduce.job.reduces=512 -Dmapreduce.reduce.memory.mb=3072 abfs://example/data/1TB-sort-input abfs://example/data/1TB-sort-output
```

**Teravalidate**

```cmd
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapreduce.job.maps=512 -Dmapreduce.map.memory.mb=3072 abfs://example/data/1TB-sort-output abfs://example/data/1TB-sort-validate
```