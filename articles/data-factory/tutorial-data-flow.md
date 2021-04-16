---
title: Przekształcanie danych przy użyciu przepływu danych mapowania
description: Ten samouczek zawiera instrukcje krok po kroku dotyczące używania Azure Data Factory do przekształcania danych za pomocą przepływu danych mapowania
author: dcstwh
ms.author: weetok
ms.reviewer: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/14/2021
ms.openlocfilehash: 0842dad0e0ea6f9987727e8abf3d0eaf8a59e821
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517522"
---
# <a name="transform-data-using-mapping-data-flows"></a>Przekształcanie danych przy użyciu przepływów danych mapowania

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Jeśli jesteś nowym użytkownikiem usługi Azure Data Factory, zobacz [Wprowadzenie do usługi Azure Data Factory](introduction.md).

W tym samouczku użyjesz interfejsu użytkownika usługi Azure Data Factory do utworzenia potoku, który kopiuje i przekształca dane ze źródła usługi Azure Data Lake Storage (ADLS) Gen2 do ujścia ADLS Gen2 przy użyciu przepływu danych mapowania. Wzorzec konfiguracji w tym samouczku można rozszerzyć podczas przekształcania danych przy użyciu przepływu danych mapowania

 >[!NOTE]
   >Ten samouczek jest przeznaczony do mapowania przepływów danych. Przepływy danych są dostępne zarówno w potokach Azure Data Factory, jak i Synapse. Jeśli jesteś nowym użytkownikiem przepływów danych w Azure Synapse Pipelines, postępuj zgodnie z Przepływ danych [użyciu Azure Synapse Pipelines](../synapse-analytics/concepts-data-flow-overview.md) 
   
W tym samouczku wykonasz następujące czynności:

> [!div class="checklist"]
> * Tworzenie fabryki danych.
> * Utwórz potok z Przepływ danych działania.
> * Tworzenie przepływu danych mapowania z czterema przekształceniami.
> * Testowe uruchamianie potoku.
> * Monitorowanie Przepływ danych danych

## <a name="prerequisites"></a>Wymagania wstępne
* **Subskrypcja platformy Azure.** Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz bezpłatne konto platformy [Azure.](https://azure.microsoft.com/free/)
* **Konto usługi Azure Storage.** Magazyn usługi ADLS jest magazynem danych *źródłowym* i *ujścia.* Jeśli nie masz konta magazynu, utwórz je, wykonując czynności przedstawione w artykule [Tworzenie konta magazynu platformy Azure](../storage/common/storage-account-create.md).

Plik, który przekształcamy w tym samouczku, jest MoviesDB.csv, który można znaleźć [tutaj.](https://raw.githubusercontent.com/djpmsft/adf-ready-demo/master/moviesDB.csv) Aby pobrać plik z usługi GitHub, skopiuj jego zawartość do wybranego edytora tekstów, aby zapisać go lokalnie jako plik CSV. Aby przekazać plik na konto magazynu, zobacz [Przekazywanie obiektów blob za pomocą Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md). Przykłady będą odwoływać się do kontenera o nazwie "sample-data".

## <a name="create-a-data-factory"></a>Tworzenie fabryki danych

W tym kroku utworzysz fabrykę danych i otworzysz Data Factory użytkownika, aby utworzyć potok w fabryce danych.

1. Otwórz przeglądarkę **Microsoft Edge** lub **Google Chrome**. Obecnie interfejs Data Factory użytkownika jest obsługiwany tylko w przeglądarkach internetowych Microsoft Edge i Google Chrome.
2. W menu po lewej stronie wybierz **pozycję Utwórz zasób**  >  **Integracja**  >  **Data Factory**:

   ![Wybór usługi Data Factory w okienku „Nowy”](./media/doc-common-process/new-azure-data-factory-menu.png)

3. Na stronie **Nowa fabryka danych** w polu **Nazwa** wprowadź wartość **ADFTutorialDataFactory**.

   Nazwa fabryki danych Azure musi być globalnie *unikatowa.* Jeśli zostanie wyświetlony komunikat o błędzie dotyczącym wartości nazwy, wprowadź inną nazwę dla fabryki danych. (na przykład TwojanazwaADFTutorialDataFactory). Reguły nazewnictwa dla artefaktów usługi Data Factory można znaleźć w artykule [Data Factory — reguły nazewnictwa](naming-rules.md).

    :::image type="content" source="./media/doc-common-process/name-not-available-error.png" alt-text="Komunikat o błędzie nowej fabryki danych dla zduplikowanej nazwy.":::
4. Wybierz **subskrypcję** platformy Azure, w której chcesz utworzyć fabrykę danych.
5. W obszarze **Grupa zasobów** wykonaj jedną z następujących czynności:

    a. Wybierz pozycję **Użyj istniejącej**, a następnie wybierz istniejącą grupę zasobów z listy rozwijanej.

    b. Wybierz **pozycję Utwórz** nową i wprowadź nazwę grupy zasobów. 
         
    Informacje na temat grup zasobów znajdują się w artykule [Using resource groups to manage your Azure resources (Używanie grup zasobów do zarządzania zasobami platformy Azure)](../azure-resource-manager/management/overview.md). 
6. W obszarze **Wersja** wybierz pozycję **V2**.
7. W obszarze **Lokalizacja** wybierz lokalizację fabryki danych. Na liście rozwijanej są wyświetlane tylko obsługiwane lokalizacje. Magazyny danych (na przykład usługi Azure Storage i SQL Database) oraz jednostki obliczeniowe (na przykład Azure HDInsight) używane przez fabrykę danych mogą być w innych regionach.
8. Wybierz przycisk **Utwórz**.
9. Po zakończeniu tworzenia zobaczysz powiadomienie w Centrum powiadomień. Wybierz **pozycję Przejdź do zasobu,** aby przejść do strony Fabryka danych.
10. Wybierz pozycję **Tworzenie i monitorowanie**, aby uruchomić interfejs użytkownika usługi Data Factory na osobnej karcie.

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>Tworzenie potoku za pomocą Przepływ danych danych

W tym kroku utworzysz potok, który zawiera Przepływ danych działania.

1. Na stronie **Zaczynajmy** wybierz pozycję **Utwórz potok**.

   ![Tworzenie potoku](./media/doc-common-process/get-started-page.png)

1. Na karcie **Ogólne** dla potoku wprowadź **wartość Transform Wyliczy w** obszarze **Nazwa** potoku.
1. W **okienku Działania** rozwiń pozycję **Przenieś i przekształć.** Przeciągnij i upuść **Przepływ danych** z okienka na kanwę potoku.

    ![Zrzut ekranu przedstawiający kanwę potoku, na której można usunąć Przepływ danych działania.](media/tutorial-data-flow/activity1.png)
1. W **oknie podręcznym Dodawanie Przepływ danych** wybierz pozycję **Utwórz** nowy Przepływ danych a następnie nadaj przepływowi danych nazwę **Transform Wass.** Po zakończeniu kliknij przycisk Zakończ.

    ![Zrzut ekranu przedstawiający miejsce, w którym nazywasz przepływ danych podczas tworzenia nowego przepływu danych.](media/tutorial-data-flow/activity2.png)
1. Na górnym pasku kanwy potoku przesuń suwak debugowania **Przepływ danych** wł. Tryb debugowania umożliwia interaktywne testowanie logiki przekształcania względem klastra Spark na żywo. Przepływ danych rozgrzewki potrwają 5–7 minut, a użytkownikom zaleca się włączenie debugowania najpierw, jeśli planują wykonać Przepływ danych dewelopera. Aby uzyskać więcej informacji, zobacz [Tryb debugowania](concepts-data-flow-debug-mode.md).

    ![Przepływ danych działania](media/tutorial-data-flow/dataflow1.png)

## <a name="build-transformation-logic-in-the-data-flow-canvas"></a>Tworzenie logiki przekształcania na kanwie przepływu danych

Po utworzeniu Przepływ danych zostaniesz automatycznie wysłany do kanwy przepływu danych. W tym kroku zostanie skompilowany przepływ danych, który przyjmuje moviesDB.csv w magazynie usługi ADLS i agreguje średnią ocenę comedies z lat 1910–2000. Następnie zapiszesz ten plik z powrotem w magazynie usługi ADLS.

1. Na kanwie przepływu danych dodaj źródło, klikając pole **Dodaj źródło.**

    ![Zrzut ekranu przedstawiający pole Dodaj źródło.](media/tutorial-data-flow/dataflow2.png)
1. Nadaj źródłowej **bazie danych MoviesDB nazwę**. Kliknij pozycję **Nowy,** aby utworzyć nowy źródłowy zestaw danych.

    ![Zrzut ekranu przedstawiający miejsce, w którym wybierasz pozycję Nowy po nazwie źródła.](media/tutorial-data-flow/dataflow3.png)
1. Wybierz **Azure Data Lake Storage Gen2**. Kliknij przycisk Kontynuuj.

    ![Zrzut ekranu przedstawiający kafelek Azure Data Lake Storage Gen2 aplikacji.](media/tutorial-data-flow/dataset1.png)
1. Wybierz **pozycję OgranicznikTekst**. Kliknij przycisk Kontynuuj.

    ![Zrzut ekranu przedstawiający kafelek Tekst rozdzielany.](media/tutorial-data-flow/dataset2.png)
1. Nadaj zestawowi **danych nazwę MoviesDB.** Na liście rozwijanej połączonej usługi wybierz pozycję **Nowy.**

    ![Zrzut ekranu przedstawiający listę rozwijaną Połączona usługa.](media/tutorial-data-flow/dataset3.png)
1. Na ekranie tworzenia połączonej usługi nadaj połączonej usłudze ADLS Gen2 **nazwę ADLSGen2** i określ metodę uwierzytelniania. Następnie wprowadź poświadczenia połączenia. W tym samouczku używamy klucza konta do nawiązania połączenia z kontem magazynu. Możesz kliknąć przycisk **Test connection,** aby sprawdzić, czy poświadczenia zostały wprowadzone poprawnie. Po zakończeniu kliknij przycisk Utwórz.

    ![Połączona usługa](media/tutorial-data-flow/ls1.png)
1. Po powrocie na ekran tworzenia zestawu danych wprowadź miejsce, w którym znajduje się plik, w polu **Ścieżka** pliku. W tym samouczku plik, moviesDB.csv znajduje się w kontenerze sample-data. Ponieważ plik ma nagłówki, zaznacz **pole Pierwszy wiersz jako nagłówek**. Wybierz **pozycję Z połączenia/magazynu,** aby zaimportować schemat nagłówka bezpośrednio z pliku w magazynie. Gdy wszystko będzie gotowe, kliknij przycisk OK.

    ![Zestawy danych](media/tutorial-data-flow/dataset4.png)
1. Jeśli klaster debugowania został uruchomiony, przejdź do karty  **Podgląd** danych przekształcenia źródła i kliknij przycisk Odśwież, aby pobrać migawkę danych. Możesz użyć podglądu danych, aby sprawdzić, czy przekształcenie jest poprawnie skonfigurowane.

    ![Zrzut ekranu przedstawiający miejsce wyświetlania podglądu danych w celu sprawdzenia, czy przekształcenie jest poprawnie skonfigurowane.](media/tutorial-data-flow/dataflow4.png)
1. Obok węzła źródłowego na kanwie przepływu danych kliknij ikonę plusa, aby dodać nowe przekształcenie. Pierwsze przekształcenie, które dodajesz, to **filtr**.

    ![Przepływ danych kanwy](media/tutorial-data-flow/dataflow5.png)
1. Nazwij przekształcenie **filtru FilterYears**. Kliknij pole wyrażenia obok pola **Filtruj,** aby otworzyć konstruktora wyrażeń. W tym miejscu określisz warunek filtrowania.

    ![Zrzut ekranu przedstawiający pole Filtruj według wyrażenia.](media/tutorial-data-flow/filter1.png)
1. Konstruktor wyrażeń przepływu danych umożliwia interaktywne tworzenie wyrażeń do użycia w różnych przekształceniach. Wyrażenia mogą zawierać wbudowane funkcje, kolumny ze schematu wejściowego i parametry zdefiniowane przez użytkownika. Aby uzyskać więcej informacji na temat tworzenia wyrażeń, zobacz [Przepływ danych konstruktora wyrażeń](concepts-data-flow-expression-builder.md).

    W tym samouczku chcesz filtrować filmy z gatunku, które pojawiły się w latach 1910–2000. Ponieważ rok jest obecnie ciągiem, należy przekonwertować go na liczbę całkowitą przy użyciu ```toInteger()``` funkcji . Użyj operatory większe niż lub równe (>=) i mniejsze niż lub równe (<=) do porównania z wartościami literału roku 1910 i 200-. Te wyrażenia są połączone z operatorem i (&&). Wyrażenie jest wyrażane w następujący sposób:

    ```toInteger(year) >= 1910 && toInteger(year) <= 2000```

    Aby dowiedzieć się, które filmy są comedies, możesz użyć funkcji , aby znaleźć wzorzec ```rlike()``` "Stan" w gatunekach kolumn. Zsuń wyrażenie rlike z porównaniem roku, aby uzyskać:

    ```toInteger(year) >= 1910 && toInteger(year) <= 2000 && rlike(genres, 'Comedy')```

    Jeśli klaster debugowania jest aktywny, możesz zweryfikować logikę, klikając pozycję **Odśwież,** aby wyświetlić dane wyjściowe wyrażenia w porównaniu z użytymi wejściami. Istnieje więcej niż jedna właściwa odpowiedź na pytanie, jak można wykonać tę logikę przy użyciu języka wyrażeń przepływu danych.

    ![Filtr](media/tutorial-data-flow/filter2.png)

    Po **zakończeniu pracy** z wyrażeniem kliknij pozycję Zapisz i zakończ.

1. Pobierz podgląd **danych,** aby sprawdzić, czy filtr działa prawidłowo.

    ![Zrzut ekranu przedstawiający pobrany podgląd danych.](media/tutorial-data-flow/filter3.png)
1. Następne przekształcenie, które dodasz, to **agregacja** przekształcenia w obszarze **Modyfikator schematu**.

    ![Zrzut ekranu przedstawiający modyfikator schematu Agregacja.](media/tutorial-data-flow/agg1.png)
1. Nazwij przekształcenie **agregujące AggregateComedyRatings**. Na karcie **Grupuj** według wybierz **rok** z listy rozwijanej, aby pogrupować agregacje według roku, w którym pojawił się film.

    ![Zrzut ekranu przedstawiający opcję roku na karcie Grupuj według w obszarze Ustawienia agregacji.](media/tutorial-data-flow/agg2.png)
1. Przejdź do **karty Agregacje.** W polu tekstowym po lewej stronie nadaj kolumnie agregacji **nazwę AverageComedyRating.** Kliknij prawe pole wyrażenia, aby wprowadzić wyrażenie agregowania za pomocą konstruktora wyrażeń.

    ![Zrzut ekranu przedstawiający opcję rok na karcie Agregacje w obszarze Ustawienia agregacji.](media/tutorial-data-flow/agg3.png)
1. Aby uzyskać średnią kolumny **Ocena,** użyj ```avg()``` funkcji agregowania. Ponieważ **funkcja Rating** jest ciągiem i przyjmuje liczbowe dane wejściowe, musimy przekonwertować wartość na liczbę za ```avg()``` pośrednictwem funkcji ```toInteger()``` . To wyrażenie wygląda następująco:

    ```avg(toInteger(Rating))```

    Po **zakończeniu kliknij pozycję Zapisz i** zakończ.

    ![Zrzut ekranu przedstawiający zapisane wyrażenie.](media/tutorial-data-flow/agg4.png)
1. Przejdź do karty **Podgląd danych,** aby wyświetlić dane wyjściowe przekształcenia. Zwróć uwagę, że istnieją tylko dwie kolumny: **year i** **AverageComedyRating**.

    ![Agregacja](media/tutorial-data-flow/agg3.png)
1. Następnie chcesz dodać przekształcenie  ujścia w obszarze **Miejsce docelowe**.

    ![Zrzut ekranu przedstawiający miejsce dodania przekształcenia ujścia w obszarze Miejsce docelowe.](media/tutorial-data-flow/sink1.png)
1. Nadaj ujściu **nazwę ujścia**. Kliknij **pozycję Nowy,** aby utworzyć zestaw danych ujścia.

    ![Zrzut ekranu przedstawiający miejsce, w którym można nazwać ujścia i utworzyć nowy zestaw danych ujścia.](media/tutorial-data-flow/sink2.png)
1. Wybierz **Azure Data Lake Storage Gen2**. Kliknij przycisk Kontynuuj.

    ![Zrzut ekranu przedstawiający Azure Data Lake Storage Gen2, który można wybrać.](media/tutorial-data-flow/dataset1.png)
1. Wybierz **pozycję RozdzielanyTekst**. Kliknij przycisk Kontynuuj.

    ![Zestaw danych](media/tutorial-data-flow/dataset2.png)
1. Nadaj zestawowi danych **ujścia nazwę MoviesSink.** W przypadku połączonej usługi wybierz usługę połączona USŁUGI ADLS Gen2 utworzoną w kroku 6. Wprowadź folder wyjściowy, w którym będą zapisywane dane. W tym samouczku piszemy do folderu "output" w kontenerze "sample-data". Folder nie musi istnieć wcześniej i można go utworzyć dynamicznie. Ustaw **opcję Pierwszy wiersz jako nagłówek na** wartość true i wybierz opcję Brak **dla** **opcji Importuj schemat.** Kliknij przycisk Zakończ.

    ![Ujście](media/tutorial-data-flow/sink3.png)

Teraz ukończono tworzenie przepływu danych. Wszystko jest gotowe do uruchomienia w potoku.

## <a name="running-and-monitoring-the-data-flow"></a>Uruchamianie i monitorowanie Przepływ danych

Przed opublikowaniem potoku można go debugować. W tym kroku wyzwolisz uruchomienie debugowania potoku przepływu danych. Podczas gdy podgląd danych nie zapisuje danych, przebieg debugowania zapisze dane w miejscu docelowym ujścia.

1. Przejdź do kanwy potoku. Kliknij **pozycję Debuguj,** aby wyzwolić uruchomienie debugowania.

    ![Zrzut ekranu przedstawiający kanwę potoku z wyróżnieniami Debug (Debugowanie).](media/tutorial-data-flow/pipeline1.png)
1. Debugowanie potoku Przepływ danych korzysta z aktywnego klastra debugowania, ale inicjowanie nadal trwa co najmniej minutę. Postęp można śledzić za pomocą **karty Dane** wyjściowe. Po pomyślnym uruchomieniu kliknij ikonę okularów, aby otworzyć okienko monitorowania.

    ![Potok](media/tutorial-data-flow/pipeline2.png)
1. W okienku monitorowania można zobaczyć liczbę wierszy i czas spędzony w każdym kroku przekształcania.

    ![Zrzut ekranu przedstawiający okienko monitorowania, w którym można zobaczyć liczbę wierszy i czas spędzony w poszczególnych krokach przekształcania.](media/tutorial-data-flow/pipeline3.png)
1. Kliknij przekształcenie, aby uzyskać szczegółowe informacje na temat kolumn i partycjonowania danych.

    ![Monitorowanie](media/tutorial-data-flow/pipeline4.png)

Jeśli ten samouczek został wykonane prawidłowo, w folderze ujścia powinny być zapisane 83 wiersze i 2 kolumny. Możesz sprawdzić, czy dane są poprawne, sprawdzając magazyn obiektów blob.

## <a name="next-steps"></a>Następne kroki

Potok w tym samouczku uruchamia przepływ danych, który agreguje średnią klasyfikację comedies z lat 1910–2000 i zapisuje dane w u usługi ADLS. W tym samouczku omówiono:

> [!div class="checklist"]
> * Tworzenie fabryki danych.
> * Tworzenie potoku z Przepływ danych działania.
> * Tworzenie przepływu danych mapowania z czterema przekształceniami.
> * Testowe uruchamianie potoku.
> * Monitorowanie Przepływ danych danych

Dowiedz się więcej o [języku wyrażeń przepływu danych](data-flow-expression-functions.md).