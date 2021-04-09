---
author: craigshoemaker
ms.service: azure-functions
ms.topic: include
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: 05d136093bd509e8c23ce8622423216326b0f1f2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102623399"
---
## <a name="add-to-your-functions-app"></a>Dodawanie do aplikacji funkcji

### <a name="functions-2x-and-higher"></a>Funkcje w wersji 2.x i nowszych

Praca z wyzwalaczem i powiązaniami wymaga odwołania do odpowiedniego pakietu. Pakiet NuGet jest używany dla bibliotek klas .NET, podczas gdy pakiet rozszerzeń jest używany dla wszystkich innych typów aplikacji.

| Język                                        | Dodaj przez...                                   | Uwagi 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Instalowanie [pakietu NuGet], wersja 3. x | |
| Skrypt C#, Java, JavaScript, Python, PowerShell | Rejestrowanie [pakietu rozszerzeń]          | [Rozszerzenie narzędzi platformy Azure] jest zalecane do użycia z Visual Studio Code. |
| Skrypt C# (tylko w trybie online w Azure Portal)         | Dodawanie powiązania                            | Aby zaktualizować istniejące rozszerzenia powiązań bez konieczności ponownego publikowania aplikacji funkcji, zobacz [Aktualizowanie rozszerzeń]. |

[Pakiet NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs
[core tools]: ../articles/azure-functions/functions-run-local.md
[pakiet rozszerzeń]: ../articles/azure-functions/functions-bindings-register.md#extension-bundles
[Aktualizowanie rozszerzeń]: ../articles/azure-functions/functions-bindings-register.md
[Rozszerzenie narzędzi platformy Azure]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="event-hubs-extension-5x-and-higher"></a>Event Hubs rozszerzenie 5. x i nowsze

Nowa wersja rozszerzenia powiązania Event Hubs jest dostępna jako [pakiet NuGet wersji zapoznawczej](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs/5.0.0-beta.1). W tej wersji zapoznawczej wprowadzono możliwość [nawiązywania połączenia przy użyciu tożsamości zamiast wpisu tajnego](../articles/azure-functions/functions-reference.md#configure-an-identity-based-connection). W przypadku aplikacji .NET zmienia również typy, do których można utworzyć powiązanie, zastępując typy z `Microsoft.Azure.EventHubs` nowszymi typami z [platformy Azure. Messaging. EventHubs](/dotnet/api/azure.messaging.eventhubs).

> [!NOTE]
> Pakiet w wersji zapoznawczej nie jest uwzględniony w pakiecie rozszerzenia i musi być zainstalowany ręcznie. W przypadku aplikacji .NET Dodaj odwołanie do pakietu. W przypadku wszystkich innych typów aplikacji zobacz [Aktualizowanie rozszerzeń].

[core tools]: ./functions-run-local.md
[pakiet rozszerzeń]: ./functions-bindings-register.md#extension-bundles
[Pakiet NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs/
[Aktualizowanie rozszerzeń]: ./functions-bindings-register.md
[Rozszerzenie narzędzi platformy Azure]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions w wersji 1.x

Aplikacje Functions 1. x automatycznie mają odwołanie do pakietu NuGet [Microsoft. Azure. WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) w wersji 2. x.

## <a name="hostjson-settings"></a>host.jsustawień
<a name="host-json"></a>

[host.jsw](../articles/azure-functions/functions-host-json.md#eventhub) pliku zawiera ustawienia sterujące zachowaniem wyzwalacza Event Hubs. Konfiguracja różni się w zależności od wersji Azure Functions.

[!INCLUDE [functions-host-json-event-hubs](../articles/azure-functions/../../includes/functions-host-json-event-hubs.md)]