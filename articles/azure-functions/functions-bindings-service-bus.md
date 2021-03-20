---
title: Azure Service Bus powiązania Azure Functions
description: Dowiedz się, jak wysyłać wyzwalacze i powiązania Azure Service Bus w Azure Functions.
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 02/19/2020
ms.author: cshoe
ms.custom: fasttrack-edit
ms.openlocfilehash: b32f16d170df9963960862bc82aef1a4baf13896
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92104448"
---
# <a name="azure-service-bus-bindings-for-azure-functions"></a>Azure Service Bus powiązania Azure Functions

Azure Functions integruje się z [Azure Service Bus](https://azure.microsoft.com/services/service-bus) za pośrednictwem [wyzwalaczy i powiązań](./functions-triggers-bindings.md). Integracja z programem Service Bus umożliwia tworzenie funkcji, które reagują na i wysyłają komunikaty dotyczące kolejki lub tematu.

| Akcja | Typ |
|---------|---------|
| Uruchom funkcję po utworzeniu kolejki Service Bus lub komunikatu tematu | [Wyzwalacz](./functions-bindings-service-bus-trigger.md) |
| Wysyłanie komunikatów Azure Service Bus |[Powiązanie danych wyjściowych](./functions-bindings-service-bus-output.md) |

## <a name="add-to-your-functions-app"></a>Dodawanie do aplikacji funkcji

> [!NOTE]
> Powiązanie Service Bus nie obsługuje obecnie uwierzytelniania przy użyciu tożsamości zarządzanej. Zamiast tego należy użyć [Service Bus sygnatury dostępu współdzielonego](../service-bus-messaging/service-bus-authentication-and-authorization.md#shared-access-signature).

### <a name="functions-2x-and-higher"></a>Funkcje w wersji 2.x i nowszych

Praca z wyzwalaczem i powiązaniami wymaga odwołania do odpowiedniego pakietu. Pakiet NuGet jest używany dla bibliotek klas .NET, podczas gdy pakiet rozszerzeń jest używany dla wszystkich innych typów aplikacji.

| Język                                        | Dodaj przez...                                   | Uwagi 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Instalowanie [pakietu NuGet]w wersji 4. x | |
| Skrypt C#, Java, JavaScript, Python, PowerShell | Rejestrowanie [pakietu rozszerzeń]          | [Rozszerzenie narzędzi platformy Azure] jest zalecane do użycia z Visual Studio Code. |
| Skrypt C# (tylko w trybie online w Azure Portal)         | Dodawanie powiązania                            | Aby zaktualizować istniejące rozszerzenia powiązań bez konieczności ponownego publikowania aplikacji funkcji, zobacz [Aktualizowanie rozszerzeń]. |

[Pakiet NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus/
[core tools]: ./functions-run-local.md
[pakiet rozszerzeń]: ./functions-bindings-register.md#extension-bundles
[Aktualizowanie rozszerzeń]: ./functions-bindings-register.md
[Rozszerzenie narzędzi platformy Azure]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions w wersji 1.x

Aplikacje Functions 1. x automatycznie mają odwołanie do pakietu NuGet [Microsoft. Azure. WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) w wersji 2. x.

## <a name="next-steps"></a>Następne kroki

- [Uruchom funkcję po utworzeniu kolejki Service Bus lub komunikatu tematu (Wyzwól)](./functions-bindings-service-bus-trigger.md)
- [Wysyłanie komunikatów Azure Service Bus z Azure Functions (powiązanie danych wyjściowych)](./functions-bindings-service-bus-output.md)