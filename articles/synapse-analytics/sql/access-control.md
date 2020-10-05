---
title: Zarządzanie dostępem do obszarów roboczych, danych i potoków
description: Dowiedz się, jak zarządzać kontrolą dostępu do obszarów roboczych, danych i potoków w obszarze roboczym usługi Azure Synapse Analytics (wersja zapoznawcza).
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: a2e6111f2df5a8d7334a85ec5b6a9e514368ad6c
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91289484"
---
# <a name="manage-access-to-workspaces-data-and-pipelines"></a>Zarządzanie dostępem do obszarów roboczych, danych i potoków

Dowiedz się, jak zarządzać kontrolą dostępu do obszarów roboczych, danych i potoków w obszarze roboczym usługi Azure Synapse Analytics (wersja zapoznawcza).

> [!NOTE]
> W odniesieniu do usługi RBAC będzie bardziej opracowywany poprzez wprowadzenie ról platformy Azure specyficznych dla Synapse

## <a name="access-control-for-workspace"></a>Access Control dla obszaru roboczego

W przypadku wdrożenia produkcyjnego w obszarze roboczym usługi Azure Synapse sugerujemy organizowanie środowiska, aby ułatwić udostępnianie użytkownikom i administratorom.

> [!NOTE]
> Podejście to należy utworzyć kilka grup zabezpieczeń, a następnie skonfigurować obszar roboczy tak, aby korzystał z nich spójnie. Po skonfigurowaniu grup administrator musi tylko zarządzać członkostwem w grupach zabezpieczeń.

### <a name="step-1-set-up-security-groups-with-names-following-this-pattern"></a>Krok 1. Konfigurowanie grup zabezpieczeń przy użyciu nazw następujących po tym wzorcu

1. Utwórz grupę zabezpieczeń o nazwie `Synapse_WORKSPACENAME_Users`
2. Utwórz grupę zabezpieczeń o nazwie `Synapse_WORKSPACENAME_Admins`
3. Dodanie argumentu `Synapse_WORKSPACENAME_Admins` do polecenia `Synapse_WORKSPACENAME_Users`

> [!NOTE]
> Dowiedz się, jak utworzyć grupę zabezpieczeń w [tym artykule](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal).
>
> Dowiedz się, jak dodać grupę zabezpieczeń z innej grupy zabezpieczeń w [tym artykule](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-membership-azure-portal).
>
> WORKSPACEname — należy zamienić tę część na rzeczywistą nazwę obszaru roboczego.

### <a name="step-2-prepare-the-default-adls-gen2-account"></a>Krok 2: przygotowanie domyślnego konta ADLS Gen2

Po aprowizacji obszaru roboczego należy wybrać konto [Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction) i kontener dla systemu plików, który będzie używany przez obszar roboczy.

1. Otwórz witrynę [Azure Portal](https://portal.azure.com).
2. Przejdź do konta Azure Data Lake Storage Gen2
3. Przejdź do kontenera (systemu plików) wybranego dla obszaru roboczego usługi Azure Synapse
4. Wybierz **Access Control (IAM)**
5. Przypisz następujące role:
   1. Rola **czytelnika** :`Synapse_WORKSPACENAME_Users`
   2. Rola **właściciela danych obiektów blob magazynu** :`Synapse_WORKSPACENAME_Admins`
   3. Rola **współautor danych obiektów blob magazynu** :`Synapse_WORKSPACENAME_Users`
   4. Rola **właściciela danych obiektów blob magazynu** :`WORKSPACENAME`

> [!NOTE]
> WORKSPACEname — należy zamienić tę część na rzeczywistą nazwę obszaru roboczego.

### <a name="step-3-configure-the-workspace-admin-list"></a>Krok 3. Konfigurowanie listy administratorów obszaru roboczego

1. Przejdź do [ **interfejsu użytkownika sieci Web usługi Azure Synapse**](https://web.azuresynapse.net)
2. Przejdź do **zarządzania**   >  **Security**  >  **kontrolą dostępu** zabezpieczeń
3. Wybierz pozycję **Dodaj administratora**, a następnie wybierz pozycję `Synapse_WORKSPACENAME_Admins`

### <a name="step-4-configure-sql-admin-access-for-the-workspace"></a>Krok 4. Konfigurowanie dostępu administratora SQL dla obszaru roboczego

1. Przejdź do witryny [Azure Portal](https://portal.azure.com).
2. Przejdź do obszaru roboczego
3. Przejdź do pozycji **Ustawienia**  >  **Active Directory administrator**
4. Wybierz pozycję **Ustaw administratora**
5. Wybierz pozycję `Synapse_WORKSPACENAME_Admins`
6. Wybierz **pozycję Wybierz**
7. Wybierz pozycję **Zapisz**

> [!NOTE]
> WORKSPACEname — należy zamienić tę część na rzeczywistą nazwę obszaru roboczego.

### <a name="step-5-add-and-remove-users-and-admins-to-security-groups"></a>Krok 5. Dodawanie i usuwanie użytkowników i administratorów do grup zabezpieczeń

1. Dodaj użytkowników, którzy potrzebują dostępu administracyjnego do `Synapse_WORKSPACENAME_Admins`
2. Dodaj wszystkich innych użytkowników do `Synapse_WORKSPACENAME_Users`

> [!NOTE]
> Dowiedz się, jak dodać użytkownika jako członka do grupy zabezpieczeń w [tym artykule](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-members-azure-portal)
> 
> WORKSPACEname — należy zamienić tę część na rzeczywistą nazwę obszaru roboczego.

## <a name="access-control-to-data"></a>Access Control do danych

Kontrola dostępu do danych bazowych jest dzielona na trzy części:

- Dostęp za pomocą płaszczyzny danych do konta magazynu (już skonfigurowanego powyżej w kroku 2)
- Dostęp za pomocą płaszczyzny danych do baz danych SQL (dla pul SQL i SQL na żądanie)
- Tworzenie poświadczeń dla baz danych SQL na żądanie za pośrednictwem konta magazynu

## <a name="access-control-to-sql-databases"></a>Kontrola dostępu do baz danych SQL

> [!TIP]
> Poniższe kroki muszą zostać uruchomione dla **każdej** bazy danych SQL, aby umożliwić użytkownikom dostęp do wszystkich baz danych SQL, z wyjątkiem [uprawnień na poziomie serwera](#server-level-permission) , gdzie można przypisywać użytkownikowi rolę administratora systemu.

### <a name="sql-on-demand"></a>SQL na żądanie

W tej sekcji znajdziesz przykłady umożliwiające użytkownikowi uprawnienie do konkretnej bazy danych lub pełnych uprawnień serwera.

#### <a name="database-level-permission"></a>Uprawnienie na poziomie bazy danych

Aby udzielić dostępu użytkownikowi do **pojedynczej** bazy danych SQL na żądanie, wykonaj czynności opisane w tym przykładzie:

1. Utwórz nazwę logowania

    ```sql
    use master
    go
    CREATE LOGIN [alias@domain.com] FROM EXTERNAL PROVIDER;
    go
    ```

2. Utwórz użytkownika

    ```sql
    use yourdb -- Use your DB name
    go
    CREATE USER alias FROM LOGIN [alias@domain.com];
    ```

3. Dodaj użytkownika do członków określonej roli

    ```sql
    use yourdb -- Use your DB name
    go
    alter role db_owner Add member alias -- Type USER name from step 2
    ```

> [!NOTE]
> Zastąp alias aliasem użytkownika, któremu chcesz nadać dostęp i domenę do używanej domeny firmowej.

#### <a name="server-level-permission"></a>Uprawnienie na poziomie serwera

Aby udzielić użytkownikowi pełnego dostępu do **wszystkich** baz danych SQL na żądanie, wykonaj czynności opisane w tym przykładzie:

```sql
CREATE LOGIN [alias@domain.com] FROM EXTERNAL PROVIDER;
ALTER SERVER ROLE  sysadmin  ADD MEMBER [alias@domain.com];
```

### <a name="sql-pools"></a>Pule SQL

Aby udzielić dostępu użytkownikowi do **jednego** SQL Database, wykonaj następujące kroki:

1. Utwórz użytkownika w bazie danych, uruchamiając następujące polecenie określające żądaną bazę danych w selektorze kontekstu (listę rozwijaną z wybranymi bazami danych):

    ```sql
    --Create user in SQL DB
    CREATE USER [<alias@domain.com>] FROM EXTERNAL PROVIDER;
    ```

2. Przyznaj użytkownikowi rolę dostępu do bazy danych:

    ```sql
    --Create user in SQL DB
    EXEC sp_addrolemember 'db_owner', '<alias@domain.com>';
    ```

> [!IMPORTANT]
> *db_datareader* i *db_datawriter* mogą współdziałać z uprawnieniami do odczytu i zapisu, jeśli udzielanie *db_owner* nie pożądane.
> Aby użytkownik platformy Spark mógł odczytywać i zapisywać dane bezpośrednio z platformy Spark do/z puli SQL, wymagane jest uprawnienie *db_owner* .

Po utworzeniu użytkowników Sprawdź, czy SQL na żądanie może wysyłać zapytania do konta magazynu.

## <a name="access-control-to-workspace-pipeline-runs"></a>Kontrola dostępu do przebiegów potoku obszaru roboczego

### <a name="workspace-managed-identity"></a>Tożsamość zarządzana przez obszar roboczy

> [!IMPORTANT]
> Aby pomyślnie uruchomić potoki, które zawierają zestawy danych lub działania odwołujące się do puli SQL, tożsamość obszaru roboczego musi mieć bezpośredni dostęp do puli SQL.

Uruchom następujące polecenia w każdej puli SQL, aby umożliwić tożsamości zarządzanej przez obszar roboczy uruchamianie potoków w bazie danych puli SQL:

```sql
--Create user in DB
CREATE USER [<workspacename>] FROM EXTERNAL PROVIDER;

--Granting permission to the identity
GRANT CONTROL ON DATABASE::<SQLpoolname> TO <workspacename>;
```

To uprawnienie można usunąć, uruchamiając następujący skrypt w tej samej puli SQL:

```sql
--Revoking permission to the identity
REVOKE CONTROL ON DATABASE::<SQLpoolname> TO <workspacename>;

--Deleting the user in the DB
DROP USER [<workspacename>];
```

## <a name="next-steps"></a>Następne kroki

Omówienie tożsamości zarządzanej przez obszar roboczy Synapse można znaleźć w temacie [tożsamość zarządzana obszaru roboczego usługi Azure Synapse](../security/synapse-workspace-managed-identity.md). Aby dowiedzieć się więcej o podmiotach zabezpieczeń bazy danych, zobacz [podmioty zabezpieczeń](https://msdn.microsoft.com/library/ms181127.aspx). Dodatkowe informacje o rolach bazy danych znajdują się w artykule [role bazy danych](https://msdn.microsoft.com/library/ms189121.aspx) .
