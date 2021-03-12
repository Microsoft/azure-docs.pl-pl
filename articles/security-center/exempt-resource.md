---
title: Zwolnij zalecenie dotyczące Azure Security Center z zasobu, subskrypcji, grupy zarządzania i bezpiecznego wyniku
description: Dowiedz się, jak utworzyć reguły wykluczające zalecenia dotyczące zabezpieczeń z subskrypcji lub grup zarządzania i uniemożliwić im wpływ na bezpieczny wynik
author: memildin
ms.author: memildin
ms.date: 03/11/2021
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: d3627f6bcda7a18204c24fc2a1347c4a512c5369
ms.sourcegitcommit: 6776f0a27e2000fb1acb34a8dddc67af01ac14ac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103149736"
---
# <a name="exempting-resources-and-recommendations-from-your-secure-score"></a>Zwalnianie zasobów i zaleceń z bezpiecznego wyniku 

Głównym priorytetem każdego zespołu ds. zabezpieczeń jest zapewnienie analityków, którzy będą mogli skupić się na zadaniach i zdarzeniach, które mają znaczenie dla organizacji. Security Center ma wiele funkcji służących do dostosowywania środowiska i zapewnienia, że Twój bezpieczny wynik odzwierciedla priorytety zabezpieczeń organizacji. Opcja **wykluczania** to jedna z tych funkcji.

Po zbadaniu zaleceń dotyczących zabezpieczeń w Azure Security Center jedną z pierwszych informacji, którą przeglądasz, jest lista zasobów, których to dotyczy.

Czasami zasób zostanie wyświetlony na liście, który nie powinien być uwzględniony. Lub zalecenie będzie widoczne w zakresie, w którym uważasz, że nie należy. Zasób mógł zostać skorygowany przez proces, który nie jest śledzony przez Security Center. Zalecenie może być nieodpowiednie dla określonej subskrypcji. Lub być może Twoja organizacja zdecydowała się na zaakceptowanie ryzyka związanego z konkretnym zasobem lub zaleceniem.

W takich przypadkach można utworzyć wykluczenie dla zalecenia:

- **Zwolnij zasób** , aby upewnić się, że nie znajduje się na liście zasobów w złej kondycji w przyszłości i nie ma wpływu na bezpieczny wynik. Zasób będzie wyświetlany jako nie dotyczy, a powód zostanie wyświetlony jako "zwolniony" z określonym uzasadnieniem.

- **Zwolnij subskrypcję lub grupę zarządzania** , aby upewnić się, że zalecenie nie ma wpływu na Twój bezpieczny wynik i nie będzie wyświetlane dla subskrypcji lub grupy zarządzania w przyszłości. Odnosi się to do istniejących zasobów i wszystkich utworzonych w przyszłości. Zalecenie zostanie oznaczone określonym uzasadnieniem wybranym dla wybranego zakresu.

## <a name="availability"></a>Dostępność

| Aspekt                          | Szczegóły                                                                                                                                                                                                                                                                                                                            |
|---------------------------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Stan wydania:                  | Wersja zapoznawcza<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)]                                                                                                                                                                                                                                             |
| Wpisaną                        | Jest to funkcja Premium platformy Azure oferowana dla klientów usługi Azure Defender bez dodatkowych kosztów. W przypadku innych użytkowników opłaty mogą być stosowane w przyszłości.                                                                                                                                                                 |
| Wymagane role i uprawnienia: | **Właściciel subskrypcji** lub **współautor zasad** , aby utworzyć wykluczenie<br>Aby utworzyć regułę, musisz mieć uprawnienia do edytowania zasad w Azure Policy.<br>Dowiedz się więcej na temat [uprawnień kontroli RBAC platformy Azure w Azure Policy](../governance/policy/overview.md#azure-rbac-permissions-in-azure-policy).                                            |
| Ograniczenia:                    | Zwolnienia mogą być tworzone tylko dla zaleceń uwzględnionych w ramach inicjatywy domyślnej Security Center, testu porównawczego zabezpieczeń platformy Azure lub dowolnych z dostarczonych, standardowych inicjatyw prawnych. Nie można wykluczać zaleceń, które są generowane na podstawie inicjatyw niestandardowych. Dowiedz się więcej na temat relacji między [zasadami, inicjatywami i zaleceniami](security-policy-concept.md). |
| Połączeń                         | ![Tak](./media/icons/yes-icon.png) Chmury komercyjne<br>![Nie](./media/icons/no-icon.png) National/suwerenne (US Gov, Chiny gov, inne gov)                                                                                                                                                                                         |
|                                 |                                                                                                                                                                                                                                                                                                                                    |

## <a name="define-an-exemption"></a>Definiowanie wykluczenia

Aby dostosować zalecenia dotyczące zabezpieczeń, które Security Center dla subskrypcji, grupy zarządzania lub zasobów, można utworzyć regułę wykluczania w celu:

- Oznacz określone **zalecenie** lub jako "skorygowane" lub "zaakceptowane ryzyko". Można utworzyć wykluczenia rekomendacji dla subskrypcji, wielu subskrypcji lub całej grupy zarządzania.
- Oznacz **co najmniej jeden zasób** jako "skorygowany" lub "ryzyko zaakceptowania" dla konkretnego zalecenia.

> [!NOTE]
> Zwolnienia mogą być tworzone tylko dla zaleceń uwzględnionych w ramach inicjatywy domyślnej Security Center, testu porównawczego zabezpieczeń platformy Azure lub któregokolwiek z dostarczonych standardowych inicjatyw prawnych. Zalecenia, które są generowane na podstawie dowolnych niestandardowych inicjatyw przypisanych do subskrypcji, nie mogą być wykluczone. Dowiedz się więcej na temat relacji między [zasadami, inicjatywami i zaleceniami](security-policy-concept.md).

> [!TIP]
> Możesz również utworzyć wykluczenia przy użyciu interfejsu API. Przykładowy kod JSON i objaśnienie odpowiednich struktur znajdują się w temacie [Azure Policy Structure](../governance/policy/concepts/exemption-structure.md).

Aby utworzyć regułę wykluczania:

1. Aby zapoznać się z konkretnym zaleceniem, Otwórz stronę szczegóły rekomendacji.

1. Na pasku narzędzi u góry strony wybierz pozycję **wykluczone**.

    :::image type="content" source="media/exempt-resource/exempting-recommendation.png" alt-text="Utwórz regułę wykluczania dla rekomendacji, która ma zostać wykluczona z subskrypcji lub grupy zarządzania.":::

1. W okienku **zwolniony** :
    1. Wybierz zakres dla tej reguły wykluczania:
        - W przypadku wybrania grupy zarządzania zalecenie zostanie wykluczone ze wszystkich subskrypcji w ramach tej grupy
        - Jeśli tworzysz tę regułę, aby wykluczyć co najmniej jeden zasób z rekomendacji, wybierz pozycję "Wybrane zasoby" i wybierz z listy odpowiednie pozycje.

    1. Wprowadź nazwę dla tej reguły wykluczania.
    1. Opcjonalnie Ustaw datę wygaśnięcia.
    1. Wybierz kategorię wykluczenia:
        - **Rozwiązane przez inną firmę (skorygowany)** — w przypadku korzystania z usługi innej firmy, która nie została zidentyfikowana Security Center. 

            > [!NOTE]
            > Jeśli wykluczasz zalecenie jako skorygowane, nie otrzymujesz punktów na swój Bezpieczny wynik. Jednak ponieważ punkty nie są *usuwane* dla zasobów w złej kondycji, wynikiem będzie wzrost wyniku.

        - **Ryzyko zaakceptowane (odstąpienie)** — Jeśli zdecydujesz się zaakceptować ryzyko nieograniczenia tego zalecenia
    1. Opcjonalnie wprowadź opis.
    1. Wybierz przycisk **Utwórz**.

    :::image type="content" source="media/exempt-resource/defining-recommendation-exemption.png" alt-text="Procedura tworzenia reguły wykluczania w celu zwolnienia zalecenia z subskrypcji lub grupy zarządzania":::

    Gdy wykluczenie zacznie obowiązywać (może to potrwać do 30 minut):
    - Zalecenie lub zasoby nie wpłyną na Twój bezpieczny wynik.
    - Jeśli wykluczono określone zasoby, zostaną one wyświetlone na karcie **nie dotyczy** strony Szczegóły rekomendacji.
    - Jeśli wyłączono rekomendację, będzie ona domyślnie ukryta na stronie zalecenia dotyczące Security Center. Wynika to z faktu, że domyślne opcje filtru **stanu zalecenia** na tej stronie mają zostać **wykluczone** . Ta sama wartość obowiązuje w przypadku wyłączenia wszystkich zaleceń w kontroli zabezpieczeń.

        :::image type="content" source="media/exempt-resource/recommendations-filters-hiding-not-applicable.png" alt-text="Domyślne filtry na stronie zalecenia dotyczące Azure Security Center Ukryj nieodpowiednie rekomendacje i kontrolki zabezpieczeń":::

    - Pasek informacji w górnej części strony Szczegóły zalecenia aktualizuje liczbę wykluczonych zasobów:
        
        :::image type="content" source="./media/exempt-resource/info-banner.png" alt-text="Liczba wykluczonych zasobów":::

1. Aby przejrzeć wykluczone zasoby, Otwórz kartę **nie dotyczy** :

    :::image type="content" source="./media/exempt-resource/modifying-exemption.png" alt-text="Modyfikowanie wykluczenia":::

    Przyczyną każdego wykluczenia jest dołączenie do tabeli (1).

    Aby zmodyfikować lub usunąć wykluczenie, wybierz menu wielokropka ("..."), jak pokazano (2).

1. Aby przejrzeć wszystkie reguły wykluczania w subskrypcji, wybierz pozycję **Wyświetl wykluczenia** z paska informacji:

    > [!IMPORTANT]
    > Aby wyświetlić określone zwolnienia dotyczące jednego zalecenia, należy odfiltrować listę zgodnie z odpowiednim zakresem i nazwą zalecenia.

    :::image type="content" source="./media/exempt-resource/policy-page-exemption.png" alt-text="Strona wykluczenia Azure Policy":::

    > [!TIP]
    > Możesz również [użyć grafu zasobów platformy Azure, aby znaleźć zalecenia dotyczące wykluczeń](#find-recommendations-with-exemptions-using-azure-resource-graph).

## <a name="monitor-exemptions-created-in-your-subscriptions"></a>Monitoruj wykluczenia utworzone w Twoich subskrypcjach

Jak wyjaśniono wcześniej na tej stronie, reguły wykluczania to zaawansowane narzędzie zapewniające szczegółową kontrolę nad zaleceń, które mają wpływ na zasoby w ramach subskrypcji i grup zarządzania. 

Aby śledzić, jak użytkownicy korzystają z tej możliwości, utworzyliśmy szablon Azure Resource Manager (ARM), który wdraża aplikację logiki element playbook i wszystkie wymagane połączenia interfejsu API w celu powiadomienia użytkownika o utworzeniu wykluczenia.

- Aby dowiedzieć się więcej na temat element PlayBook, zobacz wpis w blogu społeczność Tech. [jak śledzić wykluczenia zasobów w Azure Security Center](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-keep-track-of-resource-exemptions-in-azure-security/ba-p/1770580)
- Szablon ARM znajdziesz w [repozytorium Azure Security Center GitHub](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation/Notify-ResourceExemption)
- Aby wdrożyć wszystkie niezbędne składniki, [Użyj tego zautomatyzowanego procesu](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Security-Center%2Fmaster%2FWorkflow%2520automation%2FNotify-ResourceExemption%2Fazuredeploy.json)


## <a name="find-recommendations-with-exemptions-using-azure-resource-graph"></a>Znajdź zalecenia z wykluczeniami przy użyciu grafu zasobów platformy Azure

Usługa Azure Resource Graph (ARG) zapewnia błyskawiczny dostęp do informacji o zasobach w środowiskach chmury z niezawodnymi możliwościami filtrowania, grupowania i sortowania. Jest to szybki i wydajny sposób wykonywania zapytań dotyczących informacji w ramach subskrypcji platformy Azure programowo lub z poziomu Azure Portal.

Aby wyświetlić wszystkie zalecenia, które mają reguły wykluczania:

1. Otwórz **Eksploratora Azure Resource Graph**.

    :::image type="content" source="./media/security-center-identity-access/opening-resource-graph-explorer.png" alt-text="Uruchamianie Eksploratora Azure Resource Graph * * zalecenie" :::

1. Wprowadź następujące zapytanie i wybierz polecenie **Uruchom zapytanie**.

    ```kusto
    securityresources
    | where type == "microsoft.security/assessments"
    // Get recommendations in useful format
    | project
    ['TenantID'] = tenantId,
    ['SubscriptionID'] = subscriptionId,
    ['AssessmentID'] = name,
    ['DisplayName'] = properties.displayName,
    ['ResourceType'] = tolower(split(properties.resourceDetails.Id,"/").[7]),
    ['ResourceName'] = tolower(split(properties.resourceDetails.Id,"/").[8]),
    ['ResourceGroup'] = resourceGroup,
    ['ContainsNestedRecom'] = tostring(properties.additionalData.subAssessmentsLink),
    ['StatusCode'] = properties.status.code,
    ['StatusDescription'] = properties.status.description,
    ['PolicyDefID'] = properties.metadata.policyDefinitionId,
    ['Description'] = properties.metadata.description,
    ['RecomType'] = properties.metadata.assessmentType,
    ['Remediation'] = properties.metadata.remediationDescription,
    ['Severity'] = properties.metadata.severity,
    ['Link'] = properties.links.azurePortal
    | where StatusDescription contains "Exempt"    
    ```


Dowiedz się więcej na następujących stronach:
- [Dowiedz się więcej o usłudze Azure Resource Graph](../governance/resource-graph/index.yml).
- [Jak tworzyć zapytania za pomocą eksploratora usługi Azure Resource Graph](../governance/resource-graph/first-query-portal.md)
- [Kusto Query Language (KQL)](/azure/data-explorer/kusto/query/)





## <a name="faq---exemption-rules"></a>Często zadawane pytania — reguły wykluczania

### <a name="what-happens-when-one-recommendation-is-in-multiple-policy-initiatives"></a>Co się stanie, gdy jedno zalecenie dotyczy wielu inicjatyw dotyczących zasad?

Czasami zalecenie dotyczące zabezpieczeń występuje w więcej niż jednej inicjatywy zasad. Jeśli masz wiele wystąpień tego samego zalecenia przypisanego do tej samej subskrypcji i utworzysz wykluczenie dla tego zalecenia, wpłynie to na wszystkie inicjatywy, do których masz uprawnienia do edycji. 

Na przykład zalecenie * * * * jest częścią domyślnej inicjatywy zasad przypisanej do wszystkich subskrypcji platformy Azure przez Azure Security Center. Również w XXXXX.

Jeśli spróbujesz utworzyć wykluczenie dla tego zalecenia, zobaczysz jeden z dwóch następujących komunikatów:

- Jeśli masz odpowiednie uprawnienia do edytowania obu inicjatyw, zobaczysz:

    *To zalecenie jest zawarte w kilku inicjatywach zasad: [nazwy inicjatyw oddzielone przecinkami]. Na wszystkich z nich zostaną utworzone zwolnienia.*  

- Jeśli nie masz wystarczających uprawnień do obu inicjatyw, zobaczysz następujący komunikat:

    *Masz ograniczone uprawnienia do zastosowania wykluczenia we wszystkich inicjatywach zasad, a zwolnienia zostaną utworzone tylko w inicjatywach mających wystarczające uprawnienia.*


## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób wykluczenia zasobu z rekomendacji, dzięki czemu nie ma to wpływu na swój Bezpieczny wynik. Aby uzyskać więcej informacji na temat bezpiecznego oceny, zobacz:

- [Wskaźnik bezpieczeństwa w usłudze Azure Security Center](secure-score-security-controls.md)