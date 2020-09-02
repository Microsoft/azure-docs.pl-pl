---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 07/22/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 5bec718de3f4f4b0ee78bacf9fc41cbedb8e71df
ms.sourcegitcommit: 3246e278d094f0ae435c2393ebf278914ec7b97b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/02/2020
ms.locfileid: "89379234"
---
## <a name="azure-security-benchmark"></a>Test porównawczy zabezpieczeń platformy Azure

[Test porównawczy zabezpieczeń platformy Azure](../../../../articles/security/benchmarks/overview.md) zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure. Aby dowiedzieć się, jak usługa ta została całkowicie zamapowana na test porównawczy zabezpieczeń platformy Azure, zobacz [pliki mapowania usługi Azure Security test](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

Aby sprawdzić, jak dostępne Azure Policy wbudowane dla wszystkich usług platformy Azure są mapowane na ten standard zgodności, zobacz [Azure Policy zgodność z przepisami — Azure Security test](../../../../articles/governance/policy/samples/azure-security-benchmark.md).

|Domena |Identyfikator kontrolki |Tytuł formantu |Zasady<br /><sub>(Azure Portal)</sub> |Wersja zasad<br /><sub>GitHub</sub>  |
|---|---|---|---|---|
|Bezpieczeństwo sieci |1,1 |Ochrona zasobów przy użyciu sieciowych grup zabezpieczeń lub zapory platformy Azure na Virtual Network |[Key Vault powinien używać punktu końcowego usługi sieci wirtualnej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea4d6841-2173-4317-9747-ff522a45120f) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_KeyVault_Audit.json) |
|Rejestrowanie i monitorowanie |2.3 |Włącz rejestrowanie inspekcji dla zasobów platformy Azure |[Dzienniki diagnostyczne w Key Vault powinny być włączone](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcf820ca0-f99e-4f3e-84fb-66e913812d21) |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_AuditDiagnosticLog_Audit.json) |
|Bezpieczna konfiguracja |7,11 |Bezpieczne zarządzanie kluczami tajnymi platformy Azure |[Key Vault obiektów powinno być możliwe do odzyskania](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b60c0b2-2dc2-4e1c-b5c9-abbed971de53) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_Recoverable_Audit.json) |
|Odzyskiwanie danych |9,4 |Zapewnienie ochrony kopii zapasowych i kluczy zarządzanych przez klienta |[Key Vault obiektów powinno być możliwe do odzyskania](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b60c0b2-2dc2-4e1c-b5c9-abbed971de53) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_Recoverable_Audit.json) |

## <a name="cis-microsoft-azure-foundations-benchmark"></a>CIS Microsoft Azure Foundations Benchmark

Aby zapoznać się ze sposobem, w jaki dostępne Azure Policy wbudowane dla wszystkich usług platformy Azure są mapowane na ten standard zgodności, zobacz [Azure Policy zgodność z przepisami — CIS Microsoft Azure fundacje testów porównawczych 1.1.0](../../../../articles/governance/policy/samples/cis-azure-1-1-0.md).
Aby uzyskać więcej informacji na temat tego standardu zgodności, zobacz artykuł dotyczący [testów usług CIS Microsoft Azure](https://www.cisecurity.org/benchmark/azure/).

|Domena |Identyfikator kontrolki |Tytuł formantu |Zasady<br /><sub>(Azure Portal)</sub> |Wersja zasad<br /><sub>GitHub</sub>  |
|---|---|---|---|---|
|Rejestrowanie i monitorowanie |5.1.7 |Upewnij się, że rejestrowanie dla magazynu kluczy platformy Azure jest włączone |[Dzienniki diagnostyczne w Key Vault powinny być włączone](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcf820ca0-f99e-4f3e-84fb-66e913812d21) |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_AuditDiagnosticLog_Audit.json) |
|Inne uwagi dotyczące zabezpieczeń |8.4 |Upewnij się, że magazyn kluczy jest możliwy do odzyskania |[Key Vault obiektów powinno być możliwe do odzyskania](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b60c0b2-2dc2-4e1c-b5c9-abbed971de53) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_Recoverable_Audit.json) |

