---
title: Azure Policy kontroli zgodności z przepisami dla Wyszukiwanie poznawcze platformy Azure
description: Wyświetla listę Azure Policy kontroli zgodności z przepisami, które są dostępne dla Wyszukiwanie poznawcze platformy Azure. Te wbudowane definicje zasad zapewniają typowe podejścia do zarządzania zgodnością zasobów platformy Azure.
ms.date: 03/24/2021
ms.topic: sample
author: HeidiSteen
ms.author: heidist
ms.service: search
ms.custom: subject-policy-compliancecontrols
ms.openlocfilehash: af387d44caec333c2a320516d5c5714c803f0caa
ms.sourcegitcommit: bb330af42e70e8419996d3cba4acff49d398b399
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105033737"
---
# <a name="azure-policy-regulatory-compliance-controls-for-azure-cognitive-search"></a>Azure Policy kontroli zgodności z przepisami dla Wyszukiwanie poznawcze platformy Azure

Jeśli używasz [Azure Policy](../governance/policy/overview.md) , aby wymusić zalecenia w [usłudze Azure Security test](../security/benchmarks/introduction.md), prawdopodobnie już wiesz, że możesz utworzyć zasady dotyczące identyfikowania i rozwiązywania niezgodnych usług. Te zasady mogą być niestandardowe lub mogą opierać się na wbudowanych definicjach, które zawierają kryteria zgodności i odpowiednie rozwiązania dla dobrze zrozumiałych najlepszych rozwiązań.

W przypadku usługi Azure Wyszukiwanie poznawcze istnieje obecnie jedna wbudowana definicja wymieniona poniżej, której można użyć w przypisaniu zasad. Wbudowana jest rejestracja i monitorowanie. Korzystając z tej wbudowanej definicji w utworzonych [zasadach](../governance/policy/assign-policy-portal.md), system będzie skanował w poszukiwaniu usług wyszukiwania, które nie mają [rejestrowania diagnostycznego](search-monitor-logs.md), a następnie włączyć je odpowiednio.

[Zgodność z przepisami w Azure Policy](../governance/policy/concepts/regulatory-compliance.md) zapewnia definicje inicjatywy utworzone przez firmę Microsoft i zarządzane, znane jako _wbudowane_, dla **domen zgodności** i **kontroli zabezpieczeń** związanych z różnymi standardami zgodności. Ta strona zawiera listę **domen zgodności** i **kontroli zabezpieczeń** dla usługi Azure wyszukiwanie poznawcze. Wbudowane elementy **kontroli zabezpieczeń** można przypisać indywidualnie, aby zapewnić zgodność zasobów platformy Azure z określonym standardem.

[!INCLUDE [azure-policy-compliancecontrols-introwarning](../../includes/policy/standards/intro-warning.md)]

[!INCLUDE [azure-policy-compliancecontrols-search](../../includes/policy/standards/byrp/microsoft.search.md)]

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [zgodności z przepisami Azure Policy](../governance/policy/concepts/regulatory-compliance.md).
- Zobacz wbudowane elementy w [repozytorium GitHub usługi Azure Policy](https://github.com/Azure/azure-policy).
