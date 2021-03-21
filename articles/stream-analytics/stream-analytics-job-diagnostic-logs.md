---
title: Rozwiązywanie problemów Azure Stream Analytics przy użyciu dzienników zasobów
description: W tym artykule opisano sposób analizowania dzienników zasobów w Azure Stream Analytics.
author: jseb225
ms.author: jeanb
ms.service: stream-analytics
ms.topic: troubleshooting
ms.custom: contperf-fy21q1
ms.date: 06/18/2020
ms.openlocfilehash: 93d881419c4854b8e46608e150b55072267e0347
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100574413"
---
# <a name="troubleshoot-azure-stream-analytics-by-using-resource-logs"></a>Rozwiązywanie problemów z Azure Stream Analytics przy użyciu dzienników zasobów

Czasem może się zdarzyć, że przetwarzanie zadania usługi Azure Stream Analytics zostanie nieoczekiwanie zatrzymane. Ważne jest, aby było możliwe rozwiązanie problemu z tego rodzaju zdarzeniem. Mogą one być spowodowane przez nieoczekiwany wynik zapytania, problemy z łącznością z urządzeniami lub nieoczekiwaną awarię usługi. Dzienniki zasobów w Stream Analytics mogą pomóc w zidentyfikowaniu przyczyny problemów występujących i skróceniu czasu odzyskiwania.

Zdecydowanie zaleca się włączenie dzienników zasobów dla wszystkich zadań, ponieważ ułatwi to debugowanie i monitorowanie.

## <a name="log-types"></a>Typy dzienników

Stream Analytics oferuje dwa typy dzienników:

* [Dzienniki aktywności](../azure-monitor/essentials/platform-logs-overview.md) (zawsze włączone), które dają wgląd w operacje wykonywane na zadaniach.

* [Dzienniki zasobów](../azure-monitor/essentials/platform-logs-overview.md) (konfigurowalne), które zapewniają bogatszy wgląd w wszystko, co się dzieje z zadaniami. Dzienniki zasobów zaczynają się, gdy zadanie zostanie utworzone i zakończy się, gdy zadanie zostanie usunięte. Obejmują one zdarzenia, gdy zadanie jest aktualizowane i uruchomione.

> [!NOTE]
> Aby analizować dane niezgodne, można użyć usług takich jak Azure Storage, Azure Event Hubs i Azure Monitor logs. Opłata jest naliczana na podstawie modelu cen dla tych usług.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="debugging-using-activity-logs"></a>Debugowanie przy użyciu dzienników aktywności

Dzienniki aktywności są domyślnie włączone i zapewniają szczegółowe informacje o operacjach wykonywanych przez zadanie Stream Analytics. Informacje znajdujące się w dziennikach aktywności mogą pomóc w znalezieniu głównej przyczyny problemów wpływających na zadanie. Wykonaj następujące kroki, aby użyć dzienników aktywności w Stream Analytics:

1. Zaloguj się do Azure Portal i wybierz pozycję **Dziennik aktywności** w obszarze **Przegląd**.

   ![Stream Analytics dziennik aktywności](./media/stream-analytics-job-diagnostic-logs/stream-analytics-menu.png)

2. Można wyświetlić listę operacji, które zostały wykonane. Każda operacja, która spowodowała niepowodzenie zadania, zawiera czerwony dymek informacyjny.

3. Kliknij operację, aby wyświetlić jej widok podsumowania. Informacje są często ograniczone. Aby dowiedzieć się więcej na temat operacji, kliknij pozycję **JSON**.

   ![Podsumowanie operacji dziennika aktywności Stream Analytics](./media/stream-analytics-job-diagnostic-logs/operation-summary.png)

4. Przewiń w dół do sekcji **Właściwości** w formacie JSON, która zawiera szczegółowe informacje o błędzie, który spowodował niepowodzenie operacji. W tym przykładzie błąd był spowodowany błędem środowiska uruchomieniowego z nieprawidłowych wartości szerokości geograficznej. Niezgodność danych przetwarzanych przez zadanie Stream Analytics powoduje błąd danych. Można dowiedzieć się więcej o różnych [błędach danych wejściowych i wyjściowych oraz o tym, dlaczego występują](./data-errors.md).

   ![Szczegóły błędu JSON](./media/stream-analytics-job-diagnostic-logs/error-details.png)

5. Możesz podejmować działania naprawcze na podstawie komunikatu o błędzie w formacie JSON. W tym przykładzie sprawdza, czy wartość szerokości geograficznej jest między-90 stopni i 90 stopni należy dodać do zapytania.

6. Jeśli komunikat o błędzie w dziennikach aktywności nie jest pomocny w przypadku identyfikowania głównej przyczyny, należy włączyć dzienniki zasobów i użyć dzienników Azure Monitor.

## <a name="send-diagnostics-to-azure-monitor-logs"></a>Wyślij diagnostykę do dzienników Azure Monitor

Zdecydowanie zaleca się włączenie dzienników zasobów i wysłanie ich do dzienników Azure Monitor. Domyślnie są one **wyłączone** . Aby je włączyć, wykonaj następujące kroki:

1.  Utwórz obszar roboczy Log Analytics, jeśli jeszcze go nie masz. Zaleca się, aby obszar roboczy Log Analytics w tym samym regionie co Stream Analytics zadanie.

2.  Zaloguj się do Azure Portal i przejdź do zadania Stream Analytics. W obszarze **monitorowanie** wybierz pozycję **dzienniki diagnostyczne**. Następnie wybierz pozycję **Włącz diagnostykę**.

    ![Nawigacja w bloku do dzienników zasobów](./media/stream-analytics-job-diagnostic-logs/diagnostic-logs-monitoring.png)  

2.  Podaj **nazwę** w polu **Nazwa ustawień diagnostycznych** i zaznacz pola wyboru do **wykonania** i **tworzenia** w obszarze **Dziennik**, a następnie pozycję **AllMetrics** w obszarze **Metryka**. Następnie wybierz pozycję **Wyślij do log Analytics** a następnie wybierz obszar roboczy. Kliknij pozycję **Zapisz**.

    ![Ustawienia dzienników zasobów](./media/stream-analytics-job-diagnostic-logs/logs-setup.png)

3. Po rozpoczęciu zadania Stream Analytics dzienniki zasobów są kierowane do obszaru roboczego Log Analytics. Aby wyświetlić dzienniki zasobów dla zadania, wybierz pozycję **dzienniki** w sekcji **monitorowanie** .

   ![Zrzut ekranu przedstawia menu ogólne z wybranymi dziennikami.](./media/stream-analytics-job-diagnostic-logs/diagnostic-logs.png)

4. Stream Analytics udostępnia wstępnie zdefiniowane zapytania, które umożliwiają łatwe wyszukiwanie dzienników, które Cię interesują. Możesz wybrać wszystkie wstępnie zdefiniowane zapytania w okienku po lewej stronie, a następnie wybrać polecenie **Uruchom**. Wyniki zapytania będą widoczne w dolnym okienku. 

   ![Zrzut ekranu przedstawia dzienniki zadania Stream Analyticsowego.](./media/stream-analytics-job-diagnostic-logs/logs-example.png)

## <a name="resource-log-categories"></a>Kategorie dzienników zasobów

Azure Stream Analytics przechwytuje dwie kategorie dzienników zasobów:

* **Tworzenie**: przechwytuje zdarzenia dziennika, które są związane z operacjami tworzenia zadań, takimi jak tworzenie zadań, Dodawanie i usuwanie danych wejściowych i wyjściowych, Dodawanie i aktualizowanie zapytania oraz uruchamianie lub zatrzymywanie zadania.

* **Wykonywanie**: przechwytuje zdarzenia, które wystąpiły podczas wykonywania zadania.
    * Błędy łączności
    * Błędy przetwarzania danych, w tym:
        * Zdarzenia, które nie są zgodne z definicją zapytania (niezgodne typy pól i wartości, brakujące pola itd.)
        * Błędy oceny wyrażenia
    * Inne zdarzenia i błędy

## <a name="resource-logs-schema"></a>Schemat dzienników zasobów

Wszystkie dzienniki są przechowywane w formacie JSON. Każdy wpis ma następujące wspólne pola ciągów:

Nazwa | Opis
------- | -------
time | Sygnatura czasowa (w formacie UTC) dziennika.
resourceId | Identyfikator zasobu, w którym miała miejsce operacja, w Wielkiej litery. Zawiera identyfikator subskrypcji, grupę zasobów i nazwę zadania. Na przykład **/SUBSCRIPTIONS/6503D296-DAC1-4449-9B03-609A1F4A1C87/RESOURCEGROUPS/my-Resource-Group/Providers/Microsoft. STREAMANALYTICS/STREAMINGJOBS/MYSTREAMINGJOB**.
category | Kategoria dziennika, **wykonanie** lub **Tworzenie**.
operationName | Nazwa rejestrowanej operacji. Na przykład **wysyłanie zdarzeń: niepowodzenie zapisu danych wyjściowych SQL do mysqloutput**.
status | Stan operacji. Na przykład **nie powiodło się** lub **zakończyło** się niepowodzeniem.
poziom | Poziom dziennika. Na przykład **błąd**, **Ostrzeżenie** lub **informacyjny**.
properties | Szczegóły wpisu dziennika, Zserializowany jako ciąg JSON. Aby uzyskać więcej informacji, zobacz następujące sekcje w tym artykule.

### <a name="execution-log-properties-schema"></a>Schemat właściwości dziennika wykonywania

Dzienniki wykonywania zawierają informacje o zdarzeniach, które wystąpiły podczas wykonywania zadania Stream Analytics. Schemat właściwości różni się w zależności od tego, czy zdarzenie jest błędem danych czy zdarzeniem ogólnym.

### <a name="data-errors"></a>Błędy danych

Każdy błąd, który występuje, gdy zadanie przetwarza dane, jest w tej kategorii dzienników. Te dzienniki najczęściej są tworzone podczas operacji odczytu, serializacji i zapisu danych. Te dzienniki nie obejmują błędów łączności. Błędy łączności są traktowane jako zdarzenia ogólne. Możesz dowiedzieć się więcej na temat przyczyny różnych [błędów danych wejściowych i wyjściowych](./data-errors.md).

Nazwa | Opis
------- | -------
Element źródłowy | Nazwa danych wejściowych lub wyjściowych zadania, w których wystąpił błąd.
Komunikat | Komunikat skojarzony z błędem.
Typ | Typ błędu. Na przykład **DataConversionError**, **CsvParserError** lub **ServiceBusPropertyColumnMissingError**.
Dane | Zawiera dane, które są przydatne do dokładnego lokalizowania źródła błędu. Podlega obcięciu, w zależności od rozmiaru.

W zależności od wartości **OperationName** , błędy danych mają następujący schemat:

* Podczas operacji odczytu zdarzeń wystąpią **serializowane zdarzenia** . Występują one, gdy dane w danych wejściowych nie spełniają schematu zapytania z jednego z następujących powodów:

   * *Niezgodność typów podczas serializacji zdarzenia (de)*: Określa pole, które powoduje błąd.

   * *Nie można odczytać zdarzenia, nieprawidłowa Serializacja*: wyświetla informacje o lokalizacji w danych wejściowych, w której wystąpił błąd. Zawiera nazwę obiektu BLOB dla danych wejściowych obiektów blob, przesunięcia i próbkowania.

* **Zdarzenia wysyłania** występują podczas operacji zapisu. Identyfikują one zdarzenie przesyłania strumieniowego, które spowodowało błąd.

### <a name="generic-events"></a>Zdarzenia ogólne

Zdarzenia ogólne obejmują wszystko inne.

Nazwa | Opis
-------- | --------
Błąd | obowiązkowe Informacje o błędzie. Zazwyczaj są to informacje o wyjątkach, jeśli są dostępne.
Komunikat| Komunikat dziennika.
Typ | Typ komunikatu. Mapuje na wewnętrzną kategoryzację błędów. Na przykład **JobValidationError** lub **BlobOutputAdapterInitializationFailure**.
Identyfikator korelacji | Identyfikator GUID, który jednoznacznie identyfikuje wykonywanie zadania. Wszystkie wpisy dziennika wykonania od momentu uruchomienia zadania do momentu zatrzymania zadania mają tę samą wartość **identyfikatora korelacji** .

## <a name="next-steps"></a>Następne kroki

* [Błędy danych Stream Analytics](./data-errors.md)
* [Stream Analytics Dokumentacja języka zapytań](/stream-analytics-query/stream-analytics-query-language-reference)