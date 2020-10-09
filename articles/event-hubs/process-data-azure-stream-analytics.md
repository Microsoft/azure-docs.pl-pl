---
title: Przetwarzanie danych z Event Hubs platformy Azure przy użyciu Stream Analytics | Microsoft Docs
description: W tym artykule pokazano, jak przetwarzać dane z centrum zdarzeń platformy Azure przy użyciu zadania Azure Stream Analytics.
ms.date: 06/23/2020
ms.topic: article
ms.openlocfilehash: 2db4fba59a1a06a24ee2939c51ecdf65aa06cef3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85312901"
---
# <a name="process-data-from-your-event-hub-using-azure-stream-analytics"></a>Przetwarzanie danych z centrum zdarzeń przy użyciu Azure Stream Analytics 
Usługa Azure Stream Analytics ułatwia pozyskiwanie, przetwarzanie i analizowanie danych przesyłanych strumieniowo z usługi Azure Event Hubs, dzięki czemu można uzyskać zaawansowane informacje o działaniach w czasie rzeczywistym. Ta Integracja umożliwia szybkie utworzenie potoku analizy na gorąco. Za pomocą Azure Portal można wizualizować dane przychodzące i pisać zapytanie Stream Analytics. Gdy zapytanie będzie gotowe, możesz je przenieść do środowiska produkcyjnego tylko na kilka kliknięć. 

## <a name="key-benefits"></a>Najważniejsze korzyści
Oto najważniejsze zalety platformy Azure Event Hubs i integracji Azure Stream Analytics: 
- **Podgląd danych** — można wyświetlić podgląd danych przychodzących z centrum zdarzeń w Azure Portal.
- **Przetestuj zapytanie** — Przygotuj zapytanie transformacji i przetestuj je bezpośrednio w Azure Portal. Składnia języka zapytań znajduje się w dokumentacji [dotyczącej języka zapytań Stream Analytics](/stream-analytics-query/built-in-functions-azure-stream-analytics) .
- **Wdróż zapytanie w środowisku produkcyjnym** — możesz wdrożyć zapytanie w środowisku produkcyjnym, tworząc i uruchamiając zadanie Azure Stream Analytics.

## <a name="end-to-end-flow"></a>Przepływ end-to-end

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). 
1. Przejdź do **przestrzeni nazw Event Hubs** , a następnie przejdź do **centrum zdarzeń**, w którym znajdują się dane przychodzące. 
1. Wybierz pozycję **Przetwarzaj dane** na stronie centrum zdarzeń.  

    ![Kafelek danych procesu](./media/process-data-azure-stream-analytics/process-data-tile.png)
1. Wybierz pozycję **Eksploruj** na kafelku **Włączanie szczegółowych informacji w czasie rzeczywistym na podstawie zdarzeń** . 

    ![Wybierz Stream Analytics](./media/process-data-azure-stream-analytics/process-data-page-explore-stream-analytics.png)
1. Zostanie wyświetlona strona zapytania z wartościami, które zostały już ustawione dla następujących pól:
    1. **Centrum zdarzeń** jako dane wejściowe zapytania.
    1. Przykładowe **zapytanie SQL** z instrukcją SELECT. 
    1. Alias **danych wyjściowych** do odwoływania się do wyników testu zapytania. 

        ![Edytor zapytań](./media/process-data-azure-stream-analytics/query-editor.png)
        
        > [!NOTE]
        >  Gdy ta funkcja jest używana po raz pierwszy, na tej stronie jest wyświetlany monit o podanie uprawnień do utworzenia grupy odbiorców oraz zasad dla centrum zdarzeń w celu wyświetlenia podglądu danych przychodzących.
1. Wybierz pozycję **Utwórz** w okienku **podglądu danych wejściowych** , jak pokazano na powyższym obrazie. 
1. Na tej karcie natychmiast zobaczysz migawkę najnowszych danych przychodzących.
    - Typ serializacji w danych jest wykrywany automatycznie (JSON/CSV). Można ją zmienić ręcznie również na format JSON/CSV/AVRO.
    - Dane przychodzące można wyświetlać w formacie tabeli lub w formacie nieprzetworzonym. 
    - Jeśli wyświetlane dane nie są aktualne, wybierz pozycję **Odśwież** , aby wyświetlić najnowsze zdarzenia. 

        Oto przykład danych w **formacie tabeli**:   ![ wyniki w formacie tabeli](./media/process-data-azure-stream-analytics/snapshot-results.png)

        Oto przykład danych w **formacie nieprzetworzonym**: 

        ![Wyniki w formacie nieprzetworzonym](./media/process-data-azure-stream-analytics/snapshot-results-raw-format.png)
1. Wybierz **zapytanie testowe** , aby wyświetlić migawkę wyników testów zapytania na karcie **wyniki testu** . Możesz również pobrać wyniki.

    ![Wyniki zapytania testowego](./media/process-data-azure-stream-analytics/test-results.png)
1. Napisz własne zapytanie, aby przekształcić dane. Zobacz [Stream Analytics Dokumentacja języka zapytań](/stream-analytics-query/stream-analytics-query-language-reference).
1. Po przetestowaniu zapytania i chcesz przenieść je do środowiska produkcyjnego, wybierz pozycję **Wdróż zapytanie**. Aby wdrożyć zapytanie, Utwórz zadanie Azure Stream Analytics, w którym można ustawić dane wyjściowe dla zadania i uruchomić zadanie. Aby utworzyć zadanie Stream Analytics, określ nazwę zadania i wybierz pozycję **Utwórz**.

      ![Tworzenie zadania usługi Azure Stream Analytics](./media/process-data-azure-stream-analytics/create-stream-analytics-job.png)

      > [!NOTE] 
      >  Zalecamy utworzenie grupy odbiorców i zasad dla każdego nowego zadania Azure Stream Analytics utworzonego ze strony Event Hubs. Grupy konsumentów zezwalają tylko na pięć współbieżnych czytników, dlatego udostępnienie dedykowanej grupy odbiorców dla każdego zadania spowoduje uniknięcie wszelkich błędów, które mogą wynikać z przekroczenia tego limitu. Dedykowane zasady umożliwiają obracanie klucza lub Odwoływanie uprawnień bez wpływu na inne zasoby. 
1. Zadanie Stream Analytics jest teraz tworzone, gdy zapytanie jest przetestowane, a dane wejściowe to centrum zdarzeń. 

9.  Aby ukończyć potok, ustaw **dane wyjściowe** zapytania i wybierz pozycję **Rozpocznij** , aby uruchomić zadanie.

    > [!NOTE]
    > Przed rozpoczęciem zadania nie zapomnij zastąpić outputalias nazwą wyjściową utworzoną w Azure Stream Analytics.

      ![Ustawianie danych wyjściowych i uruchamianie zadania](./media/process-data-azure-stream-analytics/set-output-start-job.png)


## <a name="known-limitations"></a>Znane ograniczenia
Podczas testowania zapytania wyniki testów trwają około 6 sekund. Pracujemy nad ulepszeniem testów wydajności. Jednak w przypadku wdrożenia w środowisku produkcyjnym Azure Stream Analytics będzie miał opóźnienie w drugim.

## <a name="streaming-units"></a>Jednostki przesyłania strumieniowego
Domyślna wartość zadania Azure Stream Analytics to trzy jednostki przesyłania strumieniowego (SUs). Aby dostosować to ustawienie, wybierz pozycję **skalowanie** w menu po lewej stronie **zadania Stream Analytics** w Azure Portal. Aby dowiedzieć się więcej o jednostkach przesyłania strumieniowego, zobacz [Opis i Dostosowywanie jednostek przesyłania strumieniowego](../stream-analytics/stream-analytics-streaming-unit-consumption.md).

![Skalowanie jednostek przesyłania strumieniowego](./media/process-data-azure-stream-analytics/scale.png)

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej na temat zapytań Stream Analytics, zobacz [Stream Analytics języka zapytań](/stream-analytics-query/built-in-functions-azure-stream-analytics)
