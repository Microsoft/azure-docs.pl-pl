---
title: Przetwarzanie plików tekstowych o stałej długości z mapowaniem przepływów danych w Azure Data Factory
description: Dowiedz się, jak przetwarzać pliki tekstowe o stałej długości w Azure Data Factory przy użyciu mapowania przepływów danych.
author: balakreshnan
ms.service: data-factory
ms.topic: conceptual
ms.date: 8/18/2019
ms.author: makromer
ms.openlocfilehash: 2297364e8f1a531b4512f2497bc4d99963d61179
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100386207"
---
# <a name="process-fixed-length-text-files-by-using-data-factory-mapping-data-flows"></a>Przetwarzanie plików tekstowych o stałej długości przy użyciu Data Factory mapowania przepływów danych

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Korzystając z mapowania przepływów danych w Microsoft Azure Data Factory, można przekształcać dane z plików tekstowych o stałej szerokości. W poniższym zadaniu zdefiniujemy zestaw danych dla pliku tekstowego bez ogranicznika, a następnie ustawisz podział podciągów na podstawie pozycji porządkowej.

## <a name="create-a-pipeline"></a>Tworzenie potoku

1. Wybierz pozycję **+ Nowy potok** , aby utworzyć nowy potok.

2. Dodaj działanie przepływu danych, które będzie używane do przetwarzania plików o stałej szerokości:

    ![Potok o stałej szerokości](media/data-flow/fwpipe.png)

3. W działaniu przepływu danych wybierz pozycję **Nowy mapowanie przepływu danych**.

4. Dodaj źródło, kolumnę pochodną, wybór i transformację ujścia:

    ![Przepływ danych o stałej szerokości](media/data-flow/fw2.png)

5. Skonfiguruj transformację źródłową, aby korzystała z nowego zestawu danych, który będzie typem tekstu rozdzielanego.

6. Nie ustawiaj żadnego ogranicznika kolumny ani nagłówków.

   Teraz ustawimy pola początkowe i długości dla zawartości tego pliku:

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

7. Na karcie **projekcja** transformacji źródłowej powinna zostać wyświetlona kolumna ciągów o nazwie *Column_1*.

8. W kolumnie pochodna Utwórz nową kolumnę.

9. Podajemy proste nazwy kolumn, takie jak *Kol1*.

10. W Konstruktorze wyrażeń wpisz następujące polecenie:

    ```substring(Column_1,1,4)```

    ![kolumna pochodna](media/data-flow/fwderivedcol1.png)

11. Powtórz krok 10 dla wszystkich kolumn, które należy przeanalizować.

12. Wybierz kartę **Inspekcja** , aby wyświetlić nowe kolumny, które zostaną wygenerowane:

    ![skontrol](media/data-flow/fwinspect.png)

13. Użyj opcji Przekształć, aby usunąć wszystkie kolumny, które nie są potrzebne do przekształcenia:

    ![Wybierz transformację](media/data-flow/fwselect.png)

14. Użyj ujścia, aby wyprowadzić dane do folderu:

    ![zbiornik o stałej szerokości](media/data-flow/fwsink.png)

    Oto, jak wyglądają dane wyjściowe:

    ![Wyjście o stałej szerokości](media/data-flow/fxdoutput.png)

  Dane o stałej szerokości są teraz podzielone, z czterema znakami i przypisanymi do Kol1, Col2, Col3, Col4 itd. Na podstawie powyższego przykładu dane są dzielone na cztery kolumny.

## <a name="next-steps"></a>Następne kroki

* Utwórz resztę logiki przepływu danych, korzystając z mapowania [przekształceń](concepts-data-flow-overview.md)przepływów danych.
