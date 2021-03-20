---
title: 'Szybki Start: Przekształcanie danych przy użyciu przepływu danych mapowania'
description: Ten samouczek zawiera instrukcje krok po kroku dotyczące korzystania z usługi Azure Synapse Analytics do przekształcania danych przy użyciu przepływu danych mapowania.
author: djpmsft
ms.author: daperlov
ms.reviewer: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/03/2020
ms.openlocfilehash: cb0fd5464f7025d71dd384c56233aefa6a6cd364
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98218726"
---
# <a name="quickstart-transform-data-using-mapping-data-flows"></a>Szybki Start: Przekształcanie danych przy użyciu mapowania przepływów danych

W tym przewodniku szybki start użyjesz usługi Azure Synapse Analytics do utworzenia potoku, który przekształca dane ze źródła Azure Data Lake Storage Gen2 (ADLS Gen2) do ujścia ADLS Gen2 przy użyciu przepływu danych mapowania. Wzorzec konfiguracji w tym przewodniku Szybki Start może być rozwinięty podczas przekształcania danych przy użyciu przepływu danych mapowania

W tym przewodniku szybki start wykonaj następujące czynności:

> [!div class="checklist"]
> * Utwórz potok z działaniem przepływu danych w usłudze Azure Synapse Analytics.
> * Kompiluj przepływ danych mapowania z czterema przekształceniami.
> * Testowe uruchamianie potoku.
> * Monitorowanie działania przepływu danych

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**: jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).
* **Obszar roboczy usługi Azure Synapse**: Tworzenie obszaru roboczego Synapse przy użyciu Azure Portal zgodnie z instrukcjami w [przewodniku szybki start: Tworzenie obszaru roboczego Synapse](quickstart-create-workspace.md).
* **Konto usługi Azure Storage**: Magazyn ADLS jest używany jako magazyn danych *źródłowych* i *ujścia* . Jeśli nie masz konta magazynu, utwórz je, wykonując czynności przedstawione w artykule [Tworzenie konta magazynu platformy Azure](../storage/common/storage-account-create.md).

    Plik, który jest przekształcany w tym samouczku, jest MoviesDB.csv, który można znaleźć [tutaj](https://raw.githubusercontent.com/djpmsft/adf-ready-demo/master/moviesDB.csv). Aby pobrać plik z usługi GitHub, skopiuj zawartość do wybranego edytora tekstu, aby zapisać ją lokalnie jako plik CSV. Aby przekazać plik do konta magazynu, zobacz [przekazywanie obiektów BLOB za pomocą Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md). Przykłady odwołują się do kontenera o nazwie "przykładowe dane".

### <a name="navigate-to-the-synapse-studio"></a>Przejdź do Synapse Studio

Po utworzeniu obszaru roboczego usługi Azure Synapse dostępne są dwa sposoby otwierania programu Synapse Studio:

* Otwórz obszar roboczy Synapse w [Azure Portal](https://ms.portal.azure.com/#home). Wybierz pozycję **Otwórz** na karcie Otwórz kartę Synapse Studio w sekcji wprowadzenie.
* Otwórz [usługę Azure Synapse Analytics](https://web.azuresynapse.net/) i zaloguj się do swojego obszaru roboczego.

W tym przewodniku szybki start użyjemy obszaru roboczego o nazwie "adftest2020". Spowoduje to automatyczne przejście na stronę główną programu Synapse Studio.

![Strona główna programu Synapse Studio](media/doc-common-process/synapse-studio-home.png)

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>Tworzenie potoku za pomocą działania przepływu danych

Potok zawiera przepływ logiczny dla wykonywania zestawu działań. W tej sekcji utworzysz potok zawierający działanie przepływu danych.

1. Przejdź do karty **integracja** . Wybierz ikonę znaku plus obok nagłówka potoki i wybierz pozycję potok.

   ![Tworzenie nowego potoku](media/doc-common-process/new-pipeline.png)

1. Na stronie ustawienia **Właściwości** potoku wpisz **TransformMovies** w polu **Nazwa**.

1. W obszarze *Przenieś i Przekształć* w okienku *działania* przeciągnij **przepływ danych** na kanwę potoku.

1. Na stronie **Dodawanie przepływu danych** wybierz pozycję **Utwórz nowy**  ->  **przepływ danych** przepływu danych. Po zakończeniu kliknij przycisk **OK** .

   ![Tworzenie przepływu danych](media/quickstart-data-flow/new-data-flow.png)

1. Nazwij **TransformMovies** przepływu danych na stronie **Właściwości** .

## <a name="build-transformation-logic-in-the-data-flow-canvas"></a>Logika transformacji kompilacji na kanwie przepływu danych

Po utworzeniu przepływu danych zostanie on automatycznie wysłany do kanwy przepływu danych. W tym kroku utworzysz przepływ danych, który przyjmuje MoviesDB.csv w magazynie ADLS i agreguje średnią ocenę Comedies od 1910 do 2000. Następnie zapiszesz ten plik z powrotem do magazynu ADLS.

1. Przesuń suwak **debugowania przepływu danych** powyżej kanwy przepływu danych. Tryb debugowania umożliwia interaktywne Testowanie logiki transformacji na aktywnym klastrze Spark. Klastry przepływu danych zajmują 5-7 minut na rozgrzanie i zaleca się włączenie najpierw debugowania, jeśli planują one opracowywanie przepływu danych. Aby uzyskać więcej informacji, zobacz [tryb debugowania](../data-factory/concepts-data-flow-debug-mode.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json).

    ![Przesuń na slajdy debugowanie](media/quickstart-data-flow/debug-on.png)

1. Na kanwie przepływu danych Dodaj źródło, klikając je w polu **Dodaj źródło** .

1. Nadaj nazwę źródłową **MoviesDB**. Kliknij pozycję **Nowy** , aby utworzyć nowy źródłowy zestaw danych.

    ![Utwórz nowy źródłowy zestaw danych](media/quickstart-data-flow/new-source-dataset.png)

1. Wybierz **Azure Data Lake Storage Gen2**. Kliknij przycisk Kontynuuj.

    ![Wybierz Azure Data Lake Storage Gen2](media/quickstart-data-flow/select-source-dataset.png)

1. Wybierz **DelimitedText**. Kliknij przycisk Kontynuuj.

1. Nazwij zestaw danych **MoviesDB**. Z listy rozwijanej połączona usługa wybierz pozycję **Nowy**.

1. Na ekranie Tworzenie połączonej usługi nadaj ADLS Gen2 nazwę **ADLSGen2** połączonej usłudze i Określ metodę uwierzytelniania. Następnie wprowadź poświadczenia połączenia. W tym przewodniku szybki start korzystamy z klucza konta do nawiązywania połączenia z naszym kontem magazynu. Możesz kliknąć przycisk **Test connection** , aby sprawdzić, czy poświadczenia zostały wprowadzone poprawnie. Po zakończeniu kliknij przycisk **Utwórz** .

    ![Tworzenie połączonej usługi źródłowej](media/quickstart-data-flow/adls-gen2-linked-service.png)

1. Po ponownym uruchomieniu ekranu tworzenia zestawu danych, w polu **ścieżka pliku** wprowadź lokalizację pliku. W tym przewodniku szybki start plik "MoviesDB.csv" znajduje się w kontenerze "przykładowe dane". Ponieważ plik ma nagłówki, zaznacz **pierwszy wiersz jako nagłówek**. Wybierz pozycję **połączenie/magazyn** , aby zaimportować schemat nagłówka bezpośrednio z pliku w magazynie. Po zakończeniu kliknij przycisk **OK** .

    ![Ustawienia zestawu danych źródłowych](media/quickstart-data-flow/source-dataset-properties.png)

1. Jeśli klaster debugowania został uruchomiony, przejdź do karty **Podgląd danych** transformacji źródłowej i kliknij przycisk **Odśwież** , aby uzyskać migawkę danych. Możesz użyć podglądu danych, aby sprawdzić, czy transformacja została prawidłowo skonfigurowana.

    ![Podgląd danych](media/quickstart-data-flow/data-preview.png)

1. Kliknij ikonę znaku plus, aby dodać nową transformację obok węzła źródłowego na kanwie przepływu danych. Pierwszy dodawany przekształcenie to **Filtr**.

    ![Dodawanie filtru](media/quickstart-data-flow/add-filter.png)

1. Nazwij transformację filtru **FilterYears**. Kliknij pole wyrażenia obok pola **Filtr** , aby otworzyć Konstruktora wyrażeń. W tym miejscu określisz warunek filtrowania.

1. Konstruktor wyrażeń przepływu danych umożliwia interaktywną kompilację wyrażeń do użycia w różnych przekształceniach. Wyrażenia mogą zawierać wbudowane funkcje, kolumny ze schematu wejściowego i parametry zdefiniowane przez użytkownika. Aby uzyskać więcej informacji na temat sposobu kompilowania wyrażeń, zobacz [Data Flow Expression Builder](../data-factory/concepts-data-flow-expression-builder.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json).

    W tym przewodniku szybki start chcesz odfiltrować filmy z gatunku komedia, które zostały dostarczone między latami 1910 i 2000. Jako rok jest obecnie ciągiem, należy przekonwertować go na liczbę całkowitą przy użyciu ```toInteger()``` funkcji. Użyj opcji "większe niż lub równe" (>=) i mniejszej niż lub równej operatorowi (<=) do porównania z wartościami w postaci literału Year 1910 i 200-. Sumuj te wyrażenia razem z operatorem i (&&). Wyrażenie jest dostępne jako:

    ```toInteger(year) >= 1910 && toInteger(year) <= 2000```

    Aby znaleźć filmy, które są comediese, możesz użyć ```rlike()``` funkcji, aby znaleźć wzorzec "komedia" w gatunku kolumny. Utwórz wyrażenie rlike z porównaniem lat, aby uzyskać:

    ```toInteger(year) >= 1910 && toInteger(year) <= 2000 && rlike(genres, 'Comedy')```

    ![Określ warunek filtrowania](media/quickstart-data-flow/visual-expression-builder.png)

    Jeśli klaster debugowania jest aktywny, można sprawdzić logikę, klikając przycisk **Odśwież** , aby wyświetlić dane wyjściowe wyrażenia w porównaniu z używanymi danymi wejściowymi. Istnieje więcej niż jedna odpowiedź, na którą można wykonać tę logikę przy użyciu języka wyrażeń przepływu danych.

    Kliknij przycisk **Zapisz i Zakończ,** gdy skończysz pracę z wyrażeniem.

1. Pobierz **Podgląd danych** , aby sprawdzić, czy filtr działa poprawnie.

1. Kolejną przekształceniem, który dodasz, jest transformacja **zagregowana** w obszarze **modyfikator schematu**.

    ![Dodawanie agregacji](media/quickstart-data-flow/add-aggregate.png)

1. Nazwij **AggregateComedyRatings** przekształcenia agregacji. Na karcie **Grupuj według** wybierz z listy rozwijanej pozycję **Year** , aby grupować agregacje według roku, w którym znajduje się film.

    ![Ustawienia agregacji 1](media/quickstart-data-flow/aggregate-settings.png)

1. Przejdź do karty **agregaty** . W lewym polu tekstowym nadaj kolumnie agregującej **AverageComedyRating**. Kliknij pole wyrażenia z prawej strony, aby wprowadzić wyrażenie agregujące za pośrednictwem konstruktora wyrażeń.

    ![Ustawienia agregacji 2](media/quickstart-data-flow/aggregate-settings-2.png)

1. Aby uzyskać średnią **klasyfikację** kolumn, użyj ```avg()``` funkcji agregującej. Ponieważ **Klasyfikacja** jest ciągiem i ```avg()``` przyjmuje numeryczne dane wejściowe, należy przekonwertować wartość na liczbę za pośrednictwem ```toInteger()``` funkcji. To wyrażenie wygląda następująco:

    ```avg(toInteger(Rating))```

    Po zakończeniu kliknij przycisk **Zapisz i Zakończ** .

    ![Średnia Klasyfikacja komedia](media/quickstart-data-flow/average-comedy-rating.png)

1. Przejdź do karty **Podgląd danych** , aby wyświetlić dane wyjściowe transformacji. Zwróć uwagę na to, że w tym miejscu są dostępne tylko dwie kolumny — **Year** i **AverageComedyRating**.

    ![Agreguj dane zagregowane](media/quickstart-data-flow/transformation-output.png)

1. Następnie chcesz dodać transformację **ujścia** w **miejscu docelowym**.

    ![Dodawanie ujścia](media/quickstart-data-flow/add-sink.png)

1. Nazwij **ujścia** ujścia. Kliknij pozycję **Nowy** , aby utworzyć zestaw danych ujścia.

1. Wybierz **Azure Data Lake Storage Gen2**. Kliknij przycisk Kontynuuj.

1. Wybierz **DelimitedText**. Kliknij przycisk Kontynuuj.

1. Nazwij swój zestaw danych ujścia **MoviesSink**. W polu połączona usługa wybierz połączoną usługę ADLS Gen2 utworzoną w kroku 7. Wprowadź folder wyjściowy, w którym mają zostać zapisane dane. W tym przewodniku szybki start zapisujemy do folderu "output" w kontenerze "przykładowe dane". Folder nie musi istnieć wcześniej i może być tworzony dynamicznie. Ustaw **pierwszy wiersz jako nagłówek** jako wartość true, a następnie wybierz pozycję **Brak** dla **schematu importowania**. Po zakończeniu kliknij przycisk **OK** .

    ![Właściwości zestawu danych ujścia](media/quickstart-data-flow/sink-dataset-properties.png)

Teraz zakończono Kompilowanie przepływu danych. Wszystko jest gotowe do uruchomienia w potoku.

## <a name="running-and-monitoring-the-data-flow"></a>Uruchamianie i monitorowanie przepływu danych

Można debugować potok przed jego opublikowaniem. W tym kroku wywołajesz przebieg debugowania potoku przepływu danych. Gdy Podgląd danych nie zapisuje danych, uruchomienie debugowania spowoduje zapisanie danych w miejscu docelowym ujścia.

1. Przejdź do kanwy potoku. Kliknij pozycję **Debuguj** , aby wyzwolić uruchomienie debugowania.

    ![Debuguj potok](media/quickstart-data-flow/debug-pipeline.png)

1. Debugowanie potoku działań przepływu danych korzysta z aktywnego klastra debugowania, ale nadal trwa co najmniej minutę. Postęp można śledzić za pomocą karty **dane wyjściowe** . Po pomyślnym uruchomieniu kliknij ikonę okularów, aby otworzyć okienko monitorowanie.

    ![Dane wyjściowe debugowania](media/quickstart-data-flow/debugging-output.png)

1. W okienku Monitorowanie można zobaczyć liczbę wierszy i czas spędzony w każdym kroku transformacji.

    ![Monitorowanie transformacji](media/quickstart-data-flow/4-transformations.png)

1. Kliknij transformację, aby uzyskać szczegółowe informacje na temat kolumn i partycjonowania danych.

    ![Szczegóły przekształcenia](media/quickstart-data-flow/transformation-details.png)

Jeśli wykonano ten przewodnik Szybki Start, należy napisać 83 wierszy i 2 kolumny do folderu ujścia. Możesz sprawdzić dane, sprawdzając magazyn obiektów BLOB.


## <a name="next-steps"></a>Następne kroki

Przejdź do następujących artykułów, aby dowiedzieć się więcej o obsłudze usługi Azure Synapse Analytics:

> [!div class="nextstepaction"]
> [Potok i działania](../data-factory/concepts-pipelines-activities.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json) 
>  [Mapowanie przepływu danych — omówienie](../data-factory/concepts-data-flow-overview.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json) 
>  [Język wyrażeń przepływu danych](../data-factory/data-flow-expression-functions.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)