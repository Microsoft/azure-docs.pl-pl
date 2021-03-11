---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/10/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 4b71ec6158b50e3e95bc858b2f300406804fdfd6
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102611064"
---
|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Punkt końcowy usługi bot powinien być prawidłowym identyfikatorem URI HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6164527b-e1ee-4882-8673-572f425f5e0a) |Dane mogą zostać naruszone podczas przesyłania. Istnieją protokoły, które zapewniają szyfrowanie w celu rozwiązywania problemów związanych z nieprawidłowym wykorzystaniem i manipulowaniem. Aby zapewnić, że botów komunikują się tylko za pośrednictwem szyfrowanych kanałów, ustaw punkt końcowy na prawidłowy identyfikator URI HTTPS. Dzięki temu protokół HTTPS jest używany do szyfrowania danych podczas przesyłania i jest również często wymagany do zgodności z przepisami lub standardami branżowymi. Odwiedź stronę: [https://docs.microsoft.com/azure/bot-service/bot-builder-security-guidelines](https://docs.microsoft.com/azure/bot-service/bot-builder-security-guidelines) . |Inspekcja, Odmów, wyłączone |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Bot%20Service/BotService_ValidEndpoint_Audit.json) |
|[Usługa bot powinna być szyfrowana za pomocą klucza zarządzanego przez klienta](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F51522a96-0869-4791-82f3-981000c2c67f) |Azure Bot Service automatycznie szyfruje zasób, aby chronić dane i spełniać zobowiązania dotyczące zabezpieczeń i zgodności w organizacji. Domyślnie używane są klucze szyfrowania zarządzane przez firmę Microsoft. Aby uzyskać większą elastyczność w zakresie zarządzania kluczami lub kontrolowania dostępu do subskrypcji, wybierz klucze zarządzane przez klienta, znane także jako BYOK. Dowiedz się więcej na temat szyfrowania Azure Bot Service: [https://docs.microsoft.com/azure/bot-service/bot-service-encryption](https://docs.microsoft.com/azure/bot-service/bot-service-encryption) . |Inspekcja, Odmów, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Bot%20Service/BotService_CMKEnabled_Audit.json) |
