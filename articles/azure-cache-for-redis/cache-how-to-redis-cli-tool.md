---
title: Używanie interfejsu wiersza polecenia redis z interfejsem Azure Cache for Redis
description: Dowiedz się, *jak używaćredis-cli.exe* jako narzędzia wiersza polecenia do interakcji z Azure Cache for Redis jako klientem
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.openlocfilehash: 042db51ab51e9da586f028b8aa99e66ffaa36639
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775677"
---
# <a name="use-the-redis-command-line-tool-with-azure-cache-for-redis"></a>Używanie narzędzia wiersza polecenia Redis z Azure Cache for Redis

*redis-cli.exe* to popularne narzędzie wiersza polecenia do interakcji z Azure Cache for Redis jako klientem. To narzędzie jest również dostępne do użycia z Azure Cache for Redis.

Narzędzie jest dostępne dla platform Windows, pobierając narzędzia wiersza polecenia [redis dla systemu Windows.](https://github.com/MSOpenTech/redis/releases/) 

Jeśli chcesz uruchomić narzędzie wiersza polecenia na innej platformie, pobierz plik Azure Cache for Redis [https://redis.io/download](https://redis.io/download) .

## <a name="gather-cache-access-information"></a>Zbieranie informacji o dostępie do pamięci podręcznej

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Informacje wymagane do uzyskania dostępu do pamięci podręcznej można zebrać przy użyciu trzech metod:

1. Interfejs wiersza polecenia platformy Azure przy [użyciu polecenia az redis list-keys](/cli/azure/redis#az_redis_list_keys)
2. Azure PowerShell użyciu [get-AzRedisCacheKey](/powershell/module/az.rediscache/Get-AzRedisCacheKey)
3. Za pomocą witryny Azure Portal.

W tej sekcji pobierzemy klucze z Azure Portal.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]


## <a name="enable-access-for-redis-cliexe"></a>Włączanie dostępu dla redis-cli.exe

W Azure Cache for Redis domyślnie włączony jest tylko port TLS (6380). Narzędzie `redis-cli.exe` wiersza polecenia nie obsługuje obsługi TLS. Dostępne są dwie opcje konfiguracji:

1. [Włączanie portu bez obsługi TLS (6379)](cache-configure.md#access-ports)  -  **Ta konfiguracja nie jest zalecana,** ponieważ w tej konfiguracji klucze dostępu są wysyłane za pośrednictwem protokołu TCP w czystym tekście. Ta zmiana może naruszyć dostęp do pamięci podręcznej. Jedyną sytuacją, w której można rozważyć tę konfigurację, jest uzyskanie dostępu do testowej pamięci podręcznej.

2. Pobierz i zainstaluj [program stunnel.](https://www.stunnel.org/downloads.html)

    Uruchom graficzny interfejs użytkownika usługi **Stunnel Uruchom,** aby uruchomić serwer.

    Kliknij prawym przyciskiem myszy ikonę paska zadań dla serwera stunnel, a następnie kliknij polecenie **Pokaż okno dziennika.**

    W menu okna dziennika usługi Stunnel kliknij pozycję **Konfiguracja**  >  **Edytuj konfigurację,** aby otworzyć bieżący plik konfiguracji.

    Dodaj następujący wpis dla *redis-cli.exe* w sekcji **Definicje** usług. Wstaw rzeczywistą nazwę pamięci podręcznej w `yourcachename` miejsce . 

    ```
    [redis-cli]
    client = yes
    accept = 127.0.0.1:6380
    connect = yourcachename.redis.cache.windows.net:6380
    ```

    Zapisz i zamknij plik konfiguracji. 
  
    W menu okna dziennika usługi Stunnel kliknij pozycję **Konfiguracja Załaduj**  >  **ponownie konfigurację.**


## <a name="connect-using-the-redis-command-line-tool"></a>Połącz się przy użyciu narzędzia wiersza polecenia Redis.

W przypadku korzystania z usługi Stunnel uruchomredis-cli.exe *,* a następnie przekaż tylko *port* i klucz dostępu *(podstawowy* lub pomocniczy), aby nawiązać połączenie z pamięcią podręczną.

```
redis-cli.exe -p 6380 -a YourAccessKey
```

![Zrzut ekranu przedstawiający pomyślne połączenie z pamięcią podręczną.](media/cache-how-to-redis-cli-tool/cache-redis-cli-stunnel.png)

Jeśli używasz testowej pamięci podręcznej z niezabezpieczoną portem innym niż TLS, uruchom i przekaż nazwę hosta,  port i klucz dostępu (podstawowy lub pomocniczy), aby nawiązać połączenie z testową pamięcią `redis-cli.exe` podręczną.   

```
redis-cli.exe -h yourcachename.redis.cache.windows.net -p 6379 -a YourAccessKey
```

![stunnel z interfejsem wiersza polecenia redis](media/cache-how-to-redis-cli-tool/cache-redis-cli-non-ssl.png)




## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o używaniu [konsoli Redis do](cache-configure.md#redis-console) wydawania poleceń.