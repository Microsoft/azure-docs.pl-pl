---
title: Dokumentacja usługi Azure Queue Storage
description: Znajdź informacje o interfejsie API usługi Azure queue storage, plikach Readme i pakietach biblioteki klienta.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 09/10/2020
ms.service: storage
ms.subservice: queues
ms.topic: conceptual
ms.reviewer: ripohane
ms.openlocfilehash: de504cc4209aea19925c4c3e2385c7feabceedeb
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92781621"
---
# <a name="azure-queue-storage-reference"></a>Dokumentacja usługi Azure Queue Storage

Znajdź informacje o interfejsie API usługi Azure queue storage, pakietach bibliotek, plikach Readme i artykułach rozpoczynających pracę.

## <a name="net-client-libraries"></a>Biblioteki klienckie platformy .NET

W poniższej tabeli wymieniono odwołania i przykładowe dokumenty dotyczące interfejsów API .NET usługi Azure queue storage.

|  Wersja  | Dokumentacja referencyjna | Pakiet | Szybki start |
| :-------: | ----------------------- | ------- | ---------- |
| 12. x | [Przestrzeń nazw Azure. Storage. Queues](/dotnet/api/azure.storage.queues) | [Pakiet (NuGet)](https://www.nuget.org/packages/Azure.Storage.Queues/) | [Szybki Start: V12 biblioteki klienta usługi Azure queue storage dla platformy .NET](./storage-quickstart-queues-dotnet.md) |
| 11. x | [Przestrzeń nazw Microsoft. Azure. Storage. Queue](/dotnet/api/microsoft.azure.storage.queue) | [Pakiet (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.Storage.Queue/) | [Szybki Start: korzystanie z usługi Azure Storage SDK v11 for .NET do zarządzania kolejką](./storage-quickstart-queues-dotnet-legacy.md) |

### <a name="storage-management"></a>Zarządzanie magazynem

W poniższej tabeli przedstawiono dokumentację referencyjną interfejsów API platformy .NET zarządzania usługą Azure Storage.

|  Wersja  | Dokumentacja referencyjna | Pakiet |
| :-------: | ----------------------- | ------- |
| 16. x | [Microsoft.Azure.Management.Storage](/dotnet/api/microsoft.azure.management.storage) | [Pakiet (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/) |

### <a name="data-movement"></a>Przenoszenie danych

W poniższej tabeli przedstawiono dokumentację referencyjną dla interfejsów API platformy .NET usługi Azure Storage.

|  Wersja  | Dokumentacja referencyjna | Pakiet |
| :-------: | ----------------------- | ------- |
| 1.x | [Przenoszenie danych](/dotnet/api/microsoft.azure.storage.datamovement) | [Pakiet (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.Storage.DataMovement/) |

## <a name="java-client-libraries"></a>Biblioteki klienckie Java

W poniższej tabeli wymieniono odwołania i przykładowe dokumenty dotyczące interfejsów API języka Java usługi Azure queue storage.

|  Wersja  | Dokumentacja referencyjna | Pakiet | Szybki start |
| :-------: | ----------------------- | ------- | ---------- |
| 12. x | [Biblioteka klienta usługi Azure Storage Queue dla języka Java](/java/api/overview/azure/storage-queue-readme) | [Pakiet (Maven)](https://mvnrepository.com/artifact/com.azure/azure-storage-queue) | [Szybki Start: V12 biblioteki klienta usługi Azure queue storage dla języka Java](./storage-quickstart-queues-java.md) |
| 8.x | [com. Microsoft. Azure. Storage. Queue](/java/api/com.microsoft.azure.storage.queue) | [Pakiet (Maven)](https://mvnrepository.com/artifact/com.microsoft.azure/azure-storage) | [Szybki Start: Zarządzanie obiektami BLOB za pomocą zestawu SDK V8 języka Java](../blobs/storage-quickstart-blobs-java-legacy.md) |

### <a name="storage-management"></a>Zarządzanie magazynem

W poniższej tabeli przedstawiono dokumentację referencyjną interfejsów API języka Java zarządzania usługą Azure Storage.

|  Wersja  | Dokumentacja referencyjna | Pakiet |
| :-------: | ----------------------- | ------- |
| 0.9. x | [com. Microsoft. Azure. Management. Storage](/java/api/overview/azure/storage/management) | [Pakiet (Maven)](https://mvnrepository.com/artifact/com.microsoft.azure/azure-svc-mgmt-storage) |

## <a name="python-client-libraries"></a>Biblioteki klienckie języka Python

W poniższej tabeli przedstawiono odwołania i przykładową dokumentację interfejsów API języka Python usługi Azure queue storage.

|  Wersja  | Dokumentacja referencyjna | Pakiet | Szybki start |
| :-------: | ----------------------- | ------- | ---------- |
| 12. x | [Biblioteki klienta usługi Azure Storage V12 dla języka Python](/azure/developer/python/sdk/storage/overview) | [Pakiet (PyPI)](https://pypi.org/project/azure-storage-queue/) | [Szybki Start: V12 biblioteki klienta usługi Azure queue storage dla języka Python](./storage-quickstart-queues-python.md) |
| 2.x | [Biblioteki klienckie usługi Azure Storage w wersji 2 dla języka Python](/azure/developer/python/sdk/storage/overview?view=storage-py-v2&preserve-view=true) | [Pakiet (PyPI)](https://pypi.org/project/azure-storage-queue/2.1.0/) | [Jak korzystać z usługi Azure queue storage w wersji 2.1 z poziomu języka Python](./storage-python-how-to-use-queue-storage.md) |

## <a name="javascript-client-libraries"></a>Biblioteki klienckie języka JavaScript

W poniższej tabeli wymieniono odwołania i przykładowe dokumenty dotyczące interfejsów API języka JavaScript usługi Azure queue storage.

|  Wersja  | Dokumentacja referencyjna | Pakiet | Szybki start |
| :-------: | ----------------------- | ------- | ---------- |
| 12. x | [Biblioteka kliencka kolejki usługi Azure Storage dla języka JavaScript](/javascript/api/overview/azure/storage-queue-readme) | [Pakiet (npm)](https://www.npmjs.com/package/@azure/storage-queue) | [Szybki Start: Biblioteka kliencka usługi Azure queue storage V12 dla języka JavaScript](./storage-quickstart-queues-nodejs.md) |
| 10. x | [@azure/storage-queue](/javascript/api/@azure/storage-queue/?view=azure-node-legacy&preserve-view=true) | [Pakiet (npm)](https://www.npmjs.com/package/@azure/storage-queue/v/10.3.0) | [Tworzenie i usuwanie kolejek z Node.jsza pomocą usługi Azure Queue Service ](./storage-nodejs-how-to-use-queues.md) |

## <a name="rest-apis"></a>Interfejsy API REST

W poniższej tabeli przedstawiono odwołania i przykładową dokumentację interfejsów API REST usługi Azure queue storage.

| Dokumentacja referencyjna | Omówienie |
| ----------------------- | -------- |
| [Interfejs API REST usługa kolejki](/rest/api/storageservices/queue-service-rest-api) | [Koncepcje usługa kolejki](/rest/api/storageservices/queue-service-concepts) |

### <a name="other-rest-reference"></a>Inne odwołanie do REST

- [Interfejs API REST usługi Azure Storage Import-Export ](/rest/api/storageimportexport/) ułatwia zarządzanie zadaniami importu/eksportu w celu przesyłania danych do lub z magazynu obiektów BLOB.

## <a name="other-languages-and-platforms"></a>Inne języki i platformy

Poniższa lista zawiera linki do bibliotek dla innych języków programowania i platform.

- [C++](https://azure.github.io/azure-storage-cpp)
- [Ruby](https://azure.github.io/azure-storage-ruby)
- [PHP](https://azure.github.io/azure-storage-php/)
- [iOS](https://azure.github.io/azure-storage-ios/)
- [Android](https://azure.github.io/azure-storage-android)

## <a name="powershell"></a>PowerShell

Poniższa tabela zawiera linki do najnowszej wersji zawartości referencyjnej.

| Wersja | Platforma |
| ------- | -------- |
|  4.x  | [PowerShell](/powershell/module/az.storage/?view=azps-4.6.1&preserve-view=true) |
|  wersji  | [PowerShell](/powershell/module/az.storage/?view=azps-3.8.0&preserve-view=true) |
|  2.x  | [PowerShell](/powershell/module/az.storage/?view=azps-2.8.0&preserve-view=true) |

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

- [Interfejs wiersza polecenia platformy Azure](/cli/azure/storage)