---
title: Połącz z Wyszukiwanie Bing
description: Automatyzowanie zadań i przepływów pracy, które wyszukują wyniki Wyszukiwanie Bing przy użyciu Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 05/21/2018
tags: connectors
ms.openlocfilehash: 306298e4338665ef52add7f46d6da8675c97c3e2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101716554"
---
# <a name="find-results-in-bing-search-by-using-azure-logic-apps"></a>Znajdź wyniki w Wyszukiwanie Bing przy użyciu Azure Logic Apps

W tym artykule pokazano, jak można znaleźć wiadomości, klipy wideo i inne elementy za pośrednictwem Wyszukiwanie Bing z poziomu aplikacji logiki za pomocą łącznika Wyszukiwanie Bing. Dzięki temu można tworzyć aplikacje logiki, które automatyzują zadania i przepływy pracy związane z przetwarzaniem wyników wyszukiwania i udostępniają te elementy innym akcjom. 

Na przykład można znaleźć elementy wiadomości na podstawie kryteriów wyszukiwania i ogłosić te elementy jako tweety w kanale usługi Twitter.

Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/). Jeśli dopiero zaczynasz tworzyć aplikacje logiki, zapoznaj [się z tematem Azure Logic Apps](../logic-apps/logic-apps-overview.md) i [Szybki Start: Tworzenie pierwszej aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).
Informacje techniczne dotyczące konkretnego łącznika znajdują się w [dokumentacji dotyczącej łącznika wyszukiwanie Bing](/connectors/bingsearch/).

## <a name="prerequisites"></a>Wymagania wstępne

* [Konto Cognitive Services](../cognitive-services/cognitive-services-apis-create-account.md)

* [Wyszukiwanie Bing klucz interfejsu API](https://azure.microsoft.com/try/cognitive-services/?api=bing-news-search-api), który zapewnia dostęp z aplikacji logiki do interfejsy API wyszukiwania Bing

* Aplikacja logiki, do której chcesz uzyskać dostęp do centrum zdarzeń. Aby uruchomić aplikację logiki z wyzwalaczem Wyszukiwanie Bing, musisz mieć [pustą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="add-trigger"></a>

## <a name="add-a-bing-search-trigger"></a>Dodawanie wyzwalacza Wyszukiwanie Bing

W Azure Logic Apps każda aplikacja logiki musi rozpoczynać się od [wyzwalacza](../logic-apps/logic-apps-overview.md#logic-app-concepts), który jest uruchamiany w przypadku wystąpienia konkretnego zdarzenia lub spełnienia określonego warunku. Za każdym razem, gdy wyzwala wyzwalacz, aparat Logic Apps tworzy wystąpienie aplikacji logiki i uruchamia przepływ pracy aplikacji.

1. W Azure Portal lub Visual Studio Utwórz pustą aplikację logiki, która otwiera projektanta aplikacji logiki. Ten przykład używa Azure Portal.

2. W polu wyszukiwania wprowadź ciąg "wyszukiwanie Bing" jako filtr. Z listy Wyzwalacze wybierz wyzwalacz, który chcesz.

   W tym przykładzie używa tego wyzwalacza: **wyszukiwanie Bing w nowym artykule z wiadomościami**

   ![Znajdź wyzwalacz Wyszukiwanie Bing](./media/connectors-create-api-bing-search/add-trigger.png)

3. Jeśli zostanie wyświetlony monit o podanie szczegółów połączenia, [Utwórz teraz połączenie wyszukiwanie Bing](#create-connection).
Lub, jeśli połączenie już istnieje, podaj niezbędne informacje dla wyzwalacza.

   Na potrzeby tego przykładu Podaj kryteria zwracania pasujących artykułów z Wyszukiwanie Bing.

   | Właściwość | Wymagany | Wartość | Opis |
   |----------|----------|-------|-------------|
   | Zapytanie wyszukiwania | Tak | <*wyszukiwanie — słowa*> | Wprowadź słowa kluczowe wyszukiwania, które mają być używane. |
   | Do | Tak | <*ustawienie*> | Ustawienia regionalne wyszukiwania. Wartością domyślną jest "en-US", ale można wybrać inną wartość. |
   | Bezpieczne wyszukiwanie | Tak | <*poziom wyszukiwania*> | Poziom filtru dla wykluczenia treści dla dorosłych. Wartość domyślna to "umiarkowane", ale wybierasz inny poziom. |
   | Liczba | Nie | <*wyniki — liczba*> | Zwróć określoną liczbę wyników. Wartością domyślną jest 20, ale można określić inną wartość. Rzeczywista liczba zwracanych wyników może być mniejsza niż określona liczba. |
   | Przesunięcie | Nie | <*Pomiń wartość*> | Liczba wyników do pominięcia przed zwróceniem wyników |
   |||||

   Na przykład:

   ![Konfigurowanie wyzwalacza](./media/connectors-create-api-bing-search/bing-search-trigger.png)

4. Wybierz interwał i częstotliwość sprawdzania wyników przez wyzwalacz.

5. Gdy skończysz, na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

6. Teraz Kontynuuj dodawanie co najmniej jednej akcji do aplikacji logiki w celu wykonywania zadań, które chcesz wykonać z wynikami wyzwalacza.

<a name="add-action"></a>

## <a name="add-a-bing-search-action"></a>Dodaj akcję Wyszukiwanie Bing

W Azure Logic Apps [Akcja](../logic-apps/logic-apps-overview.md#logic-app-concepts) to krok w przepływie pracy, który następuje po wyzwalaczu lub innej akcji. W tym przykładzie aplikacja logiki rozpoczyna się od wyzwalacza Wyszukiwanie Bing, który zwraca artykuły z wiadomościami zgodne z określonymi kryteriami.

1. W Azure Portal lub Visual Studio Otwórz aplikację logiki w Projektancie aplikacji logiki. Ten przykład używa Azure Portal.

2. W obszarze wyzwalacza lub akcji wybierz pozycję **nowy krok**  >  **Dodaj akcję**.

   W tym przykładzie używa tego wyzwalacza:

   **Wyszukiwanie Bing — nowy artykuł z wiadomościami**

   ![Dodawanie akcji](./media/connectors-create-api-bing-search/add-action.png)

   Aby dodać akcję między istniejącymi krokami, przesuń wskaźnik myszy na strzałkę łączącą. 
   Wybierz wyświetlony znak plus ( **+** ), a następnie wybierz pozycję **Dodaj akcję**.

3. W polu wyszukiwania wprowadź ciąg "wyszukiwanie Bing" jako filtr.
Z listy Akcje wybierz żądaną akcję.

   W tym przykładzie używa tej akcji:

   **Wyszukiwanie Bing listy dyskusyjne według zapytania**

   ![Znajdź akcję Wyszukiwanie Bing](./media/connectors-create-api-bing-search/bing-search-select-action.png)

4. Jeśli zostanie wyświetlony monit o podanie szczegółów połączenia, [Utwórz teraz połączenie wyszukiwanie Bing](#create-connection). Lub, jeśli połączenie już istnieje, podaj niezbędne informacje dla tej akcji.

   Na potrzeby tego przykładu Podaj kryteria zwracające podzestaw wyników wyzwalacza.

   | Właściwość | Wymagany | Wartość | Opis |
   |----------|----------|-------|-------------|
   | Zapytanie wyszukiwania | Tak | <*Wyrażenie wyszukiwania*> | Wprowadź wyrażenie służące do wykonywania zapytań dotyczących wyników wyzwalacza. Możesz wybrać spośród pól na liście zawartości dynamicznej lub utworzyć wyrażenie przy użyciu Konstruktora wyrażeń. |
   | Do | Tak | <*ustawienie*> | Ustawienia regionalne wyszukiwania. Wartością domyślną jest "en-US", ale można wybrać inną wartość. |
   | Bezpieczne wyszukiwanie | Tak | <*poziom wyszukiwania*> | Poziom filtru dla wykluczenia treści dla dorosłych. Wartość domyślna to "umiarkowane", ale wybierasz inny poziom. |
   | Liczba | Nie | <*wyniki — liczba*> | Zwróć określoną liczbę wyników. Wartością domyślną jest 20, ale można określić inną wartość. Rzeczywista liczba zwracanych wyników może być mniejsza niż określona liczba. |
   | Przesunięcie | Nie | <*Pomiń wartość*> | Liczba wyników do pominięcia przed zwróceniem wyników |
   |||||

   Załóżmy na przykład, że chcesz uzyskać te wyniki, których nazwa kategorii zawiera wyraz "Tech".

   1. Kliknij pole **zapytania wyszukiwania** , aby wyświetlić listę zawartości dynamicznej. 
   Z tej listy wybierz pozycję **wyrażenie** , aby pojawił się Konstruktor wyrażeń. 

      ![Wyzwalacz Wyszukiwanie Bing](./media/connectors-create-api-bing-search/bing-search-action.png)

      Teraz możesz zacząć tworzyć wyrażenie.

   2. Z listy funkcje wybierz funkcję **Contains ()** , która zostanie wyświetlona w polu wyrażenie. Kliknij pozycję **zawartość dynamiczna** , aby wyświetlić listę pól ponownie, ale upewnij się, że kursor pozostaje wewnątrz nawiasów.

      ![Wybieranie funkcji](./media/connectors-create-api-bing-search/expression-select-function.png)

   3. Z listy pól wybierz **kategorię**, która konwertuje do parametru. 
   Dodaj przecinek po pierwszym parametrze, a po przecinku Dodaj następujące słowo: `'tech'` 

      ![Wybierz pole](./media/connectors-create-api-bing-search/expression-select-field.png)

   4. Gdy skończysz, wybierz opcję **OK**.

      Wyrażenie jest teraz wyświetlane w polu **zapytania wyszukiwania** w tym formacie:

      ![Gotowe wyrażenie](./media/connectors-create-api-bing-search/resolved-expression.png)

      W widoku kodu to wyrażenie jest wyświetlane w tym formacie:

      `"@{contains(triggerBody()?['category'],'tech')}"`

5. Gdy skończysz, na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

<a name="create-connection"></a>

## <a name="connect-to-bing-search"></a>Połącz z Wyszukiwanie Bing

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Po wyświetleniu monitu o podanie informacji o połączeniu podaj następujące informacje:

   | Właściwość | Wymagany | Wartość | Opis |
   |----------|----------|-------|-------------|
   | Nazwa połączenia | Tak | <*Nazwa połączenia*> | Nazwa do utworzenia dla połączenia |
   | Wersja interfejsu API | Tak | <*Wersja interfejsu API*> | Domyślnie wersja interfejsu API Wyszukiwanie Bing jest ustawiona na bieżącą wersję. W razie potrzeby możesz wybrać wcześniejszą wersję. |
   | Klucz interfejsu API | Tak | <*Klucz interfejsu API*> | Wcześniej pobrano klucz interfejsu API Wyszukiwanie Bing. Jeśli nie masz klucza, Pobierz [klucz interfejsu API teraz](https://azure.microsoft.com/try/cognitive-services/?api=bing-news-search-api). |  
   |||||  

   Na przykład:

   ![Tworzenie połączenia](./media/connectors-create-api-bing-search/bing-search-create-connection.png)

2. Gdy wszystko będzie gotowe, wybierz przycisk **Utwórz**.

## <a name="connector-reference"></a>Dokumentacja łączników

Aby uzyskać szczegółowe informacje techniczne, takie jak wyzwalacze, akcje i limity, zgodnie z opisem w pliku Swagger łącznika, zobacz [stronę odwołania łącznika](/connectors/bingsearch/).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat innych [łączników Logic Apps](../connectors/apis-list.md)

