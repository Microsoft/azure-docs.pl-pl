---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/24/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 299997dcfcd282b6e295cfef11f806fe74e53f11
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105033236"
---
|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Urządzenia brzegowe Azure Stack powinny używać podwójnego szyfrowania](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4ac1030-89c5-4697-8e00-28b5ba6a8811) |Aby zabezpieczyć dane przechowywane na urządzeniu, upewnij się, że jest on szyfrowany podwójnie, dostęp do danych jest kontrolowany, a po zdezaktywowaniu urządzenia dane są bezpiecznie usuwane z dysków danych. Podwójne szyfrowanie to użycie dwóch warstw szyfrowania: BitLocker XTS-AES 256-bitowe szyfrowanie na woluminach danych i wbudowane szyfrowanie dysków twardych. Więcej informacji zawiera dokumentacja dotycząca określonego urządzenia brzegowego stosu. |Inspekcja, Odmów, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Stack%20Edge/AzureStackEdge_DoubleEncryption_Audit.json) |
