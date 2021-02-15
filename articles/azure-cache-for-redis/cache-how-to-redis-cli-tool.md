---
title: Korzystanie z interfejsu wiersza polecenia Redis z usługą Azure cache dla Redis
description: Dowiedz się, jak używać *redis-cli.exe* jako narzędzia wiersza polecenia do współdziałania z pamięcią podręczną platformy Azure dla Redis jako klient
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.openlocfilehash: 3a1e1cdb7706cb284da57e10e35afe3420deaf75
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100375310"
---
# <a name="use-the-redis-command-line-tool-with-azure-cache-for-redis"></a>Korzystanie z narzędzia wiersza polecenia Redis z usługą Azure cache for Redis

*redis-cli.exe* to popularne narzędzie wiersza polecenia służące do współdziałania z usługą Azure cache for Redis jako klient. To narzędzie jest również dostępne do użycia z usługą Azure cache for Redis.

Narzędzie jest dostępne dla platform systemu Windows przez pobranie [Redis narzędzi wiersza polecenia dla systemu Windows](https://github.com/MSOpenTech/redis/releases/). 

Jeśli chcesz uruchomić narzędzie wiersza polecenia na innej platformie, Pobierz pamięć podręczną platformy Azure dla Redis z programu [https://redis.io/download](https://redis.io/download) .

## <a name="gather-cache-access-information"></a>Zbierz informacje o dostępie do pamięci podręcznej

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Informacje potrzebne do uzyskania dostępu do pamięci podręcznej można zebrać przy użyciu trzech metod:

1. Interfejs wiersza polecenia platformy Azure korzystający z polecenia [AZ Redis list-Keys](/cli/azure/redis?view=azure-cli-latest#az-redis-list-keys)
2. Azure PowerShell przy użyciu polecenia [Get-AzRedisCacheKey](/powershell/module/az.rediscache/Get-AzRedisCacheKey)
3. Za pomocą witryny Azure Portal.

W tej sekcji zostaną pobrane klucze z Azure Portal.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]


## <a name="enable-access-for-redis-cliexe"></a>Włącz dostęp dla redis-cli.exe

W przypadku usługi Azure cache for Redis tylko port TLS (6380) jest domyślnie włączony. `redis-cli.exe`Narzędzie wiersza polecenia nie obsługuje protokołu TLS. Dostępne są dwie opcje konfiguracji:

1. [Włącz port inny niż TLS (6379)](cache-configure.md#access-ports)  -  **Ta konfiguracja nie jest zalecana** , ponieważ w tej konfiguracji klucze dostępu są wysyłane za pośrednictwem protokołu TCP w postaci zwykłego tekstu. Ta zmiana może naruszyć dostęp do pamięci podręcznej. Jedynym scenariuszem, w którym można wziąć pod uwagę tę konfigurację, jest uzyskanie dostępu do pamięci podręcznej testów.

2. Pobierz i zainstaluj [stunnel](https://www.stunnel.org/downloads.html).

    Uruchom **interfejs GUI stunnel** , aby uruchomić serwer.

    Kliknij prawym przyciskiem myszy ikonę paska zadań dla serwera stunnel, a następnie kliknij polecenie **Pokaż okno Dziennik**.

    W menu stunnel log, kliknij kolejno opcje **Konfiguracja**  >  **Edytuj konfigurację** , aby otworzyć bieżący plik konfiguracji.

    Dodaj następujący wpis dla *redis-cli.exe* w sekcji **definicje usług** . Wstaw rzeczywistą nazwę pamięci podręcznej zamiast `yourcachename` . 

    ```
    [redis-cli]
    client = yes
    accept = 127.0.0.1:6380
    connect = yourcachename.redis.cache.windows.net:6380
    ```

    Zapisz i zamknij plik konfiguracji. 
  
    W menu stunnel log, kliknij kolejno pozycje **Konfiguracja**  >  **Załaduj ponownie konfigurację**.


## <a name="connect-using-the-redis-command-line-tool"></a>Połącz się za pomocą narzędzia wiersza polecenia Redis.

W przypadku korzystania z stunnel Uruchom *redis-cli.exe* i przekaż tylko *port* i *klucz dostępu* (podstawowy lub pomocniczy), aby połączyć się z pamięcią podręczną.

```
redis-cli.exe -p 6380 -a YourAccessKey
```

![Zrzut ekranu pokazujący, że połączenie z pamięcią podręczną zostało wykonane pomyślnie.](media/cache-how-to-redis-cli-tool/cache-redis-cli-stunnel.png)

Jeśli używasz pamięci podręcznej testów z **niezabezpieczonym** portem innym niż TLS, uruchom `redis-cli.exe` i przekaż *nazwę hosta*, *port* i *klucz dostępu* (podstawowy lub pomocniczy), aby nawiązać połączenie z pamięcią podręczną testową.

```
redis-cli.exe -h yourcachename.redis.cache.windows.net -p 6379 -a YourAccessKey
```

![stunnel z Redis — interfejs wiersza polecenia](media/cache-how-to-redis-cli-tool/cache-redis-cli-non-ssl.png)




## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat używania [konsoli Redis](cache-configure.md#redis-console) do wydawania poleceń.