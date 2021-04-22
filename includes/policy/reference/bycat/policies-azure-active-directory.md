---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 0bbd833b840809828cb13cf807a8c150d42283d8
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107866636"
---
|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Active Directory Domain Services zarządzane powinny używać trybu tylko TLS 1.2](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3aa87b5a-7813-4b57-8a43-42dd9df5aaa7) |Używaj trybu tylko TLS 1.2 dla domen zarządzanych. Domyślnie program Azure AD Domain Services korzystanie z szyfrów, takich jak NTLM v1 i TLS v1. Te szyfry mogą być wymagane w przypadku niektórych starszych aplikacji, ale są uznawane za słabe i można je wyłączyć, jeśli nie są potrzebne. Po włączeniu trybu tylko TLS 1.2 każdy klient, który wysyła żądanie, który nie korzysta z TLS 1.2, nie powiedzie się. Dowiedz się więcej na stronie [https://docs.microsoft.com/azure/active-directory-domain-services/secure-your-domain](https://docs.microsoft.com/azure/active-directory-domain-services/secure-your-domain) . |Inspekcja, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Active%20Directory/AADDomainServices_TLS_Audit.json) |
