---
title: Tematy systemu w usłudze Azure Event Grid
description: Opisuje tematy systemu w usłudze Azure Event Grid.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: spelluru
ms.openlocfilehash: 46bceeb31fa38068c6c4f9f3a86ed556ad39effb
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393150"
---
# <a name="system-topics-in-azure-event-grid"></a>Tematy systemu w usłudze Azure Event Grid
Usługa Azure Event Grid tworzy tematy systemowe podczas tworzenia pierwszej subskrypcji zdarzeń dla źródła zdarzeń platformy Azure. Obecnie usługa Event Grid nie tworzy tematów systemowych dla źródeł tematów utworzonych przed 15 marca 2020 r. Dla wszystkich źródeł tematów utworzonych w tej dacie lub później usługa Event Grid automatycznie tworzy tematy systemowe. W tym artykule opisano **tematy systemu** w usłudze Azure Event Grid.

> [!NOTE]
> Ta funkcja nie jest obecnie włączona dla chmury azure dla instytucji rządowych. 

## <a name="overview"></a>Omówienie
Podczas tworzenia pierwszej subskrypcji zdarzeń dla źródła zdarzeń platformy Azure, takich jak konto usługi Azure Storage, proces inicjowania obsługi administracyjnej dla subskrypcji tworzy dodatkowy zasób typu **Microsoft.EventGrid/systemTopics**. Po usunięciu ostatniej subskrypcji zdarzenia w źródle zdarzeń platformy Azure temat systemowy jest automatycznie usuwany.

Temat systemowy nie ma zastosowania do scenariuszy tematów niestandardowych, czyli tematów siatki zdarzeń i domen siatki zdarzeń. 

## <a name="location"></a>Lokalizacja
Dla źródeł zdarzeń platformy Azure, które znajdują się w określonym regionie/lokalizacji, temat systemu jest tworzony w tej samej lokalizacji co źródło zdarzeń platformy Azure. Na przykład jeśli utworzysz subskrypcję zdarzeń dla magazynu obiektów blob platformy Azure we wschodnich systemach USA, temat systemowy zostanie utworzony we wschodnich systemach USA. W przypadku globalnych źródeł zdarzeń platformy Azure, takich jak subskrypcje platformy Azure, grupy zasobów lub usługi Azure Maps, usługa Event Grid tworzy temat systemu w lokalizacji **globalnej.** 

## <a name="resource-group"></a>Grupa zasobów 
Ogólnie rzecz biorąc temat systemu jest tworzony w tej samej grupie zasobów, w których znajduje się źródło zdarzeń platformy Azure. W przypadku subskrypcji zdarzeń utworzonych w zakresie subskrypcji platformy Azure temat systemowy jest tworzony w ramach grupy zasobów **Default-EventGrid**. Jeśli grupa zasobów nie istnieje, usługa Azure Event Grid tworzy ją przed utworzeniem tematu systemu. 

Podczas próby usunięcia grupy zasobów z kontem magazynu, zobaczysz temat systemowy na liście zasobów, których dotyczy problem.  

![Usuwanie grupy zasobów](./media/system-topics/delete-resource-group.png)

## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły: 

- [Tematy niestandardowe](custom-topics.md)
- [Domeny](event-domains.md)