---
title: Kompilowanie zautomatyzowanych przepływów pracy opartych na harmonogramie
description: Samouczek — Tworzenie cyklicznego, zautomatyzowanego przepływu pracy opartego na harmonogramie przy użyciu Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: tutorial
ms.custom: mvc
ms.date: 09/12/2019
ms.openlocfilehash: 977f138fad1f2eb1eae95049d2bd8a730ba5687e
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87048726"
---
# <a name="tutorial-create-automated-schedule-based-recurring-workflows-by-using-azure-logic-apps"></a>Samouczek: Tworzenie zautomatyzowanych, cyklicznych przepływów pracy opartych na harmonogramach przy użyciu Azure Logic Apps

W tym samouczku pokazano, jak utworzyć [aplikację logiki](../logic-apps/logic-apps-overview.md) i zautomatyzować cykliczny przepływ pracy, który jest uruchamiany zgodnie z harmonogramem. W szczególności Ta przykładowa aplikacja logiki jest uruchamiana każdego dnia tygodnia rano i sprawdza czas podróży, w tym ruch, między dwoma miejscami. Jeśli ten czas przekracza określony limit, aplikacja logiki wysyła wiadomość e-mail z informacją, ile zajmie podróż z uwzględnieniem dodatkowego czasu.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenia pustej aplikacji logiki.
> * Dodaj wyzwalacz cykliczny określający harmonogram aplikacji logiki.
> * Dodaj akcję mapy Bing, która pobiera czas podróży dla trasy.
> * Dodaj akcję, która tworzy zmienną, konwertuje czas podróży z sekund na minuty i zapisuje wynik w zmiennej.
> * Dodawanie warunku, który porównuje czas podróży z określonym limitem.
> * Dodaj akcję wysyłającą wiadomość e-mail, jeśli czas podróży przekracza limit.

Po ukończeniu aplikacja logiki będzie ogólnie wyglądać jak ten przepływ pracy:

![Omówienie przepływu pracy aplikacji logiki wysokiego poziomu](./media/tutorial-build-scheduled-recurring-logic-app-workflow/check-travel-time-overview.png)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, przed rozpoczęciem [Utwórz bezpłatne konto platformy Azure](https://azure.microsoft.com/free/) .

* Konto e-mail od dostawcy poczty e-mail obsługiwanego przez Logic Apps, takie jak Office 365 Outlook, Outlook.com lub gmail. W przypadku innych dostawców [przejrzyj tę listę łączników](/connectors/). Ten przewodnik Szybki Start używa konta programu Outlook w usłudze Office 365. Jeśli używasz innego konta e-mail, ogólne kroki pozostają takie same, ale interfejs użytkownika może się nieco różnić.

  > [!IMPORTANT]
  > Jeśli chcesz korzystać z łącznika usługi Gmail, tylko konta firmowe z zestawu G-Suite mogą używać tego łącznika bez ograniczeń w usłudze Logic Apps. Jeśli masz konto użytkownika usługi Gmail, możesz użyć tego łącznika z tylko określonymi usługami zatwierdzonymi przez firmę Google lub możesz [utworzyć aplikację kliencką Google, która będzie używana do uwierzytelniania za pomocą łącznika usługi Gmail](/connectors/gmail/#authentication-and-bring-your-own-application). Aby uzyskać więcej informacji, zobacz [zabezpieczenia danych i zasady ochrony prywatności dla łączników Google w Azure Logic Apps](../connectors/connectors-google-data-security-privacy-policy.md).

* Aby pobrać czas podróży dla danej trasy, potrzebny jest klucz dostępu dla interfejsu API usługi Mapy Bing. Aby pobrać ten klucz, postępuj zgodnie z instrukcjami [uzyskiwania klucza usługi Mapy Bing](/bingmaps/getting-started/bing-maps-dev-center-help/getting-a-bing-maps-key).

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się do [witryny Azure Portal](https://portal.azure.com) przy użyciu poświadczeń konta Azure.

## <a name="create-your-logic-app"></a>Tworzenie aplikacji logiki

1. W głównym menu platformy Azure wybierz pozycję **Utwórz zasób**  >  **Integration**  >  **aplikacja logiki**.

   ![Tworzenie zasobu aplikacji logiki](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-new-logic-app-resource.png)

1. W obszarze **Tworzenie aplikacji logiki** wprowadź następujące informacje na temat aplikacji logiki. Gdy wszystko będzie gotowe, wybierz przycisk **Utwórz**.

   ![Podaj informacje o aplikacji logiki](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-logic-app-settings.png)

   | Właściwość | Wartość | Opis |
   |----------|-------|-------------|
   | **Nazwa** | LA-TravelTime | Nazwa aplikacji logiki, która może zawierać tylko litery, cyfry, łączniki ( `-` ), podkreślenia ( `_` ), nawiasy ( `(` , `)` ) i kropki ( `.` ). W tym przykładzie zastosowano "LA-TravelTime". |
   | **Subskrypcja** | <*Twoja nazwa — subskrypcja — Azure*> | Nazwa subskrypcji platformy Azure |
   | **Grupa zasobów** | LA-TravelTime-RG | Nazwa [grupy zasobów platformy Azure](../azure-resource-manager/management/overview.md), która jest używana do organizowania powiązanych zasobów. W tym przykładzie zastosowano "LA-TravelTime-RG". |
   | **Lokalizacja** | Zachodnie stany USA | Region tnie, w którym mają być przechowywane informacje o aplikacji logiki. W tym przykładzie zastosowano "zachodnie stany USA". |
   | **Log Analytics** | Wyłączone | Ustawienie **Wyłączone** umożliwia rejestrowanie w celach diagnostycznych. |
   ||||

1. Po wdrożeniu aplikacji przez platformę Azure na pasku narzędzi platformy Azure wybierz pozycję **powiadomienia**  >  **Przejdź do pozycji zasób** dla wdrożonej aplikacji logiki.

   ![Przejdź do nowego zasobu aplikacji logiki](./media/tutorial-build-scheduled-recurring-logic-app-workflow/go-to-new-logic-app-resource.png)

   Możesz też znaleźć i wybrać aplikację logiki, wpisując nazwę w polu wyszukiwania.

   Zostanie otwarty projektant Logic Apps i zostanie wyświetlona strona z wprowadzeniem wideo i często używanymi wyzwalaczami i wzorcami aplikacji logiki. W obszarze **Szablony** wybierz pozycję **Pusta aplikacja logiki**.

   ![Wybierz szablon pustej aplikacji logiki](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-logic-app-template.png)

Następnie Dodaj [wyzwalacz](../logic-apps/logic-apps-overview.md#logic-app-concepts)cykliczny, który jest uruchamiany zgodnie z określonym harmonogramem. Każda aplikacja logiki musi rozpoczynać się od wyzwalacza, który jest aktywowany w momencie wystąpienia konkretnego zdarzenia lub spełnienia określonego warunku przez nowe dane. Aby uzyskać więcej informacji, zobacz [Tworzenie pierwszej aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-the-recurrence-trigger"></a>Dodaj wyzwalacz cykliczny

1. W Projektancie aplikacji logiki w polu wyszukiwania wprowadź "cykl" jako filtr. Z listy **wyzwalacze** Wybierz wyzwalacz **cykliczny** .

   ![Dodaj wyzwalacz "cykl"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-schedule-recurrence-trigger.png)

1. Na kształcie **cyklu** wybierz przycisk **wielokropka** (**...**), a następnie wybierz polecenie **Zmień nazwę**. Zmień nazwę wyzwalacza na następujący opis: `Check travel time every weekday morning`

   ![Zmień nazwę opisu wyzwalacza cyklu](./media/tutorial-build-scheduled-recurring-logic-app-workflow/rename-recurrence-schedule-trigger.png)

1. W wyzwalaczu Zmień te właściwości.

   ![Zmień interwał i częstotliwość wyzwalacza cyklu](./media/tutorial-build-scheduled-recurring-logic-app-workflow/change-interval-frequency.png)

   | Właściwość | Wymagany | Wartość | Opis |
   |----------|----------|-------|-------------|
   | **Interwał** | Tak | 1 | Liczba interwałów do odczekania między sprawdzaniami |
   | **Częstotliwość** | Tak | Tydzień | Jednostka czasu cyklu |
   |||||

1. W obszarze **Interwał** i **częstotliwość**Otwórz listę **Dodaj nowy parametr** i wybierz te właściwości, które mają zostać dodane do wyzwalacza.

   * **W tych dniach**
   * **W tych godzinach**
   * **W tych minutach**

   ![Dodaj właściwości dla wyzwalacza cyklu](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-trigger-properties.png)

1. Teraz ustaw wartości dla dodatkowych właściwości, jak pokazano i opisano tutaj.

   ![Podawanie szczegółów harmonogramu i cyklu](./media/tutorial-build-scheduled-recurring-logic-app-workflow/recurrence-trigger-property-values.png)

   | Właściwość | Wartość | Opis |
   |----------|-------|-------------|
   | **W tych dniach** | poniedziałek,wtorek,środa,czwartek,piątek | Dostępne tylko wtedy, gdy w polu **Częstotliwość** wybrano pozycję Tydzień |
   | **W tych godzinach** | 7,8,9 | Dostępne tylko wtedy, gdy w polu **Częstotliwość** wybrano pozycję Tydzień lub Dzień. Wybierz, o jakich godzinach ma być uruchamiany cykl. W tym przykładzie jest uruchamiany o 7, 8 i 9. |
   | **W tych minutach** | 0,15,30,45 | Dostępne tylko wtedy, gdy w polu **Częstotliwość** wybrano pozycję Tydzień lub Dzień. Wybierz, w których minutach dnia ma być uruchamiany cykl. W tym przykładzie jest uruchamiany co 15 minut, rozpoczynając od pełnej godziny. |
   ||||

   Ten wyzwalacz jest uruchamiany codziennie, co 15 minut, po raz pierwszy o 7:00 i po raz ostatni o 9:45. W oknie **Podgląd** wyświetlany jest harmonogram cyklu. Aby uzyskać więcej informacji, zobacz [Schedule tasks and workflows that run regularly (Planowanie regularnie uruchamianych zadań i przepływów pracy)](../connectors/connectors-native-recurrence.md) i [Workflow actions and triggers (Akcje i wyzwalacze przepływu pracy)](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger).

1. Aby na razie ukryć szczegóły wyzwalacza, kliknij pasek tytułu kształtu.

   ![Zwinięty kształt w celu ukrycia szczegółów](./media/tutorial-build-scheduled-recurring-logic-app-workflow/collapse-trigger-shape.png)

1. Zapisz aplikację logiki. Na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

Twoja aplikacja logiki jest już aktywna, ale nie wykonuje żadnych akcji — jest tylko cyklicznie uruchamiana. Dodaj więc akcję reagującą na aktywowanie wyzwalacza.

## <a name="get-the-travel-time-for-a-route"></a>Pobieranie czasu podróży dla trasy

Po utworzeniu wyzwalacza możesz dodać [akcję](../logic-apps/logic-apps-overview.md#logic-app-concepts), która umożliwi pobranie czasu podróży pomiędzy dwoma miejscami. Usługa Logic Apps oferuje łącznik dla interfejsu API usługi Mapy Bing, dzięki czemu można łatwo uzyskać te informacje. Przed rozpoczęciem tego zadania upewnij się, że masz klucz interfejsu API usługi Mapy Bing, zgodnie z wymaganiami wstępnymi opisanymi w tym samouczku.

1. W Projektancie aplikacji logiki w ramach wyzwalacza wybierz pozycję **nowy krok**.

1. W obszarze **Wybierz akcję**wybierz pozycję **standardowa**. W polu wyszukiwania wprowadź ciąg "mapy Bing" jako filtr, a następnie wybierz akcję **Pobierz trasę** .

   ![Wybierz akcję "Pobierz trasę"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-get-route-action.png)

1. Jeśli nie masz połączenia z usługą Mapy Bing, zostanie wyświetlony monit o utworzenie połączenia. Podaj te szczegóły połączenia i wybierz pozycję **Utwórz**.

   ![Utwórz połączenie z interfejsem API usługi mapy Bing](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-maps-connection.png)

   | Właściwość | Wymagany | Wartość | Opis |
   |----------|----------|-------|-------------|
   | **Nazwa połączenia** | Tak | BingMapsConnection | Podaj nazwę połączenia. W tym przykładzie zastosowano "BingMapsConnection". |
   | **Klucz interfejsu API** | Tak | <*Twoje-Bing-Maps-Key*> | Wprowadź uzyskany wcześniej klucz usługi Mapy Bing. Jeśli nie masz klucza usługi Mapy Bing, dowiedz się [jak uzyskać klucz](/bingmaps/getting-started/bing-maps-dev-center-help/getting-a-bing-maps-key). |
   |||||

1. Zmień nazwę akcji na następujący opis: `Get route and travel time with traffic`

1. Wewnątrz akcji Otwórz **listę Dodaj nowy parametr**i wybierz te właściwości, które mają zostać dodane do akcji.

   * **Optymalizacja**
   * **Jednostka odległości**
   * **Tryb podróży**

   ![Dodaj właściwości do akcji "Pobierz trasę"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-bing-maps-action-properties.png) 

1. Teraz ustaw wartości właściwości akcji, jak pokazano i opisano tutaj.

   ![Podaj szczegóły akcji "Pobierz trasę"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/get-route-action-settings.png) 

   | Właściwość | Wymagany | Wartość | Opis |
   |----------|----------|-------|-------------|
   | **Punkt nawigacyjny 1** | Tak | <*lokalizacja początkowa*> | Początek trasy |
   | **Punkt nawigacyjny 2** | Tak | <*Lokalizacja końcowa*> | Koniec trasy |
   | **Optymalizacja** | Nie | timeWithTraffic | Parametr używany do optymalizowania trasy, na przykład odległość, czas podróży z uwzględnieniem aktualnego natężenia ruchu i tak dalej. Wybierz parametr "timeWithTraffic". |
   | **Jednostka odległości** | Nie | <*Twoje preferencje*> | Jednostka odległości trasy. Ten przykład używa "milowej" jako jednostki. |
   | **Tryb podróży** | Nie | Jazda samochodem | Tryb podróży dla trasy. Wybierz tryb "kierowanie". |
   ||||

   Aby uzyskać więcej informacji na temat tych parametrów, zobacz [Calculate route (Obliczanie trasy)](/bingmaps/rest-services/routes/calculate-a-route).

1. Zapisz aplikację logiki.

Następnie należy utworzyć zmienną, która umożliwia przekonwertowanie aktualnego czasu podróży i zapisanie go w minutach, a nie sekundach. Dzięki temu można uniknąć powtarzania konwersji i łatwiej używać tej wartości w kolejnych krokach. 

## <a name="create-a-variable-to-store-travel-time"></a>Utwórz zmienną do przechowywania czasu podróży

Czasami może być konieczne uruchomienie operacji na danych w przepływie pracy, a następnie użycie wyników w późniejszych akcjach. Aby zapisać te wyniki i łatwiej z nich później korzystać lub się do nich odwoływać, można utworzyć zmienne do przechowywania tych wyników po ich przetworzeniu. Zmienne można utworzyć tylko na najwyższym poziomie w aplikacji logiki.

Domyślnie Poprzednia Akcja **Pobierz trasę** zwraca bieżący czas podróży z ruchem w sekundach od właściwości **ruchu o czasie trwania podróży** . Dzięki przekonwertowaniu tej wartości i zapisaniu jej w minutach łatwiej jest użyć jej ponownie później bez konieczności ponownego konwertowania.

1. W obszarze Akcja **Pobierz trasę** wybierz pozycję **nowy krok**.

1. W obszarze **Wybierz akcję**wybierz pozycję **wbudowane**. W polu wyszukiwania wprowadź ciąg "zmienne" i wybierz akcję **zainicjuj zmienną** .

   ![Wybierz akcję "zainicjuj zmienną"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-initialize-variable-action.png)

1. Zmień nazwę akcji na następujący opis: `Create variable to store travel time`

1. Wprowadź szczegóły zmiennej zgodnie z następującym opisem:

   | Właściwość | Wymagany | Wartość | Opis |
   |----------|----------|-------|-------------|
   | **Nazwa** | Tak | travelTime | Nazwa zmiennej. W tym przykładzie zastosowano "travelTime". |
   | **Typ** | Tak | Integer | Typ danych dla zmiennej |
   | **Wartość** | Nie| Wyrażenie, które konwertuje aktualny czas podróży z sekund na minuty (zobacz instrukcje pod tą tabelą). | Początkowa wartość zmiennej |
   ||||

   1. Aby utworzyć wyrażenie dla właściwości **Value** , kliknij wewnątrz pola, aby wyświetlić listę zawartości dynamicznej. W razie potrzeby poszerz okno przeglądarki, aż lista będzie widoczna. Z listy zawartość dynamiczna wybierz pozycję **wyrażenie**.

      ![Podaj informacje dotyczące akcji inicjowania zmiennej](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings.png)

      Po kliknięciu wewnątrz niektórych pól edycji zostanie wyświetlona lista zawartość dynamiczna. Ta lista zawiera wszystkie właściwości z poprzednich akcji, których można użyć jako danych wejściowych w przepływie pracy. Lista zawartości dynamicznej zawiera Edytor wyrażeń, w którym można wybrać funkcje do uruchamiania operacji. Edytor wyrażeń jest wyświetlany tylko na liście zawartości dynamicznej.

   1. W edytorze wyrażeń wprowadź wyrażenie: `div(,60)`

      ![Wprowadź wyrażenie: „div(,60)”](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-2.png)

   1. Umieść kursor wewnątrz wyrażenia między lewym nawiasem (**(**) a przecinkiem (**,**). 
   Wybierz pozycję **zawartość dynamiczna**.

      ![Ustaw kursor, wybierz pozycję "zawartość dynamiczna"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-3.png)

   1. Na liście zawartości dynamicznej wybierz pole **Czas podróży —natężenie ruchu**.

      ![Wybierz właściwość "ruch czasu podróży"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-4.png)

   1. Po rozpoznaniu wartości właściwości wewnątrz wyrażenia wybierz **przycisk OK**.

      ![Aby zakończyć Kompilowanie wyrażenia, wybierz przycisk "OK"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-5.png)

      Teraz zostanie wyświetlona Właściwość **Value** , jak pokazano poniżej:

      ![Właściwość "value" pojawia się z rozwiązanym wyrażeniem](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-6.png)

1. Zapisz aplikację logiki.

Następnie dodaj warunek, który sprawdzi, czy aktualny czas podróży jest dłuży niż określony limit.

## <a name="compare-the-travel-time-with-limit"></a>Porównaj czas podróży z limitem

1. W obszarze Poprzednia Akcja wybierz pozycję **nowy krok**.

1. W obszarze **Wybierz akcję**wybierz pozycję **wbudowane**. W polu wyszukiwania wprowadź wartość "warunek" jako filtr. Z listy Akcje wybierz akcję **warunek** .

   ![Wybierz akcję "warunek"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-condition-action.png)

1. Zmień nazwę warunku na następujący opis: `If travel time exceeds limit`

1. Kompiluj warunek, który sprawdza, czy wartość właściwości **travelTime** przekracza określony limit, zgodnie z opisem i pokazanym tutaj:

   1. W warunku kliknij wewnątrz pola **Wybierz wartość** po lewej stronie warunku.

   1. Z wyświetlonej listy zawartości dynamicznej w obszarze **zmienne**wybierz właściwość **travelTime** .

      ![Tworzenie lewej strony warunku](./media/tutorial-build-scheduled-recurring-logic-app-workflow/build-condition-left-side.png)

   1. W środkowym polu porównania wybierz operator **jest większy niż** .

   1. W polu **Wybierz wartość** z prawej strony warunku wprowadź następujący limit:`15`

      Gdy wszystko będzie gotowe, warunek będzie wyglądać podobnie do tego przykładu:

      ![Gotowy warunek do sprawdzania czasu podróży](./media/tutorial-build-scheduled-recurring-logic-app-workflow/build-condition-check-travel-time.png)

1. Zapisz aplikację logiki.

Następnie Dodaj akcję do uruchomienia, gdy czas podróży przekroczy limit.

## <a name="send-email-when-limit-exceeded"></a>Wysyłanie wiadomości e-mail po przekroczeniu limitu

Teraz dodaj akcję, która wysyła do Ciebie wiadomość e-mail, jeśli czas podróży przekracza limit. Ta wiadomość e-mail zawiera aktualny czas podróży oraz dodatkowy czas niezbędny do pokonania określonej trasy.

1. W gałęzi warunku w **przypadku wartości true** wybierz pozycję **Dodaj akcję**.

1. W obszarze **Wybierz akcję**wybierz pozycję **standardowa**. W polu wyszukiwania wprowadź ciąg "Wyślij wiadomość e-mail". Lista zwraca wiele wyników, więc najpierw wybierz odpowiedni łącznik poczty e-mail, na przykład:

   ![Wybierz odpowiedni łącznik poczty e-mail](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-action-send-email.png)

   * W przypadku kont służbowych platformy Azure wybierz pozycję **Office 365 Outlook**.
   * W przypadku osobistych kont Microsoft wybierz pozycję **Outlook.com**.

1. Po wyświetleniu akcji łącznika wybierz pozycję "Wyślij wiadomość e-mail", której chcesz użyć, na przykład:

   ![Wybieranie akcji „Wyślij wiadomość e-mail”](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-send-email-action.png)

1. Jeśli nie masz jeszcze połączenia, pojawi się monit o zalogowanie się do swojego konta poczty e-mail.

   Usługa Logic Apps utworzy połączenie z kontem e-mail.

1. Zmień nazwę akcji na następujący opis: `Send email with travel time`

1. W polu **Do** wprowadź adres e-mail adresata. Do celów testowych możesz użyć własnego adresu e-mail.

1. W polu **Temat** określ temat wiadomości e-mail i dodaj zmienną **travelTime**.

   1. Wprowadź tekst `Current travel time (minutes):` ze spacją końcową. 

   1. Z listy zawartość dynamiczna w obszarze **zmienne**wybierz pozycję **Zobacz więcej**.

      ![Znajdź zmienną "travelTime"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/find-travelTime-variable.png)

   1. Gdy **travelTime** pojawi się w obszarze **zmienne**, wybierz pozycję **travelTime**.

      ![Wprowadzanie tekstu tematu i wyrażenia zwracającego czas podróży](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-travelTime-variable.png)

1. W polu **Treść** określ treść wiadomości e-mail.

   1. Wprowadź tekst `Add extra travel time (minutes):` ze spacją końcową.

   1. Z listy zawartość dynamiczna wybierz pozycję **wyrażenie**.

      ![Tworzenie wyrażenia dla treści wiadomości e-mail](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings.png)

   1. W edytorze wyrażeń wprowadź następujące wyrażenie, aby obliczyć, o ile minut przekroczony został limit: ```sub(,15)```

      ![Wprowadzanie wyrażenia w celu obliczenia dodatkowego czasu podróży w minutach](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-2.png)

   1. Umieść kursor wewnątrz wyrażenia między lewym nawiasem (**(**) a przecinkiem (**,**). Wybierz pozycję **zawartość dynamiczna**.

      ![Kontynuacja tworzenia wyrażenia do obliczania dodatkowego czasu podróży w minutach](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-3.png)

   1. W obszarze **Zmienne** wybierz pozycję **travelTime**.

      ![Wybierz właściwość "travelTime" do użycia w wyrażeniu](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-4.png)

   1. Po rozpoznaniu właściwości wewnątrz wyrażenia wybierz **przycisk OK**.

      ![Po rozpoznaniu właściwości "Body" Wybierz pozycję "OK"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-5.png)

      Teraz zostanie wyświetlona Właściwość **Body** , jak pokazano poniżej:

      ![Rozpoznano Właściwość "Body" w wyrażeniu](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-6.png)

1. Zapisz aplikację logiki.

Następnie przetestuj aplikację logiki, która powinna wyglądać następująco:

![Zakończono przykładowy przepływ pracy aplikacji logiki](./media/tutorial-build-scheduled-recurring-logic-app-workflow/check-travel-time-finished.png)

## <a name="run-your-logic-app"></a>Uruchamianie aplikacji logiki

Aby ręcznie uruchomić aplikację logiki, na pasku narzędzi projektanta wybierz pozycję **Uruchom**.

* Jeśli bieżący czas podróży pozostaje poniżej limitu, aplikacja logiki nie wykonuje żadnych innych czynności i czeka lub następnego interwału przed ponownym sprawdzeniem. 

* Jeśli bieżący czas podróży przekracza limit, otrzymasz wiadomość e-mail z bieżącym czasem podróży i liczbą minut powyżej limitu. Oto przykładowa wiadomość e-mail wysłana przez aplikację logiki:

![Przykład wysłanych wiadomości e-mail pokazujących czas podróży](./media/tutorial-build-scheduled-recurring-logic-app-workflow/received-example-email-notification.png)

Jeśli nie dostaniesz żadnych wiadomości e-mail, sprawdź folder wiadomości-śmieci. Filtr wiadomości-śmieci Twojej poczty e-mail może przekierowywać tego rodzaju wiadomości. W przeciwnym razie, jeśli nie masz pewności, czy aplikacja logiki została poprawnie uruchomiona, zobacz [Troubleshoot your logic app (Rozwiązywania problemów z aplikacją logiki)](../logic-apps/logic-apps-diagnosing-failures.md).

Gratulacje, udało Ci się utworzyć i uruchomić cykliczną aplikację logiki z użyciem harmonogramu. 

Aby utworzyć inne aplikacje logiki, które używają wyzwalacza **cykl** , zapoznaj się z tymi szablonami, które są dostępne po utworzeniu aplikacji logiki:

* Otrzymywanie codziennych przypomnień pocztą e-mail.
* Usuwanie starszych obiektów blob na platformie Azure.
* Dodawanie komunikatu do kolejki usługi Azure Storage.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli przykładowa aplikacja logiki nie jest już potrzebna, Usuń grupę zasobów zawierającą aplikację logiki i powiązane zasoby. 

1. W menu głównym platformy Azure przejdź do pozycji **Grupy zasobów** i wybierz grupę zasobów aplikacji logiki.

1. W menu Grupa zasobów wybierz pozycję **Przegląd**  >  **Usuń grupę zasobów**. 

   ![„Omówienie” > „Usuń grupę zasobów”](./media/tutorial-build-scheduled-recurring-logic-app-workflow/delete-resource-group.png)

1. Wprowadź nazwę grupy zasobów jako potwierdzenie, a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

Podczas pracy z tym samouczkiem utworzono aplikację logiki, która sprawdza natężenie ruchu na podstawie określonego harmonogramu (codziennie rano) i podejmuje działania (wysyła wiadomości e-mail), gdy czas podróży przekracza określony limit. Możesz teraz dowiedzieć się, w jaki sposób utworzyć aplikację logiki, która wysyła żądania listy adresowej do zatwierdzenia poprzez zintegrowanie usług platformy Azure, usług firmy Microsoft i innych aplikacji SaaS.

> [!div class="nextstepaction"]
> [Manage mailing list requests (Zarządzanie żądaniami listy adresowej)](../logic-apps/tutorial-process-mailing-list-subscriptions-workflow.md)
