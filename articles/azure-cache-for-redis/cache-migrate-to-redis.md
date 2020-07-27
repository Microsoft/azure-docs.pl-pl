---
title: Migrowanie aplikacji Managed Cache Service do Redis — Azure
description: Dowiedz się, jak migrować aplikacje Managed Cache Service i Pamięć podręczna oparta na roli do usługi Azure cache for Redis
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 07/23/2020
ms.author: yegu
ms.openlocfilehash: 3f5cfccd1f85f68c619192496c62bf80ea8d4785
ms.sourcegitcommit: d7bd8f23ff51244636e31240dc7e689f138c31f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2020
ms.locfileid: "87170175"
---
# <a name="migrate-from-managed-cache-service-to-azure-cache-for-redis-deprecated"></a>Migrowanie z Managed Cache Service do usługi Azure cache for Redis (przestarzałe)
Migrowanie aplikacji korzystających z usługi Azure Managed Cache Service do usługi Azure cache for Redis można wykonać przy minimalnych zmianach w aplikacji, w zależności od funkcji Managed Cache Service używanych przez aplikację pamięci podręcznej. Mimo że interfejsy API nie są dokładnie takie same, są podobne i większość istniejącego kodu, który używa Managed Cache Service dostępu do pamięci podręcznej, może być ponownie używana z minimalnymi zmianami. W tym artykule pokazano, jak wprowadzić niezbędne zmiany konfiguracji i aplikacji w celu migrowania aplikacji Managed Cache Service do korzystania z pamięci podręcznej platformy Azure dla usługi Redis, a także informacje o tym, jak niektóre funkcje usługi Azure cache for Redis mogą być używane do implementowania funkcji pamięci podręcznej Managed Cache Service.

>[!NOTE]
>Managed Cache Service i Pamięć podręczna oparta na roli zostały [wycofane](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/) 30 listopada 2016. Jeśli masz jakiekolwiek Pamięć podręczna oparta na roli wdrożenia, które chcesz migrować do usługi Azure cache for Redis, możesz wykonać kroki opisane w tym artykule.

## <a name="migration-steps"></a>Kroki migracji
Poniższe kroki są wymagane do przeprowadzenia migracji aplikacji Managed Cache Service do korzystania z usługi Azure cache for Redis.

* Mapowanie funkcji Managed Cache Service do usługi Azure cache for Redis
* Wybieranie oferty pamięci podręcznej
* Tworzenie pamięci podręcznej
* Konfigurowanie klientów pamięci podręcznej
  * Usuwanie konfiguracji Managed Cache Service
  * Konfigurowanie klienta pamięci podręcznej przy użyciu pakietu NuGet StackExchange. Redis
* Migrowanie kodu Managed Cache Service
  * Nawiązywanie połączenia z pamięcią podręczną przy użyciu klasy ConnectionMultiplexer
  * Uzyskiwanie dostępu do typów danych pierwotnych w pamięci podręcznej
  * Praca z obiektami platformy .NET w pamięci podręcznej
* Migruj stan sesji ASP.NET i buforowanie danych wyjściowych do usługi Azure cache dla Redis 

## <a name="map-managed-cache-service-features-to-azure-cache-for-redis"></a>Mapowanie funkcji Managed Cache Service do usługi Azure cache for Redis
Usługa Azure Managed Cache Service i usługa Azure cache for Redis są podobne, ale implementują niektóre z ich funkcji na różne sposoby. W tej sekcji opisano niektóre różnice i przedstawiono wskazówki dotyczące wdrażania funkcji Managed Cache Service w usłudze Azure cache for Redis.

| Funkcja Managed Cache Service | Obsługa Managed Cache Service | Pamięć podręczna platformy Azure do obsługi Redis |
| --- | --- | --- |
| Nazwane pamięci podręczne |Domyślna pamięć podręczna jest konfigurowana, a w ofertach pamięci podręcznej standardowa i Premium, w razie potrzeby, można skonfigurować maksymalnie dziewięć dodatkowych, nazwanych pamięci podręcznych. |Usługa Azure cache for Redis ma konfigurowalną liczbę baz danych (domyślnie 16), których można użyć do zaimplementowania podobnej funkcjonalności do nazwanych pamięci podręcznych. Aby uzyskać więcej informacji, zobacz [What are Redis databases?](cache-faq.md#what-are-redis-databases) (Co to są bazy danych Redis?) i [Default Redis server configuration](cache-configure.md#default-redis-server-configuration) (Domyślna konfiguracja serwera Redis). |
| Wysoka dostępność |Zapewnia wysoką dostępność dla elementów w pamięci podręcznej w ofertach pamięci podręcznej w warstwach Standardowa i Premium. Jeśli elementy zostaną utracone z powodu błędu, kopie zapasowe elementów w pamięci podręcznej są nadal dostępne. Operacje zapisu w pamięci podręcznej replik są wykonywane synchronicznie. |Wysoka dostępność jest dostępna w ofertach pamięci podręcznej w warstwach Standardowa i Premium, które mają dwuwęzłową konfigurację podstawowego/repliki (każda fragmentu w pamięci podręcznej Premium ma parę podstawową/repliką). Operacje zapisu w replice są wykonywane asynchronicznie. Aby uzyskać więcej informacji, zobacz [Azure cache for Redis — Cennik](https://azure.microsoft.com/pricing/details/cache/). |
| Powiadomienia |Umożliwia klientom otrzymywanie powiadomień asynchronicznych, gdy wiele operacji pamięci podręcznej jest wykonywanych w nazwanej pamięci podręcznej. |Aplikacje klienckie mogą używać powiadomień Redis/Sub lub [Space](cache-configure.md#keyspace-notifications-advanced-settings) w celu osiągnięcia podobnej funkcjonalności do powiadomień. |
| Lokalna pamięć podręczna |Przechowuje kopię buforowanych obiektów lokalnie na kliencie w celu uzyskania dodatkowego dostępu. |Aplikacje klienckie będą musiały zaimplementować tę funkcję przy użyciu słownika lub podobnej struktury danych. |
| Zasady wykluczania |None lub LRU. Zasady domyślne to LRU. |Usługa Azure cache for Redis obsługuje następujące zasady wykluczania: volatile-LRU, AllKeys-LRU, volatile-Random, AllKeys-Random, volatile-TTL, nowykluczenia. Zasady domyślne to volatile-LRU. Aby uzyskać więcej informacji, zobacz [domyślną konfigurację serwera Redis](cache-configure.md#default-redis-server-configuration). |
| Zasady wygasania |Domyślne zasady wygasania są bezwzględne i domyślny interwał wygaśnięcia wynosi 10 minut. Dostępne są również zasady przesuwania i nienigdy. |Domyślnie elementy w pamięci podręcznej nie wygasną, ale okres ważności można skonfigurować dla każdego zapisu, używając przeciążeń zestawu pamięci podręcznej. |
| Regiony i tagowanie |Regiony są podgrupami elementów w pamięci podręcznej. Regiony obsługują również adnotację elementów w pamięci podręcznej z dodatkowymi opisami ciągów nazywanymi tagami. Regiony obsługują możliwość wykonywania operacji wyszukiwania na wszystkich oznakowanych elementach w tym regionie. Wszystkie elementy w obrębie regionu znajdują się w jednym węźle klastra pamięci podręcznej. |Pamięć podręczna platformy Azure dla usługi Redis składa się z jednego węzła (chyba że klaster Redis jest włączony), więc pojęcie Managed Cache Service regionów nie ma zastosowania. Redis obsługuje wyszukiwanie i wieloznaczne operacje podczas pobierania kluczy, tak aby Tagi opisowe mogły być osadzane w nazwach kluczy i używane do późniejszego pobierania elementów. Aby zapoznać się z przykładem implementacji rozwiązania tagowania za pomocą Redis, zobacz [implementowanie tagowania pamięci podręcznej za pomocą Redis](https://stackify.com/implementing-cache-tagging-redis/). |
| Serializacja |Zarządzana pamięć podręczna obsługuje NetDataContractSerializer, BinaryFormatter i używa serializatorów niestandardowych. Wartość domyślna to NetDataContractSerializer. |Przed umieszczeniem ich w pamięci podręcznej przez aplikację kliencką można serializować obiekty .NET, a następnie wybrać serializator do deweloperów aplikacji klienta. Aby uzyskać więcej informacji i przykładowy kod, zobacz [Work with .NET objectss w pamięci podręcznej](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache). |
| Emulator pamięci podręcznej |Zarządzana pamięć podręczna zapewnia emulator lokalnej pamięci podręcznej. |Pamięć podręczna systemu Azure dla Redis nie ma emulatora, ale można [uruchomić kompilację MSOpenTech redis-server.exe lokalnie](cache-faq.md#cache-emulator) w celu zapewnienia środowiska emulatora. |

## <a name="choose-a-cache-offering"></a>Wybieranie oferty pamięci podręcznej
Pamięć podręczna Microsoft Azure dla Redis jest dostępna w następujących warstwach:

* **Podstawowa** — jeden węzeł. Wiele rozmiarów do 53 GB.
* **Standardowa** — dwa węzły (węzeł podstawowy i węzeł repliki). Wiele rozmiarów do 53 GB. Umowa SLA na poziomie 99,9%.
* **Premium** — dwa węzły (węzeł podstawowy i węzeł repliki) zawierające do 10 fragmentów. Wiele rozmiarów z 6 GB do 1,2 TB. Wszystkie funkcje warstwy Standardowej i dodatkowe funkcje, m.in. obsługa [klastra Redis](cache-how-to-premium-clustering.md), [stanu trwałego pamięci podręcznej Redis](cache-how-to-premium-persistence.md) oraz usługi [Azure Virtual Network](cache-how-to-premium-vnet.md). Umowa SLA na poziomie 99,9%.

Poszczególne warstwy różnią się od siebie pod względem funkcji i cen. Funkcje te zostały omówione w dalszej części tego przewodnika i aby uzyskać więcej informacji na temat cen, zobacz [szczegóły cennika usługi cache](https://azure.microsoft.com/pricing/details/cache/).

Punktem początkowym migracji jest wybranie rozmiaru, który jest zgodny z rozmiarem poprzedniej Managed Cache Service pamięci podręcznej, a następnie skalowanie w górę lub w dół w zależności od wymagań aplikacji. Aby uzyskać więcej informacji na temat wybierania odpowiedniej pamięci podręcznej platformy Azure dla oferty Redis, zobacz, [w jaki sposób usługa Azure cache dla oferty Redis i rozmiar należy używać](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use).

## <a name="create-a-cache"></a>Tworzenie pamięci podręcznej
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="configure-the-cache-clients"></a>Konfigurowanie klientów pamięci podręcznej
Po utworzeniu i skonfigurowaniu pamięci podręcznej następnym krokiem jest usunięcie konfiguracji Managed Cache Service i dodanie pamięci podręcznej platformy Azure na potrzeby konfiguracji i odwołań Redis, tak aby klienci pamięci podręcznej mogli uzyskiwać dostęp do pamięci podręcznej.

* Usuwanie konfiguracji Managed Cache Service
* Konfigurowanie klienta pamięci podręcznej przy użyciu pakietu NuGet StackExchange. Redis

### <a name="remove-the-managed-cache-service-configuration"></a>Usuwanie konfiguracji Managed Cache Service
Aby można było skonfigurować aplikacje klienckie dla usługi Azure cache for Redis, należy usunąć istniejącą konfigurację Managed Cache Service i odwołania do zestawów, odinstalując Managed Cache Service pakiet NuGet.

Aby odinstalować pakiet NuGet Managed Cache Service, kliknij prawym przyciskiem myszy projekt klienta w **Eksplorator rozwiązań** i wybierz polecenie **Zarządzaj pakietami NuGet**. Wybierz węzeł **zainstalowane pakiety** i wpisz w**indowsAzure. buforowanie** w polu Wyszukaj zainstalowane pakiety. Wybierz pozycję **pamięć podręczna** **systemu Windows** Azure (lub buforowanie **systemu Windows** **Azure** w zależności od wersji pakietu NuGet), kliknij przycisk **Odinstaluj**, a następnie kliknij przycisk **Zamknij**.

![Odinstaluj pakiet NuGet Managed Cache Service platformy Azure](./media/cache-migrate-to-redis/IC757666.jpg)

Odinstalowywanie pakietu NuGet Managed Cache Service powoduje usunięcie zestawów Managed Cache Service i Managed Cache Service wpisów w app.config lub web.config aplikacji klienckiej. Ponieważ niektóre dostosowane ustawienia nie mogą zostać usunięte podczas odinstalowywania pakietu NuGet, Otwórz web.config lub app.config i upewnij się, że zostały usunięte następujące elementy.

Upewnij się, że `dataCacheClients` wpis został usunięty z `configSections` elementu. Nie usuwaj całego `configSections` elementu; po prostu usuń `dataCacheClients` wpis, jeśli jest obecny.

```xml
<configSections>
  <!-- Existing sections omitted for clarity. -->
  <section name="dataCacheClients"type="Microsoft.ApplicationServer.Caching.DataCacheClientsSection, Microsoft.ApplicationServer.Caching.Core" allowLocation="true" allowDefinition="Everywhere"/>
</configSections>
```

Upewnij się, że `dataCacheClients` sekcja została usunięta. `dataCacheClients`Sekcja będzie wyglądać podobnie do poniższego przykładu.

```xml
<dataCacheClients>
  <dataCacheClientname="default">
    <!--To use the in-role flavor of Azure Cache, set identifier to be the cache cluster role name -->
    <!--To use the Azure Managed Cache Service, set identifier to be the endpoint of the cache cluster -->
    <autoDiscoverisEnabled="true"identifier="[Cache role name or Service Endpoint]"/>

    <!--<localCache isEnabled="true" sync="TimeoutBased" objectCount="100000" ttlValue="300" />-->
    <!--Use this section to specify security settings for connecting to your cache. This section is not required if your cache is hosted on a role that is a part of your cloud service. -->
    <!--<securityProperties mode="Message" sslEnabled="true">
      <messageSecurity authorizationInfo="[Authentication Key]" />
    </securityProperties>-->
  </dataCacheClient>
</dataCacheClients>
```

Po usunięciu konfiguracji Managed Cache Service można skonfigurować klienta pamięci podręcznej zgodnie z opisem w poniższej sekcji.

### <a name="configure-a-cache-client-using-the-stackexchangeredis-nuget-package"></a>Konfigurowanie klienta pamięci podręcznej przy użyciu pakietu NuGet StackExchange. Redis
[!INCLUDE [redis-cache-configure](../../includes/redis-cache-configure-stackexchange-redis-nuget.md)]

## <a name="migrate-managed-cache-service-code"></a>Migrowanie kodu Managed Cache Service
Interfejs API StackExchange. Azure cache for Redis Client jest podobny do Managed Cache Service. Ta sekcja zawiera omówienie różnic.

### <a name="connect-to-the-cache-using-the-connectionmultiplexer-class"></a>Nawiązywanie połączenia z pamięcią podręczną przy użyciu klasy ConnectionMultiplexer
W Managed Cache Service połączenia z pamięcią podręczną były obsługiwane przez `DataCacheFactory` `DataCache` klasy i. W usłudze Azure cache for Redis te połączenia są zarządzane przez `ConnectionMultiplexer` klasę.

Dodaj następującą instrukcję using na początku każdego pliku, z którego chcesz uzyskać dostęp do pamięci podręcznej.

```csharp
using StackExchange.Redis
```

Jeśli ta przestrzeń nazw nie zostanie rozpoznana, upewnij się, że został dodany pakiet NuGet StackExchange. Redis, zgodnie z opisem w [przewodniku szybki start: Użyj usługi Azure cache for Redis z aplikacją platformy .NET](cache-dotnet-how-to-use-azure-redis-cache.md).

> [!NOTE]
> Należy zauważyć, że klient StackExchange. Redis wymaga .NET Framework 4 lub nowszego.
> 
> 

Aby nawiązać połączenie z usługą Azure cache for Redis, wywołaj `ConnectionMultiplexer.Connect` metodę statyczną i przekaż punkt końcowy i klucz. Jednym z rozwiązań w zakresie udostępniania wystąpienia klasy `ConnectionMultiplexer` w aplikacji jest korzystanie z właściwości statycznej, która zwraca połączone wystąpienie podobnie jak w poniższym przykładzie. To podejście zapewnia bezpieczny wątkowo sposób inicjowania pojedynczego połączonego `ConnectionMultiplexer` wystąpienia. W tym przykładzie `abortConnect` jest ustawiona na wartość false, co oznacza, że wywołanie powiedzie się, nawet jeśli połączenie z pamięcią podręczną nie zostanie ustanowione. Kluczowa funkcja klasy `ConnectionMultiplexer` polega na automatycznym przywracaniu łączności z pamięcią podręczną po rozwiązaniu problemu z siecią lub usunięciu innych przyczyn.

```csharp
private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
{
    return ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");
});

public static ConnectionMultiplexer Connection
{
    get
    {
        return lazyConnection.Value;
    }
}
```

Punkt końcowy pamięci podręcznej, klucze i porty można uzyskać z bloku **Azure cache for Redis** dla wystąpienia pamięci podręcznej. Aby uzyskać więcej informacji, zobacz [pamięć podręczna Azure dla właściwości Redis](cache-configure.md#properties).

Po nawiązaniu połączenia Zwróć odwołanie do bazy danych usługi Azure cache for Redis przez wywołanie `ConnectionMultiplexer.GetDatabase` metody. Obiekt zwracany z metody `GetDatabase` jest lekkim obiektem przekazującym i nie wymaga przechowywania.

```csharp
IDatabase cache = Connection.GetDatabase();

// Perform cache operations using the cache object...
// Simple put of integral data types into the cache
cache.StringSet("key1", "value");
cache.StringSet("key2", 25);

// Simple get of data types from the cache
string key1 = cache.StringGet("key1");
int key2 = (int)cache.StringGet("key2");
```

Klient StackExchange. Redis używa `RedisKey` `RedisValue` typów i do uzyskiwania dostępu do elementów w pamięci podręcznej i ich przechowywania. Te typy są mapowane na większość typów języka pierwotnego, w tym ciąg i często nie są używane bezpośrednio. Ciągi Redis są najbardziej podstawowym rodzajem wartości Redis i mogą zawierać wiele typów danych, w tym serializowane strumienie binarne, a chociaż nie można używać typu bezpośrednio, należy użyć metod, które zawierają `String` nazwę. W przypadku większości typów danych pierwotnych można przechowywać i pobierać elementy z pamięci podręcznej przy użyciu `StringSet` `StringGet` metod i, chyba że przechowujesz kolekcje lub inne typy danych Redis w pamięci podręcznej. 

`StringSet`i `StringGet` są podobne do Managed Cache Service `Put` i `Get` metod, z jedną istotną różnicą przed rozpoczęciem ustawiania i pobierania obiektu .NET do pamięci podręcznej, należy najpierw serializować ją. 

W przypadku wywołania `StringGet` , jeśli obiekt istnieje, jest zwracany, a jeśli nie, zwracana jest wartość null. W takim przypadku można pobrać wartość z żądanego źródła danych i zapisać ją w pamięci podręcznej do późniejszego użycia. Ten wzorzec jest znany jako wzorzec z odkładaniem do pamięci podręcznej.

Aby określić wygaśnięcie elementu w pamięci podręcznej, użyj parametru `TimeSpan` metody `StringSet`.

```csharp
cache.StringSet("key1", "value1", TimeSpan.FromMinutes(90));
```

Pamięć podręczna systemu Azure dla usługi Redis może współdziałać z obiektami .NET, a także typami danych pierwotnych, ale zanim obiekt programu .NET może być buforowany, musi być serializowany. Ta Serializacja jest odpowiedzialna za programistę aplikacji i zapewnia elastyczność dewelopera w wyborze serializatora. Aby uzyskać więcej informacji i przykładowy kod, zobacz [Work with .NET objectss w pamięci podręcznej](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache).

## <a name="migrate-aspnet-session-state-and-output-caching-to-azure-cache-for-redis"></a>Migruj stan sesji ASP.NET i buforowanie danych wyjściowych do usługi Azure cache dla Redis
Pamięć podręczna systemu Azure dla usługi Redis ma dostawców zarówno na potrzeby buforowania stanu sesji ASP.NET, jak i danych wyjściowych strony. Aby przeprowadzić migrację aplikacji korzystającej z Managed Cache Service wersji tych dostawców, najpierw usuń istniejące sekcje z web.config, a następnie skonfiguruj pamięć podręczną platformy Azure dla wersji Redis dostawców. Aby uzyskać instrukcje dotyczące korzystania z usługi Azure cache for Redis ASP.NET Providers, zobacz dostawca [stanu sesji ASP.NET dla usługi Azure cache for Redis](cache-aspnet-session-state-provider.md) i [ASP.NET wyjściowych dostawców pamięci podręcznej dla usługi Azure cache for Redis](cache-aspnet-output-cache-provider.md).

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z [pamięcią podręczną platformy Azure, aby uzyskać dokumentację Redis](https://azure.microsoft.com/documentation/services/cache/) dla samouczków, przykładów, filmów wideo i nie tylko.

