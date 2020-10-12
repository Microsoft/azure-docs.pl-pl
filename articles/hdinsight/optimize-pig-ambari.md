---
title: Optymalizowanie oprogramowania Apache świni z Apache Ambari w usłudze Azure HDInsight
description: Użyj interfejsu użytkownika sieci Web Apache Ambari w celu skonfigurowania i zoptymalizowania oprogramowania Apache wieprz.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 05/04/2020
ms.openlocfilehash: d6f20780ccd90d83631ce07411820fb8c9280c3e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86084090"
---
# <a name="optimize-apache-pig-with-apache-ambari-in-azure-hdinsight"></a>Optymalizowanie oprogramowania Apache świni z Apache Ambari w usłudze Azure HDInsight

Apache Ambari to interfejs sieci Web umożliwiający zarządzanie i monitorowanie klastrów usługi HDInsight. Wprowadzenie do interfejsu użytkownika sieci Web Ambari można znaleźć w temacie [Zarządzanie klastrami usługi HDInsight przy użyciu interfejsu użytkownika sieci Web Apache Ambari](hdinsight-hadoop-manage-ambari.md).

Właściwości z Apache świni można modyfikować za pomocą interfejsu użytkownika sieci Web Ambari, aby dostosowywać zapytania do trzody chlewnej. Modyfikowanie właściwości świni z Ambari bezpośrednio modyfikuje właściwości świni w `/etc/pig/2.4.2.0-258.0/pig.properties` pliku.

1. Aby zmodyfikować **Właściwości świni, przejdź do karty konfiguracje** trzody chlewnej, a następnie rozwiń okienko **Zaawansowane właściwości świni** .

1. Znajdź, Usuń komentarz i zmień wartość właściwości, którą chcesz zmodyfikować.

1. Wybierz pozycję **Zapisz** w prawym górnym rogu okna, aby zapisać nową wartość. Niektóre właściwości mogą wymagać ponownego uruchomienia usługi.

    ![Zaawansowane właściwości z Apache świni](./media/optimize-pig-ambari/advanced-pig-properties.png)

> [!NOTE]  
> Wszystkie ustawienia poziomu sesji przesłaniają wartości właściwości w `pig.properties` pliku.

## <a name="tune-execution-engine"></a>Dostrajanie aparatu wykonywania

Dostępne są dwa aparaty wykonywania, które umożliwiają wykonywanie skryptów świń: MapReduce i tez. Tez to zoptymalizowany aparat i jest znacznie szybszy niż MapReduce.

1. Aby zmodyfikować aparat wykonywania, w okienku **Zaawansowane właściwości trzody chlewnej** Znajdź właściwość `exectype` .

1. Wartość domyślna to **MapReduce**. Zmień ją na **tez**.

## <a name="enable-local-mode"></a>Włącz tryb lokalny

Podobnie jak w przypadku programu Hive, tryb lokalny służy do przyspieszenia zadań z stosunkowo mniejszymi ilościami danych.

1. Aby włączyć tryb lokalny, ustaw `pig.auto.local.enabled` **wartość PRAWDA**. Wartość domyślna to false.

1. Zadania o rozmiarze danych wejściowych mniejszym niż `pig.auto.local.input.maxbytes` wartość właściwości są uważane za małe zadania. Wartość domyślna to 1 GB.

## <a name="copy-user-jar-cache"></a>Kopiuj pamięć podręczną jar użytkownika

Świnie kopiuje pliki JAR wymagane przez UDF do rozproszonej pamięci podręcznej w celu udostępnienia ich dla węzłów zadań. Te Jars nie zmieniają się często. W przypadku włączenia tego `pig.user.cache.enabled` Ustawienia umożliwia Jars w pamięci podręcznej, aby ponownie wykorzystać je do zadań wykonywanych przez tego samego użytkownika. To ustawienie powoduje niewielkie zwiększenie wydajności zadania.

1. Aby włączyć, ustaw `pig.user.cache.enabled` wartość true. Wartością domyślną jest false.

1. Aby ustawić ścieżkę bazową w pamięci podręcznej Jars, ustaw `pig.user.cache.location` na ścieżkę bazową. Wartość domyślna to `/tmp`.

## <a name="optimize-performance-with-memory-settings"></a>Optymalizowanie wydajności przy użyciu ustawień pamięci

Poniższe ustawienia pamięci mogą pomóc zoptymalizować wydajność skryptu trzody chlewnej.

* `pig.cachedbag.memusage`: Ilość pamięci podaną dla zbioru. Zbiór to kolekcja krotek. Krotka jest uporządkowanym zestawem pól, a pole jest fragmentem danych. Jeśli dane w zbiorze wykraczają poza daną pamięć, są one rozlane na dysk. Wartość domyślna to 0,2, która reprezentuje 20% dostępnej pamięci. Ta pamięć jest współdzielona przez wszystkie torby w aplikacji.

* `pig.spill.size.threshold`: Torby większe niż ten próg rozmiaru rozlania (w bajtach) są rozlane na dysk. Wartość domyślna to 5 MB.

## <a name="compress-temporary-files"></a>Kompresuj pliki tymczasowe

Świnie generuje pliki tymczasowe podczas wykonywania zadania. Kompresowanie plików tymczasowych powoduje wzrost wydajności podczas odczytywania lub zapisywania plików na dysku. Poniższe ustawienia mogą służyć do kompresowania plików tymczasowych.

* `pig.tmpfilecompression`: W przypadku wartości true włącza kompresję plików tymczasowych. Wartość domyślna to false.

* `pig.tmpfilecompression.codec`: Koder-dekoder kompresji używany do kompresowania plików tymczasowych. Zalecane kodeki kompresji to LZO i przyciąganie w celu zmniejszenia użycia procesora CPU.

## <a name="enable-split-combining"></a>Włącz łączenie podzielone

Gdy ta funkcja jest włączona, małe pliki są łączone do mniejszej liczby zadań mapy. To ustawienie poprawia wydajność zadań z wieloma małymi plikami. Aby włączyć, ustaw `pig.noSplitCombination` wartość true. Wartość domyślna to false.

## <a name="tune-mappers"></a>Dostrajaj mapowania

Liczba odwzorowań jest kontrolowana przez modyfikację właściwości `pig.maxCombinedSplitSize` . Ta właściwość określa rozmiar danych, które mają być przetwarzane przez pojedyncze zadanie mapowania. Wartość domyślna to domyślny rozmiar bloku systemu plików. Zwiększenie tej wartości spowoduje zmniejszenie liczby zadań mapowania.

## <a name="tune-reducers"></a>Dostrajaj ograniczenia

Liczba elementów ograniczających jest obliczana na podstawie parametru `pig.exec.reducers.bytes.per.reducer` . Parametr określa liczbę bajtów przetworzonych na program, domyślnie 1 GB. Aby ograniczyć maksymalną liczbę elementów ograniczających, należy ustawić `pig.exec.reducers.max` Właściwość domyślnie 999.

## <a name="next-steps"></a>Następne kroki

* [Zarządzanie klastrami usługi HDInsight przy użyciu interfejsu użytkownika sieci Web Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [Interfejs API REST usługi Apache Ambari](hdinsight-hadoop-manage-ambari-rest-api.md)
* [Optymalizowanie klastrów](./hdinsight-changing-configs-via-ambari.md)
* [Optymalizowanie usługi Apache HBase](./optimize-hbase-ambari.md)
* [Optymalizowanie technologii Apache Hive](./optimize-hive-ambari.md)
