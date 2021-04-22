---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 222a04afb0b9e1b0b8cb7f8bf2d300790b0c86fe
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107873737"
---
## <a name="azure-security-benchmark"></a>Test porównawczy zabezpieczeń platformy Azure

Test [porównawczy zabezpieczeń platformy Azure](/security/benchmark/azure/introduction) zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure. Aby zobaczyć, jak ta usługa całkowicie mapuje się na test porównawczy zabezpieczeń platformy Azure, zobacz pliki mapowania testu [porównawczego zabezpieczeń platformy Azure.](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

Aby sprawdzić, jak dostępne Azure Policy dla wszystkich usług platformy Azure są mapowe na ten standard zgodności, zobacz Azure Policy Regulatory Compliance - Azure Security Benchmark (Zgodność z przepisami — test porównawczy zabezpieczeń platformy [Azure).](../../../../articles/governance/policy/samples/azure-security-benchmark.md)

|Domena |Identyfikator kontrolki |Tytuł kontrolki |Zasady<br /><sub>(Azure Portal)</sub> |Wersja zasad<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Bezpieczeństwo sieci |NS-1 |Implementowanie zabezpieczeń dla ruchu wewnętrznego |[Azure Cosmos DB powinny mieć reguły zapory](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F862e97cf-49fc-4a5c-9de4-40d4e2e7c8eb) |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cosmos%20DB/Cosmos_NetworkRulesExist_Audit.json) |
|Bezpieczeństwo sieci |NS-4 |Ochrona aplikacji i usług przed atakami w sieci zewnętrznej |[Azure Cosmos DB powinny mieć reguły zapory](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F862e97cf-49fc-4a5c-9de4-40d4e2e7c8eb) |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cosmos%20DB/Cosmos_NetworkRulesExist_Audit.json) |
|Ochrona danych |DP-5 |Szyfrowanie danych poufnych w spoczynku |[Azure Cosmos DB powinny używać kluczy zarządzanych przez klienta do szyfrowania danych w spoczynku](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1f905d99-2ab7-462c-a6b0-f709acca6c8f) |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cosmos%20DB/Cosmos_CMK_Deny.json) |

## <a name="azure-security-benchmark-v1"></a>Test porównawczy zabezpieczeń platformy Azure w wersji 1

Test [porównawczy zabezpieczeń platformy Azure](/security/benchmark/azure/introduction) zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure. Aby zobaczyć, jak ta usługa całkowicie mapuje się na test porównawczy zabezpieczeń platformy Azure, zobacz pliki mapowania testu [porównawczego zabezpieczeń platformy Azure.](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

Aby sprawdzić, jak dostępne wbudowane Azure Policy dla wszystkich usług platformy Azure są mapowe na ten standard zgodności, zobacz Azure Policy Regulatory Compliance - Azure Security Benchmark (Zgodność z przepisami — test porównawczy zabezpieczeń platformy [Azure).](../../../../articles/governance/policy/samples/azure-security-benchmark.md)

|Domena |Identyfikator kontrolki |Tytuł kontrolki |Zasady<br /><sub>(Azure Portal)</sub> |Wersja zasad<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Bezpieczeństwo sieci |1.1 |Ochrona zasobów przy użyciu sieciowych grup zabezpieczeń lub Azure Firewall na Virtual Network |[Cosmos DB używać punktu końcowego usługi dla sieci wirtualnej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe0a2b1a3-f7f9-4569-807f-2a9edebdf4d9) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_CosmosDB_Audit.json) |

## <a name="cmmc-level-3"></a>CMMC Level 3

Aby sprawdzić, jak dostępne wbudowane Azure Policy dla wszystkich usług platformy Azure są mapowe na ten standard zgodności, zobacz Azure Policy Regulatory Compliance - CMMC Level 3 (Zgodność z przepisami — [CMMC Level 3).](../../../../articles/governance/policy/samples/cmmc-l3.md)
Aby uzyskać więcej informacji na temat tego standardu zgodności, zobacz [Cybersecurity Maturity Model Certification (CMMC)](https://www.acq.osd.mil/cmmc/docs/CMMC_Model_Main_20200203.pdf).

|Domena |Identyfikator kontrolki |Tytuł kontrolki |Zasady<br /><sub>(Azure Portal)</sub> |Wersja zasad<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Reagowanie na zdarzenia |IR.2.093 |Wykrywanie i zgłaszanie zdarzeń. |[Wdrażanie usługi Advanced Threat Protection dla Cosmos DB zabezpieczeń](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb5f04e03-92a3-4b09-9410-2cc5e5047656) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cosmos%20DB/CosmosDbAdvancedThreatProtection_Deploy.json) |

## <a name="hipaa-hitrust-92"></a>HIPAA HITRUST 9.2

Aby sprawdzić, jak dostępne wbudowane Azure Policy dla wszystkich usług platformy Azure są mapowanie na ten standard zgodności, zobacz [Azure Policy Regulatory Compliance - HIPAA HITRUST 9.2](../../../../articles/governance/policy/samples/hipaa-hitrust-9-2.md).
Aby uzyskać więcej informacji na temat tego standardu zgodności, [zobacz HIPAA HITRUST 9.2.](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html)

|Domena |Identyfikator kontrolki |Tytuł kontrolki |Zasady<br /><sub>(Azure Portal)</sub> |Wersja zasad<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Podział w sieciach |0805.01m1Organizational.12 –01.m |Bramy zabezpieczeń organizacji (np. zapory) wymuszają zasady zabezpieczeń i są skonfigurowane do filtrowania ruchu między domenami, blokowania nieautoryzowanego dostępu i są używane do zachowania podziału między wewnętrznymi segmentami sieci przewodowej, wewnętrznej bezprzewodowej i zewnętrznej (np. Internetem), w tym sieciami DMZ, oraz wymuszania zasad kontroli dostępu dla każdej domeny. |[Cosmos DB używać punktu końcowego usługi dla sieci wirtualnej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe0a2b1a3-f7f9-4569-807f-2a9edebdf4d9) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_CosmosDB_Audit.json) |
|Podział w sieciach |0806.01m2Organizational.12356–01.m |Sieć organizacji jest logicznie i fizycznie segmentowana przy użyciu zdefiniowanego obwodu zabezpieczeń i stopniowanego zestawu kontrolek, w tym podsieci dla publicznie dostępnych składników systemowych, które są logicznie oddzielone od sieci wewnętrznej na podstawie wymagań organizacyjnych; Ruch jest kontrolowany na podstawie wymaganych funkcji i klasyfikacji danych/systemów na podstawie oceny ryzyka i odpowiednich wymagań dotyczących zabezpieczeń. |[Cosmos DB używać punktu końcowego usługi dla sieci wirtualnej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe0a2b1a3-f7f9-4569-807f-2a9edebdf4d9) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_CosmosDB_Audit.json) |
|Podział w sieciach |0894.01m2Organizational.7 – 01.m |Sieci są oddzielone od sieci na poziomie produkcyjnym podczas migrowania serwerów fizycznych, aplikacji lub danych do serwerów zwirtualizowanych. |[Cosmos DB używać punktu końcowego usługi dla sieci wirtualnej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe0a2b1a3-f7f9-4569-807f-2a9edebdf4d9) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_CosmosDB_Audit.json) |
|Kontrolki sieciowe |0864.09m2Organizational.12 –09.m |Ograniczenia użycia i wskazówki dotyczące implementacji są formalnie zdefiniowane dla usługi VoIP, w tym dotyczące autoryzacji i monitorowania usługi. |[Cosmos DB używać punktu końcowego usługi dla sieci wirtualnej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe0a2b1a3-f7f9-4569-807f-2a9edebdf4d9) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_CosmosDB_Audit.json) |

