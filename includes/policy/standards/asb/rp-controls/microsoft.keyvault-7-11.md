---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 8a6d26eb0fb795f0ca047ff4a976a5b31b9493ee
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107511957"
---
|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Magazyny kluczy powinny mieć włączoną ochronę przed przeczyszczaniem](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b60c0b2-2dc2-4e1c-b5c9-abbed971de53) |Złośliwe usunięcie magazynu kluczy może prowadzić do trwałej utraty danych. Złośliwy niejawny tester w organizacji może potencjalnie usuwać i przeczyścić magazyny kluczy. Ochrona przed przeczyszczaniem chroni przed atakami niejawnych testerów, wymuszając obowiązkowy okres przechowywania nieukończonym magazynów kluczy. Nikt wewnątrz organizacji ani firma Microsoft nie będzie mógł przeczyścić twoich magazynów kluczy w okresie przechowywania usuwania nie soft delete. |Inspekcja, Odmowa, Wyłączone |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_Recoverable_Audit.json) |
