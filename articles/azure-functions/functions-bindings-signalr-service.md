---
title: Powiązania usługi Azure Functions sygnalizującego
description: Dowiedz się, jak używać powiązań usługi sygnałów z Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/28/2019
ms.author: cshoe
ms.openlocfilehash: 9e60fd9a20720d75f96a0b78ee783bd5509a8f90
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97763493"
---
# <a name="signalr-service-bindings-for-azure-functions"></a>Powiązania usługi SignalR Service dla usługi Azure Functions

Ten zestaw artykułów wyjaśnia, jak uwierzytelniać i wysyłać komunikaty w czasie rzeczywistym do klientów podłączonych do [usługi Azure Signal Service](https://azure.microsoft.com/services/signalr-service/) przy użyciu powiązań usługi sygnałów w Azure Functions. Usługa Azure Functions obsługuje powiązania wejściowe i wyjściowe dla usługi SignalR Service.

| Akcja | Typ |
|---------|---------|
| Obsługa komunikatów z usługi sygnalizującego | [Wyzwalanie powiązania](./functions-bindings-signalr-service-trigger.md) |
| Zwróć adres URL punktu końcowego usługi i token dostępu | [Powiązanie danych wejściowych](./functions-bindings-signalr-service-input.md) |
| Wysyłanie komunikatów usługi sygnalizującego |[Powiązanie danych wyjściowych](./functions-bindings-signalr-service-output.md) |

## <a name="add-to-your-functions-app"></a>Dodawanie do aplikacji funkcji

### <a name="functions-2x-and-higher"></a>Funkcje w wersji 2.x i nowszych

Praca z wyzwalaczem i powiązaniami wymaga odwołania do odpowiedniego pakietu. Pakiet NuGet jest używany dla bibliotek klas .NET, podczas gdy pakiet rozszerzeń jest używany dla wszystkich innych typów aplikacji.

| Język                                        | Dodaj przez...                                   | Uwagi 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Instalowanie [pakietu NuGet], wersja 3. x | |
| Skrypt C#, Java, JavaScript, Python, PowerShell | Rejestrowanie [pakietu rozszerzeń]          | [Rozszerzenie narzędzi platformy Azure] jest zalecane do użycia z Visual Studio Code. |
| Skrypt C# (tylko w trybie online w Azure Portal)         | Dodawanie powiązania                            | Aby zaktualizować istniejące rozszerzenia powiązań bez konieczności ponownego publikowania aplikacji funkcji, zobacz [Aktualizowanie rozszerzeń]. |

[Pakiet NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SignalRService
[core tools]: ./functions-run-local.md
[pakiet rozszerzeń]: ./functions-bindings-register.md#extension-bundles
[Aktualizowanie rozszerzeń]: ./functions-bindings-register.md
[Rozszerzenie narzędzi platformy Azure]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

Aby uzyskać szczegółowe informacje na temat konfigurowania i używania usługi sygnalizującego oraz Azure Functions ze sobą, zobacz [Azure Functions programowanie i Konfigurowanie za pomocą usługi Azure Signal Service](../azure-signalr/signalr-concept-serverless-development-config.md).

### <a name="annotations-library-java-only"></a>Biblioteka adnotacji (tylko w języku Java)

Aby użyć adnotacji usługi sygnalizującej w funkcjach języka Java, należy dodać zależność do artefaktu *platformy Azure-Functions-Java-library* (wersja 1,0 lub nowsza) do pliku *pom.xml* .

```xml
<dependency>
    <groupId>com.microsoft.azure.functions</groupId>
    <artifactId>azure-functions-java-library-signalr</artifactId>
    <version>1.0.0</version>
</dependency>
```

## <a name="next-steps"></a>Następne kroki

- [Obsługa komunikatów z usługi sygnalizującego (powiązanie wyzwalacza)](./functions-bindings-signalr-service-trigger.md)
- [Zwróć adres URL punktu końcowego usługi i token dostępu (powiązanie wejściowe)](./functions-bindings-signalr-service-input.md)
- [Komunikaty usługi wysyłania sygnałów (powiązanie danych wyjściowych)](./functions-bindings-signalr-service-output.md)