---
title: Azure Security Center spisu zasobów
description: Dowiedz się więcej na temat środowiska zarządzania zasobami Azure Security Center, które zapewnia pełną widoczność wszystkich Security Center monitorowanych zasobów.
author: memildin
manager: rkarlin
services: security-center
ms.author: memildin
ms.date: 08/11/2020
ms.service: security-center
ms.topic: conceptual
ms.openlocfilehash: 7fab15ae4e9e58af58a4490beb2d512379976d1c
ms.sourcegitcommit: 64ad2c8effa70506591b88abaa8836d64621e166
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/17/2020
ms.locfileid: "88264010"
---
# <a name="explore-and-manage-your-resources-with-asset-inventory-and-management-tools"></a>Eksplorowanie zasobów i zarządzanie nimi za pomocą spisu zasobów i narzędzi do zarządzania

Strona spisu zasobów Azure Security Center udostępnia pojedynczą stronę do wyświetlania stan zabezpieczeń zasobów, z którymi nawiązano połączenie Security Center. 

Security Center okresowo analizuje stan zabezpieczeń zasobów platformy Azure w celu zidentyfikowania potencjalnych luk w zabezpieczeniach. Następnie zawiera zalecenia dotyczące sposobu korygowania tych luk w zabezpieczeniach.

Jeśli którykolwiek z zasobów ma zaległe zalecenia, zostaną one wyświetlone w spisie.

Ten widok i jego filtry służą do rozwiązywania takich pytań jako:

- Które z subskrypcji warstwy standardowej mają zaległe rekomendacje?
- Na których maszynach ze znacznikiem "produkcja" brakuje agenta Log Analytics?
- Ile maszyn, Otagowano z określonym tagiem, mają zaległe rekomendacje?
- Ile zasobów w określonej grupie zasobów zawiera wyniki zabezpieczeń z usługi oceny luk w zabezpieczeniach?

Możliwości zarządzania zasobami dla tego narzędzia są znaczne i nadal rosną. 


## <a name="availability"></a>Dostępność

|Aspekt|Szczegóły|
|----|:----|
|Stan wydania:|Wersja zapoznawcza|
|Wpisaną|Jest bezpłatna|
|Wymagane role i uprawnienia:|Wszyscy użytkownicy|
|Połączeń|![Yes](./media/icons/yes-icon.png) Chmury komercyjne<br>![Nie](./media/icons/no-icon.png) National/suwerenne (US Gov, Chiny gov, inne gov)|
|||


## <a name="what-are-the-key-features-of-asset-inventory"></a>Jakie są kluczowe funkcje spisu zasobów?

Na stronie spisu dostępne są następujące narzędzia:

- **Podsumowania** — przed zdefiniowaniem filtrów, widoczny pasek wartości w górnej części widoku spisu pokazuje:

    - **Łączna liczba zasobów**: łączną liczbę zasobów podłączonych do Security Center.
    - **Zasoby w złej kondycji**: zasoby z zaleceniami dotyczącymi zabezpieczeń. [Dowiedz się więcej o zaleceniach dotyczących zabezpieczeń](https://docs.microsoft.com/azure/security-center/security-center-recommendations).
    - **Niemonitorowane zasoby**: zasoby z problemami związanymi z monitorowaniem agentów — mają wdrożony Agent log Analytics, ale agent nie wysyła danych lub ma inne problemy z kondycją.

- **Filtry** — wiele filtrów w górnej części strony umożliwia szybkie udoskonalenie listy zasobów na podstawie pytania, na które próbujesz odpowiedzieć. Na przykład jeśli chcesz odpowiedzieć na pytanie, *które z moich maszyn z tagiem "produkcja" brakuje agenta log Analytics?* można połączyć filtr **monitorowania agentów** z filtrem **tagów** , jak pokazano w następującym klipie:

    ![Filtrowanie do zasobów produkcyjnych, które nie są monitorowane](./media/asset-inventory/filtering-to-prod-unmonitored.gif)

    Po zastosowaniu filtrów podsumowania wartości są aktualizowane w celu powiązania z wynikami zapytania. 

- **Opcje eksportowania** — funkcja spisu udostępnia opcję eksportu wyników wybranych opcji filtru do pliku CSV. Ponadto możesz wyeksportować zapytanie do Eksploratora Azure Resource Graph, aby dodatkowo udoskonalać, zapisywać lub modyfikować zapytanie KQL.

    ![Opcje eksportu spisu](./media/asset-inventory/inventory-export-options.png)

    > [!TIP]
    > Dokumentacja KQL zawiera bazę danych z przykładowymi danymi wraz z niektórymi prostymi zapytaniami dotyczącymi języka. [Dowiedz się więcej w tym samouczku KQL](https://docs.microsoft.com/azure/data-explorer/kusto/query/tutorial?pivots=azuredataexplorer).

- **Opcje zarządzania zasobami** — spis umożliwia wykonywanie złożonych zapytań odnajdowania. Po znalezieniu zasobów pasujących do zapytań Spis zawiera skróty do operacji takich jak:

    - Przypisywanie tagów do filtrowanych zasobów — zaznacz pola wyboru obok zasobów, które chcesz oznaczyć tagami
    - Dodaj nowe serwery do Security Center — Użyj przycisku paska narzędzi **Dodawanie serwerów spoza platformy Azure**


## <a name="how-does-asset-inventory-work"></a>Jak działa spis zasobów?

Spis zasobów używa usługi Azure [Resource Graph (arg)](https://docs.microsoft.com/azure/governance/resource-graph/), która umożliwia wykonywanie Security Center zapytań dotyczących danych stan zabezpieczeń w wielu subskrypcjach.

ARGUMENT jest zaprojektowany w celu zapewnienia wydajnej eksploracji zasobów z możliwością wykonywania zapytań na dużą skalę.

Za pomocą [Kusto Query Language (KQL)](https://docs.microsoft.com/azure/data-explorer/kusto/query/), spis zasobów może szybko tworzyć szczegółowe informacje przez krzyżowe dane ASC z innymi właściwościami zasobów.


## <a name="how-to-use-asset-inventory"></a>Jak korzystać z spisu zasobów

1. Na pasku bocznym Security Center wybierz pozycję **spis**.

1. Opcjonalnie, aby wyświetlić określony zasób, wprowadź nazwę w polu **Filtruj według nazwy** .

1. Wybierz odpowiednie opcje w filtrach, aby utworzyć konkretne zapytanie, które chcesz wykonać.

    ![Filtry spisu](./media/asset-inventory/inventory-filters.png)

    Domyślnie zasoby są sortowane według liczby aktywnych zaleceń dotyczących zabezpieczeń.

    > [!IMPORTANT]
    > Opcje w każdym filtrze są specyficzne dla zasobów w aktualnie wybranych subskrypcjach **i** wybrane w innych filtrach.
    >
    > Na przykład jeśli wybrano tylko jedną subskrypcję, a subskrypcja nie ma żadnych zasobów z pozostałymi zaleceniami dotyczącymi zabezpieczeń, które zostaną skorygowane (0 zasobów w złej kondycji), filtr **zaleceń** nie będzie miał żadnych opcji. 

1. Aby można było użyć filtrów **zabezpieczeń** , należy wprowadzić tekst bezpłatny od identyfikatora, sprawdzenia zabezpieczeń lub nazwy CVE luki w zabezpieczeniach wyszukiwania do odpowiednich zasobów:

    ![Filtr "wyniki zabezpieczeń zawiera"](./media/asset-inventory/security-findings-contain-elements.png)

1. Aby użyć filtru **warstwy cenowej** , wybierz jedną lub więcej opcji (bezpłatna, częściowa lub standardowa):

    - Zasoby **bezpłatne** , które znajdują się w warstwie cenowej bezpłatna
    - Zasoby **standardowe** , które znajdują się w warstwie cenowej standardowa
    - **Częściowo** — dotyczy subskrypcji w warstwie Standardowa cenowej, ale niektóre opcjonalne plany zabezpieczeń są wyłączone. Na przykład następująca subskrypcja znajduje się w warstwie Standardowa, ale ma pięć elementów warstwy Standardowa wyłączona. 

        ![Subskrypcja warstwy cenowej standardowa (częściowa)](./media/asset-inventory/pricing-tier-partial.png)

1. Aby dodatkowo zbadać wyniki zapytania, wybierz interesujące Cię zasoby.

1. Opcjonalnie wybierz pozycję **Widok w Eksploratorze grafu zasobów** , aby otworzyć zapytanie w Eksploratorze grafów zasobów.

    ![Zapytanie spisu w ARG](./media/asset-inventory/inventory-query-in-resource-graph-explorer.png)

1. Jeśli zdefiniowano niektóre filtry i po lewej stronie zostanie otwarta strona, Security Center nie będzie automatycznie aktualizować wyników. Wszelkie zmiany w zasobach nie wpłyną na wyświetlane wyniki, chyba że ręcznie załadujesz ponownie stronę lub wybierzesz pozycję **Odśwież**.


## <a name="faq---inventory"></a>Często zadawane pytania — spis

### <a name="why-arent-all-of-my-subscriptions-machines-storage-accounts-etc-shown"></a>Dlaczego nie wszystkie moje subskrypcje, komputery, konta magazynu itp. są wyświetlane?

Widok spisu zawiera listę zasobów w perspektywie stan Management (CSPM) w chmurze. Filtry nie zwracają wszystkich zasobów w środowisku; tylko te z zaległymi (lub "aktywnymi") zaleceniami. 

Na przykład jeśli masz dziewięć subskrypcji, ale tylko osiem z nich ma zaleceń, po filtrowaniu według **typu zasobu = subskrypcje** zobaczysz tylko osiem subskrypcji z aktywnymi zaleceniami:

![Nie wszystkie są zwracane, gdy nie ma aktywnych rekomendacji](./media/asset-inventory/filtered-subscriptions-some.png)


### <a name="why-do-some-of-my-resources-show-blank-values-in-the-pricing-or-agent-monitoring-columns"></a>Dlaczego niektóre zasoby zawierają wartości puste w kolumnach monitorowanie cen i agentów?

Nie wszystkie Security Center monitorowane zasoby mają agentów. Na przykład konta usługi Azure Storage lub zasoby PaaS, takie jak dyski, Logic Apps, analiza Data Lake i centrum zdarzeń.

Gdy ceny lub monitorowanie agentów nie są odpowiednie dla zasobu, w tych kolumnach spisu nie będą wyświetlane żadne dane.

![Niektóre zasoby wyświetlają puste informacje w obszarze monitorowanie agenta lub kolumny cen](./media/asset-inventory/agent-pricing-blanks.png)



## <a name="next-steps"></a>Następne kroki

W tym artykule opisano stronę spisu zasobów Azure Security Center.

Aby uzyskać więcej informacji na temat powiązanych narzędzi, zobacz następujące strony:

- [Wykres zasobów platformy Azure (ARG)](https://docs.microsoft.com/azure/governance/resource-graph/)

- [Kusto Query Language (KQL)](https://docs.microsoft.com/azure/data-explorer/kusto/query/)