---
title: Informacje na temat zasad zabezpieczeń, inicjatyw i zaleceń w Azure Security Center
description: Dowiedz się więcej na temat zasad zabezpieczeń, inicjatyw i zaleceń w Azure Security Center.
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/04/2021
ms.openlocfilehash: 4dc29c8b52a3d0953445666672a716af013ee408
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102176437"
---
# <a name="what-are-security-policies-initiatives-and-recommendations"></a>Co to są zasady zabezpieczeń, inicjatywy i zalecenia?

Security Center stosuje inicjatywy dotyczące zabezpieczeń do subskrypcji. Te inicjatywy zawierają co najmniej jedną zasadę zabezpieczeń. Każda z tych zasad skutkuje zaleceniami dotyczącymi zabezpieczeń w celu usprawnienia stan zabezpieczeń. Na tej stronie objaśniono szczegółowo wszystkie te pomysły.


## <a name="what-is-a-security-policy"></a>Co to są zasady zabezpieczeń?

Definicja zasad platformy Azure utworzona w Azure Policy jest regułą dotyczącą określonych warunków zabezpieczeń, które mają być kontrolowane. Wbudowane definicje obejmują elementy, takie jak kontrolowanie typu zasobów, które można wdrożyć lub wymuszanie użycia tagów dla wszystkich zasobów. Możesz również utworzyć własne definicje zasad niestandardowych.

Aby zaimplementować te definicje zasad (wbudowane lub niestandardowe), należy je przypisać. Dowolną z tych zasad można przypisać za pośrednictwem witryny Azure Portal, programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

W Azure Policy istnieją różne typy zasad. Security Center korzysta głównie z zasad "Audit", które sprawdzają określone warunki i konfiguracje, a następnie raportują zgodność. Istnieją również zasady "Wymuś", których można użyć do zastosowania bezpiecznych ustawień.

## <a name="what-is-a-security-initiative"></a>Co to jest inicjatywa zabezpieczeń?

Inicjatywa Azure to zbiór definicji zasad platformy Azure lub reguł, które są pogrupowane w celu osiągnięcia określonego celu lub celu. Inicjatywy platformy Azure upraszczają zarządzanie zasadami przez zgrupowanie zestawu zasad, logicznie, jako jednego elementu.

Inicjatywa zabezpieczeń definiuje żądaną konfigurację obciążeń i pomaga upewnić się, że są one zgodne z wymaganiami firmy lub regulatorów.

Podobnie jak w przypadku zasad zabezpieczeń, Security Center inicjatywy są również tworzone w Azure Policy. Za pomocą [Azure Policy](../governance/policy/overview.md) można zarządzać zasadami, budować inicjatywy i przypisywać inicjatywy do wielu subskrypcji lub dla całej grupy zarządzania.

Domyślna inicjatywa automatycznie przypisana do każdej subskrypcji w Azure Security Center to test wydajności Azure Security. Ten test porównawczy jest zestawem wytycznych dotyczących zabezpieczeń i zgodności opartych na platformie Azure, które są zgodne z najlepszymi rozwiązaniami dotyczącymi zgodności. Ten powszechnie przestrzegany test porównawczy jest oparty na kontrolkach z [centrum na potrzeby zabezpieczeń internetowych (CIS)](https://www.cisecurity.org/benchmark/azure/) i [National Institute of Standards and Technology (NIST)](https://www.nist.gov/) , które koncentrują się na zabezpieczeniach skoncentrowanych na chmurze. Dowiedz się więcej o [teście porównawczym zabezpieczeń platformy Azure](../security/benchmarks/introduction.md).

Security Center oferuje następujące opcje pracy z inicjatywami i zasadami zabezpieczeń:

- **Wyświetlanie i edytowanie wbudowanej inicjatywy domyślnej** — po włączeniu Security Center inicjatywa "Azure Security test" jest automatycznie przypisywana do wszystkich Security Center zarejestrowanych subskrypcji. Aby dostosować tę inicjatywę, możesz włączyć lub wyłączyć poszczególne zasady w ramach tego programu, edytując parametry zasad. Zapoznaj się z listą [wbudowanych zasad zabezpieczeń](./policy-reference.md) , aby poznać dostępne opcje.

- **Dodaj własne inicjatywy niestandardowe** — Jeśli chcesz dostosować inicjatywy dotyczące zabezpieczeń stosowane do subskrypcji, możesz to zrobić w ramach Security Center. Następnie otrzymasz zalecenia, jeśli maszyny nie będą zgodne z tworzonymi zasadami. Aby uzyskać instrukcje dotyczące tworzenia i przypisywania zasad niestandardowych, zobacz [Korzystanie z niestandardowych inicjatyw i zasad zabezpieczeń](custom-security-policies.md).

- **Dodaj standardy zgodności dla inicjatyw** — pulpit nawigacyjny zgodności z przepisami Security Center przedstawia stan wszystkich ocen w środowisku w kontekście określonego standardu lub rozporządzenia (na przykład Azure CIS, NIST SP 800-53 R4, Swift CSP CSCF-V2020). Aby uzyskać więcej informacji, zobacz [poprawianie zgodności z przepisami](security-center-compliance-dashboard.md).

## <a name="what-is-a-security-recommendation"></a>Co to jest zalecenie dotyczące zabezpieczeń?

Korzystając z zasad, Security Center okresowo analizuje stan zgodności zasobów, aby identyfikować potencjalne nieodpowiednie konfiguracje i słabe zabezpieczenia. Następnie zawiera zalecenia dotyczące sposobu rozwiązania tych problemów. Zalecenia są wynikiem oceny zasobów względem odpowiednich zasad i identyfikowania zasobów, które nie spełniają określonych wymagań.

Security Center wykonuje zalecenia dotyczące zabezpieczeń w oparciu o wybrane inicjatywy. Gdy zasady z inicjatywy zostaną porównane z zasobami i znajdują się w niezgodnym zakresie, jest to zalecenie w Security Center.

Zalecenia to akcje podejmowane w celu zapewnienia bezpieczeństwa i ograniczania funkcjonalności zasobów. Każde zalecenie zawiera następujące informacje:

- Krótki opis problemu
- Czynności zaradcze, które należy wykonać w celu wdrożenia zalecenia
- Zasoby, których to dotyczy

W tym celu działa następująco:

1. Test porównawczy zabezpieczeń platformy Azure to ***inicjatywa***
1. Obejmuje ona ***zasady*** wymagające, aby wszystkie konta usługi Azure Storage ograniczyć dostęp do sieci, aby zmniejszyć ich podatność na ataki. Te zasady są nazywane "konta magazynu powinny ograniczać dostęp do sieci przy użyciu reguł sieci wirtualnej" i można je wyłączyć lub włączyć z poziomu Azure Policy
1. Jeśli Azure Security Center odnajdzie konto usługi Azure Storage dla dowolnej z chronionych subskrypcji, ocenia te konta, aby sprawdzić, czy są one chronione przy użyciu reguł sieci wirtualnej. Jeśli nie, wyświetli ***zalecenie*** , aby rozwiązać tę sytuację i zabezpieczyć zabezpieczenia tych zasobów. 

W związku z tym inicjatywa (1) zawiera zasady (2), które generują zalecenia, jeśli są odpowiednie (3). 

## <a name="viewing-the-relationship-between-a-recommendation-and-a-policy"></a>Wyświetlanie relacji między rekomendacją a zasadami

Jak wspomniano powyżej, Security Center wbudowane zalecenia są oparte na teście zabezpieczeń platformy Azure. Prawie każde zalecenie ma podstawowe zasady, które pochodzą z wymogu w teście porównawczym.

Gdy przeglądasz szczegóły zalecenia, często warto mieć możliwość wyświetlenia podstawowych zasad. Dla każdego zalecenia obsługiwanego przez zasady Użyj linku **Wyświetl definicję zasad** z strony Szczegóły zalecenia, aby przejść bezpośrednio do wpisu Azure Policy dla odpowiednich zasad:

:::image type="content" source="media/release-notes/view-policy-definition.png" alt-text="Link do Azure Policy stronie dla określonych zasad wspierających zalecenie":::

Użyj tego linku, aby wyświetlić definicję zasad i przejrzeć logikę oceny. 

Jeśli przeglądasz listę zaleceń w [przewodniku dotyczącym zaleceń dotyczących zabezpieczeń](recommendations-reference.md), zobaczysz także linki do stron definicji zasad:

:::image type="content" source="media/release-notes/view-policy-definition-from-documentation.png" alt-text="Uzyskiwanie dostępu do strony Azure Policy dla określonych zasad bezpośrednio z poziomu strony informacje o zaleceniach Azure Security Center":::


## <a name="next-steps"></a>Następne kroki

Na tej stronie wyjaśniono na wysokim poziomie podstawowe koncepcje i relacje między zasadami, inicjatywami i zaleceniami. Aby uzyskać powiązane informacje, zobacz:

- [Utwórz inicjatywy niestandardowe](custom-security-policies.md)
- [Wyłącz zasady zabezpieczeń, aby wyłączyć zalecenia](tutorial-security-policy.md#disable-security-policies-and-disable-recommendations)
- [Dowiedz się, jak edytować zasady zabezpieczeń w Azure Policy](../governance/policy/tutorials/create-and-manage.md)