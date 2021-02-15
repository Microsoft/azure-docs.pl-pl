---
title: Rozwiązywanie problemów z mapowaniem przepływów danych
description: Dowiedz się, jak rozwiązywać problemy z przepływem danych w Azure Data Factory.
ms.author: makromer
author: kromerm
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 09/11/2020
ms.openlocfilehash: a95cacafc5b1d00b1e4d04fd84cdda2de72b6a59
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/15/2021
ms.locfileid: "100523009"
---
# <a name="troubleshoot-mapping-data-flows-in-azure-data-factory"></a>Rozwiązywanie problemów z mapowaniem przepływów danych w Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

W tym artykule przedstawiono typowe metody rozwiązywania problemów związanych z mapowaniem przepływów danych w programie Azure Data Factory.

## <a name="common-error-codes-and-messages"></a>Typowe kody błędów i komunikaty 

### <a name="error-code-df-executor-sourceinvalidpayload"></a>Kod błędu: DF-wykonawca-SourceInvalidPayload
- **Komunikat**: wykonywanie operacji podglądu danych, debugowania i przepływu danych potoku nie powiodło się, ponieważ kontener nie istnieje
- **Przyczyny**: gdy zestaw danych zawiera kontener, który nie istnieje w magazynie
- **Zalecenie**: Upewnij się, że kontener, do którego odwołuje się zestaw danych, istnieje lub jest dostępny.

### <a name="error-code-df-executor-systemimplicitcartesian"></a>Kod błędu: DF-wykonawca-SystemImplicitCartesian

- **Komunikat**: niejawny produkt kartezjańskiego dla sprzężenia wewnętrznego nie jest obsługiwany, należy zamiast tego użyć SPRZĘŻENIa krzyżowego. Kolumny używane w sprzężeniu powinny tworzyć unikatowe klucze dla wierszy.
- **Przyczyny**: niejawny produkt kartezjańskiego dla sprzężenia wewnętrznego między planami logicznymi nie jest obsługiwany. Jeśli kolumny są używane w sprzężeniu, wymagana jest unikatowy klucz z co najmniej jedną kolumną z obu stron relacji.
- **Zalecenie**: w przypadku sprzężeń opartych na nierówności należy wybrać opcję niestandardowego SPRZĘŻENIa krzyżowego.

### <a name="error-code-df-executor-systeminvalidjson"></a>Kod błędu: DF-wykonawca-SystemInvalidJson

- **Message**: błąd analizy JSON, nieobsługiwane kodowanie lub wiele wierszy
- **Przyczyny**: możliwe problemy dotyczące pliku JSON: nieobsługiwane kodowanie, uszkodzone bajty lub źródło JSON jako pojedynczy dokument w wielu zagnieżdżonych wierszach
- **Zalecenie**: Sprawdź, czy kodowanie pliku JSON jest obsługiwane. Na transformacji źródłowej, która używa zestawu danych JSON, rozwiń węzeł Ustawienia JSON i Włącz opcję "pojedynczy dokument".
 
### <a name="error-code-df-executor-broadcasttimeout"></a>Kod błędu: DF-wykonawca-BroadcastTimeout

- **Komunikat**: błąd limitu czasu sprzężenia emisji, upewnij się, że strumień emisji produkuje dane w ciągu 60 sekund podczas przebiegów debugowania i 300 s w uruchomieniach zadania
- **Przyczyny**: emisja ma domyślny limit czasu wynoszący 60 s podczas przebiegów debugowania i 300 sekund w przebiegach zadania. Strumień wybrany do emisji wydaje się zbyt duży, aby można było utworzyć dane w ramach tego limitu.
- **Zalecenie**: Sprawdź, czy na karcie Optymalizacja przekształceń przepływu danych jest włączona funkcja join, EXISTS i Lookup. Domyślną opcją emisji jest "Auto". W przypadku ustawienia opcji "Auto" lub ręcznego ustawiania lewej lub prawej strony do emisji w obszarze "Fixed" można ustawić większą konfigurację Azure Integration Runtime lub wyłączyć emisję. Zalecanym podejściem do uzyskania najlepszej wydajności w przepływach danych jest umożliwienie usłudze Spark emisji przy użyciu opcji "Auto" i użycia zoptymalizowanej pod kątem pamięci Azure IR. Jeśli przepływ danych jest wykonywany w ramach wykonywania testu debugowania z przebiegu potoku debugowania, można wykonać ten warunek częściej. Jest to spowodowane tym, że usługa ADF ogranicza limit czasu emisji do 60 sekund, aby zapewnić szybsze debugowanie. Jeśli chcesz, aby przekroczyć limit czasu 300 sekund od uruchomienia wyzwalanego, możesz użyć opcji Debuguj > Użyj środowiska uruchomieniowego działania, aby użyć Azure IR zdefiniowanego w działaniu potoku przepływu danych.

- **Komunikat**: błąd limitu czasu sprzężenia emisji, można wybrać opcję "off" emisji w transformacji Join/EXISTS/Lookup, aby uniknąć tego problemu. Jeśli zamierzasz emitować opcję sprzężenia w celu zwiększenia wydajności, upewnij się, że strumień emisji może generować dane w ciągu 60 sekund podczas przebiegów debugowania i 300 s w przebiegach zadań.
- **Przyczyny**: emisja ma domyślny limit czasu wynoszący 60 s podczas przebiegów debugowania i 300 s w przebiegach zadania. W przypadku sprzężenia emisji strumień wybrany do emisji wydaje się zbyt duży, aby można było utworzyć dane w ramach tego limitu. Jeśli sprzężenie emisji nie jest używane, domyślna emisja wykonywana przez przepływu danych może osiągać ten sam limit
- **Zalecenie**: Wyłącz opcję emisji lub Unikaj rozgłaszania dużych strumieni danych, gdy przetwarzanie może trwać ponad 60 sekund. Wybierz mniejszy strumień do emisji. Duże tabele SQL/DW i pliki źródłowe są zwykle nieprawidłowymi kandydatami. W przypadku braku sprzężenia emisji należy użyć większego klastra, jeśli wystąpi błąd.

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

### <a name="error-code-df-executor-invalidtype"></a>Kod błędu: DF-wykonawc-nieprawidłowytype
- **Komunikat**: Upewnij się, że typ parametrów jest zgodny z przekazaną typem wartości. Przekazywanie parametrów zmiennoprzecinkowych z potoków nie jest obecnie obsługiwane.
- **Przyczyny**: niezgodne typy danych między zadeklarowanym typem a rzeczywistą wartością parametru
- **Zalecenie**: Sprawdź, czy wartości parametrów przekazane do przepływu danych pasują do zadeklarowanego typu.

### <a name="error-code-df-executor-columnunavailable"></a>Kod błędu: DF-wykonawca-ColumnUnavailable
- **Komunikat**: Nazwa kolumny używana w wyrażeniu jest niedostępna lub nieprawidłowa
- **Przyczyny**: nieprawidłowa lub niedostępna nazwa kolumny używana w wyrażeniach
- **Zalecenie**: Sprawdź nazwy kolumn używane w wyrażeniach

### <a name="error-code-df-executor-parseerror"></a>Kod błędu: DF-wykonawca-ParseError
- **Komunikat**: nie można przeanalizować wyrażenia
- **Przyczyny**: wyrażenie ma błędy analizy z powodu formatowania
- **Zalecenie**: Sprawdź formatowanie w wyrażeniu.

### <a name="error-code-df-executor-systemimplicitcartesian"></a>Kod błędu: DF-wykonawca-SystemImplicitCartesian
- **Komunikat**: niejawny produkt kartezjańskiego dla sprzężenia wewnętrznego nie jest obsługiwany, należy zamiast tego użyć SPRZĘŻENIa krzyżowego. Kolumny używane w sprzężeniu powinny tworzyć unikatowe klucze dla wierszy.
- **Przyczyny**: niejawny produkt kartezjańskiego dla sprzężenia wewnętrznego między planami logicznymi nie jest obsługiwany. Jeśli kolumny używane w sprzężeniu tworzą unikatowy klucz
- **Zalecenie**: w przypadku sprzężeń opartych na nierówności należy wybrać opcję SPRZĘŻENIa krzyżowego.

### <a name="error-code-df-executor-systeminvalidjson"></a>Kod błędu: DF-wykonawca-SystemInvalidJson
- **Message**: błąd analizy JSON, nieobsługiwane kodowanie lub wiele wierszy
- **Przyczyny**: możliwe problemy dotyczące pliku JSON: nieobsługiwane kodowanie, uszkodzone bajty lub źródło JSON jako pojedynczy dokument w wielu zagnieżdżonych wierszach
- **Zalecenie**: Sprawdź, czy kodowanie pliku JSON jest obsługiwane. Na transformacji źródłowej, która używa zestawu danych JSON, rozwiń węzeł Ustawienia JSON i Włącz opcję "pojedynczy dokument".



### <a name="error-code-df-executor-conversion"></a>Kod błędu: DF-wykonawca-Conversion
- **Komunikat**: konwertowanie na datę lub godzinę nie powiodło się z powodu nieprawidłowego znaku
- **Przyczyny**: dane mają nieoczekiwany format
- **Zalecenie**: Użyj poprawnego typu danych.


### <a name="error-code-df-executor-blockcountexceedslimiterror"></a>Kod błędu: DF-wykonawca-BlockCountExceedsLimitError
- **Komunikat**: liczba niezatwierdzonych bloków nie może przekroczyć maksymalnego limitu 100 000 bloków. Sprawdź konfigurację obiektu BLOB.
- **Przyczyny**: Maksymalna liczba niezatwierdzonych bloków w obiekcie blob to 100 000.
- **Zalecenie**: skontaktuj się z zespołem produktu firmy Microsoft w sprawie tego problemu, aby uzyskać więcej szczegółów

### <a name="error-code-df-executor-partitiondirectoryerror"></a>Kod błędu: DF-wykonawca-PartitionDirectoryError
- **Komunikat**: określona ścieżka źródłowa ma wiele katalogów partycjonowanych (np. *<Source Path> /<katalogu głównego partycji 1>/a = 10/b = 20, <Source Path> /<partycji katalogu głównego 2>/c = 10/d = 30*) lub partycjonowany katalog z innym plikiem lub katalogiem niepartycjonowanym (np. *<Source Path> /<katalogu głównego partycji 1>/A = 10/b = 20, <Source Path> pliki/katalog 2/plik1*), Usuń katalog główny partycji ze ścieżki źródłowej i przeczytaj go przez oddzielną transformację źródłową.
- **Przyczyny**: ścieżka źródłowa ma wiele katalogów partycjonowanych lub partycjonowany katalog z innym plikiem lub katalogiem niepartycjonowanym.
- **Zalecenie**: Usuń partycjonowany katalog główny ze ścieżki źródłowej i przeczytaj go przez oddzielną transformację źródłową.

### <a name="error-code-getcommand-outputasync-failed"></a>Kod błędu: GetCommand OutputAsync nie powiodła się
- **Komunikat**: podczas debugowania przepływu danych i podglądu danych: GetCommand OutputAsync nie powiodła się z...
- **Przyczyny**: jest to błąd usługi zaplecza. Można ponowić próbę wykonania operacji, a także ponownie uruchomić sesję debugowania.
- **Zalecenie**: Jeśli ponowienie i ponowne uruchomienie nie rozwiążą problemu, skontaktuj się z pomocą techniczną. 

### <a name="error-code-df-executor-outofmemoryerror"></a>Kod błędu: DF-wykonawca-OutOfMemoryError
 
- **Komunikat**: Wystąpił problem z pamięcią klastra podczas wykonywania, spróbuj ponownie użyć środowiska Integration Runtime z większą liczbą rdzeni i/lub typem obliczeń zoptymalizowanym pod kątem pamięci
- **Przyczyny**: za mało pamięci w klastrze.
- **Zalecenie**: klastry debugowania są przeznaczone do celów deweloperskich. Skorzystaj z próbkowania danych odpowiedni typ i rozmiar obliczeń, aby uruchomić ładunek. Zapoznaj się z [przewodnikiem dotyczącym wydajności przepływu danych](https://docs.microsoft.com/azure/data-factory/concepts-data-flow-performance) , aby dostroić przepływy pracy w celu uzyskania najlepszej wydajności.

### <a name="error-code-df-executor-illegalargument"></a>Kod błędu: DF-wykonawca-illegalArgument
- **Komunikat**: Upewnij się, że klucz dostępu w połączonej usłudze jest poprawny.
- **Przyczyny**: nazwa konta lub klucz dostępu jest niepoprawny.
- **Zalecenie**: Podaj prawidłową nazwę konta lub klucz dostępu.

- **Komunikat**: Upewnij się, że klucz dostępu w połączonej usłudze jest poprawny
- **Przyczyny**: Nieprawidłowa nazwa konta lub klucz dostępu
- **Zalecenie**: Upewnij się, że nazwa konta lub klucz dostępu określony w połączonej usłudze jest poprawny. 

### <a name="error-code-df-executor-invalidtype"></a>Kod błędu: DF-wykonawc-nieprawidłowytype
- **Komunikat**: Upewnij się, że typ parametrów jest zgodny z przekazaną typem wartości. Przekazywanie parametrów zmiennoprzecinkowych z potoków nie jest obecnie obsługiwane.
- **Przyczyny**: niezgodne typy danych między zadeklarowanym typem a rzeczywistą wartością parametru
- **Zalecenie**: Podaj prawidłowe typy danych.

### <a name="error-code-df-executor-columnunavailable"></a>Kod błędu: DF-wykonawca-ColumnUnavailable
- **Komunikat**: Nazwa kolumny używana w wyrażeniu jest niedostępna lub nieprawidłowa.
- **Przyczyny**: nieprawidłowa lub niedostępna nazwa kolumny jest używana w wyrażeniach.
- **Zalecenie**: Sprawdź nazwy kolumn używane w wyrażeniach.


### <a name="error-code-df-executor-parseerror"></a>Kod błędu: DF-wykonawca-ParseError
- **Komunikat**: nie można przeanalizować wyrażenia.
- **Przyczyny**: w wyrażeniu występują błędy analizy spowodowane formatowaniem.
- **Zalecenie**: Sprawdź formatowanie w wyrażeniu.


 ### <a name="error-code-df-executor-outofdiskspaceerror"></a>Kod błędu: DF-wykonawca-OutOfDiskSpaceError
- **Komunikat**: wewnętrzny błąd serwera
- **Przyczyny**: w klastrze kończy się miejsce na dysku.
- **Zalecenie**: spróbuj ponownie potoku. Jeśli problem będzie się powtarzać, skontaktuj się z pomocą techniczną.


 ### <a name="error-code-df-executor-storeisnotdefined"></a>Kod błędu: DF-wykonawca-StoreIsNotDefined
- **Komunikat**: nie zdefiniowano konfiguracji magazynu. Ten błąd może być spowodowany przez nieprawidłowe przypisanie parametru w potoku.
- **Przyczyny**: nieokreślony
- **Zalecenie**: Sprawdź przypisanie wartości parametrów w potoku. Wyrażenie parametru może zawierać nieprawidłowe znaki.

### <a name="error-code-df-excel-invalidconfiguration"></a>Kod błędu: DF-Excel-InvalidConfiguration
- **Komunikat**: Nazwa arkusza programu Excel lub indeks są wymagane.
- **Przyczyny**: nieokreślony
- **Zalecenie**: Sprawdź wartość parametru i określ nazwę arkusza lub indeks, aby odczytać dane programu Excel.

- **Komunikat**: Nazwa i indeks arkusza programu Excel nie mogą istnieć jednocześnie.
- **Przyczyny**: nieokreślony
- **Zalecenie**: Sprawdź wartość parametru i określ nazwę arkusza lub indeks, aby odczytać dane programu Excel.

- **Komunikat**: podano nieprawidłowy zakres.
- **Przyczyny**: nieokreślony
- **Zalecenie**: Sprawdź wartość parametru i określ prawidłowy zakres przez odwołanie: [Właściwości programu Excel](https://docs.microsoft.com/azure/data-factory/format-excel#dataset-properties).

- **Komunikat**: plik programu Excel jest nieprawidłowy, ale obsługiwane są tylko pliki xlsx i xls
- **Przyczyny**: nieokreślony
- **Zalecenie**: Upewnij się, że rozszerzenie pliku programu Excel to xlsx lub xls.


 ### <a name="error-code-df-excel-invaliddata"></a>Kod błędu: DF-Excel-InvalidData
- **Komunikat**: arkusz programu Excel nie istnieje.
- **Przyczyny**: nieokreślony
- **Zalecenie**: Sprawdź wartość parametru i określ prawidłową nazwę arkusza lub indeks, aby odczytać dane programu Excel.

- **Komunikat**: odczytywanie plików programu Excel z innym schematem nie jest obecnie obsługiwane.
- **Przyczyny**: nieokreślony
- **Zalecenie**: Użyj poprawnego pliku programu Excel.

- **Komunikat**: typ danych nie jest obsługiwany.
- **Przyczyny**: nieokreślony
- **Zalecenie**: Użyj odpowiednich typów danych w pliku programu Excel.

### <a name="error-code-4502"></a>Kod błędu: 4502
- **Komunikat**: istnieją znaczne współbieżne wykonania MappingDataflow, które powodują błędy spowodowane ograniczeniami w obszarze Integration Runtime.
- **Przyczyny**: wiele przebiegów działania przepływu danych odbywa się współbieżnie na Integration Runtime. Dowiedz się więcej na temat [limitów Azure Data Factory](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#data-factory-limits).
- **Zalecenie**: w przypadku, gdy chcesz równolegle uruchamiać więcej działań przepływu danych, Rozpowszechnij je w wielu środowiskach Integration Runtime.


### <a name="error-code-invalidtemplate"></a>Kod błędu: InvalidTemplate
- **Komunikat**: nie można obliczyć wyrażenia potoku.
- **Przyczyny**: wyrażenie potoku przesłane w działaniu przepływu danych nie jest przetwarzane poprawnie z powodu błędu składni.
- **Zalecenie**: Sprawdź działanie w obszarze Monitorowanie aktywności, aby zweryfikować wyrażenie.

### <a name="error-code-2011"></a>Kod błędu: 2011
- **Komunikat**: działanie zostało uruchomione na Azure Integration Runtime i nie powiodło się odszyfrowanie poświadczeń magazynu danych lub obliczeń połączonych za pośrednictwem samoobsługowego Integration Runtime. Sprawdź konfigurację połączonych usług skojarzonych z tym działaniem i upewnij się, że używasz odpowiedniego typu środowiska Integration Runtime.
- **Przyczyny**: przepływ danych nie obsługuje połączonych usług przy użyciu własnego środowiska Integration Runtime.
- **Zalecenie**: Skonfiguruj przepływ danych do uruchamiania w środowisku Integration Runtime przy użyciu "Managed Virtual Network".

## <a name="miscellaneous-troubleshooting-tips"></a>Różne porady dotyczące rozwiązywania problemów
- **Problem**: Wystąpił nieoczekiwany wyjątek i wykonywanie nie powiodło się
    - **Komunikat**: podczas wykonywania działania przepływu danych: Wystąpił nieoczekiwany wyjątek, a wykonywanie nie powiodło się.
    - **Przyczyny**: jest to błąd usługi zaplecza. Można ponowić próbę wykonania operacji, a także ponownie uruchomić sesję debugowania.
    - **Zalecenie**: Jeśli ponowienie i ponowne uruchomienie nie rozwiążą problemu, skontaktuj się z pomocą techniczną.

-  **Problem**: dane debugowania nie są wyświetlane podczas przyłączania
    - **Komunikat**: istnieje duża liczba wartości null lub brak wartości, które mogą być przyczyną zbyt małej liczby wierszy. Spróbuj zaktualizować limit wierszy debugowania i Odśwież dane.
    - **Przyczyna**: warunek sprzężenia nie pasuje do żadnego z wierszy lub ma dużą liczbę wartości null podczas podglądu danych.
    - **Zalecenie**: Przejdź do ustawień debugowania i Zwiększ liczbę wierszy w limicie wierszy źródłowych. Upewnij się, że wybrano Azure IR z wystarczającą ilością klastra przepływu danych do obsługi większej ilości danych.
    
- **Problem**: błąd walidacji w źródle z wielowierszowymi plikami CSV 
    - **Komunikat**: może zostać wyświetlony jeden z następujących komunikatów o błędach:
        - Brak ostatniej kolumny lub ma ona wartość null.
        - Sprawdzanie poprawności schematu w źródle nie powiodło się.
        - Importowanie schematu nie powiodło się prawidłowo w interfejsie użytkownika, a w nazwie ostatniej kolumny znajduje się znak nowego wiersza.
    - **Przyczyny**: w bloku danych mapowania obecnie, wielowierszowe Źródło CSV nie działa z \r\n jako ogranicznikiem wiersza. Czasami dodatkowe linie przy znaku powrotu karetki zwracają wartości źródłowe. 
    - **Zalecenie**: wygeneruj plik w źródle przy użyciu \n jako ogranicznika wiersza zamiast \r\n. Lub Użyj działania kopiowania, aby przekonwertować plik CSV z \r\n na \n jako ogranicznik wiersza.

## <a name="general-troubleshooting-guidance"></a>Ogólne wskazówki dotyczące rozwiązywania problemów
1. Sprawdź stan połączeń zestawu danych. W każdej transformacji źródłowej i ujścia należy odwiedzić połączoną usługę dla każdego zestawu danych, którego używasz, i przetestować połączenia.
2. Sprawdź stan połączeń plików i tabel z projektanta przepływów danych. Aby upewnić się, że masz dostęp do danych, przejdź na Debugowanie i kliknij pozycję Podgląd danych w obszarze przekształcenia źródłowe.
3. Jeśli wszystko wygląda dobrze z wersji zapoznawczej, przejdź do projektanta potoku i umieść przepływ danych w działaniu potoku. Debuguj potok na potrzeby kompleksowego testu.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać pomoc dotyczącą rozwiązywania problemów, wypróbuj następujące zasoby:

*  [Blog Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Żądania funkcji Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Wideo dotyczące platformy Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Forum przepełnienia stosu dla Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informacje o usłudze Twitter dotyczące Data Factory](https://twitter.com/hashtag/DataFactory)
