---
title: Tworzenie fabryki danych Azure przy użyciu interfejsu użytkownika Azure Data Factory
description: Tworzenie fabryki danych z potokiem, który kopiuje dane między lokalizacjami w usłudze Azure Blob Storage.
author: linda33wj
ms.service: data-factory
ms.topic: quickstart
ms.date: 12/14/2020
ms.author: jingwang
ms.openlocfilehash: 8b33005b7f0d08e6da963457813b012199d89f5f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104655104"
---
# <a name="quickstart-create-a-data-factory-by-using-the-azure-data-factory-ui"></a>Szybki start: Tworzenie fabryki danych przy użyciu interfejsu użytkownika usługi Azure Data Factory 

> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Bieżąca wersja](quickstart-create-data-factory-portal.md)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

W tym przewodniku Szybki start opisano sposób używania interfejsu użytkownika usługi Azure Data Factory w celu tworzenia i monitorowania fabryki danych. Potok tworzony w tej fabryce danych *kopiuje* dane z jednego folderu do innego folderu w usłudze Azure Blob Storage. Aby *przekształcić* dane przy użyciu Azure Data Factory, zobacz [Mapowanie przepływu danych](concepts-data-flow-overview.md).

> [!NOTE]
> Jeśli jesteś nowym użytkownikiem usługi Azure Data Factory, przed wykonaniem kroków zawartych w tym przewodniku Szybki start zobacz [Wprowadzenie do usługi Azure Data Factory](introduction.md). 

[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)] 

### <a name="video"></a>Wideo 
Obejrzenie tego filmu wideo ułatwi zapoznanie się z interfejsem użytkownika usługi Data Factory: 
>[!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Visually-build-pipelines-for-Azure-Data-Factory-v2/Player]

## <a name="create-a-data-factory"></a>Tworzenie fabryki danych

1. Uruchom przeglądarkę internetową **Microsoft Edge** lub **Google Chrome**. Obecnie interfejs użytkownika usługi Data Factory jest obsługiwany tylko przez przeglądarki internetowe Microsoft Edge i Google Chrome.
1. Przejdź do witryny [Azure Portal](https://portal.azure.com). 
1. W menu witryny Azure Portal wybierz pozycję **Utwórz zasób**.
1. Wybierz pozycję **integracja**, a następnie wybierz pozycję **Data Factory**. 
   
   :::image type="content" source="./media/doc-common-process/new-azure-data-factory-menu.png" alt-text="Data Factory wybór w nowym okienku.":::

1. Na stronie **tworzenie Data Factory** w obszarze **podstawowe** karty wybierz swoją **subskrypcję** platformy Azure, w której chcesz utworzyć fabrykę danych.
1. W obszarze **Grupa zasobów** wykonaj jedną z następujących czynności:

    a. Wybierz istniejącą grupę zasobów z listy rozwijanej.

    b. Wybierz pozycję **Utwórz nową**, a następnie wprowadź nazwę nowej grupy zasobów.
    
    Informacje na temat grup zasobów znajdują się w artykule [Using resource groups to manage your Azure resources (Używanie grup zasobów do zarządzania zasobami platformy Azure)](../azure-resource-manager/management/overview.md). 

1. W polu **region** wybierz lokalizację fabryki danych.

   Ta lista zawiera tylko lokalizacje, które są obsługiwane przez usługę Data Factory i w których będą przechowywane metadane usługi Azure Data Factory. Skojarzone magazyny danych (np. usługi Azure Storage i Azure SQL Database) i usługi obliczeniowe (np. usługa Azure HDInsight) używane przez Data Factory mogą działać w innych regionach.
 
1. W obszarze **Nazwa** wprowadź **ADFTutorialDataFactory**.
   Nazwa fabryki danych Azure musi być *globalnie unikatowa*. Jeśli zostanie wyświetlony następujący błąd, Zmień nazwę fabryki danych (na przykład **&lt; &gt; ADFTutorialDataFactory**) i spróbuj utworzyć ją ponownie. Artykuł [Usługa Data Factory — reguły nazewnictwa](naming-rules.md) zawiera reguły nazewnictwa artefaktów usługi Data Factory.

    :::image type="content" source="./media/doc-common-process/name-not-available-error.png" alt-text="Nowy komunikat o błędzie usługi Fabryka danych dla zduplikowanej nazwy.":::

1. W obszarze **Wersja** wybierz pozycję **V2**.

1. Wybierz pozycję **Dalej: Konfiguracja usługi git**, a następnie zaznacz pole wyboru **Konfiguruj git później** .

1. Wybierz pozycję **Recenzja + Utwórz**, a następnie wybierz pozycję **Utwórz** po pomyślnym przekazaniu walidacji. Po zakończeniu tworzenia wybierz pozycję **Przejdź do zasobu** , aby przejść do strony **Data Factory** . 

1. Wybierz kafelek **tworzenie & monitor** , aby uruchomić aplikację interfejsu użytkownika Azure Data Factory (UI) na oddzielnej karcie przeglądarki.
   
    :::image type="content" source="./media/doc-common-process/data-factory-home-page.png" alt-text="Strona główna Azure Data Factory z kafelkiem &quot;autor & Monitor&quot;.":::
   
   > [!NOTE]
   > Jeśli zobaczysz, że przeglądarka sieci Web jest zablokowana w "Autoryzuj", wyczyść pole wyboru **Blokuj pliki cookie innych firm i dane lokacji** . Lub zaznacz ją, Utwórz wyjątek dla **login.microsoftonline.com**, a następnie spróbuj ponownie otworzyć aplikację.
   

## <a name="create-a-linked-service"></a>Tworzenie usługi połączonej
W tej procedurze utworzysz połączoną usługę, aby połączyć konto usługi Azure Storage z fabryką danych. Połączona usługa ma informacje o połączeniu, których usługa Data Factory używa w środowisku uruchomieniowym do nawiązywania z nią połączenia.

1. Na stronie interfejs użytkownika Azure Data Factory Otwórz kartę [**Zarządzanie**](./author-management-hub.md) w okienku po lewej stronie.

1. Na stronie połączone usługi wybierz pozycję **+ Nowy** , aby utworzyć nową połączoną usługę.

   :::image type="content" source="./media/doc-common-process/new-linked-service.png" alt-text="Nowa połączona usługa.":::
   
1. Na stronie **Nowa połączona usługa** wybierz pozycję **Azure Blob Storage**, a następnie wybierz pozycję **Dalej**. 

1. Na stronie Nowa połączona usługa (Azure Blob Storage) wykonaj następujące czynności: 

   a. Wprowadź wartość **AzureStorageLinkedService** w polu **Nazwa**.

   b. W polu **nazwa konta magazynu** wybierz nazwę konta usługi Azure Storage.

   c. Wybierz pozycję **Testuj połączenie**, aby sprawdzić, czy usługa Data Factory może nawiązać połączenie z kontem magazynu. 

   d. Wybierz pozycję **Utwórz** , aby zapisać połączoną usługę. 

      :::image type="content" source="./media/quickstart-create-data-factory-portal/linked-service.png" alt-text="Połączona usługa.":::


## <a name="create-datasets"></a>Tworzenie zestawów danych
W tej procedurze tworzone są dwa zestawy danych: **InputDataset** i **OutputDataset**. Te zestawy danych są typu **AzureBlob**. Odwołują się one do połączonej usługi Azure Storage utworzonej w poprzedniej sekcji. 

Wejściowy zestaw danych reprezentuje dane źródłowe w folderze wejściowym. W definicji wejściowego zestawu danych określany jest kontener obiektów blob (**adftutorial**), folder (**input**) i plik (**emp.txt**), który zawiera dane źródłowe. 

Wyjściowy zestaw danych reprezentuje dane, które są kopiowane do lokalizacji docelowej. W definicji wyjściowego zestawu danych określany jest kontener obiektów blob (**adftutorial**), folder (**output**) i plik, do którego kopiowane są dane. Każde uruchomienie potoku ma skojarzony ze sobą unikatowy identyfikator. Aby uzyskać dostęp do tego identyfikatora, skorzystaj ze zmiennej systemowej **RunId**. Nazwa pliku wyjściowego jest dynamicznie obliczana na podstawie identyfikatora uruchomienia potoku.   

W ustawieniach połączonej usługi określono konto usługi Azure Storage, które zawiera dane źródłowe. W ustawieniach zestawu danych źródłowych należy określić, gdzie dokładnie znajduje się źródło danych (kontener obiektów blob, folder i plik). W ustawieniach zestawu danych ujścia należy określić, gdzie kopiowane są dane (kontener obiektów blob, folder i plik). 

1. W okienku po lewej stronie wybierz pozycję Karta **autora** .

1. Wybierz **+** przycisk (znak plus), a następnie wybierz pozycję **zestaw danych**.

   :::image type="content" source="./media/quickstart-create-data-factory-portal/new-dataset-menu.png" alt-text="Menu do tworzenia zestawu danych.":::

1. Na stronie **Nowy zestaw danych** wybierz pozycję **Azure Blob Storage**, a następnie wybierz pozycję **Kontynuuj**. 

1. Na stronie **Wybierz format** wybierz typ formatu danych, a następnie wybierz pozycję **Kontynuuj**. W takim przypadku wybierz opcję **plik binarny** , jeśli kopiujesz pliki jako-is bez analizy zawartości.

   :::image type="content" source="./media/quickstart-create-data-factory-portal/select-format.png" alt-text="Wybierz pozycję Format.":::   
   
1. Na stronie **Ustawianie właściwości** wykonaj następujące czynności:

    a. W polu **Nazwa** wprowadź **InputDataset**. 

    b. Wybierz pozycję **AzureStorageLinkedService** w polu **Połączona usługa**.

    c. Kliknij przycisk **Przeglądaj** w polu **Ścieżka pliku**.

    d. W oknie **Wybieranie pliku lub folderu** przejdź do folderu **Input** w kontenerze **adftutorial** , wybierz plik **emp.txt** , a następnie wybierz przycisk **OK**.
    
    e. Wybierz przycisk **OK**.   

    :::image type="content" source="./media/quickstart-create-data-factory-portal/set-properties-for-inputdataset.png" alt-text="Ustaw właściwości dla InputDataset.":::

1. Powtórz kroki, aby utworzyć wyjściowy zestaw danych:  

    a. Wybierz **+** przycisk (znak plus), a następnie wybierz pozycję **zestaw danych**.

    b. Na stronie **Nowy zestaw danych** wybierz pozycję **Azure Blob Storage**, a następnie wybierz pozycję **Kontynuuj**.

    c. Na stronie **Wybierz format** wybierz typ formatu danych, a następnie wybierz pozycję **Kontynuuj**.

    d. Na stronie **Ustawianie właściwości** Określ **OutputDataset** dla nazwy. Wybierz pozycję **AzureStorageLinkedService** jako połączoną usługę.

    e. W obszarze **ścieżka pliku** wprowadź **adftutorial/Output**. Jeśli folder **wyjściowy** nie istnieje, działanie kopiowania tworzy je w czasie wykonywania.

    f. Wybierz przycisk **OK**.   

    :::image type="content" source="./media/quickstart-create-data-factory-portal/set-properties-for-outputdataset.png" alt-text="Ustaw właściwości dla OutputDataset.":::    

## <a name="create-a-pipeline"></a>Tworzenie potoku 
Podczas tej procedury utworzysz potok i zweryfikujesz go za pomocą działania kopiowania, które korzysta z wejściowego i wyjściowego zestawu danych. Działanie kopiowania służy do kopiowania danych z pliku określonego w ustawieniach wejściowego zestawu danych do pliku określonego w ustawieniach wyjściowego zestawu danych. Jeśli wejściowy zestaw danych określa tylko folder (a nie nazwę pliku), działanie kopiowania kopiuje wszystkie pliki w folderze źródłowym do lokalizacji docelowej. 

1. Wybierz **+** przycisk (znak plus), a następnie wybierz pozycję **potok**. 

1. W panelu Ogólne w obszarze **Właściwości** Określ **CopyPipeline** dla **nazwy**. Następnie Zwiń panel, klikając ikonę właściwości w prawym górnym rogu.

1. W przyborniku **Działania** rozwiń pozycję **Przenoszenie i przekształcanie**. Przeciągnij działanie **Kopiowanie danych** z przybornika **działania** na powierzchnię projektanta potoku. Możesz również wyszukać działania w przyborniku **Działania**. Wprowadź wartość **CopyFromBlobToBlob** w polu **Nazwa**.

    :::image type="content" source="./media/quickstart-create-data-factory-portal/copy-activity.png" alt-text="Tworzenie działania kopiowania danych.":::   

1. Przejdź do karty **Źródło** w ustawieniach działania kopiowania, a następnie wybierz wartość **InputDataset** w polu **Zestaw danych źródłowych**.

1. Przejdź do karty **Ujście** w ustawieniach działania kopiowania, a następnie wybierz wartość **OutputDataset** w polu **Zestaw danych ujścia**.

1. Aby sprawdzić poprawność ustawień potoku, kliknij pozycję **Weryfikuj** na pasku narzędzi potoku powyżej kanwy. Sprawdź, czy potok został pomyślnie zweryfikowany. Aby zamknąć dane wyjściowe walidacji, wybierz przycisk walidacji w prawym górnym rogu. 

   :::image type="content" source="./media/quickstart-create-data-factory-portal/pipeline-validate.png"   alt-text="Weryfikowanie potoku.":::

## <a name="debug-the-pipeline"></a>Debugowanie potoku
W tym kroku przeprowadzisz debugowanie potoku przed jego wdrożeniem w usłudze Data Factory. 

1. Na pasku narzędzi nad kanwą potoku kliknij pozycję **Debugowanie**, aby wyzwolić przebieg testu. 
    
1. Sprawdź, czy w dolnej części karty **Dane wyjściowe** ustawień potoku wyświetlany jest stan przebiegu potoku. 
 
    :::image type="content" source="./media/quickstart-create-data-factory-portal/pipeline-output.png" alt-text="Dane wyjściowe uruchomienia potoku":::

1. Sprawdź, czy w folderze **output** kontenera **adftutorial** znajduje się plik wyjściowy. Jeśli folder wyjściowy nie istnieje, Usługa Data Factory automatycznie go utworzy. 

## <a name="trigger-the-pipeline-manually"></a>Ręczne wyzwalanie potoku
Podczas tej procedury wdrożysz jednostki (połączone usługi, zestawy danych i potoki) w usłudze Azure Data Factory. Następnie ręcznie wyzwolisz przebieg potoku. 

1. Przed wyzwoleniem potoku należy opublikować jednostki w usłudze Data Factory. Aby przeprowadzić publikowanie, wybierz pozycję **Opublikuj wszystko** w górnej części. 

    :::image type="content" source="./media/quickstart-create-data-factory-portal/publish-all.png" alt-text="Publikuj wszystko.":::    

1. Aby wyzwolić potok ręcznie, wybierz pozycję **Dodaj wyzwalacz** na pasku narzędzi potoku, a następnie wybierz pozycję **Wyzwól teraz**. Na stronie **uruchomienie potoku** wybierz pozycję **OK**.

## <a name="monitor-the-pipeline"></a>Monitorowanie potoku

1. Przejdź do karty **Monitorowanie** po lewej stronie. Kliknij przycisk **Odśwież**, aby odświeżyć listę.

    :::image type="content" source="./media/quickstart-create-data-factory-portal/monitor-trigger-now-pipeline.png" alt-text="Karta do monitorowania przebiegów potoku"::: 
1. Wybierz łącze **CopyPipeline** , na tej stronie zobaczysz stan uruchomienia działania kopiowania. 

1. Aby wyświetlić szczegółowe informacje na temat operacji kopiowania, wybierz link **szczegóły** (obraz okularów). Aby uzyskać więcej informacji o właściwościach, zobacz [Omówienie działania kopiowania](copy-activity-overview.md). 

   :::image type="content" source="./media/quickstart-create-data-factory-portal/copy-operation-details.png" alt-text="Szczegóły operacji kopiowania.":::
1. Sprawdź, czy nowy plik jest widoczny w folderze **output**. 
1. Możesz wrócić do widoku **uruchomienia potoków** z widoku **uruchomienia działania** , wybierając łącze **wszystkie uruchomienia potoków** . 

## <a name="trigger-the-pipeline-on-a-schedule"></a>Wyzwalanie potoku zgodnie z harmonogramem
W tym samouczku ta procedura jest opcjonalna. Możesz utworzyć *wyzwalacz harmonogramu*, aby zaplanować okresowe uruchamianie potoku (co godzinę, codziennie itd.). Podczas tej procedury utworzysz wyzwalacz, który będzie uruchamiany co minutę, aż do daty/godziny określonej jako data zakończenia. 

1. Przejdź do karty **Tworzenie**. 

1. Przejdź do potoku, wybierz pozycję **Dodaj wyzwalacz** na pasku narzędzi potoku, a następnie wybierz pozycję **Nowy/Edytuj**. 

1. Na stronie **Dodawanie wyzwalaczy** wybierz pozycję **Wybierz wyzwalacz**, a następnie wybierz przycisk **Nowy**. 

1. Na stronie **Nowy wyzwalacz** w obszarze **koniec** wybierz pozycję **w polu Data**, określ godzinę zakończenia kilka minut po bieżącej godzinie, a następnie wybierz przycisk **OK**. 

   Za poszczególne uruchomienia potoku są naliczane opłaty, zatem określ czas zakończenia jako późniejszy tylko o kilka minut od czasu rozpoczęcia. Upewnij się, że przypada on tego samego dnia. Należy jednak upewnić się, że jest wystarczająco dużo czasu na uruchomienie potoku między czasem publikacji a czasem zakończenia. Wyzwalacz zaczyna obowiązywać dopiero po opublikowaniu rozwiązania w fabryce Data Factory, a nie po zapisaniu go w interfejsie użytkownika. 

1. Na stronie **Nowy wyzwalacz** zaznacz pole wyboru **aktywowane** , a następnie wybierz przycisk **OK**. 

    :::image type="content" source="./media/quickstart-create-data-factory-portal/trigger-settings-next.png" alt-text="Nowe ustawienie wyzwalacza.":::   
1. Przejrzyj komunikat ostrzegawczy, a następnie wybierz **przycisk OK**.

1. Wybierz pozycję **Opublikuj wszystkie** , aby opublikować zmiany w Data Factory. 

1. Przejdź do karty **Monitorowanie** po lewej stronie. Wybierz pozycję **Odśwież**, aby odświeżyć listę. Potok będzie uruchamiany raz na minutę od czasu opublikowania do czasu zakończenia. 

   Zwróć uwagę na wartości w kolumnie **wyzwalane przez** . Ręczne uruchomienie wyzwalacza pochodzi z kroku wykonanego wcześniej (**Wyzwól teraz**). 

1. Przejdź do widoku **uruchomienia wyzwalacza** . 

1. Sprawdź, czy plik wyjściowy jest tworzony w folderze **output** dla każdego uruchomienia potoku aż do określonej daty/godziny zakończenia. 

## <a name="next-steps"></a>Następne kroki
Potok w tym przykładzie kopiuje dane z jednej lokalizacji do innej lokalizacji w usłudze Azure Blob Storage. Zapoznaj się z [samouczkami](tutorial-copy-data-portal.md), aby dowiedzieć się więcej o korzystaniu z usługi Data Factory w dalszych scenariuszach.
