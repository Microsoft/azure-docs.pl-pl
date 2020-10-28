---
title: Automatyzacja przepływu pracy w Azure Security Center | Microsoft Docs
description: Dowiedz się, jak tworzyć i automatyzować przepływy pracy w Azure Security Center
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 10/27/2020
ms.author: memildin
ms.openlocfilehash: cbe6adc37025f0b727fa5f5d3ace46ed34afaa9e
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92895226"
---
# <a name="automate-responses-to-security-center-triggers"></a>Automatyzowanie odpowiedzi na wyzwalacze Security Center

Każdy program zabezpieczeń zawiera wiele przepływów pracy dotyczących odpowiedzi na zdarzenia. Procesy te mogą obejmować Powiadamianie właściwych uczestników projektu, uruchamianie procesu zarządzania zmianami i stosowanie określonych czynności zaradczych. Specjaliści ds. zabezpieczeń zaleca się zautomatyzować dowolną liczbę kroków tych procedur. Automatyzacja redukuje obciążenie. Pozwala również zwiększyć bezpieczeństwo, zapewniając, że kroki procesu są wykonywane szybko, spójnie i zgodnie ze wstępnie zdefiniowanymi wymaganiami.

W tym artykule opisano funkcję automatyzacji przepływu pracy Azure Security Center. Ta funkcja może wyzwolić Logic Apps na temat alertów zabezpieczeń i zaleceń. Na przykład możesz chcieć, aby Security Center e-mail określonego użytkownika w przypadku wystąpienia alertu. Dowiesz się również, jak utworzyć Logic Apps przy użyciu [Azure Logic Apps](../logic-apps/logic-apps-overview.md).


## <a name="availability"></a>Dostępność

|Aspekt|Szczegóły|
|----|:----|
|Stan wydania:|Ogólnie dostępna (GA)|
|Wpisaną|Bezpłatna|
|Wymagane role i uprawnienia:|**Rola administratora zabezpieczeń** lub **właściciel** grupy zasobów<br>Musi mieć również uprawnienia do zapisu dla zasobu docelowego<br><br>Aby można było korzystać z przepływów pracy Azure Logic Apps, należy mieć również następujące role/uprawnienia Logic Apps:<br> - Wymagane są uprawnienia [operatora aplikacji logiki](../role-based-access-control/built-in-roles.md#logic-app-operator) lub dostęp do odczytu/wyzwalacza aplikacji logiki (Ta rola nie może tworzyć ani edytować aplikacji logiki; można *uruchamiać* tylko istniejące)<br> - Uprawnienia [współautora aplikacji logiki](../role-based-access-control/built-in-roles.md#logic-app-contributor) są wymagane do utworzenia i modyfikacji aplikacji logiki<br>Jeśli chcesz używać łączników aplikacji logiki, możesz potrzebować dodatkowych poświadczeń, aby zalogować się do odpowiednich usług (na przykład wystąpień programu Outlook/zespołów/zapasowych).|
|Połączeń|![Tak](./media/icons/yes-icon.png) Chmury komercyjne<br>![Tak](./media/icons/yes-icon.png) National/suwerenne (US Gov, Chiny gov, inne gov)|
|||



## <a name="create-a-logic-app-and-define-when-it-should-automatically-run"></a>Tworzenie aplikacji logiki i Definiowanie jej automatycznego uruchamiania 

1. Na pasku bocznym Security Center wybierz pozycję **Automatyzacja przepływu pracy** .

    :::image type="content" source="./media/workflow-automation/list-of-workflow-automations.png" alt-text="Lista automatyzacji przepływu pracy":::

    Na tej stronie można tworzyć nowe reguły automatyzacji, a także włączać, wyłączać lub usuwać istniejące.

1. Aby zdefiniować nowy przepływ pracy, kliknij przycisk **Dodaj automatyzację przepływu pracy** . 

    Zostanie wyświetlone okienko z opcjami nowej automatyzacji. W tym miejscu możesz wprowadzić:
    1. Nazwa i Opis automatyzacji.
    1. Wyzwalacze, które będą inicjować ten automatyczny przepływ pracy. Na przykład może być konieczne uruchomienie aplikacji logiki po wygenerowaniu alertu zabezpieczeń zawierającego "SQL".

        > [!NOTE]
        > Jeśli wyzwalacz jest zaleceniem zawierającym "podrekomendacje", na przykład **oceny luk w zabezpieczeniach baz danych SQL należy skorygować** , aplikacja logiki nie będzie wyzwalać dla każdego nowego odnajdowania zabezpieczeń; tylko wtedy, gdy stan zaleceń nadrzędnych ulega zmianie.

    1. Aplikacja logiki, która będzie uruchamiana po spełnieniu warunków wyzwalacza. 

        :::image type="content" source="./media/workflow-automation/add-workflow.png" alt-text="Lista automatyzacji przepływu pracy":::

1. W sekcji Actions (akcje) kliknij pozycję **Utwórz nową** , aby rozpocząć proces tworzenia aplikacji logiki.

    Nastąpi przekierowanie do Azure Logic Apps.

    [![Tworzenie nowej aplikacji logiki](media/workflow-automation/logic-apps-create-new.png)](media/workflow-automation/logic-apps-create-new.png#lightbox)

1. Wprowadź nazwę, grupę zasobów i lokalizację, a następnie kliknij przycisk **Utwórz** .

1. W nowej aplikacji logiki można wybrać opcję z wbudowanych, wstępnie zdefiniowanych szablonów z kategorii zabezpieczenia. Można też zdefiniować niestandardowy przepływ zdarzeń, które mają być wykonywane w momencie wyzwolenia tego procesu.

    > [!TIP]
    > Czasami w aplikacji logiki parametry są uwzględniane w łączniku jako część ciągu, a nie w własnym polu. Aby zapoznać się z przykładem EAN sposobu wyodrębniania parametrów, zobacz Krok #14 [pracy z parametrami aplikacji logiki podczas kompilowania Azure Security Center automatyzacji przepływu pracy](https://techcommunity.microsoft.com/t5/azure-security-center/working-with-logic-app-parameters-while-building-azure-security/ba-p/1342121).

    Projektant aplikacji logiki obsługuje te Security Center wyzwalacze:

    * **Po utworzeniu lub wyzwoleniu rekomendacji Azure Security Center** — Jeśli aplikacja logiki korzysta z rekomendacji, która jest przestarzała lub zastępowana, Automatyzacja przestanie działać i konieczne będzie zaktualizowanie wyzwalacza. Aby śledzić zmiany w zaleceniach, zobacz [Azure Security Center informacji o wersji](release-notes.md).

    * **Po utworzeniu lub wyzwoleniu alertu Azure Security Center** można dostosować wyzwalacz, aby odnosił się tylko do alertów z poziomami ważności, które Cię interesują.
    
    > [!NOTE]
    > Jeśli używasz starszego wyzwalacza "gdy zostanie wyzwolona odpowiedź na Azure Security Center alertu", Aplikacje logiki nie będą uruchamiane przez funkcję automatyzacji przepływu pracy. Zamiast tego należy użyć dowolnego wyzwalacza wymienionego powyżej. 

    [![Przykładowa aplikacja logiki](media/workflow-automation/sample-logic-app.png)](media/workflow-automation/sample-logic-app.png#lightbox)

1. Po zdefiniowaniu aplikacji logiki Wróć do okienka definicji automatyzacji przepływu pracy ("Dodaj automatyzację przepływu pracy"). Kliknij przycisk **Odśwież** , aby upewnić się, że nowa aplikacja logiki jest dostępna do wybrania.

    ![Odśwież](media/workflow-automation/refresh-the-list-of-logic-apps.png)

1. Wybierz aplikację logiki i Zapisz automatyzację. Należy pamiętać, że lista rozwijana aplikacji logiki zawiera tylko Logic Apps z obsługą łączników Security Center wymienionych powyżej.


## <a name="manually-trigger-a-logic-app"></a>Ręczne wyzwalanie aplikacji logiki

Logic Apps można również uruchomić ręcznie podczas wyświetlania alertów zabezpieczeń lub zaleceń.

Aby ręcznie uruchomić aplikację logiki, Otwórz alert lub zalecenie i kliknij pozycję **Wyzwalaj aplikację logiki** :

[![Ręczne wyzwalanie aplikacji logiki](media/workflow-automation/manually-trigger-logic-app.png)](media/workflow-automation/manually-trigger-logic-app.png#lightbox)


## <a name="configure-workflow-automation-at-scale-using-the-supplied-policies"></a>Konfigurowanie automatyzacji przepływu pracy na dużą skalę przy użyciu podanych zasad

Automatyzacja procesów monitorowania i reagowania na zdarzenia w organizacji może znacznie poprawić czas potrzebny do zbadania i ograniczenia zdarzeń związanych z bezpieczeństwem.

Aby wdrożyć konfiguracje automatyzacji w organizacji, użyj poAzure Policy danych poniżej zasad "DeployIfNotExist", aby utworzyć i skonfigurować procedury automatyzacji przepływu pracy.

Wprowadzenie do [szablonów automatyzacji przepływu pracy](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation).

Aby zaimplementować te zasady:

1. W poniższej tabeli wybierz zasady, które chcesz zastosować:

    |Cel  |Zasady  |Identyfikator zasad  |
    |---------|---------|---------|
    |Automatyzacja przepływu pracy dla alertów zabezpieczeń|[Wdróż automatyzację przepływu pracy dla alertów usługi Azure Security Center](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2ff1525828-9a90-4fcf-be48-268cdd02361e)|f1525828-9a90-4fcf-be48-268cdd02361e|
    |Automatyzacja przepływu pracy dla zaleceń dotyczących zabezpieczeń|[Wdróż automatyzację przepływów pracy dla zaleceń usługi Azure Security Center](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-2475-4850-afd6-43795f3492ef)|73d6ab6c-2475-4850-afd6-43795f3492ef|
    ||||

    > [!TIP]
    > Można je również znaleźć, wyszukując Azure Policy:
    > 1. Otwórz Azure Policy.
    > :::image type="content" source="./media/continuous-export/opening-azure-policy.png" alt-text="Lista automatyzacji przepływu pracy":::
    > 2. Z menu Azure Policy wybierz pozycję **definicje** i wyszukaj je według nazwy. 

1. Na odpowiedniej stronie Azure Policy wybierz pozycję **Przypisz** .
    :::image type="content" source="./media/workflow-automation/export-policy-assign.png" alt-text="Lista automatyzacji przepływu pracy":::

1. Otwórz każdą kartę i ustaw parametry zgodnie z potrzebami:
    1. Na karcie **podstawy** Ustaw zakres dla zasad. Aby użyć funkcji scentralizowanego zarządzania, przypisz zasady do grupy zarządzania zawierającej subskrypcje, które będą korzystać z konfiguracji automatyzacji przepływu pracy. 
    1. Na karcie **Parametry** Ustaw szczegóły grupy zasobów i typu danych. 
        > [!TIP]
        > Każdy parametr zawiera etykietkę narzędzia opisującą dostępne opcje.
        >
        > Karta parametry Azure Policy (1) zapewnia dostęp do podobnych opcji konfiguracji jako strony automatyzacji przepływu pracy Security Center (2).
        > :::image type="content" source="./media/workflow-automation/azure-policy-next-to-workflow-automation.png" alt-text="Lista automatyzacji przepływu pracy" lightbox="./media/workflow-automation/azure-policy-next-to-workflow-automation.png":::

    1. Opcjonalnie, aby zastosować to przypisanie do istniejących subskrypcji, Otwórz kartę **korygowanie** i wybierz opcję utworzenia zadania korygowania.

1. Przejrzyj stronę Podsumowanie i wybierz pozycję **Utwórz** .


## <a name="data-types-schemas"></a>Schematy typów danych

Aby wyświetlić nieprzetworzone schematy zdarzeń alertów zabezpieczeń lub zaleceń przesyłanych do wystąpienia aplikacji logiki, odwiedź [schematy typów danych automatyzacji przepływu pracy](https://aka.ms/ASCAutomationSchemas). Może to być przydatne w przypadkach, gdy nie Security Center korzystasz z wbudowanych łączników aplikacji logiki, które zostały wymienione powyżej, ale zamiast tego używamy ogólnego łącznika HTTP aplikacji logiki — możesz użyć schematu JSON zdarzenia, aby ręcznie przeanalizować ją zgodnie z oczekiwaniami.


## <a name="faq-for-workflow-automation"></a>Często zadawane pytania dotyczące automatyzacji przepływu pracy

### <a name="does-workflow-automation-support-any-business-continuity-or-disaster-recovery-bcdr-scenarios"></a>Czy Automatyzacja przepływu pracy obsługuje wszystkie scenariusze prowadzenia ciągłości działania lub odzyskiwania po awarii (BCDR)?

Podczas przygotowywania środowiska pod kątem scenariuszy BCDR, w którym zasób docelowy występuje awaria lub inna awaria, jest odpowiedzialnością organizacji, aby zapobiec utracie danych, ustanawiając kopie zapasowe zgodnie z wytycznymi z platformy Azure Event Hubs, Log Analytics obszaru roboczego i aplikacji logiki.

Dla każdej aktywnej usługi Automation zalecamy utworzenie identycznej (wyłączonej) automatyzacji i zapisanie jej w innej lokalizacji. Gdy wystąpi awaria, można włączyć te automatyzacje tworzenia kopii zapasowych i zachować normalne operacje.

Dowiedz się więcej [na temat ciągłości działania i odzyskiwania po awarii dla Azure Logic Apps](../logic-apps/business-continuity-disaster-recovery-guidance.md).

## <a name="next-steps"></a>Następne kroki

Ten artykuł zawiera informacje na temat tworzenia Logic Apps, automatyzowania ich wykonywania w Security Center i uruchamiania ich ręcznie. 

W przypadku pokrewnego materiału Zobacz: 

- [Moduł Microsoft Learn dotyczący używania automatyzacji przepływu pracy do automatyzowania odpowiedzi zabezpieczeń](/learn/modules/resolve-threats-with-azure-security-center/)
- [Zalecenia dotyczące zabezpieczeń w Centrum zabezpieczeń Azure](security-center-recommendations.md)
- [Alerty zabezpieczeń w Centrum zabezpieczeń Azure](security-center-alerts-overview.md)
- [Azure Logic Apps — informacje](../logic-apps/logic-apps-overview.md)
- [Łączniki dla usługi Azure Logic Apps](../connectors/apis-list.md)
- [Schematy typów danych automatyzacji przepływu pracy](https://aka.ms/ASCAutomationSchemas)