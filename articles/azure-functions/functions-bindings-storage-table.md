---
title: Powiązania usługi Azure Table Storage dla Azure Functions
description: Dowiedz się, jak używać powiązań usługi Azure Table Storage w Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 09/03/2018
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 4f2b890dc60cd50b5fcaefabe8d418268b738c20
ms.sourcegitcommit: 93329b2fcdb9b4091dbd632ee031801f74beb05b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2020
ms.locfileid: "92096729"
---
# <a name="azure-table-storage-bindings-for-azure-functions"></a>Powiązania usługi Azure Table Storage dla Azure Functions

Azure Functions integruje się z usługą [Azure Storage](../storage/index.yml) za pośrednictwem [wyzwalaczy i powiązań](./functions-triggers-bindings.md). Integracja z usługą Table Storage umożliwia tworzenie funkcji, które odczytują i zapisują dane magazynu tabeli.

| Akcja | Typ |
|---------|---------|
| Odczytaj dane magazynu tabeli w funkcji | [Powiązanie danych wejściowych](./functions-bindings-storage-table-input.md) |
| Zezwalaj funkcji na zapisywanie danych magazynu tabel |[Powiązanie danych wyjściowych](./functions-bindings-storage-table-output.md) |

## <a name="packages---functions-2x-and-higher"></a>Pakiety — funkcje 2. x i nowsze

Powiązania magazynu tabel są dostępne w pakiecie NuGet [Microsoft. Azure. WebJobs. Extensions. Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) w wersji 3. x. Kod źródłowy pakietu znajduje się w repozytorium [Azure-WebJobs-SDK](https://github.com/Azure/azure-webjobs-sdk/tree/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables) GitHub.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="packages---functions-1x"></a>Pakiety — funkcje 1. x

Powiązania magazynu tabel są dostępne w pakiecie NuGet [Microsoft. Azure. WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) w wersji 2. x. Kod źródłowy pakietu znajduje się w repozytorium [Azure-WebJobs-SDK](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.Storage/Table) GitHub.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="next-steps"></a>Następne kroki

- [Odczytaj dane magazynu tabeli, gdy działa funkcja](./functions-bindings-storage-table-input.md)
- [Zapisywanie danych magazynu tabel z funkcji](./functions-bindings-storage-table-output.md)
