---
title: Śledzenie bezpiecznego wyniku w Azure Security Center
description: Dowiedz się więcej na temat wielu sposobów uzyskiwania dostępu do i śledzenia bezpiecznego wyniku w Azure Security Center.
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 02/25/2021
ms.openlocfilehash: 5efc48d348e9cfceab590bcfba8c621e7721376f
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102107965"
---
# <a name="access-and-track-your-secure-score"></a>Dostęp i śledzenie bezpiecznego wyniku

Możesz znaleźć ogólny, bezpieczny wynik, a także wynik na subskrypcję za pomocą Azure Portal lub programowo zgodnie z opisem w poniższych sekcjach:

> [!TIP]
> Aby uzyskać szczegółowy opis sposobu, w jaki wyniki są obliczane, zobacz [obliczenia — zrozumienie oceny](secure-score-security-controls.md#calculations---understanding-your-score).

## <a name="get-your-secure-score-from-the-portal"></a>Pobierz swój Bezpieczny wynik z portalu

Security Center wyświetla wyniki w portalu w widocznym miejscu: jest to pierwszy główny kafelek na stronie Przegląd Security Center. Wybranie tego kafelka spowoduje przejście na stronę dedykowanego, bezpiecznego oceny, w której zobaczysz wynik podzielony przez subskrypcję. Wybierz jedną subskrypcję, aby wyświetlić szczegółową listę zaleceń z priorytetami i potencjalny wpływ, który korygowaniem ich na ocenę subskrypcji. 

Aby podsumowanie, Twój bezpieczny wynik jest wyświetlany w następujących lokalizacjach na stronach portalu Security Center.

- Na kafelku na Security Center **Przegląd** (główny pulpit nawigacyjny):

    :::image type="content" source="./media/secure-score-security-controls/score-on-main-dashboard.png" alt-text="Bezpieczna Ocena na pulpicie nawigacyjnym Security Center":::

- Na stronie dedykowany wskaźnik **zabezpieczeń** możesz zobaczyć bezpieczny wynik subskrypcji i grup zarządzania:

    :::image type="content" source="./media/secure-score-security-controls/score-on-dedicated-dashboard.png" alt-text="Bezpieczna ocena subskrypcji na stronie bezpiecznej oceny Security Center":::

    :::image type="content" source="./media/secure-score-security-controls/secure-score-management-groups.png" alt-text="Bezpieczna ocena grup zarządzania na Security Center stronie zabezpieczonych wyników":::

    > [!NOTE]
    > Wszystkie grupy zarządzania, dla których nie masz wystarczających uprawnień, będą wyświetlać ich wyniki jako "z ograniczeniami". 

- W górnej części strony **zalecenia** :

    :::image type="content" source="./media/secure-score-security-controls/score-on-recommendations-page.png" alt-text="Bezpieczna Ocena na stronie rekomendacji Security Center":::

## <a name="get-your-secure-score-from-the-rest-api"></a>Uzyskiwanie bezpiecznego wyniku z interfejsu API REST

Możesz uzyskać dostęp do oceny za pośrednictwem interfejsu API protokołu Secure Score. Metody interfejsu API zapewniają elastyczność umożliwiającą wykonywanie zapytań dotyczących danych i Tworzenie własnego mechanizmu raportowania z bezpiecznymi wynikami w czasie. Na przykład możesz użyć [interfejsu API Secure Scores](/rest/api/securitycenter/securescores) , aby uzyskać ocenę dla określonej subskrypcji. Ponadto można użyć [interfejsu API kontroli](/rest/api/securitycenter/securescorecontrols) zabezpieczeń, aby wyświetlić listę kontrolek bezpieczeństwa i bieżący wynik subskrypcji.

![Pobieranie pojedynczego, bezpiecznego wyniku za pośrednictwem interfejsu API](media/secure-score-security-controls/single-secure-score-via-api.png)

Przykłady narzędzi wbudowanych w interfejsie API oceny zabezpieczeń znajdują się w obszarze "bezpieczeństwo" w [naszej społeczności usługi GitHub](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score). 

## <a name="get-your-secure-score-from-azure-resource-graph"></a>Pobierz swój Bezpieczny wynik z grafu zasobów platformy Azure

Usługa Azure Resource Graph zapewnia błyskawiczny dostęp do informacji o zasobach w środowiskach chmury z niezawodnymi możliwościami filtrowania, grupowania i sortowania. Jest to szybki i wydajny sposób wykonywania zapytań dotyczących informacji w ramach subskrypcji platformy Azure programowo lub z poziomu Azure Portal. [Dowiedz się więcej o usłudze Azure Resource Graph](../governance/resource-graph/index.yml).

Aby uzyskać dostęp do bezpiecznego wyniku dla wielu subskrypcji za pomocą usługi Azure Resource Graph:

1. W Azure Portal Otwórz **Eksploratora Azure Resource Graph**.

    :::image type="content" source="./media/security-center-identity-access/opening-resource-graph-explorer.png" alt-text="Uruchamianie Eksploratora Azure Resource Graph * * zalecenie" :::

1. Wprowadź zapytanie Kusto (korzystając z przykładów poniżej, aby uzyskać wskazówki).

    - To zapytanie zwraca identyfikator subskrypcji, bieżący wynik w punktach i jako wartość procentową oraz maksymalny wynik dla subskrypcji. 

        ```kusto
        SecurityResources 
        | where type == 'microsoft.security/securescores' 
        | extend current = properties.score.current, max = todouble(properties.score.max)
        | project subscriptionId, current, max, percentage = ((current / max)*100)
        ```

    - To zapytanie zwraca stan wszystkich formantów zabezpieczeń. Dla każdej kontrolki uzyskasz liczbę zasobów w złej kondycji, bieżącą ocenę i maksymalny wynik. 

        ```kusto
        SecurityResources 
        | where type == 'microsoft.security/securescores/securescorecontrols'
        | extend SecureControl = properties.displayName, unhealthy = properties.unhealthyResourceCount, currentscore = properties.score.current, maxscore = properties.score.max
        | project SecureControl , unhealthy, currentscore, maxscore
        ```

1. Wybierz pozycję **Uruchom zapytanie**.


## <a name="tracking-your-secure-score-over-time"></a>Śledzenie bezpiecznego wyniku w czasie

### <a name="secure-score-over-time-report-in-workbooks-page"></a>Raport dotyczący raportu o czasie na stronie skoroszytów

Strona skoroszyty Security Center zawiera gotowy raport służący do wizualnego śledzenia wyników subskrypcji, kontroli zabezpieczeń i innych. Dowiedz się więcej w temacie [tworzenie rozbudowanych, interaktywnych raportów dotyczących danych Security Center](custom-dashboards-azure-workbooks.md).

:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-snip.png" alt-text="Sekcja raportu &quot;Raport z czasu bezpiecznego w czasie&quot; z galerii skoroszytów Azure Security Center":::

### <a name="power-bi-pro-dashboards"></a>Pulpity nawigacyjne Power BI Pro

Jeśli jesteś użytkownikiem Power BI przy użyciu konta Pro, możesz użyć pulpitu nawigacyjnego " **Secure score in Time** Power BI", aby śledzić swój Bezpieczny wynik w czasie i badać wszelkie zmiany.

> [!TIP]
> Możesz znaleźć ten pulpit nawigacyjny, a także inne narzędzia do pracy z programowo z bezpiecznymi wynikami w dedykowanym obszarze społeczności Azure Security Center w witrynie GitHub: https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score

Pulpit nawigacyjny zawiera dwa następujące raporty, które ułatwiają Analizowanie stanu zabezpieczeń:

- **Podsumowanie zasobów** — zawiera podsumowanie danych dotyczących kondycji zasobów.
- **Podsumowanie oceny zabezpieczeń** — zawiera podsumowanie danych dotyczących postępu oceny. Aby wyświetlić zmiany w wyniku, użyj wykresu "stawka" w czasie na subskrypcję ". Jeśli zauważysz istotną zmianę w wyniku, sprawdź "wykryte zmiany, które mogą mieć wpływ na tabelę zabezpieczonych wyników" dla możliwych zmian, które mogłyby spowodować zmianę. W tej tabeli przedstawiono usunięte zasoby, nowo wdrożone zasoby lub zasoby, dla których zmieniono stan zabezpieczeń dla jednego z zaleceń.

:::image type="content" source="./media/secure-score-security-controls/power-bi-secure-score-dashboard.png" alt-text="Opcjonalny bezpieczny wynik na pulpicie nawigacyjnym Power BI czasu na śledzenie bezpiecznego wyniku w czasie i badanie zmian":::


## <a name="next-steps"></a>Następne kroki

W tym artykule opisano, jak uzyskać dostęp do i śledzić swój Bezpieczny wynik. W przypadku pokrewnego materiału zapoznaj się z następującymi artykułami:

- [Poznaj różne elementy zalecenia](security-center-recommendations.md)
- [Dowiedz się, jak skorygować zalecenia](security-center-remediate-recommendations.md)
- [Wyświetlanie narzędzi opartych na usłudze GitHub w celu programistycznego pracy z bezpiecznym wynikiem](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score)