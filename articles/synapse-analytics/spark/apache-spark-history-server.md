---
title: Użyj rozszerzonego serwera historii platformy Spark do debugowania aplikacji — Apache Spark w usłudze Azure Synapse
description: Użyj rozszerzonego serwera historii platformy Spark do debugowania i diagnozowania aplikacji platformy Spark w usłudze Azure Synapse Analytics.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: 4f03033942517f4778192e0b12f84610df8fd469
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429215"
---
# <a name="use-extended-apache-spark-history-server-to-debug-and-diagnose-apache-spark-applications"></a>Użyj rozszerzonego serwera historii apache Spark do debugowania i diagnozowania aplikacji Apache Spark

Ten artykuł zawiera wskazówki dotyczące używania rozszerzonego serwera historii platformy Apache Spark do debugowania i diagnozowania ukończonych i uruchomionych aplikacji platformy Spark.

Rozszerzenie zawiera kartę danych, kartę wykresu i kartę **Data** diagnozy. Karta **Wykres** pokazuje przepływ danych i odtwarzanie wykresu zadań. Na karcie **Diagnoza** przedstawiono **pochylenie danych,** **pochylenie czasu**i **analizę użycia wykonawcy**.

## <a name="access-the-apache-spark-history-server"></a>Dostęp do serwera historii platformy Apache Spark

Serwer historii Platformy Spark Apache jest internetowym interfejsem użytkownika dla ukończonych i uruchomionych aplikacji Spark. Interfejs sieci web serwera historii platformy Apache Spark można otworzyć za pomocą usługi Azure Synapse Analytics.

### <a name="open-the-spark-history-server-web-ui-from-apache-spark-applications-node"></a>Otwórz interfejs użytkownika sieci Web serwera historii platformy Spark z węzła aplikacji iskrowych Apache

1. Otwórz [usługę Azure Synapse Analytics](https://web.azuresynapse.net/).

2. Kliknij **pozycję Monitor**, a następnie wybierz pozycję **Apache Spark Applications**.

    ![Kliknij przycisk Monitor, a następnie wybierz aplikację iskrzącą.](./media/apache-spark-history-server/click-monitor-spark-application.png)

3. Wybierz aplikację, a następnie otwórz **kwerendę dziennika,** klikając ją.

    ![Otwórz okno kwerendy dziennika.](./media/apache-spark-history-server/open-application-window.png)

4. Wybierz **serwer historii platformy Spark**, a następnie pojawi się interfejs użytkownika sieci Web serwera spark History Server.

    ![Otwórz serwer historii iskier.](./media/apache-spark-history-server/open-spark-history-server.png)

### <a name="open-the-spark-history-server-web-ui-from-data-node"></a>Otwórz interfejs użytkownika sieci Web serwera historii platformy Spark z węzła Dane

1. W notesie usługi Azure Synapse Studio wybierz **serwer historii platformy Spark** z komórki wyjściowej wykonywania zadań lub z panelu stanu u dołu dokumentu notesu. Wybierz pozycję **Szczegóły sesji**.

   ![Uruchamianie serwera historii platformy Spark](./media/apache-spark-history-server/launch-history-server2.png "Uruchamianie serwera historii platformy Spark")

2. Wybierz **serwer historii platformy Spark** z panelu wysunięcia.

   ![Uruchamianie serwera historii platformy Spark](./media/apache-spark-history-server/launch-history-server.png "Uruchamianie serwera historii platformy Spark")

## <a name="explore-the-data-tab-in-spark-history-server"></a>Eksplorowanie karty Dane na serwerze historii platformy Spark

Wybierz identyfikator zadania dla zadania, które chcesz wyświetlić. Następnie wybierz **pozycję Dane** w menu narzędzia, aby wyświetlić widok danych. W tej sekcji pokazano, jak wykonywać różne zadania na karcie Dane.

* Sprawdź **dane wejściowe,** **wyjścia**i **operacje tabeli,** wybierając karty oddzielnie.

    ![Karty danych dla aplikacji Spark](./media/apache-spark-history-server/apache-spark-data-tabs.png)

* Skopiuj wszystkie wiersze, wybierając pozycję **Kopiuj**.

    ![Dane dla kopii aplikacji Spark](./media/apache-spark-history-server/apache-spark-data-copy.png)

* Zapisz wszystkie dane jako plik CSV, wybierając **csv**.

    ![Dane dla zapisywania aplikacji Spark](./media/apache-spark-history-server/apache-spark-data-save.png)

* Wyszukaj, wprowadzając słowa kluczowe w polu **Wyszukiwanie**. Wyniki wyszukiwania są wyświetlane natychmiast.

    ![Dane dla wyszukiwania aplikacji Spark](./media/apache-spark-history-server/apache-spark-data-search.png)

* Wybierz nagłówek kolumny do sortowania tabeli, wybierz znak plus, aby rozwinąć wiersz, aby wyświetlić więcej szczegółów, lub wybierz znak minus, aby zwinąć wiersz.

    ![Data for Spark application table](./media/apache-spark-history-server/apache-spark-data-table.png)

* Pobierz pojedynczy plik, wybierając opcję **Pobieranie częściowe**. Wybrany plik zostanie pobrany do lokalnego. Jeśli plik już nie istnieje, zostanie wyświetleń nowy komunikat o błędzie.

    ![Wiersz pobierania danych dla aplikacji Spark](./media/apache-spark-history-server/sparkui-data-download-row.png)

* Aby skopiować pełną ścieżkę lub ścieżkę względną, zaznacz opcje **Kopiuj pełną ścieżkę** lub **Kopiuj ścieżkę względną,** które rozwijają się z menu rozwijanego. W przypadku plików usługi Azure Data Lake Storage **otwórz w Eksploratorze usługi Azure Storage** uruchamia Eksploratora usługi Azure Storage i lokalizuje folder po zalogowaniu się.

    ![Ścieżka kopiowania aplikacji Data for Spark](./media/apache-spark-history-server/sparkui-data-copy-path.png)

* Wybierz numery stron pod tabelą, aby poruszać się po stronach, gdy na jednej stronie jest zbyt wiele wierszy.

    ![Strona Data for Spark](./media/apache-spark-history-server/apache-spark-data-page.png)

* Umieść wskaźnik myszy na znaku zapytania obok **opcji Dane,** aby wyświetlić etykietkę narzędzia, lub wybierz znak zapytania, aby uzyskać więcej informacji.

    ![Dane dla aplikacji Spark więcej informacji](./media/apache-spark-history-server/sparkui-data-more-info.png)

* Wyślij opinię z problemami, wybierając **pozycję Przekaż nam opinię.**

    ![Wykres Iskrowy ponownie przekaże nam swoją opinię](./media/apache-spark-history-server/sparkui-graph-feedback.png)

## <a name="graph-tab-in-apache-spark-history-server"></a>Karta Wykres na serwerze historii platformy Apache Spark

Wybierz identyfikator zadania dla zadania, które chcesz wyświetlić. Następnie wybierz opcję **Wykres** w menu narzędzi, aby wyświetlić widok wykresu zadań.

### <a name="overview"></a>Omówienie

Przegląd zadania można wyświetlić na wykresie wygenerowanego zadania. Domyślnie wykres pokazuje wszystkie zadania. Ten widok można filtrować według **identyfikatora zadania**.

![Spark aplikacji i identyfikator zadania wykres pracy](./media/apache-spark-history-server/apache-spark-graph-jobid.png)

### <a name="display"></a>Monitor

Domyślnie zaznaczony jest ekran **Postępu.** Przepływ danych można sprawdzić, wybierając pozycję **Odczyt** lub **Zapis na** liście rozwijanej **Wyświetlanie.**

![Wyświetlanie aplikacji i wykresu zadań Spark](./media/apache-spark-history-server/sparkui-graph-display.png)

Węzeł wykresu wyświetla kolory wyświetlane w legendzie mapy cieplnej.

![Aplikacja Spark i wykres pracy wykres mapy cieplnej](./media/apache-spark-history-server/sparkui-graph-heatmap.png)

### <a name="playback"></a>Odtwarzanie

Aby odtworzyć zadanie, wybierz pozycję **Odtwarzanie**. Możesz wybrać **Zatrzymaj** w dowolnym momencie, aby zatrzymać. Kolory zadań pokazują różne stany podczas odtwarzania:

|Kolor|Znaczenie|
|-|-|
|Zielony|Powiodło się: Zadanie zostało zakończone pomyślnie.|
|Orange|Ponowione: Wystąpienia zadań, które nie powiodły się, ale nie mają wpływu na końcowy wynik zadania. Te zadania miały duplikaty lub ponawiać próby wystąpień, które mogą zakończyć się pomyślnie później.|
|Blue|Uruchomione: zadanie jest uruchomione.|
|Biały|Oczekiwanie lub pominięte: zadanie oczekuje na uruchomienie lub etap został pominięty.|
|Red|Nie powiodło się: zadanie nie powiodło się.|

Na poniższej ilustracji przedstawiono kolory stanu Zielony, Pomarańczowy i Niebieski.

![Aplikacja Spark i próbka kolorów wykresu zadań, uruchomiona](./media/apache-spark-history-server/sparkui-graph-color-running.png)

Na poniższej ilustracji przedstawiono zielone i białe kolory stanu.

![Aplikacja Spark i przykład koloru wykresu zadań, pomiń](./media/apache-spark-history-server/sparkui-graph-color-skip.png)

Na poniższej ilustracji przedstawiono kolory stanu Czerwony i Zielony.

![Aplikacja Spark i próbka kolorów wykresu zadań, nie powiodła się](./media/apache-spark-history-server/sparkui-graph-color-failed.png)

> [!NOTE]  
> Odtwarzanie dla każdego zadania jest dozwolone. W przypadku zadań niekompletnych odtwarzanie nie jest obsługiwane.

### <a name="zoom"></a>Zoom

Przewijanie myszy umożliwia powiększanie i pomniejszanie wykresu zadań, lub wybierz **opcję Powiększ, aby dopasować** go do ekranu.

![Aplikacja Spark i powiększenie wykresu zadań, aby pasowały](./media/apache-spark-history-server/sparkui-graph-zoom2fit.png)

### <a name="tooltips"></a>Etykietki narzędzi

Umieść wskaźnik myszy na węźle wykresu, aby wyświetlić etykietkę narzędzia, gdy zadania nie powiodły się, i wybierz etap, aby otworzyć jego stronę stołu montażowego.

![Aplikacja Spark i etykietka narzędzia wykresu zadań](./media/apache-spark-history-server/sparkui-graph-tooltip.png)

Na karcie wykres zadania etapy mają etykietkę narzędzia i małą ikonę wyświetlaną, jeśli mają zadania spełniające następujące warunki:

|Warunek|Opis|
|-|-|
|Pochylenie danych|rozmiar odczytu danych > średni rozmiar odczytu danych wszystkich zadań na tym etapie * 2 i rozmiar odczytu danych > 10 MB|
|Pochylenie czasu|czas wykonania > średni czas wykonywania wszystkich zadań na tym etapie * 2 i czas wykonania > 2 minuty|
   
![Ikona aplikacji spark i wykresu zadań](./media/apache-spark-history-server/sparkui-graph-skew-icon.png)

### <a name="graph-node-description"></a>Opis węzła wykresu

Węzeł wykresu zadania wyświetla następujące informacje o każdym etapie:

  * Identyfikator.
  * Nazwa lub opis.
  * Całkowita liczba zadań.
  * Odczyt danych: suma rozmiaru wejściowego i rozmiaru odczytu losowego.
  * Zapis danych: suma rozmiaru danych wyjściowych i losowego zapisu.
  * Czas wykonania: czas między czasem rozpoczęcia pierwszej próby a czasem ukończenia ostatniej próby.
  * Liczba wierszy: suma rekordów wejściowych, rekordów wyjściowych, rekordów odczytu losowego i losowych rekordów zapisu.
  * Postępu.

    > [!NOTE]  
    > Domyślnie węzeł wykresu zadania wyświetla informacje z ostatniej próby każdego etapu (z wyjątkiem czasu wykonania etapu). Jednak podczas odtwarzania węzeł wykresu pokazuje informacje o każdej próbie.
    >  
    > Rozmiar danych odczytu i zapisu wynosi 1 MB = 1000 KB = 1000 * 1000 bajtów.

### <a name="provide-feedback"></a>Przekazywanie opinii

Wyślij opinię z problemami, wybierając **pozycję Przekaż nam opinię.**

![Sprzężenie opinii o aplikacji i wykresach zadań Spark](./media/apache-spark-history-server/sparkui-graph-feedback.png)

## <a name="explore-the-diagnosis-tab-in-apache-spark-history-server"></a>Zapoznaj się z kartą Diagnostyka na serwerze historii platformy Apache Spark

Aby uzyskać dostęp do karty Diagnoza, wybierz identyfikator zadania. Następnie wybierz **opcję Diagnoza** w menu narzędzia, aby uzyskać zadanie Widok diagnostyki. Karta diagnoza zawiera **pochylenie danych,** **pochylenie czasu**i **analizę użycia executora.**

Sprawdź **pochylenie danych,** **pochylenie czasu**i **analizę użycia wykonawcy,** zaznaczając odpowiednio karty.

![Karta pochylenie danych diagnozy SparkUI ponownie](./media/apache-spark-history-server/sparkui-diagnosis-tabs.png)

### <a name="data-skew"></a>Pochylenie danych

Po wybraniu karty **Pochylenie danych** odpowiednie zadania skośne są wyświetlane na podstawie określonych parametrów.

* **Określ parametry** — w pierwszej sekcji wyświetlane są parametry, które są używane do wykrywania pochylenia danych. Domyślna reguła to: Odczyt danych zadania jest większy niż trzy razy średni odczyt danych zadania, a odczyt danych zadania wynosi więcej niż 10 MB. Jeśli chcesz zdefiniować własną regułę dla zadań pochylonych, możesz wybrać parametry, sekcje **Pochylony stół montażowy** i **Pochylenie** są odpowiednio odświeżane.

* **Pochylony etap** — w drugiej sekcji wyświetlane są etapy, które mają skośne zadania spełniające kryteria określone powyżej. Jeśli na etapie znajduje się więcej niż jedno zadanie skośne, tabela pochylona pokazuje tylko najbardziej pochylone zadanie (na przykład największe dane dla pochylenia danych).

    ![karta pochylenia danych diagnozy sparkui](./media/apache-spark-history-server/sparkui-diagnosis-dataskew-section2.png)

* **Wykres pochylenia** — po zaznaczeniu wiersza w tabeli pochylenia wykres pochylenia na wykresie pochylenia wyświetla więcej szczegółów dystrybucji zadań na podstawie czasu odczytu i wykonania danych. Pochylone zadania są zaznaczone na czerwono, a normalne zadania zaznaczone na niebiesko. Na wykresie jest wyświetlanych do 100 przykładowych zadań, a szczegóły zadania są wyświetlane w prawym dolnym panelu.

    ![wykres pochylenia sparkui dla etapu 10](./media/apache-spark-history-server/sparkui-diagnosis-dataskew-section3.png)

### <a name="time-skew"></a>Pochylenie czasu

Na karcie **Pochylenie czasu** są wyświetlane pochylone zadania na podstawie czasu wykonania zadania.

* **Określ parametry** — w pierwszej sekcji wyświetlane są parametry, które są używane do wykrywania pochylenia czasu. Domyślne kryteria wykrywania pochylenia czasu to: czas wykonywania zadania jest większy niż trzy razy średni czas wykonywania, a czas wykonywania zadania jest większy niż 30 sekund. Parametry można zmieniać w zależności od potrzeb. Wykres **pochylony** i **pochylony** wyświetla odpowiednie informacje o etapach i zadaniach, podobnie jak na karcie **Pochylenie danych** powyżej.

* Wybierz **opcję Pochylenie czasu,** a następnie filtrowany wynik jest wyświetlany w sekcji **Stół pochylony** zgodnie z parametrami ustawionymi w sekcji **Określ parametry**. Zaznacz jeden element w sekcji **Skośny stół montażowy,** a następnie odpowiedni wykres zostanie sporządzony w sekcji 3, a szczegóły zadania zostaną wyświetlone w prawym dolnym panelu.

    ![sekcja pochylenia czasu diagnostyki sparkui](./media/apache-spark-history-server/sparkui-diagnosis-timeskew-section2.png)

### <a name="executor-usage-analysis"></a>Analiza użycia wykonawcy

Wykres użycia executor wizualizuje alokacji i stanu działania wykonawcy zadania Platformy Spark.  

1. Wybierz **Opcję Analiza użycia executora**, a następnie sporządzane są cztery typy krzywych dotyczących użycia **wykonawcy,** w tym **przydzielone executory,** Uruchomione **executory,** **Wykonawca bezczynności**i Wystąpienia Max Executor . W odniesieniu do przydzielonych wykonawców, każde zdarzenie "Wykonawca dodany" lub "Wykonawca usunięty" zwiększa lub zmniejsza przydzielonych wykonawców. Możesz sprawdzić "Oś czasu zdarzenia" w zakładce "Zadania", aby uzyskać więcej porównań.

   ![karta wykonawców diagnostyki sparkui](./media/apache-spark-history-server/sparkui-diagnosis-executors.png)

2. Wybierz ikonę koloru, aby zaznaczyć lub usunąć zaznaczenie odpowiedniej zawartości we wszystkich wersjach pochyleniach.

    ![sparkui diagnozuje wybierz wykres](./media/apache-spark-history-server/sparkui-diagnosis-select-chart.png)

## <a name="known-issues"></a>Znane problemy

Dane wejściowe/wyjściowe przy użyciu elastycznych rozproszonych zestawów danych (RDD) nie są wyświetlane na karcie danych.

## <a name="next-steps"></a>Następne kroki

- [Azure Synapse Analytics](../overview-what-is.md)
- [.NET dla apache Spark dokumentacji](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

