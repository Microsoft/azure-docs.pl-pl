---
title: Wyślij dziennik aktywności platformy Azure do Log Analytics obszaru roboczego przy użyciu Azure Portal
description: Użyj Azure Portal, aby utworzyć obszar roboczy Log Analytics i ustawienia diagnostyczne w celu wysłania dziennika aktywności do dzienników Azure Monitor.
ms.topic: quickstart
author: bwren
ms.author: bwren
ms.date: 06/25/2020
ms.openlocfilehash: fec1f4f3ae13f6c9ed5fdd7ffbcd143e5c5e5f52
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102033252"
---
# <a name="send-azure-activity-log-to-log-analytics-workspace-using-azure-portal"></a>Wyślij dziennik aktywności platformy Azure do Log Analytics obszaru roboczego przy użyciu Azure Portal
Dziennik aktywności jest to dziennik platformy Azure, który zapewnia wgląd w zdarzenia na poziomie subskrypcji. Zawiera on takie informacje jak czas zmodyfikowania zasobu lub czas uruchomienia maszyny wirtualnej. Dziennik aktywności można wyświetlić w Azure Portal lub pobrać wpisów przy użyciu programu PowerShell i interfejsu wiersza polecenia. W tym przewodniku szybki start pokazano, jak za pomocą Azure Portal utworzyć obszar roboczy Log Analytics i ustawienia diagnostyczne w celu wysłania dziennika aktywności do dzienników Azure Monitor, gdzie można je analizować przy użyciu [zapytań dzienników](../logs/log-query-overview.md) i włączyć inne funkcje, takie jak [alerty dzienników](../alerts/alerts-log-query.md) i [skoroszyty](../visualize/workbooks-overview.md). 

## <a name="sign-in-to-azure-portal"></a>Logowanie do witryny Azure Portal
Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com). 



## <a name="create-a-log-analytics-workspace"></a>Tworzenie obszaru roboczego usługi Log Analytics
W Azure Portal Wyszukaj, a następnie wybierz pozycję **log Analytics obszary robocze**. 

![Zrzut ekranu przedstawiający Azure Portal pokazujący obszary robocze usługi log Analytics w polu wyszukiwania i Log Analytics obszary robocze wyróżnione w obszarze usługi.](../logs/media/quick-create-workspace/azure-portal-01.png)
  
Kliknij przycisk **Dodaj**, a następnie podaj wartości dla **grupy zasobów**, **nazwy** obszaru roboczego i **lokalizacji**. Nazwa obszaru roboczego musi być unikatowa we wszystkich subskrypcjach platformy Azure.

![Tworzenie obszaru roboczego](media/quick-collect-activity-log/create-workspace.png)

Kliknij przycisk **Przegląd + Utwórz** , aby przejrzeć ustawienia, a następnie **Utwórz** , aby utworzyć obszar roboczy. Spowoduje to wybranie domyślnej warstwy cenowej z **opcją płatność zgodnie z rzeczywistym** użyciem, która nie spowoduje ponoszenia żadnych zmian do momentu zebrania wystarczającej ilości danych. Nie jest naliczana opłata za zbieranie dziennika aktywności.


## <a name="create-diagnostic-setting"></a>Tworzenie ustawienia diagnostycznego
W Azure Portal Wyszukaj, a następnie wybierz pozycję **Monitoruj**. 

![Zrzut ekranu przedstawiający Azure Portal z "monitorem" w polu wyszukiwania i monitorze wyróżnionym w obszarze usługi.](media/quick-collect-activity-log/azure-portal-monitor.png)

Wybierz pozycję **Dziennik aktywności**. Ostatnie zdarzenia powinny być widoczne dla bieżącej subskrypcji. Kliknij pozycję **Ustawienia diagnostyczne** , aby wyświetlić ustawienia diagnostyki dla subskrypcji.

![Dziennik aktywności](media/quick-collect-activity-log/activity-log.png)

Kliknij pozycję **Dodaj ustawienie diagnostyczne** , aby utworzyć nowe ustawienie. 

![Tworzenie ustawienia diagnostycznego](media/quick-collect-activity-log/create-diagnostic-setting.png)

Wpisz nazwę, taką jak *Wyślij dziennik aktywności do obszaru roboczego*. Wybierz każdą z kategorii. Wybierz pozycję **Wyślij do log Analytics** jako jedyne miejsce docelowe, a następnie określ utworzony obszar roboczy. Kliknij przycisk **Zapisz** , aby utworzyć ustawienie diagnostyczne, a następnie zamknij stronę.

![Nowe ustawienie diagnostyczne](media/quick-collect-activity-log/new-diagnostic-setting.png)

## <a name="generate-log-data"></a>Generowanie danych dziennika
Tylko nowe wpisy dziennika aktywności będą wysyłane do obszaru roboczego Log Analytics, więc wykonaj pewne działania w ramach subskrypcji, które będą rejestrowane, takie jak uruchamianie lub zatrzymywanie maszyny wirtualnej lub tworzenie lub modyfikowanie innego zasobu. Może być konieczne odczekanie kilku minut na utworzenie ustawienia diagnostycznego i początkowy zapis danych w obszarze roboczym. Po tym opóźnieniu wszystkie zdarzenia zapisywane w dzienniku aktywności będą wysyłane do obszaru roboczego w ciągu kilku sekund.

## <a name="retrieve-data-with-a-log-query"></a>Pobieranie danych przy użyciu zapytania dziennika

Wybierz pozycję **dzienniki** w menu **Azure monitor** . Zamknij **przykładową stronę zapytań** . Jeśli zakres nie jest ustawiony w utworzonym obszarze roboczym, kliknij pozycję **Wybierz zakres** i Znajdź go.

![Zakres Log Analytics](media/quick-collect-activity-log/log-analytics-scope.png)

W oknie zapytania wpisz, `AzureActivity` a następnie kliknij przycisk **Uruchom**. Jest to proste zapytanie, które zwraca wszystkie rekordy w tabeli *usługi Azure* , która zawiera wszystkie rekordy wysyłane z dziennika aktywności.

![Proste zapytanie](media/quick-collect-activity-log/query-01.png)

Rozwiń jeden z rekordów, aby wyświetlić jego szczegółowe właściwości.

![Rozwiń właściwości](media/quick-collect-activity-log/expand-properties.png)

Spróbuj użyć bardziej złożonej kwerendy, takiej jak, `AzureActivity | summarize count() by CategoryValue` która zawiera liczbę zdarzeń podsumowywanych według kategorii.

![Zapytanie złożone](media/quick-collect-activity-log/query-02.png)


## <a name="next-steps"></a>Następne kroki
W tym przewodniku szybki start skonfigurowano wysyłanie dziennika aktywności do obszaru roboczego Log Analytics. Teraz można skonfigurować inne dane do zebrania w obszarze roboczym, w którym można je analizować przy użyciu [zapytań dzienników](../logs/log-query-overview.md) w Azure monitor i korzystać z funkcji, takich jak [alerty dzienników](../alerts/alerts-log-query.md) i [skoroszyty](../visualize/workbooks-overview.md). Następnie należy zebrać [dzienniki zasobów](../essentials/resource-logs.md) z zasobów platformy Azure, które pomogą uzyskać szczegółowe dane w dzienniku aktywności.


> [!div class="nextstepaction"]
> [Zbieranie i analizowanie dzienników zasobów przy użyciu Azure Monitor](../essentials/tutorial-resource-logs.md)