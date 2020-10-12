---
title: Jak skalować usługę Azure Cache for Redis
description: Dowiedz się, jak skalować usługę Azure cache for Redis Instances przy użyciu Azure Portal i narzędzi, takich jak Azure PowerShell, i interfejsu wiersza polecenia platformy Azure.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 04/11/2017
ms.openlocfilehash: e780ef0b82240ac6771059f8bd239b90395135d9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88213344"
---
# <a name="how-to-scale-azure-cache-for-redis"></a>Jak skalować usługę Azure Cache for Redis
Usługa Azure cache for Redis ma różne oferty pamięci podręcznej, które zapewniają elastyczność w wyborze rozmiaru i funkcji pamięci podręcznej. Po utworzeniu pamięci podręcznej można skalować rozmiar i warstwę cenową pamięci podręcznej w przypadku zmiany wymagań aplikacji. W tym artykule przedstawiono sposób skalowania pamięci podręcznej przy użyciu Azure Portal i narzędzi, takich jak Azure PowerShell i interfejs wiersza polecenia platformy Azure.

## <a name="when-to-scale"></a>Kiedy skalować
Można użyć funkcji [monitorowania](cache-how-to-monitor.md) usługi Azure cache for Redis do monitorowania kondycji i wydajności pamięci podręcznej oraz określania czasu skalowania pamięci podręcznej. 

Można monitorować następujące metryki, aby pomóc w ustaleniu, czy konieczne jest skalowanie.

* Ładowanie serwera Redis
* Użycie pamięci
* Przepustowość sieci
* Użycie procesora

Jeśli okaże się, że pamięć podręczna nie spełnia już wymagań aplikacji, można skalować ją do większej lub mniejszej warstwy cenowej pamięci podręcznej, która jest odpowiednia dla danej aplikacji. Aby uzyskać więcej informacji na temat określania warstwy cenowej pamięci podręcznej, która ma być używana, zobacz [Wybieranie odpowiedniej warstwy](cache-overview.md#choosing-the-right-tier).

## <a name="scale-a-cache"></a>Skalowanie pamięci podręcznej
Aby skalować pamięć podręczną, [Przejdź do pamięci podręcznej](cache-configure.md#configure-azure-cache-for-redis-settings) w [Azure Portal](https://portal.azure.com) a następnie kliknij pozycję **Skaluj** w **menu zasób**.

![Skalowanie](./media/cache-how-to-scale/redis-cache-scale-menu.png)

Wybierz żądaną warstwę cenową w bloku **Wybierz warstwę cenową** , a następnie kliknij przycisk **Wybierz**.

![Warstwa cenowa][redis-cache-pricing-tier-blade]


Możesz skalować do innej warstwy cenowej z następującymi ograniczeniami:

* Nie można skalować z wyższej warstwy cenowej do niższej warstwy cenowej.
  * Nie można skalować z pamięci podręcznej **Premium** do warstwy **standardowa** lub **podstawowa** .
  * Nie można skalować z poziomu **standardowej** pamięci podręcznej do **podstawowej** pamięci podręcznej.
* Możesz skalować od **podstawowej** pamięci podręcznej do **standardowej** pamięci podręcznej, ale nie możesz zmienić rozmiaru w tym samym czasie. Jeśli potrzebujesz innego rozmiaru, możesz wykonać kolejną operację skalowania do żądanego rozmiaru.
* Nie można skalować z **podstawowej** pamięci podręcznej bezpośrednio do pamięci podręcznej **Premium** . Najpierw Skaluj od warstwy **podstawowa** do **standardowa** w ramach jednej operacji skalowania, a następnie z warstwy **standardowa** do **Premium** w kolejnej operacji skalowania.
* Nie można skalować od większego rozmiaru w dół do rozmiaru **C0 (250 MB)** .
 
Podczas skalowania pamięci podręcznej do nowej warstwy cenowej w bloku **usługi Azure cache for Redis** jest wyświetlany stan **skalowania** .

![Skalowanie][redis-cache-scaling]

Po zakończeniu skalowania stan zmieni się ze **skalowanie** na **uruchomione**.

## <a name="how-to-automate-a-scaling-operation"></a>Jak zautomatyzować operację skalowania
Oprócz skalowania wystąpień pamięci podręcznej w Azure Portal można skalować za pomocą poleceń cmdlet programu PowerShell, interfejsu wiersza polecenia platformy Azure i za pomocą bibliotek zarządzania Microsoft Azure (MAML). 

* [Skalowanie przy użyciu programu PowerShell](#scale-using-powershell)
* [Skalowanie przy użyciu interfejsu wiersza polecenia platformy Azure](#scale-using-azure-cli)
* [Skalowanie za pomocą MAML](#scale-using-maml)

### <a name="scale-using-powershell"></a>Skalowanie przy użyciu programu PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Możesz skalować pamięć podręczną platformy Azure dla wystąpień Redis za pomocą programu PowerShell, używając polecenia cmdlet [Set-AzRedisCache](https://docs.microsoft.com/powershell/module/az.rediscache/set-azrediscache) `Size` , gdy `Sku` właściwości, lub `ShardCount` są modyfikowane. Poniższy przykład przedstawia sposób skalowania pamięci podręcznej o nazwie `myCache` do pamięci podręcznej 2,5 GB. 

```powershell
   Set-AzRedisCache -ResourceGroupName myGroup -Name myCache -Size 2.5GB
```

Aby uzyskać więcej informacji na temat skalowania za pomocą programu PowerShell, zobacz [skalowanie pamięci podręcznej platformy Azure dla Redis przy użyciu programu PowerShell](cache-how-to-manage-redis-cache-powershell.md#scale).

### <a name="scale-using-azure-cli"></a>Skalowanie przy użyciu interfejsu wiersza polecenia platformy Azure
Aby skalować usługę Azure cache for Redis Instances przy użyciu interfejsu wiersza polecenia platformy Azure, wywołaj `azure rediscache set` polecenie i przekaż wymagane zmiany konfiguracji, które obejmują nowy rozmiar, jednostkę SKU lub rozmiar klastra, w zależności od wymaganej operacji skalowania.

Aby uzyskać więcej informacji na temat skalowania przy użyciu interfejsu wiersza polecenia platformy Azure, zobacz [Zmiana ustawień istniejącej pamięci podręcznej platformy Azure dla usługi Redis](cache-manage-cli.md#scale).

### <a name="scale-using-maml"></a>Skalowanie za pomocą MAML
Aby skalować pamięć podręczną platformy Azure dla wystąpień Redis przy użyciu [bibliotek zarządzania Microsoft Azure (MAML)](https://azure.microsoft.com/updates/management-libraries-for-net-release-announcement/), wywołaj `IRedisOperations.CreateOrUpdate` metodę i przekaż nowy rozmiar dla `RedisProperties.SKU.Capacity` .

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

Aby uzyskać więcej informacji, zobacz przykład [zarządzania usługą Azure cache for Redis przy użyciu MAML](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML) .

## <a name="scaling-faq"></a>Skalowanie często zadawane pytania
Poniższa lista zawiera odpowiedzi na często zadawane pytania dotyczące skalowania usługi Azure cache for Redis.

* [Czy mogę skalować do, z lub w pamięci podręcznej Premium?](#can-i-scale-to-from-or-within-a-premium-cache)
* [Czy po skalowaniu należy zmienić nazwę pamięci podręcznej lub klucze dostępu?](#after-scaling-do-i-have-to-change-my-cache-name-or-access-keys)
* [Jak działa skalowanie?](#how-does-scaling-work)
* [Czy podczas skalowania utracisz dane z mojej pamięci podręcznej?](#will-i-lose-data-from-my-cache-during-scaling)
* [Czy ustawienie niestandardowych baz danych jest dotknięte problemem podczas skalowania?](#is-my-custom-databases-setting-affected-during-scaling)
* [Czy moja pamięć podręczna będzie dostępna podczas skalowania?](#will-my-cache-be-available-during-scaling)
* Po skonfigurowaniu replikacji geograficznej dlaczego nie mogę skalować pamięci podręcznej ani zmieniać fragmentów w klastrze?
* [Operacje, które nie są obsługiwane](#operations-that-are-not-supported)
* [Jak długo trwa skalowanie?](#how-long-does-scaling-take)
* [Jak mogę sprawdzić, czy skalowanie zostało zakończone?](#how-can-i-tell-when-scaling-is-complete)

### <a name="can-i-scale-to-from-or-within-a-premium-cache"></a>Czy mogę skalować do, z lub w pamięci podręcznej Premium?
* Nie można skalować z poziomu pamięci podręcznej **Premium** do warstwy cenowej **podstawowa** lub **standardowa** .
* Możesz skalować z jednej warstwy cenowej **Premium** cache do innej.
* Nie można skalować z **podstawowej** pamięci podręcznej bezpośrednio do pamięci podręcznej **Premium** . Najpierw Skaluj od warstwy **podstawowa** do **standardowa** w ramach jednej operacji skalowania, a następnie z warstwy **standardowa** do **Premium** w kolejnej operacji skalowania.
* Jeśli włączono klaster podczas tworzenia pamięci podręcznej **Premium** , można [zmienić rozmiar klastra](cache-how-to-premium-clustering.md#cluster-size). Jeśli pamięć podręczna została utworzona bez włączonej obsługi klastrowania, można skonfigurować klaster w późniejszym czasie.
  
  Aby uzyskać więcej informacji, zobacz [jak skonfigurować klastrowanie dla pamięci podręcznej Premium platformy Azure dla Redis](cache-how-to-premium-clustering.md).

### <a name="after-scaling-do-i-have-to-change-my-cache-name-or-access-keys"></a>Czy po skalowaniu należy zmienić nazwę pamięci podręcznej lub klucze dostępu?
Nie, nazwa i klucze pamięci podręcznej nie są zmieniane podczas operacji skalowania.

### <a name="how-does-scaling-work"></a>Jak działa skalowanie?
* Gdy **podstawowa** pamięć podręczna jest skalowana do innego rozmiaru, jest ona zamykana i zostanie zainicjowana Nowa pamięć podręczna przy użyciu nowego rozmiaru. W tym czasie pamięć podręczna jest niedostępna, a wszystkie dane w pamięci podręcznej zostaną utracone.
* Gdy **podstawowa** pamięć podręczna jest skalowana do **standardowej** pamięci podręcznej, zostanie zainicjowana pamięć podręczna repliki, a dane są kopiowane z podstawowej pamięci podręcznej do pamięci podręcznej repliki. Pamięć podręczna pozostaje dostępna podczas procesu skalowania.
* Gdy **standardowa** pamięć podręczna jest skalowana do innego rozmiaru lub pamięci podręcznej **Premium** , jedna z replik zostanie zamknięta i ponownie zainicjowana do nowego rozmiaru i danych przetransferowanych, a następnie druga replika wykonuje tryb failover przed ponownym zainicjowaniem obsługi administracyjnej, podobnie jak w przypadku awarii jednego z węzłów pamięci podręcznej.

### <a name="will-i-lose-data-from-my-cache-during-scaling"></a>Czy podczas skalowania utracisz dane z mojej pamięci podręcznej?
* Gdy **podstawowa** pamięć podręczna jest skalowana do nowego rozmiaru, wszystkie dane są tracone, a pamięć podręczna jest niedostępna podczas operacji skalowania.
* Gdy **podstawowa** pamięć podręczna jest skalowana do **standardowej** pamięci podręcznej, dane w pamięci podręcznej są zwykle zachowywane.
* Gdy **standardowa** pamięć podręczna jest skalowana do większego rozmiaru lub warstwy, a pamięć podręczna w **warstwie Premium** jest skalowana do większego rozmiaru, wszystkie dane są zwykle zachowywane. W przypadku skalowania pamięci podręcznej **standardowej** lub **Premium** w dół do mniejszego rozmiaru dane mogą zostać utracone w zależności od ilości danych znajdujących się w pamięci podręcznej związanej z nowym rozmiarem podczas skalowania. Jeśli dane zostaną utracone podczas skalowania w dół, klucze są wykluczane przy użyciu zasad wykluczania [AllKeys-LRU](https://redis.io/topics/lru-cache) . 

### <a name="is-my-custom-databases-setting-affected-during-scaling"></a>Czy ustawienie niestandardowych baz danych jest dotknięte problemem podczas skalowania?
Jeśli skonfigurowano wartość niestandardową dla `databases` Ustawienia podczas tworzenia pamięci podręcznej, należy pamiętać, że niektóre warstwy cenowe mają różne [limity baz danych](cache-configure.md#databases). Poniżej przedstawiono niektóre zagadnienia dotyczące skalowania w tym scenariuszu:

* Podczas skalowania do warstwy cenowej z niższym `databases` limitem niż bieżąca warstwa:
  * Jeśli używasz domyślnej liczby `databases` , która jest 16 dla wszystkich warstw cenowych, żadne dane nie zostaną utracone.
  * Jeśli używasz niestandardowej liczby `databases` , która znajduje się w granicach warstwy, do której jest skalowane, to `databases` ustawienie jest zachowywane i żadne dane nie zostaną utracone.
  * Jeśli używasz niestandardowej liczby, `databases` która przekracza limity nowej warstwy, `databases` ustawienie jest obniżane do limitów nowej warstwy, a wszystkie dane z usuniętych baz danych zostaną utracone.
* W przypadku skalowania do warstwy cenowej o tym samym lub wyższym `databases` limicie niż bieżąca warstwa `databases` ustawienie jest zachowywane i żadne dane nie zostaną utracone.

W przypadku, gdy w przypadku wersji Standard i Premium są dostępne 99,9% umowy SLA, nie ma umowy SLA na utratę danych.

### <a name="will-my-cache-be-available-during-scaling"></a>Czy moja pamięć podręczna będzie dostępna podczas skalowania?
* Pamięć podręczna w warstwach **standardowa** i **Premium** pozostaje dostępna podczas operacji skalowania. Jednak podczas skalowania pamięci podręcznej w warstwach Standardowa i Premium może wystąpić Blips połączeń, a także podczas skalowania z warstwy Podstawowa do standardowa. Te Blips połączeń powinny być małe, a klienci Redis powinni mieć możliwość natychmiastowego nawiązania połączenia.
* **Podstawowe** pamięci podręczne są w trybie offline w trakcie operacji skalowania do innego rozmiaru. Podstawowe pamięci podręczne pozostają dostępne podczas skalowania z warstwy **podstawowa** do **standardowa** , ale mogą wystąpić małe Blip połączeń. W przypadku wystąpienia Blip połączenia klienci Redis powinni mieć możliwość natychmiastowego nawiązania połączenia.


### <a name="scaling-limitations-with-geo-replication"></a>Ograniczenia skalowania z replikacją geograficzną

Po dodaniu linku replikacji geograficznej między dwiema pamięciami podręcznymi nie będzie można inicjować operacji skalowania ani zmieniać liczby fragmentów w klastrze. Aby wydać te polecenia, należy odłączyć pamięć podręczną. Aby uzyskać więcej informacji, zobacz [Konfigurowanie replikacji geograficznej](cache-how-to-geo-replication.md).


### <a name="operations-that-are-not-supported"></a>Operacje, które nie są obsługiwane
* Nie można skalować z wyższej warstwy cenowej do niższej warstwy cenowej.
  * Nie można skalować z pamięci podręcznej **Premium** do warstwy **standardowa** lub **podstawowa** .
  * Nie można skalować z poziomu **standardowej** pamięci podręcznej do **podstawowej** pamięci podręcznej.
* Możesz skalować od **podstawowej** pamięci podręcznej do **standardowej** pamięci podręcznej, ale nie możesz zmienić rozmiaru w tym samym czasie. Jeśli potrzebujesz innego rozmiaru, możesz wykonać kolejną operację skalowania do żądanego rozmiaru.
* Nie można skalować z **podstawowej** pamięci podręcznej bezpośrednio do pamięci podręcznej **Premium** . Najpierw Skaluj od warstwy **podstawowa** do **standardowa** w ramach jednej operacji skalowania, a następnie Skaluj ją od warstwy **standardowa** do warstwy **Premium** w kolejnej operacji.
* Nie można skalować od większego rozmiaru w dół do rozmiaru **C0 (250 MB)** .

Jeśli operacja skalowania nie powiedzie się, usługa podejmie próbę przywrócenia operacji, a pamięć podręczna zostanie przywrócona do oryginalnego rozmiaru.


### <a name="how-long-does-scaling-take"></a>Jak długo trwa skalowanie?
Skalowanie czasu zależy od ilości danych znajdujących się w pamięci podręcznej, dzięki czemu większa ilość danych trwa dłużej. Skalowanie trwa około 20 minut. W przypadku klastrowanych pamięci podręcznych skalowanie trwa około 20 minut na fragmentu.

### <a name="how-can-i-tell-when-scaling-is-complete"></a>Jak mogę sprawdzić, czy skalowanie zostało zakończone?
W Azure Portal można zobaczyć operację skalowania w toku. Po zakończeniu skalowania stan pamięci podręcznej zmieni się na **uruchomiony**.

<!-- IMAGES -->

[redis-cache-pricing-tier-blade]: ./media/cache-how-to-scale/redis-cache-pricing-tier-blade.png

[redis-cache-scaling]: ./media/cache-how-to-scale/redis-cache-scaling.png
