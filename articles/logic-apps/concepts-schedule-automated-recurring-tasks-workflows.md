---
title: Planowanie cyklicznych zadań i przepływów pracy w Azure Logic Apps
description: Omówienie planowania cyklicznych zautomatyzowanych zadań, procesów i przepływów pracy przy użyciu Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: conceptual
ms.date: 02/16/2021
ms.openlocfilehash: e9fbafa9f3c33d10496e84f61e1f2b97f6328d3b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "100581817"
---
# <a name="schedule-and-run-recurring-automated-tasks-processes-and-workflows-with-azure-logic-apps"></a>Planowanie i uruchamianie cyklicznych automatycznych zadań, procesów i przepływów pracy przy użyciu usługi Azure Logic Apps

Logic Apps pomaga tworzyć i uruchamiać zautomatyzowane powtarzające się zadania i procesy zgodnie z harmonogramem. Tworząc przepływ pracy aplikacji logiki, który rozpoczyna się od wbudowanego wyzwalacza lub ruchomego wyzwalacza okna, które są wyzwalaczami typu harmonogram, można uruchamiać zadania natychmiast, w późniejszym czasie lub cyklicznie. Możesz wywoływać usługi wewnątrz i na zewnątrz platformy Azure, takie jak punkty końcowe HTTP lub HTTPS, wysyłanie komunikatów do usług platformy Azure, takich jak Azure Storage i Azure Service Bus, lub pobieranie plików przekazanych do udziału plików. Wyzwalacz cyklu umożliwia również Konfigurowanie złożonych harmonogramów i zaawansowanych cykli na potrzeby uruchamiania zadań. Aby dowiedzieć się więcej o wbudowanych wyzwalaczach i akcjach harmonogramu, zobacz temat [planowanie wyzwalaczy](#schedule-triggers) i [Planowanie akcji](#schedule-actions). 

> [!TIP]
> Można zaplanować i uruchamiać obciążenia cykliczne bez tworzenia oddzielnej aplikacji logiki dla każdego zaplanowanego zadania i uruchamiania w [limicie przepływów pracy na region i subskrypcję](../logic-apps/logic-apps-limits-and-config.md#definition-limits). Zamiast tego można użyć wzorca aplikacji logiki, który jest tworzony przez [szablon szybkiego startu platformy Azure: Logic Apps harmonogram zadań](https://github.com/Azure/azure-quickstart-templates/tree/master/301-logicapps-jobscheduler/).
>
> Szablon harmonogramu zadań Logic Apps tworzy aplikację logiki CreateTimerJob, która wywołuje aplikację logiki TimerJob. Następnie można wywołać aplikację logiki CreateTimerJob jako interfejs API, wykonując żądanie HTTP i przekazując harmonogram jako dane wejściowe dla żądania. Każde wywołanie aplikacji logiki CreateTimerJob również wywołuje aplikację logiki TimerJob, która tworzy nowe wystąpienie TimerJob, które jest stale uruchamiane na podstawie określonego harmonogramu lub do czasu spełnienia określonego limitu. Dzięki temu można uruchamiać dowolną liczbę wystąpień TimerJob bez obaw o limity przepływu pracy, ponieważ wystąpienia nie są zależne od definicji przepływu pracy aplikacji logiki ani zasobów.

Ta lista zawiera przykładowe zadania, które można uruchomić za pomocą harmonogramu wbudowane wyzwalacze:

* Pobierz dane wewnętrzne, takie jak uruchamianie procedury składowanej SQL codziennie.

* Pobierz dane zewnętrzne, takie jak ściąganie raportów pogody z NOAA co 15 minut.

* Wyślij dane raportu, takie jak wysyłanie wiadomości e-mail do podsumowania dla wszystkich zamówień o wartości większej niż określona w ubiegłym tygodniu.

* Przetwarzaj dane, takie jak Kompresuj współczesne obrazy, co dzień tygodnia w godzinach poza godzinami szczytu.

* Wyczyść dane, na przykład Usuń wszystkie tweety starsze niż trzy miesiące.

* Archiwizuj dane, np. faktury wypychane do usługi kopii zapasowych w dniu 1:00, codziennie przez następne dziewięć miesięcy.

Możesz również użyć wbudowanych akcji harmonogramu, aby wstrzymać przepływ pracy przed rozpoczęciem następnej akcji, na przykład:

* Poczekaj, aż dzień tygodnia wyśle aktualizację stanu za pośrednictwem poczty e-mail.

* Opóźnij przepływ pracy do momentu zakończenia wywołania HTTP przed wznowieniem i pobraniem wyniku.

W tym artykule opisano możliwości harmonogramu wbudowane wyzwalacze i akcje.

<a name="schedule-triggers"></a>

## <a name="schedule-triggers"></a>Wyzwalacze harmonogramu

Przepływ pracy aplikacji logiki można uruchomić przy użyciu wyzwalacza cykl lub okna przesuwania, które nie jest skojarzone z żadną określoną usługą lub systemem. Te wyzwalacze uruchamiają i uruchamiają przepływy pracy na podstawie określonego cyklu, w którym można wybrać interwał i częstotliwość, takie jak liczba sekund, minut, godzin, dni, tygodnie lub miesiące. Możesz również ustawić datę i godzinę rozpoczęcia wraz ze strefą czasową. Za każdym razem, gdy uruchamiany jest wyzwalacz, Logic Apps tworzy i uruchamia nowe wystąpienie przepływu pracy dla aplikacji logiki.

Poniżej przedstawiono różnice między tymi wyzwalaczami:

* **Cykl**: uruchamia przepływ pracy w regularnych odstępach czasu zgodnie z określonym harmonogramem. Jeśli wyzwalacz zostanie pominięty, na przykład ze względu na zakłócenia lub wyłączone przepływy pracy, wyzwalacz cyklu nie przetwarza pominiętych cykli, ale uruchamia ponownie cykle przy następnym zaplanowanym interwale.

  Jeśli wybierzesz **dzień** jako częstotliwość, możesz określić godziny dnia i minuty godziny, na przykład codziennie o godzinie 2:30. Jeśli wybierzesz opcję **tydzień** jako częstotliwość, możesz również wybrać dni tygodnia, takie jak środa i sobota. Możesz również określić datę i godzinę rozpoczęcia wraz ze strefą czasową harmonogramu cyklu.

  > [!TIP]
  > Jeśli cykl nie określi konkretnej [daty i godziny rozpoczęcia](#start-time), Pierwszy cykl jest uruchamiany natychmiast po zapisaniu lub wdrożeniu aplikacji logiki, pomimo konfiguracji cyklu wyzwalacza. Aby uniknąć tego zachowania, podaj datę i godzinę rozpoczęcia pierwszego cyklu do uruchomienia.
  >
  > Jeśli cykl nie określi żadnych innych zaawansowanych opcji planowania, takich jak określone czasy uruchamiania przyszłych cykli, te cykle są oparte na czasie ostatniego uruchomienia. W związku z tym czasy rozpoczęcia tych cyklów mogą być naliczone ze względu na takie czynniki jak opóźnienie podczas wywołań magazynu. Aby upewnić się, że aplikacja logiki nie trafił cyklu, szczególnie wtedy, gdy częstotliwość jest w dniach lub dłużej, wypróbuj następujące opcje:
  >
  > * Podaj datę i godzinę rozpoczęcia cyklu oraz określone czasy wykonywania kolejnych cykli przy użyciu właściwości o nazwach **w tych godzinach** i **w tych minutach**, które są dostępne tylko dla częstotliwości **dzień** i **tydzień** .
  >
  > * Użyj [wyzwalacza okna przesuwanego](../connectors/connectors-native-sliding-window.md), a nie wyzwalacza cyklicznego.

  Aby uzyskać więcej informacji, zobacz [Tworzenie, planowanie i uruchamianie cyklicznych zadań i przepływów pracy z wyzwalaczem cyklu](../connectors/connectors-native-recurrence.md).

* **Okno przewijania**: uruchamia przepływ pracy w regularnych odstępach czasu, które obsługują dane w ciągłych fragmentach. Jeśli wyzwalacz zostanie pominięty, na przykład ze względu na zakłócenia lub wyłączone przepływy pracy, wyzwalacz okna przesuwania wraca i przetwarza pominięte cykle.

  Możesz określić datę i godzinę rozpoczęcia, strefę czasową oraz czas trwania, aby opóźnić każdy cykl w przepływie pracy. Ten wyzwalacz nie obsługuje zaawansowanych harmonogramów, na przykład określonych godzin dnia, minut godzin i dni tygodnia. Aby uzyskać więcej informacji, zobacz [Tworzenie, planowanie i uruchamianie cyklicznych zadań i przepływów pracy przy użyciu wyzwalacza okna przesuwanego](../connectors/connectors-native-sliding-window.md).

<a name="schedule-actions"></a>

## <a name="schedule-actions"></a>Planowanie akcji

Po dodaniu dowolnego działania w przepływie pracy aplikacji logiki można użyć opóźnień i opóźnień do momentu wykonania przepływu pracy przed rozpoczęciem następnej akcji.

* **Opóźnienie**: Zaczekaj na uruchomienie kolejnej akcji dla określonej liczby jednostek czasu, takich jak sekundy, minuty, godziny, dni, tygodnie lub miesiące. Aby uzyskać więcej informacji, zobacz [opóźnianie następnej akcji w przepływach pracy](../connectors/connectors-native-delay.md).

* **Opóźnij do**: Poczekaj na uruchomienie następnej akcji do określonej daty i godziny. Aby uzyskać więcej informacji, zobacz [opóźnianie następnej akcji w przepływach pracy](../connectors/connectors-native-delay.md).

<a name="start-time"></a>

## <a name="patterns-for-start-date-and-time"></a>Wzorce dla daty i godziny rozpoczęcia

Poniżej przedstawiono niektóre wzorce pokazujące, jak można kontrolować cykl z datą i godziną rozpoczęcia oraz jak usługa Logic Apps uruchamia następujące cykle:

| Godzina rozpoczęcia | Cykl bez harmonogramu | Cykl z harmonogramem (tylko wyzwalacz cykliczny) |
|------------|-----------------------------|----------------------------------------------------|
| dawaj | Natychmiast uruchamia pierwsze obciążenie. <p>Uruchamia przyszłe obciążenia na podstawie czasu ostatniego uruchomienia. | Natychmiast uruchamia pierwsze obciążenie. <p>Uruchamia przyszłe obciążenia zgodnie z określonym harmonogramem. |
| Godzina rozpoczęcia w przeszłości | Wyzwalacz **cyklu** : oblicza czasy wykonywania na podstawie określonego czasu rozpoczęcia i odrzuca czas wykonywania. <p><p>Uruchamia pierwsze obciążenie w następnym przyszłym czasie wykonywania. <p><p>Uruchamia przyszłe obciążenia na podstawie czasu ostatniego uruchomienia. <p><p>Wyzwalacz **okna przewijania** : oblicza czasy uruchamiania na podstawie określonego czasu rozpoczęcia i trwają czas wykonywania. <p><p>Uruchamia przyszłe obciążenia w oparciu o określony czas rozpoczęcia. <p><p>Aby uzyskać więcej wyjaśnień, zobacz przykład poniżej tej tabeli. | Uruchamia pierwsze obciążenie, *nie wcześniej* niż godzina rozpoczęcia, na podstawie harmonogramu obliczonego na podstawie czasu rozpoczęcia. <p><p>Uruchamia przyszłe obciążenia zgodnie z określonym harmonogramem. <p><p>**Uwaga:** Jeśli określisz cykl z harmonogramem, ale nie określisz godzin lub minut dla harmonogramu, Logic Apps oblicza przyszłe czasy wykonywania, używając odpowiednio godzin lub minut z pierwszego uruchomienia. |
| Czas rozpoczęcia teraz lub w przyszłości | Uruchamia pierwsze obciążenie o określonej godzinie rozpoczęcia. <p><p>Wyzwalacz **cyklu** : uruchamia przyszłe obciążenia na podstawie czasu ostatniego uruchomienia. <p><p>Wyzwalacz **okna przewijania** : uruchamia przyszłe obciążenia w oparciu o określony czas rozpoczęcia. | Uruchamia pierwsze obciążenie, *nie wcześniej* niż godzina rozpoczęcia, na podstawie harmonogramu obliczonego na podstawie czasu rozpoczęcia. <p><p>Uruchamia przyszłe obciążenia zgodnie z określonym harmonogramem. <p>**Uwaga:** Jeśli określisz cykl z harmonogramem, ale nie określisz godzin lub minut dla harmonogramu, Logic Apps oblicza przyszłe czasy wykonywania, używając odpowiednio godzin lub minut z pierwszego uruchomienia. |
||||

*Przykład minionego czasu rozpoczęcia i cyklu, ale bez harmonogramu*

Załóżmy, że bieżąca data i godzina to 8 września 2017 o 1:00 PM. Należy określić datę i godzinę rozpoczęcia jako 7 września 2017 o 2:00 PM, która przypada w przeszłości, a cykl, który jest uruchamiany co dwa dni.

| Godzina rozpoczęcia | Bieżący czas | Cykl | Zaplanuj |
|------------|--------------|------------|----------|
| 2017-09 –**07** T14:00:00Z <br>(2017-09 –**07** o 2:00 PM) | 2017-09 –**08** T13:00:00Z <br>(2017-09 –**08** o 1:00 PM) | Co dwa dni | dawaj |
|||||

W przypadku wyzwalacza cyklicznego aparat Logic Apps oblicza czasy wykonywania na podstawie czasu rozpoczęcia, odrzuca przeszłe czasy wykonywania, używa następnego przyszłego czasu rozpoczęcia pierwszego uruchomienia i oblicza przyszłe uruchomienia w oparciu o czas ostatniego uruchomienia.

Oto jak wygląda ten cykl:

| Godzina rozpoczęcia | Czas pierwszego uruchomienia | Przyszłe czasy wykonywania |
|------------|----------------|------------------|
| 2017-09 –**07** o 2:00 PM | 2017-09 –**09** o 2:00 PM | 2017-09 –**11** o 2:00 PM </br>2017-09 –**13** o 2:00 PM </br>2017-09 –**15** o 2:00 PM </br>i tak dalej... |
||||

W związku z tym niezależnie od tego, jak daleko w przeszłości określisz czas rozpoczęcia, na przykład 2017-09-**05** o 2:00 PM lub 2017-09-**01** o 2:00 PM, pierwsze uruchomienie zawsze będzie używane w następnym przyszłym czasie.

Dla wyzwalacza okna przesuwania, aparat Logic Apps oblicza czasy wykonywania na podstawie czasu rozpoczęcia, trwa czas rozpoczęcia pierwszego uruchomienia i oblicza przyszłe uruchomienia na podstawie czasu rozpoczęcia.

Oto jak wygląda ten cykl:

| Godzina rozpoczęcia | Czas pierwszego uruchomienia | Przyszłe czasy wykonywania |
|------------|----------------|------------------|
| 2017-09 –**07** o 2:00 PM | 2017-09 –**08** o 1:00 PM (bieżący czas) | 2017-09 –**09** o 2:00 PM </br>2017-09 –**11** o 2:00 PM </br>2017-09 –**13** o 2:00 PM </br>2017-09 –**15** o 2:00 PM </br>i tak dalej... |
||||

W związku z tym niezależnie od tego, jak daleko w przeszłości określisz czas rozpoczęcia, na przykład 2017-09-**05** o 2:00 PM lub 2017-09-**01** o 2:00 PM, pierwsze uruchomienie zawsze używa określonego czasu rozpoczęcia.

<a name="daylight-saving-standard-time"></a>

## <a name="recurrence-for-daylight-saving-time-and-standard-time"></a>Cykl czasu letniego i czasu standardowego

Powtarzające się wyzwalacze są zgodne z ustawionym harmonogramem, w tym każdą określoną strefę czasową. Jeśli nie wybierzesz strefy czasowej, czas letni (DST) może mieć wpływ na to, kiedy wyzwalacze są uruchamiane, na przykład przesunięcie godziny rozpoczęcia o godzinę do przodu, gdy naliczanie i godzinę zakończenia. Podczas planowania zadań Logic Apps umieszcza komunikat do przetworzenia w kolejce i określa, kiedy ten komunikat będzie dostępny, na podstawie czasu UTC, kiedy ostatnie zadanie zostało wykonane, oraz czasu UTC, kiedy następne zadanie zostanie zaplanowane do uruchomienia.

Aby uniknąć tej zmiany, aby aplikacja logiki działała o określonej godzinie rozpoczęcia, upewnij się, że wybrano strefę czasową. W ten sposób czas UTC dla aplikacji logiki również zostanie zmieniony w celu wykrycia zmiany czasu sezonowego.

<a name="dst-window"></a>

> [!NOTE]
> Wyzwalacze, które zaczynają się od 2:00 AM-3:00, mogą mieć problemy, ponieważ zmiany czasu letniego są naliczane o godzinie 2:00, co może spowodować, że czas rozpoczęcia będzie nieprawidłowy lub niejednoznaczny. Jeśli masz wiele aplikacji logiki w ramach tego samego niejednoznacznego interwału, mogą one się nakładać. Z tego powodu warto unikać czasu rozpoczęcia między 2:00 AM-3:00 AM.

Załóżmy na przykład, że masz dwie aplikacje logiki, które codziennie działają. Jedna aplikacja logiki jest uruchamiana o godzinie 1:30 czasu lokalnego, podczas gdy druga jest uruchomiona godzinę później o 2:30 czasu lokalnego. Co się stanie od godzin rozpoczęcia dla tych aplikacji, gdy DST zaczyna się i kończą?

* Czy wyzwalacze są uruchamiane, gdy czas zostanie przesunięty o jedną godzinę do przodu?

* Czy wyzwalacze są uruchamiane dwa razy, gdy czas zostanie przesunięty o godzinę do tyłu?

Jeśli te aplikacje logiki korzystają ze strefy czasu UTC-6:00 (Stany Zjednoczone & Kanada), ta symulacja pokazuje, jak czasy UTC przesunięte w 2019, aby wyeliminować zmiany czasu letniego, przenosząc jedną godzinę do tyłu lub do przodu w razie potrzeby, tak aby aplikacje kontynuowały działanie o oczekiwanym czasie lokalnym bez pominiętych lub zduplikowanych uruchomień.

* **03/10/2019: wymiar DST zaczyna się o 2:00 AM, przesunięcie czasu do przodu o godzinę**

  Aby skompensować po rozpoczęciu DST, czas UTC przesunie się o godzinę do tyłu, tak aby aplikacja logiki kontynuowała działanie w tym samym czasie lokalnym:

  * #1 aplikacji logiki

    | Date (Data) | Czas (lokalny) | Czas (UTC) | Uwagi |
    |------|--------------|------------|-------|
    | 03/09/2019 | 1:30:00 AM | 7:30:00 AM | UTC przed dniem, w którym obowiązuje czas letni. |
    | 03/10/2019 | 1:30:00 AM | 7:30:00 AM | Czas UTC jest taki sam, ponieważ nie został wprowadzony. |
    | 03/11/2019 | 1:30:00 AM | 6:30:00 AM | Czas UTC został przesunięty o godzinę do tyłu po upływie czasu letniego. |
    |||||

  * #2 aplikacji logiki

    | Date (Data) | Czas (lokalny) | Czas (UTC) | Uwagi |
    |------|--------------|------------|-------|
    | 03/09/2019 | 2:30:00 AM | 8:30:00 AM | UTC przed dniem, w którym obowiązuje czas letni. |
    | 03/10/2019 | 3:30:00 AM * | 8:30:00 AM | DST są już w mocy, więc czas lokalny został przesunięty o jedną godzinę do przodu, ponieważ strefa czasowa UTC-6:00 zmienia się na UTC-5:00. Aby uzyskać więcej informacji, zobacz [wyzwalacze, które zaczynają się od 2:00 am-3:00 am](#dst-window). |
    | 03/11/2019 | 2:30:00 AM | 7:30:00 AM | Czas UTC został przesunięty o godzinę do tyłu po upływie czasu letniego. |
    |||||

* **11/03/2019: DST kończą się o 2:00 godz. godz.**

  Aby skompensować, czas UTC przesuwa się o jedną godzinę do przodu w taki sposób, że aplikacja logiki będzie działać w tym samym czasie lokalnym:

  * #1 aplikacji logiki

    | Date (Data) | Czas (lokalny) | Czas (UTC) | Uwagi |
    |------|--------------|------------|-------|
    | 11/02/2019 | 1:30:00 AM | 6:30:00 AM ||
    | 11/03/2019 | 1:30:00 AM | 6:30:00 AM ||
    | 11/04/2019 | 1:30:00 AM | 7:30:00 AM ||
    |||||

  * #2 aplikacji logiki

    | Date (Data) | Czas (lokalny) | Czas (UTC) | Uwagi |
    |------|--------------|------------|-------|
    | 11/02/2019 | 2:30:00 AM | 7:30:00 AM ||
    | 11/03/2019 | 2:30:00 AM | 8:30:00 AM ||
    | 11/04/2019 | 2:30:00 AM | 8:30:00 AM ||
    |||||

<a name="run-once"></a>

## <a name="run-one-time-only"></a>Uruchom tylko raz

Jeśli chcesz uruchomić aplikację logiki tylko raz w przyszłości, możesz użyć szablonu **harmonogram: Uruchom raz** . Po utworzeniu nowej aplikacji logiki, ale przed otwarciem programu Logic Apps Designer, w sekcji **Szablony** z listy **Kategoria** wybierz pozycję **harmonogram**, a następnie wybierz pozycję Ten szablon:

![Wybierz szablon "Scheduler: Uruchom raz na zadania"](./media/concepts-schedule-automated-recurring-tasks-workflows/choose-run-once-template.png)

Lub, jeśli możesz uruchomić aplikację logiki przy użyciu **żądania HTTP i odebrać wyzwalacz żądania** , a następnie Przekaż czas rozpoczęcia jako parametr dla wyzwalacza. W przypadku pierwszej akcji Użyj **Opóźnij do-Schedule** akcji, a następnie podaj godzinę uruchomienia następnej akcji.

<a name="example-recurrences"></a>

## <a name="example-recurrences"></a>Przykładowe cykle

Poniżej przedstawiono różne przykładowe cykle, które można skonfigurować dla wyzwalaczy, które obsługują opcje:

| Wyzwalacz | Cykl | Interwał | Częstotliwość | Godzina rozpoczęcia | W tych dniach | W tych godzinach | W tych minutach | Uwaga |
|---------|------------|----------|-----------|------------|---------------|----------------|------------------|------|
| Wystąpieniu <br>Okno przewijania | Uruchamiaj co 15 minut (bez daty i godziny rozpoczęcia) | 15 | Minuta | dawaj | niedostępna | dawaj | dawaj | Ten harmonogram zaczyna się od razu, a następnie oblicza przyszłe cykle na podstawie czasu ostatniego uruchomienia. |
| Wystąpieniu <br>Okno przewijania | Uruchamiany co 15 minut (z datą i godziną rozpoczęcia) | 15 | Minuta | *StartDate* T *StartTime* Z | niedostępna | dawaj | dawaj | Ten harmonogram nie jest uruchamiany *wcześniej* niż określona data i godzina rozpoczęcia, a następnie oblicza przyszłe cykle na podstawie czasu ostatniego uruchomienia. |
| Wystąpieniu <br>Okno przewijania | Uruchamiaj co godzinę, na godzinę (z datą i godziną rozpoczęcia) | 1 | Godzina | *StartDate* THH: 00:00Z | niedostępna | dawaj | dawaj | Ten harmonogram nie jest uruchamiany *wcześniej* niż określona data i godzina rozpoczęcia. Przyszłe cykle są uruchamiane co godzinę w znaku "00" minut, który Logic Apps oblicza od czasu rozpoczęcia. <p>Jeśli częstotliwość wynosi "tydzień" lub "miesiąc", ten harmonogram odpowiednio uruchamia tylko jeden dzień na tydzień lub jeden dzień miesięcznie. |
| Wystąpieniu <br>Okno przewijania | Uruchamiaj co godzinę, codziennie (bez daty i godziny rozpoczęcia) | 1 | Godzina | dawaj | niedostępna | dawaj | dawaj | Ten harmonogram zaczyna się od razu i oblicza przyszłe cykle na podstawie czasu ostatniego uruchomienia. <p>Jeśli częstotliwość wynosi "tydzień" lub "miesiąc", ten harmonogram odpowiednio uruchamia tylko jeden dzień na tydzień lub jeden dzień miesięcznie. |
| Wystąpieniu <br>Okno przewijania | Uruchamiaj co godzinę, codziennie (z datą i godziną rozpoczęcia) | 1 | Godzina | *StartDate* T *StartTime* Z | niedostępna | dawaj | dawaj | Ten harmonogram nie jest uruchamiany *wcześniej* niż określona data i godzina rozpoczęcia, a następnie oblicza przyszłe cykle na podstawie czasu ostatniego uruchomienia. <p>Jeśli częstotliwość wynosi "tydzień" lub "miesiąc", ten harmonogram odpowiednio uruchamia tylko jeden dzień na tydzień lub jeden dzień miesięcznie. |
| Wystąpieniu <br>Okno przewijania | Uruchamiany co 15 minut po godzinie, co godzinę (z datą i godziną rozpoczęcia) | 1 | Godzina | *StartDate* T00:15:00Z | niedostępna | dawaj | dawaj | Ten harmonogram nie jest uruchamiany *wcześniej* niż określona data i godzina rozpoczęcia. Przyszłe cykle są uruchamiane z oznaczeniem "15" minut, który Logic Apps oblicza od czasu rozpoczęcia, więc o 00:15 AM, 1:15, 2:15 AM i tak dalej. |
| Cykl | Uruchamiaj co 15 minut za godzinę, co godzinę (bez daty i godziny rozpoczęcia) | 1 | Dzień | dawaj | niedostępna | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14,, 16, 17, 18, 19, 20, 21, 22, 23 | 15 | Ten harmonogram jest uruchamiany o godzinie 00:15, 1:15, 2:15 AM i tak dalej. Ponadto ten harmonogram jest równoważny z częstotliwością "godzina" i godziną rozpoczęcia z "15" min. |
| Cykl | Uruchamiany co 15 minut w określonych minutach (bez daty i godziny rozpoczęcia). | 1 | Dzień | dawaj | niedostępna | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14,, 16, 17, 18, 19, 20, 21, 22, 23 | 0, 15, 30, 45 | Ten harmonogram nie zostanie uruchomiony do następnego określonego 15-minutowego znacznika. |
| Cykl | Uruchamiaj codziennie o godzinie *8 am i od* momentu zapisania aplikacji logiki | 1 | Dzień | dawaj | niedostępna | 8 | dawaj | Bez daty i godziny rozpoczęcia ten harmonogram jest uruchamiany w oparciu o godzinę zapisywania aplikacji logiki (PUT operację). |
| Cykl | Uruchom codziennie o godzinie 8:00 (z datą i godziną rozpoczęcia) | 1 | Dzień | *StartDate* T08:00:00Z | niedostępna | dawaj | dawaj | Ten harmonogram nie jest uruchamiany *wcześniej* niż określona data i godzina rozpoczęcia. Przyszłe wystąpienia są uruchamiane codziennie o godzinie 8:00. | 
| Cykl | Uruchom codziennie o godzinie 8:00 (bez daty i godziny rozpoczęcia) | 1 | Dzień | dawaj | niedostępna | 8 | 00 | Ten harmonogram jest uruchamiany codziennie o godzinie 8:00. |
| Cykl | Uruchom codziennie o godzinie 8:00 AM i 4:00 PM | 1 | Dzień | dawaj | niedostępna | 8, 16 | 0 | |
| Cykl | Uruchom codziennie o godzinie 8:30, 8:45, 4:30 PM i 4:45 PM | 1 | Dzień | dawaj | niedostępna | 8, 16 | 30, 45 | |
| Cykl | Uruchamiaj w każdą sobotę o godzinie 5:00 PM (bez daty i godziny rozpoczęcia) | 1 | Tydzień | dawaj | Sobotę | 17 | 0 | Ten harmonogram jest uruchamiany w każdą sobotę o godzinie 5:00 PM. |
| Cykl | Uruchamiaj w każdą sobotę o godzinie 5:00 PM (z datą i godziną rozpoczęcia) | 1 | Tydzień | *StartDate* T17:00:00Z | Sobotę | dawaj | dawaj | Ten harmonogram nie jest uruchamiany *wcześniej* niż określona data i godzina rozpoczęcia, w tym przypadku 9 września 2017 o 5:00 PM. Przyszłe cykle są uruchamiane w każdą sobotę o godzinie 5:00 PM. |
| Cykl | Uruchamiaj każdy wtorek, czwartek o godzinie 5 PM *oraz* oznaczenie minuty od momentu zapisania aplikacji logiki| 1 | Tydzień | dawaj | "Wtorek", "czwartek" | 17 | dawaj | |
| Cykl | Uruchamiany co godzinę w godzinach pracy. | 1 | Tydzień | dawaj | Wybierz wszystkie dni z wyjątkiem soboty i niedziela. | Wybierz godziny, których chcesz użyć. | Wybierz dowolną liczbę minut, które chcesz wykonać. | Jeśli na przykład godziny pracy mają 8:00 AM do 5:00 PM, wybierz wartość "8, 9, 10, 11, 12, 13, 14, 15, 16, 17" jako godziny dnia *i* "0" jako minuty godziny. |
| Cykl | Uruchamiaj raz dziennie w weekendy | 1 | Tydzień | dawaj | "Sobota", "Niedziela" | Wybierz godziny, których chcesz użyć. | Wybierz dowolną liczbę minut w odpowiedniej godzinie. | Ten harmonogram jest uruchamiany co soboty i niedziela zgodnie z określonym harmonogramem. |
| Cykl | Uruchamiany co 15 minut, co dwa tygodnie w poniedziałek | 2 | Tydzień | dawaj | Poniedziałek | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14,, 16, 17, 18, 19, 20, 21, 22, 23 | 0, 15, 30, 45 | Ten harmonogram jest uruchamiany co drugi poniedziałek przy co 15 minut. |
| Cykl | Uruchamiaj co miesiąc | 1 | Month (Miesiąc) | *StartDate* T *StartTime* Z | niedostępna | niedostępna | niedostępna | Ten harmonogram nie jest uruchamiany *wcześniej* niż określona data i godzina rozpoczęcia, a następnie oblicza przyszłe cykle dla daty i godziny rozpoczęcia. Jeśli nie określisz daty i godziny rozpoczęcia, w tym harmonogramie zostanie użyta Data i godzina utworzenia. |
| Cykl | Uruchamiaj co godzinę przez jeden dzień miesięcznie | 1 | Month (Miesiąc) | {patrz Uwaga} | niedostępna | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14,, 16, 17, 18, 19, 20, 21, 22, 23 | {patrz Uwaga} | Jeśli nie określisz daty i godziny rozpoczęcia, w tym harmonogramie zostanie użyta Data i godzina utworzenia. Aby kontrolować minuty dla harmonogramu cyklu, określ minuty godziny, godzinę rozpoczęcia lub Użyj czasu tworzenia. Na przykład, jeśli czas rozpoczęcia lub czas utworzenia to 8:25 AM, ten harmonogram jest uruchamiany o 8:25 AM, 9:25 AM, 10:25 AM i tak dalej. |
|||||||||

## <a name="next-steps"></a>Następne kroki

* [Tworzenie, planowanie i uruchamianie cyklicznych zadań i przepływów pracy przy użyciu wyzwalacza cykl](../connectors/connectors-native-recurrence.md)
* [Twórz, Zaplanuj i uruchamiaj zadania cykliczne i przepływy pracy z wyzwalaczem okna przesuwanego](../connectors/connectors-native-sliding-window.md)
* [Wstrzymywanie przepływów pracy z opóźnionymi akcjami](../connectors/connectors-native-delay.md)
