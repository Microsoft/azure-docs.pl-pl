---
title: Konfigurowanie kontroli dostępu opartej na rolach dla konta Azure Cosmos DB za pomocą usługi Azure AD
description: Dowiedz się, jak skonfigurować kontrolę dostępu opartą na rolach przy użyciu Azure Active Directory dla Azure Cosmos DB konta
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: how-to
ms.date: 04/19/2021
ms.author: thweiss
ms.openlocfilehash: 209d18dfbadea89f14fd90da9a1bc57b3ccf0dfe
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107728079"
---
# <a name="configure-role-based-access-control-with-azure-active-directory-for-your-azure-cosmos-db-account-preview"></a>Konfigurowanie kontroli dostępu opartej na rolach przy użyciu Azure Active Directory dla konta Azure Cosmos DB (wersja zapoznawcza)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!IMPORTANT]
> Azure Cosmos DB kontroli dostępu opartej na rolach jest obecnie dostępna w wersji zapoznawczej. Ta wersja zapoznawcza jest dostarczana bez Umowa dotycząca poziomu usług i nie jest zalecana w przypadku obciążeń produkcyjnych. Aby uzyskać więcej informacji, zobacz Dodatkowe warunki użytkowania dla [wersji Microsoft Azure zapoznawczych.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

> [!NOTE]
> Ten artykuł dotyczy kontroli dostępu opartej na rolach dla operacji płaszczyzny danych w Azure Cosmos DB. Jeśli używasz operacji płaszczyzny zarządzania, zobacz [artykuł Kontrola dostępu](role-based-access-control.md) oparta na rolach zastosowana do operacji płaszczyzny zarządzania.

Azure Cosmos DB udostępnia wbudowany system kontroli dostępu opartej na rolach (RBAC), który umożliwia:

- Uwierzytelnianie żądań danych przy użyciu Azure Active Directory (Azure AD).
- Autoryzuj żądania danych za pomocą precyzyjnego modelu uprawnień opartego na rolach.

## <a name="concepts"></a>Pojęcia

Ta Azure Cosmos DB RBAC płaszczyzny danych jest zbudowana na pojęciach, które często znajdują się w innych systemach RBAC, takich jak [RBAC platformy Azure:](../role-based-access-control/overview.md)

- Model [uprawnień składa](#permission-model) się z zestawu **akcji**; Każda z tych akcji jest mapowa na jedną lub wiele operacji bazy danych. Niektóre przykłady akcji obejmują odczytywanie elementu, pisanie elementu lub wykonywanie zapytania.
- Azure Cosmos DB użytkownicy tworzą **[definicje ról zawierające](#role-definitions)** listę dozwolonych akcji.
- Definicje ról są przypisywane do określonych tożsamości usługi Azure AD za **[pośrednictwem przypisań ról.](#role-assignments)** Przypisanie roli definiuje również zakres, do który ma zastosowanie definicja roli; Obecnie istnieją trzy zakresy:
    - Konto Azure Cosmos DB,
    - Baza Azure Cosmos DB danych,
    - Kontener Azure Cosmos DB kontenera.

  :::image type="content" source="./media/how-to-setup-rbac/concepts.png" alt-text="Pojęcia dotyczące kontroli RBAC":::

> [!NOTE]
> Rola Azure Cosmos DB RBAC nie uwidacznia obecnie żadnych wbudowanych definicji ról.

## <a name="permission-model"></a><a id="permission-model"></a> Model uprawnień

> [!IMPORTANT]
> Ten model uprawnień obejmuje tylko operacje bazy danych, które umożliwiają odczytywanie i zapis danych. Nie obejmuje **ona** żadnych operacji zarządzania, takich jak tworzenie kontenerów lub zmienianie ich przepływności. Oznacza to, że **nie można używać żadnego zestawu SDK Azure Cosmos DB danych** do uwierzytelniania operacji zarządzania przy użyciu tożsamości usługi AAD. Zamiast tego należy użyć kontroli [RBAC platformy Azure za pośrednictwem:](role-based-access-control.md)
> - [Szablony arm](manage-with-templates.md)
> - [Azure PowerShell skryptów ,](manage-with-powershell.md)
> - [Skrypty interfejsu wiersza polecenia platformy Azure,](manage-with-cli.md)
> - Biblioteki zarządzania platformy Azure dostępne w programie
>   - [.NET](https://www.nuget.org/packages/Azure.ResourceManager.CosmosDB)
>   - [Java](https://search.maven.org/artifact/com.azure.resourcemanager/azure-resourcemanager-cosmos)
>   - [Python](https://pypi.org/project/azure-mgmt-cosmosdb/)

W poniższej tabeli wymieniono wszystkie akcje udostępniane przez model uprawnień.

| Nazwa | Odpowiednie operacji bazy danych |
|---|---|
| `Microsoft.DocumentDB/databaseAccounts/readMetadata` | Odczytywanie metadanych konta. Aby uzyskać [szczegółowe informacje, zobacz Żądania](#metadata-requests) metadanych. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/create` | Utwórz nowy element. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/read` | Odczytywanie pojedynczego elementu według jego identyfikatora i klucza partycji (odczyt punktowy). |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/replace` | Zastąp istniejący element. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/upsert` | "Upsert" element, co oznacza utworzenie go, jeśli nie istnieje, lub zastąpienie go, jeśli istnieje. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/delete` | Usuwanie elementu. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/executeQuery` | Wykonaj zapytanie [SQL](sql-query-getting-started.md). |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/readChangeFeed` | Odczytaj z zestawienia zmian [kontenera](read-change-feed.md). |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/executeStoredProcedure` | Wykonaj procedurę [składowaną](stored-procedures-triggers-udfs.md). |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/manageConflicts` | Manage [conflicts](conflict-resolution-policies.md) for multi-write region accounts (that is, list and delete items from the conflict feed). |

Symbole wieloznaczne są obsługiwane zarówno na *poziomie kontenerów,* *jak i* elementów:

- `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/*`
- `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/*`

### <a name="metadata-requests"></a><a id="metadata-requests"></a> Żądania metadanych

W przypadku Azure Cosmos DB zestawów SDK te zestawy SDK podczas inicjowania wystawiają żądania metadanych tylko do odczytu i obsługują określone żądania danych. Te żądania metadanych pobierają różne szczegóły konfiguracji, takie jak: 

- Konfiguracja globalna Twojego konta, w tym regiony platformy Azure, w których konto jest dostępne.
- Klucz partycji kontenerów lub ich zasad indeksowania.
- Lista partycji fizycznych, które sprawiają, że kontener i ich adresy.

Nie *pobierają* żadnych danych przechowywanych na Twoim koncie.

Aby zapewnić najlepszą przejrzystość naszego modelu uprawnień, te żądania metadanych są jawnie objęte `Microsoft.DocumentDB/databaseAccounts/readMetadata` akcją. Ta akcja powinna być dozwolona w każdej sytuacji, Azure Cosmos DB do konta użytkownika jest uzyskiwany za pośrednictwem jednego z Azure Cosmos DB SDK. Można go przypisać (za pomocą przypisania roli) na dowolnym poziomie w hierarchii Azure Cosmos DB (konta, bazy danych lub kontenera).

Rzeczywiste żądania metadanych dozwolone przez akcję zależą od zakresu, do który `Microsoft.DocumentDB/databaseAccounts/readMetadata` jest przypisana akcja:

| Zakres | Żądania dozwolone przez akcję |
|---|---|
| Konto | — Wyświetlanie listy baz danych w ramach konta<br>— Dla każdej bazy danych w ramach konta dozwolone akcje w zakresie bazy danych |
| baza danych | — Odczytywanie metadanych bazy danych<br>— Wyświetlanie listy kontenerów w bazie danych<br>— Dla każdego kontenera w bazie danych dozwolone akcje w zakresie kontenera |
| Kontener | — Odczytywanie metadanych kontenera<br>— Wyświetlanie listy partycji fizycznych w kontenerze<br>- Rozpoznawanie adresu każdej partycji fizycznej |

## <a name="create-role-definitions"></a><a id="role-definitions"></a> Tworzenie definicji ról

Podczas tworzenia definicji roli należy podać:

- Nazwa konta Azure Cosmos DB użytkownika.
- Grupa zasobów zawierająca Twoje konto.
- Typ definicji roli; Program `CustomRole` jest obecnie obsługiwany tylko przez program .
- Nazwa definicji roli.
- Lista [akcji,](#permission-model) na które rola ma zezwalać.
- Jeden lub wiele zakresów, w których można przypisać definicję roli; obsługiwane zakresy to:
    - `/` (na poziomie konta),
    - `/dbs/<database-name>` (poziom bazy danych),
    - `/dbs/<database-name>/colls/<container-name>` (poziom kontenera).

> [!NOTE]
> Operacje opisane poniżej są obecnie dostępne w:
> - Azure PowerShell: [Az.CosmosDB w wersji 2.0.1-preview](https://www.powershellgallery.com/packages/Az.CosmosDB/2.0.1-preview)
> - Interfejs wiersza polecenia platformy Azure: [rozszerzenie "cosmosdb-preview" w wersji 0.4.0](https://github.com/Azure/azure-cli-extensions/tree/master/src/cosmosdb-preview)

### <a name="using-azure-powershell"></a>Korzystanie z programu Azure PowerShell

Utwórz rolę o nazwie *MyReadOnlyRole,* która zawiera tylko akcje odczytu:

```powershell
$resourceGroupName = "<myResourceGroup>"
$accountName = "<myCosmosAccount>"
New-AzCosmosDBSqlRoleDefinition -AccountName $accountName `
    -ResourceGroupName $resourceGroupName `
    -Type CustomRole -RoleName MyReadOnlyRole `
    -DataAction @( `
        'Microsoft.DocumentDB/databaseAccounts/readMetadata',
        'Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/read', `
        'Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/executeQuery', `
        'Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/readChangeFeed') `
    -AssignableScope "/"
```

Utwórz rolę o nazwie *MyReadWriteRole,* która zawiera wszystkie akcje:

```powershell
New-AzCosmosDBSqlRoleDefinition -AccountName $accountName `
    -ResourceGroupName $resourceGroupName `
    -Type CustomRole -RoleName MyReadWriteRole `
    -DataAction @( `
        'Microsoft.DocumentDB/databaseAccounts/readMetadata',
        'Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/*', `
        'Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/*') `
    -AssignableScope "/"
```

Lista utworzonych definicji ról w celu pobrania ich identyfikatorów:

```powershell
Get-AzCosmosDBSqlRoleDefinition -AccountName $accountName `
    -ResourceGroupName $resourceGroupName
```

```
RoleName         : MyReadWriteRole
Id               : /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAcc
                   ounts/<myCosmosAccount>/sqlRoleDefinitions/<roleDefinitionId>
Type             : CustomRole
Permissions      : {Microsoft.Azure.Management.CosmosDB.Models.Permission}
AssignableScopes : {/subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAc
                   counts/<myCosmosAccount>}

RoleName         : MyReadOnlyRole
Id               : /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAcc
                   ounts/<myCosmosAccount>/sqlRoleDefinitions/<roleDefinitionId>
Type             : CustomRole
Permissions      : {Microsoft.Azure.Management.CosmosDB.Models.Permission}
AssignableScopes : {/subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAc
                   counts/<myCosmosAccount>}
```

### <a name="using-the-azure-cli"></a>Przy użyciu interfejsu wiersza polecenia platformy Azure

Utwórz rolę o nazwie *MyReadOnlyRole,* która zawiera tylko akcje odczytu:

```json
// role-definition-ro.json
{
    "RoleName": "MyReadOnlyRole",
    "Type": "CustomRole",
    "AssignableScopes": ["/"],
    "Permissions": [{
        "DataActions": [
            "Microsoft.DocumentDB/databaseAccounts/readMetadata",
            "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/read",
            "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/executeQuery",
            "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/readChangeFeed"
        ]
    }]
}
```

```azurecli
resourceGroupName='<myResourceGroup>'
accountName='<myCosmosAccount>'
az cosmosdb sql role definition create --account-name $accountName --resource-group $resourceGroupName --body @role-definition-ro.json
```

Utwórz rolę o *nazwie MyReadWriteRole* zawierającą wszystkie akcje:

```json
// role-definition-rw.json
{
    "RoleName": "MyReadWriteRole",
    "Type": "CustomRole",
    "AssignableScopes": ["/"],
    "Permissions": [{
        "DataActions": [
            "Microsoft.DocumentDB/databaseAccounts/readMetadata",
            "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/*",
            "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/*"
        ]
    }]
}
```

```azurecli
az cosmosdb sql role definition create --account-name $accountName --resource-group $resourceGroupName --body @role-definition-rw.json
```

Lista utworzonych definicji ról w celu pobrania ich identyfikatorów:

```azurecli
az cosmosdb sql role definition list --account-name $accountName --resource-group $resourceGroupName
```

```
[
  {
    "assignableScopes": [
      "/subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAccounts/<myCosmosAccount>"
    ],
    "id": "/subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAccounts/<myCosmosAccount>/sqlRoleDefinitions/<roleDefinitionId>",
    "name": "<roleDefinitionId>",
    "permissions": [
      {
        "dataActions": [
          "Microsoft.DocumentDB/databaseAccounts/readMetadata",
          "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/*",
          "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/*"
        ],
        "notDataActions": []
      }
    ],
    "resourceGroup": "<myResourceGroup>",
    "roleName": "MyReadWriteRole",
    "sqlRoleDefinitionGetResultsType": "CustomRole",
    "type": "Microsoft.DocumentDB/databaseAccounts/sqlRoleDefinitions"
  },
  {
    "assignableScopes": [
      "/subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAccounts/<myCosmosAccount>"
    ],
    "id": "/subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAccounts/<myCosmosAccount>/sqlRoleDefinitions/<roleDefinitionId>",
    "name": "<roleDefinitionId>",
    "permissions": [
      {
        "dataActions": [
          "Microsoft.DocumentDB/databaseAccounts/readMetadata",
          "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/read",
          "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/executeQuery",
          "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/readChangeFeed"
        ],
        "notDataActions": []
      }
    ],
    "resourceGroup": "<myResourceGroup>",
    "roleName": "MyReadOnlyRole",
    "sqlRoleDefinitionGetResultsType": "CustomRole",
    "type": "Microsoft.DocumentDB/databaseAccounts/sqlRoleDefinitions"
  }
]
```

## <a name="create-role-assignments"></a><a id="role-assignments"></a> Tworzenie przypisań ról

Po utworzeniu definicji ról można je skojarzyć z tożsamościami WAD. Podczas tworzenia przypisania roli należy podać:

- Nazwa konta Azure Cosmos DB użytkownika.
- Grupa zasobów zawierająca Twoje konto.
- Identyfikator definicji roli do przypisania.
- Identyfikator podmiotu zabezpieczeń tożsamości, do których należy przypisać definicję roli.
- Zakres przypisania roli; obsługiwane zakresy to:
    - `/` (na poziomie konta)
    - `/dbs/<database-name>` (poziom bazy danych)
    - `/dbs/<database-name>/colls/<container-name>` (poziom kontenera)

  Zakres musi być lub być zakresem podrzędnym jednego z zakresów przypisywalnych definicji roli.

> [!NOTE]
> Jeśli chcesz utworzyć przypisanie roli dla jednostki usługi,  upewnij się,  że używasz jej identyfikatora obiektu zgodnie z sekcją Aplikacje dla przedsiębiorstw w bloku **Azure Active Directory** portal.

> [!NOTE]
> Opisane poniżej operacje są obecnie dostępne w:
> - Azure PowerShell: [Az.CosmosDB w wersji 2.0.1-preview](https://www.powershellgallery.com/packages/Az.CosmosDB/2.0.1-preview)
> - Interfejs wiersza polecenia platformy Azure: [rozszerzenie "cosmosdb-preview" w wersji 0.4.0](https://github.com/Azure/azure-cli-extensions/tree/master/src/cosmosdb-preview)

### <a name="using-azure-powershell"></a>Korzystanie z programu Azure PowerShell

Przypisywanie roli do tożsamości:

```powershell
$resourceGroupName = "<myResourceGroup>"
$accountName = "<myCosmosAccount>"
$readOnlyRoleDefinitionId = "<roleDefinitionId>" // as fetched above
$principalId = "<aadPrincipalId>"
New-AzCosmosDBSqlRoleAssignment -AccountName $accountName `
    -ResourceGroupName $resourceGroupName `
    -RoleDefinitionId $readOnlyRoleDefinitionId `
    -Scope $accountName `
    -PrincipalId $principalId
```

### <a name="using-the-azure-cli"></a>Przy użyciu interfejsu wiersza polecenia platformy Azure

Przypisywanie roli do tożsamości:

```azurecli
resourceGroupName='<myResourceGroup>'
accountName='<myCosmosAccount>'
readOnlyRoleDefinitionId = '<roleDefinitionId>' // as fetched above
principalId = '<aadPrincipalId>'
az cosmosdb sql role assignment create --account-name $accountName --resource-group $resourceGroupName --scope "/" --principal-id $principalId --role-definition-id $readOnlyRoleDefinitionId
```

## <a name="initialize-the-sdk-with-azure-ad"></a>Inicjowanie zestawu SDK za pomocą usługi Azure AD

Aby użyć Azure Cosmos DB RBAC w aplikacji, musisz zaktualizować sposób inicjowania zestawu AZURE COSMOS DB SDK. Zamiast przekazywać klucz podstawowy konta, musisz przekazać wystąpienie `TokenCredential` klasy . To wystąpienie udostępnia Azure Cosmos DB SDK z kontekstem wymaganym do pobrania tokenu usługi AAD w imieniu tożsamości, która ma być potrzebna.

Sposób tworzenia wystąpienia `TokenCredential` wykracza poza zakres tego artykułu. Istnieje wiele sposobów tworzenia takiego wystąpienia w zależności od typu tożsamości usługi AAD, której chcesz użyć (jednostki użytkownika, jednostki usługi, grupy itp.). Co najważniejsze, wystąpienie musi zostać rozpoznane jako tożsamość (identyfikator podmiotu zabezpieczeń), do których `TokenCredential` przypisano role. Możesz znaleźć przykłady tworzenia `TokenCredential` klasy:

- [na .NET](/dotnet/api/overview/azure/identity-readme#credential-classes)
- [w języku Java](/java/api/overview/azure/identity-readme#credential-classes)
- [w języku JavaScript](/javascript/api/overview/azure/identity-readme#credential-classes)

W poniższych przykładach używa się jednostki usługi z `ClientSecretCredential` wystąpieniem .

### <a name="in-net"></a>Na .NET

Ta Azure Cosmos DB RBAC jest obecnie obsługiwana w wersji 3 zestawu `preview` [SDK platformy .NET.](sql-api-sdk-dotnet-standard.md)

```csharp
TokenCredential servicePrincipal = new ClientSecretCredential(
    "<azure-ad-tenant-id>",
    "<client-application-id>",
    "<client-application-secret>");
CosmosClient client = new CosmosClient("<account-endpoint>", servicePrincipal);
```

### <a name="in-java"></a>W języku Java

Ta Azure Cosmos DB RBAC jest obecnie obsługiwana w zestawie [Java SDK w wersji 4.](sql-api-sdk-java-v4.md)

```java
TokenCredential ServicePrincipal = new ClientSecretCredentialBuilder()
    .authorityHost("https://login.microsoftonline.com")
    .tenantId("<azure-ad-tenant-id>")
    .clientId("<client-application-id>")
    .clientSecret("<client-application-secret>")
    .build();
CosmosAsyncClient Client = new CosmosClientBuilder()
    .endpoint("<account-endpoint>")
    .credential(ServicePrincipal)
    .build();
```

### <a name="in-javascript"></a>W języku JavaScript

Ta Azure Cosmos DB RBAC jest obecnie obsługiwana w zestawie [SDK języka JavaScript w wersji 3.](sql-api-sdk-node.md)

```javascript
const servicePrincipal = new ClientSecretCredential(
    "<azure-ad-tenant-id>",
    "<client-application-id>",
    "<client-application-secret>");
const client = new CosmosClient({
    "<account-endpoint>",
    aadCredentials: servicePrincipal
});
```

## <a name="auditing-data-requests"></a>Inspekcja żądań danych

W przypadku korzystania Azure Cosmos DB RBAC [dzienniki](cosmosdb-monitor-resource-logs.md) diagnostyczne są rozszerzane o informacje o tożsamości i autoryzacji dla każdej operacji danych. Dzięki temu można przeprowadzić szczegółową inspekcję i pobrać tożsamość aAD używaną dla każdego żądania danych wysyłanego do Azure Cosmos DB konta.

Te dodatkowe informacje przepływają w **kategorii dzienników DataPlaneRequests** i składają się z dwóch dodatkowych kolumn:

- `aadPrincipalId_g` Wyświetla identyfikator podmiotu zabezpieczeń tożsamości usługi AAD, który został użyty do uwierzytelnienia żądania.
- `aadAppliedRoleAssignmentId_g` Pokazuje [przypisanie roli,](#role-assignments) które zostało honorowane podczas autoryzowania żądania.

## <a name="limits"></a>Limity

- Można utworzyć maksymalnie 100 definicji ról i 2000 przypisań ról na Azure Cosmos DB konto.
- Definicje ról można przypisywać tylko do tożsamości usługi Azure AD należących do tej samej dzierżawy usługi Azure AD co Azure Cosmos DB usługi.
- Rozwiązanie grupy usługi Azure AD nie jest obecnie obsługiwane w przypadku tożsamości należących do więcej niż 200 grup.
- Token usługi Azure AD jest obecnie przekazywany jako nagłówek z każdym indywidualnym żądaniem wysłanym do usługi Azure Cosmos DB, zwiększając całkowity rozmiar ładunku.
- Uzyskiwanie dostępu do danych za pomocą usługi Azure AD za [pośrednictwem Eksplorator usługi Azure Cosmos DB](data-explorer.md) nie jest jeszcze obsługiwane. Korzystanie Eksplorator usługi Azure Cosmos DB nadal wymaga, aby użytkownik miał dostęp do klucza podstawowego konta.

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="which-azure-cosmos-db-apis-are-supported-by-rbac"></a>Które interfejsy API usługi Azure Cosmos DB są obsługiwane przez kontrolę dostępu opartą na rolach?

Obecnie obsługiwany jest tylko interfejs API SQL.

### <a name="is-it-possible-to-manage-role-definitions-and-role-assignments-from-the-azure-portal"></a>Czy można zarządzać definicjami ról i przypisaniami ról z poziomu witryny Azure Portal?

Witryna Azure Portal nie udostępnia jeszcze obsługi zarządzania rolami.

### <a name="which-sdks-in-azure-cosmos-db-sql-api-support-rbac"></a>Które zestawy SDK w Azure Cosmos DB API SQL obsługują RBAC?

Zestawy [SDK .NET v3](sql-api-sdk-dotnet-standard.md) [i Java w wersji 4](sql-api-sdk-java-v4.md) są obecnie obsługiwane.

### <a name="is-the-azure-ad-token-automatically-refreshed-by-the-azure-cosmos-db-sdks-when-it-expires"></a>Czy token usługi Azure AD jest automatycznie odświeżany przez zestawy SDK usługi Azure Cosmos DB po jego wygaśnięciu?

Tak.

### <a name="is-it-possible-to-disable-the-usage-of-the-account-primary-key-when-using-rbac"></a>Czy można wyłączyć użycie klucza podstawowego konta w przypadku używania kontroli dostępu opartej na rolach?

Wyłączenie klucza podstawowego konta nie jest obecnie możliwe.

## <a name="next-steps"></a>Następne kroki

- Omówienie bezpiecznego [dostępu do danych w programie Cosmos DB](secure-access-to-data.md).
- Dowiedz się więcej o [kontroli RBAC na Azure Cosmos DB zarządzania.](role-based-access-control.md)