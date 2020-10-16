---
title: Funkcje bazy danych Apache Cassandra obsługiwane przez interfejs API Cassandra usługi Azure Cosmos DB
description: Dowiedz się więcej o obsłudze funkcji bazy danych Apache Cassandra w interfejsie API Cassandra usługi Azure Cosmos DB
author: TheovanKraay
ms.author: thvankra
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: overview
ms.date: 09/14/2020
ms.openlocfilehash: f9d472b94b5490d00eac6d160af40c61d547534b
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2020
ms.locfileid: "92107491"
---
# <a name="apache-cassandra-features-supported-by-azure-cosmos-db-cassandra-api"></a>Funkcje bazy danych Apache Cassandra obsługiwane przez interfejs API Cassandra usługi Azure Cosmos DB 

Azure Cosmos DB to rozproszona globalnie, wielomodelowa usługa bazy danych firmy Microsoft. Można komunikować się z Azure Cosmos DB interfejs API Cassandra za pomocą [sterowników](https://cassandra.apache.org/doc/latest/getting_started/drivers.html?highlight=driver)klienta CQL "open source" [w protokole](https://github.com/apache/cassandra/blob/trunk/doc/native_protocol_v4.spec) binarnym protokołu Cassandra. 

Dzięki interfejsowi API Cassandra usługi Azure Cosmos DB możesz korzystać z interfejsów API Apache Cassandra, a także możliwości klasy przedsiębiorstwa zapewnianymi przez usługę Azure Cosmos DB. Możliwości klasy przedsiębiorstwa obejmują [globalną dystrybucję](distribute-data-globally.md), [automatyczne skalowanie partycjonowania w poziomie](cassandra-partitioning.md), gwarancje dostępności i opóźnień, szyfrowanie nieużywanych danych, kopie zapasowe i wiele więcej.

## <a name="cassandra-protocol"></a>Protokół Cassandra 

Interfejs API Cassandra Azure Cosmos DB jest zgodna z interfejsem API Cassandra Query Language (CQL) v 3.11 (Wstecz-Compatible z wersją 2. x). Obsługiwane polecenia CQL, narzędzia, ograniczenia i wyjątki są wymienione poniżej. Dowolny sterownik klienta działający zgodnie z tymi protokołami umożliwia połączenie z interfejsem API Cassandra usługi Azure Cosmos DB.

## <a name="cassandra-driver"></a>Sterownik Cassandra

Następujące wersje sterowników Cassandra są obsługiwane przez interfejs API Cassandra usługi Azure Cosmos DB:

* [Java 3.5+](https://github.com/datastax/java-driver)  
* [C# 3.5+](https://github.com/datastax/csharp-driver)  
* [Nodejs 3.5+](https://github.com/datastax/nodejs-driver)  
* [Python 3.15+](https://github.com/datastax/python-driver)  
* [C++ 2.9](https://github.com/datastax/cpp-driver)   
* [PHP 1.3](https://github.com/datastax/php-driver)  
* [Gocql](https://github.com/gocql/gocql)  
 

## <a name="cql-data-types"></a>Typy danych CQL 

Interfejs API Cassandra usługi Azure Cosmos DB obsługuje następujące typy danych języka CQL:

|Polecenie  |Obsługiwane |
|---------|---------|
| ascii  | Tak |
| bigint  | Tak |
| blob  | Tak |
| boolean  | Tak |
| counter  | Tak |
| data  | Tak |
| decimal  | Tak |
| double  | Tak |
| float  | Tak |
| frozen  | Tak |
| inet  | Tak |
| int  | Tak |
| list  | Tak |
| set  | Tak |
| smallint  | Tak |
| tekst  | Tak |
| time  | Tak |
| sygnatura czasowa  | Tak |
| timeuuid  | Tak |
| tinyint  | Tak |
| tuple  | Tak |
| uuid  | Tak |
| varchar  | Tak |
| varint  | Tak |
| tuples | Tak | 
| udts  | Tak |
| map (mapa) | Tak |

Statyczny jest obsługiwany dla deklaracji typu danych.

## <a name="cql-functions"></a>Funkcje języka CQL

Interfejs API Cassandra usługi Azure Cosmos DB obsługuje następujące funkcje języka CQL:

|Polecenie  |Obsługiwane |
|---------|---------|
| Klucza | Tak |
| czas wygaśnięcia | Tak |
| writetime | Tak |
| rzutowanie | Nie |

\* Interfejs API Cassandra obsługuje token jako projekcję/selektor i zezwala tylko na token (PK) po lewej stronie klauzuli WHERE. Na przykład `WHERE token(pk) > 1024` jest obsługiwany, ale `WHERE token(pk) > token(100)` nie jest obsługiwany.


Funkcje agregujące:

|Polecenie  |Obsługiwane |
|---------|---------|
| min | Tak |
| max | Tak |
| śr | Tak |
| count | Tak |

Funkcje konwersji obiektów blob:
 
|Polecenie  |Obsługiwane |
|---------|---------|
| typeAsBlob(value)   | Tak |
| blobAsType(value) | Tak |


Funkcje UUID i timeuuid:
 
|Polecenie  |Obsługiwane |
|---------|---------|
| dateOf()  | Tak |
| now()  | Tak |
| minTimeuuid()  | Tak |
| unixTimestampOf()  | Tak |
| toDate(timeuuid)  | Tak |
| toTimestamp(timeuuid)  | Tak |
| toUnixTimestamp(timeuuid)  | Tak |
| toDate(timeuuid)  | Tak |
| toUnixTimestamp(timestamp)  | Tak |
| toTimestamp(date)  | Tak |
| toUnixTimestamp(date) | Tak |


  
## <a name="cql-commands"></a>Polecenie języka CQL

Usługa Azure Cosmos DB obsługuje następujące polecenia bazy danych na kontach interfejsu API Cassandra.

|Polecenie  |Obsługiwane |
|---------|---------|
| ZEZWALAJ NA FILTROWANIE | Tak |
| ZMIEŃ PRZESTRZEŃ KLUCZY | Nie dotyczy (usługa PaaS, zarządzane wewnętrznie replikację)|
| ZMIEŃ WIDOK Z MATERIAŁAMI | Nie |
| ZMIEŃ ROLĘ | Nie |
| ALTER TABLE | Tak |
| ZMIEŃ TYP | Nie |
| ZMIEŃ UŻYTKOWNIKA | Nie |
| Sekwencja | Tak (tylko nierejestrowana partia)|
| MAGAZYN KOMPAKTOWY | Nie dotyczy (usługa PaaS) |
| UTWÓRZ AGREGACJĘ | Nie | 
| UTWÓRZ INDEKS NIESTANDARDOWY (SASI) | Nie |
| CREATE INDEX | Tak (bez [określania nazwy indeksu](cassandra-secondary-index.md)i indeksy w kluczach klastrowania lub pełna zamrożona kolekcja nie jest obsługiwana) |
| CREATE FUNCTION | Nie |
| Utwórz miejsce na dysku (zignorowano ustawienia replikacji) | Tak |
| UTWÓRZ WIDOK Z MATERIAŁAMI | Nie |
| CREATE TABLE | Tak |
| UTWÓRZ WYZWALACZ | Nie |
| UTWÓRZ TYP | Tak |
| UTWÓRZ ROLĘ | Nie |
| Utwórz użytkownika (przestarzałe w natywnym programie Apache Cassandra) | Nie |
| DELETE | Tak |
| Usuń (lekkie transakcje z WARUNKIem IF)| Tak |
| DISTINCT | Nie |
| UPUŚĆ WARTOŚĆ ZAGREGOWANĄ | Nie |
| DROP FUNCTION | Nie |
| DROP INDEX | Tak |
| PORZUĆ MIEJSCE NA DYSKU | Tak |
| UPUŚĆ WIDOK Z MATERIAŁAMI | Nie |
| USUŃ ROLĘ | Nie |
| DROP TABLE | Tak |
| PORZUĆ WYZWALACZ | Nie | 
| TYP USUWANIA | Tak |
| UPUŚĆ użytkownika (przestarzałe w natywnym programie Apache Cassandra) | Nie |
| GRANT | Nie |
| INSERT | Tak |
| Wstaw (lekkie transakcje z WARUNKIem IF)| Tak |
| WYŚWIETL UPRAWNIENIA | Nie |
| ROLE LIST | Nie |
| Lista użytkowników (przestarzałe w natywnym programie Apache Cassandra) | Nie |
| REVOKE | Nie |
| SELECT | Tak |
| SELECT (lekkie transakcje z WARUNKIem IF)| Nie |
| UPDATE | Tak |
| Aktualizuj (uproszczone transakcje z WARUNKIem IF)| Nie |
| OBCIĄĆ | Nie |
| USE | Tak |

## <a name="json-support"></a>Obsługa JSON
|Polecenie  |Obsługiwane |
|---------|---------|
| WYBIERZ KOD JSON | Tak |
| WSTAW KOD JSON | Tak |
| fromJson() | Nie |
| toJson () | Nie |


## <a name="cassandra-api-limits"></a>Ograniczenia interfejsu API Cassandra

Interfejs API Cassandra usługi Azure Cosmos DB nie ma żadnych ograniczeń dotyczących rozmiaru danych przechowywanych w tabeli. Można przechowywać setki terabajtów lub petabajtów danych przy zapewnieniu uznania limitów klucza partycji. Podobnie każdy odpowiednik jednostki lub wiersza nie ma żadnych limitów liczby kolumn. Jednak łączny rozmiar jednostki nie powinien przekraczać 2 MB. Dane na klucz partycji nie mogą przekroczyć 20 GB, tak jak w przypadku wszystkich innych interfejsów API.

## <a name="tools"></a>Narzędzia 

Interfejs API Cassandra usługi Azure Cosmos DB to platforma usług zarządzanych. Nie wymaga żadnego narzutu związanego z zarządzaniem ani narzędzi, takich jak moduł odzyskiwania pamięci, wirtualna maszyna Java (JVM) i narzędzie nodetool do zarządzania klastrem. Obsługuje narzędzia, takie jak cqlsh, korzystające ze zgodności binarnej z językiem CQL w wersji 4. 

* Azure Portal Eksplorator danych, metryki, Diagnostyka dzienników, program PowerShell i interfejs wiersza polecenia są innymi obsługiwanymi mechanizmami do zarządzania kontem.

## <a name="hosted-cql-shell-preview"></a>Hostowana powłoka CQL (wersja zapoznawcza)

Hostowaną natywną powłokę Cassandra (CQLSH v 5.0.1) można otworzyć bezpośrednio z Eksplorator danych w [Azure Portal](data-explorer.md) lub w [Eksploratorze Cosmos platformy Azure](https://cosmos.azure.com/). Przed włączeniem powłoki CQL należy [włączyć funkcję notesów](enable-notebooks.md) na koncie (jeśli nie została jeszcze włączona, zostanie wyświetlony monit po kliknięciu `Open Cassandra Shell` ). Zapoznaj się z wyróżnioną uwagą w temacie [Włączanie notesów dla kont Azure Cosmos DB](enable-notebooks.md) dla obsługiwanych regionów świadczenia usługi Azure.

:::image type="content" source="./media/cassandra-support/cqlsh.png" alt-text="Otwórz CQLSH&quot;:::

Możesz również nawiązać połączenie z interfejs API Cassandra w Azure Cosmos DB przy użyciu CQLSH zainstalowanego na komputerze lokalnym. Jest on dostarczany z Apache Cassandra 3.1.1 i działa poza ramką przez ustawienie zmiennych środowiskowych. Poniższe sekcje zawierają instrukcje dotyczące instalowania, konfigurowania i nawiązywania połączeń interfejs API Cassandra w programie Azure Cosmos DB w systemie Windows lub Linux przy użyciu CQLSH.

> [!NOTE]
> Połączenia z usługą Azure Cosmos DB interfejs API Cassandra nie będą działały z wersjami CQLSH firmy DataStax Enterprise (DSE). Upewnij się, że podczas nawiązywania połączenia z usługą interfejs API Cassandra używasz tylko wersji CQLSH typu open source systemu Apache Cassandra. 

**Systemy**

W przypadku korzystania z systemu Windows zaleca się włączenie [systemu Windows w systemie Linux](https://docs.microsoft.com/windows/wsl/install-win10#install-the-windows-subsystem-for-linux). Następnie możesz użyć poniższych poleceń systemu Linux.

**Unix/Linux/Mac:**

```bash
# Install default-jre and default-jdk
sudo apt install default-jre
sudo apt-get update
sudo apt install default-jdk

# Import the Baltimore CyberTrust root certificate:
curl https://cacert.omniroot.com/bc2025.crt > bc2025.crt
keytool -importcert -alias bc2025ca -file bc2025.crt

# Install the Cassandra libraries in order to get CQLSH:
echo &quot;deb http://www.apache.org/dist/cassandra/debian 311x main" | sudo tee -a /etc/apt/sources.list.d/cassandra.sources.list
curl https://downloads.apache.org/cassandra/KEYS | sudo apt-key add -
sudo apt-get update
sudo apt-get install cassandra

# Export the SSL variables:
export SSL_VERSION=TLSv1_2
export SSL_VALIDATE=false

# Connect to Azure Cosmos DB API for Cassandra:
cqlsh <YOUR_ACCOUNT_NAME>.cassandra.cosmosdb.azure.com 10350 -u <YOUR_ACCOUNT_NAME> -p <YOUR_ACCOUNT_PASSWORD> --ssl

```

Wszystkie operacje CRUD, które są wykonywane za pomocą zestawu SDK zgodnego z CQL v4, zwracają dodatkowe informacje na temat liczby użytych jednostek błędu i żądania. Polecenia DELETE i UPDATE powinny być obsługiwane przez zarządzanie zasobami, które są brane pod uwagę w celu zapewnienia najbardziej wydajnego wykorzystania alokowanej przepływności.

* Uwaga: jeśli wartość gc_grace_seconds została określona, musi być równa zero.

```csharp
var tableInsertStatement = table.Insert(sampleEntity); 
var insertResult = await tableInsertStatement.ExecuteAsync(); 
 
foreach (string key in insertResult.Info.IncomingPayload) 
        { 
            byte[] valueInBytes = customPayload[key]; 
            double value = Encoding.UTF8.GetString(valueInBytes); 
            Console.WriteLine($"CustomPayload:  {key}: {value}"); 
        } 
```

## <a name="consistency-mapping"></a>Mapowanie spójności 

Interfejs API Cassandra usługi Azure Cosmos DB zapewnia możliwość wyboru spójności operacji odczytu.  Mapowanie spójności jest szczegółowo opisane w [tym miejscu](consistency-levels-across-apis.md#cassandra-mapping).

## <a name="permission-and-role-management"></a>Zarządzanie uprawnieniami i rolami

Azure Cosmos DB obsługuje kontrolę dostępu opartą na rolach (RBAC) na potrzeby aprowizacji, obracania klawiszy, wyświetlania metryk oraz haseł do odczytu i zapisu oraz kluczy, które można uzyskać za pośrednictwem [Azure Portal](https://portal.azure.com). Azure Cosmos DB nie obsługuje ról dla działań CRUD.

## <a name="keyspace-and-table-options"></a>Opcje przestrzeni kluczy i tabeli

Opcje nazwy regionu, klasy, replication_factor i centrum danych w poleceniu "Utwórz przestrzeń kluczy" są obecnie ignorowane. System używa podstawowej metody replikacji [dystrybucji](global-dist-under-the-hood.md) Azure Cosmos DB, aby dodać regiony. Jeśli potrzebujesz obecności danych między regionami, możesz ją włączyć na poziomie konta przy użyciu programu PowerShell, interfejsu wiersza polecenia lub portalu, aby dowiedzieć się więcej, zobacz artykuł [jak dodać regiony](how-to-manage-database-account.md#addremove-regions-from-your-database-account) . Nie można wyłączyć Durable_writes, ponieważ Azure Cosmos DB gwarantuje, że każdy zapis jest trwały. W każdym regionie Azure Cosmos DB replikuje dane przez zestaw replik składający się z czterech replik i nie można zmodyfikować tej [konfiguracji](global-dist-under-the-hood.md) zestawu replik.
 
Wszystkie opcje są ignorowane podczas tworzenia tabeli, z wyjątkiem gc_grace_seconds, które powinny mieć wartość zero.
Przestrzeń kluczy i tabela mają dodatkową opcję o nazwie "cosmosdb_provisioned_throughput" z minimalną wartością 400 RU/s. Przepływność przestrzeni kluczy umożliwia udostępnianie przepływności w wielu tabelach i jest przydatne w scenariuszach, gdy wszystkie tabele nie korzystają z zainicjowanej przepływności. Polecenie ALTER TABLE umożliwia zmianę zainicjowanej przepływności w regionach. 

```
CREATE  KEYSPACE  sampleks WITH REPLICATION = {  'class' : 'SimpleStrategy'}   AND cosmosdb_provisioned_throughput=2000;  

CREATE TABLE sampleks.t1(user_id int PRIMARY KEY, lastname text) WITH cosmosdb_provisioned_throughput=2000; 

ALTER TABLE gks1.t1 WITH cosmosdb_provisioned_throughput=10000 ;

```
## <a name="secondary-index"></a>Indeks pomocniczy
Interfejs API Cassandra obsługuje indeksy pomocnicze dla wszystkich typów danych, z wyjątkiem zablokowanych typów kolekcji, dziesiętnych i typów wariantów. 

## <a name="usage-of-cassandra-retry-connection-policy"></a>Użycie zasad ponawiania próby połączenia bazy danych Cassandra

Azure Cosmos DB jest systemem zarządzanym przez zasoby. Oznacza to, że można wykonać określoną liczbę operacji w danej sekundzie na podstawie jednostek żądań używanych przez operacje. Jeśli aplikacja przekroczy ten limit w danej sekundzie, żądania są ograniczone, a wyjątki zostaną zgłoszone. Interfejs API Cassandra w Azure Cosmos DB tłumaczy te wyjątki na przeciążone błędy w protokole natywnym Cassandra. Aby zapewnić, że aplikacja może przechwycić i ponowić próbę żądania w przypadku ograniczenia szybkości, dostępne są rozszerzenia [Spark](https://mvnrepository.com/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper) i [Java](https://github.com/Azure/azure-cosmos-cassandra-extensions) . Zobacz również przykłady kodu Java dla sterowników DataStax w [wersji 3](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample) i [4](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample-v4) , podczas nawiązywania połączenia z usługą interfejs API Cassandra w Azure Cosmos DB. Jeśli używasz innych zestawów SDK do uzyskiwania dostępu do interfejs API Cassandra w Azure Cosmos DB, Utwórz zasady połączenia, aby ponowić próbę wykonania tych wyjątków.

## <a name="next-steps"></a>Następne kroki

- Rozpocznij od [utworzenia konta, bazy danych i tabeli interfejsu API Cassandra](create-cassandra-api-account-java.md) przy użyciu aplikacji w języku Java
