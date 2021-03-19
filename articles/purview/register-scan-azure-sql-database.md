---
title: Rejestruj i Skanuj Azure SQL Database
description: W tym samouczku opisano sposób skanowania Azure SQL Database
author: hophanms
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 10/02/2020
ms.openlocfilehash: 11ce7b3da0d33fbe7f1229cbc00246da00858ec5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104598222"
---
# <a name="register-and-scan-an-azure-sql-database"></a>Rejestrowanie i skanowanie Azure SQL Database

W tym artykule opisano sposób rejestrowania Azure SQL Database źródła danych w programie kontrolą i konfigurowania na nim skanowania.

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Azure SQL Database źródło danych obsługuje następujące funkcje:

- **Pełne i przyrostowe skanowania** do przechwytywania metadanych i klasyfikacji w Azure SQL Database.

-  Elementy powiązane z zasobami danych dla operacji kopiowania i przepływu danych w usłudze ADF.

### <a name="known-limitations"></a>Znane ograniczenia

Usługa Azure kontrolą nie obsługuje skanowania [widoków](/sql/relational-databases/views/views?view=azuresqldb-current&preserve-view=true) w Azure SQL Database.

## <a name="prerequisites"></a>Wymagania wstępne

1. Utwórz nowe konto kontrolą, jeśli jeszcze go nie masz.

1. Dostęp sieciowy między kontem kontrolą a Azure SQL Database.


### <a name="set-up-authentication-for-a-scan"></a>Konfigurowanie uwierzytelniania na potrzeby skanowania

Uwierzytelnianie do skanowania Azure SQL Database. Jeśli musisz utworzyć nowe uwierzytelnianie, musisz [autoryzować dostęp do bazy danych do SQL Database](../azure-sql/database/logins-create-manage.md). Istnieją trzy metody uwierzytelniania, które kontrolą obecnie obsługuje:

- Uwierzytelnianie SQL
- Jednostka usługi
- Tożsamość zarządzana

#### <a name="sql-authentication"></a>Uwierzytelnianie SQL

> [!Note]
> Nowe nazwy logowania można tworzyć tylko w przypadku głównego konta serwera (utworzonego przez proces aprowizacji) lub członkiem `loginmanager` roli bazy danych w bazie danych Master. Po udzieleniu uprawnień trwa około **15 minut** , konto kontrolą powinno mieć odpowiednie uprawnienia do skanowania zasobów.

Możesz postępować zgodnie z instrukcjami w temacie [Tworzenie nazwy logowania](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-current&preserve-view=true#examples-1) , aby utworzyć identyfikator logowania dla Azure SQL Database, jeśli nie jest dostępny. Do następnych kroków będzie potrzebna **Nazwa użytkownika** i **hasło** .

1. Przejdź do magazynu kluczy w Azure Portal
1. Wybierz pozycję **ustawienia > wpisy tajne**
1. Wybierz pozycję **+ Generuj/Importuj** i wprowadź **nazwę** i **wartość** jako *hasło* z Azure SQL Database
1. Wybierz pozycję **Utwórz** , aby zakończyć
1. Jeśli Twój Magazyn kluczy nie jest jeszcze połączony z usługą kontrolą, konieczne będzie [utworzenie nowego połączenia z magazynem kluczy](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)
1. Na koniec [Utwórz nowe poświadczenie](manage-credentials.md#create-a-new-credential) przy użyciu **nazwy użytkownika** i **hasła** w celu skonfigurowania skanowania

#### <a name="service-principal-and-managed-identity"></a>Nazwa główna usługi i zarządzana tożsamość

Istnieje kilka kroków, aby zezwolić kontrolą na użycie jednostki usługi lub **tożsamości zarządzanej** przez usługę kontrolą do skanowania Azure SQL Database

   > [!Note]
   > Aby można było skanować, kontrolą będzie potrzebować **identyfikatora aplikacji** i **klucza tajnego klienta** .

##### <a name="create-or-use-an-existing-service-principal"></a>Utwórz lub Użyj istniejącej nazwy głównej usługi

> [!Note]
> Pomiń ten krok, jeśli używasz **zarządzanej tożsamości** kontrolą

Aby użyć nazwy głównej usługi, można użyć istniejącej lub utworzyć nową. 

> [!Note]
> Jeśli musisz utworzyć nową nazwę główną usługi, wykonaj następujące kroki:
> 1. Przejdź do [Azure Portal](https://portal.azure.com).
> 1. Wybierz **Azure Active Directory** z menu po lewej stronie.
> 1. Wybierz pozycję **Rejestracje aplikacji**.
> 1. Wybierz pozycję **+ Nowa rejestracja aplikacji**.
> 1. Wprowadź nazwę **aplikacji** (nazwę główną usługi).
> 1. Wybierz **konta tylko w tym katalogu organizacji**.
> 1. W obszarze Identyfikator URI przekierowania wybierz pozycję **Sieć Web** i wprowadź dowolny żądany adres URL. nie musi być w rzeczywistości ani działać.
> 1. Następnie wybierz pozycję **Zarejestruj**.

##### <a name="configure-azure-ad-authentication-in-the-database-account"></a>Konfigurowanie uwierzytelniania usługi Azure AD na koncie bazy danych

Nazwa główna usługi lub zarządzana tożsamość musi mieć uprawnienia do pobierania metadanych dla bazy danych, schematów i tabel. Ponadto musi być w stanie zbadać tabele, aby uzyskać przykład klasyfikacji.

- [Konfigurowanie uwierzytelniania usługi Azure AD i zarządzanie nim za pomocą usługi Azure SQL](../azure-sql/database/authentication-aad-configure.md)
- Jeśli używasz tożsamości zarządzanej, konto usługi kontrolą ma swoją własną tożsamość zarządzaną, która stanowi zasadniczo swoją nazwę kontrolą podczas jego tworzenia. Należy utworzyć użytkownika usługi Azure AD w Azure SQL Database z tożsamością zarządzaną kontrolą lub własną jednostką usługi zgodnie z poniższym samouczkiem dotyczącym [tworzenia użytkownika jednostki usługi w programie Azure SQL Database](../azure-sql/database/authentication-aad-service-principal-tutorial.md#create-the-service-principal-user-in-azure-sql-database). Musisz przypisać odpowiednie uprawnienia (np. `db_owner` lub `db_datareader` ) do tożsamości. Przykładowa składnia SQL służąca do tworzenia uprawnień użytkownika i udzielania:

    ```sql
    CREATE USER [Username] FROM EXTERNAL PROVIDER
    GO
    
    EXEC sp_addrolemember 'db_owner', [Username]
    GO
    ```

    > [!Note]
    > `Username`Jest to Twoja nazwa główna usługi lub tożsamość zarządzana kontrolą. Więcej informacji na temat [ról stałych baz danych i ich możliwości](/sql/relational-databases/security/authentication-access/database-level-roles?view=sql-server-ver15&preserve-view=true#fixed-database-roles).
    
##### <a name="add-service-principal-to-key-vault-and-purviews-credential"></a>Dodaj nazwę główną usługi do magazynu kluczy i poświadczenia kontrolą

> [!Note]
> Jeśli planujesz używać **tożsamości zarządzanej** przez program kontrolą, możesz pominąć ten krok, ponieważ zarządzana tożsamość kontrolą jest już w poświadczenie **kontrolą-MSI** .

Wymagane jest uzyskanie identyfikatora aplikacji i klucza tajnego jednostki usługi:

1. Przejdź do nazwy głównej usługi w [Azure Portal](https://portal.azure.com)
1. Skopiuj wartości **Identyfikator aplikacji (klienta)** z **omówienia** i **klucz tajny klienta** z **certyfikatów & wpisy tajne**.
1. Przejdź do magazynu kluczy
1. Wybierz pozycję **ustawienia > wpisy tajne**
1. Wybierz pozycję **+ Generuj/Importuj** i wprowadź wybraną **nazwę** oraz **wartość** jako **klucz tajny klienta** z nazwy głównej usługi
1. Wybierz pozycję **Utwórz** , aby zakończyć
1. Jeśli Twój Magazyn kluczy nie jest jeszcze połączony z usługą kontrolą, konieczne będzie [utworzenie nowego połączenia z magazynem kluczy](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)
1. Na koniec [Utwórz nowe poświadczenie](manage-credentials.md#create-a-new-credential) przy użyciu nazwy głównej usługi, aby skonfigurować skanowanie

### <a name="firewall-settings"></a>Ustawienia zapory

Serwer bazy danych musi zezwalać na włączenie połączeń platformy Azure. Dzięki temu platforma Azure kontrolą będzie mogła nawiązywać połączenia i łączyć się z serwerem. Aby uzyskać informacje na temat [połączeń z platformy Azure](../azure-sql/database/firewall-configure.md#connections-from-inside-azure), możesz skorzystać z przewodnika.

1. Przejdź do konta bazy danych
1. Wybierz nazwę serwera na stronie **Przegląd**
1. Wybierz **zabezpieczenia > zapory i sieci wirtualne**
1. Wybierz  opcję tak **, aby zezwolić usługom i zasobom platformy Azure na dostęp do tego serwera**

    :::image type="content" source="media/register-scan-azure-sql-database/sql-firewall.png" alt-text="Zezwól usługom i zasobom platformy Azure na dostęp do tego serwera." border="true":::
    
> [!Note]
> Obecnie usługa Azure kontrolą nie obsługuje konfiguracji sieci wirtualnej. W związku z tym nie można przeprowadzić ustawień zapory opartych na protokole IP.

## <a name="register-an-azure-sql-database-data-source"></a>Rejestrowanie źródła danych Azure SQL Database

Aby zarejestrować nowe Azure SQL Database w usłudze Data Catalog, wykonaj następujące czynności:

1. Przejdź do konta kontrolą

1. Wybierz **źródła** na lewym pasku nawigacyjnym

1. Wybierz pozycję **zarejestruj**

1. W obszarze **Rejestruj źródła** wybierz pozycję **Azure SQL Database**. Wybierz opcję **Kontynuuj**.

:::image type="content" source="media/register-scan-azure-sql-database/register-new-data-source.png" alt-text="Rejestrowanie nowego źródła danych" border="true":::

Na ekranie **Rejestr sources (Azure SQL Database)** wykonaj następujące czynności:

1. Wprowadź **nazwę** , z którą zostanie wyświetlone źródło danych w wykazie.
1. Wybierz pozycję **z subskrypcji platformy Azure**, wybierz odpowiednią subskrypcję w polu listy rozwijanej **subskrypcja platformy Azure** i odpowiedni serwer z listy rozwijanej **Nazwa serwera** .
1. **Zakończ** , aby zarejestrować źródło danych.

:::image type="content" source="media/register-scan-azure-sql-database/add-azure-sql-database.png" alt-text="opcje rejestrowania źródeł" border="true":::

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

> [!NOTE]
> Usunięcie Twojego skanowania nie powoduje usunięcia zasobów z wcześniejszych Azure SQL Database skanów.
> Jeśli tabela źródłowa zostanie zmieniona i przeskanujesz tabelę źródłową po przeprowadzeniu edycji opisu na karcie schemat elementu kontrolą, zasób nie zostanie już zaktualizowany przy użyciu zmian schematu.

## <a name="next-steps"></a>Następne kroki

- [Przeglądanie wykazu danych usługi Azure kontrolą](how-to-browse-catalog.md)
- [Przeszukaj Data Catalog Azure kontrolą](how-to-search-catalog.md)
