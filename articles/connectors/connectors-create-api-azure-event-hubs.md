---
title: Nawiązywanie połączenia z usługą Azure Event Hubs
description: Twórz zautomatyzowane zadania i przepływy pracy, które monitorują zdarzenia i zarządzają nimi przy użyciu usługi Azure Event Hubs i Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: conceptual
ms.date: 04/23/2019
tags: connectors
ms.openlocfilehash: 198a5da63ed90937c53f7f12f3559f15100e8f19
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88031429"
---
# <a name="monitor-receive-and-send-events-with-azure-event-hubs-and-azure-logic-apps"></a>Monitorowanie, odbieranie i wysyłanie zdarzeń za pomocą usług Azure Event Hubs i Azure Logic Apps

W tym artykule pokazano, jak można monitorować zdarzenia wysyłane do [usługi azure Event Hubs](../event-hubs/event-hubs-about.md) i zarządzać nimi z poziomu aplikacji logiki za pomocą łącznika usługi Azure Event Hubs. Dzięki temu możesz tworzyć aplikacje logiki, które automatyzują zadania i przepływy pracy służące do sprawdzania, wysyłania i otrzymywania zdarzeń z centrum zdarzeń. Informacje techniczne dotyczące konkretnego łącznika można znaleźć w [dokumentacji łącznika usługi Azure Event Hubs](/connectors/eventhubs/) </a> .

## <a name="prerequisites"></a>Wymagania wstępne

* Konto i subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/). 

* [Przestrzeń nazw i centrum zdarzeń usługi Azure Event Hubs](../event-hubs/event-hubs-create.md)

* Aplikacja logiki, do której chcesz uzyskać dostęp do centrum zdarzeń. Aby uruchomić aplikację logiki przy użyciu wyzwalacza usługi Azure Event Hubs, musisz mieć [pustą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).
Jeśli dopiero zaczynasz tworzyć aplikacje logiki, zapoznaj [się z tematem Azure Logic Apps](../logic-apps/logic-apps-overview.md) i [Szybki Start: Tworzenie pierwszej aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="permissions-connection-string"></a>

## <a name="check-permissions-and-get-connection-string"></a>Sprawdź uprawnienia i Pobierz parametry połączenia

Aby mieć pewność, że aplikacja logiki może uzyskać dostęp do centrum zdarzeń, Sprawdź swoje uprawnienia i Pobierz parametry połączenia dla przestrzeni nazw Event Hubs.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Przejdź do *obszaru nazw*Event Hubs, a nie do określonego centrum zdarzeń. 

1. W menu Przestrzeń nazw w obszarze **Ustawienia**wybierz pozycję **zasady dostępu współdzielonego**. W obszarze **oświadczenia**Sprawdź, czy masz uprawnienia do **zarządzania** tą przestrzenią nazw.

   ![Zarządzanie uprawnieniami dla przestrzeni nazw centrum zdarzeń](./media/connectors-create-api-azure-event-hubs/event-hubs-namespace.png)

1. Jeśli chcesz później ręcznie wprowadzić informacje o połączeniu, Pobierz parametry połączenia dla przestrzeni nazw Event Hubs.

   1. W obszarze **zasady**wybierz pozycję **RootManageSharedAccessKey**.

   1. Znajdź parametry połączenia klucza podstawowego. Wybierz przycisk Kopiuj i Zapisz parametry połączenia do późniejszego użycia.

      ![Kopiuj parametry połączenia Event Hubs przestrzeni nazw](media/connectors-create-api-azure-event-hubs/find-event-hub-namespace-connection-string.png)

      > [!TIP]
      > Aby sprawdzić, czy parametry połączenia są skojarzone z przestrzenią nazw Event Hubs, czy z określonym centrum zdarzeń, upewnij się, że parametry połączenia nie mają `EntityPath`   parametru. Jeśli ten parametr zostanie znaleziony, ciąg połączenia dotyczy określonego centrum zdarzeń "Entity" i nie jest poprawnym ciągiem używanym z aplikacją logiki.

1. Teraz Kontynuuj [Dodawanie wyzwalacza Event Hubs](#add-trigger) lub [Dodaj akcję Event Hubs](#add-action).

<a name="add-trigger"></a>

## <a name="add-event-hubs-trigger"></a>Dodawanie wyzwalacza usługi Azure Event Hubs

W Azure Logic Apps każda aplikacja logiki musi rozpoczynać się od [wyzwalacza](../logic-apps/logic-apps-overview.md#logic-app-concepts), który jest uruchamiany w przypadku wystąpienia konkretnego zdarzenia lub spełnienia określonego warunku. Za każdym razem, gdy wyzwala wyzwalacz, aparat Logic Apps tworzy wystąpienie aplikacji logiki i uruchamia przepływ pracy aplikacji.

Ten przykład pokazuje, jak uruchomić przepływ pracy aplikacji logiki, gdy do centrum zdarzeń są wysyłane nowe zdarzenia. 

> [!NOTE]
> Wszystkie wyzwalacze centrum zdarzeń są wyzwalaczami *długotrwałych* , co oznacza, że wyzwalacz przetwarza wszystkie zdarzenia, a następnie czeka 30 sekund na partycję, aby więcej zdarzeń pojawiło się w centrum zdarzeń. Tak więc, jeśli wyzwalacz jest skonfigurowany z czterema partycjami, to opóźnienie może trwać do dwóch minut, zanim wyzwalacz zakończy sondowanie wszystkich partycji. Jeśli żadne zdarzenia nie są odbierane w tym opóźnieniu, uruchomienie wyzwalacza zostanie pominięte. W przeciwnym razie wyzwalacz kontynuuje odczytywanie zdarzeń do momentu, gdy centrum zdarzeń jest puste. Sonda następnego wyzwalacza odbywa się na podstawie interwału cyklu określonego we właściwościach wyzwalacza.

1. W Azure Portal lub Visual Studio Utwórz pustą aplikację logiki, która otwiera Logic Apps projektanta. Ten przykład używa Azure Portal.

1. W polu wyszukiwania wprowadź "Centra zdarzeń" jako filtr. Z listy Wyzwalacze wybierz ten wyzwalacz: **gdy zdarzenia są dostępne w centrum zdarzeń — Event Hubs**

   ![Wybierz wyzwalacz](./media/connectors-create-api-azure-event-hubs/find-event-hubs-trigger.png)

1. Jeśli zostanie wyświetlony monit o podanie szczegółów połączenia, [Utwórz teraz połączenie Event Hubs](#create-connection). 

1. W wyzwalaczu podaj informacje o centrum zdarzeń, które chcesz monitorować. Aby uzyskać więcej właściwości, Otwórz listę **Dodaj nowy parametr** . Wybranie parametru powoduje dodanie tej właściwości do karty wyzwalacza.

   ![Właściwości wyzwalacza](./media/connectors-create-api-azure-event-hubs/event-hubs-trigger.png)

   | Właściwość | Wymagane | Opis |
   |----------|----------|-------------|
   | **Nazwa centrum zdarzeń** | Tak | Nazwa centrum zdarzeń, które ma być monitorowane. |
   | **Typ zawartości** | Nie | Typ zawartości zdarzenia. Wartość domyślna to `application/octet-stream`. |
   | **Nazwa grupy konsumentów** | Nie | [Nazwa grupy konsumentów centrum zdarzeń](../event-hubs/event-hubs-features.md#consumer-groups) , która ma być używana do odczytywania zdarzeń. Jeśli nie zostanie określony, zostanie użyta domyślna grupa odbiorców. |
   | **Maksymalna liczba zdarzeń** | Nie | Maksymalna liczba zdarzeń. Wyzwalacz zwraca między jedną i liczbą zdarzeń określonych przez tę właściwość. |
   | **Interwał** | Tak | Dodatnia liczba całkowita, która opisuje, jak często przebiega przepływ pracy na podstawie częstotliwości |
   | **Częstotliwość** | Tak | Jednostka czasu dla cyklu |
   ||||

   **Dodatkowe właściwości**

   | Właściwość | Wymagane | Opis |
   |----------|----------|-------------|
   | **Schemat zawartości** | Nie | Schemat zawartości JSON dla zdarzeń odczytywanych z centrum zdarzeń. Na przykład, jeśli określisz schemat zawartości, możesz wyzwolić aplikację logiki tylko dla tych zdarzeń, które pasują do schematu. |
   | **Minimalny klucz partycji** | Nie | Wprowadź minimalny identyfikator [partycji](../event-hubs/event-hubs-features.md#partitions) , który ma zostać odczytany. Domyślnie wszystkie partycje są odczytywane. |
   | **Maksymalny klucz partycji** | Nie | Wprowadź maksymalny identyfikator [partycji](../event-hubs/event-hubs-features.md#partitions) , który ma zostać odczytany. Domyślnie wszystkie partycje są odczytywane. |
   | **Strefa czasowa** | Nie | Ma zastosowanie tylko w przypadku określenia czasu rozpoczęcia, ponieważ ten wyzwalacz nie akceptuje przesunięcia czasu UTC. Wybierz strefę czasową, która ma zostać zastosowana. <p>Aby uzyskać więcej informacji, zobacz [Tworzenie i uruchamianie cyklicznych zadań i przepływów pracy za pomocą Azure Logic Apps](../connectors/connectors-native-recurrence.md). |
   | **Godzina rozpoczęcia** | Nie | Podaj godzinę rozpoczęcia w tym formacie: <p>RRRR-MM-DDTgg: mm: SS w przypadku wybrania strefy czasowej<p>-lub-<p>RRRR-MM-DDTgg: mm: SSS, jeśli nie wybierzesz strefy czasowej<p>Aby uzyskać więcej informacji, zobacz [Tworzenie i uruchamianie cyklicznych zadań i przepływów pracy za pomocą Azure Logic Apps](../connectors/connectors-native-recurrence.md). |
   ||||

1. Gdy skończysz, na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

1. Teraz Kontynuuj dodawanie co najmniej jednej akcji do aplikacji logiki w celu wykonywania zadań, które chcesz wykonać z wynikami wyzwalacza. 

   Na przykład, aby filtrować zdarzenia na podstawie określonej wartości, takiej jak kategoria, można dodać warunek, aby Akcja **Wyślij zdarzenie** wysyła tylko zdarzenia, które spełniają warunek. 

<a name="add-action"></a>

## <a name="add-event-hubs-action"></a>Dodawanie akcji usługi Event Hubs

W Azure Logic Apps [Akcja](../logic-apps/logic-apps-overview.md#logic-app-concepts) to krok w przepływie pracy, który następuje po wyzwalaczu lub innej akcji. W tym przykładzie aplikacja logiki rozpoczyna się od wyzwalacza Event Hubs, który sprawdza, czy są nowe zdarzenia w centrum zdarzeń.

1. W Azure Portal lub w programie Visual Studio Otwórz aplikację logiki w programie Logic Apps Designer. Ten przykład używa Azure Portal.

1. W obszarze wyzwalacza lub akcji wybierz pozycję **nowy krok**.

   Aby dodać akcję między istniejącymi krokami, przesuń wskaźnik myszy na strzałkę łączącą. 
   Wybierz wyświetlony znak plus ( **+** ), a następnie wybierz pozycję **Dodaj akcję**.

1. W polu wyszukiwania wprowadź "Centra zdarzeń" jako filtr.
Z listy Akcje wybierz pozycję Ta akcja: **Wyślij Zdarzenie — Event Hubs**

   ![Wybierz akcję "Wyślij zdarzenie"](./media/connectors-create-api-azure-event-hubs/find-event-hubs-action.png)

1. Jeśli zostanie wyświetlony monit o podanie szczegółów połączenia, [Utwórz teraz połączenie Event Hubs](#create-connection). 

1. W akcji podaj informacje o zdarzeniach, które chcesz wysłać. Aby uzyskać więcej właściwości, Otwórz listę **Dodaj nowy parametr** . Wybranie parametru powoduje dodanie tej właściwości do karty akcji.

   ![Wybierz nazwę centrum zdarzeń i podaj zawartość zdarzenia](./media/connectors-create-api-azure-event-hubs/event-hubs-send-event-action.png)

   | Właściwość | Wymagane | Opis |
   |----------|----------|-------------|
   | **Nazwa centrum zdarzeń** | Tak | Centrum zdarzeń, w którym chcesz wysłać zdarzenie |
   | **Zawartość** | Nie | Zawartość dla zdarzenia, które chcesz wysłać. |
   | **Właściwości** | Nie | Właściwości i wartości aplikacji do wysłania |
   | **Klucz partycji** | Nie | Identyfikator [partycji](../event-hubs/event-hubs-features.md#partitions) , dla której ma zostać wysłane zdarzenie |
   ||||

   Na przykład można wysłać dane wyjściowe z wyzwalacza Event Hubs do innego centrum zdarzeń:

   ![Przykład wysyłania zdarzenia](./media/connectors-create-api-azure-event-hubs/event-hubs-send-event-action-example.png)

1. Gdy skończysz, na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

<a name="create-connection"></a>

## <a name="connect-to-your-event-hub"></a>Nawiązywanie połączenia z centrum zdarzeń

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)] 

1. Po wyświetleniu monitu o podanie informacji o połączeniu podaj następujące informacje:

   | Właściwość | Wymagany | Wartość | Opis |
   |----------|----------|-------|-------------|
   | **Nazwa połączenia** | Tak | <*Nazwa połączenia*> | Nazwa do utworzenia dla połączenia |
   | **Event Hubs przestrzeń nazw** | Tak | <*Event-Hubs — przestrzeń nazw*> | Wybierz przestrzeń nazw Event Hubs, której chcesz użyć. |
   |||||  

   Na przykład:

   ![Utwórz połączenie centrum zdarzeń](./media/connectors-create-api-azure-event-hubs/create-event-hubs-connection-1.png)

   Aby ręcznie wprowadzić parametry połączenia, wybierz pozycję **ręcznie wprowadź informacje o połączeniu**. 
   Dowiedz się [, jak znaleźć parametry połączenia](#permissions-connection-string).

2. Wybierz zasady Event Hubs, które mają być używane, jeśli nie zostały jeszcze wybrane. Wybierz pozycję **Utwórz**.

   ![Tworzenie połączenia centrum zdarzeń — część 2](./media/connectors-create-api-azure-event-hubs/create-event-hubs-connection-2.png)

3. Po utworzeniu połączenia Kontynuuj [dodawanie Event Hubs wyzwalacza](#add-trigger) lub [Dodaj akcję Event Hubs](#add-action).

## <a name="connector-reference"></a>Dokumentacja łączników

Aby uzyskać szczegółowe informacje techniczne, takie jak wyzwalacze, akcje i limity, zgodnie z opisem w pliku Swagger łącznika, zobacz [stronę odwołania łącznika](/connectors/eventhubs/).

> [!NOTE]
> W przypadku aplikacji logiki w [środowisku usługi integracji (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), wersja tego łącznika z oznaczeniem ISE w zamian używa [limitów komunikatów ISE](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) .

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat innych [łączników Logic Apps](../connectors/apis-list.md)
