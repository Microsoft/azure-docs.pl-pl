---
title: Konfigurowanie kontroli dostępu opartej na rolach dla konta Azure Cosmos DB przy użyciu usługi Azure AD
description: Dowiedz się, jak skonfigurować kontrolę dostępu opartą na rolach przy użyciu Azure Active Directory dla konta Azure Cosmos DB
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: how-to
ms.date: 03/24/2021
ms.author: thweiss
ms.openlocfilehash: f8028d69e376e2b71549be52267e2f6cbdb1f8ce
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105568664"
---
# <a name="configure-role-based-access-control-with-azure-active-directory-for-your-azure-cosmos-db-account-preview"></a>Konfigurowanie kontroli dostępu opartej na rolach za pomocą Azure Active Directory dla konta Azure Cosmos DB (wersja zapoznawcza)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!IMPORTANT]
> Azure Cosmos DB kontrola dostępu oparta na rolach jest obecnie dostępna w wersji zapoznawczej. Ta wersja zapoznawcza jest dostępna bez Umowa dotycząca poziomu usług i nie jest zalecana w przypadku obciążeń produkcyjnych. Aby uzyskać więcej informacji, zobacz [dodatkowe warunki użytkowania wersji](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)zapoznawczych Microsoft Azure.

> [!NOTE]
> Ten artykuł dotyczy kontroli dostępu opartej na rolach dla operacji płaszczyzny danych w Azure Cosmos DB. Jeśli używasz operacji płaszczyzny zarządzania, zobacz [Kontrola dostępu oparta na rolach](role-based-access-control.md) zastosowana do Twojego artykułu dotyczącego Twojej płaszczyzny zarządzania.

Azure Cosmos DB udostępnia wbudowany system kontroli dostępu opartej na rolach (RBAC), który umożliwia:

- Uwierzytelniaj żądania danych za pomocą tożsamości usługi Azure Active Directory (Azure AD).
- Autoryzuj żądania danych przy użyciu szczegółowego, opartego na rolach modelu uprawnień.

## <a name="concepts"></a>Pojęcia

Azure Cosmos DB RBAC płaszczyzny danych jest oparta na pojęciach, które są często dostępne w innych systemach RBAC, takich jak [Azure RBAC](../role-based-access-control/overview.md):

- [Model uprawnień](#permission-model) składa się z zestawu **akcji**; Każda z tych akcji jest mapowana na jedną lub wiele operacji bazy danych. Niektóre przykłady akcji obejmują odczytywanie elementu, pisanie elementu lub wykonywanie zapytania.
- Azure Cosmos DB użytkownicy tworzą **[definicje ról](#role-definitions)** zawierające listę dozwolonych akcji.
- Definicje ról są przypisywane do określonych tożsamości usługi Azure AD za pomocą **[przypisań ról](#role-assignments)**. Przypisanie roli definiuje również zakres, do którego odnosi się definicja roli; obecnie trzy zakresy są obecnie:
    - Konto Azure Cosmos DB,
    - Baza danych Azure Cosmos DB,
    - Kontener Azure Cosmos DB.

  :::image type="content" source="./media/how-to-setup-rbac/concepts.png" alt-text="Koncepcje RBAC":::

> [!NOTE]
> Azure Cosmos DB RBAC nie uwidacznia obecnie żadnych wbudowanych definicji ról.

## <a name="permission-model"></a><a id="permission-model"></a> Model uprawnień

W poniższej tabeli wymieniono wszystkie akcje uwidocznione przez model uprawnień.

| Nazwa | Odpowiednie operacje bazy danych |
|---|---|
| `Microsoft.DocumentDB/databaseAccounts/readMetadata` | Odczytywanie metadanych konta. Szczegóły można znaleźć w temacie [żądania metadanych](#metadata-requests) . |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/create` | Utwórz nowy element. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/read` | Odczytaj poszczególne elementy według identyfikatora i klucza partycji (punkt-odczyt). |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/replace` | Zastąp istniejący element. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/upsert` | "Upsert" element, który oznacza, że jeśli nie istnieje, lub zastąp go, jeśli istnieje. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/delete` | Usuń element. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/executeQuery` | Wykonaj [zapytanie SQL](sql-query-getting-started.md). |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/readChangeFeed` | Odczytaj ze [źródła zmian](read-change-feed.md)kontenera. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/executeStoredProcedure` | Wykonaj [procedurę składowaną](stored-procedures-triggers-udfs.md). |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/manageConflicts` | Zarządzanie [konfliktami](conflict-resolution-policies.md) dla kont regionów z wielokrotnym zapisem (czyli list i usuwanie elementów ze źródła konfliktów). |

Symbole wieloznaczne są obsługiwane na poziomie *kontenera* i *elementów* :

- `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/*`
- `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/*`

> [!IMPORTANT]
> Ten model uprawnień obejmuje operacje bazy danych, które umożliwiają odczytywanie i zapisywanie danych. **Nie obejmuje żadnego** rodzaju operacji zarządzania, takich jak tworzenie kontenerów lub zmiana ich przepływności. Aby uwierzytelnić operacje zarządzania przy użyciu tożsamości usługi AAD, należy zamiast tego użyć kontroli [RBAC platformy Azure](role-based-access-control.md) .

### <a name="metadata-requests"></a><a id="metadata-requests"></a> Żądania metadanych

W przypadku korzystania z zestawów SDK Azure Cosmos DB te zestawy SDK wydają żądania metadanych tylko do odczytu podczas inicjalizacji i do obsłużenia określonych żądań danych. Te żądania metadanych pobierają różne szczegóły konfiguracji, takie jak: 

- Globalna Konfiguracja konta, która obejmuje regiony platformy Azure, w których konto jest dostępne.
- Klucz partycji kontenerów lub ich zasad indeksowania.
- Lista partycji fizycznych, które tworzą kontener i ich adresy.

*Nie* pobierają one żadnych danych przechowywanych na koncie.

W celu zapewnienia najlepszej przejrzystości naszego modelu uprawnień te żądania metadanych są jawnie objęte `Microsoft.DocumentDB/databaseAccounts/readMetadata` akcją. Ta akcja powinna być dozwolona w każdej sytuacji, w której konto Azure Cosmos DB jest dostępne za pomocą jednego z Azure Cosmos DB zestawów SDK. Można je przypisać (poprzez przypisanie roli) na dowolnym poziomie w hierarchii Azure Cosmos DB (to jest, konto, baza danych lub kontener).

Rzeczywiste żądania metadanych dozwolone przez `Microsoft.DocumentDB/databaseAccounts/readMetadata` akcję zależą od zakresu, do którego jest przypisana akcja:

| Zakres | Żądania dozwolone przez akcję |
|---|---|
| Konto | — Wyświetlanie listy baz danych w ramach konta<br>-Dla każdej bazy danych w ramach konta dozwolone akcje w zakresie bazy danych |
| baza danych | — Odczytywanie metadanych bazy danych<br>— Wyświetlanie listy kontenerów w bazie danych<br>-Dla każdego kontenera w ramach bazy danych dozwolone akcje w zakresie kontenera |
| Kontener | Odczytywanie metadanych kontenera<br>— Wyświetlanie listy partycji fizycznych w kontenerze<br>-Rozpoznawanie adresów poszczególnych partycji fizycznych |

## <a name="create-role-definitions"></a><a id="role-definitions"></a> Tworzenie definicji ról

Podczas tworzenia definicji roli należy podać:

- Nazwa konta Azure Cosmos DB.
- Grupa zasobów zawierająca Twoje konto.
- Typ definicji roli; tylko `CustomRole` obecnie obsługiwane.
- Nazwa definicji roli.
- Lista [akcji](#permission-model) , które mają być dozwolone przez rolę.
- Jeden lub wiele zakresów, do których można przypisać definicję roli; obsługiwane zakresy to:
    - `/` (poziom konta),
    - `/dbs/<database-name>` (na poziomie bazy danych),
    - `/dbs/<database-name>/colls/<container-name>` (na poziomie kontenera).

> [!NOTE]
> Opisane poniżej operacje są obecnie dostępne w:
> - Azure PowerShell: [AZ. CosmosDB w wersji 2.0.1 — wersja zapoznawcza](https://www.powershellgallery.com/packages/Az.CosmosDB/2.0.1-preview)
> - Interfejs wiersza polecenia platformy Azure: [wersja rozszerzenia "cosmosdb-Preview" 0.4.0](https://github.com/Azure/azure-cli-extensions/tree/master/src/cosmosdb-preview)

### <a name="using-azure-powershell"></a>Korzystanie z programu Azure PowerShell

Utwórz rolę o nazwie *MyReadOnlyRole* , która zawiera tylko akcje odczytu:

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

Utwórz rolę o nazwie *MyReadWriteRole* , która zawiera wszystkie akcje:

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

Utwórz listę utworzonych przez siebie definicji ról, aby pobrać ich identyfikatory:

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

Utwórz rolę o nazwie *MyReadOnlyRole* , która zawiera tylko akcje odczytu:

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

Utwórz rolę o nazwie *MyReadWriteRole* , która zawiera wszystkie akcje:

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

Utwórz listę utworzonych przez siebie definicji ról, aby pobrać ich identyfikatory:

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

Po utworzeniu definicji ról można je skojarzyć z tożsamościami usługi AAD. Podczas tworzenia przypisania roli należy podać:

- Nazwa konta Azure Cosmos DB.
- Grupa zasobów zawierająca Twoje konto.
- Identyfikator definicji roli do przypisania.
- Identyfikator podmiotu zabezpieczeń tożsamości, do której należy przypisać definicję roli.
- Zakres przypisania roli; obsługiwane zakresy to:
    - `/` (poziom konta)
    - `/dbs/<database-name>` (na poziomie bazy danych)
    - `/dbs/<database-name>/colls/<container-name>` (na poziomie kontenera)

  Zakres musi być zgodny lub być podzakresem jednego z zakresów przypisanych do definicji roli.

> [!NOTE]
> Jeśli chcesz utworzyć przypisanie roli dla jednostki usługi, upewnij się, że używasz jego **identyfikatora obiektu** , który znajduje się w sekcji **aplikacje dla przedsiębiorstw** w bloku portalu **Azure Active Directory** .

> [!NOTE]
> Opisane poniżej operacje są obecnie dostępne w:
> - Azure PowerShell: [AZ. CosmosDB w wersji 2.0.1 — wersja zapoznawcza](https://www.powershellgallery.com/packages/Az.CosmosDB/2.0.1-preview)
> - Interfejs wiersza polecenia platformy Azure: [wersja rozszerzenia "cosmosdb-Preview" 0.4.0](https://github.com/Azure/azure-cli-extensions/tree/master/src/cosmosdb-preview)

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

Aby użyć Azure Cosmos DB RBAC w aplikacji, należy zaktualizować sposób inicjowania zestawu Azure Cosmos DB SDK. Zamiast przekazywać klucz podstawowy konta, należy przekazać wystąpienie `TokenCredential` klasy. To wystąpienie zawiera zestaw SDK Azure Cosmos DB z kontekstem wymaganym do pobrania tokenu usługi AAD w imieniu tożsamości, której chcesz użyć.

Sposób tworzenia `TokenCredential` wystąpienia wykracza poza zakres tego artykułu. Istnieje wiele sposobów tworzenia takiego wystąpienia w zależności od typu tożsamości usługi AAD, która ma być używana (główna nazwa użytkownika, nazwa główna usługi, Grupa itp.). Co najważniejsze, `TokenCredential` wystąpienie musi rozpoznać tożsamość (Identyfikator podmiotu zabezpieczeń), do której przypisano Twoje role. Przykłady tworzenia klasy można znaleźć `TokenCredential` :

- [w programie .NET](/dotnet/api/overview/azure/identity-readme#credential-classes)
- [w języku Java](/java/api/overview/azure/identity-readme#credential-classes)
- [w języku JavaScript](/javascript/api/overview/azure/identity-readme#credential-classes)

W poniższych przykładach użyto jednostki usługi z `ClientSecretCredential` wystąpieniem.

### <a name="in-net"></a>W programie .NET

Azure Cosmos DB RBAC jest obecnie obsługiwana w `preview` wersji [zestawu .NET SDK v3](sql-api-sdk-dotnet-standard.md).

```csharp
TokenCredential servicePrincipal = new ClientSecretCredential(
    "<azure-ad-tenant-id>",
    "<client-application-id>",
    "<client-application-secret>");
CosmosClient client = new CosmosClient("<account-endpoint>", servicePrincipal);
```

### <a name="in-java"></a>W języku Java

Azure Cosmos DB RBAC jest obecnie obsługiwana w [zestawie Java SDK v4](sql-api-sdk-java-v4.md).

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

Azure Cosmos DB RBAC jest obecnie obsługiwana w [zestawie JavaScript SDK v3](sql-api-sdk-node.md).

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

Korzystając z Azure Cosmos DB RBAC, [dzienniki diagnostyczne](cosmosdb-monitor-resource-logs.md) są rozszerzane o informacje o tożsamości i autoryzacji dla każdej operacji na danych. Dzięki temu można przeprowadzić szczegółową inspekcję i pobrać tożsamość usługi AAD użytą dla każdego żądania danych wysyłanego do konta Azure Cosmos DB.

Te dodatkowe informacje są przesyłane w kategorii dziennika **DataPlaneRequests** i składają się z dwóch dodatkowych kolumn:

- `aadPrincipalId_g` pokazuje Identyfikator podmiotu zabezpieczeń usługi AAD, który został użyty do uwierzytelnienia żądania.
- `aadAppliedRoleAssignmentId_g` pokazuje [przypisanie roli](#role-assignments) , które zostało uznane przy autoryzacji żądania.

## <a name="limits"></a>Limity

- Można utworzyć maksymalnie 100 definicji ról i przypisań ról 2 000 na konto Azure Cosmos DB.
- Rozwiązanie grupy usługi Azure AD nie jest obecnie obsługiwane w przypadku tożsamości należących do więcej niż 200 grup.
- Token usługi Azure AD jest obecnie przekazywany jako nagłówek z każdym indywidualnym żądaniem wysyłanym do usługi Azure Cosmos DB, zwiększając ogólny rozmiar ładunku.
- Uzyskiwanie dostępu do danych za pomocą usługi Azure AD za pomocą [eksploratora Azure Cosmos DB](data-explorer.md) nie jest jeszcze obsługiwane. Korzystanie z Eksploratora Azure Cosmos DB nadal wymaga, aby użytkownik miał teraz dostęp do klucza podstawowego konta.

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="which-azure-cosmos-db-apis-are-supported-by-rbac"></a>Które interfejsy API usługi Azure Cosmos DB są obsługiwane przez kontrolę dostępu opartą na rolach?

Obecnie obsługiwany jest tylko interfejs API SQL.

### <a name="is-it-possible-to-manage-role-definitions-and-role-assignments-from-the-azure-portal"></a>Czy można zarządzać definicjami ról i przypisaniami ról z poziomu witryny Azure Portal?

Witryna Azure Portal nie udostępnia jeszcze obsługi zarządzania rolami.

### <a name="which-sdks-in-azure-cosmos-db-sql-api-support-rbac"></a>Które zestawy SDK w Azure Cosmos DB SQL API obsługują RBAC?

Zestawy SDK [platformy .NET v3](sql-api-sdk-dotnet-standard.md) i [Java v4](sql-api-sdk-java-v4.md) są obecnie obsługiwane.

### <a name="is-the-azure-ad-token-automatically-refreshed-by-the-azure-cosmos-db-sdks-when-it-expires"></a>Czy token usługi Azure AD jest automatycznie odświeżany przez zestawy SDK usługi Azure Cosmos DB po jego wygaśnięciu?

Tak.

### <a name="is-it-possible-to-disable-the-usage-of-the-account-primary-key-when-using-rbac"></a>Czy można wyłączyć użycie klucza podstawowego konta w przypadku używania kontroli dostępu opartej na rolach?

Wyłączenie klucza podstawowego konta nie jest obecnie możliwe.

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z omówieniem [bezpiecznego dostępu do danych w Cosmos DB](secure-access-to-data.md).
- Dowiedz się więcej o kontroli [RBAC na potrzeby Azure Cosmos DB zarządzania](role-based-access-control.md).