---
title: Powiązania usługi Azure RabbitMQ dla Azure Functions
description: Dowiedz się, jak wysyłać wyzwalacze i powiązania usługi Azure RabbitMQ w Azure Functions.
author: cachai2
ms.assetid: ''
ms.topic: reference
ms.date: 12/17/2020
ms.author: cachai
ms.custom: ''
ms.openlocfilehash: 2a480f25821f5022295b18ca24abfd2c0fb8a50c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97746528"
---
# <a name="rabbitmq-bindings-for-azure-functions-overview"></a>RabbitMQ powiązań Azure Functions przegląd

> [!NOTE]
> Powiązania RabbitMQ są w pełni obsługiwane tylko w planach **Premium i dedykowanych** . Użycie nie jest obsługiwane.

Azure Functions integruje się z usługą [RabbitMQ](https://www.rabbitmq.com/) za pośrednictwem [wyzwalaczy i powiązań](./functions-triggers-bindings.md). Azure Functions rozszerzenie RabbitMQ umożliwia wysyłanie i odbieranie komunikatów przy użyciu interfejsu API RabbitMQ z funkcjami.

| Akcja | Typ |
|---------|---------|
| Uruchom funkcję, gdy wiadomość RabbitMQ przechodzi przez kolejkę | [Wyzwalacz](./functions-bindings-rabbitmq-trigger.md) |
| Wysyłanie komunikatów RabbitMQ |[Powiązanie danych wyjściowych](./functions-bindings-rabbitmq-output.md) |

## <a name="add-to-your-functions-app"></a>Dodawanie do aplikacji funkcji

Aby rozpocząć tworzenie przy użyciu tego rozszerzenia, upewnij się, że najpierw [skonfigurowano punkt końcowy RabbitMQ](https://github.com/Azure/azure-functions-rabbitmq-extension/wiki/Setting-up-a-RabbitMQ-Endpoint). Aby dowiedzieć się więcej na temat RabbitMQ, zobacz [stronę wprowadzenie](https://www.rabbitmq.com/getstarted.html).

### <a name="functions-3x-and-higher"></a>Funkcje 3. x i nowsze

Praca z wyzwalaczem i powiązaniami wymaga odwołania do odpowiedniego pakietu. Pakiet NuGet jest używany dla bibliotek klas .NET, podczas gdy pakiet rozszerzeń jest używany dla wszystkich innych typów aplikacji.

| Język                                        | Dodaj przez...                                   | Uwagi
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Instalowanie [pakietu NuGet]w wersji 4. x | |
| Skrypt C#, Java, JavaScript, Python, PowerShell | Rejestrowanie [pakietu rozszerzeń]          | [Rozszerzenie narzędzi platformy Azure] jest zalecane do użycia z Visual Studio Code. |
| Skrypt C# (tylko w trybie online w Azure Portal)         | Dodawanie powiązania                            | Aby zaktualizować istniejące rozszerzenia powiązań bez konieczności ponownego publikowania aplikacji funkcji, zobacz [Aktualizowanie rozszerzeń]. |

[Pakiet NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.RabbitMQ
[core tools]: ./functions-run-local.md
[pakiet rozszerzeń]: ./functions-bindings-register.md#extension-bundles
[Aktualizowanie rozszerzeń]: ./functions-bindings-register.md
[Rozszerzenie narzędzi platformy Azure]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x-and-2x"></a>Funkcje 1. x i 2. x

Rozszerzenia powiązań RabbitMQ nie są obsługiwane w przypadku funkcji 1. x i 2. x. Użyj funkcji 3. x lub nowszej.

## <a name="next-steps"></a>Następne kroki

- [Uruchom funkcję po utworzeniu komunikatu RabbitMQ (Wyzwól)](./functions-bindings-rabbitmq-trigger.md)
- [Wysyłanie komunikatów RabbitMQ z Azure Functions (powiązanie danych wyjściowych)](./functions-bindings-rabbitmq-output.md)