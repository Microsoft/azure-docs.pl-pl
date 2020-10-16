---
title: Wyzwalacz usługi Azure Blob Storage i powiązania dla Azure Functions
description: Dowiedz się, jak używać wyzwalacza i powiązań usługi Azure Blob Storage w Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.openlocfilehash: e56d1add36d4296526348d12d7c0b6eb03108f27
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2020
ms.locfileid: "92104363"
---
# <a name="azure-blob-storage-bindings-for-azure-functions-overview"></a>Powiązania magazynu obiektów blob platformy Azure dla Azure Functions — Omówienie

Azure Functions integruje się z usługą [Azure Storage](../storage/index.yml) za pośrednictwem [wyzwalaczy i powiązań](./functions-triggers-bindings.md). Integracja z usługą BLOB Storage umożliwia tworzenie funkcji, które reagują na zmiany w danych obiektów blob, a także wartości odczytu i zapisu.

| Akcja | Typ |
|---------|---------|
| Uruchom funkcję jako zmiany danych magazynu obiektów BLOB | [Wyzwalacz](./functions-bindings-storage-blob-trigger.md) |
| Odczytywanie danych z magazynu obiektów BLOB w funkcji | [Powiązanie danych wejściowych](./functions-bindings-storage-blob-input.md) |
| Zezwalaj funkcji na zapisywanie danych magazynu obiektów BLOB |[Powiązanie danych wyjściowych](./functions-bindings-storage-blob-output.md) |

## <a name="add-to-your-functions-app"></a>Dodawanie do aplikacji funkcji

### <a name="functions-2x-and-higher"></a>Funkcje w wersji 2.x i nowszych

Praca z wyzwalaczem i powiązaniami wymaga odwołania do odpowiedniego pakietu. Pakiet NuGet jest używany dla bibliotek klas .NET, podczas gdy pakiet rozszerzeń jest używany dla wszystkich innych typów aplikacji.

| Język                                        | Dodaj przez...                                   | Uwagi 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Instalowanie [pakietu NuGet], wersja 3. x | |
| Skrypt C#, Java, JavaScript, Python, PowerShell | Rejestrowanie [pakietu rozszerzeń]          | [Rozszerzenie narzędzi platformy Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) jest zalecane do użycia z Visual Studio Code. |
| Skrypt C# (tylko w trybie online w Azure Portal)         | Dodawanie powiązania                            | Aby zaktualizować istniejące rozszerzenia powiązań bez konieczności ponownego publikowania aplikacji funkcji, zobacz [Aktualizowanie rozszerzeń]. |

[core tools]: ./functions-run-local.md
[pakiet rozszerzeń]: ./functions-bindings-register.md#extension-bundles
[Pakiet NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage
[Aktualizowanie rozszerzeń]: ./functions-bindings-register.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions w wersji 1.x

Aplikacje Functions 1. x automatycznie mają odwołanie do pakietu NuGet [Microsoft. Azure. WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) w wersji 2. x.

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="next-steps"></a>Następne kroki

- [Uruchom funkcję, gdy zmieniają się dane magazynu obiektów BLOB](./functions-bindings-storage-blob-trigger.md)
- [Odczytaj dane magazynu obiektów blob, gdy działa funkcja](./functions-bindings-storage-blob-input.md)
- [Zapisywanie danych usługi BLOB Storage z funkcji](./functions-bindings-storage-blob-output.md)