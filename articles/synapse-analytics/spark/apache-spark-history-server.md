---
title: Debugowanie aplikacji przy użyciu rozszerzonego serwera historii platformy Spark
description: Aby debugować i diagnozować aplikacje Spark w usłudze Azure Synapse Analytics, użyj rozszerzonego serwera historii platformy Spark.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 10/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: 4471fed7b423fe05147db30afe57f6c845fe640e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101670700"
---
# <a name="use-extended-apache-spark-history-server-to-debug-and-diagnose-apache-spark-applications"></a>Użyj serwera Apache Spark History, aby debugować i diagnozować aplikacje Apache Spark

Ten artykuł zawiera wskazówki dotyczące sposobu korzystania z serwera Apache Spark History do debugowania i diagnozowania ukończonych i uruchomionych aplikacji platformy Spark.

Rozszerzenie zawiera kartę dane, kartę graf i kartę Diagnostyka. Użyj karty **dane** , aby sprawdzić dane wejściowe i wyjściowe zadania platformy Spark. Na karcie **Wykres** znajduje się przepływ danych i odtwarzanie wykresu zadań. Karta **Diagnostyka** przedstawia  **pochylenie danych**, **pochylenie czasu** i **analizę użycia programu wykonującego**.

## <a name="access-the-apache-spark-history-server"></a>Dostęp do serwera historii platformy Apache Spark

Serwer historii Apache Spark jest interfejsem użytkownika sieci Web dla ukończonych i uruchomionych aplikacji platformy Spark. Interfejs sieci Web serwera historii Apache Spark można otworzyć z poziomu usługi Azure Synapse Analytics.

### <a name="open-the-spark-history-server-web-ui-from-apache-spark-applications-node"></a>Otwórz interfejs użytkownika sieci Web serwera historii platformy Spark z poziomu węzła aplikacje platformy Apache Spark

1. Otwórz [usługę Azure Synapse Analytics](https://web.azuresynapse.net/).

2. Wybierz pozycję **monitor**, a następnie wybierz pozycję **Apache Spark aplikacje**.

    ![Wybierz pozycję Monitoruj, a następnie wybierz pozycję Aplikacja platformy Spark.](./media/apache-spark-history-server/click-monitor-spark-application.png)

3. Wybierz aplikację, a następnie otwórz **zapytanie dziennika** , wybierając je.

    ![Otwórz okno zapytania dziennika.](./media/apache-spark-history-server/open-application-window.png)

4. Wybierz pozycję **serwer historii platformy Spark**, a następnie zostanie wyświetlony interfejs użytkownika sieci Web serwera historii platformy Spark.

    ![Otwórz serwer historii platformy Spark.](./media/apache-spark-history-server/open-spark-history-server.png)

### <a name="open-the-spark-history-server-web-ui-from-data-node&quot;></a>Otwórz interfejs użytkownika sieci Web serwera historii platformy Spark z węzła danych

1. W notesie usługi Azure Synapse Studio wybierz pozycję **serwer historii platformy Spark** z komórki wyjściowej wykonywania zadania lub z panelu stanu w dolnej części dokumentu notesu. Wybierz pozycję **Szczegóły sesji**.

   ![Uruchom serwer historii Spark 1](./media/apache-spark-history-server/launch-history-server2.png &quot;Uruchom serwer historii platformy Spark")

2. Wybierz pozycję **serwer historii platformy Spark** z panelu slajdy.

   ![Uruchom serwer historii Spark 2](./media/apache-spark-history-server/launch-history-server.png "Uruchom serwer historii platformy Spark")

## <a name="explore-the-data-tab-in-spark-history-server"></a>Eksplorowanie karty dane na serwerze historii platformy Spark

Wybierz identyfikator zadania dla zadania, które chcesz wyświetlić. Następnie wybierz pozycję **dane** w menu Narzędzia, aby wyświetlić widok danych. W tej sekcji pokazano, jak wykonać różne zadania na karcie dane.

* Sprawdź **dane wejściowe**, **wyjściowe** i **operacje tabeli** , zaznaczając karty osobno.

    ![Dane dla kart aplikacji platformy Spark](./media/apache-spark-history-server/apache-spark-data-tabs.png)

* Skopiuj wszystkie wiersze, wybierając pozycję **Kopiuj**.

    ![Dane dla kopiowania aplikacji platformy Spark](./media/apache-spark-history-server/apache-spark-data-copy.png)

* Zapisz wszystkie dane jako plik CSV, wybierając opcję **CSV**.

    ![Dane dotyczące zapisywania aplikacji Spark](./media/apache-spark-history-server/apache-spark-data-save.png)

* Wyszukaj, wpisując słowa kluczowe w polu **wyszukiwania**. Wyniki wyszukiwania są wyświetlane natychmiast.

    ![Dane dla wyszukiwania aplikacji platformy Spark](./media/apache-spark-history-server/apache-spark-data-search.png)

* Wybierz nagłówek kolumny, aby posortować tabelę, wybierz znak plus, aby rozwinąć wiersz, aby wyświetlić więcej szczegółów, lub wybierz znak minus, aby zwinąć wiersz.

    ![Dane dla tabeli aplikacji platformy Spark](./media/apache-spark-history-server/apache-spark-data-table.png)

* Pobierz pojedynczy plik, wybierając **częściowe pobieranie**. Wybrany plik jest pobierany do lokalnego. Jeśli plik już nie istnieje, zostanie wyświetlona nowa karta z komunikatem o błędzie.

    ![Dane dla wiersza pobierania aplikacji platformy Spark](./media/apache-spark-history-server/sparkui-data-download-row.png)

* Aby skopiować pełną ścieżkę lub ścieżkę względną, wybierz opcje **Kopiuj pełną ścieżkę** lub **Kopiuj ścieżkę względną** , które rozwiń z menu rozwijanego. W przypadku plików Azure Data Lake Storage **Otwórz w Eksplorator usługi Azure Storage** uruchamia Eksplorator usługi Azure Storage i lokalizuje folder podczas logowania.

    ![Dane dla ścieżki kopii aplikacji Spark](./media/apache-spark-history-server/sparkui-data-copy-path.png)

* Wybierz pozycję numery stron poniżej tabeli, aby nawigować po stronach, gdy na jednej stronie znajduje się zbyt wiele wierszy do wyświetlenia.

    ![Dane dla strony aplikacji platformy Spark](./media/apache-spark-history-server/apache-spark-data-page.png)

* Umieść kursor na znaku zapytania obok pozycji **dane** , aby wyświetlić etykietkę narzędzia, lub wybierz znak zapytania, aby uzyskać więcej informacji.

    ![Dane dotyczące aplikacji platformy Spark więcej informacji](./media/apache-spark-history-server/sparkui-data-more-info.png)

* Prześlij opinię z problemami, wybierając pozycję Prześlij **nam swoją opinię**.

    ![Program Spark Graph dostarczy nam swoją opinię](./media/apache-spark-history-server/sparkui-graph-feedback.png)

## <a name="graph-tab-in-apache-spark-history-server"></a>Karta Graf na serwerze historii Apache Spark

Wybierz identyfikator zadania dla zadania, które chcesz wyświetlić. Następnie wybierz pozycję **Graph** w menu Narzędzia, aby wyświetlić widok wykresu zadań.

### <a name="overview"></a>Omówienie

Przegląd zadania można zobaczyć na wykresie wygenerowanym zadania. Domyślnie wykres przedstawia wszystkie zadania. Ten widok można filtrować według **identyfikatora zadania**.

![Identyfikator zadania programu Spark Application i Graph zadania](./media/apache-spark-history-server/apache-spark-graph-jobid.png)

### <a name="display"></a>Wyświetl

Domyślnie jest wyświetlany ekran **postępu** . Przepływ danych można sprawdzić, wybierając pozycję **Odczytaj** lub **zapisaną** na liście rozwijanej **Wyświetlanie** .

![Wyświetlanie grafu aplikacji i zadania platformy Spark](./media/apache-spark-history-server/sparkui-graph-display.png)

W węźle grafu są wyświetlane kolory widoczne w legendzie mapę cieplną.

![Mapę cieplną wykresu aplikacji i zadania platformy Spark](./media/apache-spark-history-server/sparkui-graph-heatmap.png)

### <a name="playback"></a>Odtwarzanie

Aby odtworzyć zadanie, wybierz pozycję **odtwarzanie**. W dowolnym momencie można wybrać pozycję **Zatrzymaj** , aby zatrzymać. Kolory zadań pokazują różne stany podczas odtwarzania:

|Kolor|Znaczenie|
|-|-|
|Green (Zielony)|Zakończone powodzeniem: zadanie zostało ukończone pomyślnie.|
|Orange|Ponawianie próby: wystąpienia zadań, które zakończyły się niepowodzeniem, ale nie wpływają na końcowy wynik zadania. Te zadania mają zduplikowane lub ponawiane wystąpienia, które mogą się powieść później.|
|Blue (Niebieski)|Uruchomiono: zadanie jest uruchomione.|
|Biały|Oczekiwanie lub pominięte: zadanie oczekuje na uruchomienie lub etap został pominięty.|
|Red (Czerwony)|Niepowodzenie: zadanie zakończyło się niepowodzeniem.|

Na poniższej ilustracji przedstawiono kolor zielony, pomarańczowy i niebieski.

![Przykładowy kolor grafu aplikacji i zadania platformy Spark, uruchomiony](./media/apache-spark-history-server/sparkui-graph-color-running.png)

Na poniższej ilustracji przedstawiono kolor zielony i biały.

![Przykład koloru aplikacji i wykresu platformy Spark, Pomiń](./media/apache-spark-history-server/sparkui-graph-color-skip.png)

Na poniższej ilustracji przedstawiono kolory koloru czerwonego i zielonego.

![Próbka koloru aplikacji i wykresu platformy Spark nie powiodła się](./media/apache-spark-history-server/sparkui-graph-color-failed.png)

> [!NOTE]  
> Dla każdego zadania jest dozwolone odtwarzanie. W przypadku nieukończonych zadań odtwarzanie nie jest obsługiwane.

### <a name="zoom"></a>Zoom

Użyj przycisku przewiń myszą, aby powiększyć i pomniejszyć na wykresie zadania, lub wybierz pozycję **Powiększ, aby dopasować** ją do ekranu.

![Powiększenie aplikacji i wykresu zadań platformy Spark](./media/apache-spark-history-server/sparkui-graph-zoom2fit.png)

### <a name="tooltips"></a>Etykietki narzędzi

Umieść kursor w węźle grafu, aby wyświetlić etykietkę narzędzia, gdy zadania zakończone niepowodzeniem, a następnie wybierz etap, aby otworzyć jego stronę etapu.

![Etykietka narzędzia grafu aplikacji i zadania platformy Spark](./media/apache-spark-history-server/sparkui-graph-tooltip.png)

Na karcie wykres zadania etapy mają etykietkę narzędzia i małą ikonę wyświetlaną, jeśli mają one zadania, które spełniają następujące warunki:

|Warunek|Opis|
|-|-|
|Pochylenie danych|rozmiar odczytywania danych > średni rozmiar odczytywania danych wszystkich zadań w tym etapie * 2 i rozmiar odczytu danych > 10 MB|
|Pochylenie czasu|czas wykonywania > średni czas wykonywania wszystkich zadań w tym etapie * 2 i czas wykonywania > 2 minuty|
   
![Ikona pochylanie aplikacji i wykresu zadań platformy Spark](./media/apache-spark-history-server/sparkui-graph-skew-icon.png)

### <a name="graph-node-description"></a>Opis węzła grafu

W węźle grafu zadania są wyświetlane następujące informacje dotyczące poszczególnych etapów:

  * #C1.
  * Nazwa lub opis.
  * Łączny numer zadania.
  * Odczytane dane: suma rozmiaru danych wejściowych i rozmiaru losowego odczytu.
  * Zapis danych: suma rozmiaru wyjściowego i rozmiaru losowego zapisu.
  * Czas wykonywania: czas między godziną rozpoczęcia pierwszej próby i czas zakończenia ostatniej próby.
  * Liczba wierszy: suma rekordów wejściowych, rekordów wyjściowych, losowego odczytywania rekordów i losowego zapisu rekordów.
  * Wykonywane.

    > [!NOTE]  
    > Domyślnie węzeł grafu zadania wyświetla informacje z ostatniej próby każdego etapu (z wyjątkiem czasu wykonywania etapu). Jednak podczas odtwarzania węzeł grafu pokazuje informacje o każdej próbie.
    >  
    > Rozmiar danych odczytu i zapisu to 1 MB = 1000 KB = 1000 * 1000 bajtów.

### <a name="provide-feedback"></a>Wyraź opinię

Prześlij opinię z problemami, wybierając pozycję Prześlij **nam swoją opinię**.

![Opinia na temat aplikacji i wykresu zadań platformy Spark](./media/apache-spark-history-server/sparkui-graph-feedback.png)

## <a name="explore-the-diagnosis-tab-in-apache-spark-history-server"></a>Eksploruj kartę Diagnostyka na serwerze historii Apache Spark

Aby uzyskać dostęp do karty Diagnostyka, wybierz identyfikator zadania. Następnie wybierz pozycję **Diagnostyka** w menu Narzędzia, aby wyświetlić widok diagnostyki zadań. Karta Diagnostyka obejmuje **przechylenie danych**, **pochylenie czasu** i **analizę użycia programu wykonującego**.

Sprawdź **pochylenie danych**, **pochylenie czasu** i **analizę użycia wykonawcy** , wybierając odpowiednio karty.

![Karta przechylenie danych diagnostyki SparkUI ponownie](./media/apache-spark-history-server/sparkui-diagnosis-tabs.png)

### <a name="data-skew"></a>Pochylenie danych

Po wybraniu karty **pochylanie danych** odpowiednie skośne zadania są wyświetlane na podstawie określonych parametrów.

* **Określ parametry** — w pierwszej sekcji są wyświetlane parametry, które są używane do wykrywania pochylenia danych. Reguła domyślna to: odczyt danych zadania jest dłuższy niż trzy razy średniego odczytania danych zadania, a odczyt danych zadania przekracza 10 MB. Jeśli chcesz zdefiniować własną regułę dla zadań skośnych, możesz wybrać parametry. Sekcje **skośne** i **pochylone** są odpowiednio odświeżane.

* **Skośny etap** — druga sekcja wyświetla etapy, które mają skośne zadania spełniające określone powyżej kryteria. Jeśli na etapie występuje więcej niż jedno zadanie skośne, w tabeli etapów skośnych jest wyświetlane tylko zadanie najbardziej skośne (na przykład największe dane dotyczące pochylenia danych).

    ![Karta skośność danych diagnostyki sparkui](./media/apache-spark-history-server/sparkui-diagnosis-dataskew-section2.png)

* **Wykres skośny** — gdy jest zaznaczony wiersz w tabeli etap skośny, wykres skośny Wyświetla więcej szczegółów dystrybucji zadań w oparciu o czas odczytu i wykonywania danych. Skośne zadania są oznaczone kolorem czerwonym, a normalne zadania są oznaczone kolorem niebieskim. Wykres wyświetla do 100 przykładowych zadań, a szczegóły zadania są wyświetlane w prawym dolnym panelu.

    ![Wykres skośny sparkui dla etapu 10](./media/apache-spark-history-server/sparkui-diagnosis-dataskew-section3.png)

### <a name="time-skew"></a>Pochylenie czasu

Karta **przechylenie czasu** przedstawia zadania skośne w oparciu o czas wykonywania zadania.

* **Określ parametry** — w pierwszej sekcji są wyświetlane parametry, które są używane do wykrywania pochylenia czasu. Domyślne kryteria wykrywania pochylenia czasu to: czas wykonywania zadania jest dłuższy niż trzy razy średni czas wykonywania, a czas wykonywania zadania jest większy niż 30 sekund. Można zmienić parametry w zależności od potrzeb. Wykres **skośny** i **skośny** wyświetlają odpowiednie etapy i informacje o zadaniach, podobnie jak powyższa karta **skośność danych** .

* Wybierz opcję **przechylenie czasu**, a następnie przefiltrowany wynik jest wyświetlany w sekcji **skośne etap** zgodnie z parametrami ustawionymi w sekcji **Określanie parametrów**. Wybierz jeden element w sekcji **skośnego etapu** , a następnie odpowiedni wykres jest przygotowywany w section3, a szczegóły zadania są wyświetlane w prawym dolnym panelu.

    ![Sekcja pochylanie czasu diagnostyki sparkui](./media/apache-spark-history-server/sparkui-diagnosis-timeskew-section2.png)

### <a name="executor-usage-analysis"></a>Analiza użycia wykonawcy

Wykres użycia programu wykonującego wizualizacje przedstawia stan alokacji i uruchomienia programu wykonującego zadanie platformy Spark.  

1. Wybierz pozycję **Analiza użycia programu wykonującego**, a następnie cztery typy krzywe dotyczące użycia programu wykonującego są przygotowane, w tym **przydzieloną wykonawcy**, **uruchomione wykonawcy**, **wykonawcze bezczynne** i **maksymalne wystąpienia wykonawców**. W przypadku przyznanych modułów wykonujących, każdy "dodany przez program wykonawczy" lub "usunięty moduł wykonujący" zwiększa lub zmniejsza przydzieloną wykonawcy. Aby uzyskać więcej informacji, możesz sprawdzić "oś czasu zdarzeń" na karcie "zadania".

   ![Karta modułów wykonujących diagnostyki sparkui](./media/apache-spark-history-server/sparkui-diagnosis-executors.png)

2. Wybierz ikonę koloru, aby wybrać lub usunąć zaznaczenie odpowiedniej zawartości we wszystkich wersjach roboczych.

    ![sparkui diagnozuje wybór wykresu](./media/apache-spark-history-server/sparkui-diagnosis-select-chart.png)

## <a name="known-issues"></a>Znane problemy

Dane wejściowe/wyjściowe wykorzystujące rozproszone zestawy danych (odporne) nie są wyświetlane na karcie dane.

## <a name="next-steps"></a>Następne kroki

- [Azure Synapse Analytics](../overview-what-is.md)
- [Dokumentacja platformy .NET dla Apache Spark](/dotnet/spark)

