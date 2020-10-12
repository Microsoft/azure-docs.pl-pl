---
title: Eksport ciągły może wysyłać alerty Azure Security Center i zalecenia dotyczące Log Analytics obszarów roboczych lub platformy Azure Event Hubs
description: Dowiedz się, jak skonfigurować ciągły eksport alertów zabezpieczeń i zaleceń dotyczących Log Analytics obszarów roboczych lub platformy Azure Event Hubs
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 10/06/2020
ms.author: memildin
ms.openlocfilehash: 8b27c3d0982e945fcabc6e7748646ea2ee1a4184
ms.sourcegitcommit: ba7fafe5b3f84b053ecbeeddfb0d3ff07e509e40
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/12/2020
ms.locfileid: "91945297"
---
# <a name="continuously-export-security-alerts-and-recommendations"></a>Nieustanne eksportowanie alertów zabezpieczeń i zaleceń

Azure Security Center generuje szczegółowe alerty zabezpieczeń i zalecenia. Można je wyświetlać w portalu lub za pomocą narzędzi programistycznych. Może być również konieczne wyeksportowanie niektórych lub wszystkich tych informacji do śledzenia za pomocą innych narzędzi do monitorowania w danym środowisku. 

**Eksport ciągły** pozwala w pełni dostosować *zawartość, która* zostanie wyeksportowana, oraz miejsce, w *której* zostanie ono przeszukane. Można na przykład skonfigurować go tak, aby:

- Wszystkie alerty o wysokiej ważności są wysyłane do centrum zdarzeń platformy Azure
- Wszystkie informacje o średnim lub wyższym znaczeniu dotyczące skanowania w celu oceny luk w zabezpieczeniach serwerów SQL są wysyłane do określonego obszaru roboczego Log Analytics
- Konkretne zalecenia są dostarczane do centrum zdarzeń lub Log Analytics obszaru roboczego, gdy są generowane 

W tym artykule opisano sposób konfigurowania eksportu ciągłego do Log Analytics obszarów roboczych lub Event Hubs platformy Azure.

> [!NOTE]
> Jeśli musisz zintegrować Security Center z SIEM, przejrzyj [alerty strumienia w Siem](export-to-siem.md) dla opcji.

> [!TIP]
> Security Center oferuje także opcję jednorazowego, ręcznego eksportowania do woluminu CSV. Dowiedz się więcej w [ręcznym wywozie alertów i zaleceń](#manual-one-time-export-of-alerts-and-recommendations).


## <a name="availability"></a>Dostępność

|Aspekt|Szczegóły|
|----|:----|
|Stan wydania:|Ogólnie dostępna (GA)|
|Wpisaną|Bezpłatna|
|Wymagane role i uprawnienia:|<ul><li>**Administrator zabezpieczeń** lub **właściciel** w grupie zasobów</li><li>Uprawnienia do zapisu dla zasobu docelowego</li><li>Jeśli używasz zasad "DeployIfNotExist" Azure Policy "opisanych poniżej, będziesz mieć również uprawnienia do przypisywania zasad</li></ul>|
|Połączeń|![Tak](./media/icons/yes-icon.png) Chmury komercyjne<br>![Tak](./media/icons/yes-icon.png) US Gov<br>![Tak](./media/icons/yes-icon.png) Chiny gov (do centrum zdarzeń), inne gov|
|||





## <a name="set-up-a-continuous-export"></a>Konfigurowanie eksportu ciągłego 

Można skonfigurować eksport ciągły ze stron Security Center w Azure Portal, za pośrednictwem interfejsu API REST Security Center lub na skalę przy użyciu dostarczonych szablonów Azure Policy. Wybierz odpowiednią kartę poniżej, aby uzyskać szczegółowe informacje o każdej z nich.

### <a name="use-the-azure-portal"></a>[**Korzystanie z witryny Azure Portal**](#tab/azure-portal)

### <a name="configure-continuous-export-from-the-security-center-pages-in-azure-portal"></a>Skonfiguruj eksport ciągły na stronach Security Center w Azure Portal

Poniższe kroki są niezbędne, niezależnie od tego, czy konfigurujesz ciągły eksport do Log Analytics obszaru roboczego czy Event Hubs platformy Azure.

1. Na pasku bocznym Security Center wybierz pozycję **cennik & ustawienia**.
1. Wybierz określoną subskrypcję, dla której chcesz skonfigurować eksportowanie danych.
1. Na pasku bocznym strony Ustawienia dla tej subskrypcji wybierz pozycję **eksport ciągły**.
    [ ![ Opcje eksportowania w Azure Security Center](media/continuous-export/continuous-export-options-page.png)](media/continuous-export/continuous-export-options-page.png#lightbox) tym miejscu są wyświetlane opcje eksportowania. Dla każdego dostępnego elementu docelowego eksportu istnieje karta. 
1. Wybierz typ danych, który chcesz wyeksportować, i wybierz spośród filtrów dla każdego typu (na przykład wyeksportuj tylko alerty o wysokiej ważności).
1. Opcjonalnie, jeśli wybór obejmuje jedno z czterech zaleceń, można dołączyć do nich wyniki oceny luk w zabezpieczeniach:
    - Oceny luk w zabezpieczeniach baz danych SQL należy skorygować
    - Oceny luk w zabezpieczeniach na serwerach SQL na maszynach należy skorygować (wersja zapoznawcza)
    - Luki w zabezpieczeniach Azure Container Registry obrazów powinny być skorygowane (obsługiwane przez Qualys)
    - Luki w zabezpieczeniach maszyn wirtualnych należy skorygować

    Aby uwzględnić ustalenia z tymi zaleceniami, Włącz opcję **Uwzględnij wnioski dotyczące zabezpieczeń** .

    :::image type="content" source="./media/continuous-export/include-security-findings-toggle.png" alt-text="Uwzględnij w konfiguracji eksportu ciągłego funkcję przełączania zabezpieczeń&quot; :::

1. W obszarze &quot;Eksportuj element docelowy" Wybierz miejsce, w którym chcesz zapisać dane. Dane można zapisywać w miejscu docelowym w innej subskrypcji (na przykład w centralnym wystąpieniu centrum zdarzeń lub w centralnym obszarze roboczym Log Analytics).
1. Wybierz pozycję **Zapisz**.

### <a name="use-the-rest-api"></a>[**Korzystanie z interfejsu API REST**](#tab/rest-api)

### <a name="configure-continuous-export-using-the-rest-api"></a>Konfigurowanie eksportu ciągłego przy użyciu interfejsu API REST

Eksport ciągły można skonfigurować i zarządzać nim za pośrednictwem [interfejsu API Azure Security Center automations](https://docs.microsoft.com/rest/api/securitycenter/automations). Użyj tego interfejsu API, aby utworzyć lub zaktualizować reguły eksportowania do dowolnego z następujących możliwych miejsc docelowych:

- Centrum zdarzeń Azure
- Obszar roboczy usługi Log Analytics
- Azure Logic Apps 

Interfejs API zapewnia dodatkowe funkcje niedostępne w Azure Portal, na przykład:

* **Większy wolumin** — interfejs API umożliwia tworzenie wielu konfiguracji eksportu w ramach jednej subskrypcji. Strona **eksportu ciągłego** w interfejsie użytkownika portalu Security Center obsługuje tylko jedną konfigurację eksportu na subskrypcję.

* **Dodatkowe funkcje** — interfejs API oferuje dodatkowe parametry, które nie są wyświetlane w interfejsie użytkownika. Na przykład możesz dodać tagi do zasobu usługi Automation, a także zdefiniować eksport na podstawie szerszego zestawu właściwości alertu i rekomendacji niż te oferowane na stronie **eksportu ciągłego** w interfejsie użytkownika portalu Security Center.

* **Bardziej ukierunkowany zakres** — interfejs API zapewnia bardziej szczegółowy poziom konfiguracji eksportu. Podczas definiowania eksportu za pomocą interfejsu API można to zrobić na poziomie grupy zasobów. Jeśli używasz strony **eksportu ciągłego** w interfejsie użytkownika portalu Security Center, musisz ją zdefiniować na poziomie subskrypcji.

    > [!TIP]
    > Jeśli skonfigurowano wiele konfiguracji eksportu przy użyciu interfejsu API lub jeśli użyto parametrów tylko do API, te dodatkowe funkcje nie będą wyświetlane w interfejsie użytkownika Security Center. Zamiast tego zostanie wyświetlony transparent informujący o tym, że istnieją inne konfiguracje.

Więcej informacji na temat interfejsu API usługi automations znajduje się w [dokumentacji interfejsu API REST](https://docs.microsoft.com/rest/api/securitycenter/automations).





### <a name="deploy-at-scale-with-azure-policy"></a>[**Wdróż na dużą skalę za pomocą Azure Policy**](#tab/azure-policy)

### <a name="configure-continuous-export-at-scale-using-the-supplied-policies"></a>Konfigurowanie eksportu ciągłego na dużą skalę przy użyciu podanych zasad

Automatyzacja procesów monitorowania i reagowania na zdarzenia w organizacji może znacznie poprawić czas potrzebny do zbadania i ograniczenia zdarzeń związanych z bezpieczeństwem.

Aby wdrożyć konfiguracje eksportu ciągłego w całej organizacji, należy użyć podanych Azure Policy zasad "DeployIfNotExist" opisanych poniżej, aby utworzyć i skonfigurować procedury eksportu ciągłego.

**Aby zaimplementować te zasady**

1. W poniższej tabeli wybierz zasady, które chcesz zastosować:

    |Cel  |Zasady  |Identyfikator zasad  |
    |---------|---------|---------|
    |Eksport ciągły do centrum zdarzeń|[Wdróż eksportowanie alertów i zaleceń usługi Azure Security Center do centrum zdarzeń](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fcdfcce10-4578-4ecd-9703-530938e4abcb)|cdfcce10-4578-4ecd-9703-530938e4abcb|
    |Eksport ciągły do obszaru roboczego Log Analytics|[Wdróż eksport do obszaru roboczego usługi Log Analytics w celu uzyskania alertów i zaleceń usługi Azure Security Center](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fffb6f416-7bd2-4488-8828-56585fef2be9)|ffb6f416-7bd2-4488-8828-56585fef2be9|
    ||||

    > [!TIP]
    > Można je również znaleźć, wyszukując Azure Policy:
    > 1. Otwórz Azure Policy.
    > :::image type="content" source="./media/continuous-export/opening-azure-policy.png" alt-text="Uwzględnij w konfiguracji eksportu ciągłego funkcję przełączania zabezpieczeń&quot; :::

1. W obszarze &quot;Eksportuj element docelowy":::
    > 2. Z menu Azure Policy wybierz pozycję **definicje** i wyszukaj je według nazwy. 

1. Na odpowiedniej stronie Azure Policy wybierz pozycję **Przypisz**.
    :::image type="content" source="./media/continuous-export/export-policy-assign.png" alt-text="Uwzględnij w konfiguracji eksportu ciągłego funkcję przełączania zabezpieczeń&quot; :::

1. W obszarze &quot;Eksportuj element docelowy":::

1. Otwórz każdą kartę i ustaw parametry zgodnie z potrzebami:
    1. Na karcie **podstawy** Ustaw zakres dla zasad. Aby użyć scentralizowanego zarządzania, przypisz zasady do grupy zarządzania zawierającej subskrypcje, które będą używać konfiguracji eksportu ciągłego. 
    1. Na karcie **Parametry** Ustaw szczegóły grupy zasobów i typu danych. 
        > [!TIP]
        > Każdy parametr zawiera etykietkę narzędzia opisującą dostępne opcje.
        >
        > Karta parametry Azure Policy (1) zapewnia dostęp do podobnych opcji konfiguracji jako strony eksportu ciągłego Security Center (2).
        > :::image type="content" source="./media/continuous-export/azure-policy-next-to-continuous-export.png" alt-text="Uwzględnij w konfiguracji eksportu ciągłego funkcję przełączania zabezpieczeń&quot; :::

1. W obszarze &quot;Eksportuj element docelowy" lightbox="./media/continuous-export/azure-policy-next-to-continuous-export.png":::
    1. Opcjonalnie, aby zastosować to przypisanie do istniejących subskrypcji, Otwórz kartę **korygowanie** i wybierz opcję utworzenia zadania korygowania.
1. Przejrzyj stronę Podsumowanie i wybierz pozycję **Utwórz**.

--- 

## <a name="information-about-exporting-to-a-log-analytics-workspace"></a>Informacje na temat eksportowania do obszaru roboczego Log Analytics

Jeśli chcesz analizować Azure Security Center dane w obszarze roboczym Log Analytics lub użyć alertów platformy Azure razem z alertami Security Center, skonfiguruj eksport ciągły do obszaru roboczego Log Analytics.

### <a name="log-analytics-tables-and-schemas"></a>Log Analytics tabele i schematy

Alerty zabezpieczeń i zalecenia są przechowywane odpowiednio w tabelach *SecurityAlert* i *SecurityRecommendations* . 

Nazwa rozwiązania Log Analytics zawierającego te tabele zależy od tego, czy usługa Azure Defender jest włączona: zabezpieczenia ("Security and Audit") czy SecurityCenterFree. 

> [!TIP]
> Aby wyświetlić dane w docelowym obszarze roboczym, należy włączyć jedno z tych rozwiązań **Security and Audit** lub **SecurityCenterFree**.

![Tabela * SecurityAlert * w Log Analytics](./media/continuous-export/log-analytics-securityalert-solution.png)

Aby wyświetlić schematy zdarzeń wyeksportowanych typów danych, odwiedź [log Analytics schematy tabel](https://aka.ms/ASCAutomationSchemas).


##  <a name="view-exported-alerts-and-recommendations-in-azure-monitor"></a>Wyświetlanie wyeksportowanych alertów i zaleceń w Azure Monitor

W niektórych przypadkach można wyświetlić wyeksportowane alerty zabezpieczeń i/lub zalecenia w [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview). 

Azure Monitor zapewnia ujednolicone środowisko alertów dla różnych alertów platformy Azure, w tym dzienników diagnostycznych, alertów metryk i alertów niestandardowych opartych na zapytaniach Log Analytics obszaru roboczego.

Aby wyświetlić alerty i zalecenia z Security Center w Azure Monitor, skonfiguruj regułę alertu na podstawie zapytań Log Analytics (alert dziennika):

1. Na stronie **alerty** Azure Monitor wybierz pozycję **Nowa reguła alertu**.

    ![Strona alertów Azure Monitor](./media/continuous-export/azure-monitor-alerts.png)

1. Na stronie Tworzenie reguły Skonfiguruj nową regułę (w taki sam sposób jak w przypadku konfigurowania [reguły alertu dziennika w Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log)):

    * W obszarze **zasób**wybierz obszar roboczy log Analytics, do którego wyeksportowano alerty zabezpieczeń i zalecenia.

    * W obszarze **warunek**wybierz opcję **Wyszukiwanie w dzienniku niestandardowym**. Na wyświetlonej stronie Skonfiguruj zapytanie, okres lookback i okres częstotliwości. W zapytaniu wyszukiwania można wpisać *SecurityAlert* lub *SecurityRecommendation* , aby wykonać zapytanie dotyczące typów danych, które Security Center ciągle eksportować w miarę włączania eksportu ciągłego do log Analytics funkcji. 
    
    * Opcjonalnie Skonfiguruj [grupę akcji](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) , którą chcesz wyzwolić. Grupy akcji mogą wyzwalać wysyłanie wiadomości e-mail, bilety narzędzia ITSM, elementy webhook i inne elementy.
    ![Reguła alertu Azure Monitor](./media/continuous-export/azure-monitor-alert-rule.png)

Zobaczysz teraz nowe alerty i zalecenia dotyczące Azure Security Center (w zależności od skonfigurowanych reguł eksportu ciągłego i warunku zdefiniowanego w regule alertu Azure Monitor) w Azure Monitor alertach z automatycznym wyzwalaniem grupy akcji (jeśli została podana).

## <a name="manual-one-time-export-of-alerts-and-recommendations"></a>Ręczne, jednorazowe eksportowanie alertów i zaleceń

Aby pobrać raport CSV dotyczący alertów lub zaleceń, Otwórz stronę **alerty zabezpieczeń** lub **zalecenia** i wybierz przycisk **Pobierz raport CSV** .

[![Pobierz dane alertów jako plik CSV](media/continuous-export/download-alerts-csv.png)](media/continuous-export/download-alerts-csv.png#lightbox)

> [!NOTE]
> Te raporty zawierają alerty i zalecenia dotyczące zasobów z aktualnie wybranych subskrypcji.


## <a name="faq---continuous-export"></a>Często zadawane pytania — eksport ciągły

### <a name="what-are-the-costs-involved-in-exporting-data"></a>Jakie są koszty związane z eksportowaniem danych?

Za włączenie eksportu ciągłego nie są naliczane opłaty. Mogą być naliczane koszty pozyskiwania i przechowywania danych w obszarze roboczym Log Analytics, w zależności od konfiguracji. 

Dowiedz się więcej o [cenniku obszaru roboczego log Analytics](https://azure.microsoft.com/pricing/details/monitor/).

Dowiedz się więcej na temat [cennika usługi Azure Event Hub](https://azure.microsoft.com/pricing/details/event-hubs/).




## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób konfigurowania ciągłego eksportowania zaleceń i alertów. Wiesz również, jak pobrać dane alertów jako plik CSV. 

W przypadku pokrewnego materiału zapoznaj się z następującą dokumentacją: 

- Dowiedz się więcej o [szablonach automatyzacji przepływu pracy](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation).
- [Dokumentacja usługi Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/)
- [Dokumentacja usługi Azure Sentinel](https://docs.microsoft.com/azure/sentinel/)
- [Dokumentacja usługi Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/)
- [Eksportuj schematy typów danych](https://aka.ms/ASCAutomationSchemas)
