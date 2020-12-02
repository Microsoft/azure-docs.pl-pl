---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 12/01/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: c2385d07ccb81041bd340a8bec0412a8f14cef56
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96478520"
---
## <a name="azure-security-benchmark"></a>Test porównawczy zabezpieczeń platformy Azure

[Test porównawczy zabezpieczeń platformy Azure](../../../../articles/security/benchmarks/overview.md) zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure. Aby dowiedzieć się, jak usługa ta została całkowicie zamapowana na test porównawczy zabezpieczeń platformy Azure, zobacz [pliki mapowania usługi Azure Security test](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

Aby sprawdzić, jak dostępne Azure Policy wbudowane dla wszystkich usług platformy Azure są mapowane na ten standard zgodności, zobacz [Azure Policy zgodność z przepisami — Azure Security test](../../../../articles/governance/policy/samples/azure-security-benchmark.md).

|Domena |Identyfikator kontrolki |Tytuł formantu |Zasady<br /><sub>(Azure Portal)</sub> |Wersja zasad<br /><sub>GitHub</sub>  |
|---|---|---|---|---|
|Ochrona danych |4.8 |Szyfruj poufne informacje w stanie spoczynku |[Zmienne konta usługi Automation powinny być szyfrowane](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3657f5a0-770e-44a3-b44e-9431ba1e9735) |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Automation/Automation_AuditUnencryptedVars_Audit.json) |

> [!NOTE]
> Podczas tworzenia zmiennej konta usługi Automation możesz określić jej szyfrowanie i magazyn, Azure Automation jako bezpieczny zasób. Po utworzeniu zmiennej nie można zmienić jej stanu szyfrowania bez ponownego tworzenia zmiennej. Jeśli istnieją zmienne konta usługi Automation przechowujące dane poufne, które nie są jeszcze zaszyfrowane, należy je usunąć i utworzyć je ponownie jako zaszyfrowane zmienne. Zalecenie Azure Security Center polega na zaszyfrowaniu wszystkich zmiennych Azure Automation zgodnie z opisem w temacie [zmienne konta usługi Automation powinny być szyfrowane](../../../../articles/security-center/recommendations-reference.md#recs-computeapp). Jeśli masz niezaszyfrowane zmienne, które mają być wykluczone z tego zalecenia dotyczącego zabezpieczeń, zobacz temat [zwalnianie zasobu z rekomendacji i zabezpieczeń oceny](../../../../articles/security-center/exempt-resource.md) , aby utworzyć regułę wykluczania.