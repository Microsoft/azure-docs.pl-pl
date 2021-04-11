---
title: Najlepsze rozwiązania dotyczące zapisywania plików w usłudze Data Lake przy użyciu przepływów danych
description: Ten samouczek zawiera najlepsze rozwiązania dotyczące zapisywania plików w usłudze Data Lake przy użyciu przepływów danych
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2021
ms.date: 04/01/2021
ms.openlocfilehash: 8010f3f95c9358714b659df5821a375bd8488ad8
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106582318"
---
# <a name="best-practices-for-writing-to-files-to-data-lake-with-data-flows"></a>Najlepsze rozwiązania dotyczące zapisywania plików w usłudze Data Lake przy użyciu przepływów danych

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Jeśli jesteś nowym użytkownikiem usługi Azure Data Factory, zobacz [Wprowadzenie do usługi Azure Data Factory](introduction.md).

W tym samouczku przedstawiono najlepsze rozwiązania, które można zastosować podczas pisania plików do ADLS Gen2 lub Blob Storage platformy Azure przy użyciu przepływów danych. Będziesz potrzebować dostępu do konta usługi Azure Blob Storage lub Azure Data Lake Store konta Gen2 do odczytu pliku Parquet, a następnie przechowywania wyników w folderach.

## <a name="prerequisites"></a>Wymagania wstępne
* **Subskrypcja platformy Azure**. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/) .
* **Konto usługi Azure Storage**. Magazyn ADLS jest używany jako magazyn danych *źródłowych* i *ujścia* . Jeśli nie masz konta magazynu, utwórz je, wykonując czynności przedstawione w artykule [Tworzenie konta magazynu platformy Azure](../storage/common/storage-account-create.md).

W procedurach przedstawionych w tym samouczku założono, że masz 

## <a name="create-a-data-factory"></a>Tworzenie fabryki danych

W tym kroku utworzysz fabrykę danych i otworzysz środowisko Data Factory, aby utworzyć potok w fabryce danych.

1. Otwórz przeglądarkę **Microsoft Edge** lub **Google Chrome**. Obecnie interfejs użytkownika Data Factory jest obsługiwany tylko w przeglądarkach sieci Web Microsoft Edge i Google Chrome.
1. Z menu po lewej stronie wybierz pozycję **Utwórz**  >  **integrację** zasobów  >  **Data Factory**
1. Na stronie **Nowa fabryka danych** w obszarze **Nazwa** wprowadź **ADFTutorialDataFactory**
1. Wybierz **subskrypcję** platformy Azure, w której chcesz utworzyć fabrykę danych.
1. W obszarze **Grupa zasobów** wykonaj jedną z następujących czynności:

    a. Wybierz pozycję **Użyj istniejącej**, a następnie wybierz istniejącą grupę zasobów z listy rozwijanej.
    
    b. Wybierz pozycję **Utwórz nową**, a następnie wprowadź nazwę grupy zasobów. Aby dowiedzieć się więcej na temat grup zasobów, zobacz temat [Używanie grup zasobów do zarządzania zasobami platformy Azure](../azure-resource-manager/management/overview.md).
    
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

## <a name="build-transformation-logic-in-the-data-flow-canvas"></a>Logika transformacji kompilacji na kanwie przepływu danych

Będziesz korzystać z jakichkolwiek danych źródłowych (w tym samouczku, użyjemy źródła pliku parquet) i użyjesz transformacji ujścia, aby wystawić dane w formacie Parquet przy użyciu najbardziej efektywnych mechanizmów usługi Data Lake ETL.

![Końcowy przepływ](media/data-flow/parts-final.png "Końcowy przepływ")

### <a name="tutorial-objectives"></a>Cele samouczka

1. Wybierz dowolny ze źródłowych zestawów danych w nowym przepływie
1. Efektywne partycjonowanie zestawu danych ujścia przy użyciu przepływów danych
1. Wydziel dane partycjonowane w folderach ADLS Gen2 Lake

### <a name="start-from-a-blank-data-flow-canvas"></a>Rozpocznij od pustej kanwy przepływu danych

Po pierwsze skonfigurujemy środowisko przepływu danych dla każdego z mechanizmów opisanych poniżej w celu załadowania danych w ADLS Gen2

1. Kliknij transformację źródłową.
1. Kliknij przycisk Nowy obok zestawu danych w dolnym panelu.
1. Wybierz zestaw danych lub Utwórz nowy. W tym pokazie użyjemy zestawu danych Parquet o nazwie dane użytkownika.
1. Dodaj transformację kolumn pochodnych. Użyjemy tej opcji jako sposobu na dynamiczne ustawienie żądanych nazw folderów.
1. Dodaj transformację ujścia.
   
### <a name="hierarchical-folder-output"></a>Dane wyjściowe folderu hierarchicznego

Często można używać unikatowych wartości w danych, aby utworzyć hierarchie folderów do partycjonowania danych w Lake. Jest to bardzo optymalny sposób, aby organizować i przetwarzać dane w jeziorach i w Spark (aparat obliczeniowy za przepływy danych). Jednak w ten sposób będzie można organizować dane wyjściowe w niewielkim koszcie wydajności. Należy spodziewać się, że w ujściach zostanie wyświetlony niewielki spadek ogólnej wydajności potoku.

1. Wróć do projektanta przepływu danych i edytuj powyżej Utwórz przepływ danych. Kliknij transformację ujścia.
1. Kliknij przycisk Optymalizuj > Ustaw partycjonowanie > klucz
1. Wybierz kolumny, których chcesz użyć do ustawienia hierarchicznej struktury folderów.
1. Zwróć uwagę, że w poniższym przykładzie do kolumn dla nazw folderów jest stosowany rok i miesiąc. Wyniki będą mieć foldery formularza ```releaseyear=1990/month=8``` .
1. Podczas uzyskiwania dostępu do partycji danych w źródle przepływu danych należy wskazać tylko folder najwyższego poziomu, ```releaseyear``` a następnie użyć wzorca wieloznacznego dla każdego kolejnego folderu, np.: ```**/**/*.parquet```
1. Aby manipulować wartościami danych, a nawet jeśli trzeba generować wartości syntetyczne dla nazw folderów, użyj przekształcenia kolumn pochodnych, aby utworzyć wartości, które mają być używane w nazwach folderów.

![Partycjonowanie kluczy](media/data-flow/key-parts.png "Partycjonowanie kluczy")
   
### <a name="name-folder-as-data-values"></a>Nazwa folderu jako wartości danych

Nieco lepsza metoda ujścia dla usługi Lake Data przy użyciu ADLS Gen2, która nie oferuje takiej samej korzyści jak partycjonowanie klucza/wartości, to ```Name folder as column data``` . Natomiast styl partycjonowania klucza struktury hierarchicznej umożliwi łatwiejsze przetwarzanie wycinków danych, dlatego ta technika jest spłaszczoną strukturą folderów, która umożliwia szybsze pisanie danych.

1. Wróć do projektanta przepływu danych i edytuj powyżej Utwórz przepływ danych. Kliknij transformację ujścia.
1. Kliknij przycisk Optymalizuj > partycjonowanie > Użyj bieżącego partycjonowania.
1. Kliknij pozycję Ustawienia > nazwa folderu jako dane kolumny.
1. Wybierz kolumnę, która ma być używana do generowania nazw folderów.
1. Aby manipulować wartościami danych, a nawet jeśli trzeba generować wartości syntetyczne dla nazw folderów, użyj przekształcenia kolumn pochodnych, aby utworzyć wartości, które mają być używane w nazwach folderów.

![Opcja folderu](media/data-flow/folders.png "Foldery")

### <a name="name-file-as-data-values"></a>Nazwij plik jako wartości danych

Techniki wymienione w powyższych samouczkach są dobrym przypadkiem zastosowania do tworzenia kategorii folderów w usłudze Data Lake. Domyślnym schematem nazewnictwa plików stosowanym przez te techniki jest użycie identyfikatora zadania programu wykonującego Spark. Czasami może być konieczne ustawienie nazwy pliku wyjściowego w ujścia tekstu przepływu danych. Ta technika jest sugerowana tylko do użytku z małymi plikami. Proces scalania plików partycji w jednym pliku wyjściowym jest długotrwałym procesem.

1. Wróć do projektanta przepływu danych i edytuj powyżej Utwórz przepływ danych. Kliknij transformację ujścia.
1. Kliknij przycisk Optymalizuj > ustawić partycjonowanie > pojedynczej partycji. Jest to wymaganie pojedynczej partycji, które tworzy wąskie gardło w procesie wykonywania, gdy pliki są scalane. Ta opcja jest zalecana tylko w przypadku małych plików.
1. Kliknij pozycję Ustawienia > Nazwij plik jako dane kolumny.
1. Wybierz kolumnę, która ma być używana do generowania nazw plików.
1. Aby manipulować wartościami danych, a nawet jeśli trzeba generować wartości syntetyczne dla nazw plików, użyj przekształcenia kolumn pochodnych, aby utworzyć wartości, które mają być używane w nazwach plików.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [ujściach przepływów danych](data-flow-sink.md).
