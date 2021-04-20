---
title: Omówienie zasad zabezpieczeń, inicjatyw i zaleceń w Azure Security Center
description: Dowiedz się więcej o zasadach zabezpieczeń, inicjatywach i zaleceniach w Azure Security Center.
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/04/2021
ms.openlocfilehash: 4eea2611997732a263e9e824bc150b45ed145ecd
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107738976"
---
# <a name="what-are-security-policies-initiatives-and-recommendations"></a>Co to są zasady zabezpieczeń, inicjatywy i zalecenia?

Security Center stosuje inicjatywy zabezpieczeń do twoich subskrypcji. Te inicjatywy zawierają co najmniej jedną zasady zabezpieczeń. Każda z tych zasad powoduje zalecenie dotyczące zabezpieczeń w celu poprawy poziomu bezpieczeństwa. Na tej stronie szczegółowo opisano poszczególne koncepcje.


## <a name="what-is-a-security-policy"></a>Co to są zasady zabezpieczeń?

Definicja zasad platformy Azure, utworzona w Azure Policy, to reguła o określonych warunkach zabezpieczeń, które mają być kontrolowane. Wbudowane definicje obejmują takie rzeczy jak kontrolowanie typu zasobów, które można wdrożyć, lub wymuszanie użycia tagów we wszystkich zasobach. Możesz również utworzyć własne niestandardowe definicje zasad.

Aby zaimplementować te definicje zasad (wbudowane lub niestandardowe), należy je przypisać. Dowolną z tych zasad można przypisać za pośrednictwem witryny Azure Portal, programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

Istnieją różne typy zasad w Azure Policy. Security Center używa głównie zasad inspekcji, które sprawdzają określone warunki i konfiguracje, a następnie raportują zgodność. Istnieją również zasady "Wymuszaj", których można użyć do zastosowania bezpiecznych ustawień.

## <a name="what-is-a-security-initiative"></a>Co to jest inicjatywa zabezpieczeń?

Inicjatywa platformy Azure to kolekcja definicji lub reguł zasad platformy Azure, które są grupowane w celu określonego celu lub celu. Inicjatywy platformy Azure upraszczają zarządzanie zasadami przez grupowanie zestawu zasad razem, logicznie, jako pojedynczego elementu.

Inicjatywa zabezpieczeń definiuje żądaną konfigurację obciążeń i pomaga zapewnić zgodność z wymaganiami bezpieczeństwa firmy lub przepisów prawnych.

Podobnie jak zasady zabezpieczeń Security Center inicjatywy są również tworzone w Azure Policy. Za pomocą [Azure Policy](../governance/policy/overview.md) zarządzać zasadami, tworzyć inicjatywy i przypisywać inicjatywy do wielu subskrypcji lub dla całych grup zarządzania.

Domyślną inicjatywą, która jest automatycznie przypisywana do każdej subskrypcji w Azure Security Center jest test porównawczy zabezpieczeń platformy Azure. Ten test porównawczy jest zestawem wytycznych dotyczących najlepszych rozwiązań dotyczących zabezpieczeń i zgodności, które zostały przez firmę Microsoft specyficzne dla platformy Azure, opartych na wspólnych platformach zgodności. Ten szeroko stosowany test porównawczy opiera się na mechanizmach kontroli nist [(Center for Internet Security)](https://www.cisecurity.org/benchmark/azure/) i [National Institute of Standards and Technology (NIST)](https://www.nist.gov/) z naciskiem na zabezpieczenia skoncentrowane na chmurze. Dowiedz się więcej o [teście porównawczym zabezpieczeń platformy Azure](https://docs.microsoft.com/security/benchmark/azure/introduction).

Security Center oferuje następujące opcje do pracy z inicjatywami i zasadami zabezpieczeń:

- **Wyświetlanie i edytowanie** wbudowanej inicjatywy domyślnej — po włączeniu usługi Security Center inicjatywa o nazwie "Test porównawczy zabezpieczeń platformy Azure" jest automatycznie przypisywana do wszystkich Security Center zarejestrowanych subskrypcji. Aby dostosować tę inicjatywę, można włączyć lub wyłączyć poszczególne zasady w ramach tej inicjatywy, edytując parametry zasad. Zapoznaj się z [listą wbudowanych zasad zabezpieczeń,](./policy-reference.md) aby poznać dostępne opcje.

- **Dodaj własne niestandardowe inicjatywy** — jeśli chcesz dostosować inicjatywy zabezpieczeń zastosowane do subskrypcji, możesz to zrobić w Security Center. Następnie otrzymasz rekomendacje, jeśli maszyny nie są zgodne z zasadami, które utworzysz. Aby uzyskać instrukcje dotyczące tworzenia i przypisywania zasad niestandardowych, zobacz [Using custom security initiatives and policies (Używanie niestandardowych inicjatyw i zasad zabezpieczeń).](custom-security-policies.md)

-  Dodawanie standardów zgodności z przepisami jako inicjatyw — pulpit nawigacyjny zgodności z przepisami usługi Security Center pokazuje stan wszystkich ocen w danym środowisku w kontekście określonego standardu lub przepisów (takich jak Azure CIS, NIST SP 800-53 R4, SWIFT CSP CSCF-v2020). Aby uzyskać więcej informacji, zobacz [Improve your regulatory compliance (Ulepszanie zgodności z przepisami).](security-center-compliance-dashboard.md)

## <a name="what-is-a-security-recommendation"></a>Co to jest zalecenie dotyczące zabezpieczeń?

Korzystając z zasad, Security Center okresowo analizuje stan zgodności zasobów, aby zidentyfikować potencjalne błędy konfiguracji i słabe strony zabezpieczeń. Następnie udostępnia zalecenia dotyczące sposobu rozwiązywania tych problemów. Rekomendacje są wynikiem oceny zasobów pod względem odpowiednich zasad i identyfikowania zasobów, które nie spełniają zdefiniowanych wymagań.

Security Center rekomendacje dotyczące zabezpieczeń na podstawie wybranych inicjatyw. Gdy zasady z Twojej inicjatywy są porównywane z zasobami i wyszukuje co najmniej jeden niezgodny, są one prezentowane jako zalecenie w Security Center.

Zalecenia to akcje, które należy podjąć w celu zabezpieczenia i wzmacniania zasobów. Każde zalecenie zawiera następujące informacje:

- Krótki opis problemu
- Kroki korygowania, które należy wykonać w celu wdrożenia zalecenia
- Zasoby, których dotyczy problem

W praktyce działa to w ten sposób:

1. Test porównawczy zabezpieczeń platformy Azure to ***inicjatywa***
1. Zawiera on ***zasady, które*** wymagają, aby wszystkie konta usługi Azure Storage ograniczały dostęp do sieci, aby zmniejszyć ich powierzchnię ataku. Te zasady są nazywane "Konta magazynu powinny ograniczać dostęp do sieci przy użyciu reguł sieci wirtualnej" i można je wyłączyć lub włączyć z Azure Policy
1. Jeśli Azure Security Center konto usługi Azure Storage w dowolnej z chronionych subskrypcji, ocenia te konta, aby sprawdzić, czy są chronione za pomocą reguł sieci wirtualnej. Jeśli nie, zostanie wyświetlone zalecenie, aby ***naprawić*** tę sytuację i poprawić bezpieczeństwo tych zasobów. 

Dlatego inicjatywa (1) obejmuje zasady (2), które generują zalecenia, gdy jest to konieczne (3). 

## <a name="viewing-the-relationship-between-a-recommendation-and-a-policy"></a>Wyświetlanie relacji między zaleceniem a zasadami

Jak wspomniano powyżej, Security Center rekomendacje firmy są oparte na testach porównawczych zabezpieczeń platformy Azure. Prawie każde zalecenie ma podstawowe zasady, które są pochodną wymagania w te porównawczym.

Podczas przeglądania szczegółów zalecenia często pomocne jest sprawdzenie podstawowych zasad. Dla każdego zalecenia obsługiwanego przez  zasady użyj linku Wyświetl definicję zasad ze strony szczegółów zalecenia, aby przejść bezpośrednio do Azure Policy dla odpowiednich zasad:

:::image type="content" source="media/release-notes/view-policy-definition.png" alt-text="Link do Azure Policy dla określonych zasad wspierających zalecenie":::

Użyj tego linku, aby wyświetlić definicję zasad i przejrzeć logikę oceny. 

Jeśli przeglądasz listę zaleceń w naszym przewodniku po zaleceniach dotyczących [zabezpieczeń,](recommendations-reference.md)zobaczysz również linki do stron definicji zasad:

:::image type="content" source="media/release-notes/view-policy-definition-from-documentation.png" alt-text="Uzyskiwanie dostępu do Azure Policy dla określonych zasad bezpośrednio ze strony z zaleceniami Azure Security Center strony referencyjnej":::


## <a name="next-steps"></a>Następne kroki

Na tej stronie ogólnie wyjaśniono podstawowe pojęcia i relacje między zasadami, inicjatywami i zaleceniami. Aby uzyskać powiązane informacje, zobacz:

- [Tworzenie inicjatyw niestandardowych](custom-security-policies.md)
- [Wyłączanie zasad zabezpieczeń w celu wyłączenia zaleceń](tutorial-security-policy.md#disable-security-policies-and-disable-recommendations)
- [Dowiedz się, jak edytować zasady zabezpieczeń w Azure Policy](../governance/policy/tutorials/create-and-manage.md)