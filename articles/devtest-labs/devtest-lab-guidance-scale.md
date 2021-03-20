---
title: Skalowanie infrastruktury Azure DevTest Labs
description: Ten artykuł zawiera wskazówki dotyczące skalowania infrastruktury Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 50bf08678a12a1a0499abd08c52a264d03f4a401
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85478794"
---
# <a name="scale-up-your-azure-devtest-labs-infrastructure"></a>Skalowanie infrastruktury Azure DevTest Labs
Przed wdrożeniem laboratoriów DevTest w skali przedsiębiorstwa istnieje kilka najważniejszych punktów decyzji. Zrozumienie tych punktów decyzji na wyższym poziomie ułatwia organizacjom podejmowanie decyzji projektowych w przyszłości. Jednak te punkty nie powinny pomieścić z powrotem organizacji przed rozpoczęciem weryfikacji koncepcji. Trzy pierwsze obszary do wstępnego planowania skalowania są następujące:

- Sieci i zabezpieczenia
- Topologia subskrypcji
- Role i obowiązki

## <a name="networking-and-security"></a>Sieci i zabezpieczenia
Sieci i zabezpieczenia są podstawą dla wszystkich organizacji. Chociaż wdrożenie w całym przedsiębiorstwie wymaga znacznie dokładniejszej analizy, istnieje zmniejszona liczba wymagań, które pomogą pomyślnie wykonać weryfikację koncepcji. Oto kilka kluczowych obszarów fokusu:

- **Subskrypcja platformy Azure** — aby wdrożyć DevTest Labs, musisz mieć dostęp do subskrypcji platformy Azure z odpowiednimi prawami do tworzenia zasobów. Istnieje kilka sposobów uzyskiwania dostępu do subskrypcji platformy Azure, w tym Enterprise Agreement i płatność zgodnie z rzeczywistym użyciem. Aby uzyskać więcej informacji na temat uzyskiwania dostępu do subskrypcji platformy Azure, zobacz [Licencjonowanie platformy Azure dla przedsiębiorstwa](https://azure.microsoft.com/pricing/enterprise-agreement/).
- **Dostęp do zasobów lokalnych** — niektóre organizacje wymagają, aby zasoby w DevTest Labs miały dostęp do zasobów lokalnych. Wymagana jest bezpieczne połączenie ze środowiska lokalnego z platformą Azure. W związku z tym ważne jest, aby skonfigurować/skonfigurować połączenie sieci VPN lub Express Route przed rozpoczęciem. Aby uzyskać więcej informacji, zobacz [Omówienie sieci wirtualnych](../virtual-network/virtual-networks-overview.md).
- **Dodatkowe wymagania dotyczące zabezpieczeń** — inne wymagania dotyczące zabezpieczeń, takie jak zasady komputera, dostęp do publicznych adresów IP, nawiązywanie połączenia z Internetem to scenariusze, które mogą wymagać sprawdzenia przed wdrożeniem weryfikacji koncepcji. 

## <a name="subscription-topology"></a>Topologia subskrypcji
Topologia subskrypcji to krytyczne zagadnienia dotyczące projektowania podczas wdrażania DevTest Labs w przedsiębiorstwie. Nie jest jednak konieczne zwiększyć wszystkich decyzji do momentu ukończenia weryfikacji koncepcji. Gdy oceniasz liczbę subskrypcji wymaganych dla implementacji przedsiębiorstwa, istnieją dwa skrajnie: 

- Jedna subskrypcja dla całej organizacji
- Subskrypcja na użytkownika

Następnie wykorzystamy zalety poszczególnych rozwiązań.

### <a name="one-subscription"></a>Jedna subskrypcja
Często podejście do jednej subskrypcji nie jest zarządzane w dużych przedsiębiorstwach. Jednak ograniczenie liczby subskrypcji zapewnia następujące korzyści:

- **Prognozowanie** kosztów dla przedsiębiorstwa.  Budżetowanie jest znacznie łatwiejsze w jednej subskrypcji, ponieważ wszystkie zasoby znajdują się w jednej puli. Takie podejście umożliwia prostsze podejmowanie decyzji w czasie wykonywania środków kontroli kosztów w danym momencie w cyklu rozliczeniowym.
- **Zarządzanie** maszynami wirtualnymi, artefaktami, formułami, konfiguracją sieci, uprawnieniami, zasadami itp. jest łatwiejsze, ponieważ wszystkie aktualizacje są wymagane tylko w jednej subskrypcji, w przeciwieństwie do dokonywania aktualizacji w wielu subskrypcjach.
- Praca w **sieci** jest znacznie uproszczona w ramach jednej subskrypcji dla przedsiębiorstw, w której jest wymagane połączenie lokalne. Łączenie sieci wirtualnych między subskrypcjami (model gwiazdy) jest wymagane z dodatkowymi subskrypcjami, co wymaga dodatkowej konfiguracji, zarządzania, przestrzeni adresów IP itp.
- **Współpraca zespołowa** jest łatwiejsza, gdy wszyscy pracują w tej samej subskrypcji — na przykład można łatwiej ponownie przypisać maszynę wirtualną do współpracowników, udostępnić zasoby zespołu itd.

### <a name="subscription-per-user"></a>Subskrypcja na użytkownika
Oddzielna subskrypcja na użytkownika zapewnia równe szanse na zakres alternatywny. Korzyści wynikające z posiadania wielu subskrypcji obejmują:

- **Przydziały skalowania platformy Azure** nie przeszkadzają na przyjęciu. Na przykład podczas pisania na platformie Azure dozwolone są 200 kont magazynu na subskrypcję. Istnieją przydziały operacyjne dla większości usług na platformie Azure (wiele z nich może być dostosowana). W tym modelu subskrypcji na użytkownika bardzo mało prawdopodobne jest osiągnięcie większości przydziałów. Aby uzyskać więcej informacji na temat bieżących przydziałów skalowania platformy Azure, zobacz [limity subskrypcji i usług platformy Azure, limity przydziału i ograniczenia](../azure-resource-manager/management/azure-subscription-service-limits.md).
- **Obciążeń zwrotnych** z grupami lub Indywidualni deweloperzy stają się znacznie łatwiejsze, aby organizacje mogli obsłużyć koszty związane z bieżącym modelem.
- **Prawa własności &** w środowiskach DevTest Labs są proste. Deweloperzy mogą uzyskać dostęp na poziomie subskrypcji i są 100% odpowiedzialności za wszystko, w tym konfigurację sieci, zasady laboratorium i zarządzanie maszyną wirtualną.

W przedsiębiorstwie mogą istnieć wystarczające ograniczenia dotyczące skrajnych wartości spektrum. W związku z tym może być konieczne skonfigurowanie subskrypcji w taki sposób, aby mieściły się w środku tych skrajnych wartości. Najlepszym rozwiązaniem jest, aby celem organizacji było użycie minimalnej liczby subskrypcji, jak to możliwe, z uwzględnieniem funkcji wymuszania, które zwiększają łączną liczbę subskrypcji. Aby ponownie wykonać iterację, topologia subskrypcji ma kluczowe znaczenie dla wdrożenia w przedsiębiorstwie DevTest Labs, ale nie powinna opóźniać weryfikacji koncepcji. W artykule dotyczącym [ładu](devtest-lab-guidance-governance-policy-compliance.md) znajdują się dodatkowe szczegółowe informacje na temat podejmowania decyzji dotyczących subskrypcji i stopnia szczegółowości laboratorium w organizacji.

## <a name="roles-and-responsibilities"></a>Role i obowiązki
Weryfikacja koncepcji DevTest Labs obejmuje trzy podstawowe role z określonymi zobowiązaniami — właściciela subskrypcji, właściciela DevTest Labs, użytkownika DevTest Labs, a opcjonalnie współautorem.

- **Właściciel subskrypcji** — właściciel subskrypcji ma uprawnienia do administrowania subskrypcją platformy Azure, w tym do przypisywania użytkowników, zarządzania zasadami, tworzenia & zarządzania topologią sieci, żądania zwiększenia limitu przydziału itd. Aby uzyskać więcej informacji, zobacz [ten artykuł](../role-based-access-control/rbac-and-directory-admin-roles.md).
- **Właściciel DevTest Labs** — właściciel DevTest Labs ma pełny dostęp administracyjny do laboratorium. Ta osoba jest odpowiedzialna za Dodawanie/usuwanie użytkowników, zarządzanie ustawieniami kosztów, ogólnymi ustawieniami laboratorium i innymi zadaniami maszyn wirtualnych/artefaktów. Właściciel laboratorium ma także wszystkie prawa użytkownika DevTest Labs.
- **Użytkownik DevTest Labs** — użytkownik DevTest Labs może tworzyć i zużywać maszyny wirtualne w laboratorium. Te osoby mają pewne minimalne możliwości administracyjne na maszynach wirtualnych, które tworzą (uruchamianie/zatrzymywanie/usuwanie/Konfigurowanie ich maszyn wirtualnych). Użytkownicy nie mogą zarządzać maszynami wirtualnymi innych użytkowników.

## <a name="next-steps"></a>Następne kroki
Zobacz następny artykuł z tej serii: [organizowanie implementacji Azure DevTest Labs](devtest-lab-guidance-orchestrate-implementation.md)