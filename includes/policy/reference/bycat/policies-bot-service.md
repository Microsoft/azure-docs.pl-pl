---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 18a4ad144870352c22ca7460786fdf3dbcaabeda
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107498944"
---
|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Bot Service punkt końcowy powinien być prawidłowym adresem URI protokołu HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6164527b-e1ee-4882-8673-572f425f5e0a) |Dane mogą zostać naruszone podczas przesyłania. Istnieją protokoły, które zapewniają szyfrowanie w celu rozwiązywania problemów związanych z nieprawidłowym wykorzystaniem i manipulowaniem. Aby upewnić się, że boty komunikują się tylko za pośrednictwem szyfrowanych kanałów, ustaw punkt końcowy na prawidłowy adres URI protokołu HTTPS. Dzięki temu protokół HTTPS jest używany do szyfrowania danych podczas przesyłania i często jest również wymagany do zapewnienia zgodności ze standardami regulacyjną lub branżowymi. Odwiedź stronę: [https://docs.microsoft.com/azure/bot-service/bot-builder-security-guidelines](https://docs.microsoft.com/azure/bot-service/bot-builder-security-guidelines) . |inspekcja, odmowa, wyłączone |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Bot%20Service/BotService_ValidEndpoint_Audit.json) |
|[Bot Service powinny być szyfrowane przy użyciu klucza zarządzanego przez klienta](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F51522a96-0869-4791-82f3-981000c2c67f) |Azure Bot Service automatycznie szyfruje zasób, aby chronić dane oraz spełniać zobowiązania organizacji dotyczące zabezpieczeń i zgodności. Domyślnie są używane klucze szyfrowania zarządzane przez firmę Microsoft. Aby uzyskać większą elastyczność zarządzania kluczami lub kontrolowania dostępu do subskrypcji, wybierz klucze zarządzane przez klienta, znane również jako bring your own key (BYOK). Dowiedz się więcej o szyfrowaniu Azure Bot Service danych: [https://docs.microsoft.com/azure/bot-service/bot-service-encryption](https://docs.microsoft.com/azure/bot-service/bot-service-encryption) . |inspekcja, odmowa, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Bot%20Service/BotService_CMKEnabled_Audit.json) |
