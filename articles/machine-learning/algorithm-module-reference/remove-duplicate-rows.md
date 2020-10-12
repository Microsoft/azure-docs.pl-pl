---
title: 'Usuń zduplikowane wiersze: odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak za pomocą modułu Usuń zduplikowane wiersze w Azure Machine Learning usunąć potencjalne duplikaty z zestawu danych.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: bf35d08128aa8a3e8f545ed7184866694219f2cb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90905213"
---
# <a name="remove-duplicate-rows-module"></a>Usuń moduł zduplikowanych wierszy

W tym artykule opisano moduł w programie Azure Machine Learning Designer.

Ten moduł służy do usuwania potencjalnych duplikatów z zestawu danych.

Załóżmy na przykład, że dane wyglądają jak poniżej, i reprezentują wiele rekordów dla pacjentów. 

| PatientID | Inicjały| Płeć|Wiek|Przyjęte|
|----|----|----|----|----|
|1|F.M.| M| 53| Sty|
|2| F.A.M.| M| 53| Sty|
|3| F.A.M.| M| 24| Sty|
|3| F.M.| M| 24| Lut|
|4| F.M.| M| 23| Lut|
| | F.M.| M| 23| |
|5| F.A.M.| M| 53| |
|6| F.A.M.| M| NaN| |
|7| F.A.M.| M| NaN| |

Jasno ten przykład zawiera wiele kolumn, które mogą duplikować dane. Bez względu na to, czy są one w rzeczywistości duplikatami, zależy od wiedzy o danych. 

+ Na przykład może być wiadomo, że wielu pacjentów ma taką samą nazwę. Nie można wyeliminować duplikatów przy użyciu żadnych kolumn nazw, tylko kolumny **ID** . W ten sposób tylko wiersze z zduplikowanymi wartościami identyfikatora są odfiltrowane, bez względu na to, czy pacjenci mają taką samą nazwę, czy nie.

+ Alternatywnie możesz zdecydować, aby zezwolić na duplikaty w polu ID, a także użyć innej kombinacji plików do znajdowania unikatowych rekordów, takich jak imię, nazwisko, wiek i płeć.  

Aby ustawić kryteria dla tego, czy wiersz jest zduplikowany, należy określić pojedynczą kolumnę lub zestaw kolumn do użycia jako **klucze**. Dwa wiersze są uznawane za duplikaty tylko wtedy, gdy wartości we **wszystkich** kolumnach klucza są równe. Jeśli w dowolnym wierszu brakuje wartości **kluczy**, nie będą one uznawane za zduplikowane wiersze. Na przykład jeśli płeć i wiek są ustawione jako klucze w powyższej tabeli, wiersz 6 i 7 nie są zduplikowanymi wierszami, w których brakuje wartości w wieku.

Po uruchomieniu modułu tworzy zestaw danych kandydujących i zwraca zbiór wierszy, które nie mają duplikatów we wskazanym zestawie kolumn.

> [!IMPORTANT]
> Źródłowy zestaw danych nie jest zmieniany; Ten moduł tworzy nowy zestaw danych, który jest filtrowany w celu wykluczania duplikatów na podstawie określonych kryteriów.

## <a name="how-to-use-remove-duplicate-rows"></a>Jak używać usuwania zduplikowanych wierszy

1. Dodaj moduł do potoku. Moduł **usuwanie zduplikowanych wierszy** można znaleźć w obszarze **Przekształcanie danych**, **manipulowanie**.  

2. Połącz zestaw danych, który ma zostać wyszukany dla zduplikowanych wierszy.

3. W okienku **Właściwości** w obszarze **wyrażenie filtru wyboru kolumny klucza**kliknij pozycję **Uruchom selektor kolumny**, aby wybrać kolumny, które mają być używane do identyfikowania duplikatów.

    W tym kontekście **klucz** nie oznacza unikatowego identyfikatora. Wszystkie kolumny wybrane za pomocą selektora kolumn są **wykluczane jako kolumny kluczy**. Wszystkie niewybrane kolumny są uznawane za kolumny niebędące kolumnami klucza. Kombinacja kolumn wybranych jako klucze określa unikatowość rekordów. (Należy traktować ją jako instrukcję SQL, która używa wielu dołączeń równości).

    Przykłady:

    + "Chcę mieć pewność, że identyfikatory są unikatowe": Wybierz tylko kolumnę ID.
    + "Chcę mieć pewność, że kombinacja imienia, nazwisko i identyfikator jest unikatowa": zaznacz wszystkie trzy kolumny.

4. Użyj pola wyboru **Zachowaj pierwszy zduplikowany wiersz** , aby wskazać, który wiersz ma zostać zwrócony po znalezieniu duplikatów:

    + W przypadku zaznaczenia pierwszy wiersz jest zwracany, a inne odrzucane. 
    + W przypadku usunięcia zaznaczenia tej opcji ostatni zduplikowany wiersz jest przechowywany w wynikach, a pozostałe zostaną odrzucone. 

5. Prześlij potok.

6. Aby przejrzeć wyniki, kliknij prawym przyciskiem myszy moduł i wybierz polecenie **Wizualizuj**. 

> [!TIP]
> Jeśli wyniki są trudne do zrozumienia lub jeśli chcesz wykluczyć pewne kolumny, możesz usunąć kolumny za pomocą modułu [SELECT Columns in DataSet (Wybieranie kolumn w zestawie danych](./select-columns-in-dataset.md) ).

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning. 