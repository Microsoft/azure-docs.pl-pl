---
title: Rozwiązywanie problemów z niedostępnym wyjątkiem usługi Azure Cosmos DB
description: Jak zdiagnozować i naprawić wyjątek niedostępności usługi Cosmos DB
author: j82w
ms.service: cosmos-db
ms.date: 08/06/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: b9c24ee3b94be86ccf9d27b928c42fc194800a3b
ms.sourcegitcommit: 25bb515efe62bfb8a8377293b56c3163f46122bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/07/2020
ms.locfileid: "87987379"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-service-unavailable"></a>Diagnozowanie i rozwiązywanie problemów z usługą Azure Cosmos DB niedostępne
Zestaw SDK nie może nawiązać połączenia z usługą Azure Cosmos DB.

## <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów
Poniższa lista zawiera znane przyczyny i rozwiązania niedostępnych wyjątków usługi.

### <a name="1-the-required-ports-are-being-blocked"></a>1. wymagane porty są blokowane
Sprawdź, czy wszystkie [wymagane porty](performance-tips-dotnet-sdk-v3-sql.md#networking) są włączone.

### <a name="2-client-side-transient-connectivity-issues"></a>2. tymczasowe problemy z łącznością po stronie klienta
Obszar niedostępności usługi może być narażony, gdy występują przejściowe problemy z łącznością, które powodują przekroczenie limitu czasu. Zwykle ślad stosu związany z tym scenariuszem będzie zawierać `TransportException` , na przykład:

```C#
TransportException: A client transport error occurred: The request timed out while waiting for a server response. 
(Time: xxx, activity ID: xxx, error code: ReceiveTimeout [0x0010], base error: HRESULT 0x80131500
```

Postępuj zgodnie z [żądaniem rozwiązywania problemów](troubleshoot-dot-net-sdk-request-timeout.md#troubleshooting-steps) , aby rozwiązać ten problem.

### <a name="3-service-outage"></a>3. awaria usługi
Sprawdź [Stan platformy Azure](https://status.azure.com/status) , aby sprawdzić, czy występuje problem.


## <a name="next-steps"></a>Następne kroki
* [Diagnozowanie i rozwiązywanie](troubleshoot-dot-net-sdk.md) problemów podczas korzystania z zestawu SDK Azure Cosmos DB .NET
* Informacje o wskazówkach dotyczących wydajności dla [platform .NET v3](performance-tips-dotnet-sdk-v3-sql.md) i [.NET V2](performance-tips.md)