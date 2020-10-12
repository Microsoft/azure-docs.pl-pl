---
title: Przenoszenie zasobów usługi Azure Network Watcher | Microsoft Docs
description: Przenoszenie zasobów Network Watcher platformy Azure między regionami
services: network-watcher
documentationcenter: na
author: damendo
manager: ''
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/27/2020
ms.author: damendo
ms.openlocfilehash: 97349071fee6a95623e5b5efdc0c9818cfe7b811
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87388459"
---
# <a name="moving-azure-network-watcher-resources-across-regions"></a>Przemieszczanie zasobów Network Watcher platformy Azure między regionami

## <a name="moving-the-network-watcher-resource"></a>Przeniesienie zasobu Network Watcher
Zasób Network Watcher reprezentuje usługę zaplecza dla Network Watcher i jest w pełni zarządzana przez platformę Azure. Klienci nie muszą zarządzać nimi. Operacja przenoszenia nie jest obsługiwana dla tego zasobu.

## <a name="moving-child-resources-of-network-watcher"></a>Przeniesienie zasobów podrzędnych Network Watcher
Przeniesienie zasobów między regionami nie jest obecnie obsługiwane dla żadnego zasobu podrzędnego `*networkWatcher*` typu zasobu.

## <a name="next-steps"></a>Następne kroki
* Przeczytaj [omówienie Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)
* Zapoznaj się z [Network Watcher często zadawanych pytań](https://docs.microsoft.com/azure/network-watcher/frequently-asked-questions)
