---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: e721c25b3374e908c70fcec463625daeea0165a0
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107876131"
---
## <a name="azure-security-benchmark"></a>Test porównawczy zabezpieczeń platformy Azure

Test [porównawczy zabezpieczeń platformy Azure](/security/benchmark/azure/introduction) zawiera zalecenia dotyczące zabezpieczania rozwiązań w chmurze na platformie Azure. Aby zobaczyć, jak ta usługa całkowicie mapuje się na test porównawczy zabezpieczeń platformy Azure, zobacz pliki mapowania testu porównawczego zabezpieczeń [platformy Azure.](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

Aby sprawdzić, jak dostępne wbudowane Azure Policy dla wszystkich usług platformy Azure są mapowe na ten standard zgodności, zobacz Azure Policy Regulatory Compliance - Azure Security Benchmark (Zgodność z przepisami — test porównawczy zabezpieczeń platformy [Azure).](../../../../articles/governance/policy/samples/azure-security-benchmark.md)

|Domena |Identyfikator kontrolki |Tytuł kontrolki |Zasady<br /><sub>(Azure Portal)</sub> |Wersja zasad<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Zarządzanie tożsamością |Wiadomości błyskawiczne 1 |Standaryzacja Azure Active Directory jako centralnego systemu tożsamości i uwierzytelniania |[Service Fabric powinny używać tylko Azure Active Directory do uwierzytelniania klientów](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb54ed75b-3e1a-44ac-a333-05ba39b99ff0) |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditADAuth_Audit.json) |
|Ochrona danych |DP-5 |Szyfrowanie poufnych danych w spoczynku |[Service Fabric powinny mieć właściwość ClusterProtectionLevel ustawioną na EncryptAndSign](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F617c02be-7f02-4efd-8836-3180d47b6c68) |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditClusterProtectionLevel_Audit.json) |

## <a name="azure-security-benchmark-v1"></a>Test porównawczy zabezpieczeń platformy Azure w wersji 1

Test [porównawczy zabezpieczeń platformy Azure](/security/benchmark/azure/introduction) zawiera zalecenia dotyczące zabezpieczania rozwiązań w chmurze na platformie Azure. Aby zobaczyć, jak ta usługa całkowicie mapuje się na test porównawczy zabezpieczeń platformy Azure, zobacz pliki mapowania testu porównawczego zabezpieczeń [platformy Azure.](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

Aby sprawdzić, jak dostępne wbudowane Azure Policy dla wszystkich usług platformy Azure są mapowe na ten standard zgodności, zobacz Azure Policy Regulatory Compliance - Azure Security Benchmark (Zgodność z przepisami — test porównawczy zabezpieczeń platformy [Azure).](../../../../articles/governance/policy/samples/azure-security-benchmark.md)

|Domena |Identyfikator kontrolki |Tytuł kontrolki |Zasady<br /><sub>(Azure Portal)</sub> |Wersja zasad<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Tożsamość i kontrola dostępu |3.9 |Korzystanie z usługi Azure Active Directory |[Service Fabric klastry powinny używać tylko Azure Active Directory do uwierzytelniania klientów](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb54ed75b-3e1a-44ac-a333-05ba39b99ff0) |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditADAuth_Audit.json) |
|Ochrona danych |4.8 |Szyfrowanie poufnych informacji w spoczynku |[Service Fabric klastry powinny mieć właściwość ClusterProtectionLevel ustawioną na EncryptAndSign](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F617c02be-7f02-4efd-8836-3180d47b6c68) |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditClusterProtectionLevel_Audit.json) |

## <a name="cmmc-level-3"></a>CMMC Level 3

Aby sprawdzić, jak dostępne Azure Policy dla wszystkich usług platformy Azure są mapowe na ten standard zgodności, zobacz Azure Policy Regulatory Compliance - CMMC Level 3 (Zgodność z przepisami — [CMMC Poziom 3).](../../../../articles/governance/policy/samples/cmmc-l3.md)
Aby uzyskać więcej informacji na temat tego standardu zgodności, zobacz [Cybersecurity Maturity Model Certification (CMMC)](https://www.acq.osd.mil/cmmc/docs/CMMC_Model_Main_20200203.pdf).

|Domena |Identyfikator kontrolki |Tytuł kontrolki |Zasady<br /><sub>(Azure Portal)</sub> |Wersja zasad<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Ochrona systemu i komunikacji |SC.3.177 |Zastosowanie kryptografii zweryfikowanych przez fips, gdy jest używana do ochrony poufności cui. |[Service Fabric klastry powinny mieć właściwość ClusterProtectionLevel ustawioną na EncryptAndSign](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F617c02be-7f02-4efd-8836-3180d47b6c68) |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditClusterProtectionLevel_Audit.json) |
|Ochrona systemu i komunikacji |SC.3.191 |Ochrona poufności danych cui w spoczynku. |[Service Fabric powinny mieć właściwość ClusterProtectionLevel ustawioną na EncryptAndSign](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F617c02be-7f02-4efd-8836-3180d47b6c68) |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditClusterProtectionLevel_Audit.json) |

## <a name="iso-270012013"></a>ISO 27001:2013

Aby sprawdzić, jak dostępne Azure Policy dla wszystkich usług platformy Azure są mapowe na ten standard zgodności, zobacz [Azure Policy Regulatory Compliance - ISO 27001:2013 (Zgodność z przepisami — ISO 27001:2013).](../../../../articles/governance/policy/samples/iso-27001.md)
Aby uzyskać więcej informacji na temat tego standardu zgodności, [zobacz ISO 27001:2013](https://www.iso.org/isoiec-27001-information-security.html).

|Domena |Identyfikator kontrolki |Tytuł kontrolki |Zasady<br /><sub>(Azure Portal)</sub> |Wersja zasad<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Kontrola dostępu |9.2.3 |Zarządzanie uprawnieniami dostępu uprzywilejowanego |[Service Fabric powinny używać tylko Azure Active Directory do uwierzytelniania klientów](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb54ed75b-3e1a-44ac-a333-05ba39b99ff0) |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditADAuth_Audit.json) |
|Kryptografia |10.1.1 |Zasady dotyczące używania kontrolek kryptograficznych |[Service Fabric powinny mieć właściwość ClusterProtectionLevel ustawioną na EncryptAndSign](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F617c02be-7f02-4efd-8836-3180d47b6c68) |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditClusterProtectionLevel_Audit.json) |

## <a name="new-zealand-ism-restricted"></a>Nowa Zelandia ISM z ograniczeniami

Aby sprawdzić, jak dostępne wbudowane Azure Policy dla wszystkich usług platformy Azure są mapowanie na ten standard zgodności, zobacz [Azure Policy Regulatory Compliance - New Zelandia ISM Restricted](../../../../articles/governance/policy/samples/new-zealand-ism.md)(Zgodność z przepisami — nowa Zelandia — ograniczone przez ism).
Aby uzyskać więcej informacji na temat tego standardu zgodności, zobacz [New Zelandia ISM Restricted](https://www.nzism.gcsb.govt.nz/).

|Domena |Identyfikator kontrolki |Tytuł kontrolki |Zasady<br /><sub>(Azure Portal)</sub> |Wersja zasad<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Access Control i hasła |AC-2 |16.1.32 Identyfikacja użytkownika systemu |[Service Fabric klastry powinny używać tylko Azure Active Directory do uwierzytelniania klientów](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb54ed75b-3e1a-44ac-a333-05ba39b99ff0) |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditADAuth_Audit.json) |

## <a name="nist-sp-800-53-r4"></a>NIST SP 800-53 R4

Aby sprawdzić, jak dostępne Azure Policy dla wszystkich usług platformy Azure są mapowe na ten standard zgodności, zobacz [Azure Policy Regulatory Compliance - NIST SP 800-53 R4](../../../../articles/governance/policy/samples/nist-sp-800-53-r4.md).
Aby uzyskać więcej informacji na temat tego standardu zgodności, zobacz [NIST SP 800-53 R4](https://nvd.nist.gov/800-53).

|Domena |Identyfikator kontrolki |Tytuł kontrolki |Zasady<br /><sub>(Azure Portal)</sub> |Wersja zasad<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Kontrola dostępu |AC-2 (7) |Schematy zarządzania \| Role-Based kontami |[Service Fabric klastry powinny używać tylko Azure Active Directory do uwierzytelniania klientów](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb54ed75b-3e1a-44ac-a333-05ba39b99ff0) |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditADAuth_Audit.json) |

