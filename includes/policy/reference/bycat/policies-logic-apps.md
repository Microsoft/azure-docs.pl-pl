---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: b70057229a2d1fe47ca5e27f9ee4f1338521ff46
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107867122"
---
|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Logic Apps środowisko usługi integracji powinny być szyfrowane przy użyciu kluczy zarządzanych przez klienta](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1fafeaf6-7927-4059-a50a-8eb2a7a6f2b5) |Wdrażanie w środowisko usługi integracji w celu zarządzania szyfrowaniem danych Logic Apps danych przy użyciu kluczy zarządzanych przez klienta. Domyślnie dane klienta są szyfrowane za pomocą kluczy zarządzanych przez usługę, ale klucze zarządzane przez klienta są często wymagane do spełnienia standardów zgodności z przepisami. Klucze zarządzane przez klienta umożliwiają szyfrowanie danych przy użyciu klucza Azure Key Vault utworzonego i należącego do Ciebie. Masz pełną kontrolę i odpowiedzialność za cykl życia klucza, w tym rotację i zarządzanie. |Inspekcja, Odmowa, Wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Logic%20Apps/LogicApps_ISEWithCustomerManagedKey_AuditDeny.json) |
|[Logic Apps należy wdrożyć w środowisko usługi integracji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdc595cb1-1cde-45f6-8faf-f88874e1c0e1) |Wdrażanie Logic Apps w środowisko usługi integracji sieci wirtualnej odblokowuje zaawansowane funkcje sieci Logic Apps i zabezpieczeń oraz zapewnia większą kontrolę nad konfiguracją sieci. Dowiedz się więcej na stronie: [https://aka.ms/integration-service-environment](https://aka.ms/integration-service-environment) . Wdrażanie w środowisko usługi integracji umożliwia również szyfrowanie za pomocą kluczy zarządzanych przez klienta, co zapewnia rozszerzoną ochronę danych dzięki możliwości zarządzania kluczami szyfrowania. Często jest to zgodne z wymaganiami zgodności. |Inspekcja, Odmowa, Wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Logic%20Apps/LogicApps_LogicAppsInISE_AuditDeny.json) |
|[Dzienniki zasobów w Logic Apps powinny być włączone](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34f95f76-5386-4de7-b824-0d8478470c9d) |Inspekcja włączania dzienników zasobów. Dzięki temu można odtworzyć trasy działań do użycia na potrzeby badania. w przypadku wystąpienia zdarzenia naruszenia zabezpieczeń lub naruszenia zabezpieczeń sieci |AuditIfNotExists, Disabled |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Logic%20Apps/LogicApps_AuditDiagnosticLog_Audit.json) |
