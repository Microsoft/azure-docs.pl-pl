---
title: Przyrostowe kopiowanie nowych plików na podstawie nazwy pliku podzielonego na partycje czasu
description: Utwórz fabrykę danych platformy Azure, a następnie użyj narzędzia Kopiuj dane, aby stopniowo ładować nowe pliki tylko na podstawie nazwy pliku podzielonego na partycje czasu.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.devlang: na
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 3/17/2020
ms.openlocfilehash: 6cc089a1efc3f5960a8bca8a36063bb1019bbcc6
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81409395"
---
# <a name="incrementally-copy-new-files-based-on-time-partitioned-file-name-by-using-the-copy-data-tool"></a>Przyrostowe kopiowanie nowych plików na podstawie nazwy pliku podzielonego na partycje czasu przy użyciu narzędzia Kopiuj dane

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

W tym samouczku utworzysz fabrykę danych za pomocą witryny Azure Portal. Następnie użyj narzędzia Kopiuj dane, aby utworzyć potok, który stopniowo kopiuje nowe pliki na podstawie nazwy pliku podzielonego na partycje czasu z magazynu obiektów blob platformy Azure do magazynu obiektów blob platformy Azure.

> [!NOTE]
> Jeśli jesteś nowym użytkownikiem usługi Azure Data Factory, zobacz [Wprowadzenie do usługi Azure Data Factory](introduction.md).

Ten samouczek obejmuje wykonanie następujących kroków:

> [!div class="checklist"]
> * Tworzenie fabryki danych.
> * Tworzenie potoku za pomocą narzędzia do kopiowania danych.
> * Monitorowanie uruchomień potoku i działań.

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**: jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).
* **Konto magazynu platformy Azure:** Użyj magazynu obiektów Blob jako magazynu danych _źródłowych_ i _magazynu ujścia._ Jeśli nie masz konta usługi Azure Storage, zobacz instrukcje podane w temacie [Tworzenie konta magazynu](../storage/common/storage-account-create.md).

### <a name="create-two-containers-in-blob-storage"></a>Tworzenie dwóch kontenerów w magazynie obiektów Blob

Przygotuj magazyn obiektów Blob do samouczka, wykonując te kroki.

1. Utwórz kontener o nazwie **źródło**.  Utwórz ścieżkę folderu jako **2020/03/17/03** w kontenerze. Utwórz pusty plik tekstowy i nazwij go jako **plik 1.txt**. Przekaż plik1.txt do źródła ścieżki **folderu/2020/03/17/03** na koncie magazynu.  Do wykonania tych zadań możesz użyć różnych narzędzi, takich jak [Eksplorator usługi Azure Storage](https://storageexplorer.com/).

    ![przekazywanie plików](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/upload-file.png)

    > [!NOTE]
    > Dostosuj nazwę folderu z czasem UTC.  Jeśli na przykład bieżący czas UTC to 3:38 AM w dniu 17 marca 2020 r., można utworzyć ścieżkę folderu jako **źródło/2020/03/17/03/** według **reguły źródła/{Rok}/{Miesiąc}/{Dzień}/{Godzina}/**.

2. Utwórz kontener o nazwie **miejsce docelowe**. Do wykonania tych zadań możesz użyć różnych narzędzi, takich jak [Eksplorator usługi Azure Storage](https://storageexplorer.com/).

## <a name="create-a-data-factory"></a>Tworzenie fabryki danych

1. W menu po lewej stronie wybierz pozycję **Utwórz źródło** > **Data + Analytics** > **Data Factory**:

   ![Wybór usługi Data Factory w okienku „Nowy”](./media/doc-common-process/new-azure-data-factory-menu.png)

2. Na stronie **Nowa fabryka danych** w polu **Nazwa** wprowadź wartość **ADFTutorialDataFactory**.

    Nazwa Twojej fabryki danych musi być _globalnie unikatowa_. Może zostać wyświetlony następujący komunikat o błędzie:

   ![Komunikat o błędzie dotyczący nowej fabryki danych](./media/doc-common-process/name-not-available-error.png)

   Jeśli zostanie wyświetlony komunikat o błędzie dotyczącym wartości nazwy, wprowadź inną nazwę dla fabryki danych. Na przykład użyj nazwy _**twojanazwa**_**ADFTutorialDataFactory**. Artykuł [Data Factory naming rules (Zasady nazewnictwa fabryki danych)](naming-rules.md) zawiera zasady nazewnictwa artefaktów usługi Data Factory.
3. Wybierz **subskrypcję** platformy Azure, w której utworzysz nową fabrykę danych.
4. W obszarze **Grupa zasobów** wykonaj jedną z następujących czynności:

    a. Wybierz pozycję **Użyj istniejącej**, a następnie wybierz istniejącą grupę zasobów z listy rozwijanej.

    b. Wybierz **pozycję Utwórz nowy**i wprowadź nazwę grupy zasobów. 
         
    Informacje na temat grup zasobów znajdują się w artykule [Using resource groups to manage your Azure resources (Używanie grup zasobów do zarządzania zasobami platformy Azure)](../azure-resource-manager/management/overview.md).

5. W obszarze **Wersja** wybierz wersję **V2**.
6. W obszarze **Lokalizacja**wybierz lokalizację fabryki danych. Na liście rozwijanej są wyświetlane tylko obsługiwane lokalizacje. Magazyny danych (np. usługi Azure Storage i SQL Database) oraz jednostki obliczeniowe (np. usługa Azure HDInsight) używane przez Twoją fabrykę danych mogą mieścić się w innych lokalizacjach i regionach.
7. Wybierz pozycję **Utwórz**.
8. Po zakończeniu tworzenia zostanie wyświetlona strona główna usługi **Data Factory**.
9. Aby w osobnej karcie uruchomić interfejs użytkownika usługi Azure Data Factory, kliknij kafelek **Tworzenie i monitorowanie**.

    ![Strona główna fabryki danych](./media/doc-common-process/data-factory-home-page.png)


## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Tworzenie potoku za pomocą narzędzia do kopiowania danych

1. Na stronie **Rozpocznijmy pracę** wybierz tytuł **Kopiuj dane,** aby uruchomić narzędzie Kopiuj dane.

   ![Kafelek narzędzia do kopiowania danych](./media/doc-common-process/get-started-page.png)

2. Na stronie **Właściwości** należy wykonać następujące czynności:

    a. W **obszarze Nazwa zadania**wpisz **DeltaCopyFromBlobPipeline**.

    b. W obszarze **Rytm zadania lub Harmonogram zadań**wybierz pozycję Uruchom regularnie zgodnie z **harmonogramem**.

    d. W obszarze **Typ wyzwalacza**wybierz pozycję **Okno tumbling**.

    d. W obszarze **Cykl**wprowadź **1 godzinę.).**

    e. Wybierz opcję **Dalej**.

    Interfejs użytkownika usługi Data Factory tworzy potok o określonej nazwie zadania.

    ![Strona właściwości](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/copy-data-tool-properties-page.png)
3. Na stronie **Źródłowy magazyn danych** wykonaj następujące czynności:

    a. Kliknij pozycję **+Utwórz nowe połączenie**, aby dodać połączenie.
    
    b. Wybierz usługę Azure Blob Storage z galerii, a następnie wybierz pozycję Kontynuuj.
    
    d. Na stronie **Nowa usługa połączona (Usługa Azure Blob Storage)** wprowadź nazwę połączonej usługi. Wybierz subskrypcję platformy Azure i wybierz swoje konto magazynu z listy **Nazwa konta magazynu.** Przetestuj połączenie, a następnie wybierz pozycję **Utwórz**.

    ![Strona Źródłowy magazyn danych](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page-linkedservice.png)

    d. Wybierz nowo utworzoną usługę połączeniową na stronie **Magazyn danych źródłowych,** a następnie kliknij przycisk **Dalej**.

4. Na stronie **Wybieranie pliku lub folderu wejściowego** wykonaj następujące kroki:

    a. Przeglądaj i wybierz kontener **źródłowy,** a następnie wybierz pozycję **Wybierz**.

    ![Wybieranie pliku lub folderu wejściowego](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/choose-input-file-folder.png)

    b. W obszarze **Zachowanie ładowania plików**wybierz pozycję Obciążenie **przyrostowe: folder podzielony na partycje czasowy/nazwy plików**.

    d. Zapisz ścieżkę folderu dynamicznego jako **źródło/{rok}/{miesiąc}/{dzień}/{godzina}/** i zmień format pokazany na poniższym zrzucie ekranu. Zaznacz **opcję Kopiuj binarny** i kliknij przycisk **Dalej**.

    ![Wybieranie pliku lub folderu wejściowego](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/check-binary-copy.png)     

5. Na stronie **Docelowy magazyn danych** wybierz **pozycję AzureBlobStorage**, która jest tym samym kontem magazynu co magazyn źródeł danych, a następnie kliknij przycisk **Dalej**.

    ![Strona Docelowy magazyn danych](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/destination-data-store-page-select-linkedservice.png)
6. Na stronie **Wybieranie pliku wyjściowego lub folderu** wykonaj następujące czynności:

    a. Przeglądaj i zaznacz folder **docelowy,** a następnie kliknij przycisk **Wybierz**.

    b. Zapisz ścieżkę folderu dynamicznego jako **miejsce docelowe/{rok}/{miesiąc}/{dzień}/{godzina}/** i zmień format w następujący sposób:

    ![Wybieranie pliku lub folderu wyjściowego](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/output-file-name.png)

    d. Kliknij przycisk **Dalej**.

    ![Wybieranie pliku lub folderu wyjściowego](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/click-next-after-output-folder.png)
7. Na stronie **Ustawienia** wybierz przycisk **Dalej**.

8. Na stronie **Podsumowanie** sprawdź ustawienia, a następnie kliknij przycisk **Dalej**.

    ![Strona podsumowania](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/summary-page.png)

9. Na **stronie Wdrażanie** wybierz pozycję **Monitorowanie**, aby monitorować potok (zadanie).
    ![Strona Wdrażanie](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/deployment-page.png)

10. Zwróć uwagę, że karta **Monitor** po lewej stronie jest automatycznie wybrana.  Należy poczekać na uruchomienie potoku, gdy jest wyzwalany automatycznie (około po godzinie). Po uruchomieniu kliknij link nazwa potoku **DeltaCopyFromBlobPipeline,** aby wyświetlić szczegóły uruchomienia działania lub ponownie uruchomić potoku. Wybierz pozycję **Odśwież**, aby odświeżyć listę.

    ![Monitorowanie uruchomień potoku](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs1.png)
11. W potoku jest tylko jedno działanie (działanie kopiowania), dlatego na liście jest wyświetlana tylko jedna pozycja. Dostosuj szerokość kolumny **źródłowej** i **docelowej** (jeśli to konieczne), aby wyświetlić więcej szczegółów, możesz zobaczyć plik źródłowy (file1.txt) został skopiowany ze *źródła/2020/03/17/03/* do *miejsca docelowego/2020/03/17/03/* o tej samej nazwie pliku. 

    ![Monitorowanie uruchomień potoku](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs2.png)

    Można również sprawdzić to samo zahttps://storageexplorer.com/) pomocą Usługi Azure Storage Explorer ( do skanowania plików.

    ![Monitorowanie uruchomień potoku](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs3.png)

12. Utwórz kolejny pusty plik tekstowy o nowej nazwie jako **file2.txt**. Przekaż plik file2.txt do źródła ścieżki **folderu/2020/03/17/04** na koncie magazynu. Do wykonania tych zadań możesz użyć różnych narzędzi, takich jak [Eksplorator usługi Azure Storage](https://storageexplorer.com/).

    > [!NOTE]
    > Być może należy pamiętać, że do utworzenia nowej ścieżki folderu jest wymagana nowa ścieżka folderu. Dostosuj nazwę folderu z czasem UTC.  Na przykład, jeśli bieżący czas UTC to 4:20 AM w dniu 17 marca 2020 r., można utworzyć ścieżkę folderu jako **źródło/2020/03/17/04/** według **reguły {Rok}/{Miesiąc}/{Dzień}/{Godzina}/**.

13. Aby wrócić do widoku **Przebiegi potoku,** wybierz **opcję Wszystkie potoki są uruchamiane**i poczekaj, aż ten sam potok zostanie automatycznie wyzwolony po kolejnej godzinie.  

    ![Monitorowanie uruchomień potoku](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs5.png)

14. Wybierz nowe **łącze DeltaCopyFromBlobPipeline** dla drugiego uruchomienia potoku, gdy chodzi, i wykonaj to samo, aby przejrzeć szczegóły. Zobaczysz, że plik źródłowy (file2.txt) został skopiowany **ze źródła/2020/03/17/04/** do **miejsca docelowego/2020/03/17/04/** o tej samej nazwie pliku. Można również zweryfikować to samo zahttps://storageexplorer.com/) pomocą Usługi Azure Storage Explorer ( do skanowania plików w kontenerze **docelowym.**


## <a name="next-steps"></a>Następne kroki
Przejdź do następującego samouczka, aby dowiedzieć się więcej o przekształcaniu danych za pomocą klastra Spark na platformie Azure:

> [!div class="nextstepaction"]
>[Przekształcanie danych przy użyciu klastra Spark w chmurze](tutorial-transform-data-spark-portal.md)
