---
title: Wyzwalacz usługi Azure queue storage i powiązania dla Azure Functions — Omówienie
description: Zapoznaj się z tematem jak używać wyzwalacza usługi Azure queue storage i powiązania danych wyjściowych w Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2020
ms.author: cshoe
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: a1b9d03da29b7c89055303fa97fc38c2ef734b23
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100381481"
---
# <a name="azure-queue-storage-trigger-and-bindings-for-azure-functions-overview"></a>Wyzwalacz usługi Azure queue storage i powiązania dla Azure Functions — Omówienie

Azure Functions mogą działać jako nowe wiadomości usługi Azure queue storage są tworzone i mogą zapisywać komunikaty w kolejce w ramach funkcji.

| Akcja | Typ |
|---------|---------|
| Uruchamianie funkcji jako kolejki zmian danych | [Wyzwalacz](./functions-bindings-storage-queue-trigger.md) |
| Komunikaty magazynu kolejki zapisu |[Powiązanie danych wyjściowych](./functions-bindings-storage-queue-output.md) |

## <a name="add-to-your-functions-app"></a>Dodawanie do aplikacji funkcji

### <a name="functions-2x-and-higher"></a>Funkcje w wersji 2.x i nowszych

Praca z wyzwalaczem i powiązaniami wymaga odwołania do odpowiedniego pakietu. Pakiet NuGet jest używany dla bibliotek klas .NET, podczas gdy pakiet rozszerzeń jest używany dla wszystkich innych typów aplikacji.

| Język                                        | Dodaj przez...                                   | Uwagi 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Instalowanie [pakietu NuGet], wersja 3. x | |
| Skrypt C#, Java, JavaScript, Python, PowerShell | Rejestrowanie [pakietu rozszerzeń]          | [Rozszerzenie narzędzi platformy Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) jest zalecane do użycia z Visual Studio Code. |
| Skrypt C# (tylko w trybie online w Azure Portal)         | Dodawanie powiązania                            | Aby zaktualizować istniejące rozszerzenia powiązań bez konieczności ponownego publikowania aplikacji funkcji, zobacz [Aktualizowanie rozszerzeń]. |

#### <a name="storage-extension-5x-and-higher"></a>Rozszerzenie magazynu 5. x i nowsze

Nowa wersja rozszerzenia powiązań magazynu jest dostępna jako [pakiet NuGet wersji zapoznawczej](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage/5.0.0-beta.2). W tej wersji zapoznawczej wprowadzono możliwość [nawiązywania połączenia przy użyciu tożsamości zamiast wpisu tajnego](./functions-reference.md#configure-an-identity-based-connection). W przypadku aplikacji .NET zmienia również typy, do których można powiązać, zastępując typy z `WindowsAzure.Storage` i `Microsoft.Azure.Storage` z nowszymi typami z [usługi Azure. Storage. Queues](/dotnet/api/azure.storage.queues).

> [!NOTE]
> Pakiet w wersji zapoznawczej nie jest uwzględniony w pakiecie rozszerzenia i musi być zainstalowany ręcznie. W przypadku aplikacji .NET Dodaj odwołanie do pakietu. W przypadku wszystkich innych typów aplikacji zobacz [Aktualizowanie rozszerzeń].

[core tools]: ./functions-run-local.md
[pakiet rozszerzeń]: ./functions-bindings-register.md#extension-bundles
[Pakiet NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage
[Aktualizowanie rozszerzeń]: ./functions-bindings-register.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions w wersji 1.x

Aplikacje Functions 1. x automatycznie mają odwołanie do pakietu NuGet [Microsoft. Azure. WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) w wersji 2. x.

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>host.jsustawień

W tej sekcji opisano globalne ustawienia konfiguracji dostępne dla tego powiązania w wersji 2. x i nowszych. Przykład *host.jsna* poniższym pliku zawiera tylko ustawienia wersji 2. x dla tego powiązania. Aby uzyskać więcej informacji na temat ustawień konfiguracji globalnej w wersjach 2. x i więcej, zobacz [host.json Reference for Azure Functions](functions-host-json.md).

> [!NOTE]
> Aby uzyskać informacje na temat host.jsw funkcjach 1. x, zobacz [host.json Reference for Azure Functions 1. x](functions-host-json-v1.md).

```json
{
    "version": "2.0",
    "extensions": {
        "queues": {
            "maxPollingInterval": "00:00:02",
            "visibilityTimeout" : "00:00:30",
            "batchSize": 16,
            "maxDequeueCount": 5,
            "newBatchThreshold": 8,
            "messageEncoding": "base64"
        }
    }
}
```

|Właściwość  |Domyślne | Opis |
|---------|---------|---------|
|maxPollingInterval|00:00:01|Maksymalny interwał między sondami kolejki. Wartość minimalna to 00:00:00.100 (100 ms) i zwiększa się do 00:01:00 (1 min).  W funkcjach 2. x i wyższych typ danych to `TimeSpan` , w wersji 1. x, jest w milisekundach.|
|visibilityTimeout|00:00:00|Przedział czasu między ponownymi próbami, gdy przetwarzanie komunikatu kończy się niepowodzeniem. |
|batchSize|16|Liczba komunikatów w kolejce, które środowisko uruchomieniowe funkcji jednocześnie pobiera i przetwarza równolegle. Gdy przetwarzana liczba jest w dół `newBatchThreshold` , środowisko uruchomieniowe pobiera kolejną partię i uruchamia przetwarzanie tych komunikatów. Dlatego Maksymalna liczba współbieżnych komunikatów przetwarzanych na funkcję to `batchSize` Plus `newBatchThreshold` . Ten limit dotyczy osobno każdej funkcji wyzwalanej przez kolejkę. <br><br>Aby zapobiec równoległemu wykonywaniu komunikatów odebranych w jednej kolejce, można ustawić wartość `batchSize` 1. To ustawienie eliminuje jednak współbieżność, o ile aplikacja funkcji działa tylko na jednej maszynie wirtualnej. Jeśli aplikacja funkcji jest skalowana do wielu maszyn wirtualnych, każda maszyna wirtualna może uruchomić jedno wystąpienie każdej funkcji wyzwalanej przez kolejkę.<br><br>Wartość maksymalna `batchSize` to 32. |
|maxDequeueCount|5|Liczba prób przetworzenia komunikatu przed przeniesieniem go do kolejki trującej.|
|newBatchThreshold|batchSize/2|Gdy liczba przetwarzanych komunikatów współbieżnie przyjdzie do tego numeru, środowisko uruchomieniowe pobiera kolejną partię.|
|messageEncoding|base64| To ustawienie jest dostępne tylko w [rozszerzeniu w wersji 5.0.0 lub nowszej](#storage-extension-5x-and-higher). Reprezentuje format kodowania komunikatów. Prawidłowe wartości to `base64` i `none` .|

## <a name="next-steps"></a>Następne kroki

- [Uruchamianie funkcji jako kolejki zmian danych magazynu (wyzwalacz)](./functions-bindings-storage-queue-trigger.md)
- [Komunikaty magazynu kolejki zapisu (powiązanie danych wyjściowych)](./functions-bindings-storage-queue-output.md)
