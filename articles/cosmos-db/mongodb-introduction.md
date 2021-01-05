---
title: Wprowadzenie do interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB
description: Dowiedz się, jak można używać usługi Azure Cosmos DB do przechowywania ogromnych ilości danych i wykonywania na nich zapytań przy użyciu interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: overview
ms.date: 11/25/2020
author: sivethe
ms.author: sivethe
ms.openlocfilehash: ab335c8cc682f6b33dcb30509d682186a178c676
ms.sourcegitcommit: aeba98c7b85ad435b631d40cbe1f9419727d5884
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2021
ms.locfileid: "97861066"
---
# <a name="azure-cosmos-dbs-api-for-mongodb"></a>Interfejs API usługi Azure Cosmos DB dla bazy danych MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

[Azure Cosmos DB](introduction.md) to rozproszona globalnie wielomodelowa usługa bazy danych firmy Microsoft do aplikacji o krytycznym znaczeniu. Usługa Azure Cosmos DB zapewnia [kompleksową globalną dystrybucję](distribute-data-globally.md), [elastyczne skalowanie przepływności i pamięci](partitioning-overview.md) w skali globalnej, milisekundowe opóźnienia w 99. percentylu oraz gwarantowaną wysoką dostępność, wspierane przez [wiodące w branży umowy SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/). Usługa Azure Cosmos DB [automatycznie indeksuje dane](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) bez konieczności zarządzania schematami i indeksami. To usługa wielomodelowa, obsługująca modele oparte na dokumentach, parach klucz-wartość, grafach i kolumnach. Usługa Azure Cosmos DB implementuje protokoły sieciowe dla wspólnych interfejsów API NoSQL, w tym Cassandra, MongoDB, Gremlin i Azure Table Storage. Dzięki temu można korzystać z dobrze znanych sterowników i narzędzi klienta NoSQL do interakcji z bazą danych Cosmos.

> [!NOTE]
> [Tryb wydajności bezserwerowej](serverless.md) jest teraz dostępny w interfejsie API Azure Cosmos DB dla MongoDB.

## <a name="wire-protocol-compatibility"></a>Zgodność z protokołem przewodowym

Azure Cosmos DB implementuje protokół przewodu dla MongoDB. Ta implementacja zapewnia przezroczystą zgodność z natywnymi zestawami SDK klienta MongoDB, sterownikami i narzędziami. Azure Cosmos DB nie hostuje aparatu bazy danych MongoDB. Szczegóły obsługiwanych funkcji według MongoDB można znaleźć tutaj: 
- [Interfejs API Azure Cosmos DB dla aparatu Mongo DB w wersji 3,6](mongodb-feature-support-36.md)
- [Interfejs API Azure Cosmos DB dla aparatu Mongo DB w wersji 3,2](mongodb-feature-support.md)

Domyślnie nowe konta utworzone przy użyciu interfejsu API Azure Cosmos DB dla MongoDB są zgodne z wersją 3,6 protokołu MongoDB. Każdy sterownik klienta MongoDB, który rozumie tę wersję protokołu, powinien mieć możliwość natywnego połączenia z Cosmos DB.

:::image type="content" source="./media/mongodb-introduction/cosmosdb-mongodb.png" alt-text="Interfejs API usługi Azure Cosmos DB dla bazy danych MongoDB" border="false":::

## <a name="key-benefits"></a>Najważniejsze korzyści

Kluczowe korzyści z usługi Cosmos DB jako w pełni zarządzanej, globalnie rozproszonej bazy danych jako usługi zostały opisane [tutaj](introduction.md). Ponadto dzięki natywnej implementacji protokołów przewodowych popularnych interfejsów API NoSQL usługa Cosmos DB zapewnia następujące korzyści:

* Łatwe migrowanie aplikacji do usługi Cosmos DB przy jednoczesnym zachowaniu znacznej części logiki aplikacji.
* Zapewnienie, że aplikacja będzie nadal przenośna i niezależna od dostawcy i chmury.
* Uzyskanie wiodących w branży zabezpieczonych finansowo umów SLA dla typowych interfejsów API NoSQL dostarczanych przez usługę Cosmos DB.
* Elastyczne skalowanie aprowizowanej przepływności i magazynu dla baz danych Cosmos zgodnie z potrzebami i płatność tylko za przepływność i magazyn, które są potrzebne. Prowadzi to do znacznych oszczędności kosztów.
* Gotowe, globalna dystrybucja z wieloregionową replikacją.

## <a name="cosmos-dbs-api-for-mongodb"></a>Interfejs API usługi Cosmos DB dla bazy danych MongoDB

Postępuj zgodnie z przewodnikami Szybki Start, aby utworzyć konto usługi Azure Cosmos i przeprowadzić migrację istniejącej aplikacji MongoDB w celu użycia Azure Cosmos DB lub utworzyć nową:

* [Migrate an existing MongoDB Node.js web app](create-mongodb-nodejs.md) (Migracja istniejącej aplikacji internetowej MongoDB w środowisku Node.js).
* [Build a web app using Azure Cosmos DB's API for MongoDB and .NET SDK](create-mongodb-dotnet.md) (Tworzenie aplikacji internetowej za pomocą interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB i zestawu SDK platformy .NET)
* [Build a web app using Azure Cosmos DB's API for MongoDB and Java SDK](create-mongodb-java.md) (Tworzenie aplikacji internetowej za pomocą interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB i zestawu SDK języka Java)

## <a name="next-steps"></a>Następne kroki

Oto kilka wskazówek ułatwiających rozpoczęcie pracy:

* Postępuj zgodnie z instrukcjami z samouczka [Connect a MongoDB application to Azure Cosmos DB](connect-mongodb-account.md) (Łączenie aplikacji bazy danych MongoDB z usługą Azure Cosmos DB), aby dowiedzieć się, jak uzyskać informacje dotyczące parametrów połączenia konta.
* Postępuj zgodnie z instrukcjami z samouczka [Use Studio 3T with Azure Cosmos DB](mongodb-mongochef.md) (Używanie programu Studio 3T z usługą Azure Cosmos DB), aby dowiedzieć się, jak utworzyć połączenie między bazą danych Cosmos a aplikacją MongoDB w programie Studio 3T.
* Postępuj zgodnie z instrukcjami z samouczka [Import MongoDB data into Azure Cosmos DB ](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json) (Importowane danych MongoDB do usługi Azure Cosmos DB), aby zaimportować swoje dane do bazy danych Cosmos.
* Nawiąż połączenie z kontem Cosmos za pomocą programu [Robo 3T](mongodb-robomongo.md).
* Dowiedz się, jak [konfigurować preferencje odczytu dla aplikacji rozproszonych globalnie](../cosmos-db/tutorial-global-distribution-mongodb.md).
* Znajdź rozwiązania często występujące błędy w naszym [przewodniku rozwiązywania problemów](mongodb-troubleshoot.md)


<sup>Uwaga: w tym artykule opisano funkcję Azure Cosmos DB, która zapewnia zgodność protokołu telekomunikacyjnych z bazami danych MongoDB. Firma Microsoft nie uruchamia MongoDB baz danych w celu zapewnienia tej usługi. Azure Cosmos DB nie jest powiązany z MongoDB, Inc.</sup>