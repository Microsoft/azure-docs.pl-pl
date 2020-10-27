---
title: Informacje o wersji usługi Azure HDInsight
description: Najnowsze informacje o wersji usługi Azure HDInsight. Uzyskaj porady deweloperskie i szczegóły dotyczące usługi Hadoop, Spark, R Server, Hive i innych.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/07/2020
ms.openlocfilehash: 0c14e5537385b7518fd08d9d3599993bc6d82f88
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/26/2020
ms.locfileid: "92535505"
---
# <a name="azure-hdinsight-release-notes"></a>Informacje o wersji usługi Azure HDInsight

Ten artykuł zawiera informacje **o najnowszych aktualizacjach wersji usługi Azure** HDInsight. Aby uzyskać informacje dotyczące wcześniejszych wersji, zobacz [archiwum informacji o wersji usługi HDInsight](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Podsumowanie

Usługa Azure HDInsight to jedna z najpopularniejszych usług dla klientów korporacyjnych na potrzeby analiz typu open source na platformie Azure.

## <a name="release-date-10082020"></a>Data wydania: 10/08/2020

Ta wersja dotyczy zarówno usługi HDInsight 3,6, jak i usługi HDInsight 4,0. Wersja usługi HDInsight jest udostępniana wszystkim regionom przez kilka dni. Data wydania wskazuje na datę wydania pierwszego regionu. Jeśli nie widzisz poniżej zmian, poczekaj na zakończenie wydania w Twoim regionie w kilka dni.

## <a name="new-features"></a>Nowe funkcje
### <a name="hdinsight-private-clusters-with-no-public-ip-and-private-link-preview"></a>Prywatne klastry usługi HDInsight bez publicznego adresu IP i prywatnego (wersja zapoznawcza)
Usługa HDInsight obsługuje teraz tworzenie klastrów bez publicznego adresu IP i prywatnego połączenia z klastrami w wersji zapoznawczej. Klienci mogą używać nowych zaawansowanych ustawień sieci, aby utworzyć w pełni izolowany klaster bez publicznego adresu IP i korzystać z własnych prywatnych punktów końcowych w celu uzyskania dostępu do klastra. 

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Przechodzenie do zestawów skalowania maszyn wirtualnych platformy Azure
Usługa HDInsight teraz używa maszyn wirtualnych platformy Azure do aprowizacji klastra. Począwszy od tej wersji, usługa stopniowo przeprowadzi migrację do [zestawów skalowania maszyn wirtualnych platformy Azure](../virtual-machine-scale-sets/overview.md). Cały proces może potrwać miesiące. Po przeprowadzeniu migracji regionów i subskrypcji nowo utworzone klastry usługi HDInsight będą uruchamiane w zestawach skalowania maszyn wirtualnych bez akcji klienta. Nie oczekiwano zmiany krytycznej.

## <a name="deprecation"></a>Przestarzałe
#### <a name="deprecation-of-hdinsight-36-ml-services-cluster"></a>Wycofanie klastra usług HDInsight 3,6 ML
Typ klastra usług HDInsight 3,6 ML zostanie zakończony przez gru 31 2020. Klienci nie będą mogli tworzyć nowych klastrów usług 3,6 ML. Istniejące klastry będą działać bez pomocy technicznej firmy Microsoft. Sprawdź w [tym miejscu](./hdinsight-component-versioning.md#available-versions)wygaśnięcie pomocy technicznej dotyczącej wersji i typów klastrów usługi HDInsight.

## <a name="behavior-changes"></a>Zmiany zachowania
Brak zmian w zachowaniu dla tej wersji.

## <a name="upcoming-changes"></a>Nadchodzące zmiany
Następujące zmiany zostaną wykonane w przyszłych wydaniach.

### <a name="ability-to-select-different-zookeeper-virtual-machine-sizes-for-spark-hadoop-and-ml-services"></a>Możliwość wybierania różnych dozorcy rozmiary maszyn wirtualnych dla usług Spark, Hadoop i ML
Usługa HDInsight obecnie nie obsługuje dostosowywania rozmiaru węzła dozorcy dla typów klastrów usługi Spark, Hadoop i ML. Domyślnie A2_v2/a2 rozmiary maszyn wirtualnych, które są dostępne bezpłatnie. W nadchodzącym wydaniu można wybrać dozorcy rozmiar maszyny wirtualnej, który jest najbardziej odpowiedni dla danego scenariusza. Dozorcy węzły z maszyną wirtualną o rozmiarze innym niż A2_v2/a2 będą obciążane opłatami. Maszyny wirtualne A2_v2 i a2 są nadal udostępniane bezpłatnie.

## <a name="bug-fixes"></a>Poprawki błędów
Usługa HDInsight kontynuuje zwiększanie niezawodności i wydajności klastrów. 

## <a name="component-version-change"></a>Zmiana wersji składnika
Brak zmian wersji składnika dla tej wersji. Bieżące wersje składników usługi HDInsight 4,0 i HDInsight 3,6 można znaleźć w [tym dokumencie](./hdinsight-component-versioning.md).
