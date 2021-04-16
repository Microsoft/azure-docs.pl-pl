---
title: 'Szybki start: korzystanie z Azure Cache for Redis w języku Java'
description: W tym przewodniku Szybki start utworzysz nową aplikację Java, która korzysta z pamięci podręcznej Azure Cache for Redis.
author: yegu-ms
ms.author: yegu
ms.date: 05/22/2020
ms.topic: quickstart
ms.service: cache
ms.devlang: java
ms.custom:
- mvc
- seo-java-august2019
- seo-java-september2019
- devx-track-java
- mode-api
ms.openlocfilehash: 7128ff1e20439c57d3e6212f29e6f871997584c6
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538609"
---
# <a name="quickstart-use-azure-cache-for-redis-in-java"></a>Szybki start: korzystanie z Azure Cache for Redis w języku Java

W tym przewodniku Szybki start dołączasz aplikację Azure Cache for Redis Java przy użyciu klienta [Jedis](https://github.com/xetorthio/jedis) Redis, aby mieć dostęp do bezpiecznej, dedykowanej pamięci podręcznej dostępnej z dowolnej aplikacji na platformie Azure.

## <a name="skip-to-the-code-on-github"></a>Przejdź do kodu w witrynie GitHub

Jeśli chcesz przejść bezpośrednio do kodu, zobacz Przewodnik Szybki start [języka Java w](https://github.com/Azure-Samples/azure-cache-redis-samples/tree/main/quickstart/java) witrynie GitHub.

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure [— utwórz subskrypcję bezpłatnie](https://azure.microsoft.com/free/)
- [Apache Maven](https://maven.apache.org/download.cgi)

## <a name="create-an-azure-cache-for-redis"></a>Tworzenie usługi Azure Cache for Redis

[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-access-keys](../../includes/redis-cache-access-keys.md)]

## <a name="setting-up-the-working-environment"></a>Konfigurowanie środowiska roboczego 

W zależności od systemu operacyjnego dodaj zmienne środowiskowe dla wartości **Nazwa hosta** i Podstawowy **klucz dostępu.** Otwórz wiersz polecenia lub okno terminalu i skonfiguruj następujące wartości:

```CMD 
set REDISCACHEHOSTNAME=<YOUR_HOST_NAME>.redis.cache.windows.net
set REDISCACHEKEY=<YOUR_PRIMARY_ACCESS_KEY>
```

```bash
export REDISCACHEHOSTNAME=<YOUR_HOST_NAME>.redis.cache.windows.net
export REDISCACHEKEY=<YOUR_PRIMARY_ACCESS_KEY>
```

Zastąp symbole zastępcze następującymi wartościami:

- `<YOUR_HOST_NAME>`: nazwa hosta DNS uzyskana z sekcji *Właściwości* zasobu Azure Cache for Redis w Azure Portal.
- `<YOUR_PRIMARY_ACCESS_KEY>`: podstawowy klucz dostępu uzyskany z sekcji *Klucze* dostępu zasobu Azure Cache for Redis w Azure Portal.

## <a name="create-a-new-java-app"></a>Tworzenie nowej aplikacji Java

Za pomocą programu Maven wygeneruj nową aplikację przewodnika Szybki Start:

```CMD
mvn archetype:generate -DarchetypeGroupId=org.apache.maven.archetypes -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.3 -DgroupId=example.demo -DartifactId=redistest -Dversion=1.0
```

Zmień katalog projektu *redistest* na nowy.

Otwórz plik *pom.xml* i dodaj zależność dla [Jedis](https://github.com/xetorthio/jedis):

```xml
    <dependency>
        <groupId>redis.clients</groupId>
        <artifactId>jedis</artifactId>
        <version>3.2.0</version>
        <type>jar</type>
        <scope>compile</scope>
    </dependency>
```

Zapisz *pom.xml* plik.

Otwórz plik *App.java* i zastąp kod następującym:

```java
package example.demo;

import redis.clients.jedis.Jedis;
import redis.clients.jedis.JedisShardInfo;

/**
 * Redis test
 *
 */
public class App 
{
    public static void main( String[] args )
    {

        boolean useSsl = true;
        String cacheHostname = System.getenv("REDISCACHEHOSTNAME");
        String cachekey = System.getenv("REDISCACHEKEY");

        // Connect to the Azure Cache for Redis over the TLS/SSL port using the key.
        JedisShardInfo shardInfo = new JedisShardInfo(cacheHostname, 6380, useSsl);
        shardInfo.setPassword(cachekey); /* Use your access key. */
        Jedis jedis = new Jedis(shardInfo);      

        // Perform cache operations using the cache connection object...

        // Simple PING command        
        System.out.println( "\nCache Command  : Ping" );
        System.out.println( "Cache Response : " + jedis.ping());

        // Simple get and put of integral data types into the cache
        System.out.println( "\nCache Command  : GET Message" );
        System.out.println( "Cache Response : " + jedis.get("Message"));

        System.out.println( "\nCache Command  : SET Message" );
        System.out.println( "Cache Response : " + jedis.set("Message", "Hello! The cache is working from Java!"));

        // Demonstrate "SET Message" executed as expected...
        System.out.println( "\nCache Command  : GET Message" );
        System.out.println( "Cache Response : " + jedis.get("Message"));

        // Get the client list, useful to see if connection list is growing...
        System.out.println( "\nCache Command  : CLIENT LIST" );
        System.out.println( "Cache Response : " + jedis.clientList());

        jedis.close();
    }
}
```

W tym kodzie pokazano sposób podłączania do wystąpienia usługi Azure Cache for Redis przy użyciu nazwy hosta w pamięci podręcznej i kluczowych zmiennych środowiskowych. W kodzie jest również przechowywana i pobierana wartość ciągu w pamięci podręcznej. Następuje wykonanie poleceń `PING` i `CLIENT LIST`. 

Zapisz plik *App.java*.

## <a name="build-and-run-the-app"></a>Skompiluj i uruchom aplikację

Wykonaj następujące polecenie narzędzia Maven, aby skompilować i uruchomić aplikację:

```CMD
mvn compile
mvn exec:java -D exec.mainClass=example.demo.App
```

W poniższym przykładzie widać, że klucz `Message` miał już w pamięci podręcznej wartość, która została ustawiona za pomocą konsoli Redis w witrynie Azure Portal. Aplikacja zaktualizowała tę wartość w pamięci podręcznej. Aplikacja również wykonała polecenia `PING` i `CLIENT LIST`.

![Azure Cache for Redis ukończona](./media/cache-java-get-started/azure-cache-redis-complete.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli zamierzasz przejść do kolejnego samouczka, możesz zachować zasoby utworzone w tym przewodniku Szybki start i użyć ich ponownie.

W przeciwnym razie po zakończeniu pracy z przykładową aplikacją poradnika Szybki start możesz usunąć zasoby platformy Azure utworzone w tym poradniku Szybki start, aby uniknąć naliczania opłat. 

> [!IMPORTANT]
> Usunięcie grupy zasobów jest nieodwracalne i grupa zasobów oraz wszystkie zawarte w niej zasoby zostaną trwale usunięte. Uważaj, aby nie usunąć przypadkowo niewłaściwych zasobów lub grupy zasobów. Jeśli zasoby do hostowania tego przykładu zostały utworzone wewnątrz istniejącej grupy zasobów zawierającej zasoby, które chcesz zachować, możesz usunąć każdy zasób oddzielnie z odpowiadającego mu bloku zamiast usuwać całą grupę zasobów.
>

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) i wybierz pozycję **Grupy zasobów**.

1. W polu **tekstowym Filtruj** według nazwy wpisz nazwę grupy zasobów. Instrukcje w tym artykule używają grupy zasobów o nazwie *TestResources*. W grupie zasobów na liście wyników wybierz pozycję ... , **a** następnie **pozycję Usuń grupę zasobów.**

   ![Usunięto grupę zasobów platformy Azure](./media/cache-java-get-started/azure-cache-redis-delete-resource-group.png)

1. Zobaczysz prośbę o potwierdzenie usunięcia grupy zasobów. Wpisz nazwę grupy zasobów, aby potwierdzić, a następnie wybierz pozycję **Usuń.**

Po krótkim czasie grupa zasobów i wszystkie zawarte w niej zasoby zostaną usunięte.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start pokazano, jak korzystać z usługi Azure Cache for Redis z poziomu aplikacji Java. Przejdź do kolejnego Szybkiego startu, w którym wyjaśniono, jak używać usługi Microsoft Azure Cache for Redis z poziomu aplikacji internetowej ASP.NET.

> [!div class="nextstepaction"]
> [Tworzenie na platformie ASP.NET aplikacji internetowej, która korzysta z usługi Microsoft Azure Cache for Redis.](./cache-web-app-howto.md)
