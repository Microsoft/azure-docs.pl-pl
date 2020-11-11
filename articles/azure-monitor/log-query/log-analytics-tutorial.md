---
title: Samouczek usługi Log Analytics
description: Zapoznaj się z tym samouczkiem, jak używać funkcji Log Analytics w programie Azure Monitor do tworzenia zapytania o uruchomienie dziennika i analizowania jego wyników w Azure Portal.
ms.subservice: logs
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 10/07/2020
ms.openlocfilehash: bd6ba091b3715741af929613600a59511e7cdb0b
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/11/2020
ms.locfileid: "94498347"
---
# <a name="log-analytics-tutorial"></a>Samouczek usługi Log Analytics
Log Analytics to narzędzie w Azure Portal do edytowania i uruchamiania zapytań dzienników z danych zebranych przez dzienniki Azure Monitor i interaktywnie analizować ich wyniki. Za pomocą zapytań Log Analytics można pobierać rekordy spełniające określone kryteria, identyfikować trendy, analizować wzorce i udostępniać różnorodne informacje w danych. 

Ten samouczek przeprowadzi Cię przez interfejs Log Analytics, pozwala rozpocząć pracę z niektórymi podstawowymi zapytaniami i pokazuje, jak można korzystać z wyników. Dowiesz się:

> [!div class="checklist"]
> * Informacje o schemacie danych dziennika
> * Zapisuj i uruchamiaj proste zapytania oraz Modyfikuj zakres czasu dla zapytań
> * Filtrowanie, sortowanie i grupowanie wyników zapytania
> * Wyświetlanie, modyfikowanie i udostępnianie wizualizacji wyników zapytania
> * Zapisz, Załaduj, Eksportuj i Kopiuj zapytania i wyniki

> [!IMPORTANT]
> Ten samouczek używa funkcji Log Analytics do kompilowania i uruchamiania zapytania zamiast pracy z samymi kwerendą. Będziesz korzystać z funkcji Log Analytics, aby utworzyć jedno zapytanie i użyć innego przykładowego zapytania. Gdy wszystko jest gotowe do poznania składni zapytań i rozpoczęcia bezpośredniego edytowania zapytania, przejdź przez [samouczek języka zapytań Kusto](/azure/data-explorer/kusto/query/tutorial?pivots=azuremonitor). Ten samouczek przeprowadzi Cię przez kilka przykładowych zapytań, które można edytować i uruchamiać w Log Analytics, wykorzystując kilka funkcji, które można poznać w tym samouczku.


## <a name="prerequisites"></a>Wymagania wstępne
W tym samouczku jest używany [środowisko demonstracyjne log Analytics](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring_Logs/DemoLogsBlade), które obejmuje wiele przykładowych danych obsługujących przykładowe zapytania. Możesz również użyć własnej subskrypcji platformy Azure, ale nie masz danych w tych samych tabelach.

## <a name="open-log-analytics"></a>Otwórz Log Analytics
Otwórz [środowisko demonstracyjne log Analytics](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring_Logs/DemoLogsBlade) lub wybierz pozycję **dzienniki** z menu Azure monitor w subskrypcji. Spowoduje to ustawienie zakresu początkowego do obszaru roboczego Log Analytics, co oznacza, że zapytanie zostanie wybrane z wszystkich danych w tym obszarze roboczym. W przypadku wybrania opcji **dzienniki** z menu zasobów platformy Azure zakres jest ustawiany na tylko rekordy z tego zasobu. Aby uzyskać szczegółowe informacje o zakresie, zobacz [zakres zapytania dziennika](scope.md) .

Zakres można wyświetlić w lewym górnym rogu ekranu. Jeśli używasz własnego środowiska, zobaczysz opcję wyboru innego zakresu, ale ta opcja jest niedostępna w środowisku demonstracyjnym.

[![Zakres zapytania](media/log-analytics-tutorial/scope.png)](media/log-analytics-tutorial/scope.png#lightbox)

## <a name="table-schema"></a>Schemat tabeli
Lewa strona ekranu zawiera kartę **tabele** , która umożliwia sprawdzenie tabel dostępnych w bieżącym zakresie. Są one pogrupowane według **rozwiązań** domyślnie, ale zmiana ich grupowania lub filtrowania. 

Rozwiń rozwiązanie do **zarządzania dziennikami** i Znajdź tabelę **Azure** . Możesz rozwinąć tabelę, aby wyświetlić jej schemat, lub umieść wskaźnik myszy nad jej nazwą, aby wyświetlić dodatkowe informacje na jej temat. 

[![Widok tabel](media/log-analytics-tutorial/table-details.png)](media/log-analytics-tutorial/table-details.png#lightbox)

Kliknij pozycję **Dowiedz się więcej** , aby przejść do tabeli zawierającej informacje o każdej tabeli i jej kolumnach. Kliknij pozycję **Podgląd danych** , aby szybko zapoznać się z kilkoma ostatnimi rekordami w tabeli. Może to być przydatne, aby upewnić się, że są to dane, które są oczekiwane przed faktycznym uruchomieniem zapytania.

[![Przykładowe dane](media/log-analytics-tutorial/sample-data.png)](media/log-analytics-tutorial/sample-data.png#lightbox)

## <a name="write-a-query"></a>Napisz zapytanie
Przejdźmy do zapytania i napiszemy zapytanie przy użyciu tabeli **usługi Azure** . Kliknij dwukrotnie jego nazwę, aby dodać ją do okna zapytania. Możesz również wpisać bezpośrednio w oknie, a nawet pobrać funkcję IntelliSense, która pomoże wypełnić nazwy tabel w bieżącym zakresie i KQL polecenia.

Jest to najprostsza kwerenda, którą możemy napisać. Po prostu zwraca wszystkie rekordy w tabeli. Uruchom ją, klikając przycisk **Run (Uruchom** ) lub naciskając klawisze SHIFT + ENTER z kursorem umieszczonym w dowolnym miejscu w tekście zapytania.

[![Wyniki zapytania](media/log-analytics-tutorial/query-results.png)](media/log-analytics-tutorial/query-results.png#lightbox)

Zobaczysz, że mamy wyniki. Liczba rekordów zwracanych przez zapytanie jest wyświetlana w prawym dolnym rogu. 

## <a name="filter"></a>Filtr

Dodajmy filtr do zapytania, aby zmniejszyć liczbę zwracanych rekordów. Wybierz kartę **Filtr** w lewym okienku. Pokazuje różne kolumny w wynikach zapytania, których można użyć do filtrowania wyników. Wartości górne w tych kolumnach są wyświetlane wraz z liczbą rekordów z tą wartością. Kliknij pozycję **administracyjne** w obszarze **CategoryValue** , a następnie **Zastosuj & uruchomienie**. 

[![Okienko zapytania](media/log-analytics-tutorial/query-pane.png)](media/log-analytics-tutorial/query-pane.png#lightbox)

Instrukcja **WHERE** jest dodawana do zapytania z wybraną wartością. Wyniki zawierają teraz tylko te rekordy, które mają tę wartość, aby można było zobaczyć, że liczba rekordów jest ograniczona.

[![Przefiltrowane wyniki zapytania](media/log-analytics-tutorial/query-results-filter-01.png)](media/log-analytics-tutorial/query-results-filter-01.png#lightbox)


## <a name="time-range"></a>Przedział czasu
Wszystkie tabele w Log Analytics obszarze roboczym mają kolumnę o nazwie **TimeGenerated** , czyli godzinę utworzenia rekordu. Wszystkie zapytania mają zakres czasu, który ogranicza wyniki do rekordów z wartością **TimeGenerated** w tym zakresie. Zakres czasu można ustawić w zapytaniu lub za pomocą selektora w górnej części ekranu.

Domyślnie zapytanie zwróci rekordy z ostatnich 24 godzin. Wybierz listę rozwijaną **zakres czasu** i zmień ją na **7 dni**. Kliknij przycisk **Uruchom** ponownie, aby zwrócić wyniki. Można zobaczyć, że wyniki są zwracane, ale mamy komunikat informujący o tym, że nie widzimy wszystkich wyników. Dzieje się tak, ponieważ Log Analytics może zwrócić maksymalnie 10 000 rekordów, a zapytanie zwróciło więcej rekordów niż to. 

[![Zakres czasu](media/log-analytics-tutorial/query-results-max.png)](media/log-analytics-tutorial/query-results-max.png#lightbox)


## <a name="multiple-query-conditions"></a>Wiele warunków zapytania
Zmniejszmy nasze wyniki, dodając inny warunek filtru. Zapytanie może zawierać dowolną liczbę filtrów, aby określić odpowiedni zestaw rekordów. Wybierz pozycję **sukces** w obszarze **ActivityStatusValue** , a następnie kliknij pozycję **Zastosuj & Uruchom**. 

[![Wyniki zapytania z wieloma filtrami](media/log-analytics-tutorial/query-results-filter-02.png)](media/log-analytics-tutorial/query-results-filter-02.png#lightbox)


## <a name="analyze-results"></a>Analizowanie wyników
Oprócz pisania i uruchamiania zapytań, Log Analytics udostępnia funkcje do pracy z wynikami. Zacznij od rozwinięcia rekordu, aby wyświetlić jego wartości dla wszystkich kolumn.

[![Rozwiń rekord](media/log-analytics-tutorial/expand-record.png)](media/log-analytics-tutorial/expand-record.png#lightbox)

Kliknij nazwę dowolnej kolumny, aby posortować wyniki według tej kolumny. Kliknij ikonę filtru obok niej, aby podać warunek filtru. Jest to podobne do dodawania warunku filtru do samego zapytania, z tą różnicą, że ten filtr jest wyczyszczony, jeśli zapytanie zostanie uruchomione ponownie. Użyj tej metody, jeśli chcesz szybko analizować zestaw rekordów w ramach analizy interaktywnej.

Na przykład Ustaw filtr w kolumnie **CallerIpAddress** , aby ograniczyć rekordy do pojedynczego obiektu wywołującego. 

[![Filtr wyników zapytania](media/log-analytics-tutorial/query-results-filter.png)](media/log-analytics-tutorial/query-results-filter.png#lightbox)

Zamiast filtrować wyniki, można grupować rekordy według określonej kolumny. Wyczyść filtr, który został właśnie utworzony, a następnie Włącz suwak **Grupuj kolumny** . 

[![Grupuj kolumny](media/log-analytics-tutorial/query-results-group-columns.png)](media/log-analytics-tutorial/query-results-group-columns.png#lightbox)

Teraz przeciągnij kolumnę **CallerIpAddress** do wiersza grupowania. Wyniki są teraz zorganizowane według tej kolumny i można zwinąć każdą grupę, aby pomóc Ci w analizie.

[![Pogrupowane wyniki zapytania](media/log-analytics-tutorial/query-results-grouped.png)](media/log-analytics-tutorial/query-results-grouped.png#lightbox)

## <a name="work-with-charts"></a>Pracuj z wykresami
Przyjrzyjmy się zapytaniu korzystającemu z danych liczbowych, które można wyświetlić na wykresie. Zamiast tworzenia zapytania wybierzemy przykładowe zapytanie.

Kliknij pozycję **zapytania** w okienku po lewej stronie. To okienko zawiera przykładowe zapytania, które można dodać do okna zapytania. Jeśli używasz własnego obszaru roboczego, musisz mieć różne zapytania w wielu kategoriach, ale jeśli używasz środowiska demonstracyjnego, możesz zobaczyć tylko jedną kategorię **log Analytics obszarów roboczych** . Rozwiń, aby wyświetlić zapytania w kategorii.

Kliknij zapytanie o nazwie **Liczba żądań według ResponseCode**. Spowoduje to dodanie zapytania do okna zapytania. Zwróć uwagę, że nowe zapytanie jest oddzielone od drugiego przez pusty wiersz. Zapytanie w KQL kończą się, gdy napotka pusty wiersz, dlatego są one widoczne jako osobne zapytania. 

[![Nowe zapytanie](media/log-analytics-tutorial/example-query.png)](media/log-analytics-tutorial/example-query.png#lightbox)

Bieżące zapytanie to ten, na którym znajduje się kursor. Można zobaczyć, że pierwsze zapytanie jest wyróżnione, wskazując, że jest to bieżące zapytanie. Kliknij w dowolnym miejscu nowego zapytania, aby je zaznaczyć, a następnie kliknij przycisk **Uruchom** , aby go uruchomić.

[![Wykres wyników zapytania](media/log-analytics-tutorial/example-query-output-chart.png)](media/log-analytics-tutorial/example-query-output-chart.png#lightbox)

Zwróć uwagę, że dane wyjściowe są wykresem, a nie tabelą, jak ostatnim zapytaniem. Dzieje się tak dlatego, że przykładowe zapytanie używa polecenia [Render](/azure/data-explorer/kusto/query/renderoperator?pivots=azuremonitor) na końcu. Należy zauważyć, że istnieją różne opcje pracy z wykresem, takie jak zmiana na inny typ.

Spróbuj wybrać **wyniki** , aby wyświetlić dane wyjściowe zapytania jako tabelę. 

[![Tabela wyników zapytania](media/log-analytics-tutorial/example-query-output-table.png)](media/log-analytics-tutorial/example-query-output-table.png#lightbox)



## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak używać Log Analytics, Ukończ samouczek dotyczący korzystania z zapytań dzienników.
> [!div class="nextstepaction"]
> [Zapisz zapytania dziennika Azure Monitor](get-started-queries.md)
