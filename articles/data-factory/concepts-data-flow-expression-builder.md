---
title: Konstruktor wyrażeń w przepływie danych mapowania
description: Tworzenie wyrażeń przy użyciu Konstruktora wyrażeń podczas mapowania przepływów danych w usłudze Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 04/08/2020
ms.openlocfilehash: dda2812b5e2cc79d53658d568ba0845d593f41d6
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605375"
---
# <a name="build-expressions-in-mapping-data-flow"></a>Tworzenie wyrażeń w przepływie danych mapowania

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W mapowaniu przepływu danych wiele właściwości transformacji są wprowadzane jako wyrażenia. Wyrażenia te składają się z wartości kolumn, parametrów, funkcji, operatorów i literałów, które oceniają typ danych Platformy Spark w czasie wykonywania.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4tkur]

## <a name="open-expression-builder"></a>Otwórz Konstruktora wyrażeń

Interfejs edycji wyrażeń w usłudze Azure Data Factory środowisko użytkownika jest znany jako Konstruktor wyrażeń. Po wprowadzeniu logiki wyrażenia usługa Data Factory używa uzupełniania kodu [IntelliSense](https://docs.microsoft.com/visualstudio/ide/using-intellisense?view=vs-2019) do wyróżniania, sprawdzania składni i automatycznego uzupełniania.

![Konstruktor wyrażeń](media/data-flow/xpb1.png "Konstruktor wyrażeń")

W przekształceniach, takich jak kolumna pochodna i filtr, gdzie wyrażenia są obowiązkowe, otwórz Konstruktora wyrażeń, zaznaczając niebieskie pole wyrażeń.

![Niebieskie pole wyrażenia](media/data-flow/expressionbox.png "Konstruktor wyrażeń")

Podczas odwoływania się do kolumn w dopasowanym lub grupowym warunku wyrażenie może wyodrębnić wartości z kolumn. Aby utworzyć wyrażenie, wybierz **kolumnę Obliczona**.

![Opcja kolumny obliczane](media/data-flow/computedcolumn.png "Konstruktor wyrażeń")

W przypadkach, gdy wyrażenie lub wartość literału są prawidłowe dane wejściowe, wybierz **dodaj zawartość dynamiczną,** aby utworzyć wyrażenie, które ocenia wartość literału.

![Dodaj opcję zawartości dynamicznej](media/data-flow/add-dynamic-content.png "Konstruktor wyrażeń")

## <a name="expression-language-reference"></a>Odwołanie do języka wyrażenia

Mapowanie przepływów danych ma wbudowane funkcje i operatory, które mogą być używane w wyrażeniach. Aby uzyskać listę dostępnych funkcji, zobacz [Funkcje wyrażenia w przepływie danych mapowania](data-flow-expression-functions.md).

## <a name="column-names-with-special-characters"></a>Nazwy kolumn ze znakami specjalnymi

Jeśli masz nazwy kolumn, które zawierają znaki specjalne lub spacje, otaczają nazwę nawiasami klamrowymi, aby odwoływać się do nich w wyrażeniu.

```{[dbo].this_is my complex name$$$}```

## <a name="preview-expression-results"></a>Podgląd wyników wyrażenia

Jeśli [tryb debugowania](concepts-data-flow-debug-mode.md) jest włączony, można użyć klastra platformy Spark na żywo, aby wyświetlić podgląd w toku, co wyrażenie ocenia. Podczas tworzenia logiki można debugować wyrażenie w czasie rzeczywistym. 

![Podgląd w toku](media/data-flow/exp4b.png "Podgląd danych wyrażenia")

Wybierz **przycisk Odśwież,** aby zaktualizować wyniki wyrażenia względem próbki na żywo źródła.

![Przycisk Odśwież](media/data-flow/exp5.png "Podgląd danych wyrażenia")

## <a name="string-interpolation"></a>Interpolacja ciągów

Użyj cudzysłowu, aby ująć tekst dosłownego ciągu razem z wyrażeniami. Można dołączyć funkcje wyrażenia, kolumny i parametry. Interpolacja ciągów jest przydatna, aby uniknąć szerokiego użycia łączenia ciągów, gdy parametry są zawarte w ciągach zapytań. Aby użyć składni wyrażenia, ująć ją w nawiasy klamrowe,

Kilka przykładów interpolacji ciągów:

* ```"My favorite movie is {iif(instr(title,', The')>0,"The {split(title,', The')[1]}",title)}"```

* ```"select * from {$tablename} where orderyear > {$year}"```

* ```"Total cost with sales tax is {round(totalcost * 1.08,2)}"```

## <a name="comment-expressions"></a>Wyrażenia komentarza

Dodawanie komentarzy do wyrażeń przy użyciu składni komentarzy jednowierszowych i wielowierszowych.

![Składnia komentarzy jednowierszowych i wielowierszowych](media/data-flow/comments.png "Komentarze")

Poniższe przykłady są prawidłowymi komentarzami:

* ```/* This is my comment */```

* ```/* This is a```
*   ```multi-line comment */```
   
* ```// This is a single line comment```

Jeśli umieścisz komentarz u góry wyrażenia, pojawi się on w polu tekstowym transformacji, aby udokumentować wyrażenia transformacji.

![Komentarz w polu tekstowym transformacji](media/data-flow/comments2.png "Komentarze")

## <a name="regular-expressions"></a>Wyrażenia regularne

Wiele funkcji języka wyrażeń używa składni wyrażenia regularnego. W przypadku korzystania z funkcji wyrażenia regularnego Konstruktor\\wyrażeń próbuje zinterpretować ukośnik odwrotny ( ) jako sekwencję znaków ucieczki. W przypadku użycia ukośników odwrotnych w wyrażeniu regularnym należy zamknąć całe wyrażenie regularne w backticks (\`) lub użyć podwójnego ukośnika odwrotnego.

Przykład, który używa backticks:

```
regex_replace('100 and 200', `(\d+)`, 'digits')
```

Przykład, który używa podwójnych ukośników:

```
regex_replace('100 and 200', '(\\d+)', 'digits')
```

## <a name="address-array-indexes"></a>Indeksy tablic adresowych

W przypadku funkcji wyrażeń zwracających tablice należy używać nawiasów ([]) do adresowania określonych indeksów wewnątrz tych zwracanych obiektów tablicy. Tablica jest oparta na jedynki.

![Tablica Konstruktora wyrażeń](media/data-flow/expb2.png "Podgląd danych wyrażenia")

## <a name="keyboard-shortcuts"></a>Skróty klawiaturowe

* Ctrl+K Ctrl+C: Skomentuj cały wiersz.
* Ctrl+K Ctrl+U: Odkomentuj.
* F1: Podasz polecenia pomocy edytora.
* Klawisz alt+strzałka w dół: przesuń w dół bieżącą linię.
* Klawisz strzałki Alt+Up: przesuwaj bieżącą linię w górę.
* Ctrl+Spacja: pokaż pomoc kontekstową.

## <a name="convert-to-dates-or-timestamps"></a>Konwertowanie na daty lub sygnatury czasowe

Aby uwzględnić literały ciągu w danych wyjściowych ```toString()```sygnatury czasowej, zawiń konwersję w pliku .

```toString(toTimestamp('12/31/2016T00:12:00', 'MM/dd/yyyy\'T\'HH:mm:ss'), 'MM/dd /yyyy\'T\'HH:mm:ss')```

Aby przekonwertować milisekundy z epoki na `toTimestamp(<number of milliseconds>)`datę lub sygnaturę czasową, należy użyć programu . Jeśli czas nadchodzi w sekundach, pomnóż przez 1000.

```toTimestamp(1574127407*1000l)```

Końcowe "l" na końcu poprzedniego wyrażenia oznacza konwersję na długi typ jako składnię wbudowaną.

## <a name="next-steps"></a>Następne kroki

[Rozpoczynanie tworzenia wyrażeń przekształcania danych](data-flow-expression-functions.md)
