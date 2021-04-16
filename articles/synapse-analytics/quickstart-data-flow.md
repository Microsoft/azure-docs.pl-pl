---
title: 'Szybki start: przekształcanie danych przy użyciu przepływu danych mapowania'
description: Ten samouczek zawiera instrukcje krok po kroku dotyczące używania Azure Synapse Analytics przekształcania danych za pomocą przepływu danych mapowania.
author: djpmsft
ms.author: daperlov
ms.reviewer: makromer
ms.service: synapse-analytics
ms.subservice: pipeline
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/03/2020
ms.openlocfilehash: 37696d2f4054e46125b39f3d5efa794ce54f94b5
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567727"
---
# <a name="quickstart-transform-data-using-mapping-data-flows"></a>Szybki start: przekształcanie danych przy użyciu przepływów danych mapowania

W tym przewodniku Szybki start użyjesz usługi Azure Synapse Analytics do utworzenia potoku, który przekształca dane ze źródła Azure Data Lake Storage Gen2 (ADLS Gen2) do ujścia usługi ADLS Gen2 przy użyciu przepływu danych mapowania. Wzorzec konfiguracji w tym przewodniku Szybki start można rozszerzyć podczas przekształcania danych przy użyciu przepływu danych mapowania

W tym przewodniku Szybki start wykonaj następujące czynności:

> [!div class="checklist"]
> * Utwórz potok z działaniem Przepływ danych w Azure Synapse Analytics.
> * Tworzenie przepływu danych mapowania z czterema przekształceniami.
> * Testowe uruchamianie potoku.
> * Monitorowanie Przepływ danych danych

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**: jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).
* **Azure Synapse:** utwórz obszar roboczy usługi Synapse przy użyciu usługi Azure Portal zgodnie z instrukcjami w przewodniku [Szybki start: Tworzenie obszaru roboczego usługi Synapse.](quickstart-create-workspace.md)
* **Konto usługi Azure Storage:** magazyn usługi ADLS jest *magazynem* danych źródła i ujścia.  Jeśli nie masz konta magazynu, utwórz je, wykonując czynności przedstawione w artykule [Tworzenie konta magazynu platformy Azure](../storage/common/storage-account-create.md).

    Plik, który przekształcamy w tym samouczku, to MoviesDB.csv, który można znaleźć [tutaj.](https://raw.githubusercontent.com/djpmsft/adf-ready-demo/master/moviesDB.csv) Aby pobrać plik z usługi GitHub, skopiuj jego zawartość do wybranego edytora tekstów, aby zapisać go lokalnie jako plik CSV. Aby przekazać plik na konto magazynu, zobacz [Przekazywanie obiektów blob za pomocą Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md). Przykłady będą odwoływać się do kontenera o nazwie "sample-data".

### <a name="navigate-to-the-synapse-studio"></a>Przejdź do Synapse Studio

Po utworzeniu Azure Synapse roboczego masz dwa sposoby otwierania Synapse Studio:

* Otwórz obszar roboczy synapse w [Azure Portal](https://ms.portal.azure.com/#home). Wybierz **pozycję Otwórz** na karcie Otwórz Synapse Studio w obszarze Wprowadzenie.
* Otwórz [Azure Synapse Analytics](https://web.azuresynapse.net/) i zaloguj się do obszaru roboczego.

W tym przewodniku Szybki start jako przykładu użyjemy obszaru roboczego o nazwie "adftest2020". Spowoduje to automatyczne przejście do strony Synapse Studio głównej.

![Synapse Studio strona główna](media/doc-common-process/synapse-studio-home.png)

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>Tworzenie potoku za pomocą Przepływ danych działania

Potok zawiera logiczny przepływ wykonywania zestawu działań. W tej sekcji utworzysz potok, który zawiera Przepływ danych działania.

1. Przejdź do karty **Integracja.** Wybierz ikonę plusa obok nagłówka potoków, a następnie wybierz pozycję Potok.

   ![Tworzenie nowego potoku](media/doc-common-process/new-pipeline.png)

1. Na stronie **Ustawienia** właściwości potoku wprowadź **transformaty w nazwie** . 

1. W *obszarze Przenoszenie i przekształcanie* w *okienku Działania* przeciągnij pozycję Przepływ **danych** na kanwę potoku.

1. W **oknie podręcznym Dodawanie** przepływu danych wybierz pozycję **Utwórz nowy przepływ danych** Przepływ  ->  **danych.** Gdy wszystko **będzie gotowe,** kliknij przycisk OK.

   ![Tworzenie przepływu danych](media/quickstart-data-flow/new-data-flow.png)

1. Nazwij **przepływ danych Transform (Przekształcanie) na** stronie Properties **(Właściwości).**

## <a name="build-transformation-logic-in-the-data-flow-canvas"></a>Tworzenie logiki przekształcania na kanwie przepływu danych

Po utworzeniu Przepływ danych zostaniesz automatycznie wysłany do kanwy przepływu danych. W tym kroku zostanie skompilowany przepływ danych, który przyjmuje MoviesDB.csv w magazynie usługi ADLS i agreguje średnią ocenę comedies z lat 1910–2000. Następnie zapiszesz ten plik z powrotem w magazynie usługi ADLS.

1. Nad kanwą przepływu danych przesuń suwak **Debugowanie przepływu** danych wł. Tryb debugowania umożliwia interaktywne testowanie logiki przekształcania względem klastra Spark na żywo. Przepływ danych rozgrzewki potrwają 5–7 minut, a użytkownikom zaleca się włączenie debugowania najpierw, jeśli planują wykonać Przepływ danych dewelopera. Aby uzyskać więcej informacji, zobacz [Tryb debugowania](../data-factory/concepts-data-flow-debug-mode.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json).

    ![Przesuń suwak debugowania na](media/quickstart-data-flow/debug-on.png)

1. Na kanwie przepływu danych dodaj źródło, klikając pole **Dodaj źródło.**

1. Nadaj źródłowej **bazie danych MoviesDB nazwę**. Kliknij pozycję **Nowy,** aby utworzyć nowy źródłowy zestaw danych.

    ![Tworzenie nowego źródłowego zestawu danych](media/quickstart-data-flow/new-source-dataset.png)

1. Wybierz **Azure Data Lake Storage Gen2**. Kliknij przycisk Kontynuuj.

    ![Wybierz Azure Data Lake Storage Gen2](media/quickstart-data-flow/select-source-dataset.png)

1. Wybierz **pozycję RozdzielanyTekst**. Kliknij przycisk Kontynuuj.

1. Nadaj zestawowi **danych nazwę MoviesDB.** Na liście rozwijanej połączona usługa wybierz pozycję **Nowy.**

1. Na ekranie tworzenia połączonej usługi nadaj nazwę ADLS Gen2 **usłudze ADLSGen2** i określ metodę uwierzytelniania. Następnie wprowadź poświadczenia połączenia. W tym przewodniku Szybki start używamy klucza konta do nawiązania połączenia z kontem magazynu. Możesz kliknąć przycisk **Test connection,** aby sprawdzić, czy poświadczenia zostały wprowadzone poprawnie. Po **zakończeniu kliknij** przycisk Utwórz.

    ![Tworzenie połączonej źródłowej usługi](media/quickstart-data-flow/adls-gen2-linked-service.png)

1. Po powrocie do ekranu tworzenia zestawu danych w polu **Ścieżka** pliku wprowadź miejsce, w którym znajduje się plik. W tym przewodniku Szybki start plik "MoviesDB.csv" znajduje się w kontenerze "sample-data". Ponieważ plik ma nagłówki, zaznacz **pozycję Pierwszy wiersz jako nagłówek**. Wybierz **pozycję Z połączenia/magazynu,** aby zaimportować schemat nagłówka bezpośrednio z pliku w magazynie. Gdy **wszystko będzie gotowe,** kliknij przycisk OK.

    ![Ustawienia zestawu danych źródłowych](media/quickstart-data-flow/source-dataset-properties.png)

1. Jeśli klaster debugowania został uruchomiony, przejdź do karty **Podgląd** danych przekształcenia źródła i kliknij **przycisk** Odśwież, aby pobrać migawkę danych. Podgląd danych umożliwia sprawdzenie, czy przekształcenie jest poprawnie skonfigurowane.

    ![Podgląd danych](media/quickstart-data-flow/data-preview.png)

1. Obok węzła źródłowego na kanwie przepływu danych kliknij ikonę plusa, aby dodać nowe przekształcenie. Pierwsze przekształcenie, które dodajesz, to **filtr**.

    ![Dodawanie filtru](media/quickstart-data-flow/add-filter.png)

1. Nazwij przekształcenie **filtru FilterYears .** Kliknij pole wyrażenia obok pola **Filtruj,** aby otworzyć konstruktora wyrażeń. W tym miejscu określisz warunek filtrowania.

1. Konstruktor wyrażeń przepływu danych umożliwia interaktywne tworzenie wyrażeń do użycia w różnych przekształceniach. Wyrażenia mogą zawierać wbudowane funkcje, kolumny ze schematu wejściowego i parametry zdefiniowane przez użytkownika. Aby uzyskać więcej informacji na temat tworzenia wyrażeń, zobacz [Przepływ danych konstruktora wyrażeń](../data-factory/concepts-data-flow-expression-builder.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json).

    W tym przewodniku Szybki start chcesz filtrować filmy z gatunku, które pojawiły się w latach 1910–2000. Ponieważ rok jest obecnie ciągiem, należy przekonwertować go na liczbę całkowitą przy użyciu ```toInteger()``` funkcji . Użyj operatory większe niż lub równe (>=) i mniejsze niż lub równe (<=) do porównania z wartościami literału roku 1910 i 200-. Te wyrażenia są połączone z operatorem i (&&). Wyrażenie jest wyrażane w następujący sposób:

    ```toInteger(year) >= 1910 && toInteger(year) <= 2000```

    Aby dowiedzieć się, które filmy są comedies, możesz użyć funkcji , aby znaleźć wzorzec ```rlike()``` "Stan" w gatunekach kolumn. Zsuń wyrażenie rlike z porównaniem roku, aby uzyskać:

    ```toInteger(year) >= 1910 && toInteger(year) <= 2000 && rlike(genres, 'Comedy')```

    ![Określanie warunku filtrowania](media/quickstart-data-flow/visual-expression-builder.png)

    Jeśli masz aktywny klaster debugowania, możesz zweryfikować logikę, klikając pozycję **Odśwież,** aby wyświetlić dane wyjściowe wyrażenia w porównaniu do użytych danych wejściowych. Istnieje więcej niż jedna właściwa odpowiedź na pytanie, jak można wykonać tę logikę przy użyciu języka wyrażeń przepływu danych.

    Po **zakończeniu pracy** z wyrażeniem kliknij pozycję Zapisz i zakończ.

1. Pobierz podgląd **danych,** aby sprawdzić, czy filtr działa prawidłowo.

1. Następne przekształcenie, które dodasz, to agregacja **przekształcenia** w obszarze **Modyfikator schematu**.

    ![Dodawanie agregacji](media/quickstart-data-flow/add-aggregate.png)

1. Nazwij przekształcenie **agregujące AggregateComedyRatings**. Na karcie **Grupuj** według wybierz **rok** z listy rozwijanej, aby pogrupować agregacje według roku, w którym pojawił się film.

    ![Ustawienia agregacji 1](media/quickstart-data-flow/aggregate-settings.png)

1. Przejdź do **karty Agregacje.** W polu tekstowym po lewej stronie nadaj kolumnie zagregowanej **nazwę AverageComedyRating.** Kliknij prawe pole wyrażenia, aby wprowadzić wyrażenie agregowania za pomocą konstruktora wyrażeń.

    ![Ustawienia agregacji 2](media/quickstart-data-flow/aggregate-settings-2.png)

1. Aby uzyskać średnią kolumny **Ocena,** użyj ```avg()``` funkcji agregowania. Ponieważ **funkcja Rating** jest ciągiem i przyjmuje liczbowe dane wejściowe, musimy przekonwertować wartość na liczbę za ```avg()``` pośrednictwem funkcji ```toInteger()``` . To wyrażenie wygląda następująco:

    ```avg(toInteger(Rating))```

    Po **zakończeniu kliknij pozycję Zapisz i** zakończ.

    ![Średnia ocena dla klasyfikacji na poziomie 100](media/quickstart-data-flow/average-comedy-rating.png)

1. Przejdź do karty **Podgląd danych,** aby wyświetlić dane wyjściowe przekształcenia. Zwróć uwagę, że istnieją tylko dwie kolumny: **year i** **AverageComedyRating**.

    ![Podgląd agregowania danych](media/quickstart-data-flow/transformation-output.png)

1. Następnie chcesz dodać przekształcenie  ujścia w obszarze **Miejsce docelowe**.

    ![Dodawanie ujścia](media/quickstart-data-flow/add-sink.png)

1. Nadaj ujściu **nazwę ujścia**. Kliknij **pozycję Nowy,** aby utworzyć zestaw danych ujścia.

1. Wybierz **Azure Data Lake Storage Gen2**. Kliknij przycisk Kontynuuj.

1. Wybierz **pozycję OgranicznikTekst**. Kliknij przycisk Kontynuuj.

1. Nadaj zestawowi danych **ujścia nazwę MoviesSink.** W przypadku połączonej usługi wybierz ADLS Gen2 utworzoną w kroku 7. Wprowadź folder wyjściowy, w którym będą zapisywane dane. W tym przewodniku Szybki start piszemy do folderu "output" w kontenerze "sample-data". Folder nie musi istnieć wcześniej i można go utworzyć dynamicznie. Ustaw **opcję Pierwszy wiersz jako nagłówek na** wartość true i wybierz opcję Brak **dla** **opcji Importuj schemat.** Gdy **wszystko będzie gotowe,** kliknij przycisk OK.

    ![Właściwości zestawu danych ujścia](media/quickstart-data-flow/sink-dataset-properties.png)

Teraz ukończono tworzenie przepływu danych. Wszystko jest gotowe do uruchomienia w potoku.

## <a name="running-and-monitoring-the-data-flow"></a>Uruchamianie i monitorowanie Przepływ danych

Potok można debugować przed jego opublikowaniem. W tym kroku wyzwolisz uruchomienie debugowania potoku przepływu danych. Podczas gdy podgląd danych nie zapisuje danych, przebieg debugowania zapisze dane w miejscu docelowym ujścia.

1. Przejdź do kanwy potoku. Kliknij **pozycję Debuguj,** aby wyzwolić przebieg debugowania.

    ![Debugowanie potoku](media/quickstart-data-flow/debug-pipeline.png)

1. Debugowanie potoku Przepływ danych korzysta z aktywnego klastra debugowania, ale inicjowanie nadal trwa co najmniej minutę. Postęp można śledzić za pomocą karty **Dane** wyjściowe. Po pomyślnym uruchomieniu kliknij ikonę okularów, aby otworzyć okienko monitorowania.

    ![Debugowanie danych wyjściowych](media/quickstart-data-flow/debugging-output.png)

1. W okienku monitorowania można zobaczyć liczbę wierszy i czas spędzony w poszczególnych krokach przekształcania.

    ![Monitorowanie przekształcania](media/quickstart-data-flow/4-transformations.png)

1. Kliknij przekształcenie, aby uzyskać szczegółowe informacje na temat kolumn i partycjonowania danych.

    ![Szczegóły przekształcenia](media/quickstart-data-flow/transformation-details.png)

Jeśli ten przewodnik Szybki start został wykonane prawidłowo, w folderze ujścia powinny być zapisane 83 wiersze i 2 kolumny. Możesz zweryfikować dane, sprawdzając magazyn obiektów blob.


## <a name="next-steps"></a>Następne kroki

Zapoznaj się z następującymi artykułami, aby dowiedzieć się więcej Azure Synapse Analytics pomocy technicznej:

> [!div class="nextstepaction"]
> [Potok i działania](../data-factory/concepts-pipelines-activities.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json) 
>  [Omówienie przepływu danych mapowania](../data-factory/concepts-data-flow-overview.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json) 
>  [Język wyrażeń przepływu danych](../data-factory/data-flow-expression-functions.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)