---
title: Rozwiązywanie problemów z limitem czasu żądania usługi Azure Cosmos DB
description: Jak zdiagnozować i naprawić wyjątek limitu czasu żądania usługi Cosmos DB
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: bd8102ee73721e2a56185709d1076e489ecc7607
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87294272"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-request-timeout"></a>Diagnozowanie i rozwiązywanie problemów z limitem czasu żądania Azure Cosmos DB
Azure Cosmos DB zwrócił limit czasu żądania HTTP 408

## <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów
Poniższa lista zawiera znane przyczyny i rozwiązania dla wyjątków limitu czasu żądania.

### <a name="1-check-the-sla"></a>1. Sprawdź umowę SLA
Klient powinien sprawdzić [Azure Cosmos DB monitorowania](monitor-cosmos-db.md) , aby sprawdzić, czy liczba wyjątków 408 narusza Cosmos DB umowy SLA.

#### <a name="solution-1-it-did-not-violate-the-cosmos-db-sla"></a>Rozwiązanie 1: nie narusza Cosmos DB umowy SLA
Aplikacja powinna obsłużyć ten scenariusz i ponowić próbę wykonania tych błędów przejściowych.

#### <a name="solution-2-it-did-violate-the-cosmos-db-sla"></a>Rozwiązanie 2: naruszenie umowy SLA Cosmos DB
Skontaktuj się z pomocą techniczną platformy Azure:https://aka.ms/azure-support
 
### <a name="2-hot-partition-key"></a>2. klucz partycji aktywnej
Azure Cosmos DB dystrybuuje ogólnie zainicjowaną przepływność równomiernie między partycjami fizycznymi. Gdy istnieje partycja gorąca, co najmniej jeden klucz partycji logicznej na partycji fizycznej zużywa wszystkie wystąpienia RU/s partycji fizycznej, podczas gdy nie są one używane na innych partycjach fizycznych. Z punktu widzenia całkowita ilość zużywanych przez RU/s będzie mniejsza niż ogólnie obsługiwana wartość RU/s w bazie danych lub kontenerze, ale nadal będzie widoczne ograniczenie przepustowości (429s) na żądania dotyczące klucza logicznej partycji. Użyj [znormalizowanej metryki zużycia ru](monitor-normalized-request-units.md) , aby sprawdzić, czy obciążenie napotyka partycję gorącą. 

#### <a name="solution"></a>Rozwiązanie:
Wybierz dobry klucz partycji, który równomiernie dystrybuuje wolumin żądania i magazyn. Dowiedz się, jak [zmienić klucz partycji](https://devblogs.microsoft.com/cosmosdb/how-to-change-your-partition-key/).

## <a name="next-steps"></a>Następne kroki
* [Diagnozowanie i rozwiązywanie](troubleshoot-dot-net-sdk.md) problemów podczas korzystania z zestawu SDK Azure Cosmos DB .NET
* Informacje o wskazówkach dotyczących wydajności dla [platform .NET v3](performance-tips-dotnet-sdk-v3-sql.md) i [.NET V2](performance-tips.md)