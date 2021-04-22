---
title: Azure Policy kontroli zgodności z przepisami dla Azure Cognitive Search
description: Wyświetla Azure Policy kontroli zgodności z przepisami dostępnych dla Azure Cognitive Search. Te wbudowane definicje zasad zapewniają typowe podejścia do zarządzania zgodnością zasobów platformy Azure.
ms.date: 04/21/2021
ms.topic: sample
author: HeidiSteen
ms.author: heidist
ms.service: search
ms.custom: subject-policy-compliancecontrols
ms.openlocfilehash: a8f452703826a68fde8c1b931ab8be7cbeeca6f1
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107861948"
---
# <a name="azure-policy-regulatory-compliance-controls-for-azure-cognitive-search"></a>Azure Policy kontroli zgodności z przepisami dla Azure Cognitive Search

Jeśli używasz usługi [Azure Policy](../governance/policy/overview.md) do wymuszania zaleceń w te testach porównawczych zabezpieczeń platformy [Azure,](../security/benchmarks/introduction.md)prawdopodobnie wiesz już, że możesz tworzyć zasady identyfikowania i naprawiania niezgodnych usług. Te zasady mogą być niestandardowe lub mogą być oparte na wbudowanych definicjach, które zapewniają kryteria zgodności i odpowiednie rozwiązania dla dobrze zrozumiałych najlepszych rozwiązań.

Na Azure Cognitive Search istnieje obecnie jedna definicja wbudowana wymieniona poniżej, która może być dostępna w przypisaniu zasad. Wbudowana jest do rejestrowania i monitorowania. Korzystając z tej wbudowanej [](../governance/policy/assign-policy-portal.md)definicji w zasadach, które tworzysz, system będzie skanować w poszukiwaniu usług wyszukiwania, które nie mają rejestrowania diagnostycznego, [](search-monitor-logs.md)a następnie odpowiednio je włączyć.

[Zgodność z przepisami](../governance/policy/concepts/regulatory-compliance.md) w Azure Policy udostępnia definicje inicjatyw zarządzanych i utworzonych  przez  firmę Microsoft, znane jako _wbudowane,_ dla domen zgodności i kontroli zabezpieczeń związanych z różnymi standardami zgodności. Na tej stronie przedstawiono **domeny zgodności i** **mechanizmy kontroli zabezpieczeń dla** Azure Cognitive Search. Możesz indywidualnie przypisać wbudowane elementy  kontroli zabezpieczeń, aby zapewnić zgodność zasobów platformy Azure z określonym standardem.

[!INCLUDE [azure-policy-compliancecontrols-introwarning](../../includes/policy/standards/intro-warning.md)]

[!INCLUDE [azure-policy-compliancecontrols-search](../../includes/policy/standards/byrp/microsoft.search.md)]

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [Azure Policy zgodności z przepisami.](../governance/policy/concepts/regulatory-compliance.md)
- Zobacz wbudowane elementy w [repozytorium GitHub usługi Azure Policy](https://github.com/Azure/azure-policy).
