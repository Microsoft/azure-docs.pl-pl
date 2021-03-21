---
title: Skalowanie bazy danych w poziomie
description: Jak używać biblioteki klienckiej ShardMapManager, Elastic Database
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: 03bf92a2d77fb262ed6506bf18c0d27006e435a7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103201191"
---
# <a name="scale-out-databases-with-the-shard-map-manager"></a>Skalowanie baz danych za pomocą Menedżera mapy fragmentu
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Aby łatwo skalować bazy danych na Azure SQL Database, użyj Menedżera mapy fragmentu. Menedżer mapy fragmentu to specjalna baza danych, która przechowuje globalne informacje o mapowaniu wszystkich fragmentów (bazy danych) w zestawie fragmentu. Metadane umożliwiają aplikacji łączenie się z poprawną bazą danych na podstawie wartości **klucza fragmentowania**. Ponadto każdy fragmentu w zestawie zawiera mapy, które śledzą lokalne dane fragmentu (znane jako **podfragmentów**).

![Zarządzanie mapami fragmentów](./media/elastic-scale-shard-map-management/glossary.png)

Zrozumienie, jak skonstruowane są te mapy, ma kluczowe znaczenie dla zarządzania mapami fragmentu. Jest to realizowane za pomocą klasy ShardMapManager ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager)), która znajduje się w [bibliotece klienta Elastic Database](elastic-database-client-library.md) do zarządzania mapami fragmentu.  

## <a name="shard-maps-and-shard-mappings"></a>Mapy fragmentu i mapowania fragmentu

Dla każdego fragmentuu należy wybrać typ mapy fragmentu do utworzenia. Wybór zależy od architektury bazy danych:

1. Pojedyncze dzierżawy na bazę danych  
2. Wiele dzierżawców na bazę danych (dwa typy):
   1. Mapowanie listy
   2. Mapowanie zakresu

W przypadku modelu z jedną dzierżawą Utwórz mapę **list mapowania** fragmentu. Model o pojedynczej dzierżawie przypisuje jedną bazę danych na dzierżawcę. Jest to skuteczny model dla deweloperów SaaS, który upraszcza zarządzanie mapami fragmentu.

![Mapowanie listy][1]

Model wielu dzierżawców przypisuje kilka dzierżawców do pojedynczej bazy danych (i można dystrybuować grupy dzierżawców w wielu bazach danych). Użyj tego modelu, gdy oczekuje się, że każda dzierżawa będzie miała małe potrzeby w zakresie danych. W tym modelu Przypisz zakres dzierżawców do bazy danych przy użyciu **mapowania zakresu**.

![Mapowanie zakresu][2]

Lub można zaimplementować model bazy danych z wieloma dzierżawcami przy użyciu *mapowania listy* , aby przypisać wiele dzierżawców do pojedynczej bazy danych. Na przykład DB1 jest używany do przechowywania informacji o IDENTYFIKATORze dzierżawy 1 i 5, a w programie DB2 są przechowywane dane dla dzierżawców 7 i dzierżawców 10.

![Wiele dzierżawców w pojedynczej bazie danych][3]

### <a name="supported-types-for-sharding-keys"></a>Obsługiwane typy dla kluczy fragmentowania

Skalowanie elastyczne obsługuje następujące typy jako klucze fragmentowania:

| .NET | Java |
| --- | --- |
| liczba całkowita |liczba całkowita |
| długi |długi |
| guid |uuid |
| Byte []  |Byte [] |
| datetime | sygnatura czasowa |
| zakres czasu | czas trwania|
| datetimeoffset |offsetdatetime |

### <a name="list-and-range-shard-maps"></a>Mapowanie list i zakresów fragmentu

Mapy fragmentu można konstruować przy użyciu **list poszczególnych wartości kluczy fragmentowania** lub można je budować przy użyciu **zakresów wartości klucza fragmentowania**.

### <a name="list-shard-maps"></a>Lista map fragmentu

**Fragmentów** zawiera **podfragmentów** , a mapowanie podfragmentów na fragmentów jest obsługiwane przez mapę fragmentu. **Mapa fragmentu listy** jest skojarzeniem między kluczami poszczególnych wartości, które identyfikują podfragmentów i bazy danych, które obsługują jako fragmentów.  **Mapowania list** są jawne, a różne wartości kluczy można zamapować na tę samą bazę danych. Na przykład wartość klucza 1 mapuje do bazy danych A, a wartości kluczy 3 i 6 są mapowane do bazy danych B.

| Klucz | Lokalizacja fragmentu |
| --- | --- |
| 1 |Database_A |
| 3 |Database_B |
| 4 |Database_C |
| 6 |Database_B |
| ... |... |

### <a name="range-shard-maps"></a>Fragmentu Maps zakresu

W zakresie **mapy fragmentu** zakres kluczy jest opisywany przez parę **[niska wartość, wysoka wartość)** , gdzie *niska wartość* jest kluczem minimalnym z zakresu, a *wysoka wartość* jest pierwszą wartością wyższą niż zakres.

Na przykład **[0, 100)** zawiera wszystkie liczby całkowite większe niż lub równe 0 i mniejsze niż 100. Należy zauważyć, że wiele zakresów może wskazywać na tę samą bazę danych, a rozłączone zakresy są obsługiwane (na przykład [100 200) i [400 600) zarówno w przypadku bazy danych C w poniższym przykładzie.)

| Klucz | Lokalizacja fragmentu |
| --- | --- |
| [1, 50) |Database_A |
| [50 100) |Database_B |
| [100 200) |Database_C |
| [400 600) |Database_C |
| ... |... |

Każda z pokazanych tabel jest przykładem koncepcyjnym obiektu **ShardMap** . Każdy wiersz to uproszczony przykład poszczególnych **PointMapping** (dla mapy list fragmentu) lub **RangeMapping** (dla obiektu Range fragmentu map).

## <a name="shard-map-manager"></a>Menedżer map fragmentów

W bibliotece klienta Menedżer mapy fragmentu jest zbiorem map fragmentu. Dane zarządzane przez wystąpienie **ShardMapManager** są przechowywane w trzech miejscach:

1. **Globalna mapa fragmentu (GSM)**: określana jest baza danych służąca jako repozytorium dla wszystkich map fragmentu i mapowań. Tabele specjalne i procedury składowane są tworzone automatycznie w celu zarządzania informacjami. Jest to zazwyczaj mała baza danych i jest w niewielkim zakresie dostępna i nie powinna być używana do innych potrzeb aplikacji. Tabele znajdują się w specjalnym schemacie o nazwie **__ShardManagement**.
2. **Lokalna Mapa fragmentu (LSM)**: każda baza danych określona jako fragmentu jest modyfikowana tak, aby zawierała kilka małych tabel i specjalnych procedur składowanych, które zawierają i zarządzają informacjami o mapie fragmentu specyficznymi dla tego fragmentu. Te informacje są nadmiarowe informacjami w usłudze GSM i umożliwiają aplikacji Weryfikowanie buforowanych informacji mapy fragmentu bez konieczności naciskania obciążenia na GSM; aplikacja używa LSM, aby określić, czy buforowane mapowanie jest nadal ważne. Tabele odpowiadające LSM w każdym fragmentu znajdują się również w schemacie **__ShardManagement**.
3. **Pamięć podręczna aplikacji**: każde wystąpienie aplikacji uzyskujące dostęp do obiektu **ShardMapManager** przechowuje w lokalnej pamięci podręcznej jej mapowań. Przechowuje informacje o routingu, które zostały ostatnio pobrane.

## <a name="constructing-a-shardmapmanager"></a>Konstruowanie elementu ShardMapManager

Obiekt **ShardMapManager** jest konstruowany przy użyciu wzorca fabryki ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory)). Metoda **ShardMapManagerFactory. GetSqlShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory.getsqlshardmapmanager), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager)) przyjmuje poświadczenia (w tym nazwę serwera i nazwę bazy danych, w której znajduje się ciąg **ConnectionString** ), a następnie zwraca wystąpienie elementu **ShardMapManager**.  

**Uwaga:** Wystąpienie **ShardMapManager** powinno być tworzone tylko raz dla każdej domeny aplikacji, w ramach kodu inicjującego aplikacji. Tworzenie dodatkowych wystąpień ShardMapManager w tej samej domenie aplikacji powoduje zwiększenie wykorzystania pamięci i procesora CPU aplikacji. **ShardMapManager** może zawierać dowolną liczbę map fragmentu. Chociaż pojedyncze mapowanie fragmentu może być wystarczające dla wielu aplikacji, istnieją przypadki, w których różne zestawy baz danych są używane dla różnych schematów lub unikatowych celów; w tych przypadkach można preferować wiele map fragmentu.

W tym kodzie aplikacja próbuje otworzyć istniejący **ShardMapManager** z metodą TryGetSqlShardMapManager ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory.trygetsqlshardmapmanager), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager) ). Jeśli obiekty reprezentujące globalne **ShardMapManager** (GSM) jeszcze nie istnieją w bazie danych, Biblioteka klienta tworzy je za pomocą metody CreateSqlShardMapManager ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory.createsqlshardmapmanager), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.createsqlshardmapmanager)).

```Java
// Try to get a reference to the Shard Map Manager in the shardMapManager database.
// If it doesn't already exist, then create it.
ShardMapManager shardMapManager = null;
boolean shardMapManagerExists = ShardMapManagerFactory.tryGetSqlShardMapManager(shardMapManagerConnectionString,ShardMapManagerLoadPolicy.Lazy, refShardMapManager);
shardMapManager = refShardMapManager.argValue;

if (shardMapManagerExists) {
    ConsoleUtils.writeInfo("Shard Map %s already exists", shardMapManager);
}
else {
    // The Shard Map Manager does not exist, so create it
    shardMapManager = ShardMapManagerFactory.createSqlShardMapManager(shardMapManagerConnectionString);
    ConsoleUtils.writeInfo("Created Shard Map %s", shardMapManager);
}
```

```csharp
// Try to get a reference to the Shard Map Manager via the Shard Map Manager database.  
// If it doesn't already exist, then create it.
ShardMapManager shardMapManager;
bool shardMapManagerExists = ShardMapManagerFactory.TryGetSqlShardMapManager(
                                        connectionString,
                                        ShardMapManagerLoadPolicy.Lazy,
                                        out shardMapManager);

if (shardMapManagerExists)
{
    Console.WriteLine("Shard Map Manager already exists");
}
else
{
    // Create the Shard Map Manager.
    ShardMapManagerFactory.CreateSqlShardMapManager(connectionString);
    Console.WriteLine("Created SqlShardMapManager");

    shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager(
            connectionString,
            ShardMapManagerLoadPolicy.Lazy);

// The connectionString contains server name, database name, and admin credentials for privileges on both the GSM and the shards themselves.
}
```

W przypadku wersji .NET można użyć programu PowerShell do utworzenia nowego menedżera mapy fragmentu. Przykład jest dostępny [tutaj](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db).

## <a name="get-a-rangeshardmap-or-listshardmap"></a>Pobierz RangeShardMap lub ListShardMap

Po utworzeniu Menedżera mapy fragmentu można uzyskać RangeShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) lub ListShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.listshardmap), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.listshardmap-1)) za pomocą TryGetRangeShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.trygetrangeshardmap), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetrangeshardmap)), TryGetListShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.trygetlistshardmap), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetlistshardmap)) lub metody GetShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.getshardmap) [, .NET).](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getshardmap)

```Java
// Creates a new Range Shard Map with the specified name, or gets the Range Shard Map if it already exists.
static <T> RangeShardMap<T> createOrGetRangeShardMap(ShardMapManager shardMapManager,
            String shardMapName,
            ShardKeyType keyType) {
    // Try to get a reference to the Shard Map.
    ReferenceObjectHelper<RangeShardMap<T>> refRangeShardMap = new ReferenceObjectHelper<>(null);
    boolean isGetSuccess = shardMapManager.tryGetRangeShardMap(shardMapName, keyType, refRangeShardMap);
    RangeShardMap<T> shardMap = refRangeShardMap.argValue;

    if (isGetSuccess && shardMap != null) {
        ConsoleUtils.writeInfo("Shard Map %1$s already exists", shardMap.getName());
    }
    else {
        // The Shard Map does not exist, so create it
        try {
            shardMap = shardMapManager.createRangeShardMap(shardMapName, keyType);
        }
        catch (Exception e) {
            e.printStackTrace();
        }
        ConsoleUtils.writeInfo("Created Shard Map %1$s", shardMap.getName());
    }

    return shardMap;
}
```

```csharp
// Creates a new Range Shard Map with the specified name, or gets the Range Shard Map if it already exists.
public static RangeShardMap<T> CreateOrGetRangeShardMap<T>(ShardMapManager shardMapManager, string shardMapName)
{
    // Try to get a reference to the Shard Map.
    RangeShardMap<T> shardMap;
    bool shardMapExists = shardMapManager.TryGetRangeShardMap(shardMapName, out shardMap);

    if (shardMapExists)
    {
        ConsoleUtils.WriteInfo("Shard Map {0} already exists", shardMap.Name);
    }
    else
    {
        // The Shard Map does not exist, so create it
        shardMap = shardMapManager.CreateRangeShardMap<T>(shardMapName);
        ConsoleUtils.WriteInfo("Created Shard Map {0}", shardMap.Name);
    }

    return shardMap;
}
```

### <a name="shard-map-administration-credentials"></a>Poświadczenia administracyjne fragmentu map

Aplikacje, które administrują mapami fragmentu i manipulowania nimi, różnią się od tych, które używają map fragmentu do kierowania połączeń.

Aby administrować mapami fragmentu (dodawać lub zmieniać fragmentów, fragmentu Maps, mapowania fragmentu itp.), należy utworzyć wystąpienie **ShardMapManager** przy użyciu **poświadczeń, które mają uprawnienia do odczytu/zapisu zarówno dla bazy danych GSM, jak i dla każdej bazy danych, która służy jako fragmentu**. Poświadczenia muszą zezwalać na operacje zapisu w tabelach w metodzie GSM i LSM jako informacje o mapie fragmentu są wprowadzane lub zmieniane, a także do tworzenia tabel LSM na nowych fragmentów.  

Zobacz [poświadczenia używane do uzyskiwania dostępu do biblioteki klienta Elastic Database](elastic-scale-manage-credentials.md).

### <a name="only-metadata-affected"></a>Dotyczy tylko metadanych

Metody używane do wypełniania lub zmieniania danych **ShardMapManager** nie zmieniają danych użytkownika przechowywanych w samych fragmentów. Na przykład takie metody jak **CreateShard**, **DeleteShard**, **UpdateMapping** itp. wpływają na metadane mapy fragmentu. Nie powodują one usuwania, dodawania ani modyfikowania danych użytkownika zawartych w fragmentów. Zamiast tego te metody są przeznaczone do użycia w połączeniu z oddzielnymi operacjami wykonywanymi w celu tworzenia lub usuwania rzeczywistych baz danych lub przenoszenia wierszy z jednego fragmentu do drugiego w celu zrównoważenia środowiska podzielonej na fragmenty.  (Narzędzie **Split-Merge** dołączone do narzędzi elastycznych baz danych korzysta z tych interfejsów API wraz z organizowaniem rzeczywistego przenoszenia danych między fragmentów). Zobacz [skalowanie za pomocą narzędzia Elastic Database Split-Merge](elastic-scale-overview-split-and-merge.md).

## <a name="data-dependent-routing"></a>Routing zależny od danych

Menedżer mapy fragmentu jest używany w aplikacjach, które wymagają połączeń z bazą danych w celu wykonywania operacji na danych specyficznych dla aplikacji. Te połączenia muszą być skojarzone z poprawną bazą danych. Jest to tzw. **Routing zależny od danych**. Dla tych aplikacji Utwórz wystąpienie obiektu menedżera mapy fragmentu z fabryki przy użyciu poświadczeń, które mają dostęp tylko do odczytu w bazie danych GSM. Poszczególne żądania dotyczące późniejszych połączeń dostarczą poświadczenia niezbędne do nawiązania połączenia z odpowiednią bazą danych fragmentu.

Należy zauważyć, że te aplikacje (przy użyciu **ShardMapManager** otwarte z poświadczeniami tylko do odczytu) nie mogą wprowadzać zmian do map lub mapowań. W razie potrzeby należy utworzyć aplikacje specyficzne dla administratorów lub skrypty programu PowerShell, które dostarczają poświadczenia o wyższym poziomie uprawnień, zgodnie z wcześniejszym opisem. Zobacz [poświadczenia używane do uzyskiwania dostępu do biblioteki klienta Elastic Database](elastic-scale-manage-credentials.md).

Aby uzyskać więcej informacji, zobacz [Routing zależny od danych](elastic-scale-data-dependent-routing.md).

## <a name="modifying-a-shard-map"></a>Modyfikowanie mapy fragmentu

Mapę fragmentu można zmienić na różne sposoby. Wszystkie poniższe metody modyfikują metadane opisujące fragmentów i ich mapowania, ale nie modyfikują fizycznie danych w fragmentów ani nie tworzą ani nie usuwają rzeczywistych baz danych.  Niektóre operacje na mapie fragmentu opisane poniżej mogą być skoordynowane z akcjami administracyjnymi, które fizycznie przenosiją dane lub dodają i usuwającą bazy danych, które służą jako fragmentów.

Te metody współdziałają ze sobą jako bloki konstrukcyjne dostępne do modyfikacji ogólnej dystrybucji danych w środowisku bazy danych podzielonej na fragmenty.  

* Aby dodać lub usunąć fragmentów: Użyj **CreateShard** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.shardmap.createshard), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.createshard)) i **DeleteShard** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.shardmap.deleteshard), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.deleteshard)) klasy shardmap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.shardmap), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap)).
  
    Serwer i baza danych reprezentujące docelowy fragmentu muszą już istnieć, aby można było wykonać te operacje. Te metody nie mają żadnego wpływu na same bazy danych, tylko w metadanych na mapie fragmentu.
* Aby utworzyć lub usunąć punkty lub zakresy, które są mapowane na fragmentów: Użyj **CreateRangeMapping** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.createrangemapping), [.NET](/previous-versions/azure/dn841993(v=azure.100))), **DeleteMapping** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.deletemapping), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) klasy RangeShardMapping ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) i **CreatePointMapping** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.listshardmap.createpointmapping), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.listshardmap-1)) klasy ListShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.listshardmap) [, .NET).](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.listshardmap-1)
  
    Wiele różnych punktów lub zakresów można zamapować na tę samą fragmentu. Te metody mają wpływ tylko na metadane — nie wpływają na żadne dane, które mogą już znajdować się w fragmentów. Jeśli konieczne jest usunięcie danych z bazy danych w celu zapewnienia spójności z operacjami funkcji **DeleteMapping** , należy wykonać te operacje oddzielnie, ale w połączeniu z tymi metodami.  
* Aby podzielić istniejące zakresy na dwa lub scalić sąsiadujące zakresy w jeden: Użyj **SplitMapping** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.splitmapping), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) i **MergeMappings** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.mergemappings), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)).  
  
    Należy pamiętać, że operacje dzielenia i scalania nie **zmieniają fragmentu, do których są mapowane wartości klucza**. Podział dzieli istniejący zakres na dwie części, ale pozostawia oba jako zamapowane do tego samego fragmentu. Scalanie działa w przypadku dwóch sąsiadujących zakresów, które są już zmapowane do tego samego fragmentu, łącząc je w jeden zakres.  Przenoszenie punktów lub zakresów między fragmentów musi być skoordynowane przy użyciu **UpdateMapping** w połączeniu z rzeczywistym przenoszeniem danych.  Aby koordynować zmiany mapy fragmentu z przenoszeniem danych, należy użyć usługi **Split/Merge** , która jest częścią narzędzi elastycznych baz danych.
* Aby zmienić mapowanie (lub przenieść) pojedynczych punktów lub zakresów na różne fragmentów: Użyj **UpdateMapping** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.updatemapping), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)).  
  
    Ze względu na to, że może być konieczne przeniesienie danych z jednego fragmentu do innego, aby była spójna z operacjami **UpdateMapping** , należy wykonać ten ruch oddzielnie, ale w połączeniu z tymi metodami.

* Aby przetworzyć mapowania w trybie online i offline: Użyj **MarkMappingOffline** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.markmappingoffline), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) i **MarkMappingOnline** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.markmappingonline), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) do sterowania stanem online mapowania.
  
    Niektóre operacje dotyczące mapowań fragmentu są dozwolone tylko wtedy, gdy mapowanie jest w stanie "offline", w tym **UpdateMapping** i **DeleteMapping**. Gdy mapowanie jest w trybie offline, żądanie zależne od danych na podstawie klucza zawartego w tym mapowaniu zwraca błąd. Ponadto w przypadku pierwszego przełączenia zakresu do trybu offline wszystkie połączenia z zaatakowanymi fragmentu są automatycznie zamykane w celu uniemożliwienia niespójnych lub niekompletnych wyników dla zapytań skierowanych do zmieniających się zakresów.

Mapowania są niemodyfikowalnymi obiektami w programie .NET.  Wszystkie metody wymienione powyżej, które nie zmieniają mapowań, również unieważnią wszystkie odwołania do nich w kodzie. Aby ułatwić wykonywanie sekwencji operacji, które zmieniają stan mapowania, wszystkie metody, które zmieniają mapowanie, zwracają nowe odwołanie mapowania, więc operacje mogą być łańcucha. Na przykład, aby usunąć istniejące mapowanie w shardmap SM zawierającym klucz 25, można wykonać następujące czynności:

```
    sm.DeleteMapping(sm.MarkMappingOffline(sm.GetMappingForKey(25)));
```

## <a name="adding-a-shard"></a>Dodawanie elementu fragmentu

Aplikacje często muszą dodać nowe fragmentów do obsługi danych, które są oczekiwane z nowych kluczy lub zakresów kluczy, dla mapy fragmentu, która już istnieje. Na przykład aplikacja podzielonej na fragmenty według identyfikatora dzierżawy może potrzebować nowego fragmentu dla nowej dzierżawy, a dane podzielonej na fragmenty miesięcznie mogą potrzebować nowego fragmentu, który został udostępniony przed rozpoczęciem każdego nowego miesiąca.

Jeśli nowy zakres wartości klucza nie jest już częścią istniejącego mapowania i nie jest konieczne przenoszenie danych, można łatwo dodać nowe fragmentu i skojarzyć nowy klucz lub zakres z tym fragmentu. Aby uzyskać szczegółowe informacje na temat dodawania nowych fragmentów, zobacz [Dodawanie nowej fragmentu](elastic-scale-add-a-shard.md).

Jednak w scenariuszach wymagających przenoszenia danych narzędzie do dzielenia i scalania jest wymagane do organizowania przenoszenia danych między fragmentów w połączeniu z wymaganymi aktualizacjami mapy fragmentu. Aby uzyskać szczegółowe informacje na temat korzystania z narzędzia do dzielenia i scalania, zobacz [Omówienie dzielenia i scalania](elastic-scale-overview-split-and-merge.md)

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/elastic-scale-shard-map-management/listmapping.png
[2]: ./media/elastic-scale-shard-map-management/rangemapping.png
[3]: ./media/elastic-scale-shard-map-management/multipleonsingledb.png
