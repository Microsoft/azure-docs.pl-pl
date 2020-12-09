---
title: 'Samouczek: wysyłanie danych Event Hubs do magazynu danych — Event Grid'
description: Opisuje sposób przechowywania Event Hubs przechwyconych danych w usłudze Azure Synapse Analytics za pośrednictwem wyzwalaczy Azure Functions i Event Grid.
ms.topic: tutorial
ms.date: 12/07/2020
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 7b0e471e32650490e1896bb6ea171c8223b21378
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2020
ms.locfileid: "96854720"
---
# <a name="tutorial-stream-big-data-into-a-data-warehouse"></a>Samouczek: przesyłanie strumieniowe danych Big Data do magazynu danych
Azure [Event Grid](overview.md) to usługa inteligentnego routingu zdarzeń, która umożliwia reagowanie na powiadomienia lub zdarzenia z aplikacji i usług. Na przykład może wyzwolić funkcję platformy Azure, aby przetwarzać Event Hubs dane przechwycone do magazynu obiektów blob lub Data Lake Storage. Ten [przykład](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) pokazuje, jak używać Event Grid i Azure Functions do migrowania przechwyconych danych Event Hubs z magazynu obiektów BLOB do usługi Azure Synapse Analytics, w tym specjalnie dedykowanej puli SQL.

[!INCLUDE [event-grid-event-hubs-functions-synapse-analytics.md](../../includes/event-grid-event-hubs-functions-synapse-analytics.md)]

## <a name="next-steps"></a>Następne kroki

* Aby poznać różnice miedzy usługami obsługi komunikatów na platformie Azure, zobacz [Wybieranie między usługami platformy Azure dostarczającymi komunikaty](compare-messaging-services.md).
* Aby zapoznać się z wprowadzeniem do usługi Event Grid, zobacz [Wprowadzenie do usługi Azure Event Grid](overview.md).
* Aby zapoznać się z wprowadzeniem do funkcji Event Hubs Capture, zobacz [Włączanie funkcji przechwytywania usługi Event Hubs przy użyciu witryny Azure Portal](../event-hubs/event-hubs-capture-enable-through-portal.md).
* Aby uzyskać więcej informacji na temat konfigurowania i uruchamiania przykładu, zobacz [Przykład funkcji Event Hubs Capture i usługi Event Grid](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo).
