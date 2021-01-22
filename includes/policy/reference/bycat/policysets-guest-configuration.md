---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 281ad78418150bf4cb458e4bf02acd9446fa01dd
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98703992"
---
|Nazwa |Opis |Zasady |Wersja |
|---|---|---|---|
|[Inspekcja maszyn z niezabezpieczonymi ustawieniami zabezpieczeń haseł](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_WindowsPasswordSettingsAINE.json) |Ta inicjatywa wdraża wymagania zasad i przeprowadza inspekcje maszyn z niebezpiecznymi ustawieniami zabezpieczeń haseł. Aby uzyskać więcej informacji na temat zasad konfiguracji gościa, odwiedź stronę [https://aka.ms/gcpol](https://aka.ms/gcpol) |9 |1.0.0 |
|[Wdróż wymagania wstępne, aby włączyć zasady konfiguracji gościa na maszynach wirtualnych](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_Prerequisites.json) |Ta inicjatywa dodaje tożsamość zarządzaną przypisaną przez system i wdraża rozszerzenie konfiguracji gościa odpowiednie dla platformy na maszynach wirtualnych, które mogą być monitorowane przez zasady konfiguracji gościa. Jest to wymaganie wstępne dla wszystkich zasad konfiguracji gościa i musi być przypisane do zakresu przypisania zasad przed skorzystaniem z zasad konfiguracji gościa. Aby uzyskać więcej informacji na temat konfiguracji gościa, odwiedź stronę [https://aka.ms/gcpol](https://aka.ms/gcpol) . |4 |1.0.0 — wersja zapoznawcza |
|[Maszyny z systemem Windows powinny spełniać wymagania dotyczące podstawy zabezpieczeń platformy Azure](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_AzureBaseline.json) |Ta inicjatywa przeprowadza inspekcję maszyn z systemem Windows przy użyciu ustawień niezgodnych z punktem odniesienia zabezpieczeń platformy Azure. Aby uzyskać szczegółowe informacje, odwiedź stronę [https://aka.ms/gcpol](https://aka.ms/gcpol) |29 |2.0.0 — wersja zapoznawcza |
