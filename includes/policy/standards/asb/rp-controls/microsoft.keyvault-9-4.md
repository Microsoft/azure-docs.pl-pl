---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 78694874819f8a90f0ab318fb96cda6b8816a689
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107880661"
---
|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Magazyny kluczy powinny mieć włączoną ochronę przed przeczyszczaniem](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b60c0b2-2dc2-4e1c-b5c9-abbed971de53) |Złośliwe usunięcie magazynu kluczy może prowadzić do trwałej utraty danych. Złośliwy niejawny tester w organizacji może potencjalnie usuwać i przeczyścić magazyny kluczy. Ochrona przed przeczyszczaniem chroni przed atakami wewnętrznymi przez wymuszenie obowiązkowego okresu przechowywania dla nieukończalnie usuniętych magazynów kluczy. Nikt wewnątrz organizacji ani firma Microsoft nie będzie mógł przeczyścić magazynów kluczy w okresie przechowywania usuwania nie soft. |Inspekcja, Odmowa, Wyłączone |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_Recoverable_Audit.json) |
