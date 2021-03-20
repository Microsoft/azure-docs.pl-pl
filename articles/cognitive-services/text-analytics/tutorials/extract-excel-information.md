---
title: Wyodrębnij informacje w programie Excel przy użyciu analiza tekstu i automatyzacji
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak wyodrębnić tekst programu Excel bez konieczności pisania kodu przy użyciu analiza tekstu i automatyzacji.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 02/27/2019
ms.author: aahi
ms.openlocfilehash: 197d28b2ac3d94b6639a6611b2919bdeb2b182e2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93359905"
---
# <a name="extract-information-in-excel-using-text-analytics-and-power-automate"></a>Wyodrębnij informacje w programie Excel przy użyciu analiza tekstu i automatyzacji 

W tym samouczku utworzysz przepływ automatyzacji, aby wyodrębnić tekst w arkuszu programu Excel bez konieczności pisania kodu. 

Ten przepływ zajmie arkuszem kalkulacyjnym problemów o złożonej złożoności i klasyfikuje je do dwóch kategorii: Instalacja wodociągowa i inne. Spowoduje to również wyodrębnienie nazw i numerów telefonów dzierżawców, którzy je przysłał. Na koniec przepływ doda te informacje do arkusza programu Excel. 

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
> * Używanie automatyzacji w celu utworzenia przepływu
> * Przekazywanie danych programu Excel z usługi OneDrive dla firm
> * Wyodrębnij tekst z programu Excel i wyślij go do interfejs API analizy tekstu 
> * Użyj informacji z interfejsu API, aby zaktualizować arkusz programu Excel.

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Microsoft Azure. [Utwórz bezpłatne konto](https://azure.microsoft.com/free/cognitive-services/) lub [Zaloguj się](https://portal.azure.com/).
- Zasób analiza tekstu. Jeśli go nie masz, możesz go [utworzyć w Azure Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) i skorzystać z warstwy Bezpłatna, aby ukończyć ten samouczek.
- [Klucz i punkt końcowy](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) , który został wygenerowany dla Ciebie podczas rejestracji.
- Arkusz kalkulacyjny zawierający problemy z dzierżawcą. Przykładowe dane są dostępne w serwisie GitHub
- Microsoft 365 z usługą OneDrive dla firm.

## <a name="add-the-excel-file-to-onedrive-for-business"></a>Dodawanie pliku programu Excel do usługi OneDrive dla firm

Pobierz przykładowy plik programu Excel z usługi [GitHub](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/TextAnalytics/sample-data/ReportedIssues.xlsx). Ten plik musi być przechowywany na koncie usługi OneDrive dla firm.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/example-data.png" alt-text="Przykłady z pliku programu Excel.":::

Problemy są raportowane w tekście nieprzetworzonym. Użyjemy rozpoznawania nazwanych jednostek interfejs API analizy tekstu, aby wyodrębnić nazwisko osoby i numer telefonu. Następnie w opisie zostanie wyszukany wyraz "Instalacja wodociągowa" w celu skategoryzowania problemów. 

## <a name="create-a-new-power-automate-workflow"></a>Utwórz nowy przepływ pracy automatyzacji

Przejdź do [witryny automatyzacji](https://preview.flow.microsoft.com/)i zaloguj się. Następnie kliknij pozycję **Utwórz** i **zaplanowana przepływ**.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/flow-creation.png" alt-text="Ekran tworzenia przepływu.":::


Na stronie **Tworzenie zaplanowanego przepływu** zainicjuj przepływ przy użyciu następujących pól:

|Pole |Wartość  |
|---------|---------|
|**Nazwa przepływu**     | **Zaplanowana Recenzja** lub inna nazwa.         |
|**Uruchamianie**     |  Wprowadź bieżącą datę i godzinę.       |
|**Powtarzaj co**     | **1 godzina**        |

## <a name="add-variables-to-the-flow"></a>Dodawanie zmiennych do przepływu

> [!NOTE]
> Jeśli chcesz zobaczyć obraz ukończonego przepływu, możesz go pobrać z witryny [GitHub](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/TextAnalytics/flow-diagrams). 

Utwórz zmienne reprezentujące informacje, które zostaną dodane do pliku programu Excel. Kliknij przycisk **nowy krok** i Wyszukaj pozycję **zainicjuj zmienną**. Zrób to cztery razy, aby utworzyć cztery zmienne.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/initialize-variables.png" alt-text="Inicjuj zmienne.":::

Dodaj następujące informacje do utworzonych zmiennych. Reprezentują one kolumny pliku programu Excel. Jeśli jakiekolwiek zmienne są zwinięte, możesz je kliknąć, aby je rozwinąć.

| Akcja |Nazwa   | Typ | Wartość |
|---------|---------|---|---|
| Zainicjuj zmienną | var_person | Ciąg | Person (Osoba) |
| Zainicjuj zmienną 2 | var_phone | Ciąg | Phone_Number |
| Zainicjuj zmienną 3 | var_plumbing | Ciąg | nadmiar |
| Zainicjuj zmienną 4 | var_other | Ciąg | other | 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/flow-variables.png" alt-text="Informacje zawarte w zmiennych przepływu":::

## <a name="read-the-excel-file"></a>Odczytaj plik programu Excel

Kliknij pozycję **nowy krok** i wpisz polecenie **Excel**, a następnie wybierz pozycję **Lista wierszy znajdujących się w tabeli** z listy akcji.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/list-excel-rows.png" alt-text="Dodaj wiersze programu Excel.":::

Dodaj plik programu Excel do przepływu, wypełniając pola w tej akcji. Ten samouczek wymaga przekazania pliku do usługi OneDrive dla firm.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/list-excel-rows-options.png" alt-text="Wypełnij wiersze programu Excel":::

Kliknij pozycję **nowy krok** i Dodaj akcję **Zastosuj do każdego** .

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-apply-action.png" alt-text="Dodaj polecenie Apply.":::

Kliknij pozycję **Wybierz dane wyjściowe z poprzedniego kroku**. W wyświetlonym polu zawartość dynamiczna wybierz pozycję **wartość**.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/select-output.png" alt-text="Wybierz pozycję dane wyjściowe z pliku programu Excel.":::

## <a name="send-a-request-to-the-text-analytics-api"></a>Wyślij żądanie do interfejs API analizy tekstu

Jeśli jeszcze tego nie zrobiono, należy utworzyć [zasób analiza tekstu](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) w Azure Portal.

### <a name="create-a-text-analytics-connection"></a>Utwórz połączenie analiza tekstu

W obszarze **Zastosuj do każdego** kliknij pozycję **Dodaj akcję**. Przejdź do strony **klucza i punktu końcowego** zasobu analiza tekstu w Azure Portal, a następnie Pobierz klucz i punkt końcowy dla zasobu analiza tekstu.

W przepływie Wprowadź poniższe informacje, aby utworzyć nowe połączenie analiza tekstu.

> [!NOTE]
> Jeśli utworzono już połączenie analiza tekstu i chcesz zmienić szczegóły połączenia, kliknij przycisk wielokropka w prawym górnym rogu, a następnie kliknij pozycję **+ Dodaj nowe połączenie**.

| Pole           | Wartość                                                                                                             |
|-----------------|-------------------------------------------------------------------------------------------------------------------|
| Nazwa połączenia | Nazwa połączenia z zasobem analiza tekstu. Na przykład `TAforPowerAutomate`. |
| Klucz konta     | Klucz dla zasobu analiza tekstu.                                                                                   |
| Adres URL witryny        | Punkt końcowy dla zasobu analiza tekstu.                                                       |

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-credentials.png" alt-text="Dodaj poświadczenia analiza tekstu do przepływu.":::

## <a name="extract-the-excel-content"></a>Wyodrębnij zawartość programu Excel 

Po utworzeniu połączenia Wyszukaj **Analiza tekstu** i wybierz pozycję **jednostki**. Spowoduje to wyodrębnienie informacji z kolumny opis problemu.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/extract-info.png" alt-text="Dodaj jednostki analiza tekstu.":::

Kliknij pole **tekstowe** i wybierz pozycję **Opis** w wyświetlonych oknach zawartości dynamicznej. Wprowadź `en` dla języka. (Kliknij przycisk Pokaż opcje zaawansowane, jeśli nie widzisz języka)

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/description-from-dynamic-content.png" alt-text="Dodaj ustawienia analiza tekstu.":::


## <a name="extract-the-person-name"></a>Wyodrębnij nazwisko osoby

Następnie odnajdziemy typ podmiotu osoby w analiza tekstu danych wyjściowych. W obszarze **Zastosuj do każdego** kliknij pozycję **Dodaj akcję** i Utwórz inną akcję **Zastosuj do poszczególnych** akcji. Kliknij wewnątrz pola tekstowego i wybierz pozycję **jednostki** w oknie zawartość dynamiczna, która zostanie wyświetlona.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-apply-action-2.png" alt-text="Dodaj poświadczenia analiza tekstu do przepływu. dwóch":::

W ramach nowo utworzonego działania **Zastosuj do każdego 2** kliknij przycisk **Dodaj akcję** i Dodaj kontrolkę **warunek** .

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/create-condition.png" alt-text="Dodaj poświadczenia analiza tekstu do przepływu. r.3":::

W oknie warunek kliknij pierwsze pole tekstowe. W oknie zawartość dynamiczna Wyszukaj pozycję **typ jednostek** i wybierz ją.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/choose-entities-value.png" alt-text="Dodaj poświadczenia analiza tekstu do przepływu. czwart":::

Upewnij się, że drugie pole ma ustawioną wartość **równą**. Następnie wybierz trzecie pole i Wyszukaj `var_person` w oknie zawartość dynamiczna. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/choose-variable-value.png" alt-text="Dodaj poświadczenia analiza tekstu do przepływu. 5000":::

W warunku **if Yes** wpisz w programie Excel, a następnie wybierz pozycję **Aktualizuj wiersz**.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/yes-column-action.png" alt-text="Dodaj poświadczenia analiza tekstu do przepływu. ust":::

Wprowadź informacje w programie Excel i zaktualizuj **kolumny klucza**, **wartość klucza** i **imię** . Spowoduje to dołączenie nazwy wykrytej przez interfejs API do arkusza programu Excel. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/yes-column-action-options.png" alt-text="Dodaj poświadczenia analiza tekstu do przepływu. 7":::

## <a name="get-the-phone-number"></a>Pobierz numer telefonu

Zminimalizuj akcję **Zastosuj do każdego 2** , klikając nazwę. Następnie Dodaj kolejną akcję **Zastosuj do każdej** akcji, na przykład przed. nazwa ma **zastosowanie do każdego 3**. Zaznacz pole tekstowe i Dodaj **jednostki** jako dane wyjściowe tej akcji. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-apply-action-3.png" alt-text="Dodaj poświadczenia analiza tekstu do przepływu. 0,8":::

W obszarze **Zastosuj do każdego 3** Dodaj kontrolkę **warunek** . Zostanie nazwany **warunek 2**. W pierwszym polu tekstowym Wyszukaj i Dodaj **typ jednostek** z okna zawartość dynamiczna. Upewnij się, że pole środkowe ma ustawioną wartość **równą**. Następnie w prawym polu tekstowym wprowadź `var_phone` . 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/condition-2-options.png" alt-text="Dodaj poświadczenia analiza tekstu do przepływu. 9":::

W warunku **Jeśli tak** , Dodaj akcję **Aktualizuj wiersz** . Następnie wprowadź powyższe informacje w kolumnie numery telefonów arkusza programu Excel. Spowoduje to dołączenie numeru telefonu wykrytego przez interfejs API do arkusza programu Excel. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/condition-2-yes-column.png" alt-text="Dodaj poświadczenia analiza tekstu do przepływu. dziesięć":::


## <a name="get-the-plumbing-issues"></a>Uzyskaj problemy z instalowaniem

Minimalizuj **Zastosuj do każdego 3** , klikając nazwę. Następnie utwórz inny **Zastosuj do każdego** w akcji nadrzędnej. Zaznacz pole tekstowe i Dodaj **jednostki** jako dane wyjściowe tej akcji z okna zawartość dynamiczna. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-apply-action-4.png" alt-text="Dodaj poświadczenia analiza tekstu do przepływu. 11":::


Następnie przepływ sprawdzi, czy opis problemu w wierszu tabeli programu Excel zawiera słowo "instalacja". Jeśli tak, spowoduje to dodanie "instalacji wodociągowej" w kolumnie wystawca. W przeciwnym razie wprowadzimy "inne".

Wewnątrz akcji **Zastosuj do każdego 4** Dodaj kontrolkę **warunek** . Zostanie nazwany **warunek 3**. W pierwszym polu tekstowym Wyszukaj i Dodaj **Opis** z pliku programu Excel przy użyciu okna zawartość dynamiczna. Upewnij się, że pole środkowe **zawiera**. Następnie w prawym polu tekstowym Znajdź i wybierz `var_plumbing` . 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/condition-3-options.png" alt-text="Dodaj poświadczenia analiza tekstu do przepływu. dwunastomiesięcznych":::


W warunku **if Yes** kliknij pozycję **Dodaj akcję**, a następnie wybierz pozycję **Aktualizuj wiersz**. Następnie wprowadź informacje, jak poprzednio. W kolumnie wystawca wybierz pozycję `var_plumbing` . Spowoduje to zastosowanie do wiersza etykiety "Instalacja wodociągowa".

W **przypadku braku** warunku kliknij pozycję **Dodaj akcję**, a następnie wybierz pozycję **Aktualizuj wiersz**. Następnie wprowadź informacje, jak poprzednio. W kolumnie wystawca wybierz pozycję `var_other` . Spowoduje to zastosowanie etykiety "Other" do wiersza.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/plumbing-issue-condition.png" alt-text="Dodaj poświadczenia analiza tekstu do przepływu. trzynast":::

## <a name="test-the-workflow"></a>Testowanie przepływu pracy

W prawym górnym rogu ekranu kliknij przycisk **Zapisz**, a następnie **Testuj**. Wybierz opcję  **Chcę wykonać akcję wyzwalacza**. Kliknij przycisk **zapisz & test**, **Uruchom przepływ**, a następnie **gotowe**.

Plik programu Excel zostanie zaktualizowany na koncie w usłudze OneDrive. Będzie wyglądać tak jak poniżej.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/updated-excel-sheet.png" alt-text="Zaktualizowany arkusz kalkulacyjny programu Excel.":::

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Poznaj więcej rozwiązań](../text-analytics-user-scenarios.md)
