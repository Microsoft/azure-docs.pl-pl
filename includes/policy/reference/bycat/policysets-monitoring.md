---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 02118ff7bf6568ba928ecea3658db1aa23e5015a
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107504897"
---
|Nazwa |Opis |Zasady |Wersja |
|---|---|---|---|
|[\[Wersja \] zapoznawcza: wdrażanie — konfigurowanie wymagań wstępnych w celu włączenia Azure Monitor i agentów zabezpieczeń platformy Azure na maszynach wirtualnych](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitoring_Prerequisites.json) |Skonfiguruj maszyny tak, aby automatycznie instalowały Azure Monitor i agentów zabezpieczeń platformy Azure. Security Center zbiera zdarzenia z agentów i używa ich do zapewnienia alertów zabezpieczeń i dostosowanych zadań wzmacniania zabezpieczeń (rekomendacji). Utwórz grupę zasobów i obszar roboczy usługi Log Analytics w tym samym regionie co maszyna do przechowywania rekordów inspekcji. Te zasady dotyczą tylko maszyn wirtualnych w kilku regionach. |5 |1.0.0-preview |
|[Włącz Azure Monitor dla Virtual Machine Scale Sets](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VMSS.json) |Włącz Azure Monitor dla Virtual Machine Scale Sets w określonym zakresie (grupa zarządzania, subskrypcja lub grupa zasobów). Przyjmuje obszar roboczy usługi Log Analytics jako parametr. Uwaga: jeśli ustawienie upgradePolicy zestawu skalowania ma wartość Ręcznie, należy zastosować rozszerzenie do wszystkich maszyn wirtualnych w zestawie, wywołując na nich uaktualnienie. W interfejsie wiersza polecenia będzie to az vmss update-instances. |6 |1.0.1 |
|[Włączanie Azure Monitor dla maszyn wirtualnych](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VM.json) |Włącz Azure Monitor dla maszyn wirtualnych w określonym zakresie (grupy zarządzania, subskrypcji lub grupy zasobów). Przyjmuje obszar roboczy usługi Log Analytics jako parametr. |10 |2.0.0 |
