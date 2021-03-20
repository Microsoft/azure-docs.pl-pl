---
title: Planowanie cyklicznych zadań i przepływów pracy
description: Zaplanuj i uruchom cykliczne zadania automatyczne i przepływy pracy z wyzwalaczem cyklu w Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: conceptual
ms.date: 12/18/2020
ms.openlocfilehash: 3749a7080bf17c020b48ae3ebc3cff3aa998eeef
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100382297"
---
# <a name="create-schedule-and-run-recurring-tasks-and-workflows-with-the-recurrence-trigger-in-azure-logic-apps"></a>Twórz, Zaplanuj i uruchamiaj zadania cykliczne i przepływy pracy z wyzwalaczem cyklu w Azure Logic Apps

Aby regularnie uruchamiać zadania, procesy lub zadania według określonego harmonogramu, można uruchomić przepływ pracy aplikacji logiki z wbudowanym wyzwalaczem **cyklu** , który działa natywnie w Azure Logic Apps. Można ustawić datę i godzinę oraz strefę czasową do uruchomienia przepływu pracy i cykl dla powtarzania tego przepływu pracy. Jeśli wyzwalacz trafi z jakiegokolwiek powodu, na przykład ze względu na zakłócenia lub wyłączone przepływy pracy, ten wyzwalacz nie przetwarza pominiętych cykli, ale uruchamia ponownie cykle w następnym zaplanowanym interwale. Aby uzyskać więcej informacji o wbudowanych wyzwalaczach i akcjach harmonogramu, zobacz temat [Planowanie i uruchamianie cyklicznych zautomatyzowanych, zadań i przepływów pracy przy użyciu Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

Poniżej przedstawiono niektóre wzorce obsługiwane przez ten wyzwalacz oraz bardziej zaawansowane cykle i skomplikowane harmonogramy:

* Uruchom natychmiast i powtórz co *n* liczba sekund, minuty, godziny, dni, tygodnie lub miesiące.

* Zacznij od określonej daty i godziny, a następnie uruchamiaj i Powtarzaj co *n* liczba sekund, minuty, godziny, dni, tygodnie lub miesiące.

* Uruchamiaj i Powtarzaj co najmniej jeden raz dziennie, na przykład o 8:00 AM i 5:00 PM.

* Uruchamiaj i Powtarzaj co tydzień, ale tylko dla określonych dni, takich jak sobota i niedziela.

* Uruchamiaj i Powtarzaj co tydzień, ale tylko dla określonych dni i godzin, takich jak od poniedziałku do piątku o godzinie 8:00 i 5:00 PM.

W przypadku różnic między tym wyzwalaczem a oknem przesuwania lub aby uzyskać więcej informacji na temat planowania cyklicznych przepływów pracy, zobacz [Planowanie i uruchamianie cyklicznych zautomatyzowanych zadań, procesów i przepływów pracy przy użyciu Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

> [!TIP]
> Jeśli chcesz wyzwolić aplikację logiki i uruchamiać ją tylko raz w przyszłości, zobacz [Uruchamianie zadań tylko jeden raz](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#run-once).

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* Podstawowa wiedza na temat [aplikacji logiki](../logic-apps/logic-apps-overview.md). Jeśli dopiero zaczynasz tworzyć aplikacje logiki, Dowiedz się, [jak utworzyć pierwszą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-the-recurrence-trigger"></a>Dodaj wyzwalacz cykliczny

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Tworzenia pustej aplikacji logiki.

1. Gdy zostanie wyświetlony projektant aplikacji logiki, w polu wyszukiwania wprowadź `recurrence` jako filtr. Z listy Wyzwalacze wybierz ten wyzwalacz jako pierwszy krok w przepływie pracy aplikacji logiki: **cykl**

   ![Wybierz wyzwalacz "cykl"](./media/connectors-native-recurrence/add-recurrence-trigger.png)

1. Ustaw odstęp czasu i częstotliwość cyklu. W tym przykładzie ustaw te właściwości, aby uruchamiać przepływy pracy co tydzień.

   ![Ustawianie interwału i częstotliwości](./media/connectors-native-recurrence/recurrence-trigger-details.png)

   | Właściwość | Nazwa JSON | Wymagany | Typ | Opis |
   |----------|-----------|----------|------|-------------|
   | **Interwał** | `interval` | Tak | Liczba całkowita | Dodatnia liczba całkowita, która opisuje, jak często przebiega przepływ pracy na podstawie częstotliwości. Poniżej znajdują się minimalne i maksymalne interwały: <p>-Miesiąc: 1-16 miesięcy <br>-Tydzień: 1-71 tyg. <br>-Dzień: 1-500 dni <br>-Godz.: 1 – 12 godzin <br>-Minutę: 1 – 72000 minut <br>-Sekunda: 1 – 9999999 s<p>Jeśli na przykład interwał wynosi 6, a częstotliwość to "miesiąc", cykl jest co 6 miesięcy. |
   | **Częstotliwość** | `frequency` | Tak | Ciąg | Jednostka czasu dla cyklu: **sekunda**, **minuta**, **godzina**, **dzień**, **tydzień** lub **miesiąc** |
   ||||||

   > [!IMPORTANT]
   > Jeśli cykl nie określi konkretnej [daty i godziny rozpoczęcia](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time), Pierwszy cykl jest uruchamiany natychmiast po zapisaniu lub wdrożeniu aplikacji logiki, pomimo konfiguracji cyklu wyzwalacza. Aby uniknąć tego zachowania, podaj datę i godzinę rozpoczęcia pierwszego cyklu do uruchomienia.
   >
   > Jeśli cykl nie określi żadnych innych zaawansowanych opcji planowania, takich jak określone czasy uruchamiania przyszłych cykli, te cykle są oparte na czasie ostatniego uruchomienia. W związku z tym czasy rozpoczęcia tych cyklów mogą być naliczone ze względu na takie czynniki jak opóźnienie podczas wywołań magazynu. 
   > Aby upewnić się, że aplikacja logiki nie trafił cyklu, szczególnie wtedy, gdy częstotliwość jest w dniach lub dłużej, wypróbuj następujące opcje:
   > 
   > * Podaj datę i godzinę rozpoczęcia cyklu oraz określone czasy wykonywania kolejnych cykli przy użyciu właściwości o nazwach **w tych godzinach** i **w tych minutach**, które są dostępne tylko dla częstotliwości **dzień** i **tydzień** .
   > 
   > * Użyj [wyzwalacza okna przesuwanego](../connectors/connectors-native-sliding-window.md), a nie wyzwalacza cyklicznego.

1. Aby ustawić zaawansowane opcje planowania, Otwórz listę **Dodaj nowy parametr** . Wszystkie wybrane opcje są wyświetlane w wyzwalaczu po zaznaczeniu.

   ![Zaawansowane opcje planowania](./media/connectors-native-recurrence/recurrence-trigger-more-options-details.png)

   | Właściwość | Nazwa JSON | Wymagany | Typ | Opis |
   |----------|-----------|----------|------|-------------|
   | **Strefa czasowa** | `timeZone` | Nie | Ciąg | Ma zastosowanie tylko w przypadku określenia czasu rozpoczęcia, ponieważ ten wyzwalacz nie akceptuje [przesunięcia czasu UTC](https://en.wikipedia.org/wiki/UTC_offset). Wybierz strefę czasową, która ma zostać zastosowana. |
   | **Godzina rozpoczęcia** | `startTime` | Nie | Ciąg | Podaj datę i godzinę rozpoczęcia, która ma maksymalnie 49 lat w przyszłości i musi być zgodna ze [specyfikacją daty i godziny](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) w [formacie](https://en.wikipedia.org/wiki/Coordinated_Universal_Time)ISO 8601, ale bez [przesunięcia czasu UTC](https://en.wikipedia.org/wiki/UTC_offset): <p><p>RRRR-MM-DDTgg: mm: SS w przypadku wybrania strefy czasowej <p>-lub- <p>RRRR-MM-DDTgg: mm: SSS, jeśli nie wybierzesz strefy czasowej <p>Na przykład jeśli chcesz, aby 18 września 2020 o 2:00 PM, określ wartość "2020-09-18T14:00:00" i wybierz strefę czasową, na przykład Pacyfik (czas standardowy). Lub określ wartość "2020-09-18T14:00:00Z" bez strefy czasowej. <p><p>**Ważne:** Jeśli nie wybierzesz strefy czasowej, musisz dodać literę "Z" na końcu bez spacji. Ten "Z" odnosi się do odpowiadającego [czasu morskich](https://en.wikipedia.org/wiki/Nautical_time). W przypadku wybrania wartości strefy czasowej nie trzeba dodawać "Z" do końca wartości **czasu rozpoczęcia** . W takim przypadku Logic Apps ignoruje wartość strefy czasowej, ponieważ "Z" oznacza format czasu UTC. <p><p>W przypadku prostych harmonogramów czas rozpoczęcia jest pierwszym wystąpieniem, a w przypadku harmonogramów złożonych wyzwalacz nie jest uruchamiany dłużej niż godzina rozpoczęcia. [*Jakie są sposoby używania daty i godziny rozpoczęcia?*](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time) |
   | **W tych dniach** | `weekDays` | Nie | Ciąg lub tablica ciągów | W przypadku wybrania opcji "tydzień" można wybrać co najmniej jeden dzień, w którym chcesz uruchomić przepływ pracy: **poniedziałek**, **wtorek**, **środa**, **czwartek**, **piątek**, **Sobota** i **Niedziela** |
   | **W tych godzinach** | `hours` | Nie | Tablica liczb całkowitych lub liczb całkowitych | W przypadku wybrania opcji "dzień" lub "tydzień" można wybrać co najmniej jedną liczbę całkowitą z przedziału od 0 do 23 jako godzinę, o której ma być uruchamiany przepływ pracy. <p><p>Na przykład, jeśli określisz wartość "10", "12" i "14", otrzymujesz 10 AM, 12 PM i 2 PM w godzinach dnia, ale minuty dnia są obliczane na podstawie momentu rozpoczęcia cyklu. Aby ustawić określone minuty dnia, na przykład 10:00 AM, 12:00 PM i 2:00 PM, określ te wartości przy użyciu właściwości o nazwie **w tych minutach**. |
   | **W tych minutach** | `minutes` | Nie | Tablica liczb całkowitych lub liczb całkowitych | W przypadku wybrania opcji "dzień" lub "tydzień" można wybrać co najmniej jedną liczbę całkowitą z zakresu od 0 do 59 jako minuty godziny, Kiedy chcesz uruchomić przepływ pracy. <p>Na przykład można określić wartość "30" jako znak minuty i użyć poprzedniego przykładu dla godzin dnia, otrzymują 10:30 AM, 12:30 PM i 2:30 PM. <p>**Uwaga**: czasami sygnatura czasowa uruchomienia wyzwalanego może się różnić do 1 minuty od zaplanowanego czasu. Jeśli konieczne jest przekazanie sygnatury czasowej dokładnie zgodnie z harmonogramem do kolejnych akcji, można użyć wyrażeń szablonu, aby odpowiednio zmienić sygnaturę czasową. Aby uzyskać więcej informacji, zobacz [funkcje daty i godziny dla wyrażeń](../logic-apps/workflow-definition-language-functions-reference.md#date-time-functions). |
   |||||

   Załóżmy na przykład, że dzisiaj jest piątek, 4 września 2020. Następujący wyzwalacz cykliczny nie jest uruchamiany *wcześniej* niż data i godzina rozpoczęcia, czyli piątek, 18 września 2020 w dniu 8:00 am. Harmonogram cyklu jest jednak ustawiany dla 10:30 AM, 12:30 PM i 2:30 PM tylko w poniedziałek. W związku z tym podczas pierwszego uruchomienia wyzwalacza i utworzenia wystąpienia przepływu pracy aplikacji logiki jest w poniedziałek o godzinie 10:30. Aby dowiedzieć się więcej na temat czasu rozpoczęcia pracy, zobacz te [przykłady czasu rozpoczęcia](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time).

   Przyszłe uruchomienia są wykonywane o godzinie 12:30 PM i 2:30 PM w tym samym dniu. Każdy cykl tworzy własne wystąpienie przepływu pracy. Po wykonaniu tej czynności cały harmonogram zostanie powtórzony w ciągu następnych poniedziałku. [*Jakie są inne przykładowe wystąpienia?*](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#example-recurrences)

   ![Przykład planowania zaawansowanego](./media/connectors-native-recurrence/recurrence-trigger-advanced-schedule-options.png)

   > [!NOTE]
   > Wyzwalacz pokazuje podgląd określonego cyklu tylko w przypadku wybrania częstotliwości "dzień" lub "tydzień".

1. Teraz Skompiluj pozostałe przepływy pracy z innymi akcjami. Aby uzyskać więcej akcji, które można dodać, zobacz [Łączniki dla Azure Logic Apps](../connectors/apis-list.md).

## <a name="workflow-definition---recurrence"></a>Definicja przepływu pracy — cykl

W źródłowej definicji przepływu pracy aplikacji logiki, która używa formatu JSON, można wyświetlić [definicję wyzwalacza cyklu](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger) z wybranymi opcjami. Aby wyświetlić tę definicję, na pasku narzędzi projektanta wybierz **Widok kod**. Aby powrócić do projektanta, wybierz na pasku narzędzi projektanta pozycję **Projektant**.

Ten przykład pokazuje, jak definicja wyzwalacza cyklicznego może wyglądać w źródłowej definicji przepływu pracy:

``` json
"triggers": {
   "Recurrence": {
      "type": "Recurrence",
      "recurrence": {
         "frequency": "Week",
         "interval": 1,
         "schedule": {
            "hours": [
               10,
               12,
               14
            ],
            "minutes": [
               30
            ],
            "weekDays": [
               "Monday"
            ]
         },
         "startTime": "2020-09-07T14:00:00Z",
         "timeZone": "Pacific Standard Time"
      }
   }
}
```

<a name="daylight-saving-standard-time"></a>

## <a name="trigger-recurrence-shift-between-daylight-saving-time-and-standard-time"></a>Wyzwalaj zmianę cyklu między czasem letnim a czasem standardowym

Powtarzające się wyzwalacze są zgodne z ustawionym harmonogramem, w tym każdą określoną strefę czasową. Jeśli nie wybierzesz strefy czasowej, czas letni (DST) może mieć wpływ na to, kiedy wyzwalacze są uruchamiane, na przykład przesunięcie godziny rozpoczęcia o godzinę do przodu, gdy naliczanie i godzinę zakończenia.

Aby uniknąć tej zmiany, aby aplikacja logiki działała o określonej godzinie rozpoczęcia, upewnij się, że wybrano strefę czasową. W ten sposób czas UTC dla aplikacji logiki również zostanie zmieniony w celu wykrycia zmiany czasu sezonowego. Jednak niektóre okna mogą powodować problemy podczas przesuwania czasu. Aby uzyskać więcej informacji i przykładów, zobacz [cykle czasu letniego i czasu standardowego](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#daylight-saving-standard-time).

## <a name="next-steps"></a>Następne kroki

* [Wstrzymywanie przepływów pracy z opóźnionymi akcjami](../connectors/connectors-native-delay.md)
* [Łączniki dla usługi Logic Apps](../connectors/apis-list.md)
