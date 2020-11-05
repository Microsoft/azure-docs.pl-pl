---
title: Używanie tokenów zasobów Azure Cosmos DB z zestawem SDK Gremlin
description: Dowiedz się, jak tworzyć tokeny zasobów i korzystać z nich w celu uzyskiwania dostępu do bazy danych programu Graph.
author: christopheranderson
ms.author: chrande
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 09/06/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 22c048b748806404ccfa580e660552a1744f3781
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2020
ms.locfileid: "93361697"
---
# <a name="use-azure-cosmos-db-resource-tokens-with-the-gremlin-sdk"></a>Używanie tokenów zasobów Azure Cosmos DB z zestawem SDK Gremlin
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

W tym artykule wyjaśniono, jak używać [tokenów zasobów Azure Cosmos DB](secure-access-to-data.md) , aby uzyskać dostęp do bazy danych grafu za pomocą zestawu SDK Gremlin.

## <a name="create-a-resource-token"></a>Tworzenie tokenu zasobu

Zestaw Apache TinkerPop Gremlin SDK nie ma interfejsu API, który służy do tworzenia tokenów zasobów. Termin *token zasobu* jest koncepcją Azure Cosmos DB. Aby utworzyć tokeny zasobów, Pobierz [zestaw SDK Azure Cosmos DB](sql-api-sdk-dotnet.md). Jeśli aplikacja musi utworzyć tokeny zasobów i korzystać z nich w celu uzyskania dostępu do bazy danych programu Graph, wymagane są dwa oddzielne zestawy SDK.

Hierarchia modelu obiektów powyżej tokenów zasobów jest zilustrowana w następującym konspekcie:

- **Konto Azure Cosmos DB** — jednostka najwyższego poziomu, z którą jest skojarzony system DNS (na przykład `contoso.gremlin.cosmos.azure.com` ).
  - **Baza danych Azure Cosmos DB**
    - **Użytkownik**
      - **Uprawnienie**
        - **Token** -właściwość obiektu uprawnienia, która oznacza, jakie akcje są dozwolone lub odrzucane.

Token zasobu używa następującego formatu: `"type=resource&ver=1&sig=<base64 string>;<base64 string>;"` . Ten ciąg jest nieprzezroczysty dla klientów i powinien być używany bez modyfikacji ani interpretacji.

```csharp
// Notice that document client is created against .NET SDK endpoint, rather than Gremlin.
DocumentClient client = new DocumentClient(
  new Uri("https://contoso.documents.azure.com:443/"), 
  "<primary key>", 
  new ConnectionPolicy 
  {
    EnableEndpointDiscovery = false, 
    ConnectionMode = ConnectionMode.Direct 
  });

  // Read specific permission to obtain a token.
  // The token isn't returned during the ReadPermissionReedAsync() call.
  // The call succeeds only if database id, user id, and permission id already exist. 
  // Note that <database id> is not a database name. It is a base64 string that represents the database identifier, for example "KalVAA==".
  // Similar comment applies to <user id> and <permission id>.
  Permission permission = await client.ReadPermissionAsync(UriFactory.CreatePermissionUri("<database id>", "<user id>", "<permission id>"));

  Console.WriteLine("Obtained token {0}", permission.Token);
}
```

## <a name="use-a-resource-token"></a>Użycie tokenu zasobu
Tokenów zasobów można używać bezpośrednio jako właściwości "Password" podczas konstruowania klasy GremlinServer.

```csharp
// The Gremlin application needs to be given a resource token. It can't discover the token on its own.
// You can obtain the token for a given permission by using the Azure Cosmos DB SDK, or you can pass it into the application as a command line argument or configuration value.
string resourceToken = GetResourceToken();

// Configure the Gremlin server to use a resource token rather than a primary key.
GremlinServer server = new GremlinServer(
  "contoso.gremlin.cosmosdb.azure.com",
  port: 443,
  enableSsl: true,
  username: "/dbs/<database name>/colls/<collection name>",

  // The format of the token is "type=resource&ver=1&sig=<base64 string>;<base64 string>;".
  password: resourceToken);

  using (GremlinClient gremlinClient = new GremlinClient(server, new GraphSON2Reader(), new GraphSON2Writer(), GremlinClient.GraphSON2MimeType))
  {
      await gremlinClient.SubmitAsync("g.V().limit(1)");
  }
```

Takie samo podejście działa we wszystkich zestawach SDK TinkerPop Gremlin.

```java
Cluster.Builder builder = Cluster.build();

AuthProperties authenticationProperties = new AuthProperties();
authenticationProperties.with(AuthProperties.Property.USERNAME,
    String.format("/dbs/%s/colls/%s", "<database name>", "<collection name>"));

// The format of the token is "type=resource&ver=1&sig=<base64 string>;<base64 string>;".
authenticationProperties.with(AuthProperties.Property.PASSWORD, resourceToken);

builder.authProperties(authenticationProperties);
```

## <a name="limit"></a>Limit

Za pomocą jednego konta Gremlin można wydać nieograniczoną liczbę tokenów. Można jednak używać tylko do 100 tokenów jednocześnie w ciągu 1 godziny. Jeśli aplikacja przekracza limit tokenów na godzinę, żądanie uwierzytelnienia zostanie odrzucone i zostanie wyświetlony następujący komunikat o błędzie: "Przekroczono dozwolony limit tokenów zasobów 100, który może być używany współbieżnie". Nie działa to blisko aktywnych połączeń, które używają określonych tokenów do zwolnienia miejsc dla nowych tokenów. Aparat bazy danych Azure Cosmos DB Gremlin śledzi unikatowe tokeny w ciągu godziny bezpośrednio przed żądaniem uwierzytelnienia.

## <a name="permission"></a>Uprawnienie

Typowym błędem napotykanym przez aplikacje podczas korzystania z tokenów zasobów jest "niewystarczające uprawnienia podane w nagłówku autoryzacji dla odpowiedniego żądania. Spróbuj ponownie, używając innego nagłówka autoryzacji. " Ten błąd jest zwracany, gdy przechodzenie Gremlin próbuje napisać krawędY lub wierzchołek, ale token zasobu przyznaje tylko uprawnienia do *odczytu* . Zbadaj przechodzenie, aby sprawdzić, czy zawiera on następujące kroki: *. addV ()* , *. addE ()* , *. Drop ()* , lub *. Property ()*.

## <a name="next-steps"></a>Następne kroki
* [Kontrola dostępu oparta na rolach (Azure RBAC)](role-based-access-control.md) w Azure Cosmos DB
* [Dowiedz się, jak zabezpieczyć dostęp do danych](secure-access-to-data.md) w Azure Cosmos DB
