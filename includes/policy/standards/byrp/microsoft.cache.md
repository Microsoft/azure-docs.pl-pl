---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 53d0352448ca6ad231ecc285cc7bf795bf74d9e7
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107497318"
---
## <a name="azure-security-benchmark"></a>Test porównawczy zabezpieczeń platformy Azure

Test [porównawczy zabezpieczeń platformy Azure](../../../../articles/security/benchmarks/overview.md) zawiera zalecenia dotyczące zabezpieczania rozwiązań w chmurze na platformie Azure. Aby zobaczyć, jak ta usługa całkowicie mapuje się na test porównawczy zabezpieczeń platformy Azure, zobacz pliki mapowania testu porównawczego zabezpieczeń [platformy Azure.](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

Aby sprawdzić, jak dostępne wbudowane Azure Policy dla wszystkich usług platformy Azure są mapowe na ten standard zgodności, zobacz Azure Policy Regulatory Compliance - Azure Security Benchmark (Zgodność z przepisami — test porównawczy zabezpieczeń platformy [Azure).](../../../../articles/governance/policy/samples/azure-security-benchmark.md)

|Domena |Identyfikator kontrolki |Tytuł kontrolki |Zasady<br /><sub>(Azure Portal)</sub> |Wersja zasad<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Bezpieczeństwo sieci |NS-2 |Łączenie ze sobą sieci prywatnych |[Azure Cache for Redis znajdować się w sieci wirtualnej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7d092e0a-7acd-40d2-a975-dca21cae48c4) |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_CacheInVnet_Audit.json) |
|Ochrona danych |DP-4 |Szyfrowanie poufnych informacji podczas przesyłania |[Należy włączyć tylko bezpieczne Azure Cache for Redis z siecią](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |

## <a name="azure-security-benchmark-v1"></a>Test porównawczy zabezpieczeń platformy Azure w wersji 1

Test [porównawczy zabezpieczeń platformy Azure](../../../../articles/security/benchmarks/overview.md) zawiera zalecenia dotyczące zabezpieczania rozwiązań w chmurze na platformie Azure. Aby zobaczyć, jak ta usługa całkowicie mapuje się na test porównawczy zabezpieczeń platformy Azure, zobacz pliki mapowania testu porównawczego zabezpieczeń [platformy Azure.](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

Aby sprawdzić, jak dostępne wbudowane Azure Policy dla wszystkich usług platformy Azure są mapowe na ten standard zgodności, zobacz Azure Policy Regulatory Compliance - Azure Security Benchmark (Zgodność z przepisami — test porównawczy zabezpieczeń platformy [Azure).](../../../../articles/governance/policy/samples/azure-security-benchmark.md)

|Domena |Identyfikator kontrolki |Tytuł kontrolki |Zasady<br /><sub>(Azure Portal)</sub> |Wersja zasad<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Ochrona danych |4.4 |Szyfrowanie wszystkich informacji poufnych podczas przesyłania |[Należy włączyć tylko bezpieczne Azure Cache for Redis połączenia z siecią](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |

## <a name="cmmc-level-3"></a>CMMC Level 3

Aby sprawdzić, jak dostępne Azure Policy dla wszystkich usług platformy Azure są mapowe na ten standard zgodności, zobacz Azure Policy Regulatory Compliance - CMMC Level 3 (Zgodność z przepisami — [CMMC Level 3).](../../../../articles/governance/policy/samples/cmmc-l3.md)
Aby uzyskać więcej informacji na temat tego standardu zgodności, zobacz [Cybersecurity Maturity Model Certification (CMMC)](https://www.acq.osd.mil/cmmc/docs/CMMC_Model_Main_20200203.pdf).

|Domena |Identyfikator kontrolki |Tytuł kontrolki |Zasady<br /><sub>(Azure Portal)</sub> |Wersja zasad<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Kontrola dostępu |AC.1.002 |Ogranicz dostęp systemu informacji do typów transakcji i funkcji, które autoryzowani użytkownicy mogą wykonywać. |[Należy włączyć tylko bezpieczne Azure Cache for Redis połączenia z siecią](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|Ochrona systemu i komunikacji |SC.1.175 |Monitorowanie, kontrolowanie i ochrona komunikacji (tj. informacji przesyłanych lub odbieranych przez systemy organizacyjne) na granicach zewnętrznych i kluczowych wewnętrznych granicach systemów organizacyjnych. |[Należy włączyć tylko bezpieczne Azure Cache for Redis połączenia z siecią](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|Ochrona systemu i komunikacji |SC.3.185 |Implementowanie mechanizmów kryptograficznych w celu zapobiegania nieautoryzowanemu ujawnieniu danych CUI podczas transmisji, chyba że alternatywne zabezpieczenia fizyczne są chronione w inny sposób. |[Należy włączyć tylko bezpieczne Azure Cache for Redis z siecią](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |

## <a name="hipaa-hitrust-92"></a>HIPAA HITRUST 9.2

Aby sprawdzić, jak dostępne wbudowane Azure Policy dla wszystkich usług platformy Azure są mapowanie na ten standard zgodności, zobacz [Azure Policy Regulatory Compliance - HIPAA HITRUST 9.2](../../../../articles/governance/policy/samples/hipaa-hitrust-9-2.md).
Aby uzyskać więcej informacji na temat tego standardu zgodności, [zobacz HIPAA HITRUST 9.2.](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html)

|Domena |Identyfikator kontrolki |Tytuł kontrolki |Zasady<br /><sub>(Azure Portal)</sub> |Wersja zasad<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Kontrola połączenia sieciowego |0809.01n2Organizational.1234 -01.n |Ruch sieciowy jest kontrolowany zgodnie z zasadami kontroli dostępu organizacji za pośrednictwem zapory i innych ograniczeń związanych z siecią dla każdego punktu dostępu do sieci lub interfejsu zarządzanego zewnętrznej usługi telekomunikacyjnej. |[Należy włączyć tylko bezpieczne Azure Cache for Redis z siecią](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|Kontrola połączenia sieciowego |0810.01n2Organizational.5 – 01.n |Przesyłane informacje są zabezpieczone i szyfrowane co najmniej za pośrednictwem otwartych sieci publicznych. |[Należy włączyć tylko bezpieczne Azure Cache for Redis z siecią](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|Kontrola połączenia sieciowego |0811.01n2Organizational.6 – 01.n |Wyjątki od zasad przepływu ruchu są udokumentowane z potrzebą obsługi misji/działalności biznesowej, czasem trwania wyjątku i przeglądane co najmniej raz do roku; Wyjątki zasad przepływu ruchu są usuwane, gdy nie są już obsługiwane przez jawną misję/potrzeby biznesowe. |[Należy włączyć tylko bezpieczne Azure Cache for Redis połączenia z siecią](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|Kontrola połączenia sieciowego |0812.01n2Organizational.8 – 01.n |Urządzenia zdalne nawiązywanie połączenia nie zdalnego nie mogą komunikować się z zasobami zewnętrznymi (zdalnymi). |[Należy włączyć tylko bezpieczne Azure Cache for Redis połączenia z siecią](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|Kontrola połączenia sieciowego |0814.01n1Organizational.12 - 01.n |Możliwość łączenia się użytkowników z siecią wewnętrzną jest ograniczona przy użyciu domyślnych zasad odmowy i zezwalania na wyjątek w interfejsach zarządzanych zgodnie z zasadami kontroli dostępu i wymaganiami aplikacji klinicznych i biznesowych. |[Należy włączyć tylko bezpieczne Azure Cache for Redis połączenia z siecią](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|Identyfikacja ryzyka związanego ze stronami zewnętrznymi |1451.05iCSPOrganizational.2 — 05.i |Dostawcy usług w chmurze projektują i wdrażają mechanizmy kontroli w celu zminimalizowania i ograniczenia ryzyka związanego z bezpieczeństwem danych dzięki właściwemu rozdzieleniu obowiązków, dostępu opartego na rolach i dostępu z najmniejszymi uprawnieniami dla wszystkich pracowników w łańcuchu dostaw. |[Należy włączyć tylko bezpieczne Azure Cache for Redis połączenia z siecią](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|Transakcje online |0946.09y2Organizational.14 - 09.y |Organizacja wymaga szyfrowania między i korzystania z podpisów elektronicznych przez każdą ze stron zaangażowanych w transakcję. |[Należy włączyć tylko bezpieczne Azure Cache for Redis połączenia z siecią](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |

## <a name="iso-270012013"></a>ISO 27001:2013

Aby sprawdzić, jak dostępne Azure Policy dla wszystkich usług platformy Azure są mapowe na ten standard zgodności, zobacz [Azure Policy Regulatory Compliance - ISO 27001:2013 (Zgodność z przepisami — ISO 27001:2013).](../../../../articles/governance/policy/samples/iso-27001.md)
Aby uzyskać więcej informacji na temat tego standardu zgodności, [zobacz ISO 27001:2013](https://www.iso.org/isoiec-27001-information-security.html).

|Domena |Identyfikator kontrolki |Tytuł kontrolki |Zasady<br /><sub>(Azure Portal)</sub> |Wersja zasad<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Kryptografia |10.1.1 |Zasady dotyczące używania kontrolek kryptograficznych |[Należy włączyć tylko bezpieczne Azure Cache for Redis z siecią](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|Zabezpieczenia komunikacji |13.2.1 |Zasady i procedury transferu informacji |[Należy włączyć tylko bezpieczne Azure Cache for Redis z siecią](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |

## <a name="new-zealand-ism-restricted"></a>Nowa Zelandia ISM z ograniczeniami

Aby sprawdzić, jak dostępne wbudowane Azure Policy dla wszystkich usług platformy Azure są mapowe na ten standard zgodności, zobacz [Azure Policy Regulatory Compliance - New Zelandia ISM Restricted](../../../../articles/governance/policy/samples/new-zealand-ism.md)(Zgodność z przepisami — nowa Zelandia — ograniczone przez ism).
Aby uzyskać więcej informacji na temat tego standardu zgodności, zobacz [New Zelandia ISM Restricted](https://www.nzism.gcsb.govt.nz/).

|Domena |Identyfikator kontrolki |Tytuł kontrolki |Zasady<br /><sub>(Azure Portal)</sub> |Wersja zasad<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Zabezpieczenia oprogramowania |SS-8 |14.5.8 Aplikacje internetowe |[Należy włączyć tylko bezpieczne Azure Cache for Redis połączenia z siecią](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|Zarządzanie danymi |DM-6 |20.4.4 Pliki bazy danych |[Należy włączyć tylko bezpieczne Azure Cache for Redis połączenia z siecią](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |

## <a name="nist-sp-800-171-r2"></a>NIST SP 800-171 R2

Aby sprawdzić, jak dostępne Azure Policy dla wszystkich usług platformy Azure są mapowe na ten standard zgodności, zobacz [Azure Policy Regulatory Compliance - NIST SP 800-171 R2](../../../../articles/governance/policy/samples/nist-sp-800-171-r2.md).
Aby uzyskać więcej informacji na temat tego standardu zgodności, zobacz [NIST SP 800-171 R2](https://csrc.nist.gov/publications/detail/sp/800-171/rev-2/final).

|Domena |Identyfikator kontrolki |Tytuł kontrolki |Zasady<br /><sub>(Azure Portal)</sub> |Wersja zasad<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Ochrona systemu i komunikacji |3.13.1 |Monitorowanie, kontrolowanie i ochrona komunikacji (tj. informacji przesyłanych lub odbieranych przez systemy organizacyjne) na granicach zewnętrznych i kluczowych wewnętrznych granicach systemów organizacyjnych. |[Należy włączyć tylko bezpieczne Azure Cache for Redis połączenia z siecią](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|Ochrona systemu i komunikacji |3.13.8 |Zaimplementować mechanizmy kryptograficzne, aby zapobiec nieautoryzowanemu ujawnieniu informacji cui podczas transmisji, chyba że inaczej chronione przez alternatywne zabezpieczenia fizyczne. |[Należy włączyć tylko bezpieczne Azure Cache for Redis z siecią](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |

## <a name="nist-sp-800-53-r4"></a>NIST SP 800-53 R4

Aby sprawdzić, jak dostępne Azure Policy dla wszystkich usług platformy Azure są mapowe na ten standard zgodności, zobacz [Azure Policy Regulatory Compliance - NIST SP 800-53 R4](../../../../articles/governance/policy/samples/nist-sp-800-53-r4.md).
Aby uzyskać więcej informacji na temat tego standardu zgodności, zobacz [NIST SP 800-53 R4.](https://nvd.nist.gov/800-53)

|Domena |Identyfikator kontrolki |Tytuł kontrolki |Zasady<br /><sub>(Azure Portal)</sub> |Wersja zasad<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Ochrona systemu i komunikacji |SC-8 (1) |Poufność transmisji i integralność \| kryptograficzna lub alternatywna ochrona fizyczna |[Należy włączyć tylko bezpieczne Azure Cache for Redis z siecią](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |

