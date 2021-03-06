---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 28de9d80a1e5568b70f48e789233af38388fbe70
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107866651"
---
|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure API for FHIR używać klucza zarządzanego przez klienta do szyfrowania danych w spoczynku](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F051cba44-2429-45b9-9649-46cec11c7119) |Użyj klucza zarządzanego przez klienta, aby kontrolować szyfrowanie danych przechowywanych w programie Azure API for FHIR, gdy jest to wymaganie prawne lub zgodność. Klucze zarządzane przez klienta zapewniają również podwójne szyfrowanie, dodając drugą warstwę szyfrowania na podstawie domyślnego szyfrowania wykonywanego przy użyciu kluczy zarządzanych przez usługę. |inspekcja, wyłączone |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_EnableByok_Audit.json) |
|[Azure API for FHIR używać linku prywatnego](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1ee56206-5dd1-42ab-b02d-8aae8b1634ce) |Azure API for FHIR musi mieć co najmniej jedno zatwierdzone połączenie prywatnego punktu końcowego. Klienci w sieci wirtualnej mogą bezpiecznie uzyskać dostęp do zasobów, które mają połączenia z prywatnym punktem końcowym za pośrednictwem linków prywatnych. Aby uzyskać więcej informacji, odwiedź stronę: [https://aka.ms/fhir-privatelink](https://aka.ms/fhir-privatelink) . |Inspekcja, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_PrivateLink_Audit.json) |
|[CorS nie powinien zezwalać każdej domenie na dostęp do interfejsu API for FHIR](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fea8f8a-4169-495d-8307-30ec335f387d) |Współużytkowania zasobów między źródłami (CORS) nie należy zezwalać wszystkim domenom na dostęp do interfejsu API for FHIR. Aby chronić interfejs API dla platformy FHIR, usuń dostęp dla wszystkich domen i jawnie zdefiniuj domeny, które mogą się łączyć. |inspekcja, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_RestrictCORSAccess_Audit.json) |
