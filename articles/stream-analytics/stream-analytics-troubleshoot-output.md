---
title: Rozwiązywanie problemów dotyczących danych wyjściowych Azure Stream Analytics
description: W tym artykule opisano techniki rozwiązywania problemów z połączeniami wyjściowymi w Azure Stream Analytics zadaniach.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: troubleshooting
ms.date: 10/05/2020
ms.custom: seodec18
ms.openlocfilehash: 02a3a7ad73bf0434a215c5ab7a6e89c299e9518b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98019860"
---
# <a name="troubleshoot-azure-stream-analytics-outputs"></a>Rozwiązywanie problemów dotyczących danych wyjściowych Azure Stream Analytics

W tym artykule opisano typowe problemy związane z Azure Stream Analytics połączeniami wyjściowymi oraz Rozwiązywanie problemów z danymi wyjściowymi. Wiele kroków rozwiązywania problemów wymaga włączenia zasobów i innych dzienników diagnostycznych dla zadania Stream Analytics. Jeśli nie masz włączonych dzienników zasobów, zobacz [Rozwiązywanie problemów Azure Stream Analytics przy użyciu dzienników zasobów](stream-analytics-job-diagnostic-logs.md).

## <a name="the-job-doesnt-produce-output"></a>Zadanie nie wygenerowało danych wyjściowych

1. Sprawdź łączność z danymi wyjściowymi przy użyciu przycisku **Testuj połączenie** dla każdego elementu wyjściowego.
1. Sprawdź [metryki monitorowania](stream-analytics-monitoring.md) na karcie **monitorowanie** . Ponieważ wartości są agregowane, metryki są opóźnione o kilka minut.

   * Jeśli wartość **zdarzeń wejściowych** jest większa od zera, zadanie może odczytać dane wejściowe. Jeśli wartość **zdarzeń wejściowych** nie jest większa od zera, wystąpił problem z danymi wejściowymi zadania. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z połączeniami wejściowymi](stream-analytics-troubleshoot-input.md) . Jeśli zadanie ma referencyjne dane wejściowe, Zastosuj podział według nazwy logicznej podczas wyszukiwania metryk **zdarzeń wejściowych** . Jeśli nie ma żadnych zdarzeń wejściowych z danych referencyjnych, prawdopodobnie oznacza to, że to źródło danych wejściowych nie zostało prawidłowo skonfigurowane do pobrania odpowiedniego zestawu danych referencyjnych.
   * Jeśli wartość **błędów konwersji danych** jest większa niż zero i wspinanie się, zobacz [Azure Stream Analytics błędy danych](data-errors.md) , aby uzyskać szczegółowe informacje na temat błędów konwersji danych.
   * Jeśli wartość **błędów środowiska uruchomieniowego** jest większa od zera, zadanie odbiera dane, ale generuje błędy podczas przetwarzania zapytania. Aby znaleźć błędy, przejdź do [dzienników inspekcji](../azure-resource-manager/management/view-activity-logs.md), a następnie odfiltruj w stanie **Niepowodzenie** .
   * Jeśli wartość **zdarzeń wejściowych** jest większa od zera, a wartość **zdarzeń wyjściowych** jest równa zero, jedna z następujących instrukcji jest prawdziwa:
      * Przetwarzanie zapytania spowodowało zero zdarzeń wyjściowych.
      * Zdarzenia lub pola mogą być źle sformułowane, co spowodowało wyjście zerowe po przetworzeniu zapytania.
      * Zadanie nie może wypchnąć danych do ujścia wyjściowego w celu uzyskania łączności lub przyczyny uwierzytelnienia.

   Komunikaty dziennika operacji objaśniają dodatkowe szczegóły, w tym to, co się dzieje, z wyjątkiem przypadków, gdy logika zapytań filtruje wszystkie zdarzenia. Jeśli przetwarzanie wielu zdarzeń generuje błędy, agregaty błędów są agregowane co 10 minut.

## <a name="the-first-output-is-delayed"></a>Pierwsze wyjście jest opóźnione

Po rozpoczęciu zadania Stream Analytics są odczytywane zdarzenia wejściowe. Jednak w pewnych okolicznościach może wystąpić opóźnienie w danych wyjściowych.

Duże wartości czasu w elementach zapytań czasowych mogą przyczynić się do opóźnienia danych wyjściowych. Aby generować poprawne dane wyjściowe w dużych oknach czasu, zadanie przesyłania strumieniowego odczytuje dane z najnowszego czasu możliwego do wypełnienia przedziału czasu. Dane mogą się składać z maksymalnie siedmiu dni. Żadne dane wyjściowe nie powstają do momentu odczytania oczekujących zdarzeń wejściowych. Ten problem może być narażony, gdy system uaktualnia zadania przesyłania strumieniowego. Po uaktualnieniu zadanie zostanie ponownie uruchomione. Takie uaktualnienia są zwykle wykonywane co kilka miesięcy.

Używaj uznania podczas projektowania zapytania Stream Analytics. Jeśli używasz dużego przedziału czasowego dla elementów czasowych w składni zapytania zadania, może to spowodować opóźnienie w pierwszym danych wyjściowych podczas uruchamiania lub ponownego uruchamiania zadania. Więcej niż kilka godzin, do siedmiu dni, jest uznawana za duże przedział czasu.

Jedno ograniczenie dla tego rodzaju pierwszego opóźnienia danych wyjściowych polega na użyciu technik przetwarzanie równoległe kwerend, takich jak Partycjonowanie danych. Można też dodać więcej jednostek przesyłania strumieniowego, aby zwiększyć przepływność do momentu wychwycenia zadania.  Aby uzyskać więcej informacji, zobacz [zagadnienia dotyczące tworzenia Stream Analytics zadań](stream-analytics-concepts-checkpoint-replay.md).

Czynniki te wpływają na oś czasu pierwszego wyjścia:

* Korzystanie z agregacji okienkowych, takich jak klauzula GROUP BY wirowania, przeskoku i ruchome okna:

  * W przypadku agregacji okna wirowania lub przeskoku wyniki są generowane na końcu przedziału czasu okna.
  * W przypadku przesuwanego okna wyniki są generowane, gdy zdarzenie przejdzie lub wyjdzie z przesuwanego okna.
  * Jeśli planujesz użyć dużego rozmiaru okna, takiego jak więcej niż jedna godzina, najlepiej wybrać przeskoku lub ruchome okno. Te typy okien pozwalają na wyświetlanie danych wyjściowych częściej.

* Użycie sprzężeń czasowych, takich jak JOIN with DATEDIFF:
  * Dopasowania generują się zaraz po nadejściu obu stron dopasowanego zdarzenia.
  * Dane, które nie są zgodne, takie jak lewe SPRZĘŻENIe zewnętrzne, są generowane na końcu okna DATEDIFF dla każdego zdarzenia po lewej stronie.

* Korzystanie z funkcji analizy danych CZASowych, takich jak isfirst, LAST i LAG, z LIMITem czasu trwania:
  * Dla funkcji analitycznych dane wyjściowe są generowane dla każdego zdarzenia. Nie ma opóźnień.

## <a name="the-output-falls-behind"></a>Dane wyjściowe wykraczają poza

Podczas normalnego działania zadania dane wyjściowe mogą mieć dłuższy i dłuższy okres opóźnienia. Jeśli dane wyjściowe są podobne do tego, można wskazać główne przyczyny, sprawdzając następujące czynniki:

* Czy obiekt sink podrzędny jest ograniczany
* Czy źródło nadrzędnego jest ograniczone
* Czy logika przetwarzania w zapytaniu jest intensywnie COMPUTE

Aby wyświetlić szczegóły danych wyjściowych, wybierz zadanie przesyłania strumieniowego w Azure Portal, a następnie wybierz pozycję **Diagram zadań**. Dla każdego elementu wejściowego istnieje Metryka zdarzenia zaległości na partycję. Jeśli Metryka ciągle rośnie, jest wskaźnikiem, że zasoby systemowe są ograniczone. Zwiększenie jest potencjalnie możliwe z powodu ograniczania przepływów danych wyjściowych lub wysokiego użycia procesora CPU. Aby uzyskać więcej informacji, zobacz [debugowanie oparte na danych przy użyciu diagramu zadań](stream-analytics-job-diagram-with-metrics.md).

## <a name="key-violation-warning-with-azure-sql-database-output"></a>Ostrzeżenie o naruszeniu klucza z danymi wyjściowymi Azure SQL Database

Podczas konfigurowania usługi Azure SQL Database jako danych wyjściowych do zadania Stream Analytics zbiorczo wstawia rekordy do tabeli docelowej. Ogólnie rzecz biorąc, Azure Stream Analytics gwarantuje dostarczenie do ujścia danych wyjściowych [co najmniej raz](/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics) . Nadal można wykonać [jednorazowe dostarczanie]( https://blogs.msdn.microsoft.com/streamanalytics/2017/01/13/how-to-achieve-exactly-once-delivery-for-sql-output/) do danych WYJŚCIOWYch SQL, gdy zdefiniowano unikatowe ograniczenie tabeli SQL.

W przypadku konfigurowania ograniczeń unique Key w tabeli SQL Azure Stream Analytics usuwa zduplikowane rekordy. Dzieli dane na partie i cyklicznie wstawia je do momentu znalezienia pojedynczego duplikatu rekordu. Proces Split i Insert ignoruje duplikaty pojedynczo. W przypadku zadania przesyłania strumieniowego, które ma wiele zduplikowanych wierszy, proces jest nieefektywny i czasochłonny. Jeśli w dzienniku aktywności zobaczysz wiele komunikatów ostrzegawczych naruszenia klucza dla poprzedniej godziny, prawdopodobnie dane wyjściowe SQL spowalniają całe zadanie.

Aby rozwiązać ten problem, należy [skonfigurować indeks]( https://docs.microsoft.com/sql/t-sql/statements/create-index-transact-sql) powodujący naruszenie klucza przez włączenie opcji IGNORE_DUP_KEY. Ta opcja umożliwia programowi SQL ignorowanie zduplikowanych wartości podczas operacji wstawiania zbiorczego. Azure SQL Database po prostu generuje komunikat ostrzegawczy, a nie błąd. W związku z tym Azure Stream Analytics nie tworzy już błędów naruszenia klucza podstawowego.

Podczas konfigurowania IGNORE_DUP_KEY dla kilku typów indeksów należy zwrócić uwagę na następujące uwagi:

* Nie można ustawić IGNORE_DUP_KEY dla klucza podstawowego lub ograniczenia UNIQUE, które używa instrukcji ALTER INDEX. Należy porzucić indeks i utworzyć go ponownie.  
* IGNORE_DUP_KEY można ustawić przy użyciu instrukcji ALTER INDEX dla unikatowego indeksu. To wystąpienie jest inne niż w przypadku ograniczenia PRIMARY KEY/UNIQUE i jest tworzone przy użyciu tworzenia indeksu lub definicji indeksu.  
* Opcja IGNORE_DUP_KEY nie dotyczy indeksów magazynu kolumn, ponieważ nie można wymusić jej unikatowości.

## <a name="sql-output-retry-logic"></a>Logika ponowień danych wyjściowych SQL

Gdy zadanie Stream Analytics z danymi wyjściowymi SQL otrzyma pierwszą partię zdarzeń, wystąpią następujące czynności:

1. Zadanie próbuje nawiązać połączenie z serwerem SQL.
2. Zadanie Pobiera schemat tabeli docelowej.
3. Zadanie sprawdza poprawność nazw kolumn i typów względem schematu tabeli docelowej.
4. Zadanie przygotowuje tabelę danych znajdującą się w pamięci z rekordów wyjściowych w partii.
5. Zadanie zapisuje tabelę danych do SQL przy użyciu [interfejsu API](/dotnet/api/system.data.sqlclient.sqlbulkcopy.writetoserver)docelowa elementu BulkCopy.

Podczas wykonywania tych kroków w danych wyjściowych SQL mogą wystąpić następujące typy błędów:

* [Błędy](../azure-sql/database/troubleshoot-common-errors-issues.md#transient-fault-error-messages-40197-40613-and-others) przejściowe, które są ponawiane przy użyciu strategii wycofywaniaego ponawiania prób. Minimalny interwał ponawiania prób zależy od danego kodu błędu, ale interwały są zwykle mniejsze niż 60 sekund. Górny limit może być co najwyżej pięć minut. 

   [Błędy logowania](../azure-sql/database/troubleshoot-common-errors-issues.md#unable-to-log-in-to-the-server-errors-18456-40531) i [problemy z zaporą](../azure-sql/database/troubleshoot-common-errors-issues.md#cannot-connect-to-server-due-to-firewall-issues) są ponawiane co najmniej 5 minut od poprzedniej próby i są ponawiane do momentu pomyślnego zakończenia.

* Błędy danych, takie jak błędy rzutowania i naruszenia ograniczeń schematu, są obsługiwane z użyciem zasad błędów danych wyjściowych. Te błędy są obsługiwane przez ponawianie próby dzielenia danych binarnych do momentu, aż pojedynczy rekord powodujący błąd jest obsługiwany przez pominięcie lub ponowienie próby. Podstawowe naruszenie ograniczenia klucza podstawowego jest [zawsze obsługiwane](./stream-analytics-troubleshoot-output.md#key-violation-warning-with-azure-sql-database-output).

* Błędy nieprzejściowe mogą wystąpić, gdy występują problemy z usługą SQL lub wady kodu wewnętrznego. Na przykład gdy błędy takie jak (kod 1132) Pula elastycznają swój limit magazynowania, ponawianie próby nie rozwiąże błędu. W tych scenariuszach zadanie Stream Analyticsu zwiększa [spadek wydajności](job-states.md).
* `BulkCopy` w kroku 5 mogą wystąpić limity czasu `BulkCopy` . `BulkCopy` może napotkać czas oczekiwania operacji. Domyślny minimalny skonfigurowany limit czasu wynosi pięć minut i jest dwukrotnie powtarzany.
Po upływie limitu czasu przekraczającego 15 minut, wartość maksymalnej wskazówki dotyczącej rozmiaru wsadu `BulkCopy` zostanie zredukowana do połowy do momentu pozostałego 100 zdarzeń na partię.

## <a name="column-names-are-lowercase-in-azure-stream-analytics-10"></a>Nazwy kolumn są małymi literami w Azure Stream Analytics (1,0)

W przypadku używania oryginalnego poziomu zgodności (1,0) Azure Stream Analytics zmienia nazwy kolumn na małe litery. Takie zachowanie zostało rozwiązane na późniejszych poziomach zgodności. Aby zachować sprawność, przejdź do poziomu zgodności 1,1 lub nowszego. Aby uzyskać więcej informacji, zobacz [poziom zgodności zadań Stream Analytics](./stream-analytics-compatibility-level.md).

## <a name="get-help"></a>Uzyskaj pomoc

Aby uzyskać dalszą pomoc, Wypróbuj naszą [stronę pytań firmy&Microsoft dotyczącą Azure Stream Analytics](/answers/topics/azure-stream-analytics.html).

## <a name="next-steps"></a>Następne kroki

* [Wprowadzenie do Azure Stream Analytics](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics (Rozpoczynanie pracy z usługą Azure Stream Analytics)](stream-analytics-real-time-fraud-detection.md)
* [Scale Azure Stream Analytics jobs (Skalowanie zadań usługi Azure Stream Analytics)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Dokumentacja języka zapytań](/stream-analytics-query/stream-analytics-query-language-reference)
* [Dokumentacja interfejsu API REST zarządzania Azure Stream Analytics](/rest/api/streamanalytics/)