---
title: Wyzwalacze i powiązania HTTP usługi Azure Functions
description: Dowiedz się, jak używać wyzwalaczy i powiązań HTTP w Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.openlocfilehash: 29b5e9c7673b4a730a41bf7cf2b1c4a2a86209ed
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "77462109"
---
# <a name="azure-functions-http-triggers-and-bindings-overview"></a>Omówienie wyzwalaczy i powiązań HTTP Azure Functions

Azure Functions mogą być wywoływane przez żądania HTTP, aby kompilować bezserwerowe interfejsy API i odpowiadać na elementy [webhook](https://en.wikipedia.org/wiki/Webhook).

| Akcja | Typ |
|---------|---------|
| Uruchamianie funkcji z żądania HTTP | [Wyzwalacz](./functions-bindings-http-webhook-trigger.md) |
| Zwracanie odpowiedzi HTTP z funkcji |[Powiązanie danych wyjściowych](./functions-bindings-http-webhook-output.md) |

Kod w tym artykule jest domyślnie używany w przypadku składni .NET Core używanej w funkcjach w wersji 2. x i nowszych. Informacje na temat składni 1. x można znaleźć w [szablonach funkcji 1. x](https://github.com/Azure/azure-functions-templates/tree/v1.x/Functions.Templates/Templates).

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
[Pakiet NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Http
[Aktualizowanie rozszerzeń]: ./install-update-binding-extensions-manual.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions w wersji 1.x

Aplikacje Functions 1. x automatycznie mają odwołanie do pakietu NuGet [Microsoft. Azure. WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) w wersji 2. x.

## <a name="next-steps"></a>Następne kroki

- [Uruchamianie funkcji z żądania HTTP](./functions-bindings-http-webhook-trigger.md)
- [Zwracanie odpowiedzi HTTP z funkcji](./functions-bindings-http-webhook-output.md)
