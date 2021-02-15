---
title: Azure Security Center spisu zasobów
description: Dowiedz się więcej na temat środowiska zarządzania zasobami Azure Security Center, które zapewnia pełną widoczność wszystkich Security Center monitorowanych zasobów.
author: memildin
manager: rkarlin
services: security-center
ms.author: memildin
ms.date: 02/10/2021
ms.service: security-center
ms.topic: how-to
ms.openlocfilehash: 873fdba1d24db55b3269cc2c13f0140da4a9b4e3
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100393359"
---
# <a name="explore-and-manage-your-resources-with-asset-inventory"></a>Eksplorowanie zasobów i zarządzanie nimi za pomocą spisu zasobów

Strona spisu zasobów Azure Security Center udostępnia pojedynczą stronę do wyświetlania stan zabezpieczeń zasobów, z którymi nawiązano połączenie Security Center. 

Security Center okresowo analizuje stan zabezpieczeń zasobów platformy Azure w celu zidentyfikowania potencjalnych luk w zabezpieczeniach. Następnie zawiera zalecenia dotyczące sposobu korygowania tych luk w zabezpieczeniach.

Jeśli którykolwiek z zasobów ma zaległe zalecenia, zostaną one wyświetlone w spisie.

Ten widok i jego filtry służą do rozwiązywania takich pytań jako:

- Które z moich subskrypcji z włączoną usługą Azure Defender mają zaległe rekomendacje?
- Na których maszynach ze znacznikiem "produkcja" brakuje agenta Log Analytics?
- Ile maszyn z tagami z określonym tagiem ma zaległe rekomendacje?
- Ile zasobów w określonej grupie zasobów zawiera wyniki zabezpieczeń z usługi oceny luk w zabezpieczeniach?

Możliwości zarządzania zasobami dla tego narzędzia są znaczne i nadal rosną. 

> [!TIP]
> Zalecenia dotyczące zabezpieczeń na stronie spisu zasobów są takie same jak te na stronie **zalecenia** , ale w tym miejscu są wyświetlane zgodnie z zaatakowanym zasobem. Aby uzyskać informacje o sposobach rozwiązywania zaleceń, zobacz [implementowanie zaleceń dotyczących zabezpieczeń w Azure Security Center](security-center-recommendations.md).


## <a name="availability"></a>Dostępność
|Aspekt|Szczegóły|
|----|:----|
|Stan wydania:|Ogólna dostępność (GA)|
|Wpisaną|Bezpłatna|
|Wymagane role i uprawnienia:|Wszyscy użytkownicy|
|Połączeń|![Tak](./media/icons/yes-icon.png) Chmury komercyjne<br>![Tak](./media/icons/yes-icon.png) National/suwerenne (US Gov, Chiny gov, inne gov)|
|||


## <a name="what-are-the-key-features-of-asset-inventory"></a>Jakie są kluczowe funkcje spisu zasobów?
Na stronie spisu dostępne są następujące narzędzia:

:::image type="content" source="media/asset-inventory/highlights-of-inventory.png" alt-text="Główne funkcje strony spisu zasobów w Azure Security Center" lightbox="media/asset-inventory/highlights-of-inventory.png":::


### <a name="1---summaries"></a>1 — podsumowania
Przed zdefiniowaniem jakichkolwiek filtrów widoczny w górnej części widoku spisu są widoczne następujące wartości:

- **Łączna liczba zasobów**: łączną liczbę zasobów podłączonych do Security Center.
- **Zasoby w złej kondycji**: zasoby z zaleceniami dotyczącymi zabezpieczeń. [Dowiedz się więcej o zaleceniach dotyczących zabezpieczeń](security-center-recommendations.md).
- **Niemonitorowane zasoby**: zasoby z problemami związanymi z monitorowaniem agentów — mają wdrożony Agent log Analytics, ale agent nie wysyła danych lub ma inne problemy z kondycją.
- **Niezarejestrowane subskrypcje**: wszelkie subskrypcje w wybranym zakresie, które nie zostały jeszcze połączone z Azure Security Center.

### <a name="2---filters"></a>2 — filtry
Wiele filtrów w górnej części strony umożliwia szybkie udoskonalenie listy zasobów na podstawie pytania, na które próbujesz odpowiedzieć. Na przykład jeśli chcesz odpowiedzieć na pytanie, *które z moich maszyn ze znacznikiem "produkcja" brakuje agenta log Analytics?* filtr **monitorowania agentów** można połączyć z filtrem **Tagi** .

Po zastosowaniu filtrów podsumowania wartości są aktualizowane w celu powiązania z wynikami zapytania. 

### <a name="3---export-and-asset-management-tools"></a>3 — narzędzia do zarządzania eksportami i zasobami

**Opcje eksportu** — spis obejmuje opcję eksportu wyników wybranych opcji filtru do pliku CSV. Możesz również wyeksportować zapytanie do Eksploratora Azure Resource Graph, aby dodatkowo udoskonalać, zapisywać lub modyfikować zapytanie Kusto Query Language (KQL).

> [!TIP]
> Dokumentacja KQL zawiera bazę danych z przykładowymi danymi wraz z niektórymi prostymi zapytaniami dotyczącymi języka. [Dowiedz się więcej w tym samouczku KQL](/azure/data-explorer/kusto/query/tutorial?pivots=azuredataexplorer).

**Opcje zarządzania zasobami** — spis umożliwia wykonywanie złożonych zapytań odnajdowania. Po znalezieniu zasobów pasujących do zapytań Spis zawiera skróty do operacji takich jak:

- Przypisywanie tagów do filtrowanych zasobów — zaznacz pola wyboru obok zasobów, które chcesz oznaczyć tagami.
- Dodaj nowe serwery do Security Center — Użyj przycisku paska narzędzi **Dodawanie serwerów spoza platformy Azure** .
- Automatyzowanie obciążeń przy użyciu Azure Logic Apps — Użyj przycisku **Wyzwalaj aplikację logiki** , aby uruchomić aplikację logiki w jednym lub większej liczbie zasobów. Aplikacje logiki muszą zostać przygotowane z wyprzedzeniem i zaakceptować odpowiedni typ wyzwalacza (żądanie HTTP). [Dowiedz się więcej o usłudze Logic Apps](../logic-apps/logic-apps-overview.md).


## <a name="how-does-asset-inventory-work"></a>Jak działa spis zasobów?

Spis zasobów używa usługi Azure [Resource Graph (arg)](../governance/resource-graph/index.yml), która umożliwia wykonywanie Security Center zapytań dotyczących danych stan zabezpieczeń w wielu subskrypcjach.

ARGUMENT jest zaprojektowany w celu zapewnienia wydajnej eksploracji zasobów z możliwością wykonywania zapytań na dużą skalę.

Za pomocą [Kusto Query Language (KQL)](/azure/data-explorer/kusto/query/), spis zasobów może szybko tworzyć szczegółowe informacje przez krzyżowe dane ASC z innymi właściwościami zasobów.


## <a name="how-to-use-asset-inventory"></a>Jak korzystać z spisu zasobów

1. Na pasku bocznym Security Center wybierz pozycję **spis**.

1. Użyj pola **Filtruj według nazwy** , aby wyświetlić określony zasób, lub Użyj filtrów zgodnie z poniższym opisem.

1. Wybierz odpowiednie opcje w filtrach, aby utworzyć konkretne zapytanie, które chcesz wykonać.

    Domyślnie zasoby są sortowane według liczby aktywnych zaleceń dotyczących zabezpieczeń.

    > [!IMPORTANT]
    > Opcje w każdym filtrze są specyficzne dla zasobów w aktualnie wybranych subskrypcjach **i** wybrane w innych filtrach.
    >
    > Na przykład jeśli wybrano tylko jedną subskrypcję, a subskrypcja nie ma żadnych zasobów z pozostałymi zaleceniami dotyczącymi zabezpieczeń, które zostaną skorygowane (0 zasobów w złej kondycji), filtr **zaleceń** nie będzie miał żadnych opcji. 

    :::image type="content" source="./media/asset-inventory/filtering-to-prod-unmonitored.gif" alt-text="Używanie opcji filtru w Azure Security Center spisie zasobów do filtrowania zasobów do zasobów produkcyjnych, które nie są monitorowane":::

1. Aby można było użyć filtrów **zabezpieczeń** , należy wprowadzić tekst bezpłatny od identyfikatora, sprawdzenia zabezpieczeń lub nazwy CVE luki w zabezpieczeniach wyszukiwania do odpowiednich zasobów:

    ![Filtr "wyniki zabezpieczeń zawiera"](./media/asset-inventory/security-findings-contain-elements.png)

    > [!TIP]
    > **Ustalenia dotyczące zabezpieczeń zawierają** filtry **tagów** , które akceptują tylko jedną wartość. Aby filtrować według więcej niż jednego, użyj **Dodaj filtry**.

1. Aby użyć filtru **usługi Azure Defender** , wybierz jedną lub więcej opcji (wyłączone, włączone lub częściowe):

    - **Poza** zasobami, które nie są chronione przez plan usługi Azure Defender. Możesz kliknąć prawym przyciskiem myszy dowolny z tych elementów i uaktualnić je:

        :::image type="content" source="./media/asset-inventory/upgrade-resource-inventory.png" alt-text="Uaktualnianie zasobu do usługi Azure Defender po kliknięciu prawym przyciskiem myszy" lightbox="./media/asset-inventory/upgrade-resource-inventory.png":::

    -  Zasoby, które są chronione przez plan usługi Azure Defender
    - **Częściowo** — dotyczy **subskrypcji** , w przypadku których niektóre plany usługi Azure Defender nie zostały wyłączone. Na przykład następująca subskrypcja ma wyłączone pięć planów usługi Azure Defender. 

        :::image type="content" source="./media/asset-inventory/pricing-tier-partial.png" alt-text="Subskrypcja częściowo w usłudze Azure Defender":::

1. Aby dodatkowo zbadać wyniki zapytania, wybierz interesujące Cię zasoby.

1. Aby wyświetlić bieżące wybrane opcje filtru jako zapytanie w Eksploratorze grafu zasobów, wybierz pozycję **Otwórz zapytanie**.

    ![Zapytanie spisu w ARG](./media/asset-inventory/inventory-query-in-resource-graph-explorer.png)

1. Aby uruchomić wcześniej zdefiniowaną aplikację logiki za pomocą 

1. Jeśli zdefiniowano niektóre filtry i po lewej stronie zostanie otwarta strona, Security Center nie będzie automatycznie aktualizować wyników. Wszelkie zmiany w zasobach nie wpłyną na wyświetlane wyniki, chyba że ręcznie załadujesz ponownie stronę lub wybierzesz pozycję **Odśwież**.


## <a name="faq---inventory"></a>Często zadawane pytania — spis

### <a name="why-arent-all-of-my-subscriptions-machines-storage-accounts-etc-shown"></a>Dlaczego nie wszystkie moje subskrypcje, komputery, konta magazynu itp. są wyświetlane?

Widok spisu zawiera listę Security Center połączonych zasobów z perspektywy usługi Cloud Security stan Management (CSPM). Filtry nie zwracają wszystkich zasobów w środowisku; tylko te z zaległymi (lub "aktywnymi") zaleceniami. 

Na przykład poniższy zrzut ekranu przedstawia użytkownika z dostępem do subskrypcji 38, ale tylko 10 ma obecnie zalecenia. Dlatego w przypadku filtrowania według **typu zasobu = subskrypcje** w spisie są wyświetlane tylko 10 subskrypcji z aktywnymi zaleceniami:

:::image type="content" source="./media/asset-inventory/filtered-subscriptions-some.png" alt-text="Nie wszystkie są zwracane, gdy nie ma aktywnych rekomendacji":::

### <a name="why-do-some-of-my-resources-show-blank-values-in-the-azure-defender-or-agent-monitoring-columns"></a>Dlaczego niektóre zasoby pokazują puste wartości w kolumnach monitorowania usługi Azure Defender lub agenta?

Nie wszystkie Security Center monitorowane zasoby mają agentów. Na przykład konta usługi Azure Storage lub zasoby PaaS, takie jak dyski, Logic Apps, analiza Data Lake i centrum zdarzeń.

Gdy ceny lub monitorowanie agentów nie są odpowiednie dla zasobu, w tych kolumnach spisu nie będą wyświetlane żadne dane.

:::image type="content" source="./media/asset-inventory/agent-pricing-blanks.png" alt-text="W niektórych zasobach są wyświetlane puste informacje w kolumnach monitorowanie agenta lub Azure Defender":::

## <a name="next-steps"></a>Następne kroki

W tym artykule opisano stronę spisu zasobów Azure Security Center.

Aby uzyskać więcej informacji na temat powiązanych narzędzi, zobacz następujące strony:

- [Wykres zasobów platformy Azure (ARG)](../governance/resource-graph/index.yml)
- [Kusto Query Language (KQL)](/azure/data-explorer/kusto/query/)