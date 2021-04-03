---
title: Azure Event Grid powiązania Azure Functions
description: Informacje na temat obsługi zdarzeń Event Grid w Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.custom: fasttrack-edit
ms.openlocfilehash: 115ff1b59ecbe3f4fdb089c2bd61da955fba3984
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92104550"
---
# <a name="azure-event-grid-bindings-for-azure-functions"></a>Azure Event Grid powiązania Azure Functions

W tym temacie wyjaśniono sposób obsługi zdarzeń [Event Grid](../event-grid/overview.md) w Azure Functions. Aby uzyskać szczegółowe informacje na temat obsługi komunikatów Event Grid w punkcie końcowym HTTP, zobacz [odbieranie zdarzeń do punktu końcowego http](../event-grid/receive-events.md).

Event Grid to usługa platformy Azure, która wysyła żądania HTTP w celu powiadomienia o zdarzeniach, które wystąpiły w *wydawcach*. Wydawca jest usługą lub zasobem, który pochodzi ze zdarzenia. Na przykład konto usługi Azure Blob Storage jest wydawcą, a [przekazywanie lub usuwanie obiektów BLOB jest zdarzeniem](../storage/blobs/storage-blob-event-overview.md). Niektóre [usługi platformy Azure mają wbudowaną obsługę publikowania zdarzeń do Event Grid](../event-grid/overview.md#event-sources).

*Programy obsługi* zdarzeń odbierają i przetwarzają zdarzenia. Azure Functions to jedna z kilku [usług platformy Azure, która ma wbudowaną obsługę obsługi zdarzeń Event Grid](../event-grid/overview.md#event-handlers). W tym odwołaniu nauczysz się używać wyzwalacza Event Grid do wywołania funkcji, gdy zdarzenie zostanie odebrane z Event Grid, i aby użyć powiązania danych wyjściowych do wysyłania zdarzeń do [Event Gridego tematu niestandardowego](../event-grid/post-to-custom-topic.md).

Jeśli wolisz, możesz użyć wyzwalacza HTTP do obsługi zdarzeń Event Grid; Zobacz [odbieranie zdarzeń do punktu końcowego http](../event-grid/receive-events.md). Obecnie nie można używać wyzwalacza Event Grid dla aplikacji Azure Functions, gdy zdarzenie jest dostarczane w [schemacie CloudEvents](../event-grid/cloudevents-schema.md#azure-functions). Zamiast tego należy użyć wyzwalacza HTTP.

| Akcja | Typ |
|---------|---------|
| Uruchom funkcję po wysłaniu zdarzenia Event Grid | [Wyzwalacz](./functions-bindings-event-grid-trigger.md) |
| Wysyła Zdarzenie Event Grid |[Powiązanie danych wyjściowych](./functions-bindings-event-grid-output.md) |

Kod w tym odwołaniu domyślnie określa składnię .NET Core, używaną w funkcjach w wersji 2. x i wyższych. Informacje na temat składni 1. x można znaleźć w [szablonach funkcji 1. x](https://github.com/Azure/azure-functions-templates/tree/v1.x/Functions.Templates/Templates).

## <a name="add-to-your-functions-app"></a>Dodawanie do aplikacji funkcji

### <a name="functions-2x-and-higher"></a>Funkcje w wersji 2.x i nowszych

Praca z wyzwalaczem i powiązaniami wymaga odwołania do odpowiedniego pakietu. Pakiet NuGet jest używany dla bibliotek klas .NET, podczas gdy pakiet rozszerzeń jest używany dla wszystkich innych typów aplikacji.

| Język                                        | Dodaj przez...                                   | Uwagi 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Instalowanie [pakietu NuGet]w wersji 2. x | |
| Skrypt C#, Java, JavaScript, Python, PowerShell | Rejestrowanie [pakietu rozszerzeń]          | [Rozszerzenie narzędzi platformy Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) jest zalecane do użycia z Visual Studio Code. |
| Skrypt C# (tylko w trybie online w Azure Portal)         | Dodawanie powiązania                            | Aby zaktualizować istniejące rozszerzenia powiązań bez konieczności ponownego publikowania aplikacji funkcji, zobacz [Aktualizowanie rozszerzeń]. |

[core tools]: ./functions-run-local.md
[pakiet rozszerzeń]: ./functions-bindings-register.md#extension-bundles
[Pakiet NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid
[Aktualizowanie rozszerzeń]: ./functions-bindings-register.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions w wersji 1.x

Aplikacje Functions 1. x automatycznie mają odwołanie do pakietu NuGet [Microsoft. Azure. WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) w wersji 2. x.

## <a name="next-steps"></a>Następne kroki
* [Uruchom funkcję po wysłaniu zdarzenia Event Grid](./functions-bindings-event-grid-trigger.md)
* [Wysyłanie zdarzenia Event Grid](./functions-bindings-event-grid-trigger.md)