---
title: Zarządzanie Azure Cache for Redis za pomocą Azure PowerShell
description: Dowiedz się, jak wykonywać zadania administracyjne dla Azure Cache for Redis przy użyciu Azure PowerShell.
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 07/13/2017
ms.author: yegu
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: ac1456e2dc640e1076857da78cf4145b61ea69d4
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107832810"
---
# <a name="manage-azure-cache-for-redis-with-azure-powershell"></a>Zarządzanie Azure Cache for Redis za pomocą Azure PowerShell
> [!div class="op_single_selector"]
> * [Program PowerShell](cache-how-to-manage-redis-cache-powershell.md)
> * [Interfejs wiersza polecenia platformy Azure](cache-manage-cli.md)
> 
> 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

W tym temacie przedstawiono sposób wykonywania typowych zadań, takich jak tworzenie, aktualizowanie i skalowanie wystąpień usługi Azure Cache for Redis, ponowne generowanie kluczy dostępu i wyświetlanie informacji o pamięciach podręcznych. Aby uzyskać pełną listę poleceń cmdlet Azure Cache for Redis PowerShell, zobacz [Azure Cache for Redis cmdlet](/powershell/module/az.rediscache).

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

Aby uzyskać więcej informacji na temat klasycznego modelu wdrażania, zobacz [Azure Resource Manager vs. classic deployment: Understand deployment models and](../azure-resource-manager/management/deployment-models.md)the state of your resources (Wdrażanie w porównaniu z klasycznym wdrożeniem): Understand deployment models and the state of your resources (Informacje o modelach wdrażania i stanie zasobów).

## <a name="prerequisites"></a>Wymagania wstępne
Jeśli zainstalowano już program Azure PowerShell, musisz mieć zainstalowany Azure PowerShell w wersji 1.0.0 lub nowszej. Możesz sprawdzić wersję programu Azure PowerShell zainstalowaną za pomocą tego polecenia w wierszu Azure PowerShell wiersza polecenia.

```azurepowershell
    Get-Module Az | format-table version
```

Najpierw musisz zalogować się do platformy Azure za pomocą tego polecenia.

```azurepowershell
    Connect-AzAccount
```

Podaj adres e-mail konta platformy Azure i jego hasło w Microsoft Azure logowania.

Następnie, jeśli masz wiele subskrypcji platformy Azure, musisz ustawić subskrypcję platformy Azure. Aby wyświetlić listę bieżących subskrypcji, uruchom to polecenie.

```azurepowershell
    Get-AzSubscription | sort SubscriptionName | Select SubscriptionName
```

Aby określić subskrypcję, uruchom następujące polecenie. W poniższym przykładzie nazwa subskrypcji to `ContosoSubscription` .

```azurepowershell
    Select-AzSubscription -SubscriptionName ContosoSubscription
```

Aby można było używać Windows PowerShell z Azure Resource Manager, potrzebne są następujące elementy:

* Windows PowerShell, wersja 3.0 lub 4.0. Aby znaleźć wersję Windows PowerShell, wpisz: i sprawdź, czy wartość `$PSVersionTable` `PSVersion` to 3.0 lub 4.0. Aby zainstalować zgodną wersję, zobacz [Windows Management Framework 3.0.](https://www.microsoft.com/download/details.aspx?id=34595)

Aby uzyskać szczegółową pomoc dla dowolnego polecenia cmdlet, które jest opisane w tym samouczku, użyj Get-Help cmdlet.

```azurepowershell
    Get-Help <cmdlet-name> -Detailed
```

Aby na przykład uzyskać pomoc dla `New-AzRedisCache` polecenia cmdlet, wpisz:

```azurepowershell
    Get-Help New-AzRedisCache -Detailed
```

### <a name="how-to-connect-to-other-clouds"></a>Jak nawiązać połączenie z innymi chmurami
Domyślnie środowisko platformy Azure to `AzureCloud` , które reprezentuje globalne wystąpienie chmury platformy Azure. Aby nawiązać połączenie z innym wystąpieniem, użyj polecenia z przełącznikiem wiersza polecenia lub - z `Connect-AzAccount` `-Environment` `EnvironmentName` żądaną nazwą środowiska lub środowiska.

Aby wyświetlić listę dostępnych środowisk, uruchom `Get-AzEnvironment` polecenie cmdlet .

### <a name="to-connect-to-the-azure-government-cloud"></a>Aby nawiązać połączenie z Azure Government Cloud
Aby nawiązać połączenie z Azure Government Cloud, użyj jednego z następujących poleceń.

```azurepowershell
    Connect-AzAccount -EnvironmentName AzureUSGovernment
```

lub

```azurepowershell
    Connect-AzAccount -Environment (Get-AzEnvironment -Name AzureUSGovernment)
```

Aby utworzyć pamięć podręczną w Azure Government Cloud, użyj jednej z następujących lokalizacji.

* USGov Wirginia
* USGov Iowa

Aby uzyskać więcej informacji na temat chmury Azure Government, zobacz [Microsoft Azure Government](https://azure.microsoft.com/features/gov/) i [Microsoft Azure Government Developer Guide](../azure-government/documentation-government-developer-guide.md).

### <a name="to-connect-to-the-azure-china-cloud"></a>Aby nawiązać połączenie z chmurą Azure China Cloud
Aby nawiązać połączenie z chmurą Azure China Cloud, użyj jednego z następujących poleceń.

```azurepowershell
    Connect-AzAccount -EnvironmentName AzureChinaCloud
```

lub

```azurepowershell
    Connect-AzAccount -Environment (Get-AzEnvironment -Name AzureChinaCloud)
```

Aby utworzyć pamięć podręczną w usłudze Azure China Cloud, użyj jednej z następujących lokalizacji.

* Chiny Wschodnie
* Chiny Północne

Aby uzyskać więcej informacji na temat chmury Azure China Cloud, zobacz [AzureChinaCloud for Azure operated by 21Vianet in China (Usługa AzureChinaCloud dla](https://www.windowsazure.cn/)platformy Azure obsługiwana przez firmę 21Vianet w Chinach).

### <a name="to-connect-to-microsoft-azure-germany"></a>Aby nawiązać połączenie z Microsoft Azure (Niemcy)
Aby nawiązać Microsoft Azure (Niemcy), użyj jednego z następujących poleceń.

```azurepowershell
    Connect-AzAccount -EnvironmentName AzureGermanCloud
```

lub

```azurepowershell
    Connect-AzAccount -Environment (Get-AzEnvironment -Name AzureGermanCloud)
```

Aby utworzyć pamięć podręczną w Microsoft Azure (Niemcy), użyj jednej z następujących lokalizacji.

* Niemcy Środkowe
* Niemcy Północno-Wschodnie

Aby uzyskać więcej informacji o Microsoft Azure (Niemcy), zobacz [Microsoft Azure (Niemcy)](https://azure.microsoft.com/overview/clouds/germany/).

### <a name="properties-used-for-azure-cache-for-redis-powershell"></a>Właściwości używane w programie Azure Cache for Redis PowerShell
W poniższej tabeli przedstawiono właściwości i opisy często używanych parametrów podczas tworzenia wystąpień Azure Cache for Redis przy użyciu Azure PowerShell.

| Parametr | Opis | Domyślne |
| --- | --- | --- |
| Nazwa |Nazwa pamięci podręcznej | |
| Lokalizacja |Lokalizacja pamięci podręcznej | |
| ResourceGroupName |Nazwa grupy zasobów, w której ma być tworzyć pamięć podręczną | |
| Rozmiar |Rozmiar pamięci podręcznej. Prawidłowe wartości to: P1, P2, P3, P4, C0, C1, C2, C3, C4, C5, C6, 250MB, 1GB, 2,5GB, 6GB, 13 GB, 26 GB, 53 GB |1 GB |
| ShardCount |Liczba fragmentów do utworzenia podczas tworzenia pamięci podręcznej Premium z włączonym klastrem. Prawidłowe wartości to: 1, 2, 3, 4, 5, 6, 7, 8, 9, 10 | |
| SKU |Określa sku pamięci podręcznej. Prawidłowe wartości to: Podstawowa, Standardowa, Premium |Standardowa (Standard) |
| RedisConfiguration |Określa ustawienia konfiguracji usługi Redis. Aby uzyskać szczegółowe informacje na temat każdego ustawienia, zobacz następującą tabelę właściwości [redisConfiguration.](#redisconfiguration-properties) | |
| EnableNonSslPort |Wskazuje, czy port bez protokołu SSL jest włączony. |Fałsz |
| MaxMemoryPolicy |Ten parametr jest przestarzały — zamiast tego użyj parametru RedisConfiguration. | |
| StaticIP |Podczas hostowania pamięci podręcznej w sieci wirtualnej określa unikatowy adres IP w podsieci dla pamięci podręcznej. Jeśli nie zostanie podany, zostanie on wybrany z podsieci. | |
| Podsieć |W przypadku hostowania pamięci podręcznej w sieci wirtualnej określa nazwę podsieci, w której ma zostać wdrożona pamięć podręczna. | |
| VirtualNetwork |W przypadku hostowania pamięci podręcznej w sieci wirtualnej określa identyfikator zasobu sieci wirtualnej, w której ma zostać wdrożona pamięć podręczna. | |
| Keytype |Określa, który klucz dostępu ma zostać ponownie wygenerowany podczas odnawiania kluczy dostępu. Prawidłowe wartości to: Podstawowy, Pomocniczy | |

### <a name="redisconfiguration-properties"></a>Właściwości redisConfiguration
| Właściwość | Opis | Warstwy cenowe |
| --- | --- | --- |
| rdb-backup-enabled |Czy [włączono trwałość danych redis](cache-how-to-premium-persistence.md) |Tylko Premium |
| rdb-storage-connection-string |Parametrów połączenia z kontem magazynu w celu [utrwalenia danych usługi Redis](cache-how-to-premium-persistence.md) |Tylko Premium |
| rdb-backup-frequency |Częstotliwość tworzenia kopii zapasowych dla trwałości danych [usługi Redis](cache-how-to-premium-persistence.md) |Tylko Premium |
| maxmemory-reserved |Konfiguruje pamięć [zarezerwowaną dla](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) procesów innych niż pamięci podręcznej |Standardowa i Premium |
| maxmemory-policy |Konfiguruje zasady [eksmisji](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) dla pamięci podręcznej |Wszystkie warstwy cenowe |
| notify-keyspace-events |Konfiguruje [powiadomienia dotyczące przestrzeni kluczy](cache-configure.md#keyspace-notifications-advanced-settings) |Standardowa i Premium |
| hash-max-ziplist-entries |Konfiguruje [optymalizację pamięci dla](https://redis.io/topics/memory-optimization) małych zagregowanych typów danych |Standardowa i Premium |
| hash-max-ziplist-value |Konfiguruje [optymalizację pamięci dla](https://redis.io/topics/memory-optimization) małych zagregowanych typów danych |Standardowa i Premium |
| set-max-intset-entries |Konfiguruje [optymalizację pamięci dla](https://redis.io/topics/memory-optimization) małych zagregowanych typów danych |Standardowa i Premium |
| zset-max-ziplist-entries |Konfiguruje [optymalizację pamięci dla](https://redis.io/topics/memory-optimization) małych zagregowanych typów danych |Standardowa i Premium |
| zset-max-ziplist-value |Konfiguruje [optymalizację pamięci dla](https://redis.io/topics/memory-optimization) małych zagregowanych typów danych |Standardowa i Premium |
| bazy danych |Konfiguruje liczbę baz danych. Tę właściwość można skonfigurować tylko podczas tworzenia pamięci podręcznej. |Standardowa i Premium |

## <a name="to-create-an-azure-cache-for-redis"></a>Aby utworzyć Azure Cache for Redis
Nowe Azure Cache for Redis są tworzone przy użyciu polecenia cmdlet [New-AzRedisCache.](/powershell/module/az.rediscache/new-azrediscache)

> [!IMPORTANT]
> Przy pierwszym utworzeniu Azure Cache for Redis subskrypcji przy użyciu usługi Azure Portal portal rejestruje przestrzeń `Microsoft.Cache` nazw dla tej subskrypcji. Jeśli spróbujesz utworzyć pierwszą Azure Cache for Redis subskrypcji przy użyciu programu PowerShell, musisz najpierw zarejestrować przestrzeń nazw przy użyciu następującego polecenia. w przeciwnym razie polecenia cmdlet, takie `New-AzRedisCache` jak i , nie `Get-AzRedisCache` powiodą się.
> 
> `Register-AzResourceProvider -ProviderNamespace "Microsoft.Cache"`
> 
> 

Aby wyświetlić listę dostępnych parametrów i ich opisy dla `New-AzRedisCache` , uruchom następujące polecenie.

```azurepowershell
    PS C:\> Get-Help New-AzRedisCache -detailed

    NAME
        New-AzRedisCache

    SYNOPSIS
        Creates a new Azure Cache for Redis.


    SYNTAX
        New-AzRedisCache -Name <String> -ResourceGroupName <String> -Location <String> [-RedisVersion <String>]
        [-Size <String>] [-Sku <String>] [-MaxMemoryPolicy <String>] [-RedisConfiguration <Hashtable>] [-EnableNonSslPort
        <Boolean>] [-ShardCount <Integer>] [-VirtualNetwork <String>] [-Subnet <String>] [-StaticIP <String>]
        [<CommonParameters>]


    DESCRIPTION
        The New-AzRedisCache cmdlet creates a new Azure Cache for Redis.


    PARAMETERS
        -Name <String>
            Name of the Azure Cache for Redis to create.

        -ResourceGroupName <String>
            Name of resource group in which to create the Azure Cache for Redis.

        -Location <String>
            Location in which to create the Azure Cache for Redis.

        -RedisVersion <String>
            RedisVersion is deprecated and will be removed in future release.

        -Size <String>
            Size of the Azure Cache for Redis. The default value is 1GB or C1. Possible values are P1, P2, P3, P4, C0, C1, C2, C3,
            C4, C5, C6, 250MB, 1GB, 2.5GB, 6GB, 13GB, 26GB, 53GB.

        -Sku <String>
            Sku of Azure Cache for Redis. The default value is Standard. Possible values are Basic, Standard and Premium.

        -MaxMemoryPolicy <String>
            The 'MaxMemoryPolicy' setting has been deprecated. Please use 'RedisConfiguration' setting to set
            MaxMemoryPolicy. e.g. -RedisConfiguration @{"maxmemory-policy" = "allkeys-lru"}

        -RedisConfiguration <Hashtable>
            All Redis Configuration Settings. Few possible keys: rdb-backup-enabled, rdb-storage-connection-string,
            rdb-backup-frequency, maxmemory-reserved, maxmemory-policy, notify-keyspace-events, hash-max-ziplist-entries,
            hash-max-ziplist-value, set-max-intset-entries, zset-max-ziplist-entries, zset-max-ziplist-value, databases.

        -EnableNonSslPort <Boolean>
            EnableNonSslPort is used by Azure Cache for Redis. If no value is provided, the default value is false and the
            non-SSL port will be disabled. Possible values are true and false.

        -ShardCount <Integer>
            The number of shards to create on a Premium Cluster Cache.

        -VirtualNetwork <String>
            The exact ARM resource ID of the virtual network to deploy the Azure Cache for Redis in. Example format: /subscriptions/{
            subid}/resourceGroups/{resourceGroupName}/providers/Microsoft.ClassicNetwork/VirtualNetworks/{vnetName}

        -Subnet <String>
            Required when deploying an Azure Cache for Redis inside an existing Azure Virtual Network.

        -StaticIP <String>
            Required when deploying an Azure Cache for Redis inside an existing Azure Virtual Network.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).
```

Aby utworzyć pamięć podręczną z parametrami domyślnymi, uruchom następujące polecenie.

```azurepowershell
    New-AzRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US"
```

`ResourceGroupName`Parametry `Name` , i są `Location` wymagane, ale pozostałe są opcjonalne i mają wartości domyślne. Uruchomienie poprzedniego polecenia powoduje utworzenie standardowej Azure Cache for Redis SKU o określonej nazwie, lokalizacji i grupie zasobów o rozmiarze 1 GB z wyłączonym portem bez protokołu SSL.

Aby utworzyć pamięć podręczną Premium, określ rozmiar P1 (6 GB – 60 GB), P2 (13 GB – 130 GB), P3 (26 GB –260 GB) lub P4 (53 GB –530 GB). Aby włączyć klastrowanie, określ liczbę fragmentów przy użyciu `ShardCount` parametru . Poniższy przykład tworzy pamięć podręczną P1 Premium z 3 fragmentami. Pamięć podręczna P1 Premium ma rozmiar 6 GB, a ponieważ określono trzy fragmenty, łączny rozmiar wynosi 18 GB (3 x 6 GB).

```azurepowershell
    New-AzRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -Sku Premium -Size P1 -ShardCount 3
```

Aby określić wartości dla parametru, należy ująć wartości wewnątrz jako `RedisConfiguration` `{}` pary klucz/wartość, takie jak `@{"maxmemory-policy" = "allkeys-random", "notify-keyspace-events" = "KEA"}` . Poniższy przykład tworzy standardową pamięć podręczną o rozmiarze 1 GB z `allkeys-random` zasadami maxmemory i powiadomieniami przestrzeni kluczy skonfigurowanymi za pomocą . `KEA` Aby uzyskać więcej informacji, zobacz [Powiadomienia dotyczące przestrzeni kluczy (ustawienia zaawansowane)](cache-configure.md#keyspace-notifications-advanced-settings) i Zasady [pamięci.](cache-configure.md#memory-policies)

```azurepowershell
    New-AzRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -RedisConfiguration @{"maxmemory-policy" = "allkeys-random", "notify-keyspace-events" = "KEA"}
```

<a name="databases"></a>

## <a name="to-configure-the-databases-setting-during-cache-creation"></a>Aby skonfigurować ustawienie baz danych podczas tworzenia pamięci podręcznej
Ustawienie `databases` można skonfigurować tylko podczas tworzenia pamięci podręcznej. Poniższy przykład tworzy pamięć podręczną Premium P3 (26 GB) z 48 bazami danych przy użyciu polecenia cmdlet [New-AzRedisCache.](/powershell/module/az.rediscache/New-azRedisCache)

```azurepowershell
    New-AzRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -Sku Premium -Size P3 -RedisConfiguration @{"databases" = "48"}
```

Aby uzyskać więcej informacji na temat `databases` właściwości , zobacz Azure Cache for Redis konfiguracji serwera [.](cache-configure.md#default-redis-server-configuration) Aby uzyskać więcej informacji na temat tworzenia pamięci podręcznej za pomocą polecenia cmdlet [New-AzRedisCache,](/powershell/module/az.rediscache/new-azrediscache) zobacz poprzednią sekcję Aby utworzyć Azure Cache for Redis pamięci podręcznej.

## <a name="to-update-an-azure-cache-for-redis"></a>Aby zaktualizować Azure Cache for Redis
Azure Cache for Redis są aktualizowane przy użyciu polecenia cmdlet [Set-AzRedisCache.](/powershell/module/az.rediscache/Set-azRedisCache)

Aby wyświetlić listę dostępnych parametrów i ich opisy `Set-AzRedisCache` dla , uruchom następujące polecenie.

```azurepowershell
    PS C:\> Get-Help Set-AzRedisCache -detailed

    NAME
        Set-AzRedisCache

    SYNOPSIS
        Set Azure Cache for Redis updatable parameters.

    SYNTAX
        Set-AzRedisCache -Name <String> -ResourceGroupName <String> [-Size <String>] [-Sku <String>]
        [-MaxMemoryPolicy <String>] [-RedisConfiguration <Hashtable>] [-EnableNonSslPort <Boolean>] [-ShardCount
        <Integer>] [<CommonParameters>]

    DESCRIPTION
        The Set-AzRedisCache cmdlet sets Azure Cache for Redis parameters.

    PARAMETERS
        -Name <String>
            Name of the Azure Cache for Redis to update.

        -ResourceGroupName <String>
            Name of the resource group for the cache.

        -Size <String>
            Size of the Azure Cache for Redis. The default value is 1GB or C1. Possible values are P1, P2, P3, P4, C0, C1, C2, C3,
            C4, C5, C6, 250MB, 1GB, 2.5GB, 6GB, 13GB, 26GB, 53GB.

        -Sku <String>
            Sku of Azure Cache for Redis. The default value is Standard. Possible values are Basic, Standard and Premium.

        -MaxMemoryPolicy <String>
            The 'MaxMemoryPolicy' setting has been deprecated. Please use 'RedisConfiguration' setting to set
            MaxMemoryPolicy. e.g. -RedisConfiguration @{"maxmemory-policy" = "allkeys-lru"}

        -RedisConfiguration <Hashtable>
            All Redis Configuration Settings. Few possible keys: rdb-backup-enabled, rdb-storage-connection-string,
            rdb-backup-frequency, maxmemory-reserved, maxmemory-policy, notify-keyspace-events, hash-max-ziplist-entries,
            hash-max-ziplist-value, set-max-intset-entries, zset-max-ziplist-entries, zset-max-ziplist-value.

        -EnableNonSslPort <Boolean>
            EnableNonSslPort is used by Azure Cache for Redis. The default value is null and no change will be made to the
            currently configured value. Possible values are true and false.

        -ShardCount <Integer>
            The number of shards to create on a Premium Cluster Cache.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).
```

Polecenie `Set-AzRedisCache` cmdlet może służyć do aktualizowania właściwości, takich jak `Size` , , i `Sku` `EnableNonSslPort` `RedisConfiguration` wartości. 

Następujące polecenie aktualizuje maxmemory-policy dla Azure Cache for Redis o nazwie myCache.

```azurepowershell
    Set-AzRedisCache -ResourceGroupName "myGroup" -Name "myCache" -RedisConfiguration @{"maxmemory-policy" = "allkeys-random"}
```

<a name="scale"></a>

## <a name="to-scale-an-azure-cache-for-redis"></a>Aby skalować Azure Cache for Redis
`Set-AzRedisCache` Element może służyć do skalowania Azure Cache for Redis, `Size` gdy właściwości , lub są `Sku` `ShardCount` modyfikowane. 

> [!NOTE]
> Skalowanie pamięci podręcznej przy użyciu programu PowerShell podlega tym samym limitom i wytycznym co skalowanie pamięci podręcznej z poziomu Azure Portal. Można skalować do innej warstwy cenowej z następującymi ograniczeniami.
> 
> * Nie można skalować z wyższej warstwy cenowej do niższej warstwy cenowej.
> * Nie można skalować z pamięci podręcznej **Premium** w dół **do** standardowej lub podstawowej pamięci **podręcznej.**
> * Nie można skalować z **standardowej** pamięci podręcznej w dół do podstawowej **pamięci podręcznej.**
> * Można skalować z podstawowej **pamięci podręcznej** do **standardowej** pamięci podręcznej, ale nie można zmienić rozmiaru w tym samym czasie. Jeśli potrzebujesz innego rozmiaru, możesz wykonać kolejną operację skalowania do żądanego rozmiaru.
> * Nie można skalować z podstawowej pamięci **podręcznej** bezpośrednio do pamięci **podręcznej Premium.** W jednej operacji  skalowania **należy** wykonać skalowanie  z poziomu Podstawowego do standardowego, a następnie ze Standardowa do **Premium** w kolejnej operacji skalowania.
> * Nie można skalować od większego rozmiaru do rozmiaru **C0 (250 MB).**
> 
> Aby uzyskać więcej informacji, [zobacz Jak skalować Azure Cache for Redis](cache-how-to-scale.md).
> 
> 

W poniższym przykładzie pokazano, jak skalować pamięć podręczną o nazwie `myCache` do pamięci podręcznej o rozmiarze 2,5 GB. Należy pamiętać, że to polecenie działa zarówno w przypadku podstawowej, jak i standardowej pamięci podręcznej.

```azurepowershell
    Set-AzRedisCache -ResourceGroupName myGroup -Name myCache -Size 2.5GB
```

Po wystawionym poleceniu jest zwracany stan pamięci podręcznej (podobnie jak w przypadku wywoływania `Get-AzRedisCache` polecenia ). Zwróć uwagę, że `ProvisioningState` jest `Scaling` to .

```azurepowershell
    PS C:\> Set-AzRedisCache -Name myCache -ResourceGroupName myGroup -Size 2.5GB


    Name               : mycache
    Id                 : /subscriptions/12ad12bd-abdc-2231-a2ed-a2b8b246bbad4/resourceGroups/mygroup/providers/Mi
                         crosoft.Cache/Redis/mycache
    Location           : South Central US
    Type               : Microsoft.Cache/Redis
    HostName           : mycache.redis.cache.windows.net
    Port               : 6379
    ProvisioningState  : Scaling
    SslPort            : 6380
    RedisConfiguration : {[maxmemory-policy, volatile-lru], [maxmemory-reserved, 150], [notify-keyspace-events, KEA],
                         [maxmemory-delta, 150]...}
    EnableNonSslPort   : False
    RedisVersion       : 3.0
    Size               : 1GB
    Sku                : Standard
    ResourceGroupName  : mygroup
    PrimaryKey         : ....
    SecondaryKey       : ....
    VirtualNetwork     :
    Subnet             :
    StaticIP           :
    TenantSettings     : {}
    ShardCount         :
```

Po zakończeniu operacji skalowania wartość `ProvisioningState` zmienia się na `Succeeded` . Jeśli musisz wykonać kolejną operację skalowania, taką jak zmiana z Podstawowej na Standardowa, a następnie zmiana rozmiaru, musisz poczekać na ukończenie poprzedniej operacji lub wystąpi błąd podobny do poniższego.

```azurepowershell
    Set-AzRedisCache : Conflict: The resource '...' is not in a stable state, and is currently unable to accept the update request.
```

## <a name="to-get-information-about-an-azure-cache-for-redis"></a>Aby uzyskać informacje o Azure Cache for Redis
Informacje o pamięci podręcznej można pobrać za pomocą polecenia cmdlet [Get-AzRedisCache.](/powershell/module/az.rediscache/get-azrediscache)

Aby wyświetlić listę dostępnych parametrów i ich opisy dla `Get-AzRedisCache` , uruchom następujące polecenie.

```azurepowershell
    PS C:\> Get-Help Get-AzRedisCache -detailed

    NAME
        Get-AzRedisCache

    SYNOPSIS
        Gets details about a single cache or all caches in the specified resource group or all caches in the current
        subscription.

    SYNTAX
        Get-AzRedisCache [-Name <String>] [-ResourceGroupName <String>] [<CommonParameters>]

    DESCRIPTION
        The Get-AzRedisCache cmdlet gets the details about a cache or caches depending on input parameters. If both
        ResourceGroupName and Name parameters are provided then Get-AzRedisCache will return details about the
        specific cache name provided.

        If only ResourceGroupName is provided than it will return details about all caches in the specified resource group.

        If no parameters are given than it will return details about all caches the current subscription.

    PARAMETERS
        -Name <String>
            The name of the cache. When this parameter is provided along with ResourceGroupName, Get-AzRedisCache
            returns the details for the cache.

        -ResourceGroupName <String>
            The name of the resource group that contains the cache or caches. If ResourceGroupName is provided with Name
            then Get-AzRedisCache returns the details of the cache specified by Name. If only the ResourceGroup
            parameter is provided, then details for all caches in the resource group are returned.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).
```

Aby zwrócić informacje o wszystkich pamięciach podręcznych w bieżącej subskrypcji, uruchom `Get-AzRedisCache` bez żadnych parametrów.

```azurepowershell
    Get-AzRedisCache
```

Aby zwrócić informacje o wszystkich pamięciach podręcznych w określonej grupie zasobów, uruchom z `Get-AzRedisCache` `ResourceGroupName` parametrem .

```azurepowershell
    Get-AzRedisCache -ResourceGroupName myGroup
```

Aby zwrócić informacje o określonej pamięci podręcznej, uruchom z parametrem zawierającym nazwę pamięci podręcznej i parametrem z grupą zasobów `Get-AzRedisCache` `Name` `ResourceGroupName` zawierającą pamięć podręczną.

```azurepowershell
    PS C:\> Get-AzRedisCache -Name myCache -ResourceGroupName myGroup

    Name               : mycache
    Id                 : /subscriptions/12ad12bd-abdc-2231-a2ed-a2b8b246bbad4/resourceGroups/myGroup/providers/Mi
                         crosoft.Cache/Redis/mycache
    Location           : South Central US
    Type               : Microsoft.Cache/Redis
    HostName           : mycache.redis.cache.windows.net
    Port               : 6379
    ProvisioningState  : Succeeded
    SslPort            : 6380
    RedisConfiguration : {[maxmemory-policy, volatile-lru], [maxmemory-reserved, 62], [notify-keyspace-events, KEA],
                         [maxclients, 1000]...}
    EnableNonSslPort   : False
    RedisVersion       : 3.0
    Size               : 1GB
    Sku                : Standard
    ResourceGroupName  : myGroup
    VirtualNetwork     :
    Subnet             :
    StaticIP           :
    TenantSettings     : {}
    ShardCount         :
```

## <a name="to-retrieve-the-access-keys-for-an-azure-cache-for-redis"></a>Aby pobrać klucze dostępu dla Azure Cache for Redis
Aby pobrać klucze dostępu do pamięci podręcznej, możesz użyć polecenia cmdlet [Get-AzRedisCacheKey.](/powershell/module/az.rediscache/Get-azRedisCacheKey)

Aby wyświetlić listę dostępnych parametrów i ich opisy dla `Get-AzRedisCacheKey` , uruchom następujące polecenie.

```azurepowershell
    PS C:\> Get-Help Get-AzRedisCacheKey -detailed

    NAME
        Get-AzRedisCacheKey

    SYNOPSIS
        Gets the accesskeys for the specified Azure Cache for Redis.


    SYNTAX
        Get-AzRedisCacheKey -Name <String> -ResourceGroupName <String> [<CommonParameters>]

    DESCRIPTION
        The Get-AzRedisCacheKey cmdlet gets the access keys for the specified cache.

    PARAMETERS
        -Name <String>
            Name of the Azure Cache for Redis.

        -ResourceGroupName <String>
            Name of the resource group for the cache.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).
```

Aby pobrać klucze dla pamięci podręcznej, wywołaj polecenie cmdlet i przekaż nazwę pamięci podręcznej nazwę grupy zasobów `Get-AzRedisCacheKey` zawierającej pamięć podręczną.

```azurepowershell
    PS C:\> Get-AzRedisCacheKey -Name myCache -ResourceGroupName myGroup

    PrimaryKey   : b2wdt43sfetlju4hfbryfnregrd9wgIcc6IA3zAO1lY=
    SecondaryKey : ABhfB757JgjIgt785JgKH9865eifmekfnn649303JKL=
```

## <a name="to-regenerate-access-keys-for-your-azure-cache-for-redis"></a>Aby ponownie wygenerować klucze dostępu dla Azure Cache for Redis
Aby ponownie wygenerować klucze dostępu dla pamięci podręcznej, można użyć polecenia cmdlet [New-AzRedisCacheKey.](/powershell/module/az.rediscache/New-azRedisCacheKey)

Aby wyświetlić listę dostępnych parametrów i ich opisy dla `New-AzRedisCacheKey` , uruchom następujące polecenie.

```azurepowershell
    PS C:\> Get-Help New-AzRedisCacheKey -detailed

    NAME
        New-AzRedisCacheKey

    SYNOPSIS
        Regenerates the access key of an Azure Cache for Redis.

    SYNTAX
        New-AzRedisCacheKey -Name <String> -ResourceGroupName <String> -KeyType <String> [-Force] [<CommonParameters>]

    DESCRIPTION
        The New-AzRedisCacheKey cmdlet regenerate the access key of an Azure Cache for Redis.

    PARAMETERS
        -Name <String>
            Name of the Azure Cache for Redis.

        -ResourceGroupName <String>
            Name of the resource group for the cache.

        -KeyType <String>
            Specifies whether to regenerate the primary or secondary access key. Possible values are Primary or Secondary.

        -Force
            When the Force parameter is provided, the specified access key is regenerated without any confirmation prompts.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).
```

Aby ponownie wygenerować klucz podstawowy lub pomocniczy dla pamięci podręcznej, wywołaj polecenie cmdlet i przekaż nazwę, grupę zasobów i określ wartość `New-AzRedisCacheKey` `Primary` lub dla `Secondary` `KeyType` parametru . W poniższym przykładzie jest ponownie generowany pomocniczy klucz dostępu dla pamięci podręcznej.

```azurepowershell
    PS C:\> New-AzRedisCacheKey -Name myCache -ResourceGroupName myGroup -KeyType Secondary

    Confirm
    Are you sure you want to regenerate Secondary key for Azure Cache for Redis 'myCache'?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y


    PrimaryKey   : b2wdt43sfetlju4hfbryfnregrd9wgIcc6IA3zAO1lY=
    SecondaryKey : c53hj3kh4jhHjPJk8l0jji785JgKH9865eifmekfnn6=
```

## <a name="to-delete-an-azure-cache-for-redis"></a>Aby usunąć Azure Cache for Redis
Aby usunąć Azure Cache for Redis, użyj polecenia cmdlet [Remove-AzRedisCache.](/powershell/module/az.rediscache/remove-azrediscache)

Aby wyświetlić listę dostępnych parametrów i ich opisy `Remove-AzRedisCache` dla , uruchom następujące polecenie.

```azurepowershell
    PS C:\> Get-Help Remove-AzRedisCache -detailed

    NAME
        Remove-AzRedisCache

    SYNOPSIS
        Remove Azure Cache for Redis if exists.

    SYNTAX
        Remove-AzRedisCache -Name <String> -ResourceGroupName <String> [-Force] [-PassThru] [<CommonParameters>

    DESCRIPTION
        The Remove-AzRedisCache cmdlet removes an Azure Cache for Redis if it exists.

    PARAMETERS
        -Name <String>
            Name of the Azure Cache for Redis to remove.

        -ResourceGroupName <String>
            Name of the resource group of the cache to remove.

        -Force
            When the Force parameter is provided, the cache is removed without any confirmation prompts.

        -PassThru
            By default Remove-AzRedisCache removes the cache and does not return any value. If the PassThru par
            is provided then Remove-AzRedisCache returns a boolean value indicating the success of the operatio

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).
```

W poniższym przykładzie pamięć podręczna o nazwie `myCache` zostanie usunięta.

```azurepowershell
    PS C:\> Remove-AzRedisCache -Name myCache -ResourceGroupName myGroup

    Confirm
    Are you sure you want to remove Azure Cache for Redis 'myCache'?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y
```


## <a name="to-import-an-azure-cache-for-redis"></a>Aby zaimportować Azure Cache for Redis
Dane można zaimportować do wystąpienia Azure Cache for Redis przy użyciu `Import-AzRedisCache` polecenia cmdlet .

> [!IMPORTANT]
> Import/eksport jest dostępny tylko dla [pamięci podręcznych w warstwie Premium.](cache-overview.md#service-tiers) Aby uzyskać więcej informacji na temat importowania/eksportowania, zobacz [Importowanie i eksportowanie danych w Azure Cache for Redis](cache-how-to-import-export-data.md).
> 
> 

Aby wyświetlić listę dostępnych parametrów i ich opisy `Import-AzRedisCache` dla , uruchom następujące polecenie.

```azurepowershell
    PS C:\> Get-Help Import-AzRedisCache -detailed

    NAME
        Import-AzRedisCache

    SYNOPSIS
        Import data from blobs to Azure Cache for Redis.


    SYNTAX
        Import-AzRedisCache -Name <String> -ResourceGroupName <String> -Files <String[]> [-Format <String>] [-Force]
        [-PassThru] [<CommonParameters>]


    DESCRIPTION
        The Import-AzRedisCache cmdlet imports data from the specified blobs into Azure Cache for Redis.


    PARAMETERS
        -Name <String>
            The name of the cache.

        -ResourceGroupName <String>
            The name of the resource group that contains the cache.

        -Files <String[]>
            SAS urls of blobs whose content should be imported into the cache.

        -Format <String>
            Format for the blob.  Currently "rdb" is the only supported, with other formats expected in the future.

        -Force
            When the Force parameter is provided, import will be performed without any confirmation prompts.

        -PassThru
            By default Import-AzRedisCache imports data in cache and does not return any value. If the PassThru
            parameter is provided then Import-AzRedisCache returns a boolean value indicating the success of the
            operation.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).
```


Następujące polecenie importuje dane z obiektu blob określonego przez wartość URI sygnatury dostępu współdzielonego do Azure Cache for Redis.

```azurepowershell
    PS C:\>Import-AzRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -Files @("https://mystorageaccount.blob.core.windows.net/mycontainername/blobname?sv=2015-04-05&sr=b&sig=caIwutG2uDa0NZ8mjdNJdgOY8%2F8mhwRuGNdICU%2B0pI4%3D&st=2016-05-27T00%3A00%3A00Z&se=2016-05-28T00%3A00%3A00Z&sp=rwd") -Force
```

## <a name="to-export-an-azure-cache-for-redis"></a>Aby wyeksportować Azure Cache for Redis
Dane z wystąpienia usługi Azure Cache for Redis można wyeksportować przy użyciu `Export-AzRedisCache` polecenia cmdlet .

> [!IMPORTANT]
> Import/eksport jest dostępny tylko dla pamięci [podręcznych w warstwie Premium.](cache-overview.md#service-tiers) Aby uzyskać więcej informacji na temat importowania/eksportowania, zobacz [Importowanie i eksportowanie danych w Azure Cache for Redis](cache-how-to-import-export-data.md).
> 
> 

Aby wyświetlić listę dostępnych parametrów i ich opisy `Export-AzRedisCache` dla , uruchom następujące polecenie.

```azurepowershell
    PS C:\> Get-Help Export-AzRedisCache -detailed

    NAME
        Export-AzRedisCache

    SYNOPSIS
        Exports data from Azure Cache for Redis to a specified container.


    SYNTAX
        Export-AzRedisCache -Name <String> -ResourceGroupName <String> -Prefix <String> -Container <String> [-Format
        <String>] [-PassThru] [<CommonParameters>]


    DESCRIPTION
        The Export-AzRedisCache cmdlet exports data from Azure Cache for Redis to a specified container.


    PARAMETERS
        -Name <String>
            The name of the cache.

        -ResourceGroupName <String>
            The name of the resource group that contains the cache.

        -Prefix <String>
            Prefix to use for blob names.

        -Container <String>
            SAS url of container where data should be exported.

        -Format <String>
            Format for the blob.  Currently "rdb" is the only supported, with other formats expected in the future.

        -PassThru
            By default Export-AzRedisCache does not return any value. If the PassThru parameter is provided
            then Export-AzRedisCache returns a boolean value indicating the success of the operation.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).
```


Następujące polecenie eksportuje dane z Azure Cache for Redis do kontenera określonego przez wartość URI sygnatury dostępu współdzielonego.

```azurepowershell
    PS C:\>Export-AzRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -Prefix "blobprefix"
    -Container "https://mystorageaccount.blob.core.windows.net/mycontainer?sv=2015-04-05&sr=c&sig=HezZtBZ3DURmEGDduauE7
    pvETY4kqlPI8JCNa8ATmaw%3D&st=2016-05-27T00%3A00%3A00Z&se=2016-05-28T00%3A00%3A00Z&sp=rwdl"
```

## <a name="to-reboot-an-azure-cache-for-redis"></a>Aby ponownie uruchomić Azure Cache for Redis
Możesz ponownie uruchomić wystąpienie Azure Cache for Redis przy użyciu `Reset-AzRedisCache` polecenia cmdlet .

> [!IMPORTANT]
> Ponowne uruchomienie jest dostępne tylko dla [pamięci podręcznych w warstwie](cache-overview.md#service-tiers) Premium. Aby uzyskać więcej informacji na temat ponownego uruchamiania pamięci podręcznej, zobacz [Administrowanie pamięcią podręczną — ponowne uruchamianie.](cache-administration.md#reboot)
> 
> 

Aby wyświetlić listę dostępnych parametrów i ich opisy dla `Reset-AzRedisCache` , uruchom następujące polecenie.

```azurepowershell
    PS C:\> Get-Help Reset-AzRedisCache -detailed

    NAME
        Reset-AzRedisCache

    SYNOPSIS
        Reboot specified node(s) of an Azure Cache for Redis instance.


    SYNTAX
        Reset-AzRedisCache -Name <String> -ResourceGroupName <String> -RebootType <String> [-ShardId <Integer>]
        [-Force] [-PassThru] [<CommonParameters>]


    DESCRIPTION
        The Reset-AzRedisCache cmdlet reboots the specified node(s) of an Azure Cache for Redis instance.


    PARAMETERS
        -Name <String>
            The name of the cache.

        -ResourceGroupName <String>
            The name of the resource group that contains the cache.

        -RebootType <String>
            Which node to reboot. Possible values are "PrimaryNode", "SecondaryNode", "AllNodes".

        -ShardId <Integer>
            Which shard to reboot when rebooting a premium cache with clustering enabled.

        -Force
            When the Force parameter is provided, reset will be performed without any confirmation prompts.

        -PassThru
            By default Reset-AzRedisCache does not return any value. If the PassThru parameter is provided
            then Reset-AzRedisCache returns a boolean value indicating the success of the operation.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).
```


Następujące polecenie ponownie uruchamia oba węzły określonej pamięci podręcznej.

```azurepowershell
    PS C:\>Reset-AzRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -RebootType "AllNodes"
    -Force
```


## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej na temat Windows PowerShell z platformą Azure, zobacz następujące zasoby:

* [Azure Cache for Redis polecenia cmdlet w witrynie MSDN](/powershell/module/az.rediscache)
* [Azure Resource Manager cmdlet:](/powershell/module/)dowiedz się, jak używać tych Azure Resource Manager cmdlet.
* [Używanie grup zasobów do zarządzania zasobami platformy Azure:](../azure-resource-manager/templates/deploy-portal.md)dowiedz się, jak tworzyć grupy zasobów i zarządzać nimi w Azure Portal.
* [Blog platformy Azure:](https://azure.microsoft.com/blog/)Dowiedz się więcej o nowych funkcjach na platformie Azure.
* [Windows PowerShell blog:](https://devblogs.microsoft.com/powershell/)Dowiedz się więcej o nowych funkcjach w Windows PowerShell.
* ["Hey, Scripting Guy!" Blog:](https://devblogs.microsoft.com/scripting/tag/hey-scripting-guy/)Uzyskaj praktyczne porady i wskazówki od Windows PowerShell społeczności.
