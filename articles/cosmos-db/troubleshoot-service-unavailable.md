---
title: Rozwiązywanie problemów z niedostępnymi wyjątkami usługi Azure Cosmos DB
description: Dowiedz się, jak zdiagnozować i naprawić wyjątki usługi Azure Cosmos DB niedostępne.
author: j82w
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 08/06/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: d19d30c03412ba7212211b30646acb50c3f55ece
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93340030"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-service-unavailable-exceptions"></a>Diagnozowanie i rozwiązywanie problemów z niedostępnymi wyjątkami usługi Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Zestaw SDK nie może nawiązać połączenia z Azure Cosmos DB.

## <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów
Poniższa lista zawiera znane przyczyny i rozwiązania niedostępnych wyjątków usługi.

### <a name="the-required-ports-are-being-blocked"></a>Wymagane porty są blokowane
Sprawdź, czy wszystkie [wymagane porty](sql-sdk-connection-modes.md#service-port-ranges) są włączone.

### <a name="client-side-transient-connectivity-issues"></a>Przejściowe problemy z łącznością po stronie klienta
Wyjątki niedostępne dla usługi mogą być widoczne w przypadku przejściowych problemów z łącznością, które powodują przekroczenie limitu czasu. Zwykle ślad stosu związany z tym scenariuszem będzie zawierać `TransportException` błąd. Na przykład:

```C#
TransportException: A client transport error occurred: The request timed out while waiting for a server response. 
(Time: xxx, activity ID: xxx, error code: ReceiveTimeout [0x0010], base error: HRESULT 0x80131500
```

Wykonaj [kroki rozwiązywania problemów z limitem czasu żądania](troubleshoot-dot-net-sdk-request-timeout.md#troubleshooting-steps) , aby rozwiązać ten problem.

### <a name="service-outage"></a>Awaria usługi
Sprawdź [Stan platformy Azure](https://status.azure.com/status) , aby sprawdzić, czy występuje problem.


## <a name="next-steps"></a>Następne kroki
* [Diagnozowanie i rozwiązywanie](troubleshoot-dot-net-sdk.md) problemów podczas korzystania z zestawu SDK Azure Cosmos DB platformy .NET.
* Poznaj wskazówki dotyczące wydajności dla [platform .NET v3](performance-tips-dotnet-sdk-v3-sql.md) i [.NET V2](performance-tips.md).