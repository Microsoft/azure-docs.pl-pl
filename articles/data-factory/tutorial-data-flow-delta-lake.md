---
title: Delta lake ETL z przepływami danych
description: Ten samouczek zawiera instrukcje krok po kroku dotyczące używania przepływów danych do przekształcania i analizowania danych w u usługi Delta Lake
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2021
ms.date: 04/16/2021
ms.openlocfilehash: 4a88ed2df74d3eebb96c42e2cdc87b14153419cd
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107565376"
---
# <a name="transform-data-in-delta-lake-using-mapping-data-flows"></a>Przekształcanie danych w u usługi Delta Lake przy użyciu przepływów danych mapowania

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Jeśli jesteś nowym użytkownikiem usługi Azure Data Factory, zobacz [Wprowadzenie do usługi Azure Data Factory](introduction.md).

W tym samouczku użyjesz kanwy przepływu danych do utworzenia przepływów danych, które umożliwią analizowanie i przekształcanie danych w uście usługi Azure Data Lake Storage (ADLS) Gen2 i przechowywanie ich w u usługi Delta Lake.

## <a name="prerequisites"></a>Wymagania wstępne
* **Subskrypcja platformy Azure.** Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz bezpłatne konto platformy [Azure.](https://azure.microsoft.com/free/)
* **Konto usługi Azure Storage.** Magazyn usługi ADLS jest magazynem danych *źródła* i ujścia.  Jeśli nie masz konta magazynu, utwórz je, wykonując czynności przedstawione w artykule [Tworzenie konta magazynu platformy Azure](../storage/common/storage-account-create.md).

Plik, który przekształcamy w tym samouczku, to MoviesDB.csv, który można znaleźć [tutaj.](https://github.com/kromerm/adfdataflowdocs/blob/master/sampledata/moviesDB2.csv) Aby pobrać plik z usługi GitHub, skopiuj jego zawartość do wybranego edytora tekstów, aby zapisać go lokalnie jako plik CSV. Aby przekazać plik na konto magazynu, zobacz [Przekazywanie obiektów blob za pomocą Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md). Przykłady będą odwoływać się do kontenera o nazwie "sample-data".

## <a name="create-a-data-factory"></a>Tworzenie fabryki danych

W tym kroku utworzysz fabrykę danych i otworzysz Data Factory użytkownika, aby utworzyć potok w fabryce danych.

1. Otwórz przeglądarkę **Microsoft Edge** lub **Google Chrome**. Obecnie interfejs Data Factory użytkownika jest obsługiwany tylko w przeglądarkach internetowych Microsoft Edge i Google Chrome.
1. W menu po lewej stronie wybierz **pozycję Utwórz zasób**  >  **Integracja**  >  **Data Factory**
1. Na stronie **Nowa fabryka danych** w obszarze **Nazwa** wprowadź **wartość ADFTutorialDataFactory**
1. Wybierz **subskrypcję** platformy Azure, w której chcesz utworzyć fabrykę danych.
1. W obszarze **Grupa zasobów** wykonaj jedną z następujących czynności:

    a. Wybierz pozycję **Użyj istniejącej**, a następnie wybierz istniejącą grupę zasobów z listy rozwijanej.

    b. Wybierz **pozycję Utwórz** nową i wprowadź nazwę grupy zasobów. 
         
    Informacje na temat grup zasobów znajdują się w artykule [Using resource groups to manage your Azure resources (Używanie grup zasobów do zarządzania zasobami platformy Azure)](../azure-resource-manager/management/overview.md). 
1. W obszarze **Wersja** wybierz pozycję **V2**.
1. W obszarze **Lokalizacja** wybierz lokalizację fabryki danych. Na liście rozwijanej są wyświetlane tylko obsługiwane lokalizacje. Magazyny danych (na przykład usługi Azure Storage i SQL Database) oraz jednostki obliczeniowe (na przykład Azure HDInsight) używane przez fabrykę danych mogą być w innych regionach.
1. Wybierz przycisk **Utwórz**.
1. Po zakończeniu tworzenia zobaczysz powiadomienie w Centrum powiadomień. Wybierz **pozycję Przejdź do zasobu,** aby przejść do strony Fabryka danych.
1. Wybierz pozycję **Tworzenie i monitorowanie**, aby uruchomić interfejs użytkownika usługi Data Factory na osobnej karcie.

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>Tworzenie potoku z działaniem przepływu danych

W tym kroku utworzysz potok zawierający działanie przepływu danych.

1. Na stronie **Zaczynajmy** wybierz pozycję **Utwórz potok**.

   ![Tworzenie potoku](./media/doc-common-process/get-started-page.png)

1. Na karcie **Ogólne** potoku wprowadź **wartość DeltaLake** **w obszarze Nazwa** potoku.
1. W **okienku Działania** rozwiń accordion **Przenieś i Przekształć.** Przeciągnij i upuść **Przepływ danych** z okienka na kanwę potoku.

    ![Zrzut ekranu przedstawiający kanwę potoku, na której można usunąć Przepływ danych działania.](media/tutorial-data-flow/activity1.png)
1. W **oknie podręcznym Dodawanie Przepływ danych** wybierz pozycję **Utwórz** nowy Przepływ danych a następnie nadaj przepływowi danych **nazwę DeltaLake.** Po zakończeniu kliknij przycisk Zakończ.

    ![Zrzut ekranu przedstawiający miejsce, w którym nazywasz przepływ danych podczas tworzenia nowego przepływu danych.](media/tutorial-data-flow/activity2.png)
1. Na górnym pasku kanwy potoku przesuń suwak Przepływ danych **debugowania.** Tryb debugowania umożliwia interaktywne testowanie logiki przekształcania względem klastra Spark na żywo. Przepływ danych rozgrzewki w klastrach potrwają 5–7 minut, a użytkownicy zalecają włączenie debugowania najpierw, jeśli planują Przepływ danych dewelopera. Aby uzyskać więcej informacji, zobacz [Tryb debugowania](concepts-data-flow-debug-mode.md).

    ![Zrzut ekranu przedstawiający miejsce suwaka debugowania przepływu danych.](media/tutorial-data-flow/dataflow1.png)

## <a name="build-transformation-logic-in-the-data-flow-canvas&quot;></a>Tworzenie logiki przekształcania na kanwie przepływu danych

W tym samouczku zostaną wygenerowane dwa przepływy danych. Pierwszy przepływ danych jest prostym źródłem do ujścia w celu wygenerowania nowego rozszerzenia Delta Lake na podstawie powyższego pliku CSV filmów. Na koniec utworzysz ten projekt przepływu poniżej, aby zaktualizować dane w u usługi Delta Lake.

![Końcowy przepływ](media/data-flow/data-flow-tutorial-6.png &quot;Końcowy przepływ")

### <a name="tutorial-objectives"></a>Cele samouczka

1. Skorzystaj ze źródła zestawu danych MoviesCSV z powyższych źródeł, tworząc z niego nową ujmę delta lake
1. Tworzenie logiki w celu zaktualizowania klasyfikacji filmów z 1988 r. do wartości "1"
1. Usuwanie wszystkich filmów z 1950 r.
1. Wstawianie nowych filmów na rok 2021 przez duplikowanie filmów z 1960 r.

### <a name="start-from-a-blank-data-flow-canvas"></a>Rozpoczynanie od pustej kanwy przepływu danych

1. Kliknij przekształcenie źródła
1. Kliknij pozycję nowy obok zestawu danych na dolnym panelu 1 Tworzenie nowej połączonej usługi dla ADLS Gen2
1. Wybierz tekst rozdzielany dla typu zestawu danych
1. Nadaj zestawowi danych nazwę "MoviesCSV" 
1. Wskaż plik MoviesCSV przekazany do magazynu powyżej
1. Ustaw go jako rozdzielany przecinkami i dołącz nagłówek w pierwszym wierszu 
1. Przejdź do karty projekcji źródłowej i kliknij pozycję "Wykryj typy danych"
1. Po skonfigurowaniu projekcji możesz kontynuować 
1. Dodawanie przekształcenia ujścia
1. Delta to typ wbudowanego zestawu danych. Musisz wskazać konto magazynu ADLS Gen2 magazynu.
   
   ![Zestaw danych w tekście](media/data-flow/data-flow-tutorial-5.png "Zestaw danych w tekście")

1. Wybierz nazwę folderu w kontenerze magazynu, w którym chcesz, aby usługi ADF tworzyły magazyn Delta Lake
1. Wstecz do projektanta potoku i kliknij pozycję Debuguj, aby wykonać potok w trybie debugowania przy użyciu tylko tego działania przepływu danych na kanwie. Spowoduje to wygenerowanie nowego konta delta Lake w ADLS Gen2.
1. W zasobach fabryki kliknij nowy przepływ > Danych 
1. Użyj ponownie aplikacji MoviesCSV jako źródła i ponownie kliknij pozycję "Wykryj typy danych"
1. Dodawanie przekształcenia filtru do przekształcenia źródła w grafie
1. Zezwalaj tylko na wiersze filmów zgodne z trzema latami, z którymi będziesz pracować, czyli w latach 1950, 1988 i 1960
1. Zaktualizuj klasyfikacje dla każdego filmu z 1988 r. do wartości "1", dodając teraz pochodne przekształcenie kolumny do przekształcenia filtru
1. W tej samej kolumnie pochodnej utwórz filmy na rok 2021, biorąc istniejący rok i zmień rok na 2021. Wybierzmy rok 1960.
1. Tak będą wyglądać trzy kolumny pochodne

   ![Kolumna pochodna](media/data-flow/data-flow-tutorial-2.png "Kolumna pochodna")
   
1. ```Update, insert, delete, and upsert``` Zasady są tworzone w przekształceniach alter Row. Dodaj przekształcenie zmiany wiersza po kolumnie pochodnej.
1. Twoje zasady zmiany wiersza powinny wyglądać tak.

   ![Modyfikuj wiersz](media/data-flow/data-flow-tutorial-3.png "Modyfikuj wiersz")
   
1. Teraz, po skonfigurowaniu odpowiednich zasad dla każdego typu zmiany wiersza, sprawdź, czy dla przekształcenia ujścia zostały ustawione odpowiednie reguły aktualizacji

   ![Ujście](media/data-flow/data-flow-tutorial-4.png "Ujście")
   
1. W tym miejscu używamy ujścia usługi Delta Lake do ADLS Gen2 data lake i umożliwiamy wstawianie, aktualizacje i usuwanie. 
1. Należy pamiętać, że kolumna Kolumny kluczy jest kluczem złożonym, który składa się z kolumny Klucz podstawowy i Rok filmu. Wynika to z tego, że utworzono fałszywe filmy z 2021 roku przez duplikowanie wierszy z 1960 roku. Pozwala to uniknąć kolizji podczas szukania istniejących wierszy przez zapewnienie unikatowości.

### <a name="download-completed-sample"></a>Pobieranie ukończonego przykładu
[Oto przykładowe rozwiązanie dla potoku delty z przepływem danych dla wierszy aktualizacji/usuwania w ujechach usługi Delta:](https://github.com/kromerm/adfdataflowdocs/blob/master/sampledata/DeltaPipeline.zip)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [języku wyrażeń przepływu danych](data-flow-expression-functions.md).
