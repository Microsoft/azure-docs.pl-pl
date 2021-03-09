---
title: Rozwiązywanie problemów z mapowaniem przepływów danych
description: Dowiedz się, jak rozwiązywać problemy z przepływem danych w Azure Data Factory.
ms.author: makromer
author: kromerm
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 09/11/2020
ms.openlocfilehash: f8a852a8c4197169061a9c7633f4f363ad057337
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102505804"
---
# <a name="troubleshoot-mapping-data-flows-in-azure-data-factory"></a>Rozwiązywanie problemów z mapowaniem przepływów danych w Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

W tym artykule przedstawiono typowe metody rozwiązywania problemów związanych z mapowaniem przepływów danych w programie Azure Data Factory.

## <a name="common-error-codes-and-messages"></a>Typowe kody błędów i komunikaty 

### <a name="error-code-df-executor-sourceinvalidpayload"></a>Kod błędu: DF-wykonawca-SourceInvalidPayload
- **Komunikat**: wykonywanie operacji podglądu danych, debugowania i przepływu danych potoku nie powiodło się, ponieważ kontener nie istnieje
- **Przyczyna**: zestaw danych zawiera kontener, który nie istnieje w magazynie.
- **Zalecenie**: Upewnij się, że kontener, do którego odwołuje się zestaw danych, istnieje i czy można uzyskać do niego dostęp.

### <a name="error-code-df-executor-systemimplicitcartesian"></a>Kod błędu: DF-wykonawca-SystemImplicitCartesian

- **Komunikat**: niejawny produkt kartezjańskiego dla sprzężenia wewnętrznego nie jest obsługiwany, należy zamiast tego użyć SPRZĘŻENIa krzyżowego. Kolumny używane w sprzężeniu powinny tworzyć unikatowe klucze dla wierszy.
- **Przyczyna**: niejawne produkty kartezjańskiego dla sprzężeń wewnętrznych między planami logicznymi nie są obsługiwane. Jeśli używasz kolumn w sprzężeniu, Utwórz unikatowy klucz z co najmniej jedną kolumną z obu stron relacji.
- **Zalecenie**: dla sprzężeń opartych na nierównościch należy użyć niestandardowego SPRZĘŻENIa krzyżowego.

### <a name="error-code-df-executor-systeminvalidjson"></a>Kod błędu: DF-wykonawca-SystemInvalidJson

- **Message**: błąd analizy JSON, nieobsługiwane kodowanie lub wiele wierszy
- **Przyczyna**: możliwe problemy z plikiem JSON: nieobsługiwane kodowanie, uszkodzone bajty lub źródło JSON jako pojedynczy dokument w wielu zagnieżdżonych wierszach.
- **Zalecenie**: Sprawdź, czy kodowanie pliku JSON jest obsługiwane. Na transformacji źródłowej, która używa zestawu danych JSON, rozwiń pozycję **Ustawienia JSON** i Włącz **pojedynczy dokument**.
 
### <a name="error-code-df-executor-broadcasttimeout"></a>Kod błędu: DF-wykonawca-BroadcastTimeout

- **Komunikat**: błąd limitu czasu sprzężenia emisji, upewnij się, że strumień emisji produkuje dane w ciągu 60 sekund podczas przebiegów debugowania i 300 s w uruchomieniach zadania
- **Przyczyna**: emisja ma domyślny limit czasu wynoszący 60 sekund podczas przebiegów debugowania i 300 s w przebiegach zadania. Strumień wybrany do emisji jest zbyt duży, aby można było utworzyć dane w ramach tego limitu.
- **Zalecenie**: Sprawdź, czy na karcie **Optymalizacja** przekształceń przepływu danych jest włączona funkcja join, EXISTS i Lookup. Opcja domyślna **emisji jest taka** sama. Jeśli **ustawienie jest ustawione** na wartość autolub w przypadku ręcznego ustawiania lewej lub prawej strony do emisji przy użyciu opcji **stałe**, można ustawić większą konfigurację środowiska Azure Integration Runtime (IR) lub wyłączyć emisję. W celu uzyskania najlepszej wydajności przepływów danych zaleca się umożliwienie emisji platformy Spark **przy użyciu funkcji autoi używania** zoptymalizowanej pod kątem pamięci Azure IR. 
 
  Jeśli przepływ danych jest uruchamiany w ramach wykonywania testu debugowania z przebiegu potoku debugowania, można wykonać ten warunek częściej. Jest to spowodowane tym, że Azure Data Factory ogranicza limit czasu emisji do 60 sekund, aby zapewnić szybsze debugowanie. Limit czasu można zwiększyć na 300 sekund limitu czasu uruchomienia wyzwalanego. W tym celu można użyć opcji **Debuguj**  >  **działanie środowiska uruchomieniowego** , aby użyć Azure IR zdefiniowanego w działaniu potoku wykonywanie przepływu danych.

- **Komunikat**: błąd limitu czasu sprzężenia emisji, można wybrać opcję "off" emisji w transformacji Join/EXISTS/Lookup, aby uniknąć tego problemu. Jeśli zamierzasz emitować opcję sprzężenia w celu zwiększenia wydajności, upewnij się, że strumień emisji może generować dane w ciągu 60 sekund podczas przebiegów debugowania i 300 s w przebiegach zadań.
- **Przyczyna**: emisja ma domyślny limit czasu wynoszący 60 sekund podczas przebiegów debugowania i 300 sekund w uruchomionych zadaniach. W sprzężeniu emisji strumień wybrany do emisji jest zbyt duży, aby można było utworzyć dane w ramach tego limitu. Jeśli sprzężenie emisji nie jest używane, domyślna emisja według przepływu danych może osiągnąć ten sam limit.
- **Zalecenie**: Wyłącz opcję emisji lub Unikaj emisji dużych strumieni danych, dla których przetwarzanie może trwać ponad 60 sekund. Wybierz mniejszy strumień do emisji. Duże, Azure SQL Data Warehouse tabele i pliki źródłowe nie są zazwyczaj dobrymi opcjami. W przypadku braku sprzężenia emisji należy użyć większego klastra, jeśli wystąpi błąd.

### <a name="error-code-df-executor-conversion"></a>Kod błędu: DF-wykonawca-Conversion

- **Komunikat**: konwertowanie na datę lub godzinę nie powiodło się z powodu nieprawidłowego znaku
- **Przyczyna**: dane mają nieoczekiwany format.
- **Zalecenie**: Użyj poprawnego typu danych.

### <a name="error-code-df-executor-invalidcolumn"></a>Kod błędu: DF-wykonawca-InvalidColumn
- **Komunikat**: Nazwa kolumny musi być określona w zapytaniu, Ustawianie aliasu, jeśli używana jest funkcja SQL
- **Przyczyna**: nie określono nazwy kolumny.
- **Zalecenie**: Ustaw alias, jeśli używasz funkcji SQL, takiej jak min () lub Max ().

### <a name="error-code-df-executor-drivererror"></a>Kod błędu: DF-wykonawca-DriverError
- **Komunikat**: INT96 jest starszym typem sygnatury czasowej, który nie jest obsługiwany przez ADF przepływu danych. Rozważ uaktualnienie typu kolumny do najnowszych typów.
- **Przyczyna**: błąd sterownika.
- **Zalecenie**: INT96 jest starszym typem sygnatury czasowej, który nie jest obsługiwany przez Azure Data Factory przepływ danych. Rozważ uaktualnienie typu kolumny do najnowszego typu.

### <a name="error-code-df-executor-blockcountexceedslimiterror"></a>Kod błędu: DF-wykonawca-BlockCountExceedsLimitError
- **Komunikat**: liczba niezatwierdzonych bloków nie może przekroczyć maksymalnego limitu 100 000 bloków. Sprawdź konfigurację obiektu BLOB.
- **Przyczyna**: Maksymalna liczba niezatwierdzonych bloków w obiekcie blob to 100 000.
- **Zalecenie**: Aby uzyskać więcej informacji o tym problemie, skontaktuj się z zespołem ds. produktów firmy Microsoft.

### <a name="error-code-df-executor-partitiondirectoryerror"></a>Kod błędu: DF-wykonawca-PartitionDirectoryError
- **Komunikat**: określona ścieżka źródłowa ma wiele katalogów partycjonowanych (np. <Source Path> /<katalogu głównego partycji 1>/a = 10/b = 20, <Source Path> /<partycji katalogu głównego 2>/c = 10/d = 30) lub partycjonowany katalog z innym plikiem lub katalogiem niepartycjonowanym (na przykład, <Source Path> <katalogu głównego partycji 1>/a = 10/b = 20, <Source Path> pliki/katalog 2/plik1), Usuń katalog główny partycji ze ścieżki źródłowej i przeczytaj go za pomocą oddzielnego przekształcenia źródła.
- **Przyczyna**: ścieżka źródłowa zawiera wiele katalogów partycjonowanych lub katalog partycjonowany, który ma inny plik lub katalog niepodzielony na partycje.
- **Zalecenie**: Usuń partycjonowany katalog główny ze ścieżki źródłowej i przeczytaj go przez oddzielną transformację źródłową.

### <a name="error-code-df-executor-invalidtype"></a>Kod błędu: DF-wykonawc-nieprawidłowytype
- **Komunikat**: Upewnij się, że typ parametrów jest zgodny z przekazaną typem wartości. Przekazywanie parametrów zmiennoprzecinkowych z potoków nie jest obecnie obsługiwane.
- **Przyczyna**: typ danych dla zadeklarowanego typu nie jest zgodny z rzeczywistą wartością parametru.
- **Zalecenie**: Sprawdź, czy wartości parametrów przekazane do przepływu danych pasują do zadeklarowanego typu.

### <a name="error-code-df-executor-columnunavailable"></a>Kod błędu: DF-wykonawca-ColumnUnavailable
- **Komunikat**: Nazwa kolumny używana w wyrażeniu jest niedostępna lub nieprawidłowa
- **Przyczyna**: w wyrażeniu użyto nieprawidłowej nazwy kolumny lub jest ona niedostępna.
- **Zalecenie**: sprawdzanie nazw kolumn w wyrażeniach.

### <a name="error-code-df-executor-parseerror"></a>Kod błędu: DF-wykonawca-ParseError
- **Komunikat**: nie można przeanalizować wyrażenia
- **Przyczyna**: wyrażenie wygenerowało błędy analizy z powodu niepoprawnego formatowania.
- **Zalecenie**: Sprawdź formatowanie w wyrażeniu.

### <a name="error-code-df-executor-systemimplicitcartesian"></a>Kod błędu: DF-wykonawca-SystemImplicitCartesian
- **Komunikat**: niejawny produkt kartezjańskiego dla sprzężenia wewnętrznego nie jest obsługiwany, należy zamiast tego użyć SPRZĘŻENIa krzyżowego. Kolumny używane w sprzężeniu powinny tworzyć unikatowe klucze dla wierszy.
- **Przyczyna**: niejawne produkty kartezjańskiego dla sprzężeń wewnętrznych między planami logicznymi nie są obsługiwane. Jeśli używasz kolumn w sprzężeniu, Utwórz unikatowy klucz.
- **Zalecenie**: dla sprzężeń opartych na nierównościch należy użyć SPRZĘŻENIa krzyżowego.

### <a name="error-code-df-executor-systeminvalidjson"></a>Kod błędu: DF-wykonawca-SystemInvalidJson
- **Message**: błąd analizy JSON, nieobsługiwane kodowanie lub wiele wierszy
- **Przyczyna**: możliwe problemy z plikiem JSON: nieobsługiwane kodowanie, uszkodzone bajty lub źródło JSON jako pojedynczy dokument w wielu zagnieżdżonych wierszach.
- **Zalecenie**: Sprawdź, czy kodowanie pliku JSON jest obsługiwane. Na transformacji źródłowej, która używa zestawu danych JSON, rozwiń pozycję **Ustawienia JSON** i Włącz **pojedynczy dokument**.



### <a name="error-code-df-executor-conversion"></a>Kod błędu: DF-wykonawca-Conversion
- **Komunikat**: konwertowanie na datę lub godzinę nie powiodło się z powodu nieprawidłowego znaku
- **Przyczyna**: dane mają nieoczekiwany format.
- **Zalecenie**: Użyj poprawnego typu danych.


### <a name="error-code-df-executor-blockcountexceedslimiterror"></a>Kod błędu: DF-wykonawca-BlockCountExceedsLimitError
- **Komunikat**: liczba niezatwierdzonych bloków nie może przekroczyć maksymalnego limitu 100 000 bloków. Sprawdź konfigurację obiektu BLOB.
- **Przyczyna**: Maksymalna liczba niezatwierdzonych bloków w obiekcie blob to 100 000.
- **Zalecenie**: Aby uzyskać więcej informacji o tym problemie, skontaktuj się z zespołem ds. produktów firmy Microsoft.

### <a name="error-code-df-executor-partitiondirectoryerror"></a>Kod błędu: DF-wykonawca-PartitionDirectoryError
- **Komunikat**: określona ścieżka źródłowa ma wiele katalogów partycjonowanych (np. *<Source Path> /<katalogu głównego partycji 1>/a = 10/b = 20, <Source Path> /<partycji katalogu głównego 2>/c = 10/d = 30*) lub partycjonowany katalog z innym plikiem lub katalogiem niepartycjonowanym (np. *<Source Path> /<katalogu głównego partycji 1>/A = 10/b = 20, <Source Path> pliki/katalog 2/plik1*), Usuń katalog główny partycji ze ścieżki źródłowej i przeczytaj go przez oddzielną transformację źródłową.
- **Przyczyna**: ścieżka źródłowa zawiera wiele katalogów partycjonowanych lub katalog partycjonowany, który ma inny plik lub katalog niepodzielony na partycje. 
- **Zalecenie**: Usuń partycjonowany katalog główny ze ścieżki źródłowej i przeczytaj go przez oddzielną transformację źródłową.

### <a name="error-code-getcommand-outputasync-failed"></a>Kod błędu: GetCommand OutputAsync nie powiodła się
- **Komunikat**: podczas debugowania przepływu danych i podglądu danych: GetCommand OutputAsync nie powiodła się z...
- **Przyczyna**: ten błąd jest usługą zaplecza. 
- **Zalecenie**: ponów operację i ponownie uruchom sesję debugowania. Jeśli ponawianie próby i ponownego uruchomienia nie rozwiąże problemu, skontaktuj się z pomocą techniczną. 

### <a name="error-code-df-executor-outofmemoryerror"></a>Kod błędu: DF-wykonawca-OutOfMemoryError
 
- **Komunikat**: Wystąpił problem z pamięcią klastra podczas wykonywania, spróbuj ponownie użyć środowiska Integration Runtime z większą liczbą rdzeni i/lub typem obliczeń zoptymalizowanym pod kątem pamięci
- **Przyczyna**: za mało pamięci w klastrze.
- **Zalecenie**: klastry debugowania są przeznaczone do programowania. Użyj próbkowania danych i odpowiedniego typu obliczenia oraz rozmiaru do uruchomienia ładunku. Aby uzyskać porady dotyczące wydajności, zobacz [Przewodnik po wydajności przepływu danych](concepts-data-flow-performance.md).

### <a name="error-code-df-executor-illegalargument"></a>Kod błędu: DF-wykonawca-illegalArgument

- **Komunikat**: Upewnij się, że klucz dostępu w połączonej usłudze jest poprawny
- **Przyczyna**: nazwa konta lub klucz dostępu jest niepoprawny.
- **Zalecenie**: Upewnij się, że nazwa konta lub klucz dostępu określony w połączonej usłudze jest poprawny. 

### <a name="error-code-df-executor-invalidtype"></a>Kod błędu: DF-wykonawc-nieprawidłowytype
- **Komunikat**: Upewnij się, że typ parametrów jest zgodny z przekazaną typem wartości. Przekazywanie parametrów zmiennoprzecinkowych z potoków nie jest obecnie obsługiwane.
- **Przyczyna**: typ danych dla zadeklarowanego typu nie jest zgodny z rzeczywistą wartością parametru. 
- **Zalecenie**: Podaj prawidłowe typy danych.

### <a name="error-code-df-executor-columnunavailable"></a>Kod błędu: DF-wykonawca-ColumnUnavailable
- **Komunikat**: Nazwa kolumny używana w wyrażeniu jest niedostępna lub nieprawidłowa.
- **Przyczyna**: w wyrażeniu użyto nieprawidłowej nazwy kolumny lub jest ona niedostępna.
- **Zalecenie**: Sprawdź nazwy kolumn używane w wyrażeniach.


### <a name="error-code-df-executor-parseerror"></a>Kod błędu: DF-wykonawca-ParseError
- **Komunikat**: nie można przeanalizować wyrażenia.
- **Przyczyna**: wyrażenie wygenerowało błędy analizy z powodu niepoprawnego formatowania. 
- **Zalecenie**: Sprawdź formatowanie w wyrażeniu.


 ### <a name="error-code-df-executor-outofdiskspaceerror"></a>Kod błędu: DF-wykonawca-OutOfDiskSpaceError
- **Komunikat**: wewnętrzny błąd serwera
- **Przyczyna**: za mało miejsca na dysku w klastrze.
- **Zalecenie**: ponów próbę potoku. Jeśli to nie rozwiąże problemu, skontaktuj się z pomocą techniczną.


 ### <a name="error-code-df-executor-storeisnotdefined"></a>Kod błędu: DF-wykonawca-StoreIsNotDefined
- **Komunikat**: nie zdefiniowano konfiguracji magazynu. Ten błąd może być spowodowany przez nieprawidłowe przypisanie parametru w potoku.
- **Przyczyna**: nieokreślony.
- **Zalecenie**: Sprawdź przypisanie wartości parametrów w potoku. Wyrażenie parametru może zawierać nieprawidłowe znaki.

### <a name="error-code-df-excel-invalidconfiguration"></a>Kod błędu: DF-Excel-InvalidConfiguration
- **Komunikat**: Nazwa arkusza programu Excel lub indeks są wymagane.
- **Przyczyna**: nieokreślony.
- **Zalecenie**: Sprawdź wartość parametru. Określ nazwę lub indeks arkusza na potrzeby odczytywania danych programu Excel.

- **Komunikat**: Nazwa i indeks arkusza programu Excel nie mogą istnieć jednocześnie.
- **Przyczyna**: nieokreślony.
- **Zalecenie**: Sprawdź wartość parametru. Określ nazwę lub indeks arkusza na potrzeby odczytywania danych programu Excel.

- **Komunikat**: podano nieprawidłowy zakres.
- **Przyczyna**: nieokreślony.
- **Zalecenie**: Sprawdź wartość parametru. Określ prawidłowy zakres przez odwołanie. Aby uzyskać więcej informacji, zobacz [Właściwości programu Excel](./format-excel.md#dataset-properties).

- **Komunikat**: plik programu Excel jest nieprawidłowy, ale obsługiwane są tylko pliki xlsx i xls
- **Przyczyna**: nieokreślony.
- **Zalecenie**: Upewnij się, że rozszerzenie pliku programu Excel to xlsx lub xls.


 ### <a name="error-code-df-excel-invaliddata"></a>Kod błędu: DF-Excel-InvalidData
- **Komunikat**: arkusz programu Excel nie istnieje.
- **Przyczyna**: nieokreślony.
- **Zalecenie**: Sprawdź wartość parametru. Określ prawidłową nazwę lub indeks arkusza na potrzeby odczytywania danych programu Excel.

- **Komunikat**: odczytywanie plików programu Excel z innym schematem nie jest obecnie obsługiwane.
- **Przyczyna**: nieokreślony.
- **Zalecenie**: Użyj obsługiwanego pliku programu Excel.

- **Komunikat**: typ danych nie jest obsługiwany.
- **Przyczyna**: nieokreślony.
- **Zalecenie**: Użyj obsługiwanych typów danych plików programu Excel.

### <a name="error-code-4502"></a>Kod błędu: 4502
- **Komunikat**: istnieją znaczne współbieżne wykonania MappingDataflow, które powodują błędy spowodowane ograniczeniami w obszarze Integration Runtime.
- **Przyczyna**: duża liczba przebiegów aktywności przepływu danych jest wykonywana współbieżnie w środowisku Integration Runtime. Aby uzyskać więcej informacji, zobacz [limity Azure Data Factory](../azure-resource-manager/management/azure-subscription-service-limits.md#data-factory-limits).
- **Zalecenie**: Jeśli chcesz równolegle uruchamiać więcej działań przepływu danych, Rozpowszechnij je w wielu środowiskach Integration Runtime.


### <a name="error-code-invalidtemplate"></a>Kod błędu: InvalidTemplate
- **Komunikat**: nie można obliczyć wyrażenia potoku.
- **Przyczyna**: wyrażenie potoku przekazane w działaniu przepływu danych nie jest przetwarzane prawidłowo z powodu błędu składni.
- **Zalecenie**: Sprawdź działanie w obszarze Monitorowanie aktywności, aby zweryfikować wyrażenie.

### <a name="error-code-2011"></a>Kod błędu: 2011
- **Komunikat**: działanie zostało uruchomione na Azure Integration Runtime i nie powiodło się odszyfrowanie poświadczeń magazynu danych lub obliczeń połączonych za pośrednictwem samoobsługowego Integration Runtime. Sprawdź konfigurację połączonych usług skojarzonych z tym działaniem i upewnij się, że używasz odpowiedniego typu środowiska Integration Runtime.
- **Przyczyna**: przepływ danych nie obsługuje połączonych usług w ramach własnych środowisk Integration Runtime.
- **Zalecenie**: Konfigurowanie przepływu danych do uruchamiania w zarządzanym środowisku integration Runtime Virtual Network.

## <a name="miscellaneous-troubleshooting-tips"></a>Różne porady dotyczące rozwiązywania problemów
- **Problem**: Wystąpił nieoczekiwany wyjątek i wykonywanie nie powiodło się.
    - **Komunikat**: podczas wykonywania działania przepływu danych: Wystąpił nieoczekiwany wyjątek, a wykonywanie nie powiodło się.
    - **Przyczyna**: ten błąd jest usługą zaplecza. Ponów operację i ponownie uruchom sesję debugowania.
    - **Zalecenie**: Jeśli ponowienie próby i ponownego uruchomienia nie rozwiąże problemu, skontaktuj się z pomocą techniczną.

-  **Problem**: Brak danych wyjściowych podczas przyłączania podczas podglądu danych debugowania.
    - **Komunikat**: istnieje duża liczba wartości null lub brak wartości, które mogą być przyczyną zbyt małej liczby wierszy. Spróbuj zaktualizować limit wierszy debugowania i Odśwież dane.
    - **Przyczyna**: warunek sprzężenia nie pasuje do żadnego z wierszy ani nie ma dużej liczby wartości null w podglądzie danych.
    - **Zalecenie**: w **ustawieniach debugowania** Zwiększ liczbę wierszy w limicie wierszy źródłowych. Upewnij się, że wybrano Azure IR z klastrem przepływu danych, który jest wystarczająco duży, aby obsłużyć więcej danych.
    
- **Problem**: błąd walidacji w źródle z wielowierszowymi plikami CSV. 
    - **Komunikat**: może zostać wyświetlony jeden z następujących komunikatów o błędach:
        - Brak ostatniej kolumny lub ma ona wartość null.
        - Sprawdzanie poprawności schematu w źródle nie powiodło się.
        - Importowanie schematu nie powiodło się prawidłowo w interfejsie użytkownika, a w nazwie ostatniej kolumny znajduje się znak nowego wiersza.
    - **Przyczyna**: w przypadku przepływu danych mapowania wielowierszowe pliki źródłowe CSV nie działają obecnie, gdy \r\n jest używany jako ogranicznik wiersza. Czasami dodatkowe linie przy powrocie karetki mogą spowodować błędy. 
    - **Zalecenie**: wygeneruj plik w źródle przy użyciu \n jako ogranicznika wiersza zamiast \r\n. Lub Użyj działania kopiowania, aby przekonwertować plik CSV, tak aby używał \n jako ogranicznika wiersza.

## <a name="general-troubleshooting-guidance"></a>Ogólne wskazówki dotyczące rozwiązywania problemów
1. Sprawdź stan połączeń zestawu danych. W każdym transformację źródła i ujścia przejdź do połączonej usługi dla każdego zestawu danych, którego używasz, i przetestuj połączenia.
2. Sprawdź stan połączeń plików i tabel w Projektancie przepływu danych. W obszarze tryb debugowania wybierz pozycję **Podgląd danych** dla przekształceń źródłowych, aby upewnić się, że możesz uzyskać dostęp do danych.
3. Jeśli wszystko wygląda poprawnie w wersji zapoznawczej, przejdź do projektanta potoku i umieść przepływ danych w działaniu potoku. Debuguj potok na potrzeby kompleksowego testu.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać pomoc dotyczącą rozwiązywania problemów, zobacz następujące zasoby:

*  [Blog Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Żądania funkcji Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Wideo dotyczące platformy Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Forum Stack Overflow dla Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informacje o usłudze Twitter dotyczące Data Factory](https://twitter.com/hashtag/DataFactory)

