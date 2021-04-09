---
title: Szablony usługi Azure Resource Manager
description: Azure Resource Manager szablonów do korzystania z funkcji Azure Site Recovery.
services: site-recovery
author: rishjai
manager: gaggupta
ms.service: site-recovery
ms.topic: article
ms.date: 02/04/2021
ms.author: rishjai
ms.openlocfilehash: efd5c1ab3e23348373be76c79d1b9689bc5f4941
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101720532"
---
# <a name="azure-resource-manager-templates-for-azure-site-recovery"></a>Szablony Azure Resource Manager dla Azure Site Recovery

Poniższa tabela zawiera linki do Azure Resource Manager szablonów służących do korzystania z funkcji Azure Site Recovery.

| Template | Opis |
|---|---|
|**Azure–Azure** | |
| [Tworzenie magazynu Recovery Services](./quickstart-create-vault-template.md)| Utwórz magazyn usługi Recovery Services. Magazynu można użyć dla usług Azure Backup i Azure Site Recovery. |
| [Włącz replikację maszyn wirtualnych platformy Azure](https://aka.ms/asr-arm-enable-replication) | Włącz replikację maszyn wirtualnych platformy Azure przy użyciu istniejącego magazynu i ustawień niestandardowych obiektów docelowych.|
| [Wyzwalanie trybu failover i ponowne Włączanie ochrony](https://aka.ms/asr-arm-failover-reprotect) | Wyzwól operację przełączania do trybu failover i ponownego włączania ochrony dla zestawu maszyn wirtualnych platformy Azure. |
| [Uruchamianie kompleksowego przepływu DR dla maszyn wirtualnych platformy Azure](https://aka.ms/asr-arm-e2e-flow) | Rozpocznij kompleksowy przepływ odzyskiwania po awarii (Włącz replikację + tryb failover i ponownie Włącz ochronę + powrót po awarii i ponowne Włączanie ochrony) dla maszyn wirtualnych platformy Azure, nazywane również przepływem 540 °.|
|   |   |