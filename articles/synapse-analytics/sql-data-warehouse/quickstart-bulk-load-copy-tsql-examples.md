---
title: Mechanizmy uwierzytelniania przy użyciu instrukcji COPY
description: Zawiera opis mechanizmów uwierzytelniania do zbiorczego ładowania danych
services: synapse-analytics
author: gaursa
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 07/10/2020
ms.author: gaursa
ms.reviewer: jrasnick
ms.openlocfilehash: 70e8f15b2b02008f24c87cfe70372fccbf0506fd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104600126"
---
# <a name="securely-load-data-using-synapse-sql"></a>Bezpieczne ładowanie danych przy użyciu języka SQL Synapse

W tym artykule omówiono i przedstawiono przykłady mechanizmów bezpiecznego uwierzytelniania [instrukcji Copy](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true). Instrukcja COPY to najbardziej elastyczny i bezpieczny sposób ładowania danych w języku SQL Synapse.
## <a name="supported-authentication-mechanisms"></a>Obsługiwane mechanizmy uwierzytelniania

W poniższej macierzy opisano obsługiwane metody uwierzytelniania dla poszczególnych typów plików i kont magazynu. Dotyczy to źródłowej lokalizacji przechowywania i lokalizacji pliku błędów.

|                          |                CSV                |                      Parquet                       |                        ORC                         |
| :----------------------: | :-------------------------------: | :------------------------------------------------: | :------------------------------------------------: |
|  **Azure Blob Storage**  | SAS/MSI/NAZWA GŁÓWNA USŁUGI/USŁUGA/AAD |                      SYGNATURA DOSTĘPU WSPÓŁDZIELONEGO/KLUCZ                       |                      SYGNATURA DOSTĘPU WSPÓŁDZIELONEGO/KLUCZ                       |
| **Azure Data Lake Gen2** | SAS/MSI/NAZWA GŁÓWNA USŁUGI/USŁUGA/AAD | Sygnatura dostępu współdzielonego (BLOB<sup>1</sup>)/MSI (DFS<sup>2</sup>)/Service Principal/Key/AAD | Sygnatura dostępu współdzielonego (BLOB<sup>1</sup>)/MSI (DFS<sup>2</sup>)/Service Principal/Key/AAD |

1: Ta metoda uwierzytelniania wymaga punktu końcowego. blob (**. blob**. Core.Windows.NET) w ścieżce lokalizacji zewnętrznej.

2: dla tej metody uwierzytelniania wymagana jest punkt końcowy systemu plików DFS (**. DFS**. Core.Windows.NET) w ścieżce lokalizacji zewnętrznej.

## <a name="a-storage-account-key-with-lf-as-the-row-terminator-unix-style-new-line"></a>A. Klucz konta magazynu z LF jako terminator wiersza (nowy wiersz w stylu systemu UNIX)


```sql
--Note when specifying the column list, input field numbers start from 1
COPY INTO target_table (Col_one default 'myStringDefault' 1, Col_two default 1 3)
FROM 'https://adlsgen2account.dfs.core.windows.net/myblobcontainer/folder1/'
WITH (
    FILE_TYPE = 'CSV'
    ,CREDENTIAL=(IDENTITY= 'Storage Account Key', SECRET='<Your_Account_Key>')
    --CREDENTIAL should look something like this:
    --CREDENTIAL=(IDENTITY= 'Storage Account Key', SECRET='x6RWv4It5F2msnjelv3H4DA80n0QW0daPdw43jM0nyetx4c6CpDkdj3986DX5AHFMIf/YN4y6kkCnU8lb+Wx0Pj+6MDw=='),
    ,ROWTERMINATOR='0x0A' --0x0A specifies to use the Line Feed character (Unix based systems)
)
```
> [!IMPORTANT]
>
> - Użyj wartości szesnastkowej (0x0A), aby określić znak wysuwu wiersza/wiersz. Zwróć uwagę, że instrukcja COPY interpretuje ciąg "\n" jako "\r\n" (wiersz powrotu karetki).

## <a name="b-shared-access-signatures-sas-with-crlf-as-the-row-terminator-windows-style-new-line"></a>B. Sygnatury dostępu współdzielonego (SAS) z CRLF jako terminator wiersza (nowy wiersz w stylu systemu Windows)
```sql
COPY INTO target_table
FROM 'https://adlsgen2account.dfs.core.windows.net/myblobcontainer/folder1/'
WITH (
    FILE_TYPE = 'CSV'
    ,CREDENTIAL=(IDENTITY= 'Shared Access Signature', SECRET='<Your_SAS_Token>')
    --CREDENTIAL should look something like this:
    --CREDENTIAL=(IDENTITY= 'Shared Access Signature', SECRET='?sv=2018-03-28&ss=bfqt&srt=sco&sp=rl&st=2016-10-17T20%3A14%3A55Z&se=2021-10-18T20%3A19%3A00Z&sig=IEoOdmeYnE9%2FKiJDSFSYsz4AkNa%2F%2BTx61FuQ%2FfKHefqoBE%3D'),
    ,ROWTERMINATOR='\n'-- COPY command automatically prefixes the \r character when \n (newline) is specified. This results in carriage return newline (\r\n) for Windows based systems.
)
```

> [!IMPORTANT]
>
> - Nie należy określać ROWTERMINATOR jako "\r\n", który będzie interpretowany jako "\r\r\n" i może powodować problemy z analizowaniem

## <a name="c-managed-identity"></a>C. Tożsamość zarządzana

Uwierzytelnianie tożsamości zarządzanej jest wymagane, gdy konto magazynu jest dołączone do sieci wirtualnej. 

### <a name="prerequisites"></a>Wymagania wstępne

1. Zainstaluj program Azure PowerShell, korzystając z tego [przewodnika](/powershell/azure/install-az-ps?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
2. Jeśli masz konto ogólnego przeznaczenia w wersji 1 lub konto magazynu blob, najpierw musisz przeprowadzić uaktualnienie do konta ogólnego przeznaczenia w wersji 2, korzystając z tego [przewodnika](../../storage/common/storage-account-upgrade.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
3. Musisz **zezwolić zaufanym usługom firmy Microsoft na dostęp do tego konta magazynu** , włączone w obszarze zapory konta usługi Azure Storage i menu ustawienia **sieci wirtualnych** . Aby uzyskać więcej informacji, zapoznaj się z tym [przewodnikiem](../../storage/common/storage-network-security.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#exceptions).

#### <a name="steps"></a>Kroki

1. Jeśli masz autonomiczną dedykowaną pulę SQL, zarejestruj swój serwer SQL w usłudze Azure Active Directory (AAD) przy użyciu programu PowerShell: 

   ```powershell
   Connect-AzAccount
   Select-AzSubscription -SubscriptionId <subscriptionId>
   Set-AzSqlServer -ResourceGroupName your-database-server-resourceGroup -ServerName your-SQL-servername -AssignIdentity
   ```

   Ten krok nie jest wymagany w przypadku dedykowanych pul SQL w obszarze roboczym Synapse.

1. Jeśli masz obszar roboczy Synapse, zarejestruj tożsamość zarządzaną przez system obszaru roboczego:

   1. Przejdź do obszaru roboczego Synapse w Azure Portal
   2. Przejdź do bloku zarządzane tożsamości 
   3. Upewnij się, że opcja "Zezwalaj na potoki" jest włączona
   
   ![Zarejestruj plik msi systemu obszaru roboczego](./media/quickstart-bulk-load-copy-tsql-examples/msi-register-example.png)

1. Utwórz **konto magazynu ogólnego przeznaczenia w wersji 2** za pomocą tego [przewodnika](../../storage/common/storage-account-create.md).

   > [!NOTE]
   >
   > - Jeśli masz konto usługi Magazyn ogólnego przeznaczenia w wersji 1 lub BLOB, musisz **najpierw przeprowadzić uaktualnienie do wersji 2** przy użyciu tego [przewodnika](../../storage/common/storage-account-upgrade.md).
   > - Aby uzyskać znane problemy z Azure Data Lake Storage Gen2, zapoznaj się z tym [przewodnikiem](../../storage/blobs/data-lake-storage-known-issues.md).

1. W obszarze konto magazynu przejdź do pozycji **Access Control (IAM)**, a następnie wybierz pozycję **Dodaj przypisanie roli**. Przypisywanie **danych obiektów blob magazynu współautor** roli platformy Azure do serwera lub obszaru roboczego hostującym dedykowaną pulę SQL, która została zarejestrowana w usłudze Azure Active Directory (AAD).

   > [!NOTE]
   > Tylko członkowie z uprawnieniami właściciela mogą wykonać ten krok. W przypadku różnych wbudowanych ról platformy Azure Zapoznaj się z tym [przewodnikiem](../../role-based-access-control/built-in-roles.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
   
    > [!IMPORTANT]
    > Określ rolę właściciela **danych obiektów BLOB** , współautora lub czytelnika usługi **Storage** . Role te są inne niż wbudowane role właściciela, współautora i czytelnika. 

    ![Przyznawanie uprawnień usługi Azure RBAC do ładowania](./media/quickstart-bulk-load-copy-tsql-examples/rbac-load-permissions.png)

4. Teraz można uruchomić instrukcję COPY określającą "tożsamość zarządzaną":

    ```sql
    COPY INTO dbo.target_table
    FROM 'https://myaccount.blob.core.windows.net/myblobcontainer/folder1/*.txt'
    WITH (
        FILE_TYPE = 'CSV',
        CREDENTIAL = (IDENTITY = 'Managed Identity'),
    )
    ```

## <a name="d-azure-active-directory-authentication"></a>D. Uwierzytelnianie usługi Azure Active Directory
#### <a name="steps"></a>Kroki

1. W obszarze konto magazynu przejdź do pozycji **Access Control (IAM)**, a następnie wybierz pozycję **Dodaj przypisanie roli**. Przypisz rolę **właściciel danych obiektów blob magazynu, współautor lub czytelnika** do użytkownika usługi Azure AD. 

    > [!IMPORTANT]
    > Określ rolę właściciela **danych obiektów BLOB** , współautora lub czytelnika usługi **Storage** . Role te są inne niż wbudowane role właściciela, współautora i czytelnika.

    ![Przyznawanie uprawnień usługi Azure RBAC do ładowania](./media/quickstart-bulk-load-copy-tsql-examples/rbac-load-permissions.png)

2. Skonfiguruj uwierzytelnianie usługi Azure AD, wykonując poniższą [dokumentację](../../azure-sql/database/authentication-aad-configure.md?tabs=azure-powershell). 

3. Połącz się z pulą SQL przy użyciu Active Directory, gdzie można teraz uruchomić instrukcję COPY bez określania poświadczeń:

    ```sql
    COPY INTO dbo.target_table
    FROM 'https://myaccount.blob.core.windows.net/myblobcontainer/folder1/*.txt'
    WITH (
        FILE_TYPE = 'CSV'
    )
    ```


## <a name="e-service-principal-authentication"></a>E. Uwierzytelnianie jednostki usługi
#### <a name="steps"></a>Kroki

1. [Tworzenie aplikacji Azure Active Directory](../..//active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)
2. [Pobierz identyfikator aplikacji](../..//active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)
3. [Pobierz klucz uwierzytelniania](../../active-directory/develop/howto-create-service-principal-portal.md#authentication-two-options)
4. [Pobierz punkt końcowy tokenu OAuth 2,0 w wersji 1](../../data-lake-store/data-lake-store-service-to-service-authenticate-using-active-directory.md?bc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2ftoc.json#step-4-get-the-oauth-20-token-endpoint-only-for-java-based-applications)
5. [Przypisywanie uprawnień do odczytu, zapisu i wykonywania do aplikacji usługi Azure AD](../../data-lake-store/data-lake-store-service-to-service-authenticate-using-active-directory.md?bc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2ftoc.json#step-3-assign-the-azure-ad-application-to-the-azure-data-lake-storage-gen1-account-file-or-folder) na koncie magazynu
6. Teraz można uruchomić instrukcję COPY:

    ```sql
    COPY INTO dbo.target_table
    FROM 'https://myaccount.blob.core.windows.net/myblobcontainer/folder0/*.txt'
    WITH (
        FILE_TYPE = 'CSV'
        ,CREDENTIAL=(IDENTITY= '<application_ID>@<OAuth_2.0_Token_EndPoint>' , SECRET= '<authentication_key>')
        --CREDENTIAL should look something like this:
        --,CREDENTIAL=(IDENTITY= '92761aac-12a9-4ec3-89b8-7149aef4c35b@https://login.microsoftonline.com/72f714bf-86f1-41af-91ab-2d7cd011db47/oauth2/token', SECRET='juXi12sZ6gse]woKQNgqwSywYv]7A.M')
    )
    ```

> [!IMPORTANT]
>
> - Użyj wersji **1** punktu końcowego tokenu OAuth 2,0

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się ze szczegółowym opisem składni artykułu w artykule dotyczącym [kopiowania instrukcji](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true#syntax)
- Zapoznaj się z artykułem [Przegląd ładowania danych](./design-elt-data-loading.md#what-is-elt) w celu załadowania najlepszych rozwiązań
