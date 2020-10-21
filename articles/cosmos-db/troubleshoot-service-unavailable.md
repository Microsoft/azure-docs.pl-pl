---
title: Rozwiązywanie problemów z niedostępnymi wyjątkami usługi Azure Cosmos DB
description: Dowiedz się, jak zdiagnozować i naprawić wyjątki usługi Azure Cosmos DB niedostępne.
author: j82w
ms.service: cosmos-db
ms.date: 08/06/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: b40787e1b7c40e5c238b2e400f6b449ad8963dd1
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/20/2020
ms.locfileid: "92277144"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-service-unavailable-exceptions"></a>Diagnozowanie i rozwiązywanie problemów z niedostępnymi wyjątkami usługi Azure Cosmos DB
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