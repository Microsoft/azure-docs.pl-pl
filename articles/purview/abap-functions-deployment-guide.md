---
title: Wdróż moduł funkcji ABAP wyodrębniania metadanych dla rodziny SAP R3 w usłudze Azure kontrolą
description: W tym artykule przedstawiono kroki wdrażania modułu funkcji ABAP na serwerze SAP
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/13/2020
ms.openlocfilehash: 911238efafb948b304455cf75cc4ec2c3c605c76
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102042099"
---
# <a name="deploy-the-metadata-extraction-abap-function-module-for-the-sap-r3-family-of-bridges"></a>Wdróż moduł funkcji ABAP wyodrębniania metadanych dla rodziny SAP R3 
W tym artykule przedstawiono kroki wdrażania modułu funkcji ABAP na serwerze SAP
## <a name="overview"></a>Omówienie 

W celu wyodrębnienia metadanych z serwera SAP można użyć programu SAP Business Suite 4 HANA (S/4HANA), ECC i R/3 ERP. Jest to osiągane przez umieszczenie modułu funkcji ABAP na serwerze SAP. Ten moduł funkcji jest zdalnie dostępny przez mostek do wykonywania zapytań i pobierania (jako plik tekstowy) metadanych zawierających na serwerze SAP.
Po wykonaniu tego mostka:

1.  Importuje metadane z istniejącego pliku, który został już pobrany lokalnie z poprzedniego wykonania mostka.

2.  Wywołuje interfejs API modułu ABAP, poczekaj na pobranie, a następnie zaimportuj metadane z tego pliku.

Ten dokument zawiera szczegółowe instrukcje wymagane do wdrożenia tego modułu.

> [!Note] 
>Następujące instrukcje zostały skompilowane w oparciu o graficzny interfejs użytkownika SAP v. 7.2

## <a name="deployment-of-the-module"></a>Wdrożenie modułu 

### <a name="create-a-package"></a>Tworzenie pakietu 

Ten krok jest opcjonalny i można użyć istniejącego pakietu.

1.  Zaloguj się do serwera SAP S/4HANA lub SAP ECC i Otwórz okno \" Nawigator obiektów \" (SE80 Transaction).

2.  Wybierz \" z listy pozycję Pakiet opcji \" i wprowadź nazwę nowego pakietu (na przykład z \_ MITI), a następnie naciśnij przycisk "Display" (Wyświetl).

3.  W oknie "Tworzenie pakietu" naciśnij pozycję "tak". W związku z tym \" Konstruktor pakietów okna: Utwórz pakiet \" zostanie otwarty. Wprowadź wartość w polu "Krótki opis" i naciśnij \" ikonę Kontynuuj \" .

4.  W oknie "Monituj o lokalne żądanie Workbench" naciśnij pozycję "własne żądania". Wybierz żądanie "Programowanie".

### <a name="create-a-function-group"></a>Tworzenie grupy funkcji 

W oknie Nawigator obiektów wybierz \" \" z listy grupę funkcji i wpisz jej nazwę w polu wejściowym poniżej (na przykład z \_ MITI \_ FGROUP). Naciśnij ikonę widoku.

1.  W \" oknie Tworzenie obiektu \" naciśnij pozycję tak, aby utworzyć nową grupę funkcji.

2.  Określ odpowiedni opis w \" polu krótki tekst \" i naciśnij przycisk \" Zapisz \" .

3.  Wybierz pakiet, który został przygotowany w poprzednim kroku, \" Utwórz pakiet \" i kliknij pozycję \" Zapisz \" .

4.  Potwierdź żądanie, naciskając ikonę \" Kontynuuj \" .

5.  Aktywuj tę grupę funkcji.

### <a name="create-the-abap-function-module"></a>Tworzenie modułu funkcji ABAP 

Po utworzeniu i wybraniu grupy funkcji kliknij prawym przyciskiem myszy jej nazwę w przeglądarce repozytorium i wybierz polecenie \" Utwórz \> moduł funkcji \" .

Wprowadź \" \_ MITI \_ Pobierz \" do \" pola modułu funkcji \" i wypełnij \" krótkie wprowadzanie tekstu \" z odpowiednim opisem.

Po utworzeniu modułu podaj następujące informacje:

1.  Przejdź do \" karty atrybuty \" .

2.  Wybierz typ przetwarzania = Remote-Enabled module funkcji.

    :::image type="content" source="media/abap-functions-deployment-guide/processing-type.png" alt-text="opcje rejestrowania źródeł" border="true":::

3.  Przejdź do \" karty kod źródłowy \" . Istnieją dwa sposoby wdrażania kodu dla funkcji:

    a.  Z menu głównego Przekaż plik tekstowy [z MITI pliku do \_ \_ pobrania](https://github.com/Azure/Purview-Samples/tree/master/connectors/sap) , wybierając pozycję Narzędzia: \> więcej narzędzi = \> przekazywanie/pobieranie = \> przekazywanie.

    b.  Alternatywnie Otwórz plik, skopiuj jego zawartość i wklej go do \" obszaru kod źródłowy \" .

4.  Przejdź do \" karty Importowanie \" i Utwórz następujące parametry:

    a.  \_Typ obszaru P DD02L-TABNAME (opcjonalnie = true)

    b.  *P \_ \_Ciąg typu ścieżki lokalnej* (opcjonalnie = true)

    c.  *P \_ — Typ języka L001TAB — domyślne dane \' E\'*

    d.  *Typ ROWSKIPS, więc \_ int wartość domyślna 0*

    e.  *Typ liczby wierszy, a \_ wartość domyślna to int 0*

    > [!Note]
    > Wybierz \" wartość Przekaż \" dla wszystkich z nich

    :::image type="content" source="media/abap-functions-deployment-guide/import.png" alt-text="opcje rejestrowania źródeł" border="true":::

5.  Przejdź do karty "tabele" i Zdefiniuj następujące elementy:

    *Eksportuj \_ tabelę, taką jak TAB512*

    :::image type="content" source="media/abap-functions-deployment-guide/export-table.png" alt-text="opcje rejestrowania źródeł" border="true":::

6.  Przejdź do \" karty wyjątki \" i zdefiniuj następujący wyjątek:

    *\_ \_ DOWNLOADFAILED graficzny interfejs użytkownika \_*

    :::image type="content" source="media/abap-functions-deployment-guide/exceptions.png" alt-text="opcje rejestrowania źródeł" border="true":::

7.  Zapisz funkcję (naciśnij klawisze CTRL + S lub wybierz pozycję Moduł funkcji = \> Zapisz w menu głównym).

8.  Kliknij przycisk \" Aktywuj \" ikonę na pasku narzędzi (Ctrl + F3) i \" naciśnij \" przycisk Kontynuuj w oknie dialogowym. Jeśli zostanie wyświetlony monit, należy wybrać wygenerowane, które mają zostać aktywowane wraz z modułem głównej funkcji.

### <a name="testing-the-function"></a>Testowanie funkcji 

Po zakończeniu wszystkich poprzednich kroków postępuj zgodnie z poniższymi krokami, aby przetestować funkcję:

1.  Otwórz \_ \_ moduł funkcji pobierania Z MITI.

2.  Wybierz pozycję \" Function module = \> test = \> moduł funkcji testowej \" z menu głównego (lub naciśnij klawisz F8).

3.  Wprowadź ścieżkę do folderu w lokalnym systemie plików do parametru P \_ \_ ścieżka lokalna i naciśnij przycisk \" Wykonaj \" ikonę na pasku narzędzi (lub naciśnij klawisz F8).

4.  \_Jeśli plik z metadanymi musi zostać pobrany lub zaktualizowany, należy umieścić w polu pola obszaru. Gdy funkcja zakończy pracę, folder, który został wskazany w \_ \_ parametrze P ścieżka lokalna, musi zawierać kilka plików z metadanymi wewnątrz. Nazwy plików, które można określić w \_ polu P obszaru.

Funkcja zakończy wykonywanie operacji, a metadane zostaną pobrane znacznie szybciej w przypadku ich uruchomienia na maszynie, która ma szybkie połączenie sieciowe z serwerem SAP S/4HANA lub ECC.

## <a name="next-steps"></a>Następne kroki

- [Rejestrowanie i skanowanie źródła SAP ECC](register-scan-sapecc-source.md)
- [Rejestrowanie i skanowanie źródła danych SAP S/4HANA](register-scan-saps4hana-source.md)