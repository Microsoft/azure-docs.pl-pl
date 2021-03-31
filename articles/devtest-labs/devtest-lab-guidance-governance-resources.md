---
title: Zarządzanie infrastrukturą Azure DevTest Labs — zasób
description: Ten artykuł dotyczy wyrównania i zarządzania zasobami dla Azure DevTest Labs w organizacji.
ms.topic: article
ms.date: 06/26/2020
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 1e470da5cd317d49f0d0734caa11eed6630d3f32
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "85480919"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---resources"></a>Zarządzanie infrastrukturą Azure DevTest Labs — zasoby
Ten artykuł dotyczy wyrównania i zarządzania zasobami dla DevTest Labs w organizacji. 

## <a name="align-within-an-azure-subscription"></a>Wyrównywanie w ramach subskrypcji platformy Azure 

### <a name="question"></a>Pytanie
Jak mogę wyrównać zasoby DevTest Labs w ramach subskrypcji platformy Azure?

### <a name="answer"></a>Odpowiedź
Zanim organizacja zacznie używać platformy Azure do ogólnego opracowywania aplikacji, planisty IT powinny najpierw zapoznać się z wprowadzeniem możliwości jako części ogólnego portfela usług. Obszary do przeglądu powinny dotyczyć następujących kwestii:

- Jak zmierzyć koszt związany z cyklem tworzenia aplikacji?
- Jak organizacja dopasowuje proponowaną ofertę usługi przy użyciu firmowych zasad zabezpieczeń? 
- Czy segmentacja jest wymagana do oddzielenia środowisk deweloperskich i produkcyjnych? 
- Jakie kontrolki są wprowadzane do długoterminowej łatwości zarządzania, stabilności i wzrostu?

**Pierwszym zalecanym** celem jest przejrzenie taksonomii platformy Azure w organizacji, w której znajdują się podziały między subskrypcjami produkcyjnymi i programistycznymi. Na poniższym diagramie Sugerowana Taksonomia umożliwia logiczne rozdzielenie środowiska deweloperskiego/testowego i produkcyjnego. W ramach tego podejścia organizacja może wprowadzić kody rozliczeń, aby śledzić koszty związane z każdym środowiskiem osobno. Aby uzyskać więcej informacji, zobacz [zalecenia dotyczące zarządzania subskrypcjami](/azure/architecture/cloud-adoption/appendix/azure-scaffold). Ponadto można używać [tagów platformy Azure](../azure-resource-manager/management/tag-resources.md) do organizowania zasobów na potrzeby śledzenia i rozliczania.

**Drugim zalecanym sposobem** jest włączenie subskrypcji DevTest w portalu Azure Enterprise. Dzięki temu organizacja może uruchamiać systemy operacyjne klienta, które nie są zwykle dostępne w ramach subskrypcji platformy Azure Enterprise. Następnie korzystaj z oprogramowania korporacyjnego, w którym płacisz wyłącznie za obliczenia i nie obawiaj się o Licencjonowanie. Gwarantuje to, że opłaty za wyznaczone usługi, w tym obrazy z galerii w IaaS, takie jak Microsoft SQL Server, opierają się wyłącznie na zużyciu. Szczegółowe informacje o subskrypcji usługi Azure DevTest można znaleźć [tutaj](https://azure.microsoft.com/offers/ms-azr-0148p/) dla klientów z Enterprise Agreement (EA) i w [tym miejscu](https://azure.microsoft.com/offers/ms-azr-0023p/) dla klientów z opcją płatność zgodnie z rzeczywistym użyciem.

![Wyrównanie zasobów przy użyciu subskrypcji](./media/devtest-lab-guidance-governance/resource-alignment-with-subscriptions.png)

Ten model zapewnia organizacjom elastyczność umożliwiającą wdrażanie Azure DevTest Labs w odpowiedniej skali. Organizacja może obsługiwać setki laboratoriów dla różnych jednostek gospodarczych z 100 do 1000 maszyn wirtualnych działających równolegle. Promuje pojęcie scentralizowanego rozwiązania Enterprise Lab, które może korzystać z tych samych zasad zarządzania konfiguracją i kontroli zabezpieczeń.

Ten model gwarantuje również, że organizacja nie wyczerpuje limitów zasobów skojarzonych z ich subskrypcją platformy Azure. Aby uzyskać szczegółowe informacje o limitach subskrypcji i usług, zobacz [limity subskrypcji i usług platformy Azure, przydziały i ograniczenia](../azure-resource-manager/management/azure-subscription-service-limits.md). Proces aprowizacji DevTest Labs może zużywać dużą liczbę grup zasobów. Możesz poprosić o zwiększenie limitów przez żądanie pomocy technicznej w ramach subskrypcji usługi Azure DevTest. Nie ma to żadnego oddziaływania na zasoby w ramach subskrypcji produkcyjnej, ponieważ zwiększa się użycie subskrypcji deweloperskiej. Aby uzyskać więcej informacji na temat skalowania DevTest Labs, zobacz [skalowanie przydziałów i limitów w laboratoriach DevTest](devtest-lab-scale-lab.md).

Wspólny limit na poziomie subskrypcji, dla którego należy uwzględnić, to sposób przydzielania przypisań zakresu adresów IP sieci do obsługi zarówno subskrypcji produkcyjnych, jak i deweloperskich. Te przydziały powinny uwzględniać wzrost czasu (przy założeniu, że lokalna lub inna topologia sieci wymaga od przedsiębiorstwa zarządzania stosem sieciowym zamiast domyślnego wdrożenia platformy Azure). Zalecanym sposobem jest posiadanie kilku sieci wirtualnych, które mają duże prefiksy adresów IP i są dzielone z wieloma dużymi podsieciami, a nie z wieloma sieciami wirtualnymi z małymi podsieciami. Na przykład w przypadku 10 subskrypcji można zdefiniować 10 sieci wirtualnych (jednej dla każdej subskrypcji). Wszystkie laboratoria, które nie wymagają izolacji, mogą współużytkować tę samą podsieć w sieci wirtualnej subskrypcji.

## <a name="maintain-naming-conventions"></a>Obsługa konwencji nazewnictwa

### <a name="question"></a>Pytanie
Jak mogę zachować konwencję nazewnictwa w środowisku DevTest Labs?

### <a name="answer"></a>Odpowiedź
Możesz chcieć przyciągnąć bieżące konwencje nazewnictwa przedsiębiorstwa do operacji platformy Azure i zapewnić ich spójność w środowisku DevTest Labs.

Zalecamy, aby podczas wdrażania DevTest Labs były określone zasady uruchamiania. Te zasady są wdrażane za pomocą centralnego skryptu i szablonów JSON w celu wymuszenia spójności. Zasady nazewnictwa można zaimplementować za pomocą zasad platformy Azure, które są stosowane na poziomie subskrypcji. Aby uzyskać przykłady JSON dla Azure Policy, zobacz [Azure Policy Samples](../governance/policy/samples/index.md).

## <a name="number-of-users-per-lab-and-labs-per-organization"></a>Liczba użytkowników na laboratoria i laboratoria na organizację

### <a name="question"></a>Pytanie 
Jak mogę określić stosunek użytkowników na laboratorium i ogólną liczbę laboratoriów wymaganych przez organizację?

### <a name="answer"></a>Odpowiedź
Zalecamy, aby jednostki biznesowe i grupy programistyczne skojarzone z tym samym projektem programistycznym były skojarzone z tym samym laboratorium. Umożliwia zastosowanie tych samych typów zasad, obrazów i zasad zamykania do obu grup. 

Może być również konieczne rozważenie granic geograficznych. Na przykład deweloperzy w regionie Północno-środkowe Stany Zjednoczone (USA) mogą używać laboratorium zainicjowanego w regionie Wschodnie stany USA 2. Deweloperzy w Dallas, Texas i Denver mogą być kierowani do używania zasobów w Południowo-środkowe stany USA. W przypadku współpracy z zewnętrznymi osobami trzecimi mogą zostać przypisane do laboratorium, które nie jest używane przez wewnętrznych deweloperów. 

Możesz również użyć laboratorium dla określonego projektu w ramach projektów DevOps platformy Azure. Następnie należy zastosować zabezpieczenia za pomocą określonej grupy Azure Active Directory, co umożliwia dostęp do obu zestawów zasobów. Sieć wirtualna przypisana do laboratorium może być kolejną granicą, aby skonsolidować użytkowników.

## <a name="deletion-of-resources"></a>Usuwanie zasobów

### <a name="question"></a>Pytanie
Jak zapobiegamy usunięciu zasobów w ramach laboratorium?

### <a name="answer"></a>Odpowiedź
Zaleca się, aby ustawić odpowiednie uprawnienia na poziomie laboratorium, tak aby tylko autoryzowani użytkownicy mogli usuwać zasoby lub zmieniać zasady laboratorium. Deweloperzy powinni być umieszczani w grupie **użytkowników DevTest Labs** . Deweloperem potencjalnego klienta lub liderem infrastruktury powinien być **właściciel DevTest Labs**. Zalecamy posiadanie tylko dwóch właścicieli laboratorium. Te zasady rozszerzają się do repozytorium kodu w celu uniknięcia uszkodzenia. Program Lab stosuje prawa do używania zasobów, ale nie może aktualizować zasad laboratorium. Zapoznaj się z poniższym artykułem zawierającym listę ról i praw, które każda wbudowana grupa ma w laboratorium: [Dodawanie właścicieli i użytkowników w Azure DevTest Labs](devtest-lab-add-devtest-user.md).

## <a name="move-lab-to-another-resource-group"></a>Przenieś laboratorium do innej grupy zasobów 

### <a name="question"></a>Pytanie
Czy jest obsługiwane przenoszenie laboratorium do innej grupy zasobów?

### <a name="answer"></a>Odpowiedź
Tak. Przejdź do strony grupy zasobów ze strony głównej dla laboratorium. Następnie wybierz pozycję **Przenieś** na pasku narzędzi i wybierz laboratorium, które chcesz przenieść do innej grupy zasobów. Po utworzeniu laboratorium zostanie automatycznie utworzona grupa zasobów. Można jednak przenieść laboratorium do innej grupy zasobów, która jest zgodna z konwencjami nazewnictwa przedsiębiorstwa. 

## <a name="next-steps"></a>Następne kroki
Zobacz [Zarządzanie kosztami i własnością](devtest-lab-guidance-governance-cost-ownership.md).
