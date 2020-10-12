---
title: Kroki optymalizacji po migracji z interfejsem API Azure Cosmos DB dla MongoDB
description: Ten dokument zawiera techniki optymalizacji po migracji z MongoDB do interfejsu APi usługi Mongo DB Azure Cosmos DB.
author: jasonwhowell
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 03/20/2020
ms.author: jasonh
ms.openlocfilehash: f21aa30de2b2dfcf85e45c7ed4d5c485fe45eed7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91409701"
---
# <a name="post-migration-optimization-steps-when-using-azure-cosmos-dbs-api-for-mongodb"></a>Kroki optymalizacji po migracji podczas korzystania z interfejsu API Azure Cosmos DB dla MongoDB

Po przeprowadzeniu migracji danych przechowywanych w bazie danych MongoDB do interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB możesz nawiązać połączenie z usługą Azure Cosmos DB i zarządzać danymi. Ten przewodnik zawiera opis kroków, których wykonanie należy rozważyć po przeprowadzeniu migracji. Zapoznaj się z samouczkiem dotyczącym migracji [MongoDB Azure Cosmos DB do interfejsu API usługi MongoDB](../dms/tutorial-mongodb-cosmos-db.md) w celu zaplanowania czynności związanych z migracją.

Niniejszy przewodnik zawiera informacje na temat wykonywania następujących czynności:

- [Łączenie aplikacji](#connect-your-application)
- [Optymalizowanie zasad indeksowania](#optimize-the-indexing-policy)
- [Skonfiguruj dystrybucję globalną dla interfejsu API Azure Cosmos DB dla MongoDB](#globally-distribute-your-data)
- [Ustaw poziom spójności](#set-consistency-level)

> [!NOTE]
> Jedynym wymaganym etapem po migracji na poziomie aplikacji jest zmiana parametrów połączenia w aplikacji w taki sposób, aby wskazywały nowe konto Azure Cosmos DB. Wszystkie inne kroki migracji to zalecane optymalizacje.
>

## <a name="connect-your-application"></a>Łączenie aplikacji

1. W nowym oknie Zaloguj się do [Azure Portal](https://www.portal.azure.com/)
2. W obszarze [Azure Portal](https://www.portal.azure.com/)w lewym okienku Otwórz menu **wszystkie zasoby** i Znajdź konto Azure Cosmos DB, do którego przeprowadzono migrację danych.
3. Otwórz blok **parametrów połączenia** . Prawe okienko zawiera wszystkie informacje potrzebne do pomyślnego połączenia z kontem.
4. Użyj informacji o połączeniu w konfiguracji aplikacji (lub innych odpowiednich miejscach), aby odzwierciedlić interfejs API Azure Cosmos DB dla połączenia MongoDB w aplikacji.
:::image type="content" source="./media/mongodb-post-migration/connection-string.png" alt-text="Zrzut ekranu przedstawia ustawienia parametrów połączenia.":::

Aby uzyskać więcej informacji, zobacz stronę [łączenie aplikacji MongoDB na Azure Cosmos DB](connect-mongodb-account.md) .

## <a name="optimize-the-indexing-policy"></a>Optymalizowanie zasad indeksowania

Wszystkie pola danych są automatycznie indeksowane domyślnie podczas migracji danych do Azure Cosmos DB. W wielu przypadkach ta domyślna zasada indeksowania jest akceptowalna. Ogólnie rzecz biorąc, usuwanie indeksów optymalizuje żądania zapisu i ma domyślne zasady indeksowania (tj. Automatyczne indeksowanie) optymalizuje żądania odczytu.

Aby uzyskać więcej informacji na temat indeksowania, zobacz [indeksowanie danych w interfejsie API Azure Cosmos DB MongoDB](mongodb-indexing.md) oraz [indeksowanie w](index-overview.md) artykułach Azure Cosmos DB.

## <a name="globally-distribute-your-data"></a>Dystrybuuj globalnie dane

Azure Cosmos DB jest dostępna we wszystkich [regionach świadczenia usługi Azure](https://azure.microsoft.com/regions/#services) na całym świecie. Po wybraniu domyślnego poziomu spójności dla konta Azure Cosmos DB można skojarzyć jeden lub więcej regionów świadczenia usługi Azure (w zależności od potrzeb związanych z dystrybucją globalną). Aby zapewnić wysoką dostępność i ciągłość biznesową, zawsze zalecamy uruchamianie w co najmniej dwóch regionach. Możesz zapoznać się ze wskazówkami dotyczącymi [optymalizacji kosztów wdrożeń wieloregionowych w Azure Cosmos DB](optimize-cost-regions.md).

Aby globalnie dystrybuować dane, zobacz [dystrybuowanie danych globalnie w interfejsie API Azure Cosmos DB MongoDB](tutorial-global-distribution-mongodb.md).

## <a name="set-consistency-level"></a>Ustaw poziom spójności

Azure Cosmos DB oferuje 5 dobrze zdefiniowanych [poziomów spójności](consistency-levels.md). Aby zapoznać się z mapowaniem między MongoDB i Azure Cosmos DB poziomów spójności, odczyt [poziomów spójności i Azure Cosmos DB interfejsów API](consistency-levels-across-apis.md). Domyślny poziom spójności to poziom spójności sesji. Zmiana poziomu spójności jest opcjonalna i można ją zoptymalizować dla aplikacji. Aby zmienić poziom spójności przy użyciu Azure Portal:

1. Przejdź do **domyślnego bloku spójności** w obszarze Ustawienia.
2. Wybierz [poziom spójności](consistency-levels.md)

Większość użytkowników opuszcza poziom spójności w domyślnym ustawieniu spójności sesji. Istnieją jednak [kompromisy dostępności i wydajności dla różnych poziomów spójności](consistency-levels-tradeoffs.md).

## <a name="next-steps"></a>Następne kroki

* [Łączenie aplikacji bazy danych MongoDB z usługą Azure Cosmos DB](connect-mongodb-account.md)
* [Nawiązywanie połączenia z kontem Azure Cosmos DB przy użyciu programu Studio 3T](mongodb-mongochef.md)
* [Jak globalnie dystrybuować odczyty przy użyciu interfejsu API Azure Cosmos DB dla MongoDB](mongodb-readpreference.md)
* [Wygasanie danych za pomocą interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB](mongodb-time-to-live.md)
* [Poziomy spójności w Azure Cosmos DB](consistency-levels.md)
* [Indeksowanie w usłudze Azure Cosmos DB](index-overview.md)
* [Jednostki żądania w usłudze Azure Cosmos DB](request-units.md)
