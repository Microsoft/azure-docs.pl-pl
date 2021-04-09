---
title: Rozwiązywanie typowych błędów w Azure Cosmos DB interfejs API Cassandra
description: W tym dokumencie omówiono sposoby rozwiązywania typowych problemów występujących w Azure Cosmos DB interfejs API Cassandra
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 03/02/2021
ms.author: thvankra
ms.openlocfilehash: f9b6e586879b8697660ced7aa6f1e75083e3ee29
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101658575"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-db-cassandra-api"></a>Rozwiązywanie typowych problemów z Azure Cosmos DB interfejs API Cassandra
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Interfejs API Cassandra w Azure Cosmos DB to warstwa zgodności, która zapewnia [obsługę protokołu przewodowego](cassandra-support.md) dla popularnej bazy danych Apache Cassandra typu open source i jest obsługiwane przez [Azure Cosmos DB](./introduction.md). Jako w pełni zarządzana usługa w chmurze, Azure Cosmos DB zapewnia [gwarancje dotyczące dostępności, przepływności i spójności](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/) interfejs API Cassandra. Te gwarancje nie są możliwe w starszych implementacjach platformy Apache Cassandra. Interfejs API Cassandra ułatwia również wykonywanie operacji na platformie o zerowej konserwacji i stosowanie poprawek bez przestojów. W związku z tym wiele operacji zaplecza jest różna od platformy Apache Cassandra, dlatego zalecamy określone ustawienia i podejścia, aby uniknąć typowych błędów. 

W tym artykule opisano typowe błędy i rozwiązania dla aplikacji korzystających z Azure Cosmos DB interfejs API Cassandra. Jeśli błąd nie jest wymieniony poniżej i wystąpił błąd podczas wykonywania [obsługiwanej operacji w interfejs API Cassandra, w](cassandra-support.md)której wystąpił błąd *podczas korzystania z natywnego elementu Apache Cassandra*, [Utwórz żądanie pomocy technicznej platformy Azure](../azure-portal/supportability/how-to-create-azure-support-request.md).

## <a name="nonodeavailableexception"></a>NoNodeAvailableException
Jest to wyjątek otoki najwyższego poziomu z dużą liczbą możliwych przyczyn i wyjątkami wewnętrznymi, z których wiele może być związanych z klientem. 
### <a name="solution"></a>Rozwiązanie
Niektóre popularne przyczyny i rozwiązania są następujące: 
- Limit czasu bezczynności usługi Azure LoadBalancers: może to również być manifest `ClosedConnectionException` . Aby rozwiązać ten problem, ustaw ustawienie Zachowaj aktywność w sterowniku (zobacz [poniżej](#enable-keep-alive-for-java-driver)) i zwiększ ustawienia utrzymywania aktywności w systemie operacyjnym lub [Dostosuj limit czasu bezczynności w Azure Load Balancer](../load-balancer/load-balancer-tcp-idle-timeout.md?tabs=tcp-reset-idle-portal). 
- **Wyczerpanie zasobów aplikacji klienta:** upewnij się, że komputery klienckie mają wystarczającą ilość zasobów do ukończenia żądania. 

## <a name="cannot-connect-to-host"></a>Nie można nawiązać połączenia z hostem
Może pojawić się następujący błąd: `Cannot connect to any host, scheduling retry in 600000 milliseconds` . 

### <a name="solution"></a>Rozwiązanie
Może to być wyczerpanie ze strony klienta. Postępuj zgodnie z instrukcjami w obszarze [adresów sieciowych dla połączeń wychodzących](../load-balancer/load-balancer-outbound-connections.md) , aby wykluczyć ten problem. Może to być również problem z limitem czasu bezczynności w przypadku, gdy moduł równoważenia obciążenia platformy Azure domyślnie ma 4 minuty czasu bezczynności. Zapoznaj się z dokumentacją w obszarze [limit czasu bezczynności modułu równoważenia obciążenia](../load-balancer/load-balancer-tcp-idle-timeout.md?tabs=tcp-reset-idle-portal). Włącz ruch TCP-utrzymywanie aktywności z ustawień sterownika (patrz [poniżej](#enable-keep-alive-for-java-driver)) i ustaw `keepAlive` interwał w systemie operacyjnym na mniej niż 4 minuty.

 

## <a name="overloadedexception-java"></a>Załadowaneexception (Java)
Całkowita liczba zużytych jednostek żądania jest większa niż jednostka żądania obsługiwana w obszarze kluczy lub tabeli. Żądania są ograniczone.
### <a name="solution"></a>Rozwiązanie
Rozważ przeskalowanie przepływności przypisanej do przestrzeni kluczy lub tabeli z Azure Portal (zobacz [tutaj](manage-scale-cassandra.md) , aby skalować operacje w interfejs API Cassandra), lub Zaimplementuj zasady ponawiania. W przypadku języka Java Zobacz przykłady ponownych prób dla sterownika [v3. x](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample) i [v4. x](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample-v4). Zobacz również [rozszerzenia usługi Azure Cosmos Cassandra dla języka Java](https://github.com/Azure/azure-cosmos-cassandra-extensions).

### <a name="overloadedexception-even-with-sufficient-throughput"></a>Załadowaneexception nawet z wystarczającą przepływność 
System wydaje się otrzymywać żądania ograniczania przepustowości, pomimo że jest dostępna wystarczająca przepływność dla woluminu żądania i/lub zużytego kosztu jednostki żądania. Istnieją dwie możliwe przyczyny nieoczekiwanego ograniczenia szybkości:
- **Operacje na poziomie schematu:** Interfejs API Cassandra implementuje budżet przepływności systemu dla operacji na poziomie schematu (CREATE TABLE, ALTER TABLE, DROP TABLE). Ten budżet powinien być wystarczający dla operacji schematu w systemie produkcyjnym. Jeśli jednak masz dużą liczbę operacji na poziomie schematu, możliwe jest przekroczenie tego limitu. Ponieważ ten budżet nie jest kontrolowany przez użytkownika, należy rozważyć zmniejszenie liczby uruchamianych operacji schematu. Jeśli wykonanie tej akcji nie rozwiąże problemu lub nie jest to możliwe w obciążeniu, [Utwórz żądanie pomocy technicznej platformy Azure](../azure-portal/supportability/how-to-create-azure-support-request.md).
- **Pochylenie danych:** w przypadku obsługi przepływności w interfejs API Cassandra jest on podzielony równomiernie między partycjami fizycznymi, a każda partycja fizyczna ma górny limit. W przypadku wstawiania dużej ilości danych lub odszukania ich z jednej konkretnej partycji, możliwe jest ograniczenie liczby, nawet w przypadku aprowizacji dużej ilości ogólnej przepływności (jednostki żądań) dla tej tabeli. Przejrzyj model danych i upewnij się, że nie masz nadmiernego pochylenia, które może powodować powstanie partycji na gorąco. 

## <a name="intermittent-connectivity-errors-java"></a>Sporadyczne błędy łączności (Java) 
Połączenie jest nieoczekiwane lub przerzucane.

### <a name="solution"></a>Rozwiązanie 
Sterowniki Apache Cassandra dla języka Java zapewniają dwie natywne zasady ponownego połączenia: `ExponentialReconnectionPolicy` i `ConstantReconnectionPolicy` . Wartość domyślna to `ExponentialReconnectionPolicy`. Jednakże w przypadku Azure Cosmos DB interfejs API Cassandra zalecamy `ConstantReconnectionPolicy` opóźnienie wynoszące 2 sekundy. Zobacz [dokumentację sterowników](https://docs.datastax.com/en/developer/java-driver/4.9/manual/core/reconnection/)  dla sterownika Java v4. x i [tutaj](https://docs.datastax.com/en/developer/java-driver/3.7/manual/reconnection/) dla języka Java 3. x wskazówki, zobacz również [Konfigurowanie ReconnectionPolicy for Java](#configuring-reconnectionpolicy-for-java-driver) przykłady.

## <a name="error-with-load-balancing-policy"></a>Błąd przy użyciu zasad równoważenia obciążenia

Jeśli zaimplementowano zasady równoważenia obciążenia w wersji 3. x sterownika DataStax języka Java, w kodzie podobnym do poniższego:

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

Jeśli wartość parametru nie jest `withLocalDc()` zgodna z centrum danych punktu kontaktu, może wystąpić bardzo Sporadyczny błąd: `com.datastax.driver.core.exceptions.NoHostAvailableException: All host(s) tried for query failed (no host was tried)` . 

### <a name="solution"></a>Rozwiązanie 
Zaimplementuj [CosmosLoadBalancingPolicy](https://github.com/Azure/azure-cosmos-cassandra-extensions/blob/master/package/src/main/java/com/microsoft/azure/cosmos/cassandra/CosmosLoadBalancingPolicy.java) (może być konieczne uaktualnienie wersji pomocniczej DataStax w celu jej działania):

```java
LoadBalancingPolicy loadBalancingPolicy = new CosmosLoadBalancingPolicy.Builder().withWriteDC("West US").withReadDC("West US").build();
```

## <a name="count-fails-on-large-table"></a>Liczba w dużej tabeli nie powiodła się
Po uruchomieniu `select count(*) from table` lub podobne dla dużej liczby wierszy serwer przeprowadzi limit czasu.

### <a name="solution"></a>Rozwiązanie 
W przypadku korzystania z lokalnego klienta CQLSH można spróbować zmienić `--connect-timeout` `--request-timeout` Ustawienia lub (Zobacz więcej szczegółów [tutaj](https://cassandra.apache.org/doc/latest/tools/cqlsh.html)). Jeśli ta wartość nie jest wystarczająca i liczba nadal przekracza limit czasu, można uzyskać liczbę rekordów z telemetrii wewnętrznej Azure Cosmos DB, przechodząc do karty metryki w Azure Portal, wybierając metrykę `document count` , a następnie dodając filtr dla bazy danych lub kolekcji (analogicznie tabeli w Azure Cosmos DB). Następnie można umieścić wskaźnik na wynikowym wykresie dla danego punktu w czasie, w którym ma zostać obliczona liczba rekordów.

:::image type="content" source="./media/cassandra-troubleshoot/metrics.png" alt-text="Widok metryk":::


## <a name="configuring-reconnectionpolicy-for-java-driver"></a>Konfigurowanie ReconnectionPolicy dla sterownika Java

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

## <a name="enable-keep-alive-for-java-driver"></a>Włącz funkcję Keep-Alive dla sterownika Java

### <a name="version-3x"></a>Wersja 3. x

W przypadku wersji 3. x sterownika Java ustaw wartość Keep-Alive podczas tworzenia obiektu klastra i upewnij się, [że w systemie operacyjnym włączona jest funkcja](https://knowledgebase.progress.com/articles/Article/configure-OS-TCP-KEEPALIVE-000080089)Keep-Alive:

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

W przypadku wersji 4. x sterownika Java ustaw wartość Keep-Alive, zastępując ustawienia w `reference.conf` i upewnij się, że [w systemie operacyjnym włączona jest funkcja](https://knowledgebase.progress.com/articles/Article/configure-OS-TCP-KEEPALIVE-000080089)Keep-Alive:

```xml
datastax-java-driver {
  advanced {
    socket{
      keep-alive = true
    }
}
```

## <a name="next-steps"></a>Następne kroki

- Poznaj [obsługiwane funkcje](cassandra-support.md) w Azure Cosmos DB interfejs API Cassandra.
- Dowiedz się, jak [przeprowadzić migrację z macierzystego Cassandra Apache do Azure Cosmos DB interfejs API Cassandra](cassandra-migrate-cosmos-db-databricks.md)
