---
title: Planowanie zadań do obsługi danych ciągłych
description: Twórz i uruchamiaj zadania cykliczne obsługujące ciągłe dane przy użyciu okien przestawnych w Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: deli, logicappspm
ms.topic: conceptual
ms.date: 03/25/2020
ms.openlocfilehash: 103805fbf395dc120acc96fbcee273abcf14939d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96010422"
---
# <a name="schedule-and-run-tasks-for-contiguous-data-by-using-the-sliding-window-trigger-in-azure-logic-apps"></a>Zaplanuj i Uruchom zadania dla ciągłych danych przy użyciu wyzwalacza okna przewijania w Azure Logic Apps

Aby regularnie uruchamiać zadania, procesy lub zadania, które muszą obsługiwać dane w ciągłych fragmentach, można uruchomić przepływ pracy aplikacji logiki przy użyciu wyzwalacza **okna przewijania** . Można ustawić datę i godzinę oraz strefę czasową do uruchomienia przepływu pracy i cykl dla powtarzania tego przepływu pracy. Jeśli cykle zostały pominięte z jakiegokolwiek powodu, na przykład z powodu zakłóceń lub wyłączonych przepływów pracy, ten wyzwalacz przetwarza pominięte cykle. Na przykład podczas synchronizowania danych między bazą danych i magazynem kopii zapasowych Użyj wyzwalacza okna przesuwanego, aby dane były synchronizowane bez ponoszenia przerw. Aby uzyskać więcej informacji o wbudowanych wyzwalaczach i akcjach harmonogramu, zobacz temat [Planowanie i uruchamianie cyklicznych zautomatyzowanych, zadań i przepływów pracy przy użyciu Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

Poniżej przedstawiono niektóre wzorce obsługiwane przez ten wyzwalacz:

* Uruchom natychmiast i powtórz co *n* liczba sekund, minuty, godziny, dni, tygodnie lub miesiące.

* Zacznij od określonej daty i godziny, a następnie uruchamiaj i Powtarzaj co *n* liczba sekund, minuty, godziny, dni, tygodnie lub miesiące. Za pomocą tego wyzwalacza można określić godzinę rozpoczęcia w przeszłości, która będzie uruchamiać wszystkie poprzednie cykle.

* Opóźnij każdy cykl przez określony czas przed uruchomieniem.

Aby uzyskać różnice między tym wyzwalaczem i wyzwalaczem cyklicznym lub aby uzyskać więcej informacji na temat planowania cyklicznych przepływów pracy, zobacz temat [Planowanie i uruchamianie cyklicznych zautomatyzowanych zadań, procesów i przepływów pracy przy użyciu Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

> [!TIP]
> Jeśli chcesz wyzwolić aplikację logiki i uruchamiać ją tylko raz w przyszłości, zobacz [Uruchamianie zadań tylko jeden raz](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#run-once).

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, możesz [zarejestrować się w celu uzyskania bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* Podstawowa wiedza na temat [aplikacji logiki](../logic-apps/logic-apps-overview.md). Jeśli dopiero zaczynasz tworzyć aplikacje logiki, Dowiedz się, [jak utworzyć pierwszą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-sliding-window-trigger"></a>Dodaj wyzwalacz przedziału ruchomego

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Tworzenia pustej aplikacji logiki.

1. Gdy zostanie wyświetlony projektant aplikacji logiki, w polu wyszukiwania wprowadź `sliding window` jako filtr. Z listy wyzwalania Wybierz wyzwalacz **okna przesuwanego** jako pierwszy krok w przepływie pracy aplikacji logiki.

   ![Wybierz wyzwalacz "okno przewijania"](./media/connectors-native-sliding-window/add-sliding-window-trigger.png)

1. Ustaw odstęp czasu i częstotliwość cyklu. W tym przykładzie ustaw te właściwości, aby uruchamiać przepływy pracy co tydzień.

   ![Ustawianie interwału i częstotliwości](./media/connectors-native-sliding-window/sliding-window-trigger-details.png)

   | Właściwość | Nazwa JSON | Wymagany | Typ | Opis |
   |----------|----------|-----------|------|-------------|
   | **Interwał** | `interval` | Tak | Liczba całkowita | Dodatnia liczba całkowita, która opisuje, jak często przebiega przepływ pracy na podstawie częstotliwości. Poniżej znajdują się minimalne i maksymalne interwały: <p>-Miesiąc: 1-16 miesięcy <br>-Tydzień: 1-71 tyg. <br>-Dzień: 1-500 dni <br>-Godz.: 1 – 12 godzin <br>-Minutę: 1 – 72000 minut <br>-Sekunda: 1 – 9999999 s <p>Jeśli na przykład interwał wynosi 6, a częstotliwość to "miesiąc", cykl jest co 6 miesięcy. |
   | **Częstotliwość** | `frequency` | Tak | Ciąg | Jednostka czasu dla cyklu: **sekunda**, **minuta**, **godzina**, **dzień**, **tydzień** lub **miesiąc** |
   ||||||

   ![Zaawansowane opcje cyklu](./media/connectors-native-sliding-window/sliding-window-trigger-more-options-details.png)

   Aby uzyskać więcej opcji cyklu, Otwórz listę **Dodaj nowy parametr** . Wszystkie wybrane opcje są wyświetlane w wyzwalaczu po zaznaczeniu.

   | Właściwość | Wymagany | Nazwa JSON | Typ | Opis |
   |----------|----------|-----------|------|-------------|
   | **Opóźnienie** | Nie | opóźnienie | Ciąg | Czas trwania każdego cyklu z zastosowaniem [specyfikacji daty i godziny ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Durations) |
   | **Strefa czasowa** | Nie | timeZone | Ciąg | Ma zastosowanie tylko w przypadku określenia czasu rozpoczęcia, ponieważ ten wyzwalacz nie akceptuje [przesunięcia czasu UTC](https://en.wikipedia.org/wiki/UTC_offset). Wybierz strefę czasową, która ma zostać zastosowana. |
   | **Godzina rozpoczęcia** | Nie | startTime | Ciąg | Podaj datę i godzinę rozpoczęcia w tym formacie: <p>RRRR-MM-DDTgg: mm: SS w przypadku wybrania strefy czasowej <p>-lub- <p>RRRR-MM-DDTgg: mm: SSS, jeśli nie wybierzesz strefy czasowej <p>Na przykład jeśli chcesz, aby 18 września 2017 o 2:00 PM, określ wartość "2017-09-18T14:00:00" i wybierz strefę czasową, na przykład Pacyfik (czas standardowy). Lub określ wartość "2017-09-18T14:00:00Z" bez strefy czasowej. <p>**Uwaga:** Ta godzina rozpoczęcia musi być zgodna ze [specyfikacją daty i godziny ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) w [formacie daty i godziny czasu UTC](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), ale bez [przesunięcia czasu UTC](https://en.wikipedia.org/wiki/UTC_offset). Jeśli nie wybierzesz strefy czasowej, musisz dodać literę "Z" na końcu bez spacji. Ten "Z" odnosi się do odpowiadającego [czasu morskich](https://en.wikipedia.org/wiki/Nautical_time). <p>W przypadku prostych harmonogramów czas rozpoczęcia jest pierwszym wystąpieniem, podczas gdy dla zaawansowanych cykli wyzwalacz nie jest uruchamiany wcześniej niż godzina rozpoczęcia. [*Jakie są sposoby używania daty i godziny rozpoczęcia?*](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time) |
   |||||

1. Teraz Skompiluj pozostałe przepływy pracy z innymi akcjami. Aby uzyskać więcej akcji, które można dodać, zobacz [Łączniki dla Azure Logic Apps](../connectors/apis-list.md).

## <a name="workflow-definition---sliding-window"></a>Definicja przepływu pracy — ruchome okno

W podstawowej definicji przepływu pracy aplikacji logiki, która używa formatu JSON, można wyświetlić definicję wyzwalacza okna przewijania z wybranymi opcjami. Aby wyświetlić tę definicję, na pasku narzędzi projektanta wybierz **Widok kod**. Aby powrócić do projektanta, wybierz na pasku narzędzi projektanta pozycję **Projektant**.

Ten przykład pokazuje, jak definicja wyzwalacza okna przewijania może wyglądać w źródłowej definicji przepływu pracy, gdzie opóźnienie dla każdego cyklu wynosi pięć sekund w przypadku cyklu godzinowego:

``` json
"triggers": {
   "Recurrence": {
      "type": "SlidingWindow",
      "Sliding_Window": {
         "inputs": {
            "delay": "PT5S"
         },
         "recurrence": {
            "frequency": "Hour",
            "interval": 1,
            "startTime": "2019-05-13T14:00:00Z",
            "timeZone": "Pacific Standard Time"
         }
      }
   }
}
```

## <a name="next-steps"></a>Następne kroki

* [Opóźnij następną akcję w przepływach pracy](../connectors/connectors-native-delay.md)
* [Łączniki dla usługi Logic Apps](../connectors/apis-list.md)
