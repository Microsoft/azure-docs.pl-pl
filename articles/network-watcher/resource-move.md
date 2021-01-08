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
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: 4853f485e4424c3c3263a18d27834d0f9ae94918
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98019656"
---
# <a name="moving-azure-network-watcher-resources-across-regions"></a>Przemieszczanie zasobów Network Watcher platformy Azure między regionami

## <a name="moving-the-network-watcher-resource"></a>Przeniesienie zasobu Network Watcher
Zasób Network Watcher reprezentuje usługę zaplecza dla Network Watcher i jest w pełni zarządzana przez platformę Azure. Klienci nie muszą zarządzać nimi. Operacja przenoszenia nie jest obsługiwana dla tego zasobu.

## <a name="moving-child-resources-of-network-watcher"></a>Przeniesienie zasobów podrzędnych Network Watcher
Przeniesienie zasobów między regionami nie jest obecnie obsługiwane dla żadnego zasobu podrzędnego `*networkWatcher*` typu zasobu.

## <a name="next-steps"></a>Następne kroki
* Przeczytaj [omówienie Network Watcher](./network-watcher-monitoring-overview.md)
* Zapoznaj się z [Network Watcher często zadawanych pytań](./frequently-asked-questions.md)