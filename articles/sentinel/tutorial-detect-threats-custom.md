---
title: Tworzenie niestandardowych reguł analizy w celu wykrywania zagrożeń za pomocą Azure Sentinel| Microsoft Docs
description: Z tego samouczka dowiesz się, jak tworzyć niestandardowe reguły analizy w celu wykrywania zagrożeń bezpieczeństwa za pomocą Azure Sentinel. Skorzystaj z zalet grupowania zdarzeń, grupowania alertów i wzbogacania alertów oraz poznaj funkcję AUTO DISABLED.
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
ms.date: 04/21/2021
ms.author: yelevin
ms.openlocfilehash: 180a5edd00b6085ffd91568471ca763f5e4e9711
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107814860"
---
# <a name="tutorial-create-custom-analytics-rules-to-detect-threats"></a>Samouczek: tworzenie niestandardowych reguł analizy w celu wykrywania zagrożeń

Teraz, po [](quickstart-onboard.md) połączeniu źródeł danych z usługą Azure Sentinel, możesz utworzyć niestandardowe reguły analizy, aby ułatwić odnajdywanie zagrożeń i anomalii zachowań, które występują w Twoim środowisku. Te reguły wyszukują określone zdarzenia lub zestawy zdarzeń w danym środowisku, ostrzegają o osiągnięciu określonych progów zdarzeń lub warunków, generują zdarzenia dla soc w celu ujednania i badania zagrożeń oraz reagują na zagrożenia za pomocą zautomatyzowanych procesów śledzenia i korygowania. 

Ten samouczek ułatwia tworzenie reguł niestandardowych w celu wykrywania zagrożeń za pomocą Azure Sentinel.

Po ukończeniu tego samouczka będziesz mieć możliwość wykonania następujących czynności:
> [!div class="checklist"]
> * Tworzenie reguł analizy
> * Definiowanie sposobu przetwarzania zdarzeń i alertów
> * Definiowanie sposobu generowania alertów i zdarzeń
> * Wybieranie automatycznych odpowiedzi na zagrożenia dla reguł

## <a name="create-a-custom-analytics-rule-with-a-scheduled-query"></a>Tworzenie niestandardowej reguły analizy z zaplanowanym zapytaniem

1. Z Azure Sentinel nawigacji wybierz pozycję **Analiza.**

1. Na pasku akcji u góry wybierz pozycję **+Utwórz** i wybierz pozycję **Zaplanowana reguła zapytania.** Spowoduje to otwarcie **kreatora reguły analizy**.

    :::image type="content" source="media/tutorial-detect-threats-custom/create-scheduled-query-small.png" alt-text="Tworzenie zaplanowanego zapytania" lightbox="media/tutorial-detect-threats-custom/create-scheduled-query-full.png":::

### <a name="analytics-rule-wizard---general-tab"></a>Kreator reguły analizy — karta Ogólne

- Podaj unikatową **nazwę** i **opis**. 

- W polu **Taktyka** można wybrać spośród kategorii ataków, według których należy sklasyfikować regułę. Są one oparte na taktyki struktury [MITRE ATT&CK.](https://attack.mitre.org/)

- Ustaw ważność **alertu odpowiednio** do potrzeb. 

- Po utworzeniu reguły jej  **stan jest** domyślnie włączony, co oznacza, że zostanie ona uruchamiana natychmiast po zakończeniu jej tworzenia. Jeśli nie chcesz, aby była uruchamiana natychmiast, wybierz pozycję **Wyłączone,** a reguła zostanie dodana do karty **Aktywne** reguły i będzie można ją włączyć z tego urządzenia w razie potrzeby.

   :::image type="content" source="media/tutorial-detect-threats-custom/general-tab.png" alt-text="Rozpoczynanie tworzenia niestandardowej reguły analizy":::

## <a name="define-the-rule-query-logic-and-configure-settings"></a>Definiowanie logiki zapytania reguły i konfigurowanie ustawień

Na karcie **Ustaw logikę reguły** możesz napisać zapytanie  bezpośrednio w polu Zapytanie reguły lub utworzyć zapytanie w u usługi Log Analytics, a następnie skopiować i wkleić je tutaj.

- Zapytania są pisane w języku KQL (Kusto Query Language). Dowiedz się więcej o pojęciach [i](/azure/data-explorer/kusto/concepts/) zapytaniach języka KQL [oraz](/azure/data-explorer/kusto/query/)zapoznaj się z tym [podręcznym przewodnikiem informacyjnym.](/azure/data-explorer/kql-quick-reference)

- W przykładzie pokazanym na tym zrzucie ekranu jest wysyłana kwerenda do tabeli *SecurityEvent* w celu wyświetlenia typu zdarzeń logowania systemu Windows, które zakończyły się [niepowodzeniem.](/windows/security/threat-protection/auditing/event-4625)

   :::image type="content" source="media/tutorial-detect-threats-custom/set-rule-logic-tab-1-new.png" alt-text="Konfigurowanie logiki i ustawień reguły zapytania" lightbox="media/tutorial-detect-threats-custom/set-rule-logic-tab-all-1-new.png":::

- Oto kolejne przykładowe zapytanie, które ostrzega, gdy w działaniu platformy Azure zostanie utworzona nieomyliczna [liczba zasobów.](../azure-monitor/essentials/activity-log.md)

    ```kusto
    AzureActivity
    | where OperationName == "Create or Update Virtual Machine" or OperationName =="Create Deployment"
    | where ActivityStatus == "Succeeded"
    | make-series dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(ago(7d), now(), 1d) by Caller
    ```

    > [!NOTE]
    > #### <a name="rule-query-best-practices"></a>Najlepsze rozwiązania dotyczące zapytań reguł
    > - Długość zapytania powinna mieć długość od 1 do 10 000 znaków i nie może zawierać znaków " `search *` " lub " `union *` ". Aby wyeliminować ograniczenie długości [zapytania, można](/azure/data-explorer/kusto/query/functions/user-defined-functions) użyć funkcji zdefiniowanych przez użytkownika.
    >
    > - Używanie funkcji ADX do tworzenia Azure Data Explorer w oknie zapytania usługi Log Analytics **nie jest obsługiwane.**
    >
    > - Jeśli podczas korzystania z funkcji w zapytaniu kolumny są projektowe jako pola przy użyciu wartości "", a kolumna nie istnieje, zapytanie **`bag_unpack`** `project field1` nie powiedzie się. Aby chronić się przed tym dzieje, należy rzutować kolumnę w następujący sposób:
    >   - `project field1 = column_ifexists("field1","")`

### <a name="alert-enrichment"></a>Wzbogacanie alertów

> [!IMPORTANT]
> Funkcje wzbogacania alertów są obecnie dostępne w wersji **zapoznawczej**. Aby uzyskać [Microsoft Azure dodatkowe](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) postanowienia prawne dotyczące funkcji platformy Azure, które są dostępne w wersji beta, wersji zapoznawczej lub w inny sposób, które nie zostały jeszcze wydane w wersji ogólnie dostępnej, zobacz Dodatkowe warunki użytkowania dotyczące wersji zapoznawczych.
    
- Sekcja **Konfiguracja mapowania jednostek** zawiera mapowanie parametrów z wyników zapytania na Azure Sentinel rozpoznanych jednostek. Jednostki wzbogacają dane wyjściowe reguł (alerty i zdarzenia) podstawowymi informacjami, które służą jako bloki konstrukcyjne wszelkich procesów badania i działań korygacyjnych, które należy wykonać. Są to również kryteria, według których można grupować alerty w zdarzenia na **karcie Ustawienia** zdarzenia.

    Dowiedz się więcej o [jednostkach w Azure Sentinel](entities-in-azure-sentinel.md).

    Zobacz [Mapowanie pól danych na jednostki w](map-data-fields-to-entities.md) Azure Sentinel, aby uzyskać pełne instrukcje dotyczące mapowania jednostek oraz ważne informacje na temat [zgodności z poprzednimi wersjami.](map-data-fields-to-entities.md#notes-on-the-new-version)

- Sekcja **Konfiguracja szczegółów** niestandardowych umożliwia wyodrębnianie elementów danych zdarzeń z zapytania i ich wykrywanie w alertach wytwarzanych przez tę regułę, co zapewnia natychmiastowy wgląd w zawartość zdarzeń w alertach i zdarzeniach.

    Dowiedz się więcej na temat przeglądania niestandardowych szczegółów w alertach i zapoznaj się z [kompletnymi instrukcjami](surface-custom-details-in-alerts.md).

### <a name="query-scheduling-and-alert-threshold"></a>Planowanie zapytań i próg alertów

- W **sekcji Planowanie zapytań** ustaw następujące parametry:

   :::image type="content" source="media/tutorial-detect-threats-custom/set-rule-logic-tab-2.png" alt-text="Ustawianie harmonogramu zapytań i grupowania zdarzeń" lightbox="media/tutorial-detect-threats-custom/set-rule-logic-tab-all-2-new.png":::

    - Ustaw **ustawienie Uruchom zapytanie co,** aby kontrolować, jak często zapytanie jest uruchamiane — co 5 minut lub rzadko co 14 dni.

    - Ustaw **ustawienie Odnośnik** danych od ostatniego, aby określić okres danych objętych zapytaniem — na przykład może ono odpytować dane z ostatnich 10 minut lub ostatnie 6 godzin danych. Wartość maksymalna to 14 dni.

        > [!NOTE]
        > **Interwały zapytań i okres wyszukiwania**
        >
        >  Te dwa ustawienia są od siebie niezależne do punktu. Zapytanie można uruchomić w krótkim interwale obejmującym okres dłuższy niż interwał (w efekcie nakładające się zapytania), ale nie można uruchomić zapytania w interwale, który przekracza okres pokrycia. W przeciwnym razie będą przerwy w ogólnym pokrycia zapytaniach.
        >
        > **Opóźnienie pozyskiwania**
        >
        > Aby uwzględnić  opóźnienia, które mogą wystąpić między generowaniem zdarzenia w źródle i jego pozyskiwaniem do usługi Azure Sentinel, oraz aby zapewnić  pełne pokrycie bez duplikowania danych, program Azure Sentinel uruchamia zaplanowane reguły analizy z pięciominutowym opóźnieniem od zaplanowanego czasu.
        >
        > Aby uzyskać szczegółowe wyjaśnienie techniczne, dlaczego to opóźnienie jest konieczne i jak rozwiązuje ten problem, zobacz doskonały wpis w blogu Rona Marsiano"["Handling ingestion delay in Azure Sentinel scheduled alert rules" (Obsługa](https://techcommunity.microsoft.com/t5/azure-sentinel/handling-ingestion-delay-in-azure-sentinel-scheduled-alert-rules/ba-p/2052851)opóźnienia pozyskiwania w zaplanowanych Azure Sentinel alertów).

- Użyj sekcji **Próg alertu,** aby zdefiniować poziom czułości reguły. Na przykład ustaw wartość Generuj  **alert,** gdy liczba wyników zapytania jest większa niż, i wprowadź liczbę 1000, jeśli reguła ma generować alert tylko wtedy, gdy zapytanie zwraca więcej niż 1000 wyników przy każdym jego uruchamianiu. Jest to pole wymagane, więc jeśli nie chcesz ustawiać progu , to oznacza to, że jeśli chcesz, aby alert rejestrował każde zdarzenie, wprowadź wartość 0 w polu liczby.
    
### <a name="results-simulation"></a>Symulacja wyników

W  obszarze Symulacja wyników po prawej stronie  kreatora wybierz pozycję Testuj z bieżącymi danymi Azure Sentinel aby wyświetlić wykres wyników (zdarzeń dziennika), które zapytanie wygenerowałoby w ciągu ostatnich 50 razy, zgodnie z aktualnie zdefiniowanym harmonogramem. Jeśli zmodyfikujemy zapytanie, wybierz ponownie pozycję **Testuj przy użyciu bieżących danych,** aby zaktualizować graf. Wykres przedstawia liczbę wyników w zdefiniowanym okresie określonym przez ustawienia w sekcji **Planowanie** zapytań.
  
Oto jak może wyglądać symulacja wyników dla zapytania na powyższym zrzucie ekranu. Lewa strona jest widokiem domyślnym, a prawa strona jest tym, co widzisz po umieszczeniu wskaźnika myszy na punkcie w czasie na wykresie.

:::image type="content" source="media/tutorial-detect-threats-custom/results-simulation.png" alt-text="Zrzuty ekranu symulacji wyników":::

Jeśli zobaczysz, że zapytanie wyzwoli zbyt wiele lub zbyt częste  alerty, możesz poeksperymentować z ustawieniami w sekcjach Planowanie zapytań i **Próg alertu,** [](#query-scheduling-and-alert-threshold) a następnie ponownie wybrać pozycję Testuj z bieżącymi danymi. 

### <a name="event-grouping-and-rule-suppression"></a>Grupowanie zdarzeń i pomijanie reguł

> [!IMPORTANT]
> Grupowanie zdarzeń jest obecnie dostępne w wersji **zapoznawczej**. Aby uzyskać dodatkowe postanowienia prawne dotyczące funkcji platformy [Azure,](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) które są dostępne w wersji beta, wersji zapoznawczej lub w inny sposób nie są jeszcze ogólnie dostępne, zobacz Dodatkowe warunki użytkowania dotyczące wersji zapoznawczych usługi Microsoft Azure.
    
- W **obszarze Grupowanie zdarzeń** wybierz jeden z dwóch sposobów obsługi grupowania zdarzeń **w** **alerty:** 

    - **Grupuj wszystkie zdarzenia w jeden alert** (ustawienie domyślne). Reguła generuje pojedynczy alert przy każdym jego uruchamianiu, o ile zapytanie zwróci więcej wyników niż określony **powyżej próg alertu.** Alert zawiera podsumowanie wszystkich zdarzeń zwróconych w wynikach. 

    - **Wyzwalaj alert dla każdego zdarzenia**. Reguła generuje unikatowy alert dla każdego zdarzenia zwróconego przez zapytanie. Jest to przydatne, jeśli chcesz, aby zdarzenia były wyświetlane indywidualnie lub jeśli chcesz pogrupować je według pewnych parametrów — według użytkownika, nazwy hosta lub czegoś innego. Te parametry można zdefiniować w zapytaniu.
    
        Obecnie liczba alertów, które może wygenerować reguła, jest ograniczona do 20. Jeśli w określonej  regułę grupowanie zdarzeń jest ustawione na wartość Wyzwolij **alert** dla każdego zdarzenia, a zapytanie reguły zwróci więcej niż 20 zdarzeń, każde z pierwszych 19 zdarzeń wygeneruje unikatowy alert, a 20. alert podsumuje cały zestaw zwróconych zdarzeń. Innymi słowy, 20. alert zostałby wygenerowany w obszarze Grupuj wszystkie zdarzenia **w jedną opcję alertu.**

    > [!NOTE]
    > Jaka jest różnica między **zdarzeniami i** **alertami?**
    >
    > - Zdarzenie **to** opis pojedynczego wystąpienia akcji. Na przykład pojedynczy wpis w pliku dziennika może być liczony jako zdarzenie. W tym kontekście zdarzenie odwołuje się do pojedynczego wyniku zwróconego przez zapytanie w regułę analizy.
    >
    > - Alert **to** kolekcja zdarzeń, które łącznie są istotne z punktu widzenia zabezpieczeń. Alert może zawierać pojedyncze zdarzenie, jeśli zdarzenie miało znaczące skutki dla bezpieczeństwa — na przykład logowanie administracyjne z obcego kraju poza godzinami pracy.
    >
    > - Przy okazji, co to są **zdarzenia?** Azure Sentinel wewnętrzna tworzy zdarzenia **z** **alertów** lub grup alertów. Kolejka zdarzeń jest centralnym punktem pracy analityków SOC — triage, investigation and remediation (Ocenianie, badanie i korygowanie).
    > 
    > Azure Sentinel pozyskami nieprzetworzonych zdarzeń z niektórych źródeł danych i już przetworzonymi alertami z innych źródeł. Ważne jest, aby zwrócić uwagę na to, z którym masz do czynienia w dowolnym momencie.

- W  sekcji Pomijanie można włączyć ustawienie Zatrzymaj uruchomione  zapytanie po wygenerowaniu **alertu** Wł., jeśli po wygenerowaniu alertu chcesz wstrzymać działanie tej reguły na okres dłuższy niż interwał zapytania. Jeśli włączysz tę wartość, musisz ustawić dla ustawienia Zatrzymaj uruchomione zapytanie czas, przez który zapytanie powinno przestać działać, do 24 godzin. 

## <a name="configure-the-incident-creation-settings"></a>Konfigurowanie ustawień tworzenia zdarzenia

Na karcie **Ustawienia zdarzenia** możesz wybrać, czy i w jaki sposób Azure Sentinel alerty w zdarzenia z akcjami. Jeśli ta karta zostanie pozostawiony, Azure Sentinel utworzy pojedyncze, oddzielne zdarzenie od każdego alertu. Możesz zdecydować, że nie zostaną utworzone żadne zdarzenia, lub pogrupować kilka alertów w jedno zdarzenie, zmieniając ustawienia na tej karcie.

> [!IMPORTANT]
> Karta ustawień zdarzenia jest obecnie dostępna w wersji **zapoznawczej**. Aby uzyskać [Microsoft Azure dodatkowe](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) postanowienia prawne dotyczące funkcji platformy Azure, które są dostępne w wersji beta, wersji zapoznawczej lub w inny sposób, które nie zostały jeszcze wydane w wersji ogólnie dostępnej, zobacz Dodatkowe warunki użytkowania dotyczące wersji zapoznawczych.

:::image type="content" source="media/tutorial-detect-threats-custom/incident-settings-tab.png" alt-text="Definiowanie ustawień tworzenia zdarzeń i grupowania alertów":::

### <a name="incident-settings"></a>Ustawienia zdarzenia

W  sekcji Ustawienia  zdarzenia opcja Utwórz zdarzenia z alertów wyzwalanych przez tę regułę analizy jest domyślnie ustawiona na wartość **Włączone,** co oznacza, że usługa Azure Sentinel utworzy pojedyncze, oddzielne zdarzenie dla każdego alertu wyzwalanego przez regułę.
    
- Jeśli nie chcesz, aby ta reguła spowodowała utworzenie jakichkolwiek zdarzeń (na przykład jeśli ta reguła ma na celu zbieranie informacji do późniejszej analizy), ustaw dla tej reguły wartość **Wyłączone.**

- Jeśli chcesz utworzyć pojedyncze zdarzenie z grupy alertów, a nie jedno dla każdego alertu, zobacz następną sekcję.

### <a name="alert-grouping"></a>Grupowanie alertów

Jeśli  w sekcji Grupowanie alertów chcesz, aby pojedyncze zdarzenie było generowane z grupy maksymalnie 150 alertów podobnych lub cyklicznych (zobacz uwaga), ustaw alerty związane z grupą **wyzwalane** przez tę regułę analizy na zdarzenia na wartość Włączone **i** ustaw następujące parametry.

- **Ogranicz grupę do alertów** utworzonych w wybranym okresie: określ ramy czasowe grupowania podobnych lub cyklicznych alertów. Wszystkie odpowiednie alerty w tym okresie będą zbiorczo generować zdarzenie lub zestaw zdarzeń (w zależności od poniższych ustawień grupowania). Alerty poza tym okresem będą generować oddzielne zdarzenia lub zestawy zdarzeń.

- **Grupuj alerty wyzwalane przez tę** regułę analizy w pojedyncze zdarzenie według : Wybierz podstawę grupowania alertów:

    - **Pogrupuj** alerty w pojedyncze zdarzenie, jeśli wszystkie jednostki są zgodne: alerty są grupowane, jeśli współużytkują one identyczne wartości dla każdej z zamapowanych jednostek (zdefiniowane na karcie Ustawianie logiki reguły powyżej). Jest to zalecane ustawienie.

    - **Grupuj** wszystkie alerty wyzwalane przez tę regułę w jednym zdarzeniu: wszystkie alerty wygenerowane przez tę regułę są grupowane, nawet jeśli nie mają identycznych wartości.

    - **Pogrupuj** alerty w pojedyncze zdarzenie, jeśli wybrane jednostki są zgodne: alerty są grupowane, jeśli współużytkują identyczne wartości dla niektórych zamapowanych jednostek (które można wybrać z listy rozwijanej). Tego ustawienia można użyć, jeśli na przykład chcesz utworzyć oddzielne zdarzenia na podstawie źródłowych lub docelowych adresów IP.

- Ponowne otwieranie zamkniętych zdarzeń pasujących: jeśli zdarzenie zostało rozwiązane i zamknięte, a później po wygenerowaniu innego alertu, który powinien należeć  do tego zdarzenia, ustaw to ustawienie na wartość **Włączone,** jeśli chcesz ponownie otworzyć zamknięte zdarzenie, i pozostaw wartość Wyłączone, jeśli chcesz, aby alert tworzył nowe zdarzenie. 
    
    > [!NOTE]
    > **Maksymalnie 150 alertów** można pogrupować w pojedyncze zdarzenie. Jeśli reguła, która grupuje je w pojedyncze zdarzenie, generuje więcej niż 150 alertów, zostanie wygenerowane nowe zdarzenie z tym samymi szczegółami zdarzenia co oryginalny, a nadmiarowe alerty zostaną pogrupowane w nowe zdarzenie.

## <a name="set-automated-responses-and-create-the-rule"></a>Ustawianie automatycznych odpowiedzi i tworzenie reguły

1. Na karcie **Odpowiedzi automatyczne** wybierz wszystkie podręczniki, które mają być uruchamiane automatycznie po wygenerowaniu alertu przez regułę niestandardową. Aby uzyskać więcej informacji na temat tworzenia i automatyzowania podręczników, zobacz [Reagowanie na zagrożenia.](tutorial-respond-threats-playbook.md)

    :::image type="content" source="media/tutorial-detect-threats-custom/automated-response-tab.png" alt-text="Definiowanie ustawień automatycznej odpowiedzi":::

1. Wybierz **pozycję Przejrzyj i utwórz,** aby przejrzeć wszystkie ustawienia nowej reguły alertu. Gdy pojawi się komunikat "Weryfikacja została pomyślnie przekazana", wybierz **pozycję Utwórz,** aby zainicjować regułę alertu.

    :::image type="content" source="media/tutorial-detect-threats-custom/review-and-create-tab.png" alt-text="Przejrzyj wszystkie ustawienia i utwórz regułę":::

## <a name="view-the-rule-and-its-output"></a>Wyświetlanie reguły i jej danych wyjściowych
  
- Nowo utworzona reguła niestandardowa (typu "Zaplanowane") znajduje się w tabeli na karcie **Aktywne** reguły na głównym ekranie **Analiza.** Z tej listy można włączać, wyłączać lub usuwać każdą regułę.

- Aby wyświetlić wyniki tworzyć reguły alertów,  przejdź do strony Zdarzenia, na której można przeprowadzić triaż, [](tutorial-investigate-cases.md)zbadać zdarzenia i skorygować zagrożenia.

> [!NOTE]
> Alerty wygenerowane w Azure Sentinel są dostępne za [pośrednictwem Microsoft Graph Security.](/graph/security-concept-overview) Aby uzyskać więcej informacji, zobacz [dokumentację Microsoft Graph Security alerts (Alerty zabezpieczeń).](/graph/api/resources/security-api-overview)

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="issue-no-events-appear-in-query-results"></a>Problem: W wynikach zapytania nie są wyświetlane żadne zdarzenia

Jeśli  grupowanie zdarzeń jest ustawione w celu wyzwolenia **alertu** dla każdego zdarzenia , w niektórych scenariuszach podczas wyświetlania wyników zapytania w późniejszym czasie (na przykład podczas przewrócania do alertów po zdarzeniu), możliwe, że nie zostaną wyświetlone żadne wyniki zapytania. Jest to spowodowane tym, że połączenie zdarzenia z alertem jest realizowane przez skrót informacji o konkretnym zdarzeniu i dołączenie skrótu w zapytaniu. Jeśli wyniki zapytania zmieniły się od czasu wygenerowania alertu, skrót nie będzie już prawidłowy i nie będą wyświetlane żadne wyniki. 

Aby wyświetlić zdarzenia, ręcznie usuń wiersz ze skrótem z zapytania reguły i uruchom zapytanie.

### <a name="issue-a-scheduled-rule-failed-to-execute-or-appears-with-auto-disabled-added-to-the-name"></a>Problem: Nie można wykonać zaplanowanej reguły lub jest wyświetlana z wartością AUTO DISABLED dodaną do nazwy

Zdarza się rzadko, że nie można uruchomić zaplanowanej reguły zapytania, ale może się to zdarzyć. Azure Sentinel klasyfikuje błędy z góry jako przejściowe lub trwałe na podstawie określonego typu awarii i okoliczności, które do niego doprowadziły.

#### <a name="transient-failure"></a>Błąd przejściowy

Błąd przejściowy występuje z powodu okoliczności, która jest tymczasowa i wkrótce wróci do normalnego stanu, po czym wykonanie reguły zakończy się powodzeniem. Niektóre przykłady błędów, które Azure Sentinel jako przejściowe, to:

- Wykonywanie zapytania reguły trwa zbyt długo, a jego czas jest zbyt długi.
- Problemy z łącznością między źródłami danych i usługą Log Analytics lub między usługą Log Analytics Azure Sentinel.
- Wszystkie inne nowe i nieznane błędy są uznawane za przejściowe.

W przypadku błędu przejściowego program Azure Sentinel nadal próbuje ponownie wykonać regułę po wstępnie ustalonych i stale rosnących interwałach, aż do punktu. Następnie reguła zostanie ponownie uruchamiana tylko w następnym zaplanowanym czasie. Reguła nigdy nie zostanie automatycznie wyłączona z powodu błędu przejściowego.

#### <a name="permanent-failure---rule-auto-disabled"></a>Błąd trwały — automatycznie wyłączone reguły

Trwała awaria występuje z powodu zmiany warunków, które zezwalają na uruchomienie reguły, która bez interwencji człowieka nie wróci do wcześniejszego stanu. Poniżej przedstawiono kilka przykładów błędów sklasyfikowanych jako trwałe:

- Docelowy obszar roboczy (w którym działa zapytanie reguły) został usunięty.
- Usunięto tabelę docelową (na której działało zapytanie reguły).
- Azure Sentinel został usunięty z docelowego obszaru roboczego.
- Funkcja używana przez zapytanie reguły nie jest już prawidłowa; Został on zmodyfikowany lub usunięty.
- Zmieniono uprawnienia do jednego ze źródeł danych zapytania reguły.
- Jedno ze źródeł danych zapytania reguły zostało usunięte lub odłączone.

**W przypadku wstępnie określonej** liczby kolejnych trwałych niepowodzeń tego samego typu i dla tej samej reguły, Azure Sentinel przestanie próbować wykonać regułę, a także wykonuje następujące czynności:

- Wyłącza regułę.
- Dodaje **wyrazy "AUTO DISABLED"** na początku nazwy reguły.
- Dodaje przyczynę błędu (i wyłączenie) do opisu reguły.

Możesz łatwo określić obecność dowolnych automatycznie wyłączonych reguł, sortując listę reguł według nazwy. Automatycznie wyłączone reguły będą się w górnej części listy lub w jej górnej części.

Menedżerowie SOC powinni regularnie sprawdzać listę reguł pod kątem obecności automatycznie wyłączonych reguł.

## <a name="next-steps"></a>Następne kroki

W tym samouczku opisano, jak rozpocząć wykrywanie zagrożeń przy użyciu Azure Sentinel.

- Dowiedz się, jak [badać zdarzenia w Azure Sentinel.](tutorial-investigate-cases.md)
- Dowiedz się więcej [o jednostkach w Azure Sentinel](entities-in-azure-sentinel.md).
- Dowiedz się, [jak skonfigurować automatyczne odpowiedzi na zagrożenia w Azure Sentinel](tutorial-respond-threats-playbook.md).
