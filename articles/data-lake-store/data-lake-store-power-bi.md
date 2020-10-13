---
title: Analizowanie danych w Azure Data Lake Storage Gen1-Power BI
description: Dowiedz się, jak za pomocą Power BI Desktop analizować i wizualizować dane przechowywane w Azure Data Lake Storage Gen1.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: bcc561cd5eea4372d798fff4580362ba0879c3a4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91574198"
---
# <a name="analyze-data-in-azure-data-lake-storage-gen1-by-using-power-bi"></a>Analizowanie danych w Azure Data Lake Storage Gen1 przy użyciu Power BI
W tym artykule dowiesz się, jak za pomocą Power BI Desktop analizować i wizualizować dane przechowywane w Azure Data Lake Storage Gen1.

## <a name="prerequisites"></a>Wymagania wstępne
Przed przystąpieniem do wykonania kroków opisanych w tym samouczku należy dysponować następującymi elementami:

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Konto Data Lake Storage Gen1**. Postępuj zgodnie z instrukcjami w punkcie wprowadzenie [do Azure Data Lake Storage Gen1 przy użyciu Azure Portal](data-lake-store-get-started-portal.md). W tym artykule przyjęto założenie, że utworzono już konto Data Lake Storage Gen1 o nazwie **myadlsg1**i przekazano do niego przykładowy plik danych (**Drivers.txt**). Ten przykładowy plik jest dostępny do pobrania z [Azure Data Lake repozytorium git](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/Drivers.txt).
* **Power BI Desktop**. Możesz pobrać ten program z [Centrum pobierania Microsoft](https://www.microsoft.com/en-us/download/details.aspx?id=45331). 

## <a name="create-a-report-in-power-bi-desktop"></a>Tworzenie raportu w programie Power BI Desktop
1. Uruchom Power BI Desktop na komputerze.
2. Na Wstążce **Narzędzia główne** kliknij pozycję **Pobierz dane**, a następnie kliknij pozycję więcej. W oknie dialogowym **pobieranie danych** kliknij pozycję **Azure**, kliknij pozycję **Azure Data Lake Store**, a następnie kliknij pozycję **Połącz**.
   
    ![Zrzut ekranu przedstawiający okno dialogowe Pobieranie danych z wyróżnioną opcją Azure Data Lake Store i opcją Połącz wywołana.](./media/data-lake-store-power-bi/get-data-lake-store-account.png "Połącz z Data Lake Storage Gen1")
3. Jeśli zobaczysz okno dialogowe dotyczące łącznika w fazie tworzenia, wybierz opcję Kontynuuj.
4. W oknie dialogowym **Azure Data Lake Store** Podaj adres URL konta Data Lake Storage Gen1, a następnie kliknij przycisk **OK**.
   
    ![Adres URL Data Lake Storage Gen1](./media/data-lake-store-power-bi/get-data-lake-store-account-url.png "Adres URL Data Lake Storage Gen1")
5. W następnym oknie dialogowym kliknij przycisk **Zaloguj** się, aby zalogować się do konta Data Lake Storage Gen1. Nastąpi przekierowanie do strony logowania w organizacji. Postępuj zgodnie z monitami, aby zalogować się do konta.
   
    ![Zaloguj się do Data Lake Storage Gen1](./media/data-lake-store-power-bi/get-data-lake-store-account-signin.png "Zaloguj się do Data Lake Storage Gen1")
6. Po pomyślnym zalogowaniu kliknij pozycję **Połącz**.
   
    ![Zrzut ekranu przedstawiający okno dialogowe Azure Data Lake Store z opcją Połącz o nazwie out.](./media/data-lake-store-power-bi/get-data-lake-store-account-connect.png "Połącz z Data Lake Storage Gen1")
7. Następne okno dialogowe zawiera plik przekazany do konta Data Lake Storage Gen1. Sprawdź informacje, a następnie kliknij przycisk **Załaduj**.
   
    ![Ładowanie danych z Data Lake Storage Gen1](./media/data-lake-store-power-bi/get-data-lake-store-account-load.png "Ładowanie danych z Data Lake Storage Gen1")
8. Po pomyślnym załadowaniu danych do Power BI na karcie **pola** zostaną wyświetlone następujące pola.
   
    ![Importowane pola](./media/data-lake-store-power-bi/imported-fields.png "Importowane pola")
   
    Jednak aby wizualizować i analizować dane, wolimy, aby dane były dostępne według następujących pól
   
    ![Żądane pola](./media/data-lake-store-power-bi/desired-fields.png "Żądane pola")
   
    W następnych krokach zaktualizujemy zapytanie w celu przekonwertowania zaimportowanych danych w żądanym formacie.
9. Na Wstążce **Narzędzia główne** kliknij pozycję **Edytuj zapytania**.
   
    ![Zrzut ekranu wstążki Narzędzia główne z opcją Edytuj zapytania o nazwie out.](./media/data-lake-store-power-bi/edit-queries.png "Edytuj zapytania")
10. W edytorze zapytań w kolumnie **zawartość** kliknij pozycję dane **binarne**.
    
    ![Zrzut ekranu edytora zapytań z kolumną zawartości o nazwie out.](./media/data-lake-store-power-bi/convert-query1.png "Edytuj zapytania")
11. Zostanie wyświetlona ikona pliku reprezentująca plik **Drivers.txt** , który został przekazany. Kliknij plik prawym przyciskiem myszy, a następnie kliknij pozycję **CSV**.    
    
    ![Zrzut ekranu edytora zapytań z opcją CSV o nazwie out.](./media/data-lake-store-power-bi/convert-query2.png "Edytuj zapytania")
12. Powinny pojawić się dane wyjściowe, jak pokazano poniżej. Twoje dane są teraz dostępne w formacie, którego można użyć do tworzenia wizualizacji.
    
    ![Zrzut ekranu edytora zapytań z danymi wyjściowymi wyświetlanymi zgodnie z oczekiwaniami.](./media/data-lake-store-power-bi/convert-query3.png "Edytuj zapytania")
13. Na Wstążce **Narzędzia główne** kliknij przycisk **Zamknij i Zastosuj**, a następnie kliknij przycisk **Zamknij i Zastosuj**.
    
    ![Zrzut ekranu wstążki Narzędzia główne z opcją Zamknij i Zastosuj o nazwie wychodzącej.](./media/data-lake-store-power-bi/load-edited-query.png "Edytuj zapytania")
14. Po zaktualizowaniu zapytania na karcie **pola** zostaną wyświetlone nowe pola dostępne dla wizualizacji.
    
    ![Zaktualizowane pola](./media/data-lake-store-power-bi/updated-query-fields.png "Zaktualizowane pola")
15. Pozwól nam utworzyć wykres kołowy reprezentujący sterowniki w poszczególnych miastach dla danego kraju/regionu. W tym celu należy wybrać następujące opcje.
    
    1. Na karcie wizualizacje kliknij symbol wykresu kołowego.
       
        ![Utwórz wykres kołowy](./media/data-lake-store-power-bi/create-pie-chart.png "Utwórz wykres kołowy")
    2. Kolumny, które będą używane, to **kolumna 4** (nazwa miasta) i **kolumna 7** (nazwa kraju/regionu). Przeciągnij te kolumny z karty **pola** na kartę **wizualizacje** , jak pokazano poniżej.
       
        ![Tworzenie wizualizacji](./media/data-lake-store-power-bi/create-visualizations.png "Tworzenie wizualizacji")
    3. Wykres kołowy powinien teraz wyglądać podobnie do przedstawionego poniżej.
       
        ![Wykres kołowy](./media/data-lake-store-power-bi/pie-chart.png "Tworzenie wizualizacji")
16. Wybierając określony kraj/region z filtrów na poziomie strony, można teraz zobaczyć liczbę sterowników w każdym mieście wybranego kraju/regionu. Na przykład na karcie **wizualizacje** w obszarze filtry na **poziomie strony**wybierz pozycję **Brazylia**.
    
    ![Wybierz kraj/region](./media/data-lake-store-power-bi/select-country.png "Wybierz kraj/region")
17. Wykres kołowy jest automatycznie aktualizowany w celu wyświetlenia sterowników w miejscowościach Brazylii.
    
    ![Sterowniki w kraju/regionie](./media/data-lake-store-power-bi/driver-per-country.png "Sterowniki na kraj/region")
18. W menu **plik** kliknij polecenie **Zapisz** , aby zapisać wizualizację jako plik Power BI Desktop.

## <a name="publish-report-to-power-bi-service"></a>Publikuj raport w usługa Power BI
Po utworzeniu wizualizacji w Power BI Desktop możesz udostępnić ją innym osobom, publikując ją w usługa Power BI. Aby uzyskać instrukcje, jak to zrobić, zobacz temat [Publikowanie z Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-upload-desktop-files/).

## <a name="see-also"></a>Zobacz też
* [Analizowanie danych w Data Lake Storage Gen1 przy użyciu Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md)

