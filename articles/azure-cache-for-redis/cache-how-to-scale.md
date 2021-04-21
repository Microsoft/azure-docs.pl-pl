---
title: Skalowanie Azure Cache for Redis danych
description: Dowiedz się, jak skalować wystąpienia Azure Cache for Redis przy użyciu interfejsu Azure Portal i narzędzi, takich jak Azure PowerShell i interfejs wiersza polecenia platformy Azure
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 43db8d4c094ec1b08a24c29fdaccf97f63ef29b9
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833980"
---
# <a name="scale-an-azure-cache-for-redis-instance"></a>Skalowanie Azure Cache for Redis danych
Azure Cache for Redis oferuje różne oferty pamięci podręcznej, które zapewniają elastyczność wyboru rozmiaru i funkcji pamięci podręcznej. W przypadku pamięci podręcznej w warstwie Podstawowa, Standardowa lub Premium możesz zmienić jej rozmiar i warstwę po jej utworzeniu, aby nadązyć za potrzebami aplikacji. W tym artykule pokazano, jak skalować pamięć podręczną przy użyciu Azure Portal i narzędzi, takich jak Azure PowerShell i interfejs wiersza polecenia platformy Azure.

## <a name="when-to-scale"></a>Kiedy skalować
Za pomocą funkcji [monitorowania usługi](cache-how-to-monitor.md) Azure Cache for Redis monitorować kondycję i wydajność pamięci podręcznej oraz ułatwić określenie, kiedy należy skalować pamięć podręczną. 

Możesz monitorować następujące metryki, aby ułatwić określenie, czy konieczne jest skalowanie.

* Ładowanie serwera Redis
* Użycie pamięci
* Przepustowość sieci
* Użycie procesora

Jeśli ustalisz, że pamięć podręczna nie spełnia już wymagań aplikacji, możesz skalować do większej lub mniejszej warstwy cenowej pamięci podręcznej, która jest właściwa dla aplikacji. Aby uzyskać więcej informacji na temat określania warstwy cenowej pamięci podręcznej do użycia, zobacz [Wybieranie właściwej warstwy](cache-overview.md#choosing-the-right-tier).

## <a name="scale-a-cache"></a>Skalowanie pamięci podręcznej
Aby skalować pamięć podręczną, [przejdź do](cache-configure.md#configure-azure-cache-for-redis-settings) pamięci podręcznej w Azure Portal [i](https://portal.azure.com) kliknij pozycję **Skaluj** w **menu Zasób.**

![Skalowanie](./media/cache-how-to-scale/redis-cache-scale-menu.png)

Wybierz żądaną warstwę cenową w bloku **Wybierz warstwę cenową,** a następnie kliknij **pozycję Wybierz.**

![Warstwa cenowa][redis-cache-pricing-tier-blade]


Można skalować do innej warstwy cenowej z następującymi ograniczeniami:

* Nie można skalować z wyższej warstwy cenowej do niższej warstwy cenowej.
  * Nie można skalować z pamięci podręcznej **Premium** w dół **do** standardowej lub podstawowej pamięci **podręcznej.**
  * Nie można skalować z **standardowej** pamięci podręcznej w dół do podstawowej **pamięci podręcznej.**
* Można skalować z podstawowej **pamięci podręcznej** do **standardowej** pamięci podręcznej, ale nie można zmienić rozmiaru w tym samym czasie. Jeśli potrzebujesz innego rozmiaru, możesz wykonać kolejną operację skalowania do żądanego rozmiaru.
* Nie można skalować bezpośrednio z **podstawowej** pamięci podręcznej do pamięci **podręcznej Premium.** Najpierw przeskaluj **z podstawowej** do standardowej w jednej operacji skalowania, a następnie ze **Standardowa** do **Premium** w kolejnej operacji skalowania. 
* Nie można skalować od większego rozmiaru do **rozmiaru C0 (250 MB).** Można jednak skalować w dół do dowolnego innego rozmiaru w ramach tej samej warstwy cenowej. Na przykład można skalować w dół z C5 Standard do C1 Standard.
 
Gdy pamięć podręczna jest skalowana do  nowej warstwy cenowej, w bloku Azure Cache for Redis jest **wyświetlany stan skalowania.**

![Skalowanie][redis-cache-scaling]

Po zakończeniu skalowania stan zmieni się z **Skalowanie** na **Uruchomione.**

## <a name="how-to-automate-a-scaling-operation"></a>Jak zautomatyzować operację skalowania
Oprócz skalowania wystąpień pamięci podręcznej w usłudze Azure Portal można skalować przy użyciu poleceń cmdlet programu PowerShell, interfejsu wiersza polecenia platformy Azure i bibliotek zarządzania Microsoft Azure (MAML). 

* [Skalowanie przy użyciu programu PowerShell](#scale-using-powershell)
* [Skalowanie przy użyciu interfejsu wiersza polecenia platformy Azure](#scale-using-azure-cli)
* [Skalowanie przy użyciu zarządzania aplikacjami mobilnymi (MAML)](#scale-using-maml)

### <a name="scale-using-powershell"></a>Skalowanie przy użyciu programu PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Możesz skalować wystąpienia Azure Cache for Redis za pomocą programu PowerShell przy użyciu polecenia cmdlet [Set-AzRedisCache,](/powershell/module/az.rediscache/set-azrediscache) gdy właściwości `Size` , lub zostaną `Sku` `ShardCount` zmodyfikowane. W poniższym przykładzie pokazano, jak skalować pamięć podręczną o nazwie `myCache` do pamięci podręcznej o rozmiarze 2,5 GB. 

```powershell
   Set-AzRedisCache -ResourceGroupName myGroup -Name myCache -Size 2.5GB
```

Aby uzyskać więcej informacji na temat skalowania za pomocą programu PowerShell, zobacz [Aby skalować Azure Cache for Redis przy użyciu programu PowerShell.](cache-how-to-manage-redis-cache-powershell.md#scale)

### <a name="scale-using-azure-cli"></a>Skalowanie przy użyciu interfejsu wiersza polecenia platformy Azure
Aby skalować wystąpienia Azure Cache for Redis przy użyciu interfejsu wiersza polecenia platformy Azure, wywołaj polecenie i przekaż żądane zmiany konfiguracji, które obejmują nowy rozmiar, sku lub rozmiar klastra, w zależności od żądanej operacji `azure rediscache set` skalowania.

Aby uzyskać więcej informacji na temat skalowania za pomocą interfejsu wiersza polecenia platformy Azure, zobacz Zmienianie ustawień [istniejącego Azure Cache for Redis.](cache-manage-cli.md#scale)

### <a name="scale-using-maml"></a>Skalowanie przy użyciu zarządzania aplikacjami mobilnymi (MAML)
Aby skalować wystąpienia Azure Cache for Redis przy użyciu bibliotek zarządzania Microsoft Azure [(MAML),](https://azure.microsoft.com/updates/management-libraries-for-net-release-announcement/)wywołaj metodę i przekaż nowy rozmiar `IRedisOperations.CreateOrUpdate` dla `RedisProperties.SKU.Capacity` .

```csharp
    static void Main(string[] args)
    {
        // For instructions on getting the access token, see
        // https://azure.microsoft.com/documentation/articles/cache-configure/#access-keys
        string token = GetAuthorizationHeader();

        TokenCloudCredentials creds = new TokenCloudCredentials(subscriptionId,token);

        RedisManagementClient client = new RedisManagementClient(creds);
        var redisProperties = new RedisProperties();

        // To scale, set a new size for the redisSKUCapacity parameter.
        redisProperties.Sku = new Sku(redisSKUName,redisSKUFamily,redisSKUCapacity);
        redisProperties.RedisVersion = redisVersion;
        var redisParams = new RedisCreateOrUpdateParameters(redisProperties, redisCacheRegion);
        client.Redis.CreateOrUpdate(resourceGroupName,cacheName, redisParams);
    }
```

Aby uzyskać więcej informacji, zobacz przykład [Manage Azure Cache for Redis using MAML (Zarządzanie](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML) aplikacjami mobilnymi przy użyciu maml).

## <a name="scaling-faq"></a>Często zadawane pytania dotyczące skalowania
Na poniższej liście znajdują się odpowiedzi na często zadawane pytania dotyczące Azure Cache for Redis skalowania.

* [Czy można skalować do, z lub w pamięci podręcznej Premium?](#can-i-scale-to-from-or-within-a-premium-cache)
* [Czy po skalowaniu muszę zmienić nazwę pamięci podręcznej lub klucze dostępu?](#after-scaling-do-i-have-to-change-my-cache-name-or-access-keys)
* [Jak działa skalowanie?](#how-does-scaling-work)
* [Czy podczas skalowania utracę dane z pamięci podręcznej?](#will-i-lose-data-from-my-cache-during-scaling)
* [Czy podczas skalowania ma to wpływ na ustawienie niestandardowych baz danych?](#is-my-custom-databases-setting-affected-during-scaling)
* [Czy moja pamięć podręczna będzie dostępna podczas skalowania?](#will-my-cache-be-available-during-scaling)
* Dlaczego po skonfigurowaniu replikacji geograficznej nie mogę skalować pamięci podręcznej ani zmieniać fragmentów w klastrze?
* [Operacje, które nie są obsługiwane](#operations-that-are-not-supported)
* [Jak długo trwa skalowanie?](#how-long-does-scaling-take)
* [Jak sprawdzić, kiedy skalowanie jest zakończone?](#how-can-i-tell-when-scaling-is-complete)

### <a name="can-i-scale-to-from-or-within-a-premium-cache"></a>Czy można skalować do, z lub w pamięci podręcznej Premium?
* Nie można skalować z pamięci **podręcznej Premium** w dół do warstwy **cenowej Podstawowa** **lub Standardowa.**
* Możesz skalować z jednej warstwy **cenowej premium** pamięci podręcznej do innej.
* Nie można skalować z podstawowej pamięci **podręcznej** bezpośrednio do pamięci **podręcznej Premium.** Najpierw przeskaluj **z podstawowej do** **standardowej** w jednej operacji skalowania, a następnie ze **Standardowa** do **Premium** w kolejnej operacji skalowania.
* Jeśli klastrowanie włączono podczas tworzenia pamięci podręcznej **Premium,** możesz [zmienić rozmiar klastra](cache-how-to-premium-clustering.md#cluster-size). Jeśli pamięć podręczna została utworzona bez włączonego klastrowania, można skonfigurować klastrowanie w późniejszym czasie.
  
  Aby uzyskać więcej informacji, [zobacz How to configure clustering for a Premium Azure Cache for Redis (Jak](cache-how-to-premium-clustering.md)skonfigurować klastrowanie dla usługi Premium Azure Cache for Redis).

### <a name="after-scaling-do-i-have-to-change-my-cache-name-or-access-keys"></a>Czy po skalowaniu muszę zmienić nazwę pamięci podręcznej lub klucze dostępu?
Nie, nazwa i klucze pamięci podręcznej nie ulegają zmianie podczas operacji skalowania.

### <a name="how-does-scaling-work"></a>Jak działa skalowanie?
* Gdy **podstawowa pamięć** podręczna jest skalowana do innego rozmiaru, jest zamykana, a nowa pamięć podręczna jest aprowizowana przy użyciu nowego rozmiaru. W tym czasie pamięć podręczna jest niedostępna i wszystkie dane w pamięci podręcznej są utracone.
* Gdy **podstawowa pamięć** podręczna jest skalowana do standardowej pamięci podręcznej, aprowizowana jest replika pamięci podręcznej, **a** dane są kopiowane z podstawowej pamięci podręcznej do pamięci podręcznej repliki. Pamięć podręczna pozostaje dostępna podczas procesu skalowania.
* Gdy  standardowa pamięć podręczna jest skalowana do innego rozmiaru lub pamięci podręcznej **Premium,** jedna z replik jest zamykana i ponownie aprowizowana dla nowego rozmiaru i przesyłanych danych, a następnie druga replika wykonuje tryb failover przed jej ponowną aprowizcją, podobnie jak w przypadku procesu, który występuje podczas awarii jednego z węzłów pamięci podręcznej.

### <a name="will-i-lose-data-from-my-cache-during-scaling"></a>Czy podczas skalowania utracę dane z pamięci podręcznej?
* Gdy **podstawowa pamięć** podręczna jest skalowana do nowego rozmiaru, wszystkie dane są utracone, a pamięć podręczna jest niedostępna podczas operacji skalowania.
* Gdy **podstawowa pamięć** podręczna jest skalowana do **standardowej** pamięci podręcznej, dane w pamięci podręcznej są zwykle zachowywane.
* Gdy pamięć **podręczna w** warstwie Standardowa jest skalowana do większego rozmiaru lub warstwy albo pamięć podręczna w warstwie **Premium** jest skalowana do większego rozmiaru, wszystkie dane są zwykle zachowywane. Podczas skalowania **standardowej** lub **premium** pamięci podręcznej w dół do mniejszego rozmiaru dane mogą zostać utracone w zależności od tego, ile danych znajduje się w pamięci podręcznej powiązanej z nowym rozmiarem podczas skalowania. Jeśli dane zostaną utracone podczas skalowania w dół, klucze zostaną eksmisje przy użyciu [zasad eksmisji allkeys-lru.](https://redis.io/topics/lru-cache) 

### <a name="is-my-custom-databases-setting-affected-during-scaling"></a>Czy ustawienie niestandardowych baz danych ma wpływ na skalowanie?
Jeśli podczas tworzenia pamięci podręcznej skonfigurowano wartość niestandardową dla tego ustawienia, należy pamiętać, że niektóre warstwy cenowe `databases` mają różne [limity baz danych.](cache-configure.md#databases) Oto kilka kwestii, które należy wziąć pod uwagę podczas skalowania w tym scenariuszu:

* W przypadku skalowania do warstwy cenowej z niższym `databases` limitem niż bieżąca warstwa:
  * Jeśli używasz domyślnej liczby , czyli 16 dla wszystkich warstw `databases` cenowych, żadne dane nie zostaną utracone.
  * Jeśli używasz niestandardowej liczby, która mieści się w limitach dla warstwy, do której jest skalowana, to ustawienie zostanie zachowane i żadne dane `databases` `databases` nie zostaną utracone.
  * Jeśli używasz niestandardowej liczby, która przekracza limity nowej warstwy, to ustawienie jest niższe do limitów nowej warstwy, a wszystkie dane w usuniętych bazach danych `databases` `databases` zostaną utracone.
* W przypadku skalowania do warstwy cenowej z tym samym lub wyższym limitem co bieżąca warstwa ustawienie jest zachowywane i `databases` żadne dane nie zostaną `databases` utracone.

Mimo że w przypadku pamięci podręcznych w standardowych i Premium 99,9% czasu dostępności istnieje umowa SLA na poziomie 99,9%, nie ma umowy SLA dotyczącej utraty danych.

### <a name="will-my-cache-be-available-during-scaling"></a>Czy moja pamięć podręczna będzie dostępna podczas skalowania?
* **Pamięci** **podręczne** w standardowych i premium pozostają dostępne podczas operacji skalowania. Jednak wielokropki połączeń mogą wystąpić podczas skalowania pamięci podręcznych w standardowych i premium, a także podczas skalowania z pamięci podręcznych w standardowych do podstawowych. Te wielokropki połączeń powinny być małe, a klienci usługi Redis powinni mieć możliwość natychmiastowego ponownego nawiązania połączenia.
* **Podstawowe** pamięci podręczne są w trybie offline podczas operacji skalowania do innego rozmiaru. Podstawowe pamięci podręczne pozostają dostępne podczas skalowania z **podstawowej** do **standardowej,** ale mogą mieć niewielki wielokropek połączenia. W przypadku wystąpienia wielokropka połączenia klienci usługi Redis powinni mieć możliwość natychmiastowego ponownego nawiązania połączenia.


### <a name="scaling-limitations-with-geo-replication"></a>Ograniczenia skalowania z replikacją geograficzną

Po dodaniu linku replikacji geograficznej między dwiema pamięciami podręcznymi nie będzie już można zainicjować operacji skalowania ani zmienić liczby fragmentów w klastrze. Aby wydać te polecenia, należy odłączyć pamięć podręczną. Aby uzyskać więcej informacji, zobacz [Konfigurowanie replikacji geograficznej.](cache-how-to-geo-replication.md)


### <a name="operations-that-are-not-supported"></a>Operacje, które nie są obsługiwane
* Nie można skalować z wyższej warstwy cenowej do niższej warstwy cenowej.
  * Nie można skalować z pamięci podręcznej **Premium** w dół **do** standardowej lub podstawowej pamięci **podręcznej.**
  * Nie można skalować z **standardowej** pamięci podręcznej w dół do podstawowej **pamięci podręcznej.**
* Można skalować z podstawowej **pamięci podręcznej** do **standardowej** pamięci podręcznej, ale nie można zmienić rozmiaru w tym samym czasie. Jeśli potrzebujesz innego rozmiaru, możesz wykonać kolejną operację skalowania do żądanego rozmiaru.
* Nie można skalować z podstawowej pamięci **podręcznej** bezpośrednio do pamięci **podręcznej Premium.** Najpierw przeskaluj **z podstawowej** do **standardowej** w jednej operacji skalowania, a następnie przeskaluj z **Standardowej** do **Premium** w kolejnej operacji.
* Nie można skalować od większego rozmiaru do rozmiaru **C0 (250 MB).**

Jeśli operacja skalowania nie powiedzie się, usługa spróbuje przywrócić operację, a pamięć podręczna przywróci oryginalny rozmiar.


### <a name="how-long-does-scaling-take"></a>Jak długo trwa skalowanie?
Czas skalowania zależy od ilości danych w pamięci podręcznej, a ukończenie większych ilości danych trwa dłużej. Skalowanie trwa około 20 minut. W przypadku klastrowanych pamięci podręcznych skalowanie trwa około 20 minut na fragment.

### <a name="how-can-i-tell-when-scaling-is-complete"></a>Jak sprawdzić, kiedy skalowanie jest zakończone?
W Azure Portal widać, że operacja skalowania jest w toku. Po zakończeniu skalowania stan pamięci podręcznej zmieni się na **Uruchomiony.**

<!-- IMAGES -->

[redis-cache-pricing-tier-blade]: ./media/cache-how-to-scale/redis-cache-pricing-tier-blade.png

[redis-cache-scaling]: ./media/cache-how-to-scale/redis-cache-scaling.png
