---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: e884d11aa3d86a2d9e156aeb14cf4d53a301f2b2
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98738821"
---
|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Punkt końcowy usługi bot powinien być prawidłowym identyfikatorem URI HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6164527b-e1ee-4882-8673-572f425f5e0a) |Dane mogą zostać naruszone podczas przesyłania. Istnieją protokoły, które zapewniają szyfrowanie w celu rozwiązywania problemów związanych z nieprawidłowym wykorzystaniem i manipulowaniem. Aby zapewnić, że botów komunikują się tylko za pośrednictwem szyfrowanych kanałów, ustaw punkt końcowy na prawidłowy identyfikator URI HTTPS. Dzięki temu protokół HTTPS jest używany do szyfrowania danych podczas przesyłania i jest również często wymagany do zgodności z przepisami lub standardami branżowymi. Odwiedź stronę: [https://docs.microsoft.com/azure/bot-service/bot-builder-security-guidelines](https://docs.microsoft.com/azure/bot-service/bot-builder-security-guidelines) . |Inspekcja, Odmów, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Bot%20Services/BotService_ValidEndpoint_Audit.json) |
