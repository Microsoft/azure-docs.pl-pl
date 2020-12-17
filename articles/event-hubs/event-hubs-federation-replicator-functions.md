---
title: Zadania i aplikacje replikacji zdarzeń — Event Hubs platformy Azure | Microsoft Docs
description: Ten artykuł zawiera omówienie tworzenia zadań i aplikacji replikacji zdarzeń przy użyciu Azure Functions
ms.topic: article
ms.date: 12/01/2020
ms.openlocfilehash: a65815c7da400af8b5b6d46358e6bca6edbd7543
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97663646"
---
# <a name="event-replication-tasks-and-applications-with-azure-functions"></a>Zadania i aplikacje replikacji zdarzeń z Azure Functions

> [!TIP]
> W przypadku wszystkich zadań związanych z replikacją stanową należy wziąć pod uwagę ładunki zdarzeń i przekształcanie, agregowanie, wzbogacanie lub zmniejszanie ich, zamiast Azure Functions użyć [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) .

Zgodnie z opisem w artykule [replikacja zdarzeń i w ramach federacji między różnymi regionami](event-hubs-federation-overview.md) , bezstanowa replikacja strumieni zdarzeń między parami Event Hubs i między Event Hubs i innymi źródłami strumieni zdarzeń oraz docelowymi różnicami w Azure Functions.

[Azure Functions](../azure-functions/functions-overview.md) to skalowalne i niezawodne środowisko wykonawcze służące do konfigurowania i uruchamiania aplikacji bezserwerowych, takich jak replikacja zdarzeń i zadania federacyjne.

W tym omówieniu dowiesz się więcej na temat funkcji wbudowanych w Azure Functionsch dla takich aplikacji, bloków kodu, które można dostosować i modyfikować w celu wykonywania zadań transformacji, oraz sposobu konfigurowania aplikacji Azure Functions, która integruje się najlepiej z Event Hubs i innymi usługami Azure Messaging. Aby uzyskać więcej informacji, ten artykuł będzie wskazywał dokumentację Azure Functions.

[!INCLUDE [messaging-replicator-functions](../../includes/messaging-replicator-functions.md)]









