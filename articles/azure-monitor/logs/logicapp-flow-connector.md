---
title: Korzystanie z dzienników Azure Monitor z użyciem Azure Logic Apps i automatyzacji
description: Dowiedz się, jak można użyć Azure Logic Apps i automatyzacji, aby szybko zautomatyzować powtarzalne procesy przy użyciu łącznika Azure Monitor.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/13/2020
ms.openlocfilehash: 9d93e3ec08ff16a1fcca0c914b9e817d4ca89d60
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101707952"
---
# <a name="azure-monitor-logs-connector-for-logic-apps-and-power-automate"></a>Łącznik Azure Monitor Logs dla Logic Apps i automatyzacji
[Azure Logic Apps](../../logic-apps/index.yml) i [Automatyzacja](https://flow.microsoft.com) — umożliwia tworzenie zautomatyzowanych przepływów pracy przy użyciu setek działań dla różnych usług. Łącznik Azure Monitor Logs umożliwia tworzenie przepływów pracy, które pobierają dane z obszaru roboczego Log Analytics lub aplikacji Application Insights w Azure Monitor. W tym artykule opisano akcje dołączone do łącznika i przedstawiono Przewodnik tworzenia przepływu pracy przy użyciu tych danych.

Na przykład możesz utworzyć aplikację logiki, która będzie używać danych dziennika Azure Monitor w powiadomieniach e-mail z pakietu Office 365, utworzyć usterkę w usłudze Azure DevOps lub opublikować komunikat o zapasach.  Przepływ pracy można wyzwolić według prostego harmonogramu lub z niektórych akcji w połączonej usłudze, takiej jak w przypadku otrzymania wiadomości e-mail lub tweetu. 

## <a name="connector-limits"></a>Limity łączników
Łącznik dzienników Azure Monitor ma następujące limity:
* Maksymalny rozmiar danych: 16 MB
* Maksymalny rozmiar odpowiedzi kwerendy 100 MB
* Maksymalna liczba rekordów: 500 000
* Maksymalny limit czasu zapytania 110 sekundę.
* Wizualizacje wykresów mogą być dostępne na stronie dzienników i brakuje ich w łączniku, ponieważ strona łącznik i dzienniki nie używają obecnie tych samych bibliotek wykresów.

W zależności od rozmiaru danych i użytego zapytania łącznik może trafić w jego limity i zakończyć się niepowodzeniem. Takie przypadki można obejść podczas dopasowywania cyklu wyzwalacza do uruchamiania częściej i wykonywania zapytań o mniej danych. Możesz użyć zapytań, które agregują dane, aby zwracać mniejsze rekordy i kolumny.

## <a name="actions"></a>Akcje
W poniższej tabeli opisano akcje dołączone do łącznika dzienników Azure Monitor. Oba umożliwiają uruchamianie zapytania dziennika względem obszaru roboczego Log Analytics lub Application Insights aplikacji. Różnica polega na tym, jak dane są zwracane.

> [!NOTE]
> Łącznik Azure Monitor Logs zastępuje [Łącznik usługi azure log Analytics](/connectors/azureloganalytics/) i [łącznik usługi Azure Application Insights](/connectors/applicationinsights/). Ten łącznik zapewnia te same funkcje co inne i jest preferowaną metodą uruchamiania zapytania w obszarze roboczym Log Analytics lub aplikacji Application Insights.


| Akcja | Opis |
|:---|:---|
| [Uruchom zapytanie i Wyświetl wyniki](/connectors/azuremonitorlogs/#run-query-and-list-results) | Zwraca każdy wiersz jako własny obiekt. Użyj tej akcji, gdy chcesz współpracować z każdym wierszem oddzielnie w pozostałej części przepływu pracy. Akcja zazwyczaj następuje [dla każdego działania](../../logic-apps/logic-apps-control-flow-loops.md#foreach-loop). |
| [Uruchamianie zapytań i wizualizacji wyników](/connectors/azuremonitorlogs/#run-query-and-visualize-results) | Zwraca wszystkie wiersze w zestawie wyników jako jeden sformatowany obiekt. Użyj tej akcji, jeśli chcesz użyć zestawu wyników razem w pozostałej części przepływu pracy, na przykład wysyłając wyniki w wiadomości e-mail.  |

## <a name="walkthroughs"></a>Wskazówki
Poniższe samouczki ilustrują użycie łączników Azure Monitor w Azure Logic Apps. Ten sam przykład można wykonać przy użyciu automatyzacji, ale jedyną różnicą, jak utworzyć początkowy przepływ pracy i uruchomić go po zakończeniu. Konfiguracja przepływu pracy i akcji jest taka sama między nimi. Aby rozpocząć, zobacz [Tworzenie przepływu na podstawie szablonu w programie do automatyzacji](/power-automate/get-started-logic-template) .


### <a name="create-a-logic-app"></a>Tworzenie aplikacji logiki

Przejdź do **Logic Apps** w Azure Portal i kliknij przycisk **Dodaj**. Wybierz **subskrypcję**, **grupę zasobów** i **region** , aby zapisać nową aplikację logiki, a następnie nadaj jej unikatową nazwę. Możesz włączyć ustawienie **log Analytics** , aby zbierać informacje o danych i zdarzeniach środowiska uruchomieniowego zgodnie z opisem w temacie [Konfigurowanie Azure monitor dzienników i zbierać dane diagnostyczne dla Azure Logic Apps](../../logic-apps/monitor-logic-apps-log-analytics.md). To ustawienie nie jest wymagane w przypadku korzystania z łącznika dzienników Azure Monitor.

![Tworzenie aplikacji logiki](media/logicapp-flow-connector/create-logic-app.png)


Kliknij przycisk **Przegląd + Utwórz** , a następnie **Utwórz**. Po zakończeniu wdrażania kliknij pozycję **Przejdź do zasobu** , aby otworzyć **projektanta Logic Apps**.

### <a name="create-a-trigger-for-the-logic-app"></a>Tworzenie wyzwalacza dla aplikacji logiki
W obszarze **Rozpocznij od typowego wyzwalacza** wybierz pozycję **cykl**. Spowoduje to utworzenie aplikacji logiki, która jest automatycznie uruchamiana w regularnych odstępach czasu. W polu **częstotliwość** akcji wybierz pozycję **dzień** , a następnie w polu **Interwał** wprowadź wartość **1** , aby uruchomić przepływ pracy raz dziennie.

![Akcja cyklu](media/logicapp-flow-connector/recurrence-action.png)

## <a name="walkthrough-mail-visualized-results"></a>Przewodnik: wyniki wizualizacji wiadomości
W poniższym samouczku pokazano, jak utworzyć aplikację logiki, która wysyła wyniki zapytania dziennika Azure Monitor za pośrednictwem poczty e-mail. 

### <a name="add-azure-monitor-logs-action"></a>Dodaj akcję dzienników Azure Monitor
Kliknij pozycję **+ nowy krok** , aby dodać akcję, która jest uruchamiana po akcji cyklu. W obszarze **Wybierz akcję** wpisz **Azure monitor** , a następnie wybierz pozycję **dzienniki Azure monitor**.

![Akcja dzienników Azure Monitor](media/logicapp-flow-connector/select-azure-monitor-connector.png)

Kliknij pozycję **Azure log Analytics — uruchom zapytanie i Wizualizuj wyniki**.

![Zrzut ekranu przedstawiający nową akcję dodawaną do kroku w Projektancie aplikacji logiki. Dzienniki Azure Monitor są wyróżnione w obszarze Wybierz akcję.](media/logicapp-flow-connector/select-query-action-visualize.png)


### <a name="add-azure-monitor-logs-action"></a>Dodaj akcję dzienników Azure Monitor

Wybierz **subskrypcję** i **grupę zasobów** dla obszaru roboczego log Analytics. Wybierz *log Analytics obszar roboczy* dla **typu zasobu** , a następnie wybierz nazwę obszaru roboczego w obszarze **nazwa zasobu**.

Dodaj następujące zapytanie dziennika do okna **zapytania** .  

```Kusto
Event
| where EventLevelName == "Error" 
| where TimeGenerated > ago(1day)
| summarize TotalErrors=count() by Computer
| sort by Computer asc   
```

Wybierz pozycję *Ustaw w polu zapytanie* dla **zakresu czasu** i **tabeli HTML** dla **typu wykresu**.
   
![Zrzut ekranu przedstawiający ustawienia nowej akcji dzienników Azure Monitor o nazwie Run Query i Wizualizuj wyniki.](media/logicapp-flow-connector/run-query-visualize-action.png)

Wiadomość e-mail zostanie wysłana przez konto skojarzone z bieżącym połączeniem. Możesz określić inne konto, klikając pozycję **Zmień połączenie**.

### <a name="add-email-action"></a>Dodaj akcję poczty e-mail

Kliknij pozycję **+ nowy krok**, a następnie kliknij pozycję **+ Dodaj akcję**. W obszarze **Wybierz akcję** wpisz **Outlook** , a następnie wybierz pozycję **Office 365 Outlook**.

![Wybieranie łącznika programu Outlook](media/logicapp-flow-connector/select-outlook-connector.png)

Wybierz pozycję **Wyślij wiadomość e-mail (wersja 2)**.

![Okno wyboru pakietu Office 365 w programie Outlook](media/logicapp-flow-connector/select-mail-action.png)

Kliknij w dowolnym miejscu w polu **treść** , aby otworzyć okno **zawartości dynamicznej** z wartościami z poprzednich akcji w aplikacji logiki. Wybierz pozycję **Zobacz więcej** , a następnie **treść** , która jest wynikiem zapytania w akcji log Analytics.

![Wybierz treść](media/logicapp-flow-connector/select-body.png)

Określ adres e-mail adresata w oknie **do** i temat wiadomości E-mail w **temacie**. 

![Akcja poczty](media/logicapp-flow-connector/mail-action.png)


### <a name="save-and-test-your-logic-app"></a>Zapisz i przetestuj aplikację logiki
Kliknij przycisk **Zapisz** , a następnie **Uruchom** , aby wykonać przebieg testowy aplikacji logiki.

![Zapisz i uruchom](media/logicapp-flow-connector/save-run.png)


Po zakończeniu działania aplikacji logiki Sprawdź pocztę określonego adresata.  Powinna zostać odebrana wiadomość e-mail z treścią podobną do następującej:

![Przykładowa wiadomość e-mail](media/logicapp-flow-connector/sample-mail.png)



## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej [na temat zapytań dzienników w Azure monitor](./log-query-overview.md).
- Dowiedz się więcej o [Logic Apps](../../logic-apps/index.yml)
- Dowiedz się więcej o [automatyzacji](https://flow.microsoft.com).