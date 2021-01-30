---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/29/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: b38f33947ceaca40546febc7d73dbf514ffe0c40
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/30/2021
ms.locfileid: "99215225"
---
|Nazwa |Opis |Zasady |Wersja |
|---|---|---|---|
|[Wdrażanie — Konfigurowanie wymagań wstępnych w celu włączenia Azure Monitor i agentów zabezpieczeń platformy Azure na maszynach wirtualnych](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitoring_Prerequisites.json) |Skonfiguruj maszyny, aby automatycznie instalować Azure Monitor i agentów zabezpieczeń platformy Azure. Security Center zbiera zdarzenia od agentów i używa ich do udostępniania alertów zabezpieczeń i dostosowanych zadań związanych z zaostrzonymi zabezpieczeniami (zalecenia). Utwórz grupę zasobów i obszar roboczy Log Analytics w tym samym regionie co maszyna do przechowywania rekordów inspekcji. Te zasady mają zastosowanie tylko do maszyn wirtualnych w kilku regionach. |5 |1.0.0 — wersja zapoznawcza |
|[Włącz Azure Monitor dla Virtual Machine Scale Sets](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VMSS.json) |Włącz Azure Monitor dla Virtual Machine Scale Sets w określonym zakresie (grupy zarządzania, subskrypcji lub grupy zasobów). Przyjmuje Log Analytics obszar roboczy jako parametr. Uwaga: Jeśli zestaw skalowania upgradePolicy jest ustawiony na ręczny, należy zastosować rozszerzenie do wszystkich maszyn wirtualnych w zestawie, wywołując na nich uaktualnienie. W interfejsie wiersza polecenia będzie to AZ VMSS Update-Instances. |6 |1.0.1 |
|[Włącz Azure Monitor dla maszyn wirtualnych](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VM.json) |Włącz Azure Monitor dla maszyn wirtualnych w określonym zakresie (grupy zarządzania, subskrypcji lub grupy zasobów). Przyjmuje Log Analytics obszar roboczy jako parametr. |10 |2.0.0 |
