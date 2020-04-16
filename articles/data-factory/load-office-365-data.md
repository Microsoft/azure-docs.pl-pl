---
title: Ładowanie danych z usługi Office 365 przy użyciu usługi Azure Data Factory
description: Kopiowanie danych z usługi Office 365 za pomocą usługi Azure Data Factory
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/22/2018
ms.author: jingwang
ms.openlocfilehash: 3422176ed89b7f575c11cc40e5be8420da0018b0
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415811"
---
# <a name="load-data-from-office-365-by-using-azure-data-factory"></a>Ładowanie danych z usługi Office 365 przy użyciu usługi Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

W tym artykule pokazano, jak używać danych ładowania danych z _usługi Office 365 do magazynu obiektów Blob platformy Azure._ Możesz wykonać podobne kroki, aby skopiować dane do usługi Azure Data Lake Gen1 lub Gen2. Zapoznaj się z [artykułem łącznika usługi Office 365](connector-office-365.md) na temat kopiowania danych z usługi Office 365 w ogóle.

## <a name="create-a-data-factory"></a>Tworzenie fabryki danych

1. W menu po lewej stronie wybierz pozycję **Utwórz fabrykę** > **danych****analizy** > zasobów: 
   
   ![Wybór usługi Data Factory w okienku „Nowy”](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. Na stronie **Nowa fabryka danych** podaj wartości pól wyświetlanych na poniższym obrazie:
      
   ![Strona Nowa fabryka danych](./media/load-office-365-data/new-azure-data-factory.png)
 
    * **Nazwa:** Wprowadź globalnie unikatową nazwę dla fabryki danych platformy Azure. Jeśli zostanie wyświetlony błąd "Nazwa fabryki danych *LoadFromOffice365Demo* nie jest dostępna", wprowadź inną nazwę dla fabryki danych. Na przykład można użyć nazwy _**yourname**_**LoadFromOffice365Demo**. Spróbuj ponownie utworzyć fabrykę danych. Artykuł [Data Factory naming rules (Zasady nazewnictwa fabryki danych)](naming-rules.md) zawiera zasady nazewnictwa artefaktów usługi Data Factory.
    * **Subskrypcja**: Wybierz subskrypcję platformy Azure, w której chcesz utworzyć fabrykę danych. 
    * **Grupa zasobów:** Wybierz istniejącą grupę zasobów z listy rozwijanej lub wybierz opcję **Utwórz nową** i wprowadź nazwę grupy zasobów. Informacje na temat grup zasobów znajdują się w artykule [Using resource groups to manage your Azure resources](../azure-resource-manager/management/overview.md) (Używanie grup zasobów do zarządzania zasobami platformy Azure).  
    * **Wersja**: Wybierz **V2**.
    * **Lokalizacja**: Wybierz lokalizację fabryki danych. Na liście rozwijanej są wyświetlane tylko obsługiwane lokalizacje. Magazyny danych, które są używane przez fabrykę danych może znajdować się w innych lokalizacjach i regionach. Te magazyny danych obejmują usługi Azure Data Lake Store, Usługa Azure Storage, usługa Azure SQL Database i tak dalej.

3. Wybierz pozycję **Utwórz**.
4. Po zakończeniu tworzenia przejdź do fabryki danych. Zostanie wyświetlona strona główna **usługi Data Factory,** jak pokazano na poniższej ilustracji:
   
   ![Strona główna fabryki danych](./media/load-office-365-data/data-factory-home-page.png)

5. Wybierz kafelek **Tworzenie i monitorowanie**, aby w osobnej karcie uruchomić aplikację Integracja danych.

## <a name="create-a-pipeline"></a>Tworzenie potoku

1. Na stronie "Zacznijmy" wybierz pozycję **Utwórz potok**.
 
    ![Tworzenie potoku](./media/load-office-365-data/create-pipeline-entry.png)

2. Na **karcie Ogólne** dla potoku wprowadź "CopyPipeline" dla **nazwy** potoku.

3. W polu narzędziowym Działania > przenieś & przekształć > przeciągnij i upuść **działanie Kopiowanie** z pola narzędziowego na powierzchnię projektanta potoku. Określ "CopyFromOffice365ToBlob" jako nazwę działania.

### <a name="configure-source"></a>Konfigurowanie źródła

1. Przejdź do karty > **źródło**potoku kliknij przycisk **+ Nowy,** aby utworzyć źródłowy zestaw danych. 

2. W oknie Nowy zestaw danych wybierz pozycję **Office 365**, a następnie wybierz pozycję **Kontynuuj**.
 
3. Jesteś teraz na karcie konfiguracji działania kopiowania. **Edit**

    ![Konfiguracja zestawu danych usługi Office 365 ogólne](./media/load-office-365-data/transition-to-edit-dataset.png)
 
4. Zostanie wyświetlena nowa karta otwarta dla zestawu danych usługi Office 365. Na **karcie Ogólne** u dołu okna Właściwości wprowadź pozycję "SourceOffice365Dataset" dla name.
 
5. Przejdź do **karty Połączenie** w oknie Właściwości. Obok pola tekstowego Połączone usługi kliknij pozycję **+ Nowy**.

6. W oknie Nowa usługa połączona wprowadź "Office365LinkedService" jako nazwę, wprowadź identyfikator jednostki usługi i klucz główny usługi, a następnie przetestuj połączenie i wybierz pozycję **Utwórz,** aby wdrożyć usługę połączeniową.

    ![Nowa usługa połączona z usługą Office 365](./media/load-office-365-data/new-office-365-linked-service.png)
 
7. Po utworzeniu połączonej usługi wrócisz do ustawień zestawu danych. Obok **pozycji Tabela**wybierz strzałkę w dół, aby rozwinąć listę dostępnych zestawów danych usługi Office 365, a następnie wybierz opcję "BasicDataSet_v0. Message_v0" z listy rozwijanej:

    ![Tabela zestawów danych usługi Config Office 365](./media/load-office-365-data/edit-dataset.png)

8. Teraz wróć do**karty Źródło** **potoku,** > aby kontynuować konfigurowanie dodatkowych właściwości wyodrębniania danych usługi Office 365.  Filtr zakresu użytkownika i zakresu użytkownika to opcjonalne predykaty, które można zdefiniować w celu ograniczenia danych, które chcesz wyodrębnić z usługi Office 365. Zobacz [sekcję Właściwości zestawu danych usługi Office 365,](https://docs.microsoft.com/azure/data-factory/connector-office-365#dataset-properties) aby dowiedzieć się, jak skonfigurować te ustawienia.

9. Musisz wybrać jeden z filtrów daty i podać wartości godziny rozpoczęcia i czasu zakończenia.

10. Kliknij kartę **Importowanie schematu,** aby zaimportować schemat zestawu danych wiadomości.

    ![Konfiguracja schematu zestawu danych usługi Office 365](./media/load-office-365-data/edit-source-properties.png)

### <a name="configure-sink"></a>Konfigurowanie ujścia

1. Przejdź do karty > **ujścia**potoku i wybierz + **Nowy,** aby utworzyć zestaw danych ujścia.
 
2. W oknie Nowy zestaw danych zwróć uwagę, że podczas kopiowania z usługi Office 365 są wybierane tylko obsługiwane miejsca docelowe. Wybierz **pozycję Usługa Azure Blob Storage**, wybierz format binarny, a następnie wybierz pozycję **Kontynuuj**.  W tym samouczku można skopiować dane usługi Office 365 do usługi Azure Blob Storage.

3. Kliknij przycisk **Edytuj** obok zestawu danych usługi Azure Blob Storage, aby kontynuować konfigurację danych.

4. Na **karcie Ogólne** okna Właściwości w oknie Nazwa wprowadź "OutputBlobDataset".

5. Przejdź do **karty Połączenie** w oknie Właściwości. Kliknij pozycję + Nowy obok pola tekstowego **Połączona usługa**.

6. W oknie Nowa usługa połączona wprowadź "AzureStorageLinkedService" jako nazwę, wybierz "Service Principal" z listy rozwijanej metod uwierzytelniania, wypełnij punkt końcowy usługi, dzierżawę, identyfikator jednostki usługi i klucz główny usługi, a następnie wybierz pozycję Zapisz, aby wdrożyć usługę połączone.  Zapoznaj się [tutaj,](connector-azure-blob-storage.md#service-principal-authentication) aby dowiedzieć się, jak skonfigurować uwierzytelnianie jednostkowe usługi dla usługi Azure Blob Storage.

    ![Nowa usługa połączona obiektu Blob](./media/load-office-365-data/configure-blob-linked-service.png)


## <a name="validate-the-pipeline"></a>Weryfikowanie potoku

Wybierz na pasku narzędzi pozycję **Weryfikuj**, aby zweryfikować potok.

Można również zobaczyć kod JSON skojarzone z potoku, klikając kod w prawym górnym rogu.

## <a name="publish-the-pipeline"></a>Publikowanie potoku

Na górnym pasku narzędzi wybierz pozycję **Opublikuj wszystko**. Ta akcja powoduje opublikowanie utworzonych jednostek (zestawy danych i potok) w usłudze Data Factory.

![Publikowanie zmian](./media/load-office-365-data/publish-changes.png) 

## <a name="trigger-the-pipeline-manually"></a>Ręczne wyzwalanie potoku

Wybierz **pozycję Dodaj wyzwalacz** na pasku narzędzi, a następnie wybierz pozycję **Wyzwalaj teraz**. Na stronie Uruchomienie potoku wybierz przycisk **Zakończ**. 

## <a name="monitor-the-pipeline"></a>Monitorowanie potoku

Przejdź do karty **Monitorowanie** po lewej stronie. Widoczne jest uruchomienie potoku, które zostało wyzwolone za pomocą wyzwalacza ręcznego. Łącza w kolumnie **Akcje** umożliwiają wyświetlanie szczegółów działania i ponowne uruchomienie potoku.

![Monitorowanie potoku](./media/load-office-365-data/pipeline-status.png) 

Aby wyświetlić uruchomienia działań skojarzone z uruchomieniem potoku, wybierz link **Wyświetl uruchomienia działań** w kolumnie Akcje. W tym przykładzie istnieje tylko jedno działanie, dlatego na liście widnieje tylko jedna pozycja. Aby uzyskać szczegółowe informacje na temat operacji kopiowania, wybierz link **Szczegóły** (ikona okularów) w kolumnie Akcje.

![Monitorowanie aktywności](./media/load-office-365-data/activity-status.png) 

Jeśli jest to pierwszy raz, gdy żądasz danych w tym kontekście (kombinacja, do której tabeli danych jest dostęp, które konto docelowe jest ładowane do danych i która tożsamość użytkownika sprawia, że żądanie dostępu do danych), zobaczysz stan działania kopiowania jako **W toku,** a dopiero po kliknięciu łącza "Szczegóły" w obszarze Akcje zobaczysz stan **jako RequesetingConsent**.  Członek grupy zatwierdzającej dostępu do danych musi zatwierdzić żądanie w zarządzania dostępem uprzywilejowanym, zanim będzie można kontynuować wyodrębnianie danych.

_Status jako żądanie zgody:_
![Szczegóły wykonania działania - zgoda żądania](./media/load-office-365-data/activity-details-request-consent.png) 

_Stan jako wyodrębnianie danych:_

![Szczegóły wykonania działania — wyodrębnianie danych](./media/load-office-365-data/activity-details-extract-data.png) 

Po udzieleniu zgody wyodrębnianie danych będzie kontynuowane, a po pewnym czasie uruchomienie potoku zostanie wyświetlone jako pomyślne.

![Monitoruj potok - powiodło się](./media/load-office-365-data/pipeline-monitoring-succeeded.png) 

Teraz przejdź do docelowego usługi Azure Blob Storage i sprawdź, czy dane usługi Office 365 zostały wyodrębnione w formacie binarnym.

## <a name="next-steps"></a>Następne kroki

Przejdź do następującego artykułu, aby dowiedzieć się więcej o pomocy technicznej usługi Azure SQL Data Warehouse: 

> [!div class="nextstepaction"]
>[Łącznik usługi Office 365](connector-office-365.md)