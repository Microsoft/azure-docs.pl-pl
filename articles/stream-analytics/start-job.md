---
title: Jak uruchomić zadanie Azure Stream Analytics
description: W tym artykule opisano sposób uruchamiania zadania Stream Analytics z Azure Portal, programu PowerShell i programu Visual Studio.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: how-to
ms.date: 04/03/2019
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 79149d8e9862ece24b4b2da4c2ca4afcceb23d63
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98016256"
---
# <a name="how-to-start-an-azure-stream-analytics-job"></a>Jak uruchomić zadanie Azure Stream Analytics

Zadanie Azure Stream Analytics można uruchomić za pomocą Azure Portal, programu Visual Studio i programu PowerShell. Po rozpoczęciu zadania wybierz godzinę rozpoczęcia tworzenia danych wyjściowych. Azure Portal, Visual Studio i PowerShell mają różne metody ustawiania czasu rozpoczęcia. Te metody są opisane poniżej.

## <a name="start-options"></a>Opcje uruchamiania
Trzy poniższe opcje są dostępne do uruchomienia zadania. Należy zauważyć, że wszystkie podane poniżej godziny są określone w [znaczniku czasu przez](/stream-analytics-query/timestamp-by-azure-stream-analytics). Jeśli SYGNATURa CZASowa przez nie zostanie określona, zostanie użyty czas odbioru.
* **Teraz**: sprawia, że punkt początkowy strumienia zdarzeń wyjściowych jest taki sam jak podczas uruchamiania zadania. Jeśli jest używany operator danych czasowych (np. przedział czasu, opóźnienie lub SPRZĘŻENIe), Azure Stream Analytics automatycznie powróci do danych w źródle wejściowym. Na przykład, jeśli uruchomisz zadanie "teraz" i jeśli zapytanie korzysta z okna wirowania 5 minut, Azure Stream Analytics będzie szukać danych z 5 minut temu w danych wejściowych.
Pierwsze możliwe zdarzenie wyjściowe ma sygnaturę czasową równą lub większą od bieżącego czasu, a w systemie ASA są gwarancje, że wszystkie zdarzenia wejściowe, które mogą logicznie wchodzić w skład danych wyjściowych, zostały uwzględnione. Na przykład nie są generowane częściowe agregacje okna. Zawsze jest to pełna agregowana wartość.

* **Niestandardowe**: możesz wybrać punkt początkowy danych wyjściowych. Podobnie jak w przypadku opcji **teraz** , Azure Stream Analytics automatycznie odczytywać dane przed upływem tego czasu, jeśli jest używany operator danych czasowych 

* **Gdy ostatnie zostało zatrzymane**. Ta opcja jest dostępna, gdy zadanie zostało wcześniej uruchomione, ale zostało zatrzymane ręcznie lub niepowodzeniem. Po wybraniu tej opcji Azure Stream Analytics użyje ostatniego czasu wyjściowego, aby ponownie uruchomić zadanie, dzięki czemu żadne dane nie zostaną utracone. Podobnie jak w przypadku poprzednich opcji, Azure Stream Analytics automatycznie odczytywać dane przed upływem tego czasu, jeśli używany jest operator danych czasowych. Ponieważ kilka partycji wejściowych może mieć różny czas, jest używany Najwcześniejszy czas zatrzymania wszystkich partycji, ponieważ niektóre duplikaty mogą być widoczne w danych wyjściowych. Więcej informacji na temat przetwarzania jednokrotnego jest dostępnych na stronie [gwarancje dostarczenia zdarzeń](/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics).


## <a name="azure-portal"></a>Azure Portal

Przejdź do zadania w Azure Portal i wybierz pozycję **Rozpocznij** na stronie Przegląd. Wybierz **godzinę rozpoczęcia zadania wyjściowego** , a następnie wybierz pozycję **Uruchom**.

Wybierz jedną z opcji dla **czasu rozpoczęcia zadania**. Opcje są *teraz*, *niestandardowe* i, jeśli zadanie zostało wcześniej uruchomione,  *gdy ostatnie zostało zatrzymane*. Aby uzyskać więcej informacji na temat tych opcji, zobacz powyżej.

## <a name="visual-studio"></a>Visual Studio

W widoku zadania wybierz przycisk Zielona strzałka, aby uruchomić zadanie. Ustaw **tryb uruchamiania danych wyjściowych zadania** i wybierz pozycję **Uruchom**. Stan zadania zmieni się na **uruchomiony**.

Istnieją trzy opcje **trybu uruchamiania danych wyjściowych zadania**: *JobStartTime*, *CustomTime* i *LastOutputEventTime*. Jeśli ta właściwość jest nieobecna, wartość domyślna to *JobStartTime*. Aby uzyskać więcej informacji na temat tych opcji, zobacz powyżej.


## <a name="powershell"></a>PowerShell

Użyj następującego polecenia cmdlet, aby uruchomić zadanie przy użyciu programu PowerShell:

```powershell
Start-AzStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -Name $jobName `
  -OutputStartMode 'JobStartTime'
```

Dostępne są trzy opcje **Jeśli**: *JobStartTime*, *CustomTime* i *LastOutputEventTime*. Jeśli ta właściwość jest nieobecna, wartość domyślna to *JobStartTime*. Aby uzyskać więcej informacji na temat tych opcji, zobacz powyżej.

Aby uzyskać więcej informacji na temat `Start-AzStreamAnalyitcsJob` polecenia cmdlet, zobacz temat [Start-AzStreamAnalyticsJob](/powershell/module/az.streamanalytics/start-azstreamanalyticsjob).

## <a name="next-steps"></a>Następne kroki

* [Szybki start: tworzenie zadania usługi Stream Analytics przy użyciu witryny Azure Portal](stream-analytics-quick-create-portal.md)
* [Szybki Start: Tworzenie zadania Stream Analytics przy użyciu Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Szybki start: tworzenie zadania usługi Stream Analytics przy użyciu narzędzi usługi Azure Stream Analytics Tools for Visual Studio](stream-analytics-quick-create-vs.md)