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
ms.openlocfilehash: 9458c89922fab8450b7cb8e202491d4c47e250e8
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86537646"
---
# <a name="copy-data-securely-from-azure-blob-storage-to-a-sql-database-by-using-private-endpoints"></a>Bezpieczne kopiowanie danych z usługi Azure Blob Storage do bazy danych SQL za pomocą prywatnych punktów końcowych

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

W tym samouczku utworzysz fabrykę danych przy użyciu interfejsu użytkownika usługi Azure Data Factory. **Potok w tej fabryce danych kopiuje dane bezpiecznie z usługi Azure Blob Storage do bazy danych Azure SQL Database (co pozwala na dostęp tylko do wybranych sieci) przy użyciu prywatnych punktów końcowych w [Azure Data Factory zarządzanych Virtual Network](managed-virtual-network-private-endpoint.md).** Wzorzec konfiguracji w tym samouczku ma zastosowanie do kopiowania danych z magazynu opartego na plikach do relacyjnego magazynu danych. Aby zapoznać się z listą magazynów danych obsługiwanych jako źródła i ujścia, zobacz tabelę zawierającą [obsługiwane magazyny danych](https://docs.microsoft.com/azure/data-factory/copy-activity-overview).

> [!NOTE]
>
> - Jeśli jesteś nowym użytkownikiem usługi Data Factory, zobacz [Wprowadzenie do usługi Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction).

Ten samouczek obejmuje wykonanie następujących kroków:

> * Tworzenie fabryki danych
> * Tworzenie potoku za pomocą działania kopiowania


## <a name="prerequisites"></a>Wymagania wstępne
* **Subskrypcja platformy Azure**. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/) .
* **Konto usługi Azure Storage**. Magazyn obiektów blob jest używany jako magazyn danych będący *źródłem*. Jeśli nie masz konta magazynu, utwórz je, wykonując czynności przedstawione w artykule [Tworzenie konta magazynu platformy Azure](https://docs.microsoft.com/azure/storage/common/storage-account-create?tabs=azure-portal). **Upewnij się, że konto magazynu zezwala na dostęp tylko z wybranych sieci.** 
* **Azure SQL Database**. Baza danych jest używana jako magazyn danych będący *ujściem*. Jeśli nie masz bazy danych SQL Azure, zobacz [Tworzenie bazy danych SQL](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal) w celu wykonania czynności. **Upewnij się, że konto Azure SQL Database zezwala na dostęp tylko z wybranych sieci.** 

### <a name="create-a-blob-and-a-sql-table"></a>Tworzenie obiektu blob i tabeli SQL

Teraz przygotuj swój magazyn obiektów blob i bazę danych SQL na potrzeby tego samouczka, wykonując następujące kroki.

#### <a name="create-a-source-blob"></a>Tworzenie źródłowego obiektu Blob

1. Uruchom program Notatnik. Skopiuj poniższy tekst i zapisz go na dysku jako plik **emp.txt**:

    ```
    FirstName,LastName
    John,Doe
    Jane,Doe
    ```

1. W magazynie obiektów blob utwórz kontener o nazwie **adftutorial**. W tym kontenerze utwórz folder o nazwie **input**. Następnie przekaż plik **emp.txt** do folderu **input**. Do wykonania tych zadań użyj witryny Azure Portal lub narzędzi takich jak [Eksplorator usługi Azure Storage](https://storageexplorer.com/).

#### <a name="create-a-sink-sql-table"></a>Tworzenie tabeli SQL ujścia

1. Utwórz tabelę **dbo.emp** w bazie danych SQL przy użyciu poniższego skryptu SQL:

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

1. Zezwól usługom platformy Azure na dostęp do programu SQL Server. Upewnij się, że ustawienie **Zezwalaj na dostęp do usług platformy Azure** jest włączone i ma wartość **WŁĄCZ** dla programu SQL Server, aby usługa Data Factory mogła zapisywać dane w programie SQL Server. Aby sprawdzić i włączyć to ustawienie, przejdź do pozycji Azure SQL Server > Overview > Ustaw zaporę serwera> ustaw opcję **Zezwalaj na dostęp do usług platformy Azure** na wartość **włączone**.

## <a name="create-a-data-factory"></a>Tworzenie fabryki danych
W tym kroku utworzysz fabrykę danych i uruchomisz interfejs użytkownika usługi Data Factory, aby utworzyć potok w fabryce danych.

1. Otwórz przeglądarkę **Microsoft Edge** lub **Google Chrome**. Obecnie interfejs użytkownika usługi Data Factory jest obsługiwany tylko przez przeglądarki internetowe Microsoft Edge i Google Chrome.


2. W menu po lewej stronie wybierz pozycję **Utwórz zasób**  >  **Analytics**  >  **Data Factory**.

3. Na stronie **Nowa fabryka danych** w polu **Nazwa** wprowadź wartość **ADFTutorialDataFactory**.

   Nazwa fabryki danych Azure musi być *globalnie unikatowa*. Jeśli zostanie wyświetlony komunikat o błędzie dotyczącym wartości nazwy, wprowadź inną nazwę dla fabryki danych. (na przykład Twojanazwaadftutorialdatafactory). Reguły nazewnictwa dla artefaktów usługi Data Factory można znaleźć w artykule [Data Factory — reguły nazewnictwa](https://docs.microsoft.com/azure/data-factory/naming-rules).

4. Wybierz **subskrypcję** platformy Azure, w której chcesz utworzyć fabrykę danych.

5. W obszarze **Grupa zasobów** wykonaj jedną z następujących czynności:

    a. Wybierz pozycję **Użyj istniejącej**, a następnie wybierz istniejącą grupę zasobów z listy rozwijanej.

    b. Wybierz pozycję **Utwórz nową**, a następnie wprowadź nazwę grupy zasobów. 
         
    Informacje na temat grup zasobów znajdują się w artykule [Using resource groups to manage your Azure resources (Używanie grup zasobów do zarządzania zasobami platformy Azure)](https://docs.microsoft.com/azure/azure-resource-manager/management/overview). 

6. W obszarze **Wersja** wybierz pozycję **V2**.

7. W obszarze **Lokalizacja** wybierz lokalizację fabryki danych. Na liście rozwijanej są wyświetlane tylko obsługiwane lokalizacje. Magazyny danych (np. usługi Azure Storage i SQL Database) oraz jednostki obliczeniowe (np. usługa Azure HDInsight) używane przez fabrykę danych mogą znajdować się w innych regionach.


8. Wybierz pozycję **Utwórz**.


9. Po zakończeniu tworzenia zobaczysz powiadomienie w centrum powiadomień. Wybierz pozycję **Przejdź do zasobu** , aby przejść do strony Fabryka danych.

10. Wybierz pozycję **Tworzenie i monitorowanie**, aby uruchomić interfejs użytkownika usługi Data Factory na osobnej karcie.

## <a name="create-an-azure-integration-runtime-in-adf-managed-virtual-network"></a>Utwórz Azure Integration Runtime w usłudze ADF Managed Virtual Network
W tym kroku utworzysz Azure Integration Runtime i włączysz Virtual Network zarządzane.

1. W portalu ADF przejdź do pozycji **Zarządzaj centrum** , a następnie kliknij pozycję **Nowy** , aby utworzyć nowy Azure Integration Runtime.
   ![Utwórz nowy Azure Integration Runtime](./media/tutorial-copy-data-portal-private/create-new-azure-ir.png)
2. Wybierz, aby utworzyć Integration Runtime platformy Azure * *.
   ![Nowe Azure Integration Runtime](./media/tutorial-copy-data-portal-private/azure-ir.png)
3. Włącz **Virtual Network**.
   ![Nowe Azure Integration Runtime](./media/tutorial-copy-data-portal-private/enable-managed-vnet.png)
4. Wybierz pozycję **Utwórz**.

## <a name="create-a-pipeline"></a>Tworzenie potoku
W tym kroku utworzysz potok z działaniem kopiowania w fabryce danych. Działanie kopiowania kopiuje dane z magazynu obiektów blob do usługi SQL Database. W [samouczku szybkiego startu](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal) utworzono potok, wykonując następujące czynności:

1. Utworzenie połączonej usługi.
1. Utworzenie wejściowych i wyjściowych zestawów danych.
1. Tworzenie potoku.

W tym samouczku zaczniesz od utworzenia potoku. Następnie utworzysz usługi połączone i zestawy danych, gdy będą potrzebne do skonfigurowania potoku.

1. Na stronie **Zaczynajmy** wybierz pozycję **Utwórz potok**.

   ![Tworzenie potoku](./media/doc-common-process/get-started-page.png)
1. W okienku **Właściwości** potoku wpisz **CopyPipeline** dla **nazwy** potoku.

1. W oknie Narzędzie **działania** rozwiń kategorię **przenoszenie i przekształcanie** , a następnie przeciągnij i upuść działanie **Kopiowanie danych** z okna narzędzia do powierzchni projektanta potoku. Wprowadź wartość **CopyFromBlobToSql** w polu **Nazwa**.

    ![Działanie kopiowania](./media/tutorial-copy-data-portal-private/drag-drop-copy-activity.png)

### <a name="configure-source"></a>Konfigurowanie źródła

>[!TIP]
>W tym samouczku użyto *klucza konta* jako typu uwierzytelniania dla źródłowego magazynu danych, ale można wybrać inne obsługiwane metody uwierzytelniania: *Identyfikator URI SAS*,*nazwę główną usługi* i *tożsamość zarządzaną* , jeśli jest to konieczne. Aby uzyskać szczegółowe informacje, zapoznaj się z odpowiednimi sekcjami w [tym artykule](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#linked-service-properties) .
>Aby bezpiecznie przechowywać wpisy tajne dla magazynów danych, zaleca się również korzystanie z Azure Key Vault. Szczegółowe ilustracje znajdują się w [tym artykule](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault) .

#### <a name="create-source-dataset-and-linked-service"></a>Tworzenie zestawu danych źródłowych i połączonej usługi

1. Przejdź do karty **Źródło** . Wybierz pozycję **+ Nowy** , aby utworzyć źródłowy zestaw danych.

1. W oknie dialogowym **Nowy zestaw danych** wybierz pozycję **Azure Blob Storage**, a następnie wybierz pozycję **Kontynuuj**. Dane źródłowe znajdują się w magazynie obiektów blob, musisz więc wybrać usługę **Azure Blob Storage** dla źródłowego zestawu danych.

1. W oknie dialogowym **Wybieranie formatu** wybierz typ formatu danych, a następnie wybierz pozycję **Kontynuuj**.

1. W oknie dialogowym **Ustawianie właściwości** wpisz **SourceBlobDataset** w polu Nazwa. Zaznacz pole wyboru **pierwszy wiersz jako nagłówek**. W polu tekstowym **połączona usługa** wybierz pozycję **+ Nowy**.

1. W oknie dialogowym **Nowa połączona usługa (Azure Blob Storage)** wprowadź **AzureStorageLinkedService** jako nazwę, a następnie wybierz konto magazynu z listy **nazwa konta magazynu** . 

1. Upewnij się, że włączono funkcję **interaktywnego tworzenia**. Włączenie tej możliwości może potrwać około 1 minuty.

    ![Tworzenie interaktywne](./media/tutorial-copy-data-portal-private/interactive-authoring.png)

1. Wybierz **Test connection**, powinna zakończyć się niepowodzeniem, gdy konto magazynu zezwala na dostęp tylko z poziomu wybranej sieci i wymaga Azure Data Factory do utworzenia prywatnego punktu końcowego, który powinien zostać zatwierdzony przed użyciem. W komunikacie o błędzie powinien zostać wyświetlony link umożliwiający utworzenie **prywatnego punktu końcowego** , który można wykonać, aby utworzyć zarządzany prywatny punkt końcowy. *Alternatywnie przejdź bezpośrednio do karty Zarządzanie i postępuj zgodnie z instrukcjami w [następnej sekcji](#create-a-managed-private-endpoint) , aby utworzyć zarządzany prywatny punkt końcowy*
> [!NOTE]
> Karta Zarządzanie może być niedostępna dla wszystkich wystąpień usługi Fabryka danych. Jeśli nie widzisz go, możesz nadal uzyskiwać dostęp do prywatnych punktów końcowych za poorednictwem karty "**autor**" — > "**połączenia**" — > "**prywatny punkt końcowy**"
1. Pozostaw otwarte okno dialogowe, a następnie przejdź do wybranego powyżej konta magazynu.

1. Postępuj zgodnie z instrukcjami w [tej sekcji](#approval-of-a-private-link-in-storage-account) , aby zatwierdzić link prywatny.

1. Wróć do okna dialogowego. **Test connection** ponownie i wybierz pozycję **Utwórz** , aby wdrożyć połączoną usługę.

1. Po utworzeniu połączonej usługi zostanie ona wyłączona z powrotem do strony **Ustawianie właściwości** . Wybierz przycisk **Przeglądaj** obok pozycji **Ścieżka pliku**.

1. Przejdź do folderu **adftutorial/Input** , wybierz plik **emp.txt** , a następnie wybierz przycisk **OK**.

1. Wybierz przycisk **OK**. Automatycznie przechodzi do strony potoku. Na karcie **Źródło** upewnij się, że wybrano opcję **SourceBlobDataset** . Aby wyświetlić podgląd danych na tej stronie, wybierz pozycję **Podgląd danych**.

    ![Zestaw danych źródłowych](./media/tutorial-copy-data-portal-private/source-dataset-selected.png)

#### <a name="create-a-managed-private-endpoint"></a>Tworzenie zarządzanego prywatnego punktu końcowego

Jeśli w przypadku testowania powyższego połączenia nie klikniesz hiperlinku, postępuj zgodnie z następującą ścieżką. Teraz musisz utworzyć zarządzany prywatny punkt końcowy, który zostanie połączony z utworzoną powyżej połączoną usługą.

1. Przejdź na kartę Zarządzanie.
> [!NOTE]
> Karta Zarządzanie może być niedostępna dla wszystkich wystąpień usługi Fabryka danych. Jeśli nie widzisz go, możesz nadal uzyskiwać dostęp do prywatnych punktów końcowych za poorednictwem karty "**autor**" — > "**połączenia**" — > "**prywatny punkt końcowy**"

1. Przejdź do sekcji zarządzane prywatne punkty końcowe.

1. Wybierz pozycję **+ Nowy** w obszarze zarządzane prywatne punkty końcowe.

    ![Nowy zarządzany prywatny punkt końcowy](./media/tutorial-copy-data-portal-private/new-managed-private-endpoint.png) 

1. Wybierz kafelek Blob Storage platformy Azure z listy i wybierz pozycję **Kontynuuj**.

1. Wprowadź nazwę utworzonego powyżej konta magazynu.

1. Wybierz pozycję **Utwórz**.

1. Po odczekaniu kilku sekund powinna zostać wyświetlona, że utworzone łącze prywatne wymaga zatwierdzenia.

1. Wybierz utworzony powyżej prywatny punkt końcowy. Można wyświetlić hiperłącze, które umożliwi zatwierdzenie prywatnego punktu końcowego na poziomie konta magazynu.

    ![Zarządzanie prywatnym punktem końcowym](./media/tutorial-copy-data-portal-private/manage-private-endpoint.png) 

#### <a name="approval-of-a-private-link-in-storage-account"></a>Zatwierdzanie prywatnego linku na koncie magazynu
1. Na koncie magazynu przejdź do pozycji **prywatne połączenia punktów końcowych** w sekcji Ustawienia.

1. Zaznacz utworzony powyżej prywatny punkt końcowy i wybierz pozycję **Zatwierdź**.

    ![Zatwierdź prywatny punkt końcowy](./media/tutorial-copy-data-portal-private/approve-private-endpoint.png)

1. Dodaj opis, a następnie kliknij przycisk **tak** .
1. Wróć do sekcji **zarządzane prywatne punkty końcowe** na karcie **Zarządzanie** w Azure Data Factory.
1. Zatwierdzenie prywatnego punktu końcowego w Azure Data Factory interfejsie użytkownika powinno potrwać około 1-2 minut.


### <a name="configure-sink"></a>Konfigurowanie ujścia
>[!TIP]
>W tym samouczku jako typ uwierzytelniania dla magazynu danych ujścia używasz *uwierzytelniania SQL* , ale możesz wybrać inne obsługiwane metody uwierzytelniania: *nazwę główną usługi* i *tożsamość zarządzaną* , jeśli jest to konieczne. Aby uzyskać szczegółowe informacje, zapoznaj się z odpowiednimi sekcjami w [tym artykule](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-database#linked-service-properties) .
>Aby bezpiecznie przechowywać wpisy tajne dla magazynów danych, zaleca się również korzystanie z Azure Key Vault. Szczegółowe ilustracje znajdują się w [tym artykule](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault) .

#### <a name="create-sink-dataset-and-linked-service"></a>Tworzenie zestawu danych ujścia i połączonej usługi
1. Przejdź do karty **Ujście**, a następnie wybierz pozycję **+ Nowy**, aby utworzyć zestaw danych będący ujściem.

1. W oknie dialogowym **Nowy zestaw danych** wprowadź wartość "SQL" w polu wyszukiwania, aby odfiltrować łączniki, wybierz pozycję **Azure SQL Database**, a następnie wybierz pozycję **Kontynuuj**. W tym samouczku skopiujesz dane do bazy danych SQL.

1. W oknie dialogowym **Ustawianie właściwości** wpisz **OutputSqlDataset** w polu Nazwa. Z listy rozwijanej **połączona usługa** wybierz pozycję **+ Nowy**. Zestaw danych musi być skojarzony z połączoną usługą. Połączona usługa ma parametry połączenia, których usługa Data Factory używa do nawiązywania połączenia z usługą SQL Database w środowisku uruchomieniowym. Zestaw danych określa kontener, folder i plik (opcjonalnie), do którego dane są kopiowane.

1. W oknie dialogowym **Nowa połączona usługa (Azure SQL Database)** wykonaj następujące czynności:

    1. W obszarze **Nazwa** wprowadź wartość **AzureSqlDatabaseLinkedService**.
    1. W polu **Nazwa serwera** wybierz swoje wystąpienie programu SQL Server.
    1. Upewnij się, że włączono funkcję **interaktywnego tworzenia**.
    1. W polu **Nazwa bazy danych** wybierz swoją usługę SQL Database.
    1. W polu **Nazwa użytkownika** wprowadź nazwę użytkownika.
    1. W polu **Hasło** wprowadź hasło użytkownika.
    1. Wybierz pozycję **Testuj połączenie**. Nie powinna działać, ponieważ program SQL Server zezwala na dostęp tylko z wybranych sieci i wymaga Azure Data Factory do utworzenia prywatnego punktu końcowego, który powinien zostać zatwierdzony przed użyciem. W komunikacie o błędzie powinien zostać wyświetlony link umożliwiający utworzenie **prywatnego punktu końcowego** , który można wykonać, aby utworzyć zarządzany prywatny punkt końcowy. *Alternatywnie przejdź bezpośrednio do karty Zarządzanie i postępuj zgodnie z instrukcjami w następnej sekcji, aby utworzyć zarządzany prywatny punkt końcowy*
    1. Pozostaw otwarte okno dialogowe, a następnie przejdź do wybranego powyżej serwera SQL.    
    1. Postępuj zgodnie z instrukcjami w [tej sekcji](#approval-of-a-private-link-in-sql-server) , aby zatwierdzić link prywatny.
    1. Wróć do okna dialogowego. **Test connection** ponownie i wybierz pozycję **Utwórz** , aby wdrożyć połączoną usługę.

1. Powoduje automatyczne przejście do okna dialogowego **Ustawianie właściwości** . W obszarze **Tabela** wybierz pozycję **[dbo].[emp]**. Następnie wybierz pozycję **OK**.

1. Przejdź do karty z potokiem i upewnij się, że w obszarze **Zestaw danych będący ujściem** wybrano pozycję **OutputSqlDataset**.

    ![Karta potoku](./media/tutorial-copy-data-portal-private/pipeline-tab-2.png)       

Opcjonalnie można zmapować schemat źródła do odpowiedniego schematu docelowego przez następujące [Mapowanie schematu w działaniu kopiowania](https://docs.microsoft.com/azure/data-factory/copy-activity-schema-and-type-mapping).

#### <a name="create-a-managed-private-endpoint"></a>Tworzenie zarządzanego prywatnego punktu końcowego

Jeśli w przypadku testowania powyższego połączenia nie klikniesz hiperlinku, postępuj zgodnie z następującą ścieżką. Teraz musisz utworzyć zarządzany prywatny punkt końcowy, który zostanie połączony z utworzoną powyżej połączoną usługą.

1. Przejdź na kartę Zarządzanie.
1. Przejdź do sekcji zarządzane prywatne punkty końcowe.
1. Wybierz pozycję **+ Nowy** w obszarze zarządzane prywatne punkty końcowe.

    ![Nowy zarządzany prywatny punkt końcowy](./media/tutorial-copy-data-portal-private/new-managed-private-endpoint.png) 

1. Wybierz z listy kafelek Azure SQL Database i wybierz pozycję **Kontynuuj**.
1. Wprowadź nazwę serwera SQL, który został wybrany powyżej.
1. Wybierz pozycję **Utwórz**.
1. Po odczekaniu kilku sekund powinna zostać wyświetlona, że utworzone łącze prywatne wymaga zatwierdzenia.
1. Wybierz utworzony powyżej prywatny punkt końcowy. Można wyświetlić hiperłącze, które umożliwi zatwierdzenie prywatnego punktu końcowego na poziomie programu SQL Server.


#### <a name="approval-of-a-private-link-in-sql-server"></a>Zatwierdzanie prywatnego linku w programie SQL Server
1. W programie SQL Server przejdź do pozycji **prywatne połączenia punktów końcowych** w sekcji Ustawienia.
1. Zaznacz utworzony powyżej prywatny punkt końcowy i wybierz pozycję **Zatwierdź**.
1. Dodaj opis, a następnie kliknij przycisk **tak**.
1. Wróć do sekcji **zarządzane prywatne punkty końcowe** na karcie **Zarządzanie** w Azure Data Factory.
1. Zatwierdzenie zatwierdzenia dla prywatnego punktu końcowego powinno potrwać około 1-2 minut.

#### <a name="debug-and-publish-the-pipeline"></a>Debugowanie i publikowanie potoku

Przed opublikowaniem artefaktów (połączone usługi, zestawy danych i potok) w usłudze Data Factory lub własnym repozytorium Git usługi Azure Repos możesz debugować potok.

1. Aby debugować potok, wybierz na pasku narzędzi pozycję **Debuguj**. Na karcie **Dane wyjściowe** w dolnej części okna wyświetlany jest stan uruchomienia potoku.
2. Po pomyślnym uruchomieniu potoku na górnym pasku narzędzi wybierz pozycję **Opublikuj wszystko**. Ta akcja powoduje opublikowanie utworzonych jednostek (zestawy danych i potok) w usłudze Data Factory.
3. Poczekaj na wyświetlenie komunikatu **Pomyślnie opublikowano**. Aby wyświetlić komunikaty powiadomień, kliknij przycisk **Pokaż powiadomienia** w prawym górnym rogu (przycisk dzwonka).


#### <a name="summary"></a>Podsumowanie
Potok w tym przykładzie kopiuje dane z magazynu obiektów BLOB do usługi Azure SQL DB przy użyciu prywatnego punktu końcowego w zarządzanym Virtual Network. W tym samouczku omówiono:

> * Tworzenie fabryki danych
> * Tworzenie potoku za pomocą działania kopiowania

