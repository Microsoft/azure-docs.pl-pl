---
title: Tworzenie przepływów pracy automatyzacji opartych na harmonogramie przy użyciu platformy Azure
description: Samouczek — Tworzenie cyklicznego przepływu pracy automatyzacji opartego na harmonogramie, który integruje się w usługach w chmurze przy użyciu Azure Logic Apps.
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: tutorial
ms.custom: mvc
ms.date: 09/30/2020
ms.openlocfilehash: 95275e68d0c7674caf4dd2b20f5586db5193fd03
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2021
ms.locfileid: "99054063"
---
# <a name="tutorial-create-schedule-based-and-recurring-automation-workflows-with-azure-logic-apps"></a>Samouczek: Tworzenie przepływów pracy automatyzacji opartych na harmonogramie i cyklicznych za pomocą Azure Logic Apps

W tym samouczku przedstawiono sposób tworzenia przykładowej [aplikacji logiki](../logic-apps/logic-apps-overview.md) , która automatyzuje przepływ pracy uruchamiany zgodnie z cyklicznym harmonogramem. W szczególności Ta przykładowa aplikacja logiki sprawdza czas podróży, w tym ruch, między dwoma miejscami i jest uruchamiany każdego dnia tygodnia rano. Jeśli czas przekracza określony limit, aplikacja logiki wyśle wiadomość e-mail zawierającą czas podróży i dodatkowy czas wymagany do osiągnięcia w miejscu docelowym. Przepływ pracy zawiera różne kroki, które zaczynają się od wyzwalacza opartego na harmonogramie, po którym następuje akcja mapy Bing, Akcja operacji na danych, Akcja przepływu sterowania i akcja powiadomienia e-mail.

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
> * Tworzenia pustej aplikacji logiki.
> * Dodaj wyzwalacz cykliczny określający harmonogram aplikacji logiki.
> * Dodaj akcję mapy Bing, która pobiera czas podróży dla trasy.
> * Dodaj akcję, która tworzy zmienną, konwertuje czas podróży z sekund na minuty i zapisuje wynik w zmiennej.
> * Dodawanie warunku, który porównuje czas podróży z określonym limitem.
> * Dodaj akcję wysyłającą wiadomość e-mail, jeśli czas podróży przekracza limit.

Po ukończeniu aplikacja logiki będzie ogólnie wyglądać jak ten przepływ pracy:

![Zrzut ekranu pokazujący ogólne omówienie przykładowego przepływu pracy aplikacji logiki.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/check-travel-time-overview.png)

## <a name="prerequisites"></a>Wymagania wstępne

* Konto i subskrypcja platformy Azure. Jeśli nie masz subskrypcji, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* Konto e-mail od dostawcy poczty e-mail obsługiwanego przez Logic Apps, takie jak Office 365 Outlook, Outlook.com lub gmail. W przypadku innych dostawców [przejrzyj tę listę łączników](/connectors/). Ten przewodnik Szybki Start korzysta z pakietu Office 365 Outlook z kontem służbowym. Jeśli używasz innego konta e-mail, ogólne kroki pozostają takie same, ale interfejs użytkownika może się nieco różnić.

  > [!IMPORTANT]
  > Jeśli chcesz korzystać z łącznika usługi Gmail, tylko konta firmowe z zestawu G-Suite mogą używać tego łącznika bez ograniczeń w usłudze Logic Apps. Jeśli masz konto użytkownika usługi Gmail, możesz użyć tego łącznika z tylko określonymi usługami zatwierdzonymi przez firmę Google lub możesz [utworzyć aplikację kliencką Google, która będzie używana do uwierzytelniania za pomocą łącznika usługi Gmail](/connectors/gmail/#authentication-and-bring-your-own-application). Aby uzyskać więcej informacji, zobacz [zabezpieczenia danych i zasady ochrony prywatności dla łączników Google w Azure Logic Apps](../connectors/connectors-google-data-security-privacy-policy.md).

* Aby pobrać czas podróży dla danej trasy, potrzebny jest klucz dostępu dla interfejsu API usługi Mapy Bing. Aby pobrać ten klucz, postępuj zgodnie z instrukcjami [uzyskiwania klucza usługi Mapy Bing](/bingmaps/getting-started/bing-maps-dev-center-help/getting-a-bing-maps-key).

* Jeśli aplikacja logiki musi komunikować się przez zaporę, która ogranicza ruch do określonych adresów IP, zapora musi zezwolić na dostęp *zarówno* do [przychodzącego](logic-apps-limits-and-config.md#inbound) , jak i [wychodzącego](logic-apps-limits-and-config.md#outbound) adresu IP używanego przez usługę Logic Apps lub środowisko uruchomieniowe w regionie platformy Azure, w którym znajduje się aplikacja logiki. Jeśli aplikacja logiki używa również łączników [zarządzanych](../connectors/apis-list.md#managed-api-connectors), takich jak łącznik usługi Office 365 Outlook lub łącznik SQL, lub używa [łączników niestandardowych](/connectors/custom-connectors/), zapora musi również zezwolić na dostęp *wszystkich* [wychodzących adresów IP łącznika zarządzanego](logic-apps-limits-and-config.md#outbound) w regionie platformy Azure aplikacji logiki.

## <a name="create-your-logic-app"></a>Tworzenie aplikacji logiki

1. Zaloguj się do [witryny Azure Portal](https://portal.azure.com) przy użyciu poświadczeń konta Azure. Na stronie głównej platformy Azure wybierz pozycję **Utwórz zasób**.

1. W menu portalu Azure Marketplace wybierz pozycję **Integration**  >  **Logic App**.

   ![Zrzut ekranu pokazujący menu portalu Azure Marketplace z wybraną opcją "Integracja" i "aplikacja logiki".](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-new-logic-app-resource.png)

1. W okienku **aplikacja logiki** Podaj tutaj informacje o aplikacji logiki, którą chcesz utworzyć.

   ![Zrzut ekranu pokazujący okienko tworzenia aplikacji logiki oraz informacje, które mają zostać podane dla nowej aplikacji logiki.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-logic-app-settings.png)

   | Właściwość | Wartość | Opis |
   |----------|-------|-------------|
   | **Subskrypcja** | <*Azure — nazwa subskrypcji*> | Nazwa subskrypcji platformy Azure. W tym przykładzie użyto wartości `Pay-As-You-Go`. |
   | **Grupa zasobów** | LA-TravelTime-RG | Nazwa [grupy zasobów platformy Azure](../azure-resource-manager/management/overview.md), która jest używana do organizowania powiązanych zasobów. Ten przykład tworzy nową grupę zasobów o nazwie `LA-TravelTime-RG` . |
   | **Nazwa** | LA-TravelTime | Nazwa aplikacji logiki, która może zawierać tylko litery, cyfry, łączniki ( `-` ), podkreślenia ( `_` ), nawiasy ( `(` , `)` ) i kropki ( `.` ). W tym przykładzie użyto wartości `LA-TravelTime`. |
   | **Lokalizacja** | Zachodnie stany USA | Region, w którym są przechowywane informacje o aplikacji logiki. W tym przykładzie użyto wartości `West US`. |
   | **Log Analytics** | Wyłączone | Ustawienie **Wyłączone** umożliwia rejestrowanie w celach diagnostycznych. |
   ||||

1. Gdy skończysz, wybierz pozycję **Przegląd + Utwórz**. Po sprawdzeniu przez platformę Azure informacji o aplikacji logiki wybierz pozycję **Utwórz**.

1. Po wdrożeniu aplikacji przez platformę Azure wybierz pozycję **Przejdź do zasobu**.

   Na platformie Azure zostanie otwarte okienko wybór szablonów Logic Apps, w którym wyświetlane są filmy wideo z wprowadzeniem, często używane wyzwalacze i wzorce szablonów aplikacji logiki.

1. Przewiń w dół sekcje wideo i typowe wyzwalacze do sekcji **Szablony** , a następnie wybierz pozycję **pusta aplikacja logiki**.

   ![Zrzut ekranu, który pokazuje okienko Logic Apps wyboru szablonów z wybraną opcją "pusta aplikacja logiki".](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-logic-app-template.png)

Następnie Dodaj [wyzwalacz](../logic-apps/logic-apps-overview.md#logic-app-concepts)cykliczny, który uruchamia przepływ pracy zgodnie z określonym harmonogramem. Każda aplikacja logiki musi rozpoczynać się od wyzwalacza, który jest aktywowany w momencie wystąpienia konkretnego zdarzenia lub spełnienia określonego warunku przez nowe dane. Aby uzyskać więcej informacji, zobacz [Tworzenie pierwszej aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-the-recurrence-trigger"></a>Dodaj wyzwalacz cykliczny

1. W polu wyszukiwania programu Logic Apps Designer wprowadź `recurrence` i wybierz wyzwalacz o nazwie **cykl**.

   ![Zrzut ekranu przedstawiający pole wyszukiwania programu Logic Apps Designer zawierające termin wyszukiwania "cykl" i na liście "wyzwalacze" zostanie wybrany wyzwalacz "cykl".](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-schedule-recurrence-trigger.png)

1. Na kształcie **cyklu** wybierz przycisk **wielokropka** (**...**), a następnie wybierz polecenie **Zmień nazwę**. Zmień nazwę wyzwalacza na następujący opis: `Check travel time every weekday morning`

   ![Zrzut ekranu, na którym zostanie wybrany przycisk wielokropka, zostanie otwarta lista "Ustawienia" i wybrane polecenie "Zmień nazwę".](./media/tutorial-build-scheduled-recurring-logic-app-workflow/rename-recurrence-schedule-trigger.png)

1. Wewnątrz wyzwalacza Zmień te właściwości zgodnie z opisem i pokazanym tutaj.

   ![Zrzut ekranu pokazujący zmiany interwału i częstotliwości wyzwalacza.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/change-interval-frequency.png)

   | Właściwość | Wymagany | Wartość | Opis |
   |----------|----------|-------|-------------|
   | **Interwał** | Tak | 1 | Liczba interwałów do odczekania między sprawdzaniami |
   | **Częstotliwość** | Tak | Tydzień | Jednostka czasu cyklu |
   |||||

1. W obszarze **Interwał** i **częstotliwość** Otwórz listę **Dodaj nowy parametr** i wybierz te właściwości, które mają zostać dodane do wyzwalacza.

   * **W tych dniach**
   * **W tych godzinach**
   * **W tych minutach**

   ![Zrzut ekranu pokazujący otwartą listę "Dodaj nowy parametr" i wybrane właściwości: "w tych dniach", "w tych godzinach" i "w tych minutach".](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-trigger-properties.png)

1. Teraz ustaw wartości dla dodatkowych właściwości, jak pokazano i opisano tutaj.

   ![Zrzut ekranu, na którym są wyświetlane dodatkowe właściwości ustawione na wartości, zgodnie z opisem w poniższej tabeli.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/recurrence-trigger-property-values.png)

   | Właściwość | Wartość | Opis |
   |----------|-------|-------------|
   | **W tych dniach** | poniedziałek,wtorek,środa,czwartek,piątek | To ustawienie jest dostępne tylko w przypadku ustawienia **częstotliwości** na **tydzień**. |
   | **W tych godzinach** | 7,8,9 | To ustawienie jest dostępne tylko w przypadku ustawienia **częstotliwości** na **tydzień** lub **dzień**. Dla tego cyklu wybierz godziny dnia. Ten przykład jest uruchamiany w `7` `8` `9` znakach, i-godzinowych. |
   | **W tych minutach** | 0,15,30,45 | To ustawienie jest dostępne tylko w przypadku ustawienia **częstotliwości** na **tydzień** lub **dzień**. Dla tego cyklu wybierz minuty dnia. Ten przykład zaczyna się od znaku 0 godziny i jest uruchamiany co 15 minut. |
   ||||

   Ten wyzwalacz jest uruchamiany codziennie, co 15 minut, po raz pierwszy o 7:00 i po raz ostatni o 9:45. W oknie **Podgląd** wyświetlany jest harmonogram cyklu. Aby uzyskać więcej informacji, zobacz [Schedule tasks and workflows that run regularly (Planowanie regularnie uruchamianych zadań i przepływów pracy)](../connectors/connectors-native-recurrence.md) i [Workflow actions and triggers (Akcje i wyzwalacze przepływu pracy)](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger).

1. Aby ukryć szczegóły wyzwalacza teraz, Zwiń kształt, klikając wewnątrz paska tytułu kształtu.

   ![Zrzut ekranu pokazujący zwinięty kształt wyzwalacza.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/collapse-trigger-shape.png)

1. Zapisz aplikację logiki. Na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

Twoja aplikacja logiki jest teraz aktywna w Azure Portal, ale nie wykonuje żadnych innych czynności niż wyzwalacz oparty na określonym harmonogramie. Dodaj więc akcję reagującą na aktywowanie wyzwalacza.

## <a name="get-the-travel-time-for-a-route"></a>Pobieranie czasu podróży dla trasy

Po utworzeniu wyzwalacza możesz dodać [akcję](../logic-apps/logic-apps-overview.md#logic-app-concepts), która umożliwi pobranie czasu podróży pomiędzy dwoma miejscami. Usługa Logic Apps oferuje łącznik dla interfejsu API usługi Mapy Bing, dzięki czemu można łatwo uzyskać te informacje. Przed rozpoczęciem tego zadania upewnij się, że masz klucz interfejsu API usługi Mapy Bing, zgodnie z wymaganiami wstępnymi opisanymi w tym samouczku.

1. W Projektancie aplikacji logiki w obszarze wyzwalacza cyklu wybierz pozycję **nowy krok**.

1. W obszarze **Wybierz operację** wybierz pozycję **standardowa**. W polu wyszukiwania wprowadź `bing maps` i wybierz akcję o nazwie **Get Route**.

   ![Zrzut ekranu przedstawiający listę "Wybieranie operacji" przefiltrowaną przez akcje "mapy Bing" i wybraną akcję "Pobierz trasę".](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-get-route-action.png)

1. Jeśli nie masz połączenia z usługą Mapy Bing, zostanie wyświetlony monit o utworzenie połączenia. Podaj szczegóły połączenia, jak pokazano i opisano, a następnie wybierz pozycję **Utwórz**.

   ![Zrzut ekranu przedstawiający pole połączenia mapy Bing z określoną nazwą połączenia i kluczem interfejsu API usługi mapy Bing.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-maps-connection.png)

   | Właściwość | Wymagany | Wartość | Opis |
   |----------|----------|-------|-------------|
   | **Nazwa połączenia** | Tak | BingMapsConnection | Podaj nazwę połączenia. W tym przykładzie użyto wartości `BingMapsConnection`. |
   | **Klucz interfejsu API** | Tak | <*Bing-Maps-interfejs API-Key*> | Wprowadź wcześniej otrzymany klucz interfejsu API usługi mapy Bing. Jeśli nie masz klucza usługi Mapy Bing, dowiedz się [jak uzyskać klucz](/bingmaps/getting-started/bing-maps-dev-center-help/getting-a-bing-maps-key). |
   |||||

1. Zmień nazwę akcji na następujący Opis: `Get route and travel time with traffic` .

1. W akcji Otwórz **listę Dodaj nowy parametr** i wybierz te właściwości.

   * **Optymalizacja**
   * **Jednostka odległości**
   * **Tryb podróży**

   ![Zrzut ekranu, który pokazuje "Pobierz trasę..." Akcja z wybranymi właściwościami "Optymalizuj", "Distance Unit" i "Tryb podróży".](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-bing-maps-action-properties.png) 

1. Teraz wprowadź wartości właściwości wyświetlanych i opisanych poniżej.

   ![Zrzut ekranu pokazujący dodatkowe wartości właściwości dla akcji "Pobierz trasę".](./media/tutorial-build-scheduled-recurring-logic-app-workflow/get-route-action-settings.png) 

   | Właściwość | Wymagany | Wartość | Opis |
   |----------|----------|-------|-------------|
   | **Punkt nawigacyjny 1** | Tak | <*lokalizacja początkowa*> | Początek trasy. Ten przykład określa przykładowy adres początkowy. |
   | **Punkt nawigacyjny 2** | Tak | <*Lokalizacja końcowa*> | Miejsce docelowe trasy. Ten przykład określa przykładowy adres docelowy. |
   | **Optymalizacja** | Nie | timeWithTraffic | Parametr używany do optymalizowania trasy, na przykład odległość, czas podróży z uwzględnieniem aktualnego natężenia ruchu i tak dalej. Wybierz wartość parametru **timeWithTraffic**. |
   | **Jednostka odległości** | Nie | <*Twoje preferencje*> | Jednostka odległości trasy. W tym przykładzie **jest stosowana jednostka** . |
   | **Tryb podróży** | Nie | Jazda samochodem | Tryb podróży dla trasy. Wybierz tryb **kierowania** . |
   |||||

   Aby uzyskać więcej informacji na temat tych parametrów i wartości, zobacz [Obliczanie trasy](/bingmaps/rest-services/routes/calculate-a-route).

1. Na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

Następnie należy utworzyć zmienną, która umożliwia przekonwertowanie aktualnego czasu podróży i zapisanie go w minutach, a nie sekundach. Dzięki temu można uniknąć powtarzania konwersji i łatwiej używać tej wartości w kolejnych krokach. 

## <a name="create-a-variable-to-store-travel-time"></a>Utwórz zmienną do przechowywania czasu podróży

Czasami może być konieczne uruchomienie operacji na danych w przepływie pracy, a następnie użycie wyników w późniejszych akcjach. Aby zapisać te wyniki w taki sposób, aby można je było łatwo ponownie wykorzystać lub odwoływać się do nich, można utworzyć zmienne, które przechowują te wyniki po przetworzeniu. Zmienne można utworzyć tylko na najwyższym poziomie w aplikacji logiki.

Domyślnie Akcja **Pobierz trasę** zwraca bieżący czas podróży z ruchem w sekundach od właściwości **ruchu czasu trwania podróży** . Dzięki przekonwertowaniu tej wartości i zapisaniu jej w minutach łatwiej jest użyć jej ponownie później bez konieczności ponownego konwertowania.

1. W projektancie w obszarze Akcja **Pobierz trasę** wybierz pozycję **nowy krok**.

1. W obszarze **Wybierz operację** wybierz pozycję **wbudowane**. W polu wyszukiwania wprowadź `variables` i wybierz akcję o nazwie **Initialize Variable**.

   ![Zrzut ekranu przedstawiający wybraną akcję "zainicjuj zmienną".](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-initialize-variable-action.png)

1. Zmień nazwę akcji na następujący opis: `Create variable to store travel time`

1. Podaj te informacje dla zmiennej, jak pokazano w tej tabeli i w poniższych krokach:

   | Właściwość | Wymagany | Wartość | Opis |
   |----------|----------|-------|-------------|
   | **Nazwa** | Tak | travelTime | Nazwa zmiennej. W tym przykładzie użyto wartości `travelTime`. |
   | **Typ** | Tak | Liczba całkowita | Typ danych dla zmiennej |
   | **Wartość** | Nie | Wyrażenie, które konwertuje bieżący czas podróży z sekund na minuty (zobacz kroki opisane w tej tabeli). | Początkowa wartość zmiennej |
   |||||

   1. Aby utworzyć wyrażenie dla właściwości **Value** , kliknij wewnątrz pola, aby wyświetlić listę zawartości dynamicznej. W razie potrzeby poszerzenie przeglądarki do momentu wyświetlenia listy dynamicznej. Z listy zawartość dynamiczna wybierz pozycję **wyrażenie**, które pokazuje Edytor wyrażeń.

      ![Zrzut ekranu pokazujący akcję "zainicjuj zmienną" z kursorem wewnątrz właściwości "value", która otwiera listę zawartości dynamicznej.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings.png)

      Lista zawartości dynamicznej zawiera dane wyjściowe z poprzednich akcji, które są dostępne do wybrania jako dane wejściowe dla kolejnych akcji w przepływie pracy. Lista zawartości dynamicznej zawiera Edytor wyrażeń, którego można użyć do wybrania funkcji, które wykonują operacje w wyrażeniu. Ten Edytor wyrażeń jest dostępny tylko na liście zawartości dynamicznej.

   1. W edytorze wyrażeń wprowadź wyrażenie: `div(,60)`

      ![Zrzut ekranu pokazujący Edytor wyrażeń z wprowadzonym wyrażeniem "DIV (, 60)".](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-2.png)

   1. W wyrażeniu Umieść kursor między lewym nawiasem (**(**) i przecinkiem (**,**) i wybierz **zawartość dynamiczną**.

      ![Zrzut ekranu pokazujący, gdzie umieścić kursor w wyrażeniu "DIV (, 60)" z wybraną opcją "zawartość dynamiczna".](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-3.png)

   1. Z listy zawartość dynamiczna w obszarze Wybierz wartość właściwości, **ruch czasu trwania podróży**.

      ![Zrzut ekranu przedstawiający wybraną wartość właściwości "ruch czasu podróży".](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-4.png)

   1. Po rozpoznaniu wartości właściwości wewnątrz wyrażenia wybierz **przycisk OK**.

      ![Zrzut ekranu pokazujący przycisk "OK" jako wybrany.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-5.png)

      Teraz zostanie wyświetlona Właściwość **Value** , jak pokazano poniżej:

      ![Zrzut ekranu przedstawiający Właściwość "value" z rozpoznanym wyrażeniem.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-6.png)

1. Zapisz aplikację logiki.

Następnie dodaj warunek, który sprawdzi, czy aktualny czas podróży jest dłuży niż określony limit.

## <a name="compare-the-travel-time-with-limit"></a>Porównaj czas podróży z limitem

1. W obszarze **Utwórz zmienną do przechowywania czasu podróży** wybierz pozycję **nowy krok**.

1. W obszarze **Wybierz operację** wybierz pozycję **wbudowane**. W polu wyszukiwania wpisz `condition`. z listy Akcje wybierz akcję o nazwie **warunek**.

   ![Zrzut ekranu przedstawiający wybraną akcję "warunek"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-condition-action.png)

1. Zmień nazwę warunku na następujący opis: `If travel time exceeds limit`

1. Kompiluj warunek, który sprawdza, czy wartość właściwości **travelTime** przekracza określony limit, zgodnie z opisem i pokazanym tutaj:

   1. W warunku po lewej stronie warunku kliknij wewnątrz pola **Wybierz wartość** .

   1. Z wyświetlonej listy zawartości dynamicznej w obszarze **zmienne** wybierz właściwość o nazwie **travelTime**.

      ![Zrzut ekranu pokazujący pole "Wybierz wartość" po lewej stronie warunku z otwartą listą zawartości dynamicznej i wybraną Właściwość "travelTime".](./media/tutorial-build-scheduled-recurring-logic-app-workflow/build-condition-left-side.png)

   1. W środkowym polu porównania wybierz operator o nazwie **jest większy niż**.

   1. Po prawej stronie warunku w polu **Wybierz wartość** wprowadź następujący limit: `15`

      Gdy wszystko będzie gotowe, warunek będzie wyglądać podobnie do tego przykładu:

      ![Zrzut ekranu pokazujący gotowy warunek porównujący czas podróży z określonym limitem.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/build-condition-check-travel-time.png)

1. Zapisz aplikację logiki.

Następnie Dodaj akcję do uruchomienia, gdy czas podróży przekroczy limit.

## <a name="send-email-when-limit-exceeded"></a>Wysyłanie wiadomości e-mail po przekroczeniu limitu

Teraz Dodaj akcję, która wysyła wiadomość e-mail, gdy czas podróży przekroczy limit. Ta wiadomość e-mail zawiera aktualny czas podróży oraz dodatkowy czas niezbędny do pokonania określonej trasy.

1. W gałęzi **prawdy** warunku wybierz pozycję **Dodaj akcję**.

1. W obszarze **Wybierz operację** wybierz pozycję **standardowa**. W polu wyszukiwania wpisz `send email`. Lista zwraca wiele wyników, dlatego aby ułatwić filtrowanie listy, najpierw wybierz odpowiedni łącznik poczty e-mail.

   Jeśli na przykład masz konto e-mail w programie Outlook, wybierz łącznik dla swojego typu konta:

   * W przypadku kont służbowych platformy Azure wybierz pozycję **Office 365 Outlook**.
   * W przypadku osobistych kont Microsoft wybierz pozycję **Outlook.com**.

   Ten przykład kontynuuje, wybierając pozycję Office 365 Outlook.

   ![Zrzut ekranu, na którym jest zaznaczona opcja "Wybierz listę operacji" z kategorią "Standardowa" i "łącznik Office 365 Outlook".](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-action-send-email.png)

1. Po wyświetleniu akcji łącznika wybierz akcję, która wysyła wiadomość e-mail, na przykład:

   ![Zrzut ekranu przedstawiający wybraną akcję "Wyślij wiadomość e-mail".](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-send-email-action.png)

1. Jeśli nie masz jeszcze połączenia, zaloguj się i Uwierzytelnij dostęp do konta e-mail po wyświetleniu monitu.

   Azure Logic Apps tworzy połączenie z kontem e-mail.

1. Zmień nazwę akcji na następujący opis: `Send email with travel time`

1. Dla właściwości **do** wprowadź adres e-mail adresata. Do celów testowych możesz użyć swojego adresu e-mail.

1. Dla właściwości **podmiotu** Określ temat wiadomości e-mail i Uwzględnij zmienną **travelTime** , wykonując następujące czynności:

   1. Wprowadź tekst `Current travel time (minutes):` ze spacją końcową. Pozostaw kursor w polu **tematu** , aby lista zawartości dynamicznej pozostawała otwarta.

   1. Z listy zawartość dynamiczna w nagłówku **zmienne** wybierz pozycję **Zobacz więcej** , tak aby zmienna o nazwie **travelTime** pojawia się.

      ![Zrzut ekranu przedstawiający listę zawartości dynamicznej z sekcją "zmienne" i "Zobacz więcej".](./media/tutorial-build-scheduled-recurring-logic-app-workflow/find-travelTime-variable.png)

      > [!NOTE]
      > Na liście zawartości dynamicznej nie jest automatycznie pokazywana zmienna **travelTime** , ponieważ właściwość **subject** oczekuje wartości ciągu, a **travelTime** jest wartością całkowitą.

      ![Zrzut ekranu pokazujący dynamiczną listę zawartości z wybraną zmienną "travelTime".](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-travelTime-variable.png)

1. Dla właściwości **treść** Określ treść wiadomości e-mail, wykonując następujące kroki:

   1. Wprowadź tekst `Add extra travel time (minutes):` ze spacją końcową. Pozostaw kursor w polu **treść** , aby lista zawartości dynamicznej pozostawała otwarta.

   1. Z listy zawartość dynamiczna wybierz pozycję **wyrażenie**, które pokazuje Edytor wyrażeń.

      ![Zrzut ekranu, który pokazuje listę zawartości dynamicznej z wybranym wyrażeniem "Expression".](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings.png)

   1. W edytorze wyrażeń wprowadź, `sub(,15)` Aby można było obliczyć liczbę minut, które przekraczają limit: 

      ![Zrzut ekranu pokazujący Edytor wyrażeń z wprowadzonym wyrażeniem "Sub (, 15)".](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-2.png)

   1. W wyrażeniu Umieść kursor między lewym nawiasem (**(**) i przecinkiem (**,**) i wybierz **zawartość dynamiczną**.

      ![Zrzut ekranu pokazujący, gdzie umieścić kursor w wyrażeniu "Sub (, 15)" z wybraną opcją "zawartość dynamiczna".](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-3.png)

   1. W obszarze **Zmienne** wybierz pozycję **travelTime**.

      ![Zrzut ekranu pokazujący dynamiczną listę zawartości z wybraną zmienną "travelTime".](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-4.png)

   1. Po rozpoznaniu właściwości wewnątrz wyrażenia wybierz **przycisk OK**.

      ![Zrzut ekranu pokazujący dynamiczną listę zawartości i wybraną wartość "OK".](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-5.png)

      Teraz zostanie wyświetlona Właściwość **Body** , jak pokazano poniżej:

      ![Zrzut ekranu pokazujący listę zawartości dynamicznej z wyrażeniem rozwiązanym we właściwości akcji wiadomości e-mail.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-6.png)

1. Zapisz aplikację logiki.

Następnie przetestuj i uruchom aplikację logiki, która jest teraz podobna do poniższego przykładu:

![Zrzut ekranu przedstawiający ukończony przykładowy przepływ pracy aplikacji logiki](./media/tutorial-build-scheduled-recurring-logic-app-workflow/check-travel-time-finished.png)

## <a name="run-your-logic-app"></a>Uruchamianie aplikacji logiki

Aby ręcznie uruchomić aplikację logiki, na pasku narzędzi projektanta wybierz pozycję **Uruchom**.

* Jeśli bieżący czas podróży pozostaje poniżej limitu, aplikacja logiki nie wykonuje żadnych innych czynności i czeka lub następnego interwału przed ponownym sprawdzeniem. 

* Jeśli bieżący czas podróży przekracza limit, otrzymasz wiadomość e-mail z bieżącym czasem podróży i liczbą minut powyżej limitu. Oto przykładowa wiadomość e-mail wysłana przez aplikację logiki:

  ![Zrzut ekranu pokazujący przykładową wiadomość e-mail, która raportuje bieżący czas podróży i dodatkowy czas podróży przekraczający określony limit.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/received-example-email-notification.png)

  > [!TIP]
  > Jeśli nie dostaniesz żadnych wiadomości e-mail, sprawdź folder wiadomości-śmieci. Filtr wiadomości-śmieci Twojej poczty e-mail może przekierowywać tego rodzaju wiadomości. W przeciwnym razie, jeśli nie masz pewności, czy aplikacja logiki została poprawnie uruchomiona, zobacz [Troubleshoot your logic app (Rozwiązywania problemów z aplikacją logiki)](../logic-apps/logic-apps-diagnosing-failures.md).

Gratulacje, udało Ci się utworzyć i uruchomić cykliczną aplikację logiki z użyciem harmonogramu. 

Aby utworzyć inne aplikacje logiki, które używają wyzwalacza **cykl** , zapoznaj się z tymi szablonami, które są dostępne po utworzeniu aplikacji logiki:

* Otrzymywanie codziennych przypomnień pocztą e-mail.
* Usuwanie starszych obiektów blob na platformie Azure.
* Dodawanie komunikatu do kolejki usługi Azure Storage.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Aplikacja logiki kontynuuje działanie do momentu wyłączenia lub usunięcia aplikacji. Jeśli przykładowa aplikacja logiki nie jest już potrzebna, Usuń grupę zasobów zawierającą aplikację logiki i powiązane zasoby.

1. W polu wyszukiwania Azure Portal wprowadź nazwę utworzonej grupy zasobów. Z wyników w obszarze **grupy zasobów** wybierz grupę zasobów.

   Ten przykład utworzył grupę zasobów o nazwie `LA-TravelTime-RG` .

   ![Zrzut ekranu przedstawiający pole usługi Azure Search z opcją "La-podróże-Time-RG", które zostało wprowadzone i * * LA-TravelTime-RG * * zaznaczone.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/find-resource-group.png)

   > [!TIP]
   > Jeśli na stronie głównej platformy Azure zostanie wyświetlona Grupa zasobów w obszarze **ostatnie zasoby**, możesz wybrać grupę ze strony głównej.

1. W menu Grupa zasobów Sprawdź, czy jest zaznaczona wartość **Przegląd** . Na pasku narzędzi okienka **Przegląd** wybierz pozycję **Usuń grupę zasobów**.

   ![Zrzut ekranu przedstawiający okienko "przegląd" grupy zasobów i na pasku narzędzi okienka jest zaznaczona wartość "Usuń grupę zasobów".](./media/tutorial-build-scheduled-recurring-logic-app-workflow/delete-resource-group.png)

1. W wyświetlonym okienku potwierdzenia wprowadź nazwę grupy zasobów, a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym samouczku utworzysz aplikację logiki, która sprawdza ruch na podstawie określonego harmonogramu (dnia tygodnia rano) i podejmuje akcję (wysyła wiadomość e-mail), gdy czas podróży przekracza określony limit. Teraz Dowiedz się, jak utworzyć aplikację logiki, która wysyła żądania listy adresowej do zatwierdzenia przez integrację usług platformy Azure, usług firmy Microsoft i innych aplikacji typu oprogramowanie jako usługa (SaaS).

> [!div class="nextstepaction"]
> [Manage mailing list requests (Zarządzanie żądaniami listy adresowej)](../logic-apps/tutorial-process-mailing-list-subscriptions-workflow.md)
