---
title: Wyklucz Azure Security Center z zasobu, subskrypcji, grupy zarządzania i oceny bezpieczeństwa
description: Dowiedz się, jak tworzyć reguły wykluczające zalecenia dotyczące zabezpieczeń z subskrypcji lub grup zarządzania i zapobiegające wpływowi na ocenę bezpieczeństwa
author: memildin
ms.author: memildin
ms.date: 03/11/2021
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 13abb35d0fa9ad3ee949b6edf5205de601a02956
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107718561"
---
# <a name="exempting-resources-and-recommendations-from-your-secure-score"></a>Zwalnianie zasobów i zaleceń z oceny bezpieczeństwa 

Podstawowym priorytetem każdego zespołu ds. zabezpieczeń jest zapewnienie analitykom skoncentrowania się na zadaniach i zdarzeniach, które mają znaczenie dla organizacji. Security Center wiele funkcji dostosowywania obsługi i upewnienia się, że twój poziom bezpieczeństwa odzwierciedla priorytety zabezpieczeń organizacji. Jedną **z takich** funkcji jest opcja wykluczona.

Podczas badania zaleceń dotyczących zabezpieczeń w Azure Security Center, jedną z pierwszych przeglądanych informacji jest lista zasobów, których dotyczy problem.

Od czasu do czasu zostanie wyświetlone źródło informacji o zasobie, który nie powinien zostać uwzględniony. Lub zalecenie będzie wyświetlane w zakresie, w którym uważasz, że nie należy. Zasób mógł zostać skorygowany przez proces, który nie jest śledzony przez Security Center. Zalecenie może być nieodpowiednie dla określonej subskrypcji. Być może Twoja organizacja po prostu zdecydowała się zaakceptować ryzyko związane z określonym zasobem lub zaleceniem.

W takich przypadkach można utworzyć wyjątek dla zalecenia, aby:

- **Wyklucz zasób,** aby upewnić się, że w przyszłości nie będzie on wymieniony z zasobami w złej kondycji, i nie będzie mieć wpływu na ocenę bezpieczeństwa. Zasób zostanie wymieniony jako nie dotyczy, a przyczyna zostanie pokazana jako "wykluczona" z określonym uzasadnieniem, które wybierzesz.

- **Wyklucz subskrypcję** lub grupę zarządzania, aby upewnić się, że zalecenie nie ma wpływu na ocenę bezpieczeństwa i nie będzie wyświetlane dla subskrypcji lub grupy zarządzania w przyszłości. Jest to związane z istniejącymi i dowolnymi zasobami, które utworzysz w przyszłości. Zalecenie zostanie oznaczone konkretnym uzasadnieniem wybranym dla wybranego zakresu.

## <a name="availability"></a>Dostępność

| Aspekt                          | Szczegóły                                                                                                                                                                                                                                                                                                                            |
|---------------------------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Stan wydania:                  | Wersja zapoznawcza<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)]                                                                                                                                                                                                                                             |
| Ceny:                        | Jest to funkcja premium Azure Policy oferowana dla klientów Azure Defender bez dodatkowych kosztów. W przypadku innych użytkowników opłaty mogą być naliczane w przyszłości.                                                                                                                                                                 |
| Wymagane role i uprawnienia: | **Właściciel** lub **współautor zasad zasobów w** celu utworzenia zwolnienia<br>Aby utworzyć regułę, musisz mieć uprawnienia do edytowania zasad w Azure Policy.<br>Dowiedz się więcej na [temat uprawnień RBAC platformy Azure w Azure Policy](../governance/policy/overview.md#azure-rbac-permissions-in-azure-policy).                                            |
| Ograniczenia:                    | Wyjątki można tworzyć tylko w przypadku zaleceń zawartych Security Center domyślnej inicjatywy firmy Security Center, testu porównawczego zabezpieczeń platformy [Azure](https://docs.microsoft.com/security/benchmark/azure/introduction)lub dowolnej z dostarczonych inicjatyw dotyczących standardów prawnych. Rekomendacji generowanych na podstawie inicjatyw niestandardowych nie można wykluczyć. Dowiedz się więcej o relacjach między [zasadami, inicjatywami i zaleceniami.](security-policy-concept.md) |
| Chmury:                         | ![Tak](./media/icons/yes-icon.png) Chmury komercyjne<br>![Nie](./media/icons/no-icon.png) National/Sovereign (US Gov, China Gov, Other Gov)                                                                                                                                                                                         |
|                                 |                                                                                                                                                                                                                                                                                                                                    |

## <a name="define-an-exemption"></a>Definiowanie zwolnienia

Aby dostosować zalecenia dotyczące zabezpieczeń Security Center subskrypcji, grupy zarządzania lub zasobów, możesz utworzyć regułę wykluczania, aby:

- Oznacz określone zalecenie **lub** jako "skorygowane" lub "zaakceptowane ryzyko". Możesz utworzyć wyjątki zaleceń dla subskrypcji, wielu subskrypcji lub całej grupy zarządzania.
- Oznacz **co najmniej jeden za zasoby** jako "skorygowane" lub "zaakceptowane ryzyko" dla określonego zalecenia.

> [!NOTE]
> Wyjątki można tworzyć tylko w przypadku zaleceń zawartych w domyślnej inicjatywie firmy Security Center, testie porównawczym zabezpieczeń platformy Azure lub dowolnej z dostarczonych inicjatyw dotyczących standardów prawnych. Rekomendacji generowanych z dowolnych inicjatyw niestandardowych przypisanych do Subskrypcji nie można wykluczyć. Dowiedz się więcej o relacjach między [zasadami, inicjatywami i zaleceniami.](security-policy-concept.md)

> [!TIP]
> Możesz również utworzyć wyjątki przy użyciu interfejsu API. Aby uzyskać przykładowy dane JSON i wyjaśnienie odpowiednich struktur, zobacz [Azure Policy struktury wyieńcowej](../governance/policy/concepts/exemption-structure.md).

Aby utworzyć regułę wykluczania:

1. Otwórz stronę szczegółów zaleceń dla określonej rekomendacji.

1. Na pasku narzędzi w górnej części strony wybierz pozycję **Wyklucz**.

    :::image type="content" source="media/exempt-resource/exempting-recommendation.png" alt-text="Utwórz regułę wykluczania, aby rekomendacja została wykluczona z subskrypcji lub grupy zarządzania.":::

1. W **okienku Wyklucz:**
    1. Wybierz zakres dla tej reguły wykluczania:
        - Jeśli wybierzesz grupę zarządzania, zalecenie zostanie wykluczone ze wszystkich subskrypcji w tej grupie
        - Jeśli tworzysz tę regułę w celu wyłączenia co najmniej jednego zasobów z zalecenia, wybierz pozycję "Wybrane zasoby" i wybierz odpowiednie z listy

    1. Wprowadź nazwę reguły wykluczania.
    1. Opcjonalnie ustaw datę wygaśnięcia.
    1. Wybierz kategorię zwolnienia:
        - **Rozwiązane za pośrednictwem innych firm (skorygowane)** — jeśli używasz usługi innej firmy, która Security Center nie została zidentyfikowana. 

            > [!NOTE]
            > Gdy wyklucz zalecenie w wyniku ograniczenia, nie masz punktów do oceny bezpieczeństwa. Jednak ze względu na to, że punkty nie są *usuwane* w przypadku zasobów w złej kondycji, wynik jest taki, że twój wynik zwiększy się.

        - **Ryzyko zaakceptowane (zniechęceń)** — jeśli zdecydujesz się zaakceptować ryzyko nienadmiania tego zalecenia
    1. Opcjonalnie wprowadź opis.
    1. Wybierz przycisk **Utwórz**.

    :::image type="content" source="media/exempt-resource/defining-recommendation-exemption.png" alt-text="Kroki tworzenia reguły wykluczania w celu zwolnienia zalecenia z subskrypcji lub grupy zarządzania":::

    Gdy zwolnienie dzieje się tak (może to potrwać do 30 minut):
    - Zalecenie lub zasoby nie będą miały wpływu na ocenę bezpieczeństwa.
    - Jeśli określone zasoby zostały wykluczone, zostaną one wyświetlone na karcie **Nie** dotyczy na stronie szczegółów zalecenia.
    - Jeśli zalecenie zostało wykluczone, będzie ono domyślnie ukryte na Security Center rekomendacji. Wynika to z tego, że domyślne opcje filtru **Stan** rekomendacji na tej stronie mają wykluczać **zalecenia Nie dotyczy.** To samo dotyczy sytuacji, w których wszystkie zalecenia są wykluczone z kontroli zabezpieczeń.

        :::image type="content" source="media/exempt-resource/recommendations-filters-hiding-not-applicable.png" alt-text="Domyślne filtry na Azure Security Center zaleceń firmy ukrywają nie dotyczy zaleceń i mechanizmy kontroli zabezpieczeń":::

    - Pasek informacji w górnej części strony szczegółów zalecenia aktualizuje liczbę wykluczeń zasobów:
        
        :::image type="content" source="./media/exempt-resource/info-banner.png" alt-text="Liczba wykluczonych zasobów":::

1. Aby przejrzeć wykluczone zasoby, otwórz **kartę Nie dotyczy:**

    :::image type="content" source="./media/exempt-resource/modifying-exemption.png" alt-text="Modyfikowanie zwolnienia":::

    Przyczyna każdego zwolnienia znajduje się w tabeli (1).

    Aby zmodyfikować lub usunąć zwolnienie, wybierz menu wielokropka ("..."), jak pokazano (2).

1. Aby przejrzeć wszystkie reguły wydpowiedzialności w ramach subskrypcji, wybierz pozycję **Wyświetl** wyjątki na pasku informacji:

    > [!IMPORTANT]
    > Aby wyświetlić konkretne wyjątki dotyczące jednego zalecenia, przefiltruj listę zgodnie z odpowiednim zakresem i nazwą zalecenia.

    :::image type="content" source="./media/exempt-resource/policy-page-exemption.png" alt-text="Azure Policy strony wyzłania":::

    > [!TIP]
    > Alternatywnie możesz [użyć Azure Resource Graph, aby znaleźć rekomendacje z wyjątkami](#find-recommendations-with-exemptions-using-azure-resource-graph).

## <a name="monitor-exemptions-created-in-your-subscriptions"></a>Monitorowanie wyjątków utworzonych w subskrypcjach

Jak wyjaśniono wcześniej na tej stronie, reguły wywłaszania są wydajnym narzędziem zapewniającym szczegółową kontrolę nad zaleceniami wpływania na zasoby w subskrypcjach i grupach zarządzania. 

Aby śledzić sposób korzystania z tej możliwości przez użytkowników, utworzono szablon usługi Azure Resource Manager (ARM), który wdraża podręcznik aplikacji logiki i wszystkie niezbędne połączenia interfejsu API w celu powiadamiania o utworzeniu zwolnienia.

- Aby dowiedzieć się więcej na temat podręcznika, zobacz wpis w blogu społeczności technicznej [How to keep track of Resource Exemptions in Azure Security Center](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-keep-track-of-resource-exemptions-in-azure-security/ba-p/1770580)
- Szablon ARM znajduje się w [repozytorium Azure Security Center GitHub](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation/Notify-ResourceExemption)
- Aby wdrożyć wszystkie niezbędne składniki, [użyj tego zautomatyzowanego procesu](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Security-Center%2Fmaster%2FWorkflow%2520automation%2FNotify-ResourceExemption%2Fazuredeploy.json)


## <a name="find-recommendations-with-exemptions-using-azure-resource-graph"></a>Znajdowanie rekomendacji z wyjątkami przy użyciu Azure Resource Graph

Azure Resource Graph (ARG) zapewnia natychmiastowy dostęp do informacji o zasobach w środowiskach chmury dzięki niezawodnym możliwościom filtrowania, grupowania i sortowania. Jest to szybki i wydajny sposób wykonywania zapytań o informacje w subskrypcjach platformy Azure programowo lub z poziomu Azure Portal.

Aby wyświetlić wszystkie zalecenia, które mają reguły wydpowiedzialności:

1. Otwórz **Azure Resource Graph Explorer .**

    :::image type="content" source="./media/security-center-identity-access/opening-resource-graph-explorer.png" alt-text="Uruchamianie Azure Resource Graph rekomendacji eksploratora**" :::

1. Wprowadź następujące zapytanie i wybierz pozycję **Uruchom zapytanie.**

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
- [Dowiedz się więcej o Azure Resource Graph](../governance/resource-graph/index.yml).
- [Jak tworzyć zapytania za pomocą eksploratora usługi Azure Resource Graph](../governance/resource-graph/first-query-portal.md)
- [Kusto Query Language (KQL)](/azure/data-explorer/kusto/query/)





## <a name="faq---exemption-rules"></a>Często zadawane pytania — reguły wywłaszcze

### <a name="what-happens-when-one-recommendation-is-in-multiple-policy-initiatives"></a>Co się stanie, gdy jedno zalecenie znajduje się w wielu inicjatywach zasad?

Czasami zalecenie dotyczące zabezpieczeń pojawia się w więcej niż jednej inicjatywie zasad. Jeśli masz wiele wystąpień tego samego zalecenia przypisanych do tej samej subskrypcji i utworzysz wyjątek dla rekomendacji, będzie to miało wpływ na wszystkie inicjatywy, do których masz uprawnienia do edycji. 

Na przykład zalecenie *" jest częścią domyślnej inicjatywy zasad przypisanej do wszystkich subskrypcji platformy Azure przez Azure Security Center. Jest również w XXXXX.

Jeśli spróbujesz utworzyć wyjątek dla tego zalecenia, zobaczysz jeden z dwóch następujących komunikatów:

- Jeśli masz uprawnienia niezbędne do edytowania obu inicjatyw, zobaczysz:

    *To zalecenie znajduje się w kilku inicjatywach zasad: [nazwy inicjatyw rozdzielone przecinkami]. Wyjątki zostaną utworzone dla wszystkich z nich.*  

- Jeśli nie masz wystarczających uprawnień do obu inicjatyw, zamiast tego zobaczysz ten komunikat:

    *Masz ograniczone uprawnienia do stosowania zwolnienia do wszystkich inicjatyw zasad. Wyjątki zostaną utworzone tylko dla inicjatyw z wystarczającymi uprawnieniami.*


## <a name="next-steps"></a>Następne kroki

W tym artykule opisano sposób wykluczania zasobu z zalecenia, aby nie wpływał on na ocenę bezpieczeństwa. Aby uzyskać więcej informacji na temat oceny bezpieczeństwa, zobacz:

- [Wskaźnik bezpieczeństwa w usłudze Azure Security Center](secure-score-security-controls.md)
