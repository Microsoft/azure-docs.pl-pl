---
title: Przetwarzanie plików tekstowych o stałej długości z przepływami danych mapowania w usłudze Azure Data Factory
description: Dowiedz się, jak przetwarzać pliki tekstowe o stałej długości w usłudze Azure Data Factory przy użyciu przepływów danych mapowania.
services: data-factory
author: balakreshnan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 8/18/2019
ms.author: makromer
ms.openlocfilehash: d629a9031f032a77efc953311a45b55996568191
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414370"
---
# <a name="process-fixed-length-text-files-by-using-data-factory-mapping-data-flows"></a>Przetwarzanie plików tekstowych o stałej długości przy użyciu przepływów danych mapowania fabryki danych

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Za pomocą mapowania przepływów danych w usłudze Microsoft Azure Data Factory można przekształcać dane z plików tekstowych o stałej szerokości. W poniższym zadaniu zdefiniujemy zestaw danych dla pliku tekstowego bez ogranicznika, a następnie skonfigurujemy podziały podciągów na podstawie położenia porządkowego.

## <a name="create-a-pipeline"></a>Tworzenie potoku

1. Wybierz **+Nowy potok,** aby utworzyć nowy potok.

2. Dodaj działanie przepływu danych, które będzie używane do przetwarzania plików o stałej szerokości:

    ![Potok o stałej szerokości](media/data-flow/fwpipe.png)

3. W działaniu przepływu danych wybierz pozycję **Nowy przepływ danych mapowania**.

4. Dodaj transformację Źródło, Kolumna pochodna, Wybierz i Zlew:

    ![Przepływ danych o stałej szerokości](media/data-flow/fw2.png)

5. Skonfiguruj transformację źródła, aby używać nowego zestawu danych, który będzie typu Tekst rozdzielany.

6. Nie ustawiaj żadnych ogranicznika kolumn ani nagłówków.

   Teraz ustawimy punkty początkowe pola i długości zawartości tego pliku:

    ```
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    ```

7. Na karcie **Rzutowanie** transformacji źródła powinna zostać wyświetleni kolumna ciągu o nazwie *Column_1*.

8. W kolumnie Pochodne utwórz nową kolumnę.

9. Damy kolumny proste nazwy, takie jak *col1*.

10. W konstruktorze wyrażeń wpisz następujące polecenie:

    ```substring(Column_1,1,4)```

    ![kolumna pochodna](media/data-flow/fwderivedcol1.png)

11. Powtórz krok 10 dla wszystkich kolumn, które chcesz przeanalizować.

12. Wybierz kartę **Inspekcja,** aby wyświetlić nowe kolumny, które zostaną wygenerowane:

    ![Sprawdzić](media/data-flow/fwinspect.png)

13. Użyj wybierz transformację, aby usunąć dowolną kolumnę, która nie jest potrzebna do transformacji:

    ![wybierz transformację](media/data-flow/fwselect.png)

14. Użyj sink do wysunięcia danych do folderu:

    ![umywalka o stałej szerokości](media/data-flow/fwsink.png)

    Oto jak wygląda dane wyjściowe:

    ![wyjście o stałej szerokości](media/data-flow/fxdoutput.png)

  Dane o stałej szerokości są teraz dzielone, z czterema znakami każdy i przypisane do Col1, Col2, Col3, Col4 i tak dalej. Na podstawie poprzedniego przykładu dane są dzielone na cztery kolumny.

## <a name="next-steps"></a>Następne kroki

* Skompiluj pozostałą część logiki przepływu danych przy użyciu [przekształceń przepływów](concepts-data-flow-overview.md)danych mapowania .
