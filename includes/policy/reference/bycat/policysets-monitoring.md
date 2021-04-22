---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: f8095fc7c9be6ea23a573de8a5e871965d6c2a66
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107866038"
---
|Nazwa |Opis |Zasady |Wersja |
|---|---|---|---|
|[\[Wersja \] zapoznawcza: wdrażanie — konfigurowanie wymagań wstępnych w celu włączenia Azure Monitor i agentów zabezpieczeń platformy Azure na maszynach wirtualnych](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitoring_Prerequisites.json) |Skonfiguruj maszyny tak, aby automatycznie instalowały Azure Monitor i agentów zabezpieczeń platformy Azure. Security Center zbiera zdarzenia z agentów i używa ich do zapewnienia alertów zabezpieczeń i dostosowanych zadań wzmacniania zabezpieczeń (rekomendacji). Utwórz grupę zasobów i obszar roboczy usługi Log Analytics w tym samym regionie co maszyna do przechowywania rekordów inspekcji. Te zasady dotyczą tylko maszyn wirtualnych w kilku regionach. |5 |1.0.0-preview |
|[Włącz Azure Monitor dla Virtual Machine Scale Sets](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VMSS.json) |Włącz Azure Monitor dla Virtual Machine Scale Sets w określonym zakresie (grupa zarządzania, subskrypcja lub grupa zasobów). Przyjmuje obszar roboczy usługi Log Analytics jako parametr. Uwaga: jeśli zasady upgradePolicy zestawu skalowania są ustawione na ręczne, należy zastosować rozszerzenie do wszystkich maszyn wirtualnych w zestawie, wywołując uaktualnienie na nich. W interfejsie wiersza polecenia będzie to az vmss update-instances. |6 |1.0.1 |
|[Włącz Azure Monitor dla maszyn wirtualnych](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VM.json) |Włącz Azure Monitor dla maszyn wirtualnych w określonym zakresie (grupy zarządzania, subskrypcji lub grupy zasobów). Przyjmuje obszar roboczy usługi Log Analytics jako parametr. |10 |2.0.0 |
