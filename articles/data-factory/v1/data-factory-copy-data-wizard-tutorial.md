---
title: 'Samouczek: tworzenie potoku przy użyciu Kreatora kopiowania '
description: Ten samouczek zawiera instrukcje dotyczące tworzenia potoku usługi Azure Data Factory za pomocą działania kopiowania przy użyciu Kreatora kopiowania obsługiwanego w usłudze Data Factory
author: linda33wj
ms.service: data-factory
ms.topic: tutorial
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 60a575fc211c512c8657bffd567c96f98cc3d69a
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100377010"
---
# <a name="tutorial-create-a-pipeline-with-copy-activity-using-data-factory-copy-wizard"></a>Samouczek: tworzenie potoku za pomocą działania kopiowania przy użyciu Kreatora kopiowania usługi Fabryka danych
> [!div class="op_single_selector"]
> * [Przegląd i wymagania wstępne](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Kreator kopiowania](data-factory-copy-data-wizard-tutorial.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [Program PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Szablon usługi Azure Resource Manager](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [Interfejs API REST](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [Interfejs API .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli korzystasz z bieżącej wersji usługi Data Factory, zobacz [samouczek dotyczący działania kopiowania](../quickstart-create-data-factory-dot-net.md). 


W tym samouczku pokazano, jak za pomocą **Kreatora kopiowania** skopiować dane z usługi Azure Blob storage do Azure SQL Database. 

**Kreator kopiowania** w usłudze Azure Data Factory umożliwia szybkie utworzenie potoku danych, który kopiuje dane z obsługiwanego źródłowego magazynu danych do obsługiwanego docelowego magazynu danych. Dlatego zalecamy użycie kreatora jako pierwszego kroku procesu tworzenia przykładowego potoku dla scenariusza przenoszenia danych. Aby zapoznać się z listą magazynów danych obsługiwanych jako źródła i lokalizacje docelowe, zobacz [obsługiwane magazyny danych](data-factory-data-movement-activities.md#supported-data-stores-and-formats).  

W tym samouczku pokazano, jak utworzyć fabrykę danych Azure Data Factory, uruchomić Kreatora kopiowania oraz wykonać procedurę umożliwiającą podanie szczegółów scenariusza wprowadzania/przenoszenia danych. Po zakończeniu kroków w Kreatorze Kreator automatycznie utworzy potok z działaniem kopiowania, aby skopiować dane z magazynu obiektów blob platformy Azure do Azure SQL Database. Więcej informacji o Działaniu kopiowania znajduje się w temacie [Działania związane z przenoszeniem danych](data-factory-data-movement-activities.md).

## <a name="prerequisites"></a>Wymagania wstępne
Przed wykonaniem instrukcji z tego samouczka wykonaj działania dotyczące wymagań wstępnych podane w artykule [Omówienie samouczka](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

## <a name="create-data-factory"></a>Tworzenie fabryki danych
W tym kroku opisano tworzenie fabryki danych Azure o nazwie **ADFTutorialDataFactory** przy użyciu witryny Azure Portal.

1. Zaloguj się do [Azure Portal](https://portal.azure.com).
2. Kliknij polecenie **Utwórz zasób** w lewym górnym rogu, kliknij pozycję **Dane + analiza**, a następnie **Fabryka danych**. 
   
   ![Nowy->Fabryka danych](./media/data-factory-copy-data-wizard-tutorial/new-data-factory-menu.png)
2. W bloku **Nowa fabryka danych**:
   
   1. Wprowadź wartość **ADFTutorialDataFactory** dla opcji **Nazwa**.
       Nazwa fabryki danych Azure musi być globalnie unikatowa. Jeśli wystąpi błąd: `Data factory name "ADFTutorialDataFactory" is not available`, zmień nazwę fabryki danych (np. twojanazwaADFTutorialDataFactoryRRRRMMDD) i spróbuj utworzyć ją ponownie. Artykuł [Data Factory — Naming Rules](data-factory-naming-rules.md) (Fabryka danych — zasady nazewnictwa) zawiera zasady nazewnictwa artefaktów usługi Fabryka danych.  
      
       ![Nazwa fabryki danych jest niedostępna](./media/data-factory-copy-data-wizard-tutorial/getstarted-data-factory-not-available.png)    
   2. Wybierz swoją **subskrypcję** platformy Azure.
   3. Wykonaj jedną z następujących czynności dotyczącą grupy zasobów: 
      
      - Wybierz pozycję **Użyj istniejącej**, aby wybrać istniejącą grupę zasobów.
      - Wybierz pozycję **Utwórz nowy**, aby wprowadzić nazwę grupy zasobów.
          
        W niektórych krokach w tym samouczku zakłada się, że nazwa grupy zasobów to **ADFTutorialResourceGroup**. Informacje na temat grup zasobów znajdują się w artykule [Using resource groups to manage your Azure resources](../../azure-resource-manager/management/overview.md) (Używanie grup zasobów do zarządzania zasobami platformy Azure).
   4. Wybierz **lokalizację** fabryki danych.
   5. Zaznacz pole wyboru **Przypnij do pulpitu nawigacyjnego** u dołu bloku.  
   6. Kliknij pozycję **Utwórz**.
      
       ![Blok Nowa fabryka danych](media/data-factory-copy-data-wizard-tutorial/new-data-factory-blade.png)            
3. Po zakończeniu tworzenia zostanie wyświetlony blok **Data Factory** , jak pokazano na poniższej ilustracji:
   
   ![Strona główna fabryki danych](./media/data-factory-copy-data-wizard-tutorial/getstarted-data-factory-home-page.png)

## <a name="launch-copy-wizard"></a>Uruchamianie Kreatora kopiowania
1. W bloku Fabryka danych kliknij pozycję **Kopiuj dane**, aby uruchomić **Kreatora kopiowania**. 
   
   > [!NOTE]
   > Jeśli przeglądarka internetowa będzie cały czas wyświetlać komunikat „Autoryzowanie...”, wyłącz ustawienie **Blokuj pliki cookie i dane witryn innych firm** w ustawieniach przeglądarki lub pozostaw je włączone, ale utwórz wyjątek dla witryny **login.microsoftonline.com**, a następnie spróbuj ponownie uruchomić kreatora.
2. Na stronie **Właściwości**:
   
   1. Wprowadź wartość **CopyFromBlobToAzureSql** w polu **Nazwa zadania**
   2. Wprowadź **opis** (opcjonalnie).
   3. Zmień ustawienia **Data/godzina rozpoczęcia** i **Data/godzina zakończenia** tak, aby data zakończenia przypadała w bieżącym dniu, a data rozpoczęcia pięć dni wcześniej.  
   4. Kliknij przycisk **Dalej**.  
      
      ![Narzędzie kopiowania — strona Właściwości](./media/data-factory-copy-data-wizard-tutorial/copy-tool-properties-page.png) 
3. Na stronie **Magazyn danych źródłowych** kliknij kafelek **Azure Blob Storage**. Ta strona służy do określania magazynu danych źródłowych do zadania kopiowania. 
   
    ![Narzędzie kopiowania — strona magazynu danych źródłowych](./media/data-factory-copy-data-wizard-tutorial/copy-tool-source-data-store-page.png)
4. Na stronie **Określanie konta usługi Azure Blob Storage**:
   
   1. Wprowadź wartość **AzureStorageLinkedService** w polu **Nazwa połączonej usługi**.
   2. Upewnij się, że wybrano opcję **Z subskrypcji Azure** dla ustawienia **Metoda wyboru konta**.
   3. Wybierz swoją **subskrypcję** platformy Azure.  
   4. Wybierz **konto usługi Azure Storage** z listy kont usługi Azure Storage dostępnych w ramach wybranej subskrypcji. Można również wprowadzić ustawienia konta magazynu ręcznie, wybierając opcję **Wprowadź ręcznie** dla ustawienia **Metoda wyboru konta**, a następnie klikając przycisk **Dalej**. 
      
      ![Narzędzie kopiowania — określanie konta usługi Azure Blob Storage](./media/data-factory-copy-data-wizard-tutorial/copy-tool-specify-azure-blob-storage-account.png)
5. Na stronie **Wybieranie pliku lub folderu wejściowego**:
   
   1. Kliknij dwukrotnie pozycję **adftutorial** (folder).
   2. Zaznacz plik **emp.txt** i kliknij przycisk **Wybierz**.
      
      ![Zrzut ekranu przedstawia opcję Wybierz dla pliku wejściowego.](./media/data-factory-copy-data-wizard-tutorial/copy-tool-choose-input-file-or-folder.png)
6. Na stronie **Wybieranie pliku lub folderu wejściowego** kliknij przycisk **Dalej**. Nie wybieraj opcji **Kopia binarna**. 
   
    ![Zrzut ekranu przedstawia opcję kopiowania binarnego dla danych wejściowych.](./media/data-factory-copy-data-wizard-tutorial/chose-input-file-folder.png) 
7. Na stronie **Ustawienia formatu pliku** są wyświetlane ograniczniki i schemat wykrywany automatycznie przez kreatora w ramach analizy pliku. Możesz też ręcznie wprowadzić ograniczniki kreatora kopiowania, aby zatrzymać automatyczne wykrywanie lub przesłonić ustawienia. Po przejrzeniu ograniczników i wyświetleniu podglądu danych kliknij przycisk **Dalej**. 
   
    ![Narzędzie kopiowania — ustawienia formatu pliku](./media/data-factory-copy-data-wizard-tutorial/copy-tool-file-format-settings.png)  
8. Na stronie docelowego magazynu danych wybierz pozycję **Azure SQL Database** i kliknij przycisk **Dalej**.
   
    ![Narzędzie kopiowania — wybieranie magazynu docelowego](./media/data-factory-copy-data-wizard-tutorial/choose-destination-store.png)
9. Na stronie **Określanie bazy danych Azure SQL Database**:
   
   1. Wprowadź wartość **AzureSqlLinkedService** w polu **Nazwa połączenia**.
   2. Upewnij się, że wybrano opcję **Z subskrypcji Azure** dla ustawienia **Metoda wyboru serwera/bazy danych**.
   3. Wybierz swoją **subskrypcję** platformy Azure.  
   4. Wybierz opcje **Nazwa serwera** i **Baza danych**.
   5. Wypełnij pola **Nazwa użytkownika** i **Hasło**.
   6. Kliknij przycisk **Dalej**.  
      
      ![Narzędzie kopiowania — Określanie Azure SQL Database](./media/data-factory-copy-data-wizard-tutorial/specify-azure-sql-database.png)
10. Na stronie **Mapowanie tabeli** wybierz pozycję **emp** z listy rozwijanej w polu **Miejsce docelowe**, kliknij **strzałkę w dół** (opcjonalnie), aby wyświetlić schemat i podgląd danych.
    
     ![Narzędzie kopiowania — mapowanie tabeli](./media/data-factory-copy-data-wizard-tutorial/copy-tool-table-mapping-page.png) 
11. Na stronie **Mapowanie schematu** kliknij przycisk **Dalej**.
    
    ![Narzędzie kopiowania — mapowanie schematu](./media/data-factory-copy-data-wizard-tutorial/schema-mapping-page.png)
12. Na stronie **Ustawienia wydajności** kliknij przycisk **Dalej**. 
    
    ![Zrzut ekranu przedstawia stronę Ustawienia wydajności, na której można wybrać przycisk Dalej.](./media/data-factory-copy-data-wizard-tutorial/performance-settings.png)
13. Przejrzyj informacje na stronie **Podsumowanie** i kliknij przycisk **Zakończ**. Kreator utworzy dwie połączone usługi, dwa zestawy danych (wejściowy i wyjściowy) i jeden potok w fabryce danych (z której został uruchomiony Kreator kopiowania). 
    
    ![Zrzut ekranu przedstawia stronę Podsumowanie, na której można wybrać pozycję Dalej.](./media/data-factory-copy-data-wizard-tutorial/summary-page.png)

## <a name="launch-monitor-and-manage-application"></a>Uruchamianie aplikacji do monitorowania i zarządzania
1. Na stronie **Wdrożenie** kliknij link: `Click here to monitor copy pipeline`.
   
   ![Narzędzie kopiowania — wdrażanie zakończyło się pomyślnie](./media/data-factory-copy-data-wizard-tutorial/copy-tool-deployment-succeeded.png)  
2. Aplikacja do monitorowania jest uruchamiana w osobnej karcie w przeglądarce sieci Web.   
   
   ![Aplikacja do monitorowania](./media/data-factory-copy-data-wizard-tutorial/monitoring-app.png)   
3. Aby wyświetlić najnowszy stan wycinków godzinowych, kliknij przycisk **Odśwież** na liście **OKNA DZIAŁANIA** na dole. Zostanie wyświetlonych pięć okien działania dla pięciu dni między czasem rozpoczęcia a czasem zakończenia dla potoku. Lista nie jest automatycznie odświeżana, więc konieczne może okazać się kilkukrotne kliknięcie przycisku Odśwież, zanim zostaną wyświetlone wszystkie okna działania w stanie Gotowe. 
4. Wybierz okno działania z listy. Zobacz szczegółowe informacje jego dotyczące w **Eksploratorze okna działania** po prawej stronie.

    ![Szczegóły okna działania](media/data-factory-copy-data-wizard-tutorial/activity-window-details.png)    

    Należy zwrócić uwagę na to, że daty 11, 12, 13, 14 oraz 15 mają kolor zielony, co oznacza, że dzienne wycinki wyjściowe dla tych dat zostały już utworzone. To kodowanie kolorami jest również widoczne w potoku oraz wyjściowym zestawie danych w widoku diagramu. W poprzednim kroku zwróć uwagę na to, że dwa wycinki zostały już utworzone, jeden wycinek jest obecnie przetwarzany, a dwa inne oczekują na przetworzenie (w oparciu o kodowanie kolorami). 

    Aby uzyskać więcej informacji na temat używania tej aplikacji, zobacz artykuł [Monitor and manage pipeline using Monitoring App](data-factory-monitor-manage-app.md) (Monitorowanie potoku i zarządzanie nim przy użyciu aplikacji do monitorowania).

## <a name="next-steps"></a>Następne kroki
W tym samouczku użyto magazynu obiektów blob platformy Azure jako źródłowego magazynu danych i Azure SQL Database jako docelowy magazyn danych w operacji kopiowania. Poniższa tabela zawiera listę magazynów danych obsługiwanych przez działanie kopiowania jako źródła i lokalizacje docelowe: 

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

Aby uzyskać szczegółowe informacje dotyczące pól/właściwości wyświetlanych w kreatorze kopiowania dla magazynu danych, kliknij link dla magazynu danych w tabeli. 