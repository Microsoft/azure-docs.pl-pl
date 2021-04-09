---
title: Różnicowe Lake ETL z przepływami danych
description: Ten samouczek zawiera instrukcje krok po kroku dotyczące korzystania z przepływów danych w celu przekształcania i analizowania danych w ramach różnicowej usługi Lake
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2021
ms.date: 02/09/2021
ms.openlocfilehash: cb8d44353e826df14ed3baab2c4ca66ffed4a569
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100417662"
---
# <a name="transform-data-in-delta-lake-using-mapping-data-flows"></a>Przekształcanie danych w usłudze Delta Lake przy użyciu mapowania przepływów danych

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Jeśli jesteś nowym użytkownikiem usługi Azure Data Factory, zobacz [Wprowadzenie do usługi Azure Data Factory](introduction.md).

W tym samouczku użyjesz kanwy przepływu danych do tworzenia przepływów danych, które umożliwiają analizowanie i Przekształcanie danych w Azure Data Lake Storage (ADLS) Gen2 i przechowywanie ich w usłudze Delta Lake.

## <a name="prerequisites"></a>Wymagania wstępne
* **Subskrypcja platformy Azure**. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/) .
* **Konto usługi Azure Storage**. Magazyn ADLS jest używany jako magazyn danych *źródłowych* i *ujścia* . Jeśli nie masz konta magazynu, utwórz je, wykonując czynności przedstawione w artykule [Tworzenie konta magazynu platformy Azure](../storage/common/storage-account-create.md).

Plik, który jest przekształcany w tym samouczku, jest MoviesDB.csv, który można znaleźć [tutaj](https://github.com/kromerm/adfdataflowdocs/blob/master/sampledata/moviesDB2.csv). Aby pobrać plik z usługi GitHub, skopiuj zawartość do wybranego edytora tekstu, aby zapisać ją lokalnie jako plik CSV. Aby przekazać plik do konta magazynu, zobacz [przekazywanie obiektów BLOB za pomocą Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md). Przykłady odwołują się do kontenera o nazwie "przykładowe dane".

## <a name="create-a-data-factory"></a>Tworzenie fabryki danych

W tym kroku utworzysz fabrykę danych i otworzysz środowisko Data Factory, aby utworzyć potok w fabryce danych.

1. Otwórz przeglądarkę **Microsoft Edge** lub **Google Chrome**. Obecnie interfejs użytkownika Data Factory jest obsługiwany tylko w przeglądarkach sieci Web Microsoft Edge i Google Chrome.
1. Z menu po lewej stronie wybierz pozycję **Utwórz**  >  **integrację** zasobów  >  **Data Factory**
1. Na stronie **Nowa fabryka danych** w obszarze **Nazwa** wprowadź **ADFTutorialDataFactory**
1. Wybierz **subskrypcję** platformy Azure, w której chcesz utworzyć fabrykę danych.
1. W obszarze **Grupa zasobów** wykonaj jedną z następujących czynności:

    a. Wybierz pozycję **Użyj istniejącej**, a następnie wybierz istniejącą grupę zasobów z listy rozwijanej.

    b. Wybierz pozycję **Utwórz nową**, a następnie wprowadź nazwę grupy zasobów. 
         
    Informacje na temat grup zasobów znajdują się w artykule [Using resource groups to manage your Azure resources (Używanie grup zasobów do zarządzania zasobami platformy Azure)](../azure-resource-manager/management/overview.md). 
1. W obszarze **Wersja** wybierz pozycję **V2**.
1. W obszarze **Lokalizacja** wybierz lokalizację fabryki danych. Na liście rozwijanej są wyświetlane tylko obsługiwane lokalizacje. Magazyny danych (np. usługi Azure Storage i SQL Database) i obliczenia (na przykład usługa Azure HDInsight) używane przez fabrykę danych mogą znajdować się w innych regionach.
1. Wybierz przycisk **Utwórz**.
1. Po zakończeniu tworzenia zobaczysz powiadomienie w centrum powiadomień. Wybierz pozycję **Przejdź do zasobu** , aby przejść do strony Fabryka danych.
1. Wybierz pozycję **Tworzenie i monitorowanie**, aby uruchomić interfejs użytkownika usługi Data Factory na osobnej karcie.

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>Tworzenie potoku za pomocą działania przepływu danych

W tym kroku utworzysz potok zawierający działanie przepływu danych.

1. Na stronie **Zaczynajmy** wybierz pozycję **Utwórz potok**.

   ![Tworzenie potoku](./media/doc-common-process/get-started-page.png)

1. Na karcie **Ogólne** potoku wpisz **DeltaLake** dla **nazwy** potoku.
1. Na górnym pasku fabryki przesuń suwak **debugowania przepływu danych** na. Tryb debugowania umożliwia interaktywne Testowanie logiki transformacji na aktywnym klastrze Spark. Klastry przepływu danych zajmują 5-7 minut na rozgrzanie i zaleca się włączenie najpierw debugowania, jeśli planują one opracowywanie przepływu danych. Aby uzyskać więcej informacji, zobacz [tryb debugowania](concepts-data-flow-debug-mode.md).

    ![Działanie przepływu danych](media/tutorial-data-flow/dataflow1.png)
1. W okienku **działania** rozwiń pozycję **Przenieś i Przekształć** . Przeciągnij i upuść działanie **przepływu danych** z okienka do kanwy potoku.

    ![Zrzut ekranu przedstawiający kanwę potoku, w której można usunąć działanie przepływu danych.](media/tutorial-data-flow/activity1.png)
1. W oknie podręcznym **Dodawanie przepływu danych** wybierz pozycję **Utwórz nowy przepływ danych** , a następnie nadaj nazwę przepływowi danych **DeltaLake**. Po zakończeniu kliknij przycisk Zakończ.

    ![Zrzut ekranu pokazujący lokalizację przepływu danych podczas tworzenia nowego przepływu danych.](media/tutorial-data-flow/activity2.png)

## <a name="build-transformation-logic-in-the-data-flow-canvas&quot;></a>Logika transformacji kompilacji na kanwie przepływu danych

W tym samouczku zostaną wygenerowane dwa przepływy danych. Przepływ danych Fist jest prostym źródłem do ujścia w celu wygenerowania nowej usługi Delta z pliku CSV filmów z powyżej. Na koniec utworzysz następujący projekt przepływu, aby zaktualizować dane w usłudze Delta Lake.

![Końcowy przepływ](media/data-flow/data-flow-tutorial-6.png &quot;Końcowy przepływ")

### <a name="tutorial-objectives"></a>Cele samouczka

1. Wybierz źródło zestawu danych MoviesCSV z powyżej, Utwórz w nim nową różnicową Lake.
1. Kompilowanie logiki w celu zaktualizowania klasyfikacji dla filmów 1988 na "1"
1. Usuń wszystkie filmy z 1950
1. Wstaw nowe filmy dla 2021 przez duplikowanie filmów z 1960

### <a name="start-from-a-blank-data-flow-canvas"></a>Rozpocznij od pustej kanwy przepływu danych

1. Kliknij transformację źródłową
1. Kliknij pozycję Nowy obok zestawu danych w dolnym panelu 1 Utwórz nową połączoną usługę dla ADLS Gen2
1. Wybierz Tekst rozdzielany dla typu zestawu danych
1. Nazwij zestaw danych "MoviesCSV" 
1. Wskaż plik MoviesCSV, który został przekazany do magazynu powyżej
1. Ustaw jako rozdzielaną przecinkami i Dołącz nagłówek w pierwszym wierszu 
1. Przejdź do karty projekcja źródła i kliknij pozycję "Wykryj typy danych".
1. Po ustawieniu projekcji możesz kontynuować 
1. Dodaj transformację ujścia
1. Delta jest typem wbudowanego zestawu danych. Musisz wskazać konto magazynu ADLS Gen2.
   
   ![Wbudowany zestaw danych](media/data-flow/data-flow-tutorial-5.png "Wbudowany zestaw danych")

1. Wybierz nazwę folderu w kontenerze magazynu, w którym chcesz utworzyć ADF
1. Wróć do projektanta potoku, a następnie kliknij pozycję Debuguj, aby uruchomić potok w trybie debugowania z zaledwie tym działaniem przepływu danych na kanwie. Spowoduje to wygenerowanie nowej delty w ADLS Gen2.
1. Z zasobów fabrycznych kliknij kolejno pozycje nowy > przepływ danych 
1. Ponownie Użyj MoviesCSV jako źródła, a następnie kliknij przycisk "Wykryj typy danych".
1. Dodawanie transformacji filtru do transformacji źródłowej na grafie
1. Zezwalaj tylko na wiersze filmu, które pasują do trzech lat, z którymi chcesz korzystać, które będą 1950, 1988 i 1960
1. Zaktualizuj klasyfikacje dla każdego filmu 1988 do ' 1 ' przez dodanie przekształcenia kolumn pochodnych do przekształcenia filtru
1. W tej samej kolumnie pochodnej Utwórz filmy dla 2021, wykonując istniejący rok i Zmień rok na 2021. Wybierzmy 1960.
1. Oto, jak będą wyglądać trzy kolumny pochodne

   ![Kolumna pochodna](media/data-flow/data-flow-tutorial-2.png "Kolumna pochodna")
   
1. ```Update, insert, delete, and upsert``` zasady są tworzone w obszarze ALTER Row Transform. Dodaj transformację zmiany wiersza po kolumnie pochodnej.
1. Zasady ALTER Row powinny wyglądać następująco.

   ![Modyfikuj wiersz](media/data-flow/data-flow-tutorial-3.png "Modyfikuj wiersz")
   
1. Teraz, gdy ustawisz odpowiednie zasady dla każdego typu elementu ALTER Row, sprawdź, czy zostały ustawione odpowiednie reguły aktualizacji dla transformacji ujścia

   ![Ujście](media/data-flow/data-flow-tutorial-4.png "Ujście")
   
1. W tym miejscu korzystamy z usługi Data Lake sink do ADLS Gen2 danych i umożliwia wstawianie, aktualizowanie i usuwanie. 
1. Należy pamiętać, że kolumny kluczy to złożone klucze składające się z kolumny klucza podstawowego filmu i kolumny Year. Wynika to z faktu, że zostały utworzone fałszywe 2021 filmy przez duplikowanie wierszy 1960. Pozwala to uniknąć kolizji podczas wyszukiwania istniejących wierszy, zapewniając unikatowość.

### <a name="download-completed-sample"></a>Pobierz ukończony przykład
[Oto przykładowe rozwiązanie dla potoku różnicowego z przepływem danych służącym do aktualizowania/usuwania wierszy w usłudze Lake:](https://github.com/kromerm/adfdataflowdocs/blob/master/sampledata/DeltaPipeline.zip)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat [języka wyrażeń przepływu danych](data-flow-expression-functions.md).
