---
title: Moduł funkcji ABAP do ekstrakcji metadanych w oprogramowaniu SAP R3 — Azure kontrolą
description: W tym artykule przedstawiono kroki wdrażania modułu funkcji ABAP na serwerze SAP
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/13/2020
ms.openlocfilehash: 9bd3c315fcc15317a9fa483289fdc326ca6aa47f
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102614364"
---
# <a name="deploy-the-metadata-extraction-abap-function-module-for-the-sap-r3-family-of-bridges"></a>Wdróż moduł funkcji ABAP wyodrębniania metadanych dla rodziny SAP R3

W tym artykule opisano procedurę wdrażania modułu funkcji ABAP na serwerze SAP.

## <a name="overview"></a>Omówienie

W celu wyodrębnienia metadanych z serwera SAP można użyć programu SAP Business Suite 4 HANA (S/4HANA), ECC i R/3 ERP. Jest to osiągane przez umieszczenie modułu funkcji ABAP na serwerze SAP. Ten moduł funkcji jest zdalnie dostępny przez mostek do wykonywania zapytań i pobierania (jako plik tekstowy) metadanych zawierających na serwerze SAP.

Po wykonaniu tego mostka:

1. Importuje metadane z istniejącego pliku, który został już pobrany lokalnie z poprzedniego wykonania mostka.

2. Wywołuje interfejs API modułu ABAP, poczekaj na pobranie, a następnie zaimportuj metadane z tego pliku.

Ten dokument zawiera szczegółowe instrukcje wymagane do wdrożenia tego modułu.

> [!Note]
> Następujące instrukcje zostały skompilowane w oparciu o graficzny interfejs użytkownika SAP v. 7.2

## <a name="deployment-of-the-module"></a>Wdrożenie modułu

### <a name="create-a-package"></a>Tworzenie pakietu

Ten krok jest opcjonalny i można użyć istniejącego pakietu.

1. Zaloguj się do serwera SAP S/4HANA lub SAP ECC i Otwórz okno **Nawigator obiektów** (SE80 Transaction).

2. Wybierz z listy pozycję **pakiet** opcji i wprowadź nazwę nowego pakietu (na przykład z \_ MITI), a następnie naciśnij przycisk **Wyświetl**.

3. W oknie **Tworzenie pakietu** wybierz pozycję **tak** . W związku z tym zostanie otwarty **Konstruktor pakietów okna: Utwórz pakiet** . Wprowadź wartość w polu **Krótki opis** , a następnie wybierz ikonę **Kontynuuj** .

4. W **wierszu polecenia dla lokalnego żądania Workbench** wybierz pozycję **własne żądania** . Wybierz pozycję żądanie **deweloperskie** .

### <a name="create-a-function-group"></a>Tworzenie grupy funkcji

W oknie Nawigator obiektów wybierz z listy **grupę funkcji** i wpisz jej nazwę w polu wejściowym poniżej (na przykład z \_ MITI \_ FGROUP). Wybierz ikonę **widoku** .

1. W oknie **Tworzenie obiektu** wybierz pozycję **tak** , aby utworzyć nową grupę funkcji.

2. Określ odpowiedni opis w polu **krótki tekst** i naciśnij przycisk **Zapisz**.

3. Wybierz pakiet, który został przygotowany w poprzednim kroku, **Utwórz pakiet** i wybierz pozycję **Zapisz**.

4. Potwierdź żądanie, naciskając ikonę **Kontynuuj**.

5. Aktywuj grupę funkcji.

### <a name="create-the-abap-function-module"></a>Tworzenie modułu funkcji ABAP

1. Po utworzeniu grupy funkcji wybierz ją.

2. Kliknij prawym przyciskiem myszy nazwę grupy funkcji w przeglądarce repozytorium, a następnie wybierz pozycję **Utwórz**, a następnie **moduł funkcji**.

3. W polu **moduł funkcji** wprowadź wartość `Z_MITI_DOWNLOAD` . Wypełnij **krótkie** dane wejściowe o odpowiednim opisie.

Po utworzeniu modułu podaj następujące informacje:

1. Przejdź do karty **atrybuty** .

2. Wybierz **Typ przetwarzania** jako **moduł funkcji zdalnych**.

   :::image type="content" source="media/abap-functions-deployment-guide/processing-type.png" alt-text="Opcja Rejestruj źródła — Remote-Enabled module funkcji" border="true":::

3. Przejdź do karty **kod źródłowy** . Istnieją dwa sposoby wdrażania kodu dla funkcji:

   a. Z menu głównego Przekaż plik tekstowy [z \_ MITI \_ Download](https://github.com/Azure/Purview-Samples/tree/master/connectors/sap) . Aby to zrobić, wybierz kolejno opcje **Narzędzia**, **więcej narzędzi**, następnie **Przekaż/Pobierz**, a następnie **Przekaż**.

   b. Alternatywnie Otwórz plik, skopiuj jego zawartość i wklej go do obszaru **kod źródłowy** .

4. Przejdź do karty **Importowanie** i Utwórz następujące parametry:

   a.  \_Typ obszaru P DD02L-TABNAME (opcjonalnie = true)

   b.  *P \_ \_Ciąg typu ścieżki lokalnej* (opcjonalnie = true)

   c.  *P \_ — Typ języka L001TAB — domyślne dane \' E\'*

   d.  *Typ ROWSKIPS, więc \_ int wartość domyślna 0*

   e.  *Typ liczby wierszy, a \_ wartość domyślna to int 0*

   > [!Note]
   > Wybierz **wartość Przekaż** dla wszystkich z nich

   :::image type="content" source="media/abap-functions-deployment-guide/import.png" alt-text="Opcja Rejestruj źródła — Importuj parametry" border="true":::

5. Przejdź do karty "tabele" i Zdefiniuj następujące elementy:

   `EXPORT_TABLE LIKE TAB512`

   :::image type="content" source="media/abap-functions-deployment-guide/export-table.png" alt-text="Rejestrowanie opcji źródeł — karta tabele" border="true":::

6. Przejdź do karty **wyjątki** i zdefiniuj następujący wyjątek: `E_EXP_GUI_DOWNLOADFAILED`

   :::image type="content" source="media/abap-functions-deployment-guide/exceptions.png" alt-text="Rejestrowanie opcji źródeł — karta wyjątki" border="true":::

7. Zapisz funkcję (naciśnij klawisze CTRL + S lub wybierz **moduł funkcji**, a następnie **Zapisz** w menu głównym).

8. Kliknij przycisk **Aktywuj** ikonę na pasku narzędzi (Ctrl + F3) i wybierz przycisk  **Kontynuuj** w oknie dialogowym. Jeśli zostanie wyświetlony monit, należy wybrać wygenerowane, które mają zostać aktywowane wraz z modułem głównej funkcji.

### <a name="testing-the-function"></a>Testowanie funkcji

Po zakończeniu wszystkich poprzednich kroków postępuj zgodnie z poniższymi krokami, aby przetestować funkcję:

1. Otwórz \_ \_ moduł funkcji pobierania Z MITI.

2. Wybierz pozycję **moduł funkcji**, a następnie **Przetestuj**, a następnie **moduł testów** z menu głównego (lub naciśnij klawisz F8).

3. Wprowadź ścieżkę do folderu w lokalnym systemie plików do parametru P \_ \_ ścieżka lokalna i naciśnij przycisk **Wykonaj** ikonę na pasku narzędzi (lub naciśnij klawisz F8).

4. \_Jeśli plik z metadanymi musi zostać pobrany lub zaktualizowany, należy umieścić w polu pola obszaru. Gdy funkcja zakończy pracę, folder, który został wskazany w \_ \_ parametrze P ścieżka lokalna, musi zawierać kilka plików z metadanymi wewnątrz. Nazwy plików, które można określić w \_ polu P obszaru.

Funkcja zakończy wykonywanie operacji, a metadane zostaną pobrane znacznie szybciej w przypadku ich uruchomienia na maszynie, która ma szybkie połączenie sieciowe z serwerem SAP S/4HANA lub ECC.

## <a name="next-steps"></a>Następne kroki

- [Rejestrowanie i skanowanie źródła SAP ECC](register-scan-sapecc-source.md)
- [Rejestrowanie i skanowanie źródła danych SAP S/4HANA](register-scan-saps4hana-source.md)
