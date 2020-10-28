---
title: Przekształcanie danych za pomocą przepływu danych mapowania zarządzanej sieci wirtualnej Azure Data Factory
description: Ten samouczek zawiera instrukcje krok po kroku dotyczące korzystania z Azure Data Factory do przekształcania danych za pomocą mapowania przepływów danych.
author: djpmsft
ms.author: daperlov
ms.reviewer: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/19/2019
ms.openlocfilehash: 52e45017643c63937ffc521adfe08d6415460254
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92637143"
---
# <a name="transform-data-securely-by-using-mapping-data-flow"></a>Bezpieczne Przekształcanie danych przy użyciu funkcji mapowania przepływu danych

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Jeśli jesteś nowym użytkownikiem usługi Azure Data Factory, zobacz [Wprowadzenie do usługi Azure Data Factory](./introduction.md).

W tym samouczku użyjesz interfejsu użytkownika Data Factory, aby utworzyć potok, który kopiuje i przekształca dane *ze źródła Azure Data Lake Storage Gen2 do ujścia Data Lake Storage Gen2 (oba umożliwiają dostęp tylko do wybranych sieci)* , używając funkcji mapowania przepływu danych w [Data Factory zarządzanym Virtual Network](managed-virtual-network-private-endpoint.md). Podczas przekształcania danych przy użyciu funkcji mapowania przepływu danych można rozwijać wzorzec konfiguracji w tym samouczku.

W tym samouczku wykonasz następujące czynności:

> [!div class="checklist"]
>
> * Tworzenie fabryki danych.
> * Utwórz potok z działaniem przepływu danych.
> * Kompiluj przepływ danych mapowania z czterema przekształceniami.
> * Testowe uruchamianie potoku.
> * Monitoruj działanie przepływu danych.

## <a name="prerequisites"></a>Wymagania wstępne
* **Subskrypcja platformy Azure** . Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/) .
* **Konto usługi Azure Storage** . Używasz Data Lake Storage jako magazynu danych *źródłowych* i *ujścia* . Jeśli nie masz konta magazynu, utwórz je, wykonując czynności przedstawione w artykule [Tworzenie konta magazynu platformy Azure](../storage/common/storage-account-create.md?tabs=azure-portal). *Upewnij się, że konto magazynu zezwala na dostęp tylko z wybranych sieci.* 

Plik, który zostanie przekształcony w tym samouczku, jest moviesDB.csv, który można znaleźć w tej [witrynie zawartości usługi GitHub](https://raw.githubusercontent.com/djpmsft/adf-ready-demo/master/moviesDB.csv). Aby pobrać plik z usługi GitHub, skopiuj zawartość do wybranego edytora tekstu, aby zapisać ją lokalnie jako plik CSV. Aby przekazać plik do konta magazynu, zobacz [przekazywanie obiektów BLOB za pomocą Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md). Przykłady odwołują się do kontenera o nazwie **Sample-Data** .

## <a name="create-a-data-factory"></a>Tworzenie fabryki danych

W tym kroku utworzysz fabrykę danych i otworzysz interfejs użytkownika Data Factory, aby utworzyć potok w fabryce danych.

1. Otwórz przeglądarkę Microsoft Edge lub Google Chrome. Obecnie tylko przeglądarki sieci Web Microsoft Edge i Google Chrome obsługują interfejs użytkownika Data Factory.
1. W menu po lewej stronie wybierz pozycję **Utwórz zasób**  >  **Analytics**  >  **Data Factory** .
1. Na stronie **Nowa fabryka danych** w polu **Nazwa** wprowadź wartość **ADFTutorialDataFactory** .

   Nazwa fabryki danych musi być *globalnie unikatowa* . Jeśli zostanie wyświetlony komunikat o błędzie dotyczący wartości nazwy, wprowadź inną nazwę fabryki danych (na przykład Twojanazwaadftutorialdatafactory). Reguły nazewnictwa dla artefaktów usługi Data Factory można znaleźć w artykule [Data Factory — reguły nazewnictwa](naming-rules.md).

1. Wybierz **subskrypcję** platformy Azure, w której chcesz utworzyć fabrykę danych.
1. W obszarze **Grupa zasobów** wykonaj jedną z następujących czynności:

    * Wybierz pozycję **Użyj istniejącej** , a następnie wybierz istniejącą grupę zasobów z listy rozwijanej.
    * Wybierz pozycję **Utwórz nową** , a następnie wprowadź nazwę grupy zasobów. 
         
    Informacje na temat grup zasobów znajdują się w artykule [Using resource groups to manage your Azure resources (Używanie grup zasobów do zarządzania zasobami platformy Azure)](../azure-resource-manager/management/overview.md). 
1. W obszarze **Wersja** wybierz pozycję **V2** .
1. W obszarze **Lokalizacja** wybierz lokalizację fabryki danych. Na liście rozwijanej są wyświetlane tylko obsługiwane lokalizacje. Magazyny danych (np. usługi Azure Storage i Azure SQL Database) i obliczenia (na przykład usługa Azure HDInsight) używane przez fabrykę danych mogą znajdować się w innych regionach.

1. Wybierz pozycję **Utwórz** .
1. Po zakończeniu tworzenia zobaczysz powiadomienie w centrum powiadomień. Wybierz pozycję **Przejdź do zasobu** , aby przejść do strony **Data Factory** .
1. Wybierz pozycję **Tworzenie i monitorowanie** , aby uruchomić interfejs użytkownika usługi Data Factory na osobnej karcie.

## <a name="create-an-azure-ir-in-data-factory-managed-virtual-network"></a>Utwórz Azure IR w Data Factory zarządzanym Virtual Network
W tym kroku utworzysz Azure IR i włączysz Data Factory zarządzanym Virtual Network.

1. W portalu Data Factory przejdź do pozycji **Zarządzaj** , a następnie wybierz pozycję **Nowy** , aby utworzyć nowy Azure IR.

   ![Zrzut ekranu przedstawiający tworzenie nowego Azure IR.](./media/tutorial-copy-data-portal-private/create-new-azure-ir.png)
1. Wybierz opcję **Azure** IR.

   ![Zrzut ekranu przedstawiający nowy Azure IR.](./media/tutorial-copy-data-portal-private/azure-ir.png)

1. W obszarze **Konfiguracja sieci wirtualnej (wersja zapoznawcza)** wybierz pozycję **Włącz** .

   ![Zrzut ekranu pokazujący Włączanie nowego Azure IR.](./media/tutorial-copy-data-portal-private/enable-managed-vnet.png)

1. Wybierz pozycję **Utwórz** .

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>Tworzenie potoku za pomocą działania przepływu danych

W tym kroku utworzysz potok zawierający działanie przepływu danych.

1. Na stronie **Zaczynajmy** wybierz pozycję **Utwórz potok** .

   ![Zrzut ekranu przedstawiający tworzenie potoku.](./media/doc-common-process/get-started-page.png)

1. W okienku właściwości potoku wpisz **TransformMovies** dla nazwy potoku.
1. Na górnym pasku fabryki przesuń suwak **debugowania przepływu danych** na. Tryb debugowania umożliwia interaktywne Testowanie logiki transformacji na aktywnym klastrze Spark. W celu rozgrzania klastrów przepływów danych trwają od 5 do siedmiu minut. Włącz najpierw **debugowanie przepływu danych** , jeśli planujesz tworzenie przepływu danych. Aby uzyskać więcej informacji, zobacz [tryb debugowania](./concepts-data-flow-debug-mode.md).

    ![Zrzut ekranu pokazujący suwak debugowania przepływu danych.](media/tutorial-data-flow-private/dataflow-debug.png)
1. W okienku **działania** rozwiń pozycję **Przenieś i Przekształć** . Przeciągnij działanie **przepływu danych** z okienka do kanwy potoku.

1. W oknie podręcznym **Dodawanie przepływu danych** wybierz pozycję **Utwórz nowy przepływ danych** , a następnie wybierz pozycję **Mapowanie przepływu danych** . Po zakończeniu wybierz **przycisk OK** .

    ![Zrzut ekranu pokazujący mapowanie przepływu danych.](media/tutorial-data-flow-private/mapping-dataflow.png)

1. Nazwij **TransformMovies** przepływu danych w okienku właściwości.

## <a name="build-transformation-logic-in-the-data-flow-canvas"></a>Logika transformacji kompilacji na kanwie przepływu danych

Po utworzeniu przepływu danych zostanie on automatycznie wysłany do kanwy przepływu danych. W tym kroku utworzysz przepływ danych, który pobiera moviesDB.csv plik w Data Lake Storage i agreguje średnią ocenę Comedies od 1910 do 2000. Następnie zapiszesz ten plik z powrotem do Data Lake Storage.

### <a name="add-the-source-transformation"></a>Dodaj transformację źródłową

W tym kroku skonfigurujesz Data Lake Storage Gen2 jako źródło.

1. Na kanwie przepływu danych Dodaj źródło, zaznaczając pole **Dodaj źródło** .

1. Nadaj nazwę źródłową **MoviesDB** . Wybierz pozycję **Nowy** , aby utworzyć nowy źródłowy zestaw danych.

1. Wybierz pozycję **Azure Data Lake Storage Gen2** , a następnie wybierz pozycję **Kontynuuj** .

1. Wybierz pozycję **DelimitedText** , a następnie wybierz pozycję **Kontynuuj** .

1. Nazwij zestaw danych **MoviesDB** . Z listy rozwijanej połączona usługa wybierz pozycję **Nowy** .

1. Na ekranie Tworzenie połączonej usługi nadaj Data Lake Storage Gen2 nazwę **ADLSGen2** połączonej usłudze i Określ metodę uwierzytelniania. Następnie wprowadź poświadczenia połączenia. W tym samouczku używamy **klucza konta** do nawiązywania połączenia z naszym kontem magazynu. 

1. Upewnij się, że włączono funkcję **interaktywnego tworzenia** . Może to potrwać minutę.

    ![Zrzut ekranu pokazujący interaktywną autorstwo.](./media/tutorial-data-flow-private/interactive-authoring.png)

1. Wybierz pozycję **Testuj połączenie** . Nie powinno się to zakończyć, ponieważ konto magazynu nie umożliwia dostępu do niego bez tworzenia i zatwierdzania prywatnego punktu końcowego. W komunikacie o błędzie powinien zostać wyświetlony link umożliwiający utworzenie prywatnego punktu końcowego, który można wykonać, aby utworzyć zarządzany prywatny punkt końcowy. Alternatywnie przejdź bezpośrednio do karty **Zarządzanie** i postępuj zgodnie z instrukcjami w [tej sekcji](#create-a-managed-private-endpoint) , aby utworzyć zarządzany prywatny punkt końcowy.

1. Pozostaw otwarte okno dialogowe, a następnie przejdź do konta magazynu.

1. Postępuj zgodnie z instrukcjami w [tej sekcji](#approval-of-a-private-link-in-a-storage-account) , aby zatwierdzić link prywatny.

1. Wróć do okna dialogowego. Wybierz ponownie **Test connection** i wybierz pozycję **Utwórz** , aby wdrożyć połączoną usługę.

1. Na ekranie Tworzenie zestawu danych wprowadź miejsce, w którym znajduje się plik w polu **ścieżka pliku** . W tym samouczku plik moviesDB.csv znajduje się w kontenerze **przykład — dane** . Ponieważ plik ma nagłówki, zaznacz pole wyboru **pierwszy wiersz jako nagłówek** . Wybierz pozycję **połączenie/magazyn** , aby zaimportować schemat nagłówka bezpośrednio z pliku w magazynie. Po zakończeniu wybierz **przycisk OK** .

    ![Zrzut ekranu pokazujący ścieżkę źródłową.](media/tutorial-data-flow-private/source-file-path.png)

1. Jeśli klaster debugowania został uruchomiony, przejdź do karty **Podgląd danych** transformacji źródłowej i wybierz pozycję **Odśwież** , aby uzyskać migawkę danych. Możesz użyć podglądu danych, aby sprawdzić, czy transformacja została prawidłowo skonfigurowana.

    ![Zrzut ekranu przedstawiający kartę Podgląd danych.](media/tutorial-data-flow-private/data-preview.png)

#### <a name="create-a-managed-private-endpoint"></a>Tworzenie zarządzanego prywatnego punktu końcowego

Jeśli nie używasz hiperlinku podczas testowania poprzedniego połączenia, postępuj zgodnie ze ścieżką. Teraz musisz utworzyć zarządzany prywatny punkt końcowy, który zostanie połączony z utworzoną połączoną usługą.

1. Przejdź na kartę **Zarządzanie** .

   > [!NOTE]
   > Karta **Zarządzanie** może być niedostępna dla wszystkich wystąpień Data Factory. Jeśli nie widzisz go, możesz uzyskać dostęp do prywatnych punktów końcowych, wybierając pozycję **Tworzenie**  >  **połączeń**  >  **prywatny punkt końcowy** .

1. Przejdź do sekcji **zarządzane prywatne punkty końcowe** .
1. Wybierz pozycję **+ Nowy** w obszarze **zarządzane prywatne punkty końcowe** .

    ![Zrzut ekranu pokazujący zarządzany przycisk nowy prywatny punkt końcowy.](./media/tutorial-data-flow-private/new-managed-private-endpoint.png) 

1. Wybierz kafelek **Azure Data Lake Storage Gen2** z listy, a następnie wybierz pozycję **Kontynuuj** .
1. Wprowadź nazwę utworzonego konta magazynu.
1. Wybierz pozycję **Utwórz** .
1. Po kilku sekundach powinna zostać wyświetlona, że utworzone łącze prywatne wymaga zatwierdzenia.
1. Wybierz utworzony prywatny punkt końcowy. Można wyświetlić hiperłącze, które umożliwi zatwierdzenie prywatnego punktu końcowego na poziomie konta magazynu.

    ![Zrzut ekranu przedstawiający okienko zarządzanie prywatnym punktem końcowym.](./media/tutorial-data-flow-private/manage-private-endpoint.png) 

#### <a name="approval-of-a-private-link-in-a-storage-account"></a>Zatwierdzanie prywatnego linku na koncie magazynu

1. Na koncie magazynu przejdź do pozycji **prywatne połączenia punktów końcowych** w sekcji **Ustawienia** .

1. Zaznacz pole wyboru w utworzonym przez siebie prywatnym punkcie końcowym, a następnie wybierz pozycję **Zatwierdź** .

    ![Zrzut ekranu przedstawiający przycisk Zatwierdź prywatny punkt końcowy.](./media/tutorial-data-flow-private/approve-private-endpoint.png)

1. Dodaj opis i wybierz opcję **tak** .
1. Wróć do sekcji **zarządzane prywatne punkty końcowe** na karcie **Zarządzanie** w Data Factory.
1. Po około minucie powinno być widoczne zatwierdzenie dla prywatnego punktu końcowego.

### <a name="add-the-filter-transformation"></a>Dodaj przekształcenie filtru

1. Obok węzła źródłowego na kanwie przepływu danych wybierz ikonę znaku plus, aby dodać nowe przekształcenie. Pierwsze przekształcenie, które dodasz, to **Filtr** .

    ![Zrzut ekranu pokazujący Dodawanie filtru.](media/tutorial-data-flow-private/add-filter.png)
1. Nazwij transformację filtru **FilterYears** . Zaznacz pole wyrażenia obok pola **Filtr** , aby otworzyć Konstruktora wyrażeń. W tym miejscu określisz warunek filtrowania.

    ![Zrzut ekranu pokazujący FilterYears.](media/tutorial-data-flow-private/filter-years.png)
1. Konstruktor wyrażeń przepływu danych umożliwia interaktywną kompilację wyrażeń do użycia w różnych przekształceniach. Wyrażenia mogą zawierać wbudowane funkcje, kolumny ze schematu wejściowego i parametry zdefiniowane przez użytkownika. Aby uzyskać więcej informacji na temat sposobu kompilowania wyrażeń, zobacz [Data Flow Expression Builder](./concepts-data-flow-expression-builder.md).

    * W tym samouczku zawarto filtrowanie filmów w komedia gatunku, które zostały dostarczone między latami 1910 i 2000. Ponieważ rok jest obecnie ciągiem, należy przekonwertować go na liczbę całkowitą przy użyciu ```toInteger()``` funkcji. Użyj opcji "większe niż lub równe" (>=) i mniejszej lub równej operatorowi (<=) do porównania z wartościami w postaci literału Year 1910 i 2000. Sumuj te wyrażenia razem z operatorem i (&&). Wyrażenie jest dostępne jako:

        ```toInteger(year) >= 1910 && toInteger(year) <= 2000```

    * Aby znaleźć filmy, które są Comedies, możesz użyć ```rlike()``` funkcji, aby znaleźć wzorzec "komedia" w gatunku kolumny. Utwórz wyrażenie rlike z porównaniem lat, aby uzyskać:

        ```toInteger(year) >= 1910 && toInteger(year) <= 2000 && rlike(genres, 'Comedy')```

    * Jeśli masz aktywny klaster debugowania, możesz sprawdzić logikę, wybierając pozycję **Odśwież** , aby wyświetlić dane wyjściowe wyrażenia w porównaniu z używanymi danymi wejściowymi. Istnieje więcej niż jedna odpowiedź odpowiadająca sposobowi wykonania tej logiki przy użyciu języka wyrażenia przepływu danych.

        ![Zrzut ekranu pokazujący wyrażenie filtru.](media/tutorial-data-flow-private/filter-expression.png)

    * Wybierz pozycję **Zapisz i Zakończ** po zakończeniu korzystania z wyrażenia.

1. Pobierz **Podgląd danych** , aby sprawdzić, czy filtr działa poprawnie.

    ![Zrzut ekranu pokazujący przefiltrowany Podgląd danych.](media/tutorial-data-flow-private/filter-data.png)

### <a name="add-the-aggregate-transformation"></a>Dodaj Przekształcenie agregacji

1. Kolejną przekształceniem, który dodasz, jest transformacja **zagregowana** w obszarze **modyfikator schematu** .

    ![Zrzut ekranu pokazujący Dodawanie agregacji.](media/tutorial-data-flow-private/add-aggregate.png)
1. Nazwij **AggregateComedyRating** przekształcenia agregacji. Na karcie **Grupuj według** wybierz pozycję **Year (rok** ) w polu listy rozwijanej, aby grupować agregacje według roku, w którym znajduje się film.

    ![Zrzut ekranu przedstawiający grupę agregacji.](media/tutorial-data-flow-private/group-by-year.png)
1. Przejdź do karty **agregaty** . W lewym polu tekstowym nadaj kolumnie agregującej **AverageComedyRating** . Wybierz pole wyrażenia z prawej strony, aby wprowadzić wyrażenie agregujące za pośrednictwem konstruktora wyrażeń.

    ![Zrzut ekranu, na którym jest wyświetlana nazwa kolumny agregowanej.](media/tutorial-data-flow-private/name-column.png)
1. Aby uzyskać średnią **klasyfikację** kolumn, użyj ```avg()``` funkcji agregującej. Ponieważ **Klasyfikacja** jest ciągiem i ```avg()``` przyjmuje numeryczne dane wejściowe, należy przekonwertować wartość na liczbę za pośrednictwem ```toInteger()``` funkcji. To wyrażenie wygląda następująco:

    ```avg(toInteger(Rating))```

1. Wybierz pozycję **Zapisz i Zakończ** po zakończeniu.

    ![Zrzut ekranu pokazujący zapisywanie agregacji.](media/tutorial-data-flow-private/save-aggregate.png)
1. Przejdź do karty **Podgląd danych** , aby wyświetlić dane wyjściowe transformacji. Zwróć uwagę na to, że w tym miejscu są dostępne tylko dwie kolumny — **Year** i **AverageComedyRating** .

### <a name="add-the-sink-transformation"></a>Dodaj transformację ujścia

1. Następnie chcesz dodać transformację **ujścia** w **miejscu docelowym** .

    ![Zrzut ekranu pokazujący Dodawanie ujścia.](media/tutorial-data-flow-private/add-sink.png)
1. Nazwij **ujścia** ujścia. Wybierz pozycję **Nowy** , aby utworzyć zestaw danych ujścia.

    ![Zrzut ekranu przedstawiający tworzenie ujścia.](media/tutorial-data-flow-private/create-sink.png)
1. Na stronie **Nowy zestaw danych** wybierz pozycję **Azure Data Lake Storage Gen2** a następnie wybierz pozycję **Kontynuuj** .

1. Na stronie **Wybierz format** wybierz pozycję **DelimitedText** , a następnie wybierz pozycję **Kontynuuj** .

1. Nazwij swój zestaw danych ujścia **MoviesSink** . W polu połączona usługa wybierz tę samą **ADLSGen2** połączonej usługi, która została utworzona dla transformacji źródłowej. Wprowadź folder wyjściowy, w którym mają zostać zapisane dane. W tym samouczku zapisujemy dane **wyjściowe** folderu w **przykładzie kontenera — dane** . Folder nie musi istnieć wcześniej i może być tworzony dynamicznie. Zaznacz pole wyboru **pierwszy wiersz jako nagłówek** , a następnie wybierz pozycję **Brak** dla opcji **Importuj schemat** . Wybierz przycisk **OK** .

    ![Zrzut ekranu pokazujący ścieżkę ujścia.](media/tutorial-data-flow-private/sink-file-path.png)

Teraz zakończono Kompilowanie przepływu danych. Wszystko jest gotowe do uruchomienia w potoku.

## <a name="run-and-monitor-the-data-flow"></a>Uruchamianie i monitorowanie przepływu danych

Można debugować potok przed jego opublikowaniem. W tym kroku Wyzwalasz przebieg debugowania potoku przepływu danych. Podczas gdy Podgląd danych nie zapisuje danych, uruchomienie debugowania spowoduje zapisanie danych w miejscu docelowym ujścia.

1. Przejdź do kanwy potoku. Wybierz **Debuguj** , aby wyzwolić uruchomienie debugowania.

1. Debugowanie potoku działań przepływu danych korzysta z aktywnego klastra debugowania, ale nadal trwa co najmniej minutę. Postęp można śledzić za pomocą karty **dane wyjściowe** . Po pomyślnym uruchomieniu wybierz ikonę okularów, aby uzyskać szczegółowe informacje.

1. Na stronie szczegółów można zobaczyć liczbę wierszy i czas spędzony na każdym kroku transformacji.

    ![Zrzut ekranu przedstawiający przebieg monitorowania.](media/tutorial-data-flow-private/run-details.png)
1. Wybierz transformację, aby uzyskać szczegółowe informacje na temat kolumn i partycjonowania danych.

Jeśli wykonano ten samouczek poprawnie, należy napisać 83 wierszy i 2 kolumny w folderze ujścia. Możesz sprawdzić, czy dane są poprawne, sprawdzając magazyn obiektów BLOB.

## <a name="summary"></a>Podsumowanie

W tym samouczku użyto Data Factory interfejsu użytkownika do utworzenia potoku, który kopiuje i przekształca dane ze źródła Data Lake Storage Gen2 do ujścia Data Lake Storage Gen2 (oba umożliwiają dostęp tylko do wybranych sieci), używając funkcji mapowanie przepływu danych w [Data Factory zarządzanym Virtual Network](managed-virtual-network-private-endpoint.md).