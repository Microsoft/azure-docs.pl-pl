---
title: Jak skonfigurować czas rozpoczęcia procesora dla kanału informacyjnego zmiany Azure Cosmos DB
description: Dowiedz się, jak skonfigurować procesor kanału informacyjnego w celu rozpoczęcia odczytywania z określonego dnia i godziny
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/13/2019
ms.author: maquaran
ms.openlocfilehash: 600556a06d3f58c4d2ec79a49fdee5e8e04d4036
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77586278"
---
# <a name="how-to-configure-the-change-feed-processor-start-time"></a>Jak skonfigurować czas rozpoczęcia procesora kanału informacyjnego zmiany

W tym artykule opisano sposób konfigurowania [procesora źródła zmian](./change-feed-processor.md) w celu rozpoczęcia odczytywania z określonego dnia i godziny.

## <a name="default-behavior"></a>Zachowanie domyślne

Gdy procesor źródła zmian zostanie uruchomiony po raz pierwszy, zostanie zainicjowany kontener dzierżawy i rozpocznie [cykl życia przetwarzania](./change-feed-processor.md#processing-life-cycle). Wszelkie zmiany, które wystąpiły w kontenerze przed zainicjowaniem procesora źródła zmian po raz pierwszy nie zostaną wykryte.

## <a name="reading-from-a-previous-date-and-time"></a>Odczytywanie od podanej daty i godziny

Możliwe jest zainicjowanie procesora kanału informacyjnego, aby odczytywać zmiany, rozpoczynając od **określonej daty i godziny**, przekazując wystąpienie `DateTime` do rozszerzenia `WithStartTime` konstruktora:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=TimeInitialization)]

Procesor kanału informacyjnego zmian zostanie zainicjowany dla tej konkretnej daty i godziny i rozpocznie się odczytywanie zmian, które wystąpiły po.

## <a name="reading-from-the-beginning"></a>Odczytywanie od początku

W innych scenariuszach, takich jak migracja danych lub analizowanie całej historii kontenera, musimy przeczytać Źródło zmian od **początku okresu istnienia tego kontenera**. Aby to zrobić, można użyć `WithStartTime` na rozszerzeniu konstruktora, ale przekazywać `DateTime.MinValue.ToUniversalTime()`, które generują reprezentację UTC wartości minimalnej `DateTime` , na przykład:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=StartFromBeginningInitialization)]

Procesor kanału informacyjnego zmian zostanie zainicjowany i rozpocznie się odczytywanie zmian od początku okresu istnienia kontenera.

> [!NOTE]
> Te opcje dostosowania działają tylko w celu skonfigurowania punktu początkowego w czasie procesora źródła zmian. Po zainicjowaniu kontenera dzierżaw po raz pierwszy zmiana nie ma żadnego wpływu.

> [!NOTE]
> Podczas określania punktu w czasie odczytywane będą tylko zmiany elementów, które aktualnie istnieją w kontenerze. Jeśli element został usunięty, jego historia w kanale zmian również zostanie usunięta.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zestaw SDK Azure Cosmos DB](sql-api-sdk-dotnet.md)
* [Przykłady użycia w witrynie GitHub](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [Dodatkowe przykłady w witrynie GitHub](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>Następne kroki

Teraz można dowiedzieć się więcej o procesorze źródła zmian w następujących artykułach:

* [Omówienie procesora kanału informacyjnego zmiany](change-feed-processor.md)
* [Korzystanie z estymatora zestawienia zmian](how-to-use-change-feed-estimator.md)
