---
title: Power BI integrację pulpitu nawigacyjnego z Azure Stream Analytics
description: W tym artykule opisano sposób użycia pulpitu nawigacyjnego Power BI w czasie rzeczywistym w celu wizualizacji danych z zadania Azure Stream Analytics.
author: jseb225
ms.author: jeanb
ms.service: stream-analytics
ms.topic: how-to
ms.date: 11/16/2020
ms.openlocfilehash: 3bd35df91e836245de52d8959dff0671582ebc3f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98012448"
---
# <a name="stream-analytics-and-power-bi-a-real-time-analytics-dashboard-for-streaming-data"></a>Stream Analytics i Power BI: pulpit nawigacyjny analizy w czasie rzeczywistym dla danych przesyłanych strumieniowo

Azure Stream Analytics umożliwia korzystanie z jednego z wiodących narzędzi analizy biznesowej, [Microsoft Power BI](https://powerbi.com/). W tym artykule dowiesz się, jak tworzyć narzędzia do analizy biznesowej za pomocą Power BI jako danych wyjściowych dla Azure Stream Analytics zadań. Dowiesz się również, jak utworzyć i używać pulpitu nawigacyjnego w czasie rzeczywistym, który jest stale aktualizowany przez zadanie Stream Analytics.

Ten artykuł jest nadal wykonywany w samouczku [wykrywania oszustw w czasie rzeczywistym](stream-analytics-real-time-fraud-detection.md) Stream Analytics. Kompiluje on przepływ pracy utworzony w tym samouczku i dodaje Power BI dane wyjściowe, dzięki czemu można wizualizować fałszywe połączenia telefoniczne wygenerowane przez zadanie usługi Stream Analytics. 

Możesz obejrzeć [film wideo](https://www.youtube.com/watch?v=SGUpT-a99MA)  , który ilustruje ten scenariusz.


## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że dysponujesz następującymi elementami:

* Konto platformy Azure.
* Konto dla Power BI Pro. Możesz użyć konta służbowego lub szkolnego.
* Zakończona wersja samouczka [wykrywania oszustw w czasie rzeczywistym](stream-analytics-real-time-fraud-detection.md) . Samouczek zawiera aplikację, która generuje fikcyjne metadane połączeń telefonicznych. W tym samouczku utworzysz centrum zdarzeń i wyślesz dane połączenia telefonicznego przesyłania strumieniowego do centrum zdarzeń. Napisz zapytanie, które wykrywa fałszywe wywołania (wywołania z tego samego numeru w tym samym czasie w różnych lokalizacjach). 


## <a name="add-power-bi-output"></a>Dodawanie Power BI danych wyjściowych
W samouczku wykrywanie oszustw w czasie rzeczywistym dane wyjściowe są wysyłane do usługi Azure Blob Storage. W tej sekcji dodasz dane wyjściowe, które wysyłają informacje do Power BI.

1. W Azure Portal Otwórz utworzone wcześniej zadanie usługi Stream Analytics. Jeśli użyto sugerowanej nazwy, zadanie ma nazwę `sa_frauddetection_job_demo` .

2. Z menu po lewej stronie wybierz pozycję dane **wyjściowe** w obszarze **topologia zadania**. Następnie wybierz pozycję **+ Dodaj** i wybierz pozycję **Power BI** z menu rozwijanego.

3. Wybierz pozycję **+ Dodaj**  >  **Power BI**. Następnie wypełnij formularz poniższymi informacjami i wybierz pozycję **Autoryzuj** , aby użyć własnej tożsamości użytkownika do nawiązania połączenia z usługą Power BI (token jest ważny przez 90 dni). 

>[!NOTE]
>W przypadku zadań produkcyjnych zaleca się nawiązanie połączenia z [użyciem tożsamości zarządzanej w celu uwierzytelnienia zadania Azure Stream Analytics w Power BI](./powerbi-output-managed-identity.md).

   |**Ustawienie**  |**Sugerowana wartość**  |
   |---------|---------|
   |Alias danych wyjściowych  |  CallStream-PowerBI  |
   |Nazwa zestawu danych  |   sa — zestaw danych  |
   |Nazwa tabeli |  oszukańcze wywołania  |

   ![Konfigurowanie danych wyjściowych usługi Stream Analytics](media/stream-analytics-power-bi-dashboard/configure-stream-analytics-output.png)

   > [!WARNING]
   > Jeśli Power BI ma zestaw danych i tabelę, które mają takie same nazwy jak te określone w zadaniu Stream Analytics, istniejące są zastępowane.
   > Zalecamy, aby nie tworzyć jawnie tego zestawu danych i tabeli na koncie Power BI. Są one tworzone automatycznie podczas uruchamiania zadania Stream Analytics, a zadanie rozpoczyna pompowanie danych wyjściowych do Power BI. Jeśli zapytanie zadania nie zwraca żadnych wyników, zestaw danych i tabela nie są tworzone.
   >

4. Po wybraniu pozycji **Autoryzuj** zostanie otwarte okno podręczne i zostanie wyświetlona prośba o podanie poświadczeń w celu uwierzytelnienia na koncie usługi Power BI. Kiedy autoryzacja zakończy się pomyślnie, **zapisz** ustawienia.

8. Kliknij pozycję **Utwórz**.

Zestaw danych jest tworzony z następującymi ustawieniami:

* **defaultRetentionPolicy: BasicFIFO** — dane są w języku FIFO z maksymalnie 200 000 wierszami.
* **DefaultMode: hybrydowy** — zestaw danych obsługuje oba kafelki przesyłania strumieniowego (nazywane również wypychaniem) oraz tradycyjne wizualizacje oparte na raportach. W przypadku zawartości wypychanej dane są stale aktualizowane z zadania usługi Stream Analytics w tym przypadku, bez konieczności planowania odświeżania po stronie Power BI.

Obecnie nie można tworzyć zestawów danych z innymi flagami.

Aby uzyskać więcej informacji na temat Power BI zestawów danych, zobacz temat informacje o [interfejsie API REST Power BI](/rest/api/power-bi/) .


## <a name="write-the-query"></a>Napisz zapytanie

1. Zamknij blok dane **wyjściowe** i wróć do bloku zadanie.

2. Kliknij pole **zapytanie** . 

3. Wprowadź następujące zapytanie. To zapytanie jest podobne do zapytania samosprzężenia utworzonego w samouczku wykrywania oszustwa. Różnica polega na tym, że ta kwerenda wysyła wyniki do nowo utworzonych danych wyjściowych ( `CallStream-PowerBI` ). 

    >[!NOTE]
    >Jeśli nie nazwijesz danych wejściowych `CallStream` w samouczku wykrywania oszustwa, `CallStream` **Zastąp** jego nazwę w klauzulach **from** i JOIN w zapytaniu.

   ```SQL
   /* Our criteria for fraud:
   Calls made from the same caller to two phone switches in different locations (for example, Australia and Europe) within five seconds */

   SELECT System.Timestamp AS WindowEnd, COUNT(*) AS FraudulentCalls
   INTO "CallStream-PowerBI"
   FROM "CallStream" CS1 TIMESTAMP BY CallRecTime
   JOIN "CallStream" CS2 TIMESTAMP BY CallRecTime

   /* Where the caller is the same, as indicated by IMSI (International Mobile Subscriber Identity) */
   ON CS1.CallingIMSI = CS2.CallingIMSI

   /* ...and date between CS1 and CS2 is between one and five seconds */
   AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5

   /* Where the switch location is different */
   WHERE CS1.SwitchNum != CS2.SwitchNum
   GROUP BY TumblingWindow(Duration(second, 1))
   ```

4. Kliknij pozycję **Zapisz**.


## <a name="test-the-query"></a>Testowanie zapytania

Ta sekcja jest opcjonalna, ale zalecana. 

1. Jeśli aplikacja TelcoStreaming nie jest obecnie uruchomiona, należy ją uruchomić, wykonując następujące czynności:

    * Otwórz wiersz polecenia.
    * Przejdź do folderu, w którym telcogenerator.exe i zmodyfikowano pliki telcodatagen.exe.config.
    * Uruchom następujące polecenie:

       `telcodatagen.exe 1000 .2 2`

2. Na stronie **kwerendy** Stream Analytics zadania kliknij kropki obok `CallStream` danych wejściowych, a następnie wybierz pozycję **dane przykładowe z danych wejściowych**.

3. Określ, że chcesz, aby trzy minuty "dane", a następnie kliknij przycisk **OK**. Poczekaj, aż otrzymasz powiadomienie, że próbka danych została przygotowana.

4. Kliknij przycisk **Testuj** i przejrzyj wyniki.

## <a name="run-the-job"></a>Uruchamianie zadania

1. Upewnij się, że aplikacja TelcoStreaming jest uruchomiona.

2. Przejdź do strony **Przegląd** zadania Stream Analytics i wybierz pozycję **Uruchom**.

    ![Uruchamianie zadania Stream Analytics](./media/stream-analytics-power-bi-dashboard/stream-analytics-sa-job-start-output.png)

Zadanie usługi Stream Analytics rozpocznie wyszukiwanie fałszywych wywołań w strumieniu przychodzącym. Zadanie tworzy również zestaw danych i tabelę w Power BI i zaczyna wysyłać dane dotyczące fałszywych wywołań do nich.


## <a name="create-the-dashboard-in-power-bi"></a>Tworzenie pulpitu nawigacyjnego w Power BI

1. Przejdź do [PowerBI.com](https://powerbi.com) i zaloguj się przy użyciu konta służbowego. Jeśli zadanie Stream Analytics wysyła wyniki zapytania wyjściowego, zobaczysz, że zestaw danych jest już utworzony:

    ![Lokalizacja zestawu danych przesyłania strumieniowego w Power BI](./media/stream-analytics-power-bi-dashboard/stream-analytics-streaming-dataset.png)

2. W obszarze roboczym kliknij pozycję **+ &nbsp; Utwórz**.

    ![Przycisk Utwórz w obszarze roboczym Power BI](./media/stream-analytics-power-bi-dashboard/pbi-create-dashboard.png)

3. Utwórz nowy pulpit nawigacyjny i nadaj mu nazwę `Fraudulent Calls` .

    ![Utwórz pulpit nawigacyjny i nadaj mu nazwę w obszarze roboczym Power BI](./media/stream-analytics-power-bi-dashboard/pbi-create-dashboard-name.png)

4. W górnej części okna kliknij pozycję **Dodaj kafelek**, wybierz pozycję **niestandardowe dane przesyłane strumieniowo**, a następnie kliknij przycisk **dalej**.

    ![Kafelek niestandardowego zestawu danych przesyłania strumieniowego w Power BI](./media/stream-analytics-power-bi-dashboard/custom-streaming-data.png)

5. W obszarze **DATSETS** wybierz swój zestaw danych, a następnie kliknij przycisk **dalej**.

    ![Zestaw danych przesyłania strumieniowego w Power BI](./media/stream-analytics-power-bi-dashboard/your-streaming-dataset.png)

6. W obszarze **typ wizualizacji** wybierz pozycję **karta**, a następnie na liście **pola** wybierz pozycję **fraudulentcalls**.

    ![Szczegóły wizualizacji dla nowego kafelka](./media/stream-analytics-power-bi-dashboard/add-fraudulent-calls-tile.png)

7. Kliknij przycisk **Dalej**.

8. Wypełnij szczegóły kafelka, takie jak tytuł i podtytuł.

    ![Tytuł i podtytuł dla nowego kafelka](./media/stream-analytics-power-bi-dashboard/pbi-new-tile-details.png)

9. Kliknij pozycję **Zastosuj**.

    Teraz masz licznik oszustw!

    ![Licznik oszustw w Power BI pulpicie nawigacyjnym](./media/stream-analytics-power-bi-dashboard/power-bi-fraud-counter-tile.png)

8. Wykonaj ponownie kroki w celu dodania kafelka (począwszy od kroku 4). Tym razem wykonaj następujące czynności:

    * Po wybraniu **typu wizualizacji** wybierz pozycję **Wykres liniowy**. 
    * Dodaj oś i wybierz pozycję **windowend**. 
    * Dodaj wartość i wybierz pozycję **fraudulentcalls**.
    * W ustawieniu **Okno czasowe do wyświetlenia** wybierz ostatnie 10 minut.

      ![Utwórz kafelek dla wykresu liniowego w Power BI](./media/stream-analytics-power-bi-dashboard/pbi-create-tile-line-chart.png)

9. Kliknij przycisk **dalej**, Dodaj tytuł i podtytuł, a następnie kliknij przycisk **Zastosuj**.

     Pulpit nawigacyjny Power BI udostępnia teraz dwa widoki danych dotyczących fałszywych wywołań, które zostały wykryte w danych przesyłanych strumieniowo.

     ![Zakończono Power BI pulpit nawigacyjny przedstawiający dwa kafelki dla fałszywych wywołań](./media/stream-analytics-power-bi-dashboard/pbi-dashboard-fraudulent-calls-finished.png)

## <a name="learn-about-limitations-and-best-practices"></a>Informacje o ograniczeniach i najlepszych rozwiązaniach
Obecnie Power BI można wywołać w dowolnym momencie na sekundę. Wizualizacje przesyłania strumieniowego obsługują pakiety o wartości 15 KB. Poza tym, wizualizacje przesyłania strumieniowego kończą się niepowodzeniem (ale wypchnięcie kontynuuje pracę). Ze względu na te ograniczenia Power BI się w sytuacji, w której wystąpiły najczęstsze sytuacje, w których Azure Stream Analytics znaczący spadek obciążenia danych. Zalecamy korzystanie z okna wirowania lub okna przeskoku w celu zapewnienia, że wypychanie danych jest co najwyżej jedną wypychaną na sekundę, oraz że zapytanie jest używane w ramach wymagań dotyczących przepływności.

Możesz użyć poniższego równania, aby obliczyć wartość w ciągu kilku sekund:

![Równanie wartości obliczeniowej, aby przyznać okno w sekundach](./media/stream-analytics-power-bi-dashboard/compute-window-seconds-equation.png)  

Na przykład:

* Masz 1 000 urządzeń wysyłających dane w odstępach jednosekundowych.
* Używasz jednostki SKU Power BI Pro, która obsługuje 1 000 000 wierszy na godzinę.
* Chcesz opublikować średnią ilość danych na urządzenie do Power BI.

W efekcie równanie zmieni się:

![Równanie na podstawie przykładowych kryteriów](./media/stream-analytics-power-bi-dashboard/power-bi-example-equation.png)  

Uwzględniając tę konfigurację, można zmienić oryginalne zapytanie na następujące:

```SQL
    SELECT
        MAX(hmdt) AS hmdt,
        MAX(temp) AS temp,
        System.TimeStamp AS time,
        dspl
    INTO "CallStream-PowerBI"
    FROM
        Input TIMESTAMP BY time
    GROUP BY
        TUMBLINGWINDOW(ss,4),
        dspl
```

### <a name="renew-authorization"></a>Odnów autoryzację
Jeśli hasło zostało zmienione od czasu utworzenia lub ostatniego uwierzytelnienia zadania, należy ponownie uwierzytelnić konto Power BI. Jeśli usługa Azure AD Multi-Factor Authentication jest skonfigurowana w dzierżawie Azure Active Directory (Azure AD), należy również odnowić Power BI autoryzację co dwa tygodnie. Jeśli nie odnowisz, zobaczysz objawy, takie jak brak danych wyjściowych zadania lub `Authenticate user error` w dziennikach operacji.

Podobnie, jeśli zadanie rozpoczyna się po wygaśnięciu tokenu, wystąpi błąd i zadanie kończy się niepowodzeniem. Aby rozwiązać ten problem, Zatrzymaj uruchomione zadanie i przejdź do danych wyjściowych Power BI. Aby uniknąć utraty danych, wybierz łącze **Odnów autoryzację** , a następnie uruchom ponownie zadanie od **czasu ostatniego zatrzymania**.

Po odświeżeniu autoryzacji za pomocą Power BI zielony alert pojawi się w obszarze autoryzacji w celu odzwierciedlenia, że problem został rozwiązany.

## <a name="next-steps"></a>Następne kroki
* [Wprowadzenie do Azure Stream Analytics](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics (Rozpoczynanie pracy z usługą Azure Stream Analytics)](stream-analytics-real-time-fraud-detection.md)
* [Stream Analytics dane wyjściowe](stream-analytics-define-outputs.md)
* [Azure Stream Analytics Dokumentacja języka zapytań](/stream-analytics-query/stream-analytics-query-language-reference)
* [Dokumentacja interfejsu API REST zarządzania Azure Stream Analytics](/rest/api/streamanalytics/)
* [Za pomocą tożsamości zarządzanej można uwierzytelniać zadanie Azure Stream Analytics w Power BI](./powerbi-output-managed-identity.md)
