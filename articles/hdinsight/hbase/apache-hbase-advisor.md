---
title: Optymalizacja pod kątem zaleceń usługi Cluster Advisor
titleSuffix: Azure HDInsight
description: Zoptymalizuj rekomendacje Apache HBase na potrzeby usługi Azure HDInsight.
author: ramkrish86
ms.author: ramvasu
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/03/2021
ms.openlocfilehash: 73af7e2a1920e6cfdad9245d965908255ef95a1f
ms.sourcegitcommit: f6f928180504444470af713c32e7df667c17ac20
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/07/2021
ms.locfileid: "97964596"
---
# <a name="apache-hbase-advisories-in-azure-hdinsight"></a>Klasyfikatory Apache HBase w usłudze Azure HDInsight

W tym artykule opisano kilka klasyfikatorów, które ułatwiają optymalizację wydajności Apache HBase w usłudze Azure HDInsight. 

## <a name="optimize-hbase-to-read-most-recently-written-data"></a>Optymalizuj HBase, aby odczytywać ostatnio zapisywane dane

Jeśli UseCase obejmuje odczytywanie ostatnio pisanych danych z HBase, ten poradnik może Ci pomóc. W celu uzyskania wysokiej wydajności optymalne jest, że odczyty HBase są obsługiwane z magazynu, a nie z magazynu zdalnego.

Klasyfikator zapytania wskazuje, że dla danej rodziny kolumn w tabeli > odczyty 75%, które są obsługiwane przez magazynu. Ten wskaźnik sugeruje, że nawet jeśli opróżnianie występuje na magazynu, należy uzyskać dostęp do najnowszego pliku i musi on być w pamięci podręcznej. Dane są najpierw zapisywane, aby magazynu system uzyskuje dostęp do najnowszych danych w tym miejscu. Istnieje szansa, że wewnętrzne wątki HBase opróżniania wykryją, że dany region osiągnął rozmiar 128M (domyślny) i może wyzwolić opróżnianie. W tym scenariuszu występuje nawet najnowsze dane, które zostały zapisaną, gdy magazynu miało 128M rozmiar. W związku z tym później odczytywanie tych najnowszych rekordów może wymagać odczytu pliku, a nie z magazynu. W związku z tym najlepszym rozwiązaniem jest zoptymalizowanie, że mimo że ostatnio opróżnione dane mogą znajdować się w pamięci podręcznej.

Aby zoptymalizować ostatnie dane w pamięci podręcznej, należy wziąć pod uwagę następujące ustawienia konfiguracji:

1. Ustaw `hbase.rs.cacheblocksonwrite` wartość `true` . Ta domyślna konfiguracja w usłudze HDInsight HBase to `true` , więc sprawdź, czy nie jest resetowana do programu `false` .

2. Zwiększ `hbase.hstore.compactionThreshold` wartość, aby uniknąć rozpoczęcia kompaktowania w. Domyślna wartość to `3`. Można zwiększyć ją do wyższej wartości, takiej jak `10` .

3. Jeśli przeobserwujesz krok 2 i ustawisz compactionThreshold, następnie zmienisz `hbase.hstore.compaction.max` na przykład wyższą wartość `100` , a także zwiększy wartość konfiguracji `hbase.hstore.blockingStoreFiles` na przykład na wyższą wartość `300` .

4. Jeśli masz pewność, że musisz odczytać tylko najnowsze dane, ustaw opcję Konfiguracja na wartość `hbase.rs.cachecompactedblocksonwrite` **włączone**. Ta konfiguracja informuje system, że nawet w przypadku kompaktowania dane pozostają w pamięci podręcznej. Konfiguracje można ustawić na poziomie rodziny. 

   W powłoce HBase Uruchom następujące polecenie, aby ustawić `hbase.rs.cachecompactedblocksonwrite` konfigurację:
   
   ```
   alter '<TableName>', {NAME => '<FamilyName>', CONFIGURATION => {'hbase.hstore.blockingStoreFiles' => '300'}}
   ```

5. Pamięć podręczną bloków można wyłączyć dla danej rodziny w tabeli. Upewnij się, że jest **włączona dla rodzin** , które mają najnowsze operacje odczytu danych. Domyślnie pamięć podręczna bloków jest włączona dla wszystkich rodzin w tabeli. Jeśli pamięć podręczna bloków została wyłączona dla rodziny i musi ona zostać włączona, użyj polecenia ALTER z powłoki HBase.

   Te konfiguracje zapewniają, że dane są dostępne w pamięci podręcznej, a najnowsze dane nie są poddawane kompaktom. Jeśli w danym scenariuszu jest możliwy czas wygaśnięcia, rozważ użycie kompaktowania warstwowego. Aby uzyskać więcej informacji, zobacz temat [Przewodnik dotyczący programu Apache HBase: kompaktowanie warstwowe.](https://hbase.apache.org/book.html#ops.date.tiered)  

## <a name="optimize-the-flush-queue"></a>Optymalizowanie kolejki opróżniania

Ten poradnik wskazuje, że opróżnianie HBase może wymagać dostrajania. Bieżąca konfiguracja programów obsługi opróżniania może nie być wystarczająco wysoka, aby można było obsługiwać ruch związany z zapisem, co może prowadzić do spowolnienia operacji opróżniania.

W interfejsie użytkownika serwera regionu Zwróć uwagę, czy kolejka opróżniania przekracza 100. Ten próg wskazuje, że operacje opróżniania są wolne i konieczne może być dostosowanie   `hbase.hstore.flusher.count` konfiguracji. Domyślnie wartość jest równa 2. Upewnij się, że maksymalna liczba wątków opróżniania nie wzrasta dłużej niż 6.

Ponadto sprawdź, czy masz rekomendacje dotyczące dostrajania liczby regionów. Jeśli będziemy nam, zalecamy wypróbowanie dostrajania regionu, aby zobaczyć, czy ułatwia to szybsze opróżnianie. W przeciwnym razie dostrojenie wątków opróżniania może Ci pomóc.

## <a name="region-count-tuning"></a>Dostrajanie liczby regionów

Poradnik dostrajania liczby regionów wskazuje, że HBase ma zablokowane aktualizacje, a liczba regionów może być większa niż optymalnie obsługiwany rozmiar sterty. Można dostosować rozmiar sterty, rozmiar magazynu i liczbę regionów.

Przykładowy scenariusz:

- Załóżmy, że rozmiar sterty serwera regionu wynosi 10 GB. Domyślnie `hbase.hregion.memstore.flush.size` jest to `128M` . Wartość domyślna `hbase.regionserver.global.memstore.size` to `0.4` . Oznacza to, że z 10 GB zostanie przydzielono 4 GB do magazynu (globalnie).

- Załóżmy, że istnieje równomierny rozkład obciążenia zapisu we wszystkich regionach, przy założeniu, że każdy region powiększa się do 128 MB, a następnie Maksymalna liczba regionów w tej konfiguracji to `32` regiony. Jeśli dany serwer regionu jest skonfigurowany do 32 regionów, system lepiej unika blokowania aktualizacji.

- W przypadku wprowadzenia tych ustawień liczba regionów wynosi 100. Magazynu globalna 4 GB jest teraz dzielona w 100 regionach. Dlatego efektywnie każdy region otrzymuje tylko 40 MB dla magazynu. Gdy zapisy są jednorodne, system wykonuje częste opróżnianie i mniejszy rozmiar zamówienia < 40 MB. Wiele wątków opróżniania może zwiększyć szybkość opróżniania `hbase.hstore.flusher.count` .

Poradnik oznacza, że warto replikować liczbę regionów na serwer, rozmiar sterty i globalną konfigurację rozmiaru magazynu oraz dostrajać wątki opróżniania, aby uniknąć zablokowania aktualizacji.

## <a name="compaction-queue-tuning"></a>Dostrajanie kolejki kompaktowania

Jeśli kolejka kompaktowa HBase powiększa się do ponad 2000 i okresowo odbywa się, można zwiększyć liczbę wątków kompaktowania do większej wartości.

W przypadku zbyt dużej liczby plików do kompaktowania może to spowodować zwiększenie użycia sterty związanej z współdziałaniem plików z systemem plików platformy Azure. Dlatego lepiej jest dokończyć kompaktowanie tak szybko, jak to możliwe. Czasami w starszych klastrach konfiguracje kompaktowania związane z ograniczaniem wydajności mogą prowadzić do wolniejszej szybkości kompaktowania.

Sprawdź konfiguracje `hbase.hstore.compaction.throughput.lower.bound` i `hbase.hstore.compaction.throughput.higher.bound` . Jeśli są już ustawione na 50 mln i 100 mln, należy pozostawić je w takiej postaci. Jeśli jednak te ustawienia zostały skonfigurowane na niższą wartość (w przypadku starszych klastrów), zmień odpowiednio limity na 50 mln i 100 mln.

Konfiguracje są `hbase.regionserver.thread.compaction.small` i `hbase.regionserver.thread.compaction.large` (wartości domyślne to 1 Każdy).
Wartość maksymalna dla tej konfiguracji nie może być mniejsza niż 3.

## <a name="full-table-scan"></a>Pełne skanowanie tabeli

Poradnik pełnego skanowania tabeli wskazuje, że ponad 75% wystawionych skanów jest pełnych operacji skanowania tabeli/regionu. Możesz ponownie przejść do sposobu, w jaki kod wywołuje skanowania w celu zwiększenia wydajności zapytań. Należy wziąć pod uwagę następujące rozwiązania:

* Ustaw odpowiedni wiersz początkowy i Zatrzymaj dla każdego skanowania.

* Użyj interfejsu API **MultiRowRangeFilter** , aby można było wykonywać zapytania dotyczące różnych zakresów w jednym wywołaniu skanowania. Aby uzyskać więcej informacji, zobacz [dokumentację interfejsu API MultiRowRangeFilter](https://hbase.apache.org/2.1/apidocs/org/apache/hadoop/hbase/filter/MultiRowRangeFilter.html).

* W przypadkach, gdy wymagane jest pełne skanowanie tabeli lub regionu, sprawdź, czy istnieje możliwość uniknięcia użycia pamięci podręcznej dla tych zapytań, dzięki czemu inne zapytania, które używają pamięci podręcznej, mogą nie wykluczać bloków, które są gorącą. Aby upewnić się, że skanowania nie używają pamięci podręcznej, użyj interfejsu API **skanowania** z opcją setcache **(false)** w kodzie: 

   ```
   scan#setCaching(false)
   ```
   
## <a name="next-steps"></a>Następne kroki

[Optymalizowanie usługi Apache HBase przy użyciu usługi Ambari](../optimize-hbase-ambari.md)
