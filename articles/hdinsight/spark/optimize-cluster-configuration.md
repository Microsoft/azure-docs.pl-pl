---
title: Optymalizowanie konfiguracji klastra Apache Spark — usługa Azure HDInsight
description: Dowiedz się, jak skonfigurować klaster Apache Spark w celu zmaksymalizowania przepływności w usłudze Azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/21/2020
ms.custom: contperf-fy21q1
ms.openlocfilehash: f70e9f242b0f12abf58e72554e7cb6819ce8f7a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98944734"
---
# <a name="cluster-configuration-optimization-for-apache-spark"></a>Optymalizacja konfiguracji klastra dla Apache Spark

W tym artykule omówiono sposób optymalizowania konfiguracji klastra Apache Spark w celu uzyskania najlepszej wydajności w usłudze Azure HDInsight.

## <a name="overview"></a>Omówienie

W zależności od obciążenia klastra platformy Spark można określić, że niedomyślna konfiguracja platformy Spark będzie powodować bardziej zoptymalizowane wykonywanie zadań platformy Spark.  Wykonaj testy testowe z przykładowymi obciążeniami, aby zweryfikować wszystkie inne niż domyślne konfiguracje klastrów.

Poniżej przedstawiono niektóre typowe parametry, które można dostosować:

|Parametr |Opis |
|---|---|
|--NUM-wykonawcy|Ustawia odpowiednią liczbę wykonawców.|
|--wykonawcze-rdzenie|Ustawia liczbę rdzeni dla każdego wykonawcy. Zwykle powinny być stosowane wykonawcy o rozmiarze średnim, ponieważ inne procesy zużywają część dostępnej pamięci.|
|--Wykonawca pamięci|Ustawia rozmiar pamięci dla każdego wykonawcy, który kontroluje rozmiar sterty w PRZĘDZe. Pozostaw pewną ilość pamięci, aby wykonać obciążenie.|

## <a name="select-the-correct-executor-size"></a>Wybierz odpowiedni rozmiar wykonawcy

Podczas podejmowania decyzji o konfiguracji programu wykonującego należy wziąć pod uwagę narzut na wyrzucanie elementów bezużytecznych języka Java.

* Czynniki zmniejszające rozmiar programu wykonującego:
    1. Zmniejsz rozmiar sterty poniżej 32 GB, aby zachować obciążenie GC < 10%.
    2. Zmniejsz liczbę rdzeni, aby zachować obciążenie GC < 10%.

* Czynniki zwiększające rozmiar wykonawcy:
    1. Zmniejszenie obciążenia komunikacji między wykonawcami.
    2. Zmniejsz liczbę otwartych połączeń między modułami wykonującymi (N2) w większych klastrach (>100).
    3. Zwiększ rozmiar sterty, aby zapewnić obsługę zadań intensywnie korzystających z pamięci.
    4. Opcjonalnie: Zmniejsz obciążenie pamięci dla modułu wykonawczego.
    5. Opcjonalne: Zwiększ użycie i współbieżność przez zasubskrybowanie procesora CPU.

Jako ogólna zasada, podczas wybierania rozmiaru wykonawcy:

1. Zacznij od 30 GB na wykonawcę i Dystrybuuj dostępne rdzenie maszynowe.
2. Zwiększ liczbę rdzeni wykonawców dla większych klastrów (> 100).
3. Zmodyfikuj rozmiar na podstawie zarówno w przypadku wersji próbnej, jak i w powyższych czynnikach, takich jak obciążenie GC.

Podczas uruchamiania współbieżnych zapytań należy rozważyć następujące kwestie:

1. Zacznij od 30 GB na wykonawcę i wszystkich rdzeni maszynowych.
2. Utwórz wiele równoległych aplikacji platformy Spark przez zasubskrybowanie procesora CPU (około 30% poprawy opóźnienia).
3. Dystrybuuj zapytania w aplikacjach równoległych.
4. Zmodyfikuj rozmiar na podstawie zarówno w przypadku wersji próbnej, jak i w powyższych czynnikach, takich jak obciążenie GC.

Aby uzyskać więcej informacji na temat używania Ambari do konfigurowania modułów wykonujących, zobacz [ustawienia Apache Spark — wykonawcze platformy Spark](apache-spark-settings.md#configuring-spark-executors).

Monitoruj wydajność zapytań pod kątem wartości odstających lub innych problemów z wydajnością, przeglądając widok oś czasu. Program SQL Graph, statystyki zadań i tak dalej. Aby uzyskać informacje na temat debugowania zadań platformy Spark przy użyciu PRZĘDZy i serwera historii platformy Spark, zobacz [debugowanie Apache Spark zadania uruchomione w usłudze Azure HDInsight](apache-spark-job-debugging.md). Aby uzyskać porady na temat używania serwera osi czasu PRZĘDZy, zobacz [dostęp Apache Hadoop Dzienniki aplikacji przędzy](../hdinsight-hadoop-access-yarn-app-logs-linux.md).

Czasami jeden lub kilka z wykonawców jest wolniejsze niż inne, a wykonywanie zadań trwa znacznie dłużej. Ten spowolnienie często odbywa się w większych klastrach (> 30 węzłach). W takim przypadku należy podzielić pracę na większą liczbę zadań, aby harmonogram mógł wyrównać powolne zadania. Na przykład należy mieć co najmniej dwa razy więcej zadań jako liczbę rdzeni wykonawców w aplikacji. Możesz również włączyć spekulacyjne wykonywanie zadań za pomocą `conf: spark.speculation = true` .

## <a name="next-steps"></a>Następne kroki

* [Optymalizowanie przetwarzania danych dla Apache Spark](optimize-cluster-configuration.md)
* [Optymalizuj magazyn danych dla Apache Spark](optimize-data-storage.md)
* [Optymalizuj użycie pamięci przez Apache Spark](optimize-memory-usage.md)
