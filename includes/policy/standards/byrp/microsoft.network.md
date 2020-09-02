---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 07/22/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 93dcd58550d16a25c9d886107692c03c782596ff
ms.sourcegitcommit: 3246e278d094f0ae435c2393ebf278914ec7b97b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/02/2020
ms.locfileid: "89379238"
---
## <a name="azure-security-benchmark"></a>Test porównawczy zabezpieczeń platformy Azure

[Test porównawczy zabezpieczeń platformy Azure](../../../../articles/security/benchmarks/overview.md) zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure. Aby dowiedzieć się, jak usługa ta została całkowicie zamapowana na test porównawczy zabezpieczeń platformy Azure, zobacz [pliki mapowania usługi Azure Security test](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

Aby sprawdzić, jak dostępne Azure Policy wbudowane dla wszystkich usług platformy Azure są mapowane na ten standard zgodności, zobacz [Azure Policy zgodność z przepisami — Azure Security test](../../../../articles/governance/policy/samples/azure-security-benchmark.md).

|Domena |Identyfikator kontrolki |Tytuł formantu |Zasady<br /><sub>(Azure Portal)</sub> |Wersja zasad<br /><sub>GitHub</sub>  |
|---|---|---|---|---|
|Bezpieczeństwo sieci |1,1 |Ochrona zasobów przy użyciu sieciowych grup zabezpieczeń lub zapory platformy Azure na Virtual Network |[Cały ruch internetowy powinien być kierowany przy użyciu wdrożonej zapory platformy Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffc5e4038-4584-4632-8c85-c0448d374b2c) |[2.0.0 — wersja zapoznawcza](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ASC_All_Internet_traffic_should_be_routed_via_Azure_Firewall.json) |
|Bezpieczeństwo sieci |1,1 |Ochrona zasobów przy użyciu sieciowych grup zabezpieczeń lub zapory platformy Azure na Virtual Network |[Podsieci powinny być skojarzone z sieciową grupą zabezpieczeń](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|Bezpieczeństwo sieci |1,1 |Ochrona zasobów przy użyciu sieciowych grup zabezpieczeń lub zapory platformy Azure na Virtual Network |[Maszyny wirtualne powinny być połączone z zatwierdzoną siecią wirtualną](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |
|Bezpieczeństwo sieci |1,1 |Ochrona zasobów przy użyciu sieciowych grup zabezpieczeń lub zapory platformy Azure na Virtual Network |[Sieci wirtualne powinny używać określonej bramy sieci wirtualnej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff1776c76-f58c-4245-a8d0-2b207198dc8b) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetwork_ApprovedVirtualNetworkGateway_AuditIfNotExists.json) |
|Bezpieczeństwo sieci |1,2 |Monitoruj i Rejestruj konfigurację oraz ruch sieci wirtualnych, podsieci i kart sieciowych |[Network Watcher powinna być włączona](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6e2945c-0b7b-40f5-9233-7a5323b5cdc6) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Enabled_Audit.json) |
|Bezpieczeństwo sieci |1.4 |Odmów komunikacji ze znanymi złośliwymi adresami IP |[Cały ruch internetowy powinien być kierowany przy użyciu wdrożonej zapory platformy Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffc5e4038-4584-4632-8c85-c0448d374b2c) |[2.0.0 — wersja zapoznawcza](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ASC_All_Internet_traffic_should_be_routed_via_Azure_Firewall.json) |
|Bezpieczeństwo sieci |1.4 |Odmów komunikacji ze znanymi złośliwymi adresami IP |[Należy włączyć Standard Azure DDoS Protection](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa7aca53f-2ed4-4466-a25e-0b45ade68efd) |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableDDoSProtection_Audit.json) |
|Bezpieczeństwo sieci |1.5 |Rejestrowanie pakietów sieciowych i dzienników przepływów |[Network Watcher powinna być włączona](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6e2945c-0b7b-40f5-9233-7a5323b5cdc6) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Enabled_Audit.json) |

## <a name="cis-microsoft-azure-foundations-benchmark"></a>CIS Microsoft Azure Foundations Benchmark

Aby zapoznać się ze sposobem, w jaki dostępne Azure Policy wbudowane dla wszystkich usług platformy Azure są mapowane na ten standard zgodności, zobacz [Azure Policy zgodność z przepisami — CIS Microsoft Azure fundacje testów porównawczych 1.1.0](../../../../articles/governance/policy/samples/cis-azure-1-1-0.md).
Aby uzyskać więcej informacji na temat tego standardu zgodności, zobacz artykuł dotyczący [testów usług CIS Microsoft Azure](https://www.cisecurity.org/benchmark/azure/).

|Domena |Identyfikator kontrolki |Tytuł formantu |Zasady<br /><sub>(Azure Portal)</sub> |Wersja zasad<br /><sub>GitHub</sub>  |
|---|---|---|---|---|
|Security Center |2.9 |Upewnij się, że domyślne ustawienie zasad ASC "Włącz monitorowanie zapory nowej generacji (zapory następnej generacji)" nie jest wyłączone |[Podsieci powinny być skojarzone z sieciową grupą zabezpieczeń](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|Networking |6.1 |Upewnij się, że dostęp do protokołu RDP jest ograniczony z Internetu |[Dostęp RDP z Internetu powinien być zablokowany](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe372f825-a257-4fb8-9175-797a8a8627d6) |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkSecurityGroup_RDPAccess_Audit.json) |
|Networking |6,2 |Upewnij się, że dostęp SSH jest ograniczony z Internetu |[Dostęp SSH z Internetu powinien być zablokowany](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2c89a2e5-7285-40fe-afe0-ae8654b92fab) |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkSecurityGroup_SSHAccess_Audit.json) |
|Networking |6.5 |Upewnij się, że Network Watcher jest "Enabled" |[Network Watcher powinna być włączona](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6e2945c-0b7b-40f5-9233-7a5323b5cdc6) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Enabled_Audit.json) |

## <a name="nist-sp-800-171-r2"></a>NIST SP 800-171 R2

Aby sprawdzić, jak dostępne Azure Policy wbudowane dla wszystkich usług platformy Azure są mapowane na ten standard zgodności, zobacz [Azure Policy zgodność z przepisami — NIST SP 800-171 R2](../../../../articles/governance/policy/samples/nist-sp-800-171-r2.md).
Aby uzyskać więcej informacji na temat tego standardu zgodności, zobacz [NIST SP 800-171 R2](https://csrc.nist.gov/publications/detail/sp/800-171/rev-2/final).

|Domena |Identyfikator kontrolki |Tytuł formantu |Zasady<br /><sub>(Azure Portal)</sub> |Wersja zasad<br /><sub>GitHub</sub>  |
|---|---|---|---|---|
|Integralność systemu i informacji |3.14.6 |Monitorowanie systemów organizacyjnych, w tym ruchu przychodzącego i wychodzącego, w celu wykrywania ataków i wskaźników potencjalnych ataków. |[Network Watcher powinna być włączona](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6e2945c-0b7b-40f5-9233-7a5323b5cdc6) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Enabled_Audit.json) |

## <a name="nist-sp-800-53-r4"></a>NIST SP 800-53 R4

Aby sprawdzić, jak dostępne Azure Policy wbudowane dla wszystkich usług platformy Azure są mapowane na ten standard zgodności, zobacz [Azure Policy zgodność z przepisami — NIST SP 800-53 R4](../../../../articles/governance/policy/samples/nist-sp-800-53-r4.md).
Aby uzyskać więcej informacji na temat tego standardu zgodności, zobacz [NIST SP 800-53 R4](https://nvd.nist.gov/800-53).

|Domena |Identyfikator kontrolki |Tytuł formantu |Zasady<br /><sub>(Azure Portal)</sub> |Wersja zasad<br /><sub>GitHub</sub>  |
|---|---|---|---|---|
|Ochrona systemu i komunikacji |SC-5 |Odmowa usługi ochrony |[Należy włączyć Standard Azure DDoS Protection](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa7aca53f-2ed4-4466-a25e-0b45ade68efd) |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableDDoSProtection_Audit.json) |

