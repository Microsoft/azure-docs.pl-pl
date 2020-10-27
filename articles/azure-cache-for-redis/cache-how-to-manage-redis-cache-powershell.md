---
title: Zarządzanie usługą Azure cache for Redis za pomocą Azure PowerShell
description: Dowiedz się, jak wykonywać zadania administracyjne dla usługi Azure cache for Redis przy użyciu Azure PowerShell.
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 07/13/2017
ms.author: yegu
ms.openlocfilehash: 88e433dbfa87d8ea483789b1fd838c62a6a481c0
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/26/2020
ms.locfileid: "92536763"
---
# <a name="manage-azure-cache-for-redis-with-azure-powershell"></a>Zarządzanie usługą Azure cache for Redis za pomocą Azure PowerShell
> [!div class="op_single_selector"]
> * [Program PowerShell](cache-how-to-manage-redis-cache-powershell.md)
> * [Interfejs wiersza polecenia platformy Azure](cache-manage-cli.md)
> 
> 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

W tym temacie pokazano, jak wykonywać typowe zadania, takie jak tworzenie, aktualizowanie i skalowanie pamięci podręcznej platformy Azure dla wystąpień Redis, jak generować ponownie klucze dostępu i jak wyświetlać informacje o pamięci podręcznej. Aby uzyskać pełną listę poleceń cmdlet programu PowerShell dla usługi Azure cache for Redis, zobacz [pamięć podręczna Azure dla poleceń cmdlet Redis](/powershell/module/az.rediscache).

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

Aby uzyskać więcej informacji na temat klasycznego modelu wdrażania, zobacz [Azure Resource Manager a wdrożenie klasyczne: Omówienie modeli wdrażania i stanu zasobów](../azure-resource-manager/management/deployment-models.md).

## <a name="prerequisites"></a>Wymagania wstępne
Jeśli zainstalowano już Azure PowerShell, musisz mieć Azure PowerShell w wersji 1.0.0 lub nowszej. W wierszu polecenia Azure PowerShell można sprawdzić wersję Azure PowerShell, która została zainstalowana za pomocą tego polecenia.

```azurepowershell
    Get-Module Az | format-table version
```

Najpierw musisz zalogować się do platformy Azure za pomocą tego polecenia.

```azurepowershell
    Connect-AzAccount
```

Określ adres e-mail konta platformy Azure i jego hasło w oknie dialogowym logowania Microsoft Azure.

Jeśli masz wiele subskrypcji platformy Azure, musisz ustawić subskrypcję platformy Azure. Aby wyświetlić listę bieżących subskrypcji, Uruchom to polecenie.

```azurepowershell
    Get-AzSubscription | sort SubscriptionName | Select SubscriptionName
```

Aby określić subskrypcję, uruchom następujące polecenie. W poniższym przykładzie nazwa subskrypcji to `ContosoSubscription` .

```azurepowershell
    Select-AzSubscription -SubscriptionName ContosoSubscription
```

Aby można było używać programu Windows PowerShell z Azure Resource Manager, potrzebne są następujące elementy:

* Windows PowerShell, wersja 3,0 lub 4,0. Aby znaleźć wersję programu Windows PowerShell, wpisz: `$PSVersionTable` i sprawdź, czy wartość `PSVersion` to 3,0 lub 4,0. Aby zainstalować zgodną wersję, zobacz [Windows Management Framework 3,0](https://www.microsoft.com/download/details.aspx?id=34595).

Aby uzyskać szczegółową pomoc dotyczącą każdego polecenia cmdlet wyświetlanego w tym samouczku, użyj polecenia cmdlet Get-Help.

```azurepowershell
    Get-Help <cmdlet-name> -Detailed
```

Na przykład aby uzyskać pomoc dotyczącą `New-AzRedisCache` polecenia cmdlet, wpisz:

```azurepowershell
    Get-Help New-AzRedisCache -Detailed
```

### <a name="how-to-connect-to-other-clouds"></a>Jak nawiązać połączenie z innymi chmurami
Domyślnie środowisko platformy Azure to `AzureCloud` , które reprezentuje globalne wystąpienie chmury platformy Azure. Aby nawiązać połączenie z innym wystąpieniem, użyj `Connect-AzAccount` polecenia z `-Environment` `EnvironmentName` przełącznikiem lub z wiersza polecenia z odpowiednią nazwą środowiska lub środowiska.

Aby wyświetlić listę dostępnych środowisk, uruchom `Get-AzEnvironment` polecenie cmdlet.

### <a name="to-connect-to-the-azure-government-cloud"></a>Aby nawiązać połączenie z chmurą Azure Government
Aby nawiązać połączenie z chmurą Azure Government, użyj jednego z następujących poleceń.

```azurepowershell
    Connect-AzAccount -EnvironmentName AzureUSGovernment
```

lub

```azurepowershell
    Connect-AzAccount -Environment (Get-AzEnvironment -Name AzureUSGovernment)
```

Aby utworzyć pamięć podręczną w chmurze Azure Government, użyj jednej z następujących lokalizacji.

* USGov Wirginia
* USGov Iowa

Aby uzyskać więcej informacji na temat chmury Azure Government, zobacz [przewodnik dewelopera](../azure-government/documentation-government-developer-guide.md) [Microsoft Azure Government](https://azure.microsoft.com/features/gov/) i Microsoft Azure Government.

### <a name="to-connect-to-the-azure-china-cloud"></a>Aby nawiązać połączenie z chmurą platformy Azure w Chinach
Aby nawiązać połączenie z chmurą Chin platformy Azure, użyj jednego z następujących poleceń.

```azurepowershell
    Connect-AzAccount -EnvironmentName AzureChinaCloud
```

lub

```azurepowershell
    Connect-AzAccount -Environment (Get-AzEnvironment -Name AzureChinaCloud)
```

Aby utworzyć pamięć podręczną w chmurze platformy Azure w Chinach, użyj jednej z następujących lokalizacji.

* Chiny Wschodnie
* Chiny Północne

Aby uzyskać więcej informacji o chmurze platformy Azure w Chinach, zobacz [AzureChinaCloud for Azure obsługiwane przez firmę 21Vianet w Chinach](https://www.windowsazure.cn/).

### <a name="to-connect-to-microsoft-azure-germany"></a>Aby nawiązać połączenie z Microsoft Azure (Niemcy)
Aby nawiązać połączenie z Microsoft Azure (Niemcy), użyj jednego z następujących poleceń.

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

Aby uzyskać więcej informacji na temat Microsoft Azure (Niemcy), zobacz [Microsoft Azure (Niemcy)](https://azure.microsoft.com/overview/clouds/germany/).

### <a name="properties-used-for-azure-cache-for-redis-powershell"></a>Właściwości używane dla usługi Azure cache for Redis PowerShell
Poniższa tabela zawiera właściwości i opisy często używanych parametrów podczas tworzenia i zarządzania pamięcią podręczną platformy Azure dla wystąpień Redis przy użyciu Azure PowerShell.

| Parametr | Opis | Domyślne |
| --- | --- | --- |
| Nazwa |Nazwa pamięci podręcznej | |
| Lokalizacja |Lokalizacja pamięci podręcznej | |
| ResourceGroupName |Nazwa grupy zasobów, w której ma zostać utworzona pamięć podręczna | |
| Rozmiar |Rozmiar pamięci podręcznej. Prawidłowe wartości to: P1, P2, P3, P4, C0, C1, C2, C3, C4, C5, C6, 250 MB, 1 GB, 2,5 GB, 6 GB, 13GB, 26GB, 53GB |PAMIĘCI |
| ShardCount |Liczba fragmentów do utworzenia podczas tworzenia pamięci podręcznej Premium z włączoną obsługą klastrowania. Prawidłowe wartości to: 1, 2, 3, 4, 5, 6, 7, 8, 9, 10 | |
| SKU |Określa jednostkę SKU pamięci podręcznej. Prawidłowe wartości to: Basic, standard, Premium |Standardowa |
| RedisConfiguration |Określa ustawienia konfiguracji Redis. Aby uzyskać szczegółowe informacje na temat każdego ustawienia, zobacz następującą tabelę [Właściwości RedisConfiguration](#redisconfiguration-properties) . | |
| EnableNonSslPort |Wskazuje, czy port bez obsługi protokołu SSL jest włączony. |Fałsz |
| MaxMemoryPolicy |Ten parametr jest przestarzały — zamiast tego należy użyć RedisConfiguration. | |
| StaticIP |W przypadku hostowania pamięci podręcznej w sieci wirtualnej określa unikatowy adres IP w podsieci dla pamięci podręcznej. Jeśli nie zostanie podany, jeden z nich zostanie wybrany dla Ciebie z podsieci. | |
| Podsieć |W przypadku hostowania pamięci podręcznej w sieci wirtualnej określa nazwę podsieci, w której ma zostać wdrożona pamięć podręczna. | |
| VirtualNetwork |W przypadku hostowania pamięci podręcznej w sieci wirtualnej określa identyfikator zasobu sieci wirtualnej, w którym ma zostać wdrożona pamięć podręczna. | |
| KeyType |Określa, który klucz dostępu ma zostać wygenerowany ponownie podczas odnawiania kluczy dostępu. Prawidłowe wartości to: podstawowa, pomocnicza | |

### <a name="redisconfiguration-properties"></a>Właściwości RedisConfiguration
| Właściwość | Opis | Warstwy cenowe |
| --- | --- | --- |
| RDB — włączono tworzenie kopii zapasowej |Czy jest włączona funkcja [trwałości danych Redis](cache-how-to-premium-persistence.md) |Tylko wersja Premium |
| RDB-Storage-Connection-String |Parametry połączenia z kontem magazynu w celu zapewnienia [trwałości danych Redis](cache-how-to-premium-persistence.md) |Tylko wersja Premium |
| RDB — częstotliwość tworzenia kopii zapasowych |Częstotliwość tworzenia kopii zapasowych dla [trwałości danych Redis](cache-how-to-premium-persistence.md) |Tylko wersja Premium |
| maxmemory — zarezerwowane |Konfiguruje [pamięć zarezerwowaną](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) dla procesów poza pamięcią podręczną |Standard i Premium |
| maxmemory — zasady |Konfiguruje [Zasady wykluczania](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) dla pamięci podręcznej |Wszystkie warstwy cenowe |
| Powiadamiaj-o miejscu — zdarzenia |Konfiguruje [powiadomienia o przestrzeni](cache-configure.md#keyspace-notifications-advanced-settings) kluczy |Standard i Premium |
| Hash-Max-ZipList-wpisów |Konfiguruje [optymalizację pamięci](https://redis.io/topics/memory-optimization) dla małych typów zagregowanych danych |Standard i Premium |
| Hash — Max-ZipList-Value |Konfiguruje [optymalizację pamięci](https://redis.io/topics/memory-optimization) dla małych typów zagregowanych danych |Standard i Premium |
| set-max-intset-wpisów |Konfiguruje [optymalizację pamięci](https://redis.io/topics/memory-optimization) dla małych typów zagregowanych danych |Standard i Premium |
| zset-Max-ZipList-wpisów |Konfiguruje [optymalizację pamięci](https://redis.io/topics/memory-optimization) dla małych typów zagregowanych danych |Standard i Premium |
| zset — Max-ZipList-Value |Konfiguruje [optymalizację pamięci](https://redis.io/topics/memory-optimization) dla małych typów zagregowanych danych |Standard i Premium |
| bazy danych |Konfiguruje liczbę baz danych. Tę właściwość można skonfigurować tylko podczas tworzenia pamięci podręcznej. |Standard i Premium |

## <a name="to-create-an-azure-cache-for-redis"></a>Aby utworzyć pamięć podręczną platformy Azure dla usługi Redis
Nowe wystąpienia usługi Azure cache dla wystąpień Redis są tworzone za pomocą polecenia cmdlet [New-AzRedisCache](/powershell/module/az.rediscache/new-azrediscache) .

> [!IMPORTANT]
> Przy pierwszym utworzeniu pamięci podręcznej platformy Azure dla usługi Redis w ramach subskrypcji przy użyciu Azure Portal Portal rejestruje `Microsoft.Cache` przestrzeń nazw dla tej subskrypcji. W przypadku próby utworzenia pierwszej pamięci podręcznej platformy Azure dla usługi Redis w ramach subskrypcji przy użyciu programu PowerShell należy najpierw zarejestrować tę przestrzeń nazw przy użyciu następującego polecenia: w przeciwnym razie polecenia cmdlet `New-AzRedisCache` , takie jak i, `Get-AzRedisCache` kończą się niepowodzeniem.
> 
> `Register-AzResourceProvider -ProviderNamespace "Microsoft.Cache"`
> 
> 

Aby wyświetlić listę dostępnych parametrów i ich opisów dla programu `New-AzRedisCache` , uruchom następujące polecenie.

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

Aby utworzyć pamięć podręczną z domyślnymi parametrami, uruchom następujące polecenie.

```azurepowershell
    New-AzRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US"
```

`ResourceGroupName`, `Name` , i `Location` są wymaganymi parametrami, ale REST jest opcjonalne i mają wartości domyślne. Uruchomienie poprzedniego polecenia powoduje utworzenie w pamięci podręcznej standardowej jednostki SKU platformy Azure dla wystąpienia Redis o określonej nazwie, lokalizacji i grupie zasobów o rozmiarze 1 GB z wyłączonym portem innym niż SSL.

Aby utworzyć pamięć podręczną Premium, określ rozmiar P1 (6 GB-60 GB), P2 (13 GB-130 GB), P3 (26 GB – 260 GB) lub P4 (53 GB-530 GB). Aby włączyć klastrowanie, określ liczbę fragmentu przy użyciu `ShardCount` parametru. Poniższy przykład tworzy pamięć podręczną P1 Premium z 3 fragmentów. Pamięć podręczna w warstwie Premium P1 ma rozmiar 6 GB, a ponieważ określono trzy fragmentów, całkowity rozmiar to 18 GB (3 x 6 GB).

```azurepowershell
    New-AzRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -Sku Premium -Size P1 -ShardCount 3
```

Aby określić wartości `RedisConfiguration` parametru, należy ująć wartości wewnątrz `{}` par klucz/wartość, takich jak `@{"maxmemory-policy" = "allkeys-random", "notify-keyspace-events" = "KEA"}` . Poniższy przykład tworzy pamięć podręczną o pojemności 1 GB z `allkeys-random` zasadami maxmemory i powiadomieniami o przestrzeni kluczy skonfigurowanymi przy użyciu `KEA` . Aby uzyskać więcej informacji, zobacz [powiadomienia o przestrzeni kluczy (Ustawienia zaawansowane)](cache-configure.md#keyspace-notifications-advanced-settings) i [zasady pamięci](cache-configure.md#memory-policies).

```azurepowershell
    New-AzRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -RedisConfiguration @{"maxmemory-policy" = "allkeys-random", "notify-keyspace-events" = "KEA"}
```

<a name="databases"></a>

## <a name="to-configure-the-databases-setting-during-cache-creation"></a>Aby skonfigurować ustawienia baz danych podczas tworzenia pamięci podręcznej
To `databases` ustawienie można skonfigurować tylko podczas tworzenia pamięci podręcznej. Poniższy przykład tworzy pamięć podręczną P3 (26 GB) z bazami danych 48 za pomocą polecenia cmdlet [New-AzRedisCache](/powershell/module/az.rediscache/New-azRedisCache) .

```azurepowershell
    New-AzRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -Sku Premium -Size P3 -RedisConfiguration @{"databases" = "48"}
```

Aby uzyskać więcej informacji na temat `databases` właściwości, zobacz [domyślna pamięć podręczna platformy Azure dla konfiguracji serwera Redis](cache-configure.md#default-redis-server-configuration). Aby uzyskać więcej informacji na temat tworzenia pamięci podręcznej przy użyciu polecenia cmdlet [New-AzRedisCache](/powershell/module/az.rediscache/new-azrediscache) , zobacz sekcję poprzednią w celu utworzenia pamięci podręcznej platformy Azure dla Redis.

## <a name="to-update-an-azure-cache-for-redis"></a>Aby zaktualizować pamięć podręczną platformy Azure dla usługi Redis
Usługa Azure cache for Redis Instances jest aktualizowana przy użyciu polecenia cmdlet [Set-AzRedisCache](/powershell/module/az.rediscache/Set-azRedisCache) .

Aby wyświetlić listę dostępnych parametrów i ich opisów dla programu `Set-AzRedisCache` , uruchom następujące polecenie.

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

`Set-AzRedisCache`Polecenie cmdlet może służyć do aktualizowania właściwości, takich jak `Size` , `Sku` , `EnableNonSslPort` i `RedisConfiguration` wartości. 

Następujące polecenie aktualizuje zasady maxmemory dla usługi Azure cache dla Redis o nazwie moja pamięć podręczna.

```azurepowershell
    Set-AzRedisCache -ResourceGroupName "myGroup" -Name "myCache" -RedisConfiguration @{"maxmemory-policy" = "allkeys-random"}
```

<a name="scale"></a>

## <a name="to-scale-an-azure-cache-for-redis"></a>Aby skalować pamięć podręczną platformy Azure dla usługi Redis
`Set-AzRedisCache` może służyć do skalowania pamięci podręcznej platformy Azure dla wystąpienia Redis `Size` , gdy `Sku` właściwości, lub `ShardCount` są modyfikowane. 

> [!NOTE]
> Skalowanie pamięci podręcznej przy użyciu programu PowerShell podlega tym samym limitom i wytycznym jak skalowanie pamięci podręcznej z Azure Portal. Możesz skalować do innej warstwy cenowej, korzystając z następujących ograniczeń.
> 
> * Nie można skalować z wyższej warstwy cenowej do niższej warstwy cenowej.
> * Nie można skalować z pamięci podręcznej **Premium** do warstwy **standardowa** lub **podstawowa** .
> * Nie można skalować z poziomu **standardowej** pamięci podręcznej do **podstawowej** pamięci podręcznej.
> * Możesz skalować od **podstawowej** pamięci podręcznej do **standardowej** pamięci podręcznej, ale nie możesz zmienić rozmiaru w tym samym czasie. Jeśli potrzebujesz innego rozmiaru, możesz wykonać kolejną operację skalowania do żądanego rozmiaru.
> * Nie można skalować z **podstawowej** pamięci podręcznej bezpośrednio do pamięci podręcznej **Premium** . Należy skalować od warstwy **podstawowa** do **standardowa** w ramach jednej operacji skalowania, a następnie z warstwy **standardowa** do **Premium** w kolejnej operacji skalowania.
> * Nie można skalować od większego rozmiaru w dół do rozmiaru **C0 (250 MB)** .
> 
> Aby uzyskać więcej informacji, zobacz [Jak skalować pamięć podręczną platformy Azure dla Redis](cache-how-to-scale.md).
> 
> 

Poniższy przykład przedstawia sposób skalowania pamięci podręcznej o nazwie `myCache` do pamięci podręcznej 2,5 GB. Należy zauważyć, że to polecenie działa w przypadku podstawowej lub standardowej pamięci podręcznej.

```azurepowershell
    Set-AzRedisCache -ResourceGroupName myGroup -Name myCache -Size 2.5GB
```

Po wydaniu tego polecenia jest zwracany stan pamięci podręcznej (podobnie jak w przypadku wywoływania `Get-AzRedisCache` ). Należy pamiętać, że `ProvisioningState` jest to `Scaling` .

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

Po zakończeniu operacji skalowania `ProvisioningState` zmiany są wprowadzane do `Succeeded` . Jeśli trzeba wykonać kolejną operację skalowania, taką jak zmiana z warstwy Podstawowa na standardowa, a następnie zmiana rozmiaru, należy poczekać na ukończenie poprzedniej operacji lub pojawić się błąd podobny do poniższego.

```azurepowershell
    Set-AzRedisCache : Conflict: The resource '...' is not in a stable state, and is currently unable to accept the update request.
```

## <a name="to-get-information-about-an-azure-cache-for-redis"></a>Aby uzyskać informacje na temat pamięci podręcznej platformy Azure dla usługi Redis
Informacje o pamięci podręcznej można pobrać przy użyciu polecenia cmdlet [Get-AzRedisCache](/powershell/module/az.rediscache/get-azrediscache) .

Aby wyświetlić listę dostępnych parametrów i ich opisów dla programu `Get-AzRedisCache` , uruchom następujące polecenie.

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

Aby zwrócić informacje o wszystkich pamięciach podręcznych w bieżącej subskrypcji, uruchom polecenie `Get-AzRedisCache` bez żadnych parametrów.

```azurepowershell
    Get-AzRedisCache
```

Aby zwrócić informacje o wszystkich pamięciach podręcznych w określonej grupie zasobów, uruchom polecenie `Get-AzRedisCache` z `ResourceGroupName` parametrem.

```azurepowershell
    Get-AzRedisCache -ResourceGroupName myGroup
```

Aby zwrócić informacje o określonej pamięci podręcznej, należy uruchomić polecenie `Get-AzRedisCache` z `Name` parametrem zawierającym nazwę pamięci podręcznej, a `ResourceGroupName` parametr z grupą zasobów zawierającą tę pamięć podręczną.

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

## <a name="to-retrieve-the-access-keys-for-an-azure-cache-for-redis"></a>Aby pobrać klucze dostępu dla pamięci podręcznej platformy Azure dla usługi Redis
Aby pobrać klucze dostępu do pamięci podręcznej, można użyć polecenia cmdlet [Get-AzRedisCacheKey](/powershell/module/az.rediscache/Get-azRedisCacheKey) .

Aby wyświetlić listę dostępnych parametrów i ich opisów dla programu `Get-AzRedisCacheKey` , uruchom następujące polecenie.

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

Aby pobrać klucze pamięci podręcznej, wywołaj `Get-AzRedisCacheKey` polecenie cmdlet i przekaż nazwę pamięci podręcznej nazwę grupy zasobów zawierającej pamięć podręczną.

```azurepowershell
    PS C:\> Get-AzRedisCacheKey -Name myCache -ResourceGroupName myGroup

    PrimaryKey   : b2wdt43sfetlju4hfbryfnregrd9wgIcc6IA3zAO1lY=
    SecondaryKey : ABhfB757JgjIgt785JgKH9865eifmekfnn649303JKL=
```

## <a name="to-regenerate-access-keys-for-your-azure-cache-for-redis"></a>Aby wygenerować ponownie klucze dostępu dla swojej pamięci podręcznej platformy Azure dla usługi Redis
Aby ponownie wygenerować klucze dostępu do pamięci podręcznej, można użyć polecenia cmdlet [New-AzRedisCacheKey](/powershell/module/az.rediscache/New-azRedisCacheKey) .

Aby wyświetlić listę dostępnych parametrów i ich opisów dla programu `New-AzRedisCacheKey` , uruchom następujące polecenie.

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

Aby ponownie wygenerować klucz podstawowy lub pomocniczy dla pamięci podręcznej, wywołaj `New-AzRedisCacheKey` polecenie cmdlet i przekaż nazwę, grupę zasobów, a następnie określ `Primary` lub `Secondary` dla `KeyType` parametru. W poniższym przykładzie zostanie ponownie wygenerowany pomocniczy klucz dostępu dla pamięci podręcznej.

```azurepowershell
    PS C:\> New-AzRedisCacheKey -Name myCache -ResourceGroupName myGroup -KeyType Secondary

    Confirm
    Are you sure you want to regenerate Secondary key for Azure Cache for Redis 'myCache'?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y


    PrimaryKey   : b2wdt43sfetlju4hfbryfnregrd9wgIcc6IA3zAO1lY=
    SecondaryKey : c53hj3kh4jhHjPJk8l0jji785JgKH9865eifmekfnn6=
```

## <a name="to-delete-an-azure-cache-for-redis"></a>Aby usunąć pamięć podręczną platformy Azure dla usługi Redis
Aby usunąć pamięć podręczną platformy Azure dla usługi Redis, użyj polecenia cmdlet [Remove-AzRedisCache](/powershell/module/az.rediscache/remove-azrediscache) .

Aby wyświetlić listę dostępnych parametrów i ich opisów dla programu `Remove-AzRedisCache` , uruchom następujące polecenie.

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

W poniższym przykładzie nazwa pamięci podręcznej `myCache` jest usuwana.

```azurepowershell
    PS C:\> Remove-AzRedisCache -Name myCache -ResourceGroupName myGroup

    Confirm
    Are you sure you want to remove Azure Cache for Redis 'myCache'?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y
```


## <a name="to-import-an-azure-cache-for-redis"></a>Aby zaimportować pamięć podręczną platformy Azure dla usługi Redis
Dane można zaimportować do wystąpienia usługi Azure cache for Redis przy użyciu `Import-AzRedisCache` polecenia cmdlet.

> [!IMPORTANT]
> Usługa Import/Export jest dostępna tylko w przypadku pamięci podręcznych [warstwy Premium](cache-overview.md#service-tiers) . Aby uzyskać więcej informacji na temat importowania/eksportowania, zobacz [Importowanie i eksportowanie danych w usłudze Azure cache for Redis](cache-how-to-import-export-data.md).
> 
> 

Aby wyświetlić listę dostępnych parametrów i ich opisów dla programu `Import-AzRedisCache` , uruchom następujące polecenie.

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


Następujące polecenie importuje dane z obiektu blob określonego przez identyfikator URI sygnatury dostępu współdzielonego do usługi Azure cache for Redis.

```azurepowershell
    PS C:\>Import-AzRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -Files @("https://mystorageaccount.blob.core.windows.net/mycontainername/blobname?sv=2015-04-05&sr=b&sig=caIwutG2uDa0NZ8mjdNJdgOY8%2F8mhwRuGNdICU%2B0pI4%3D&st=2016-05-27T00%3A00%3A00Z&se=2016-05-28T00%3A00%3A00Z&sp=rwd") -Force
```

## <a name="to-export-an-azure-cache-for-redis"></a>Aby wyeksportować pamięć podręczną platformy Azure dla usługi Redis
Dane można eksportować z wystąpienia usługi Azure cache for Redis przy użyciu `Export-AzRedisCache` polecenia cmdlet.

> [!IMPORTANT]
> Usługa Import/Export jest dostępna tylko w przypadku pamięci podręcznych [warstwy Premium](cache-overview.md#service-tiers) . Aby uzyskać więcej informacji na temat importowania/eksportowania, zobacz [Importowanie i eksportowanie danych w usłudze Azure cache for Redis](cache-how-to-import-export-data.md).
> 
> 

Aby wyświetlić listę dostępnych parametrów i ich opisów dla programu `Export-AzRedisCache` , uruchom następujące polecenie.

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


Następujące polecenie eksportuje dane z wystąpienia usługi Azure cache for Redis do kontenera określonego przez identyfikator URI sygnatury dostępu współdzielonego.

```azurepowershell
    PS C:\>Export-AzRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -Prefix "blobprefix"
    -Container "https://mystorageaccount.blob.core.windows.net/mycontainer?sv=2015-04-05&sr=c&sig=HezZtBZ3DURmEGDduauE7
    pvETY4kqlPI8JCNa8ATmaw%3D&st=2016-05-27T00%3A00%3A00Z&se=2016-05-28T00%3A00%3A00Z&sp=rwdl"
```

## <a name="to-reboot-an-azure-cache-for-redis"></a>Aby ponownie uruchomić pamięć podręczną platformy Azure dla usługi Redis
Można uruchomić ponownie wystąpienie usługi Azure cache for Redis przy użyciu `Reset-AzRedisCache` polecenia cmdlet.

> [!IMPORTANT]
> Ponowny rozruch jest dostępny tylko w przypadku pamięci podręcznych [warstwy Premium](cache-overview.md#service-tiers) . Aby uzyskać więcej informacji na temat ponownego uruchamiania pamięci podręcznej, zobacz temat [Administrowanie pamięcią podręczną](cache-administration.md#reboot).
> 
> 

Aby wyświetlić listę dostępnych parametrów i ich opisów dla programu `Reset-AzRedisCache` , uruchom następujące polecenie.

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


Poniższe polecenie ponownie wykonuje rozruch obu węzłów określonej pamięci podręcznej.

```azurepowershell
    PS C:\>Reset-AzRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -RebootType "AllNodes"
    -Force
```


## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej o korzystaniu z programu Windows PowerShell z platformą Azure, zobacz następujące zasoby:

* [Dokumentacja poleceń cmdlet usługi Azure cache for Redis w witrynie MSDN](/powershell/module/az.rediscache)
* [Polecenia cmdlet Azure Resource Manager](/powershell/module/): informacje na temat używania poleceń cmdlet w module Azure Resource Manager.
* [Używanie grup zasobów do zarządzania zasobami platformy Azure](../azure-resource-manager/templates/deploy-portal.md): Dowiedz się, jak tworzyć grupy zasobów i zarządzać nimi w Azure Portal.
* [Blog platformy Azure](https://azure.microsoft.com/blog/): Poznaj nowe funkcje platformy Azure.
* [Blog programu Windows PowerShell](https://devblogs.microsoft.com/powershell/): informacje o nowych funkcjach w programie Windows PowerShell.
* ["Hej, obsługa skryptów Guy!" Blog](https://devblogs.microsoft.com/scripting/tag/hey-scripting-guy/): Uzyskaj rzeczywiste porady i wskazówki ze społeczności programu Windows PowerShell.