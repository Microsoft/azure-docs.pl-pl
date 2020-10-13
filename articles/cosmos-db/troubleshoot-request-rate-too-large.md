---
title: Rozwiązywanie problemów z zbyt dużymi wyjątkami Azure Cosmos DB
description: Dowiedz się, jak zdiagnozować i naprawić częstotliwość żądań za duże wyjątki.
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: f8b1129c94ecf80efb60a13a0b80b1cc1817ff3f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88871092"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-request-rate-too-large-exceptions"></a>Diagnozowanie i rozwiązywanie problemów Azure Cosmos DB częstotliwość żądań za duże wyjątki
Komunikat "częstotliwość żądań zbyt duża" lub kod błędu 429 wskazuje, że Twoje żądania są ograniczone.

## <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów
Poniższa sekcja zawiera znane przyczyny i rozwiązania zbyt wiele żądań.

### <a name="check-the-metrics"></a>Sprawdź metryki
Sprawdź [Azure Cosmos DB monitorowania](monitor-cosmos-db.md) , aby zobaczyć liczbę wyjątków 429.

#### <a name="cause"></a>Przyczyna:
Wykorzystana przepływność (liczba jednostek żądań na sekundę) przekroczyła [zainicjowaną przepływność](set-throughput.md). Zestaw SDK automatycznie ponawia żądania na podstawie określonych zasad ponawiania. Jeśli ten błąd występuje często, należy rozważyć zwiększenie przepływności kolekcji. Sprawdź metryki portalu, aby sprawdzić, czy są wyświetlane błędy 429. Przejrzyj klucz partycji, aby upewnić się, że jest to [równomierny rozkład magazynu i woluminu żądania](partition-data.md).

#### <a name="solution"></a>Rozwiązanie:
1. Użyj [portalu lub zestawu SDK](set-throughput.md) , aby zwiększyć przepływność aprowizacji.
1. Przełącz bazę danych lub kontener na [Automatyczne skalowanie](provision-throughput-autoscale.md).

## <a name="next-steps"></a>Następne kroki
* [Diagnozowanie i rozwiązywanie](troubleshoot-dot-net-sdk.md) problemów podczas korzystania z zestawu SDK Azure Cosmos DB platformy .NET.
* Poznaj wskazówki dotyczące wydajności dla [platform .NET v3](performance-tips-dotnet-sdk-v3-sql.md) i [.NET V2](performance-tips.md).