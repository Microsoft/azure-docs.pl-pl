---
title: Zarządzanie usługą Azure cache for Redis przy użyciu klasycznego interfejsu wiersza polecenia platformy Azure
description: Dowiedz się, jak zainstalować klasyczny interfejs wiersza polecenia platformy Azure na dowolnej platformie, jak używać go do nawiązywania połączenia z kontem platformy Azure oraz jak tworzyć pamięć podręczną platformy Azure dla Redis z poziomu klasycznego interfejsu wiersza polecenia i zarządzać nią.
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 01/23/2017
ms.author: yegu
ms.custom: devx-track-azurecli
ms.openlocfilehash: db55ac4ce868f2e6ce2afbfbf014aac67653ce4a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87500508"
---
# <a name="how-to-create-and-manage-azure-cache-for-redis-using-the-azure-classic-cli"></a>Jak utworzyć usługę Azure cache for Redis i zarządzać nią przy użyciu klasycznego interfejsu wiersza polecenia platformy Azure
> [!div class="op_single_selector"]
> * [Program PowerShell](cache-how-to-manage-redis-cache-powershell.md)
> * [Klasyczny interfejs wiersza polecenia platformy Azure](cache-manage-cli.md)
>

Klasyczny interfejs wiersza polecenia platformy Azure to doskonały sposób na zarządzanie infrastrukturą platformy Azure z dowolnej platformy. W tym artykule opisano sposób tworzenia i zarządzania pamięcią podręczną platformy Azure dla wystąpień Redis przy użyciu klasycznego interfejsu wiersza polecenia platformy Azure.

[!INCLUDE [outdated-cli-content](../../includes/contains-classic-cli-content.md)]
> [!NOTE]
> Najnowsze przykładowe skrypty interfejsu wiersza polecenia platformy Azure można znaleźć w [pamięci podręcznej interfejsu wiersza polecenia platformy Azure dla przykładów Redis](cli-samples.md).

## <a name="prerequisites"></a>Wymagania wstępne
Aby tworzyć wystąpienia usługi Azure cache for Redis i zarządzać nimi przy użyciu klasycznego interfejsu wiersza polecenia platformy Azure, należy wykonać następujące czynności.

* Musisz mieć konto platformy Azure. Jeśli go nie masz, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/) w zaledwie kilka minut.
* [Zainstaluj klasyczny interfejs wiersza polecenia platformy Azure](../cli-install-nodejs.md).
* Podłącz instalację interfejsu wiersza polecenia platformy Azure przy użyciu osobistego konta platformy Azure lub konta platformy Azure w ramach pracy lub szkoły, a następnie zaloguj się przy użyciu klasycznego interfejsu wiersza polecenia za pomocą `azure login` poleceń.
* Przed uruchomieniem dowolnego z poniższych poleceń Przełącz klasyczny interfejs wiersza polecenia do trybu Menedżer zasobów, uruchamiając `azure config mode arm` polecenie. Aby uzyskać więcej informacji, zobacz [Korzystanie z klasycznego interfejsu wiersza polecenia platformy Azure do zarządzania zasobami i grupami zasobów platformy Azure](../xplat-cli-azure-resource-manager.md).

## <a name="azure-cache-for-redis-properties"></a>Właściwości usługi Azure Cache for Redis
Następujące właściwości są używane podczas tworzenia i aktualizowania usługi Azure cache for Redis Instances.

| Właściwość | Przełącznik | Opis |
| --- | --- | --- |
| name |-n,--Name |Nazwa pamięci podręcznej platformy Azure dla Redis. |
| grupa zasobów |-g,--Resource-Group |Nazwa grupy zasobów. |
| location |-l,--lokalizacja |Lokalizacja do utworzenia pamięci podręcznej. |
| size |-z,--size |Rozmiar pamięci podręcznej platformy Azure dla Redis. Prawidłowe wartości: [C0, C1, C2, C3, C4, C5, C6, P1, P2, P3, P4] |
| sku |-x,--SKU |Jednostka SKU Redis. Powinien być jednym z: [podstawowa, standardowa, Premium] |
| EnableNonSslPort |-e,--Enable-port bez protokołu SSL |Właściwość EnableNonSslPort pamięci podręcznej platformy Azure dla Redis. Dodaj tę flagę, jeśli chcesz włączyć port inny niż TLS/SSL dla pamięci podręcznej |
| Konfiguracja Redis |-c,--Redis-Configuration |Konfiguracja Redis. W tym miejscu wprowadź ciąg w formacie JSON. Format: "{" ":" "," ":" "}" |
| Konfiguracja Redis |-f,--Redis-Configuration-File |Konfiguracja Redis. Wprowadź tutaj ścieżkę pliku zawierającego klucze i wartości konfiguracji. Format wpisu pliku: {"": "", "": ""} |
| Liczba fragmentu |-r,--fragmentu-Count |Liczba fragmentów do utworzenia w pamięci podręcznej klastrów w warstwie Premium z klastrowaniem. |
| Virtual Network |-v,--Virtual-Network |W przypadku hostowania pamięci podręcznej w sieci wirtualnej określa dokładnego identyfikatora zasobu usługi ARM do wdrożenia pamięci podręcznej platformy Azure dla Redis w systemie. Przykładowy format:/subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1 |
| Typ klucza |-t,--typ klucza |Typ klucza do odnowienia. Prawidłowe wartości: [podstawowa, pomocnicza] |
| StaticIP |-p,--Static-IP \<static-ip\> |W przypadku hostowania pamięci podręcznej w sieci wirtualnej określa unikatowy adres IP w podsieci dla pamięci podręcznej. Jeśli nie zostanie podany, jeden z nich zostanie wybrany dla Ciebie z podsieci. |
| Podsieć |t,--Subnet \<subnet\> |W przypadku hostowania pamięci podręcznej w sieci wirtualnej określa nazwę podsieci, w której ma zostać wdrożona pamięć podręczna. |
| VirtualNetwork |-v,--Virtual-Network \<virtual-network\> |W przypadku hostowania pamięci podręcznej w sieci wirtualnej określa dokładnego identyfikatora zasobu usługi ARM do wdrożenia pamięci podręcznej platformy Azure dla Redis w systemie. Przykładowy format:/subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1 |
| Subskrypcja |-s,--subskrypcja |Identyfikator subskrypcji. |

## <a name="see-all-azure-cache-for-redis-commands"></a>Zobacz wszystkie polecenia usługi Azure cache for Redis
Aby wyświetlić wszystkie polecenia usługi Azure cache for Redis i ich parametry, użyj `azure rediscache -h` polecenia.

```azurecli
C:\>azure rediscache -h
help:    Commands to manage your Azure Cache for Redis(s)
help:
help:    Create an Azure Cache for Redis
help:      rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]
help:
help:    Delete an existing Azure Cache for Redis
help:      rediscache delete [--name <name> --resource-group <resource-group> ]
help:
help:    List all Azure Cache for Redis within your Subscription or Resource Group
help:      rediscache list [options]
help:
help:    Show properties of an existing Azure Cache for Redis
help:      rediscache show [--name <name> --resource-group <resource-group>]
help:
help:    Change settings of an existing Azure Cache for Redis
help:      rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]
help:
help:    Renew the authentication key for an existing Azure Cache for Redis
help:      rediscache renew-key [--name <name> --resource-group <resource-group> ]
help:
help:    Lists Primary and Secondary key of an existing Azure Cache for Redis
help:      rediscache list-keys [--name <name> --resource-group <resource-group>]
help:
help:    Options:
help:      -h, --help  output usage information
help:
help:    Current Mode: arm (Azure Resource Management)
```

## <a name="create-an-azure-cache-for-redis"></a>Tworzenie usługi Azure Cache for Redis
Aby utworzyć pamięć podręczną platformy Azure dla usługi Redis, użyj następującego polecenia:

```azurecli
    azure rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]
```

Aby uzyskać więcej informacji na temat tego polecenia, uruchom `azure rediscache create -h` polecenie.

```azurecli
C:\>azure rediscache create -h
help:    Create an Azure Cache for Redis
help:
help:    Usage: rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]
help:
help:    Options:
help:      -h, --help                                               output usage information
help:      -v, --verbose                                            use verbose output
help:      -vv                                                      more verbose with debug output
help:      --json                                                   use json output
help:      -n, --name <name>                                        Name of the Azure Cache for Redis.
help:      -g, --resource-group <resource-group>                    Name of the Resource Group
help:      -l, --location <location>                                Location to create cache.
help:      -z, --size <size>                                        Size of the Azure Cache for Redis. Valid values: [C0, C1, C2, C3, C4, C5, C6, P1, P2, P3, P4]
help:      -x, --sku <sku>                                          Redis SKU. Should be one of : [Basic, Standard, Premium]
help:      -e, --enable-non-ssl-port                                EnableNonSslPort property of the Azure Cache for Redis. Add this flag if you want to enable the non-TLS/SSL Port for your cache
help:      -c, --redis-configuration <redis-configuration>          Redis Configuration. Enter a JSON formatted string of configuration keys and values here. Format:"{"<key1>":"<value1>","<key2>":"<value2>"}"
help:      -f, --redis-configuration-file <redisConfigurationFile>  Redis Configuration. Enter the path of a file containing configuration keys and values here. Format for the file entry: {"<key1>":"<value1>","<key2>":"<value2>"}
help:      -r, --shard-count <shard-count>                          Number of Shards to create on a Premium Cluster Cache
help:      -v, --virtual-network <virtual-network>                  The exact ARM resource ID of the virtual network to deploy the Azure Cache for Redis in. Example format: /subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1
help:      -t, --subnet <subnet>                                    Required when deploying an Azure Cache for Redis inside an existing Azure Virtual Network
help:      -p, --static-ip <static-ip>                              Required when deploying an Azure Cache for Redis inside an existing Azure Virtual Network
help:      -s, --subscription <id>                                  the subscription identifier
help:
help:    Current Mode: arm (Azure Resource Management)
```

## <a name="delete-an-existing-azure-cache-for-redis"></a>Usuwanie istniejącej pamięci podręcznej platformy Azure dla usługi Redis
Aby usunąć pamięć podręczną platformy Azure dla usługi Redis, użyj następującego polecenia:

```azurecli
    azure rediscache delete [--name <name> --resource-group <resource-group> ]
```

Aby uzyskać więcej informacji na temat tego polecenia, uruchom `azure rediscache delete -h` polecenie.

```azurecli
C:\>azure rediscache delete -h
help:    Delete an existing Azure Cache for Redis
help:
help:    Usage: rediscache delete [--name <name> --resource-group <resource-group> ]
help:
help:    Options:
help:      -h, --help                             output usage information
help:      -v, --verbose                          use verbose output
help:      -vv                                    more verbose with debug output
help:      --json                                 use json output
help:      -n, --name <name>                      Name of the Azure Cache for Redis.
help:      -g, --resource-group <resource-group>  Name of the Resource Group under which the cache exists
help:      -s, --subscription <subscription>      the subscription identifier
help:
help:    Current Mode: arm (Azure Resource Management)
```

## <a name="list-all-azure-cache-for-redis-within-your-subscription-or-resource-group"></a>Wyświetl listę wszystkich pamięci podręcznej platformy Azure dla usługi Redis w ramach subskrypcji lub grupy zasobów
Aby wyświetlić listę wszystkich usług Azure cache for Redis w ramach subskrypcji lub grupy zasobów, użyj następującego polecenia:

```azurecli
    azure rediscache list [options]
```

Aby uzyskać więcej informacji na temat tego polecenia, uruchom `azure rediscache list -h` polecenie.

```azurecli
C:\>azure rediscache list -h
help:    List all Azure Cache for Redis within your Subscription or Resource Group
help:
help:    Usage: rediscache list [options]
help:
help:    Options:
help:      -h, --help                             output usage information
help:      -v, --verbose                          use verbose output
help:      -vv                                    more verbose with debug output
help:      --json                                 use json output
help:      -g, --resource-group <resource-group>  Name of the Resource Group
help:      -s, --subscription <subscription>      the subscription identifier
help:
help:    Current Mode: arm (Azure Resource Management)
```

## <a name="show-properties-of-an-existing-azure-cache-for-redis"></a>Pokaż właściwości istniejącej pamięci podręcznej platformy Azure dla Redis
Aby wyświetlić właściwości istniejącej pamięci podręcznej platformy Azure dla usługi Redis, użyj następującego polecenia:

```azurecli
    azure rediscache show [--name <name> --resource-group <resource-group>]
```

Aby uzyskać więcej informacji na temat tego polecenia, uruchom `azure rediscache show -h` polecenie.

```azurecli
C:\>azure rediscache show -h
help:    Show properties of an existing Azure Cache for Redis
help:
help:    Usage: rediscache show [--name <name> --resource-group <resource-group>]
help:
help:    Options:
help:      -h, --help                             output usage information
help:      -v, --verbose                          use verbose output
help:      -vv                                    more verbose with debug output
help:      --json                                 use json output
help:      -n, --name <name>                      Name of the Azure Cache for Redis.
help:      -g, --resource-group <resource-group>  Name of the Resource Group
help:      -s, --subscription <subscription>      the subscription identifier
help:
help:    Current Mode: arm (Azure Resource Management)
```

<a name="scale"></a>

## <a name="change-settings-of-an-existing-azure-cache-for-redis"></a>Zmień ustawienia istniejącej pamięci podręcznej platformy Azure dla usługi Redis
Aby zmienić ustawienia istniejącej pamięci podręcznej platformy Azure dla usługi Redis, użyj następującego polecenia:

```azurecli
    azure rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]
```

Aby uzyskać więcej informacji na temat tego polecenia, uruchom `azure rediscache set -h` polecenie.

```azurecli
C:\>azure rediscache set -h
help:    Change settings of an existing Azure Cache for Redis
help:
help:    Usage: rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]
help:
help:    Options:
help:      -h, --help                                               output usage information
help:      -v, --verbose                                            use verbose output
help:      -vv                                                      more verbose with debug output
help:      --json                                                   use json output
help:      -n, --name <name>                                        Name of the Azure Cache for Redis.
help:      -g, --resource-group <resource-group>                    Name of the Resource Group
help:      -c, --redis-configuration <redis-configuration>          Redis Configuration. Enter a JSON formatted string of configuration keys and values here.
help:      -f, --redis-configuration-file <redisConfigurationFile>  Redis Configuration. Enter the path of a file containing configuration keys and values here.
help:      -s, --subscription <subscription>                        the subscription identifier
help:
help:    Current Mode: arm (Azure Resource Management)
```

## <a name="renew-the-authentication-key-for-an-existing-azure-cache-for-redis"></a>Odnów klucz uwierzytelniania istniejącej pamięci podręcznej platformy Azure dla usługi Redis
Aby odnowić klucz uwierzytelniania istniejącej pamięci podręcznej platformy Azure dla usługi Redis, użyj następującego polecenia:

```azurecli
    azure rediscache renew-key [--name <name> --resource-group <resource-group> --key-type <key-type>]
```

Określ `Primary` lub `Secondary` dla `key-type` .

Aby uzyskać więcej informacji na temat tego polecenia, uruchom `azure rediscache renew-key -h` polecenie.

```azurecli
C:\>azure rediscache renew-key -h
help:    Renew the authentication key for an existing Azure Cache for Redis
help:
help:    Usage: rediscache renew-key [--name <name> --resource-group <resource-group> ]
help:
help:    Options:
help:      -h, --help                             output usage information
help:      -v, --verbose                          use verbose output
help:      -vv                                    more verbose with debug output
help:      --json                                 use json output
help:      -n, --name <name>                      Name of the Azure Cache for Redis.
help:      -g, --resource-group <resource-group>  Name of the Resource Group under which cache exists
help:      -t, --key-type <key-type>              type of key to renew. Valid values are: 'Primary', 'Secondary'.
help:      -s, --subscription <subscription>      the subscription identifier
help:
help:    Current Mode: arm (Azure Resource Management)
```

## <a name="list-primary-and-secondary-keys-of-an-existing-azure-cache-for-redis"></a>Utwórz listę kluczy podstawowych i pomocniczych istniejącej pamięci podręcznej platformy Azure dla usługi Redis
Aby wyświetlić listę podstawowych i pomocniczych kluczy istniejącej pamięci podręcznej platformy Azure dla usługi Redis, użyj następującego polecenia:

```azurecli
    azure rediscache list-keys [--name <name> --resource-group <resource-group>]
```

Aby uzyskać więcej informacji na temat tego polecenia, uruchom `azure rediscache list-keys -h` polecenie.

```azurecli
C:\>azure rediscache list-keys -h
help:    Lists Primary and Secondary key of an existing Azure Cache for Redis
help:
help:    Usage: rediscache list-keys [--name <name> --resource-group <resource-group>]
help:
help:    Options:
help:      -h, --help                             output usage information
help:      -v, --verbose                          use verbose output
help:      -vv                                    more verbose with debug output
help:      --json                                 use json output
help:      -n, --name <name>                      Name of the Azure Cache for Redis.
help:      -g, --resource-group <resource-group>  Name of the Resource Group under which Cache exists
help:      -s, --subscription <subscription>      the subscription identifier
help:
help:    Current Mode: arm (Azure Resource Management)
```
