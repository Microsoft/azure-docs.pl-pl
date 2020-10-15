---
title: Wskazówki dotyczące obsługi błędów Azure Functions
description: Dowiedz się, jak obsłużyć błędy w Azure Functions z łączami do określonych błędów powiązań.
author: craigshoemaker
ms.topic: conceptual
ms.date: 09/11/2019
ms.author: cshoe
ms.openlocfilehash: 0617d55f7c67c788b1e898d963f7d509cef72d49
ms.sourcegitcommit: 93329b2fcdb9b4091dbd632ee031801f74beb05b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2020
ms.locfileid: "92096848"
---
# <a name="azure-functions-error-handling"></a>Obsługa błędów Azure Functions

Obsługa błędów w Azure Functions ma na celu uniknięcie utraty danych, nieodebranych zdarzeń i monitorowania kondycji aplikacji.

W tym artykule opisano ogólne strategie obsługi błędów oraz linki do błędów specyficznych dla powiązania.

## <a name="handling-errors"></a>Obsługa błędów

[!INCLUDE [bindings errors intro](../../includes/functions-bindings-errors-intro.md)]

## <a name="binding-error-codes"></a>Kody błędów powiązania

W przypadku integracji z usługami platformy Azure błędy mogą pochodzić z interfejsów API podstawowych usług. Informacje dotyczące błędów związanych z powiązaniem są dostępne w sekcji **wyjątki i kody powrotu** w następujących artykułach:

+ [Azure Cosmos DB](functions-bindings-cosmosdb.md#exceptions-and-return-codes)

+ [Blob Storage](functions-bindings-storage-blob-output.md#exceptions-and-return-codes)

+ [Event Hubs](functions-bindings-event-hubs-output.md#exceptions-and-return-codes)

+ [Centra IoT](functions-bindings-event-iot-output.md#exceptions-and-return-codes)

+ [Notification Hubs](functions-bindings-notification-hubs.md#exceptions-and-return-codes)

+ [Queue Storage](functions-bindings-storage-queue-output.md#exceptions-and-return-codes)

+ [Service Bus](functions-bindings-service-bus-output.md#exceptions-and-return-codes)

+ [Table Storage](functions-bindings-storage-table-output.md#exceptions-and-return-codes)
