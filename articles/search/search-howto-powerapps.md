---
title: 'Samouczek: wykonywanie zapytań z aplikacji zaawansowanych'
titleSuffix: Azure Cognitive Search
description: Wskazówki krok po kroku dotyczące tworzenia aplikacji zaawansowanej, która łączy się z indeksem Wyszukiwanie poznawcze platformy Azure, wysyła zapytania i renderuje wyniki.
author: luiscabrer
manager: eladz
ms.author: luisca
ms.service: cognitive-search
ms.devlang: rest-api
ms.topic: tutorial
ms.date: 11/17/2020
ms.openlocfilehash: e8c16f02cf6b77fa54d2a19abac48e9914aa99bd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96008569"
---
# <a name="tutorial-query-a-cognitive-search-index-from-power-apps"></a>Samouczek: wykonywanie zapytania dotyczącego indeksu Wyszukiwanie poznawcze z poziomu aplikacji zaawansowanych

Skorzystaj z szybkiego środowiska tworzenia aplikacji dla aplikacji zaawansowanych, aby utworzyć niestandardową aplikację do przeszukiwanej zawartości w usłudze Azure Wyszukiwanie poznawcze.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Nawiązywanie połączenia z usługą Azure Wyszukiwanie poznawcze
> * Konfigurowanie żądania zapytania
> * Wizualizowanie wyników w aplikacji kanwy

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Otwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="prerequisites"></a>Wymagania wstępne

* [Konto aplikacji zaawansowanych](https://make.powerapps.com)

* [Hotele — przykładowy indeks](search-get-started-portal.md) hostowany w usłudze wyszukiwania

* [Klucz interfejsu API zapytania](search-security-api-keys.md#find-existing-keys)

## <a name="1---create-a-custom-connector"></a>1 — Tworzenie łącznika niestandardowego

Łącznik w usłudze aplikacje zaawansowane to połączenie ze źródłem danych. W tym kroku utworzysz łącznik niestandardowy w celu nawiązania połączenia z indeksem wyszukiwania w chmurze.

1. [Zaloguj](https://make.powerapps.com) się do aplikacji zaawansowanych.

1. Po lewej stronie rozwiń węzeł   >  **Łączniki niestandardowe** danych.
 
    :::image type="content" source="./media/search-howto-powerapps/1-2-custom-connector.png" alt-text="Menu łączników niestandardowych" border="true":::

1. Wybierz pozycję  **+ Nowy łącznik niestandardowy**, a następnie wybierz pozycję **Utwórz z pustego**.

    :::image type="content" source="./media/search-howto-powerapps/1-3-create-blank.png" alt-text="Utwórz z pustego menu" border="true":::

1. Podaj nazwę łącznika niestandardowego (na przykład *AzureSearchQuery*), a następnie kliknij przycisk **Kontynuuj**.

1. Wprowadź informacje na stronie Ogólne:

   * Kolor tła ikony (na przykład #007ee5)
   * Opis (na przykład "Łącznik do Wyszukiwanie poznawcze platformy Azure")
   * Na hoście musisz wprowadzić adres URL usługi wyszukiwania (na przykład `<yourservicename>.search.windows.net` ).
   * W przypadku podstawowego adresu URL wystarczy wprowadzić "/"

    :::image type="content" source="./media/search-howto-powerapps/1-5-general-info.png" alt-text="Dialog informacji ogólnych" border="true":::

1. Na stronie Zabezpieczenia ustaw *klucz interfejsu API* jako **Typ uwierzytelniania**, ustaw zarówno etykietę parametru, jak i nazwę parametru na *klucz API-Key*. W obszarze **Lokalizacja parametru** wybierz pozycję *nagłówek* , jak pokazano poniżej.

    :::image type="content" source="./media/search-howto-powerapps/1-6-authentication-type.png" alt-text="Opcja typu uwierzytelniania" border="true":::

1. Na stronie definicje wybierz pozycję **+ Nowa akcja** , aby utworzyć akcję, która będzie wysyłać zapytania do indeksu. Wprowadź wartość "Query" dla podsumowania i nazwę identyfikatora operacji. Wprowadź opis *"zapytania w indeksie wyszukiwania"*.

    :::image type="content" source="./media/search-howto-powerapps/1-7-new-action.png" alt-text="Nowe opcje akcji" border="true":::

1. Przewiń w dół. W obszarze żądania wybierz pozycję **+ Importuj z przykładowego** przycisku, aby skonfigurować żądanie zapytania do usługi wyszukiwania:

   * Wybierz zlecenie `GET`

   * W polu adres URL wprowadź przykładowe zapytanie dla indeksu wyszukiwania ( `search=*` zwraca wszystkie dokumenty, `$select=` umożliwia wybranie pól). Wymagana jest wersja interfejsu API. W pełni określony adres URL może wyglądać następująco: `https://mydemo.search.windows.net/indexes/hotels-sample-index/docs?search=*&$select=HotelName,Description,Address/City&api-version=2020-06-30`

   * Dla nagłówka wpisz `Content-Type` . Wartość zostanie ustawiona na `application/json` w późniejszym kroku.

     **Aplikacje zaawansowane** wykorzystują składnię w adresie URL do wyodrębniania parametrów z zapytania: parametry Search, Select i API-Version można skonfigurować w miarę postępu przez kreatora.

       :::image type="content" source="./media/search-howto-powerapps/1-8-1-import-from-sample.png" alt-text="Importowanie z próbki" border="true":::

1. Kliknij przycisk **Importuj** , aby wypełnić żądanie. Ukończ Ustawianie metadanych parametrów, klikając symbol **...** obok każdego z parametrów. Po każdej aktualizacji parametru kliknij przycisk **Wstecz** , aby powrócić do strony żądania.

   :::image type="content" source="./media/search-howto-powerapps/1-8-2-import-from-sample.png" alt-text="Importuj z przykładowego dialogu" border="true":::

1. Dla *wyszukiwania*: Ustaw `*` **wartość domyślną**, Ustaw jako *Fałsz* i ustaw **widoczność** na *none*.  

    :::image type="content" source="./media/search-howto-powerapps/1-10-1-parameter-metadata-search.png" alt-text="Metadane parametrów wyszukiwania" border="true":::

1. Dla *opcji Select*: `HotelName,Description,Address/City` Ustaw jako **wartość domyślną**, ustaw parametr **Required** na *wartość false* i ustaw **widoczność** na *none*.  

    :::image type="content" source="./media/search-howto-powerapps/1-10-4-parameter-metadata-select.png" alt-text="Wybieranie metadanych parametrów" border="true":::

1. W przypadku *interfejsu API-Version*: Ustaw `2020-06-30` **wartość domyślną**, ustaw **wymaganą** *wartość true* i ustaw **widoczność** jako *wewnętrzną*.  

    :::image type="content" source="./media/search-howto-powerapps/1-10-2-parameter-metadata-version.png" alt-text="Metadane parametru wersji" border="true":::

1. Dla *typu zawartości*: Ustaw wartość `application/json` .

1. Po wprowadzeniu tych zmian Przełącz się do widoku **edytora Swagger** . W sekcji Parameters powinna zostać wyświetlona następująca konfiguracja:

    ```JSON
    parameters:
      - {name: search, in: query, required: false, type: string, default: '*'}
      - {name: $select, in: query, required: false, type: string, default: 'HotelName,Description,Address/City'}
      - {name: api-version, in: query, required: true, type: string, default: '2020-06-30',
        x-ms-visibility: internal}
      - {name: Content-Type, in: header, required: false, type: string}
    ```

1. Przełącz się z powrotem do kreatora i wróć do **3. Krok żądania** . Przewiń w dół do sekcji odpowiedź. Kliknij pozycję **"Dodaj odpowiedź domyślną"**. Jest to ważne, ponieważ ułatwia aplikacjom poznanie schematu odpowiedzi. 

1. Wklej przykładową odpowiedź. Prostą metodą przechwytywania przykładowej odpowiedzi jest użycie Eksploratora wyszukiwania w Azure Portal. W Eksploratorze wyszukiwania należy wprowadzić to samo zapytanie jak dla żądania, ale dodać **$Top = 2** , aby ograniczyć wyniki do zaledwie dwóch dokumentów:: `search=*&$select=HotelName,Description,Address/City&$top=2` . 

   Aplikacje zaawansowane wymagają tylko kilku wyników, aby wykryć schemat. Możesz teraz skopiować następującą odpowiedź do kreatora, zakładając, że używasz opcji hoteli-Sample-index.

    ```JSON
    {
        "@odata.context": "https://mydemo.search.windows.net/indexes('hotels-sample-index')/$metadata#docs(*)",
        "value": [
            {
                "@search.score": 1,
                "HotelName": "Arcadia Resort & Restaurant",
                "Description": "The largest year-round resort in the area offering more of everything for your vacation – at the best value!  What can you enjoy while at the resort, aside from the mile-long sandy beaches of the lake? Check out our activities sure to excite both young and young-at-heart guests. We have it all, including being named “Property of the Year” and a “Top Ten Resort” by top publications.",
                "Address": {
                    "City": "Seattle"
                }
            },
            {
                "@search.score": 1,
                "HotelName": "Travel Resort",
                "Description": "The Best Gaming Resort in the area.  With elegant rooms & suites, pool, cabanas, spa, brewery & world-class gaming.  This is the best place to play, stay & dine.",
                "Address": {
                    "City": "Albuquerque"
                }
            }
        ]
    }
    ```

    > [!TIP] 
    > Istnieje limit znaków w odpowiedzi JSON, którą można wprowadzić, aby można było uprościć kod JSON przed jego wklejeniem. Schemat i format odpowiedzi są ważniejsze niż same wartości. Na przykład pole Opis można uprościć tylko do pierwszego zdania.

1. Kliknij przycisk **Importuj** , aby dodać odpowiedź domyślną.

1. Kliknij przycisk **Utwórz łącznik** w prawym górnym rogu, aby zapisać definicję.

1. Kliknij przycisk **Zamknij** , aby zamknąć łącznik.

## <a name="2---test-the-connection"></a>2 — testowanie połączenia

Po pierwszym utworzeniu łącznika należy go otworzyć ponownie z listy łączniki niestandardowe w celu przetestowania. Później w przypadku wprowadzenia dodatkowych aktualizacji można testować za pomocą kreatora.

Dla tego zadania potrzebny będzie [klucz interfejsu API zapytania](search-security-api-keys.md#find-existing-keys) . Za każdym razem, gdy tworzone jest połączenie, niezależnie od tego, czy dla uruchomienia testu lub dołączenia do aplikacji łącznik potrzebuje klucza interfejsu API zapytania używanego do łączenia się z usługą Azure Wyszukiwanie poznawcze.

1. Po lewej stronie kliknij pozycję **Łączniki niestandardowe**.

1. Znajdź swój łącznik na liście (w tym samouczku jest to "AzureSearchQuery").

1. Wybierz łącznik, rozwiń listę akcje, a następnie wybierz pozycję **Wyświetl właściwości**.

    :::image type="content" source="./media/search-howto-powerapps/1-11-1-test-connector.png" alt-text="Wyświetl właściwości" border="true":::

1. Wybierz pozycję **Edytuj** w prawym górnym rogu.

1. Wybierz pozycję **4. Przetestuj** , aby otworzyć stronę testową.

1. W obszarze Operacja testowa kliknij pozycję **+ nowe połączenie**.

1. Wprowadź klucz interfejsu API zapytania. Jest to zapytanie Wyszukiwanie poznawcze platformy Azure umożliwiające dostęp tylko do odczytu do indeksu. Klucz można [znaleźć](search-security-api-keys.md#find-existing-keys) w Azure Portal. 

1. W obszarze operacje kliknij przycisk **Testuj operację** . Jeśli zakończyło się pomyślnie, powinien zostać wyświetlony stan 200 i w treści odpowiedzi powinna zostać wyświetlona notacja JSON opisująca wyniki wyszukiwania.

    :::image type="content" source="./media/search-howto-powerapps/1-11-2-test-connector.png" alt-text="Odpowiedź JSON" border="true":::

## <a name="3---visualize-results"></a>3 — Wizualizacja wyników

W tym kroku utworzysz aplikację, korzystając z pola wyszukiwania, przycisku wyszukiwania i obszaru wyświetlania dla wyników. Aplikacja energetyczna będzie łączyć się z ostatnio utworzonym łącznikiem niestandardowym w celu pobrania danych z Azure Search.

1. Po lewej stronie rozwiń pozycję **aplikacje**  >  **i Nowa**  >  **Kanwa** aplikacji.

    :::image type="content" source="./media/search-howto-powerapps/2-1-create-canvas.png" alt-text="Tworzenie aplikacji kanwy" border="true":::

1. Wybierz typ aplikacji. Na potrzeby tego samouczka Utwórz **pustą aplikację** z **układem telefonu**. Zostanie wyświetlona **aplikacja PowerShell Apps** .

1. W programie Studio wybierz kartę **źródła danych** , a następnie kliknij nowo utworzony łącznik. W naszym przypadku jest nazywana *AzureSearchQuery*. Kliknij pozycję **Dodaj połączenie**.

   Wprowadź klucz interfejsu API zapytania.

    :::image type="content" source="./media/search-howto-powerapps/2-3-connect-connector.png" alt-text="Łącznik połączenia" border="true":::

    Teraz *AzureSearchQuery* to źródło danych, które jest dostępne do użycia w aplikacji.

1. Na **karcie Wstawianie** Dodaj kilka kontrolek do kanwy.

    :::image type="content" source="./media/search-howto-powerapps/2-4-add-controls.png" alt-text="Wstaw kontrolki" border="true":::

1. Wstaw następujące elementy:

   * Etykieta tekstowa z wartością "zapytanie:"
   * Element tekstu wejściowego (Wywołaj go *txtQuery*, wartość domyślna: "*")
   * Przycisk z tekstem "Wyszukaj" 
   * Pionowa Galeria o nazwie (Wywołaj ją *galleryResults*)

    Kanwa powinna wyglądać następująco:

    :::image type="content" source="./media/search-howto-powerapps/2-5-controls-layout.png" alt-text="Układ formantów" border="true":::

1. Aby **przycisk wyszukiwania** wystawił zapytanie, wklej następującą akcję do **opcji OnSelect**:

    ```
    If(!IsBlank(txtQuery.Text),
        ClearCollect(azResult, AzureSearchQuery.Query({search: txtQuery.Text}).value))
    ```

   Poniższy zrzut ekranu przedstawia pasek formuły akcji **OnSelect** .

    :::image type="content" source="./media/search-howto-powerapps/2-6-search-button-event.png" alt-text="Przycisk OnSelect" border="true":::

   Ta akcja spowoduje aktualizację nowej kolekcji o nazwie *azResult* z wynikiem zapytania wyszukiwania, przy użyciu tekstu w polu tekstowym *txtQuery* jako terminu zapytania.

   > [!NOTE]
   > Wypróbuj, jeśli wystąpi błąd składniowy formuły "funkcja" ClearCollect "zawiera nieprawidłowe funkcje":
   > 
   > * Najpierw upewnij się, że odwołanie do łącznika jest poprawne. Wyczyść nazwę łącznika i rozpocznij wpisywanie nazwy łącznika. Funkcja IntelliSense powinna zasugerować właściwy łącznik i zlecenie.
   > 
   > * Jeśli błąd będzie się powtarzać, Usuń i Utwórz ponownie łącznik. Jeśli istnieje wiele wystąpień łącznika, aplikacja może korzystać z niewłaściwego.
   > 

1. Połącz formant galerii pionowej z kolekcją *azResult* , która została utworzona po zakończeniu poprzedniego kroku. 

   Wybierz kontrolkę Galeria i wykonaj następujące czynności w okienku właściwości.

   * Ustaw wartość **DataSource** na *azResult*.
   * Wybierz **Układ** , który działa w oparciu o typ danych w indeksie. W takim przypadku użyto *tytułu, podtytuł i układu treści* .
   * **Edytuj pola** i wybierz pola, które chcesz wizualizować.

    Ze względu na to, że podczas definiowania łącznika podano przykładowy wynik, aplikacja rozpoznaje pola dostępne w indeksie.
    
    :::image type="content" source="./media/search-howto-powerapps/2-7-gallery-select-fields.png" alt-text="Pola galerii" border="true":::   
 
1. Naciśnij klawisz **F5** , aby wyświetlić podgląd aplikacji.  

    :::image type="content" source="./media/search-howto-powerapps/2-8-3-final.png" alt-text="Końcowa aplikacja" border="true":::    

<!--     Remember that the fields can be set to calculated values.

    For the example, setting using the *"Image, Title and Subtitle"* layout and specifying the *Image* function as the concatenation of the root path for the data and the file name (for instance, `"https://mystore.blob.core.windows.net/multilang/" & ThisItem.metadata_storage_name`) will produce the result below.

    :::image type="content" source="./media/search-howto-powerapps/2-8-2-final.png" alt-text="Final app" border="true":::         -->

## <a name="clean-up-resources"></a>Czyszczenie zasobów

W przypadku pracy w ramach własnej subskrypcji warto sprawdzić po zakończeniu projektu, czy dalej potrzebuje się utworzonych zasobów. Uruchomione zasoby mogą generować koszty. Zasoby możesz usuwać pojedynczo lub możesz usunąć grupę zasobów, aby usunąć cały ich zestaw.

Zasoby można znaleźć w portalu i zarządzać nimi za pomocą linku **wszystkie zasoby** lub **grupy zasobów** w okienku nawigacji po lewej stronie.

Jeśli używasz bezpłatnej usługi, pamiętaj, że masz ograniczone do trzech indeksów, indeksatorów i źródeł danych. Możesz usunąć poszczególne elementy w portalu, aby zachować limit.

## <a name="next-steps"></a>Następne kroki

Aplikacje zaawansowane umożliwiają szybkie tworzenie aplikacji niestandardowych. Teraz, gdy wiesz już, jak nawiązać połączenie z indeksem wyszukiwania, Dowiedz się więcej na temat tworzenia bogatego środowiska wizualizacji w niestandardowej aplikacji zaawansowanej.

> [!div class="nextstepaction"]
> [Katalog uczenia aplikacji zaawansowanych](/powerapps/learning-catalog/get-started)