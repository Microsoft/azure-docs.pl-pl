---
title: Skonfiguruj połączenie z kontem usługi Cosmos DB przy użyciu tożsamości zarządzanej (wersja zapoznawcza)
titleSuffix: Azure Cognitive Search
description: Dowiedz się, jak skonfigurować połączenie indeksatora z kontem Cosmos DB przy użyciu tożsamości zarządzanej (wersja zapoznawcza)
manager: luisca
author: markheff
ms.author: maheff
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/18/2020
ms.openlocfilehash: f65aa4b307108682fa6e190a229e9d82b6efdec0
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/18/2020
ms.locfileid: "88553206"
---
# <a name="set-up-an-indexer-connection-to-a-cosmos-db-database-using-a-managed-identity-preview"></a>Konfigurowanie połączenia indeksatora z bazą danych Cosmos DB przy użyciu tożsamości zarządzanej (wersja zapoznawcza)

> [!IMPORTANT] 
> Obsługa konfigurowania połączenia ze źródłem danych przy użyciu tożsamości zarządzanej jest obecnie dostępna w publicznej wersji zapoznawczej. Funkcje wersji zapoznawczej są dostępne bez umowy dotyczącej poziomu usług i nie są zalecane w przypadku obciążeń produkcyjnych.

Na tej stronie opisano sposób konfigurowania połączenia indeksatora z bazą danych Azure Cosmos DB przy użyciu tożsamości zarządzanej zamiast podawania poświadczeń w parametrach połączenia obiektu źródła danych.

Aby dowiedzieć się więcej o tej funkcji, zalecamy zapoznanie się z tym, co to jest indeksator i jak skonfigurować indeksator dla źródła danych. Więcej informacji można znaleźć w następujących linkach:
* [Omówienie indeksatora](search-indexer-overview.md)
* [Indeksator usługi Azure Cosmos DB](search-howto-index-cosmosdb.md)

## <a name="set-up-a-connection-using-a-managed-identity"></a>Konfigurowanie połączenia przy użyciu tożsamości zarządzanej

### <a name="1---turn-on-system-assigned-managed-identity"></a>1 — Włączanie tożsamości zarządzanej przypisanej przez system

Gdy jest włączona tożsamość zarządzana przypisana przez system, platforma Azure tworzy tożsamość usługi wyszukiwania, która może służyć do uwierzytelniania w innych usługach platformy Azure w ramach tej samej dzierżawy i subskrypcji. Tej tożsamości można następnie użyć w przypisaniach kontroli dostępu opartej na rolach (RBAC), które umożliwiają dostęp do danych podczas indeksowania.

![Włącz tożsamość zarządzaną przypisaną przez system](./media/search-managed-identities/turn-on-system-assigned-identity.png "Włącz tożsamość zarządzaną przypisaną przez system")

Po wybraniu pozycji **Zapisz** zostanie wyświetlony identyfikator obiektu przypisany do usługi wyszukiwania.

![Identyfikator obiektu](./media/search-managed-identities/system-assigned-identity-object-id.png "Identyfikator obiektu")
 
### <a name="2---add-a-role-assignment"></a>2 — Dodawanie przypisania roli

W tym kroku nadajesz usłudze Azure Wyszukiwanie poznawcze uprawnienia do odczytywania danych z bazy danych Cosmos DB.

1. W Azure Portal przejdź do konta Cosmos DB zawierającego dane, które chcesz zindeksować.
2. Wybieranie **kontroli dostępu (IAM)**
3. Wybierz pozycję **Dodaj** , a następnie **Dodaj przypisanie roli**

    ![Dodaj przypisanie roli](./media/search-managed-identities/add-role-assignment-cosmos-db.png "Dodaj przypisanie roli")

4. Wybierz **rolę czytnika konta Cosmos DB**
5. Pozostaw **uprawnienia do przypisywania** jako **użytkownik usługi Azure AD, Grupa lub nazwa główna usługi**
6. Wyszukaj swoją usługę wyszukiwania, wybierz ją, a następnie wybierz pozycję **Zapisz** .

    ![Dodawanie przypisania roli czytnika i dostępu do danych](./media/search-managed-identities/add-role-assignment-cosmos-db-account-reader-role.png "Dodawanie przypisania roli czytnika i dostępu do danych")

### <a name="3---create-the-data-source"></a>3 — Tworzenie źródła danych

[Interfejs API REST](https://docs.microsoft.com/rest/api/searchservice/create-data-source), Azure Portal i [zestaw SDK platformy .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource?view=azure-dotnet) obsługują parametry połączenia tożsamości zarządzanej. Poniżej przedstawiono przykład sposobu tworzenia źródła danych do indeksowania danych z Cosmos DB przy użyciu [interfejsu API REST](https://docs.microsoft.com/rest/api/searchservice/create-data-source) i parametrów połączenia zarządzanej tożsamości. Format parametrów połączenia tożsamości zarządzanej jest taki sam dla interfejsu API REST, zestawu .NET SDK i Azure Portal.

Podczas uwierzytelniania przy użyciu tożsamości zarządzanych **poświadczenia** nie będą zawierać klucza konta.

```
POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
Content-Type: application/json
api-key: [Search service admin key]

{
    "name": "cosmos-db-datasource",
    "type": "cosmosdb",
    "credentials": {
        "connectionString": "Database=sql-test-db;ResourceId=/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/cosmos-db-resource-group/providers/Microsoft.DocumentDB/databaseAccounts/my-cosmos-db-account/;"
    },
    "container": { "name": "myCollection", "query": null },
    "dataChangeDetectionPolicy": {
        "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName": "_ts"
    }
}
```    

Treść żądania zawiera definicję źródła danych, która powinna zawierać następujące pola:

| Pole   | Opis |
|---------|-------------|
| **Nazwij** | Wymagany. Wybierz dowolną nazwę reprezentującą obiekt źródła danych. |
|**Wprowadź**| Wymagany. Musi być `cosmosdb` . |
|**uwierzytelniające** | Wymagany. <br/><br/>Podczas nawiązywania połączenia przy użyciu tożsamości zarządzanej powinien być format **poświadczeń** : *Database = [Database-Name]; ResourceId = [Resource-ID-String];(rodzaju interfejsu API = [API-Kind];)*<br/> <br/>Format ResourceId: *ResourceID =/SUBSCRIPTIONS/**Identyfikator subskrypcji**/resourceGroups/**nazwę grupy zasobów**/Providers/Microsoft.DocumentDB/databaseAccounts/**nazwę konta Cosmos DB**/;*<br/><br/>W przypadku kolekcji SQL parametry połączenia nie wymagają rodzaju interfejsu API.<br/><br/>W przypadku kolekcji MongoDB Dodaj **rodzaju interfejsu API = MongoDB** do parametrów połączenia. <br/><br/>[Aby uzyskać](https://aka.ms/azure-cognitive-search/indexer-preview) dostęp do wersji zapoznawczej i zapoznać się z informacjami na temat sposobu formatowania poświadczeń, w przypadku wykresów Gremlin i tabel Cassandra.<br/>|
| **kontener** | Zawiera następujące elementy: <br/>**Nazwa**: wymagane. Określ identyfikator kolekcji baz danych do indeksowania.<br/>**zapytanie**: opcjonalne. Możesz określić zapytanie, aby spłaszczyć dowolny dokument JSON do prostego schematu, który usługa Azure Wyszukiwanie poznawcze może indeksować.<br/>W przypadku interfejsu API MongoDB, interfejsu API Gremlin i interfejs API Cassandra zapytania nie są obsługiwane. |
| **dataChangeDetectionPolicy** | Zalecane |
|**dataDeletionDetectionPolicy** | Opcjonalne |

### <a name="4---create-the-index"></a>4 — Tworzenie indeksu

Indeks określa pola w dokumencie, atrybuty i inne konstrukcje, które kształtują środowisko wyszukiwania.

Oto jak utworzyć indeks z polem z możliwością wyszukiwania `booktitle` :

```
POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "my-target-index",
    "fields": [
    { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
    { "name": "booktitle", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
    ]
}
```

Aby uzyskać więcej informacji na temat tworzenia indeksów, zobacz [Tworzenie indeksu](https://docs.microsoft.com/rest/api/searchservice/create-index)

### <a name="5---create-the-indexer"></a>5 — Tworzenie indeksatora

Indeksator łączy źródło danych z docelowym indeksem wyszukiwania i zawiera harmonogram do automatyzowania odświeżania danych.

Po utworzeniu indeksu i źródła danych można przystąpić do tworzenia indeksatora.

Przykładowa definicja indeksatora:

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "cosmos-db-indexer",
      "dataSourceName" : "cosmos-db-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }
```

Ten indeksator będzie uruchamiany co dwie godziny (interwał harmonogramu jest ustawiony na wartość "PT2H"). Aby uruchomić indeksator co 30 minut, ustaw interwał na wartość "PT30M". Najkrótszy obsługiwany interwał to 5 minut. Harmonogram jest opcjonalny — w przypadku pominięcia, indeksator jest uruchamiany tylko raz, gdy zostanie utworzony. Można jednak uruchomić indeksator na żądanie w dowolnym momencie.   

Aby uzyskać więcej informacji na temat interfejsu API tworzenia indeksatora, zapoznaj się z tematem [Tworzenie indeksatora](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

Więcej informacji o definiowaniu harmonogramów indeksatorów znajduje się w temacie [jak zaplanować indeksatory dla platformy Azure wyszukiwanie poznawcze](search-howto-schedule-indexers.md).

## <a name="see-also"></a>Zobacz też

Dowiedz się więcej o Cosmos DB indeksatorów:
* [Indeksator usługi Azure Cosmos DB](search-howto-index-cosmosdb.md)
