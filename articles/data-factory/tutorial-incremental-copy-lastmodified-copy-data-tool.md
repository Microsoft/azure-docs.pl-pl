---
title: Narzędzie Dane do kopiowania nowych i zaktualizowanych plików przyrostowo
description: Utwórz fabrykę danych platformy Azure, a następnie użyj narzędzia Kopiuj dane, aby stopniowo ładować nowe pliki na podstawie LastModifiedDate.
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.devlang: na
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 3/18/2020
ms.openlocfilehash: 3098ca0d3d5e41c298d3058ffa84fcf129648281
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399482"
---
# <a name="incrementally-copy-new-and-changed-files-based-on-lastmodifieddate-by-using-the-copy-data-tool"></a>Przyrostowo kopiuj nowe i zmienione pliki na podstawie lastmodifieddate za pomocą narzędzia Kopiuj dane

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

W tym samouczku użyjesz witryny Azure portal do utworzenia fabryki danych. Następnie użyjesz narzędzia Kopiuj dane, aby utworzyć potok, który przyrostowo kopiuje tylko nowe i zmienione pliki, z magazynu obiektów Blob platformy Azure do magazynu obiektów blob platformy Azure. Służy `LastModifiedDate` do określenia, które pliki mają być kopiowane.

Po wykonaniu tych czynności usługa Azure Data Factory przeskanuje wszystkie pliki `LastModifiedDate`w magazynie źródłowym, zastosuje filtr plików według programu i skopiuje do magazynu docelowego tylko pliki, które są nowe lub zostały zaktualizowane od czasu ostatniego. Należy zauważyć, że jeśli usługa Data Factory skanuje dużą liczbę plików, nadal należy oczekiwać długich czasów trwania. Skanowanie plików jest czasochłonne, nawet jeśli ilość kopiowanych danych jest zmniejszona.

> [!NOTE]
> Jeśli jesteś nowym użytkownikiem usługi Data Factory, zobacz [Wprowadzenie do usługi Azure Data Factory](introduction.md).

W tym samouczku wykonasz następujące zadania:

> [!div class="checklist"]
> * Tworzenie fabryki danych.
> * Tworzenie potoku za pomocą narzędzia do kopiowania danych.
> * Monitorowanie uruchomień potoku i działań.

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**: jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).
* **Konto usługi Azure Storage:** Użyj magazynu obiektów Blob dla magazynów danych źródłowych i ujścia. Jeśli nie masz konta usługi Azure Storage, postępuj zgodnie z instrukcjami w [obszarze Tworzenie konta magazynu](../storage/common/storage-account-create.md).

## <a name="create-two-containers-in-blob-storage"></a>Tworzenie dwóch kontenerów w magazynie obiektów Blob

Przygotuj magazyn obiektów blob do samouczka, wykonując następujące kroki:

1. Utwórz kontener o nazwie **źródło**. Do wykonania tego zadania można użyć różnych narzędzi, takich jak [Eksplorator usług Azure Storage](https://storageexplorer.com/).

2. Utwórz kontener o nazwie **miejsce docelowe**.

## <a name="create-a-data-factory"></a>Tworzenie fabryki danych

1. W okienku po lewej stronie wybierz pozycję **Utwórz zasób**. Wybierz**fabrykę danych** **analytics:** > 

   ![Wybierz fabrykę danych](./media/doc-common-process/new-azure-data-factory-menu.png)

2. Na stronie **Nowa fabryka danych** w polu **Nazwa** wprowadź wartość **ADFTutorialDataFactory**.

   Nazwa Twojej fabryki danych musi być globalnie unikatowa. Może pojawić się ten komunikat o błędzie:

   ![Komunikat o błędzie Nazwa niedostępna](./media/doc-common-process/name-not-available-error.png)

   Jeśli zostanie wyświetlony komunikat o błędzie dotyczącym wartości nazwy, wprowadź inną nazwę dla fabryki danych. Na przykład użyj nazwy _**twojanazwa**_**ADFTutorialDataFactory**. Artykuł [Data Factory naming rules (Zasady nazewnictwa fabryki danych)](naming-rules.md) zawiera zasady nazewnictwa artefaktów usługi Data Factory.
3. W obszarze **Subskrypcja**wybierz subskrypcję platformy Azure, w której utworzysz nową fabrykę danych.
4. W obszarze **Grupa zasobów**wykonaj jedną z następujących czynności:

    * Wybierz **pozycję Użyj istniejącej,** a następnie wybierz istniejącą grupę zasobów na liście.

    * Wybierz **pozycję Utwórz nowy,** a następnie wprowadź nazwę grupy zasobów.
         
    Informacje na temat grup zasobów znajdują się w artykule [Using resource groups to manage your Azure resources (Używanie grup zasobów do zarządzania zasobami platformy Azure)](../azure-resource-manager/management/overview.md).

5. W obszarze **Wersja** wybierz pozycję **V2**.
6. W obszarze **Lokalizacja** wybierz lokalizację fabryki danych. Na liście są wyświetlane tylko obsługiwane lokalizacje. Magazyny danych (na przykład Usługa Azure Storage i azure SQL Database) i oblicza (na przykład Usługi Azure HDInsight), które używa fabryka danych może znajdować się w innych lokalizacjach i regionach.
8. Wybierz pozycję **Utwórz**.
9. Po utworzeniu fabryki danych zostanie wyświetlona strona główna fabryki danych.
10. Aby otworzyć interfejs użytkownika usługi Azure Data Factory (UI) na osobnej karcie, wybierz kafelek **Monitor & autora:**

    ![Strona główna fabryki danych](./media/doc-common-process/data-factory-home-page.png)

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Tworzenie potoku za pomocą narzędzia do kopiowania danych

1. Na stronie **Rozpocznijmy pracę,** wybierz kafelek **Kopiuj dane,** aby otworzyć narzędzie Kopiuj dane:

   ![Kopiowanie kafelka Danych](./media/doc-common-process/get-started-page.png)

2. Na stronie **Właściwości** należy wykonać następujące czynności:

    a. W **obszarze Nazwa zadania**wpisz **DeltaCopyFromBlobPipeline**.

    b. W obszarze **Rytm zadania lub Harmonogram zadań**wybierz pozycję Uruchom regularnie zgodnie z **harmonogramem**.

    d. W obszarze **Typ wyzwalacza**wybierz **okno Tumbling**.

    d. W obszarze **Cykl**wprowadź **15 minut.**

    e. Wybierz opcję **Dalej**.

    Fabryka danych tworzy potok o określonej nazwie zadania.

    ![Strona Kopiowanie właściwości danych](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/copy-data-tool-properties-page.png)

3. Na stronie **Magazyn danych źródłowych** wykonaj następujące czynności:

    a. Wybierz **pozycję Utwórz nowe połączenie,** aby dodać połączenie.

    b. Wybierz **usługę Azure Blob Storage** z galerii, a następnie wybierz pozycję **Kontynuuj:**

    ![Wybieranie usługi Azure Blog Storage](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-blob.png)

    d. Na stronie **Nowa usługa łączona (usługa Azure Blob Storage)** wybierz konto magazynu z listy **Nazwa konta magazynu.** Przetestuj połączenie, a następnie wybierz pozycję **Utwórz**.

    d. Wybierz nową usługę połączony, a następnie wybierz pozycję **Dalej:**

   ![Wybierz nową usługę połączony](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-linkedservice.png)

4. Na stronie **Wybieranie pliku lub folderu wejściowego** wykonaj następujące czynności:

    a. Wyszukaj i wybierz folder **źródłowy,** a następnie wybierz pozycję **Wybierz**.

    ![Wybieranie pliku lub folderu wejściowego](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-input-file-folder.png)

    b. W obszarze **Zachowanie ładowania plików**wybierz pozycję Obciążenie **przyrostowe: Ostatnia data duszna**.

    d. Wybierz **opcję Kopiuj binarny,** a następnie wybierz pozycję **Dalej:**

     ![Wybieranie pliku wejściowego lub strony folderu](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/check-binary-copy.png)

5. Na stronie **Docelowy magazyn danych** wybierz utworzoną usługę **AzureBlobStorage.** Jest to to samo konto magazynu co źródłowy magazyn danych. Następnie wybierz **przycisk Dalej**.

6. Na stronie **Wybieranie pliku lub folderu wyjściowego** wykonaj następujące czynności:

    a. Wyszukaj i wybierz folder **docelowy,** a następnie wybierz pozycję **Wybierz**:

    ![Wybieranie pliku wyjściowego lub strony folderu](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-output-file-folder.png)

    b. Wybierz opcję **Dalej**.

7. Na stronie **Ustawienia** wybierz przycisk **Dalej**.

8. Na stronie **Podsumowanie** przejrzyj ustawienia, a następnie wybierz pozycję **Dalej**.

    ![Strona podsumowania](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/summary-page.png)

9. Na **stronie Wdrażanie** wybierz pozycję **Monitorowanie**, aby monitorować potok (zadanie).

    ![Strona Wdrażanie](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/deployment-page.png)

10. Zwróć uwagę, że karta **Monitor** po lewej stronie jest automatycznie wybrana. Aplikacja przełączy się na kartę **Monitor.** Zobaczysz stan potoku. Wybierz pozycję **Odśwież**, aby odświeżyć listę. Wybierz łącze w obszarze **NAZWA POTOKU,** aby wyświetlić szczegóły uruchomienia działania lub ponownie uruchomić potok.

    ![Odświeżanie listy i wyświetlanie szczegółów uruchomienia aktywności](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs1.png)

11. Istnieje tylko jedno działanie (działanie kopiowania) w potoku, więc widzisz tylko jeden wpis. Aby uzyskać szczegółowe informacje na temat operacji kopiowania, wybierz łącze **Szczegóły** (ikona okularów) w kolumnie **NAZWA DZIAŁANIA.** Aby uzyskać szczegółowe informacje o właściwościach, zobacz [Omówienie działania kopiowania](copy-activity-overview.md).

    ![Kopiowanie działania w potoku](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs2.png)

    Ponieważ na koncie magazynu obiektów Blob nie ma żadnych plików, żadne pliki nie będą kopiowane do kontenera docelowego na koncie:

    ![Brak plików w kontenerze źródłowym lub kontenerze docelowym](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3.png)

12. Utwórz pusty plik tekstowy i nadaj jego nazwę **plikowi1.txt**. Przekaż ten plik tekstowy do kontenera źródłowego na koncie magazynu. Do wykonywania tych zadań można używać różnych narzędzi, takich jak [Eksplorator usług Azure Storage](https://storageexplorer.com/).

    ![Utwórz plik1.txt i przekaż go do kontenera źródłowego](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3-1.png)

13. Aby wrócić do widoku **Przebiegi potoku,** wybierz **opcję Wszystkie uruchomienia potoku**i poczekaj, aż ten sam potok zostanie automatycznie uruchomiony ponownie.  

    ![Wybierz wszystkie przebiegi potoku](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs4.png)

14. Po zakończeniu drugiego uruchomienia potoku wykonaj te same kroki wymienione wcześniej, aby przejrzeć szczegóły uruchomienia działania.  

    Zobaczysz, że jeden plik (file1.txt) został skopiowany z kontenera źródłowego do kontenera docelowego konta magazynu obiektów Blob:

    ![plik1.txt został skopiowany z kontenera źródłowego do kontenera docelowego](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs6.png)

15. Utwórz kolejny pusty plik tekstowy i nadaj jego nazwę **plikowi file2.txt**. Przekaż ten plik tekstowy do kontenera źródłowego na koncie magazynu obiektów Blob.

16. Powtórz kroki 13 i 14 dla drugiego pliku tekstowego. Zobaczysz, że tylko nowy plik (file2.txt) został skopiowany z kontenera źródłowego do kontenera docelowego konta magazynu podczas tego uruchomienia potoku.  

    Można również sprawdzić, czy tylko jeden plik został skopiowany przy użyciu [Usługi Azure Storage Explorer](https://storageexplorer.com/) do skanowania plików:

    ![Skanowanie plików przy użyciu Eksploratora usługi Azure Storage](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs8.png)


## <a name="next-steps"></a>Następne kroki
Przejdź do następującego samouczka, aby dowiedzieć się, jak przekształcać dane przy użyciu klastra Platformy Spark Apache na platformie Azure:

> [!div class="nextstepaction"]
>[Przekształcanie danych w chmurze przy użyciu klastra Platformy Spark Apache](tutorial-transform-data-spark-portal.md)
