---
title: Przekształcanie danych przy użyciu przepływu danych mapowania
description: Ten samouczek zawiera instrukcje krok po kroku dotyczące korzystania z Azure Data Factory do przekształcania danych przy użyciu przepływu danych mapowania
author: dcstwh
ms.author: weetok
ms.reviewer: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/09/2019
ms.openlocfilehash: b0f564f68a638e7efd1cd1ce9116a26f4d19f277
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96497065"
---
# <a name="transform-data-using-mapping-data-flows"></a>Przekształcanie danych przy użyciu przepływów danych mapowania

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Jeśli jesteś nowym użytkownikiem usługi Azure Data Factory, zobacz [Wprowadzenie do usługi Azure Data Factory](introduction.md).

W tym samouczku użyjesz interfejsu użytkownika Azure Data Factory, aby utworzyć potok, który kopiuje i przekształca dane ze źródła Azure Data Lake Storage (ADLS) Gen2 do ujścia ADLS Gen2 przy użyciu przepływu danych mapowania. Wzorzec konfiguracji w tym samouczku można rozszerzyć przy przekształcaniu danych przy użyciu przepływu danych mapowania

W tym samouczku wykonasz następujące czynności:

> [!div class="checklist"]
> * Tworzenie fabryki danych.
> * Utwórz potok z działaniem przepływu danych.
> * Kompiluj przepływ danych mapowania z czterema przekształceniami.
> * Testowe uruchamianie potoku.
> * Monitorowanie działania przepływu danych

## <a name="prerequisites"></a>Wymagania wstępne
* **Subskrypcja platformy Azure**. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/) .
* **Konto usługi Azure Storage**. Magazyn ADLS jest używany jako magazyn danych *źródłowych* i *ujścia* . Jeśli nie masz konta magazynu, utwórz je, wykonując czynności przedstawione w artykule [Tworzenie konta magazynu platformy Azure](../storage/common/storage-account-create.md).

Plik, który jest przekształcany w tym samouczku, jest MoviesDB.csv, który można znaleźć [tutaj](https://raw.githubusercontent.com/djpmsft/adf-ready-demo/master/moviesDB.csv). Aby pobrać plik z usługi GitHub, skopiuj zawartość do wybranego edytora tekstu, aby zapisać ją lokalnie jako plik CSV. Aby przekazać plik do konta magazynu, zobacz [przekazywanie obiektów BLOB za pomocą Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md). Przykłady odwołują się do kontenera o nazwie "przykładowe dane".

## <a name="create-a-data-factory"></a>Tworzenie fabryki danych

W tym kroku utworzysz fabrykę danych i otworzysz środowisko Data Factory, aby utworzyć potok w fabryce danych.

1. Otwórz przeglądarkę **Microsoft Edge** lub **Google Chrome**. Obecnie interfejs użytkownika Data Factory jest obsługiwany tylko w przeglądarkach sieci Web Microsoft Edge i Google Chrome.
2. W menu po lewej stronie wybierz pozycję **Utwórz zasób**  >  **integracja**  >  **Data Factory**:

   ![Wybór usługi Data Factory w okienku „Nowy”](./media/doc-common-process/new-azure-data-factory-menu.png)

3. Na stronie **Nowa fabryka danych** w polu **Nazwa** wprowadź wartość **ADFTutorialDataFactory**.

   Nazwa fabryki danych Azure musi być *globalnie unikatowa*. Jeśli zostanie wyświetlony komunikat o błędzie dotyczącym wartości nazwy, wprowadź inną nazwę dla fabryki danych. (na przykład Twojanazwaadftutorialdatafactory). Reguły nazewnictwa dla artefaktów usługi Data Factory można znaleźć w artykule [Data Factory — reguły nazewnictwa](naming-rules.md).

     ![Nowa fabryka danych](./media/doc-common-process/name-not-available-error.png)
4. Wybierz **subskrypcję** platformy Azure, w której chcesz utworzyć fabrykę danych.
5. W obszarze **Grupa zasobów** wykonaj jedną z następujących czynności:

    a. Wybierz pozycję **Użyj istniejącej**, a następnie wybierz istniejącą grupę zasobów z listy rozwijanej.

    b. Wybierz pozycję **Utwórz nową**, a następnie wprowadź nazwę grupy zasobów. 
         
    Informacje na temat grup zasobów znajdują się w artykule [Using resource groups to manage your Azure resources (Używanie grup zasobów do zarządzania zasobami platformy Azure)](../azure-resource-manager/management/overview.md). 
6. W obszarze **Wersja** wybierz pozycję **V2**.
7. W obszarze **Lokalizacja** wybierz lokalizację fabryki danych. Na liście rozwijanej są wyświetlane tylko obsługiwane lokalizacje. Magazyny danych (np. usługi Azure Storage i SQL Database) i obliczenia (na przykład usługa Azure HDInsight) używane przez fabrykę danych mogą znajdować się w innych regionach.
8. Wybierz pozycję **Utwórz**.
9. Po zakończeniu tworzenia zobaczysz powiadomienie w centrum powiadomień. Wybierz pozycję **Przejdź do zasobu** , aby przejść do strony Fabryka danych.
10. Wybierz pozycję **Tworzenie i monitorowanie**, aby uruchomić interfejs użytkownika usługi Data Factory na osobnej karcie.

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>Tworzenie potoku za pomocą działania przepływu danych

W tym kroku utworzysz potok zawierający działanie przepływu danych.

1. Na stronie **Zaczynajmy** wybierz pozycję **Utwórz potok**.

   ![Tworzenie potoku](./media/doc-common-process/get-started-page.png)

1. Na karcie **Ogólne** potoku wpisz **TransformMovies** dla **nazwy** potoku.
1. Na górnym pasku fabryki przesuń suwak **debugowania przepływu danych** na. Tryb debugowania umożliwia interaktywne Testowanie logiki transformacji na aktywnym klastrze Spark. Klastry przepływu danych zajmują 5-7 minut na rozgrzanie i zaleca się włączenie najpierw debugowania, jeśli planują one opracowywanie przepływu danych. Aby uzyskać więcej informacji, zobacz [tryb debugowania](concepts-data-flow-debug-mode.md).

    ![Działanie przepływu danych](media/tutorial-data-flow/dataflow1.png)
1. W okienku **działania** rozwiń pozycję **Przenieś i Przekształć** . Przeciągnij i upuść działanie **przepływu danych** z okienka do kanwy potoku.

    ![Zrzut ekranu przedstawiający kanwę potoku, w której można usunąć działanie przepływu danych.](media/tutorial-data-flow/activity1.png)
1. W oknie podręcznym **Dodawanie przepływu danych** wybierz pozycję **Utwórz nowy przepływ danych** , a następnie nadaj nazwę przepływowi danych **TransformMovies**. Po zakończeniu kliknij przycisk Zakończ.

    ![Zrzut ekranu pokazujący lokalizację przepływu danych podczas tworzenia nowego przepływu danych.](media/tutorial-data-flow/activity2.png)

## <a name="build-transformation-logic-in-the-data-flow-canvas"></a>Logika transformacji kompilacji na kanwie przepływu danych

Po utworzeniu przepływu danych zostanie on automatycznie wysłany do kanwy przepływu danych. W tym kroku utworzysz przepływ danych, który przyjmuje moviesDB.csv w magazynie ADLS i agreguje średnią ocenę Comedies od 1910 do 2000. Następnie zapiszesz ten plik z powrotem do magazynu ADLS.

1. Na kanwie przepływu danych Dodaj źródło, klikając je w polu **Dodaj źródło** .

    ![Zrzut ekranu przedstawiający pole Dodaj źródło.](media/tutorial-data-flow/dataflow2.png)
1. Nadaj nazwę źródłową **MoviesDB**. Kliknij pozycję **Nowy** , aby utworzyć nowy źródłowy zestaw danych.

    ![Zrzut ekranu pokazujący, gdzie wybierać nowe po nazwie źródła.](media/tutorial-data-flow/dataflow3.png)
1. Wybierz **Azure Data Lake Storage Gen2**. Kliknij przycisk Kontynuuj.

    ![Zrzut ekranu przedstawiający kafelek Azure Data Lake Storage Gen2.](media/tutorial-data-flow/dataset1.png)
1. Wybierz **DelimitedText**. Kliknij przycisk Kontynuuj.

    ![Zrzut ekranu przedstawiający kafelek DelimitedText.](media/tutorial-data-flow/dataset2.png)
1. Nazwij zestaw danych **MoviesDB**. Z listy rozwijanej połączona usługa wybierz pozycję **Nowy**.

    ![Zrzut ekranu przedstawiający listę rozwijaną połączonej usługi.](media/tutorial-data-flow/dataset3.png)
1. Na ekranie Tworzenie połączonej usługi Nadaj nazwę ADLS Gen2 połączonej usłudze **ADLSGen2** i Określ metodę uwierzytelniania. Następnie wprowadź poświadczenia połączenia. W tym samouczku używamy klucza konta do nawiązywania połączenia z naszym kontem magazynu. Możesz kliknąć przycisk **Test connection** , aby sprawdzić, czy poświadczenia zostały wprowadzone poprawnie. Po zakończeniu kliknij przycisk Utwórz.

    ![Połączona usługa](media/tutorial-data-flow/ls1.png)
1. Po ponownym uruchomieniu ekranu tworzenia zestawu danych wprowadź miejsce, w którym znajduje się plik w polu **ścieżka pliku** . W tym samouczku plik moviesDB.csv znajduje się w kontenerze przykład — dane. Ponieważ plik ma nagłówki, zaznacz **pierwszy wiersz jako nagłówek**. Wybierz pozycję **połączenie/magazyn** , aby zaimportować schemat nagłówka bezpośrednio z pliku w magazynie. Po zakończeniu kliknij przycisk OK.

    ![Zestawy danych](media/tutorial-data-flow/dataset4.png)
1. Jeśli klaster debugowania został uruchomiony, przejdź do karty **Podgląd danych** transformacji źródłowej i kliknij przycisk **Odśwież** , aby uzyskać migawkę danych. Możesz użyć podglądu danych, aby sprawdzić, czy transformacja została prawidłowo skonfigurowana.

    ![Zrzut ekranu pokazujący, gdzie można wyświetlić podgląd danych, aby sprawdzić, czy transformacja została prawidłowo skonfigurowana.](media/tutorial-data-flow/dataflow4.png)
1. Kliknij ikonę znaku plus, aby dodać nową transformację obok węzła źródłowego na kanwie przepływu danych. Pierwszy dodawany przekształcenie to **Filtr**.

    ![Kanwa przepływu danych](media/tutorial-data-flow/dataflow5.png)
1. Nazwij transformację filtru **FilterYears**. Kliknij pole wyrażenia obok pola **Filtr** , aby otworzyć Konstruktora wyrażeń. W tym miejscu określisz warunek filtrowania.

    ![Zrzut ekranu pokazujący pole filtr dla wyrażenia.](media/tutorial-data-flow/filter1.png)
1. Konstruktor wyrażeń przepływu danych umożliwia interaktywną kompilację wyrażeń do użycia w różnych przekształceniach. Wyrażenia mogą zawierać wbudowane funkcje, kolumny ze schematu wejściowego i parametry zdefiniowane przez użytkownika. Aby uzyskać więcej informacji na temat sposobu kompilowania wyrażeń, zobacz [Data Flow Expression Builder](concepts-data-flow-expression-builder.md).

    W tym samouczku zawarto filtrowanie filmów z gatunku komedia, które zostały dostarczone między latami 1910 i 2000. Jako rok jest obecnie ciągiem, należy przekonwertować go na liczbę całkowitą przy użyciu ```toInteger()``` funkcji. Użyj opcji "większe niż lub równe" (>=) i mniejszej niż lub równej operatorowi (<=) do porównania z wartościami w postaci literału Year 1910 i 200-. Sumuj te wyrażenia razem z operatorem i (&&). Wyrażenie jest dostępne jako:

    ```toInteger(year) >= 1910 && toInteger(year) <= 2000```

    Aby znaleźć filmy, które są comediese, możesz użyć ```rlike()``` funkcji, aby znaleźć wzorzec "komedia" w gatunku kolumny. Utwórz wyrażenie rlike z porównaniem lat, aby uzyskać:

    ```toInteger(year) >= 1910 && toInteger(year) <= 2000 && rlike(genres, 'Comedy')```

    Jeśli klaster debugowania jest aktywny, można sprawdzić logikę, klikając przycisk **Odśwież** , aby wyświetlić dane wyjściowe wyrażenia w porównaniu z używanymi danymi wejściowymi. Istnieje więcej niż jedna odpowiedź, na którą można wykonać tę logikę przy użyciu języka wyrażeń przepływu danych.

    ![Zostaną przefiltrowane](media/tutorial-data-flow/filter2.png)

    Kliknij przycisk **Zapisz i Zakończ,** gdy skończysz pracę z wyrażeniem.

1. Pobierz **Podgląd danych** , aby sprawdzić, czy filtr działa poprawnie.

    ![Zrzut ekranu przedstawiający pobraną wersję zapoznawczą danych.](media/tutorial-data-flow/filter3.png)
1. Kolejną przekształceniem, który dodasz, jest transformacja **zagregowana** w obszarze **modyfikator schematu**.

    ![Zrzut ekranu pokazujący modyfikator schematu agregacji.](media/tutorial-data-flow/agg1.png)
1. Nazwij **AggregateComedyRatings** przekształcenia agregacji. Na karcie **Grupuj według** wybierz z listy rozwijanej pozycję **Year** , aby grupować agregacje według roku, w którym znajduje się film.

    ![Zrzut ekranu pokazujący opcję Year w karcie Grupuj według w obszarze Ustawienia agregacji.](media/tutorial-data-flow/agg2.png)
1. Przejdź do karty **agregaty** . W lewym polu tekstowym nadaj kolumnie agregującej **AverageComedyRating**. Kliknij pole wyrażenia z prawej strony, aby wprowadzić wyrażenie agregujące za pośrednictwem konstruktora wyrażeń.

    ![Zrzut ekranu pokazujący opcję Year na karcie agregacje w obszarze Ustawienia agregacji.](media/tutorial-data-flow/agg3.png)
1. Aby uzyskać średnią **klasyfikację** kolumn, użyj ```avg()``` funkcji agregującej. Ponieważ **Klasyfikacja** jest ciągiem i ```avg()``` przyjmuje numeryczne dane wejściowe, należy przekonwertować wartość na liczbę za pośrednictwem ```toInteger()``` funkcji. To wyrażenie wygląda następująco:

    ```avg(toInteger(Rating))```

    Po zakończeniu kliknij przycisk **Zapisz i Zakończ** .

    ![Zrzut ekranu, który pokazuje zapisane wyrażenie.](media/tutorial-data-flow/agg4.png)
1. Przejdź do karty **Podgląd danych** , aby wyświetlić dane wyjściowe transformacji. Zwróć uwagę na to, że w tym miejscu są dostępne tylko dwie kolumny — **Year** i **AverageComedyRating**.

    ![Agregacja](media/tutorial-data-flow/agg3.png)
1. Następnie chcesz dodać transformację **ujścia** w **miejscu docelowym**.

    ![Zrzut ekranu pokazujący, gdzie dodać transformację ujścia w miejscu docelowym.](media/tutorial-data-flow/sink1.png)
1. Nazwij **ujścia** ujścia. Kliknij pozycję **Nowy** , aby utworzyć zestaw danych ujścia.

    ![Zrzut ekranu pokazujący, gdzie można nazwać ujścia i utworzyć nowy zestaw danych ujścia.](media/tutorial-data-flow/sink2.png)
1. Wybierz **Azure Data Lake Storage Gen2**. Kliknij przycisk Kontynuuj.

    ![Zrzut ekranu przedstawiający kafelek Azure Data Lake Storage Gen2, który można wybrać.](media/tutorial-data-flow/dataset1.png)
1. Wybierz **DelimitedText**. Kliknij przycisk Kontynuuj.

    ![Zestaw danych](media/tutorial-data-flow/dataset2.png)
1. Nazwij swój zestaw danych ujścia **MoviesSink**. W polu połączona usługa wybierz połączoną usługę ADLS Gen2 utworzoną w kroku 6. Wprowadź folder wyjściowy, w którym mają zostać zapisane dane. W tym samouczku zapisujemy do folderu "output" w kontenerze "przykładowe dane". Folder nie musi istnieć wcześniej i może być tworzony dynamicznie. Ustaw **pierwszy wiersz jako nagłówek** jako wartość true, a następnie wybierz pozycję **Brak** dla **schematu importowania**. Kliknij przycisk Zakończ.

    ![Ujście](media/tutorial-data-flow/sink3.png)

Teraz zakończono Kompilowanie przepływu danych. Wszystko jest gotowe do uruchomienia w potoku.

## <a name="running-and-monitoring-the-data-flow"></a>Uruchamianie i monitorowanie przepływu danych

Można debugować potok przed jego opublikowaniem. W tym kroku wywołajesz przebieg debugowania potoku przepływu danych. Gdy Podgląd danych nie zapisuje danych, uruchomienie debugowania spowoduje zapisanie danych w miejscu docelowym ujścia.

1. Przejdź do kanwy potoku. Kliknij pozycję **Debuguj** , aby wyzwolić uruchomienie debugowania.

    ![Zrzut ekranu pokazujący kanwę potoku z wyróżnioną pozycją Debuguj.](media/tutorial-data-flow/pipeline1.png)
1. Debugowanie potoku działań przepływu danych korzysta z aktywnego klastra debugowania, ale nadal trwa co najmniej minutę. Postęp można śledzić za pomocą karty **dane wyjściowe** . Po pomyślnym uruchomieniu kliknij ikonę okularów, aby otworzyć okienko monitorowanie.

    ![Potok](media/tutorial-data-flow/pipeline2.png)
1. W okienku Monitorowanie można zobaczyć liczbę wierszy i czas spędzony w każdym kroku transformacji.

    ![Zrzut ekranu przedstawiający okienko monitorowanie, w którym można zobaczyć liczbę wierszy i czas spędzony w każdym kroku transformacji.](media/tutorial-data-flow/pipeline3.png)
1. Kliknij transformację, aby uzyskać szczegółowe informacje na temat kolumn i partycjonowania danych.

    ![Monitorowanie](media/tutorial-data-flow/pipeline4.png)

Jeśli wykonano ten samouczek poprawnie, należy napisać 83 wierszy i 2 kolumny w folderze ujścia. Możesz sprawdzić, czy dane są poprawne, sprawdzając magazyn obiektów BLOB.

## <a name="next-steps"></a>Następne kroki

Potok w tym samouczku służy do uruchamiania przepływu danych, który agreguje średnią ocenę Comedies z 1910 do 2000 i zapisuje dane w ADLS. W tym samouczku omówiono:

> [!div class="checklist"]
> * Tworzenie fabryki danych.
> * Utwórz potok z działaniem przepływu danych.
> * Kompiluj przepływ danych mapowania z czterema przekształceniami.
> * Testowe uruchamianie potoku.
> * Monitorowanie działania przepływu danych

Dowiedz się więcej na temat [języka wyrażeń przepływu danych](data-flow-expression-functions.md).
