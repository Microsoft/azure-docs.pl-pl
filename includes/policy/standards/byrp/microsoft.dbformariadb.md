---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 11/17/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: b2f286d3229b572ea06ef607c8d8ee8189a7f9da
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94700279"
---
## <a name="azure-security-benchmark"></a>Test porównawczy zabezpieczeń platformy Azure

[Test porównawczy zabezpieczeń platformy Azure](../../../../articles/security/benchmarks/overview.md) zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure. Aby dowiedzieć się, jak usługa ta została całkowicie zamapowana na test porównawczy zabezpieczeń platformy Azure, zobacz [pliki mapowania usługi Azure Security test](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

Aby sprawdzić, jak dostępne Azure Policy wbudowane dla wszystkich usług platformy Azure są mapowane na ten standard zgodności, zobacz [Azure Policy zgodność z przepisami — Azure Security test](../../../../articles/governance/policy/samples/azure-security-benchmark.md).

|Domena |Identyfikator kontrolki |Tytuł formantu |Zasady<br /><sub>(Azure Portal)</sub> |Wersja zasad<br /><sub>GitHub</sub>  |
|---|---|---|---|---|
|Bezpieczeństwo sieci |1,1 |Ochrona zasobów przy użyciu sieciowych grup zabezpieczeń lub zapory platformy Azure na Virtual Network |[Prywatny punkt końcowy powinien być włączony dla serwerów MariaDB](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a1302fb-a631-4106-9753-f3d494733990) |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_EnablePrivateEndPoint_Audit.json) |
|Odzyskiwanie danych |9,1 |Zapewnianie regularnej zautomatyzowanej kopii zapasowej |[Tworzenie kopii zapasowej nadmiarowej geograficznie należy włączyć dla Azure Database for MariaDB](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ec47710-77ff-4a3d-9181-6aa50af424d0) |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMariaDB_Audit.json) |
|Odzyskiwanie danych |9,2 |Wykonaj kompletne kopie zapasowe systemu i Utwórz kopię zapasową wszystkich kluczy zarządzanych przez klienta |[Tworzenie kopii zapasowej nadmiarowej geograficznie należy włączyć dla Azure Database for MariaDB](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ec47710-77ff-4a3d-9181-6aa50af424d0) |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMariaDB_Audit.json) |

## <a name="hipaa-hitrust-92"></a>HIPAA HITRUST 9.2

Aby sprawdzić, jak dostępne Azure Policy wbudowane dla wszystkich usług platformy Azure są mapowane na ten standard zgodności, zobacz [Azure Policy zgodność z przepisami — HIPAA HITRUST 9,2](../../../../articles/governance/policy/samples/hipaa-hitrust-9-2.md).
Aby uzyskać więcej informacji na temat tego standardu zgodności, zobacz [HIPAA HITRUST 9,2](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html).

|Domena |Identyfikator kontrolki |Tytuł formantu |Zasady<br /><sub>(Azure Portal)</sub> |Wersja zasad<br /><sub>GitHub</sub>  |
|---|---|---|---|---|
|Tworzenie kopii zapasowej |1619.09 l1Organizational. 7 – 09. l |Obsługiwane są rekordy spisu kopii zapasowych, w tym zawartość i bieżąca lokalizacja. |[Tworzenie kopii zapasowej nadmiarowej geograficznie należy włączyć dla Azure Database for MariaDB](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ec47710-77ff-4a3d-9181-6aa50af424d0) |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMariaDB_Audit.json) |
|Tworzenie kopii zapasowej |1624.09 l3Organizational. 12 – 09. l |Organizacja wykonuje przyrostowe lub różnicowe kopie zapasowe codziennie i pełnymi kopiami zapasowymi na osobnym nośniku. |[Tworzenie kopii zapasowej nadmiarowej geograficznie należy włączyć dla Azure Database for MariaDB](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ec47710-77ff-4a3d-9181-6aa50af424d0) |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMariaDB_Audit.json) |
|Tworzenie kopii zapasowej |1627.09 l3Organizational. 6 – 09. l |Organizacja testuje informacje o kopii zapasowej po każdej kopii zapasowej, aby zweryfikować niezawodność i integralność informacji oraz co najmniej raz na rok. |[Tworzenie kopii zapasowej nadmiarowej geograficznie należy włączyć dla Azure Database for MariaDB](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ec47710-77ff-4a3d-9181-6aa50af424d0) |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMariaDB_Audit.json) |

