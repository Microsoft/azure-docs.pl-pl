---
title: Konstruktor wyrażeń w mapowaniu przepływu danych
description: Tworzenie wyrażeń przy użyciu programu Expression Builder w mapowaniu przepływów danych w Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/04/2021
ms.openlocfilehash: 753f201fbde5d9e7100b6e257f8dc79e4462d7b6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99584927"
---
# <a name="build-expressions-in-mapping-data-flow"></a>Tworzenie wyrażeń w mapowaniu przepływu danych

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W mapowaniu przepływu danych wiele właściwości transformacji jest wprowadzanych jako wyrażenia. Te wyrażenia składają się z wartości kolumn, parametrów, funkcji, operatorów i literałów, które są obliczane do typu danych Spark w czasie wykonywania. Mapowanie przepływów danych ma dedykowane środowisko w celu ułatwienia tworzenia tych wyrażeń nazywanych **konstruktorem wyrażeń**. Przy użyciu uzupełniania kodu  [IntelliSense](/visualstudio/ide/using-intellisense) do wyróżniania, sprawdzania składni i automatycznego uzupełniania, Konstruktor wyrażeń służy do łatwego tworzenia przepływów danych. W tym artykule wyjaśniono, jak efektywnie kompilować logikę biznesową za pomocą Konstruktora wyrażeń.

![Konstruktor wyrażeń](media/data-flow/expresion-builder.png "Konstruktor wyrażeń")

## <a name="open-expression-builder"></a>Otwórz konstruktora wyrażeń

Istnieje wiele punktów wejścia do otwarcia konstruktora wyrażeń. Są one zależne od określonego kontekstu transformacji przepływu danych. Najczęstszym przypadkiem użycia jest w transformacjach, takich jak [kolumna pochodna](data-flow-derived-column.md) i [agregowanie](data-flow-aggregate.md) , gdzie użytkownicy tworzą lub aktualizują kolumny przy użyciu języka wyrażeń przepływu danych. Konstruktora wyrażeń można otworzyć, wybierając pozycję **Otwórz Konstruktor wyrażeń** powyżej listy kolumn. Możesz również kliknąć kontekst kolumny i otworzyć Konstruktora wyrażeń bezpośrednio do tego wyrażenia.

![Otwórz Konstruktor wyrażeń pochodnych](media/data-flow/open-expression-builder-derive.png "Otwórz Konstruktor wyrażeń pochodnych")

W przypadku niektórych przekształceń, takich jak [Filter](data-flow-filter.md), kliknięcie pola tekstowego wyrażenie niebieskie spowoduje otwarcie konstruktora wyrażeń. 

![Niebieskie pole wyrażenia](media/data-flow/expressionbox.png "Niebieskie pole wyrażenia")

W przypadku odwoływania się do kolumn w zgodnym lub grupowym warunku wyrażenie może wyodrębnić wartości z kolumn. Aby utworzyć wyrażenie, wybierz **kolumnę obliczaną**.

![Opcja kolumny obliczanej](media/data-flow/computedcolumn.png "Opcja kolumny obliczanej")

W przypadkach, gdy wyrażenie lub wartość literału są prawidłowymi danymi wejściowymi, wybierz pozycję **Dodaj zawartość dynamiczną** , aby skompilować wyrażenie, którego wynikiem jest wartość literału.

![Dodaj opcję zawartości dynamicznej](media/data-flow/add-dynamic-content.png "Dodaj opcję zawartości dynamicznej")

## <a name="expression-elements"></a>Elementy wyrażenia

W mapowaniu przepływów danych wyrażenia mogą składać się z wartości kolumn, parametrów, funkcji, zmiennych lokalnych, operatorów i literałów. Te wyrażenia muszą być oceniane na typ danych Spark, takie jak String, Boolean lub Integer.

![Elementy wyrażenia](media/data-flow/expression-elements.png "Elementy wyrażenia")

### <a name="functions"></a>Funkcje

Mapowanie przepływów danych ma wbudowane funkcje i operatory, których można używać w wyrażeniach. Listę dostępnych funkcji można znaleźć w [dokumentacji dotyczącej mapowania języka przepływu danych](data-flow-expression-functions.md).

#### <a name="address-array-indexes"></a>Indeksy tablicy adresów

W przypadku kolumn lub funkcji zwracających typy tablicowe, użyj nawiasów kwadratowych ([]), aby uzyskać dostęp do określonego elementu. Jeśli indeks nie istnieje, wyrażenie zwróci wartość NULL.

![Tablica konstruktora wyrażeń](media/data-flow/expression-array.png "Podgląd danych wyrażeń")

> [!IMPORTANT]
> W mapowaniu przepływów danych tablice są oparte na jednym, co oznacza, że pierwszy element jest przywoływany przez indeks jeden. Na przykład, obiekt webarray [1] będzie miał dostęp do pierwszego elementu tablicy o nazwie "Moja Array".

### <a name="input-schema"></a>Schemat wejściowy

Jeśli przepływ danych używa zdefiniowanego schematu w dowolnym ze źródeł, można odwoływać się do kolumny według nazwy w wielu wyrażeniach. Jeśli używasz dryfowania schematu, możesz odwoływać się do kolumn jawnie przy użyciu `byName()` `byNames()` wzorców lub funkcji lub dopasowania przy użyciu kolumn.

#### <a name="column-names-with-special-characters"></a>Nazwy kolumn zawierające znaki specjalne

Jeśli masz nazwy kolumn, które zawierają znaki specjalne lub spacje, umieść je w nawiasach klamrowych, aby odwoływać się do nich w wyrażeniu.

```{[dbo].this_is my complex name$$$}```

### <a name="parameters"></a>Parametry

Parametry są wartościami, które są przekazane do przepływu danych w czasie wykonywania z potoku. Aby odwołać się do parametru, należy kliknąć parametr w widoku **elementów wyrażenia** lub odwołać się do niego przy użyciu znaku dolara przed jego nazwą. Na przykład parametr o nazwie parametr1 będzie przywoływany przez `$parameter1` . Aby dowiedzieć się więcej, zobacz [parametryzacjaing](parameters-data-flow.md)Flow Flows.

### <a name="cached-lookup"></a>Buforowane wyszukiwanie

Buforowane wyszukiwanie pozwala wykonać wbudowane wyszukiwanie danych wyjściowych w pamięci podręcznej. Dostępne są dwie funkcje do użycia w każdym ujścia `lookup()` i `outputs()` . Składnia do odwoływania się do tych funkcji to `cacheSinkName#functionName()` . Aby uzyskać więcej informacji, zobacz [ujścia pamięci podręcznej](data-flow-sink.md#cache-sink).

`lookup()` przyjmuje pasujące kolumny w bieżącej transformacji jako parametry i zwraca kolumnę złożoną równą wierszowi pasującemu do kolumn klucza w ujścia pamięci podręcznej. Zwracana kolumna złożona zawiera podkolumnę dla każdej kolumny mapowanej w ujścia pamięci podręcznej. Na przykład jeśli masz obiekt ujścia pamięci podręcznej kodu błędu `errorCodeCache` , który miał kolumnę klucza zgodną z kodem i kolumną o nazwie `Message` . Wywołanie `errorCodeCache#lookup(errorCode).Message` zwróci komunikat odpowiadający danemu przekazaniu. 

`outputs()` nie przyjmuje parametrów i zwraca cały ujścia pamięci podręcznej jako tablicę kolumn złożonych. Nie można wywołać tej metody, jeśli kolumny kluczy są określone w zlewie i powinny być używane tylko wtedy, gdy w ujściach pamięci podręcznej znajduje się niewielka liczba wierszy. Typowym przypadkiem użycia jest dołączenie maksymalnej wartości przyrostowego klucza. Jeśli buforowany pojedynczy wiersz `CacheMaxKey` zawiera kolumnę `MaxKey` , można odwołać się do pierwszej wartości, wywołując metodę `CacheMaxKey#outputs()[1].MaxKey` .

![Buforowane wyszukiwanie](media/data-flow/cached-lookup-example.png "Buforowane wyszukiwanie")

### <a name="locals"></a>Zmienne lokalne

Jeśli udostępniasz logikę w wielu kolumnach lub chcesz compartmentalize logikę, możesz utworzyć lokalną w ramach pochodnego column\. Aby odwołać się do lokalnego, należy kliknąć element lokalny w widoku **elementów wyrażenia** lub odwołać się do niego za pomocą dwukropka przed jego nazwą. Na przykład w przypadku lokalnego o nazwie local1 będzie przywoływany `:local1` . Dowiedz się więcej o ustawieniach lokalnych w [dokumentacji kolumny pochodnej](data-flow-derived-column.md#locals).

## <a name="preview-expression-results"></a>Podgląd wyników wyrażenia

Jeśli [tryb debugowania](concepts-data-flow-debug-mode.md) jest włączony, można interaktywnie używać klastra debugowania do wyświetlania podglądu, do czego służy wyrażenie. Wybierz pozycję **Odśwież** obok pozycji Podgląd danych, aby zaktualizować wyniki wersji zapoznawczej danych. Możesz zobaczyć dane wyjściowe każdego wiersza z kolumnami wejściowymi.

![Podgląd w toku](media/data-flow/preview-expression.png "Podgląd danych wyrażeń")

## <a name="string-interpolation"></a>Interpolacja ciągów

Podczas tworzenia długich ciągów, które używają elementów wyrażenia, użyj interpolacji ciągów, aby łatwo utworzyć złożoną logikę ciągów. Interpolacja ciągów pozwala uniknąć intensywnego użycia łączenia ciągów, gdy parametry są zawarte w ciągach zapytań. Użyj podwójnych cudzysłowów, aby ująć tekst ciągu literału z wyrażeniami. Można uwzględnić funkcje wyrażenia, kolumny i parametry. Aby użyć składni wyrażenia, należy ująć ją w nawiasy klamrowe,

Przykłady interpolacji ciągów:

* ```"My favorite movie is {iif(instr(title,', The')>0,"The {split(title,', The')[1]}",title)}"```

* ```"select * from {$tablename} where orderyear > {$year}"```

* ```"Total cost with sales tax is {round(totalcost * 1.08,2)}"```

* ```"{:playerName} is a {:playerRating} player"```

> [!NOTE]
> W przypadku używania składni interpolacji ciągów w zapytaniach źródła SQL ciąg zapytania musi znajdować się w jednym wierszu, bez "/n".

## <a name="commenting-expressions"></a>Komentowanie wyrażeń

Dodaj komentarze do wyrażeń, używając składni komentarzy jednowierszowych i w wielu wierszach.

Następujące przykłady są prawidłowymi komentarzami:

* ```/* This is my comment */```

* ```/* This is a```
* ```multi-line comment */```

Jeśli umieścisz komentarz w górnej części wyrażenia, pojawi się w polu tekstowym przekształcanie, aby udokumentować wyrażenia transformacji.

![Komentarz w polu tekstowym przekształcenia](media/data-flow/comment-expression.png "Komentarze")

## <a name="regular-expressions"></a>Wyrażenia regularne

Wiele funkcji języka wyrażeń używa składni wyrażeń regularnych. W przypadku korzystania z funkcji wyrażeń regularnych program Expression Builder próbuje zinterpretować ukośnik odwrotny ( \\ ) jako sekwencję znaku ucieczki. W przypadku używania ukośników odwrotnych w wyrażeniu regularnym należy ująć całe wyrażenie regularne w Takty ( \` ) lub użyć podwójnego ukośnika odwrotnego.

Przykład, który używa znaczników:

```
regex_replace('100 and 200', `(\d+)`, 'digits')
```

Przykład, który używa podwójnych ukośników:

```
regex_replace('100 and 200', '(\\d+)', 'digits')
```

## <a name="keyboard-shortcuts"></a>Skróty klawiaturowe

Poniżej znajduje się lista skrótów dostępnych w Konstruktorze wyrażeń. Większość skrótów IntelliSense jest dostępnych podczas tworzenia wyrażeń.

* CTRL + K Ctrl + C: komentarz cały wiersz.
* CTRL + K Ctrl + U: Usuń komentarz.
* F1: udostępnianie poleceń pomocy edytora.
* Alt + Strzałka w dół: Przenieś w dół bieżący wiersz.
* Alt + Strzałka w górę: Przenieś w górę bieżący wiersz.
* Ctrl + spacja: Pokaż Pomoc kontekstową.

## <a name="commonly-used-expressions"></a>Powszechnie używane wyrażenia

### <a name="convert-to-dates-or-timestamps"></a>Konwertuj na daty lub sygnatury czasowe

Aby uwzględnić literały ciągu w danych wyjściowych znacznika czasu, zawiń konwersję w ```toString()``` .

```toString(toTimestamp('12/31/2016T00:12:00', 'MM/dd/yyyy\'T\'HH:mm:ss'), 'MM/dd /yyyy\'T\'HH:mm:ss')```

Aby skonwertować milisekundy z epoki do daty lub sygnatury czasowej, użyj `toTimestamp(<number of milliseconds>)` . Jeśli czas przekroczy sekundy, pomnóż przez 1 000.

```toTimestamp(1574127407*1000l)```

Końcowe "l" na końcu poprzedniego wyrażenia oznacza konwersję do typu Long jako składnię wbudowaną.

### <a name="find-time-from-epoch-or-unix-time"></a>Znajdź czas od epoki lub czasu systemu UNIX

toLong (currentTimestamp ()-toTimestamp ("1970-01-01 00:00:00.000", "RRRR-MM-DD GG: mm: SS". SSS ")) * 1000

## <a name="next-steps"></a>Następne kroki

[Rozpocznij tworzenie wyrażeń transformacji danych](data-flow-expression-functions.md)
