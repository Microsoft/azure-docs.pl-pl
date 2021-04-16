---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 024d5a8b71d269da478bc8a9e3559b1eb4b92c63
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107505471"
---
## <a name="cmmc-level-3"></a>CMMC Level 3

Aby sprawdzić, jak dostępne wbudowane Azure Policy dla wszystkich usług platformy Azure są mapowe na ten standard zgodności, zobacz Azure Policy Regulatory Compliance - CMMC Level 3 (Zgodność z przepisami — [CMMC Level 3).](../../../../articles/governance/policy/samples/cmmc-l3.md)
Aby uzyskać więcej informacji na temat tego standardu zgodności, zobacz [Cybersecurity Maturity Model Certification (CMMC)](https://www.acq.osd.mil/cmmc/docs/CMMC_Model_Main_20200203.pdf).

|Domena |Identyfikator kontrolki |Tytuł kontrolki |Zasady<br /><sub>(Azure Portal)</sub> |Wersja zasad<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Kontrola dostępu |AC.1.001 |Ogranicz dostęp systemu informacji do autoryzowanych użytkowników, procesów działających w imieniu autoryzowanych użytkowników i urządzeń (w tym innych systemów informacyjnych). |[CorS nie powinien zezwalać każdej domenie na dostęp do interfejsu API for FHIR](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fea8f8a-4169-495d-8307-30ec335f387d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_RestrictCORSAccess_Audit.json) |
|Kontrola dostępu |AC.1.002 |Ogranicz dostęp systemu informacji do typów transakcji i funkcji, które autoryzowani użytkownicy mogą wykonywać. |[CorS nie powinien zezwalać każdej domenie na dostęp do interfejsu API for FHIR](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fea8f8a-4169-495d-8307-30ec335f387d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_RestrictCORSAccess_Audit.json) |
|Kontrola dostępu |AC.2.016 |Sterowanie przepływem cui zgodnie z zatwierdzonych autoryzacji. |[CorS nie powinien zezwalać każdej domenie na dostęp do interfejsu API for FHIR](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fea8f8a-4169-495d-8307-30ec335f387d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_RestrictCORSAccess_Audit.json) |
|Zarządzanie konfiguracją |CM.3.068 |Ograniczanie, wyłączanie lub uniemożliwianie korzystania z nieocenowych programów, funkcji, portów, protokołów i usług. |[CorS nie powinien zezwalać każdej domenie na dostęp do interfejsu API for FHIR](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fea8f8a-4169-495d-8307-30ec335f387d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_RestrictCORSAccess_Audit.json) |
|Ochrona systemu i komunikacji |SC.3.177 |Zastosowanie kryptografii zweryfikowanych przez fips, gdy jest używana do ochrony poufności cui. |[Azure API for FHIR używać klucza zarządzanego przez klienta do szyfrowania danych w spoczynku](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F051cba44-2429-45b9-9649-46cec11c7119) |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_EnableByok_Audit.json) |
|Ochrona systemu i komunikacji |SC.3.183 |Domyślnie odrzucaj ruch komunikacji sieciowej i zezwalaj na ruch komunikacji sieciowej przez wyjątek (tj. odmów wszystkim, zezwol z wyjątkiem). |[CorS nie powinien zezwalać każdej domenie na dostęp do interfejsu API for FHIR](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fea8f8a-4169-495d-8307-30ec335f387d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_RestrictCORSAccess_Audit.json) |

