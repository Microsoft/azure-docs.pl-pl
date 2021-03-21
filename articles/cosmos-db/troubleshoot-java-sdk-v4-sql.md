---
title: Diagnozowanie i rozwiązywanie problemów Azure Cosmos DB Java SDK v4
description: Korzystaj z funkcji, takich jak rejestrowanie po stronie klienta i innych narzędzi innych firm, aby identyfikować, diagnozować i rozwiązywać problemy Azure Cosmos DB w zestawie Java SDK v4.
author: anfeldma-ms
ms.service: cosmos-db
ms.date: 06/11/2020
ms.author: anfeldma
ms.devlang: java
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.custom: devx-track-java
ms.openlocfilehash: cba8b97adb40ca2c277268188ff6ad541c7e9676
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100596463"
---
# <a name="troubleshoot-issues-when-you-use-azure-cosmos-db-java-sdk-v4-with-sql-api-accounts"></a>Rozwiązywanie problemów podczas korzystania z Azure Cosmos DB Java SDK v4 z kontami interfejsu API SQL
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [Java SDK 4](troubleshoot-java-sdk-v4-sql.md)
> * [Async Java SDK 2](troubleshoot-java-async-sdk.md)
> * [.NET](troubleshoot-dot-net-sdk.md)
> 

> [!IMPORTANT]
> W tym artykule omówiono Rozwiązywanie problemów dotyczących tylko Azure Cosmos DB Java SDK v4. Aby uzyskać więcej informacji, zobacz informacje o [wersji](sql-api-sdk-java-v4.md)programu Azure Cosmos DB Java SDK v4, [repozytorium Maven](https://mvnrepository.com/artifact/com.azure/azure-cosmos)oraz [porady dotyczące wydajności](performance-tips-java-sdk-v4-sql.md) . Jeśli obecnie używasz starszej wersji niż v4, zapoznaj się z przewodnikiem [Migrowanie do Azure Cosmos DB Java SDK v4](migrate-java-v4-sdk.md) , aby uzyskać pomoc w uaktualnianiu do wersji v4.
>

W tym artykule opisano typowe problemy, obejścia, kroki diagnostyczne i narzędzia używane w programie Azure Cosmos DB Java SDK v4 z Azure Cosmos DB kontami interfejsu API SQL.
Azure Cosmos DB Java SDK v4 zapewnia logiczną reprezentację po stronie klienta, aby uzyskać dostęp do Azure Cosmos DB interfejsu API SQL. W tym artykule opisano narzędzia i podejścia pomocne w przypadku napotkania jakichkolwiek problemów.

Rozpocznij od tej listy:

* Zapoznaj się z sekcją [typowe problemy i obejścia] w tym artykule.
* Zapoznaj się z zestawem SDK języka Java w Azure Cosmos DB centralnym repozytorium, które jest dostępne jako [Open Source w serwisie GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-cosmos). Zawiera [sekcję problemy](https://github.com/Azure/azure-sdk-for-java/issues) , która jest aktywnie monitorowana. Sprawdź, czy podobny problem z obejściem został już zgłoszony. Jedną z przydatnych porad jest filtrowanie problemów przez tag *Cosmos: v4-Item* .
* Zapoznaj się z [poradami dotyczącymi wydajności](performance-tips-java-sdk-v4-sql.md) Azure Cosmos DB Java SDK v4 i postępuj zgodnie z zaleceniami.
* Zapoznaj się z resztą tego artykułu, jeśli nie odnaleziono rozwiązania. Następnie należy [rozwiązać problem](https://github.com/Azure/azure-sdk-for-java/issues)z usługą GitHub. Jeśli jest dostępna opcja dodania tagów do problemu w usłudze GitHub, Dodaj tag *Cosmos: v4-Item* .

### <a name="retry-logic"></a>Logika ponawiania <a id="retry-logics"></a>
Zestaw Cosmos DB SDK w przypadku dowolnego błędy we/wy podejmie próbę ponowienia operacji zakończonej niepowodzeniem, jeśli jest możliwe jej ponowienie. Ponowienie próby w przypadku jakiegokolwiek błędu jest dobrym sposobem, ale w przypadku niepowodzenia przetwarzania/ponawiania próby zapisu jest to konieczne. Zalecane jest korzystanie z najnowszego zestawu SDK, ponieważ logika ponowień jest ciągle ulepszana.

1. Błędy we/wy odczytu i zapytania zostaną ponowione przez zestaw SDK bez obsłużynia ich użytkownikowi końcowemu.
2. Operacje zapisu (Create, Upsert, Replace, Delete) nie są idempotentne, a tym samym zestaw SDK nie zawsze może niemniej ponowić próbę wykonania nieudanych operacji zapisywania. Wymagane jest, aby logika aplikacji użytkownika mogła obsłużyć błąd, i ponowić próbę.
3. Problemy związane z uzyskaniem [dostępności zestawu SDK](troubleshoot-sdk-availability.md) objaśniają ponawianie prób dla wieloregionowych kont Cosmos DB.

## <a name="common-issues-and-workarounds"></a><a name="common-issues-workarounds"></a>Typowe problemy i ich rozwiązania

### <a name="network-issues-netty-read-timeout-failure-low-throughput-high-latency"></a>Problemy z siecią, Niepowodzenie przekroczenia limitu czasu odczytu z sieci, niska przepływność, duże opóźnienie

#### <a name="general-suggestions"></a>Ogólne sugestie
W celu uzyskania najlepszej wydajności:
* Upewnij się, że aplikacja działa w tym samym regionie co konto Azure Cosmos DB. 
* Sprawdź użycie procesora na hoście, na którym działa aplikacja. Jeśli użycie procesora CPU wynosi 50% lub więcej, uruchom aplikację na hoście o wyższej konfiguracji. Można też rozłożyć obciążenie na więcej maszyn.
    * Jeśli uruchamiasz aplikację w usłudze Azure Kubernetes, możesz [użyć Azure monitor do monitorowania użycia procesora CPU](../azure-monitor/containers/container-insights-analyze.md).

#### <a name="connection-throttling"></a>Ograniczanie połączeń
Możliwe jest ograniczenie połączenia z powodu [limitu połączeń na komputerze hosta lub w] [wyczerpaniu portów usługi Azure translator adresów sieciowych].

##### <a name="connection-limit-on-a-host-machine"></a><a name="connection-limit-on-host"></a>Limit połączeń na komputerze hosta
Niektóre systemy Linux, takie jak Red Hat, mają górny limit łącznej liczby otwartych plików. Gniazda w systemie Linux są zaimplementowane jako pliki, więc ta liczba ogranicza łączną liczbę połączeń.
Uruchom następujące polecenie.

```bash
ulimit -a
```
Maksymalna dozwolona liczba otwartych plików, które są identyfikowane jako "nofile", musi mieć co najmniej dwa razy więcej rozmiaru puli połączeń. Aby uzyskać więcej informacji, zapoznaj się ze [wskazówkami dotyczącymi wydajności](performance-tips-java-sdk-v4-sql.md)Azure Cosmos DB Java SDK v4.

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

Zestaw SDK używa biblioteki [we/wy w celu](https://netty.io/) komunikowania się z Azure Cosmos DB. Zestaw SDK zawiera asynchroniczny interfejs API i używa nieblokowanych interfejsów API we/wy z sieci. Operacja we/wy zestawu SDK jest wykonywana w wątkach z dyskami we/wy. Liczba wątków sieci we/wy jest skonfigurowana tak samo jak liczba rdzeni procesora CPU maszyny aplikacji. 

Wątki we/wy są przeznaczone do użycia tylko dla nieblokujących sieci we/wy. Zestaw SDK zwraca wynik wywołania interfejsu API na jednym z wątków we/wy do kodu aplikacji. Jeśli aplikacja wykonuje długotrwałą operację po odebraniu wyników w wątku sieci, zestaw SDK może nie mieć wystarczającej liczby wątków we/wy do wykonania wewnętrznej operacji we/wy. Takie kodowanie aplikacji może spowodować niską przepływność, duże opóźnienia i `io.netty.handler.timeout.ReadTimeoutException` niepowodzenia. Obejście polega na przełączeniu wątku, gdy wiadomo, że operacja trwa.

Na przykład zapoznaj się z poniższym fragmentem kodu, który dodaje elementy do kontenera (patrz [tutaj](create-sql-api-java.md) , aby uzyskać wskazówki dotyczące konfigurowania bazy danych i kontenera). Można wykonywać długotrwałe zadania, które trwają więcej niż kilka milisekund w wątku. Jeśli tak, możesz przejść do stanu, w którym nie ma żadnego wątku we/wy w celu przetworzenia operacji we/wy. W związku z tym zostanie wyświetlony błąd ReadTimeoutException.

### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-readtimeout"></a>Java SDK v4 (Maven com. Azure:: Azure-Cosmos) Async API

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=TroubleshootNeedsSchedulerAsync)]

Obejście polega na zmianie wątku, w którym wykonywane są zadania. Zdefiniuj pojedyncze wystąpienie harmonogramu dla swojej aplikacji.

### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-scheduler"></a>Java SDK v4 (Maven com. Azure:: Azure-Cosmos) Async API

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=TroubleshootCustomSchedulerAsync)]

Może być konieczne wykonanie zadań, które zajmują dużo czasu, na przykład obliczeniowe duże ilości pracy lub blokowanie operacji we/wy. W takim przypadku należy przełączyć wątek do procesu roboczego dostarczonego przez użytkownika przy `customScheduler` użyciu `.publishOn(customScheduler)` interfejsu API.

### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-apply-custom-scheduler"></a>Java SDK v4 (Maven com. Azure:: Azure-Cosmos) Async API

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=TroubleshootPublishOnSchedulerAsync)]

Przy użyciu `publishOn(customScheduler)` , należy wydać wątek we/wy na dysku i przełączyć się na własny niestandardowy wątek udostępniony przez niestandardowy harmonogram. Ta modyfikacja rozwiązuje problem. Błąd nie zostanie `io.netty.handler.timeout.ReadTimeoutException` już wyświetlony.

### <a name="request-rate-too-large"></a>Zbyt duży współczynnik żądań
Ten błąd jest niepowodzeniem po stronie serwera. Oznacza to, że wykorzystano zainicjowaną przepływność. Spróbuj ponownie później. Jeśli ten błąd występuje często, należy rozważyć zwiększenie przepływności kolekcji.

* **Implementowanie wycofywania w interwałach getRetryAfterInMilliseconds**

    Podczas testowania wydajności należy zwiększyć obciążenie, dopóki nie zostanie ograniczona niewielka liczba żądań. W przypadku ograniczenia przepustowości aplikacja kliencka powinna wycofywania dla interwału ponawiania określonych przez serwer. Poszanowanie wycofywania gwarantuje, że spędzasz minimalny czas oczekiwania między ponownymi próbami.

### <a name="failure-connecting-to-azure-cosmos-db-emulator"></a>Nie można nawiązać połączenia z emulatorem Azure Cosmos DB

Certyfikat HTTPS emulatora Azure Cosmos DB jest podpisany z podpisem własnym. Aby zestaw SDK działał z emulatorem, zaimportuj certyfikat emulatora do TrustStore języka Java. Aby uzyskać więcej informacji, zobacz [eksportowanie Azure Cosmos DB emulatora certyfikatów](local-emulator-export-ssl-certificates.md).

### <a name="dependency-conflict-issues"></a>Problemy z konfliktami zależności

Zestaw SDK języka Java Azure Cosmos DB pobiera wiele zależności; Ogólnie mówiąc, jeśli drzewo zależności projektu zawiera starszą wersję artefaktu, od którego zależy Azure Cosmos DB Java SDK, może to spowodować wygenerowanie nieoczekiwanych błędów podczas uruchamiania aplikacji. W przypadku debugowania, Dlaczego aplikacja nieoczekiwanie zgłasza wyjątek, dobrym pomysłem jest podwójne sprawdzenie, czy drzewo zależności nie zostanie przypadkowo pobrane w starszej wersji co najmniej jednej Azure Cosmos DB zależności zestawu SDK języka Java.

Obejście tego problemu polega na zidentyfikowaniu, które zależności projektu są w starej wersji i wykluczeniu zależności przechodniej w tej starszej wersji i umożliwieniu Azure Cosmos DBm zestawu Java SDK do przyłączenia do nowszej wersji.

Aby określić, które zależności projektu są zgodne ze starszą wersją elementu, od którego zależy Azure Cosmos DB Java SDK, uruchom następujące polecenie względem pliku pom.xml projektu:
```bash
mvn dependency:tree
```
Aby uzyskać więcej informacji, zobacz [Przewodnik drzewa zależności Maven](https://maven.apache.org/plugins/maven-dependency-plugin/examples/resolving-conflicts-using-the-dependency-tree.html).

Gdy wiadomo, która zależność projektu jest zależna od starszej wersji, można zmodyfikować zależność od tej biblioteki w pliku pliku pom i wykluczyć zależność przechodnią, postępując zgodnie z poniższym przykładem (w tym przypadku, gdy replika *-rdzeń* jest nieaktualną zależnością):

```xml
<dependency>
  <groupId>${groupid-of-lib-which-brings-in-reactor}</groupId>
  <artifactId>${artifactId-of-lib-which-brings-in-reactor}</artifactId>
  <version>${version-of-lib-which-brings-in-reactor}</version>
  <exclusions>
    <exclusion>
      <groupId>io.projectreactor</groupId>
      <artifactId>reactor-core</artifactId>
    </exclusion>
  </exclusions>
</dependency>
```

Aby uzyskać więcej informacji, zobacz [Przewodnik dotyczący wykluczania zależności przechodniej](https://maven.apache.org/guides/introduction/introduction-to-optional-and-excludes-dependencies.html).


## <a name="enable-client-sdk-logging"></a><a name="enable-client-sice-logging"></a>Włącz rejestrowanie zestawu SDK klienta

Azure Cosmos DB Java SDK v4 używa SLF4j jako elewacji rejestrowania, która obsługuje logowanie do popularnych struktur rejestrowania, takich jak Log4J i logback.

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

# Set root logger level to INFO and its only appender to A1.
log4j.rootLogger=INFO, A1

log4j.category.com.azure.cosmos=INFO
#log4j.category.io.netty=OFF
#log4j.category.io.projectreactor=OFF
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

W systemie Windows można uruchomić to samo polecenie z innymi flagami argumentów:
```bash
netstat -abn
```

Przefiltruj wynik wyłącznie do połączeń z punktem końcowym Azure Cosmos DB.

Liczba połączeń z punktem końcowym Azure Cosmos DB w `ESTABLISHED` stanie nie może być większa niż skonfigurowany rozmiar puli połączeń.

Wiele połączeń z punktem końcowym Azure Cosmos DB może być w `CLOSE_WAIT` stanie. Może być większa niż 1 000. Duża liczba wskazuje, że połączenia są nawiązywane i szybko podłączane. Ta sytuacja może powodować problemy. Aby uzyskać więcej informacji, zobacz sekcję [typowe problemy i rozwiązania] .

 <!--Anchors-->
[Typowe problemy i ich rozwiązania]: #common-issues-workarounds
[Enable client SDK logging]: #enable-client-sice-logging
[Limit połączeń na komputerze hosta]: #connection-limit-on-host
[Wyczerpanie portów (z) na platformie Azure]: #snat
