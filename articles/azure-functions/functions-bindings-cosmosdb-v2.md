---
title: Powiązania Azure Cosmos DB dla funkcji 2. x
description: Dowiedz się, jak używać wyzwalaczy Azure Cosmos DB i powiązań w programie Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/24/2017
ms.author: cshoe
ms.openlocfilehash: f258a7aff52796a53540706bc8413575d63c9e7d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77605756"
---
# <a name="azure-cosmos-db-trigger-and-bindings-for-azure-functions-2x-overview"></a>Azure Cosmos DB wyzwalacz i powiązania dla Azure Functions 2. x — Omówienie

> [!div class="op_single_selector" title1="Wybierz wersję środowiska uruchomieniowego Azure Functions, którego używasz: "]
> * [Wersja 1](functions-bindings-cosmosdb.md)
> * [Wersja 2](functions-bindings-cosmosdb-v2.md)

Ten zestaw artykułów wyjaśnia sposób pracy z powiązaniami [Azure Cosmos DB](../cosmos-db/serverless-computing-database.md) w Azure Functions 2. x. Azure Functions obsługuje powiązania wyzwalacza, danych wejściowych i wyjściowych dla Azure Cosmos DB.

| Akcja | Typ |
|---------|---------|
| Uruchom funkcję w przypadku utworzenia lub zmodyfikowania dokumentu Azure Cosmos DB | [Wyzwalacz](./functions-bindings-cosmosdb-v2-trigger.md) |
| Odczytaj dokument Azure Cosmos DB | [Powiązanie danych wejściowych](./functions-bindings-cosmosdb-v2-input.md) |
| Zapisz zmiany w dokumencie Azure Cosmos DB  |[Powiązanie danych wyjściowych](./functions-bindings-cosmosdb-v2-output.md) |

> [!NOTE]
> To odwołanie dotyczy [Azure Functions wersji 2. x](functions-versions.md).  Aby uzyskać informacje o sposobach używania tych powiązań w funkcjach 1. x, zobacz [Azure Cosmos DB powiązania dla Azure Functions 1. x](functions-bindings-cosmosdb.md).
>
> To powiązanie było pierwotnie nazwane DocumentDB. W funkcjach w wersji 2. x wyzwalacz, powiązania i pakiet mają nazwę Cosmos DB.

## <a name="supported-apis"></a>Obsługiwane interfejsy API

[!INCLUDE [SQL API support only](../../includes/functions-cosmosdb-sqlapi-note.md)]

## <a name="add-to-your-functions-app"></a>Dodawanie do aplikacji funkcji

### <a name="functions-2x-and-higher"></a>Funkcje 2. x i nowsze

Praca z wyzwalaczem i powiązaniami wymaga odwołania do odpowiedniego pakietu. Pakiet NuGet jest używany dla bibliotek klas .NET, podczas gdy pakiet rozszerzeń jest używany dla wszystkich innych typów aplikacji.

| Język                                        | Dodaj przez...                                   | Uwagi 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Instalowanie [pakietu NuGet], wersja 3. x | |
| Skrypt C#, Java, JavaScript, Python, PowerShell | Rejestrowanie [pakietu rozszerzeń]          | [Rozszerzenie narzędzi platformy Azure] jest zalecane do użycia z Visual Studio Code. |
| Skrypt C# (tylko w trybie online w Azure Portal)         | Dodawanie powiązania                            | Aby zaktualizować istniejące rozszerzenia powiązań bez konieczności ponownego publikowania aplikacji funkcji, zobacz [Aktualizowanie rozszerzeń]. |

[Pakiet NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB
[core tools]: ./functions-run-local.md
[pakiet rozszerzeń]: ./functions-bindings-register.md#extension-bundles
[Aktualizowanie rozszerzeń]: ./install-update-binding-extensions-manual.md
[Rozszerzenie narzędzi platformy Azure]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions w wersji 1.x

Aplikacje Functions 1. x automatycznie mają odwołanie do pakietu NuGet [Microsoft. Azure. WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) w wersji 2. x.

## <a name="next-steps"></a>Następne kroki

- [Uruchom funkcję w przypadku utworzenia lub zmodyfikowania dokumentu Azure Cosmos DB (wyzwalacz)](./functions-bindings-cosmosdb-v2-trigger.md)
- [Odczytaj dokument Azure Cosmos DB (powiązanie danych wejściowych)](./functions-bindings-cosmosdb-v2-input.md)
- [Zapisz zmiany w dokumencie Azure Cosmos DB (powiązanie danych wyjściowych)](./functions-bindings-cosmosdb-v2-output.md)
