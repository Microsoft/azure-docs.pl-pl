---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 10/07/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 4b48b68d354a21430d53799f9c6a58bf1f7768f6
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/07/2020
ms.locfileid: "91820119"
---
|Nazwa |Opis |Zasady |Wersja |
|---|---|---|---|
|[Inspekcja maszyn z niezabezpieczonymi ustawieniami zabezpieczeń haseł](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_WindowsPasswordSettingsAINE.json) |Ta inicjatywa wdraża wymagania zasad i przeprowadza inspekcje maszyn z niebezpiecznymi ustawieniami zabezpieczeń haseł. Aby uzyskać więcej informacji na temat zasad konfiguracji gościa, odwiedź stronę [https://aka.ms/gcpol](https://aka.ms/gcpol) |9 |1.0.0 |
|[Wdróż wymagania wstępne, aby włączyć zasady konfiguracji gościa na maszynach wirtualnych](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_Prerequisites.json) |Ta inicjatywa dodaje tożsamość zarządzaną przypisaną przez system i wdraża rozszerzenie konfiguracji gościa odpowiednie dla platformy na maszynach wirtualnych, które mogą być monitorowane przez zasady konfiguracji gościa. Jest to wymaganie wstępne dla wszystkich zasad konfiguracji gościa i musi być przypisane do zakresu przypisania zasad przed skorzystaniem z zasad konfiguracji gościa. Aby uzyskać więcej informacji na temat konfiguracji gościa, odwiedź stronę [https://aka.ms/gcpol](https://aka.ms/gcpol) . |4 |1.0.0 — wersja zapoznawcza |
|[Maszyny z systemem Windows powinny spełniać wymagania dotyczące podstawy zabezpieczeń platformy Azure](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_AzureBaseline.json) |Ta inicjatywa przeprowadza inspekcję maszyn z systemem Windows przy użyciu ustawień niezgodnych z punktem odniesienia zabezpieczeń platformy Azure. Aby uzyskać szczegółowe informacje, odwiedź stronę [https://aka.ms/gcpol](https://aka.ms/gcpol) |29 |2.0.0 — wersja zapoznawcza |
