---
title: Tworzenie przepływów pracy automatyzacji opartych na harmonogramie za pomocą platformy Azure
description: Samouczek — tworzenie cyklicznego przepływu pracy automatyzacji opartego na harmonogramie, który integruje się między usługami w chmurze przy użyciu Azure Logic Apps.
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/24/2021
ms.openlocfilehash: 0dab2f49043f3a59dd4f06633e3f0228fc39a424
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107792097"
---
# <a name="tutorial-create-schedule-based-and-recurring-automation-workflows-with-azure-logic-apps"></a>Samouczek: tworzenie cyklicznych i opartych na harmonogramie przepływów pracy automatyzacji przy użyciu Azure Logic Apps

W tym samouczku pokazano, jak utworzyć przykładową aplikację [logiki,](../logic-apps/logic-apps-overview.md) która automatyzuje przepływ pracy uruchamiany zgodnie z harmonogramem cyklicznym. W szczególności ta przykładowa aplikacja logiki sprawdza czas podróży, w tym ruch, między dwoma miejscami i jest uruchamiany codziennie rano. Jeśli czas przekracza określony limit, aplikacja logiki wysyła wiadomość e-mail, która zawiera czas podróży i dodatkowy czas niezbędny do dotarć do miejsca docelowego. Przepływ pracy obejmuje różne kroki, które rozpoczynają się od wyzwalacza opartego na harmonogramie, po którym następuje akcja mapy Bing, akcja operacji na danych, akcja przepływu sterowania i akcja powiadomienia e-mail.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenia pustej aplikacji logiki.
> * Dodaj wyzwalacz Cyklicznie, który określa harmonogram aplikacji logiki.
> * Dodaj akcję usługi Mapy Bing, która pobiera czas podróży dla trasy.
> * Dodaj akcję, która tworzy zmienną, konwertuje czas podróży z sekund na minuty i przechowuje wynik tej zmiennej.
> * Dodawanie warunku, który porównuje czas podróży z określonym limitem.
> * Dodaj akcję, która wysyła wiadomość e-mail, jeśli czas podróży przekracza limit.

Po ukończeniu aplikacja logiki będzie ogólnie wyglądać jak ten przepływ pracy:

![Zrzut ekranu przedstawiający ogólne omówienie przykładowego przepływu pracy aplikacji logiki.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/check-travel-time-overview.png)

## <a name="prerequisites"></a>Wymagania wstępne

* Konto i subskrypcja platformy Azure. Jeśli nie masz subskrypcji, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* Konto e-mail od dostawcy poczty e-mail obsługiwane przez usługę Logic Apps, takie jak Office 365 Outlook, Outlook.com lub Gmail. W przypadku innych dostawców [przejrzyj tę listę łączników](/connectors/). W tym przewodniku Szybki start jest używana usługa Office 365 Outlook z kontem służbowym. Jeśli używasz innego konta e-mail, ogólne kroki pozostają takie same, ale interfejs użytkownika może się nieco różnić.

  > [!IMPORTANT]
  > Jeśli chcesz używać łącznika usługi Gmail, tylko konta biznesowe usługi G-Suite mogą używać tego łącznika bez ograniczeń w aplikacjach logiki. Jeśli masz konto użytkownika usługi Gmail, możesz używać tego łącznika tylko z określonymi usługami zatwierdzonymi przez Google lub utworzyć aplikację kliency Google, która będzie używać do uwierzytelniania za pomocą łącznika [usługi Gmail.](/connectors/gmail/#authentication-and-bring-your-own-application) Aby uzyskać więcej informacji, zobacz [Zasady zabezpieczeń danych i ochrony prywatności dla łączników Google](../connectors/connectors-google-data-security-privacy-policy.md)w Azure Logic Apps .

* Aby pobrać czas podróży dla danej trasy, potrzebny jest klucz dostępu dla interfejsu API usługi Mapy Bing. Aby pobrać ten klucz, postępuj zgodnie z instrukcjami [uzyskiwania klucza usługi Mapy Bing](/bingmaps/getting-started/bing-maps-dev-center-help/getting-a-bing-maps-key).

* Jeśli aplikacja logiki musi komunikować się za pośrednictwem zapory, która ogranicza  ruch [](logic-apps-limits-and-config.md#inbound) do określonych adresów IP, musi zezwalać na dostęp zarówno dla przychodzących, jak i wychodzących adresów [IP](logic-apps-limits-and-config.md#outbound) używanych przez usługę Logic Apps lub środowisko uruchomieniowe w regionie świadczenia usługi Azure, w którym istnieje aplikacja logiki. Jeśli aplikacja logiki używa również łączników zarządzanych, takich jak łącznik office 365 Outlook lub łącznik  SQL, lub łączników niestandardowych, [](/connectors/custom-connectors/)zapora musi również zezwalać na dostęp dla wszystkich wychodzących adresów [IP](logic-apps-limits-and-config.md#outbound) łącznika zarządzanego w regionie platformy Azure aplikacji logiki. [](../connectors/managed.md)

## <a name="create-your-logic-app"></a>Tworzenie aplikacji logiki

1. Zaloguj się do [witryny Azure Portal](https://portal.azure.com) przy użyciu poświadczeń konta Azure. Na stronie głównej platformy Azure wybierz **pozycję Utwórz zasób.**

1. W menu Azure Marketplace integration logic app (Integracja **aplikacji**  >  **logiki).**

   ![Zrzut ekranu przedstawiający Azure Marketplace z wybranymi opcjami "Integracja" i "Aplikacja logiki".](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-new-logic-app-resource.png)

1. W **okienku Aplikacja logiki** podaj informacje opisane tutaj dotyczące aplikacji logiki, którą chcesz utworzyć.

   ![Zrzut ekranu przedstawiający okienko tworzenia aplikacji logiki i informacje, które należy podać dla nowej aplikacji logiki.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-logic-app-settings.png)

   | Właściwość | Wartość | Opis |
   |----------|-------|-------------|
   | **Subskrypcja** | <*Nazwa subskrypcji platformy Azure*> | Twoja nazwa subskrypcji platformy Azure. W tym przykładzie użyto wartości `Pay-As-You-Go`. |
   | **Grupa zasobów** | LA-TravelTime-RG | Nazwa grupy zasobów [platformy Azure](../azure-resource-manager/management/overview.md), która służy do organizowania powiązanych zasobów. Ten przykład tworzy nową grupę zasobów o nazwie `LA-TravelTime-RG` . |
   | **Nazwa** | LA-TravelTime | Nazwa aplikacji logiki, która może zawierać tylko litery, cyfry, łączniki ( ), podkreślenia `-` ( `_` ), nawiasy ( , ) i `(` `)` kropki ( `.` ). W tym przykładzie użyto wartości `LA-TravelTime`. |
   | **Lokalizacja** | Zachodnie stany USA | Region, w którym mają być przechowywane informacje o aplikacji logiki. W tym przykładzie użyto wartości `West US`. |
   | **Log Analytics** | Wyłączone | Ustawienie **Wyłączone** umożliwia rejestrowanie w celach diagnostycznych. |
   ||||

1. Gdy wszystko będzie gotowe, wybierz pozycję **Przejrzyj i utwórz**. Po zweryfikowaniu informacji o aplikacji logiki przez platformę Azure wybierz pozycję **Utwórz**.

1. Po wdrożeniu aplikacji na platformie Azure wybierz **pozycję Przejdź do zasobu.**

   Platforma Azure otwiera Logic Apps wyboru szablonu, w którym przedstawiono wprowadzenie wideo, często używane wyzwalacze i wzorce szablonów aplikacji logiki.

1. Przewiń w dół po sekcji wideo i typowych wyzwalaczy do sekcji **Szablony,** a następnie wybierz pozycję **Pusta aplikacja logiki.**

   ![Zrzut ekranu przedstawiający okienko wyboru Logic Apps z wybraną pustą aplikacją logiki.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-logic-app-template.png)

Następnie dodaj wyzwalacz [Cykl,](../logic-apps/logic-apps-overview.md#logic-app-concepts)który uruchamia przepływ pracy zgodnie z określonym harmonogramem. Każda aplikacja logiki musi rozpoczynać się od wyzwalacza, który jest aktywowany w momencie wystąpienia konkretnego zdarzenia lub spełnienia określonego warunku przez nowe dane. Aby uzyskać więcej informacji, zobacz [Tworzenie pierwszej aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-the-recurrence-trigger"></a>Dodawanie wyzwalacza Cyklicznie

1. W polu wyszukiwania Logic Apps Designer wprowadź i wybierz wyzwalacz `recurrence` o nazwie **Cykl.**

   ![Zrzut ekranu przedstawiający pole Logic Apps Designer zawierające wyszukiwany termin "cykl". Na liście "Wyzwalacze" zostanie wyświetlony wybrany wyzwalacz "Cykl".](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-schedule-recurrence-trigger.png)

1. Na kształcie **Cykl** wybierz przycisk **wielokropka** (**...**), a następnie wybierz pozycję **Zmień nazwę**. Zmień nazwę wyzwalacza na następujący opis: `Check travel time every weekday morning`

   ![Zrzut ekranu przedstawiający wybrany przycisk wielokropka, otwartą listę "Ustawienia" i wybrane polecenie "Zmień nazwę".](./media/tutorial-build-scheduled-recurring-logic-app-workflow/rename-recurrence-schedule-trigger.png)

1. Wewnątrz wyzwalacza zmień te właściwości zgodnie z opisem i pokazanym tutaj.

   ![Zrzut ekranu przedstawiający zmiany interwału i częstotliwości wyzwalacza.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/change-interval-frequency.png)

   | Właściwość | Wymagany | Wartość | Opis |
   |----------|----------|-------|-------------|
   | **Interwał** | Tak | 1 | Liczba interwałów do odczekania między sprawdzaniami |
   | **Częstotliwość** | Tak | Tydzień | Jednostka czasu cyklu |
   |||||

1. W **obszarze** **Interwał i Częstotliwość** otwórz listę Dodaj nowy **parametr** i wybierz te właściwości, aby dodać je do wyzwalacza.

   * **W tych dniach**
   * **W tych godzinach**
   * **W tych minutach**

   ![Zrzut ekranu przedstawiający otwartą listę "Dodaj nowy parametr" i wybrane właściwości: "W tych dniach", "O tych godzinach" i "O tych minutach".](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-trigger-properties.png)

1. Teraz ustaw wartości dodatkowych właściwości, jak pokazano i opisano tutaj.

   ![Zrzut ekranu przedstawiający dodatkowe właściwości ustawione na wartości zgodnie z opisem w poniższej tabeli.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/recurrence-trigger-property-values.png)

   | Właściwość | Wartość | Opis |
   |----------|-------|-------------|
   | **W tych dniach** | poniedziałek,wtorek,środa,czwartek,piątek | To ustawienie jest dostępne tylko wtedy, gdy ustawiono **częstotliwość** na **tydzień**. |
   | **W tych godzinach** | 7,8,9 | To ustawienie jest dostępne tylko w przypadku ustawienia **częstotliwości** **na tydzień lub** **dzień**. W przypadku tego cyklu wybierz godziny dnia. Ten przykład jest uruchamiany w `7` znacznikach `8` , i `9` -hour. |
   | **W tych minutach** | 0,15,30,45 | To ustawienie jest dostępne tylko w przypadku ustawienia **częstotliwości** **na tydzień lub** **dzień**. W przypadku tego cyklu wybierz minuty dnia. Ten przykład rozpoczyna się od znaku zerogodzinnego i jest uruchamiany co 15 minut. |
   ||||

   Ten wyzwalacz jest uruchamiany codziennie, co 15 minut, po raz pierwszy o 7:00 i po raz ostatni o 9:45. W oknie **Podgląd** wyświetlany jest harmonogram cyklu. Aby uzyskać więcej informacji, zobacz [Schedule tasks and workflows that run regularly (Planowanie regularnie uruchamianych zadań i przepływów pracy)](../connectors/connectors-native-recurrence.md) i [Workflow actions and triggers (Akcje i wyzwalacze przepływu pracy)](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger).

1. Aby na razie ukryć szczegóły wyzwalacza, zwiń kształt, klikając wewnątrz paska tytułu kształtu.

   ![Zrzut ekranu przedstawiający zwinięty kształt wyzwalacza.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/collapse-trigger-shape.png)

1. Zapisz aplikację logiki. Na pasku narzędzi projektanta wybierz pozycję **Zapisz.**

Aplikacja logiki jest teraz żywa w Azure Portal ale nie robi niczego poza wyzwalaczem zgodnie z określonym harmonogramem. Dodaj więc akcję reagującą na aktywowanie wyzwalacza.

## <a name="get-the-travel-time-for-a-route"></a>Pobieranie czasu podróży dla trasy

Po utworzeniu wyzwalacza możesz dodać [akcję](../logic-apps/logic-apps-overview.md#logic-app-concepts), która umożliwi pobranie czasu podróży pomiędzy dwoma miejscami. Usługa Logic Apps oferuje łącznik dla interfejsu API usługi Mapy Bing, dzięki czemu można łatwo uzyskać te informacje. Przed rozpoczęciem tego zadania upewnij się, że masz klucz interfejsu API usługi Mapy Bing, zgodnie z wymaganiami wstępnymi opisanymi w tym samouczku.

1. W Projektancie aplikacji logiki w obszarze wyzwalacza Cykl wybierz **pozycję Nowy krok.**

1. W **obszarze Wybierz operację** wybierz pozycję **Standardowa.** W polu wyszukiwania wprowadź `bing maps` i wybierz akcję o nazwie **Pobierz trasę**.

   ![Zrzut ekranu przedstawiający listę "Wybierz operację" filtrowaną według akcji "mapy bing" i wybraną akcję "Pobierz trasę".](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-get-route-action.png)

1. Jeśli nie masz połączenia z usługą Mapy Bing, zostanie wyświetlony monit o utworzenie połączenia. Podaj szczegóły połączenia, jak pokazano i opisano, a następnie wybierz pozycję **Utwórz.**

   ![Zrzut ekranu przedstawiający pole połączenia usługi Mapy Bing z określoną nazwą połączenia i kluczem interfejsu API usługi Mapy Bing.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-maps-connection.png)

   | Właściwość | Wymagany | Wartość | Opis |
   |----------|----------|-------|-------------|
   | **Nazwa połączenia** | Tak | BingMapsConnection | Podaj nazwę połączenia. W tym przykładzie użyto wartości `BingMapsConnection`. |
   | **Klucz interfejsu API** | Tak | <*Klucz interfejsu API usługi Mapy Bing*> | Wprowadź otrzymany wcześniej klucz interfejsu API usługi Mapy Bing. Jeśli nie masz klucza usługi Mapy Bing, dowiedz się [jak uzyskać klucz](/bingmaps/getting-started/bing-maps-dev-center-help/getting-a-bing-maps-key). |
   |||||

1. Zmień nazwę akcji na ten opis: `Get route and travel time with traffic` .

1. W akcji otwórz listę **Dodaj nowy parametr** i wybierz te właściwości.

   * **Optymalizacji**
   * **Jednostka odległości**
   * **Tryb podróży**

   ![Zrzut ekranu przedstawiający "Pobierz trasę..." z wybranymi właściwościami "Optimize", "Distance unit" (Jednostka odległości) i "Travel mode" (Tryb podróży).](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-bing-maps-action-properties.png) 

1. Teraz wprowadź wartości właściwości pokazanych i opisanych tutaj.

   ![Zrzut ekranu przedstawiający dodatkowe wartości właściwości dla akcji "Pobierz trasę".](./media/tutorial-build-scheduled-recurring-logic-app-workflow/get-route-action-settings.png) 

   | Właściwość | Wymagany | Wartość | Opis |
   |----------|----------|-------|-------------|
   | **Punkt nawigacyjny 1** | Tak | <*lokalizacja rozpoczęcia*> | Źródło trasy. W tym przykładzie określono przykładowy adres początkowy. |
   | **Punkt nawigacyjny 2** | Tak | <*lokalizacja końcowa*> | Miejsce docelowe trasy. W tym przykładzie określono przykładowy adres docelowy. |
   | **Optymalizacji** | Nie | timeWithTraffic | Parametr używany do optymalizowania trasy, na przykład odległość, czas podróży z uwzględnieniem aktualnego natężenia ruchu i tak dalej. Wybierz wartość parametru **timeWithTraffic.** |
   | **Jednostka odległości** | Nie | <*preferencje użytkownika*> | Jednostka odległości trasy. W tym przykładzie **użyto Mile** jako jednostki. |
   | **Tryb podróży** | Nie | Jazda samochodem | Tryb podróży dla trasy. Wybierz **pozycję Tryb** jazdy. |
   |||||

   Aby uzyskać więcej informacji na temat tych parametrów i wartości, zobacz [Obliczanie trasy](/bingmaps/rest-services/routes/calculate-a-route).

1. Na pasku narzędzi projektanta wybierz pozycję **Zapisz.**

Następnie należy utworzyć zmienną, która umożliwia przekonwertowanie aktualnego czasu podróży i zapisanie go w minutach, a nie sekundach. Dzięki temu można uniknąć powtarzania konwersji i łatwiej używać tej wartości w kolejnych krokach. 

## <a name="create-a-variable-to-store-travel-time"></a>Tworzenie zmiennej do przechowywania czasu podróży

Czasami można uruchamiać operacje na danych w przepływie pracy, a następnie używać wyników w późniejszych akcjach. Aby zapisać te wyniki w celu łatwego ponownego użycia lub odwołania się do nich, można utworzyć zmienne, które przechowują te wyniki po przetworzeniu. Zmienne można utworzyć tylko na najwyższym poziomie w aplikacji logiki.

Domyślnie akcja Pobierz **trasę** zwraca bieżący czas podróży z ruchem w sekundach z właściwości **Czas** trwania podróży Ruch. Dzięki przekonwertowaniu tej wartości i zapisaniu jej w minutach łatwiej jest użyć jej ponownie później bez konieczności ponownego konwertowania.

1. W projektancie w obszarze akcji **Pobierz trasę** wybierz **pozycję Nowy krok.**

1. W **obszarze Wybierz operację** wybierz pozycję **Wbudowany**. W polu wyszukiwania wprowadź `variables` i wybierz akcję o nazwie **Inicjuj zmienną**.

   ![Zrzut ekranu przedstawiający wybraną akcję "Inicjowanie zmiennej".](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-initialize-variable-action.png)

1. Zmień nazwę akcji na następujący opis: `Create variable to store travel time`

1. Podaj te informacje dla zmiennej, jak pokazano w tej tabeli i w krokach poniżej tabeli:

   | Właściwość | Wymagany | Wartość | Opis |
   |----------|----------|-------|-------------|
   | **Nazwa** | Tak | travelTime | Nazwa zmiennej. W tym przykładzie użyto wartości `travelTime`. |
   | **Typ** | Tak | Liczba całkowita | Typ danych dla zmiennej |
   | **Wartość** | Nie | Wyrażenie, które konwertuje bieżący czas podróży z sekund na minuty (zobacz kroki w tej tabeli). | Początkowa wartość zmiennej |
   |||||

   1. Aby utworzyć wyrażenie dla właściwości **Value,** kliknij wewnątrz pola, aby wyświetlić listę zawartości dynamicznej. W razie potrzeby poszerz przeglądarkę, aż pojawi się lista dynamiczna. Na liście zawartości dynamicznej wybierz pozycję **Wyrażenie**, co pokazuje edytor wyrażeń.

      ![Zrzut ekranu przedstawiający akcję "Inicjuj zmienną" z kursorem wewnątrz właściwości "Value", która otwiera listę zawartości dynamicznej.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings.png)

      Dynamiczna lista zawartości zawiera dane wyjściowe z poprzednich akcji, które można wybrać jako dane wejściowe kolejnych akcji w przepływie pracy. Lista zawartości dynamicznej zawiera edytor wyrażeń, za pomocą których można wybierać funkcje, które wykonują operacje w wyrażeniu. Ten edytor wyrażeń jest dostępny tylko na liście zawartości dynamicznej.

   1. W edytorze wyrażeń wprowadź wyrażenie: `div(,60)`

      ![Zrzut ekranu przedstawiający edytor wyrażeń z wprowadzonym wyrażeniem "div(,60)".](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-2.png)

   1. W wyrażeniu umieść kursor między lewym nawiasem **()** i przecinkiem (**,**), a następnie wybierz **pozycję Zawartość dynamiczna**.

      ![Zrzut ekranu przedstawiający miejsce, w którym należy umieścić kursor w wyrażeniu "div(,60)" z wybraną dynamiczną zawartością.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-3.png)

   1. Na liście zawartości dynamicznej w obszarze wybierz wartość właściwości Czas trwania podróży **Ruch**.

      ![Zrzut ekranu przedstawiający wybraną wartość właściwości "Ruch czas trwania podróży".](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-4.png)

   1. Po rozpoznaniu wartości właściwości wewnątrz wyrażenia wybierz przycisk **OK**.

      ![Zrzut ekranu przedstawiający wybrany przycisk "OK".](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-5.png)

      Właściwość **Value** jest teraz wyświetlana tak, jak pokazano poniżej:

      ![Zrzut ekranu przedstawiający właściwość "Value" z rozpoznanym wyrażeniem.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-6.png)

1. Zapisz aplikację logiki.

Następnie dodaj warunek, który sprawdzi, czy aktualny czas podróży jest dłuży niż określony limit.

## <a name="compare-the-travel-time-with-limit"></a>Porównywanie czasu podróży z limitem

1. W obszarze **akcji Utwórz zmienną do przechowywania** czasu podróży wybierz pozycję Nowy **krok**.

1. W **obszarze Wybierz operację** wybierz **pozycję Wbudowany**. W polu wyszukiwania wpisz `condition`. z listy akcji wybierz akcję o nazwie **Warunek**.

   ![Zrzut ekranu przedstawiający wybraną akcję "Warunek"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-condition-action.png)

1. Zmień nazwę warunku na następujący opis: `If travel time exceeds limit`

1. Skompilowanie warunku, który sprawdza, czy wartość **właściwości travelTime** przekracza określony limit, zgodnie z opisem i pokazanym tutaj:

   1. W warunku po lewej stronie warunku kliknij wewnątrz pola **Wybierz** wartość.

   1. Z wyświetlonej listy zawartości dynamicznej w **obszarze Zmienne** wybierz właściwość o nazwie **travelTime.**

      ![Zrzut ekranu przedstawiający pole "Wybierz wartość" po lewej stronie warunku z otwartą listą zawartości dynamicznej i wybraną właściwością "travelTime".](./media/tutorial-build-scheduled-recurring-logic-app-workflow/build-condition-left-side.png)

   1. W środkowym polu porównania wybierz operator o nazwie **jest większy niż**.

   1. Po prawej stronie warunku w **polu Wybierz wartość** wprowadź ten limit: `15`

      Gdy wszystko będzie gotowe, warunek będzie wyglądać podobnie do tego przykładu:

      ![Zrzut ekranu przedstawiający gotowy warunek porównywania czasu podróży z określonym limitem.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/build-condition-check-travel-time.png)

1. Zapisz aplikację logiki.

Następnie dodaj akcję, która ma być uruchamiana, gdy czas podróży przekroczy limit.

## <a name="send-email-when-limit-exceeded"></a>Wysyłanie wiadomości e-mail po przekroczeniu limitu

Teraz dodaj akcję, która wyśle Ci wiadomość e-mail, gdy czas podróży przekroczy limit. Ta wiadomość e-mail zawiera aktualny czas podróży oraz dodatkowy czas niezbędny do pokonania określonej trasy.

1. W gałęzi True **warunku** wybierz pozycję **Dodaj akcję**.

1. W **obszarze Wybierz operację** wybierz pozycję **Standardowa.** W polu wyszukiwania wpisz `send email`. Lista zwraca wiele wyników, dlatego aby ułatwić filtrowanie listy, najpierw wybierz łącznik poczty e-mail.

   Jeśli na przykład masz konto e-mail programu Outlook, wybierz łącznik dla typu konta:

   * W przypadku kont służbowych platformy Azure wybierz pozycję **Office 365 Outlook**.
   * W przypadku osobistych kont Microsoft wybierz pozycję **Outlook.com**.

   Ten przykład jest kontynuowany przez wybranie usługi Office 365 Outlook.

   ![Zrzut ekranu przedstawiający listę "Wybierz listę operacji" z wybraną kategorią "Standardowa" i łącznikiem "Office 365 Outlook".](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-action-send-email.png)

1. Gdy pojawią się akcje łącznika, wybierz akcję, która wysyła wiadomość e-mail, na przykład:

   ![Zrzut ekranu przedstawiający wybraną akcję "Wyślij wiadomość e-mail".](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-send-email-action.png)

1. Jeśli nie masz jeszcze połączenia, po wyświetleniu monitu zaloguj się i uwierzytelnij dostęp do swojego konta e-mail.

   Azure Logic Apps tworzy połączenie z kontem e-mail.

1. Zmień nazwę akcji na następujący opis: `Send email with travel time`

1. W przypadku **właściwości Do** wprowadź adres e-mail adresata. Do celów testowych możesz użyć swojego adresu e-mail.

1. Dla właściwości **Temat** określ temat wiadomości e-mail i dołącz **zmienną travelTime,** wykonać następujące kroki:

   1. Wprowadź tekst `Current travel time (minutes):` ze spacją końcową. Umieść kursor w polu **Temat,** aby lista zawartości dynamicznej pozostawała otwarta.

   1. Z listy zawartości dynamicznej w **nagłówku** Zmienne wybierz pozycję **Zobacz** więcej, aby pojawiła się zmienna o nazwie **travelTime.**

      ![Zrzut ekranu przedstawiający listę zawartości dynamicznej z wybraną sekcją "Zmienne" i "Zobacz więcej".](./media/tutorial-build-scheduled-recurring-logic-app-workflow/find-travelTime-variable.png)

      > [!NOTE]
      > Dynamiczna lista zawartości nie wyświetla automatycznie zmiennej **travelTime,** ponieważ właściwość **Subject** oczekuje wartości ciągu, a **travelTime** jest wartością całkowitą.

      ![Zrzut ekranu przedstawiający listę zawartości dynamicznej z wybraną zmienną "travelTime".](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-travelTime-variable.png)

1. Dla właściwości **Treść** określ zawartość treści wiadomości e-mail, wykonać następujące kroki:

   1. Wprowadź tekst `Add extra travel time (minutes):` ze spacją końcową. Umieść kursor w polu **Treść,** aby lista zawartości dynamicznej pozostawała otwarta.

   1. Na liście zawartości dynamicznej wybierz pozycję **Wyrażenie**, co pokazuje edytor wyrażeń.

      ![Zrzut ekranu przedstawiający dynamiczną listę zawartości z wybraną elekcją "Wyrażenie".](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings.png)

   1. W edytorze wyrażeń wprowadź wartość , aby można `sub(,15)` było obliczyć liczbę minut, które przekraczają limit: 

      ![Zrzut ekranu przedstawiający edytor wyrażeń z wprowadzonym wyrażeniem "sub(,15)".](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-2.png)

   1. W wyrażeniu umieść kursor między lewym nawiasem **()** i przecinkiem (**,**), a następnie wybierz **pozycję Zawartość dynamiczna**.

      ![Zrzut ekranu przedstawiający miejsce, w którym należy umieścić kursor w wyrażeniu "sub(,15)" z wybraną dynamiczną zawartością.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-3.png)

   1. W obszarze **Zmienne** wybierz pozycję **travelTime**.

      ![Zrzut ekranu przedstawiający listę zawartości dynamicznej z wybraną zmienną "travelTime".](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-4.png)

   1. Po rozpoznaniu właściwości wewnątrz wyrażenia wybierz przycisk **OK**.

      ![Zrzut ekranu przedstawiający listę zawartości dynamicznej i wybraną pozycję "OK".](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-5.png)

      Właściwość **Body** jest teraz wyświetlana tak, jak pokazano poniżej:

      ![Zrzut ekranu przedstawiający dynamiczną listę zawartości z wyrażeniem rozpoznanym we właściwości "Treść" akcji wiadomości e-mail.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-6.png)

1. Zapisz aplikację logiki.

Następnie przetestuj i uruchom aplikację logiki, która teraz wygląda podobnie do tego przykładu:

![Zrzut ekranu przedstawiający gotowy przykładowy przepływ pracy aplikacji logiki](./media/tutorial-build-scheduled-recurring-logic-app-workflow/check-travel-time-finished.png)

## <a name="run-your-logic-app"></a>Uruchamianie aplikacji logiki

Aby ręcznie uruchomić aplikację logiki, na pasku narzędzi projektanta wybierz pozycję **Uruchom**.

* Jeśli bieżący czas podróży pozostaje poniżej limitu, aplikacja logiki nie robi nic więcej i czeka lub następny interwał przed sprawdzeniem ponownie. 

* Jeśli bieżący czas podróży przekracza limit, otrzymasz wiadomość e-mail z aktualnym czasem podróży i liczbą minut przekraczających limit. Oto przykładowa wiadomość e-mail wysłana przez aplikację logiki:

  ![Zrzut ekranu przedstawiający przykładową wiadomość e-mail z raportem o bieżącym czasie podróży i dodatkowym czasie podróży, który przekracza określony limit.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/received-example-email-notification.png)

  > [!TIP]
  > Jeśli nie dostaniesz żadnych wiadomości e-mail, sprawdź folder wiadomości-śmieci. Filtr wiadomości-śmieci Twojej poczty e-mail może przekierowywać tego rodzaju wiadomości. W przeciwnym razie, jeśli nie masz pewności, czy aplikacja logiki została poprawnie uruchomiona, zobacz [Troubleshoot your logic app (Rozwiązywania problemów z aplikacją logiki)](../logic-apps/logic-apps-diagnosing-failures.md).

Gratulacje, udało Ci się utworzyć i uruchomić cykliczną aplikację logiki z użyciem harmonogramu. 

Aby utworzyć inne aplikacje logiki, które używają **wyzwalacza Cykl,** zapoznaj się z tymi szablonami, które są dostępne po utworzeniu aplikacji logiki:

* Otrzymywanie codziennych przypomnień pocztą e-mail.
* Usuwanie starszych obiektów blob na platformie Azure.
* Dodawanie komunikatu do kolejki usługi Azure Storage.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Aplikacja logiki będzie nadal działać do momentu wyłączenia lub usunięcia aplikacji. Gdy przykładowa aplikacja logiki nie jest już potrzebna, usuń grupę zasobów zawierającą aplikację logiki i powiązane zasoby.

1. W Azure Portal wyszukiwania aplikacji wprowadź nazwę utworzonej grupy zasobów. W wynikach w obszarze **Grupy zasobów** wybierz grupę zasobów.

   W tym przykładzie utworzono grupę zasobów o nazwie `LA-TravelTime-RG` .

   ![Zrzut ekranu przedstawiający pole wyszukiwania platformy Azure z wpisaną wartością "la-travel-time-rg" i wybraną oknie **LA-TravelTime-RG**.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/find-resource-group.png)

   > [!TIP]
   > Jeśli na stronie głównej platformy Azure zostanie pokazana grupa zasobów w obszarze **Ostatnie zasoby,** możesz wybrać tę grupę na stronie głównej.

1. W menu grupy zasobów sprawdź, czy **wybrano pozycję** Przegląd. Na pasku **narzędzi** okienka Przegląd wybierz pozycję **Usuń grupę zasobów.**

   ![Zrzut ekranu przedstawiający okienko "Przegląd" grupy zasobów, na pasku narzędzi okienka jest zaznaczona opcja "Usuń grupę zasobów".](./media/tutorial-build-scheduled-recurring-logic-app-workflow/delete-resource-group.png)

1. W wyświetlonym okienku potwierdzenia wprowadź nazwę grupy zasobów, a następnie wybierz pozycję **Usuń.**

## <a name="next-steps"></a>Następne kroki

W tym samouczku utworzono aplikację logiki, która sprawdza ruch na podstawie określonego harmonogramu (w dni robocze rano) i podejmuje działania (wysyła wiadomość e-mail), gdy czas podróży przekracza określony limit. Teraz dowiesz się, jak utworzyć aplikację logiki, która wysyła żądania listy adresowej do zatwierdzenia przez integrację usług platformy Azure, usług usługi firmy Microsoft i innych aplikacji typu oprogramowanie jako usługa (SaaS).

> [!div class="nextstepaction"]
> [Manage mailing list requests (Zarządzanie żądaniami listy adresowej)](../logic-apps/tutorial-process-mailing-list-subscriptions-workflow.md)
