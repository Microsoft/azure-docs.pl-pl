---
title: Udostępnianie i odbieranie danych z usługi Azure SQL Database i usługi Azure Synapse Analytics
description: Dowiedz się, jak udostępniać i odbierać dane z Azure SQL Database i usługi Azure Synapse Analytics
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 02/24/2021
ms.openlocfilehash: f87ad76e9bb1db4d71716bf860d5fee2d413e8e9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101740379"
---
# <a name="share-and-receive-data-from-azure-sql-database-and-azure-synapse-analytics"></a>Udostępnianie i odbieranie danych z usługi Azure SQL Database i usługi Azure Synapse Analytics

[!INCLUDE[appliesto-sql](includes/appliesto-sql.md)]

Udział danych platformy Azure obsługuje funkcję udostępniania opartego na migawce Azure SQL Database i usługi Azure Synapse Analytics. W tym artykule wyjaśniono, jak udostępniać i odbierać dane z tych źródeł.

Udział danych platformy Azure obsługuje udostępnianie tabel i widoków z usług Azure SQL Database i Azure Synapse Analytics (dawniej Azure SQL DW) oraz udostępnianie tabel z dedykowanej puli SQL usługi Azure Synapse Analytics (Workspace). Udostępnianie z puli usługi Azure Synapse Analytics (Workspace) nie jest obecnie obsługiwane. Konsumenci danych mogą zdecydować się na zaakceptowanie danych do Azure Data Lake Storage Gen2 lub Blob Storage platformy Azure jako plików CSV lub Parquet, a także w Azure SQL Database i Azure Synapse Analytics jako tabele.

Gdy akceptujesz dane do Azure Data Lake Store Gen2 lub Azure Blob Storage, pełne migawki zastąpią zawartość pliku docelowego, jeśli już istnieje.
Gdy dane są odbierane do tabeli SQL i jeśli tabela docelowa jeszcze nie istnieje, udział danych platformy Azure tworzy tabelę SQL ze schematem źródłowym. Jeśli tabela docelowa już istnieje o tej samej nazwie, zostanie porzucona i zastąpiona najnowszą pełną migawką. Migawki przyrostowe nie są obecnie obsługiwane.

## <a name="share-data"></a>Udostępnianie danych

### <a name="prerequisites-to-share-data"></a>Wymagania wstępne dotyczące udostępniania danych

* Subskrypcja platformy Azure: Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/) .
* Adres e-mail logowania odbiorcy platformy Azure (przy użyciu aliasu poczty e-mail nie będzie działał).
* Jeśli źródłowy magazyn danych platformy Azure znajduje się w innej subskrypcji platformy Azure niż ta, która będzie używana do tworzenia zasobu udziału danych, zarejestruj [dostawcę zasobów Microsoft. datashare](concepts-roles-permissions.md#resource-provider-registration) w subskrypcji, w której znajduje się magazyn danych platformy Azure. 

### <a name="prerequisites-for-sql-source"></a>Wymagania wstępne dotyczące źródła SQL
Poniżej znajduje się lista wymagań wstępnych dotyczących udostępniania danych ze źródła SQL. 

#### <a name="prerequisites-for-sharing-from-azure-sql-database-or-azure-synapse-analytics-formerly-azure-sql-dw"></a>Wymagania wstępne dotyczące udostępniania z usług Azure SQL Database lub Azure Synapse Analytics (dawniej: Azure SQL DW)
Aby skonfigurować wymagania wstępne, można wykonać [pokaz krok po kroku](https://youtu.be/hIE-TjJD8Dc) .

* Azure SQL Database lub usługa Azure Synapse Analytics (dawniej usługa Azure SQL DW) z tabelami i widokami, które chcesz udostępnić.
* Uprawnienia do zapisu w bazach danych programu SQL Server, które znajdują się w *Microsoft. SQL/serwery/bazy danych/zapis*. To uprawnienie istnieje w roli **Współautor**.
* Uprawnienie do zarządzania tożsamością zasobu udziału danych w celu uzyskania dostępu do bazy danych. Można to zrobić, wykonując następujące czynności: 
    1. W Azure Portal przejdź do serwera SQL i ustaw go jako **administratora Azure Active Directory**.
    1. Nawiązywanie połączenia z usługą Azure SQL Database/magazynem danych przy użyciu [edytora zapytań](../azure-sql/database/connect-query-portal.md#connect-using-azure-active-directory) lub SQL Server Management Studio z uwierzytelnianiem Azure Active Directory. 
    1. Wykonaj Poniższy skrypt, aby dodać tożsamość zarządzaną zasobu udziału danych jako db_datareader. Musisz nawiązać połączenie przy użyciu Active Directory, a nie SQL Server uwierzytelniania. 
    
        ```sql
        create user "<share_acct_name>" from external provider;     
        exec sp_addrolemember db_datareader, "<share_acct_name>"; 
        ```                   
       Należy pamiętać, że *<share_acc_name>* to nazwa zasobu udziału danych. Jeśli zasób udział danych nie został jeszcze utworzony, możesz wrócić do tego wymagania wstępnego później.  

* Użytkownik Azure SQL Database z dostępem **"db_datareader"** do nawigowania i wybierania tabel i/lub widoków, które chcesz udostępnić. 

* SQL Server dostęp do zapory. Można to zrobić, wykonując następujące czynności: 
    1. W Azure Portal przejdź do programu SQL Server. Wybierz *zapory i sieci wirtualne* z nawigacji po lewej stronie.
    1. Kliknij  przycisk tak *, aby zezwolić usługom i zasobom platformy Azure na dostęp do tego serwera*.
    1. Kliknij pozycję **+ Dodaj adres IP klienta**. Adres IP klienta może ulec zmianie. Ten proces może wymagać powtarzania przy następnym udostępnieniu danych SQL z Azure Portal. Możesz również dodać zakres adresów IP.
    1. Kliknij pozycję **Zapisz**. 

#### <a name="prerequisites-for-sharing-from-azure-synapse-analytics-workspace-sql-pool"></a>Wymagania wstępne dotyczące udostępniania z puli SQL usługi Azure Synapse Analytics (Workspace)

* Dedykowana Pula SQL usługi Azure Synapse Analytics z tabelami, które chcesz udostępnić. Udostępnianie widoku nie jest obecnie obsługiwane. Udostępnianie z puli SQL bez serwera nie jest obecnie obsługiwane.
* Uprawnienia do zapisu w puli SQL w obszarze roboczym Synapse, które znajdują się w *witrynie Microsoft. Synapse/Workspaces/Sqlpools/Write*. To uprawnienie istnieje w roli **Współautor**.
* Uprawnienie do zarządzanej tożsamości zasobu udziału danych w celu uzyskania dostępu do puli SQL obszaru roboczego Synapse. Można to zrobić, wykonując następujące czynności: 
    1. W Azure Portal przejdź do obszaru roboczego Synapse. Wybierz pozycję SQL Active Directory administrator na lewym panelu nawigacyjnym i ustaw siebie jako **administratora Azure Active Directory**.
    1. Otwórz program Synapse Studio, wybierz pozycję *Zarządzaj* w lewym obszarze nawigacji. Wybierz pozycję *Kontrola dostępu* w obszarze zabezpieczenia. Przypisz siebie do roli administratora **SQL** lub **obszaru roboczego** .
    1. W programie Synapse Studio wybierz pozycję *programowanie* na lewym pasku nawigacyjnym. Wykonaj następujący skrypt w puli SQL, aby dodać tożsamość zarządzaną zasobu udziału danych jako db_datareader. 
    
        ```sql
        create user "<share_acct_name>" from external provider;     
        exec sp_addrolemember db_datareader, "<share_acct_name>"; 
        ```                   
       Należy pamiętać, że *<share_acc_name>* to nazwa zasobu udziału danych. Jeśli zasób udział danych nie został jeszcze utworzony, możesz wrócić do tego wymagania wstępnego później.  

* Dostęp do zapory obszaru roboczego Synapse. Można to zrobić, wykonując następujące czynności: 
    1. W Azure Portal przejdź do obszaru roboczego Synapse. Wybierz pozycję *zapory* na lewym pasku nawigacyjnym.
    1. Kliknij  pozycję Włącz *, aby zezwolić usługom i zasobom platformy Azure na dostęp do tego obszaru roboczego*.
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

1. Wybierz przycisk **Utwórz**.   

1. Wprowadź szczegółowe informacje o udziale. Określ nazwę, typ udziału, opis zawartości udziału i warunki użytkowania (opcjonalnie). 

    ![EnterShareDetails](./media/enter-share-details.png "Wprowadź szczegóły udostępniania") 

1. Wybierz opcję **Kontynuuj**.

1. Aby dodać zestawy danych do udziału, wybierz pozycję **Dodaj zestawy danych**. 

    ![Dodawanie zestawów danych do udziału](./media/datasets.png "Zestawy danych")

1. Wybierz typ zestawu danych, który chcesz dodać. Zostanie wyświetlona inna lista typów zestawów danych w zależności od typu udziału (migawka lub miejsce w miejscu) wybranej w poprzednim kroku. 

    ![Adddatasets](./media/add-datasets.png "Dodaj zestawy danych")    

1. Wybierz obszar roboczy programu SQL Server lub Synapse, podaj poświadczenia, jeśli zostanie wyświetlony monit, a następnie wybierz pozycję **dalej** , aby przejść do obiektu, który chcesz udostępnić, i wybierz pozycję "Dodaj zestawy danych". Możesz wybrać tabele i widoki z Azure SQL Database i usługi Azure Synapse Analytics (dawniej Azure SQL DW) lub tabel z dedykowanej puli SQL usługi Azure Synapse Analytics (Workspace). 

    ![SelectDatasets](./media/select-datasets-sql.png "Wybierz zestawy danych")    

1. Na karcie adresaci wprowadź adres e-mail odbiorcy danych, wybierając pozycję "+ Dodaj odbiorcę". Adres e-mail musi być adresem e-mail odbiorcy logowania na platformie Azure.

    ![Addrecipients](./media/add-recipient.png "Dodawanie adresatów") 

1. Wybierz opcję **Kontynuuj**.

1. W przypadku wybrania typu udziału migawek można skonfigurować harmonogram migawek w taki sposób, aby dostarczał aktualizacje danych do konsumenta danych. 

    ![EnableSnapshots](./media/enable-snapshots.png "Włącz migawki") 

1. Wybierz czas rozpoczęcia i interwał cyklu. 

1. Wybierz opcję **Kontynuuj**.

1. Na karcie Recenzja + tworzenie przejrzyj zawartość pakietu, ustawienia, adresatów i ustawienia synchronizacji. Wybierz przycisk **Utwórz**.

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

* Konto usługi Azure Storage: Jeśli jeszcze go nie masz, możesz utworzyć [konto usługi Azure Storage](../storage/common/storage-account-create.md). 
* Uprawnienie do zapisu na koncie magazynu, które jest obecne w usłudze *Microsoft. Storage/storageAccounts/Write*. To uprawnienie istnieje w roli **Współautor**. 
* Uprawnienie do dodawania przypisania roli zarządzanej przez zasób udział danych do konta magazynu, które jest obecne w *firmie Microsoft. Autoryzacja/przypisania ról/zapis*. To uprawnienie istnieje w roli **Właściciel**.  

### <a name="prerequisites-for-sql-target"></a>Wymagania wstępne dotyczące programu SQL Target
Jeśli zdecydujesz się na otrzymywanie danych do Azure SQL Database, usługa Azure Synapse Analytics poniżej stanowi listę wymagań wstępnych. 

#### <a name="prerequisites-for-receiving-data-into-azure-sql-database-or-azure-synapse-analytics-formerly-azure-sql-dw"></a>Wymagania wstępne dotyczące otrzymywania danych w usłudze Azure SQL Database lub Azure Synapse Analytics (dawniej: Azure SQL DW)
Aby skonfigurować wymagania wstępne, można wykonać [pokaz krok po kroku](https://youtu.be/aeGISgK1xro) .

* Azure SQL Database lub usługa Azure Synapse Analytics (dawniej usługa Azure SQL DW).
* Uprawnienia do zapisu w bazach danych programu SQL Server, które znajdują się w *Microsoft. SQL/Servers/Databases/Write*. To uprawnienie istnieje w roli **Współautor**. 
* Uprawnienie do zarządzanej tożsamości zasobu udziału danych w celu uzyskania dostępu do Azure SQL Database lub analizy usługi Azure Synapse. Można to zrobić, wykonując następujące czynności: 
    1. W Azure Portal przejdź do serwera SQL i ustaw go jako **administratora Azure Active Directory**.
    1. Nawiązywanie połączenia z usługą Azure SQL Database/magazynem danych przy użyciu [edytora zapytań](../azure-sql/database/connect-query-portal.md#connect-using-azure-active-directory) lub SQL Server Management Studio z uwierzytelnianiem Azure Active Directory. 
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
    1. Kliknij  przycisk tak *, aby zezwolić usługom i zasobom platformy Azure na dostęp do tego serwera*.
    1. Kliknij pozycję **+ Dodaj adres IP klienta**. Adres IP klienta może ulec zmianie. Ten proces może wymagać powtarzania przy następnym udostępnieniu danych SQL z Azure Portal. Możesz również dodać zakres adresów IP.
    1. Kliknij pozycję **Zapisz**. 
 
#### <a name="prerequisites-for-receiving-data-into-azure-synapse-analytics-workspace-sql-pool"></a>Wymagania wstępne dotyczące otrzymywania danych w puli SQL usługi Azure Synapse Analytics (Workspace)

* Dedykowana Pula SQL usługi Azure Synapse Analytics (Workspace). Otrzymywanie danych do puli SQL bez serwera nie jest obecnie obsługiwane.
* Uprawnienia do zapisu w puli SQL w obszarze roboczym Synapse, które znajdują się w *witrynie Microsoft. Synapse/Workspaces/Sqlpools/Write*. To uprawnienie istnieje w roli **Współautor**.
* Uprawnienie do zarządzanej tożsamości zasobu udziału danych w celu uzyskania dostępu do puli SQL obszaru roboczego Synapse. Można to zrobić, wykonując następujące czynności: 
    1. W Azure Portal przejdź do obszaru roboczego Synapse. Wybierz pozycję SQL Active Directory administrator na lewym panelu nawigacyjnym i ustaw siebie jako **administratora Azure Active Directory**.
    1. Otwórz program Synapse Studio, wybierz pozycję *Zarządzaj* w lewym obszarze nawigacji. Wybierz pozycję *Kontrola dostępu* w obszarze zabezpieczenia. Przypisz siebie do roli administratora **SQL** lub **obszaru roboczego** .
    1. W programie Synapse Studio wybierz pozycję *programowanie* na lewym pasku nawigacyjnym. Wykonaj następujący skrypt w puli SQL, aby dodać tożsamość zarządzaną zasobu udziału danych jako "db_datareader, db_datawriter, db_ddladmin". 
    
        ```sql
        create user "<share_acc_name>" from external provider; 
        exec sp_addrolemember db_datareader, "<share_acc_name>"; 
        exec sp_addrolemember db_datawriter, "<share_acc_name>"; 
        exec sp_addrolemember db_ddladmin, "<share_acc_name>";
        ```                   
       Należy pamiętać, że *<share_acc_name>* to nazwa zasobu udziału danych. Jeśli zasób udział danych nie został jeszcze utworzony, możesz wrócić do tego wymagania wstępnego później.  

* Dostęp do zapory obszaru roboczego Synapse. Można to zrobić, wykonując następujące czynności: 
    1. W Azure Portal przejdź do obszaru roboczego Synapse. Wybierz pozycję *zapory* na lewym pasku nawigacyjnym.
    1. Kliknij  pozycję Włącz *, aby zezwolić usługom i zasobom platformy Azure na dostęp do tego obszaru roboczego*.
    1. Kliknij pozycję **+ Dodaj adres IP klienta**. Adres IP klienta może ulec zmianie. Ten proces może wymagać powtarzania przy następnym udostępnieniu danych SQL z Azure Portal. Możesz również dodać zakres adresów IP.
    1. Kliknij pozycję **Zapisz**. 

### <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

### <a name="open-invitation"></a>Otwórz zaproszenie

1. Możesz otworzyć zaproszenie z poczty e-mail lub bezpośrednio z Azure Portal. 

   Aby otworzyć zaproszenie z poczty e-mail, sprawdź skrzynkę odbiorczą dla zaproszenia od dostawcy danych. Zaproszenie pochodzi z Microsoft Azure, zatytułowane **zaproszenie <yourdataprovider@domain.com> udziału danych platformy Azure**. Kliknij pozycję **Wyświetl zaproszenie** , aby zobaczyć zaproszenie na platformie Azure. 

   Aby otworzyć zaproszenie z Azure Portal bezpośrednio, Wyszukaj **zaproszenia udziału danych** w Azure Portal. Spowoduje to przejście do listy zaproszeń udziału danych.

   ![Lista zaproszeń](./media/invitations.png "Lista zaproszeń") 

1. Wybierz udział, który chcesz wyświetlić. 

### <a name="accept-invitation"></a>Zaakceptuj zaproszenie
1. Upewnij się, że wszystkie pola są przeglądane, w tym **warunki użytkowania**. Jeśli akceptujesz warunki użytkowania, musisz zaznaczyć pole wyboru, aby wskazać, że zgadzasz się. 

   ![Warunki użytkowania](./media/terms-of-use.png "Warunki użytkowania") 

1. W obszarze *docelowe konto udziału danych* wybierz subskrypcję i grupę zasobów, w ramach której będziesz wdrażać udział danych. 

   W polu **konto udostępniania danych** wybierz pozycję **Utwórz nową** , jeśli nie masz istniejącego konta udziału danych. W przeciwnym razie wybierz istniejące konto udziału danych, do którego chcesz zaakceptować udział danych. 

   W polu **Nazwa otrzymanego udziału** można pozostawić wartość domyślną określoną przez dane lub określić nową nazwę dla odebranego udziału. 

   Po uzgodnieniu warunków użytkowania i określeniu konta udziału danych do zarządzania odebranym udziałem wybierz pozycję **Zaakceptuj i skonfiguruj**. Zostanie utworzona subskrypcja udziału. 

   ![Zaakceptuj opcje](./media/accept-options.png "Zaakceptuj opcje") 

   Spowoduje to przejście do otrzymanego udziału na koncie udziału danych. 

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

1. Możesz wyzwolić migawkę, wybierając kartę **szczegóły** , a następnie pozycję **Wyzwalaj migawkę**. Tutaj można wyzwolić pełną lub przyrostową migawkę danych. Jeśli po raz pierwszy otrzymujesz dane od dostawcy danych, wybierz pozycję pełna kopia. W przypadku źródeł SQL obsługiwana jest tylko pełna migawka. Gdy wykonywana jest migawka, kolejne migawki nie rozpocznie się aż do ukończenia poprzedniej.

   ![Wyzwalanie migawki](./media/trigger-snapshot.png "Wyzwalanie migawki") 

1. Po *pomyślnym* zakończeniu ostatniego uruchomienia Przejdź do docelowego magazynu danych, aby wyświetlić odebrane dane. Wybierz pozycję **zestawy danych**, a następnie kliknij link w ścieżce docelowej. 

   ![Zestawy danych dla odbiorców](./media/consumer-datasets.png "Mapowanie zestawu danych klienta") 

### <a name="view-history"></a>Wyświetlanie historii
Ten krok dotyczy tylko udostępniania opartego na migawce. Aby wyświetlić historię migawek, wybierz pozycję Karta **historia** . W tym miejscu znajdziesz historię wszystkich migawek, które zostały wygenerowane w ciągu ostatnich 30 dni. 

## <a name="supported-data-types"></a>Obsługiwane typy danych
Po udostępnieniu danych ze źródła SQL następujące mapowanie są używane z SQL Server typów danych do danych tymczasowych usługi Azure Data Share podczas procesu tworzenia migawek. 

| Typ danych SQL Server | Typ danych tymczasowych udziałów danych platformy Azure |
|:--- |:--- |
| bigint |Int64 |
| binarny |Byte [] |
| bit |Wartość logiczna |
| char |String, Char [] |
| data |DateTime |
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
> 1.  Jeśli udostępniasz dane z usługi Azure SQL Database w usłudze Azure Synapse Analytics, nie wszystkie typy danych są obsługiwane. Aby uzyskać szczegółowe informacje, zobacz [typy danych tabeli w dedykowanej puli SQL](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-tables-data-types.md) . 

## <a name="sql-always-encrypted-or-dynamic-data-masking"></a>SQL Always Encrypted lub dynamiczne maskowanie danych
Obecnie udział danych platformy Azure nie obsługuje baz danych usługi Azure SQL, w których skonfigurowano Always Encrypted. 

W przypadku źródłowych tabel SQL z dynamiczną maską danych dane będą wyświetlane na stronie adresatów.

## <a name="sql-snapshot-performance"></a>Wydajność migawek SQL
Na wydajność migawki SQL ma wpływ wiele czynników. Zawsze zaleca się przeprowadzanie własnych testów wydajnościowych. Poniżej przedstawiono przykładowe czynniki wpływające na wydajność.

* Konfiguracja sprzętu (np. rdzeni wirtualnych, Memory, jednostek dwu) źródłowego i docelowego magazynu danych SQL. 
* Współbieżny dostęp do źródłowych i docelowych magazynów danych. Jeśli udostępniasz wiele tabel i widoków z tego samego magazynu danych SQL lub otrzymasz wiele tabel i widoków do tego samego magazynu danych SQL, wpłynie to na wydajność.   
* Lokalizacja źródłowych i docelowych magazynów danych. 

## <a name="troubleshoot-sql-snapshot-failure"></a>Rozwiązywanie problemów z niepowodzeniem migawek SQL
Najczęstszym powodem błędu migawki jest to, że udział danych nie ma uprawnień do źródłowego lub docelowego magazynu danych. Aby udzielić uprawnienia do udostępniania danych źródłowej lub docelowej Azure SQL Database lub usługi Azure Synapse Analytics (dawniej usługa Azure SQL DW), należy uruchomić podany skrypt SQL podczas łączenia się z bazą danych SQL przy użyciu uwierzytelniania Azure Active Directory. Aby rozwiązać problem z dodatkowym błędem migawki SQL, zobacz [Rozwiązywanie problemów z błędem migawki](data-share-troubleshoot.md#snapshots).

## <a name="next-steps"></a>Następne kroki
Wiesz już, jak udostępniać i odbierać dane ze źródeł SQL za pomocą usługi udziału danych platformy Azure. Aby dowiedzieć się więcej o udostępnianiu z innych źródeł danych, przejdź do [obsługiwanych magazynów danych](supported-data-stores.md).