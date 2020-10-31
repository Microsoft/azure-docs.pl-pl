---
title: 'Szybki Start: korzystanie z usługi Azure cache for Redis w Node.js'
description: W tym przewodniku Szybki start dowiesz się, jak używać usługi Azure Cache for Redis w środowisku Node.js oraz node_redis.
author: yegu-ms
ms.service: cache
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 05/21/2018
ms.author: yegu
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019, devx-track-js
ms.openlocfilehash: aa22cffc1fc38e055c6c2bb504c311c012f31ac2
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93087149"
---
# <a name="quickstart-use-azure-cache-for-redis-in-nodejs"></a>Szybki Start: korzystanie z usługi Azure cache for Redis w Node.js

W tym przewodniku szybki start dodaliśmy usługę Azure cache for Redis do aplikacji Node.js, aby uzyskać dostęp do bezpiecznej, dedykowanej pamięci podręcznej dostępnej z dowolnej aplikacji na platformie Azure.

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/)
- [node_redis](https://github.com/mranney/node_redis), którą można zainstalować za pomocą polecenia `npm install redis` . 

Przykłady użycia innych klientów Node.js można znaleźć w dokumentacji poszczególnych klientów Node.js wymienionych na stronie [klientów Node.js usługi Redis](https://redis.io/clients#nodejs).

## <a name="create-a-cache"></a>Tworzenie pamięci podręcznej
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-access-keys](../../includes/redis-cache-access-keys.md)]


Dodaj zmienne środowiskowe dla **NAZWY HOSTA** i **podstawowego** klucza dostępu. Użyj zmiennych z kodu, nie dodawaj wrażliwych informacji bezpośrednio w kodzie.

```
set REDISCACHEHOSTNAME=contosoCache.redis.cache.windows.net
set REDISCACHEKEY=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
```

## <a name="connect-to-the-cache"></a>Łączenie z pamięcią podręczną

Najnowsze kompilacje [node_redis](https://github.com/mranney/node_redis) zapewniają obsługę łączenia z usługą Azure cache for Redis przy użyciu protokołu TLS. Poniższy przykład pokazuje, jak nawiązać połączenie z usługą Azure cache for Redis przy użyciu punktu końcowego TLS 6380. 

```js
var redis = require("redis");

// Add your cache name and access key.
var client = redis.createClient(6380, process.env.REDISCACHEHOSTNAME,
    {auth_pass: process.env.REDISCACHEKEY, tls: {servername: process.env.REDISCACHEHOSTNAME}});
```

Nie twórz nowego połączenia dla każdej operacji w kodzie. Zamiast tego należy w miarę możliwości używać połączeń ponownie. 

## <a name="create-a-new-nodejs-app"></a>Tworzenie nowej aplikacji na platformie Node.js

Utwórz nowy plik skryptu o nazwie *redistest.js* . Użyj polecenia, `npm install redis bluebird` Aby zainstalować wymagane pakiety.

Dodaj do pliku poniższy przykład kodu JavaScript. W tym kodzie pokazano sposób podłączania do wystąpienia usługi Azure Cache for Redis przy użyciu nazwy hosta w pamięci podręcznej i kluczowych zmiennych środowiskowych. W kodzie jest również przechowywana i pobierana wartość ciągu w pamięci podręcznej. Następuje wykonanie poleceń `PING` i `CLIENT LIST`. Aby uzyskać więcej przykładów użycia usługi Redis z klientem [node_redis](https://github.com/mranney/node_redis), zobacz [https://redis.js.org/](https://redis.js.org/).

```js
var redis = require("redis");
var bluebird = require("bluebird");

// Convert Redis client API to use promises, to make it usable with async/await syntax
bluebird.promisifyAll(redis.RedisClient.prototype);
bluebird.promisifyAll(redis.Multi.prototype);

async function testCache() {

    // Connect to the Azure Cache for Redis over the TLS port using the key.
    var cacheConnection = redis.createClient(6380, process.env.REDISCACHEHOSTNAME, 
        {auth_pass: process.env.REDISCACHEKEY, tls: {servername: process.env.REDISCACHEHOSTNAME}});
        
    // Perform cache operations using the cache connection object...

    // Simple PING command
    console.log("\nCache command: PING");
    console.log("Cache response : " + await cacheConnection.pingAsync());

    // Simple get and put of integral data types into the cache
    console.log("\nCache command: GET Message");
    console.log("Cache response : " + await cacheConnection.getAsync("Message"));    

    console.log("\nCache command: SET Message");
    console.log("Cache response : " + await cacheConnection.setAsync("Message",
        "Hello! The cache is working from Node.js!"));    

    // Demonstrate "SET Message" executed as expected...
    console.log("\nCache command: GET Message");
    console.log("Cache response : " + await cacheConnection.getAsync("Message"));    

    // Get the client list, useful to see if connection list is growing...
    console.log("\nCache command: CLIENT LIST");
    console.log("Cache response : " + await cacheConnection.clientAsync("LIST"));    
}

testCache();
```

Uruchom skrypt języka Node.js.

```
node redistest.js
```

W poniższym przykładzie widać, że klucz `Message` miał już w pamięci podręcznej wartość, która została ustawiona za pomocą konsoli Redis w witrynie Azure Portal. Aplikacja zaktualizowała tę wartość w pamięci podręcznej. Aplikacja również wykonała polecenia `PING` i `CLIENT LIST`.

![Zakończono działanie aplikacji Redis Cache](./media/cache-nodejs-get-started/redis-cache-app-complete.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli zamierzasz przejść do kolejnego samouczka, możesz zachować zasoby utworzone w tym przewodniku Szybki start i użyć ich ponownie.

W przeciwnym razie po zakończeniu pracy z przykładową aplikacją poradnika Szybki start możesz usunąć zasoby platformy Azure utworzone w tym poradniku Szybki start, aby uniknąć naliczania opłat. 

> [!IMPORTANT]
> Usunięcie grupy zasobów jest nieodwracalne i grupa zasobów oraz wszystkie zawarte w niej zasoby zostaną trwale usunięte. Uważaj, aby nie usunąć przypadkowo niewłaściwych zasobów lub grupy zasobów. Jeśli zasoby do hostowania tego przykładu zostały utworzone wewnątrz istniejącej grupy zasobów zawierającej zasoby, które chcesz zachować, możesz usunąć każdy zasób oddzielnie z odpowiadającego mu bloku zamiast usuwać całą grupę zasobów.
>

Zaloguj się do witryny [Azure Portal](https://portal.azure.com) i wybierz pozycję **Grupy zasobów** .

W polu tekstowym **Filtruj według nazwy** wprowadź nazwę grupy zasobów. Instrukcje w tym artykule używają grupy zasobów o nazwie *TestResources* . W grupie zasobów na liście wynik wybierz pozycję **...** , a następnie **Usuń grupę zasobów** .

![Usuń grupę zasobów platformy Azure](./media/cache-nodejs-get-started/redis-cache-delete-resource-group.png)

Zobaczysz prośbę o potwierdzenie usunięcia grupy zasobów. Aby potwierdzić, wprowadź nazwę grupy zasobów, a następnie wybierz pozycję **Usuń** .

Po krótkim czasie grupa zasobów i wszystkie zawarte w niej zasoby zostaną usunięte.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start przedstawiono sposób użycia usługi Azure Cache for Redis z poziomu aplikacji platformy Node.js. Przejdź do kolejnego Szybkiego startu, w którym wyjaśniono, jak używać usługi Microsoft Azure Cache for Redis z poziomu aplikacji internetowej ASP.NET.

> [!div class="nextstepaction"]
> [Tworzenie na platformie ASP.NET aplikacji internetowej, która korzysta z usługi Microsoft Azure Cache for Redis.](./cache-web-app-howto.md)
