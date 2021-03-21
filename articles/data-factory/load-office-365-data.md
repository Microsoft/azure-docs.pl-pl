---
title: Ładowanie danych z pakietu Office 365 przy użyciu Azure Data Factory
description: Użyj Azure Data Factory, aby skopiować dane z pakietu Office 365
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: jingwang
ms.openlocfilehash: 54aa511414695d28e390529af61d484e465f1c19
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101710281"
---
# <a name="load-data-from-office-365-by-using-azure-data-factory"></a>Ładowanie danych z pakietu Office 365 przy użyciu Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule przedstawiono sposób użycia Data Factory _ładowania danych z pakietu Office 365 do usługi Azure Blob Storage_. Możesz wykonać podobne kroki, aby skopiować dane do Azure Data Lake Gen1 lub Gen2. Zapoznaj się z [artykułem łącznika pakietu office 365](connector-office-365.md) w sprawie kopiowania danych z pakietu Office 365 ogólnie.

## <a name="create-a-data-factory"></a>Tworzenie fabryki danych

1. Z menu po lewej stronie wybierz pozycję **Utwórz**  >    >  **Data Factory** analizy zasobów: 
   
   ![Wybór usługi Data Factory w okienku „Nowy”](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. Na stronie **Nowa fabryka danych** podaj wartości pól, które są wyświetlane na poniższej ilustracji:
      
   ![Strona Nowa fabryka danych](./media/load-office-365-data/new-azure-data-factory.png)
 
    * **Nazwa**: wprowadź globalnie unikatową nazwę usługi Azure Data Factory. Jeśli zostanie wyświetlony komunikat o błędzie "Nazwa fabryki danych *LoadFromOffice365Demo* jest niedostępna", wprowadź inną nazwę fabryki danych. Można na przykład _**użyć nazwy namename**_**LoadFromOffice365Demo**. Spróbuj ponownie utworzyć fabrykę danych. Artykuł [Data Factory naming rules (Zasady nazewnictwa fabryki danych)](naming-rules.md) zawiera zasady nazewnictwa artefaktów usługi Data Factory.
    * **Subskrypcja**: wybierz subskrypcję platformy Azure, w której chcesz utworzyć fabrykę danych. 
    * **Grupa zasobów**: wybierz istniejącą grupę zasobów z listy rozwijanej lub wybierz opcję **Utwórz nową** , a następnie wprowadź nazwę grupy zasobów. Informacje na temat grup zasobów znajdują się w artykule [Using resource groups to manage your Azure resources](../azure-resource-manager/management/overview.md) (Używanie grup zasobów do zarządzania zasobami platformy Azure).  
    * **Wersja**: Wybierz pozycję **Wersja 2**.
    * **Lokalizacja**: Wybierz lokalizację fabryki danych. Na liście rozwijanej są wyświetlane tylko obsługiwane lokalizacje. Magazyny danych używane przez fabrykę danych mogą znajdować się w innych lokalizacjach i regionach. Te magazyny danych obejmują Azure Data Lake Store, Azure Storage, Azure SQL Database i tak dalej.

3. Wybierz przycisk **Utwórz**.
4. Po zakończeniu tworzenia przejdź do fabryki danych. Zostanie wyświetlona strona główna **Data Factory** , jak pokazano na poniższej ilustracji:
   
   ![Strona główna fabryki danych](./media/load-office-365-data/data-factory-home-page.png)

5. Wybierz kafelek **Tworzenie i monitorowanie**, aby w osobnej karcie uruchomić aplikację Integracja danych.

## <a name="create-a-pipeline"></a>Tworzenie potoku

1. Na stronie "wprowadzenie" Wybierz pozycję **Utwórz potok**.
 
    ![Tworzenie potoku](./media/load-office-365-data/create-pipeline-entry.png)

2. Na **karcie Ogólne** potoku wprowadź wartość "CopyPipeline" dla **nazwy** potoku.

3. W oknie Narzędzie działania > Przenieś & kategorii Przekształć > przeciągnij i upuść **działanie Copy (kopiowanie** ) z okna narzędzi do powierzchni projektanta potoku. Określ wartość "CopyFromOffice365ToBlob" jako nazwę działania.

### <a name="configure-source"></a>Konfigurowanie źródła

1. Przejdź do **karty źródło**> potoku, kliknij pozycję **+ Nowy** , aby utworzyć źródłowy zestaw danych. 

2. W oknie Nowy zestaw danych wybierz pozycję **Office 365**, a następnie wybierz pozycję **Kontynuuj**.
 
3. Jesteś teraz na karcie Konfiguracja działania kopiowania. Kliknij przycisk **Edytuj** obok zestawu danych pakietu Office 365, aby kontynuować konfigurację danych.

    ![Konfiguracja zestawu danych pakietu Office 365 — ogólne](./media/load-office-365-data/transition-to-edit-dataset.png)
 
4. Zostanie wyświetlona nowa karta dla zestawu danych pakietu Office 365. Na **karcie Ogólne** w dolnej części okno właściwości wprowadź wartość "SourceOffice365Dataset" jako nazwę.
 
5. Przejdź do **karty połączenie** w okno właściwości. Obok pola tekstowego połączona usługa kliknij pozycję **+ Nowy**.

6. W oknie Nowa połączona usługa wprowadź wartość "Office365LinkedService" jako nazwę, wprowadź identyfikator jednostki usługi i klucz jednostki usługi, a następnie Testuj połączenie i wybierz pozycję **Utwórz** , aby wdrożyć połączoną usługę.

    ![Nowa połączona usługa pakietu Office 365](./media/load-office-365-data/new-office-365-linked-service.png)
 
7. Po utworzeniu połączonej usługi wrócisz do ustawień zestawu danych. Obok pozycji **tabela** wybierz strzałkę w dół, aby rozwinąć listę dostępnych zestawów danych pakietu Office 365, a następnie wybierz pozycję "BasicDataSet_v0. Message_v0 "z listy rozwijanej:

    ![Konfiguracja tabeli zestawu danych pakietu Office 365](./media/load-office-365-data/edit-dataset.png)

8. Teraz wróć do karty Źródło **potoku**,  >   aby kontynuować konfigurowanie dodatkowych właściwości wyodrębniania danych pakietu Office 365.  Zakres użytkownika i filtr zakresu użytkownika to opcjonalne predykaty, które można zdefiniować, aby ograniczyć dane, które mają zostać wyodrębnione z pakietu Office 365. Aby skonfigurować te ustawienia, zobacz sekcję [Właściwości zestawu danych pakietu Office 365](./connector-office-365.md#dataset-properties) .

9. Wymagane jest wybranie jednego z filtrów daty i podanie wartości czasu rozpoczęcia i czasu zakończenia.

10. Kliknij kartę **Importuj schemat** , aby zaimportować schemat dla zestawu danych komunikatów.

    ![Konfiguracja schematu zestawu danych pakietu Office 365](./media/load-office-365-data/edit-source-properties.png)

### <a name="configure-sink"></a>Konfigurowanie ujścia

1. Przejdź do **karty ujścia**> potoku, a następnie wybierz pozycję **+ Nowy** , aby utworzyć zestaw danych ujścia.
 
2. W oknie Nowy zestaw danych należy zauważyć, że podczas kopiowania z pakietu Office 365 wybierane są tylko obsługiwane miejsca docelowe. Wybierz pozycję **Azure Blob Storage**, wybierz pozycję format binarny, a następnie wybierz pozycję **Kontynuuj**.  W tym samouczku skopiujesz dane pakietu Office 365 do Blob Storage platformy Azure.

3. Kliknij przycisk **Edytuj** obok zestawu danych BLOB Storage platformy Azure, aby kontynuować konfigurację danych.

4. Na **karcie ogólne** okno właściwości w polu Nazwa wprowadź wartość "OutputBlobDataset".

5. Przejdź do **karty połączenie** w okno właściwości. Kliknij pozycję + Nowy obok pola tekstowego **Połączona usługa**.

6. W oknie Nowa połączona usługa wprowadź wartość "AzureStorageLinkedService" jako nazwę, wybierz pozycję "główna usługa" z listy rozwijanej metody uwierzytelniania, Wypełnij punkt końcowy usługi, dzierżawę, identyfikator jednostki usługi i klucz jednostki usługi, a następnie wybierz pozycję Zapisz, aby wdrożyć połączoną usługę.  Zapoznaj się z [tym](connector-azure-blob-storage.md#service-principal-authentication) artykułem, jak skonfigurować uwierzytelnianie jednostki usługi dla BLOB Storage platformy Azure.

    ![Nowa połączona Usługa obiektu BLOB](./media/load-office-365-data/configure-blob-linked-service.png)


## <a name="validate-the-pipeline"></a>Weryfikowanie potoku

Wybierz na pasku narzędzi pozycję **Weryfikuj**, aby zweryfikować potok.

Możesz również wyświetlić kod JSON skojarzony z potoku, klikając pozycję kod w prawym górnym rogu.

## <a name="publish-the-pipeline"></a>Publikowanie potoku

Na górnym pasku narzędzi wybierz pozycję **Opublikuj wszystko**. Ta akcja powoduje opublikowanie utworzonych jednostek (zestawy danych i potok) w usłudze Data Factory.

![Publikowanie zmian](./media/load-office-365-data/publish-changes.png) 

## <a name="trigger-the-pipeline-manually"></a>Ręczne wyzwalanie potoku

Na pasku narzędzi wybierz pozycję **Dodaj wyzwalacz** , a następnie wybierz pozycję **Wyzwól teraz**. Na stronie Uruchomienie potoku wybierz przycisk **Zakończ**. 

## <a name="monitor-the-pipeline"></a>Monitorowanie potoku

Przejdź do karty **Monitorowanie** po lewej stronie. Widoczne jest uruchomienie potoku, które zostało wyzwolone za pomocą wyzwalacza ręcznego. Możesz użyć linków w kolumnie **Akcje** , aby wyświetlić szczegóły działania i ponownie uruchomić potok.

![Monitorowanie potoku](./media/load-office-365-data/pipeline-status.png) 

Aby wyświetlić uruchomienia działań skojarzone z uruchomieniem potoku, wybierz link **Wyświetl uruchomienia działań** w kolumnie Akcje. W tym przykładzie istnieje tylko jedno działanie, dlatego na liście widnieje tylko jedna pozycja. Aby uzyskać szczegółowe informacje na temat operacji kopiowania, wybierz link **Szczegóły** (ikona okularów) w kolumnie Akcje.

![Monitorowanie aktywności](./media/load-office-365-data/activity-status.png) 

Jeśli żądanie danych dla tego kontekstu jest wykonywane po raz pierwszy (połączenie z tabelą danych jest dostępne, do której należy dane konto docelowe, i które tożsamość użytkownika tworzy żądanie dostępu do danych), zobaczysz stan działania kopiowania jako **w toku** i tylko po kliknięciu linku "Szczegóły" w obszarze Akcje zobaczysz stan jako **RequesetingConsent**.  Członek grupy osoby zatwierdzającej dostęp do danych musi zatwierdzić żądanie w Privileged Access Management zanim będzie możliwe kontynuowanie wyodrębniania danych.

_Stan jako żądanie zgody:_ 
 ![ Szczegóły wykonania działania — zgoda na żądanie](./media/load-office-365-data/activity-details-request-consent.png) 

_Stan jako dane wyodrębniania:_

![Szczegóły wykonania działania — Wyodrębnij dane](./media/load-office-365-data/activity-details-extract-data.png) 

Po podaniu zgody wyodrębnianie danych będzie kontynuowane, a po pewnym czasie zostanie wyświetlony stan pomyślnego uruchomienia potoku.

![Monitorowanie potoku — powodzenie](./media/load-office-365-data/pipeline-monitoring-succeeded.png) 

Teraz przejdź do docelowego Blob Storage platformy Azure i sprawdź, czy dane pakietu Office 365 zostały wyodrębnione w formacie binarnym.

## <a name="next-steps"></a>Następne kroki

Przejdź do poniższego artykułu, aby dowiedzieć się więcej o obsłudze usługi Azure Synapse Analytics: 

> [!div class="nextstepaction"]
>[Łącznik usługi Office 365](connector-office-365.md)