---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/31/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: f3d63180219caa99fed22a5f8249de7e8f1a3eea
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106097666"
---
|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[W magazynach kluczy powinna być włączona ochrona przed czyszczeniem](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b60c0b2-2dc2-4e1c-b5c9-abbed971de53) |Złośliwe usunięcie magazynu kluczy może prowadzić do utraty danych trwałych. Złośliwy Tester w organizacji może potencjalnie usuwać i czyścić magazyny kluczy. Ochrona przed przeczyszczeniem chroni przed atakami z niejawnym testerem, wymuszając obowiązkowy okres przechowywania dla nieusuniętych magazynów kluczy. Nikt nie jest w Twojej organizacji, a firma Microsoft będzie mogła przeczyścić Twoje magazyny kluczy w okresie przechowywania nietrwałego. |Inspekcja, Odmów, wyłączone |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_Recoverable_Audit.json) |
