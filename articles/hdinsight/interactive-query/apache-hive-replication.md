---
title: Jak używać replikacji Apache Hive w klastrach usługi Azure HDInsight
description: Dowiedz się, jak za pomocą replikacji Hive w klastrach usługi HDInsight replikować magazyn metadanych Hive i Azure Data Lake Storage Gen 2 Data Lake.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/08/2020
ms.openlocfilehash: af74392b3368a25e5d238f774292c80de5f91c65
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91857745"
---
# <a name="how-to-use-apache-hive-replication-in-azure-hdinsight-clusters"></a>Jak używać replikacji Apache Hive w klastrach usługi Azure HDInsight

W kontekście baz danych i magazynów replikacja jest procesem duplikowania jednostek z jednego magazynu do innego. Duplikowanie może dotyczyć całej bazy danych lub mniejszego poziomu, takiego jak tabela lub partycja. Celem jest posiadanie repliki, która zmienia się za każdym razem, gdy zmienia się jednostka podstawowa. Replikacja na Apache Hive koncentruje się na odzyskiwaniu po awarii i oferuje jednokierunkową replikację z kopią podstawową. W klastrach usługi HDInsight replikacja Hive może być używana do jednokierunkowego replikowania magazyn metadanych Hive i skojarzonej z nią usługi Data Lake w Azure Data Lake Storage Gen2.  

Replikacja programu Hive została rozbudowana w latach z nowszymi wersjami zapewniającymi lepszą funkcjonalność i szybsze i mniej obciążające zasoby. W tym artykule omówiono replikację Hive (Replv2), która jest obsługiwana zarówno w przypadku klastrów HDInsight 3,6, jak i HDInsight 4,0.

## <a name="advantages-of-replv2"></a>Zalety Replv2

W przypadku korzystania z pierwszej wersji replikacji programu Hive [ReplicationV2](https://cwiki.apache.org/confluence/display/Hive/HiveReplicationv2Development) lub (Replv2) ma następujące [zalety:](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ImportExport)

- Replikacja przyrostowa oparta na zdarzeniach
- Replikacja do punktu w czasie  
- Wymagania dotyczące mniejszej przepustowości  
- Zmniejszenie liczby kopii pośrednich  
- Stan replikacji jest obsługiwany
- Replikacja ograniczona  
- Obsługa modelu gwiazdy i gwiazdy  
- Obsługa tabel KWASowych (w usłudze HDInsight 4,0)

## <a name="replication-phases"></a>Fazy replikacji

Replikacja oparta na zdarzeniach Hive jest konfigurowana między klastrem podstawowym i pomocniczym. Ta replikacja obejmuje dwie odrębne fazy: uruchamianie i przyrostowe uruchomienia.

### <a name="bootstrapping"></a>Uruchamianie

Uruchamianie jest przeznaczone do uruchomienia jednokrotnego, aby replikować podstawowy stan baz danych z podstawowego do pomocniczego. Możesz skonfigurować uruchamianie, w razie potrzeby, aby uwzględnić podzbiór tabel w dostosowanej bazie danych, w której należy włączyć replikację.

### <a name="incremental-runs"></a>Uruchomienia przyrostowe

Po uruchomieniu programu przyrostowe uruchomienia są zautomatyzowane w klastrze głównym, a zdarzenia generowane podczas tych przyrostowych uruchomień są odtwarzane w klastrze pomocniczym. Gdy klaster pomocniczy przechwytuje do klastra podstawowego, pomocniczy jest spójny ze zdarzeniami podstawowymi.

## <a name="replication-commands"></a>Polecenia replikacji

Program Hive oferuje zestaw poleceń REPL — `DUMP` , `LOAD` i `STATUS` — do organizowania przepływu zdarzeń. `DUMP`Polecenie generuje dziennik lokalny wszystkich zdarzeń DDL/DML w klastrze podstawowym. `LOAD`Polecenie jest podejściem do opóźnieniem kopiowania metadanych i danych zarejestrowanych w wydzielonym zrzucie replikacji i jest wykonywane w klastrze docelowym. `STATUS`Polecenie jest uruchamiane z klastra docelowego w celu zapewnienia najnowszego identyfikatora zdarzenia, że ostatnie ładowanie replikacji zostało pomyślnie zreplikowane.

### <a name="set-replication-source"></a>Ustaw Źródło replikacji

Przed rozpoczęciem pracy z replikacją upewnij się, że baza danych, która ma zostać zreplikowana, została ustawiona jako źródło replikacji. Możesz użyć polecenia, `DESC DATABASE EXTENDED <db_name>` Aby określić, czy parametr `repl.source.for` jest ustawiony z nazwą zasad.

Jeśli zasady są zaplanowane i `repl.source.for` parametr nie jest ustawiony, należy najpierw ustawić ten parametr przy użyciu polecenia `ALTER DATABASE <db_name> SET DBPROPERTIES ('repl.source.for'='<policy_name>')` .

```sql
ALTER DATABASE tpcds_orc SET DBPROPERTIES ('repl.source.for'='replpolicy1') 
```

### <a name="dump-metadata-to-the-data-lake"></a>Zrzuć metadane w usłudze Data Lake

`REPL DUMP [database name]. => location / event_id`Polecenie jest używane w fazie ładowania początkowego do zrzutu odpowiednich metadanych do Azure Data Lake Storage Gen2. `event_id`Określa minimalne zdarzenie, do którego zostały umieszczone odpowiednie metadane Azure Data Lake Storage Gen2. 
 
```sql
repl dump tpcds_orc; 
```
Przykładowe dane wyjściowe:

| dump_dir|last_repl_id
|-|-|
|/tmp/hive/repl/38896729-67d5-41b2-90dc-46eeed4c5dd0|2925|

### <a name="load-data-to-the-target-cluster"></a>Ładowanie danych do klastra docelowego

`REPL LOAD [database name] FROM [ location ] { WITH ( ‘key1’=‘value1’{, ‘key2’=‘value2’} ) }`Polecenie służy do ładowania danych do klastra docelowego zarówno dla ładowania początkowego, jak i przyrostowych faz replikacji. `[database name]`Może być taka sama jak źródłowa lub inna nazwa w klastrze docelowym. `[location]`Reprezentuje lokalizację z danych wyjściowych wcześniejszego `REPL DUMP` polecenia. Oznacza to, że klaster docelowy powinien być w stanie komunikować się z klastrem źródłowym. `WITH`Klauzula została przede wszystkim dodana, aby zapobiec ponownemu uruchomieniu klastra docelowego, co umożliwia replikację.

```sql
repl load tpcds_orc from '/tmp/hive/repl/38896729-67d5-41b2-90dc-46eeed4c5dd0'; 
```

### <a name="output-the-last-replicated-event-id"></a>Wyprowadzanie ostatniego zreplikowanego zdarzenia o IDENTYFIKATORze

`REPL STATUS [database name]`Polecenie jest wykonywane w klastrach docelowych i wyprowadza ostatnią replikację `event_id` . To polecenie umożliwia również użytkownikom dowiedzieć się, do jakiego stanu jest replikowana klaster docelowy. Możesz użyć danych wyjściowych tego polecenia, aby utworzyć następne `REPL DUMP` polecenie dla replikacji przyrostowej.

```sql
repl status tpcds_orc;
```

Przykładowe dane wyjściowe:

|last_repl_id|
|-|
|2925|

### <a name="dump-relevant-data-and-metadata-to-the-data-lake"></a>Zrzuć dane i metadane dotyczące danych w usłudze Data Lake

`REPL DUMP [database name] FROM [event-id] { TO [event-id] } { LIMIT [number of events] }`Polecenie służy do zrzutu odpowiednich metadanych i danych w celu Azure Data Lake Storage. To polecenie jest używane w fazie przyrostowej i jest uruchamiane w magazynie źródłowym. `FROM [event-id]`Jest wymagana dla fazy przyrostowej, a wartość `event-id` może być pochodna, uruchamiając `REPL STATUS [database name]` polecenie w hurtowni docelowej.

```sql
repl dump tpcds_orc from 2925;
```

Przykładowe dane wyjściowe:

|dump_dir|last_repl_id|
|-|-|
| /tmp/hive/repl/38896729-67d5-41b2-90dc-466466agadd0 | 2960|

## <a name="hive-replication-process"></a>Proces replikacji Hive

Poniższe kroki to sekwencyjne zdarzenia, które odbywają się podczas procesu replikacji programu Hive.

1. Upewnij się, że zreplikowane tabele są ustawione jako źródło replikacji dla określonych zasad.

1. `REPL_DUMP`Polecenie zostało wystawione dla klastra podstawowego z skojarzonymi ograniczeniami, takimi jak nazwa bazy danych, zakres identyfikatorów zdarzeń i adres URL magazynu Azure Data Lake Storage Gen2.

1. System serializacji zrzutu wszystkich śledzonych zdarzeń z magazynu metadanych do najnowszego. Ten zrzut jest przechowywany na koncie magazynu Azure Data Lake Storage Gen2 w klastrze podstawowym przy użyciu adresu URL określonego przez `REPL_DUMP` .  

1. Klaster podstawowy utrwala metadane replikacji do magazynu Azure Data Lake Storage Gen2 klastra podstawowego. Ścieżka można skonfigurować w interfejsie użytkownika konfiguracji programu Hive w Ambari. Proces zawiera ścieżkę, w której przechowywane są metadane, oraz identyfikator ostatniego śledzonego zdarzenia DML/DDL.

1. `REPL_LOAD`Polecenie jest wydawane z klastra pomocniczego. Polecenie wskazuje ścieżkę skonfigurowaną w kroku 3.

1. Klaster pomocniczy odczytuje plik metadanych z śledzonymi zdarzeniami, które zostały utworzone w kroku 3. Upewnij się, że klaster pomocniczy ma łączność sieciową z Azure Data Lake Storage Gen2 magazynem podstawowym klastra, w którym są przechowywane śledzone zdarzenia `REPL_DUMP` .  

1. Klaster pomocniczy powoduje utworzenie wystąpienia obliczeniowego kopiowania rozproszonego ( `DistCP` ).

1. Klaster pomocniczy kopiuje dane z magazynu podstawowego klastra.  

1. Magazyn metadanych pomocniczych został zaktualizowany.  

1. Identyfikator ostatniego śledzonego zdarzenia jest przechowywany w podstawowym magazynie metadanych.

Replikacja przyrostowa jest zgodna z tym samym procesem i wymaga ostatniego zreplikowanego identyfikatora zdarzenia jako dane wejściowe. Prowadzi to do kopii przyrostowej od momentu ostatniego zdarzenia replikacji. Replikacje przyrostowe są zwykle zautomatyzowane przy użyciu wstępnie ustalonej częstotliwości, aby osiągnąć wymagane cele punktu odzyskiwania (RPO).

:::image type="content" source="media/apache-hive-replication/hive-replication-diagram.png" alt-text="Diagram replikacji programu Hive":::

## <a name="replication-patterns"></a>Wzorce replikacji  

Replikacja jest zwykle konfigurowana jednokierunkowo między głównym i pomocniczym, gdzie podstawowym celem jest żądanie odczytu i zapisu. Klaster pomocniczy ma tylko żądania odczytu. Jeśli wystąpi awaria, operacje zapisu są dozwolone na serwerze pomocniczym, ale replikacji odwrotnej należy skonfigurować z powrotem do podstawowego.

:::image type="content" source="media/apache-hive-replication/replication-pattern.png" alt-text="Diagram replikacji programu Hive":::

Istnieje wiele wzorców, które są odpowiednie dla replikacji Hive, w tym podstawowe — pomocnicze, Hub i Relay.

W usłudze HDInsight Active Primary — pomocnicza usługa w trybie rezerwy jest powszechnym wzorcem ciągłości działania i odzyskiwania po awarii (BCDR), a HiveReplicationV2 może używać tego wzorca z rozdzielonymi w regionach klastrami Hadoop usługi HDInsight z równorzędnymi sieciami wirtualnymi. Wspólna maszyna wirtualna w obu klastrach może służyć do hostowania skryptów automatyzacji replikacji. Aby uzyskać więcej informacji na temat możliwych wzorców BCDR usługi HDInsight, zobacz [dokumentację dotyczącą ciągłości biznesowej usługi HDInsight](../hdinsight-business-continuity.md).  

### <a name="hive-replication-with-enterprise-security-package"></a>Replikacja Hive przy użyciu pakiet Enterprise Security  

W przypadkach, gdy replikacja Hive jest planowana w klastrach usługi HDInsight Hadoop z pakiet Enterprise Security, należy wziąć pod uwagę mechanizmy replikacji dla magazynu metadanych Ranger i Azure Active Directory Domain Services (AD DS).  

Użyj funkcji zestawy replik AD DS platformy Azure, aby utworzyć więcej niż jeden zestaw Azure AD DS Replica dla dzierżawy usługi Azure AD w wielu regionach. Poszczególne zestawy replik muszą być połączone za pomocą komunikacji równorzędnej z sieci wirtualnych usługi HDInsight w odpowiednich regionach. W tej konfiguracji zmiany w usłudze Azure AD DS, w tym konfiguracja, tożsamość użytkownika i poświadczenia, grupy, obiekty zasad grupy, obiekty komputerów i inne zmiany są stosowane do wszystkich zestawów replik w domenie zarządzanej przy użyciu usługi Azure AD DS Replication.
  
Zasady Ranger mogą być okresowo tworzone i replikowane z poziomu podstawowego do pomocniczego przy użyciu funkcji Ranger Import-Export. Możesz zdecydować się na replikację wszystkich lub podzbioru zasad Ranger w zależności od poziomu autoryzacji, które zamierzasz wdrożyć w klastrze pomocniczym.  

## <a name="sample-code"></a>Przykładowy kod  

Poniższa sekwencja kodu zawiera przykład, jak można zaimplementować uruchamianie i replikację przyrostową w przykładowej tabeli o nazwie `tpcds_orc` .  

1. Ustaw tabelę jako źródło dla zasad replikacji.

   ```sql
   ALTER DATABASE tpcds_orc SET DBPROPERTIES ('repl.source.   for'='replpolicy1');
   ```

1. Zrzut ładowania początkowego w klastrze podstawowym.

   ```sql
   repl dump tpcds_orc with ('hive.repl.rootdir'='/tmpag/hiveag/replag'); 
   ```
   
   Przykładowe dane wyjściowe:
   
   |dump_dir|last_repl_id|
   |-|-|
   |/tmpag/hiveag/replag/675d1bea-2361-4cad-bcbf-8680d305a27a|2925|
 
1. Ładowanie ładowania początkowego w klastrze pomocniczym. 

   ```sql
   repl load tpcds_orc from '/tmpag/hiveag/replag 675d1bea-2361-4cad-bcbf-8680d305a27a'; 
   ```

1. Sprawdź `REPL` stan w klastrze pomocniczym.

   ```sql
   repl status tpcds_orc; 
   ```
 
   |last_repl_id|
   |-|
   |2925|

1. Przyrostowy zrzut w klastrze podstawowym.

   ```sql
   repl dump tpcds_orc from 2925 with ('hive.repl.rootdir'='/tmpag/hiveag/ replag');
   ```

   Przykładowe dane wyjściowe:
   
   |dump_dir | last_repl_id|
   |-|-|
   |/tmpag/hiveag/replag/31177ff7-a40f-4f67-a613-3b64ebe3bb31|2960|

1. Przyrostowe ładowanie w klastrze pomocniczym.  

   ```sql
   repl load tpcds_orc from '/tmpag/hiveag/replag/31177ff7-a40f-4f67-a613-3b64ebe3bb31';
   ```

1. Sprawdź `REPL` stan w klastrze pomocniczym.

   ```sql
   repl status tpcds_orc;
   ```

   |last_repl_id|
   |-|
   |2960|

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat elementów omówionych w tym artykule, zobacz:

- [Ciągłość biznesowa usługi Azure HDInsight](../hdinsight-business-continuity.md)
- [Architektury ciągłości biznesowej usługi Azure HDInsight](../hdinsight-business-continuity-architecture.md)
- [Analiza przypadku architektury rozwiązania o wysokiej dostępności usługi Azure HDInsight](../hdinsight-high-availability-case-study.md)
- [Co to jest Apache Hive i HiveQL w usłudze Azure HDInsight?](../hadoop/hdinsight-use-hive.md)