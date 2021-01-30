---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/29/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 329393fb1e4ea7959d78f1a6b40614812be2b0b5
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/30/2021
ms.locfileid: "99093795"
---
## <a name="azure-security-benchmark"></a>Test porównawczy zabezpieczeń platformy Azure

[Test porównawczy zabezpieczeń platformy Azure](../../../../articles/security/benchmarks/overview.md) zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure. Aby dowiedzieć się, jak usługa ta została całkowicie zamapowana na test porównawczy zabezpieczeń platformy Azure, zobacz [pliki mapowania usługi Azure Security test](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

Aby sprawdzić, jak dostępne Azure Policy wbudowane dla wszystkich usług platformy Azure są mapowane na ten standard zgodności, zobacz [Azure Policy zgodność z przepisami — Azure Security test](../../../../articles/governance/policy/samples/azure-security-benchmark.md).

|Domena |Identyfikator kontrolki |Tytuł formantu |Zasady<br /><sub>(Azure Portal)</sub> |Wersja zasad<br /><sub>GitHub</sub>  |
|---|---|---|---|---|
|Bezpieczeństwo sieci |NS-2 |Łączenie sieci prywatnych |[Pamięć podręczna platformy Azure dla Redis powinna znajdować się w sieci wirtualnej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7d092e0a-7acd-40d2-a975-dca21cae48c4) |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_CacheInVnet_Audit.json) |
|Ochrona danych |DP-4 |Szyfruj poufne informacje podczas przesyłania |[Należy włączyć tylko bezpieczne połączenia z usługą Azure cache for Redis](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |

## <a name="azure-security-benchmark-v1"></a>Azure Security test — wersja 1

[Test porównawczy zabezpieczeń platformy Azure](../../../../articles/security/benchmarks/overview.md) zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure. Aby dowiedzieć się, jak usługa ta została całkowicie zamapowana na test porównawczy zabezpieczeń platformy Azure, zobacz [pliki mapowania usługi Azure Security test](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

Aby sprawdzić, jak dostępne Azure Policy wbudowane dla wszystkich usług platformy Azure są mapowane na ten standard zgodności, zobacz [Azure Policy zgodność z przepisami — Azure Security test](../../../../articles/governance/policy/samples/azure-security-benchmark.md).

|Domena |Identyfikator kontrolki |Tytuł formantu |Zasady<br /><sub>(Azure Portal)</sub> |Wersja zasad<br /><sub>GitHub</sub>  |
|---|---|---|---|---|
|Ochrona danych |4.4 |Szyfruj wszystkie poufne informacje podczas przesyłania |[Należy włączyć tylko bezpieczne połączenia z usługą Azure cache for Redis](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |

## <a name="cmmc-level-3"></a>Poziom 3 CMMC

Aby sprawdzić, jak dostępne Azure Policy wbudowane dla wszystkich usług platformy Azure są mapowane na ten standard zgodności, zobacz [Azure Policy zgodność z przepisami — poziom CMMC 3](../../../../articles/governance/policy/samples/cmmc-l3.md).
Aby uzyskać więcej informacji na temat tego standardu zgodności, zobacz [cyberbezpieczeństwa data_spłaty model Certification (CMMC)](https://www.acq.osd.mil/cmmc/docs/CMMC_Model_Main_20200203.pdf).

|Domena |Identyfikator kontrolki |Tytuł formantu |Zasady<br /><sub>(Azure Portal)</sub> |Wersja zasad<br /><sub>GitHub</sub>  |
|---|---|---|---|---|
|Kontrola dostępu |AC. 1.002 |Ogranicz dostęp do systemu informacji do typów transakcji i funkcji, które mogą być wykonywane przez autoryzowanych użytkowników. |[Należy włączyć tylko bezpieczne połączenia z usługą Azure cache for Redis](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|Ochrona systemu i komunikacji |SC. 1.175 |Monitorowanie, kontrolowanie i ochrona komunikacji (tj. informacje przesyłane lub odbierane przez systemy organizacyjne) w granicach zewnętrznych i kluczowych granicach systemów organizacji. |[Należy włączyć tylko bezpieczne połączenia z usługą Azure cache for Redis](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|Ochrona systemu i komunikacji |SC. 3.185 |Zaimplementuj mechanizmy kryptograficzne, aby zapobiec nieautoryzowanemu ujawnianiu CUI podczas przesyłania, chyba że jest to chronione przez alternatywne zabezpieczenia fizyczne. |[Należy włączyć tylko bezpieczne połączenia z usługą Azure cache for Redis](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |

## <a name="hipaa-hitrust-92"></a>HIPAA HITRUST 9.2

Aby sprawdzić, jak dostępne Azure Policy wbudowane dla wszystkich usług platformy Azure są mapowane na ten standard zgodności, zobacz [Azure Policy zgodność z przepisami — HIPAA HITRUST 9,2](../../../../articles/governance/policy/samples/hipaa-hitrust-9-2.md).
Aby uzyskać więcej informacji na temat tego standardu zgodności, zobacz [HIPAA HITRUST 9,2](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html).

|Domena |Identyfikator kontrolki |Tytuł formantu |Zasady<br /><sub>(Azure Portal)</sub> |Wersja zasad<br /><sub>GitHub</sub>  |
|---|---|---|---|---|
|Sterowanie połączeniem sieciowym |0809.01 n2Organizational. 1234-01. n |Ruch sieciowy jest kontrolowany zgodnie z zasadami kontroli dostępu organizacji przez zaporę i inne ograniczenia związane z siecią dla każdego punktu dostępu do sieci lub z zarządzanym zewnętrznym interfejsem usługi telekomunikacyjnej. |[Należy włączyć tylko bezpieczne połączenia z usługą Azure cache for Redis](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|Sterowanie połączeniem sieciowym |0810.01 n2Organizational. 5-01. n |Przesyłane informacje są zabezpieczone i co najmniej zaszyfrowane przez otwarte sieci publiczne. |[Należy włączyć tylko bezpieczne połączenia z usługą Azure cache for Redis](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|Sterowanie połączeniem sieciowym |0811.01 n2Organizational. 6-01. n |Wyjątki od zasad przepływu ruchu są udokumentowane z obsługą potrzebnych zadań, czasu trwania wyjątku i przeglądem co najmniej raz na rok; wyjątki zasad przepływu ruchu są usuwane, gdy nie są już obsługiwane przez jawną potrzebę firmy. |[Należy włączyć tylko bezpieczne połączenia z usługą Azure cache for Redis](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|Sterowanie połączeniem sieciowym |0812.01 n2Organizational. 8-01. n |Urządzenia zdalne ustanawiające niezdalne połączenie nie mogą komunikować się z zewnętrznymi (zdalnymi) zasobami. |[Należy włączyć tylko bezpieczne połączenia z usługą Azure cache for Redis](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|Sterowanie połączeniem sieciowym |0814.01 n1Organizational. 12-01. n |Możliwość nawiązywania połączeń z siecią wewnętrzną jest ograniczona przy użyciu zasad Odmów przez domyślne i zezwala na wyjątki w interfejsach zarządzanych zgodnie z zasadami kontroli dostępu oraz wymaganiami dotyczącymi aplikacji klinicznych i firmowych. |[Należy włączyć tylko bezpieczne połączenia z usługą Azure cache for Redis](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|Identyfikacja zagrożeń związanych ze stronami zewnętrznymi |1451.05 iCSPOrganizational. 2 – 05. i |Dostawcy usług w chmurze projektują i implementują kontrole w celu ograniczenia i zawierania zagrożeń bezpieczeństwa danych poprzez odpowiednie rozdzielenie obowiązków, dostęp oparty na rolach i dostęp do najniższych uprawnień dla wszystkich pracowników w łańcuchu dostaw. |[Należy włączyć tylko bezpieczne połączenia z usługą Azure cache for Redis](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|Transakcje online |0946.09 y2Organizational. 14 – 09. y |Organizacja wymaga użycia szyfrowania między, a korzystanie z podpisów elektronicznych przez, każda ze stron uwzględnionych w transakcji. |[Należy włączyć tylko bezpieczne połączenia z usługą Azure cache for Redis](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |

## <a name="iso-270012013"></a>ISO 27001:2013

Aby sprawdzić, jak dostępne Azure Policy wbudowane dla wszystkich usług platformy Azure są mapowane na ten standard zgodności, zobacz [Azure Policy zgodność z przepisami — ISO 27001:2013](../../../../articles/governance/policy/samples/iso-27001.md).
Aby uzyskać więcej informacji na temat tego standardu zgodności, zobacz [ISO 27001:2013](https://www.iso.org/isoiec-27001-information-security.html).

|Domena |Identyfikator kontrolki |Tytuł formantu |Zasady<br /><sub>(Azure Portal)</sub> |Wersja zasad<br /><sub>GitHub</sub>  |
|---|---|---|---|---|
|Kryptografia |10.1.1 |Zasady korzystania z formantów kryptograficznych |[Należy włączyć tylko bezpieczne połączenia z usługą Azure cache for Redis](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|Bezpieczeństwo komunikacji |13.2.1 |Zasady i procedury dotyczące transferu informacji |[Należy włączyć tylko bezpieczne połączenia z usługą Azure cache for Redis](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |

## <a name="nist-sp-800-171-r2"></a>NIST SP 800-171 R2

Aby sprawdzić, jak dostępne Azure Policy wbudowane dla wszystkich usług platformy Azure są mapowane na ten standard zgodności, zobacz [Azure Policy zgodność z przepisami — NIST SP 800-171 R2](../../../../articles/governance/policy/samples/nist-sp-800-171-r2.md).
Aby uzyskać więcej informacji na temat tego standardu zgodności, zobacz [NIST SP 800-171 R2](https://csrc.nist.gov/publications/detail/sp/800-171/rev-2/final).

|Domena |Identyfikator kontrolki |Tytuł formantu |Zasady<br /><sub>(Azure Portal)</sub> |Wersja zasad<br /><sub>GitHub</sub>  |
|---|---|---|---|---|
|Ochrona systemu i komunikacji |3.13.1 |Monitorowanie, kontrolowanie i ochrona komunikacji (tj. informacje przesyłane lub odbierane przez systemy organizacyjne) w granicach zewnętrznych i kluczowych granicach systemów organizacji. |[Należy włączyć tylko bezpieczne połączenia z usługą Azure cache for Redis](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|Ochrona systemu i komunikacji |3.13.8 |Zaimplementuj mechanizmy kryptograficzne, aby zapobiec nieautoryzowanemu ujawnianiu CUI podczas przesyłania, chyba że jest to chronione przez alternatywne zabezpieczenia fizyczne. |[Należy włączyć tylko bezpieczne połączenia z usługą Azure cache for Redis](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |

## <a name="nist-sp-800-53-r4"></a>NIST SP 800-53 R4

Aby sprawdzić, jak dostępne Azure Policy wbudowane dla wszystkich usług platformy Azure są mapowane na ten standard zgodności, zobacz [Azure Policy zgodność z przepisami — NIST SP 800-53 R4](../../../../articles/governance/policy/samples/nist-sp-800-53-r4.md).
Aby uzyskać więcej informacji na temat tego standardu zgodności, zobacz [NIST SP 800-53 R4](https://nvd.nist.gov/800-53).

|Domena |Identyfikator kontrolki |Tytuł formantu |Zasady<br /><sub>(Azure Portal)</sub> |Wersja zasad<br /><sub>GitHub</sub>  |
|---|---|---|---|---|
|Ochrona systemu i komunikacji |SC-8 (1) |Poufność transmisji i integralność \| kryptograficzna lub alternatywna ochrona fizyczna |[Należy włączyć tylko bezpieczne połączenia z usługą Azure cache for Redis](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |

