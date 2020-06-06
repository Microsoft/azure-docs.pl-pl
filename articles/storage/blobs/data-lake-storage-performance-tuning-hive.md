---
title: 'Dostrajanie wydajności: Hive, & HDInsight Azure Data Lake Storage Gen2 | Microsoft Docs'
description: Azure Data Lake Storage Gen2 wskazówki dotyczące dostrajania wydajności Hive.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 11/18/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 9a54565f320ae45a4a8297a40027c5e6b3b25202
ms.sourcegitcommit: 813f7126ed140a0dff7658553a80b266249d302f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/06/2020
ms.locfileid: "84465970"
---
# <a name="tune-performance-hive-hdinsight--azure-data-lake-storage-gen2"></a>Dostrajanie wydajności: Hive, & usługi HDInsight Azure Data Lake Storage Gen2

Ustawienia domyślne zostały skonfigurowane w taki sposób, aby zapewnić dobrą wydajność w wielu różnych przypadkach użycia.  W przypadku zapytań intensywnie korzystających z operacji we/wy można dostrajać gałąź, aby uzyskać lepszą wydajność dzięki Azure Data Lake Storage Gen2.  

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Konto Data Lake Storage Gen2**. Aby uzyskać instrukcje dotyczące sposobu tworzenia takiego elementu, zobacz [Szybki Start: Tworzenie konta magazynu Azure Data Lake Storage Gen2](data-lake-storage-quickstart-create-account.md)
* **Klaster usługi Azure HDInsight** z dostępem do konta Data Lake Storage Gen2. Zobacz [używanie Azure Data Lake Storage Gen2 z klastrami usługi Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2)
* **Uruchamianie programu Hive w usłudze HDInsight**.  Aby dowiedzieć się więcej na temat uruchamiania zadań Hive w usłudze HDInsight, zobacz [Korzystanie z usługi Hive w usłudze HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-use-hive)
* **Wskazówki dotyczące dostrajania wydajności na Data Lake Storage Gen2**.  Ogólne pojęcia dotyczące wydajności znajdują się w temacie [Data Lake Storage Gen2 wskazówki dotyczące dostrajania wydajności](data-lake-storage-performance-tuning-guidance.md)

## <a name="parameters"></a>Parametry

Poniżej przedstawiono najważniejsze ustawienia, które należy dostosować w celu zwiększenia wydajności Data Lake Storage Gen2:

* **Hive. tez. Container. size** — ilość pamięci używanej przez poszczególne zadania

* **tez. Grouping. min-size** — minimalny rozmiar każdego mapowania

* **tez. Grouping. Max-size** — maksymalny rozmiar każdego mapowania

* **Hive. exec. redukować. Bytes. per. zredukować** — rozmiar każdego środka zmniejszającego

**Hive. tez. Container. size** — rozmiar kontenera określa ilość dostępnej pamięci dla każdego zadania.  Jest to główne dane wejściowe służące do kontrolowania współbieżności w usłudze Hive.  

**tez. Group. min-size** — ten parametr pozwala ustawić minimalny rozmiar każdego mapowania.  Jeśli liczba odwzorowań, które tez wybór jest mniejsza niż wartość tego parametru, tez będzie używać wartości ustawionej w tym miejscu.

**tez. Group. Max-size** — parametr umożliwia ustawienie maksymalnego rozmiaru każdego mapowania.  Jeśli liczba odwzorowań, które tez wybór są większe niż wartość tego parametru, tez będzie używać wartości ustawionej w tym miejscu.

**Hive. exec. redukować. Bytes. per. zredukować** — ten parametr ustawia rozmiar każdego środka.  Domyślnie każdy z nich zmniejsza wartość 256 MB.  

## <a name="guidance"></a>Wskazówki

**Set Hive. exec. redukować. Bytes. per. zredukować** — wartość domyślna działa prawidłowo, gdy dane są nieskompresowane.  W przypadku skompresowanych danych należy zmniejszyć rozmiar tego ograniczenia.  

**Ustaw gałąź. tez. Container. size** — w każdym węźle pamięć jest określana przez przędzę. nodemanager. Resource. Memory-MB i powinna być prawidłowo ustawiona w klastrze HDI domyślnie.  Aby uzyskać dodatkowe informacje na temat ustawiania odpowiedniej pamięci w ramach PRZĘDZy, zobacz ten [wpis](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-hive-out-of-memory-error-oom).

Obciążenia intensywnie korzystające z operacji we/wy mogą korzystać z większej liczby równoległości, zmniejszając rozmiar kontenera tez. Dzięki temu użytkownik ma więcej kontenerów, co zwiększa współbieżność.  Jednak niektóre zapytania programu Hive wymagają znacznej ilości pamięci (np. MapJoin).  Jeśli zadanie nie ma wystarczającej ilości pamięci, wystąpi wyjątek braku pamięci w czasie wykonywania.  Jeśli wystąpią wyjątki dotyczące braku pamięci, należy zwiększyć ilość pamięci.   

Liczba współbieżnych zadań uruchomionych lub równoległych zostanie ograniczona przez łączną ilość pamięci PRZĘDZy.  Liczba kontenerów PRZĘDZy będzie określać liczbę współbieżnych zadań, które można uruchomić.  Aby znaleźć pamięć PRZĘDZy na węzeł, możesz przejść do Ambari.  Przejdź do PRZĘDZy i Wyświetl kartę konfiguracje.  W tym oknie zostanie wyświetlona pamięć PRZĘDZy.  

        Total YARN memory = nodes * YARN memory per node
        # of YARN containers = Total YARN memory / Tez container size
Kluczem do poprawienia wydajności przy użyciu Data Lake Storage Gen2 jest zwiększenie współbieżności tak, jak to możliwe.  Tez automatycznie oblicza liczbę zadań, które należy utworzyć, aby nie trzeba było ich ustawiać.   

## <a name="example-calculation"></a>Przykładowe obliczenie

Załóżmy, że masz klaster D14 z 8 węzłami.  

    Total YARN memory = nodes * YARN memory per node
    Total YARN memory = 8 nodes * 96GB = 768GB
    # of YARN containers = 768GB / 3072MB = 256

## <a name="further-information-on-hive-tuning"></a>Więcej informacji na temat dostrajania programu Hive

Oto kilka blogów, które pomogą dostosować zapytania programu Hive:
* [Optymalizowanie zapytań programu Hive dla platformy Hadoop w usłudze HDInsight](https://azure.microsoft.com/documentation/articles/hdinsight-hadoop-optimize-hive-query/)
* [Rozwiązywanie problemów z wydajnością zapytań Hive](https://blogs.msdn.microsoft.com/bigdatasupport/2015/08/13/troubleshooting-hive-query-performance-in-hdinsight-hadoop-cluster/)
* [Zapłon z technologią optymalizacji Hive w usłudze HDInsight](https://channel9.msdn.com/events/Machine-Learning-and-Data-Sciences-Conference/Data-Science-Summit-2016/MSDSS25)
