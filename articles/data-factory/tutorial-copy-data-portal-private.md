---
title: Używanie prywatnych punktów końcowych do tworzenia Azure Data Factory potoku
description: Ten samouczek zawiera instrukcje krok po kroku dotyczące tworzenia fabryki danych z potokiem za pomocą witryny Azure Portal. Potok używa działania kopiowania do kopiowania danych z usługi Azure Blob Storage do Azure SQL danych.
author: linda33wj
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 04/14/2021
ms.author: jingwang
ms.openlocfilehash: 191ad61990e10fdb718eebf1a8f57d8edaadcf35
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107515461"
---
# <a name="copy-data-securely-from-azure-blob-storage-to-a-sql-database-by-using-private-endpoints"></a>Bezpieczne kopiowanie danych z usługi Azure Blob Storage do bazy danych SQL przy użyciu prywatnych punktów końcowych

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym samouczku utworzysz fabrykę danych przy użyciu interfejsu użytkownika usługi Azure Data Factory. *Potok w tej fabryce danych bezpiecznie kopiuje dane z usługi Azure Blob Storage do bazy danych usługi Azure SQL (obie zezwalają na dostęp tylko do wybranych sieci) przy użyciu prywatnych punktów końcowych w usłudze [Azure Data Factory Managed Virtual Network.](managed-virtual-network-private-endpoint.md)* Wzorzec konfiguracji w tym samouczku ma zastosowanie do kopiowania danych z magazynu opartego na plikach do relacyjnego magazynu danych. Aby uzyskać listę magazynów danych obsługiwanych jako źródła i ujścia, zobacz tabelę [Obsługiwane magazyny i formaty](./copy-activity-overview.md) danych.

> [!NOTE]
> Jeśli jesteś nowym użytkownikiem usługi Data Factory, zobacz [Wprowadzenie do usługi Azure Data Factory](./introduction.md).

W tym samouczku wykonasz następujące czynności:

* Tworzenie fabryki danych.
* Tworzenie potoku z działaniem kopiowania.


## <a name="prerequisites"></a>Wymagania wstępne
* **Subskrypcja platformy Azure.** Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz bezpłatne konto platformy [Azure.](https://azure.microsoft.com/free/)
* **Konto usługi Azure Storage.** Magazyn obiektów blob jest używany jako magazyn danych będący *źródłem*. Jeśli nie masz konta magazynu, utwórz je, wykonując czynności przedstawione w artykule [Tworzenie konta magazynu platformy Azure](../storage/common/storage-account-create.md?tabs=azure-portal). *Upewnij się, że konto magazynu zezwala na dostęp tylko z wybranych sieci.* 
* **Azure SQL Database**. Baza danych jest używana jako magazyn danych będący *ujściem*. Jeśli nie masz bazy danych Azure SQL, zobacz Tworzenie bazy danych [SQL,](../azure-sql/database/single-database-create-quickstart.md) aby uzyskać instrukcje dotyczące jej tworzenia. *Upewnij się, SQL Database zezwala na dostęp tylko z wybranych sieci.* 

### <a name="create-a-blob-and-a-sql-table"></a>Tworzenie obiektu blob i tabeli SQL

Teraz przygotuj magazyn obiektów blob i bazę danych SQL do pracy z samouczkiem, wykonując następujące kroki.

#### <a name="create-a-source-blob"></a>Tworzenie źródłowego obiektu Blob

1. Otwórz Notatnik. Skopiuj poniższy tekst i zapisz go na dysku jako plik **emp.txt**:

    ```
    FirstName,LastName
    John,Doe
    Jane,Doe
    ```

1. Utwórz kontener o nazwie **adftutorial w** magazynie obiektów blob. W tym kontenerze utwórz folder o nazwie **input**. Następnie przekaż plik **emp.txt** do folderu **input**. Do wykonania tych zadań użyj witryny Azure Portal lub narzędzi takich jak [Eksplorator usługi Azure Storage](https://storageexplorer.com/).

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

1. Otwórz przeglądarkę Microsoft Edge lub Google Chrome. Obecnie tylko przeglądarki Microsoft Edge i Google Chrome obsługują interfejs Data Factory użytkownika.

1. W menu po lewej stronie wybierz **pozycję Utwórz zasób**  >  **Analiza**  >  **Data Factory**.

1. Na stronie **Nowa fabryka danych** w polu **Nazwa** wprowadź wartość **ADFTutorialDataFactory**.

   Nazwa fabryki danych Azure musi być globalnie *unikatowa.* Jeśli zostanie wyświetlony komunikat o błędzie z wartością nazwy, wprowadź inną nazwę fabryki danych (na przykład twojanazwaADFTutorialDataFactory). Reguły nazewnictwa dla artefaktów usługi Data Factory można znaleźć w artykule [Data Factory — reguły nazewnictwa](./naming-rules.md).

1. Wybierz **subskrypcję** platformy Azure, w której chcesz utworzyć fabrykę danych.

1. W obszarze **Grupa zasobów** wykonaj jedną z następujących czynności:

    - Wybierz pozycję **Użyj istniejącej**, a następnie wybierz istniejącą grupę zasobów z listy rozwijanej.
    - Wybierz **pozycję Utwórz** nową i wprowadź nazwę grupy zasobów. 
     
    Informacje na temat grup zasobów znajdują się w artykule [Using resource groups to manage your Azure resources (Używanie grup zasobów do zarządzania zasobami platformy Azure)](../azure-resource-manager/management/overview.md). 

1. W obszarze **Wersja** wybierz pozycję **V2**.

1. W obszarze **Lokalizacja** wybierz lokalizację fabryki danych. Na liście rozwijanej są wyświetlane tylko obsługiwane lokalizacje. Magazyny danych (np. usługi Azure Storage i SQL Database) oraz jednostki obliczeniowe (np. usługa Azure HDInsight) używane przez fabrykę danych mogą znajdować się w innych regionach.

1. Wybierz przycisk **Utwórz**.

1. Po zakończeniu tworzenia w Centrum powiadomień zostanie wyświetlony ten powiadomienie. Wybierz **pozycję Przejdź do zasobu,** aby przejść do **Data Factory** zasobów.

1. Wybierz pozycję **Tworzenie i monitorowanie**, aby uruchomić interfejs użytkownika usługi Data Factory na osobnej karcie.

## <a name="create-an-azure-integration-runtime-in-data-factory-managed-virtual-network"></a>Tworzenie środowiska Azure Integration Runtime w usłudze Data Factory Managed Virtual Network
W tym kroku utworzysz środowisko Azure Integration Runtime i włączysz środowisko Data Factory Managed Virtual Network.

1. W portalu Data Factory przejdź do opcji Zarządzaj i **wybierz** pozycję **Nowy,** aby utworzyć nowe środowisko Azure Integration Runtime.

   ![Zrzut ekranu przedstawiający tworzenie nowego środowiska Azure Integration Runtime.](./media/tutorial-copy-data-portal-private/create-new-azure-ir.png)
1. Na stronie **Konfiguracja środowiska Integration Runtime** wybierz środowisko Integration Runtime do utworzenia na podstawie wymaganych możliwości. W tym samouczku wybierz pozycję **Azure (Self-Hosted),** a następnie kliknij przycisk **Continue (Kontynuuj).** 
1. Wybierz **pozycję Azure,** a następnie kliknij **przycisk Kontynuuj,** aby utworzyć środowisko Azure Integration Runtime.

   ![Zrzut ekranu przedstawiający nowe środowisko Azure Integration Runtime.](./media/tutorial-copy-data-portal-private/azure-ir.png)
1. W **obszarze Konfiguracja sieci wirtualnej (wersja zapoznawcza)** wybierz pozycję **Włącz.**

   ![Zrzut ekranu przedstawiający włączanie nowego środowiska Azure Integration Runtime.](./media/tutorial-copy-data-portal-private/enable-managed-vnet.png)
1. Wybierz przycisk **Utwórz**.

## <a name="create-a-pipeline"></a>Tworzenie potoku
W tym kroku utworzysz potok z działaniem kopiowania w fabryce danych. Działanie kopiowania kopiuje dane z magazynu obiektów blob do usługi SQL Database. W [samouczku szybkiego startu](./quickstart-create-data-factory-portal.md) utworzono potok, wykonując następujące czynności:

1. Utworzenie połączonej usługi.
1. Utworzenie wejściowych i wyjściowych zestawów danych.
1. Tworzenie potoku.

W tym samouczku rozpoczniesz od utworzenia potoku. Następnie utworzysz usługi połączone i zestawy danych, gdy będą potrzebne do skonfigurowania potoku.

1. Na stronie **Zaczynajmy** wybierz pozycję **Utwórz potok**.

   ![Zrzut ekranu przedstawiający tworzenie potoku.](./media/doc-common-process/get-started-page.png)
1. W okienku właściwości potoku wprowadź **wartość CopyPipeline** jako nazwę potoku.

1. W polu **narzędzia Działania** rozwiń kategorię **Przenieś** i przekształć, a następnie przeciągnij działanie **Kopiowanie** danych z pola narzędzia na powierzchnię projektanta potoku. Wprowadź **wartość CopyFromBlobToSql** jako nazwę.

    ![Zrzut ekranu przedstawiający działanie kopiowania.](./media/tutorial-copy-data-portal-private/drag-drop-copy-activity.png)

### <a name="configure-a-source"></a>Konfigurowanie źródła

>[!TIP]
>W tym samouczku **użyjemy klucza konta** jako typu uwierzytelniania dla źródłowego magazynu danych. W razie potrzeby można również wybrać inne obsługiwane metody uwierzytelniania, takie jak **sygnatura URI sygnatury** dostępu  współdzielonego,jednostkę usługi i tożsamość zarządzana. Aby uzyskać więcej informacji, zobacz odpowiednie sekcje w temacie [Kopiowanie i przekształcanie danych w usłudze Azure Blob Storage przy użyciu Azure Data Factory](./connector-azure-blob-storage.md#linked-service-properties).
>
>Aby bezpiecznie przechowywać wpisy tajne dla magazynów danych, zalecamy również używanie Azure Key Vault. Aby uzyskać więcej informacji i ilustracji, zobacz [Store credentials in Azure Key Vault](./store-credentials-in-key-vault.md)(Przechowywanie poświadczeń w Azure Key Vault ).

#### <a name="create-a-source-dataset-and-linked-service"></a>Tworzenie źródłowego zestawu danych i połączonej usługi

1. Przejdź do **karty Źródło.** Wybierz **pozycję + Nowy,** aby utworzyć źródłowy zestaw danych.

1. W **oknie dialogowym Nowy** zestaw danych wybierz pozycję **Azure Blob Storage**, a następnie wybierz pozycję **Kontynuuj.** Dane źródłowe znajdują się w magazynie obiektów blob, musisz więc wybrać usługę **Azure Blob Storage** dla źródłowego zestawu danych.

1. W **oknie dialogowym** Wybieranie formatu wybierz typ formatu danych, a następnie wybierz pozycję **Kontynuuj.**

1. W **oknie dialogowym Ustawianie** właściwości wprowadź **wartość SourceBlobDataset w** polu **Nazwa**. Zaznacz pole wyboru pierwszy wiersz **jako nagłówek**. W polu **tekstowym Połączona usługa** wybierz pozycję **+ Nowa.**

1. W **oknie dialogowym** Nowa połączona usługa (Azure Blob Storage) wprowadź wartość **AzureStorageLinkedService** w polu Nazwa **i** wybierz konto magazynu z listy **Nazwa konta** magazynu. 

1. Upewnij się, że włączyć **opcję Tworzenie interakcyjne.** Może to potrwać około minuty.

    ![Zrzut ekranu przedstawiający tworzenie interakcyjne.](./media/tutorial-copy-data-portal-private/interactive-authoring.png)

1. Wybierz pozycję **Testuj połączenie**. Powinno to się nie powieść,  gdy konto magazynu zezwala na dostęp tylko z wybranych sieci i wymaga Data Factory utworzenia dla niego prywatnego punktu końcowego, który powinien zostać zatwierdzony przed użyciem. W komunikacie o błędzie powinien zostać wyświetlony link do tworzenia prywatnego punktu końcowego, który można śledzić, aby utworzyć zarządzany prywatny punkt końcowy. Alternatywą jest bezpośrednie przejdź do karty **Zarządzanie** i postępuj zgodnie z instrukcjami w [następnej sekcji,](#create-a-managed-private-endpoint) aby utworzyć zarządzany prywatny punkt końcowy.

   > [!NOTE]
   > Karta **Zarządzanie** może nie być dostępna dla wszystkich wystąpień fabryki danych. Jeśli go nie widzisz, możesz uzyskać dostęp do prywatnych punktów końcowych, wybierając pozycję **Tworzenie**  >  **połączeń**  >  **Prywatny punkt końcowy**.
1. Nie otwieraj okna dialogowego, a następnie przejdź do konta magazynu.

1. Postępuj zgodnie z [instrukcjami w tej sekcji,](#approval-of-a-private-link-in-a-storage-account) aby zatwierdzić link prywatny.

1. Wstecz do okna dialogowego. Wybierz **Test connection** ponownie, a następnie wybierz **pozycję Utwórz,** aby wdrożyć usługę połączona.

1. Po utworzeniu połączonej usługi wróci ona do strony **Ustawianie właściwości.** Wybierz przycisk **Przeglądaj** obok pozycji **Ścieżka pliku**.

1. Przejdź do **folderu adftutorial/input,** wybierzemp.txt, a następnie wybierz przycisk **OK.** 

1. Wybierz przycisk **OK**. Automatycznie przechodzi do strony potoku. Na karcie **Source** (Źródło) upewnij się, **że wybrano pozycję SourceBlobDataset.** Aby wyświetlić podgląd danych na tej stronie, wybierz pozycję **Podgląd danych**.

    ![Zrzut ekranu przedstawiający źródłowy zestaw danych.](./media/tutorial-copy-data-portal-private/source-dataset-selected.png)

#### <a name="create-a-managed-private-endpoint"></a>Tworzenie zarządzanego prywatnego punktu końcowego

Jeśli hiperlink nie został wybrane podczas przetestowania połączenia, postępuj zgodnie ze ścieżką. Teraz musisz utworzyć zarządzany prywatny punkt końcowy, który połączysz z utworzoną połączoną usługą.

1. Przejdź do karty **Zarządzanie.**

   > [!NOTE]
   > Karta **Zarządzanie** może nie być dostępna dla wszystkich Data Factory wystąpień. Jeśli go nie widzisz, możesz uzyskać dostęp do prywatnych punktów końcowych, wybierając pozycję **Tworzenie**  >  **połączeń**  >  **Prywatny punkt końcowy**.

1. Przejdź do sekcji **Zarządzane prywatne punkty** końcowe.

1. Wybierz **pozycję + Nowy w** obszarze Zarządzane prywatne punkty **końcowe.**

    ![Zrzut ekranu przedstawiający przycisk Nowe zarządzane prywatne punkty końcowe.](./media/tutorial-copy-data-portal-private/new-managed-private-endpoint.png) 

1. Wybierz **kafelek Azure Blob Storage** z listy, a następnie wybierz pozycję **Kontynuuj.**

1. Wprowadź nazwę utworzonego konta magazynu.

1. Wybierz przycisk **Utwórz**.

1. Po kilku sekundach powinno być widać, że utworzony link prywatny wymaga zatwierdzenia.

1. Wybierz utworzony prywatny punkt końcowy. Zostanie wyświetlony hiperlink, który spowoduje zatwierdzenie prywatnego punktu końcowego na poziomie konta magazynu.

    ![Zrzut ekranu przedstawiający okienko Zarządzany prywatny punkt końcowy.](./media/tutorial-copy-data-portal-private/manage-private-endpoint.png) 

#### <a name="approval-of-a-private-link-in-a-storage-account"></a>Zatwierdzanie linku prywatnego na koncie magazynu
1. Na koncie magazynu przejdź do sekcji **Połączenia prywatnego punktu końcowego** **w sekcji** Ustawienia.

1. Zaznacz pole wyboru dla utworzonego prywatnego punktu końcowego, a następnie wybierz pozycję **Zatwierdź.**

    ![Zrzut ekranu przedstawiający przycisk Zatwierdź dla prywatnego punktu końcowego.](./media/tutorial-copy-data-portal-private/approve-private-endpoint.png)

1. Dodaj opis i wybierz pozycję **Tak.**
1. Wstecz sekcji **Zarządzane prywatne punkty końcowe** na karcie **Zarządzanie** w Data Factory.
1. Po około jednej lub dwóch minutach zatwierdzenie prywatnego punktu końcowego powinno zostać wyświetlone w interfejsie Data Factory użytkownika.


### <a name="configure-a-sink"></a>Konfigurowanie ujścia
>[!TIP]
>W tym samouczku użyjemy uwierzytelniania **SQL** jako typu uwierzytelniania dla magazynu danych ujścia. W razie potrzeby można również wybrać inne obsługiwane metody uwierzytelniania, takie jak **service principal** (Jednostka usługi) i Managed **Identity (Tożsamość** zarządzana). Aby uzyskać więcej informacji, zobacz odpowiednie sekcje w temacie Kopiowanie i przekształcanie [danych Azure SQL Database za pomocą Azure Data Factory](./connector-azure-sql-database.md#linked-service-properties).
>
>Aby bezpiecznie przechowywać wpisy tajne dla magazynów danych, zalecamy również używanie Azure Key Vault. Aby uzyskać więcej informacji i ilustracji, zobacz [Store credentials in Azure Key Vault](./store-credentials-in-key-vault.md)(Przechowywanie poświadczeń w Azure Key Vault ).

#### <a name="create-a-sink-dataset-and-linked-service"></a>Tworzenie zestawu danych ujścia i połączonej usługi
1. Przejdź do karty **Ujście**, a następnie wybierz pozycję **+ Nowy**, aby utworzyć zestaw danych będący ujściem.

1. W **oknie dialogowym Nowy** zestaw danych wprowadź **SQL** w polu wyszukiwania, aby filtrować łączniki. Wybierz **Azure SQL Database**, a następnie wybierz pozycję **Kontynuuj.** W tym samouczku skopiujesz dane do bazy danych SQL.

1. W **oknie dialogowym Ustawianie** właściwości wprowadź **wartość OutputSqlDataset w** polu **Nazwa**. Z **listy rozwijanej** Połączona usługa wybierz pozycję **+ Nowy.** Zestaw danych musi być skojarzony z połączoną usługą. Połączona usługa ma parametry połączenia, których usługa Data Factory używa do nawiązywania połączenia z usługą SQL Database w środowisku uruchomieniowym. Zestaw danych określa kontener, folder i plik (opcjonalnie), do którego dane są kopiowane.

1. W **oknie dialogowym Nowa połączona usługa (Azure SQL Database)** wybierz następujące kroki:

    1. W obszarze **Nazwa** wprowadź wartość **AzureSqlDatabaseLinkedService**.
    1. W polu **Nazwa serwera** wybierz swoje wystąpienie programu SQL Server.
    1. Upewnij się, że **włączyć opcję Tworzenie interakcyjne.**
    1. W polu **Nazwa bazy danych** wybierz swoją usługę SQL Database.
    1. W polu **Nazwa użytkownika** wprowadź nazwę użytkownika.
    1. W polu **Hasło** wprowadź hasło użytkownika.
    1. Wybierz pozycję **Testuj połączenie**. Powinno to się nie powieść,  ponieważ program SQL Server zezwala na dostęp tylko z wybranych sieci i wymaga Data Factory utworzenia dla niego prywatnego punktu końcowego, który powinien zostać zatwierdzony przed jego użyciem. W komunikacie o błędzie powinien zostać wyświetlony link do tworzenia prywatnego punktu końcowego, który można śledzić w celu utworzenia zarządzanego prywatnego punktu końcowego. Alternatywą jest bezpośrednie przejdź do karty **Zarządzanie** i postępuj zgodnie z instrukcjami w następnej sekcji, aby utworzyć zarządzany prywatny punkt końcowy.
    1. Nie otwieraj okna dialogowego, a następnie przejdź do wybranego serwera SQL.
    1. Postępuj zgodnie z [instrukcjami w tej sekcji,](#approval-of-a-private-link-in-sql-server) aby zatwierdzić link prywatny.
    1. Wstecz do okna dialogowego. Wybierz **Test connection** ponownie, a następnie wybierz **pozycję Utwórz,** aby wdrożyć połączona usługę.

1. Automatycznie przechodzi do okna **dialogowego Ustawianie** właściwości. W obszarze **Tabela** wybierz pozycję **[dbo].[emp]**. Następnie wybierz przycisk **OK**.

1. Przejdź do karty z potokiem i w obszarze Zestaw danych ujścia upewnij się, że wybrano pozycję **OutputSqlDataset.**

    ![Zrzut ekranu przedstawiający kartę Potok.](./media/tutorial-copy-data-portal-private/pipeline-tab-2.png)

Opcjonalnie można zamapować schemat źródła na odpowiedni schemat miejsca docelowego, korzystając z mapowania schematu [w działaniu kopiowania](./copy-activity-schema-and-type-mapping.md).

#### <a name="create-a-managed-private-endpoint"></a>Tworzenie zarządzanego prywatnego punktu końcowego

Jeśli hiperlink nie został wybrane podczas przetestowania połączenia, postępuj zgodnie ze ścieżką. Teraz musisz utworzyć zarządzany prywatny punkt końcowy, który połączysz z utworzoną połączoną usługą.

1. Przejdź do karty **Zarządzanie.**
1. Przejdź do sekcji **Zarządzane prywatne punkty końcowe.**
1. Wybierz **pozycję + Nowy w** obszarze Zarządzane prywatne punkty **końcowe.**

    ![Zrzut ekranu przedstawiający przycisk Nowe zarządzane prywatne punkty końcowe.](./media/tutorial-copy-data-portal-private/new-managed-private-endpoint.png) 

1. Wybierz **kafelek Azure SQL Database** z listy, a następnie wybierz pozycję **Kontynuuj.**
1. Wprowadź nazwę wybranego serwera SQL.
1. Wybierz przycisk **Utwórz**.
1. Po kilku sekundach powinno być widać, że utworzony link prywatny wymaga zatwierdzenia.
1. Wybierz utworzony prywatny punkt końcowy. Zostanie wyświetlony hiperlink, który spowoduje zatwierdzenie prywatnego punktu końcowego na poziomie serwera SQL.


#### <a name="approval-of-a-private-link-in-sql-server"></a>Zatwierdzenie linku prywatnego w SQL Server
1. W programie SQL Server przejdź do sekcji **Połączenia prywatnego punktu końcowego** **w sekcji** Ustawienia.
1. Zaznacz pole wyboru dla utworzonego prywatnego punktu końcowego, a następnie wybierz pozycję **Zatwierdź.**
1. Dodaj opis i wybierz pozycję **Tak.**
1. Wstecz sekcji **Zarządzane prywatne punkty końcowe** na karcie **Zarządzanie** w Data Factory.
1. Zatwierdzenie prywatnego punktu końcowego powinno zająć jedną lub dwie minuty.

#### <a name="debug-and-publish-the-pipeline"></a>Debugowanie i publikowanie potoku

Przed opublikowaniem artefaktów (połączone usługi, zestawy danych i potok) w usłudze Data Factory lub własnym repozytorium Git usługi Azure Repos możesz debugować potok.

1. Aby debugować potok, wybierz na pasku narzędzi pozycję **Debuguj**. Na karcie **Dane wyjściowe** w dolnej części okna wyświetlany jest stan uruchomienia potoku.
1. Po pomyślnym uruchomieniu potoku na górnym pasku narzędzi wybierz pozycję **Opublikuj wszystko.** Ta akcja publikuje utworzone jednostki (zestawy danych i potoki) w Data Factory.
1. Poczekaj na wyświetlenie komunikatu **Pomyślnie opublikowano**. Aby wyświetlić komunikaty powiadomień, wybierz pozycję **Pokaż powiadomienia** w prawym górnym rogu (przycisk dzwonka).


#### <a name="summary"></a>Podsumowanie
Potok w tym przykładzie kopiuje dane z usługi Blob Storage do usługi SQL Database przy użyciu prywatnych punktów końcowych w Data Factory Managed Virtual Network. W tym samouczku omówiono:

* Tworzenie fabryki danych.
* Tworzenie potoku z działaniem kopiowania.