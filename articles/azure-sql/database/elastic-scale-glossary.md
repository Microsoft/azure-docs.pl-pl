---
title: Słownik narzędzi Elastic Database
description: Wyjaśnienie terminów używanych w narzędziach Elastic Database
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/04/2018
ms.openlocfilehash: 9c229fe6c1ccc7e2ce8e4f46ece51f315c8b7a90
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91362618"
---
# <a name="elastic-database-tools-glossary"></a>Słownik narzędzi Elastic Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Poniższe terminy są zdefiniowane dla [narzędzi Elastic Database](elastic-scale-introduction.md). Narzędzia służą do zarządzania [mapami fragmentu](elastic-scale-shard-map-management.md)oraz obejmują [bibliotekę kliencką](elastic-database-client-library.md), [Narzędzie do dzielenia](elastic-scale-overview-split-and-merge.md)i kwerendy, [elastyczne pule](elastic-pool-overview.md)i [zapytania](elastic-query-overview.md). 

Te warunki są używane w przypadku [dodawania fragmentu przy użyciu narzędzi Elastic Database](elastic-scale-add-a-shard.md) i [używania klasy recoverymanager do rozwiązywania problemów z mapą fragmentu](elastic-database-recovery-manager.md).

![Terminy dotyczące skalowania elastycznego][1]

**Baza danych**: baza danych w Azure SQL Database. 

**Routing zależny od danych**: funkcje, które umożliwiają aplikacji łączenie się z fragmentu za pomocą określonego klucza fragmentowania. Zobacz [Routing zależny od danych](elastic-scale-data-dependent-routing.md). Porównaj z **[kwerendą fragmentu](elastic-scale-multishard-querying.md)**.

**Globalna mapa fragmentu**: Mapa między kluczami fragmentowania i ich odpowiednimi fragmentów w **zestawie fragmentu**. Globalna mapa fragmentu jest przechowywana w **Menedżerze mapy fragmentu**. Porównaj z **lokalną mapą fragmentu**.

**Lista mapy fragmentu**: Mapa fragmentu, w której klucze fragmentowania są mapowane pojedynczo. Porównaj z **zakresem mapy fragmentu**.   

**Lokalna Mapa fragmentu**: przechowywana na fragmentu, lokalna Mapa fragmentu zawiera mapowania dla podfragmentów, które znajdują się w fragmentu.

**Zapytanie fragmentu**: możliwość wystawienia zapytania względem wielu fragmentów; zestawy wyników są zwracane przy użyciu semantyki ALL (zwanej także "zapytaniem"). Porównaj z **routingiem zależnym od danych**.

Z **wieloma dzierżawcami** i **jedną dzierżawą**: spowoduje to wyświetlenie bazy danych z jedną dzierżawą i wielodostępną bazą danych:

![Pojedyncze i wielodostępne bazy danych](./media/elastic-scale-glossary/multi-single-simple.png)

Poniżej przedstawiono reprezentację baz danych o pojedynczej **podzielonej na fragmenty** i wielu dzierżawców. 

![Pojedyncze i wielodostępne bazy danych](./media/elastic-scale-glossary/shards-single-multi.png)

**Mapa fragmentu zakresu**: Mapa fragmentu, w której strategia dystrybucji fragmentu jest oparta na wielu zakresach ciągłych wartości. 

**Tabele odwołań**: tabele, które nie są podzielonej na fragmenty, ale są replikowane w fragmentów. Na przykład kody ZIP mogą być przechowywane w tabeli referencyjnej. 

**Fragmentu**: baza danych w Azure SQL Database, która przechowuje dane z zestawu danych podzielonej na fragmenty. 

**Elastyczność fragmentu**: możliwość przeprowadzenia zarówno **skalowania poziomego** , jak i **skalowania w pionie**.

**Podzielonej na fragmenty tabele**: tabele, które są podzielonej na fragmenty, czyli których dane są dystrybuowane w fragmentów w oparciu o ich wartości klucza fragmentowania. 

**Klucz fragmentowania**: wartość kolumny, która określa, jak dane są dystrybuowane w fragmentów. Typ wartości może być jednym z następujących: **int**, **bigint**, **varbinary**lub **unikatowy**. 

**Zestaw fragmentu**: Kolekcja fragmentów, które są przypisane do tej samej mapy fragmentu w Menedżerze fragmentu map.  

**Podfragmentu**: wszystkie dane skojarzone z pojedynczą wartością klucza fragmentowania na fragmentu. Podfragmentu to najmniejsza jednostka przenoszenia danych możliwa podczas redystrybucji tabel podzielonej na fragmenty. 

**Mapa fragmentu**: zestaw mapowań między kluczami fragmentowania i ich odpowiednimi fragmentów.

**Menedżer mapy fragmentu**: obiekt zarządzania i magazyn danych, który zawiera mapy fragmentu, lokalizacje fragmentu i mapowania dla co najmniej jednego zestawu fragmentu.

![Diagram przedstawia Menedżera mapy fragmentu skojarzony z shardmaps_global, shards_global i shard_mappings_global.][2]

## <a name="verbs"></a>zlecenia
**Skalowanie w poziomie**: czynność skalowania (lub w) kolekcji fragmentów przez dodanie lub usunięcie fragmentów do mapy fragmentu, jak pokazano poniżej.

![Skalowanie w poziomie i w pionie][3]

**Scalanie**: czynność przeniesienia podfragmentów z dwóch fragmentów do jednego fragmentu i odpowiednio aktualizuje mapę fragmentu.

**Podfragmentu Move**: czynność przenoszenia pojedynczego podfragmentu do innego fragmentu. 

**Fragmentu**: czynność partycjonowania w poziomie identycznie uporządkowanych danych w wielu bazach danych na podstawie klucza fragmentowania.

**Podział**: czynność przeniesienia kilku podfragmentów z jednego fragmentu do innego (zazwyczaj nowość) fragmentu. Klucz fragmentowania jest dostarczany przez użytkownika jako punkt podziału.

**Skalowanie w pionie**: czynność skalowania w górę (lub w dół) wielkości obliczeniowej pojedynczego fragmentuu. Na przykład zmiana fragmentu z standardowa na Premium (co powoduje zwiększenie zasobów obliczeniowych). 

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/elastic-scale-glossary/glossary.png
[2]: ./media/elastic-scale-glossary/mappings.png
[3]: ./media/elastic-scale-glossary/h_versus_vert.png

