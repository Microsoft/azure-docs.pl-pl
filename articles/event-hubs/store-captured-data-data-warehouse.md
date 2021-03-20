---
title: 'Samouczek: Migrowanie danych zdarzenia do usługi Azure Synapse Analytics — Azure Event Hubs'
description: Opisuje sposób korzystania z Azure Event Grid i funkcji w celu migrowania Event Hubs przechwyconych danych do usługi Azure Synapse Analytics.
services: event-hubs
ms.date: 12/07/2020
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.openlocfilehash: 77a2b35dea57c71e9e6f07056bd106df2ac109b8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96854240"
---
# <a name="tutorial-migrate-captured-event-hubs-data-to-azure-synapse-analytics-using-event-grid-and-azure-functions"></a>Samouczek: Migrowanie przechwyconych danych Event Hubs do usługi Azure Synapse Analytics przy użyciu Event Grid i Azure Functions
[Przechwytywanie](./event-hubs-capture-overview.md) Event Hubs platformy Azure umożliwia automatyczne przechwytywanie danych przesyłanych strumieniowo w Event Hubs w usłudze Azure Blob storage lub Azure Data Lake Storage. W tym samouczku pokazano, jak migrować przechwycone Event Hubs dane z magazynu do usługi Azure Synapse Analytics przy użyciu funkcji platformy Azure, która jest wyzwalana przez [Event Grid](../event-grid/overview.md).

[!INCLUDE [event-grid-event-hubs-functions-synapse-analytics.md](../../includes/event-grid-event-hubs-functions-synapse-analytics.md)]

## <a name="next-steps"></a>Następne kroki 
Aby uzyskać praktyczne informacje, możesz użyć zaawansowanych narzędzi do wizualizacji danych w magazynie danych.

W tym artykule pokazano, jak używać [Power BI z usługą Azure Synapse Analytics](/power-bi/connect-data/service-azure-sql-data-warehouse-with-direct-connect)