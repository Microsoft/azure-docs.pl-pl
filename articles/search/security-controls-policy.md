---
title: Azure Policy kontroli zgodności z przepisami dla Azure Cognitive Search
description: Wyświetla Azure Policy kontroli zgodności z przepisami dostępne dla Azure Cognitive Search. Te wbudowane definicje zasad zapewniają typowe podejścia do zarządzania zgodnością zasobów platformy Azure.
ms.date: 04/14/2021
ms.topic: sample
author: HeidiSteen
ms.author: heidist
ms.service: search
ms.custom: subject-policy-compliancecontrols
ms.openlocfilehash: 807201d5626bcdbc421d182f06b888c5775dcc48
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107497420"
---
# <a name="azure-policy-regulatory-compliance-controls-for-azure-cognitive-search"></a>Azure Policy kontroli zgodności z przepisami dla Azure Cognitive Search

Jeśli używasz usługi [Azure Policy](../governance/policy/overview.md) do wymuszania zaleceń w te testach porównawczych zabezpieczeń platformy [Azure,](../security/benchmarks/introduction.md)prawdopodobnie wiesz już, że możesz tworzyć zasady identyfikowania i naprawiania niezgodnych usług. Te zasady mogą być niestandardowe lub oparte na wbudowanych definicjach, które zapewniają kryteria zgodności i odpowiednie rozwiązania dla dobrze zrozumiałych najlepszych rozwiązań.

Na Azure Cognitive Search obecnie istnieje jedna definicja wbudowana wymieniona poniżej, która może być dostępna w przypisaniu zasad. Wbudowana jest do rejestrowania i monitorowania. Korzystając z tej wbudowanej [](../governance/policy/assign-policy-portal.md)definicji w zasadach, które [tworzysz,](search-monitor-logs.md)system przeskanuje usługi wyszukiwania, które nie mają rejestrowania diagnostycznego, a następnie włączy je odpowiednio.

[Zgodność z przepisami](../governance/policy/concepts/regulatory-compliance.md) w Azure Policy udostępnia definicje inicjatyw utworzonych przez firmę  Microsoft i  zarządzanych, znane jako _wbudowane,_ dla domen zgodności i kontroli zabezpieczeń związanych z różnymi standardami zgodności. Ta strona zawiera listę **domen zgodności i** kontroli zabezpieczeń **dla** Azure Cognitive Search. Możesz przypisać wbudowane elementy kontroli  zabezpieczeń indywidualnie, aby ułatwić zgodność zasobów platformy Azure z określonym standardem.

[!INCLUDE [azure-policy-compliancecontrols-introwarning](../../includes/policy/standards/intro-warning.md)]

[!INCLUDE [azure-policy-compliancecontrols-search](../../includes/policy/standards/byrp/microsoft.search.md)]

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [Azure Policy zgodności z przepisami.](../governance/policy/concepts/regulatory-compliance.md)
- Zobacz wbudowane elementy w [repozytorium GitHub usługi Azure Policy](https://github.com/Azure/azure-policy).
