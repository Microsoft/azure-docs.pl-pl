---
title: Przyrostowe kopiowanie nowych plików na podstawie nazwy pliku podzielonego na partycje
description: Utwórz fabrykę danych platformy Azure, a następnie użyj narzędzia Kopiowanie danych, aby przyrostowo ładować nowe pliki na podstawie nazwy pliku podzielonego na partycje.
author: dearandyxu
ms.author: yexu
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/18/2021
ms.openlocfilehash: 26703920fda8746badf085f96686f922ee250513
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104606637"
---
# <a name="incrementally-copy-new-files-based-on-time-partitioned-file-name-by-using-the-copy-data-tool"></a>Przyrostowe kopiowanie nowych plików na podstawie nazwy pliku podzielonego na partycje przy użyciu narzędzia Kopiowanie danych

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym samouczku utworzysz fabrykę danych za pomocą witryny Azure Portal. Następnie użyj narzędzia Kopiowanie danych, aby utworzyć potok, który przyrostowo kopiuje nowe pliki na podstawie nazwy pliku z podziałem czasu z usługi Azure Blob Storage do usługi Azure Blob Storage.

> [!NOTE]
> Jeśli jesteś nowym użytkownikiem usługi Azure Data Factory, zobacz [Wprowadzenie do usługi Azure Data Factory](introduction.md).

Ten samouczek obejmuje wykonanie następujących kroków:

> [!div class="checklist"]
> * Tworzenie fabryki danych.
> * Tworzenie potoku za pomocą narzędzia do kopiowania danych.
> * Monitorowanie uruchomień potoku i działań.

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**: jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).
* **Konto usługi Azure Storage**: Użyj magazynu obiektów BLOB jako magazynu danych _źródłowych_  i _ujścia_ . Jeśli nie masz konta usługi Azure Storage, zobacz instrukcje podane w temacie [Tworzenie konta magazynu](../storage/common/storage-account-create.md).

### <a name="create-two-containers-in-blob-storage"></a>Tworzenie dwóch kontenerów w usłudze BLOB Storage

Aby przygotować magazyn obiektów BLOB do samouczka, wykonaj te kroki.

1. Utwórz kontener o nazwie **Source**.  Utwórz ścieżkę folderu jako **2020/03/17/03** w kontenerze. Utwórz pusty plik tekstowy i nadaj mu nazwę **file1.txt**. Przekaż file1.txt do ścieżki folderu **Source/2020/03/17/03** na koncie magazynu.  Do wykonania tych zadań możesz użyć różnych narzędzi, takich jak [Eksplorator usługi Azure Storage](https://storageexplorer.com/).

    ![Przekaż pliki](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/upload-file.png)

    > [!NOTE]
    > Dostosuj nazwę folderu o czas UTC.  Na przykład jeśli bieżący czas UTC to 3:38 marca, 2020, można utworzyć ścieżkę folderu jako **Źródło/2020/03/17/03/** według reguły **źródłowej/{Year}/{Month}/{Day}/{Hour}/**.

2. Utwórz kontener o nazwie **Destination**. Do wykonania tych zadań możesz użyć różnych narzędzi, takich jak [Eksplorator usługi Azure Storage](https://storageexplorer.com/).

## <a name="create-a-data-factory"></a>Tworzenie fabryki danych

1. W menu po lewej stronie wybierz pozycję **Utwórz zasób**  >  **integracja**  >  **Data Factory**:

   ![Wybór usługi Data Factory w okienku „Nowy”](./media/doc-common-process/new-azure-data-factory-menu.png)

2. Na stronie **Nowa fabryka danych** w polu **Nazwa** wprowadź wartość **ADFTutorialDataFactory**.

    Nazwa Twojej fabryki danych musi być _globalnie unikatowa_. Może zostać wyświetlony następujący komunikat o błędzie:

   :::image type="content" source="./media/doc-common-process/name-not-available-error.png" alt-text="Nowy komunikat o błędzie usługi Fabryka danych dla zduplikowanej nazwy.":::

   Jeśli zostanie wyświetlony komunikat o błędzie dotyczącym wartości nazwy, wprowadź inną nazwę dla fabryki danych. Na przykład użyj nazwy _**twojanazwa**_**ADFTutorialDataFactory**. Artykuł [Data Factory naming rules (Zasady nazewnictwa fabryki danych)](naming-rules.md) zawiera zasady nazewnictwa artefaktów usługi Data Factory.
3. Wybierz **subskrypcję** platformy Azure, w której utworzysz nową fabrykę danych.
4. W obszarze **Grupa zasobów** wykonaj jedną z następujących czynności:

    a. Wybierz pozycję **Użyj istniejącej**, a następnie wybierz istniejącą grupę zasobów z listy rozwijanej.

    b. Wybierz pozycję **Utwórz nową**, a następnie wprowadź nazwę grupy zasobów. 
         
    Informacje na temat grup zasobów znajdują się w artykule [Using resource groups to manage your Azure resources (Używanie grup zasobów do zarządzania zasobami platformy Azure)](../azure-resource-manager/management/overview.md).

5. W obszarze **Wersja** wybierz wersję **V2**.
6. W obszarze **Lokalizacja** wybierz lokalizację fabryki danych. Na liście rozwijanej są wyświetlane tylko obsługiwane lokalizacje. Magazyny danych (np. usługi Azure Storage i SQL Database) oraz jednostki obliczeniowe (np. usługa Azure HDInsight) używane przez Twoją fabrykę danych mogą mieścić się w innych lokalizacjach i regionach.
7. Wybierz przycisk **Utwórz**.
8. Po zakończeniu tworzenia zostanie wyświetlona strona główna usługi **Data Factory**.
9. Aby w osobnej karcie uruchomić interfejs użytkownika usługi Azure Data Factory, kliknij kafelek **Tworzenie i monitorowanie**.

    :::image type="content" source="./media/doc-common-process/data-factory-home-page.png" alt-text="Strona główna Azure Data Factory z kafelkiem &quot;autor & Monitor&quot;.":::

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Tworzenie potoku za pomocą narzędzia do kopiowania danych

1. **Na stronie Wprowadzenie** wybierz tytuł **Kopiowanie danych** , aby uruchomić narzędzie kopiowanie danych.

   ![Kafelek narzędzia do kopiowania danych](./media/doc-common-process/get-started-page.png)

2. Na stronie **Właściwości** wykonaj następujące czynności:

    a. W obszarze **Nazwa zadania** wprowadź **DeltaCopyFromBlobPipeline**.

    b. W obszarze **zadanie erze lub harmonogram zadań** wybierz pozycję **Uruchom regularnie zgodnie z harmonogramem**.

    c. W obszarze **Typ wyzwalacza** wybierz pozycję **okno wirowania**.

    d. W obszarze **cykl** wprowadź **1 godzinę**.

    e. Wybierz opcję **Dalej**.

    Interfejs użytkownika usługi Data Factory tworzy potok o określonej nazwie zadania.

    ![Strona właściwości](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/copy-data-tool-properties-page.png)
3. Na stronie **Źródłowy magazyn danych** wykonaj następujące czynności:

    a. Kliknij pozycję **+Utwórz nowe połączenie**, aby dodać połączenie.
    
    b. Wybierz pozycję Azure Blob Storage z galerii, a następnie wybierz pozycję Kontynuuj.
    
    c. Na stronie **Nowa połączona usługa (Azure Blob Storage)** wprowadź nazwę połączonej usługi. Wybierz swoją subskrypcję platformy Azure, a następnie wybierz konto magazynu z listy **nazwa konta magazynu** . Test connection a następnie wybierz pozycję **Utwórz**.

    ![Strona Źródłowy magazyn danych](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page-linkedservice.png)

    d. Wybierz nowo utworzoną połączoną usługę na stronie **źródłowy magazyn danych** , a następnie kliknij przycisk **dalej**.

4. Na stronie **Wybieranie pliku lub folderu wejściowego** wykonaj następujące kroki:

    a. Przeglądaj i wybierz kontener **źródłowy** , a następnie wybierz pozycję **Wybierz**.

    ![Zrzut ekranu przedstawia okno dialogowe Wybieranie pliku wejściowego lub folderu.](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/choose-input-file-folder.png)

    b. W obszarze **zachowanie ładowania plików** wybierz pozycję **obciążenie przyrostowe: nazwy folderów/plików z podziałem na partycje**.

    c. Zapisz ścieżkę folderu dynamicznego jako **Źródło/{Year}/{Month}/{Day}/{Hour}/**, a następnie Zmień format, jak pokazano na poniższym zrzucie ekranu. Sprawdź **kopię binarną** i kliknij przycisk **dalej**.

    ![Zrzut ekranu przedstawia okno dialogowe Wybieranie pliku wejściowego lub folderu z wybranym folderem.](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/check-binary-copy.png)     

5. Na stronie **docelowy magazyn danych** wybierz **AzureBlobStorage**, który jest tym samym kontem magazynu co magazyn źródła danych, a następnie kliknij przycisk **dalej**.

    ![Strona Docelowy magazyn danych](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/destination-data-store-page-select-linkedservice.png)
6. Na stronie **Wybieranie pliku lub folderu wyjściowego** wykonaj następujące czynności:

    a. Przeglądaj i wybierz folder **docelowy** , a następnie kliknij przycisk **Wybierz**.

    b. Zapisz ścieżkę do folderu dynamicznego jako **lokalizację docelową/{Year}/{Month}/{Day}/{Hour}/**, a następnie Zmień format w następujący sposób:

    ![Zrzut ekranu przedstawia okno dialogowe Wybieranie pliku wyjściowego lub folderu.](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/output-file-name.png)

    c. Kliknij przycisk **Dalej**.

    ![Zrzut ekranu przedstawia okno dialogowe Wybieranie pliku wyjściowego lub folderu z wybranym przyciskiem dalej.](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/click-next-after-output-folder.png)
7. Na stronie **Ustawienia** wybierz przycisk **Dalej**.

8. Na stronie **Podsumowanie** sprawdź ustawienia, a następnie kliknij przycisk **Dalej**.

    ![Strona podsumowania](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/summary-page.png)

9. Na **stronie Wdrażanie** wybierz pozycję **Monitorowanie**, aby monitorować potok (zadanie).
    ![Strona Wdrażanie](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/deployment-page.png)

10. Zwróć uwagę, że karta **Monitor** po lewej stronie jest automatycznie wybrana.  Musisz poczekać na uruchomienie potoku, gdy zostanie on wyzwolony automatycznie (około godzinę). Gdy jest uruchomiona, kliknij link Nazwa potoku **DeltaCopyFromBlobPipeline** , aby wyświetlić szczegóły uruchomienia działania lub ponownie uruchomić potok. Wybierz pozycję **Odśwież**, aby odświeżyć listę.

    ![Zrzut ekranu przedstawia okienko uruchomienia potoku.](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs-1.png)
11. W potoku jest tylko jedno działanie (działanie kopiowania), dlatego na liście jest wyświetlana tylko jedna pozycja. Dostosuj szerokość kolumny **źródłowej** i **docelowej** (w razie potrzeby), aby wyświetlić więcej szczegółów, plik źródłowy (file1.txt) został skopiowany ze  *źródła/2020/03/17/03/* do *lokalizacji docelowej/2020/03/17/03/* o tej samej nazwie pliku. 

    ![Zrzut ekranu przedstawia szczegóły uruchomienia potoku.](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs2.png)

    Możesz również sprawdzić to samo przy użyciu Eksplorator usługi Azure Storage ( https://storageexplorer.com/) Aby skanować pliki.

    ![Zrzut ekranu przedstawia szczegóły uruchomienia potoku dla miejsca docelowego.](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs3.png)

12. Utwórz kolejny pusty plik tekstowy o nowej nazwie jako **file2.txt**. Przekaż plik file2.txt do ścieżki folderu **Source/2020/03/17/04** na koncie magazynu. Do wykonania tych zadań możesz użyć różnych narzędzi, takich jak [Eksplorator usługi Azure Storage](https://storageexplorer.com/).

    > [!NOTE]
    > Należy pamiętać, że należy utworzyć nową ścieżkę folderu. Dostosuj nazwę folderu o czas UTC.  Na przykład, jeśli bieżący czas UTC to 4:20 AM. siedemnast, 2020, można utworzyć ścieżkę folderu jako **Source/2020/03/17/04/** według reguły **{Year}/{Month}/{Day}/{Hour}/**.

13. Aby powrócić do widoku **uruchomienia potoków** , wybierz pozycję **wszystkie uruchomienia**, a następnie poczekaj na automatyczne wyzwolenie tego samego potoku po upływie kolejnej godziny.  

    ![Zrzut ekranu przedstawia link wszystkie uruchomienia potoku, aby powrócić do tej strony.](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs5.png)

14. Wybierz łącze Nowy **DeltaCopyFromBlobPipeline** dla drugiego przebiegu potoku, a następnie wykonaj te same czynności, aby przejrzeć szczegóły. Zostanie wyświetlony plik źródłowy (file2.txt) skopiowany ze  **źródła/2020/03/17/04/**  do **lokalizacji docelowej/2020/03/17/04/** o tej samej nazwie pliku. Możesz również sprawdzić to samo przy użyciu Eksplorator usługi Azure Storage ( https://storageexplorer.com/) do skanowania plików w kontenerze **docelowym** .


## <a name="next-steps"></a>Następne kroki
Przejdź do następującego samouczka, aby dowiedzieć się więcej o przekształcaniu danych za pomocą klastra Spark na platformie Azure:

> [!div class="nextstepaction"]
>[Przekształcanie danych przy użyciu klastra Spark w chmurze](tutorial-transform-data-spark-portal.md)
