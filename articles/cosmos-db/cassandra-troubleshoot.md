---
title: Rozwiązywanie typowych błędów w Azure Cosmos DB interfejs API Cassandra
description: W tym artykule omówiono typowe problemy interfejs API Cassandra Azure Cosmos DB oraz sposoby rozwiązywania problemów z nimi.
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 03/02/2021
ms.author: thvankra
ms.openlocfilehash: e81ab2539527c9d5c5cf2c6bff77fd19887103cd
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105967357"
---
# <a name="troubleshoot-common-issues-in-the-azure-cosmos-db-cassandra-api"></a>Rozwiązywanie typowych problemów z Azure Cosmos DB interfejs API Cassandra

[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Interfejs API Cassandra w [Azure Cosmos DB](./introduction.md) jest warstwą zgodności, która zapewnia [obsługę protokołu przewodowego](cassandra-support.md) dla bazy danych Apache Cassandra typu open source.

W tym artykule opisano typowe błędy i rozwiązania dla aplikacji, które używają interfejs API Cassandra Azure Cosmos DB. Jeśli błąd nie jest wyświetlany i wystąpi błąd podczas wykonywania [obsługiwanej operacji w programie Cassandra](cassandra-support.md), ale błąd nie występuje podczas korzystania z natywnego Apache Cassandra, [Utwórz żądanie pomocy technicznej platformy Azure](../azure-portal/supportability/how-to-create-azure-support-request.md).

>[!NOTE]
>Jako w pełni zarządzana usługa w chmurze, Azure Cosmos DB zapewnia [gwarancje dotyczące dostępności, przepływności i spójności](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/) interfejs API Cassandra. Interfejs API Cassandra ułatwia również wykonywanie operacji na platformie o zerowej konserwacji oraz poprawek bez przestojów.
>
>Te gwarancje nie są możliwe w poprzednich implementacjach platformy Apache Cassandra, więc wiele interfejs API Cassandra operacji zaplecza różni się od platformy Apache Cassandra. Zalecamy konkretne ustawienia i podejścia, które pozwolą uniknąć typowych błędów.

## <a name="nonodeavailableexception"></a>NoNodeAvailableException

Ten błąd to wyjątek otoki najwyższego poziomu z dużą liczbą możliwych przyczyn i wyjątkami wewnętrznymi, z których wiele może być związanych z klientem.

Typowe przyczyny i rozwiązania:

- **Limit czasu bezczynności usługi Azure LoadBalancers**: ten problem może również być manifestem `ClosedConnectionException` . Aby rozwiązać ten problem, należy ustawić ustawienie Keep-Alive w sterowniku (zobacz [Włączanie Keep-Alive dla sterownika Java](#enable-keep-alive-for-the-java-driver)) i zwiększyć ustawienia utrzymywania aktywności w systemie operacyjnym lub [dopasować limit czasu bezczynności w Azure Load Balancer](../load-balancer/load-balancer-tcp-idle-timeout.md?tabs=tcp-reset-idle-portal).

- **Wyczerpanie zasobów aplikacji klienta**: Upewnij się, że komputery klienckie mają wystarczającą ilość zasobów do ukończenia żądania.

## <a name="cant-connect-to-a-host"></a>Nie można nawiązać połączenia z hostem

Może pojawić się następujący błąd: "nie można nawiązać połączenia z żadnym hostem, zaplanować ponowną próbę w 600000 milisekund".

Ten błąd może być spowodowany wyczerpaniem danych na stronie klienta przy użyciu translacji adresów sieciowych (NAT). Postępuj zgodnie z instrukcjami w obszarze [adresów sieciowych dla połączeń wychodzących](../load-balancer/load-balancer-outbound-connections.md) , aby wykluczyć ten problem.

Ten błąd może być również problemem z limitem czasu bezczynności w przypadku, gdy moduł równoważenia obciążenia platformy Azure domyślnie ma cztery minuty czasu bezczynności. Zobacz [limit czasu bezczynności modułu równoważenia obciążenia](../load-balancer/load-balancer-tcp-idle-timeout.md?tabs=tcp-reset-idle-portal). [Włącz opcję Keep-Alive dla sterownika Java](#enable-keep-alive-for-the-java-driver) i ustaw `keepAlive` interwał w systemie operacyjnym na mniej niż cztery minuty.

## <a name="overloadedexception-java"></a>Załadowaneexception (Java)

Żądania są ograniczone, ponieważ łączna liczba zużytych jednostek żądań jest większa niż liczba jednostek żądania, które zostały zainicjowane w przestrzeni kluczy lub tabeli.

Rozważ przeskalowanie przepływności przypisanej do przestrzeni kluczy lub tabeli z Azure Portal (zobacz [elastyczne skalowanie Azure Cosmos DB interfejs API Cassandra konta](manage-scale-cassandra.md)) lub wdrożenie zasad ponawiania.

W przypadku języka Java Zobacz przykłady ponownych prób dla [sterownika v3. x](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample) i [sterownika v4. x](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample-v4). Zobacz również [rozszerzenia usługi Azure Cosmos Cassandra dla języka Java](https://github.com/Azure/azure-cosmos-cassandra-extensions).

### <a name="overloadedexception-despite-sufficient-throughput"></a>Nadładowanieexception pomimo wystarczającej przepływności

System wydaje się ograniczać żądania, nawet jeśli zapewniona jest wystarczająca przepływność dla woluminu żądania lub koszt zużytej jednostki żądania. Istnieją dwie możliwe przyczyny:

- **Operacje na poziomie schematu**: interfejs API Cassandra implementuje budżet przepływności systemu dla operacji na poziomie schematu (CREATE TABLE, ALTER TABLE, Drop Table). Ten budżet powinien być wystarczający dla operacji schematu w systemie produkcyjnym. Jeśli jednak masz dużą liczbę operacji na poziomie schematu, możesz przekroczyć ten limit.

  Ponieważ budżet nie jest kontrolowany przez użytkownika, rozważ obniżenie liczby uruchomionych operacji schematu. Jeśli ta akcja nie rozwiąże problemu lub nie jest to możliwe w obciążeniu, [Utwórz żądanie pomocy technicznej platformy Azure](../azure-portal/supportability/how-to-create-azure-support-request.md).

- **Pochylenie danych**: w przypadku obsługi przepływności w interfejs API Cassandra jest on podzielony równomiernie między partycjami fizycznymi, a każda partycja fizyczna ma górny limit. W przypadku wstawiania dużej ilości danych lub zapytania z jednej konkretnej partycji może być ono ograniczone, nawet w przypadku aprowizacji dużej ilości ogólnej przepływności (jednostki żądań) dla tej tabeli.

  Przejrzyj model danych i upewnij się, że nie masz nadmiernego pochylenia, które może powodować występowanie partycji na gorąco.

## <a name="intermittent-connectivity-errors-java"></a>Sporadyczne błędy łączności (Java)

Połączenie jest nieoczekiwane lub przerzucane.

Sterowniki Apache Cassandra dla języka Java zapewniają dwie natywne zasady ponownego połączenia: `ExponentialReconnectionPolicy` i `ConstantReconnectionPolicy` . Wartość domyślna to `ExponentialReconnectionPolicy`. Jednakże w przypadku Azure Cosmos DB interfejs API Cassandra zalecamy `ConstantReconnectionPolicy` dwusekundowe opóźnienie.

Zapoznaj się z [dokumentacją sterownika Java 4. x](https://docs.datastax.com/en/developer/java-driver/4.9/manual/core/reconnection/), [dokumentacją sterownika Java 3. x](https://docs.datastax.com/en/developer/java-driver/3.7/manual/reconnection/)lub [Skonfiguruj ReconnectionPolicy dla przykładów sterownika Java](#configure-reconnectionpolicy-for-the-java-driver) .

## <a name="error-with-load-balancing-policy"></a>Błąd przy użyciu zasad równoważenia obciążenia

Być może zaimplementowano zasady równoważenia obciążenia w wersji 3. x sterownika DataStax języka Java z kodem podobnym do:

```java
cluster = Cluster.builder()
        .addContactPoint(cassandraHost)
        .withPort(cassandraPort)
        .withCredentials(cassandraUsername, cassandraPassword)
        .withPoolingOptions(new PoolingOptions() .setConnectionsPerHost(HostDistance.LOCAL, 1, 2)
                .setMaxRequestsPerConnection(HostDistance.LOCAL, 32000).setMaxQueueSize(Integer.MAX_VALUE))
        .withSSL(sslOptions)
        .withLoadBalancingPolicy(DCAwareRoundRobinPolicy.builder().withLocalDc("West US").build())
        .withQueryOptions(new QueryOptions().setConsistencyLevel(ConsistencyLevel.LOCAL_QUORUM))
        .withSocketOptions(getSocketOptions())
        .build();
```

Jeśli wartość parametru `withLocalDc()` nie jest zgodna z centrum danych punktu kontaktu, może wystąpić Sporadyczny błąd: `com.datastax.driver.core.exceptions.NoHostAvailableException: All host(s) tried for query failed (no host was tried)` .

Zaimplementuj [CosmosLoadBalancingPolicy](https://github.com/Azure/azure-cosmos-cassandra-extensions/blob/master/package/src/main/java/com/microsoft/azure/cosmos/cassandra/CosmosLoadBalancingPolicy.java). Aby to umożliwić, może być konieczne uaktualnienie DataStax przy użyciu następującego kodu:

```java
LoadBalancingPolicy loadBalancingPolicy = new CosmosLoadBalancingPolicy.Builder().withWriteDC("West US").withReadDC("West US").build();
```

## <a name="the-count-fails-on-a-large-table"></a>Liczba w dużej tabeli nie powiodła się

Po uruchomieniu `select count(*) from table` lub podobnej części dla dużej liczby wierszy serwer przeprowadzi limit czasu.

Jeśli używasz lokalnego klienta CQLSH, Zmień `--connect-timeout` `--request-timeout` Ustawienia lub. Zobacz [cqlsh: The CQL Shell](https://cassandra.apache.org/doc/latest/tools/cqlsh.html).

Jeśli liczba nadal przekracza limit czasu, można uzyskać liczbę rekordów z Azure Cosmos DB danych telemetrycznych zaplecza, przechodząc do karty metryki w Azure Portal, wybierając metrykę `document count` , a następnie dodając filtr dla bazy danych lub kolekcji (wartość analogowa tabeli w Azure Cosmos DB). Następnie można umieścić wskaźnik na wynikowym wykresie dla danego punktu w czasie, w którym ma zostać obliczona liczba rekordów.

:::image type="content" source="./media/cassandra-troubleshoot/metrics.png" alt-text="Widok metryk":::

## <a name="configure-reconnectionpolicy-for-the-java-driver"></a>Konfigurowanie ReconnectionPolicy dla sterownika Java

### <a name="version-3x"></a>Wersja 3. x

W przypadku wersji 3. x sterownika Java Skonfiguruj zasady ponownego połączenia podczas tworzenia obiektu klastra:

```java
import com.datastax.driver.core.policies.ConstantReconnectionPolicy;

Cluster.builder()
  .withReconnectionPolicy(new ConstantReconnectionPolicy(2000))
  .build();
```

### <a name="version-4x"></a>4. x

W przypadku wersji 4. x sterownika Java Skonfiguruj zasady ponownego połączenia, zastępując ustawienia w `reference.conf` pliku:

```xml
datastax-java-driver {
  advanced {
    reconnection-policy{
      # The driver provides two implementations out of the box: ExponentialReconnectionPolicy and
      # ConstantReconnectionPolicy. We recommend ConstantReconnectionPolicy for Cassandra API, with 
      # base-delay of 2 seconds.
      class = ConstantReconnectionPolicy
      base-delay = 2 second
    }
}
```

## <a name="enable-keep-alive-for-the-java-driver"></a>Włącz utrzymywanie aktywności dla sterownika Java

### <a name="version-3x"></a>Wersja 3. x

W przypadku wersji 3. x sterownika Java ustaw wartość Keep-Alive podczas tworzenia obiektu klastra, a następnie upewnij się, że [w systemie operacyjnym włączona jest funkcja](https://knowledgebase.progress.com/articles/Article/configure-OS-TCP-KEEPALIVE-000080089)Keep-Alive:

```java
import java.net.SocketOptions;
    
SocketOptions options = new SocketOptions();
options.setKeepAlive(true);
cluster = Cluster.builder().addContactPoints(contactPoints).withPort(port)
  .withCredentials(cassandraUsername, cassandraPassword)
  .withSocketOptions(options)
  .build();
```

### <a name="version-4x"></a>4. x

W przypadku wersji 4. x sterownika Java ustaw wartość Keep-Alive poprzez zastępowanie ustawień w programie `reference.conf` , a następnie upewnij się, że [w systemie operacyjnym włączona jest funkcja](https://knowledgebase.progress.com/articles/Article/configure-OS-TCP-KEEPALIVE-000080089)Keep-Alive:

```xml
datastax-java-driver {
  advanced {
    socket{
      keep-alive = true
    }
}
```

## <a name="next-steps"></a>Następne kroki

- Informacje o [obsługiwanych funkcjach](cassandra-support.md) interfejs API Cassandra Azure Cosmos DB.
- Dowiedz się, jak [przeprowadzić migrację z natywnego programu Apache Cassandra do interfejs API Cassandra Azure Cosmos DB](cassandra-migrate-cosmos-db-databricks.md).
