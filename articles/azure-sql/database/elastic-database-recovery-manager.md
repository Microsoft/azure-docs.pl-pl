---
title: Recovery Manager rozwiązać problemy dotyczące mapy fragmentu
description: Używanie klasy Recoverymanager do rozwiązywania problemów z mapami fragmentu
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/03/2019
ms.openlocfilehash: fdd5f7d291d9c56361c17547628795b378091109
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91443439"
---
# <a name="using-the-recoverymanager-class-to-fix-shard-map-problems"></a>Używanie klasy RecoveryManager do rozwiązywanie problemów z mapą fragmentów
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Klasa [recoverymanager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager) udostępnia aplikacje ADO.NET, które ułatwiają wykrywanie i poprawianie wszelkich niespójności między globalnym mapowaniem FRAGMENTU (GSM) a lokalną mapą FRAGMENTU (LSM) w środowisku bazy danych podzielonej na fragmenty.

Metody GSM i LSM śledzą mapowanie każdej bazy danych w środowisku podzielonej na fragmentyu. Sporadycznie występuje przerwy między GSM i LSM. W takim przypadku należy użyć klasy Recoverymanager do wykrycia i naprawy przerwy.

Klasa Recoverymanager jest częścią [Elastic Databaseej biblioteki klienta](elastic-database-client-library.md).

![Mapa fragmentu][1]

Aby zapoznać się z definicjami warunków, zobacz [słownik narzędzi Elastic Database](elastic-scale-glossary.md). Aby zrozumieć, w jaki sposób **ShardMapManager** jest używany do zarządzania danymi w rozwiązaniu podzielonej na fragmenty, zobacz [Zarządzanie mapami fragmentu](elastic-scale-shard-map-management.md).

## <a name="why-use-the-recovery-manager"></a>Dlaczego warto korzystać z Menedżera odzyskiwania

W środowisku bazy danych podzielonej na fragmenty istnieje jedna dzierżawa na bazę danych, a wiele baz danych na serwer. W środowisku może być również wiele serwerów. Każda baza danych jest mapowana na mapie fragmentu, więc wywołania można kierować do właściwego serwera i bazy danych. Bazy danych są śledzone według **klucza fragmentowania**, a każdy fragmentu ma przypisany **zakres wartości klucza**. Na przykład klucz fragmentowania może reprezentować nazwy klientów od "D" do "F". Mapowanie wszystkich fragmentów (nazywanych również bazami danych) i ich zakresy mapowania są zawarte w **globalnej mapie fragmentu (GSM)**. Każda baza danych zawiera również mapę zakresów zawartych w fragmentu, która jest znana jako **lokalna Mapa fragmentu (LSM)**. Gdy aplikacja nawiązuje połączenie z fragmentu, mapowanie jest buforowane z aplikacją do szybkiego pobierania. LSM jest używany do walidacji danych w pamięci podręcznej.

Usługi GSM i LSM mogą stać się niezsynchronizowane z następujących powodów:

1. Usunięcie elementu fragmentu, którego zakres nie jest już używany, lub zmiana nazwy fragmentu. Usunięcie fragmentu skutkuje **mapowaniem oddzielonym fragmentu**. Podobnie baza danych o zmienionej nazwie może spowodować oddzielenie mapowania fragmentu. W zależności od intencji zmiany fragmentu może być konieczne usunięcie lub należy zaktualizować lokalizację fragmentu. Aby odzyskać usuniętą bazę danych, zobacz [przywracanie usuniętej bazy danych](recovery-using-backups.md).
2. Wystąpi zdarzenie geograficzne — tryb failover. Aby kontynuować, należy zaktualizować nazwę serwera i nazwę bazy danych Menedżera mapy fragmentu w aplikacji, a następnie zaktualizować szczegóły mapowania fragmentu dla wszystkich fragmentów na mapie fragmentu. W przypadku geograficznego trybu failover taka logika odzyskiwania powinna być zautomatyzowana w ramach przepływu pracy trybu failover. Automatyzacja akcji odzyskiwania umożliwia łatwość zarządzania bazami danych z obsługą geograficzną i unika ręcznych działań ludzkich. Aby dowiedzieć się więcej o opcjach odzyskiwania bazy danych w przypadku awarii centrum danych, zobacz [ciągłość](business-continuity-high-availability-disaster-recover-hadr-overview.md) działania i [odzyskiwanie po awarii](disaster-recovery-guidance.md).
3. Baza danych fragmentu lub ShardMapManager jest przywracana do wcześniejszego punktu w czasie. Aby dowiedzieć się więcej o odzyskiwaniu do punktu w czasie za pomocą kopii zapasowych, zobacz [odzyskiwanie przy użyciu kopii zapas](recovery-using-backups.md)

Aby uzyskać więcej informacji na temat Azure SQL Database narzędzi Elastic Database, replikacji geograficznej i przywracania, zobacz następujące tematy:

* [Przegląd: Ciągłość biznesowa w chmurze i odzyskiwanie po awarii bazy danych za pomocą SQL Database](business-continuity-high-availability-disaster-recover-hadr-overview.md)
* [Wprowadzenie do narzędzi elastycznych baz danych](elastic-scale-get-started.md)  
* [ShardMap Management](elastic-scale-shard-map-management.md)

## <a name="retrieving-recoverymanager-from-a-shardmapmanager"></a>Pobieranie elementu Recoverymanager z ShardMapManager

Pierwszym krokiem jest utworzenie wystąpienia programu Recoverymanager. [Metoda Getrecoverymanager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getrecoverymanager) zwraca Menedżera odzyskiwania dla bieżącego wystąpienia [ShardMapManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager) . Aby rozwiązać wszelkie niespójności w mapie fragmentu, należy najpierw pobrać program Recoverymanager dla konkretnej mapy fragmentu.

   ```java
    ShardMapManager smm = ShardMapManagerFactory.GetSqlShardMapManager(smmConnectionString,  
             ShardMapManagerLoadPolicy.Lazy);
             RecoveryManager rm = smm.GetRecoveryManager();
   ```

W tym przykładzie zostanie zainicjowany program Recoverymanager z ShardMapManager. ShardMapManager zawierający ShardMap jest również już zainicjowany.

Ponieważ ten kod aplikacji manipuluje mapą fragmentu, poświadczenia używane w metodzie fabryki (w poprzednim przykładzie, smmConnectionString) powinny mieć poświadczenia, które mają uprawnienia do odczytu i zapisu w bazie danych GSM, do których odwołuje się ciąg połączenia. Te poświadczenia zwykle różnią się od poświadczeń używanych do otwierania połączeń dla routingu zależnego od danych. Aby uzyskać więcej informacji, zobacz [używanie poświadczeń w kliencie Elastic Database](elastic-scale-manage-credentials.md).

## <a name="removing-a-shard-from-the-shardmap-after-a-shard-is-deleted"></a>Usuwanie elementu fragmentu z ShardMap po usunięciu fragmentu

[Metoda DetachShard](https://docs.microsoft.com/previous-versions/azure/dn842083(v=azure.100)) odłącza dany fragmentu z mapy fragmentu i usuwa mapowania skojarzone z fragmentu.  

* Parametr Location jest lokalizacją fragmentu, w odróżnieniu od nazwy serwera i nazwy bazy danych fragmentu.
* Parametr shardMapName jest nazwą mapy fragmentu. Jest to wymagane tylko wtedy, gdy wiele map fragmentu jest zarządzanych przez ten sam Menedżer mapy fragmentu. Opcjonalny.

> [!IMPORTANT]
> Tej techniki należy używać tylko wtedy, gdy masz pewność, że zakres zaktualizowanego mapowania jest pusty. Powyższe metody nie sprawdzają danych dla przenoszonego zakresu, dlatego najlepiej dołączać czeki w kodzie.

Ten przykład usuwa fragmentów z mapy fragmentu.

   ```java
   rm.DetachShard(s.Location, customerMap);
   ```

Mapa fragmentu odzwierciedla lokalizację fragmentu w GSM przed usunięciem fragmentu. Ponieważ fragmentu został usunięty, zakłada się, że jest to zamierzone, a zakres kluczy fragmentowania nie jest już używany. W przeciwnym razie można wykonać przywracanie do punktu w czasie. Aby odzyskać fragmentu z wcześniejszego punktu w czasie. (W tym przypadku zapoznaj się z poniższą sekcją w celu wykrycia niespójności fragmentu). Aby odzyskać, zobacz [odzyskiwanie do punktu w czasie](recovery-using-backups.md).

Ponieważ przyjęto założenie, że usunięcie bazy danych zostało zamierzone, końcowa Akcja oczyszczania administracyjnego polega na usunięciu wpisu do fragmentu w Menedżerze fragmentu map. Zapobiega to przypadkowemu zapisywaniu informacji przez aplikację do zakresu, który nie jest oczekiwany.

## <a name="to-detect-mapping-differences"></a>Aby wykryć różnice mapowania

[Metoda DetectMappingDifferences](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.detectmappingdifferences) wybiera i zwraca jeden z map fragmentu (lokalne lub globalne) jako źródło prawdy i uzgadnia mapowania na obu mapach FRAGMENTU (GSM i LSM).

   ```java
   rm.DetectMappingDifferences(location, shardMapName);
   ```

* *Lokalizacja* określa nazwę serwera i nazwę bazy danych.
* Parametr *shardMapName* jest nazwą mapy fragmentu. Jest to wymagane tylko wtedy, gdy wiele map fragmentu jest zarządzanych przez ten sam Menedżer mapy fragmentu. Opcjonalny.

## <a name="to-resolve-mapping-differences"></a>Aby rozwiązać różnice dotyczące mapowania

[Metoda ResolveMappingDifferences](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.resolvemappingdifferences) wybiera jeden z map fragmentu (lokalne lub globalne) jako źródło prawdy i uzgadnia mapowania na obu mapach FRAGMENTU (GSM i LSM).

   ```java
   ResolveMappingDifferences (RecoveryToken, MappingDifferenceResolution.KeepShardMapping);
   ```

* Parametr *RecoveryToken* wylicza różnice w MAPOWANIU między GSM i LSM dla określonego fragmentu.
* [Wyliczenie MappingDifferenceResolution](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.mappingdifferenceresolution) jest używane do wskazywania metody rozpoznawania różnicy między mapowaniami fragmentu.
* **MappingDifferenceResolution. KeepShardMapping** zaleca się, aby gdy LSM zawiera dokładne mapowanie i dlatego należy użyć mapowania w fragmentu. Zwykle jest to sytuacja, gdy istnieje przejście w tryb failover: fragmentu teraz znajduje się na nowym serwerze. Ponieważ fragmentu należy najpierw usunąć z usługi GSM (przy użyciu metody Recoverymanager. DetachShard), mapowanie nie istnieje już w usłudze GSM. W związku z tym LSM musi być używany do ponownego ustanowienia mapowania fragmentu.

## <a name="attach-a-shard-to-the-shardmap-after-a-shard-is-restored"></a>Dołącz fragmentu do ShardMap po przywróceniu fragmentu

[Metoda AttachShard](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.attachshard) dołącza daną fragmentu do mapy fragmentu. Następnie wykrywa wszystkie niespójności mapy fragmentu i aktualizuje mapowania tak, aby pasowały do fragmentu w punkcie przywracania fragmentu. Zakłada się, że nazwa bazy danych jest również zmieniona, aby odzwierciedlała oryginalną nazwę bazy danych (przed przystąpieniem do fragmentuu), ponieważ przywracanie do punktu w czasie jest domyślnie dodawane do nowej bazy danych z sygnaturą czasową.

   ```java
   rm.AttachShard(location, shardMapName)
   ```

* Parametr *Location* to nazwa serwera i nazwa bazy danych fragmentu, które są dołączane.
* Parametr *shardMapName* jest nazwą mapy fragmentu. Jest to wymagane tylko wtedy, gdy wiele map fragmentu jest zarządzanych przez ten sam Menedżer mapy fragmentu. Opcjonalny.

Ten przykład dodaje fragmentu do mapy fragmentu, która została ostatnio przywrócona z wcześniejszego punktu w czasie. Ponieważ fragmentu (tj. mapowanie dla fragmentu w LSM) zostało przywrócone, może być niespójny z wpisem fragmentu w GSM. Na zewnątrz tego przykładowego kodu fragmentu został przywrócony i zmieniono nazwę na oryginalną. Ponieważ został przywrócony, zakłada się, że mapowanie w LSM jest zaufanym mapowaniem.

   ```java
   rm.AttachShard(s.Location, customerMap);
   var gs = rm.DetectMappingDifferences(s.Location);
      foreach (RecoveryToken g in gs)
       {
       rm.ResolveMappingDifferences(g, MappingDifferenceResolution.KeepShardMapping);
       }
   ```

## <a name="updating-shard-locations-after-a-geo-failover-restore-of-the-shards"></a>Aktualizowanie lokalizacji fragmentu po geograficznej trybie failover (Przywracanie) fragmentów

W przypadku geograficznego przejścia w tryb failover pomocnicza baza danych jest dostępna do zapisu i będzie nową podstawową bazą danych. Nazwa serwera i potencjalnie baza danych (w zależności od konfiguracji) mogą różnić się od oryginalnego elementu podstawowego. W związku z tym wpisy mapowania dla fragmentu w GSM i LSM muszą być ustalone. Podobnie, jeśli baza danych zostanie przywrócona do innej nazwy lub lokalizacji lub do wcześniejszego punktu w czasie, może to spowodować niespójności w mapach fragmentu. Menedżer mapy fragmentu obsługuje dystrybucję otwartych połączeń do prawidłowej bazy danych. Dystrybucja opiera się na danych na mapie fragmentu oraz wartości klucza fragmentowania, który jest elementem docelowym żądania aplikacji. Po geograficznym przejściu do trybu failover należy zaktualizować te informacje przy użyciu dokładnej nazwy serwera, nazwy bazy danych i mapowania fragmentu odzyskanej bazy danych.

## <a name="best-practices"></a>Najlepsze rozwiązania

Geograficznie tryb failover i odzyskiwanie są zwykle zarządzane przez administratora chmury aplikacji celowo wykorzystującej Azure SQL Database funkcje ciągłości działania. Planowanie ciągłości działalności biznesowej wymaga procesów, procedur i miar, aby zapewnić, że operacje biznesowe mogą być kontynuowane bez przeszkód. Metody dostępne jako część klasy Recoverymanager powinny być używane w tym przepływie pracy, aby upewnić się, że GSM i LSM są aktualne na podstawie wykonywanej akcji odzyskiwania. Istnieje pięć podstawowych kroków, aby prawidłowo zapewnić, że GSM i LSM odzwierciedlają dokładne informacje po przejściu w tryb failover. Kod aplikacji do wykonania tych kroków można zintegrować z istniejącymi narzędziami i przepływem pracy.

1. Pobierz z ShardMapManager.
2. Odłącz starą fragmentu od mapy fragmentu.
3. Dołącz nowy fragmentu do mapy fragmentu, w tym nowej lokalizacji fragmentu.
4. Wykrywaj niespójności w mapowaniu między GSM i LSM.
5. Rozwiąż różnice między GSM i LSM, aby ufać LSM.

W tym przykładzie wykonywane są następujące czynności:

1. Usuwa fragmentów z mapy fragmentu, która odzwierciedla lokalizacje fragmentu przed zdarzeniem trybu failover.
2. Dołącza fragmentów do mapy fragmentu odzwierciedlające nowe lokalizacje fragmentu (parametr "Configuration. SecondaryServer" to nowa nazwa serwera, ale ta sama nazwa bazy danych).
3. Pobiera tokeny odzyskiwania przez wykrywanie różnic mapowania między GSM i LSM dla każdego fragmentuu.
4. Rozwiązuje niespójności, ufając mapowanie od LSM każdego fragmentuu.

   ```java
   var shards = smm.GetShards();
   foreach (shard s in shards)
   {
     if (s.Location.Server == Configuration.PrimaryServer)
         {
          ShardLocation slNew = new ShardLocation(Configuration.SecondaryServer, s.Location.Database);
          rm.DetachShard(s.Location);
          rm.AttachShard(slNew);
          var gs = rm.DetectMappingDifferences(slNew);
          foreach (RecoveryToken g in gs)
            {
               rm.ResolveMappingDifferences(g, MappingDifferenceResolution.KeepShardMapping);
            }
        }
    }
   ```

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/elastic-database-recovery-manager/recovery-manager.png
