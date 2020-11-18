---
title: Rozwiązywanie problemów z mapowaniem przepływów danych
description: Dowiedz się, jak rozwiązywać problemy z przepływem danych w Azure Data Factory.
services: data-factory
ms.author: makromer
author: kromerm
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 09/11/2020
ms.openlocfilehash: 5f29474705919f402b1c114c3fd2df0df037cdae
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94696068"
---
# <a name="troubleshoot-mapping-data-flows-in-azure-data-factory"></a>Rozwiązywanie problemów z mapowaniem przepływów danych w Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

W tym artykule przedstawiono typowe metody rozwiązywania problemów związanych z mapowaniem przepływów danych w programie Azure Data Factory.

## <a name="common-errors-and-messages"></a>Typowe błędy i komunikaty

### <a name="error-code-df-executor-sourceinvalidpayload"></a>Kod błędu: DF-wykonawca-SourceInvalidPayload
- **Komunikat**: wykonywanie operacji podglądu danych, debugowania i przepływu danych potoku nie powiodło się, ponieważ kontener nie istnieje
- **Przyczyny**: gdy zestaw danych zawiera kontener, który nie istnieje w magazynie
- **Zalecenie**: Upewnij się, że kontener, do którego odwołuje się zestaw danych, istnieje lub jest dostępny.

### <a name="error-code-df-executor-systemimplicitcartesian"></a>Kod błędu: DF-wykonawca-SystemImplicitCartesian

- **Komunikat**: niejawny produkt kartezjańskiego dla sprzężenia wewnętrznego nie jest obsługiwany, należy zamiast tego użyć SPRZĘŻENIa krzyżowego. Kolumny używane w sprzężeniu powinny tworzyć unikatowe klucze dla wierszy.
- **Przyczyny**: niejawny produkt kartezjańskiego dla sprzężenia wewnętrznego między planami logicznymi nie jest obsługiwany. Jeśli kolumny używane w sprzężeniu tworzą unikatowy klucz, wymagana jest co najmniej jedna kolumna z obu stron relacji.
- **Zalecenie**: w przypadku sprzężeń opartych na nierówności należy wybrać opcję niestandardowego SPRZĘŻENIa krzyżowego.

### <a name="error-code-df-executor-systeminvalidjson"></a>Kod błędu: DF-wykonawca-SystemInvalidJson

- **Message**: błąd analizy JSON, nieobsługiwane kodowanie lub wiele wierszy
- **Przyczyny**: możliwe problemy dotyczące pliku JSON: nieobsługiwane kodowanie, uszkodzone bajty lub źródło JSON jako pojedynczy dokument w wielu zagnieżdżonych wierszach
- **Zalecenie**: Sprawdź, czy kodowanie pliku JSON jest obsługiwane. Na transformacji źródłowej, która używa zestawu danych JSON, rozwiń węzeł Ustawienia JSON i Włącz opcję "pojedynczy dokument".
 
### <a name="error-code-df-executor-broadcasttimeout"></a>Kod błędu: DF-wykonawca-BroadcastTimeout

- **Komunikat**: błąd limitu czasu sprzężenia emisji, upewnij się, że strumień emisji produkuje dane w ciągu 60 sekund podczas przebiegów debugowania i 300 s w uruchomieniach zadania
- **Przyczyny**: emisja ma domyślny limit czasu wynoszący 60 s podczas przebiegów debugowania i 300 sekund w przebiegach zadania. Strumień wybrany do emisji wydaje się zbyt duży, aby można było utworzyć dane w ramach tego limitu.
- **Zalecenie**: Sprawdź, czy na karcie Optymalizacja przekształceń przepływu danych jest włączona funkcja join, EXISTS i Lookup. Domyślną opcją emisji jest "Auto". W przypadku ustawienia opcji "Auto" lub ręcznego ustawiania lewej lub prawej strony do emisji w obszarze "Fixed" można ustawić większą konfigurację Azure Integration Runtime lub wyłączyć emisję. Zalecanym podejściem do uzyskania najlepszej wydajności w przepływach danych jest umożliwienie usłudze Spark emisji przy użyciu opcji "Auto" i użycia zoptymalizowanej pod kątem pamięci Azure IR.

Jeśli przepływ danych jest wykonywany w ramach wykonywania testu debugowania z przebiegu potoku debugowania, można wykonać ten warunek częściej. Jest to spowodowane tym, że usługa ADF ogranicza limit czasu emisji do 60 sekund, aby zapewnić szybsze debugowanie. Jeśli chcesz, aby przekroczyć limit czasu 300 sekund od uruchomienia wyzwalanego, możesz użyć opcji Debuguj > Użyj środowiska uruchomieniowego działania, aby użyć Azure IR zdefiniowanego w działaniu potoku przepływu danych.

### <a name="error-code-df-executor-conversion"></a>Kod błędu: DF-wykonawca-Conversion

- **Komunikat**: konwertowanie na datę lub godzinę nie powiodło się z powodu nieprawidłowego znaku
- **Przyczyny**: dane mają nieoczekiwany format
- **Zalecenie**: Użyj poprawnego typu danych

### <a name="error-code-df-executor-invalidcolumn"></a>Kod błędu: DF-wykonawca-InvalidColumn

- **Komunikat**: Nazwa kolumny musi być określona w zapytaniu, Ustawianie aliasu, jeśli używana jest funkcja SQL
- **Przyczyny**: nie określono nazwy kolumny
- **Zalecenie**: Ustaw alias, jeśli używana jest funkcja SQL, taka jak min ()/Max () itp.

 ### <a name="error-code-df-executor-drivererror"></a>Kod błędu: DF-wykonawca-DriverError
- **Komunikat**: INT96 jest starszym typem sygnatury czasowej, który nie jest obsługiwany przez ADF przepływu danych. Rozważ uaktualnienie typu kolumny do najnowszych typów.
- **Przyczyny**: błąd sterownika
- **Zalecenie**: INT96 jest starszym typem sygnatury czasowej, który nie jest obsługiwany przez ADF przepływu danych. Rozważ uaktualnienie typu kolumny do najnowszych typów.

 ### <a name="error-code-df-executor-blockcountexceedslimiterror"></a>Kod błędu: DF-wykonawca-BlockCountExceedsLimitError
- **Komunikat**: liczba niezatwierdzonych bloków nie może przekroczyć maksymalnego limitu 100 000 bloków. Sprawdź konfigurację obiektu BLOB.
- **Przyczyny**: Maksymalna liczba niezatwierdzonych bloków w obiekcie blob to 100 000.
- **Zalecenie**: skontaktuj się z zespołem ds. produktów firmy Microsoft w sprawie tego problemu, aby uzyskać więcej szczegółów

 ### <a name="error-code-df-executor-partitiondirectoryerror"></a>Kod błędu: DF-wykonawca-PartitionDirectoryError
- **Komunikat**: określona ścieżka źródłowa ma wiele katalogów partycjonowanych (np. <Source Path> /<katalogu głównego partycji 1>/a = 10/b = 20, <Source Path> /<partycji katalogu głównego 2>/c = 10/d = 30) lub partycjonowany katalog z innym plikiem lub katalogiem niepartycjonowanym (na przykład, <Source Path> <katalogu głównego partycji 1>/a = 10/b = 20, <Source Path> pliki/katalog 2/plik1), Usuń katalog główny partycji ze ścieżki źródłowej i przeczytaj go za pomocą oddzielnego przekształcenia źródła.
- **Przyczyny**: ścieżka źródłowa ma wiele katalogów partycjonowanych lub partycjonowany katalog z innym plikiem lub katalogiem niepartycjonowanym.
- **Zalecenie**: Usuń partycjonowany katalog główny ze ścieżki źródłowej i przeczytaj go przez oddzielną transformację źródłową.

 ### <a name="error-code-df-executor-outofmemoryerror"></a>Kod błędu: DF-wykonawca-OutOfMemoryError
- **Komunikat**: Wystąpił problem z pamięcią klastra podczas wykonywania, spróbuj ponownie użyć środowiska Integration Runtime z większą liczbą rdzeni i/lub typem obliczeń zoptymalizowanym pod kątem pamięci
- **Przyczyny**: za mało pamięci w klastrze
- **Zalecenie**: klastry debugowania są przeznaczone do celów deweloperskich. Wykorzystanie próbkowania danych, odpowiedniego typu obliczenia i rozmiaru do uruchomienia ładunku. Zapoznaj się z [przewodnikiem dotyczącym wydajności przepływu danych mapowania](concepts-data-flow-performance.md) dla dostrajania, aby osiągnąć najlepszą wydajność.

 ### <a name="error-code-df-executor-illegalargument"></a>Kod błędu: DF-wykonawca-illegalArgument
- **Komunikat**: Upewnij się, że klucz dostępu w połączonej usłudze jest poprawny
- **Przyczyny**: Nieprawidłowa nazwa konta lub klucz dostępu
- **Zalecenie**: Upewnij się, że nazwa konta lub klucz dostępu określony w połączonej usłudze jest poprawny. 

 ### <a name="error-code-df-executor-invalidtype"></a>Kod błędu: DF-wykonawc-nieprawidłowytype
- **Komunikat**: Upewnij się, że typ parametrów jest zgodny z przekazaną typem wartości. Przekazywanie parametrów zmiennoprzecinkowych z potoków nie jest obecnie obsługiwane.
- **Przyczyny**: niezgodne typy danych między zadeklarowanym typem a rzeczywistą wartością parametru
- **Zalecenie**: Sprawdź, czy wartości parametrów przekazane do przepływu danych pasują do zadeklarowanego typu.

 ### <a name="error-code-df-executor-columnunavailable"></a>Kod błędu: DF-wykonawca-ColumnUnavailable
- **Komunikat**: Nazwa kolumny używana w wyrażeniu jest niedostępna lub nieprawidłowa
- **Przyczyny**: nieprawidłowa lub niedostępna nazwa kolumny używana w wyrażeniach
- **Zalecenie**: sprawdzanie nazw kolumn używanych w wyrażeniach

 ### <a name="error-code-df-executor-parseerror"></a>Kod błędu: DF-wykonawca-ParseError
- **Komunikat**: nie można przeanalizować wyrażenia
- **Przyczyny**: wyrażenie ma błędy analizy z powodu formatowania
- **Zalecenie**: sprawdzanie formatowania w wyrażeniu

### <a name="error-code-getcommand-outputasync-failed"></a>Kod błędu: GetCommand OutputAsync nie powiodła się

- **Komunikat**: podczas debugowania przepływu danych i podglądu danych: GetCommand OutputAsync nie powiodła się z...
- **Przyczyny**: jest to błąd usługi zaplecza. Można ponowić próbę wykonania operacji, a także ponownie uruchomić sesję debugowania.
- **Zalecenie**: Jeśli ponowienie i ponowne uruchomienie nie rozwiążą problemu, skontaktuj się z pomocą techniczną.

### <a name="error-code-hit-unexpected-exception-and-execution-failed"></a>Kod błędu: Wystąpił nieoczekiwany wyjątek i wykonywanie nie powiodło się

- **Komunikat**: podczas wykonywania działania przepływu danych: Wystąpił nieoczekiwany wyjątek, a wykonywanie nie powiodło się.
- **Przyczyny**: jest to błąd usługi zaplecza. Można ponowić próbę wykonania operacji, a także ponownie uruchomić sesję debugowania.
- **Zalecenie**: Jeśli ponowienie i ponowne uruchomienie nie rozwiążą problemu, skontaktuj się z pomocą techniczną.

### <a name="error-code-debug-data-preview-no-output-data-on-join"></a>Kod błędu: dane debugowania nie są wyświetlane podczas przyłączania

- **Komunikat**: istnieje duża liczba wartości null lub brak wartości, które mogą być przyczyną zbyt małej liczby wierszy. Spróbuj zaktualizować limit wierszy debugowania i Odśwież dane.
- **Przyczyna**: warunek sprzężenia nie pasuje do żadnego z wierszy lub ma dużą liczbę wartości null podczas podglądu danych.
- **Zalecenie**: Przejdź do ustawień debugowania i Zwiększ liczbę wierszy w limicie wierszy źródłowych. Upewnij się, że wybrano Azure IR z wystarczającą ilością klastra przepływu danych do obsługi większej ilości danych.

### <a name="error-code-validation-error-at-source-with-multiline-csv-files"></a>Kod błędu: błąd walidacji w źródle z wielowierszowymi plikami CSV 

- **Komunikat**: może zostać wyświetlony jeden z następujących komunikatów o błędach:
   - Brak ostatniej kolumny lub ma ona wartość null.
   - Sprawdzanie poprawności schematu w źródle nie powiodło się.
   - Importowanie schematu nie powiodło się prawidłowo w interfejsie użytkownika, a w nazwie ostatniej kolumny znajduje się znak nowego wiersza.
- **Przyczyny**: w bloku danych mapowania obecnie, wielowierszowe Źródło CSV nie działa z \r\n jako ogranicznikiem wiersza. Czasami dodatkowe linie przy znaku powrotu karetki zwracają wartości źródłowe. 
- **Zalecenie**: wygeneruj plik w źródle przy użyciu \n jako ogranicznika wiersza zamiast \r\n. Lub Użyj działania kopiowania, aby przekonwertować plik CSV z \r\n na \n jako ogranicznik wiersza.

## <a name="general-troubleshooting-guidance"></a>Ogólne wskazówki dotyczące rozwiązywania problemów

1. Sprawdź stan połączeń zestawu danych. W każdej transformacji źródłowej i ujścia należy odwiedzić połączoną usługę dla każdego zestawu danych, którego używasz, i przetestować połączenia.
1. Sprawdź stan połączeń plików i tabel z projektanta przepływów danych. Aby upewnić się, że masz dostęp do danych, przejdź na Debugowanie i kliknij pozycję Podgląd danych w obszarze przekształcenia źródłowe.
1. Jeśli wszystko wygląda dobrze z wersji zapoznawczej, przejdź do projektanta potoku i umieść przepływ danych w działaniu potoku. Debuguj potok na potrzeby kompleksowego testu.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji dotyczących rozwiązywania problemów, wypróbuj następujące zasoby:
*  [Blog Data Factory](https://techcommunity.microsoft.com/t5/azure-data-factory/bg-p/AzureDataFactoryBlog)
*  [Żądania funkcji Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Wideo na platformie Azure](https://www.youtube.com/channel/UC2S0k7NeLcEm5_IhHUwpN0g/videos)
*  [Strona pytania&pytań i odpowiedzi](/answers/topics/azure-data-factory.html)
*  [Forum Stack Overflow dla Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informacje o usłudze Twitter dotyczące Data Factory](https://twitter.com/hashtag/DataFactory)
*  [Przewodnik dotyczący wydajności przepływów danych dla mapowania ADF](concepts-data-flow-performance.md)
