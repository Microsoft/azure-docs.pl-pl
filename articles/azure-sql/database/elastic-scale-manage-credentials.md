---
title: Zarządzanie poświadczeniami w bibliotece klienta Elastic Database
description: Jak ustawić prawidłowy poziom poświadczeń, administrator tylko do odczytu dla aplikacji Elastic Database
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/03/2019
ms.openlocfilehash: 51439edd1d8c7094a5b857821f632ace9e2dea53
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91442760"
---
# <a name="credentials-used-to-access-the-elastic-database-client-library"></a>Poświadczenia używane do uzyskiwania dostępu do biblioteki klienta Elastic Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

[Biblioteka klienta Elastic Database](elastic-database-client-library.md) korzysta z trzech różnych rodzajów poświadczeń w celu uzyskania dostępu do [Menedżera map fragmentu](elastic-scale-shard-map-management.md). W zależności od potrzeb należy użyć poświadczeń z najniższym możliwym poziomem dostępu.

* **Poświadczenia zarządzania**: na potrzeby tworzenia i manipulowania menedżerem map fragmentu. (Zobacz [słownik](elastic-scale-glossary.md)).
* **Poświadczenia dostępu**: Aby uzyskać dostęp do istniejącego Menedżera mapy fragmentu w celu uzyskania informacji na temat fragmentów.
* **Poświadczenia połączenia**: Aby nawiązać połączenie z usługą fragmentów.

Zobacz również [Zarządzanie bazami danych i nazwami logowania w Azure SQL Database](logins-create-manage.md).

## <a name="about-management-credentials"></a>Informacje o poświadczeniach zarządzania

Poświadczenia zarządzania służą do tworzenia obiektu **ShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager)) dla aplikacji, które manipulują mapami fragmentu. (Na przykład zobacz [Dodawanie fragmentu przy użyciu narzędzi Elastic Database](elastic-scale-add-a-shard.md) i [routingu zależnego od danych](elastic-scale-data-dependent-routing.md)). Użytkownik biblioteki klienta skalowania elastycznego tworzy użytkowników SQL i logowania SQL i sprawdza, czy każdy z nich ma przyznane uprawnienia do odczytu i zapisu w globalnej bazie danych map fragmentu i wszystkie bazy danych fragmentu. Te poświadczenia są używane do obsługi globalnej mapy fragmentu i lokalnych map fragmentu, gdy są wykonywane zmiany w mapie fragmentu. Na przykład Użyj poświadczeń zarządzania, aby utworzyć obiekt Menedżera mapy fragmentu (za pomocą **GetSqlShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory.getsqlshardmapmanager), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager)):

```java
// Obtain a shard map manager.
ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager(smmAdminConnectionString,ShardMapManagerLoadPolicy.Lazy);
```

Zmienna **smmAdminConnectionString** jest parametrami połączenia, które zawierają poświadczenia zarządzania. Identyfikator użytkownika i hasło zapewniają dostęp do odczytu i zapisu do bazy danych fragmentu map i poszczególnych fragmentówów. Parametry połączenia zarządzania obejmują również nazwę serwera i nazwę bazy danych, aby zidentyfikować globalną bazę danych map fragmentu. Oto typowe parametry połączenia dla tego celu:

```java
"Server=<yourserver>.database.windows.net;Database=<yourdatabase>;User ID=<yourmgmtusername>;Password=<yourmgmtpassword>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;”
```

Nie używaj wartości w postaci " username@server " — zamiast tego użyj wartości "username".  Wynika to z faktu, że poświadczenia muszą działać zarówno w przypadku bazy danych Menedżera map fragmentu, jak i poszczególnych fragmentów, które mogą znajdować się na różnych serwerach.

## <a name="access-credentials"></a>Poświadczenia dostępu

Podczas tworzenia Menedżera map fragmentu w aplikacji, która nie administruje mapami fragmentu, należy użyć poświadczeń z uprawnieniami tylko do odczytu na globalnej mapie fragmentu. Informacje pobierane z globalnej mapy fragmentu pod tymi poświadczeniami są używane do [routingu zależnego od danych](elastic-scale-data-dependent-routing.md) i do wypełniania pamięci podręcznej mapy fragmentu na kliencie. Poświadczenia są udostępniane za pomocą tego samego wzorca wywołania do **GetSqlShardMapManager**:

```java
// Obtain shard map manager.
ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager(smmReadOnlyConnectionString, ShardMapManagerLoadPolicy.Lazy);  
```

Zwróć uwagę na użycie **smmReadOnlyConnectionString** , aby odzwierciedlić użycie różnych poświadczeń dla tego dostępu w imieniu użytkowników **niebędących administratorami** : poświadczenia te nie powinny udostępniać uprawnień do zapisu na globalnej mapie fragmentu.

## <a name="connection-credentials"></a>Poświadczenia połączenia

Dodatkowe poświadczenia są potrzebne podczas korzystania z metody **OpenConnectionForKey**  ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper.listshardmapper.openconnectionforkey), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey)) w celu uzyskania dostępu do fragmentu skojarzonego z kluczem fragmentowania. Te poświadczenia muszą zapewniać uprawnienia dostępu tylko do odczytu do lokalnych tabel mapy fragmentu znajdujących się w fragmentu. Jest to konieczne do przeprowadzenia walidacji połączenia dla routingu zależnego od danych na fragmentu. Ten fragment kodu umożliwia dostęp do danych w kontekście routingu zależnego od danych:

```csharp
using (SqlConnection conn = rangeMap.OpenConnectionForKey<int>(targetWarehouse, smmUserConnectionString, ConnectionOptions.Validate))
```

W tym przykładzie **smmUserConnectionString** przechowuje parametry połączenia dla poświadczeń użytkownika. Poniżej przedstawiono typowe parametry połączenia dla poświadczeń użytkownika: Azure SQL Database

```java
"User ID=<yourusername>; Password=<youruserpassword>; Trusted_Connection=False; Encrypt=True; Connection Timeout=30;”  
```

Podobnie jak w przypadku poświadczeń administratora, nie należy używać wartości w postaci " username@server ". Zamiast tego wystarczy użyć "username".  Należy również zauważyć, że parametry połączenia nie zawierają nazwy serwera i bazy danych. Wynika to z faktu, że wywołanie **OpenConnectionForKey** automatycznie kieruje połączenie do poprawnej fragmentu na podstawie klucza. W związku z tym nazwa bazy danych i nazwa serwera nie są podane.

## <a name="see-also"></a>Zobacz też

[Zarządzanie bazami danych i logowaniami w usłudze Azure SQL Database](logins-create-manage.md)

[Zabezpieczanie bazy danych SQL](security-overview.md)

[zadania Elastic Database](elastic-jobs-overview.md)

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]
