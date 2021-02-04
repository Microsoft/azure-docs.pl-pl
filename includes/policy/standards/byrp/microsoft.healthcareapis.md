---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/04/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: f802758c20e06a8e7201c08087b990c4924e434b
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/04/2021
ms.locfileid: "99555288"
---
## <a name="cmmc-level-3"></a>Poziom 3 CMMC

Aby sprawdzić, jak dostępne Azure Policy wbudowane dla wszystkich usług platformy Azure są mapowane na ten standard zgodności, zobacz [Azure Policy zgodność z przepisami — poziom CMMC 3](../../../../articles/governance/policy/samples/cmmc-l3.md).
Aby uzyskać więcej informacji na temat tego standardu zgodności, zobacz [cyberbezpieczeństwa data_spłaty model Certification (CMMC)](https://www.acq.osd.mil/cmmc/docs/CMMC_Model_Main_20200203.pdf).

|Domena |Identyfikator kontrolki |Tytuł formantu |Zasady<br /><sub>(Azure Portal)</sub> |Wersja zasad<br /><sub>GitHub</sub>  |
|---|---|---|---|---|
|Kontrola dostępu |AC. 1.001 |Ogranicz dostęp do systemu informacji do autoryzowanych użytkowników, procesów działających w imieniu autoryzowanych użytkowników i urządzeń (w tym innych systemów informacyjnych). |[Mechanizm CORS nie powinien zezwalać każdej domenie na dostęp do interfejsu API dla FHIR](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fea8f8a-4169-495d-8307-30ec335f387d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_RestrictCORSAccess_Audit.json) |
|Kontrola dostępu |AC. 1.002 |Ogranicz dostęp do systemu informacji do typów transakcji i funkcji, które mogą być wykonywane przez autoryzowanych użytkowników. |[Mechanizm CORS nie powinien zezwalać każdej domenie na dostęp do interfejsu API dla FHIR](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fea8f8a-4169-495d-8307-30ec335f387d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_RestrictCORSAccess_Audit.json) |
|Kontrola dostępu |AC. 2.016 |Kontroluj przepływ CUI zgodnie z zatwierdzonymi autoryzacjami. |[Mechanizm CORS nie powinien zezwalać każdej domenie na dostęp do interfejsu API dla FHIR](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fea8f8a-4169-495d-8307-30ec335f387d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_RestrictCORSAccess_Audit.json) |
|Zarządzanie konfiguracją |CM. 3.068 |Ogranicz, wyłącz lub uniemożliwiaj korzystanie z nieistotnych programów, funkcji, portów, protokołów i usług. |[Mechanizm CORS nie powinien zezwalać każdej domenie na dostęp do interfejsu API dla FHIR](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fea8f8a-4169-495d-8307-30ec335f387d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_RestrictCORSAccess_Audit.json) |
|Ochrona systemu i komunikacji |SC. 3.177 |Zastosowanie kryptografii opartej na standardzie FIPS do ochrony poufności CUI. |[Usługa Azure API for FHIR powinna używać klucza zarządzanego przez klienta (CMK) do szyfrowania danych przechowywanych w usłudze REST](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F051cba44-2429-45b9-9649-46cec11c7119) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_EnableByok_Audit.json) |
|Ochrona systemu i komunikacji |SC. 3.183 |Domyślnie Odmów ruchu sieciowego komunikacji sieciowej i Zezwalaj na ruch związany z komunikacją sieciową przez wyjątek (tj. Odmów wszystkich, zezwalania przez wyjątek). |[Mechanizm CORS nie powinien zezwalać każdej domenie na dostęp do interfejsu API dla FHIR](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fea8f8a-4169-495d-8307-30ec335f387d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_RestrictCORSAccess_Audit.json) |

