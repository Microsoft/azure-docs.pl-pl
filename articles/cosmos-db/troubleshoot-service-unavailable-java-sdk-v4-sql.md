---
title: Rozwiązywanie problemów z usługą Azure Cosmos DB niedostępne wyjątki z zestawem SDK Java v4
description: Dowiedz się, jak zdiagnozować i naprawić wyjątki usługi Azure Cosmos DB niedostępne w programie Java v4 SDK.
author: kushagrathapar
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 10/28/2020
ms.author: kuthapar
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 03c899307b00010ca87b279ed720b92946d3673c
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93340091"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-java-v4-sdk-service-unavailable-exceptions"></a>Diagnozowanie i rozwiązywanie problemów w przypadku niedostępności usługi SDK w programie Java v4 Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]
Zestaw SDK dla języka Java v4 nie był w stanie połączyć się z Azure Cosmos DB.

## <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów
Poniższa lista zawiera znane przyczyny i rozwiązania niedostępnych wyjątków usługi.

### <a name="the-required-ports-are-being-blocked"></a>Wymagane porty są blokowane
Sprawdź, czy wszystkie [wymagane porty](sql-sdk-connection-modes.md#service-port-ranges) są włączone.

### <a name="client-side-transient-connectivity-issues"></a>Przejściowe problemy z łącznością po stronie klienta
Wyjątki niedostępne dla usługi mogą być widoczne w przypadku przejściowych problemów z łącznością, które powodują przekroczenie limitu czasu. Zwykle ślad stosu związany z tym scenariuszem będzie zawierać `ServiceUnavailableException` błąd z danymi diagnostycznymi. Przykład:

```java
Exception in thread "main" ServiceUnavailableException{userAgent=azsdk-java-cosmos/4.6.0 Linux/4.15.0-1096-azure JRE/11.0.8, error=null, resourceAddress='null', requestUri='null', statusCode=503, message=Service is currently unavailable, please retry after a while. If this problem persists please contact support.: Message: "" {"diagnostics"}
```

Wykonaj [kroki rozwiązywania problemów z limitem czasu żądania](troubleshoot-request-timeout-java-sdk-v4-sql.md#troubleshooting-steps) , aby rozwiązać ten problem.

### <a name="service-outage"></a>Awaria usługi
Sprawdź [Stan platformy Azure](https://status.azure.com/status) , aby sprawdzić, czy występuje problem.


## <a name="next-steps"></a>Następne kroki
* [Diagnozowanie i rozwiązywanie](troubleshoot-java-sdk-v4-sql.md) problemów w przypadku używania Azure Cosmos DB Java v4 SDK.
* Poznaj wskazówki dotyczące wydajności dla [zestawu SDK dla języka Java v4](performance-tips-java-sdk-v4-sql.md).