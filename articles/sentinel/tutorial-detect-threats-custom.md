---
title: Tworzenie niestandardowych reguł analizy w celu wykrywania zagrożeń przy użyciu platformy Azure Microsoft Docs
description: Skorzystaj z tego samouczka, aby dowiedzieć się, jak tworzyć niestandardowe reguły analizy w celu wykrywania zagrożeń bezpieczeństwa przy użyciu platformy Azure. Korzystaj z zalet grupowania zdarzeń, grupowania alertów i wzbogacania alertów oraz informacje o wyłączaniu.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/10/2021
ms.author: yelevin
ms.openlocfilehash: 6f0a94daef8c5db820a17fe8cb50eda616bcf260
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/08/2021
ms.locfileid: "102453982"
---
# <a name="tutorial-create-custom-analytics-rules-to-detect-threats"></a>Samouczek: Tworzenie niestandardowych reguł analizy w celu wykrywania zagrożeń

Teraz, gdy [źródła danych zostały połączone](quickstart-onboard.md) z platformą Azure — wskaźnikiem, można utworzyć niestandardowe reguły analizy, które ułatwiają wykrywanie zagrożeń i nietypowych zachowań, które są obecne w danym środowisku. Te reguły szukają określonych zdarzeń lub zestawów zdarzeń w danym środowisku, ostrzegają o osiągnięciu określonych progów lub warunków zdarzeń, generują zdarzenia dla SOC do Klasyfikacja i badania zagrożeń oraz reagują na zagrożenia przy użyciu zautomatyzowanych procesów śledzenia i korygowania. 

Ten samouczek ułatwia tworzenie reguł niestandardowych w celu wykrywania zagrożeń przy użyciu platformy Azure.

Po ukończeniu tego samouczka będziesz w stanie wykonać następujące czynności:
> [!div class="checklist"]
> * Tworzenie reguł analizy
> * Definiowanie sposobu przetwarzania zdarzeń i alertów
> * Definiowanie sposobu generowania alertów i zdarzeń
> * Wybieranie zautomatyzowanych odpowiedzi na zagrożenia dla reguł

## <a name="create-a-custom-analytics-rule-with-a-scheduled-query"></a>Utwórz niestandardową regułę analizy z zaplanowanym zapytaniem

1. Z menu nawigacyjnego wskaźnikowego platformy Azure wybierz pozycję **Analiza**.

1. Na pasku akcji u góry wybierz pozycję **+ Utwórz** i wybierz pozycję **zaplanowana reguła zapytania**. Spowoduje to otwarcie **kreatora reguły analizy**.

    :::image type="content" source="media/tutorial-detect-threats-custom/create-scheduled-query-small.png" alt-text="Utwórz zaplanowane zapytanie" lightbox="media/tutorial-detect-threats-custom/create-scheduled-query-full.png":::

### <a name="analytics-rule-wizard---general-tab"></a>Kreator reguły analizy — karta Ogólne

- Podaj unikatową **nazwę** i **Opis**. 

- W polu **taktykę** można wybierać spośród kategorii ataków, według których ma zostać sklasyfikowana reguła. Są one oparte na taktykę platformy [Mitre ATT&](https://attack.mitre.org/) .

- Ustaw odpowiednio **ważność** alertu. 

- Po utworzeniu reguły jego **stan** jest domyślnie **włączony** , co oznacza, że zostanie uruchomiony natychmiast po zakończeniu tworzenia. Jeśli nie chcesz, aby był uruchamiany natychmiast, wybierz pozycję **wyłączone**, a reguła zostanie dodana do karty **aktywne reguły** i możesz ją włączyć z niej, gdy będzie potrzebna.

   :::image type="content" source="media/tutorial-detect-threats-custom/general-tab.png" alt-text="Rozpocznij tworzenie niestandardowej reguły analizy":::

## <a name="define-the-rule-query-logic-and-configure-settings"></a>Zdefiniuj logikę zapytania reguły i skonfiguruj ustawienia

Na karcie **Ustawianie logiki reguły** można napisać zapytanie bezpośrednio w polu **zapytania reguły** lub utworzyć zapytanie w log Analytics a następnie skopiować i wkleić je w tym miejscu.

- Zapytania są pisane w języku KQL (Kusto Query Language). Dowiedz się więcej o [pojęciach](/azure/data-explorer/kusto/concepts/) i [zapytaniach](/azure/data-explorer/kusto/query/)KQL, a następnie zobacz ten użyteczny [Przewodnik Szybki](/azure/data-explorer/kql-quick-reference).

- Przykład przedstawiony na tym zrzucie ekranu wysyła zapytanie do tabeli *SecurityEvent* w celu wyświetlenia typu [nieudanych zdarzeń logowania systemu Windows](/windows/security/threat-protection/auditing/event-4625).

   :::image type="content" source="media/tutorial-detect-threats-custom/set-rule-logic-tab-1-new.png" alt-text="Konfigurowanie logiki i ustawień reguły zapytania" lightbox="media/tutorial-detect-threats-custom/set-rule-logic-tab-all-1-new.png":::

- Oto inne przykładowe zapytanie, które będzie powiadamiać o utworzeniu nietypowej liczby zasobów w [działaniu platformy Azure](../azure-monitor/platform/activity-log.md).

    ```kusto
    AzureActivity
    | where OperationName == "Create or Update Virtual Machine" or OperationName =="Create Deployment"
    | where ActivityStatus == "Succeeded"
    | make-series dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(ago(7d), now(), 1d) by Caller
    ```

    > [!NOTE]
    > #### <a name="rule-query-best-practices"></a>Najlepsze rozwiązania dotyczące zapytania dotyczącego reguł
    > - Długość zapytania powinna wynosić od 1 do 10 000 znaków i nie może zawierać znaku " `search *` " ani " `union *` ".
    >
    > - Używanie funkcji ADX do tworzenia zapytań usługi Azure Eksplorator danych w ramach okna zapytania Log Analytics **nie jest obsługiwane**.
    >
    > - W przypadku korzystania z **`bag_unpack`** funkcji w zapytaniu, jeśli kolumny są uwzględniane jako pola przy użyciu " `project field1` ", a kolumna nie istnieje, zapytanie zakończy się niepowodzeniem. Aby zapewnić ochronę przed tą przyczyną, należy zaprojektować kolumnę w następujący sposób:
    >   - `project field1 = column_ifexists("field1","")`

### <a name="alert-enrichment"></a>Wzbogacanie alertów

> [!IMPORTANT]
> Funkcje wzbogacania alertów są obecnie dostępne w **wersji zapoznawczej**. Zapoznaj się z dodatkowymi [warunkami użytkowania Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) wersji zapoznawczych, aby uzyskać dodatkowe postanowienia prawne dotyczące funkcji systemu Azure, które są w wersji beta, Preview lub w inny sposób nie zostały jeszcze udostępnione publicznie.
    
- Sekcja konfiguracji **mapowania jednostek** służy do mapowania parametrów z wyników zapytania do jednostek rozpoznanych przez wskaźnik na platformie Azure. Podmioty wzbogacają reguły danych wyjściowych (alerty i zdarzenia) o podstawowe informacje służące jako bloki konstrukcyjne wszelkich procesów dochodzeniowych i czynności zaradczych, które są zgodne. Są one również kryteriami, za pomocą których można grupować alerty ze zdarzeniami na karcie **Ustawienia zdarzenia** .

    Dowiedz się więcej o [jednostkach na platformie Azure](entities-in-azure-sentinel.md).

    Zobacz sekcję [Mapowanie pól danych do jednostek na platformie Azure,](map-data-fields-to-entities.md) Aby uzyskać pełne instrukcje dotyczące mapowania jednostek oraz ważne informacje dotyczące [zgodności z poprzednimi wersjami](map-data-fields-to-entities.md#notes-on-the-new-version).

- Sekcja konfiguracji **szczegółów niestandardowych** służy do wyodrębniania elementów danych zdarzeń z zapytania i podawania ich w alertach generowanych przez tę regułę, dzięki czemu można natychmiast uwidocznić zawartość zdarzeń w alertach i zdarzeniach.

    Dowiedz się więcej o wyświetlaniu niestandardowych szczegółów w alertach i zapoznaj się z [kompletnymi instrukcjami](surface-custom-details-in-alerts.md).

### <a name="query-scheduling-and-alert-threshold"></a>Harmonogram zapytań i próg alertu

- W sekcji **planowanie zapytań** ustaw następujące parametry:

   :::image type="content" source="media/tutorial-detect-threats-custom/set-rule-logic-tab-2.png" alt-text="Ustawianie harmonogramu zapytań i grupowania zdarzeń" lightbox="media/tutorial-detect-threats-custom/set-rule-logic-tab-all-2-new.png":::

    - Ustaw **Uruchom zapytanie co** , aby kontrolować częstotliwość uruchamiania zapytania — tak często, jak co 5 minut, lub tak rzadko, jak co 14 dni.

    - Ustaw **dane wyszukiwania od czasu ostatniego** , aby określić okres danych uwzględnionych w zapytaniu — na przykład może wysyłać zapytania do ostatnich 10 minut danych lub ostatnich 6 godzin. Wartość maksymalna to 14 dni.

        > [!NOTE]
        > **Interwały zapytań i okres lookback**
        >
        >  Te dwa ustawienia są niezależne od siebie, aż do momentu. Zapytanie można uruchomić w krótkim odstępie czasu, przez okres dłuższy niż interwał (w efekcie mającym nakładające się zapytania), ale nie można uruchomić zapytania w przedziale czasowym, który przekracza okres pokrycia, w przeciwnym razie w ogólnym zapotrzebowaniu na zapytania będą występować przerwy.
        >
        > **Opóźnienie pozyskiwania**
        >
        > W celu uwzględnienia **opóźnień** , które mogą wystąpić między generowaniem zdarzenia w źródle i jego pozyskaniu na platformie Azure, a w celu zapewnienia pełnego pokrycia bez duplikowania danych, wskaźnik platformy Azure będzie uruchamiać zaplanowane reguły analizy w przypadku **opóźnienia** z zaplanowanym czasem.
        >
        > Aby uzyskać szczegółowe informacje techniczne na temat tego, jak to opóźnienie jest niezbędne i w jaki sposób rozwiązuje ten problem, zobacz Piotr Marsiano (doskonałe wpisy w blogu) na temat "[Obsługa opóźnień pozyskiwania w ramach zaplanowanych reguł alertów platformy Azure](https://techcommunity.microsoft.com/t5/azure-sentinel/handling-ingestion-delay-in-azure-sentinel-scheduled-alert-rules/ba-p/2052851)".

- Użyj sekcji **próg alertu** , aby zdefiniować poziom czułości reguły. Na przykład ustaw **Wygeneruj alert, gdy liczba wyników zapytania** **jest większa niż** i wprowadź liczbę 1000, jeśli chcesz, aby reguła wygenerowała alert tylko wtedy, gdy kwerenda zwróci więcej niż 1000 wyników przy każdym uruchomieniu. To pole jest wymagane, dlatego jeśli nie chcesz ustawić progu — to oznacza, że jeśli chcesz, aby alert rejestrował każde zdarzenie — wprowadź wartość 0 w polu Liczba.
    
### <a name="results-simulation"></a>Symulacja wyników

W obszarze **symulacja wyników** po prawej stronie kreatora wybierz pozycję **Testuj z bieżącymi danymi** , a na platformie Azure wskaźnikiem myszy zostanie wyświetlony wykres wyników (zdarzenia dziennika), które wygenerowało zapytanie w ciągu ostatnich 50 razy, zgodnie z aktualnie zdefiniowanym harmonogramem. Jeśli zmodyfikujesz zapytanie, wybierz ponownie polecenie **Testuj z bieżącymi danymi** , aby zaktualizować wykres. Wykres pokazuje liczbę wyników w określonym przedziale czasu, która jest określana przez ustawienia w sekcji **planowanie zapytań** .
  
Oto, co może wyglądać symulacja wyników dla zapytania na powyższym zrzucie ekranu. Po lewej stronie jest wyświetlany widok domyślny, a po prawej stronie jest wyświetlany po umieszczeniu wskaźnika myszy nad punktem w czasie na wykresie.

:::image type="content" source="media/tutorial-detect-threats-custom/results-simulation.png" alt-text="Zrzuty ekranu symulacji wyników":::

Jeśli zobaczysz, że zapytanie wyzwoli zbyt wiele lub zbyt częste alerty, można eksperymentować z ustawieniami w [sekcjach](#query-scheduling-and-alert-threshold) **planowanie zapytań** i **próg alertu** i ponownie wybrać opcję **Testuj z bieżącymi danymi** .

### <a name="event-grouping-and-rule-suppression"></a>Grupowanie zdarzeń i pomijanie reguł

> [!IMPORTANT]
> Grupowanie zdarzeń jest obecnie w **wersji zapoznawczej**. Zapoznaj się z dodatkowymi [warunkami użytkowania Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) wersji zapoznawczych, aby uzyskać dodatkowe postanowienia prawne dotyczące funkcji systemu Azure, które są w wersji beta, Preview lub w inny sposób nie zostały jeszcze udostępnione publicznie.
    
- W obszarze **grupowanie zdarzeń** wybierz jeden z dwóch sposobów obsługi grupowania **zdarzeń** w **alertach**: 

    - **Grupuj wszystkie zdarzenia w pojedynczym alercie** (ustawienie domyślne). Reguła generuje pojedynczy alert przy każdym uruchomieniu, pod warunkiem, że zapytanie zwróci więcej wyników niż określony **próg alertu** powyżej. Alert zawiera podsumowanie wszystkich zdarzeń zwróconych w wyniku. 

    - **Wyzwalaj alert dla każdego zdarzenia**. Reguła generuje unikatowy alert dla każdego zdarzenia zwróconego przez zapytanie. Jest to przydatne, jeśli chcesz, aby zdarzenia były wyświetlane indywidualnie, lub jeśli chcesz pogrupować je według określonych parametrów — według użytkownika, nazwy hosta lub innego elementu. Można zdefiniować te parametry w zapytaniu.
    
        Obecnie liczba alertów, które może wygenerować reguła, jest ograniczona do 20. Jeśli w określonej zasadzie **grupowanie zdarzeń** jest ustawione na **wyzwolenie alertu dla każdego zdarzenia**, a zapytanie reguły zwróci więcej niż 20 zdarzeń, każde z pierwszych 19 zdarzeń wygeneruje unikatowy alert, a 20 alertu spowoduje podsumowanie całego zestawu zwracanych zdarzeń. Innymi słowy, dwudziesty alert to co zostało wygenerowane w ramach opcji **Grupuj wszystkie zdarzenia do pojedynczego alertu** .

    > [!NOTE]
    > Czym różnią się **zdarzenia** i **alerty**?
    >
    > - **Zdarzenie** to opis pojedynczego wystąpienia akcji. Na przykład pojedynczy wpis w pliku dziennika może być traktowany jako zdarzenie. W tym kontekście zdarzenie odnosi się do pojedynczego wyniku zwróconego przez zapytanie w regule analizy.
    >
    > - **Alert** to zbiór zdarzeń, które razem są istotne w przypadku bezpieczeństwa punktu widzenia zabezpieczeń. Alert może zawierać pojedyncze zdarzenie, jeśli zdarzenie miało znaczące konsekwencje dla bezpieczeństwa — logowanie administracyjne z obcego kraju poza godzinami pracy, na przykład.
    >
    > - Co to jest **incydenty**? Wewnętrzna logika wskaźnikowa platformy Azure tworzy **zdarzenia** z **alertów** lub grup alertów. Kolejka zdarzeń jest punktem ogniskowym, klasyfikacja, badanie i korygowanie "analityków".
    > 
    > Na platformie Azure Wskaźnikowanie pozyskuje zdarzenia pierwotne z niektórych źródeł danych i już przetwarza alerty od innych użytkowników. Ważne jest, aby zwrócić uwagę na to, do której kwestii w dowolnym momencie.

- W sekcji **pomijania** można włączyć opcję **Zatrzymaj uruchomienie zapytania po wygenerowaniu alertu** **w** przypadku, gdy otrzymasz alert, chcesz wstrzymać operację tej reguły przez okres czasu przekraczający Interwał zapytania. W przypadku włączenia tej opcji należy ustawić polecenie **Zatrzymaj wykonywanie zapytania przez** czas, w którym zapytanie powinno zostać zatrzymane, do 24 godzin.

## <a name="configure-the-incident-creation-settings"></a>Konfigurowanie ustawień tworzenia zdarzenia

Na karcie **Ustawienia zdarzenia** możesz określić, czy i w jaki sposób wskaźnik platformy Azure ma przełączać alerty do zdarzeń z możliwością działania. Jeśli ta karta jest poprzednia, wskaźnik myszy platformy Azure utworzy pojedyncze, oddzielne zdarzenie z każdego i każdego alertu. Można zrezygnować z tworzenia zdarzeń lub zgrupować kilka alertów w jednym zdarzeniu, zmieniając ustawienia na tej karcie.

> [!IMPORTANT]
> Karta Ustawienia zdarzenia jest obecnie dostępna w **wersji zapoznawczej**. Zapoznaj się z dodatkowymi [warunkami użytkowania Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) wersji zapoznawczych, aby uzyskać dodatkowe postanowienia prawne dotyczące funkcji systemu Azure, które są w wersji beta, Preview lub w inny sposób nie zostały jeszcze udostępnione publicznie.

:::image type="content" source="media/tutorial-detect-threats-custom/incident-settings-tab.png" alt-text="Zdefiniuj ustawienia tworzenia zdarzenia i grupowania alertów":::

### <a name="incident-settings"></a>Ustawienia zdarzenia

W sekcji **Ustawienia zdarzenia** **Tworzenie zdarzeń z alertów wyzwalanych przez tę regułę analizy** jest domyślnie ustawione na wartość **włączone**, co oznacza, że punkt kontrolny platformy Azure utworzy pojedyncze, oddzielne zdarzenie z każdego i każdego alertu wyzwalanego przez regułę.
    
- Jeśli nie chcesz, aby ta reguła nie powodowała tworzenia żadnych zdarzeń (na przykład jeśli ta reguła jest tylko do zbierania informacji do dalszej analizy), ustaw dla tej opcji wartość **wyłączone**.

- Jeśli chcesz utworzyć pojedyncze zdarzenie z grupy alertów, a nie dla każdego pojedynczego alertu, zobacz następną sekcję.

### <a name="alert-grouping"></a>Grupowanie alertów

Jeśli w sekcji **grupowanie alertów** ma zostać wygenerowane pojedyncze zdarzenie z grupy do 150 podobnych lub cyklicznych alertów (patrz Uwaga), ustaw **alerty powiązane z grupą, wyzwalane przez tę regułę analizy, w zdarzeniach** do **włączenia** i ustaw następujące parametry.

- **Ogranicz grupę do alertów utworzonych w ramach wybranego przedziału czasu**: Określ przedział czasu, w którym będą grupowane podobne lub cykliczne alerty. Wszystkie odpowiednie alerty w tym przedziale czasu spowodują zbiorcze wygenerowanie incydentu lub zestawu incydentów (w zależności od ustawień grupowania poniżej). Alerty poza tym przedziałem czasu generują oddzielne zdarzenie lub zestaw zdarzeń.

- **Grupuj alerty wyzwalane przez tę regułę analizy w ramach pojedynczego zdarzenia przez**: Wybierz podstawę, z której będą grupowane alerty:

    - **Grupuj alerty w pojedynczym zdarzeniu, jeśli wszystkie jednostki pasują** do siebie: alerty są grupowane razem, jeśli współużytkują te same wartości dla każdej mapowanej jednostki (zdefiniowanej na karcie Reguły logiki zasad powyżej). Jest to zalecane ustawienie.

    - **Grupuj wszystkie alerty wyzwalane przez tę regułę w pojedynczym zdarzeniu**: wszystkie alerty wygenerowane przez tę regułę są grupowane razem, nawet jeśli nie mają identycznych wartości.

    - **Grupuj alerty w pojedynczym zdarzeniu, jeśli wybrane jednostki są zgodne**: alerty są grupowane razem, jeśli współużytkują te same wartości dla niektórych mapowanych jednostek (które można wybrać z listy rozwijanej). Możesz chcieć użyć tego ustawienia, jeśli na przykład chcesz utworzyć oddzielne zdarzenia na podstawie źródłowego lub docelowego adresu IP.

- **Ponownie otwórz zamknięte zdarzenia dopasowane**: Jeśli zdarzenie zostało rozwiązane i zamknięte, a później zostanie wygenerowany inny alert, który powinien należeć do tego zdarzenia, ustaw to ustawienie na **włączone** , jeśli chcesz ponownie otworzyć zamknięte zdarzenie i pozostaw jako **wyłączone** , jeśli chcesz, aby alert utworzył nowe zdarzenie.
    
    > [!NOTE]
    > **Do 150 alertów** można grupować w jedno zdarzenie. W przypadku wygenerowania więcej niż 150 alertów przez regułę grupującą je w pojedyncze zdarzenie zostanie wygenerowane nowe zdarzenie zawierające te same szczegóły zdarzenia co oryginał, a nadmiarowe alerty zostaną zgrupowane w nowym zdarzeniu.

## <a name="set-automated-responses-and-create-the-rule"></a>Ustawianie odpowiedzi automatycznych i Tworzenie reguły

1. Na karcie **odpowiedzi automatyczne** zaznacz wszystkie elementy PlayBook, które mają być uruchamiane automatycznie po wygenerowaniu alertu przez regułę niestandardową. Aby uzyskać więcej informacji na temat tworzenia i automatyzowania elementy PlayBook, zobacz [reagowanie na zagrożenia](tutorial-respond-threats-playbook.md).

    :::image type="content" source="media/tutorial-detect-threats-custom/automated-response-tab.png" alt-text="Zdefiniuj ustawienia automatycznej odpowiedzi":::

1. Wybierz pozycję **Przegląd i Utwórz,** aby przejrzeć wszystkie ustawienia dla nowej reguły alertu. Gdy zostanie wyświetlony komunikat "Walidacja przebiegła", wybierz pozycję **Utwórz** , aby zainicjować regułę alertu.

    :::image type="content" source="media/tutorial-detect-threats-custom/review-and-create-tab.png" alt-text="Przejrzyj wszystkie ustawienia i Utwórz regułę":::

## <a name="view-the-rule-and-its-output"></a>Wyświetlanie reguły i jej danych wyjściowych
  
- Nowo utworzoną regułę niestandardową (typu "zaplanowane") można znaleźć w tabeli na karcie **aktywne reguły** na ekranie głównym **analizy** . Z tej listy można włączać, wyłączać lub usuwać każdą regułę.

- Aby wyświetlić wyniki utworzonych reguł alertów, przejdź do strony **incydenty** , na której można klasyfikacja, [zbadać zdarzenia](tutorial-investigate-cases.md)i skorygować zagrożenia.

> [!NOTE]
> Alerty generowane na platformie Azure — wskaźnikiem dostępności są dostępne za pomocą [Microsoft Graph zabezpieczenia](/graph/security-concept-overview). Aby uzyskać więcej informacji, zapoznaj się z [dokumentacją dotyczącą alertów zabezpieczeń Microsoft Graph](/graph/api/resources/security-api-overview).

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="issue-no-events-appear-in-query-results"></a>Problem: żadne zdarzenia nie są wyświetlane w wynikach zapytania

Jeśli **grupowanie zdarzeń** jest ustawione na **wyzwolenie alertu dla każdego zdarzenia**, wówczas w niektórych scenariuszach podczas przeglądania wyników zapytania w późniejszym czasie (na przykład w przypadku przestawiania z powrotem do alertów ze zdarzenia) istnieje możliwość, że nie zostaną wyświetlone żadne wyniki zapytania. Wynika to z faktu, że połączenie z alertem jest wykonywane przez mieszanie informacji o konkretnym zdarzeniu i włączenie skrótu w zapytaniu. Jeśli wyniki zapytania uległy zmianie od czasu wygenerowania alertu, skrót nie będzie już prawidłowy i nie będą wyświetlane żadne wyniki. 

Aby wyświetlić zdarzenia, ręcznie usuń wiersz z wartością skrótu z zapytania reguły, a następnie uruchom kwerendę.

### <a name="issue-a-scheduled-rule-failed-to-execute-or-appears-with-auto-disabled-added-to-the-name"></a>Problem: zaplanowana reguła nie została wykonana lub jest wyświetlana z opcją autowyłączenia dodaną do nazwy

Jest to rzadki przypadek, w którym nie można uruchomić zaplanowanej reguły zapytania, ale może się to zdarzyć. Platforma Azure wskaźnikowego klasyfikuje błędy jako przejściowe lub stałe, na podstawie określonego typu awarii i okoliczności, które doprowadziły do niego.

#### <a name="transient-failure"></a>Błąd przejściowy

Przejściowy błąd występuje z powodu sytuacji, która jest tymczasowa i wkrótce wróci do normalnego, w którym momencie wykonanie reguły powiedzie się. Niektóre przykłady błędów, które klasyfikują wskaźnik platformy Azure jako przejściowe:

- Uruchamianie zapytania reguły trwa zbyt długo i przekracza limit czasu.
- Problemy z łącznością między źródłami danych a Log Analytics lub między Log Analytics i wskaźnikiem kontrolnym platformy Azure.
- Wszelkie inne nowe i nieznane awarie są uznawane za przejściowe.

W przypadku błędu przejściowego wskaźnik platformy Azure próbuje wykonać regułę ponownie po wstępnie określonym i ciągle rosnących odstępach czasu, aż do momentu. Następnie reguła zostanie uruchomiona ponownie tylko w następnym zaplanowanym czasie. Reguła nigdy nie zostanie wyłączona z powodu przejściowego błędu.

#### <a name="permanent-failure---rule-auto-disabled"></a>Trwały błąd — reguła została wyłączona samoobsługowo

Trwały błąd występuje ze względu na zmianę warunków zezwalających na uruchomienie reguły, która bez interwencji człowieka nie powróci do poprzedniego stanu. Poniżej przedstawiono kilka przykładów niepowodzeń, które są klasyfikowane jako trwałe:

- Docelowy obszar roboczy (na którym jest obsługiwana kwerenda reguły) został usunięty.
- Tabela docelowa (na której jest obsługiwana kwerenda reguły) została usunięta.
- Wskaźnik "Azure" został usunięty z docelowego obszaru roboczego.
- Funkcja używana przez zapytanie reguły nie jest już prawidłowa; został on zmodyfikowany lub usunięty.
- Zmieniono uprawnienia do jednego ze źródeł danych zapytania reguły.
- Jedno z źródeł danych zapytania reguły zostało usunięte lub rozłączone.

**W przypadku wstępnie określoną liczbę kolejnych stałych niepowodzeń tego samego typu i tej samej reguły,** Punkt kontrolny platformy Azure nie próbuje wykonać reguły, a także wykonuje następujące czynności:

- Wyłącza regułę.
- Dodaje wyrazy **"automatycznie wyłączone"** na początku nazwy reguły.
- Dodaje przyczynę niepowodzenia (i wyłączenie) do opisu reguły.

Możesz łatwo określić obecność wszystkich reguł, które są wyłączone, sortując listę reguł według nazwy. Reguły autowyłączone będą znajdować się na początku lub w górnej części listy.

Menedżerów SOC powinni mieć pewność, że lista reguł ma być regularnie sprawdzana pod kątem obecności reguł, które są wyłączone.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób rozpoczynania wykrywania zagrożeń przy użyciu platformy Azure.

- Dowiedz się, jak [zbadać zdarzenia na platformie Azure — wskaźnik](tutorial-investigate-cases.md).
- Dowiedz się więcej o [jednostkach na platformie Azure — wskaźnik](entities-in-azure-sentinel.md).
- Dowiedz się, jak [skonfigurować automatyczne reagowanie na zagrożenia na platformie Azure — wskaźnik](tutorial-respond-threats-playbook.md).
