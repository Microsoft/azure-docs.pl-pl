---
title: Dostrajanie wydajności — Hive na Azure Data Lake Storage Gen1
description: Dowiedz się więcej o dostosowywaniu wydajności dla programu Hive w usłudze HdInsight i Azure Data Lake Storage Gen1. W przypadku zapytań intensywnie korzystających z operacji we/wy dostrojenie usługi Hive w celu uzyskania lepszej wydajności.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 12/19/2016
ms.author: twooley
ms.openlocfilehash: 082d684ed0a29cb6bf2de9c506886b6d98cf174a
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/22/2020
ms.locfileid: "97723785"
---
# <a name="performance-tuning-guidance-for-hive-on-hdinsight-and-azure-data-lake-storage-gen1"></a>Wskazówki dotyczące dostrajania wydajności dla programu Hive w usłudze HDInsight i Azure Data Lake Storage Gen1

Ustawienia domyślne zostały skonfigurowane w taki sposób, aby zapewnić dobrą wydajność w wielu różnych przypadkach użycia.  W przypadku zapytań intensywnie korzystających z operacji we/wy można dostrajać gałąź, aby uzyskać lepszą wydajność dzięki Azure Data Lake Storage Gen1.  

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Konto Data Lake Storage Gen1**. Aby uzyskać instrukcje dotyczące sposobu tworzenia takiego elementu, zobacz Wprowadzenie [do Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* **Klaster usługi Azure HDInsight** z dostępem do konta Data Lake Storage Gen1. Zobacz [Tworzenie klastra usługi HDInsight przy użyciu Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md). Upewnij się, że Pulpit zdalny dla klastra są włączone.
* **Uruchamianie programu Hive w usłudze HDInsight**.  Aby dowiedzieć się więcej na temat uruchamiania zadań Hive w usłudze HDInsight, zobacz [Korzystanie z usługi Hive w usłudze HDInsight](../hdinsight/hadoop/hdinsight-use-hive.md)
* **Wskazówki dotyczące dostrajania wydajności na Data Lake Storage Gen1**.  Ogólne pojęcia dotyczące wydajności znajdują się w temacie [Data Lake Storage Gen1 wskazówki dotyczące dostrajania wydajności](./data-lake-store-performance-tuning-guidance.md)

## <a name="parameters"></a>Parametry

Poniżej przedstawiono najważniejsze ustawienia, które należy dostosować w celu zwiększenia wydajności Data Lake Storage Gen1:

* **Hive. tez. Container. size** — ilość pamięci używanej przez poszczególne zadania

* **tez. Grouping. min-size** — minimalny rozmiar każdego mapowania

* **tez. Grouping. Max-size** — maksymalny rozmiar każdego mapowania

* **hive.exec. zredukować. bajtów. na. zmniejszenie** — rozmiar każdego środka zmniejszającego

**Hive. tez. Container. size** — rozmiar kontenera określa ilość dostępnej pamięci dla każdego zadania.  Jest to główne dane wejściowe służące do kontrolowania współbieżności w usłudze Hive.  

**tez. Group. min-size** — ten parametr pozwala ustawić minimalny rozmiar każdego mapowania.  Jeśli liczba odwzorowań, które tez wybór jest mniejsza niż wartość tego parametru, tez będzie używać wartości ustawionej w tym miejscu.

**tez. Group. Max-size** — parametr umożliwia ustawienie maksymalnego rozmiaru każdego mapowania.  Jeśli liczba odwzorowań, które tez wybór są większe niż wartość tego parametru, tez będzie używać wartości ustawionej w tym miejscu.

**hive.exec. zredukować. bajtów. na. zredukować** — ten parametr ustawia rozmiar poszczególnych elementów zmniejszających.  Domyślnie każdy z nich zmniejsza wartość 256 MB.  

## <a name="guidance"></a>Wskazówki

**Ustaw hive.exec. redukować. Bytes. per. zredukować** — wartość domyślna działa prawidłowo, gdy dane są nieskompresowane.  W przypadku skompresowanych danych należy zmniejszyć rozmiar tego ograniczenia.  

**Ustaw gałąź. tez. Container. size** — w każdym węźle pamięć jest określana przez przędzę. nodemanager. Resource. Memory-MB i powinna być prawidłowo ustawiona w klastrze HDI domyślnie.  Aby uzyskać dodatkowe informacje na temat ustawiania odpowiedniej pamięci w ramach PRZĘDZy, zobacz ten [wpis](../hdinsight/hdinsight-hadoop-hive-out-of-memory-error-oom.md).

Obciążenia intensywnie korzystające z operacji we/wy mogą korzystać z większej liczby równoległości, zmniejszając rozmiar kontenera tez. Dzięki temu użytkownik ma więcej kontenerów, co zwiększa współbieżność.  Jednak niektóre zapytania programu Hive wymagają znacznej ilości pamięci (np. MapJoin).  Jeśli zadanie nie ma wystarczającej ilości pamięci, wystąpi wyjątek braku pamięci w czasie wykonywania.  Jeśli wystąpią wyjątki dotyczące braku pamięci, należy zwiększyć ilość pamięci.   

Liczba współbieżnych zadań uruchomionych lub równoległych zostanie ograniczona przez łączną ilość pamięci PRZĘDZy.  Liczba kontenerów PRZĘDZy będzie określać liczbę współbieżnych zadań, które można uruchomić.  Aby znaleźć pamięć PRZĘDZy na węzeł, możesz przejść do Ambari.  Przejdź do PRZĘDZy i Wyświetl kartę konfiguracje.  W tym oknie zostanie wyświetlona pamięć PRZĘDZy.  

> Łączna ilość pamięci PRZĘDZy = węzły * pamięć PRZĘDZy na węzeł liczba kontenerów PRZĘDZy = Łączna ilość pamięci PRZĘDZy/rozmiar kontenera tez

Kluczem do poprawienia wydajności przy użyciu Data Lake Storage Gen1 jest zwiększenie współbieżności tak, jak to możliwe.  Tez automatycznie oblicza liczbę zadań, które należy utworzyć, aby nie trzeba było ich ustawiać.   

## <a name="example-calculation"></a>Przykładowe obliczenie

Załóżmy, że masz klaster D14 z 8 węzłami.  

> Łączna ilość pamięci PRZĘDZy = węzły * pamięć PRZĘDZy na węzeł Łączna ilość pamięci PRZĘDZy = 8 węzłów * 96GB = 768GB liczba kontenerów PRZĘDZy = 768GB/3072MB = 256

## <a name="limitations"></a>Ograniczenia

**Ograniczanie Data Lake Storage Gen1** 

W przypadku osiągnięcia limitów przepustowości zapewnianej przez Data Lake Storage Gen1 można zacząć wyświetlać błędy zadań. Może to być zidentyfikowane przez obserwowanie błędów ograniczania w dziennikach zadań.  Równoległość można zmniejszyć przez zwiększenie rozmiaru kontenera tez.  Jeśli potrzebujesz większej współbieżności dla swojego zadania, skontaktuj się z nami.

Aby sprawdzić, czy masz ograniczone ograniczenia, należy włączyć rejestrowanie debugowania po stronie klienta. Oto jak to zrobić:

1. Umieść następującą właściwość we właściwościach Log4J w konfiguracji Hive. Można to zrobić z widoku Ambari: Log4J. rejestrator. com. Microsoft. Azure. datalake. Store = DEBUGUJ ponownie uruchom wszystkie węzły/usługę, aby konfiguracja zaczęła obowiązywać.

2. W przypadku uzyskania ograniczenia przepustowości w pliku dziennika programu Hive zobaczysz kod błędu HTTP 429. Plik dziennika programu Hive znajduje się w/tmp/ &lt; użytkownika &gt; /Hive.log

## <a name="further-information-on-hive-tuning"></a>Więcej informacji na temat dostrajania programu Hive

Oto kilka blogów, które pomogą dostosować zapytania programu Hive:
* [Optymalizowanie zapytań programu Hive dla platformy Hadoop w usłudze HDInsight](../hdinsight/hdinsight-hadoop-optimize-hive-query.md)
* [Kodowanie pliku zapytania programu Hive w usłudze Azure HDInsight](/archive/blogs/bigdatasupport/encoding-the-hive-query-file-in-azure-hdinsight)
* [Zapłon z technologią optymalizacji Hive w usłudze HDInsight](https://channel9.msdn.com/events/Machine-Learning-and-Data-Sciences-Conference/Data-Science-Summit-2016/MSDSS25)