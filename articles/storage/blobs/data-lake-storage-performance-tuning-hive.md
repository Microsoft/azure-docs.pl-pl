---
title: 'Dostrajanie wydajności: Hive, & HDInsight Azure Data Lake Storage Gen2 | Microsoft Docs'
description: Poznaj wskazówki dotyczące dostrajania dla zapytań intensywnie korzystających z operacji we/wy przy użyciu usługi Hive, usługi HDInsight i Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 11/18/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 4b1e5dd3c72122ade2fd4d4092bb18a7acf215f5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95912947"
---
# <a name="tune-performance-hive-hdinsight--azure-data-lake-storage-gen2"></a>Dostrajanie wydajności: Hive, & usługi HDInsight Azure Data Lake Storage Gen2

Ustawienia domyślne zostały skonfigurowane w taki sposób, aby zapewnić dobrą wydajność w wielu różnych przypadkach użycia.  W przypadku zapytań intensywnie korzystających z operacji we/wy można dostrajać gałąź, aby uzyskać lepszą wydajność dzięki Azure Data Lake Storage Gen2.  

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Konto Data Lake Storage Gen2**. Aby uzyskać instrukcje dotyczące sposobu tworzenia takiego elementu, zobacz [Szybki Start: Tworzenie konta magazynu Azure Data Lake Storage Gen2](../common/storage-account-create.md)
* **Klaster usługi Azure HDInsight** z dostępem do konta Data Lake Storage Gen2. Zobacz [używanie Azure Data Lake Storage Gen2 z klastrami usługi Azure HDInsight](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md)
* **Uruchamianie programu Hive w usłudze HDInsight**.  Aby dowiedzieć się więcej na temat uruchamiania zadań Hive w usłudze HDInsight, zobacz [Korzystanie z usługi Hive w usłudze HDInsight](../../hdinsight/hadoop/hdinsight-use-hive.md)
* **Wskazówki dotyczące dostrajania wydajności na Data Lake Storage Gen2**.  Ogólne pojęcia dotyczące wydajności znajdują się w temacie [Data Lake Storage Gen2 wskazówki dotyczące dostrajania wydajności](data-lake-storage-performance-tuning-guidance.md)

## <a name="parameters"></a>Parametry

Poniżej przedstawiono najważniejsze ustawienia, które należy dostosować w celu zwiększenia wydajności Data Lake Storage Gen2:

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

**Ustaw gałąź. tez. Container. size** — w każdym węźle pamięć jest określana przez przędzę. nodemanager. Resource. Memory-MB i powinna być prawidłowo ustawiona w klastrze HDI domyślnie.  Aby uzyskać dodatkowe informacje na temat ustawiania odpowiedniej pamięci w ramach PRZĘDZy, zobacz ten [wpis](../../hdinsight/hdinsight-hadoop-hive-out-of-memory-error-oom.md).

Obciążenia intensywnie korzystające z operacji we/wy mogą korzystać z większej liczby równoległości, zmniejszając rozmiar kontenera tez. Dzięki temu użytkownik ma więcej kontenerów, co zwiększa współbieżność.  Jednak niektóre zapytania programu Hive wymagają znacznej ilości pamięci (np. MapJoin).  Jeśli zadanie nie ma wystarczającej ilości pamięci, wystąpi wyjątek braku pamięci w czasie wykonywania.  Jeśli wystąpią wyjątki dotyczące braku pamięci, należy zwiększyć ilość pamięci.   

Liczba współbieżnych zadań uruchomionych lub równoległych zostanie ograniczona przez łączną ilość pamięci PRZĘDZy.  Liczba kontenerów PRZĘDZy będzie określać liczbę współbieżnych zadań, które można uruchomić.  Aby znaleźć pamięć PRZĘDZy na węzeł, możesz przejść do Ambari.  Przejdź do PRZĘDZy i Wyświetl kartę konfiguracje.  W tym oknie zostanie wyświetlona pamięć PRZĘDZy.  

- Łączna ilość pamięci PRZĘDZy = węzły * pamięć PRZĘDZy na węzeł
- \# kontenerów PRZĘDZy = Łączna ilość pamięci PRZĘDZy/rozmiar kontenera tez

Kluczem do poprawienia wydajności przy użyciu Data Lake Storage Gen2 jest zwiększenie współbieżności tak, jak to możliwe.  Tez automatycznie oblicza liczbę zadań, które należy utworzyć, aby nie trzeba było ich ustawiać.   

## <a name="example-calculation"></a>Przykładowe obliczenie

Załóżmy, że masz klaster D14 z 8 węzłami.  

- Łączna ilość pamięci PRZĘDZy = węzły * pamięć PRZĘDZy na węzeł
- Łączna ilość pamięci PRZĘDZy = 8 węzłów * 96GB = 768GB
- \# kontenerów PRZĘDZy = 768GB/3072MB = 256

## <a name="further-information-on-hive-tuning"></a>Więcej informacji na temat dostrajania programu Hive

Oto kilka blogów, które pomogą dostosować zapytania programu Hive:
* [Optymalizowanie zapytań programu Hive dla platformy Hadoop w usłudze HDInsight](../../hdinsight/hdinsight-hadoop-optimize-hive-query.md)
* [Optymalizowanie zapytań technologii Apache Hive w usłudze Azure HDInsight](../../hdinsight/hdinsight-hadoop-optimize-hive-query.md)
* [Zapłon z technologią optymalizacji Hive w usłudze HDInsight](https://channel9.msdn.com/events/Machine-Learning-and-Data-Sciences-Conference/Data-Science-Summit-2016/MSDSS25)