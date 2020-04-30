---
title: Konstruktor wyrażeń w mapowaniu przepływu danych
description: Tworzenie wyrażeń przy użyciu programu Expression Builder w mapowaniu przepływów danych w Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 04/08/2020
ms.openlocfilehash: dda2812b5e2cc79d53658d568ba0845d593f41d6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81605375"
---
# <a name="build-expressions-in-mapping-data-flow"></a>Tworzenie wyrażeń w mapowaniu przepływu danych

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W mapowaniu przepływu danych wiele właściwości transformacji jest wprowadzanych jako wyrażenia. Te wyrażenia składają się z wartości kolumn, parametrów, funkcji, operatorów i literałów, które są obliczane do typu danych Spark w czasie wykonywania.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4tkur]

## <a name="open-expression-builder"></a>Otwórz konstruktora wyrażeń

Interfejs edytowania wyrażeń w środowisku użytkownika Azure Data Factory jest znany jako Konstruktor wyrażeń. Podczas wprowadzania logiki wyrażeń, Data Factory używa uzupełniania kodu [IntelliSense](https://docs.microsoft.com/visualstudio/ide/using-intellisense?view=vs-2019) do wyróżniania, sprawdzania składni i automatycznego uzupełniania.

![Konstruktor wyrażeń](media/data-flow/xpb1.png "Konstruktor wyrażeń")

W transformacjach, takich jak kolumna pochodna i filtr, gdzie wyrażenia są obowiązkowe, Otwórz program Expression Builder, zaznaczając niebieskie pole wyrażenia.

![Niebieskie pole wyrażenia](media/data-flow/expressionbox.png "Konstruktor wyrażeń")

W przypadku odwoływania się do kolumn w zgodnym lub grupowym warunku wyrażenie może wyodrębnić wartości z kolumn. Aby utworzyć wyrażenie, wybierz **kolumnę obliczaną**.

![Opcja kolumny obliczanej](media/data-flow/computedcolumn.png "Konstruktor wyrażeń")

W przypadkach, gdy wyrażenie lub wartość literału są prawidłowymi danymi wejściowymi, wybierz pozycję **Dodaj zawartość dynamiczną** , aby skompilować wyrażenie, którego wynikiem jest wartość literału.

![Dodaj opcję zawartości dynamicznej](media/data-flow/add-dynamic-content.png "Konstruktor wyrażeń")

## <a name="expression-language-reference"></a>Dokumentacja języka wyrażeń

Mapowanie przepływów danych ma wbudowane funkcje i operatory, których można używać w wyrażeniach. Aby uzyskać listę dostępnych funkcji, zobacz [funkcje wyrażeń w przepływie danych mapowania](data-flow-expression-functions.md).

## <a name="column-names-with-special-characters"></a>Nazwy kolumn zawierające znaki specjalne

Jeśli masz nazwy kolumn, które zawierają znaki specjalne lub spacje, umieść je w nawiasach klamrowych, aby odwoływać się do nich w wyrażeniu.

```{[dbo].this_is my complex name$$$}```

## <a name="preview-expression-results"></a>Podgląd wyników wyrażenia

Jeśli [tryb debugowania](concepts-data-flow-debug-mode.md) jest włączony, można użyć klastra Spark na żywo, aby zobaczyć w toku podgląd tego, co daje wyrażenie. Podczas kompilowania logiki można debugować wyrażenie w czasie rzeczywistym. 

![Podgląd w toku](media/data-flow/exp4b.png "Podgląd danych wyrażeń")

Wybierz pozycję **Odśwież** , aby zaktualizować wyniki wyrażenia do próbki na żywo źródłowej.

![Przycisk Odśwież](media/data-flow/exp5.png "Podgląd danych wyrażeń")

## <a name="string-interpolation"></a>Interpolacja ciągów

Użyj znaków cudzysłowu, aby ująć tekst ciągu literału razem z wyrażeniami. Można uwzględnić funkcje wyrażenia, kolumny i parametry. Interpolacja ciągów jest przydatna, aby uniknąć rozległego użycia łączenia ciągów, gdy parametry są zawarte w ciągach zapytań. Aby użyć składni wyrażenia, należy ująć ją w nawiasy klamrowe,

Przykłady interpolacji ciągów:

* ```"My favorite movie is {iif(instr(title,', The')>0,"The {split(title,', The')[1]}",title)}"```

* ```"select * from {$tablename} where orderyear > {$year}"```

* ```"Total cost with sales tax is {round(totalcost * 1.08,2)}"```

## <a name="comment-expressions"></a>Wyrażenia komentarzy

Dodaj komentarze do wyrażeń, używając składni komentarzy jednowierszowych i w wielu wierszach.

![Składnia komentarzy jednowierszowych i wielowierszowych](media/data-flow/comments.png "Komentarze")

Następujące przykłady są prawidłowymi komentarzami:

* ```/* This is my comment */```

* ```/* This is a```
*   ```multi-line comment */```
   
* ```// This is a single line comment```

Jeśli umieścisz komentarz w górnej części wyrażenia, pojawi się w polu tekstowym przekształcanie, aby udokumentować wyrażenia transformacji.

![Komentarz w polu tekstowym przekształcenia](media/data-flow/comments2.png "Komentarze")

## <a name="regular-expressions"></a>Wyrażenia regularne

Wiele funkcji języka wyrażeń używa składni wyrażeń regularnych. W przypadku korzystania z funkcji wyrażeń regularnych program Expression Builder próbuje zinterpretować ukośnik odwrotny (\\) jako sekwencję znaku ucieczki. W przypadku używania ukośników odwrotnych w wyrażeniu regularnym należy ująć całe wyrażenie regularne w Takty (\`) lub użyć podwójnego ukośnika odwrotnego.

Przykład, który używa znaczników:

```
regex_replace('100 and 200', `(\d+)`, 'digits')
```

Przykład, który używa podwójnych ukośników:

```
regex_replace('100 and 200', '(\\d+)', 'digits')
```

## <a name="address-array-indexes"></a>Indeksy tablicy adresów

Za pomocą funkcji wyrażeń, które zwracają tablice, użyj nawiasów ([]) do adresowania określonych indeksów wewnątrz tych obiektów tablicy zwracanej. Tablica jest oparta na tych.

![Tablica konstruktora wyrażeń](media/data-flow/expb2.png "Podgląd danych wyrażeń")

## <a name="keyboard-shortcuts"></a>Skróty klawiaturowe

* CTRL + K Ctrl + C: komentarz cały wiersz.
* CTRL + K Ctrl + U: Usuń komentarz.
* F1: udostępnianie poleceń pomocy edytora.
* Alt + Strzałka w dół: Przenieś w dół bieżący wiersz.
* Alt + Strzałka w górę: Przenieś w górę bieżący wiersz.
* Ctrl + spacja: Pokaż Pomoc kontekstową.

## <a name="convert-to-dates-or-timestamps"></a>Konwertuj na daty lub sygnatury czasowe

Aby uwzględnić literały ciągu w danych wyjściowych znacznika czasu, zawiń konwersję w ```toString()```.

```toString(toTimestamp('12/31/2016T00:12:00', 'MM/dd/yyyy\'T\'HH:mm:ss'), 'MM/dd /yyyy\'T\'HH:mm:ss')```

Aby skonwertować milisekundy z epoki do daty lub sygnatury `toTimestamp(<number of milliseconds>)`czasowej, użyj. Jeśli czas przekroczy sekundy, pomnóż przez 1 000.

```toTimestamp(1574127407*1000l)```

Końcowe "l" na końcu poprzedniego wyrażenia oznacza konwersję do typu Long jako składnię wbudowaną.

## <a name="next-steps"></a>Następne kroki

[Rozpocznij tworzenie wyrażeń transformacji danych](data-flow-expression-functions.md)
