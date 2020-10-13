---
title: Liczniki wydajności do śledzenia Menedżera mapy fragmentu
description: Liczniki wydajności routingu zależnego od danych ShardMapManager i klas
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: seoapril2019, seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 02/07/2019
ms.openlocfilehash: 6038ec1d83957f20ca6e2759eeb5a88e66c2f77f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91443407"
---
# <a name="create-performance-counters-to-track-performance-of-shard-map-manager"></a>Tworzenie liczników wydajności w celu śledzenia wydajności Menedżera mapy fragmentu
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Liczniki wydajności służą do śledzenia wydajności operacji [routingu zależnych od danych](elastic-scale-data-dependent-routing.md) . Te liczniki są dostępne w Monitorze wydajności w kategorii "Elastic Database: fragmentu Management".

Wydajność [Menedżera mapy fragmentu](elastic-scale-shard-map-management.md)można przechwycić, szczególnie w przypadku korzystania z [routingu zależnego od danych](elastic-scale-data-dependent-routing.md). Liczniki są tworzone przy użyciu metod klasy Microsoft. Azure. SQLDatabase. ElasticScale. Client.  


**Najnowsza wersja:** Przejdź do [Microsoft. Azure. SQLDatabase. ElasticScale. Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/). Zobacz też [Uaktualnianie aplikacji, aby użyć najnowszej biblioteki klienta Elastic Database](elastic-scale-upgrade-client-library.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Aby utworzyć kategorię wydajności i liczniki, użytkownik musi być częścią lokalnej grupy **administratorów** na komputerze hostującym aplikację.  
* Aby utworzyć wystąpienie licznika wydajności i zaktualizować liczniki, użytkownik musi być członkiem grupy **administratorzy** lub **Użytkownicy monitora wydajności** .

## <a name="create-performance-category-and-counters"></a>Tworzenie kategorii i liczników wydajności

Aby utworzyć liczniki, wywołaj metodę CreatePerformanceCategoryAndCounters [klasy ShardMapManagementFactory](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory). Tylko administrator może wykonać metodę:

`ShardMapManagerFactory.CreatePerformanceCategoryAndCounters()`

Za pomocą [tego](https://gallery.technet.microsoft.com/scriptcenter/Elastic-DB-Tools-for-Azure-17e3d283) skryptu programu PowerShell można także wykonać metodę.
Metoda tworzy następujące liczniki wydajności:  

* **Mapowania buforowane**: liczba mapowań buforowanych dla mapy fragmentu.
* **Operacje DDR/s**: częstotliwość operacji routingu zależnych od danych dla mapy fragmentu. Ten licznik jest aktualizowany, gdy wywołanie [OpenConnectionForKey ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey) powoduje pomyślne połączenie z miejscem docelowym fragmentu.
* **Trafienia pamięci podręcznej wyszukiwania mapowania/s**: szybkość pomyślnych operacji wyszukiwania w pamięci podręcznej dla mapowań w mapie fragmentu.
* **Chybienia w pamięci podręcznej wyszukiwania mapowania/s**: szybkość nieudanych operacji wyszukiwania w pamięci podręcznej dla mapowań w mapie fragmentu.
* **Dodano lub Zaktualizowano mapowania w pamięci podręcznej/s**: szybkość, z jaką mapowania są dodawane lub aktualizowane w pamięci podręcznej dla mapy fragmentu.
* **Mapowania usunięte z pamięci podręcznej/s**: szybkość, z jaką mapowania są usuwane z pamięci podręcznej dla mapy fragmentu.

Liczniki wydajności są tworzone dla każdej buforowanej mapy fragmentu na proces.  

## <a name="notes"></a>Uwagi

Następujące zdarzenia wyzwalają tworzenie liczników wydajności:  

* Inicjalizacja [ShardMapManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager) z [ładowaniem eager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerloadpolicy), jeśli ShardMapManager zawiera wszystkie mapy fragmentu. Obejmują one metody [GetSqlShardMapManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager) i [TryGetSqlShardMapManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager) .
* Pomyślne wyszukiwanie mapy fragmentu (przy użyciu [GetShardMap ()](https://msdn.microsoft.com/library/azure/dn824215.aspx), [GetListShardMap ()](https://msdn.microsoft.com/library/azure/dn824212.aspx) lub [GetRangeShardMap ()](https://msdn.microsoft.com/library/azure/dn824173.aspx)).
* Pomyślne utworzenie mapy fragmentu za pomocą CreateShardMap ().

Liczniki wydajności zostaną zaktualizowane przez wszystkie operacje pamięci podręcznej wykonywane na mapie i mapowania fragmentu. Pomyślne usunięcie mapy fragmentu za pomocą DeleteShardMap () powoduje usunięcie wystąpienia liczników wydajności.  

## <a name="best-practices"></a>Najlepsze rozwiązania

* Tworzenie kategorii wydajności i liczników należy wykonać tylko raz przed utworzeniem obiektu ShardMapManager. Każde wykonanie polecenia CreatePerformanceCategoryAndCounters () czyści poprzednie liczniki (tracące dane zgłaszane przez wszystkie wystąpienia) i tworzy nowe.  
* Wystąpienia liczników wydajności są tworzone dla poszczególnych procesów. Wszystkie awarie aplikacji lub usunięcie mapy fragmentu z pamięci podręcznej spowodują usunięcie wystąpień liczników wydajności.  

### <a name="see-also"></a>Zobacz też

[Elastic Database features overview (Omówienie funkcji Elastic Database)](elastic-scale-introduction.md)  

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
