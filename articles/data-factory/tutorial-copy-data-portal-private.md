---
title: Używanie prywatnych punktów końcowych do tworzenia potoku Azure Data Factory
description: Ten samouczek zawiera instrukcje krok po kroku dotyczące tworzenia fabryki danych z potokiem za pomocą witryny Azure Portal. Potok używa działania kopiowania do kopiowania danych z usługi Azure Blob Storage do bazy danych Azure SQL Database.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 05/15/2020
ms.author: jingwang
ms.openlocfilehash: c08dd1b5b2f90e874f36c6cf01c4cc5f5ae74d17
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92636259"
---
# <a name="copy-data-securely-from-azure-blob-storage-to-a-sql-database-by-using-private-endpoints"></a>Bezpieczne kopiowanie danych z usługi Azure Blob Storage do bazy danych SQL za pomocą prywatnych punktów końcowych

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

W tym samouczku utworzysz fabrykę danych przy użyciu interfejsu użytkownika usługi Azure Data Factory. *Potok w tej fabryce danych kopiuje dane bezpiecznie z usługi Azure Blob Storage do usługi Azure SQL Database (jednocześnie zezwalającej na dostęp tylko do wybranych sieci) za pomocą prywatnych punktów końcowych w [Azure Data Factory zarządzanych Virtual Network](managed-virtual-network-private-endpoint.md).* Wzorzec konfiguracji w tym samouczku ma zastosowanie do kopiowania danych z magazynu opartego na plikach do relacyjnego magazynu danych. Listę magazynów danych obsługiwanych jako źródła i ujścia można znaleźć w tabeli [obsługiwane magazyny i formaty danych](./copy-activity-overview.md) .

> [!NOTE]
> Jeśli jesteś nowym użytkownikiem usługi Data Factory, zobacz [Wprowadzenie do usługi Azure Data Factory](./introduction.md).

W tym samouczku wykonasz następujące czynności:

* Tworzenie fabryki danych.
* Tworzenie potoku z działaniem kopiowania.


## <a name="prerequisites"></a>Wymagania wstępne
* **Subskrypcja platformy Azure** . Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/) .
* **Konto usługi Azure Storage** . Magazyn obiektów blob jest używany jako magazyn danych będący *źródłem* . Jeśli nie masz konta magazynu, utwórz je, wykonując czynności przedstawione w artykule [Tworzenie konta magazynu platformy Azure](../storage/common/storage-account-create.md?tabs=azure-portal). *Upewnij się, że konto magazynu zezwala na dostęp tylko z wybranych sieci.* 
* **Azure SQL Database** . Baza danych jest używana jako magazyn danych będący *ujściem* . Jeśli nie masz bazy danych SQL Azure, zobacz [Tworzenie bazy danych SQL](../azure-sql/database/single-database-create-quickstart.md) w celu wykonania czynności. *Upewnij się, że konto SQL Database zezwala na dostęp tylko z wybranych sieci.* 

### <a name="create-a-blob-and-a-sql-table"></a>Tworzenie obiektu blob i tabeli SQL

Teraz możesz przygotować magazyn obiektów blob i bazę danych SQL na potrzeby samouczka, wykonując następujące kroki.

#### <a name="create-a-source-blob"></a>Tworzenie źródłowego obiektu Blob

1. Otwórz Notatnik. Skopiuj poniższy tekst i zapisz go na dysku jako plik **emp.txt** :

    ```
    FirstName,LastName
    John,Doe
    Jane,Doe
    ```

1. Utwórz kontener o nazwie **adftutorial** w magazynie obiektów BLOB. W tym kontenerze utwórz folder o nazwie **input** . Następnie przekaż plik **emp.txt** do folderu **input** . Do wykonania tych zadań użyj witryny Azure Portal lub narzędzi takich jak [Eksplorator usługi Azure Storage](https://storageexplorer.com/).

#### <a name="create-a-sink-sql-table"></a>Tworzenie tabeli SQL ujścia

Utwórz tabelę **dbo.emp** w bazie danych SQL przy użyciu poniższego skryptu SQL:

    ```sql
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50)
    )
    GO

    CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);
    ```

## <a name="create-a-data-factory"></a>Tworzenie fabryki danych
W tym kroku utworzysz fabrykę danych i uruchomisz interfejs użytkownika usługi Data Factory, aby utworzyć potok w fabryce danych.

1. Otwórz przeglądarkę Microsoft Edge lub Google Chrome. Obecnie tylko przeglądarki sieci Web Microsoft Edge i Google Chrome obsługują interfejs użytkownika Data Factory.

1. W menu po lewej stronie wybierz pozycję **Utwórz zasób**  >  **Analytics**  >  **Data Factory** .

1. Na stronie **Nowa fabryka danych** w polu **Nazwa** wprowadź wartość **ADFTutorialDataFactory** .

   Nazwa fabryki danych Azure musi być *globalnie unikatowa* . Jeśli zostanie wyświetlony komunikat o błędzie dotyczący wartości nazwy, wprowadź inną nazwę fabryki danych (na przykład Twojanazwaadftutorialdatafactory). Reguły nazewnictwa dla artefaktów usługi Data Factory można znaleźć w artykule [Data Factory — reguły nazewnictwa](./naming-rules.md).

1. Wybierz **subskrypcję** platformy Azure, w której chcesz utworzyć fabrykę danych.

1. W obszarze **Grupa zasobów** wykonaj jedną z następujących czynności:

    - Wybierz pozycję **Użyj istniejącej** , a następnie wybierz istniejącą grupę zasobów z listy rozwijanej.
    - Wybierz pozycję **Utwórz nową** , a następnie wprowadź nazwę grupy zasobów. 
     
    Informacje na temat grup zasobów znajdują się w artykule [Using resource groups to manage your Azure resources (Używanie grup zasobów do zarządzania zasobami platformy Azure)](../azure-resource-manager/management/overview.md). 

1. W obszarze **Wersja** wybierz pozycję **V2** .

1. W obszarze **Lokalizacja** wybierz lokalizację fabryki danych. Na liście rozwijanej są wyświetlane tylko obsługiwane lokalizacje. Magazyny danych (np. usługi Azure Storage i SQL Database) oraz jednostki obliczeniowe (np. usługa Azure HDInsight) używane przez fabrykę danych mogą znajdować się w innych regionach.

1. Wybierz pozycję **Utwórz** .

1. Po zakończeniu tworzenia zobaczysz powiadomienie w centrum powiadomień. Wybierz pozycję **Przejdź do zasobu** , aby przejść do strony **Data Factory** .

1. Wybierz pozycję **Tworzenie i monitorowanie** , aby uruchomić interfejs użytkownika usługi Data Factory na osobnej karcie.

## <a name="create-an-azure-integration-runtime-in-data-factory-managed-virtual-network"></a>Tworzenie środowiska Azure Integration Runtime w programie Data Factory Managed Virtual Network
W tym kroku utworzysz środowisko Azure Integration Runtime i włączysz Data Factory zarządzanym Virtual Network.

1. W portalu Data Factory przejdź do pozycji **Zarządzaj** i wybierz pozycję **Nowy** , aby utworzyć nowe środowisko Azure Integration Runtime.

   ![Zrzut ekranu przedstawiający tworzenie nowego środowiska Azure Integration Runtime.](./media/tutorial-copy-data-portal-private/create-new-azure-ir.png)
1. Wybierz utworzenie środowiska **Azure** Integration Runtime.

   ![Zrzut ekranu pokazujący nowe środowisko Azure Integration Runtime.](./media/tutorial-copy-data-portal-private/azure-ir.png)
1. W obszarze **Konfiguracja sieci wirtualnej (wersja zapoznawcza)** wybierz pozycję **Włącz** .

   ![Zrzut ekranu pokazujący włączenie nowego środowiska Azure Integration Runtime.](./media/tutorial-copy-data-portal-private/enable-managed-vnet.png)
1. Wybierz pozycję **Utwórz** .

## <a name="create-a-pipeline"></a>Tworzenie potoku
W tym kroku utworzysz potok z działaniem kopiowania w fabryce danych. Działanie kopiowania kopiuje dane z magazynu obiektów blob do usługi SQL Database. W [samouczku szybkiego startu](./quickstart-create-data-factory-portal.md) utworzono potok, wykonując następujące czynności:

1. Utworzenie połączonej usługi.
1. Utworzenie wejściowych i wyjściowych zestawów danych.
1. Tworzenie potoku.

W tym samouczku Zacznij od utworzenia potoku. Następnie utworzysz usługi połączone i zestawy danych, gdy będą potrzebne do skonfigurowania potoku.

1. Na stronie **Zaczynajmy** wybierz pozycję **Utwórz potok** .

   ![Zrzut ekranu przedstawiający tworzenie potoku.](./media/doc-common-process/get-started-page.png)
1. W okienku właściwości potoku wpisz **CopyPipeline** dla nazwy potoku.

1. W oknie Narzędzie **działania** rozwiń kategorię **przenoszenie i przekształcanie** , a następnie przeciągnij działanie **Kopiuj dane** z okna narzędzia do powierzchni projektanta potoku. Wprowadź **wartość copyfromblobtosql** dla nazwy.

    ![Zrzut ekranu pokazujący działanie kopiowania.](./media/tutorial-copy-data-portal-private/drag-drop-copy-activity.png)

### <a name="configure-a-source"></a>Konfigurowanie źródła

>[!TIP]
>W tym samouczku użyto **klucza konta** jako typu uwierzytelniania dla źródłowego magazynu danych. W razie konieczności można także wybrać inne obsługiwane metody uwierzytelniania, takie jak **Identyfikator URI SAS** , nazwa **główna usługi** i **tożsamość zarządzana** . Aby uzyskać więcej informacji, zobacz odpowiednie sekcje w temacie [kopiowanie i Przekształcanie danych w usłudze Azure Blob Storage za pomocą Azure Data Factory](./connector-azure-blob-storage.md#linked-service-properties).
>
>Aby bezpiecznie przechowywać wpisy tajne dla magazynów danych, zalecamy również korzystanie z Azure Key Vault. Aby uzyskać więcej informacji i ilustracje, zobacz temat [przechowywanie poświadczeń w Azure Key Vault](./store-credentials-in-key-vault.md).

#### <a name="create-a-source-dataset-and-linked-service"></a>Tworzenie zestawu danych źródłowych i połączonej usługi

1. Przejdź do karty **Źródło** . Wybierz pozycję **+ Nowy** , aby utworzyć źródłowy zestaw danych.

1. W oknie dialogowym **Nowy zestaw danych** wybierz pozycję **Azure Blob Storage** , a następnie wybierz pozycję **Kontynuuj** . Dane źródłowe znajdują się w magazynie obiektów blob, musisz więc wybrać usługę **Azure Blob Storage** dla źródłowego zestawu danych.

1. W oknie dialogowym **Wybieranie formatu** wybierz typ formatu danych, a następnie wybierz pozycję **Kontynuuj** .

1. W oknie dialogowym **Ustawianie właściwości** wpisz **SourceBlobDataset** w polu **Nazwa** . Zaznacz pole wyboru dla **pierwszego wiersza jako nagłówek** . W polu tekstowym **połączona usługa** wybierz pozycję **+ Nowy** .

1. W oknie dialogowym **Nowa połączona usługa (Azure Blob Storage)** wprowadź **AzureStorageLinkedService** jako **nazwę** , a następnie wybierz konto magazynu z listy **nazwa konta magazynu** . 

1. Upewnij się, że włączono funkcję **interaktywnego tworzenia** . Włączenie może potrwać około jednej minuty.

    ![Zrzut ekranu pokazujący interaktywną autorstwo.](./media/tutorial-copy-data-portal-private/interactive-authoring.png)

1. Wybierz pozycję **Testuj połączenie** . W przypadku, gdy konto magazynu zezwala na dostęp tylko z **wybranych sieci** i wymaga Data Factory do utworzenia prywatnego punktu końcowego, który powinien zostać zatwierdzony przed użyciem. W komunikacie o błędzie powinien zostać wyświetlony link umożliwiający utworzenie prywatnego punktu końcowego, który można wykonać, aby utworzyć zarządzany prywatny punkt końcowy. Alternatywnie przejdź bezpośrednio do karty **Zarządzanie** i postępuj zgodnie z instrukcjami w [następnej sekcji](#create-a-managed-private-endpoint) , aby utworzyć zarządzany prywatny punkt końcowy.

   > [!NOTE]
   > Karta **Zarządzanie** może być niedostępna dla wszystkich wystąpień usługi Fabryka danych. Jeśli nie widzisz go, możesz uzyskać dostęp do prywatnych punktów końcowych, wybierając pozycję **Tworzenie**  >  **połączeń**  >  **prywatny punkt końcowy** .
1. Pozostaw otwarte okno dialogowe, a następnie przejdź do konta magazynu.

1. Postępuj zgodnie z instrukcjami w [tej sekcji](#approval-of-a-private-link-in-a-storage-account) , aby zatwierdzić link prywatny.

1. Wróć do okna dialogowego. Wybierz ponownie **Test connection** i wybierz pozycję **Utwórz** , aby wdrożyć połączoną usługę.

1. Po utworzeniu połączonej usługi powraca do strony **Ustawianie właściwości** . Wybierz przycisk **Przeglądaj** obok pozycji **Ścieżka pliku** .

1. Przejdź do folderu **adftutorial/Input** , wybierz plik **emp.txt** , a następnie wybierz przycisk **OK** .

1. Wybierz przycisk **OK** . Automatycznie przechodzi do strony potoku. Na karcie **Źródło** upewnij się, że wybrano opcję **SourceBlobDataset** . Aby wyświetlić podgląd danych na tej stronie, wybierz pozycję **Podgląd danych** .

    ![Zrzut ekranu pokazujący źródłowy zestaw danych.](./media/tutorial-copy-data-portal-private/source-dataset-selected.png)

#### <a name="create-a-managed-private-endpoint"></a>Tworzenie zarządzanego prywatnego punktu końcowego

Jeśli nie wybrano hiperlinku podczas przetestowania połączenia, postępuj zgodnie ze ścieżką. Teraz musisz utworzyć zarządzany prywatny punkt końcowy, który zostanie połączony z utworzoną połączoną usługą.

1. Przejdź na kartę **Zarządzanie** .

   > [!NOTE]
   > Karta **Zarządzanie** może być niedostępna dla wszystkich wystąpień Data Factory. Jeśli nie widzisz go, możesz uzyskać dostęp do prywatnych punktów końcowych, wybierając pozycję **Tworzenie**  >  **połączeń**  >  **prywatny punkt końcowy** .

1. Przejdź do sekcji **zarządzane prywatne punkty końcowe** .

1. Wybierz pozycję **+ Nowy** w obszarze **zarządzane prywatne punkty końcowe** .

    ![Zrzut ekranu pokazujący zarządzany przycisk nowy prywatny punkt końcowy.](./media/tutorial-copy-data-portal-private/new-managed-private-endpoint.png) 

1. Wybierz kafelek **BLOB Storage platformy Azure** z listy, a następnie wybierz pozycję **Kontynuuj** .

1. Wprowadź nazwę utworzonego konta magazynu.

1. Wybierz pozycję **Utwórz** .

1. Po kilku sekundach powinna zostać wyświetlona, że utworzone łącze prywatne wymaga zatwierdzenia.

1. Wybierz utworzony prywatny punkt końcowy. Można wyświetlić hiperłącze, które umożliwi zatwierdzenie prywatnego punktu końcowego na poziomie konta magazynu.

    ![Zrzut ekranu przedstawiający okienko zarządzany prywatny punkt końcowy.](./media/tutorial-copy-data-portal-private/manage-private-endpoint.png) 

#### <a name="approval-of-a-private-link-in-a-storage-account"></a>Zatwierdzanie prywatnego linku na koncie magazynu
1. Na koncie magazynu przejdź do pozycji **prywatne połączenia punktów końcowych** w sekcji **Ustawienia** .

1. Zaznacz pole wyboru dla utworzonego prywatnego punktu końcowego, a następnie wybierz pozycję **Zatwierdź** .

    ![Zrzut ekranu przedstawiający przycisk Zatwierdź dla prywatnego punktu końcowego.](./media/tutorial-copy-data-portal-private/approve-private-endpoint.png)

1. Dodaj opis i wybierz opcję **tak** .
1. Wróć do sekcji **zarządzane prywatne punkty końcowe** na karcie **Zarządzanie** w Data Factory.
1. Po około jednej lub dwóch minut powinna zostać wyświetlona informacja o zatwierdzeniu prywatnego punktu końcowego wyświetlana w interfejsie użytkownika Data Factory.


### <a name="configure-a-sink"></a>Konfigurowanie ujścia
>[!TIP]
>W tym samouczku jako typ uwierzytelniania dla magazynu danych ujścia używasz **uwierzytelniania SQL** . W razie konieczności można także wybrać inne obsługiwane metody uwierzytelniania, takie jak nazwa **główna usługi** i **tożsamość zarządzana** . Aby uzyskać więcej informacji, zobacz odpowiednie sekcje w artykule [kopiowanie i Przekształcanie danych w Azure SQL Database przy użyciu Azure Data Factory](./connector-azure-sql-database.md#linked-service-properties).
>
>Aby bezpiecznie przechowywać wpisy tajne dla magazynów danych, zalecamy również korzystanie z Azure Key Vault. Aby uzyskać więcej informacji i ilustracje, zobacz temat [przechowywanie poświadczeń w Azure Key Vault](./store-credentials-in-key-vault.md).

#### <a name="create-a-sink-dataset-and-linked-service"></a>Tworzenie zestawu danych ujścia i połączonej usługi
1. Przejdź do karty **Ujście** , a następnie wybierz pozycję **+ Nowy** , aby utworzyć zestaw danych będący ujściem.

1. W oknie dialogowym **Nowy zestaw danych** wprowadź **SQL** w polu wyszukiwania, aby odfiltrować łączniki. Wybierz pozycję **Azure SQL Database** , a następnie wybierz pozycję **Kontynuuj** . W tym samouczku skopiujesz dane do bazy danych SQL.

1. W oknie dialogowym **Ustawianie właściwości** wpisz **OutputSqlDataset** w polu **Nazwa** . Z listy rozwijanej **połączona usługa** wybierz pozycję **+ Nowy** . Zestaw danych musi być skojarzony z połączoną usługą. Połączona usługa ma parametry połączenia, których usługa Data Factory używa do nawiązywania połączenia z usługą SQL Database w środowisku uruchomieniowym. Zestaw danych określa kontener, folder i plik (opcjonalnie), do którego dane są kopiowane.

1. W oknie dialogowym **Nowa połączona usługa (Azure SQL Database)** wykonaj następujące czynności:

    1. W obszarze **Nazwa** wprowadź wartość **AzureSqlDatabaseLinkedService** .
    1. W polu **Nazwa serwera** wybierz swoje wystąpienie programu SQL Server.
    1. Upewnij się, że włączono funkcję **interaktywnego tworzenia** .
    1. W polu **Nazwa bazy danych** wybierz swoją usługę SQL Database.
    1. W polu **Nazwa użytkownika** wprowadź nazwę użytkownika.
    1. W polu **Hasło** wprowadź hasło użytkownika.
    1. Wybierz pozycję **Testuj połączenie** . Nie powinno to być spowodowane tym, że program SQL Server zezwala na dostęp tylko z **wybranych sieci** i wymaga Data Factory do utworzenia prywatnego punktu końcowego, który powinien zostać zatwierdzony przed jego użyciem. W komunikacie o błędzie powinien zostać wyświetlony link umożliwiający utworzenie prywatnego punktu końcowego, który można wykonać, aby utworzyć zarządzany prywatny punkt końcowy. Alternatywnie przejdź bezpośrednio do karty **Zarządzanie** i postępuj zgodnie z instrukcjami w następnej sekcji, aby utworzyć zarządzany prywatny punkt końcowy.
    1. Pozostaw otwarte okno dialogowe, a następnie przejdź do wybranego serwera SQL.
    1. Postępuj zgodnie z instrukcjami w [tej sekcji](#approval-of-a-private-link-in-sql-server) , aby zatwierdzić link prywatny.
    1. Wróć do okna dialogowego. Wybierz ponownie **Test connection** i wybierz pozycję **Utwórz** , aby wdrożyć połączoną usługę.

1. Automatycznie przechodzi do okna dialogowego **Ustawianie właściwości** . W obszarze **Tabela** wybierz pozycję **[dbo].[emp]** . Następnie wybierz przycisk **OK** .

1. Przejdź do karty z potokiem i w **zestawie danych ujścia** , upewnij się, że wybrano opcję **OutputSqlDataset** .

    ![Zrzut ekranu pokazujący kartę potoku.](./media/tutorial-copy-data-portal-private/pipeline-tab-2.png)

Opcjonalnie można zmapować schemat źródła do odpowiedniego schematu docelowego przez następujące [Mapowanie schematu w działaniu kopiowania](./copy-activity-schema-and-type-mapping.md).

#### <a name="create-a-managed-private-endpoint"></a>Tworzenie zarządzanego prywatnego punktu końcowego

Jeśli nie wybrano hiperlinku podczas przetestowania połączenia, postępuj zgodnie ze ścieżką. Teraz musisz utworzyć zarządzany prywatny punkt końcowy, który zostanie połączony z utworzoną połączoną usługą.

1. Przejdź na kartę **Zarządzanie** .
1. Przejdź do sekcji **zarządzane prywatne punkty końcowe** .
1. Wybierz pozycję **+ Nowy** w obszarze **zarządzane prywatne punkty końcowe** .

    ![Zrzut ekranu pokazujący zarządzany przycisk nowy prywatny punkt końcowy.](./media/tutorial-copy-data-portal-private/new-managed-private-endpoint.png) 

1. Wybierz kafelek **Azure SQL Database** z listy, a następnie wybierz pozycję **Kontynuuj** .
1. Wprowadź nazwę wybranego programu SQL Server.
1. Wybierz pozycję **Utwórz** .
1. Po kilku sekundach powinna zostać wyświetlona, że utworzone łącze prywatne wymaga zatwierdzenia.
1. Wybierz utworzony prywatny punkt końcowy. Można wyświetlić hiperłącze, które umożliwi zatwierdzenie prywatnego punktu końcowego na poziomie programu SQL Server.


#### <a name="approval-of-a-private-link-in-sql-server"></a>Zatwierdzanie prywatnego linku w SQL Server
1. W programie SQL Server przejdź do pozycji **prywatne połączenia punktów końcowych** w sekcji **Ustawienia** .
1. Zaznacz pole wyboru dla utworzonego prywatnego punktu końcowego, a następnie wybierz pozycję **Zatwierdź** .
1. Dodaj opis i wybierz opcję **tak** .
1. Wróć do sekcji **zarządzane prywatne punkty końcowe** na karcie **Zarządzanie** w Data Factory.
1. Zatwierdzenie dla prywatnego punktu końcowego powinno potrwać jedną lub dwie minuty.

#### <a name="debug-and-publish-the-pipeline"></a>Debugowanie i publikowanie potoku

Przed opublikowaniem artefaktów (połączone usługi, zestawy danych i potok) w usłudze Data Factory lub własnym repozytorium Git usługi Azure Repos możesz debugować potok.

1. Aby debugować potok, wybierz na pasku narzędzi pozycję **Debuguj** . Na karcie **Dane wyjściowe** w dolnej części okna wyświetlany jest stan uruchomienia potoku.
1. Po pomyślnym uruchomieniu potoku na górnym pasku narzędzi wybierz pozycję **Opublikuj wszystko** . Ta akcja publikuje jednostki (zestawy danych i potoki) utworzone w celu Data Factory.
1. Poczekaj na wyświetlenie komunikatu **Pomyślnie opublikowano** . Aby wyświetlić komunikaty powiadomień, wybierz pozycję **Pokaż powiadomienia** w prawym górnym rogu (przycisk dzwonka).


#### <a name="summary"></a>Podsumowanie
Potok w tym przykładzie kopiuje dane z magazynu obiektów BLOB do SQL Database za pomocą prywatnych punktów końcowych w Data Factory zarządzanym Virtual Network. W tym samouczku omówiono:

* Tworzenie fabryki danych.
* Tworzenie potoku z działaniem kopiowania.