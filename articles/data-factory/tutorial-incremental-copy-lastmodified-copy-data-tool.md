---
title: Narzędzie danych do przyrostowego kopiowania nowych i zaktualizowanych plików
description: Utwórz fabrykę danych platformy Azure, a następnie użyj narzędzia Kopiowanie danych, aby przyrostowo ładować nowe pliki na podstawie LastModifiedDate.
author: dearandyxu
ms.author: yexu
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/18/2021
ms.openlocfilehash: 25e39d3fc9c56a282714bafb630fee65421fb5ac
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104606671"
---
# <a name="incrementally-copy-new-and-changed-files-based-on-lastmodifieddate-by-using-the-copy-data-tool"></a>Przyrostowe kopiowanie nowych i zmienionych plików na podstawie LastModifiedDate przy użyciu narzędzia Kopiowanie danych

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym samouczku utworzysz fabrykę danych za pomocą Azure Portal. Następnie użyjesz narzędzia Kopiowanie danych, aby utworzyć potok, który przyrostowo kopiuje nowe i zmienione pliki, z usługi Azure Blob Storage do usługi Azure Blob Storage. Używa `LastModifiedDate` do określenia plików do skopiowania.

Po wykonaniu tych czynności Azure Data Factory przeskanuje wszystkie pliki w magazynie źródłowym, zastosuje filtr plików według `LastModifiedDate` i skopiuje do plików tylko do magazynu docelowego, które są nowe lub zostały zaktualizowane od ostatniego czasu. Należy pamiętać, że jeśli Data Factory skanuje dużą liczbę plików, nadal powinny oczekiwać długotrwałych czasów trwania. Skanowanie plików jest czasochłonne, nawet w przypadku zmniejszenia ilości kopiowanych danych.

> [!NOTE]
> Jeśli jesteś nowym użytkownikiem usługi Data Factory, zobacz [Wprowadzenie do usługi Azure Data Factory](introduction.md).

W tym samouczku wykonasz następujące zadania:

> [!div class="checklist"]
> * Tworzenie fabryki danych.
> * Tworzenie potoku za pomocą narzędzia do kopiowania danych.
> * Monitorowanie uruchomień potoku i działań.

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**: jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).
* **Konto usługi Azure Storage**: Użyj usługi BLOB Storage dla magazynów danych źródłowych i ujścia. Jeśli nie masz konta usługi Azure Storage, postępuj zgodnie z instrukcjami w temacie [Tworzenie konta magazynu](../storage/common/storage-account-create.md).

## <a name="create-two-containers-in-blob-storage"></a>Tworzenie dwóch kontenerów w usłudze BLOB Storage

Przygotuj magazyn obiektów BLOB dla tego samouczka, wykonując następujące czynności:

1. Utwórz kontener o nazwie **Source**. Do wykonania tego zadania można użyć różnych narzędzi, takich jak [Eksplorator usługi Azure Storage](https://storageexplorer.com/).

2. Utwórz kontener o nazwie **Destination**.

## <a name="create-a-data-factory"></a>Tworzenie fabryki danych

1. W okienku po lewej stronie wybierz pozycję **Utwórz zasób**. Wybierz   >  **Data Factory** integracji:

   ![Wybierz Data Factory](./media/doc-common-process/new-azure-data-factory-menu.png)

2. Na stronie **Nowa fabryka danych** w polu **Nazwa** wprowadź wartość **ADFTutorialDataFactory**.

   Nazwa Twojej fabryki danych musi być globalnie unikatowa. Może pojawić się następujący komunikat o błędzie:

    :::image type="content" source="./media/doc-common-process/name-not-available-error.png" alt-text="Nowy komunikat o błędzie usługi Fabryka danych dla zduplikowanej nazwy.":::

   Jeśli zostanie wyświetlony komunikat o błędzie dotyczącym wartości nazwy, wprowadź inną nazwę dla fabryki danych. Na przykład użyj nazwy _**twojanazwa**_**ADFTutorialDataFactory**. Artykuł [Data Factory naming rules (Zasady nazewnictwa fabryki danych)](naming-rules.md) zawiera zasady nazewnictwa artefaktów usługi Data Factory.
3. W obszarze **subskrypcja** wybierz subskrypcję platformy Azure, w której chcesz utworzyć nową fabrykę danych.
4. W obszarze **Grupa zasobów** wykonaj jedną z następujących czynności:

    * Wybierz pozycję **Użyj istniejącej** , a następnie wybierz istniejącą grupę zasobów na liście.

    * Wybierz pozycję **Utwórz nowy** , a następnie wprowadź nazwę grupy zasobów.
         
    Informacje na temat grup zasobów znajdują się w artykule [Using resource groups to manage your Azure resources (Używanie grup zasobów do zarządzania zasobami platformy Azure)](../azure-resource-manager/management/overview.md).

5. W obszarze **Wersja** wybierz pozycję **V2**.
6. W obszarze **Lokalizacja** wybierz lokalizację fabryki danych. Na liście są wyświetlane tylko obsługiwane lokalizacje. Magazyny danych (na przykład Azure Storage i Azure SQL Database) i obliczenia (na przykład usługa Azure HDInsight) używane przez fabrykę danych mogą znajdować się w innych lokalizacjach i regionach.
8. Wybierz przycisk **Utwórz**.
9. Po utworzeniu fabryki danych zostanie wyświetlona strona główna Fabryka danych.
10. Aby otworzyć Azure Data Factory interfejs użytkownika na oddzielnej karcie, wybierz kafelek **tworzenie & monitor** :

    :::image type="content" source="./media/doc-common-process/data-factory-home-page.png" alt-text="Strona główna Azure Data Factory z kafelkiem &quot;autor & Monitor&quot;.":::

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Tworzenie potoku za pomocą narzędzia do kopiowania danych

1. **Na stronie Wprowadzenie** wybierz kafelek **Kopiowanie danych** , aby otworzyć narzędzie kopiowanie danych:

   ![Kafelek Kopiowanie danych](./media/doc-common-process/get-started-page.png)

2. Na stronie **Właściwości** wykonaj następujące czynności:

    a. W obszarze **Nazwa zadania** wprowadź **DeltaCopyFromBlobPipeline**.

    b. W obszarze **zadanie erze lub harmonogram zadań** wybierz pozycję **Uruchom regularnie zgodnie z harmonogramem**.

    c. W obszarze **Typ wyzwalacza** wybierz pozycję **okno wirowania**.

    d. W obszarze **cykl** wprowadź **15 minut**.

    e. Wybierz opcję **Dalej**.

    Data Factory tworzy potok o określonej nazwie zadania.

    ![Strona właściwości kopiowania danych](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/copy-data-tool-properties-page.png)

3. Na stronie **Magazyn danych źródłowych** wykonaj następujące kroki:

    a. Wybierz pozycję  **Utwórz nowe połączenie** , aby dodać połączenie.

    b. Wybierz pozycję **Azure Blob Storage** z galerii, a następnie wybierz pozycję **Kontynuuj**:

    ![Wybieranie magazynu blogów platformy Azure](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-blob.png)

    c. Na stronie **Nowa połączona usługa (Azure Blob Storage)** wybierz konto magazynu z listy **nazwa konta magazynu** . Przetestuj połączenie, a następnie wybierz pozycję **Utwórz**.

    d. Wybierz nową połączoną usługę, a następnie wybierz pozycję **dalej**:

   ![Wybierz nową połączoną usługę](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-linkedservice.png)

4. Na stronie **Wybieranie pliku lub folderu wejściowego** wykonaj następujące czynności:

    a. Wyszukaj i wybierz folder **źródłowy** , a następnie wybierz pozycję **Wybierz**.

    ![Wybieranie pliku lub folderu wejściowego](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-input-file-folder.png)

    b. W obszarze **zachowanie ładowania pliku** wybierz pozycję **obciążenie przyrostowe: LastModifiedDate**.

    c. Wybierz pozycję **kopia binarna** , a następnie wybierz pozycję **dalej**:

     ![Wybieranie pliku lub folderu wejściowego](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/check-binary-copy.png)

5. Na stronie **docelowy magazyn danych** Wybierz utworzoną usługę **AzureBlobStorage** . To konto magazynu jest takie samo jak magazyn danych źródłowych. Następnie wybierz przycisk **Dalej**.

6. Na stronie **Wybieranie pliku lub folderu wyjściowego** wykonaj następujące czynności:

    a. Wyszukaj i wybierz folder **docelowy** , a następnie wybierz pozycję **Wybierz**:

    ![Wybieranie pliku lub folderu wyjściowego](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-output-file-folder.png)

    b. Wybierz opcję **Dalej**.

7. Na stronie **Ustawienia** wybierz przycisk **Dalej**.

8. Na stronie **Podsumowanie** przejrzyj ustawienia, a następnie wybierz przycisk **dalej**.

    ![Strona podsumowania](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/summary-page.png)

9. Na **stronie Wdrażanie** wybierz pozycję **Monitorowanie**, aby monitorować potok (zadanie).

    ![Strona Wdrażanie](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/deployment-page.png)

10. Zwróć uwagę, że karta **Monitor** po lewej stronie jest automatycznie wybrana. Aplikacja przełączy się na kartę **monitorowanie** . Zobaczysz stan potoku. Wybierz pozycję **Odśwież**, aby odświeżyć listę. Wybierz link pod **nazwą potoku** , aby wyświetlić szczegóły uruchomienia działania lub ponownie uruchomić potok.

    ![Odśwież listę i Wyświetl szczegóły uruchomienia działania](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs-1.png)

11. W potoku jest tylko jedno działanie (działanie kopiowania), więc zobaczysz tylko jeden wpis. Aby uzyskać szczegółowe informacje na temat operacji kopiowania, wybierz link **szczegóły** (ikona okularów) w kolumnie **Nazwa działania** . Aby uzyskać szczegółowe informacje na temat właściwości, zobacz [Omówienie działania kopiowania](copy-activity-overview.md).

    ![Działanie kopiowania w potoku](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs2.png)

    Ponieważ nie ma żadnych plików w kontenerze źródłowym na koncie magazynu obiektów blob, nie będą widoczne żadne pliki skopiowane do kontenera docelowego na koncie:

    ![Brak plików w kontenerze źródłowym lub kontenerze docelowym](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3.png)

12. Utwórz pusty plik tekstowy i nadaj mu nazwę **file1.txt**. Przekaż ten plik tekstowy do kontenera źródłowego na koncie magazynu. Do wykonywania tych zadań można użyć różnych narzędzi, takich jak [Eksplorator usługi Azure Storage](https://storageexplorer.com/).

    ![Utwórz file1.txt i przekaż go do kontenera źródłowego](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3-1.png)

13. Aby powrócić do widoku **uruchomienia potoków** , zaznacz **wszystkie uruchomienia potoków** i poczekaj na ponowne wyzwolenie tego samego potoku.  

14. Po zakończeniu drugiego przebiegu potoku wykonaj powyższe same kroki, aby przejrzeć szczegóły uruchomienia działania.  

    Zobaczysz, że jeden plik (file1.txt) został skopiowany z kontenera źródłowego do docelowego kontenera konta usługi BLOB Storage:

    ![file1.txt skopiowano z kontenera źródłowego do kontenera docelowego](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs6.png)

15. Utwórz kolejny pusty plik tekstowy i nadaj mu nazwę **file2.txt**. Przekaż ten plik tekstowy do kontenera źródłowego na koncie magazynu obiektów BLOB.

16. Powtórz kroki od 13 do 14 dla drugiego pliku tekstowego. Zobaczysz, że tylko nowy plik (file2.txt) został skopiowany z kontenera źródłowego do docelowego kontenera konta magazynu podczas tego uruchomienia potoku.  

    Można również sprawdzić, czy tylko jeden plik został skopiowany przy użyciu [Eksplorator usługi Azure Storage](https://storageexplorer.com/) do skanowania plików:

    ![Skanuj pliki przy użyciu Eksplorator usługi Azure Storage](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs8.png)


## <a name="next-steps"></a>Następne kroki
Przejdź do następującego samouczka, aby dowiedzieć się, jak przekształcić dane przy użyciu klastra Apache Spark na platformie Azure:

> [!div class="nextstepaction"]
>[Przekształcanie danych w chmurze przy użyciu klastra Apache Spark](tutorial-transform-data-spark-portal.md)
