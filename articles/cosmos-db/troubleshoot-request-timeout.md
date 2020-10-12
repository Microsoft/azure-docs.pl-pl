---
title: Rozwiązywanie problemów z wyjątkami limitu czasu żądania usługi Azure Cosmos DB
description: Dowiedz się, jak zdiagnozować i naprawić wyjątki limitu czasu żądania usługi Azure Cosmos DB.
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 76a1558534728613dcdedc78b64a0366f2bd643d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88871075"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-request-timeout-exceptions"></a>Diagnozowanie i rozwiązywanie problemów z wyjątkami limitu czasu żądania Azure Cosmos DB
Azure Cosmos DB zwrócił limit czasu żądania HTTP 408.

## <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów
Poniższa lista zawiera znane przyczyny i rozwiązania dla wyjątków limitu czasu żądania.

### <a name="check-the-sla"></a>Sprawdź umowę SLA
Sprawdź [Azure Cosmos DB monitorowania](monitor-cosmos-db.md) , aby sprawdzić, czy liczba wyjątków 408 narusza Azure Cosmos DB umowy SLA.

#### <a name="solution-1-it-didnt-violate-the-azure-cosmos-db-sla"></a>Rozwiązanie 1: nie naruszono umowy SLA Azure Cosmos DB
Aplikacja powinna obsłużyć ten scenariusz i ponowić próbę wykonania tych błędów przejściowych.

#### <a name="solution-2-it-did-violate-the-azure-cosmos-db-sla"></a>Rozwiązanie 2: naruszenie umowy SLA Azure Cosmos DB
Skontaktuj się z [pomocą techniczną platformy Azure](https://aka.ms/azure-support).
 
### <a name="hot-partition-key"></a>Klucz partycji aktywnej
Azure Cosmos DB dystrybuuje ogólnie zainicjowaną przepływność równomiernie między partycjami fizycznymi. Gdy istnieje partycja gorąca, co najmniej jeden klucz partycji logicznej na partycji fizycznej zużywa wszystkie jednostki żądań partycji fizycznej na sekundę (RU/s). W tym samym czasie jednostki RU/s na innych partycjach fizycznych nie są używane. Z tego objawu całkowita ilość zużywanych przez RU/s będzie mniejsza niż ogólnie obsługiwana wartość RU/s w bazie danych lub kontenerze. Nadal zobaczysz ograniczenie przepustowości (429s) na żądaniach dotyczących klucza partycji logicznej. Użyj [znormalizowanej metryki zużycia ru](monitor-normalized-request-units.md) , aby sprawdzić, czy obciążenie napotyka partycję gorącą. 

#### <a name="solution"></a>Rozwiązanie:
Wybierz dobry klucz partycji, który równomiernie dystrybuuje wolumin żądania i magazyn. Dowiedz się, jak [zmienić klucz partycji](https://devblogs.microsoft.com/cosmosdb/how-to-change-your-partition-key/).

## <a name="next-steps"></a>Następne kroki
* [Diagnozowanie i rozwiązywanie](troubleshoot-dot-net-sdk.md) problemów podczas korzystania z zestawu SDK Azure Cosmos DB platformy .NET.
* Poznaj wskazówki dotyczące wydajności dla [platform .NET v3](performance-tips-dotnet-sdk-v3-sql.md) i [.NET V2](performance-tips.md).