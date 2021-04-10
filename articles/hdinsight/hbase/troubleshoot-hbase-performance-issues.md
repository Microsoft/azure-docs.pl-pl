---
title: Rozwiązywanie problemów z wydajnością oprogramowania Apache HBase w usłudze Azure HDInsight
description: Różne wskazówki i wskazówki dotyczące dostrajania wydajności usługi Apache HBase w celu uzyskania optymalnej wydajności w usłudze Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 09/24/2019
ms.openlocfilehash: 466fac524601e2d569bfa0ccf90179fe9419210d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98942887"
---
# <a name="troubleshoot-apache-hbase-performance-issues-on-azure-hdinsight"></a>Rozwiązywanie problemów z wydajnością oprogramowania Apache HBase w usłudze Azure HDInsight

W tym artykule opisano różne wskazówki i wskazówki dotyczące dostrajania wydajności usługi Apache HBase w celu uzyskania optymalnej wydajności w usłudze Azure HDInsight. Wiele z tych wskazówek zależą od określonego obciążenia i wzorca odczytu/zapisu/skanowania. Przed zastosowaniem zmian w konfiguracji w środowisku produkcyjnym należy je dokładnie przetestować.

## <a name="hbase-performance-insights"></a>Szczegółowe informacje o wydajności HBase

Największe wąskie gardło w większości obciążeń HBase to zapis z wyprzedzeniem (WAL). Znacznie wpływa na wydajność zapisu. Usługa HDInsight HBase ma rozdzielony model obliczeniowy magazynu. Dane są przechowywane zdalnie w usłudze Azure Storage, mimo że maszyny wirtualne hostuje serwery regionów. Do ostatniego zapisywana była również usługa Azure Storage. W usłudze HDInsight to zachowanie wzmacnia ten wąskie gardło. Funkcja [szybsze zapisy](./apache-hbase-accelerated-writes.md) została zaprojektowana w celu rozwiązania tego problemu. Zapisuje on dane w WAL na dyskach zarządzanych przez usługę Azure SSD w warstwie Premium. Ta nieznacznie zaletuje wydajność zapisu i ułatwia wiele problemów spowodowanych przez niektóre obciążenia intensywnie korzystające z pisania.

Aby uzyskać znaczące ulepszenia operacji odczytu, użyj [konta blokowego Premium BLOB Storage](https://azure.microsoft.com/blog/azure-premium-block-blob-storage-is-now-generally-available/) jako magazynu zdalnego. Ta opcja jest możliwa tylko wtedy, gdy funkcja WAL jest włączona.

## <a name="compaction"></a>Kompaktowania

Kompaktowanie to kolejne potencjalne wąskie gardło, które jest zasadniczo uzgadniane w społeczności. Domyślnie kompaktowanie główne jest wyłączone w klastrach usługi HDInsight HBase. Kompaktowanie jest wyłączone, ponieważ chociaż jest to proces intensywnie obciążający zasoby, klienci mają pełną elastyczność, aby zaplanować ją zgodnie z ich obciążeniami. Na przykład mogą zaplanować je w godzinach poza godzinami szczytu. Ponadto nie jest to problem, ponieważ nasz magazyn jest zdalny (za pomocą usługi Azure Storage), a nie do lokalnego rozproszony system plików Hadoop (HDFS).

Klienci powinni zaplanować główne kompaktowanie w ich wygodie. Jeśli nie wykonują tej konserwacji, kompaktowanie będzie niekorzystnie wpłynąć na wydajność odczytu w długim przebiegu.

W przypadku operacji skanowania średnie opóźnienia, które są znacznie większe niż 100 milisekund, powinny być przyczyną problemu. Sprawdź, czy Poprzednia wersja kompaktowania została dokładnie zaplanowana.

## <a name="apache-phoenix-workload"></a>Obciążenie Apache Phoenix

Udzielenie odpowiedzi na następujące pytania pomoże Ci lepiej zrozumieć Apache Phoenix obciążenie:

* Czy wszystkie Twoje "odczyty" są używane do skanowania?
    * Jeśli tak, jakie są cechy tych skanów?
    * Czy schemat tabeli platformy Phoenix został zoptymalizowany dla tych skanów, w tym do odpowiedniego indeksowania?
* Czy użyto instrukcji, `EXPLAIN` Aby zrozumieć plany zapytania dotyczące generowania "operacji odczytu"?
* Czy Twoje zapisy to "upsert-Select"?
    * Jeśli tak, również będą wykonywały skanowanie. Oczekiwane opóźnienie skanowania orednie około 100 milisekund, w porównaniu do 10 milisekund dla punktu, w HBase.  

## <a name="test-methodology-and-metrics-monitoring"></a>Metodologia testowania i monitorowanie metryk

Jeśli używasz testów porównawczych, takich jak Yahoo! Chmura obsługująca testy porównawcze, JMeter lub Pherf do testowania i dostrajania wydajności, należy upewnić się, że:

- Komputery klienckie nie stają się wąskim gardłem. W tym celu należy sprawdzić użycie procesora CPU na komputerach klienckich.

- Konfiguracje po stronie klienta, takie jak liczba wątków, są odpowiednio dopasowane, aby zwiększyć nasycenie przepustowości klienta.

- Wyniki testu są rejestrowane dokładnie i systematycznie.

Jeśli zapytania nagle rozpoczęły działanie znacznie gorszy niż wcześniej, sprawdź, czy w kodzie aplikacji są możliwe usterki. Czy nagle generuje duże ilości nieprawidłowych danych? Jeśli tak, może to wydłużyć opóźnienia odczytu.

## <a name="migration-issues"></a>Problemy z migracją

W przypadku migrowania do usługi Azure HDInsight upewnij się, że migracja jest przeprowadzana systematycznie i dokładnie, najlepiej za pośrednictwem automatyzacji. Unikaj ręcznej migracji. Upewnij się, że:

- Atrybuty tabeli są migrowane dokładnie. Atrybuty mogą obejmować kompresję, filtry rozwinięte i tak dalej.

- Ustawienia Sole w tabelach w Phoenix są mapowane odpowiednio do nowego rozmiaru klastra. Na przykład liczba zasobników soli powinna być wielokrotnością liczby węzłów procesu roboczego w klastrze. I należy użyć wielu, która jest czynnikiem ilości gorącej wykrywania trendów.

## <a name="server-side-configuration-tunings"></a>Dostrajania konfiguracji po stronie serwera

W usłudze HDInsight HBase HFiles są przechowywane w magazynie zdalnym. W przypadku braku miejsca w pamięci podręcznej koszt odczytu jest wyższy niż w przypadku systemów lokalnych, ponieważ dane w systemach lokalnych są obsługiwane przez lokalny system plików HDFS. W przypadku większości scenariuszy inteligentne użycie pamięci podręcznej HBase (pamięć podręczna bloków i pamięć podręczna zasobników) zostało zaprojektowane w celu obejścia tego problemu. W przypadkach, gdy problem nie jest omijany, użycie konta blokowego obiektu BLOB w warstwie Premium może pomóc w tym problemie. Sterownik Azure Storage Blob systemu Windows opiera się na pewnych właściwościach, takich jak `fs.azure.read.request.size` pobieranie danych w blokach na podstawie tego, co jest określane jako tryb odczytu (sekwencyjne i losowe), dzięki czemu mogą być nadal wystąpienia większej liczby opóźnień z odczytami. W wyniku eksperymentów doświadczalnych znaleźliśmy, że ustawienie rozmiaru bloku żądania odczytu ( `fs.azure.read.request.size` ) na 512 KB i dopasowanie rozmiaru bloku tabel HBase do tego samego rozmiaru daje najlepszy wynik.

W przypadku większości klastrów węzłów o dużej wielkości Usługa HDInsight HBase udostępnia `bucketcache` jako plik w lokalnym SSD w warstwie Premium, który jest podłączony do maszyny wirtualnej, która jest uruchamiana `regionservers` . Użycie pamięci podręcznej poza stertą może zapewnić pewne udoskonalenia. To obejście ma ograniczenie wykorzystania dostępnej pamięci i potencjalnie mniejszej niż pamięć podręczna oparta na plikach, dzięki czemu może nie zawsze być najlepszym wyborem.

Poniżej przedstawiono niektóre z innych określonych parametrów, które zostały dostrojone i które były pomocne w różnych stopniach:

- Zwiększ `memstore` rozmiar z domyślnych 128 MB do 256 MB. Zwykle to ustawienie jest zalecane w przypadku dużych scenariuszy zapisu.

- Zwiększ liczbę wątków, które są dedykowane dla kompaktowania, z domyślnego ustawienia od **1** do **4**. To ustawienie ma zastosowanie, jeśli obserwujemy często drobne kompakty.

- Unikaj blokowania `memstore` opróżniania z powodu limitu magazynu. Aby podać ten bufor, zwiększ wartość `Hbase.hstore.blockingStoreFiles` Ustawienia na **100**.

- Aby kontrolować opróżnianie, użyj następujących ustawień:

    - `Hbase.regionserver.maxlogs`: **140** (unika opróżniania z powodu limitów Wal)

    - `Hbase.regionserver.global.memstore.lowerLimit`: **0,55**

    - `Hbase.regionserver.global.memstore.upperLimit`: **0,60**

- Konfiguracje specyficzne dla platformy Phoenix dla dostrajania puli wątków:

    - `Phoenix.query.queuesize`: **10000**

    - `Phoenix.query.threadpoolsize`: **512**

- Inne konfiguracje specyficzne dla Phoenix:

    - `Phoenix.rpc.index.handler.count`: **50** (jeśli istnieją duże lub liczne wyszukiwania indeksów)

    - `Phoenix.stats.updateFrequency`: **1 godzina**

    - `Phoenix.coprocessor.maxmetadatacachetimetolivems`: **1 godzina**

    - `Phoenix.coprocessor.maxmetadatacachesize`: **50 MB**

- Limity czasu wywołań RPC: **3 minuty**

   - Limity czasu wywołań RPC obejmują limit czasu usługi HBase RPC, limit czasu skanera klienta HBase i limit czasu zapytania w Phoenix. 
   - Upewnij się, że `hbase.client.scanner.caching` parametr jest ustawiony na tę samą wartość zarówno na końcu serwera, jak i na końcu klienta. Jeśli nie są one takie same, to ustawienie prowadzi do błędów klienta, które są powiązane z `OutOfOrderScannerException` . Dla tego ustawienia należy ustawić niską wartość dla dużych skanów. Ta wartość jest ustawiana na **100**.

## <a name="other-considerations"></a>Inne zagadnienia

Poniżej znajdują się dodatkowe parametry, które należy wziąć pod uwagę:

- `Hbase.rs.cacheblocksonwrite` — Domyślnie w HDI, to ustawienie ma **wartość true**.

- Ustawienia, które zezwalają na odroczenie mniejszych kompaktowania w przyszłości.

- Ustawienia eksperymentalne, takie jak dopasowanie wartości procentowej kolejek, które są zarezerwowane dla żądań odczytu i zapisu.

## <a name="next-steps"></a>Następne kroki

Jeśli problem nie zostanie rozwiązany, odwiedź jeden z następujących kanałów, aby uzyskać dodatkowe wsparcie:

- Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej dla społeczności platformy Azure](https://azure.microsoft.com/support/community/).

- Połącz się z [@AzureSupport](https://twitter.com/azuresupport) . Jest to oficjalne konto Microsoft Azure na potrzeby ulepszania środowiska klienta. Łączy społeczność platformy Azure z właściwymi zasobami: odpowiedziami, pomocą techniczną i ekspertami.

- Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy technicznej z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na pasku menu wybierz pozycję **Obsługa** , a następnie otwórz Centrum **pomocy i obsługi technicznej** . Aby uzyskać szczegółowe informacje, zapoznaj [się z tematem jak utworzyć żądanie pomocy technicznej platformy Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Twoja subskrypcja Microsoft Azure obejmuje dostęp do zarządzania subskrypcjami i rozliczeń, a pomoc techniczna jest świadczona za pomocą jednego z [planów pomocy technicznej systemu Azure](https://azure.microsoft.com/support/plans/).