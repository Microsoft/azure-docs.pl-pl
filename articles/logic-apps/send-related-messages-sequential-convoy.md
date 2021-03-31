---
title: Wysyłanie skorelowanych komunikatów w kolejności przy użyciu konwoju sekwencyjnego
description: Wysyłaj powiązane komunikaty w kolejności przy użyciu wzorca sekwencyjnego konwoju w Azure Logic Apps z Azure Service Bus
services: logic-apps
ms.suite: integration
ms.reviewer: apseth, divswa, logicappspm
ms.topic: conceptual
ms.date: 05/29/2020
ms.openlocfilehash: 8c00d2e4f622bcfad7b2468013336f0d936e318c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87048668"
---
# <a name="send-related-messages-in-order-by-using-a-sequential-convoy-in-azure-logic-apps-with-azure-service-bus"></a>Wysyłaj powiązane komunikaty w kolejności przy użyciu konwoju sekwencyjnego w Azure Logic Apps z Azure Service Bus

Gdy zachodzi potrzeba wysyłania skorelowanych komunikatów w określonej kolejności, można użyć [wzorca *sekwencyjnej konwoju*](/azure/architecture/patterns/sequential-convoy) podczas korzystania z [Azure Logic Apps](../logic-apps/logic-apps-overview.md) przy użyciu [łącznika Azure Service Bus](../connectors/connectors-create-api-servicebus.md). Skorelowane komunikaty mają właściwość definiującą relację między tymi komunikatami, na przykład identyfikator [sesji](../service-bus-messaging/message-sessions.md) w Service Bus.

Załóżmy na przykład, że masz 10 komunikatów dla sesji o nazwie "Session 1" i masz 5 komunikatów dla sesji o nazwie "Session 2", które są wysyłane do tej samej [kolejki Service Bus](../service-bus-messaging/service-bus-queues-topics-subscriptions.md). Można utworzyć aplikację logiki, która przetwarza komunikaty z kolejki, tak aby wszystkie komunikaty z "sesji 1" były obsługiwane przez pojedynczy przebieg wyzwalacza, a wszystkie komunikaty z "sesji 2" są obsługiwane przez następnego uruchomienia wyzwalacza.

![Wzorzec sekwencyjnej konwoju](./media/send-related-messages-sequential-convoy/sequential-convoy-pattern-general.png)

W tym artykule pokazano, jak utworzyć aplikację logiki, która implementuje ten wzorzec przy użyciu **skorelowanego dostarczania w określonej kolejności przy użyciu szablonu sesji usługi Service Bus** . Ten szablon definiuje przepływ pracy aplikacji logiki, który rozpoczyna się od łącznika Service Bus **po odebraniu komunikatu w wyzwalaczu kolejki (PEEK-Lock)** , który odbiera komunikaty z [kolejki Service Bus](../service-bus-messaging/service-bus-queues-topics-subscriptions.md). Poniżej przedstawiono ogólne kroki wykonywane przez tę aplikację logiki:

* Zainicjuj sesję na podstawie komunikatu, który wyzwalacz odczytuje z kolejki Service Bus.

* Odczytaj i Przetwórz wszystkie komunikaty z tej samej sesji w kolejce podczas bieżącego przebiegu przepływu pracy.

Aby przejrzeć plik JSON tego szablonu, zobacz artykuł [GitHub: service-bus-sessions.json](https://github.com/Azure/logicapps/blob/master/templates/service-bus-sessions.json).

Aby uzyskać więcej informacji, zobacz [wzorzec konwoju wzorca — wzorce projektowania architektury platformy Azure](/azure/architecture/patterns/sequential-convoy).

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* Przestrzeń nazw Service Bus i [kolejka Service Bus](../service-bus-messaging/service-bus-queues-topics-subscriptions.md), która jest jednostką obsługi komunikatów, która będzie używana w aplikacji logiki. Te elementy i aplikacja logiki muszą używać tej samej subskrypcji platformy Azure. Upewnij się, że podczas tworzenia kolejki wybierasz opcję **Włącz sesje** . Jeśli nie masz tych elementów, Dowiedz się, [jak utworzyć Service Bus przestrzeń nazw i kolejkę](../service-bus-messaging/service-bus-create-namespace-portal.md).

  [!INCLUDE [Warning about creating infinite loops](../../includes/connectors-infinite-loops.md)]

* Podstawowa wiedza na temat tworzenia aplikacji logiki. Jeśli jesteś nowym do Azure Logic Apps, wypróbuj Przewodnik Szybki Start, aby [utworzyć pierwszy zautomatyzowany przepływ pracy](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="permissions-connection-string"></a>

## <a name="check-access-to-service-bus-namespace"></a>Sprawdź dostęp do Service Bus przestrzeni nazw

Jeśli nie masz pewności, czy aplikacja logiki ma uprawnienia dostępu do obszaru nazw Service Bus, potwierdź te uprawnienia.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Znajdź i wybierz swoją *przestrzeń nazw* Service Bus.

1. W menu Przestrzeń nazw w obszarze **Ustawienia** wybierz pozycję **zasady dostępu współdzielonego**. W obszarze **oświadczenia** Sprawdź, czy masz uprawnienia do **zarządzania** tą przestrzenią nazw.

   ![Zarządzaj uprawnieniami dla Service Bus przestrzeni nazw](./media/send-related-messages-sequential-convoy/check-service-bus-permissions.png)

1. Teraz Pobierz parametry połączenia dla przestrzeni nazw Service Bus. Tego ciągu można użyć później podczas tworzenia połączenia z przestrzenią nazw z poziomu aplikacji logiki.

   1. W okienku **zasady dostępu współdzielonego** w obszarze **zasady** wybierz pozycję **RootManageSharedAccessKey**.
   
   1. Obok podstawowych parametrów połączenia wybierz przycisk Kopiuj. Zapisz parametry połączenia do późniejszego użycia.

      ![Kopiuj parametry połączenia Service Bus przestrzeni nazw](./media/send-related-messages-sequential-convoy/copy-service-bus-connection-string.png)

   > [!TIP]
   > Aby sprawdzić, czy parametry połączenia są skojarzone z przestrzenią nazw Service Bus, czy z jednostką obsługi komunikatów, taką jak kolejka, Wyszukaj parametry połączenia dla `EntityPath` parametru. Jeśli ten parametr zostanie znaleziony, ciąg połączenia jest przeznaczony dla określonej jednostki i nie jest poprawnym ciągiem używanym z aplikacją logiki.

## <a name="create-logic-app"></a>Tworzenie aplikacji logiki

W tej sekcji utworzysz aplikację logiki przy użyciu **skorelowanego dostarczania w określonej kolejności przy użyciu szablonu sesji usługi Service Bus** , który obejmuje wyzwalacz i akcje wdrożenia tego wzorca przepływu pracy. Należy również utworzyć połączenie z przestrzenią nazw Service Bus i określić nazwę kolejki Service Bus, która ma być używana.

1. W [Azure Portal](https://portal.azure.com)Utwórz pustą aplikację logiki. Na stronie głównej platformy Azure wybierz pozycję **Utwórz zasób**  >    >  **aplikacja logiki**.

1. Po wyświetleniu galerii szablonów przewiń w dół sekcje wideo i typowe wyzwalacze. W sekcji **Szablony** wybierz szablon, **skorelowane dostarczanie w kolejności przy użyciu sesji magistrali usług**.

   ![Wybierz szablon "skorelowane dostarczanie w kolejności przy użyciu sesji magistrali usług"](./media/send-related-messages-sequential-convoy/select-correlated-in-order-delivery-template.png)

1. Po wyświetleniu pola potwierdzenia wybierz pozycję **Użyj tego szablonu**.

1. W Projektancie aplikacji logiki w kształcie **Service Bus** wybierz pozycję **Kontynuuj**, a następnie wybierz znak plus ( **+** ), który pojawia się na kształcie.

   ![Wybierz pozycję "Kontynuuj", aby nawiązać połączenie z Azure Service Bus](./media/send-related-messages-sequential-convoy/connect-to-service-bus.png)

1. Teraz Utwórz połączenie Service Bus, wybierając jedną z opcji:

   * Aby użyć parametrów połączenia, które zostały skopiowane wcześniej z przestrzeni nazw Service Bus, wykonaj następujące kroki:

     1. Wybierz pozycję **ręcznie wprowadź informacje o połączeniu**.

     1. W obszarze **Nazwa połączenia** Podaj nazwę połączenia. W obszarze **Parametry połączenia** wklej parametry połączenia z przestrzenią nazw i wybierz pozycję **Utwórz**, na przykład:

        ![Wprowadź nazwę połączenia i Service Bus parametry połączenia](./media/send-related-messages-sequential-convoy/provide-service-bus-connection-string.png)

        > [!TIP]
        > Jeśli nie masz tych parametrów połączenia, Dowiedz się, jak [znaleźć i skopiować parametry połączenia Service Bus przestrzeni nazw](#permissions-connection-string).

   * Aby wybrać Service Bus przestrzeń nazw z bieżącej subskrypcji platformy Azure, wykonaj następujące kroki:

     1. W obszarze **Nazwa połączenia** Podaj nazwę połączenia. W **obszarze Przestrzeń nazw Service Bus** wybierz Service Bus przestrzeń nazw, na przykład:

        ![Wprowadź nazwę połączenia i wybierz pozycję Service Bus przestrzeń nazw](./media/send-related-messages-sequential-convoy/create-service-bus-connection.png)

     1. Po wyświetleniu kolejnego okienka wybierz zasady Service Bus a następnie wybierz pozycję **Utwórz**.

        ![Wybierz zasady Service Bus a następnie pozycję "Utwórz"](./media/send-related-messages-sequential-convoy/create-service-bus-connection-2.png)

1. Gdy skończysz, wybierz pozycję **Kontynuuj**.

   Projektant aplikacji logiki będzie teraz przedstawiał **skorelowaną dostawę w kolejności przy użyciu szablonu sesji usługi Service Bus** , który zawiera wstępnie wypełniony przepływ pracy z wyzwalaczem i akcjami, w tym dwa zakresy, które implementują obsługę błędów, która jest zgodna ze `Try-Catch` wzorcem.

Teraz można dowiedzieć się więcej na temat wyzwalacza i akcji w szablonie lub przeskoczyć do góry, aby [podać wartości dla szablonu aplikacji logiki](#complete-template).

<a name="template-summary"></a>

## <a name="template-summary"></a>Podsumowanie szablonu

Poniżej znajduje się przepływ pracy najwyższego poziomu w ramach **skorelowanej dostawy w kolejności przy użyciu szablonu sesji usługi Service Bus** , gdy szczegóły są zwinięte:

![Przepływ pracy najwyższego poziomu szablonu](./media/send-related-messages-sequential-convoy/template-top-level-flow.png)

| Nazwa | Opis |
|------|-------------|
| **`When a message is received in a queue (peek-lock)`** | W oparciu o określony cykl ten Service Bus wyzwalacza sprawdza określoną kolejkę Service Bus dla dowolnych komunikatów. Jeśli w kolejce istnieje komunikat, uruchamiany jest wyzwalacz, który tworzy i uruchamia wystąpienie przepływu pracy. <p><p>Okres *blokowania* oznacza, że wyzwalacz wysyła żądanie pobrania komunikatu z kolejki. Jeśli komunikat istnieje, wyzwalacz pobiera i blokuje komunikat, tak aby żadne inne przetwarzanie nie zachodził na ten komunikat do momentu wygaśnięcia okresu blokady. Aby uzyskać szczegółowe informacje, [zainicjuj sesję](#initialize-session). |
| **`Init isDone`** | Ta [Akcja **inicjowania zmiennej**](../logic-apps/logic-apps-create-variables-store-values.md#initialize-variable) powoduje utworzenie zmiennej logicznej, która jest ustawiona na `false` i wskazuje, kiedy są spełnione następujące warunki: <p><p>-Więcej komunikatów w sesji nie można odczytać. <br>-Nie trzeba już odnowić blokady sesji, aby można było zakończyć bieżące wystąpienie przepływu pracy. <p><p>Aby uzyskać szczegółowe informacje, zobacz [Inicjowanie sesji](#initialize-session). |
| **`Try`** | Ta [Akcja **zakresu**](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md) zawiera akcje, które są uruchamiane w celu przetworzenia komunikatu. Jeśli w zakresie wystąpi problem `Try` , kolejna `Catch` Akcja **zakresu** obsłuży ten problem. Aby uzyskać więcej informacji, zobacz [zakres "try"](#try-scope). |
| **`Catch`**| Ta [Akcja **zakresu**](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md) zawiera akcje, które są uruchamiane w przypadku wystąpienia problemu w poprzednim `Try` zakresie. Aby uzyskać więcej informacji, zobacz [zakres "Catch"](#catch-scope). |
|||

<a name="try-scope"></a>

### <a name="try-scope"></a>Zakres "try"

Poniżej znajduje się przepływ najwyższego poziomu w `Try` [akcji zakresu](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md) , gdy szczegóły są zwinięte:

![Przepływ pracy akcji zakresu "try"](./media/send-related-messages-sequential-convoy/try-scope-action.png)

| Nazwa | Opis |
|------|-------------|
| **`Send initial message to topic`** | Tę akcję można zastąpić dowolną akcją, która ma obsługiwać pierwszy komunikat z sesji w kolejce. Identyfikator sesji określa sesję. <p><p>W przypadku tego szablonu akcja Service Bus wysyła pierwszy komunikat do tematu Service Bus. Aby uzyskać szczegółowe informacje, zobacz [Obsługa początkowego komunikatu](#handle-initial-message). |
| (rozgałęzienie równoległe) | Ta [Akcja gałęzi równoległej](../logic-apps/logic-apps-control-flow-branches.md) tworzy dwie ścieżki: <p><p>-Branch #1: Kontynuuj przetwarzanie komunikatu. Aby uzyskać więcej informacji, zobacz [gałąź #1: Ukończ początkowy komunikat w kolejce](#complete-initial-message). <p><p>-Branch #2: Porzuć komunikat, jeśli coś się nie stało, i zwolnij do pobrania przez inny przebieg wyzwalacza. Aby uzyskać więcej informacji, zobacz [gałąź #2: Porzuć początkowy komunikat z kolejki](#abandon-initial-message). <p><p>Obie ścieżki przyłączają się później do **sesji zamknięcia w kolejce i akcje zakończone powodzeniem** , opisane w następnym wierszu. |
| **`Close a session in a queue and succeed`** | Ta Service Bus akcja sprzęga wcześniej opisane gałęzie i zamyka sesję w kolejce po wystąpieniu jednego z następujących zdarzeń: <p><p>-Przepływ pracy kończy przetwarzanie dostępnych komunikatów w kolejce. <br>-Przepływ pracy porzuca komunikat początkowy, ponieważ wystąpił błąd. <p><p>Aby uzyskać szczegółowe informacje, zobacz [zamykanie sesji w kolejce i powodzenie](#close-session-succeed). |
|||

<a name="complete-initial-message"></a>

#### <a name="branch-1-complete-initial-message-in-queue"></a>Gałąź #1: Ukończ początkowy komunikat w kolejce

| Nazwa | Opis |
|------|-------------|
| `Complete initial message in queue` | Ta Service Bus akcja oznacza pomyślnie pobrany komunikat jako zakończony i usuwa komunikat z kolejki, aby zapobiec przeprzetwarzaniu. Aby uzyskać szczegółowe informacje, zobacz [Obsługa początkowego komunikatu](#handle-initial-message). |
| `While there are more messages for the session in the queue` | Dzieje się [ **tak, dopóki** pętla nie](../logic-apps/logic-apps-control-flow-loops.md#until-loop) będzie w stanie uzyskać komunikatów, gdy istnieją komunikaty lub do czasu przekroczenia jednej godziny. Aby uzyskać więcej informacji o akcjach w tej pętli, zobacz, [gdy w kolejce są więcej komunikatów](#while-more-messages-for-session). |
| **`Set isDone = true`** | Gdy nie ma więcej komunikatów, ta [ **zmienna ustawia ustawioną wartość**](../logic-apps/logic-apps-create-variables-store-values.md#set-variable) `isDone` `true` . |
| **`Renew session lock until cancelled`** | Dzieje się [ **tak do momentu** , gdy pętla](../logic-apps/logic-apps-control-flow-loops.md#until-loop) jest zablokowana przez tę aplikację logiki, gdy istnieją komunikaty lub do czasu przekroczenia jednej godziny. Aby uzyskać więcej informacji o akcjach w tej pętli, zobacz [Odnów sesję blokady do czasu anulowania](#renew-session-while-messages-exist). |
|||

<a name="abandon-initial-message"></a>

#### <a name="branch-2-abandon-initial-message-from-the-queue"></a>Gałąź #2: Porzuć początkowy komunikat z kolejki

Jeśli akcja, która obsługuje pierwszy komunikat, zakończy się niepowodzeniem, Akcja Service Bus, **Porzuć początkowy komunikat z kolejki**, zwalnia komunikat dla innego wystąpienia przepływu pracy, aby wybrać i przetworzyć. Aby uzyskać szczegółowe informacje, zobacz [Obsługa początkowego komunikatu](#handle-initial-message).

<a name="catch-scope"></a>

### <a name="catch-scope"></a>Zakres "Catch"

Jeśli akcje w `Try` zakresie zakończą się niepowodzeniem, aplikacja logiki musi nadal zamknąć sesję. `Catch` [Akcja zakresu](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md) jest uruchamiana `Try` , gdy akcja zakresu skutkuje stanem, `Failed` , `Skipped` , lub `TimedOut` . Zakres zwraca komunikat o błędzie, który zawiera identyfikator sesji, w której wystąpił problem, i kończy działanie aplikacji logiki.

Poniżej znajduje się przepływ najwyższego poziomu w `Catch` akcji zakresu, gdy szczegóły są zwinięte:

![Przepływ pracy akcji zakresu "Catch"](./media/send-related-messages-sequential-convoy/catch-scope-action.png)

| Nazwa | Opis |
|------|-------------|
| **`Close a session in a queue and fail`** | Ta Service Bus akcja zamyka sesję w kolejce, aby blokada sesji nie pozostała otwarta. Aby uzyskać szczegółowe informacje, zobacz [zamykanie sesji w kolejce i niepowodzenie](#close-session-fail). |
| **`Find failure msg from 'Try' block`** | Ta [Akcja **filtrowania tablicy**](../logic-apps/logic-apps-perform-data-operations.md#filter-array-action) tworzy tablicę z danych wejściowych i wyjściowych ze wszystkich akcji wewnątrz `Try` zakresu na podstawie określonych kryteriów. W takim przypadku ta akcja zwraca dane wyjściowe z akcji, które spowodowały jego `Failed` stan. Aby uzyskać szczegółowe informacje, zobacz Znajdowanie komunikatu o [błędach z bloku "try"](#find-failure-message). |
| **`Select error details`** | Ta [Akcja **wyboru**](../logic-apps/logic-apps-perform-data-operations.md#select-action) tworzy tablicę zawierającą obiekty JSON na podstawie określonych kryteriów. Te obiekty JSON są tworzone na podstawie wartości w tablicy utworzonej przez poprzednią akcję `Find failure msg from 'Try' block` . W takim przypadku ta akcja zwraca tablicę zawierającą obiekt JSON utworzony na podstawie szczegółów błędu zwróconych z poprzedniej akcji. Aby uzyskać szczegółowe informacje, zobacz [Wybieranie szczegółów błędu](#select-error-details). |
| **`Terminate`** | Ta [Akcja **przerwania**](../logic-apps/logic-apps-workflow-actions-triggers.md#terminate-action) zatrzymuje przebieg przepływu pracy, anuluje wszystkie akcje w toku, pomija wszystkie pozostałe akcje i zwraca określony stan, identyfikator sesji oraz wynik błędu z `Select error details` akcji. Aby uzyskać szczegółowe informacje, zobacz [Kończenie działania aplikacji logiki](#terminate-logic-app). |
|||

<a name="complete-template"></a>

## <a name="complete-the-template"></a>Ukończ szablon

Aby podać wartości dla wyzwalacza i akcji w **skorelowanej dostawie w kolejności przy użyciu szablonu sesji usługi Service Bus** , wykonaj następujące kroki. Przed zapisaniem aplikacji logiki należy podać wszystkie wymagane wartości, które są oznaczone gwiazdką ( **\*** ).

<a name="initialize-session"></a>

### <a name="initialize-the-session"></a>Inicjowanie sesji

* W przypadku **odebrania komunikatu w wyzwalaczu kolejki (PEEK-Lock)** podaj te informacje, aby szablon mógł zainicjować sesję przy użyciu właściwości **Identyfikator sesji** , na przykład:

  ![Szczegóły wyzwalacza Service Bus dla "po odebraniu komunikatu w kolejce (blokada blokady)"](./media/send-related-messages-sequential-convoy/service-bus-check-message-peek-lock-trigger.png)

  > [!NOTE]
  > Początkowo interwał sondowania jest ustawiany na trzy minuty, dzięki czemu aplikacja logiki nie uruchamia się częściej niż oczekiwano i spowoduje nieoczekiwane naliczanie opłat. Najlepszym rozwiązaniem jest ustawienie interwału i częstotliwości na 30 sekund, aby aplikacja logiki była wyzwalana natychmiast po nadejściu wiadomości.

  | Właściwość | Wymagane dla tego scenariusza | Wartość | Opis |
  |----------|----------------------------|-------|-------------|
  | **Nazwa kolejki** | Tak | <*Nazwa kolejki*> | Nazwa utworzonej wcześniej kolejki Service Bus. W tym przykładzie zastosowano "Fabrikam-Service-Bus-queue". |
  | **Typ kolejki** | Tak | **Główną** | Twoja Kolejka Service Bus główna |
  | **Identyfikator sesji** | Tak | **Następne dostępne** | Ta opcja pobiera sesję dla każdego uruchomienia wyzwalacza na podstawie identyfikatora sesji z komunikatu w kolejce Service Bus. Sesja jest również zablokowana, tak aby żadna inna aplikacja logiki lub inny klient nie przetwarza komunikatów powiązanych z tą sesją. Kolejne akcje przepływu pracy przetwarzają wszystkie komunikaty, które są skojarzone z tą sesją, zgodnie z opisem w dalszej części tego artykułu. <p><p>Poniżej znajduje się więcej informacji na temat innych opcji **identyfikatora sesji** : <p>- **Brak**: opcja domyślna, która powoduje brak sesji i nie może być używana do implementowania wzorca sekwencyjnego konwoju. <p>- **Wprowadź wartość niestandardową**: Użyj tej opcji, jeśli znasz identyfikator sesji, który ma być używany, i zawsze chcesz uruchomić wyzwalacz dla tego identyfikatora sesji. <p>**Uwaga**: Łącznik Service Bus może zapisywać ograniczoną liczbę unikatowych sesji w czasie od Azure Service Bus do pamięci podręcznej łączników. Jeśli liczba sesji przekracza ten limit, stare sesje są usuwane z pamięci podręcznej. Aby uzyskać więcej informacji, zobacz [wiadomości programu Exchange w chmurze z Azure Logic Apps i Azure Service Bus](../connectors/connectors-create-api-servicebus.md#connector-reference). |
  | **Interwał** | Tak | <*Liczba interwałów*> | Liczba jednostek czasu między cyklami przed sprawdzeniem komunikatu. |
  | **Częstotliwość** | Tak | **Sekunda**, **minuta**, **godzina**, **dzień**, **tydzień** lub **miesiąc** | Jednostka czasu, z której będzie korzystać cykl podczas sprawdzania komunikatu. <p>**Porada**: aby dodać **strefę czasową** lub **godzinę rozpoczęcia**, wybierz te właściwości z listy **Dodaj nowy parametr** . |
  |||||

  Aby uzyskać więcej informacji o wyzwalaczach, zobacz [Service Bus — gdy w kolejce jest odbierany komunikat (blokada blokady)](/connectors/servicebus/#when-a-message-is-received-in-a-queue-(peek-lock)). Wyzwalacz wyprowadza element [ServiceBusMessage](/connectors/servicebus/#servicebusmessage).

Po zainicjowaniu sesji przepływ pracy używa akcji **Inicjuj zmienną** do utworzenia zmiennej logicznej, która początkowo została ustawiona na `false` i wskazuje, kiedy są spełnione następujące warunki: 

* Nie ma więcej komunikatów w sesji, które można odczytać.

* Nie trzeba już odnowić blokady sesji, aby można było zakończyć bieżące wystąpienie przepływu pracy.

![Szczegóły akcji "zainicjuj zmienną" dla "init isdone"](./media/send-related-messages-sequential-convoy/init-is-done-variable.png)

Następnie w bloku **try** przepływ pracy wykonuje akcje dotyczące pierwszego odczytanego komunikatu.

<a name="handle-initial-message"></a>

### <a name="handle-the-initial-message"></a>Obsługuj komunikat początkowy

Pierwsza akcja jest symbolem zastępczym Service Bus akcji, **wysyła komunikat początkowy do tematu**, który można zastąpić każdą inną akcją, która ma obsługiwać pierwszy komunikat z sesji w kolejce. Identyfikator sesji określa sesję, z której pochodzi wiadomość.

Akcja Service Bus symbolu zastępczego wysyła pierwszy komunikat do tematu Service Bus, który jest określony przez właściwość **Identyfikator sesji** . Dzięki temu wszystkie komunikaty, które są skojarzone z określoną sesją, przejdą do tego samego tematu. Wszystkie właściwości **identyfikatora sesji** dla kolejnych akcji w tym szablonie używają tej samej wartości identyfikatora sesji.

![Service Bus szczegóły akcji dla "Wyślij wiadomość początkową do tematu"](./media/send-related-messages-sequential-convoy/send-initial-message-to-topic-action.png)

1. W akcji Service Bus **Ukończ początkowy komunikat w kolejce**, podaj nazwę kolejki Service Bus i Zachowaj wszystkie inne wartości domyślne właściwości w akcji.

   ![Service Bus szczegóły akcji dla "Ukończ początkowy komunikat w kolejce"](./media/send-related-messages-sequential-convoy/complete-initial-message-queue.png)

1. W akcji Service Bus **Porzuć początkowy komunikat z kolejki**, podaj nazwę kolejki Service Bus i Zachowaj wszystkie inne wartości domyślne właściwości w akcji.

   ![Service Bus szczegóły akcji "Porzuć początkowy komunikat z kolejki"](./media/send-related-messages-sequential-convoy/abandon-initial-message-from-queue.png)

Następnie podaj niezbędne informacje dotyczące akcji, które są zgodne z **ukończeniem początkowego komunikatu w kolejce** . Zaczniesz od akcji w **czasie, gdy dla sesji w pętli kolejki będzie więcej komunikatów** .

<a name="while-more-messages-for-session"></a>

### <a name="while-there-are-more-messages-for-the-session-in-the-queue"></a>Gdy istnieje więcej komunikatów dla sesji w kolejce

To [ **do momentu** uruchomienia przez pętlę](../logic-apps/logic-apps-control-flow-loops.md#until-loop) tych akcji, gdy komunikaty istnieją w kolejce lub do czasu przekroczenia jednej godziny. Aby zmienić limit czasu pętli, Edytuj wartość właściwości **limit czasu** pętli.

* Pobierz dodatkowe komunikaty z kolejki, gdy istnieją komunikaty.

* Sprawdź liczbę pozostałych komunikatów. Jeśli nadal istnieją komunikaty, Kontynuuj przetwarzanie komunikatów. Jeśli nie ma więcej komunikatów, przepływ pracy ustawia `isDone` zmienną na `true` i opuszcza pętlę.

![Do momentu przetworzenia komunikatów przez pętlę w kolejce](./media/send-related-messages-sequential-convoy/while-more-messages-for-session-in-queue.png)

1. W akcji Service Bus **uzyskaj dodatkowe komunikaty z sesji**, podaj nazwę kolejki Service Bus. W przeciwnym razie Zachowaj wszystkie inne domyślne wartości właściwości w akcji.

   > [!NOTE]
   > Domyślnie maksymalna liczba komunikatów jest ustawiona na `175` , ale ten limit ma wpływ na rozmiar komunikatu i Właściwość maksymalny rozmiar komunikatu w Service Bus. Aby uzyskać więcej informacji, zobacz [rozmiar komunikatu dla kolejki](../service-bus-messaging/service-bus-quotas.md).

   ![Akcja Service Bus — "Pobierz dodatkowe komunikaty z sesji"](./media/send-related-messages-sequential-convoy/get-additional-messages-from-session.png)

   Następnie przepływ pracy zostaje podzielony na te gałęzie równoległe:

   * Jeśli wystąpi błąd lub błąd podczas sprawdzania dodatkowych komunikatów, ustaw `isDone` zmienną na `true` .

   * **Komunikaty procesu, jeśli mamy jakikolwiek** warunek sprawdza, czy liczba pozostałych komunikatów wynosi zero. Jeśli istnieje wartość FAŁSZ i więcej komunikatów, Kontynuuj przetwarzanie. Jeśli wartość jest równa true i nie istnieje więcej komunikatów, przepływ pracy ustawia `isDone` zmienną na `true` .

   ![Warunek — Przetwarzaj komunikaty, jeśli istnieją](./media/send-related-messages-sequential-convoy/process-messages-if-any.png)

   W sekcji **Jeśli false** , pętla **for each** przetwarza każdy komunikat w pierwszej kolejności (FIFO). W **ustawieniach** pętli ustawienie **kontroli współbieżności** jest ustawione na `1` , więc tylko jeden komunikat jest przetwarzany jednocześnie.

   ![Pętla "for each" — Przetwarzaj każdy komunikat pojedynczo](./media/send-related-messages-sequential-convoy/for-each-additional-message.png)

1. W przypadku akcji Service Bus **Wykonaj komunikat w kolejce** i **Porzuć komunikat w kolejce**, podaj nazwę kolejki Service Bus.

   ![Service Bus akcje — "Ukończ komunikat w kolejce" i "Porzuć komunikat w kolejce"](./media/send-related-messages-sequential-convoy/abandon-or-complete-message-in-queue.png)

   Po zakończeniu **wykonywania kolejnych komunikatów dla sesji w kolejce** przepływ pracy ustawia `isDone` zmienną na `true` .

Następnie podaj informacje niezbędne do wykonania akcji w **bloku Odnów sesję do czasu anulowania** pętli.

<a name="renew-session-while-messages-exist"></a>

### <a name="renew-session-lock-until-cancelled"></a>Odnów blokadę sesji do anulowania

Dzieje się [ **tak do momentu** , gdy pętla](../logic-apps/logic-apps-control-flow-loops.md#until-loop) jest utrzymywana przez tę aplikację logiki, gdy komunikaty istnieją w kolejce lub do czasu przekroczenia jednej godziny, uruchamiając te akcje. Aby zmienić limit czasu pętli, Edytuj wartość właściwości **limit czasu** pętli.

* Opóźnienie przez 25 sekund lub ilość czasu, która jest mniejsza niż limit czasu blokady dla kolejki, która jest przetwarzana. Najmniejszy czas trwania blokady wynosi 30 sekund, więc wartość domyślna jest wystarczająca. Można jednak zoptymalizować liczbę uruchomień pętli, odpowiednio dostosowując.

* Sprawdź, czy `isDone` zmienna jest ustawiona na `true` .

  * Jeśli wartość `isDone` nie jest ustawiona na `true` , przepływ pracy będzie nadal przetwarzał komunikaty, więc przepływ pracy odnawia blokadę sesji w kolejce i sprawdza warunek pętli ponownie.

    Musisz podać nazwę kolejki Service Bus w akcji Service Bus, [**Odnów blokadę sesji w kolejce**](#renew-lock-on-session).

  * Jeśli `isDone` jest ustawiona na `true` , przepływ pracy nie odnowi blokady sesji w kolejce i opuszcza pętlę.

  ![Do pętli — "Odnów blokadę sesji do anulowania"](./media/send-related-messages-sequential-convoy/renew-lock-until-session-cancelled.png)

<a name="renew-lock-on-session"></a>

#### <a name="renew-lock-on-the-session-in-a-queue"></a>Odnów blokadę sesji w kolejce

Ta Service Bus akcja odnawia blokadę sesji w kolejce, gdy przepływ pracy nadal przetwarza komunikaty.

* W akcji Service Bus **Odnów blokadę sesji w kolejce**, podaj nazwę kolejki Service Bus.

  ![Akcja Service Bus — "Odnów blokadę sesji w kolejce"](./media/send-related-messages-sequential-convoy/renew-lock-on-session-in-queue.png)

Następnie należy podać informacje niezbędne do działania Service Bus, **zamknąć sesję w kolejce i zakończyć się powodzeniem**.

<a name="close-session-succeed"></a>

### <a name="close-a-session-in-a-queue-and-succeed"></a>Zamykanie sesji w kolejce i pomyślne

Ta Service Bus akcja zamyka sesję w kolejce po zakończeniu przetwarzania wszystkich dostępnych komunikatów w kolejce przez przepływ pracy lub przepływ pracy porzuca komunikat początkowy.

* W akcji Service Bus **Zamknij sesję w kolejce i powiodło się**, podaj nazwę kolejki Service Bus.

  ![Akcja Service Bus — "zamykanie sesji w kolejce i pomyślne"](./media/send-related-messages-sequential-convoy/close-session-in-queue-succeed.png)

W poniższych sekcjach opisano akcje w `Catch` sekcji, które obsługują błędy i wyjątki, które występują w przepływie pracy.

<a name="close-session-fail"></a>

### <a name="close-a-session-in-a-queue-and-fail"></a>Zamykanie sesji w kolejce i niepowodzenie

Ta akcja Service Bus jest zawsze uruchamiana jako pierwsza akcja w `Catch` zakresie i zamyka sesję w kolejce.

* W akcji Service Bus **Zamknij sesję w kolejce i niepowodzenie** Podaj nazwę kolejki Service Bus.

  ![Akcja Service Bus — "zamykanie sesji w kolejce i niepowodzenie"](./media/send-related-messages-sequential-convoy/close-session-in-queue-fail.png)

Następnie przepływ pracy tworzy tablicę, która zawiera dane wejściowe i wyjściowe ze wszystkich akcji w zakresie, `Try` dzięki czemu aplikacja logiki będzie mogła uzyskać dostęp do informacji o błędzie lub niepowodzeniu.

<a name="find-failure-message"></a>

### <a name="find-failure-msg-from-try-block"></a>Znajdź komunikat o niepowodzeniu z bloku "try"

Ta [Akcja **filtrowania tablicy**](../logic-apps/logic-apps-perform-data-operations.md#filter-array-action) tworzy tablicę, która zawiera dane wejściowe i wyjściowe ze wszystkich akcji wewnątrz `Try` zakresu na podstawie określonych kryteriów przy użyciu [ `result()` funkcji](../logic-apps/workflow-definition-language-functions-reference.md#result). W takim przypadku ta akcja zwraca dane wyjściowe z akcji, które mają `Failed` stan przy użyciu [ `equals()` funkcji](../logic-apps/workflow-definition-language-functions-reference.md#equals) i [ `item()` funkcji](../logic-apps/workflow-definition-language-functions-reference.md#item).

![Akcja filtrowania tablicy — "Znajdź komunikat o niepowodzeniu z bloku" try "](./media/send-related-messages-sequential-convoy/find-failure-message.png)

Oto definicja JSON dla tej akcji:

```json
"Find_failure_msg_from_'Try'_block": {
   "inputs": {
      "from": "@Result('Try')",
      "where": "@equals(item()['status'], 'Failed')"
   },
   "runAfter": {
      "Close_the_session_in_the_queue_and_fail": [
         "Succeeded"
      ]
   },
   "type": "Query"
},
```

Następnie przepływ pracy tworzy tablicę z obiektem JSON, który zawiera informacje o błędzie w tablicy zwracanej z `Find failure msg from 'Try' block` akcji.

<a name="select-error-details"></a>

### <a name="select-error-details"></a>Wybierz szczegóły błędu

Ta [Akcja **SELECT**](../logic-apps/logic-apps-perform-data-operations.md#select-action) tworzy tablicę zawierającą obiekty JSON na podstawie tablicy wejściowej, która jest wyprowadzana z poprzedniej akcji `Find failure msg from 'Try' block` . W tym celu ta akcja zwraca tablicę, która ma tylko określone właściwości dla każdego obiektu w tablicy. W tym przypadku tablica zawiera nazwę akcji i właściwości wyniku błędu.

![Wybierz akcję — "Wybierz szczegóły błędu"](./media/send-related-messages-sequential-convoy/select-error-details.png)

Oto definicja JSON dla tej akcji:

```json
"Select_error_details": {
   "inputs": {
      "from": "@body('Find_failure_msg_from_''Try''_block')[0]['outputs']",
      "select": {
         "action": "@item()['name']",
         "errorResult": "@item()"
      }
   },
   "runAfter": {
      "Find_failure_msg_from_'Try'_block": [
         "Succeeded"
      ]
   },
   "type": "Select"
},
```

Następnie przepływ pracy kończy działanie aplikacji logiki i zwraca stan uruchomienia wraz z więcej informacji o błędzie lub niepowodzeniu.

<a name="terminate-logic-app"></a>

### <a name="terminate-logic-app-run"></a>Przerwij przebieg aplikacji logiki

Ta [Akcja **zakończenia**](../logic-apps/logic-apps-workflow-actions-triggers.md#terminate-action) powoduje zatrzymanie działania aplikacji logiki i zwrócenie jej `Failed` jako stanu dla uruchomienia aplikacji logiki wraz z identyfikatorem sesji oraz wynikiem błędu z `Select error details` akcji.

![Przerwij akcję w celu zatrzymania uruchomienia aplikacji logiki](./media/send-related-messages-sequential-convoy/terminate-logic-app-run.png)

Oto definicja JSON dla tej akcji:

```json
"Terminate": {
   "description": "This Failure Termination only runs if the Close Session upon Failure action runs - otherwise the LA will be terminated as Success",
   "inputs": {
      "runError": {
         "code": "",
         "message": "There was an error processing messages for Session ID @{triggerBody()?['SessionId']}. The following error(s) occurred: @{body('Select_error_details')['errorResult']}"
         },
         "runStatus": "Failed"
      },
      "runAfter": {
         "Select_error_details": [
            "Succeeded"
         ]
      },
      "type": "Terminate"
   }
},
```

## <a name="save-and-run-logic-app"></a>Zapisz i uruchom aplikację logiki

Po ukończeniu szablonu możesz teraz zapisać aplikację logiki. Na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

Aby przetestować aplikację logiki, Wyślij komunikaty do kolejki Service Bus. 

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat [wyzwalaczy i akcji łącznika Service Bus](/connectors/servicebus/)
