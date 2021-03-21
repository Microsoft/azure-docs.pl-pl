---
title: Komunikaty programu Exchange z Azure Service Bus
description: Twórz zautomatyzowane zadania i przepływy pracy, które wysyłają i odbierają wiadomości za pomocą Azure Service Bus w Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm, azla
ms.topic: conceptual
ms.date: 02/10/2021
tags: connectors
ms.openlocfilehash: 98d2ee8a85d25065c0021841a9b99a6d616a35d8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100367422"
---
# <a name="exchange-messages-in-the-cloud-by-using-azure-logic-apps-and-azure-service-bus"></a>Wymiana komunikatów w chmurze przy użyciu Azure Logic Apps i Azure Service Bus

Za pomocą [Azure Logic Apps](../logic-apps/logic-apps-overview.md) i łącznika [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) można tworzyć automatyczne zadania i przepływy pracy, które przesyłają dane, takie jak zamówienia sprzedaży i zakupu, dzienniki i przesunięcia spisu między aplikacjami w organizacji. Łącznik nie tylko monitoruje, wysyła i zarządza komunikatami, ale wykonuje także akcje z kolejkami, sesjami, tematami, subskrypcjami i tak dalej, na przykład:

* Monitoruj po nadejściu wiadomości (Autouzupełnianie) lub odebraniu (wgląd-blokada) w kolejkach, tematach i subskrypcjach tematów.
* Wysyłanie komunikatów.
* Tworzenie i usuwanie subskrypcji tematu.
* Zarządzanie komunikatami w kolejkach i subskrypcjami tematów, na przykład pobieranie, pobieranie odroczone, pełne, odkładanie, porzucanie i martwe.
* Odnów blokady komunikatów i sesji w kolejkach i subskrypcjach tematów.
* Zamknij sesje w kolejkach i tematach.

Można użyć wyzwalaczy, które pobierają odpowiedzi z Service Bus i udostępnić dane wyjściowe innym akcjom w usłudze Logic Apps. Możesz również mieć inne akcje, które używają danych wyjściowych z Service Bus akcji. Jeśli dopiero zaczynasz Service Bus i Logic Apps, sprawdź, [co to jest Azure Service Bus?](../service-bus-messaging/service-bus-messaging-overview.md) i [co to jest Azure Logic Apps](../logic-apps/logic-apps-overview.md)?

[!INCLUDE [Warning about creating infinite loops](../../includes/connectors-infinite-loops.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Konto i subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* Service Bus przestrzeń nazw i jednostki obsługi komunikatów, takie jak kolejka. Te elementy i aplikacja logiki muszą używać tej samej subskrypcji platformy Azure. Jeśli nie masz tych elementów, Dowiedz się, jak [utworzyć Service Bus przestrzeń nazw i kolejkę](../service-bus-messaging/service-bus-create-namespace-portal.md).

* Podstawowa wiedza [na temat tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikacja logiki, w której jest używana Service Bus przestrzeni nazw i jednostki obsługi komunikatów. Twoja aplikacja logiki i Usługa Service Bus muszą korzystać z tej samej subskrypcji platformy Azure. Aby uruchomić przepływ pracy z wyzwalaczem Service Bus, [Utwórz pustą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). Aby użyć akcji Service Bus w przepływie pracy, uruchom aplikację logiki z innym wyzwalaczem, na przykład [wyzwalaczem cyklu](../connectors/connectors-native-recurrence.md).

<a name="permissions-connection-string"></a>

## <a name="check-permissions"></a>Sprawdź uprawnienia

Upewnij się, że aplikacja logiki ma uprawnienia do uzyskiwania dostępu do przestrzeni nazw Service Bus.

1. W [Azure Portal](https://portal.azure.com)Zaloguj się przy użyciu konta platformy Azure.

1. Przejdź do *przestrzeni nazw* Service Bus. Na stronie przestrzeń nazw w obszarze **Ustawienia** wybierz pozycję **zasady dostępu współdzielonego**. W obszarze **oświadczenia** Sprawdź, czy masz uprawnienia do **zarządzania** tą przestrzenią nazw.

   ![Zarządzaj uprawnieniami dla Service Bus przestrzeni nazw](./media/connectors-create-api-azure-service-bus/azure-service-bus-namespace.png)

1. Pobierz parametry połączenia dla przestrzeni nazw Service Bus. Ten ciąg jest potrzebny, gdy podajesz informacje o połączeniu w aplikacji logiki.

   1. W okienku **zasady dostępu współdzielonego** wybierz pozycję **RootManageSharedAccessKey**.

   1. Obok podstawowych parametrów połączenia wybierz przycisk Kopiuj. Zapisz parametry połączenia do późniejszego użycia.

      ![Kopiuj parametry połączenia Service Bus przestrzeni nazw](./media/connectors-create-api-azure-service-bus/find-service-bus-connection-string.png)

   > [!TIP]
   > Aby sprawdzić, czy parametry połączenia są skojarzone z przestrzenią nazw Service Bus, czy z jednostką obsługi komunikatów, taką jak kolejka, Wyszukaj parametry połączenia dla `EntityPath` parametru. Jeśli ten parametr zostanie znaleziony, ciąg połączenia jest przeznaczony dla określonej jednostki i nie jest poprawnym ciągiem używanym z aplikacją logiki.

## <a name="add-service-bus-trigger"></a>Dodaj wyzwalacz Service Bus

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Zaloguj się do [Azure Portal](https://portal.azure.com)i Otwórz pustą aplikację logiki w Projektancie aplikacji logiki.

1. W polu wyszukiwania portalu wprowadź wartość `azure service bus` . Z wyświetlonej listy Wyzwalacze wybierz wyzwalacz, który chcesz.

   Na przykład, aby wyzwolić aplikację logiki, gdy nowy element zostanie wysłany do kolejki Service Bus, wybierz opcję **po odebraniu komunikatu w kolejce (Autouzupełnianie)** .

   ![Wybierz wyzwalacz Service Bus](./media/connectors-create-api-azure-service-bus/select-service-bus-trigger.png)

   Poniżej przedstawiono niektóre zagadnienia dotyczące korzystania z wyzwalacza Service Bus:

   * Wszystkie wyzwalacze Service Bus są wyzwalaczami *długiego sondowania* . Ten opis oznacza, że podczas uruchamiania wyzwalacza, wyzwalacz przetwarza wszystkie komunikaty, a następnie czeka 30 sekund, aby więcej komunikatów pojawiło się w ramach subskrypcji kolejki lub tematu. Jeśli w ciągu 30 sekund nie pojawią się żadne komunikaty, uruchomienie wyzwalacza zostanie pominięte. W przeciwnym razie wyzwalacz będzie kontynuował odczytywanie wiadomości do momentu, gdy subskrypcja kolejki lub tematu nie jest pusta. Sonda następnego wyzwalacza zależy od interwału cyklu określonego we właściwościach wyzwalacza.

   * Niektóre wyzwalacze, takie jak **po nadejściu co najmniej jednego komunikatu w kolejce (AutoComplete)** , mogą zwrócić jeden lub więcej komunikatów. Kiedy te wyzwalacze są wyzwalane, zwracają między jedną i liczbę komunikatów, które są określone przez wartość właściwości **Maksymalna liczba komunikatów** wyzwalacza.

     > [!NOTE]
     > Wyzwalacz autouzupełniania automatycznie kończy komunikat, ale uzupełnianie odbywa się tylko przy następnym wywołaniu do Service Bus. Takie zachowanie może mieć wpływ na projekt aplikacji logiki. Na przykład Unikaj zmiany współbieżności w wyzwalaczu autouzupełniania, ponieważ ta zmiana może spowodować zduplikowane komunikaty, jeśli aplikacja logiki przejdzie do stanu ograniczenia. Zmiana kontroli współbieżności powoduje utworzenie następujących warunków: wyzwalacze ograniczające są pomijane przy użyciu `WorkflowRunInProgress` kodu, operacja ukończenia nie zostanie wykonana, a następne uruchomienie wyzwalacza następuje po interwale sondowania. Musisz ustawić czas trwania blokady usługi Service Bus na wartość dłuższą niż interwał sondowania. Jednak pomimo tego ustawienia komunikat nadal może nie zostać ukończony, jeśli aplikacja logiki pozostanie w stanie ograniczenia w kolejnym interwale sondowania.

   * W przypadku [włączenia ustawienia współbieżności](../logic-apps/logic-apps-workflow-actions-triggers.md#change-trigger-concurrency) dla wyzwalacza Service Bus wartość domyślna dla `maximumWaitingRuns` Właściwości to 10. Na podstawie ustawienia czasu trwania blokady jednostki Service Bus i czasu trwania uruchomienia dla wystąpienia aplikacji logiki ta wartość domyślna może być zbyt duża i może spowodować wyjątek "blokada utraconych". Aby znaleźć optymalną wartość dla danego scenariusza, Rozpocznij testowanie z wartością 1 lub 2 dla `maximumWaitingRuns` właściwości. Aby zmienić maksymalną wartość oczekujących uruchomień, zobacz [Limit uruchamiania oczekujących zmian](../logic-apps/logic-apps-workflow-actions-triggers.md#change-waiting-runs).

1. Jeśli wyzwalacz nawiązuje połączenie z przestrzenią nazw Service Bus po raz pierwszy, wykonaj następujące kroki, gdy projektant aplikacji logiki monituje o informacje o połączeniu.

   1. Podaj nazwę połączenia i wybierz swoją przestrzeń nazw Service Bus.

      ![Zrzut ekranu pokazujący podawanie nazwy połączenia i wybieranie Service Bus przestrzeni nazw](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-trigger-1.png)

      Aby zamiast tego wprowadzić parametry połączenia, wybierz pozycję **ręcznie wprowadź informacje o połączeniu**. Jeśli nie masz parametrów połączenia, Dowiedz się, [jak znaleźć parametry połączenia](#permissions-connection-string).

   1. Wybierz zasady Service Bus i wybierz pozycję **Utwórz**.

      ![Zrzut ekranu pokazujący wybór zasad Service Bus](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-trigger-2.png)

   1. Wybierz żądaną jednostkę obsługi komunikatów, na przykład kolejkę lub temat. Na potrzeby tego przykładu wybierz kolejkę Service Bus.
   
      ![Zrzut ekranu pokazujący wybór kolejki Service Bus](./media/connectors-create-api-azure-service-bus/service-bus-select-queue-trigger.png)

1. Podaj informacje niezbędne do wybranego wyzwalacza. Aby dodać inne dostępne właściwości do akcji, Otwórz listę **Dodaj nowy parametr** i wybierz żądane właściwości.

   Dla wyzwalacza tego przykładu wybierz interwał sondowania i częstotliwość sprawdzania kolejki.

   ![Zrzut ekranu, który pokazuje ustawienie interwału sondowania w wyzwalaczu Service Bus](./media/connectors-create-api-azure-service-bus/service-bus-trigger-details.png)

   Aby uzyskać więcej informacji o dostępnych wyzwalaczach i właściwościach, zobacz [stronę odwołania](/connectors/servicebus/)łącznika.

1. Kontynuuj tworzenie aplikacji logiki, dodając odpowiednie akcje.

   Na przykład możesz dodać akcję, która wysyła wiadomość e-mail po nadejściu nowej wiadomości. Gdy wyzwalacz sprawdzi swoją kolejkę i odnajdzie nowy komunikat, aplikacja logiki uruchamia wybrane akcje dla znalezionego komunikatu.

## <a name="add-service-bus-action"></a>Dodaj akcję Service Bus

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. W [Azure Portal](https://portal.azure.com)Otwórz aplikację logiki w Projektancie aplikacji logiki.

1. W kroku, w którym chcesz dodać akcję, wybierz pozycję **nowy krok**.

   Aby dodać akcję między krokami, przesuń wskaźnik myszy nad strzałkę między tymi krokami. Wybierz wyświetlony znak plus ( **+** ), a następnie wybierz pozycję **Dodaj akcję**.

1. W obszarze **Wybierz akcję**, w polu wyszukiwania wpisz `azure service bus` . Z wyświetlonej listy Akcje wybierz żądaną akcję. 

   Na potrzeby tego przykładu wybierz akcję **Wyślij wiadomość** .

   ![Zrzut ekranu pokazujący wybór akcji Service Bus](./media/connectors-create-api-azure-service-bus/select-service-bus-send-message-action.png) 

1. Jeśli akcja nawiązuje połączenie z przestrzenią nazw Service Bus po raz pierwszy, wykonaj następujące kroki, gdy projektant aplikacji logiki monituje o informacje o połączeniu.

   1. Podaj nazwę połączenia i wybierz swoją przestrzeń nazw Service Bus.

      ![Zrzut ekranu pokazujący podawanie nazwy połączenia i wybieranie Service Bus przestrzeni nazw](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-action-1.png)

      Aby zamiast tego wprowadzić parametry połączenia, wybierz pozycję **ręcznie wprowadź informacje o połączeniu**. Jeśli nie masz parametrów połączenia, Dowiedz się, [jak znaleźć parametry połączenia](#permissions-connection-string).

   1. Wybierz zasady Service Bus i wybierz pozycję **Utwórz**.

      ![Zrzut ekranu pokazujący Wybieranie zasad Service Bus i wybieranie przycisku Utwórz](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-action-2.png)

   1. Wybierz żądaną jednostkę obsługi komunikatów, na przykład kolejkę lub temat. Na potrzeby tego przykładu wybierz kolejkę Service Bus.

      ![Zrzut ekranu pokazujący wybór kolejki Service Bus](./media/connectors-create-api-azure-service-bus/service-bus-select-queue-action.png)

1. Podaj niezbędne szczegóły wybranej akcji. Aby dodać inne dostępne właściwości do akcji, Otwórz listę **Dodaj nowy parametr** i wybierz żądane właściwości.

   Na przykład wybierz **zawartość** i właściwości **typu zawartości** , aby dodać je do akcji. Następnie określ zawartość wiadomości, która ma zostać wysłana.

   ![Zrzut ekranu pokazujący podanie typu zawartości komunikatu i szczegółów](./media/connectors-create-api-azure-service-bus/service-bus-send-message-details.png)

   Aby uzyskać więcej informacji o dostępnych akcjach i ich właściwościach, zobacz [stronę odwołania](/connectors/servicebus/)łącznika.

1. Kontynuuj tworzenie aplikacji logiki, dodając wszystkie inne akcje.

   Na przykład możesz dodać akcję, która wysyła wiadomość e-mail, aby potwierdzić, że wiadomość została wysłana.

1. Zapisz aplikację logiki. Na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

<a name="sequential-convoy"></a>

## <a name="send-correlated-messages-in-order"></a>Wysyłanie skorelowanych komunikatów w kolejności

Gdy musisz wysyłać powiązane wiadomości w określonej kolejności, możesz użyć [wzorca *sekwencyjnego konwoju*](/azure/architecture/patterns/sequential-convoy) za pomocą [łącznika Azure Service Bus](../connectors/connectors-create-api-servicebus.md). Skorelowane komunikaty mają właściwość definiującą relację między tymi komunikatami, na przykład identyfikator [sesji](../service-bus-messaging/message-sessions.md) w Service Bus.

Podczas tworzenia aplikacji logiki można wybrać **skorelowaną dostawę w kolejności przy użyciu szablonu sesji usługi Service Bus** , który implementuje wzorzec sekwencyjnej konwoju. Aby uzyskać więcej informacji, zobacz [wysyłanie powiązanych komunikatów w kolejności](../logic-apps/send-related-messages-sequential-convoy.md).

## <a name="delays-in-updates-to-your-logic-app-taking-effect"></a>Opóźnienia aktualizacji aplikacji logiki

Jeśli interwał sondowania wyzwalacza Service Bus jest mały, na przykład 10 sekund, aktualizacje aplikacji logiki mogą nie obowiązywać przez maksymalnie 10 minut. Aby obejść ten problem, możesz wyłączyć aplikację logiki, wprowadzić zmiany, a następnie ponownie włączyć aplikację logiki.

<a name="connector-reference"></a>

## <a name="connector-reference"></a>Dokumentacja łączników

Z poziomu usługi Service Bus łącznik Service Bus może jednocześnie zaoszczędzić do 1 500 unikatowych sesji w pamięci podręcznej łączników, za pomocą [Service Bus jednostki obsługi komunikatów, takiej jak subskrypcja lub temat](../service-bus-messaging/service-bus-queues-topics-subscriptions.md). Jeśli liczba sesji przekracza ten limit, stare sesje są usuwane z pamięci podręcznej. Aby uzyskać więcej informacji, zobacz [sesje komunikatów](../service-bus-messaging/message-sessions.md).

Aby poznać inne szczegóły techniczne dotyczące wyzwalaczy, akcji i limitów, które są opisane w opisie struktury Swagger łącznika, przejrzyj [stronę odwołania łącznika](/connectors/servicebus/). Aby uzyskać więcej informacji na temat Azure Service Bus Messaging, zobacz [co to jest Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)?

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat innych [łączników Logic Apps](../connectors/apis-list.md)
