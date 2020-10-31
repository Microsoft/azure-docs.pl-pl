---
title: Diagnozowanie i rozwiązywanie problemów Azure Cosmos DB Async Java SDK V2
description: Korzystaj z funkcji, takich jak rejestrowanie po stronie klienta i innych narzędzi innych firm, aby identyfikować, diagnozować i rozwiązywać problemy Azure Cosmos DB w asynchronicznym zestawie Java SDK V2.
author: anfeldma-ms
ms.service: cosmos-db
ms.date: 05/11/2020
ms.author: anfeldma
ms.devlang: java
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.reviewer: sngun
ms.custom: devx-track-java
ms.openlocfilehash: eca64411ccb61e5dcbac5e988f12096550eaaa2c
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93101004"
---
# <a name="troubleshoot-issues-when-you-use-the-azure-cosmos-db-async-java-sdk-v2-with-sql-api-accounts"></a>Rozwiązywanie problemów w przypadku korzystania z Azure Cosmos DB asynchronicznego zestawu Java SDK V2 z kontami interfejsu API SQL
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [Java SDK 4](troubleshoot-java-sdk-v4-sql.md)
> * [Async Java SDK 2](troubleshoot-java-async-sdk.md)
> * [.NET](troubleshoot-dot-net-sdk.md)
> 

> [!IMPORTANT]
> To *nie* jest najnowszy zestaw SDK języka Java dla Azure Cosmos DB! Należy uaktualnić projekt do [Azure Cosmos DB Java SDK v4](sql-api-sdk-java-v4.md) , a następnie zapoznać się z [przewodnikiem rozwiązywania problemów](troubleshoot-java-sdk-v4-sql.md)z zestawem SDK Azure Cosmos DB dla języka Java. Aby przeprowadzić uaktualnienie, postępuj zgodnie z instrukcjami w przewodniku [Migrowanie do Azure Cosmos DB Java SDK v4](migrate-java-v4-sdk.md) i [reaktorem programu vs RxJava](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/reactor-rxjava-guide.md) . 
>
> W tym artykule omówiono Rozwiązywanie problemów dotyczących Azure Cosmos DB asynchronicznego zestawu Java SDK V2. Aby uzyskać więcej informacji, zobacz informacje o [wersji](sql-api-sdk-async-java.md)Azure Cosmos DB asynchronicznego zestawu Java SDK V2, [repozytorium Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) i [porady dotyczące wydajności](performance-tips-async-java.md) .
>

W tym artykule opisano typowe problemy, obejścia, kroki diagnostyczne i narzędzia używane w przypadku korzystania z [asynchronicznego zestawu SDK Java](sql-api-sdk-async-java.md) z kontami interfejsu API SQL Azure Cosmos DB.
Zestaw Java Async SDK zapewnia reprezentację logiczną po stronie klienta służącą do uzyskania dostępu do interfejsu API SQL usługi Azure Cosmos DB. W tym artykule opisano narzędzia i podejścia pomocne w przypadku napotkania jakichkolwiek problemów.

Rozpocznij od tej listy:

* Zapoznaj się z sekcją [typowe problemy i obejścia] w tym artykule.
* Zapoznaj się z zestawem SDK, który jest dostępny jako " [Open Source" w witrynie GitHub](https://github.com/Azure/azure-cosmosdb-java). Zawiera [sekcję problemy](https://github.com/Azure/azure-cosmosdb-java/issues) , która jest aktywnie monitorowana. Sprawdź, czy podobny problem z obejściem został już zgłoszony.
* Zapoznaj się z [poradami dotyczącymi wydajności](performance-tips-async-java.md)i postępuj zgodnie z zaleceniami.
* Zapoznaj się z resztą tego artykułu, jeśli nie odnaleziono rozwiązania. Następnie należy [rozwiązać problem](https://github.com/Azure/azure-cosmosdb-java/issues)z usługą GitHub.

## <a name="common-issues-and-workarounds"></a><a name="common-issues-workarounds"></a>Typowe problemy i ich rozwiązania

### <a name="network-issues-netty-read-timeout-failure-low-throughput-high-latency"></a>Problemy z siecią, Niepowodzenie przekroczenia limitu czasu odczytu z sieci, niska przepływność, duże opóźnienie

#### <a name="general-suggestions"></a>Ogólne sugestie
* Upewnij się, że aplikacja działa w tym samym regionie co konto Azure Cosmos DB. 
* Sprawdź użycie procesora na hoście, na którym działa aplikacja. Jeśli użycie procesora CPU wynosi 90% lub więcej, uruchom aplikację na hoście o wyższej konfiguracji. Można też rozłożyć obciążenie na więcej maszyn.

#### <a name="connection-throttling"></a>Ograniczanie połączeń
Możliwe jest ograniczenie połączenia z powodu [limitu połączeń na komputerze hosta lub w] [wyczerpaniu portów usługi Azure translator adresów sieciowych].

##### <a name="connection-limit-on-a-host-machine"></a><a name="connection-limit-on-host"></a>Limit połączeń na komputerze hosta
Niektóre systemy Linux, takie jak Red Hat, mają górny limit łącznej liczby otwartych plików. Gniazda w systemie Linux są zaimplementowane jako pliki, więc ta liczba ogranicza łączną liczbę połączeń.
Uruchom następujące polecenie.

```bash
ulimit -a
```
Maksymalna dozwolona liczba otwartych plików, które są identyfikowane jako "nofile", musi mieć co najmniej dwa razy więcej rozmiaru puli połączeń. Aby uzyskać więcej informacji, zobacz [porady dotyczące wydajności](performance-tips-async-java.md).

##### <a name="azure-snat-pat-port-exhaustion"></a><a name="snat"></a>Wyczerpanie portów (z) na platformie Azure

Jeśli aplikacja jest wdrażana na platformie Azure Virtual Machines bez publicznego adresu IP, domyślnie [porty usługi Azure](../load-balancer/load-balancer-outbound-connections.md#preallocatedports) IPSec ustanawiają połączenia z dowolnym punktem końcowym poza maszyną wirtualną. Liczba połączeń dozwolonych między maszyną wirtualną a punktem końcowym Azure Cosmos DB jest ograniczona przez [konfigurację usługi Azure translatora adresów sieciowych](../load-balancer/load-balancer-outbound-connections.md#preallocatedports).

 Porty protokołu IPSec platformy Azure są używane tylko wtedy, gdy maszyna wirtualna ma prywatny adres IP, a proces z maszyny wirtualnej próbuje połączyć się z publicznym adresem IP. Istnieją dwa obejścia, aby uniknąć ograniczenia dotyczącego translatora adresów sieciowych platformy Azure:

* Dodaj punkt końcowy usługi Azure Cosmos DB do podsieci sieci wirtualnej platformy Azure Virtual Machines. Aby uzyskać więcej informacji, zobacz [punkty końcowe usługi Azure Virtual Network](../virtual-network/virtual-network-service-endpoints-overview.md). 

    Po włączeniu punktu końcowego usługi żądania nie są już wysyłane z publicznego adresu IP do Azure Cosmos DB. Zamiast tego jest wysyłana tożsamość sieci wirtualnej i podsieci. Ta zmiana może spowodować, że Zapora spadnie, jeśli dozwolone są tylko publiczne adresy IP. Jeśli używasz zapory, po włączeniu punktu końcowego usługi Dodaj podsieć do zapory przy użyciu [list acl Virtual Network](/previous-versions/azure/virtual-network/virtual-networks-acl).
* Przypisz publiczny adres IP do maszyny wirtualnej platformy Azure.

##### <a name="cant-reach-the-service---firewall"></a><a name="cant-connect"></a>Nie można nawiązać połączenia z usługą — Zapora
``ConnectTimeoutException`` wskazuje, że zestaw SDK nie może nawiązać połączenia z usługą.
W przypadku korzystania z trybu bezpośredniego może wystąpić błąd podobny do następującego:
```
GoneException{error=null, resourceAddress='https://cdb-ms-prod-westus-fd4.documents.azure.com:14940/apps/e41242a5-2d71-5acb-2e00-5e5f744b12de/services/d8aa21a5-340b-21d4-b1a2-4a5333e7ed8a/partitions/ed028254-b613-4c2a-bf3c-14bd5eb64500/replicas/131298754052060051p//', statusCode=410, message=Message: The requested resource is no longer available at the server., getCauseInfo=[class: class io.netty.channel.ConnectTimeoutException, message: connection timed out: cdb-ms-prod-westus-fd4.documents.azure.com/101.13.12.5:14940]
```

Jeśli na maszynie aplikacji jest uruchomiona Zapora, Otwórz zakres portów od 10 000 do 20 000, który jest używany przez tryb bezpośredni.
Należy również postępować zgodnie z [limitem połączeń na komputerze-hoście](#connection-limit-on-host).

#### <a name="http-proxy"></a>Serwer proxy HTTP

W przypadku korzystania z serwera proxy HTTP upewnij się, że może on obsługiwać liczbę połączeń skonfigurowanych w zestawie SDK `ConnectionPolicy` .
W przeciwnym razie nastąpiły problemy z połączeniem.

#### <a name="invalid-coding-pattern-blocking-netty-io-thread"></a>Nieprawidłowy wzorzec kodowania: blokowanie wielosieciowego wątku we/wy

Zestaw SDK używa biblioteki [we/wy w celu](https://netty.io/) komunikowania się z Azure Cosmos DB. Zestaw SDK ma asynchroniczne interfejsy API i używa nieblokowanych interfejsów API we/wy z sieci. Operacja we/wy zestawu SDK jest wykonywana w wątkach z dyskami we/wy. Liczba wątków sieci we/wy jest skonfigurowana tak samo jak liczba rdzeni procesora CPU maszyny aplikacji. 

Wątki we/wy są przeznaczone do użycia tylko dla nieblokujących sieci we/wy. Zestaw SDK zwraca wynik wywołania interfejsu API na jednym z wątków we/wy do kodu aplikacji. Jeśli aplikacja wykonuje długotrwałą operację po odebraniu wyników w wątku sieci, zestaw SDK może nie mieć wystarczającej liczby wątków we/wy do wykonania wewnętrznej operacji we/wy. Takie kodowanie aplikacji może spowodować niską przepływność, duże opóźnienia i `io.netty.handler.timeout.ReadTimeoutException` niepowodzenia. Obejście polega na przełączeniu wątku, gdy wiadomo, że operacja trwa.

Na przykład zapoznaj się z poniższym fragmentem kodu. Można wykonywać długotrwałe zadania, które trwają więcej niż kilka milisekund w wątku. Jeśli tak, możesz przejść do stanu, w którym nie ma żadnego wątku we/wy w celu przetworzenia operacji we/wy. W związku z tym zostanie wyświetlony błąd ReadTimeoutException.

### <a name="async-java-sdk-v2-maven-commicrosoftazureazure-cosmosdb"></a><a id="asyncjava2-readtimeout"></a>Async Java SDK V2 (Maven com. Microsoft. Azure:: Azure-cosmosdb)

```java
@Test
public void badCodeWithReadTimeoutException() throws Exception {
    int requestTimeoutInSeconds = 10;

    ConnectionPolicy policy = new ConnectionPolicy();
    policy.setRequestTimeoutInMillis(requestTimeoutInSeconds * 1000);

    AsyncDocumentClient testClient = new AsyncDocumentClient.Builder()
            .withServiceEndpoint(TestConfigurations.HOST)
            .withMasterKeyOrResourceToken(TestConfigurations.MASTER_KEY)
            .withConnectionPolicy(policy)
            .build();

    int numberOfCpuCores = Runtime.getRuntime().availableProcessors();
    int numberOfConcurrentWork = numberOfCpuCores + 1;
    CountDownLatch latch = new CountDownLatch(numberOfConcurrentWork);
    AtomicInteger failureCount = new AtomicInteger();

    for (int i = 0; i < numberOfConcurrentWork; i++) {
        Document docDefinition = getDocumentDefinition();
        Observable<ResourceResponse<Document>> createObservable = testClient
                .createDocument(getCollectionLink(), docDefinition, null, false);
        createObservable.subscribe(r -> {
                    try {
                        // Time-consuming work is, for example,
                        // writing to a file, computationally heavy work, or just sleep.
                        // Basically, it's anything that takes more than a few milliseconds.
                        // Doing such operations on the IO Netty thread
                        // without a proper scheduler will cause problems.
                        // The subscriber will get a ReadTimeoutException failure.
                        TimeUnit.SECONDS.sleep(2 * requestTimeoutInSeconds);
                    } catch (Exception e) {
                    }
                },

                exception -> {
                    //It will be io.netty.handler.timeout.ReadTimeoutException.
                    exception.printStackTrace();
                    failureCount.incrementAndGet();
                    latch.countDown();
                },
                () -> {
                    latch.countDown();
                });
    }

    latch.await();
    assertThat(failureCount.get()).isGreaterThan(0);
}
```
Obejście polega na zmianie wątku, w którym wykonywane są zadania. Zdefiniuj pojedyncze wystąpienie harmonogramu dla swojej aplikacji.

### <a name="async-java-sdk-v2-maven-commicrosoftazureazure-cosmosdb"></a><a id="asyncjava2-scheduler"></a>Async Java SDK V2 (Maven com. Microsoft. Azure:: Azure-cosmosdb)

```java
// Have a singleton instance of an executor and a scheduler.
ExecutorService ex  = Executors.newFixedThreadPool(30);
Scheduler customScheduler = rx.schedulers.Schedulers.from(ex);
```
Może być konieczne wykonanie zadań, które zajmują dużo czasu, na przykład obliczeniowe duże ilości pracy lub blokowanie operacji we/wy. W takim przypadku należy przełączyć wątek do procesu roboczego dostarczonego przez użytkownika przy `customScheduler` użyciu `.observeOn(customScheduler)` interfejsu API.

### <a name="async-java-sdk-v2-maven-commicrosoftazureazure-cosmosdb"></a><a id="asyncjava2-applycustomscheduler"></a>Async Java SDK V2 (Maven com. Microsoft. Azure:: Azure-cosmosdb)

```java
Observable<ResourceResponse<Document>> createObservable = client
        .createDocument(getCollectionLink(), docDefinition, null, false);

createObservable
        .observeOn(customScheduler) // Switches the thread.
        .subscribe(
            // ...
        );
```
Przy użyciu `observeOn(customScheduler)` , należy wydać wątek we/wy na dysku i przełączyć się na własny niestandardowy wątek udostępniony przez niestandardowy harmonogram. Ta modyfikacja rozwiązuje problem. Błąd nie zostanie `io.netty.handler.timeout.ReadTimeoutException` już wyświetlony.

### <a name="connection-pool-exhausted-issue"></a>Problem z wyczerpaniem puli połączeń

`PoolExhaustedException` to błąd po stronie klienta. Ten błąd wskazuje, że obciążenie aplikacji jest większe niż to, co może obsłużyć pula połączeń zestawu SDK. Zwiększ rozmiar puli połączeń lub Rozpowszechnij obciążenie w wielu aplikacjach.

### <a name="request-rate-too-large"></a>Zbyt duży współczynnik żądań
Ten błąd jest niepowodzeniem po stronie serwera. Oznacza to, że wykorzystano zainicjowaną przepływność. Spróbuj ponownie później. Jeśli ten błąd występuje często, należy rozważyć zwiększenie przepływności kolekcji.

### <a name="failure-connecting-to-azure-cosmos-db-emulator"></a>Nie można nawiązać połączenia z emulatorem Azure Cosmos DB

Certyfikat HTTPS emulatora Azure Cosmos DB jest podpisany z podpisem własnym. Aby zestaw SDK działał z emulatorem, zaimportuj certyfikat emulatora do TrustStore języka Java. Aby uzyskać więcej informacji, zobacz [eksportowanie Azure Cosmos DB emulatora certyfikatów](local-emulator-export-ssl-certificates.md).

### <a name="dependency-conflict-issues"></a>Problemy z konfliktami zależności

```console
Exception in thread "main" java.lang.NoSuchMethodError: rx.Observable.toSingle()Lrx/Single;
```

Powyższy wyjątek sugeruje, że masz zależność od starszej wersji biblioteki RxJava (np. 1.2.2). Nasze zestawy SDK opierają się na RxJava 1.3.8, która ma interfejsy API niedostępne w starszej wersji programu RxJava. 

Obejście tych problemów polega na zidentyfikowaniu, która inna zależność jest RxJava-1.2.2 i wykluczać zależność przechodnią w RxJava-1.2.2 i Zezwalanie na CosmosDB zestawu SDK.

Aby określić, która biblioteka znajduje się w RxJava-1.2.2, uruchom następujące polecenie obok pliku pom.xml projektu:
```bash
mvn dependency:tree
```
Aby uzyskać więcej informacji, zobacz [Przewodnik drzewa zależności Maven](https://maven.apache.org/plugins/maven-dependency-plugin/examples/resolving-conflicts-using-the-dependency-tree.html).

Po zidentyfikowaniu RxJava-1.2.2 jest zależność przechodnia, dla której jest inna zależność projektu, można zmodyfikować zależność od tej biblioteki w pliku pliku pom i wykluczyć RxJava przechodniej zależności:

```xml
<dependency>
  <groupId>${groupid-of-lib-which-brings-in-rxjava1.2.2}</groupId>
  <artifactId>${artifactId-of-lib-which-brings-in-rxjava1.2.2}</artifactId>
  <version>${version-of-lib-which-brings-in-rxjava1.2.2}</version>
  <exclusions>
    <exclusion>
      <groupId>io.reactivex</groupId>
      <artifactId>rxjava</artifactId>
    </exclusion>
  </exclusions>
</dependency>
```

Aby uzyskać więcej informacji, zobacz [Przewodnik dotyczący wykluczania zależności przechodniej](https://maven.apache.org/guides/introduction/introduction-to-optional-and-excludes-dependencies.html).


## <a name="enable-client-sdk-logging"></a><a name="enable-client-sice-logging"></a>Włącz rejestrowanie zestawu SDK klienta

Asynchroniczny zestaw SDK Java używa SLF4j jako elewacji rejestrowania, która obsługuje logowanie do popularnych struktur rejestrowania, takich jak Log4J i logback.

Na przykład jeśli chcesz użyć Log4J jako struktury rejestrowania, Dodaj następujący libs w ścieżce klasy Java.

```xml
<dependency>
  <groupId>org.slf4j</groupId>
  <artifactId>slf4j-log4j12</artifactId>
  <version>${slf4j.version}</version>
</dependency>
<dependency>
  <groupId>log4j</groupId>
  <artifactId>log4j</artifactId>
  <version>${log4j.version}</version>
</dependency>
```

Dodaj również konfigurację Log4J.
```
# this is a sample log4j configuration

# Set root logger level to DEBUG and its only appender to A1.
log4j.rootLogger=INFO, A1

log4j.category.com.microsoft.azure.cosmosdb=DEBUG
#log4j.category.io.netty=INFO
#log4j.category.io.reactivex=INFO
# A1 is set to be a ConsoleAppender.
log4j.appender.A1=org.apache.log4j.ConsoleAppender

# A1 uses PatternLayout.
log4j.appender.A1.layout=org.apache.log4j.PatternLayout
log4j.appender.A1.layout.ConversionPattern=%d %5X{pid} [%t] %-5p %c - %m%n
```

Aby uzyskać więcej informacji, zobacz [Podręcznik rejestrowania sfl4j](https://www.slf4j.org/manual.html).

## <a name="os-network-statistics"></a><a name="netstats"></a>Statystyka sieci systemu operacyjnego
Uruchom polecenie netstat, aby poznać liczbę połączeń w Stanach, takich jak `ESTABLISHED` i `CLOSE_WAIT` .

W systemie Linux można uruchomić następujące polecenie.
```bash
netstat -nap
```
Przefiltruj wynik wyłącznie do połączeń z punktem końcowym Azure Cosmos DB.

Liczba połączeń z punktem końcowym Azure Cosmos DB w `ESTABLISHED` stanie nie może być większa niż skonfigurowany rozmiar puli połączeń.

Wiele połączeń z punktem końcowym Azure Cosmos DB może być w `CLOSE_WAIT` stanie. Może być większa niż 1 000. Duża liczba wskazuje, że połączenia są nawiązywane i szybko podłączane. Ta sytuacja może powodować problemy. Aby uzyskać więcej informacji, zobacz sekcję [typowe problemy i rozwiązania] .

 <!--Anchors-->
[Typowe problemy i ich rozwiązania]: #common-issues-workarounds
[Enable client SDK logging]: #enable-client-sice-logging
[Limit połączeń na komputerze hosta]: #connection-limit-on-host
[Wyczerpanie portów (z) na platformie Azure]: #snat