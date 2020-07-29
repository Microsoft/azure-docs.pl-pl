---
title: Rozwiązywanie problemów z częstotliwością żądań Azure Cosmos DB do dużego wyjątku
description: Jak zdiagnozować i naprawić częstotliwość żądań do dużego wyjątku
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: b20ee2e10648d2d8eb248fc3f98c4fb33805f914
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87294271"
---
# <a name="diagnose-and-troubleshoot-cosmos-db-too-many-requests"></a>Diagnozowanie i rozwiązywanie problemów Cosmos DB zbyt wiele żądań
"Częstotliwość żądań zbyt duża" lub kod błędu 429 wskazuje, że Twoje żądania są ograniczone.

## <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów
Poniższa lista zawiera znane przyczyny i rozwiązania zbyt wiele żądań.

### <a name="1-check-the-metrics"></a>1. Sprawdź metryki
Klient powinien sprawdzić [Azure Cosmos DB monitorowania](monitor-cosmos-db.md) , aby sprawdzić, czy liczba wyjątków 429.

## <a name="cause"></a>Przyczyna:
Wykorzystana przepływność (RU/s) przekroczyła [zainicjowaną przepływność](set-throughput.md). Zestaw SDK będzie automatycznie ponawiać próbę żądania na podstawie określonych zasad ponawiania. Jeśli ten błąd występuje często, należy rozważyć zwiększenie przepływności kolekcji. Sprawdź metryki portalu, aby sprawdzić, czy są wyświetlane błędy 429. Przejrzyj klucz partycji, aby upewnić się, że jest to [równomierny rozkład magazynu i woluminu żądania](partition-data.md).

## <a name="solution"></a>Rozwiązanie:
1. Użyj [portalu lub zestawu SDK](set-throughput.md) , aby zwiększyć przepływność aprowizacji.
2. Przełącz bazę danych lub kontener na [Automatyczne skalowanie](provision-throughput-autoscale.md).

## <a name="next-steps"></a>Następne kroki
* [Diagnozowanie i rozwiązywanie](troubleshoot-dot-net-sdk.md) problemów podczas korzystania z zestawu SDK Azure Cosmos DB .NET
* Informacje o wskazówkach dotyczących wydajności dla [platform .NET v3](performance-tips-dotnet-sdk-v3-sql.md) i [.NET V2](performance-tips.md)