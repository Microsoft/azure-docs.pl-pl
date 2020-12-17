---
title: Zadania i aplikacje replikacji komunikatów — Azure Service Bus | Microsoft Docs
description: Ten artykuł zawiera omówienie tworzenia zadań i aplikacji replikacji komunikatów przy użyciu Azure Functions
ms.topic: article
ms.date: 12/12/2020
ms.openlocfilehash: 4db151f54a2ad236ba937b005ba6a1fd3edd5967
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97657497"
---
# <a name="message-replication-tasks-and-applications"></a>Zadania i aplikacje replikacji komunikatów

Zgodnie z objaśnieniem w artykule [replikacja komunikatów i w ramach federacji między różnymi](service-bus-federation-overview.md) lokalizacjami, replikacja sekwencji komunikatów między parami jednostek Service Bus i między Service Bus i innymi źródłami komunikatów i obiektami docelowymi zazwyczaj jest pochylenie w Azure Functions.

[Azure Functions](../azure-functions/functions-overview.md) to skalowalne i niezawodne środowisko wykonawcze służące do konfigurowania i uruchamiania aplikacji bezserwerowych, takich jak [replikacja komunikatów i zadania federacyjne](service-bus-federation-overview.md) .

W tym omówieniu dowiesz się więcej na temat funkcji wbudowanych w Azure Functionsch dla takich aplikacji, bloków kodu, które można dostosować i modyfikować w celu wykonywania zadań transformacji, oraz sposobu konfigurowania aplikacji Azure Functions, która integruje się najlepiej z Service Bus i innymi usługami Azure Messaging. Aby uzyskać więcej informacji, ten artykuł będzie wskazywał dokumentację Azure Functions.

[!INCLUDE [messaging-replicator-functions](../../includes/messaging-replicator-functions.md)]
