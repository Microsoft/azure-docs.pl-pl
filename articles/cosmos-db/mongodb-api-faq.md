---
title: Często zadawane pytania dotyczące interfejsu API Azure Cosmos DB dla MongoDB
description: Uzyskaj odpowiedzi na często zadawane pytania dotyczące interfejsu API Azure Cosmos DB dla MongoDB
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/28/2020
ms.author: sngun
ms.openlocfilehash: 03c47b398a715a128a32fb6a6fd9d99564ccb891
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93096635"
---
# <a name="frequently-asked-questions-about-the-azure-cosmos-dbs-api-for-mongodb"></a>Często zadawane pytania dotyczące interfejsu API Azure Cosmos DB dla MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Interfejs API Azure Cosmos DB dla MongoDB to warstwa zgodności protokołu przewodowego, która umożliwia aplikacjom łatwe i nieprzezroczyste komunikowanie się z natywnym aparatem bazy danych Cosmos platformy Azure przy użyciu istniejących, obsługiwanych przez społeczność zestawów SDK i sterowników dla MongoDB. Deweloperzy mogą teraz używać istniejących MongoDB łańcuchy narzędzi i umiejętności do kompilowania aplikacji, które korzystają z Azure Cosmos DB. Deweloperzy mogą korzystać z unikatowych możliwości Azure Cosmos DB, które obejmują dystrybucję globalną z wieloregionową replikacją zapisu, funkcję autoindeksowania, konserwację kopii zapasowej, finansowo objęte umową dotyczącą poziomu usług (umowy SLA) itp.

## <a name="how-do-i-connect-to-my-database"></a>Jak mogę połączyć się z moją bazą danych?

Najszybszym sposobem łączenia się z bazą danych Cosmos Azure Cosmos DB z interfejsem API programu MongoDB jest przełączenie do [Azure Portal](https://portal.azure.com). Przejdź do swojego konta, a następnie w menu nawigacji po lewej stronie kliknij pozycję **Szybki Start** . Szybki Start to najlepszy sposób na uzyskanie fragmentów kodu w celu nawiązania połączenia z bazą danych.

Azure Cosmos DB wymusza ścisłe wymagania i standardy dotyczące zabezpieczeń. Konta Azure Cosmos DB wymagają uwierzytelniania i bezpiecznej komunikacji za pośrednictwem protokołu TLS, dlatego należy używać TLSv 1.2.

Aby uzyskać więcej informacji, zobacz [łączenie się z bazą danych Cosmos przy użyciu interfejsu API Azure Cosmos DB dla MongoDB](connect-mongodb-account.md).

## <a name="error-codes-while-using-azure-cosmos-dbs-api-for-mongodb"></a>Kody błędów podczas korzystania z interfejsu API Azure Cosmos DB dla MongoDB?

Wraz ze wspólnymi kodami błędów MongoDB interfejs API Azure Cosmos DB dla MongoDB ma własne konkretne kody błędów:

| Błąd               | Kod  | Opis  | Rozwiązanie  |
|---------------------|-------|--------------|-----------|
| TooManyRequests     | 16500 | Całkowita liczba zużytych jednostek żądania jest większa niż stawka żądania aprowizacji dla kontenera i została ograniczona. | Rozważ przeskalowanie przepływności przypisanej do kontenera lub zestawu kontenerów z Azure Portal lub ponowienie próby. |
| ExceededMemoryLimit | 16501 | W ramach usługi wielodostępnej Operacja przekroczyła przydział pamięci klienta. | Zmniejsz zakres operacji przy użyciu bardziej restrykcyjnych kryteriów zapytania lub skontaktuj się z pomocą techniczną z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). <br><br> Przykład: `db.getCollection('users').aggregate([{$match: {name: "Andy"}}, {$sort: {age: -1}}]))` |

## <a name="supported-drivers"></a>Obsługiwane sterowniki

### <a name="is-the-simba-driver-for-mongodb-supported-for-use-with-azure-cosmos-dbs-api-for-mongodb"></a>Czy sterownik Simba dla MongoDB jest obsługiwany z interfejsem API Azure Cosmos DB dla MongoDB?

Tak, możesz użyć sterownika Simba Mongo ODBC z interfejsem API Azure Cosmos DB dla MongoDB

## <a name="next-steps"></a>Następne kroki

* [Tworzenie aplikacji sieci Web platformy .NET przy użyciu interfejsu API Azure Cosmos DB dla MongoDB](create-mongodb-dotnet.md)
* [Tworzenie aplikacji konsolowej przy użyciu języka Java i interfejsu API MongoDB w Azure Cosmos DB](create-mongodb-java.md)
