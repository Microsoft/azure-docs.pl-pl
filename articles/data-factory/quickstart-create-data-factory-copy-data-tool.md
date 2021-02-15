---
title: Kopiowanie danych za pomocą narzędzia Kopiowanie danych platformy Azure
description: Utwórz Azure Data Factory a następnie użyj narzędzia Kopiowanie danych, aby skopiować dane z jednej lokalizacji w usłudze Azure Blob Storage do innej lokalizacji.
author: dearandyxu
ms.author: yexu
ms.service: data-factory
ms.topic: quickstart
ms.date: 11/09/2020
ms.openlocfilehash: 48624e415649be2fb76aed99c5cb0e1b3b12f04b
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100372862"
---
# <a name="quickstart-use-the-copy-data-tool-to-copy-data"></a>Szybki Start: Używanie narzędzia Kopiowanie danych do kopiowania danych

> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Bieżąca wersja](quickstart-create-data-factory-copy-data-tool.md)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

W tym przewodniku Szybki start utworzysz fabrykę danych przy użyciu witryny Azure Portal. Następnie za pomocą narzędzia do kopiowania danych utworzysz potok służący do kopiowania danych z folderu w magazynie Azure Blob Storage do innego folderu. 

> [!NOTE]
> Jeśli jesteś nowym użytkownikiem usługi Azure Data Factory, przed wykonaniem kroków zawartych w tym przewodniku Szybki start zobacz [Wprowadzenie do usługi Azure Data Factory](introduction.md). 

[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)] 

## <a name="create-a-data-factory"></a>Tworzenie fabryki danych

1. Uruchom przeglądarkę internetową **Microsoft Edge** lub **Google Chrome**. Obecnie interfejs użytkownika usługi Data Factory jest obsługiwany tylko przez przeglądarki internetowe Microsoft Edge i Google Chrome.
1. Przejdź do witryny [Azure Portal](https://portal.azure.com). 
1. Z menu Azure Portal wybierz pozycję **Utwórz zasób**  >  **integracja**  >  **Data Factory**:

    ![Tworzenie nowej fabryki danych](./media/doc-common-process/new-azure-data-factory-menu.png)

1. Na stronie **Nowa fabryka danych** wprowadź wartość **ADFTutorialDataFactory** w polu **Nazwa**. 
 
   Nazwa Azure Data Factory musi być *globalnie unikatowa*. Jeśli zostanie wyświetlony następujący błąd, Zmień nazwę fabryki danych (na przykład **&lt; &gt; ADFTutorialDataFactory**) i spróbuj utworzyć ją ponownie. Artykuł [Usługa Data Factory — reguły nazewnictwa](naming-rules.md) zawiera reguły nazewnictwa artefaktów usługi Data Factory.
  
   ![Komunikat o błędzie występujący, jeśli nazwa jest niedostępna](./media/doc-common-process/name-not-available-error.png)
1. W obszarze **Subskrypcja** wybierz subskrypcję platformy Azure, w której chcesz utworzyć fabrykę danych. 
1. W obszarze **Grupa zasobów** wykonaj jedną z następujących czynności:
     
   - Wybierz pozycję **Użyj istniejącej**, a następnie wybierz istniejącą grupę zasobów z listy. 
   - Wybierz pozycję **Utwórz nową**, a następnie wprowadź nazwę grupy zasobów.   
         
   Informacje na temat grup zasobów znajdują się w artykule [Using resource groups to manage your Azure resources](../azure-resource-manager/management/overview.md) (Używanie grup zasobów do zarządzania zasobami platformy Azure).  
1. W obszarze **Wersja** wybierz pozycję **V2**.
1. W obszarze **Lokalizacja** wybierz lokalizację fabryki danych.

   Ta lista zawiera tylko lokalizacje, które są obsługiwane przez usługę Data Factory i w których będą przechowywane metadane usługi Azure Data Factory. Skojarzone magazyny danych (np. usługi Azure Storage i Azure SQL Database) i usługi obliczeniowe (np. usługa Azure HDInsight) używane przez Data Factory mogą działać w innych regionach.

1. Wybierz przycisk **Utwórz**.

1. Po zakończeniu tworzenia zostanie wyświetlona strona **Data Factory** . Wybierz kafelek **Tworzenie i monitorowanie**, aby na osobnej karcie uruchomić aplikację interfejsu użytkownika usługi Azure Data Factory.
   
   ![Strona główna fabryki danych z kafelkiem „Tworzenie i monitorowanie”](./media/doc-common-process/data-factory-home-page.png)

## <a name="start-the-copy-data-tool"></a>Uruchamianie narzędzia do kopiowania danych

1. Na stronie **Wprowadzenie** wybierz kafelek **Kopiowanie danych**, aby uruchomić narzędzie do kopiowania danych. 

   ![Kafelek „Kopiowanie danych”](./media/doc-common-process/get-started-page.png)

1. Na stronie **Właściwości** narzędzia do kopiowania danych możesz określić nazwę potoku i jego opis, a następnie wybrać przycisk **Dalej**. 

   ![Strona „Właściwości”](./media/quickstart-create-data-factory-copy-data-tool/copy-data-tool-properties-page.png)
1. Na stronie **Źródłowy magazyn danych** wykonaj następujące czynności:

    a. Kliknij pozycję **+ Utwórz nowe połączenie** , aby dodać połączenie.

    b. Wybierz typ połączonej usługi, który chcesz utworzyć dla połączenia źródłowego. W tym samouczku używamy **platformy Azure Blob Storage**. Wybierz ją z galerii, a następnie wybierz pozycję **Kontynuuj**.
    
    ![Wybierz obiekt BLOB](./media/quickstart-create-data-factory-copy-data-tool/select-blob-source.png)

    c. Na stronie **Nowa połączona usługa (Azure Blob Storage)** Określ nazwę połączonej usługi. Wybierz konto magazynu z listy **nazwa konta magazynu** , Testuj połączenie, a następnie wybierz pozycję **Utwórz**. 

    ![Konfigurowanie konta usługi Azure Blob Storage](./media/quickstart-create-data-factory-copy-data-tool/configure-blob-storage.png)

    d. Wybierz nowo utworzoną połączoną usługę jako źródło, a następnie kliknij przycisk **dalej**.


1. Na stronie **Wybieranie pliku lub folderu wejściowego** wykonaj następujące czynności:

   a. Kliknij przycisk **Przeglądaj** , aby przejść do folderu **adftutorial/Input** , wybierz plik **emp.txt** , a następnie kliknij przycisk **Wybierz**. 

   d. Zaznacz pole wyboru **Kopiuj binarnie** , aby skopiować plik jako-is, a następnie wybierz przycisk **dalej**. 

   ![Strona „Wybieranie pliku lub folderu wejściowego”](./media/quickstart-create-data-factory-copy-data-tool/select-binary-copy.png)


1. Na stronie **docelowy magazyn danych** Wybierz utworzoną usługę połączoną **BLOB Storage Azure** , a następnie wybierz pozycję **dalej**. 

1. Na stronie **Wybieranie pliku lub folderu wyjściowego** wprowadź **adftutorial/Output** dla ścieżki folderu, a następnie wybierz przycisk **dalej**. 

   ![Strona „Wybieranie pliku lub folderu wyjściowego”](./media/quickstart-create-data-factory-copy-data-tool/configure-sink-path.png) 

1. Na stronie **Ustawienia** wybierz przycisk **Dalej**, aby użyć konfiguracji domyślnych. 

1. Sprawdź wszystkie ustawienia na stronie **Podsumowanie**, a następnie wybierz przycisk **Dalej**. 

1. Na stronie **Zakończono wdrożenie** wybierz pozycję **Monitoruj**, aby monitorować utworzony potok. 

    ![Strona „Zakończono wdrożenie”](./media/quickstart-create-data-factory-copy-data-tool/deployment-page.png)

1. Aplikacja przełączy się na kartę **monitorowanie** . Na tej karcie zobaczysz stan potoku. Wybierz pozycję **Odśwież** , aby odświeżyć listę. Kliknij link pod **nazwą potoku** , aby wyświetlić szczegóły uruchomienia działania lub ponownie uruchomić potok. 
   
    ![Odśwież potok](./media/quickstart-create-data-factory-copy-data-tool/refresh-pipeline.png)

1. Na stronie uruchomienia działania wybierz link **szczegóły** (ikona okularów) w kolumnie **Nazwa działania** , aby uzyskać więcej informacji na temat operacji kopiowania. Aby uzyskać więcej informacji o właściwościach, zobacz [Omówienie działania kopiowania](copy-activity-overview.md). 

1. Aby wrócić do widoku uruchomień potoków, wybierz link **wszystkie uruchomienia potoków** w menu stron nadrzędnych. Aby odświeżyć widok, wybierz pozycję **Odśwież**. 

1. Sprawdź, czy plik **emp.txt** został utworzony w folderze **output** w kontenerze **adftutorial**. Jeśli folder wyjściowy nie istnieje, Usługa Data Factory automatycznie go utworzy. 

1. Przejdź do karty **Tworzenie** powyżej karty **Monitorowanie** na lewym panelu, aby móc edytować połączone usługi, zestawy danych i potoki. Aby dowiedzieć się więcej o edytowaniu tych elementów w interfejsie użytkownika usługi Data Factory, zobacz [Tworzenie fabryki danych za pomocą witryny Azure Portal](quickstart-create-data-factory-portal.md).

    ![Wybieranie karty autora](./media/quickstart-create-data-factory-copy-data-tool/select-author.png)

## <a name="next-steps"></a>Następne kroki
Potok w tym przykładzie kopiuje dane z jednej lokalizacji do innej lokalizacji w usłudze Azure Blob Storage. Zapoznaj się z [samouczkami](tutorial-copy-data-portal.md), aby dowiedzieć się więcej o korzystaniu z usługi Data Factory w dalszych scenariuszach. 
