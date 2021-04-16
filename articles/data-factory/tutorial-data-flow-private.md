---
title: Przekształcanie danych za pomocą przepływu Azure Data Factory mapowania sieci wirtualnej
description: Ten samouczek zawiera instrukcje krok po kroku dotyczące używania Azure Data Factory do przekształcania danych za pomocą przepływów danych mapowania.
author: dcstwh
ms.author: weetok
ms.reviewer: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/14/2021
ms.openlocfilehash: ad101bee84256662d1436ba8d8a49304aecb9129
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107518295"
---
# <a name="transform-data-securely-by-using-mapping-data-flow"></a>Bezpieczne przekształcanie danych przy użyciu przepływu danych mapowania

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Jeśli jesteś nowym użytkownikiem usługi Azure Data Factory, zobacz [Wprowadzenie do usługi Azure Data Factory](./introduction.md).

W tym samouczku użyjesz interfejsu użytkownika usługi Data Factory, aby utworzyć potok, który kopiuje i przekształca dane ze źródła usługi Azure Data Lake Storage Gen2 do ujścia usługi *Data Lake Storage Gen2 (obie* te usługi zezwalają na dostęp tylko do wybranych sieci) przy użyciu przepływu danych mapowania w ujściu usługi [Data Factory Managed Virtual Network.](managed-virtual-network-private-endpoint.md) Wzorzec konfiguracji w tym samouczku można rozszerzyć podczas przekształcania danych przy użyciu przepływu danych mapowania.

W tym samouczku wykonasz następujące czynności:

> [!div class="checklist"]
>
> * Tworzenie fabryki danych.
> * Tworzenie potoku z działaniem przepływu danych.
> * Tworzenie przepływu danych mapowania z czterema przekształceniami.
> * Testowe uruchamianie potoku.
> * Monitorowanie działania przepływu danych.

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure.** Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz bezpłatne konto platformy [Azure.](https://azure.microsoft.com/free/)
* **Konto usługi Azure Storage.** Magazyny danych Data Lake Storage jako *magazyny danych źródła* *i* ujścia. Jeśli nie masz konta magazynu, utwórz je, wykonując czynności przedstawione w artykule [Tworzenie konta magazynu platformy Azure](../storage/common/storage-account-create.md?tabs=azure-portal). *Upewnij się, że konto magazynu zezwala na dostęp tylko z wybranych sieci.* 

Plik, który przekształcimy w tym samouczku, to plik moviesDB.csv, który można znaleźć w tej [witrynie zawartości GitHub.](https://raw.githubusercontent.com/djpmsft/adf-ready-demo/master/moviesDB.csv) Aby pobrać plik z usługi GitHub, skopiuj jego zawartość do wybranego edytora tekstów, aby zapisać go lokalnie jako plik CSV. Aby przekazać plik na konto magazynu, zobacz [Przekazywanie obiektów blob za pomocą Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md). Przykłady będą odwoływać się do kontenera o nazwie **sample-data.**

## <a name="create-a-data-factory"></a>Tworzenie fabryki danych

W tym kroku utworzysz fabrykę danych i otworzysz interfejs użytkownika Data Factory, aby utworzyć potok w fabryce danych.

1. Otwórz przeglądarkę Microsoft Edge lub Google Chrome. Obecnie tylko przeglądarki Microsoft Edge i Google Chrome obsługują interfejs Data Factory użytkownika.
1. W menu po lewej stronie wybierz **pozycję Utwórz zasób**  >  **Analiza**  >  **Data Factory**.
1. Na stronie **Nowa fabryka danych** w polu **Nazwa** wprowadź wartość **ADFTutorialDataFactory**.

   Nazwa fabryki danych musi być *globalnie unikatowa*. Jeśli zostanie wyświetlony komunikat o błędzie z wartością nazwy, wprowadź inną nazwę fabryki danych (na przykład twojanazwaADFTutorialDataFactory). Reguły nazewnictwa dla artefaktów usługi Data Factory można znaleźć w artykule [Data Factory — reguły nazewnictwa](naming-rules.md).

1. Wybierz **subskrypcję** platformy Azure, w której chcesz utworzyć fabrykę danych.
1. W obszarze **Grupa zasobów** wykonaj jedną z następujących czynności:

    * Wybierz pozycję **Użyj istniejącej**, a następnie wybierz istniejącą grupę zasobów z listy rozwijanej.
    * Wybierz **pozycję Utwórz** nową i wprowadź nazwę grupy zasobów. 
         
    Informacje na temat grup zasobów znajdują się w artykule [Using resource groups to manage your Azure resources (Używanie grup zasobów do zarządzania zasobami platformy Azure)](../azure-resource-manager/management/overview.md). 
1. W obszarze **Wersja** wybierz pozycję **V2**.
1. W obszarze **Lokalizacja** wybierz lokalizację fabryki danych. Na liście rozwijanej są wyświetlane tylko obsługiwane lokalizacje. Magazyny danych (na przykład usługi Azure Storage i Azure SQL Database) oraz jednostki obliczeniowe (na przykład Azure HDInsight) używane przez fabrykę danych mogą być w innych regionach.

1. Wybierz przycisk **Utwórz**.
1. Po zakończeniu tworzenia zostanie wyświetlony powiadomienie w Centrum powiadomień. Wybierz **pozycję Przejdź do zasobu,** aby przejść do **Data Factory** zasobów.
1. Wybierz pozycję **Tworzenie i monitorowanie**, aby uruchomić interfejs użytkownika usługi Data Factory na osobnej karcie.

## <a name="create-an-azure-ir-in-data-factory-managed-virtual-network"></a>Tworzenie Azure IR w programie Data Factory Managed Virtual Network

W tym kroku utworzysz nową Azure IR i włączysz Data Factory Managed Virtual Network.

1. W portalu Data Factory przejdź do opcji **Zarządzaj** i wybierz pozycję **Nowy,** aby utworzyć nową Azure IR.

   ![Zrzut ekranu przedstawiający tworzenie nowego Azure IR.](./media/tutorial-copy-data-portal-private/create-new-azure-ir.png)
1. Na stronie **Konfiguracja środowiska Integration Runtime** wybierz środowisko Integration Runtime do utworzenia na podstawie wymaganych możliwości. W tym samouczku wybierz pozycję **Azure (Self-Hosted),** a następnie kliknij przycisk **Continue (Kontynuuj).** 
1. Wybierz **pozycję Azure,** a następnie kliknij przycisk **Kontynuuj,** aby utworzyć środowisko Azure Integration Runtime.

   ![Zrzut ekranu przedstawiający nową Azure IR.](./media/tutorial-copy-data-portal-private/azure-ir.png)

1. W **obszarze Konfiguracja sieci wirtualnej (wersja zapoznawcza)** wybierz pozycję **Włącz**.

   ![Zrzut ekranu przedstawiający włączanie nowego Azure IR.](./media/tutorial-copy-data-portal-private/enable-managed-vnet.png)

1. Wybierz przycisk **Utwórz**.

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>Tworzenie potoku z działaniem przepływu danych

W tym kroku utworzysz potok zawierający działanie przepływu danych.

1. Na stronie **Zaczynajmy** wybierz pozycję **Utwórz potok**.

   ![Zrzut ekranu przedstawiający tworzenie potoku.](./media/doc-common-process/get-started-page.png)

1. W okienku właściwości potoku wprowadź **transformaty jako** nazwę potoku.
1. W **okienku Działania** rozwiń pozycję **Przenieś i przekształć.** Przeciągnij **Przepływ danych** z okienka na kanwę potoku.

1. W **oknie podręcznym Dodawanie przepływu** danych wybierz pozycję **Utwórz nowy przepływ danych,** a następnie wybierz pozycję **Mapowanie** Przepływ danych . Po zakończeniu wybierz przycisk **OK.**

    ![Zrzut ekranu przedstawiający mapowanie Przepływ danych.](media/tutorial-data-flow-private/mapping-dataflow.png)

1. Nadaj przepływowi **danych nazwę Transform Przepływs** w okienku właściwości.
1. Na górnym pasku kanwy potoku przesuń suwak Przepływ danych **debugowania.** Tryb debugowania umożliwia interaktywne testowanie logiki przekształcania względem klastra Spark na żywo. Przepływ danych rozgrzewki potrwają 5–7 minut, a użytkownikom zaleca się włączenie debugowania najpierw, jeśli planują wykonać Przepływ danych dewelopera. Aby uzyskać więcej informacji, zobacz [Tryb debugowania](concepts-data-flow-debug-mode.md).

    ![Zrzut ekranu przedstawiający suwak Debugowanie przepływu danych.](media/tutorial-data-flow-private/dataflow-debug.png)

## <a name="build-transformation-logic-in-the-data-flow-canvas"></a>Tworzenie logiki przekształcania na kanwie przepływu danych

Po utworzeniu przepływu danych zostaniesz automatycznie wysłany do kanwy przepływu danych. W tym kroku zostanie skompilowany przepływ danych, który pobiera plik moviesDB.csv w programie Data Lake Storage i agreguje średnią klasyfikację kompilacji z lat 1910–2000. Następnie zapiszesz ten plik z powrotem w Data Lake Storage.

### <a name="add-the-source-transformation"></a>Dodawanie przekształcenia źródła

W tym kroku skonfigurujemy Data Lake Storage Gen2 jako źródło.

1. Na kanwie przepływu danych dodaj źródło, wybierając **pole Dodaj źródło.**

1. Nadaj źródłowej **bazie danych MoviesDB nazwę**. Wybierz **pozycję Nowy,** aby utworzyć nowy źródłowy zestaw danych.

1. Wybierz **Azure Data Lake Storage Gen2**, a następnie wybierz pozycję **Kontynuuj.**

1. Wybierz **pozycję DelimitedText (Tekst rozdzielany),** a następnie wybierz pozycję **Continue (Kontynuuj).**

1. Nadaj zestawowi **danych nazwę MoviesDB.** Z listy rozwijanej połączona usługa wybierz pozycję **Nowy.**

1. Na ekranie tworzenia połączonej usługi nazwij Data Lake Storage Gen2 usługę **ADLSGen2** i określ metodę uwierzytelniania. Następnie wprowadź poświadczenia połączenia. W tym samouczku używamy klucza **konta do** nawiązania połączenia z kontem magazynu. 

1. Upewnij się, że **włączyć opcję Tworzenie interakcyjne.** Może to potrwać minutę.

    ![Zrzut ekranu przedstawiający tworzenie interakcyjne.](./media/tutorial-data-flow-private/interactive-authoring.png)

1. Wybierz pozycję **Testuj połączenie**. Powinno to kończyć się niepowodzeniem, ponieważ konto magazynu nie umożliwia dostępu do niego bez utworzenia i zatwierdzenia prywatnego punktu końcowego. W komunikacie o błędzie powinien zostać wyświetlony link do tworzenia prywatnego punktu końcowego, który można śledzić w celu utworzenia zarządzanego prywatnego punktu końcowego. Alternatywą jest bezpośrednie przejdź do karty **Zarządzanie** i postępuj zgodnie z instrukcjami w tej [sekcji,](#create-a-managed-private-endpoint) aby utworzyć zarządzany prywatny punkt końcowy.

1. Nie otwieraj okna dialogowego, a następnie przejdź do swojego konta magazynu.

1. Postępuj zgodnie z [instrukcjami w tej sekcji,](#approval-of-a-private-link-in-a-storage-account) aby zatwierdzić link prywatny.

1. Wstecz do okna dialogowego. Wybierz **Test connection** ponownie, a następnie wybierz **pozycję Utwórz,** aby wdrożyć usługę połączona.

1. Na ekranie tworzenia zestawu danych wprowadź miejsce, w którym znajduje się plik, w **polu Ścieżka** pliku. W tym samouczku plik moviesDB.csv znajduje się w kontenerze **sample-data.** Ponieważ plik ma nagłówki, zaznacz **pole wyboru Pierwszy** wiersz jako nagłówek. Wybierz **pozycję Z połączenia/magazynu,** aby zaimportować schemat nagłówka bezpośrednio z pliku w magazynie. Po zakończeniu wybierz przycisk **OK.**

    ![Zrzut ekranu przedstawiający ścieżkę źródłową.](media/tutorial-data-flow-private/source-file-path.png)

1. Jeśli klaster debugowania został uruchomiony, przejdź do karty  **Podgląd** danych przekształcenia źródła i wybierz pozycję Odśwież, aby pobrać migawkę danych. Podgląd danych umożliwia sprawdzenie, czy przekształcenie jest poprawnie skonfigurowane.

    ![Zrzut ekranu przedstawiający kartę Podgląd danych.](media/tutorial-data-flow-private/data-preview.png)

#### <a name="create-a-managed-private-endpoint"></a>Tworzenie zarządzanego prywatnego punktu końcowego

Jeśli podczas poprzednich testów połączenia nie używasz hiperlinku, postępuj zgodnie ze ścieżką. Teraz musisz utworzyć zarządzany prywatny punkt końcowy, który połączysz z utworzoną połączoną usługą.

1. Przejdź do karty **Zarządzanie.**

   > [!NOTE]
   > Karta **Zarządzanie** może nie być dostępna dla wszystkich Data Factory wystąpień. Jeśli go nie widzisz, możesz uzyskać dostęp do prywatnych punktów końcowych, wybierając pozycję **Tworzenie** połączeń z prywatnym  >    >  **punktem końcowym**.

1. Przejdź do sekcji **Zarządzane prywatne punkty końcowe.**
1. Wybierz **pozycję + Nowy w** obszarze Zarządzane prywatne punkty **końcowe.**

    ![Zrzut ekranu przedstawiający przycisk Nowe zarządzane prywatne punkty końcowe.](./media/tutorial-data-flow-private/new-managed-private-endpoint.png) 

1. Wybierz **kafelek Azure Data Lake Storage Gen2** z listy, a następnie wybierz pozycję **Kontynuuj.**
1. Wprowadź nazwę utworzonego konta magazynu.
1. Wybierz przycisk **Utwórz**.
1. Po kilku sekundach powinno być widać, że utworzony link prywatny wymaga zatwierdzenia.
1. Wybierz utworzony prywatny punkt końcowy. Zostanie wyświetlony hiperlink, który spowoduje zatwierdzenie prywatnego punktu końcowego na poziomie konta magazynu.

    ![Zrzut ekranu przedstawiający okienko Zarządzanie prywatnym punktem końcowym.](./media/tutorial-data-flow-private/manage-private-endpoint.png) 

#### <a name="approval-of-a-private-link-in-a-storage-account"></a>Zatwierdzanie linku prywatnego na koncie magazynu

1. Na koncie magazynu przejdź do sekcji **Połączenia prywatnego punktu końcowego** **w sekcji** Ustawienia.

1. Zaznacz pole wyboru obok utworzonego prywatnego punktu końcowego, a następnie wybierz pozycję **Zatwierdź.**

    ![Zrzut ekranu przedstawiający przycisk Zatwierdzanie prywatnego punktu końcowego.](./media/tutorial-data-flow-private/approve-private-endpoint.png)

1. Dodaj opis i wybierz pozycję **Tak.**
1. Wstecz sekcji **Zarządzane prywatne punkty końcowe** na karcie **Zarządzanie** w Data Factory.
1. Po około minucie powinno zostać wyświetlone zatwierdzenie prywatnego punktu końcowego.

### <a name="add-the-filter-transformation"></a>Dodawanie przekształcenia filtru

1. Obok węzła źródłowego na kanwie przepływu danych wybierz ikonę plusa, aby dodać nowe przekształcenie. Pierwsze przekształcenie, które dodasz, to **Filtr**.

    ![Zrzut ekranu przedstawiający dodawanie filtru.](media/tutorial-data-flow-private/add-filter.png)
1. Nazwij przekształcenie **filtru FilterYears .** Wybierz pole wyrażenia obok pola **Filtruj według,** aby otworzyć konstruktora wyrażeń. W tym miejscu określisz warunek filtrowania.

    ![Zrzut ekranu przedstawiający ekran FilterYears (Roku filtrowania).](media/tutorial-data-flow-private/filter-years.png)
1. Konstruktor wyrażeń przepływu danych umożliwia interaktywne tworzenie wyrażeń do użycia w różnych przekształceniach. Wyrażenia mogą zawierać wbudowane funkcje, kolumny ze schematu wejściowego i parametry zdefiniowane przez użytkownika. Aby uzyskać więcej informacji na temat tworzenia wyrażeń, zobacz [Konstruktor wyrażeń przepływu danych](./concepts-data-flow-expression-builder.md).

    * W tym samouczku chcesz filtrować filmy według gatunku chłoniaka, który pojawił się w latach 1910–2000. Ponieważ rok jest obecnie ciągiem, należy przekonwertować go na liczbę całkowitą przy użyciu ```toInteger()``` funkcji . Użyj operatory większe niż lub równe (>=) i mniejsze niż lub równe (<=), aby porównać je z wartościami literału roku 1910 i 2000. Te wyrażenia są połączone z operatorem i (&&). Wyrażenie jest wyrażane w następujący sposób:

        ```toInteger(year) >= 1910 && toInteger(year) <= 2000```

    * Aby dowiedzieć się, które filmy są comedies, możesz użyć funkcji , aby znaleźć wzorzec ```rlike()``` "Stan" w gatunekach kolumn. Zsuń wyrażenie rlike z porównaniem roku, aby uzyskać:

        ```toInteger(year) >= 1910 && toInteger(year) <= 2000 && rlike(genres, 'Comedy')```

    * Jeśli klaster debugowania jest aktywny, możesz zweryfikować logikę, wybierając pozycję **Odśwież,** aby wyświetlić dane wyjściowe wyrażenia w porównaniu do użytych danych wejściowych. Istnieje więcej niż jedna właściwa odpowiedź na pytanie, jak można wykonać tę logikę przy użyciu języka wyrażeń przepływu danych.

        ![Zrzut ekranu przedstawiający wyrażenie filtru.](media/tutorial-data-flow-private/filter-expression.png)

    * Po **zakończeniu pracy** z wyrażeniem wybierz pozycję Zapisz i zakończ.

1. Pobierz podgląd **danych,** aby sprawdzić, czy filtr działa prawidłowo.

    ![Zrzut ekranu przedstawiający przefiltrowany podgląd danych.](media/tutorial-data-flow-private/filter-data.png)

### <a name="add-the-aggregate-transformation"></a>Dodawanie przekształcenia agregacji

1. Następne przekształcenie, które dodasz, to **agregacja** przekształcenia w obszarze **Modyfikator schematu**.

    ![Zrzut ekranu przedstawiający dodawanie agregacji.](media/tutorial-data-flow-private/add-aggregate.png)
1. Nazwij przekształcenie **agregujące AggregateComedyRating**. Na karcie **Grupuj** według wybierz **rok** z pola listy rozwijanej, aby pogrupować agregacje według roku, w którym pojawił się film.

    ![Zrzut ekranu przedstawiający grupę zagregowaną.](media/tutorial-data-flow-private/group-by-year.png)
1. Przejdź do **karty Agregacje.** W polu tekstowym po lewej stronie nadaj kolumnie agregacji **nazwę AverageComedyRating.** Wybierz odpowiednie pole wyrażenia, aby wprowadzić wyrażenie agregowania za pomocą konstruktora wyrażeń.

    ![Zrzut ekranu przedstawiający nazwę kolumny agregacji.](media/tutorial-data-flow-private/name-column.png)
1. Aby uzyskać średnią z **kolumny Ocena,** użyj ```avg()``` funkcji agregowania. Ponieważ **funkcja Rating** jest ciągiem i przyjmuje liczbowe dane wejściowe, musimy przekonwertować wartość na liczbę za ```avg()``` pośrednictwem funkcji ```toInteger()``` . To wyrażenie wygląda następująco:

    ```avg(toInteger(Rating))```

1. Po **zakończeniu wybierz** pozycję Zapisz i zakończ.

    ![Zrzut ekranu przedstawiający zapisywanie agregacji.](media/tutorial-data-flow-private/save-aggregate.png)
1. Przejdź do karty **Podgląd danych,** aby wyświetlić dane wyjściowe przekształcenia. Zwróć uwagę, że istnieją tylko dwie kolumny: **year i** **AverageComedyRating**.

### <a name="add-the-sink-transformation"></a>Dodawanie przekształcenia ujścia

1. Następnie chcesz dodać przekształcenie  ujścia w obszarze **Miejsce docelowe**.

    ![Zrzut ekranu przedstawiający dodawanie ujścia.](media/tutorial-data-flow-private/add-sink.png)
1. Nadaj ujściu **nazwę ujścia**. Wybierz **pozycję Nowy,** aby utworzyć zestaw danych ujścia.

    ![Zrzut ekranu przedstawiający tworzenie ujścia.](media/tutorial-data-flow-private/create-sink.png)
1. Na stronie **Nowy zestaw** danych wybierz pozycję **Azure Data Lake Storage Gen2** a następnie wybierz pozycję **Kontynuuj.**

1. Na stronie **Wybieranie formatu** wybierz pozycję **OgranicznikTekst,** a następnie wybierz pozycję **Kontynuuj.**

1. Nadaj zestawowi danych **ujścia nazwę MoviesSink.** W przypadku połączonej usługi wybierz tę samą **usługę połączona USŁUGI ADLSGen2,** która została utworzona na podstawie przekształcenia źródła. Wprowadź folder wyjściowy, w którym będą zapisywane dane. W tym samouczku zapisaliśmy do danych wyjściowych **folderu** w kontenerze **sample-data**. Folder nie musi istnieć wcześniej i można go utworzyć dynamicznie. Zaznacz pole **wyboru Pierwszy wiersz jako nagłówek,** a następnie wybierz opcję Brak **dla** **opcji Importuj schemat.** Wybierz przycisk **OK**.

    ![Zrzut ekranu przedstawiający ścieżkę ujścia.](media/tutorial-data-flow-private/sink-file-path.png)

Teraz ukończono tworzenie przepływu danych. Wszystko jest gotowe do uruchomienia w potoku.

## <a name="run-and-monitor-the-data-flow"></a>Uruchamianie i monitorowanie przepływu danych

Przed opublikowaniem potoku można go debugować. W tym kroku wyzwolisz przebieg debugowania potoku przepływu danych. Podczas gdy podgląd danych nie zapisuje danych, przebieg debugowania zapisze dane w miejscu docelowym ujścia.

1. Przejdź do kanwy potoku. Wybierz **pozycję Debuguj,** aby wyzwolić przebieg debugowania.

1. Debugowanie potoku działań przepływu danych używa aktywnego klastra debugowania, ale inicjowanie nadal trwa co najmniej minutę. Postęp można śledzić za pomocą karty **Dane** wyjściowe. Po pomyślnym uruchomieniu wybierz ikonę okularów, aby uzyskać szczegółowe informacje o uruchomieniu.

1. Na stronie szczegółów można zobaczyć liczbę wierszy i czas spędzony w poszczególnych krokach przekształcania.

    ![Zrzut ekranu przedstawiający przebieg monitorowania.](media/tutorial-data-flow-private/run-details.png)
1. Wybierz przekształcenie, aby uzyskać szczegółowe informacje na temat kolumn i partycjonowania danych.

Jeśli ten samouczek został wykonane prawidłowo, w folderze ujścia powinny zostać zapisane 83 wiersze i 2 kolumny. Możesz sprawdzić, czy dane są poprawne, sprawdzając magazyn obiektów blob.

## <a name="summary"></a>Podsumowanie

W tym samouczku interfejs użytkownika usługi Data Factory został użyty do utworzenia potoku, który kopiuje i przekształca dane ze źródła usługi Data Lake Storage Gen2 do ujścia usługi Data Lake Storage Gen2 (obie te usługi zezwalają na dostęp tylko do wybranych sieci) przy użyciu przepływu danych mapowania w ujściu usługi [Data Factory Managed Virtual Network.](managed-virtual-network-private-endpoint.md)