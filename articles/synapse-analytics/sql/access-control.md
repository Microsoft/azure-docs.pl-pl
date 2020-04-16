---
title: Zarządzanie dostępem do obszarów roboczych, danych i potoków
description: Dowiedz się, jak zarządzać kontrolą dostępu do obszarów roboczych, danych i potoków w obszarze roboczym usługi Azure Synapse Analytics (wersja zapoznawcza).
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 89d2105ab080309639c4341072c3f5f36608dfce
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424769"
---
# <a name="manage-access-to-workspaces-data-and-pipelines"></a>Zarządzanie dostępem do obszarów roboczych, danych i potoków

Dowiedz się, jak zarządzać kontrolą dostępu do obszarów roboczych, danych i potoków w obszarze roboczym usługi Azure Synapse Analytics (wersja zapoznawcza).

> [!NOTE]
> W przypadku ga, RBAC będzie bardziej rozwinięty poprzez wprowadzenie ról RBAC specyficzne dla Synapse

## <a name="access-control-for-workspace"></a>Kontrola dostępu dla obszaru roboczego

W przypadku wdrożenia produkcyjnego w obszarze roboczym usługi Azure Synapse zalecamy zorganizowanie środowiska, aby ułatwić aprowekcję użytkowników i administratorów.

> [!NOTE]
> Podejście przyjęte w tym miejscu jest utworzenie kilku grup zabezpieczeń, a następnie skonfigurować obszar roboczy, aby używać ich konsekwentnie. Po skonfigurowaniu grup administrator musi zarządzać tylko członkostwem w grupach zabezpieczeń.

### <a name="step-1-set-up-security-groups-with-names-following-this-pattern"></a>Krok 1: Konfigurowanie grup zabezpieczeń o nazwach następujących po tym wzorcu

1. Tworzenie grupy zabezpieczeń o nazwie`Synapse_WORKSPACENAME_Users`
2. Tworzenie grupy zabezpieczeń o nazwie`Synapse_WORKSPACENAME_Admins`
3. Dodanie argumentu `Synapse_WORKSPACENAME_Admins` do polecenia `ProjectSynapse_WORKSPACENAME_Users`

### <a name="step-2-prepare-the-default-adls-gen2-account"></a>Krok 2: Przygotowanie domyślnego konta ADLS Gen2

Podczas inicjowania obsługi administracyjnej obszaru roboczego trzeba było wybrać konto ADLSGEN2 i kontener dla systemu plików dla obszaru roboczego do użycia.

1. Otwieranie [portalu platformy Azure](https://portal.azure.com)
2. Przejdź do konta ADLSGEN2
3. Przejdź do kontenera (systemu plików) wybranego dla obszaru roboczego Usługi Azure Synapse
4. Kliknij **pozycję Kontrola dostępu (IAM)**
5. Przypisz następujące role:
   1. **Rola czytelnika:**`Synapse_WORKSPACENAME_Users`
   2. **Rola właściciela obiektu blob magazynu:**`Synapse_WORKSPACENAME_Admins`
   3. **Rola współautora danych obiektów blob magazynu:**`Synapse_WORKSPACENAME_Users`
   4. **Rola właściciela obiektu blob magazynu:**`WORKSPACENAME`
  
### <a name="step-3-configure-the-workspace-admin-list"></a>Krok 3: Konfigurowanie listy administratorów obszaru roboczego

1. Przejdź do [ **interfejsu użytkownika sieci Web usługi Azure Synapse**](https://web.azuresynapse.net)
2. Przejdź do kontroli**zarządzania** >  **dostępem**  > **do** zabezpieczeń
3. Kliknij **pozycję Dodaj administratora**i wybierz pozycję`Synapse_WORKSPACENAME_Admins`

### <a name="step-4-configure-sql-admin-access-for-the-workspace"></a>Krok 4: Konfigurowanie programu SQL Admin Access dla obszaru roboczego

1. Przejdź do witryny [Azure Portal](https://portal.azure.com).
2. Przechodzenie do obszaru roboczego
3. Przejdź do **ustawień** > **administratora usługi Active Directory**
4. Kliknij **pozycję Ustaw administratora**
5. Wybierz pozycję `Synapse_WORKSPACENAME_Admins`
6. kliknij przycisk **Wybierz**
7. kliknij pozycję **Zapisz**

### <a name="step-5-add-and-remove-users-and-admins-to-security-groups"></a>Krok 5: Dodawanie i usuwanie użytkowników i administratorów do grup zabezpieczeń

1. Dodawanie użytkowników, którzy potrzebują dostępu administracyjnego do`Synapse_WORKSPACENAME_Admins`
2. Dodaj wszystkich innych użytkowników do`Synapse_WORKSPACENAME_Users`

## <a name="access-control-to-data"></a>Kontrola dostępu do danych

Kontrola dostępu do danych bazowych jest podzielona na trzy części:

- Dostęp płaszczyzny danych do konta magazynu (już skonfigurowany powyżej w kroku 2)
- Dostęp do baz danych SQL (zarówno dla pul SQL, jak i SQL na żądanie)
- Tworzenie poświadczeń dla baz danych SQL na żądanie za pośrednictwem konta magazynu

## <a name="access-control-to-sql-databases"></a>Kontrola dostępu do baz danych SQL

> [!TIP]
> Poniższe kroki muszą być uruchamiane dla **każdej** bazy danych SQL, aby udzielić użytkownikowi dostępu do wszystkich baz danych SQL.

### <a name="sql-on-demand"></a>SQL na żądanie

Aby udzielić dostępu do pojedynczej bazy danych **SQL** na żądanie, wykonaj kroki opisane w tym przykładzie:

1. Tworzenie loginu

    ```sql
    use master
    go
    CREATE LOGIN [John.Thomas@microsoft.com] FROM EXTERNAL PROVIDER;
    go
    ```

2. Tworzenie użytkownika

    ```sql
    use yourdb -- Use your DB name
    go
    CREATE USER john FROM LOGIN [John.Thomas@microsoft.com];
    ```

3. Dodawanie użytkownika do członków określonej roli

    ```sql
    use yourdb -- Use your DB name
    go
    alter role db_owner Add member john -- Type USER name from step 2
    ```

### <a name="sql-pools"></a>Pule SQL

Aby udzielić użytkownikowi dostępu do **pojedynczej** bazy danych SQL, wykonaj następujące kroki:

1. Utwórz użytkownika w bazie danych, uruchamiając następujące polecenie docelowe żądanej bazy danych w selektorze kontekstu (rozwijane, aby wybrać bazy danych):

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
> *db_datareader* i *db_datawriter* mogą pracować dla uprawnień do odczytu/zapisu, jeśli udzielanie *uprawnień db_owner* jest niepożądane.
> Dla użytkownika platformy Spark do odczytu i zapisu bezpośrednio z platformy Spark do/z puli SQL, *db_owner* uprawnienia jest wymagane.

Po utworzeniu użytkowników sprawdź, czy sql na żądanie może wysyłać zapytania do konta magazynu:

- Uruchom następujące polecenie kierowanie do **głównej** bazy danych sql na żądanie:

    ```sql
    CREATE CREDENTIAL [https://<storageaccountname>.dfs.core.windows.net]
    WITH IDENTITY='User Identity';
    ```

## <a name="access-control-to-workspace-pipeline-runs"></a>Kontrola dostępu do przebiegów potoku obszaru roboczego

### <a name="workspace-managed-identity"></a>Tożsamość zarządzana obszarem roboczym

> [!IMPORTANT]
> Aby pomyślnie uruchomić potoki, które zawierają zestawy danych lub działania, które odwołują się do puli SQL, tożsamość obszaru roboczego musi mieć bezpośredni dostęp do puli SQL.

Uruchom następujące polecenia w każdej puli SQL, aby umożliwić tożsamości zarządzanej obszaru roboczego uruchamianie potoków w bazie danych puli SQL:

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

Aby uzyskać omówienie dostępu i kontroli w synapse SQL, zobacz [Kontrola dostępu Synapse SQL](../sql/access-control.md). Aby dowiedzieć się więcej o podmiotach bazy danych, zobacz [Podmioty.](https://msdn.microsoft.com/library/ms181127.aspx) Dodatkowe informacje na temat ról bazy danych można znaleźć w artykule [Role bazy danych.](https://msdn.microsoft.com/library/ms189121.aspx)
