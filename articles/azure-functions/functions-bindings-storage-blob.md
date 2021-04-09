---
title: Wyzwalacz usługi Azure Blob Storage i powiązania dla Azure Functions
description: Dowiedz się, jak używać wyzwalacza i powiązań usługi Azure Blob Storage w Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.openlocfilehash: 4ec21086ee94610be1d9cf5da7b64c837b5311a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100381532"
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

#### <a name="storage-extension-5x-and-higher"></a>Rozszerzenie magazynu 5. x i nowsze

Nowa wersja rozszerzenia powiązań magazynu jest dostępna jako [pakiet NuGet wersji zapoznawczej](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage/5.0.0-beta.2). W tej wersji zapoznawczej wprowadzono możliwość [nawiązywania połączenia przy użyciu tożsamości zamiast wpisu tajnego](./functions-reference.md#configure-an-identity-based-connection). W przypadku aplikacji .NET zmienia również typy, do których można utworzyć powiązanie, zastępując typy z `WindowsAzure.Storage` i `Microsoft.Azure.Storage` z nowszymi typami z [platformy Azure. Storage. blob](/dotnet/api/azure.storage.blobs).

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

## <a name="hostjson-settings"></a>host.jsustawień

> [!NOTE]
> Ta sekcja nie ma zastosowania w przypadku używania wersji rozszerzenia przed 5.0.0. Dla tych wersji nie istnieją globalne ustawienia konfiguracji dla obiektów BLOB.

W tej sekcji opisano globalne ustawienia konfiguracji dostępne dla tego powiązania w przypadku używania [rozszerzenia w wersji 5.0.0 lub nowszej](#storage-extension-5x-and-higher). Przykład *host.jsna* poniższym pliku zawiera tylko ustawienia wersji 2. x dla tego powiązania. Aby uzyskać więcej informacji na temat ustawień globalnej konfiguracji w funkcjach w wersji 2. x i więcej, zobacz [host.json Reference for Azure Functions](functions-host-json.md).

```json
{
    "version": "2.0",
    "extensions": {
        "blobs": {
            "maxDegreeOfParallelism": "4"
        }
    }
}
```

|Właściwość  |Domyślne | Opis |
|---------|---------|---------|
|maxDegreeOfParallelism|8 * (liczba dostępnych rdzeni)|Liczba całkowita liczby współbieżnych wywołań dozwolona dla każdej funkcji wyzwalanej przez obiekt BLOB. Minimalna dozwolona wartość to 1.|

## <a name="next-steps"></a>Następne kroki

- [Uruchom funkcję, gdy zmieniają się dane magazynu obiektów BLOB](./functions-bindings-storage-blob-trigger.md)
- [Odczytaj dane magazynu obiektów blob, gdy działa funkcja](./functions-bindings-storage-blob-input.md)
- [Zapisywanie danych usługi BLOB Storage z funkcji](./functions-bindings-storage-blob-output.md)
