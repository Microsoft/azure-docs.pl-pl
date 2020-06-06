---
title: Tematy systemowe w Azure Event Grid
description: Opisuje tematy systemowe w Azure Event Grid.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 06/02/2020
ms.author: spelluru
ms.openlocfilehash: c34ada2c7437d8415b52c68fb66103ec3aa81d95
ms.sourcegitcommit: ba8df8424d73c8c4ac43602678dae4273af8b336
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2020
ms.locfileid: "84456933"
---
# <a name="system-topics-in-azure-event-grid"></a>Tematy systemowe w Azure Event Grid
Tematy dotyczące systemu są tematy utworzone dla usług platformy Azure, takich jak Azure Storage i Azure Event Hubs. Tematy systemowe można utworzyć przy użyciu szablonu Azure Portal, programu PowerShell, interfejsu wiersza polecenia lub Azure Resource Manager.  

> [!NOTE]
> Ta funkcja nie jest obecnie włączona dla Azure Government Cloud. 

## <a name="create-system-topics"></a>Tworzenie tematów systemowych
Temat systemowy można utworzyć na dwa sposoby: 

- Utwórz temat systemowy dla zasobu platformy Azure, a następnie Utwórz subskrypcję zdarzeń dla tego tematu systemu.
- Utwórz subskrypcję zdarzeń dla zasobu platformy Azure, która wewnętrznie tworzy temat systemowy.

W przypadku korzystania z pierwszego podejścia temat systemowy nie jest automatycznie usuwany po usunięciu ostatniej subskrypcji zdarzeń dla tego tematu systemu. W przypadku korzystania z drugiego podejścia temat systemowy jest automatycznie usuwany po usunięciu ostatniej subskrypcji zdarzenia. 

Aby uzyskać szczegółowe instrukcje dotyczące tworzenia tematów systemowych przy użyciu Azure Portal, programu PowerShell lub interfejsu wiersza polecenia, zobacz następujące artykuły:

- [Tworzenie i wyświetlanie tematów systemowych oraz zarządzanie nimi za pomocą Azure Portal](create-view-manage-system-topics.md).
- [Tworzenie i wyświetlanie Event Grid tematów systemowych oraz zarządzanie nimi za pomocą interfejsu wiersza polecenia platformy Azure](create-view-manage-system-topics-cli.md)
- [Tworzenie Event Grid tematów systemowych przy użyciu szablonów Azure Resource Manager](create-view-manage-system-topics-arm.md)

## <a name="system-topic-name"></a>Nazwa tematu systemu
Event Grid nie utworzył tematów systemowych dla źródeł platformy Azure (Azure Storage, Azure Event Hubs itp.), które zostały utworzone przed Mar, 15 2020. Jeśli utworzono subskrypcję dla zdarzenia zgłoszonego przez źródła platformy Azure z zakresu od 3/15/2020 do 6/2/2020, usługa Event Grid automatycznie utworzyła temat systemowy o **losowo generowanej nazwie**. Po 6/2/2020 można określić nazwę tematu systemu podczas tworzenia subskrypcji zdarzeń dla źródła platformy Azure. 

## <a name="location-and-resource-group"></a>Lokalizacja i Grupa zasobów
W przypadku źródeł zdarzeń platformy Azure, które znajdują się w określonym regionie/lokalizacji, temat systemowy jest tworzony w tej samej lokalizacji, w której znajduje się źródło zdarzeń platformy Azure. Jeśli na przykład utworzysz subskrypcję zdarzeń dla magazynu obiektów blob platformy Azure w regionie Wschodnie stany USA, temat systemowy zostanie utworzony w regionie Wschodnie stany USA. W przypadku globalnych źródeł zdarzeń platformy Azure, takich jak subskrypcje platformy Azure, grupy zasobów lub Azure Maps, Event Grid tworzy temat systemowy w lokalizacji **globalnej** . 

Ogólnie rzecz biorąc, temat systemowy jest tworzony w tej samej grupie zasobów, w której znajduje się źródło zdarzeń platformy Azure. W przypadku subskrypcji zdarzeń utworzonych w ramach zakresu subskrypcji platformy Azure temat systemowy jest tworzony w ramach grupy zasobów **default-EventGrid**. Jeśli grupa zasobów nie istnieje, Azure Event Grid ją utworzyć przed utworzeniem tematu systemowego. 

## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły: 

- [Tworzenie i wyświetlanie tematów systemowych oraz zarządzanie nimi za pomocą Azure Portal](create-view-manage-system-topics.md).
- [Tworzenie i wyświetlanie Event Grid tematów systemowych oraz zarządzanie nimi za pomocą interfejsu wiersza polecenia platformy Azure](create-view-manage-system-topics-cli.md)
- [Tworzenie Event Grid tematów systemowych przy użyciu szablonów Azure Resource Manager](create-view-manage-system-topics-arm.md)
