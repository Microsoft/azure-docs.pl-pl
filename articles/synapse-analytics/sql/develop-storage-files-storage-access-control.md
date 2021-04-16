---
title: Kontrolowanie dostępu do konta magazynu dla bez serwera puli SQL
description: Opisuje sposób, w jaki bez serwera pula SQL uzyskuje dostęp do usługi Azure Storage i jak można kontrolować dostęp do magazynu dla bez serwera puli SQL w Azure Synapse Analytics.
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 06/11/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 266a6c27261107b883fdc0c1cdd274e6345de6db
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107483456"
---
# <a name="control-storage-account-access-for-serverless-sql-pool-in-azure-synapse-analytics"></a>Kontrolowanie dostępu do konta magazynu dla bez serwera puli SQL w Azure Synapse Analytics

Bez serwera zapytanie puli SQL odczytuje pliki bezpośrednio z usługi Azure Storage. Uprawnienia dostępu do plików w usłudze Azure Storage są kontrolowane na dwóch poziomach:
- **Poziom magazynu** — użytkownik powinien mieć uprawnienia dostępu do bazowych plików magazynu. Administrator magazynu powinien zezwolić podmiotów zabezpieczeń usługi Azure AD na odczyt/zapis plików lub wygenerować klucz SAS, który będzie używany do uzyskiwania dostępu do magazynu.
- **Poziom usługi SQL —** użytkownik powinien mieć uprawnienia do odczytu danych przy użyciu tabeli zewnętrznej [lub](develop-tables-external-tables.md) do wykonywania `OPENROWSET` funkcji. Przeczytaj więcej na [temat wymaganych uprawnień w tej sekcji.](develop-storage-files-overview.md#permissions)

W tym artykule opisano typy poświadczeń, których można użyć, oraz sposób, w jaki wyszukiwania poświadczeń są wprowadzane dla użytkowników usług SQL i Azure AD.

## <a name="storage-permissions"></a>Uprawnienia magazynu

Bez serwera pula SQL w Synapse Analytics roboczym może odczytywać zawartość plików przechowywanych w usłudze Azure Data Lake Storage. Należy skonfigurować uprawnienia do magazynu, aby umożliwić użytkownikowi, który wykonuje zapytanie SQL, odczytywanie plików. Istnieją trzy metody włączania dostępu do plików>
- **[Kontrola dostępu oparta na rolach (RBAC)](../../role-based-access-control/overview.md)** umożliwia przypisanie roli do niektórych użytkowników usługi Azure AD w dzierżawie, w której znajduje się magazyn. Role RBAC można przypisać do użytkowników usługi Azure AD. Czytelnik musi mieć `Storage Blob Data Reader` rolę `Storage Blob Data Contributor` , lub `Storage Blob Data Owner` . Użytkownik, który zapisuje dane w usłudze Azure Storage, musi mieć `Storage Blob Data Writer` rolę `Storage Blob Data Owner` lub . Pamiętaj, `Storage Owner` że rola nie oznacza, że użytkownik jest również `Storage Data Owner` użytkownikiem .
- **Access Control (ACL)** umożliwiają definiowanie precyzyjnego modelu uprawnień dla plików i katalogów w usłudze Azure Storage. Listy ACL można przypisać do użytkowników usługi Azure AD. Jeśli czytelnicy chcą odczytać plik w ścieżce w usłudze Azure Storage, muszą wykonać (X) listy ACL w każdym folderze w ścieżce pliku i listy ACL odczytu (R) w pliku. [Dowiedz się więcej na temat sposobu ustawienia uprawnień listy ACL w warstwie magazynu](../../storage/blobs/data-lake-storage-access-control.md#how-to-set-acls)
- **Sygnatura dostępu współdzielonego umożliwia** czytelnikowi dostęp do plików w usłudze Azure Data Lake Storage przy użyciu tokenu ograniczonego czasowo. Czytelnik nie musi nawet być uwierzytelniany jako użytkownik usługi Azure AD. Token SAS zawiera uprawnienia przyznane czytelnikowi, a także okres ważności tokenu. Token SAS jest dobrym wyborem w przypadku ograniczonego w czasie dostępu do dowolnego użytkownika, który nie musi nawet być w tej samej dzierżawie usługi Azure AD. Token SAS można zdefiniować na koncie magazynu lub w określonych katalogach. Dowiedz się więcej o [udzielaniu ograniczonego dostępu do zasobów usługi Azure Storage przy użyciu sygnatur dostępu współdzielonych.](../../storage/common/storage-sas-overview.md)

## <a name="supported-storage-authorization-types"></a>Obsługiwane typy autoryzacji magazynu

Użytkownik, który zalogował się do bez serwera w puli SQL, musi być autoryzowany do uzyskiwania dostępu do plików i wykonywania zapytań w usłudze Azure Storage, jeśli pliki nie są publicznie dostępne. Możesz użyć trzech typów autoryzacji, aby uzyskać dostęp do magazynu niepublicznie — [tożsamość użytkownika,](?tabs=user-identity) [sygnatura](?tabs=shared-access-signature)dostępu współdzielona i tożsamość [zarządzana.](?tabs=managed-identity)

> [!NOTE]
> **Domyślną zachowaniem podczas tworzenia obszaru** roboczego jest pass-through usługi Azure AD.

### <a name="user-identity"></a>[Tożsamość użytkownika](#tab/user-identity)

**Tożsamość użytkownika**, znana również jako "azure AD pass-through", to typ autoryzacji, w którym tożsamość użytkownika usługi Azure AD zalogowanego do bez serwera puli SQL jest używana do autoryzowania dostępu do danych. Przed uzyskaniem dostępu do danych administrator usługi Azure Storage musi udzielić uprawnień użytkownikowi usługi Azure AD. Jak wskazano w poniższej tabeli, nie jest ona obsługiwana w przypadku typu użytkownika SQL.

> [!IMPORTANT]
> Token uwierzytelniania usługi AAD może być buforowany przez aplikacje klienckie. Na przykład program PowerBI buforuje token usługi AAD i ponownie używa tego samego tokenu przez godzinę. Długotrwałe zapytania mogą zakończyć się niepowodzeniem, jeśli token wygaśnie w trakcie wykonywania zapytania. Jeśli występują błędy zapytań spowodowane przez token dostępu usługi AAD, który wygasa w [](develop-storage-files-storage-access-control.md?tabs=managed-identity#supported-storage-authorization-types) trakcie zapytania, rozważ przełączenie na tożsamość zarządzaną lub sygnaturę [dostępu współdzielonych](develop-storage-files-storage-access-control.md?tabs=shared-access-signature#supported-storage-authorization-types).

Aby uzyskać dostęp do danych, musisz mieć rolę właściciela/współautora/czytelnika danych obiektu blob usługi Storage. Alternatywnie można określić szczegółowe reguły listy ACL, aby uzyskać dostęp do plików i folderów. Nawet jeśli jesteś właścicielem konta magazynu, nadal musisz dodać siebie do jednej z ról danych obiektu blob magazynu.
Aby dowiedzieć się więcej na temat kontroli dostępu w usłudze Azure Data Lake Store Gen2, zapoznaj się z artykułem [Access control in Azure Data Lake Storage Gen2 (Kontrola](../../storage/blobs/data-lake-storage-access-control.md) dostępu w usłudze Azure Azure Data Lake Storage Gen2 Gen2).


### <a name="shared-access-signature"></a>[Sygnatura dostępu współdzielonego](#tab/shared-access-signature)

**Sygnatura dostępu współdzielonego (SAS)** zapewnia delegowany dostęp do zasobów na koncie magazynu. Dzięki sygnaturze dostępu współdzielonego klient może udzielić klientom dostępu do zasobów na koncie magazynu bez udostępniania kluczy konta. Sygnatura dostępu współdzielonego zapewnia szczegółową kontrolę nad typem dostępu udzielanych klientom, którzy mają sygnaturę dostępu współdzielonego, w tym interwałem ważności, udzielonym uprawnieniam, dopuszczalnym zakresem adresów IP i akceptowalnym protokołem (https/http).

Token sygnatury dostępu współdzielonego można uzyskać, przechodząc do konta magazynu **Azure Portal -> ->** Sygnatura dostępu współdzielonego -> Konfigurowanie uprawnień -> Generowanie sygnatury dostępu współdzielonego i parametrów połączenia.

> [!IMPORTANT]
> Po wygenerowaniu tokenu sygnatury dostępu współdzielonego zawiera on znak zapytania ("?"") na początku tokenu. Aby użyć tokenu w bez serwerach puli SQL, należy usunąć znak zapytania ("?"") podczas tworzenia poświadczeń. Na przykład:
>
> Token sygnatury dostępu współdzielonego: ?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&<6> st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D

Aby włączyć dostęp przy użyciu tokenu SAS, musisz utworzyć poświadczenia w zakresie bazy danych lub serwera 


> [!IMPORTANT]
> Nie można uzyskać dostępu do kont magazynu prywatnego przy użyciu tokenu SAS. Rozważ przełączenie na tożsamość [zarządzaną lub](develop-storage-files-storage-access-control.md?tabs=managed-identity#supported-storage-authorization-types) uwierzytelnianie [pass-through usługi Azure AD](develop-storage-files-storage-access-control.md?tabs=user-identity#supported-storage-authorization-types) w celu uzyskania dostępu do magazynu chronionego.

### <a name="managed-identity"></a>[Tożsamość zarządzana](#tab/managed-identity)

**Tożsamość zarządzana** jest również znana jako tożsamość usługi zarządzanej. Jest to funkcja usługi azure Azure Active Directory (Azure AD), która zapewnia usługi platformy Azure dla bez serwera puli SQL. Ponadto wdraża automatycznie zarządzaną tożsamość w usłudze Azure AD. Ta tożsamość może służyć do autoryzowania żądania dostępu do danych w usłudze Azure Storage.

Przed uzyskaniem dostępu do danych administrator usługi Azure Storage musi udzielić tożsamości zarządzanej uprawnień dostępu do danych. Udzielanie uprawnień tożsamości zarządzanej odbywa się tak samo, jak udzielanie uprawnień dowolnej innej użytkownikowi usługi Azure AD.

### <a name="anonymous-access"></a>[Dostęp anonimowy](#tab/public-access)

Możesz uzyskać dostęp do publicznie dostępnych plików umieszczonych na kontach usługi Azure Storage, które [zezwalają na dostęp anonimowy.](../../storage/blobs/anonymous-read-access-configure.md)

---

### <a name="supported-authorization-types-for-databases-users"></a>Obsługiwane typy autoryzacji dla użytkowników baz danych

W poniższej tabeli można znaleźć dostępne typy autoryzacji:

| Typ autoryzacji                    | *Użytkownik SQL*    | *Użytkownik usługi Azure AD*     |
| ------------------------------------- | ------------- | -----------    |
| [Tożsamość użytkownika](?tabs=user-identity#supported-storage-authorization-types)       | Nieobsługiwane | Obsługiwane      |
| [SAS](?tabs=shared-access-signature#supported-storage-authorization-types)       | Obsługiwane     | Obsługiwane      |
| [Tożsamość zarządzana](?tabs=managed-identity#supported-storage-authorization-types) | Obsługiwane | Obsługiwane      |

### <a name="supported-storages-and-authorization-types"></a>Obsługiwane magazyny i typy autoryzacji

Można użyć następujących kombinacji autoryzacji i typów usługi Azure Storage:

| Typ autoryzacji  | Blob Storage   | ADLS Gen1        | ADLS Gen2     |
| ------------------- | ------------   | --------------   | -----------   |
| [SAS](?tabs=shared-access-signature#supported-storage-authorization-types)    | Obsługiwane\*      | Nie są obsługiwane   | Obsługiwane\*     |
| [Tożsamość zarządzana](?tabs=managed-identity#supported-storage-authorization-types) | Obsługiwane      | Obsługiwane        | Obsługiwane     |
| [Tożsamość użytkownika](?tabs=user-identity#supported-storage-authorization-types)    | Obsługiwane\*      | Obsługiwane\*        | Obsługiwane\*     |

\* Token SAS i tożsamość usługi Azure AD mogą służyć do uzyskiwania dostępu do magazynu, który nie jest chroniony za pomocą zapory.


### <a name="querying-firewall-protected-storage"></a>Wykonywanie zapytań względem magazynu chronionego przez zaporę

Podczas uzyskiwania dostępu do magazynu chronionego przez zaporę można użyć tożsamości **użytkownika** lub tożsamości **zarządzanej.**

> [!NOTE]
> Funkcja zapory w usłudze Storage jest w publicznej wersji zapoznawczej i jest dostępna we wszystkich regionach chmury publicznej. 

#### <a name="user-identity"></a>Tożsamość użytkownika

Aby uzyskać dostęp do magazynu chronionego za pomocą zapory za pośrednictwem tożsamości użytkownika, możesz użyć Azure Portal użytkownika lub modułu Programu PowerShell Az.Storage.
#### <a name="configuration-via-azure-portal"></a>Konfiguracja za pośrednictwem Azure Portal

1. Wyszukaj konto magazynu w Azure Portal.
1. Przejdź do sekcji Sieć w sekcji Ustawienia.
1. W sekcji "Wystąpienia zasobów" dodaj wyjątek dla obszaru roboczego usługi Synapse.
1. Wybierz pozycję Microsoft.Synapse/workspaces jako typ zasobu.
1. Wybierz nazwę obszaru roboczego w obszarze Nazwa wystąpienia.
1. Kliknij pozycję Zapisz.

#### <a name="configuration-via-powershell"></a>Konfiguracja za pośrednictwem programu PowerShell

Wykonaj następujące kroki, aby skonfigurować zaporę konta magazynu i dodać wyjątek dla obszaru roboczego usługi Synapse.

1. Otwórz program PowerShell lub [zainstaluj program PowerShell](/powershell/scripting/install/installing-powershell-core-on-windows)
2. Zainstaluj moduł Az.Storage 3.4.0 i Az.Synapse 0.7.0: 
    ```powershell
    Install-Module -Name Az.Storage -RequiredVersion 3.4.0
    Install-Module -Name Az.Synapse -RequiredVersion 0.7.0
    ```
    > [!IMPORTANT]
    > Upewnij się, że używasz **wersji 3.4.0.** Wersję pakietu Az.Storage możesz sprawdzić, uruchamiając to polecenie:  
    > ```powershell 
    > Get-Module -ListAvailable -Name  Az.Storage | select Version
    > ```
    > 

3. Połącz się z dzierżawą platformy Azure: 
    ```powershell
    Connect-AzAccount
    ```
4. Zdefiniuj zmienne w programie PowerShell: 
    - Nazwa grupy zasobów — tę nazwę można znaleźć w Azure Portal w przeglądzie konta magazynu.
    - Nazwa konta — nazwa konta magazynu chronionego przez reguły zapory.
    - Identyfikator dzierżawy — ten identyfikator można znaleźć w Azure Portal w Azure Active Directory w informacjach o dzierżawie.
    - Nazwa obszaru roboczego — nazwa obszaru roboczego synapse.

    ```powershell
        $resourceGroupName = "<resource group name>"
        $accountName = "<storage account name>"
        $tenantId = "<tenant id>"
        $workspaceName = "<synapse workspace name>"
        
        $workspace = Get-AzSynapseWorkspace -Name $workspaceName
        $resourceId = $workspace.Id
        $index = $resourceId.IndexOf("/resourceGroups/", 0)
        # Replace G with g - /resourceGroups/ to /resourcegroups/
        $resourceId = $resourceId.Substring(0,$index) + "/resourcegroups/" + $resourceId.Substring($index + "/resourceGroups/".Length)
        $resourceId
    ```
    > [!IMPORTANT]
    > Upewnij się, że identyfikator zasobu jest taki jak ten szablon na wydruku zmiennej resourceId.
    >
    > Ważne jest, aby zapisywać **grupy zasobów pisane** małe literami.
    > Przykład jednego identyfikatora zasobu: 
    > ```
    > /subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Synapse/workspaces/{name-of-workspace}
    > ```
    > 
5. Dodaj regułę sieci magazynu: 
    ```powershell
        Add-AzStorageAccountNetworkRule -ResourceGroupName $resourceGroupName -Name $accountName -TenantId $tenantId -ResourceId $resourceId
    ```
6. Sprawdź, czy reguła została zastosowana na koncie magazynu: 
    ```powershell
        $rule = Get-AzStorageAccountNetworkRuleSet -ResourceGroupName $resourceGroupName -Name $accountName
        $rule.ResourceAccessRules | ForEach-Object { 
            if ($_.ResourceId -cmatch "\/subscriptions\/(\w\-*)+\/resourcegroups\/(.)+") { 
                Write-Host "Storage account network rule is successfully configured." -ForegroundColor Green
                $rule.ResourceAccessRules
            } else {
                Write-Host "Storage account network rule is not configured correctly. Remove this rule and follow the steps in detail." -ForegroundColor Red
                $rule.ResourceAccessRules
            }
        }
    ```

#### <a name="managed-identity"></a>Tożsamość zarządzana
Musisz zezwolić [na zaufane usługi firmy Microsoft... ustawienie](../../storage/common/storage-network-security.md#trusted-microsoft-services) i [jawne przypisanie roli platformy Azure](../../storage/common/storage-auth-aad.md#assign-azure-roles-for-access-rights) do [przypisanej](../../active-directory/managed-identities-azure-resources/overview.md) przez system tożsamości zarządzanej dla tego wystąpienia zasobu. W takim przypadku zakres dostępu dla wystąpienia odpowiada roli platformy Azure przypisanej do tożsamości zarządzanej.

## <a name="credentials"></a>Poświadczenia

Aby można było odpytować plik znajdujący się w usłudze Azure Storage, punkt końcowy bez serwera w puli SQL wymaga poświadczeń, które zawierają informacje o uwierzytelnianiu. Używane są dwa typy poświadczeń:
- Funkcja CREDENTIAL na poziomie serwera jest używana w przypadku zapytań ad hoc wykonywanych przy użyciu `OPENROWSET` funkcji . Nazwa poświadczeń musi być dopasowana do adresu URL magazynu.
- POŚWIADCZENIA W ZAKRESIE BAZY DANYCH są używane w przypadku tabel zewnętrznych. Odwołania do tabeli `DATA SOURCE` zewnętrznej z poświadczeniami, które powinny być używane do uzyskiwania dostępu do magazynu.

Aby umożliwić użytkownikowi utworzenie lub upuszczenie poświadczeń, administrator może udzielić lub odmówić użytkownikowi uprawnienia ALTER ANY CREDENTIAL:

```sql
GRANT ALTER ANY CREDENTIAL TO [user_name];
```

Użytkownicy bazy danych, którzy mają dostęp do magazynu zewnętrznego, muszą mieć uprawnienia do używania poświadczeń.

### <a name="grant-permissions-to-use-credential"></a>Udzielanie uprawnień do używania poświadczeń

Aby użyć poświadczeń, użytkownik musi mieć `REFERENCES` uprawnienia do określonego poświadczenia. Aby udzielić `REFERENCES` uprawnienia NA storage_credential dla specific_user, wykonaj:

```sql
GRANT REFERENCES ON CREDENTIAL::[storage_credential] TO [specific_user];
```

## <a name="server-scoped-credential"></a>Poświadczenie w zakresie serwera

Poświadczenia w zakresie serwera są używane, gdy logowanie SQL wywołuje `OPENROWSET` funkcję bez `DATA_SOURCE` odczytywania plików na niektórych kontach magazynu. Nazwa poświadczeń w zakresie serwera musi **być** dopasowana do podstawowego adresu URL usługi Azure Storage (opcjonalnie z nazwą kontenera). Poświadczenie jest dodawane przez uruchomienie [create credential](/sql/t-sql/statements/create-credential-transact-sql?view=azure-sqldw-latest&preserve-view=true). Należy podać argument CREDENTIAL NAME.

> [!NOTE]
> `FOR CRYPTOGRAPHIC PROVIDER`Argument nie jest obsługiwany.

Nazwa CREDENTIAL na poziomie serwera musi odpowiadać pełnej ścieżce do konta magazynu (i opcjonalnie kontenera) w następującym formacie: `<prefix>://<storage_account_path>[/<container_name>]` . Ścieżki konta magazynu zostały opisane w poniższej tabeli:

| Zewnętrzne źródło danych       | Prefiks | Ścieżka konta magazynu                                |
| -------------------------- | ------ | --------------------------------------------------- |
| Azure Blob Storage         | https  | <storage_account>.blob.core.windows.net             |
| Usługa Azure Data Lake Storage 1. generacji | https  | <storage_account>.azuredatalakestore.net/webhdfs/v1 |
| Usługa Azure Data Lake Storage 2. generacji | https  | <storage_account>.dfs.core.windows.net              |

Poświadczenia w zakresie serwera umożliwiają dostęp do usługi Azure Storage przy użyciu następujących typów uwierzytelniania:

### <a name="user-identity"></a>[Tożsamość użytkownika](#tab/user-identity)

Użytkownicy usługi Azure AD mogą uzyskać dostęp do dowolnego pliku w usłudze Azure Storage, jeśli mają `Storage Blob Data Owner` `Storage Blob Data Contributor` rolę , lub `Storage Blob Data Reader` . Użytkownicy usługi Azure AD nie potrzebują poświadczeń, aby uzyskać dostęp do magazynu. 

Użytkownicy SQL nie mogą używać uwierzytelniania usługi Azure AD do uzyskiwania dostępu do magazynu.

### <a name="shared-access-signature"></a>[Sygnatura dostępu współdzielonego](#tab/shared-access-signature)

Poniższy skrypt tworzy poświadczenia na poziomie serwera, które mogą być używane przez funkcję do uzyskiwania dostępu do dowolnego pliku w usłudze `OPENROWSET` Azure Storage przy użyciu tokenu SAS. Utwórz to poświadczenie, aby włączyć podmiot zabezpieczeń SQL, który wykonuje funkcję, aby odczytywać pliki chronione za pomocą klucza SYGNATURY dostępu współdzielonego w usłudze Azure Storage, które są takie sama jak `OPENROWSET` adres URL w nazwie poświadczeń.

Program Exchange <*mystorageaccountname*> rzeczywistą nazwą konta magazynu, <*mystorageaccountcontainername*> rzeczywistą nazwą kontenera:

```sql
CREATE CREDENTIAL [https://<mystorageaccountname>.dfs.core.windows.net/<mystorageaccountcontainername>]
WITH IDENTITY='SHARED ACCESS SIGNATURE'
, SECRET = 'sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D';
GO
```

Opcjonalnie możesz użyć tylko podstawowego adresu URL konta magazynu bez nazwy kontenera.

### <a name="managed-identity"></a>[Tożsamość zarządzana](#tab/managed-identity)

Poniższy skrypt tworzy poświadczenia na poziomie serwera, które mogą być używane przez funkcję do uzyskiwania dostępu do dowolnego pliku w usłudze Azure Storage przy użyciu tożsamości `OPENROWSET` zarządzanej przez obszar roboczy.

```sql
CREATE CREDENTIAL [https://<storage_account>.dfs.core.windows.net/<container>]
WITH IDENTITY='Managed Identity'
```

Opcjonalnie możesz użyć tylko podstawowego adresu URL konta magazynu bez nazwy kontenera.

### <a name="public-access"></a>[Dostęp publiczny](#tab/public-access)

Poświadczenia o zakresie bazy danych nie są wymagane, aby zezwolić na dostęp do publicznie dostępnych plików. Utwórz [źródło danych bez poświadczeń o zakresie bazy danych,](develop-tables-external-tables.md?tabs=sql-ondemand#example-for-create-external-data-source) aby uzyskać dostęp do publicznie dostępnych plików w usłudze Azure Storage.

---

## <a name="database-scoped-credential"></a>Poświadczenie w zakresie bazy danych

Poświadczenia o zakresie bazy danych są używane, gdy dowolny podmiot zabezpieczeń wywołuje funkcję z tabelą zewnętrzną lub wybiera dane z tabeli zewnętrznej, `OPENROWSET` które nie mają dostępu do plików `DATA_SOURCE` publicznych. [](develop-tables-external-tables.md) Poświadczenia o zakresie bazy danych nie muszą być zgodne z nazwą konta magazynu. Będzie on jawnie używany w ŹRÓDLE DANYCH, które definiuje lokalizację magazynu.

Poświadczenia o zakresie bazy danych umożliwiają dostęp do usługi Azure Storage przy użyciu następujących typów uwierzytelniania:

### <a name="azure-ad-identity"></a>[Tożsamość usługi Azure AD](#tab/user-identity)

Użytkownicy usługi Azure AD mogą uzyskać dostęp do dowolnego pliku w usłudze Azure Storage, jeśli mają co `Storage Blob Data Owner` najmniej rolę , lub `Storage Blob Data Contributor` `Storage Blob Data Reader` . Użytkownicy usługi Azure AD nie potrzebują poświadczeń, aby uzyskać dostęp do magazynu.

```sql
CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>'
)
```

Użytkownicy SQL nie mogą używać uwierzytelniania usługi Azure AD do uzyskiwania dostępu do magazynu.

### <a name="shared-access-signature"></a>[Sygnatura dostępu współdzielonego](#tab/shared-access-signature)

Poniższy skrypt tworzy poświadczenie, które jest używane do uzyskiwania dostępu do plików w magazynie przy użyciu tokenu SAS określonego w poświadczeniu. Skrypt utworzy przykładowe zewnętrzne źródło danych, które używa tego tokenu SAS do uzyskiwania dostępu do magazynu.

```sql
-- Optional: Create MASTER KEY if not exists in database:
-- CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<Very Strong Password>'
GO
CREATE DATABASE SCOPED CREDENTIAL [SasToken]
WITH IDENTITY = 'SHARED ACCESS SIGNATURE',
     SECRET = 'sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D';
GO
CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>',
          CREDENTIAL = SasToken
)
```

### <a name="managed-identity"></a>[Tożsamość zarządzana](#tab/managed-identity)

Poniższy skrypt tworzy poświadczenia w zakresie bazy danych, których można użyć do personifikacji bieżącego użytkownika usługi Azure AD jako tożsamości zarządzanej usługi. Skrypt utworzy przykładowe zewnętrzne źródło danych, które używa tożsamości obszaru roboczego do uzyskiwania dostępu do magazynu.

```sql
-- Optional: Create MASTER KEY if not exists in database:
-- CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<Very Strong Password>
CREATE DATABASE SCOPED CREDENTIAL SynapseIdentity
WITH IDENTITY = 'Managed Identity';
GO
CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>',
          CREDENTIAL = SynapseIdentity
)
```

Poświadczenia o zakresie bazy danych nie muszą być zgodne z nazwą konta magazynu, ponieważ będą jawnie używane w źródle danych, które definiuje lokalizację magazynu.

### <a name="public-access"></a>[Dostęp publiczny](#tab/public-access)

Poświadczenia o zakresie bazy danych nie są wymagane, aby zezwolić na dostęp do publicznie dostępnych plików. Utwórz [źródło danych bez poświadczeń o zakresie bazy danych,](develop-tables-external-tables.md?tabs=sql-ondemand#example-for-create-external-data-source) aby uzyskać dostęp do publicznie dostępnych plików w usłudze Azure Storage.

```sql
CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://<storage_account>.blob.core.windows.net/<container>/<path>'
)
```
---

Poświadczenia o zakresie bazy danych są używane w zewnętrznych źródłach danych w celu określenia metody uwierzytelniania, która będzie używana do uzyskiwania dostępu do tego magazynu:

```sql
CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>',
          CREDENTIAL = <name of database scoped credential> 
)
```

## <a name="examples"></a>Przykłady

### <a name="access-a-publicly-available-data-source"></a>**Uzyskiwanie dostępu do publicznie dostępnego źródła danych**

Użyj poniższego skryptu, aby utworzyć tabelę, która uzyskuje dostęp do publicznie dostępnego źródła danych.

```sql
CREATE EXTERNAL FILE FORMAT [SynapseParquetFormat]
       WITH ( FORMAT_TYPE = PARQUET)
GO
CREATE EXTERNAL DATA SOURCE publicData
WITH (    LOCATION   = 'https://<storage_account>.dfs.core.windows.net/<public_container>/<path>' )
GO

CREATE EXTERNAL TABLE dbo.userPublicData ( [id] int, [first_name] varchar(8000), [last_name] varchar(8000) )
WITH ( LOCATION = 'parquet/user-data/*.parquet',
       DATA_SOURCE = [publicData],
       FILE_FORMAT = [SynapseParquetFormat] )
```

Użytkownik bazy danych może odczytać zawartość plików ze źródła danych przy użyciu tabeli zewnętrznej lub [funkcji OPENROWSET,](develop-openrowset.md) która odwołuje się do źródła danych:

```sql
SELECT TOP 10 * FROM dbo.userPublicData;
GO
SELECT TOP 10 * FROM OPENROWSET(BULK 'parquet/user-data/*.parquet',
                                DATA_SOURCE = 'mysample',
                                FORMAT='PARQUET') as rows;
GO
```

### <a name="access-a-data-source-using-credentials"></a>**Uzyskiwanie dostępu do źródła danych przy użyciu poświadczeń**

Zmodyfikuj poniższy skrypt, aby utworzyć tabelę zewnętrzną, która uzyskuje dostęp do usługi Azure Storage przy użyciu tokenu SAS, tożsamości użytkownika usługi Azure AD lub tożsamości zarządzanej obszaru roboczego.

```sql
-- Create master key in databases with some password (one-off per database)
CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'Y*********0'
GO

-- Create databases scoped credential that use Managed Identity or SAS token. User needs to create only database-scoped credentials that should be used to access data source:

CREATE DATABASE SCOPED CREDENTIAL WorkspaceIdentity
WITH IDENTITY = 'Managed Identity'
GO
CREATE DATABASE SCOPED CREDENTIAL SasCredential
WITH IDENTITY = 'SHARED ACCESS SIGNATURE', SECRET = 'sv=2019-10-1********ZVsTOL0ltEGhf54N8KhDCRfLRI%3D'

-- Create data source that one of the credentials above, external file format, and external tables that reference this data source and file format:

CREATE EXTERNAL FILE FORMAT [SynapseParquetFormat] WITH ( FORMAT_TYPE = PARQUET)
GO

CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>'
-- Uncomment one of these options depending on authentication method that you want to use to access data source:
--,CREDENTIAL = WorkspaceIdentity 
--,CREDENTIAL = SasCredential 
)

CREATE EXTERNAL TABLE dbo.userData ( [id] int, [first_name] varchar(8000), [last_name] varchar(8000) )
WITH ( LOCATION = 'parquet/user-data/*.parquet',
       DATA_SOURCE = [mysample],
       FILE_FORMAT = [SynapseParquetFormat] );

```

Użytkownik bazy danych może odczytać zawartość plików [](develop-tables-external-tables.md) ze źródła danych przy użyciu tabeli zewnętrznej lub [funkcji OPENROWSET,](develop-openrowset.md) która odwołuje się do źródła danych:

```sql
SELECT TOP 10 * FROM dbo.userdata;
GO
SELECT TOP 10 * FROM OPENROWSET(BULK 'parquet/user-data/*.parquet', DATA_SOURCE = 'mysample', FORMAT='PARQUET') as rows;
GO
```

## <a name="next-steps"></a>Następne kroki

Poniższe artykuły pomogą Ci dowiedzieć się, jak tworzyć zapytania dotyczące różnych typów folderów, typów plików oraz tworzyć widoki i używać ich:

- [Wykonywanie zapytań o pojedynczy plik CSV](query-single-csv-file.md)
- [Wykonywanie zapytań o foldery i wiele plików CSV](query-folders-multiple-csv-files.md)
- [Wykonywanie zapytań o określone pliki](query-specific-files.md)
- [Wykonywanie zapytań względem plików Parquet](query-parquet-files.md)
- [Tworzenie widoków i korzystanie z nich](create-use-views.md)
- [Wykonywanie zapytań względem plików JSON](query-json-files.md)
- [Wykonywanie zapytań względem typów zagnieżdżonych Parquet](query-parquet-nested-types.md)
