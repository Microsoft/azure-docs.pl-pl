---
title: Udostępnianie i odbieranie danych z usługi Azure SQL Database i usługi Azure Synapse Analytics
description: Dowiedz się, jak udostępniać i odbierać dane z Azure SQL Database i usługi Azure Synapse Analytics
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 10/02/2020
ms.openlocfilehash: 3f243a1a8d4f4b3ee4688ac3942debee5282a9a4
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2020
ms.locfileid: "91761927"
---
# <a name="share-and-receive-data-from-azure-sql-database-and-azure-synapse-analytics"></a>Udostępnianie i odbieranie danych z usługi Azure SQL Database i usługi Azure Synapse Analytics

[!INCLUDE[appliesto-sql](includes/appliesto-sql.md)]

Udział danych platformy Azure obsługuje funkcję udostępniania opartego na migawce Azure SQL Database i usługi Azure Synapse Analytics (dawniej Azure SQL DW). W tym artykule wyjaśniono, jak udostępniać i odbierać dane z tych źródeł.

Udział danych platformy Azure obsługuje udostępnianie tabel lub widoków z usług Azure SQL Database i Azure Synapse Analytics (dawniej Azure SQL DW). Konsumenci danych mogą zdecydować się na zaakceptowanie danych do Azure Data Lake Storage Gen2 lub Blob Storage platformy Azure jako plików CSV lub Parquet, a także w Azure SQL Database i Azure Synapse Analytics jako tabele.

Gdy akceptujesz dane do Azure Data Lake Store Gen2 lub Azure Blob Storage, pełne migawki zastąpią zawartość pliku docelowego, jeśli już istnieje.
Gdy dane są odbierane do tabeli, a tabela docelowa jeszcze nie istnieje, udział danych platformy Azure tworzy tabelę SQL ze schematem źródłowym. Jeśli tabela docelowa już istnieje o tej samej nazwie, zostanie porzucona i zastąpiona najnowszą pełną migawką. Migawki przyrostowe nie są obecnie obsługiwane.

## <a name="share-data"></a>Udostępnianie danych

### <a name="prerequisites-to-share-data"></a>Wymagania wstępne dotyczące udostępniania danych

* Subskrypcja platformy Azure: Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/) .
* Adres e-mail logowania odbiorcy platformy Azure (przy użyciu aliasu poczty e-mail nie będzie działał).
* Jeśli źródłowy magazyn danych platformy Azure znajduje się w innej subskrypcji platformy Azure niż ta, która będzie używana do tworzenia zasobu udziału danych, zarejestruj [dostawcę zasobów Microsoft. datashare](concepts-roles-permissions.md#resource-provider-registration) w subskrypcji, w której znajduje się magazyn danych platformy Azure. 

### <a name="prerequisites-for-sql-source"></a>Wymagania wstępne dotyczące źródła SQL
Poniżej znajduje się lista wymagań wstępnych dotyczących udostępniania danych ze źródła SQL. Możesz również wykonać [pokaz krok po kroku](https://youtu.be/hIE-TjJD8Dc) , aby skonfigurować wymagania wstępne.

* Azure SQL Database lub usługa Azure Synapse Analytics (wcześniej SQL Data Warehouse) z tabelami i widokami, które chcesz udostępnić.
* Uprawnienia do zapisu w bazach danych programu SQL Server, które znajdują się w *Microsoft. SQL/serwery/bazy danych/zapis*. To uprawnienie istnieje w roli Współautor.
* Uprawnienie do udziału danych w celu uzyskania dostępu do magazynu danych. Można to zrobić, wykonując następujące czynności: 
    1. W Azure Portal przejdź do serwera SQL i ustaw go jako administratora Azure Active Directory.
    1. Nawiązywanie połączenia z usługą Azure SQL Database/magazynem danych przy użyciu [edytora zapytań](https://docs.microsoft.com/azure/azure-sql/database/connect-query-portal#connect-using-azure-active-directory) lub SQL Server Management Studio z uwierzytelnianiem Azure Active Directory. 
    1. Wykonaj Poniższy skrypt, aby dodać tożsamość zarządzaną zasobu udziału danych jako db_datareader. Musisz nawiązać połączenie przy użyciu Active Directory, a nie SQL Server uwierzytelniania. 
    
        ```sql
        create user "<share_acct_name>" from external provider;     
        exec sp_addrolemember db_datareader, "<share_acct_name>"; 
        ```                   
       Należy pamiętać, że *<share_acc_name>* to nazwa zasobu udziału danych. Jeśli zasób udział danych nie został jeszcze utworzony, możesz wrócić do tego wymagania wstępnego później.  

* Użytkownik Azure SQL Database z dostępem "db_datareader" do nawigowania i wybierania tabel i/lub widoków, które chcesz udostępnić. 

* SQL Server dostęp do zapory. Można to zrobić, wykonując następujące czynności: 
    1. W programie SQL Server w Azure Portal przejdź do *zapór i sieci wirtualnych*
    1. Kliknij **Yes** przycisk tak *, aby zezwolić usługom i zasobom platformy Azure na dostęp do tego serwera*.
    1. Kliknij pozycję **+ Dodaj adres IP klienta**. Adres IP klienta może ulec zmianie. Ten proces może wymagać powtarzania przy następnym udostępnieniu danych SQL z Azure Portal. Możesz również dodać zakres adresów IP.
    1. Kliknij pozycję **Zapisz**. 

### <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

### <a name="create-a-data-share-account"></a>Tworzenie konta udziału danych

Utwórz zasób udziału danych platformy Azure w grupie zasobów platformy Azure.

1. Wybierz przycisk menu w lewym górnym rogu portalu, a następnie wybierz pozycję **Utwórz zasób** (+).

1. Wyszukaj *udział danych*.

1. Wybierz pozycję udział danych i wybierz pozycję **Utwórz**.

1. Wypełnij podstawowe szczegóły zasobu udziału danych platformy Azure, korzystając z poniższych informacji. 

     **Ustawienie** | **Sugerowana wartość** | **Opis pola**
    |---|---|---|
    | Subskrypcja | Twoja subskrypcja | Wybierz subskrypcję platformy Azure, która ma być używana dla konta udziału danych.|
    | Grupa zasobów | *Testuj grupę zasobów* | Użyj istniejącej grupy zasobów lub utwórz nową. |
    | Lokalizacja | *Wschodnie stany USA 2* | Wybierz region dla konta udziału danych.
    | Nazwa | *datashareaccount* | Określ nazwę konta udziału danych. |
    | | |

1. Wybierz pozycję **Przegląd + Utwórz**, a następnie pozycję **Utwórz** , aby zainicjować obsługę konta udziału danych. Inicjowanie obsługi nowego konta udziału danych zwykle trwa około 2 minuty. 

1. Po zakończeniu wdrażania wybierz pozycję **Przejdź do zasobu**.

### <a name="create-a-share"></a>Tworzenie udziału

1. Przejdź do strony Przegląd udostępniania danych.

    ![Udostępnianie danych](./media/share-receive-data.png "Udostępnianie danych") 

1. Wybierz pozycję **Rozpocznij udostępnianie danych**.

1. Wybierz pozycję **Utwórz**.   

1. Wprowadź szczegółowe informacje o udziale. Określ nazwę, typ udziału, opis zawartości udziału i warunki użytkowania (opcjonalnie). 

    ![EnterShareDetails](./media/enter-share-details.png "Wprowadź szczegóły udostępniania") 

1. Wybierz pozycję **Continue** (Kontynuuj).

1. Aby dodać zestawy danych do udziału, wybierz pozycję **Dodaj zestawy danych**. 

    ![Dodawanie zestawów danych do udziału](./media/datasets.png "Zestawy danych")

1. Wybierz typ zestawu danych, który chcesz dodać. Zostanie wyświetlona inna lista typów zestawów danych w zależności od typu udziału (migawka lub miejsce w miejscu) wybranej w poprzednim kroku. 

    ![Adddatasets](./media/add-datasets.png "Dodaj zestawy danych")    

1. Wybierz serwer SQL, podaj poświadczenia i kliknij przycisk **dalej** , aby przejść do obiektu, który chcesz udostępnić, i wybierz pozycję "Dodaj zestawy danych". 

    ![SelectDatasets](./media/select-datasets-sql.png "Wybierz zestawy danych")    

1. Na karcie adresaci wprowadź adres e-mail odbiorcy danych, wybierając pozycję "+ Dodaj odbiorcę". 

    ![Addrecipients](./media/add-recipient.png "Dodawanie adresatów") 

1. Wybierz pozycję **Continue** (Kontynuuj).

1. W przypadku wybrania typu udziału migawek można skonfigurować harmonogram migawek w taki sposób, aby dostarczał aktualizacje danych do konsumenta danych. 

    ![EnableSnapshots](./media/enable-snapshots.png "Włącz migawki") 

1. Wybierz czas rozpoczęcia i interwał cyklu. 

1. Wybierz pozycję **Continue** (Kontynuuj).

1. Na karcie Recenzja + tworzenie przejrzyj zawartość pakietu, ustawienia, adresatów i ustawienia synchronizacji. Wybierz pozycję **Utwórz**.

Udział danych platformy Azure został utworzony, a odbiorca Twojego udziału danych jest teraz gotowy do zaakceptowania Twojego zaproszenia. 

## <a name="receive-data"></a>Odbierz dane

### <a name="prerequisites-to-receive-data"></a>Wymagania wstępne dotyczące odbierania danych
Przed zaakceptowaniem zaproszenia do udziału danych musisz udostępnić kilka zasobów platformy Azure, które są wymienione poniżej. 

Przed zaakceptowaniem zaproszenia udziału danych upewnij się, że wszystkie wymagania wstępne zostały ukończone. 

* Subskrypcja platformy Azure: Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/) .
* Zaproszenie do udziału danych: zaproszenie od Microsoft Azure z podmiotem zatytułowanym "zaproszenie do udziału danych platformy Azure **<yourdataprovider@domain.com>** ".
* Zarejestruj [dostawcę zasobów Microsoft. datashare](concepts-roles-permissions.md#resource-provider-registration) w subskrypcji platformy Azure, w której utworzysz zasób udziału danych i subskrypcję platformy Azure, w której znajdują się docelowe magazyny danych platformy Azure.

### <a name="prerequisites-for-target-storage-account"></a>Wymagania wstępne dotyczące docelowego konta magazynu
W przypadku wybrania opcji odbierania danych do usługi Azure Storage poniżej znajduje się lista wymagań wstępnych.

* Konto usługi Azure Storage: Jeśli jeszcze go nie masz, możesz utworzyć [konto usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account). 
* Uprawnienie do zapisu na koncie magazynu, które jest obecne w usłudze *Microsoft. Storage/storageAccounts/Write*. To uprawnienie istnieje w roli Współautor. 
* Uprawnienie do dodawania przypisania roli do konta magazynu, które jest obecne w *firmie Microsoft. Autoryzacja/przypisania ról/zapis*. To uprawnienie istnieje w roli Właściciel.  

### <a name="prerequisites-for-sql-target"></a>Wymagania wstępne dotyczące programu SQL Target
Jeśli zdecydujesz się na otrzymywanie danych do Azure SQL Database, usługa Azure Synapse Analytics poniżej stanowi listę wymagań wstępnych. Możesz również wykonać [pokaz krok po kroku](https://youtu.be/aeGISgK1xro) , aby skonfigurować wymagania wstępne.

* Uprawnienia do zapisu w bazach danych programu SQL Server, które znajdują się w *Microsoft. SQL/Servers/Databases/Write*. To uprawnienie istnieje w roli Współautor. 
* Uprawnienie do zarządzanej tożsamości zasobu udziału danych w celu uzyskania dostępu do Azure SQL Database lub analizy usługi Azure Synapse. Można to zrobić, wykonując następujące czynności: 
    1. W Azure Portal przejdź do serwera SQL i ustaw go jako administratora Azure Active Directory.
    1. Nawiązywanie połączenia z usługą Azure SQL Database/magazynem danych przy użyciu [edytora zapytań](https://docs.microsoft.com/azure/azure-sql/database/connect-query-portal#connect-using-azure-active-directory) lub SQL Server Management Studio z uwierzytelnianiem Azure Active Directory. 
    1. Wykonaj następujący skrypt, aby dodać tożsamość zarządzaną udziału danych jako "db_datareader, db_datawriter, db_ddladmin". Musisz nawiązać połączenie przy użyciu Active Directory, a nie SQL Server uwierzytelniania. 

        ```sql
        create user "<share_acc_name>" from external provider; 
        exec sp_addrolemember db_datareader, "<share_acc_name>"; 
        exec sp_addrolemember db_datawriter, "<share_acc_name>"; 
        exec sp_addrolemember db_ddladmin, "<share_acc_name>";
        ```      
        Należy pamiętać, że *<share_acc_name>* to nazwa zasobu udziału danych. Jeśli zasób udział danych nie został jeszcze utworzony, możesz wrócić do tego wymagania wstępnego później.         

* SQL Server dostęp do zapory. Można to zrobić, wykonując następujące czynności: 
    1. W programie SQL Server w Azure Portal przejdź do *zapór i sieci wirtualnych*
    1. Kliknij **Yes** przycisk tak *, aby zezwolić usługom i zasobom platformy Azure na dostęp do tego serwera*.
    1. Kliknij pozycję **+ Dodaj adres IP klienta**. Adres IP klienta może ulec zmianie. Ten proces może wymagać powtarzania przy następnym udostępnieniu danych SQL z Azure Portal. Możesz również dodać zakres adresów IP.
    1. Kliknij pozycję **Zapisz**. 

### <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

### <a name="open-invitation"></a>Otwórz zaproszenie

1. Możesz otworzyć zaproszenie z poczty e-mail lub bezpośrednio z Azure Portal. 

   Aby otworzyć zaproszenie z poczty e-mail, sprawdź skrzynkę odbiorczą dla zaproszenia od dostawcy danych. Zaproszenie pochodzi z Microsoft Azure, zatytułowane **zaproszenie <yourdataprovider@domain.com> udziału danych platformy Azure **. Kliknij pozycję **Wyświetl zaproszenie** , aby zobaczyć zaproszenie na platformie Azure. 

   Aby otworzyć zaproszenie z Azure Portal bezpośrednio, Wyszukaj **zaproszenia udziału danych** w Azure Portal. Spowoduje to przejście do listy zaproszeń udziału danych.

   ![Lista zaproszeń](./media/invitations.png "Lista zaproszeń") 

1. Wybierz udział, który chcesz wyświetlić. 

### <a name="accept-invitation"></a>Zaakceptuj zaproszenie
1. Upewnij się, że wszystkie pola są przeglądane, w tym **warunki użytkowania**. Jeśli akceptujesz warunki użytkowania, musisz zaznaczyć pole wyboru, aby wskazać, że zgadzasz się. 

   ![Warunki użytkowania](./media/terms-of-use.png "Warunki użytkowania") 

1. W obszarze *docelowe konto udziału danych*wybierz subskrypcję i grupę zasobów, w ramach której będziesz wdrażać udział danych. 

   W polu **konto udostępniania danych** wybierz pozycję **Utwórz nową** , jeśli nie masz istniejącego konta udziału danych. W przeciwnym razie wybierz istniejące konto udziału danych, do którego chcesz zaakceptować udział danych. 

   W polu **Nazwa otrzymanego udziału** można pozostawić wartość domyślną określoną przez dane lub określić nową nazwę dla odebranego udziału. 

   Po uzgodnieniu warunków użytkowania i określeniu konta udziału danych do zarządzania odebranym udziałem wybierz pozycję **Zaakceptuj i skonfiguruj**. Zostanie utworzona subskrypcja udziału. 

   ![Zaakceptuj opcje](./media/accept-options.png "Zaakceptuj opcje") 

   Spowoduje to przejście do otrzymanego udziału w Twoim koncie udostępniania danych. 

   Jeśli nie chcesz zaakceptować zaproszenia, wybierz pozycję *Odrzuć*. 

### <a name="configure-received-share"></a>Konfiguruj odebrany udział
Wykonaj poniższe kroki, aby skonfigurować miejsce, w którym chcesz otrzymywać dane.

1. Wybierz kartę **zestawy danych** . Zaznacz pole wyboru obok zestawu danych, do którego chcesz przypisać miejsce docelowe. Wybierz pozycję **+ Mapuj, aby** wybrać docelowy magazyn danych. 

   ![Mapuj do elementu docelowego](./media/dataset-map-target.png "Mapuj do elementu docelowego") 

1. Wybierz docelowy magazyn danych, w którym chcesz umieścić dane. Wszystkie pliki danych lub tabele w docelowym magazynie danych o tej samej ścieżce i nazwie zostaną zastąpione. 

   ![Docelowe konto magazynu](./media/dataset-map-target-sql.png "Docelowy magazyn danych") 

1. W przypadku udostępniania opartego na migawce, jeśli dostawca danych utworzył harmonogram migawek w celu zapewnienia regularnej aktualizacji danych, można również włączyć harmonogram migawek, wybierając kartę **harmonogram migawek** . Zaznacz pole wyboru obok harmonogramu migawek i wybierz pozycję **+ Włącz**.

   ![Włącz harmonogram migawek](./media/enable-snapshot-schedule.png "Włącz harmonogram migawek")

### <a name="trigger-a-snapshot"></a>Wyzwalanie migawki
Te kroki dotyczą tylko udostępniania opartego na migawce.

1. Możesz wyzwolić migawkę, wybierając kartę **szczegóły** , a następnie pozycję **Wyzwalaj migawkę**. Tutaj można wyzwolić pełną lub przyrostową migawkę danych. Jeśli po raz pierwszy otrzymujesz dane od dostawcy danych, wybierz pozycję pełna kopia. W przypadku źródeł SQL obsługiwana jest tylko pełna migawka.

   ![Wyzwalanie migawki](./media/trigger-snapshot.png "Wyzwalanie migawki") 

1. Po *pomyślnym*zakończeniu ostatniego uruchomienia Przejdź do docelowego magazynu danych, aby wyświetlić odebrane dane. Wybierz pozycję **zestawy danych**, a następnie kliknij link w ścieżce docelowej. 

   ![Zestawy danych dla odbiorców](./media/consumer-datasets.png "Mapowanie zestawu danych klienta") 

### <a name="view-history"></a>Wyświetlanie historii
Ten krok dotyczy tylko udostępniania opartego na migawce. Aby wyświetlić historię migawek, wybierz pozycję Karta **historia** . W tym miejscu znajdziesz historię wszystkich migawek, które zostały wygenerowane w ciągu ostatnich 30 dni. 

## <a name="supported-data-types"></a>Obsługiwane typy danych
Po udostępnieniu danych ze źródła SQL następujące mapowanie są używane z SQL Server typów danych do danych tymczasowych usługi Azure Data Share podczas procesu tworzenia migawek. 

| Typ danych SQL Server | Typ danych tymczasowych udziałów danych platformy Azure |
|:--- |:--- |
| bigint |Int64 |
| binarny |Byte [] |
| bit |Boolean (wartość logiczna) |
| char |String, Char [] |
| date |DateTime |
| Datetime (data/godzina) |DateTime |
| datetime2 |DateTime |
| DateTimeOffset |DateTimeOffset |
| Liczba dziesiętna |Liczba dziesiętna |
| FILESTREAM — atrybut (varbinary (max)) |Byte [] |
| Float |Double |
| image (obraz) |Byte [] |
| int |Int32 |
| pieniędzy |Liczba dziesiętna |
| nchar |String, Char [] |
| ntext |String, Char [] |
| numeryczne |Liczba dziesiętna |
| nvarchar |String, Char [] |
| liczba rzeczywista |Pojedynczy |
| rowversion |Byte [] |
| smalldatetime |DateTime |
| smallint |Int16 |
| smallmoney |Liczba dziesiętna |
| sql_variant |Obiekt |
| tekst |String, Char [] |
| time |przedział_czasu |
| sygnatura czasowa |Byte [] |
| tinyint |Int16 |
| uniqueidentifier |Guid (identyfikator GUID) |
| varbinary |Byte [] |
| varchar |String, Char [] |
| xml |Ciąg |

>[!NOTE]
> 1. W przypadku typów danych, które są mapowane na typ pośredni dziesiętnego, obecnie migawka obsługuje dokładność do 28. Jeśli masz dane wymagające dokładności większej niż 28, Rozważ przekonwertowanie na ciąg. 
> 1.  Jeśli udostępniasz dane z usługi Azure SQL Database w usłudze Azure Synapse Analytics, nie wszystkie typy danych są obsługiwane. Aby uzyskać szczegółowe informacje, zobacz [typy danych tabeli w puli SQL Synapse](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-tables-data-types) . 


## <a name="next-steps"></a>Następne kroki
Wiesz już, jak udostępniać i odbierać dane z konta magazynu przy użyciu usługi Azure Data Share Service. Aby dowiedzieć się więcej o udostępnianiu z innych źródeł danych, przejdź do [obsługiwanych magazynów danych](supported-data-stores.md).

