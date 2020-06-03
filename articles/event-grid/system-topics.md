---
title: Tematy systemowe w Azure Event Grid
description: Opisuje tematy systemowe w Azure Event Grid.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 06/02/2020
ms.author: spelluru
ms.openlocfilehash: 67746ebd8a16eb02b8f02d238b0e3c0125989189
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/03/2020
ms.locfileid: "84308272"
---
# <a name="system-topics-in-azure-event-grid"></a>Tematy systemowe w Azure Event Grid
Usługa Azure Event Grid tworzy tematy systemowe podczas tworzenia pierwszej subskrypcji zdarzeń dla źródła zdarzeń platformy Azure. Obecnie Event Grid nie tworzy tematów systemowych dla źródeł tematów, które zostały utworzone przed Mar, 15, 2020. W przypadku wszystkich źródeł tematu utworzonych w dniu lub po tej dacie Event Grid automatycznie tworzy tematy systemowe. W tym artykule opisano **Tematy systemowe** w Azure Event Grid.

> [!NOTE]
> Ta funkcja nie jest obecnie włączona dla Azure Government Cloud. 

## <a name="overview"></a>Omówienie
Podczas tworzenia pierwszej subskrypcji zdarzeń dla źródła zdarzeń platformy Azure, takiego jak konto usługi Azure Storage, proces aprowizacji subskrypcji tworzy dodatkowy zasób typu **Microsoft. EventGrid/systemTopics**. Po usunięciu ostatniej subskrypcji zdarzeń do źródła zdarzeń platformy Azure temat systemowy zostanie automatycznie usunięty.

Temat systemowy nie ma zastosowania do scenariuszy niestandardowych tematów, czyli Event Grid tematów i Event Grid domen. 

## <a name="name"></a>Nazwa 
Wcześniej podczas tworzenia subskrypcji dla zdarzenia zgłoszonego przez źródła platformy Azure usługa Event Grid automatycznie utworzyła temat systemowy z **losowo wygenerowaną nazwą**. Teraz można określić nazwę tematu systemu podczas tworzenia tematu w Azure Portal. Ten zasób tematu systemu służy do odnajdywania metryk i dzienników diagnostycznych.

## <a name="location"></a>Lokalizacja
W przypadku źródeł zdarzeń platformy Azure, które znajdują się w określonym regionie/lokalizacji, temat systemowy jest tworzony w tej samej lokalizacji, w której znajduje się źródło zdarzeń platformy Azure. Jeśli na przykład utworzysz subskrypcję zdarzeń dla magazynu obiektów blob platformy Azure w regionie Wschodnie stany USA, temat systemowy zostanie utworzony w regionie Wschodnie stany USA. W przypadku globalnych źródeł zdarzeń platformy Azure, takich jak subskrypcje platformy Azure, grupy zasobów lub Azure Maps, Event Grid tworzy temat systemowy w lokalizacji **globalnej** . 

## <a name="resource-group"></a>Grupa zasobów 
Ogólnie rzecz biorąc, temat systemowy jest tworzony w tej samej grupie zasobów, w której znajduje się źródło zdarzeń platformy Azure. W przypadku subskrypcji zdarzeń utworzonych w ramach zakresu subskrypcji platformy Azure temat systemowy jest tworzony w ramach grupy zasobów **default-EventGrid**. Jeśli grupa zasobów nie istnieje, Azure Event Grid ją utworzyć przed utworzeniem tematu systemowego. 

Podczas próby usunięcia grupy zasobów przy użyciu konta magazynu zostanie wyświetlony temat system na liście zasobów, których to dotyczy.  

![Usuwanie grupy zasobów](./media/system-topics/delete-resource-group.png)


## <a name="next-steps"></a>Następne kroki
Zapoznaj się z następującym artykułem: [Tworzenie, wyświetlanie i zarządzanie tematami systemowymi](create-view-manage-system-topics.md).