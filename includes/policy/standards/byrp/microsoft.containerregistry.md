---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 10/20/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 53816717bbb93ebe699a8590b6fd711601efa832
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2020
ms.locfileid: "92308283"
---
## <a name="azure-security-benchmark"></a>Test porównawczy zabezpieczeń platformy Azure

[Test porównawczy zabezpieczeń platformy Azure](../../../../articles/security/benchmarks/overview.md) zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure. Aby dowiedzieć się, jak usługa ta została całkowicie zamapowana na test porównawczy zabezpieczeń platformy Azure, zobacz [pliki mapowania usługi Azure Security test](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

Aby sprawdzić, jak dostępne Azure Policy wbudowane dla wszystkich usług platformy Azure są mapowane na ten standard zgodności, zobacz [Azure Policy zgodność z przepisami — Azure Security test](../../../../articles/governance/policy/samples/azure-security-benchmark.md).

|Obszar |Identyfikator kontrolki |Tytuł formantu |Zasady<br /><sub>(Azure Portal)</sub> |Wersja zasad<br /><sub>GitHub</sub>  |
|---|---|---|---|---|
|Bezpieczeństwo sieci |1,1 |Ochrona zasobów przy użyciu sieciowych grup zabezpieczeń lub zapory platformy Azure na Virtual Network |[Container Registry powinien używać punktu końcowego usługi sieci wirtualnej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |[1.0.0 — wersja zapoznawcza](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |

## <a name="hipaa-hitrust-92"></a>HIPAA HITRUST 9.2

Aby sprawdzić, jak dostępne Azure Policy wbudowane dla wszystkich usług platformy Azure są mapowane na ten standard zgodności, zobacz [Azure Policy zgodność z przepisami — HIPAA HITRUST 9,2](../../../../articles/governance/policy/samples/hipaa-hitrust-9-2.md).
Aby uzyskać więcej informacji na temat tego standardu zgodności, zobacz [HIPAA HITRUST 9,2](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html).

|Obszar |Identyfikator kontrolki |Tytuł formantu |Zasady<br /><sub>(Azure Portal)</sub> |Wersja zasad<br /><sub>GitHub</sub>  |
|---|---|---|---|---|
|Dzielenie w sieci |0805.01 m1Organizational. 12-01. m |Bramy zabezpieczeń organizacji (np. zapory) wymuszają zasady zabezpieczeń i są skonfigurowane do filtrowania ruchu między domenami, blokują nieautoryzowany dostęp i służą do obsługi rozdzielania między wewnętrzną przewodową, wewnętrzną siecią bezprzewodową i zewnętrznymi segmentami sieci (np. Internetem), w tym stref DMZ i wymuszanie zasad kontroli dostępu dla każdej z domen. |[Container Registry powinien używać punktu końcowego usługi sieci wirtualnej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |[1.0.0 — wersja zapoznawcza](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |
|Dzielenie w sieci |0806.01 m2Organizational. 12356-01. m |Sieć organizacji jest logicznie i fizycznie dzielona ze zdefiniowanym obwodem zabezpieczeń oraz stopniowanym zestawem kontrolek, w tym podsieciami dla publicznie dostępnych składników systemu, które są logicznie oddzielone od sieci wewnętrznej, w oparciu o wymagania organizacyjne. ruch jest kontrolowany w oparciu o funkcje wymagane i Klasyfikacja danych/systemów w oparciu o ocenę ryzyka i odpowiednie wymagania dotyczące zabezpieczeń. |[Container Registry powinien używać punktu końcowego usługi sieci wirtualnej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |[1.0.0 — wersja zapoznawcza](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |
|Dzielenie w sieci |0894.01 m2Organizational. 7-01. m |Sieci są segregowane z sieci na poziomie produkcyjnym podczas migrowania serwerów fizycznych, aplikacji lub danych do serwerów zwirtualizowanych. |[Container Registry powinien używać punktu końcowego usługi sieci wirtualnej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |[1.0.0 — wersja zapoznawcza](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |
|Kontrolki sieci |0868.09 m3Organizational. 18 – 09. m |Organizacja tworzy konfigurację zapory w celu ograniczenia ruchu przychodzącego i wychodzącego do programu, który jest niezbędny dla środowiska danych objętych usługą. |[Container Registry powinien używać punktu końcowego usługi sieci wirtualnej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |[1.0.0 — wersja zapoznawcza](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |
|Kontrolki sieci |0869.09 m3Organizational. 19 – 09. m |Pliki konfiguracji routera są zabezpieczone i synchronizowane. |[Container Registry powinien używać punktu końcowego usługi sieci wirtualnej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |[1.0.0 — wersja zapoznawcza](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |
|Kontrolki sieci |0870.09 m3Organizational. 20-09. m |Odmowa dostępu do wszystkich serwerów proxy, z wyjątkiem tych hostów, portów i usług, które są jawnie wymagane. |[Container Registry powinien używać punktu końcowego usługi sieci wirtualnej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |[1.0.0 — wersja zapoznawcza](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |
|Kontrolki sieci |0871.09 m3Organizational. 22-09. m |Autorytatywne serwery DNS są podzielone na role wewnętrzne i zewnętrzne. |[Container Registry powinien używać punktu końcowego usługi sieci wirtualnej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |[1.0.0 — wersja zapoznawcza](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |

