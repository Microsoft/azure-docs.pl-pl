---
title: Szybki Start — wdrażanie dzienników przepływu sieciowej grupy zabezpieczeń i zarządzanie nimi przy użyciu Azure Policy
titleSuffix: Azure Network Watcher
description: W tym artykule wyjaśniono, jak używać wbudowanych zasad do zarządzania wdrażaniem dzienników przepływu sieciowej grupy zabezpieczeń
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/01/2020
ms.author: damendo
ms.openlocfilehash: a603f55bd5bff7b2ed68623e9c712faaf8ac766f
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91877031"
---
# <a name="quickstart-deploy-and-manage-nsg-flow-logs-using-azure-policy"></a>Szybki Start: wdrażanie dzienników przepływu sieciowej grupy zabezpieczeń i zarządzanie nimi za pomocą Azure Policy 

## <a name="overview"></a>Omówienie
Usługa Azure Policy pomaga wymuszać standardy organizacyjne i oceniać zgodność na dużą skalę. Typowe przypadki użycia Azure Policy obejmują wdrażanie ładu pod kątem spójności zasobów, zgodności z przepisami, bezpieczeństwa, kosztów i zarządzania. W tym artykule będziemy używać dwóch wbudowanych zasad dostępnych dla dzienników przepływów sieciowej grupy zabezpieczeń do zarządzania konfiguracją dzienników przepływów. Pierwsze zasady flaguje wszelkie sieciowych grup zabezpieczeń bez włączonych dzienników przepływów. Druga zasada automatycznie wdraża dzienniki przepływów dla sieciowych grup zabezpieczeń bez włączonych dzienników przepływów. 

Jeśli tworzysz zasady platformy Azure po raz pierwszy, możesz przeczytać: 
- [Przegląd Azure Policy](https://docs.microsoft.com/azure/governance/policy/overview) 
- [Samouczek dotyczący tworzenia zasad](https://docs.microsoft.com/azure/governance/policy/assign-policy-portal#create-a-policy-assignment).


## <a name="locate-the-policies"></a>Lokalizowanie zasad
1. Przejdź do Azure Portal — [Portal.Azure.com](https://portal.azure.com) 

Przejdź do strony Azure Policy, wyszukując zasady na pierwszej ![ stronie głównej zasad paska wyszukiwania](./media/network-watcher-builtin-policy/1_policy-search.png)

2. Przejdź do karty **przypisania** w okienku po lewej stronie

![Karta przypisania](./media/network-watcher-builtin-policy/2_assignments-tab.png)

3. Kliknij przycisk **Przypisz zasady** 

![Przycisk przypisywania zasad](./media/network-watcher-builtin-policy/3_assign-policy-button.png)

4. Kliknij menu z trzema kropkami w obszarze "definicje zasad", aby wyświetlić dostępne zasady.

5. Użyj filtru typu i wybierz pozycję "wbudowane". Następnie wyszukaj ciąg "Flow log"

Powinny zostać wyświetlone dwie zasady wbudowane dla listy zasad dzienników przepływów ![](./media/network-watcher-builtin-policy/4_filter-for-flow-log-policies.png)

6. Wybierz zasady, które chcesz przypisać

- *Dla każdej sieciowej grupy zabezpieczeń należy skonfigurować* zasady inspekcji, które flaguje niezgodne sieciowych grup zabezpieczeń, które są sieciowych grup zabezpieczeń bez włączonego rejestrowania przepływów.
- *"Wdrożenie zasobu dziennika przepływu z grupą zabezpieczeń sieci docelowej"* to zasada z akcją wdrożenia, która umożliwia wykonywanie dzienników przepływów na wszystkich sieciowych grup zabezpieczeń bez dzienników przepływów

Poniżej znajdują się oddzielne instrukcje dotyczące poszczególnych zasad.  

## <a name="audit-policy"></a>Zasady dotyczące inspekcji 

### <a name="how-the-policy-works"></a>Jak działają zasady

Zasady sprawdzają wszystkie istniejące obiekty ARM typu "Microsoft. Network/networkSecurityGroups", które sprawdzają wszystkie sieciowych grup zabezpieczeń w danym zakresie i sprawdzają istnienie połączonych dzienników przepływów za pośrednictwem właściwości Logs dziennika sieciowej grupy zabezpieczeń. Jeśli właściwość nie istnieje, sieciowej grupy zabezpieczeń jest oflagowana.

Jeśli chcesz zobaczyć pełną definicję zasad, możesz odwiedzić [kartę definicje](https://ms.portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/Definitions) i wyszukać pozycję "dzienniki przepływu", aby znaleźć zasady

### <a name="assignment"></a>Przypisanie

1. Wypełnij szczegóły zasad

- Zakres: subskrypcja jest powszechną opcją, ale możesz też wybrać grupę zarządzania lub grupę zasobów stosownie do potrzeb.  
- Definicja zasad: należy wybrać, jak pokazano w sekcji "lokalizowanie zasad".
- Przypisanie: wybierz nazwę opisową 

2. Kliknij pozycję "Przejrzyj + Utwórz", aby przejrzeć przypisanie

Zasady nie wymagają żadnych parametrów. Podczas przypisywania zasad inspekcji nie trzeba wypełniać szczegółów na karcie "korygowanie".  

![Przegląd zasad inspekcji](./media/network-watcher-builtin-policy/5_1_audit-policy-review.png)

### <a name="results"></a>Wyniki

Aby sprawdzić wyniki, Otwórz kartę zgodność i wyszukaj nazwę przypisania.
Po uruchomieniu zasad powinna zostać wyświetlona coś podobnego do poniższego zrzutu ekranu. Jeśli zasady nie zostały uruchomione, poczekaj przez jakiś czas. 

![Inspekcja wyników zasad](./media/network-watcher-builtin-policy/7_1_audit-policy-results.png)

## <a name="deploy-if-not-exists-policy"></a>Zasady wdrażania — Jeśli nie istnieją 

### <a name="policy-structure"></a>Struktura zasad

Zasady sprawdzają wszystkie istniejące obiekty ARM typu "Microsoft. Network/networkSecurityGroups", które sprawdzają wszystkie sieciowych grup zabezpieczeń w danym zakresie i sprawdzają istnienie połączonych dzienników przepływów za pośrednictwem właściwości Logs dziennika sieciowej grupy zabezpieczeń. Jeśli właściwość nie istnieje, zasady wdrażają dziennik przepływu. 

Jeśli chcesz zobaczyć pełną definicję zasad, możesz odwiedzić [kartę definicje](https://ms.portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/Definitions) i wyszukać pozycję "dzienniki przepływu", aby znaleźć zasady. 

### <a name="assignment"></a>Przypisanie

1. Wypełnij szczegóły zasad

- Zakres: subskrypcja jest powszechną opcją, ale możesz też wybrać grupę zarządzania lub grupę zasobów stosownie do potrzeb.  
- Definicja zasad: należy wybrać, jak pokazano w sekcji "lokalizowanie zasad".
- Przypisanie: wybierz nazwę opisową 

2. Dodaj parametry zasad 

Usługa Network Watcher jest usługą regionalną. Te parametry umożliwiają wykonanie akcji zasad wdrażania dzienników przepływów. 
- Region sieciowej grupy zabezpieczeń: regiony platformy Azure, w których są stosowane zasady
- Identyfikator magazynu: pełny identyfikator zasobu konta magazynu. Uwaga: to konto magazynu powinno znajdować się w tym samym regionie co sieciowej grupy zabezpieczeń. 
- Obserwatorzy sieci RG: Nazwa grupy zasobów zawierającej zasób Network Watcher. Jeśli nie zmieniono jej nazwy, możesz wprowadzić wartość "NetworkWatcherRG", która jest wartością domyślną.
- Nazwa Network Watcher: nazwa usługi obserwatora sieci regionalnej. Format: NetworkWatcher_RegionName. Przykład: NetworkWatcher_centralus. Zobacz pełną listę.

![Parametry zasad DINE](./media/network-watcher-builtin-policy/5_2_1_dine-policy-details-alt.png)

3. Dodaj szczegóły korygowania

- Zaznacz pole wyboru "Utwórz zadanie korygowania", jeśli chcesz, aby zasady miały wpływ na istniejące zasoby 
- "Utwórz tożsamość zarządzaną" powinna być już zaznaczona
- Wybrano tę samą lokalizację, w której znajduje się Poprzednia tożsamość zarządzana 
- Aby móc korzystać z tych zasad, musisz mieć uprawnienia współautora lub właściciela. Jeśli masz te uprawnienia, nie powinno być wyświetlane żadne błędy.

![Korygowanie zasad DINE](./media/network-watcher-builtin-policy/5_2_2_dine-remediation.png) 

4. Kliknij pozycję "Przejrzyj + Utwórz", aby przejrzeć przydziały, zobacz coś podobnego do poniższego zrzutu ekranu.

![Przegląd zasad DINE](./media/network-watcher-builtin-policy/5_2_3_dine-review.png) 


### <a name="results"></a>Wyniki

Aby sprawdzić wyniki, Otwórz kartę zgodność i wyszukaj nazwę przypisania.
Po wprowadzeniu zasad powinien zostać wyświetlony podobny do poniższego zrzutu ekranu. Jeśli zasady nie zostały uruchomione, poczekaj przez jakiś czas.

![Wyniki zasad DINE](./media/network-watcher-builtin-policy/7_2_dine-policy-results.png)  


## <a name="next-steps"></a>Następne kroki 

-   Skorzystaj z tego [samouczka](https://docs.microsoft.com/azure/network-watcher/quickstart-configure-network-security-group-flow-logs-from-arm-template) , aby wdrożyć dzienniki przepływów i Analiza ruchu przy użyciu szablonów usługi ARM.
-   Dowiedz się więcej o [Network Watcher](https://docs.microsoft.com/azure/network-watcher/)
