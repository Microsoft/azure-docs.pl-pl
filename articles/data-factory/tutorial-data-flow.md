---
title: Przekształcanie danych przy użyciu przepływu danych mapowania
description: Ten samouczek zawiera instrukcje krok po kroku dotyczące korzystania z usługi Azure Data Factory w celu przekształcania danych za pomocą mapowania przepływu danych
author: djpmsft
ms.author: daperlov
ms.reviewer: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/07/2019
ms.openlocfilehash: 917a8d6edf04d8a160c3a6a5ac59949623dfee5c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418681"
---
# <a name="transform-data-using-mapping-data-flows"></a>Przekształcanie danych przy użyciu przepływów danych mapowania

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Jeśli jesteś nowym użytkownikiem usługi Azure Data Factory, zobacz [Wprowadzenie do usługi Azure Data Factory](introduction.md).

W tym samouczku użyjesz interfejsu użytkownika usługi Azure Data Factory (UX) do utworzenia potoku, który kopiuje i przekształca dane ze źródła usługi Azure Data Lake Storage (ADLS) Gen2 do ujścia ADLS Gen2 przy użyciu przepływu danych mapowania. Wzorzec konfiguracji w tym samouczku można rozszerzyć podczas przekształcania danych przy użyciu przepływu danych mapowania

W tym samouczku wykonasz następujące czynności:

> [!div class="checklist"]
> * Tworzenie fabryki danych.
> * Utwórz potok z działaniem przepływu danych.
> * Tworzenie przepływu danych mapowania z czterech przekształceń.
> * Testowe uruchamianie potoku.
> * Monitorowanie aktywności przepływu danych

## <a name="prerequisites"></a>Wymagania wstępne
* **Subskrypcja platformy Azure**. Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/) przed rozpoczęciem.
* **Konto magazynu platformy Azure**. Używany magazyn ADLS jako *źródło* i magazyny danych *ujścia.* Jeśli nie masz konta magazynu, utwórz je, wykonując czynności przedstawione w artykule [Tworzenie konta magazynu platformy Azure](../storage/common/storage-account-create.md).

Plik, który przekształcamy w tym poradniku jest MoviesDB.csv, który można znaleźć [tutaj](https://raw.githubusercontent.com/djpmsft/adf-ready-demo/master/moviesDB.csv). Aby pobrać plik z gitHub, skopiuj zawartość do wybranego edytora tekstu, aby zapisać go lokalnie jako plik csv. Aby przekazać plik do konta magazynu, zobacz [Przekazywanie obiektów blob za pomocą witryny Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md). Przykłady będą odwoływać się do kontenera o nazwie "sample-data".

## <a name="create-a-data-factory"></a>Tworzenie fabryki danych

W tym kroku należy utworzyć fabrykę danych i otworzyć środowisko UX fabryki danych, aby utworzyć potok w fabryce danych.

1. Otwórz przeglądarkę **Microsoft Edge** lub **Google Chrome**. Obecnie interfejs użytkownika fabryki danych jest obsługiwany tylko w przeglądarkach internetowych Microsoft Edge i Google Chrome.
2. W menu po lewej stronie wybierz pozycję **Utwórz fabrykę** > **danych****analizy** > zasobów:

   ![Wybór usługi Data Factory w okienku „Nowy”](./media/doc-common-process/new-azure-data-factory-menu.png)

3. Na stronie **Nowa fabryka danych** w polu **Nazwa** wprowadź wartość **ADFTutorialDataFactory**.

   Nazwa fabryki danych platformy Azure musi być *unikatowa globalnie.* Jeśli zostanie wyświetlony komunikat o błędzie dotyczącym wartości nazwy, wprowadź inną nazwę dla fabryki danych. (na przykład yournameADFTutorialDataFactory). Reguły nazewnictwa dla artefaktów usługi Data Factory można znaleźć w artykule [Data Factory — reguły nazewnictwa](naming-rules.md).

     ![Nowa fabryka danych](./media/doc-common-process/name-not-available-error.png)
4. Wybierz **subskrypcję** platformy Azure, w której chcesz utworzyć fabrykę danych.
5. W obszarze **Grupa zasobów** wykonaj jedną z następujących czynności:

    a. Wybierz pozycję **Użyj istniejącej**, a następnie wybierz istniejącą grupę zasobów z listy rozwijanej.

    b. Wybierz **pozycję Utwórz nowy**i wprowadź nazwę grupy zasobów. 
         
    Informacje na temat grup zasobów znajdują się w artykule [Using resource groups to manage your Azure resources (Używanie grup zasobów do zarządzania zasobami platformy Azure)](../azure-resource-manager/management/overview.md). 
6. W obszarze **Wersja** wybierz pozycję **V2**.
7. W obszarze **Lokalizacja** wybierz lokalizację fabryki danych. Na liście rozwijanej są wyświetlane tylko obsługiwane lokalizacje. Magazyny danych (na przykład usługa Azure Storage i baza danych SQL) i obliczeń (na przykład usługi Azure HDInsight) używanych przez fabrykę danych mogą znajdować się w innych regionach.
8. Wybierz pozycję **Utwórz**.
9. Po zakończeniu tworzenia zostanie wyświetlone powiadomienie w Centrum powiadomień. Wybierz **pozycję Przejdź do zasobu,** aby przejść do strony Fabryka danych.
10. Wybierz pozycję **Tworzenie i monitorowanie**, aby uruchomić interfejs użytkownika usługi Data Factory na osobnej karcie.

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>Tworzenie potoku z działaniem przepływu danych

W tym kroku utworzysz potok, który zawiera działanie przepływu danych.

1. Na stronie **Zaczynajmy** wybierz pozycję **Utwórz potok**.

   ![Tworzenie potoku](./media/doc-common-process/get-started-page.png)

1. Na karcie **Ogólne** dla potoku wprowadź **transformmovies** dla **nazwy** potoku.
1. Na pasku głównym fabryki przesuń suwak **debugowania przepływa danych.** Tryb debugowania umożliwia interaktywne testowanie logiki transformacji względem klastra platformy Spark na żywo. Klastry przepływu danych trwa 5-7 minut, aby rozgrzać i użytkownicy są zalecane, aby włączyć debugowanie najpierw, jeśli planują zrobić data flow rozwoju. Aby uzyskać więcej informacji, zobacz [Tryb debugowania](concepts-data-flow-debug-mode.md).

    ![Aktywność przepływu danych](media/tutorial-data-flow/dataflow1.png)
1. W okienku **Działania** rozwiń akordeon **Przenieś i Przekształć.** Przeciągnij i upuść działanie **Przepływ danych** z okienka do kanwy potoku.

    ![Aktywność przepływu danych](media/tutorial-data-flow/activity1.png)
1. W **wyskakującym** okienku Dodawanie przepływu danych wybierz pozycję **Utwórz nowy przepływ danych,** a następnie nazwij przepływ danych **TransformMovies**. Po zakończeniu kliknij przycisk Zakończ.

    ![Aktywność przepływu danych](media/tutorial-data-flow/activity2.png)

## <a name="build-transformation-logic-in-the-data-flow-canvas"></a>Tworzenie logiki transformacji w kanwie przepływu danych

Po utworzeniu przepływu danych zostaniesz automatycznie wysłany do obszaru roboczego przepływu danych. W tym kroku zbudujemy przepływ danych, który zajmuje moviesDB.csv w pamięci masowej ADLS i agreguje średnią ocenę komedii od 1910 do 2000. Następnie zapiszesz ten plik z powrotem do magazynu ADLS.

1. Na kanwie przepływu danych dodaj źródło, klikając pole **Dodaj źródło.**

    ![Kanwa przepływu danych](media/tutorial-data-flow/dataflow2.png)
1. Nazwij źródło **MoviesDB**. Kliknij **przycisk Nowy,** aby utworzyć nowy źródłowy zestaw danych.

    ![Kanwa przepływu danych](media/tutorial-data-flow/dataflow3.png)
1. Wybierz **pozycję Azure Data Lake Storage Gen2**. Kliknij przycisk Kontynuuj.

    ![Dataset](media/tutorial-data-flow/dataset1.png)
1. Wybierz pozycję **Tekst rozdzielany**. Kliknij przycisk Kontynuuj.

    ![Dataset](media/tutorial-data-flow/dataset2.png)
1. Nadaj nazwę zestawowi danych **MoviesDB**. Z listy rozwijanej połączonej usługi wybierz pozycję **Nowy**.

    ![Dataset](media/tutorial-data-flow/dataset3.png)
1. Na ekranie tworzenia usługi połączonej nazwij usługę adls gen2 **adlsgen2** i określ metodę uwierzytelniania. Następnie wprowadź poświadczenia połączenia. W tym samouczku używamy klucza konta, aby połączyć się z naszym kontem magazynu. Możesz kliknąć **przycisk Testuj połączenie,** aby sprawdzić, czy poświadczenia zostały wprowadzone poprawnie. Po zakończeniu kliknij pozycję Utwórz.

    ![Usługa połączona](media/tutorial-data-flow/ls1.png)
1. Po powrocie na ekran tworzenia zestawu danych wprowadź miejsce, w którym znajduje się plik w polu **Ścieżka pliku.** W tym samouczku plik moviesDB.csv znajduje się w próbkach kontenera danych. Ponieważ plik ma nagłówki, zaznacz pierwszy **wiersz jako nagłówek**. Wybierz **opcję Z połączenia/magazynu,** aby zaimportować schemat nagłówka bezpośrednio z pliku w magazynie. Po zakończeniu kliknij przycisk OK.

    ![Zestawy danych](media/tutorial-data-flow/dataset4.png)
1. Jeśli klaster debugowania został uruchomiony, przejdź do karty **Podgląd danych** transformacji źródła i kliknij przycisk **Odśwież,** aby uzyskać migawkę danych. Za pomocą podglądu danych można sprawdzić, czy transformacja jest poprawnie skonfigurowana.

    ![Kanwa przepływu danych](media/tutorial-data-flow/dataflow4.png)
1. Obok węzła źródłowego na kanwie przepływu danych kliknij ikonę plus, aby dodać nową transformację. Pierwszą dodaną transformacją jest **filtr**.

    ![Kanwa przepływu danych](media/tutorial-data-flow/dataflow5.png)
1. Nazwij transformację filtru **FilterYears**. Kliknij pole wyrażenia obok **pozycji Filtruj,** aby otworzyć konstruktora wyrażeń. W tym miejscu określ stan filtrowania.

    ![Filtr](media/tutorial-data-flow/filter1.png)
1. Konstruktor wyrażeń przepływu danych umożliwia interaktywne tworzenie wyrażeń do użycia w różnych przekształceniach. Wyrażenia mogą zawierać wbudowane funkcje, kolumny ze schematu wejściowego i parametry zdefiniowane przez użytkownika. Aby uzyskać więcej informacji na temat tworzenia wyrażeń, zobacz [Konstruktor wyrażeń przepływ danych](concepts-data-flow-expression-builder.md).

    W tym samouczku, chcesz filtrować filmy komedii gatunku, który wyszedł między latami 1910 i 2000. Ponieważ rok jest obecnie ciągiem, należy przekonwertować go ```toInteger()``` na całkowitą za pomocą funkcji. Użyj operatorów większych lub równych (>=) i mniej niż lub równych (<=) operatorów, aby porównać je z wartościami roku dosłownego 1910 i 200-. te wyrażenia wraz z operatorem i (&&). Wyrażenie wychodzi jako:

    ```toInteger(year) >= 1910 && toInteger(year) <= 2000```

    Aby dowiedzieć się, które filmy są ```rlike()``` komedie, można użyć funkcji, aby znaleźć wzór "Komedia" w gatunkach kolumn. Unii rlike wyrażenie z porównaniem roku, aby uzyskać:

    ```toInteger(year) >= 1910 && toInteger(year) <= 2000 && rlike(genres, 'Comedy')```

    Jeśli masz aktywny klaster debugowania, możesz zweryfikować swoją logikę, klikając **przycisk Odśwież,** aby wyświetlić dane wyjściowe wyrażenia w porównaniu z użytymi wejściami. Istnieje więcej niż jedna właściwa odpowiedź na temat sposobu wykonania tej logiki przy użyciu języka wyrażenia przepływu danych.

    ![Filtr](media/tutorial-data-flow/filter2.png)

    Po zakończeniu wyrażenia kliknij pozycję **Zapisz i zakończ.**

1. Pobierz **podgląd danych,** aby sprawdzić, czy filtr działa poprawnie.

    ![Filtr](media/tutorial-data-flow/filter3.png)
1. Następna transformacja, którą dodasz, to transformacja **agregująca** w obszarze **modyfikatora schematu**.

    ![Agregacja](media/tutorial-data-flow/agg1.png)
1. Nazwij swoją agregację transformacji **AggregateComedyRatings**. Na karcie **Grupa według** wybierz **rok** z listy rozwijanej, aby pogrupować agregacje według roku, w który film wyszedł.

    ![Agregacja](media/tutorial-data-flow/agg2.png)
1. Przejdź do karty **Agreguj.** W lewym polu tekstowym nazwij kolumnę agregującą **AverageComedyRating**. Kliknij pole wyrażenia po prawej stronie, aby wprowadzić wyrażenie agregacji za pośrednictwem konstruktora wyrażeń.

    ![Agregacja](media/tutorial-data-flow/agg3.png)
1. Aby uzyskać średnią **klasyfikacji**kolumny, ```avg()``` użyj funkcji agregującej. Ponieważ **Ocena** jest ```avg()``` ciągiem i przyjmuje dane liczbowe, musimy przekonwertować wartość na liczbę za pośrednictwem ```toInteger()``` funkcji. To wyrażenie wygląda tak:

    ```avg(toInteger(Rating))```

    Po zakończeniu kliknij **pozycję Zapisz i zakończ.**

    ![Agregacja](media/tutorial-data-flow/agg4.png)
1. Przejdź do karty **Podgląd danych,** aby wyświetlić dane wyjściowe transformacji. Zwróć uwagę, że są tylko dwie kolumny, **rok** i **AverageComedyRating**.

    ![Agregacja](media/tutorial-data-flow/agg3.png)
1. Następnie chcesz dodać transformację **zlewu** w obszarze **Miejsce docelowe**.

    ![Ujście](media/tutorial-data-flow/sink1.png)
1. Nazwij **zlew zlew .** Kliknij **przycisk Nowy,** aby utworzyć zestaw danych ujścia.

    ![Ujście](media/tutorial-data-flow/sink2.png)
1. Wybierz **pozycję Azure Data Lake Storage Gen2**. Kliknij przycisk Kontynuuj.

    ![Dataset](media/tutorial-data-flow/dataset1.png)
1. Wybierz pozycję **Tekst rozdzielany**. Kliknij przycisk Kontynuuj.

    ![Dataset](media/tutorial-data-flow/dataset2.png)
1. Nazwij swój zestaw danych **ujścia MoviesSink**. W przypadku usługi połączonej wybierz usługę połączony ADLS gen2 utworzoną w kroku 6. Wprowadź folder wyjściowy, w który chcesz zapisać dane. W tym samouczku piszemy do folderu "dane wyjściowe" w kontenerze "sample-data". Folder nie musi istnieć wcześniej i może być tworzony dynamicznie. Ustaw **pierwszy wiersz jako nagłówek** jako prawdziwy i wybierz opcję **Brak** dla **schematu importu**. Kliknij przycisk Zakończ.

    ![Ujście](media/tutorial-data-flow/sink3.png)

Teraz masz gotowe do tworzenia przepływu danych. Możesz go uruchomić w potoku.

## <a name="running-and-monitoring-the-data-flow"></a>Uruchamianie i monitorowanie przepływu danych

Można debugować potoku przed jego opublikowaniem. W tym kroku masz zamiar wyzwolić uruchomienie debugowania potoku przepływu danych. Podczas gdy podgląd danych nie zapisuje danych, uruchom debugowanie będzie zapisywać dane do miejsca docelowego ujścia.

1. Przejdź do kanwy potoku. Kliknij **przycisk Debugowanie,** aby wyzwolić uruchomienie debugowania.

    ![Potok](media/tutorial-data-flow/pipeline1.png)
1. Debugowanie potoku działań przepływu danych używa aktywnego klastra debugowania, ale nadal trwa co najmniej minutę, aby zainicjować. Postęp można śledzić za pomocą karty **Dane wyjściowe.** Po pomyślnym uruchomieniu kliknij ikonę okularów, aby otworzyć okienko monitorowania.

    ![Potok](media/tutorial-data-flow/pipeline2.png)
1. W okienku monitorowania można zobaczyć liczbę wierszy i czas spędzony w każdym kroku transformacji.

    ![Monitorowanie](media/tutorial-data-flow/pipeline3.png)
1. Kliknij transformację, aby uzyskać szczegółowe informacje na temat kolumn i partycjonowania danych.

    ![Monitorowanie](media/tutorial-data-flow/pipeline4.png)

Jeśli po tym kursie poprawnie, należy napisać 83 wiersze i 2 kolumny do folderu ujścia. Można sprawdzić, czy dane są poprawne, sprawdzając magazyn obiektów blob.

## <a name="next-steps"></a>Następne kroki

Potok w tym samouczku uruchamia przepływ danych, który agreguje średnią ocenę komedii od 1910 do 2000 i zapisuje dane do ADLS. W tym samouczku omówiono:

> [!div class="checklist"]
> * Tworzenie fabryki danych.
> * Utwórz potok z działaniem przepływu danych.
> * Tworzenie przepływu danych mapowania z czterech przekształceń.
> * Testowe uruchamianie potoku.
> * Monitorowanie aktywności przepływu danych

Dowiedz się więcej o [języku wyrażeń przepływu danych](data-flow-expression-functions.md).
