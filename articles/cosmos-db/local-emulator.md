---
title: Opracowywanie lokalnie za pomocą emulatora usługi Azure Cosmos
description: Za pomocą emulatora usługi Azure Cosmos można lokalnie opracowywać i testować swoją aplikację bez konieczności tworzenia subskrypcji platformy Azure.
ms.service: cosmos-db
ms.topic: how-to
author: markjbrown
ms.author: mjbrown
ms.date: 08/19/2020
ms.openlocfilehash: 40c32226f0e79e66db45d0c32614eaa4c5b543f9
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2020
ms.locfileid: "88607539"
---
# <a name="use-the-azure-cosmos-emulator-for-local-development-and-testing"></a>Korzystanie z emulatora usługi Azure Cosmos na potrzeby lokalnego tworzenia i testowania

Emulator usługi Azure Cosmos udostępnia środowisko lokalne, które emuluje usługę Azure Cosmos DB na potrzeby programowania. Korzystając z emulatora usługi Azure Cosmos, możesz tworzyć i testować aplikację lokalnie, bez konieczności tworzenia subskrypcji platformy Azure ani ponoszenia kosztów. Gdy aplikacja działa w emulatorze usługi Azure Cosmos, możesz przełączyć się do korzystania z konta usługi Azure Cosmos w chmurze.

Można opracowywać przy użyciu emulatora usługi Azure Cosmos z użyciem kont interfejsu API [SQL](local-emulator.md#sql-api), [Cassandra](local-emulator.md#cassandra-api), [MongoDB](local-emulator.md#azure-cosmos-dbs-api-for-mongodb), [Gremlin](local-emulator.md#gremlin-api)i [tabel](local-emulator.md#table-api) . Jednak w tym momencie widok Eksplorator danych w emulatorze w pełni obsługuje tylko klientów dla interfejsu API SQL. 

## <a name="how-the-emulator-works"></a>Jak działa emulator

Emulator Azure Cosmos zapewnia emulację o wysokiej dokładności usługi Azure Cosmos DB. Obsługuje ona takie same funkcje jak Azure Cosmos DB, w tym obsługa tworzenia i wykonywania zapytań dotyczących danych, inicjowania obsługi i skalowania kontenerów oraz wykonywania procedur składowanych i wyzwalaczy. Możesz tworzyć i testować aplikacje za pomocą emulatora usługi Azure Cosmos, a następnie wdrażać je na platformie Azure na skalę globalną, wprowadzając jedną zmianę konfiguracji do punktu końcowego połączenia dla Azure Cosmos DB.

Chociaż emulator dokładnie odzwierciedla działanie usługi Azure Cosmos DB, różni się od niej pod względem wdrożenia. Na przykład emulator używa standardowych składników systemu operacyjnego — między innymi lokalnego systemu plików jako magazynu trwałego i stosu protokołu HTTPS na potrzeby łączności. Funkcje, które opierają się na infrastrukturze platformy Azure, takiej jak replikacja globalna, opóźnienie w milisekundach jednocyfrowych dla operacji odczytu/zapisu i nie mają zastosowania poziomy spójności możliwość dostosowania.

Dane można migrować między emulatorem usługi Azure Cosmos i usługą Azure Cosmos DB przy użyciu narzędzia do [migracji danych Azure Cosmos DB](https://github.com/azure/azure-documentdb-datamigrationtool).

Możesz uruchomić emulator Azure Cosmos w kontenerze platformy Docker systemu Windows. Aby uzyskać więcej informacji, zobacz [centrum Docker](https://hub.docker.com/r/microsoft/azure-cosmosdb-emulator/) dla Docker pull polecenia i [GitHub](https://github.com/Azure/azure-cosmos-db-emulator-docker) `Dockerfile` .

## <a name="differences-between-the-emulator-and-the-service"></a>Różnice między emulatorem a usługą

Ponieważ emulator usługi Azure Cosmos udostępnia emulowane środowisko działające na lokalnej stacji roboczej dewelopera, istnieją pewne różnice w działaniu między emulatorem a kontem usługi Azure Cosmos w chmurze:

* Obecnie Eksplorator danych w emulatorze obsługuje klientów dla interfejsu API SQL. Widok Eksplorator danych i operacje dotyczące Azure Cosmos DB interfejsów API, takich jak MongoDB, Table, Graph i Cassandra, nie są w pełni obsługiwane.
* Emulator Azure Cosmos obsługuje tylko jedno stałe konto i dobrze znanego klucza głównego. Nie można Regeneracja klucza w emulatorze usługi Azure Cosmos, ale klucz domyślny można zmienić przy użyciu opcji wiersza polecenia.
* Emulator usługi Azure Cosmos obsługuje konto usługi Azure Cosmos w trybie [przepływności aprowizacji](set-throughput.md) . obecnie nie jest obsługiwane konto usługi Azure Cosmos w trybie [bezserwerowym](serverless.md) .
* Emulator usługi Azure Cosmos nie jest skalowalną usługą i nie będzie obsługiwał dużej liczby kontenerów.
* Emulator usługi Azure Cosmos nie oferuje różnych [poziomów spójności Azure Cosmos DB](consistency-levels.md).
* Emulator usługi Azure Cosmos nie oferuje [wieloregionowej replikacji](distribute-data-globally.md).
* Ponieważ Twoja kopia emulatora usługi Azure Cosmos może nie być zawsze aktualna przy użyciu najnowszych zmian w usłudze Azure Cosmos DB, należy odwołać się do [Azure Cosmos DB planisty wydajności](https://www.documentdb.com/capacityplanner) , aby dokładnie oszacować wymagania dotyczące przepływności produkcyjnej (jednostek ru) aplikacji.
* W przypadku korzystania z emulatora usługi Azure Cosmos domyślnie można utworzyć maksymalnie 25 kontenerów o stałym rozmiarze (obsługiwane tylko przy użyciu zestawów SDK Azure Cosmos DB) lub 5 nieograniczonych kontenerów za pomocą emulatora usługi Azure Cosmos. Aby uzyskać więcej informacji na temat zmiany tej wartości, zobacz [Ustawianie wartości PartitionCount](#set-partitioncount).
* Emulator obsługuje maksymalny rozmiar właściwości identyfikatora wynoszący 254 znaków.

## <a name="system-requirements"></a>Wymagania systemowe

Emulator usługi Azure Cosmos ma następujące wymagania dotyczące sprzętu i oprogramowania:

* Wymagania dotyczące oprogramowania
  * Windows Server 2012 R2, Windows Server 2016 lub Windows 10
  * 64-bitowy system operacyjny
* Minimalne wymagania sprzętowe
  * 2 GB pamięci RAM
  * 10 GB dostępnego miejsca na dysku twardym

## <a name="installation"></a>Instalacja

Emulator Azure Cosmos można pobrać i zainstalować z [Centrum pobierania Microsoft](https://aka.ms/cosmosdb-emulator) lub uruchomić emulator na Docker for Windows. Aby uzyskać instrukcje dotyczące korzystania z emulatora w aplikacji Docker for Windows, zobacz sekcję [Uruchamianie na platformie Docker](#running-on-docker).

> [!NOTE]
> Aby zainstalować, skonfigurować i uruchomić emulator usługi Azure Cosmos, musisz mieć uprawnienia administracyjne na tym komputerze. Emulator utworzy/doda certyfikat, a także ustawi reguły zapory w celu uruchomienia jej usług; w związku z tym do emulatora może być możliwe wykonanie takich operacji.

## <a name="running-on-windows"></a>Uruchamianie w systemie Windows

Aby uruchomić emulator Azure Cosmos, wybierz przycisk Start lub naciśnij klawisz systemu Windows. Zacznij wpisywać tekst **emulatora usługi Azure Cosmos**i wybierz emulator z listy aplikacji.

:::image type="content" source="./media/local-emulator/database-local-emulator-start.png" alt-text="Wybierz przycisk Start lub naciśnij klawisz systemu Windows, zacznij wpisywać tekst emulatora usługi Azure Cosmos i wybierz emulator z listy aplikacji":::

Po uruchomieniu emulatora pojawi się ikona w obszarze powiadomień paska zadań systemu Windows. 

:::image type="content" source="./media/local-emulator/database-local-emulator-taskbar.png" alt-text="Powiadomienie na pasku zadań Azure Cosmos DB lokalnego emulatora":::

Emulator usługi Azure Cosmos jest domyślnie uruchamiany na maszynie lokalnej ("localhost"), który nasłuchuje na porcie 8081.

Emulator usługi Azure Cosmos jest instalowany `C:\Program Files\Azure Cosmos DB Emulator` domyślnie. Można również uruchomić i zatrzymać emulator z poziomu wiersza polecenia. Więcej informacji zawiera [dokumentacja narzędzia wiersza polecenia](#command-line).

## <a name="start-data-explorer"></a>Uruchamianie Eksploratora danych

Po uruchomieniu emulatora usługi Azure Cosmos automatycznie otwiera Eksplorator danych Cosmos platformy Azure w przeglądarce. Adres jest wyświetlany jako `https://localhost:8081/_explorer/index.html`. Jeśli zamkniesz Eksploratora i chcesz otworzyć go później, możesz otworzyć adres URL w przeglądarce lub uruchomić go za pomocą emulatora usługi Azure Cosmos na ikonie na pasku zadań systemu Windows, jak pokazano poniżej.

:::image type="content" source="./media/local-emulator/database-local-emulator-data-explorer-launcher.png" alt-text="Moduł uruchamiający Eksploratora danych lokalnego emulatora usługi Azure Cosmos":::

## <a name="checking-for-updates"></a>Sprawdzanie dostępności aktualizacji

Eksplorator danych informuje o możliwości pobrania nowych aktualizacji.

> [!NOTE]
> W przypadku korzystania z innej wersji nie ma gwarancji, że dane utworzone w jednej wersji emulatora usługi Azure Cosmos (zobacz Ustawienia opcjonalne dla%LOCALAPPDATA%\CosmosDBEmulator lub ścieżki danych). Jeśli musisz utrzymywać dane przez długi czas, zalecamy przechowywanie tych danych na koncie usługi Azure Cosmos, a nie w emulatorze Cosmos platformy Azure.

## <a name="authenticating-requests"></a>Uwierzytelnianie żądań

Podobnie jak w przypadku Azure Cosmos DB w chmurze, każde żądanie, które należy wykonać względem emulatora usługi Azure Cosmos, musi zostać uwierzytelnione. Emulator Azure Cosmos obsługuje pojedyncze stałe konto i dobrze znane klucze uwierzytelniania na potrzeby uwierzytelniania przy użyciu klucza głównego. To konto i klucz są jedynymi poświadczeniami, które mogą być używane z emulatorem usługi Azure Cosmos. Są to:

```bash
Account name: localhost:<port>
Account key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
```

> [!NOTE]
> Klucz główny obsługiwany przez emulator usługi Azure Cosmos jest przeznaczony do użycia tylko z emulatorem. Nie można używać konta i klucza Azure Cosmos DB produkcyjnych z emulatorem usługi Azure Cosmos.

> [!NOTE]
> Jeśli emulator został uruchomiony przy użyciu opcji/Key, użyj wygenerowanego klucza zamiast `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==` . Aby uzyskać więcej informacji na temat opcji/Key, zobacz [informacje dotyczące narzędzia wiersza polecenia.](#command-line)

Podobnie jak w przypadku Azure Cosmos DB emulator Azure Cosmos obsługuje tylko bezpieczną komunikację za pośrednictwem protokołu TLS.

## <a name="running-on-a-local-network"></a>Uruchamianie w sieci lokalnej

Emulator możesz uruchomić w sieci lokalnej. Aby włączyć dostęp do sieci, należy określić `/AllowNetworkAccess` opcję w [wierszu polecenia](#command-line-syntax), która wymaga również określenia `/Key=key_string` lub `/KeyFile=file_name` . Możesz użyć `/GenKeyFile=file_name` do wygenerowania pliku z kluczem losowym z góry. Następnie możesz przekazać ten program do `/KeyFile=file_name` lub `/Key=contents_of_file` .

Aby włączyć dostęp do sieci po raz pierwszy, użytkownik powinien zamknąć emulator i usunąć katalog danych emulatora (%LOCALAPPDATA%\CosmosDBEmulator).

## <a name="developing-with-the-emulator"></a>Programowanie za pomocą emulatora

### <a name="sql-api"></a>Interfejs API SQL

Po uruchomieniu emulatora usługi Azure Cosmos na pulpicie możesz użyć dowolnego obsługiwanego [zestawu SDK Azure Cosmos DB](sql-api-sdk-dotnet-standard.md) lub [interfejsu API REST Azure Cosmos DB](/rest/api/cosmos-db/) , aby móc korzystać z emulatora. Emulator usługi Azure Cosmos obejmuje również wbudowaną Eksplorator danych, która umożliwia tworzenie kontenerów dla interfejsu API SQL lub Cosmos DB dla interfejsu API usługi Mongo DB, a także wyświetlanie i edytowanie elementów bez konieczności pisania kodu.

```csharp
// Connect to the Azure Cosmos Emulator running locally
CosmosClient client = new CosmosClient(
   "https://localhost:8081", 
    "C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==");

```

### <a name="azure-cosmos-dbs-api-for-mongodb"></a>Interfejs API usługi Azure Cosmos DB dla bazy danych MongoDB

Po uruchomieniu emulatora usługi Azure Cosmos na pulpicie możesz użyć [interfejsu API Azure Cosmos DB](mongodb-introduction.md) , aby MongoDB do współdziałania z emulatorem. Uruchom emulator z wiersza polecenia jako administrator z "/EnableMongoDbEndpoint". Następnie użyj następujących parametrów połączenia, aby nawiązać połączenie z kontem interfejsu API MongoDB:

```bash
mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true
```

### <a name="table-api"></a>Interfejs API tabel

Po uruchomieniu emulatora usługi Azure Cosmos na pulpicie możesz użyć [zestawu SDK interfejs API tabel Azure Cosmos DB](table-storage-how-to-use-dotnet.md) , aby korzystać z emulatora. Uruchom emulator z wiersza polecenia jako administrator z "/EnableTableEndpoint". Następnie uruchom następujący kod, aby nawiązać połączenie z kontem interfejsu API tabel:

```csharp
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Table;
using CloudTable = Microsoft.WindowsAzure.Storage.Table.CloudTable;
using CloudTableClient = Microsoft.WindowsAzure.Storage.Table.CloudTableClient;

string connectionString = "DefaultEndpointsProtocol=http;AccountName=localhost;AccountKey=C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==;TableEndpoint=http://localhost:8902/;";

CloudStorageAccount account = CloudStorageAccount.Parse(connectionString);
CloudTableClient tableClient = account.CreateCloudTableClient();
CloudTable table = tableClient.GetTableReference("testtable");
table.CreateIfNotExists();
table.Execute(TableOperation.Insert(new DynamicTableEntity("partitionKey", "rowKey")));
```

### <a name="cassandra-api"></a>Interfejs API rozwiązania Cassandra

Uruchom emulator z wiersza polecenia administratora z "/EnableCassandraEndpoint". Alternatywnie można również ustawić zmienną środowiskową `AZURE_COSMOS_EMULATOR_CASSANDRA_ENDPOINT=true` .

* [Zainstaluj Język Python 2,7](https://www.python.org/downloads/release/python-2716/)

* [Instalowanie interfejsu wiersza polecenia Cassandra/CQLSH](https://cassandra.apache.org/download/)

* Uruchom następujące polecenia w zwykłym oknie wiersza polecenia:

  ```bash
  set Path=c:\Python27;%Path%
  cd /d C:\sdk\apache-cassandra-3.11.3\bin
  set SSL_VERSION=TLSv1_2
  set SSL_VALIDATE=false
  cqlsh localhost 10350 -u localhost -p C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw== --ssl
  ```

* W powłoce CQLSH Uruchom następujące polecenia, aby nawiązać połączenie z punktem końcowym Cassandra:

  ```bash
  CREATE KEYSPACE MyKeySpace WITH replication = {'class':'MyClass', 'replication_factor': 1};
  DESCRIBE keyspaces;
  USE mykeyspace;
  CREATE table table1(my_id int PRIMARY KEY, my_name text, my_desc text);
  INSERT into table1 (my_id, my_name, my_desc) values( 1, 'name1', 'description 1');
  SELECT * from table1;
  EXIT
  ```

### <a name="gremlin-api"></a>Interfejs API języka Gremlin

Uruchom emulator z wiersza polecenia administratora z "/EnableGremlinEndpoint". Alternatywnie można również ustawić zmienną środowiskową `AZURE_COSMOS_EMULATOR_GREMLIN_ENDPOINT=true`

* [Zainstaluj Apache-tinkerpop-Gremlin-Console-3.3.4](https://archive.apache.org/dist/tinkerpop/3.3.4).

* W Eksplorator danych emulatora Utwórz bazę danych "DB1" i kolekcję "coll1"; w przypadku klucza partycji wybierz pozycję "/name"

* Uruchom następujące polecenia w zwykłym oknie wiersza polecenia:

  ```bash
  cd /d C:\sdk\apache-tinkerpop-gremlin-console-3.3.4-bin\apache-tinkerpop-gremlin-console-3.3.4
  
  copy /y conf\remote.yaml conf\remote-localcompute.yaml
  notepad.exe conf\remote-localcompute.yaml
    hosts: [localhost]
    port: 8901
    username: /dbs/db1/colls/coll1
    password: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
    connectionPool: {
    enableSsl: false}
    serializer: { className: org.apache.tinkerpop.gremlin.driver.ser.GraphSONMessageSerializerV1d0,
    config: { serializeResultToString: true  }}

  bin\gremlin.bat
  ```

* W powłoce Gremlin Uruchom następujące polecenia, aby nawiązać połączenie z punktem końcowym Gremlin:

  ```bash
  :remote connect tinkerpop.server conf/remote-localcompute.yaml
  :remote console
  :> g.V()
  :> g.addV('person1').property(id, '1').property('name', 'somename1')
  :> g.addV('person2').property(id, '2').property('name', 'somename2')
  :> g.V()
  ```

## <a name="export-the-tlsssl-certificate"></a>Eksportowanie certyfikatu TLS/SSL

Do nawiązywania bezpiecznego połączenia z lokalnym emulatorem usługi Azure Cosmos DB języki i środowisko uruchomieniowe .NET używają magazynu certyfikatów systemu Windows. Inne języki korzystają z własnych metod używania certyfikatów i zarządzania nimi. Język Java używa własnego [magazynu certyfikatów](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html), a język Python — [otok gniazd](https://docs.python.org/2/library/ssl.html).

Aby uzyskać certyfikat do użycia z językami i środowiskami uruchomieniowymi, które nie zapewniają integracji z magazynem certyfikatów systemu Windows, trzeba wyeksportować ten certyfikat przy użyciu menedżera certyfikatów systemu Windows. Można uruchomić go, uruchamiając certlm. msc lub postępując zgodnie z instrukcjami krok po kroku w temacie [Eksportowanie certyfikatów emulatora usługi Azure Cosmos](./local-emulator-export-ssl-certificates.md). Po uruchomieniu menedżera certyfikatów otwórz sekcję certyfikatów osobistych, jak pokazano poniżej, i wyeksportuj certyfikat o przyjaznej nazwie „DocumentDBEmulatorCertificate” jako plik Certyfikat X.509 szyfrowany algorytmem Base-64 (CER).

:::image type="content" source="./media/local-emulator/database-local-emulator-ssl_certificate.png" alt-text="Azure Cosmos DB certyfikat TLS/SSL lokalnego emulatora":::

Aby zaimportować certyfikat X.509 do magazynu certyfikatów Java, postępuj zgodnie z instrukcjami zawartymi w artykule [Adding a Certificate to the Java CA Certificates Store (Dodawanie certyfikatu do magazynu certyfikatów Java CA)](https://docs.microsoft.com/azure/java-add-certificate-ca-store). Po zaimportowaniu certyfikatu do magazynu certyfikatów klienci programu SQL i interfejsu API Azure Cosmos DB dla MongoDB będą mogli nawiązywać połączenia z emulatorem usługi Azure Cosmos.

Podczas nawiązywania połączenia z emulatorem z poziomu zestawów SDK w języku Python i Node.js, weryfikacja protokołu TLS jest wyłączona.

## <a name="command-line-tool-reference"></a><a id="command-line"></a>Dokumentacja narzędzia wiersza polecenia
W lokalizacji instalacji można użyć wiersza polecenia do uruchamiania i zatrzymywania emulatora, konfigurowania opcji i wykonywania innych operacji.

### <a name="command-line-syntax"></a>Składnia wiersza polecenia

```cmd
Microsoft.Azure.Cosmos.Emulator.exe [/Shutdown] [/DataPath] [/Port] [/MongoPort] [/DirectPorts] [/Key] [/EnableRateLimiting] [/DisableRateLimiting] [/NoUI] [/NoExplorer] [/EnableMongoDbEndpoint] [/?]
```

Aby wyświetlić listę opcji, wpisz ciąg `Microsoft.Azure.Cosmos.Emulator.exe /?` w wierszu polecenia.

|**Opcja** | **Opis** | **Polecenie**| **Argumenty**|
|---|---|---|---|
|[Bez argumentów] | Uruchamia emulator Azure Cosmos z ustawieniami domyślnymi. |Microsoft.Azure.Cosmos.Emulator.exe| |
|[Help] |Wyświetla listę obsługiwanych argumentów wiersza polecenia.|Microsoft.Azure.Cosmos.Emulator.exe/? | |
| GetStatus |Pobiera stan emulatora usługi Azure Cosmos. Stan jest wskazywany przez kod zakończenia: 1 = uruchamiany, 2 = uruchomiony, 3 = zatrzymany. Kod zakończenia o wartości ujemnej informuje o wystąpieniu błędu. Inne dane wyjściowe nie są generowane. | Microsoft.Azure.Cosmos.Emulator.exe/GetStatus| |
| Zamykanie| Zamyka emulator usługi Azure Cosmos.| Microsoft.Azure.Cosmos.Emulator.exe/Shutdown | |
|DataPath | Określa ścieżkę przechowywania plików danych. Wartość domyślna to%LocalAppdata%\CosmosDBEmulator. | Microsoft.Azure.Cosmos.Emulator.exe/DataPath =\<datapath\> | \<datapath\>: Dostępna ścieżka |
|Port | Określa numer portu używanego przez emulatora. Wartość domyślna to 8081. |Microsoft.Azure.Cosmos.Emulator.exe/port =\<port\> | \<port\>: Numer pojedynczego portu |
| ComputePort | Określono numer portu, który ma być używany w przypadku usługi bramy międzyoperacyjności obliczeń. Port sondowania punktu końcowego HTTP bramy jest obliczany jako ComputePort + 79. W związku z tym ComputePort i ComputePort + 79 muszą być otwarte i dostępne. Wartość domyślna to 8900. | Microsoft.Azure.Cosmos.Emulator.exe/ComputePort =\<computeport\> | \<computeport\>: Numer pojedynczego portu |
| EnableMongoDbEndpoint = 3.2 | Włącza interfejs API MongoDB 3,2 | Microsoft.Azure.Cosmos.Emulator.exe/EnableMongoDbEndpoint = 3.2 | |
| EnableMongoDbEndpoint = 3.6 | Włącza interfejs API MongoDB 3,6 | Microsoft.Azure.Cosmos.Emulator.exe/EnableMongoDbEndpoint = 3.6 | |
| MongoPort | Określa numer portu używanego w celu zapewnienia zgodności z interfejsem API usługi MongoDB. Wartość domyślna to 10255. |Microsoft.Azure.Cosmos.Emulator.exe/MongoPort =\<mongoport\>|\<mongoport\>: Numer pojedynczego portu|
| EnableCassandraEndpoint | Włącza interfejs API Cassandra | Microsoft.Azure.Cosmos.Emulator.exe/EnableCassandraEndpoint | |
| CassandraPort | Określa numer portu, który ma być używany dla punktu końcowego Cassandra. Wartość domyślna to 10350. | Microsoft.Azure.Cosmos.Emulator.exe/CassandraPort =\<cassandraport\> | \<cassandraport\>: Numer pojedynczego portu |
| EnableGremlinEndpoint | Włącza interfejs API Gremlin | Microsoft.Azure.Cosmos.Emulator.exe/EnableGremlinEndpoint | |
| GremlinPort | Numer portu do użycia w punkcie końcowym Gremlin. Wartość domyślna to 8901. | Microsoft.Azure.Cosmos.Emulator.exe/GremlinPort =\<port\> | \<port\>: Numer pojedynczego portu |
|EnableTableEndpoint | Włącza interfejs API tabel platformy Azure | Microsoft.Azure.Cosmos.Emulator.exe/EnableTableEndpoint | |
|TablePort | Numer portu do użycia w punkcie końcowym tabeli platformy Azure. Wartość domyślna to 8902. | Microsoft.Azure.Cosmos.Emulator.exe/TablePort =\<port\> | \<port\>: Numer pojedynczego portu|
| KeyFile | Odczytaj klucz autoryzacji z określonego pliku. Użyj opcji/GenKeyFile, aby wygenerować KeyFile | Microsoft.Azure.Cosmos.Emulator.exe/KeyFile =\<file_name\> | \<file_name\>: Ścieżka do pliku |
| ResetDataPath | Rekursywnie usuwa wszystkie pliki z określonej ścieżki. Jeśli ścieżka nie zostanie określona, domyślnie%LOCALAPPDATA%\CosmosDbEmulator | Microsoft.Azure.Cosmos.Emulator.exe/ResetDataPath =\<path> | \<path\>: Ścieżka pliku  |
| StartTraces  |  Rozpocznij zbieranie dzienników śledzenia debugowania za pomocą narzędzia LOGMAN. | Microsoft.Azure.Cosmos.Emulator.exe/StartTraces | |
| StopTraces     | Zatrzymaj zbieranie dzienników śledzenia debugowania za pomocą narzędzia LOGMAN. | Microsoft.Azure.Cosmos.Emulator.exe/StopTraces  | |
| StartWprTraces  |  Rozpocznij zbieranie dzienników śledzenia debugowania przy użyciu narzędzia do rejestrowania wydajności systemu Windows. | Microsoft.Azure.Cosmos.Emulator.exe/StartWprTraces | |
| StopWprTraces     | Zatrzymaj zbieranie dzienników śledzenia debugowania przy użyciu narzędzia do nagrywania wydajności systemu Windows. | Microsoft.Azure.Cosmos.Emulator.exe/StopWprTraces  | |
|FailOnSslCertificateNameMismatch | Domyślnie emulator ponownie generuje swój certyfikat TLS/SSL z podpisem własnym, jeśli sieć SAN certyfikatu nie zawiera nazwy domeny hosta emulatora, lokalnego adresu IPv4, "localhost" i "127.0.0.1". W przypadku tej opcji emulator zakończy się niepowodzeniem przy uruchamianiu. Następnie należy użyć opcji/GenCert, aby utworzyć i zainstalować nowy certyfikat TLS/SSL z podpisem własnym. | Microsoft.Azure.Cosmos.Emulator.exe/FailOnSslCertificateNameMismatch  | |
| GenCert | Wygeneruj i zainstaluj nowy certyfikat TLS/SSL z podpisem własnym. Opcjonalnie, w tym rozdzieloną przecinkami listę dodatkowych nazw DNS w celu uzyskania dostępu do emulatora za pośrednictwem sieci. | Microsoft.Azure.Cosmos.Emulator.exe/GenCert =\<dns-names\> |\<dns-names\>: Opcjonalna rozdzielana przecinkami lista dodatkowych nazw DNS  |
| DirectPorts |Określa porty używane przez połączenia bezpośrednie. Wartość domyślna to 10251,10252,10253,10254. | /DirectPorts Microsoft.Azure.Cosmos.Emulator.exe:\<directports\> | \<directports\>: Rozdzielana przecinkami lista 4 portów |
| Klucz |Klucz autoryzacji dla emulatora. Klucz musi być 64-bajtowym wektorem szyfrowanym algorytmem Base-64. | /Key Microsoft.Azure.Cosmos.Emulator.exe:\<key\> | \<key\>: Klucz musi być kodowaniem Base-64 dla wektora 64-Byte|
| EnableRateLimiting | Wskazuje, że ograniczanie szybkości żądania jest włączone. |Microsoft.Azure.Cosmos.Emulator.exe/EnableRateLimiting | |
| DisableRateLimiting |Wskazuje, że ograniczanie szybkości żądania jest wyłączone. |Microsoft.Azure.Cosmos.Emulator.exe/DisableRateLimiting | |
| NoUI | Nie wyświetla interfejsu użytkownika emulatora. | Microsoft.Azure.Cosmos.Emulator.exe/NoUI | |
| NoExplorer | Nie wyświetla Eksploratora danych podczas uruchamiania. |Microsoft.Azure.Cosmos.Emulator.exe/NoExplorer | | 
| PartitionCount | Określa maksymalną liczbę kontenerów podzielonych na partycje. Aby uzyskać więcej informacji [, zobacz Zmiana liczby kontenerów](#set-partitioncount) . | Microsoft.Azure.Cosmos.Emulator.exe/PartitionCount =\<partitioncount\> | \<partitioncount\>: Maksymalna liczba dozwolonych kontenerów pojedynczej partycji. Wartość domyślna to 25. Maksymalna dozwolona wartość to 250.|
| DefaultPartitionCount| Określa domyślną liczbę partycji dla kontenera partycjonowanego. | Microsoft.Azure.Cosmos.Emulator.exe/DefaultPartitionCount =\<defaultpartitioncount\> | \<defaultpartitioncount\> Wartość domyślna to 25.|
| AllowNetworkAccess | Włącza dostęp do emulatora za pośrednictwem sieci. Aby włączyć dostęp do sieci, należy również przekazać/Key = \<key_string\> lub/KeyFile = \<file_name\> . | Microsoft.Azure.Cosmos.Emulator.exe/AllowNetworkAccess/Key = \<key_string\> lub Microsoft.Azure.Cosmos.Emulator.exe/AllowNetworkAccess/KeyFile =\<file_name\>| |
| NoFirewall | Nie dostosowuj reguł zapory, gdy jest używana opcja/AllowNetworkAccess. |Microsoft.Azure.Cosmos.Emulator.exe/NoFirewall | |
| GenKeyFile | Generuje nowy klucz autoryzacji i zapisuje go w określonym pliku. Wygenerowanego klucza można używać z opcją /Key lub /KeyFile. | Microsoft.Azure.Cosmos.Emulator.exe/GenKeyFile =\<path to key file\> | |
| Spójność | Ustawia domyślny poziom spójności dla konta. | Microsoft.Azure.Cosmos.Emulator.exe/Consistency =\<consistency\> | \<consistency\>: Wartość musi być jednym z następujących [poziomów spójności](consistency-levels.md): sesja, silne, ostateczne lub BoundedStaleness. Wartość domyślna to Sesja. |
| ? | Wyświetla komunikat pomocy.| | |

## <a name="change-the-number-of-containers"></a><a id="set-partitioncount"></a>Zmień liczbę kontenerów

Domyślnie można utworzyć maksymalnie 25 kontenerów o stałym rozmiarze (obsługiwane tylko przy użyciu zestawów SDK Azure Cosmos DB) lub 5 nieograniczoną liczbę kontenerów za pomocą emulatora usługi Azure Cosmos. Modyfikując wartość **PartitionCount** , można utworzyć maksymalnie 250 kontenerów o stałym rozmiarze lub 50 nieograniczoną liczbę kontenerów lub dowolną kombinację tych dwóch, która nie przekracza 250 kontenerów o stałym rozmiarze (gdzie jeden kontener o stałym rozmiarze = 5 kontenerów). Nie zaleca się jednak konfigurowania emulatora do uruchamiania z ponad 200 kontenerów o stałym rozmiarze. Ze względu na obciążenie, które dodaje do operacji we/wy dysku, co powoduje nieprzewidywalne przekroczenie limitu czasu podczas korzystania z interfejsów API punktu końcowego.

Jeśli podjęto próbę utworzenia kontenera po przekroczeniu liczby bieżącej partycji, Emulator zgłasza wyjątek serviceniedostępne z następującym komunikatem.

"Niestety obecnie mamy wysokie zapotrzebowanie w tym regionie i w tej chwili nie można zrealizować Twojego żądania. Pracujemy w sposób ciągły, aby zwiększyć pojemność w trybie online, i zachęcamy do ponowienia próby.
ActivityId: 12345678-1234-1234-1234-123456789abc "

Aby zmienić liczbę kontenerów dostępnych w emulatorze usługi Azure Cosmos, uruchom następujące kroki:

1. Usuń wszystkie lokalne dane emulatora usługi Azure Cosmos, klikając prawym przyciskiem myszy ikonę **emulatora Azure Cosmos DB** na pasku zadań, a następnie klikając polecenie **Resetuj dane...**.
2. Usuń wszystkie dane emulatora z tego folderu `%LOCALAPPDATA%\CosmosDBEmulator` .
3. Zamknij wszystkie otwarte wystąpienia, klikając prawym przyciskiem myszy ikonę **emulatora usługi Azure Cosmos DB** w obszarze powiadomień, a następnie klikając polecenie **Exit** (Zakończ). Zamykanie wszystkich wystąpień może potrwać około minuty.
4. Zainstaluj najnowszą wersję [emulatora usługi Azure Cosmos](https://aka.ms/cosmosdb-emulator).
5. Uruchom emulator z flagą PartitionCount, ustawiając wartość <= 250. Na przykład: `C:\Program Files\Azure Cosmos DB Emulator> Microsoft.Azure.Cosmos.Emulator.exe /PartitionCount=100`.

## <a name="controlling-the-emulator"></a>Sterowanie emulatorem

Emulator zawiera moduł programu PowerShell umożliwiający uruchamianie, zatrzymywanie, Odinstalowywanie i pobieranie stanu usługi. Uruchom następujące polecenie cmdlet, aby użyć modułu programu PowerShell:

```powershell
Import-Module "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules\Microsoft.Azure.CosmosDB.Emulator"
```

lub umieść `PSModules` katalog na serwerze `PSModulesPath` i zaimportuj go, jak pokazano w następującym poleceniu:

```powershell
$env:PSModulesPath += "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules"
Import-Module Microsoft.Azure.CosmosDB.Emulator
```

Poniżej przedstawiono podsumowanie poleceń umożliwiających sterowanie emulatorem za pomocą programu PowerShell:

### `Get-CosmosDbEmulatorStatus`

**Składnia**

`Get-CosmosDbEmulatorStatus`

**Uwagi**

Zwraca jedną z następujących wartości ServiceControllerStatus: ServiceControllerStatus.StartPending, ServiceControllerStatus.Running lub ServiceControllerStatus.Stopped.

### `Start-CosmosDbEmulator`

**Składnia**

`Start-CosmosDbEmulator [-DataPath <string>] [-DefaultPartitionCount <uint16>] [-DirectPort <uint16[]>] [-MongoPort <uint16>] [-NoUI] [-NoWait] [-PartitionCount <uint16>] [-Port <uint16>] [<CommonParameters>]`

**Uwagi**

Uruchamia emulator. Domyślnie to polecenie czeka, aż emulator zgłosi gotowość do odbierania żądań. Jeśli chcesz, aby polecenie cmdlet zakończyło działanie od razu po uruchomieniu emulatora, użyj opcji -NoWait.

### `Stop-CosmosDbEmulator`

**Składnia**

 `Stop-CosmosDbEmulator [-NoWait]`

**Uwagi**

Zatrzymuje emulator. Domyślnie to polecenie czeka, aż emulator zostanie w pełni zamknięty. Jeśli chcesz, aby polecenie cmdlet zakończyło działanie od razu po rozpoczęciu zamykania emulatora, użyj opcji -NoWait.

### `Uninstall-CosmosDbEmulator`

**Składnia**

`Uninstall-CosmosDbEmulator [-RemoveData]`

**Uwagi**

Odinstalowuje emulator i opcjonalnie usuwa całą zawartość katalogu $env:LOCALAPPDATA\CosmosDbEmulator.
To polecenie cmdlet gwarantuje zatrzymanie emulatora przed jego odinstalowaniem.

## <a name="running-on-docker"></a>Uruchamianie na platformie Docker

Emulator usługi Azure Cosmos można uruchomić na Docker for Windows. Emulator nie współdziała z aplikacją Docker for Oracle Linux.

Po zainstalowaniu aplikacji [Docker for Windows](https://www.docker.com/docker-windows) przełącz się na kontenery systemu Windows, klikając prawym przyciskiem myszy ikonę programu Docker na pasku narzędzi i wybierając polecenie **Switch to Windows containers** (Przełącz na kontenery systemu Windows).

Następnie ściągnij obraz emulatora z usługi Docker Hub, uruchamiając następujące polecenie w wybranej powłoce.

```bash
docker pull mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator
```
Aby uruchomić obraz, uruchom następujące polecenia.

W wierszu polecenia:
```cmd

md %LOCALAPPDATA%\CosmosDBEmulator\bind-mount

docker run --name azure-cosmosdb-emulator --memory 2GB --mount "type=bind,source=%LOCALAPPDATA%\CosmosDBEmulator\bind-mount,destination=C:\CosmosDB.Emulator\bind-mount" --interactive --tty -p 8081:8081 -p 8900:8900 -p 8901:8901 -p 8902:8902 -p 10250:10250 -p 10251:10251 -p 10252:10252 -p 10253:10253 -p 10254:10254 -p 10255:10255 -p 10256:10256 -p 10350:10350 mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator
```

> [!NOTE]
> Jeśli zostanie wyświetlony komunikat o błędzie konfliktu portów (określony port jest już używany) po uruchomieniu polecenia Docker Run można przekazać port niestandardowy, zmieniając numery portów. Na przykład można zmienić wartość "-p 8081:8081" na "-p 443:8081"

W programie PowerShell:
```powershell

md $env:LOCALAPPDATA\CosmosDBEmulator\bind-mount 2>null

docker run --name azure-cosmosdb-emulator --memory 2GB --mount "type=bind,source=$env:LOCALAPPDATA\CosmosDBEmulator\bind-mount,destination=C:\CosmosDB.Emulator\bind-mount" --interactive --tty -p 8081:8081 -p 8900:8900 -p 8901:8901 -p 8902:8902 -p 10250:10250 -p 10251:10251 -p 10252:10252 -p 10253:10253 -p 10254:10254 -p 10255:10255 -p 10256:10256 -p 10350:10350 mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator

```

Odpowiedź wygląda mniej więcej tak:

```
Starting emulator
Emulator Endpoint: https://172.20.229.193:8081/
Master Key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
Exporting SSL Certificate
You can import the SSL certificate from an administrator command prompt on the host by running:
cd /d %LOCALAPPDATA%\CosmosDBEmulatorCert
powershell .\importcert.ps1
--------------------------------------------------------------------------------------------------
Starting interactive shell
```

Teraz użyj punktu końcowego i klucza głównego z odpowiedzi na kliencie i zaimportuj certyfikat TLS/SSL do hosta. Aby zaimportować certyfikat TLS/SSL, wykonaj następujące czynności z poziomu wiersza polecenia administratora:

W wierszu polecenia:

```cmd
cd  %LOCALAPPDATA%\CosmosDBEmulator\bind-mount
powershell .\importcert.ps1
```

W programie PowerShell:
```powershell
cd $env:LOCALAPPDATA\CosmosDBEmulator\bind-mount
.\importcert.ps1
```

Zamknięcie powłoki interaktywnej po uruchomieniu emulatora spowoduje zamknięcie kontenera emulatora.

Aby otworzyć Eksploratora danych, otwórz następujący adres URL w przeglądarce. Punkt końcowy emulatora znajduje się w powyższym komunikacie odpowiedzi.

**Protokół \: // https** \<emulator endpoint provided in response> **/_explorer/index.html**

Jeśli masz uruchomioną aplikację kliencką platformy .NET w kontenerze platformy Docker systemu Linux i jeśli korzystasz z emulatora usługi Azure Cosmos na maszynie hosta, postępuj zgodnie z poniższą sekcją w systemie Linux, aby zaimportować certyfikat do kontenera platformy Docker systemu Linux.

## <a name="running-on-mac-or-linux"></a>Uruchomiona na komputerze Mac lub w systemie Linux<a id="mac"></a>

Obecnie emulator Cosmos można uruchomić tylko w systemie Windows. Użytkownicy z systemem Mac lub Linux mogą uruchomić emulator w maszynie wirtualnej z systemem Windows hostowanej w funkcji hypervisor, takiej jak Parallels lub VirtualBox. Poniżej znajdują się kroki umożliwiające wykonanie tej czynności.

Na maszynie wirtualnej z systemem Windows uruchom poniższe polecenie i Zanotuj adres IPv4.

```cmd
ipconfig.exe
```

W aplikacji należy zmienić identyfikator URI używany jako punkt końcowy, aby użyć adresu IPv4 zwróconego przez `ipconfig.exe` zamiast `localhost` .

Następnym krokiem z poziomu maszyny wirtualnej z systemem Windows jest uruchomienie emulatora Cosmos z poziomu wiersza polecenia przy użyciu następujących opcji.

```cmd
Microsoft.Azure.Cosmos.Emulator.exe /AllowNetworkAccess /Key=C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
```

Na koniec należy rozwiązać proces zaufania certyfikatu między aplikacją działającą w środowisku Linux lub Mac a emulatorem. Dostępne są dwie opcje:

1. Wyłącz weryfikację SSL w aplikacji:

# <a name="net-standard-21"></a>[.NET Standard 2.1 +](#tab/ssl-netstd21)

   W przypadku dowolnej aplikacji uruchomionej w strukturze zgodnej z .NET Standard 2,1 lub nowszej można wykorzystać `CosmosClientOptions.HttpClientFactory` :

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/HttpClientFactory/Program.cs?name=DisableSSLNETStandard21)]

# <a name="net-standard-20"></a>[.NET Standard 2,0](#tab/ssl-netstd20)

   Dla każdej aplikacji działającej w strukturze zgodnej z .NET Standard 2,0 można wykorzystać `CosmosClientOptions.HttpClientFactory` :

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/HttpClientFactory/Program.cs?name=DisableSSLNETStandard20)]

# <a name="nodejs"></a>[Node.js](#tab/ssl-nodejs)

   W przypadku aplikacji Node.js można zmodyfikować plik, `package.json` Aby ustawić `NODE_TLS_REJECT_UNAUTHORIZED` podczas uruchamiania aplikacji:

   ```json
   "start": NODE_TLS_REJECT_UNAUTHORIZED=0 node app.js
   ```

--- 

> [!NOTE]
> Wyłączenie walidacji protokołu SSL jest zalecane tylko w celach programistycznych i nie powinno być wykonywane w środowisku produkcyjnym.

2. Zaimportuj certyfikat emulatora CA do środowiska Linux lub Mac:

### <a name="linux"></a>Linux

Jeśli pracujesz w systemie Linux, .NET przekazuje na OpenSSL w celu sprawdzenia poprawności:

1. [Wyeksportuj certyfikat w formacie PFX](./local-emulator-export-ssl-certificates.md#how-to-export-the-azure-cosmos-db-tlsssl-certificate) (plik PFX jest dostępny podczas wybierania eksportu klucza prywatnego). 

1. Skopiuj ten plik PFX do środowiska systemu Linux.

1. Konwertuj plik PFX na plik CRT

   ```bash
   openssl pkcs12 -in YourPFX.pfx -clcerts -nokeys -out YourCTR.crt
   ```

1. Skopiuj plik CRT do folderu, który zawiera niestandardowe certyfikaty w dystrybucji systemu Linux. Często w przypadku dystrybucji Debian znajdują się w `/usr/local/share/ca-certificates/` .

   ```bash
   cp YourCTR.crt /usr/local/share/ca-certificates/
   ```

1. Zaktualizuj certyfikaty urzędu certyfikacji, co spowoduje zaktualizowanie `/etc/ssl/certs/` folderu.

   ```bash
   update-ca-certificates
   ```

### <a name="macos"></a>macOS

Jeśli pracujesz na komputerze Mac, wykonaj następujące czynności:

1. [Wyeksportuj certyfikat w formacie PFX](./local-emulator-export-ssl-certificates.md#how-to-export-the-azure-cosmos-db-tlsssl-certificate) (plik PFX jest dostępny podczas wybierania eksportu klucza prywatnego).

1. Skopiuj ten plik PFX do środowiska Mac.

1. Otwórz aplikację *dostęp do łańcucha kluczy* i zaimportuj plik PFX.

1. Otwórz listę certyfikatów i zidentyfikuj ją z nazwą `localhost` .

1. Otwórz menu kontekstowe dla danego elementu, wybierz opcję *Pobierz element* i w obszarze *zaufanie*w  >  *przypadku korzystania z tego certyfikatu* , wybierz pozycję *Zawsze ufaj*. 

   :::image type="content" source="./media/local-emulator/mac-trust-certificate.png" alt-text="Otwórz menu kontekstowe dla danego elementu, wybierz pozycję Pobierz element i w obszarze zaufanie — w przypadku korzystania z tego certyfikatu wybierz opcję Zawsze ufaj.":::

Po wykonaniu tych kroków środowisko będzie ufać certyfikatowi używanemu przez emulator podczas łączenia się z adresem IP uwidacznianym przez `/AllowNetworkAccess` .

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Poniższe wskazówki ułatwiają rozwiązywanie problemów napotykanych przez emulator usługi Azure Cosmos:

- Jeśli po zainstalowaniu nowej wersji emulatora występują błędy, upewnij się, że dane zostały zresetowane. Możesz zresetować dane, klikając prawym przyciskiem myszy ikonę emulatora usługi Azure Cosmos na pasku zadań, a następnie klikając polecenie Zresetuj dane.... Jeśli to nie rozwiąże błędów, można odinstalować emulator i wszystkie starsze wersje emulatora, jeśli zostały znalezione, Usuń katalog "C:\Program files\Azure Cosmos DB emulator" i ponownie zainstaluj emulator. Odpowiednie instrukcje znajdziesz w sekcji [Odinstalowywanie lokalnego emulatora](#uninstall).

- W przypadku awarii emulatora usługi Azure Cosmos należy zebrać pliki zrzutu z folderu "%LOCALAPPDATA%\CrashDumps", je skompresować i otworzyć bilet pomocy technicznej z [Azure Portal](https://portal.azure.com).

- Jeśli wystąpi awaria w programie `Microsoft.Azure.Cosmos.ComputeServiceStartupEntryPoint.exe` , może to być objawem, w którym liczniki wydajności są w stanie uszkodzenia. Zwykle uruchomienie następującego polecenia z poziomu wiersza polecenia administratora rozwiązuje problem:

  ```cmd
  lodctr /R
   ```

- W przypadku wystąpienia problemu z łącznością należy [zebrać pliki śledzenia](#trace-files), skompresować je i otworzyć bilet pomocy technicznej w [Azure Portal](https://portal.azure.com).

- Jeśli zostanie wyświetlony komunikat **Service Unavailable** (Usługa niedostępna), może to wskazywać na błąd inicjacji stosu sieciowego. Sprawdź, czy masz zainstalowanego klienta Pulse Secure lub Juniper Networks — ich sterowniki filtrów sieci mogą powodować problemy. Odinstalowanie sterowników filtrów sieci innych firm zwykle pozwala rozwiązać problem. Alternatywnie można uruchomić emulator przy użyciu/DisableRIO, co spowoduje przełączenie komunikacji między sieciami emulatora do zwykłego interfejsu Winsock. 

- Jeśli wystąpi **"zabronione", "komunikat": "żądanie jest wykonywane z niedozwolonym szyfrowaniem w protokole tranzytowym lub szyfrie. Sprawdź ustawienia Minimalny dozwolony protokół SSL/TLS konta... "** problemy z łącznością mogą być spowodowane przez globalne zmiany w systemie operacyjnym (na przykład kompilacja Insider Preview 20170) lub ustawienia przeglądarki, które domyślnie włączają protokół TLS 1,3. Podobny błąd może wystąpić podczas używania zestawu SDK do wykonywania żądania względem emulatora Cosmos, takiego jak **Microsoft.Azure.Documents.DocumentClientException: żądanie jest wykonywane z niedozwolonym szyfrowaniem w protokole tranzytowym lub szyfrem. Sprawdź ustawienie Minimalny dozwolony protokół SSL/TLS konta**. Jest to oczekiwane w tej chwili, ponieważ emulator Cosmos akceptuje tylko protokół TLS 1,2 i współpracuje z nim. Zalecaną obejściem jest zmiana ustawień i domyślnego protokołu TLS 1,2; na przykład w Menedżerze usług IIS przejdź do lokalizacji "Lokacje" — > "domyślne witryny sieci Web" i Znajdź "powiązania witryny" dla portu 8081 i edytuj je, aby wyłączyć protokół TLS 1,3. Podobną operację można wykonać dla przeglądarki sieci Web za pomocą opcji "Ustawienia".

- Kiedy emulator jest uruchomiony, jeśli komputer przejdzie w tryb uśpienia lub uruchomi dowolną aktualizację systemu operacyjnego, możesz zobaczyć komunikat **Service is currently unavailable** (Usługa jest obecnie niedostępna). Zresetuj dane emulatora, klikając prawym przyciskiem myszy ikonę widoczną na pasku powiadomień systemu Windows i wybierając pozycję **Zresetuj dane**.

### <a name="collect-trace-files"></a><a id="trace-files"></a>Zbieranie plików śledzenia

Aby zebrać dane śledzenia debugowania, uruchom następujące polecenia w wierszu polecenia z uprawnieniami administratora:

1. `cd /d "%ProgramFiles%\Azure Cosmos DB Emulator"`
2. `Microsoft.Azure.Cosmos.Emulator.exe /shutdown`. W obszarze powiadomień sprawdź, czy program został zamknięty — może to potrwać około minuty. Możesz również kliknąć pozycję **Zakończ** w interfejsie użytkownika emulatora usługi Azure Cosmos.
3. `Microsoft.Azure.Cosmos.Emulator.exe /startwprtraces`
4. `Microsoft.Azure.Cosmos.Emulator.exe`
5. Odtwórz problem. Jeśli Eksplorator danych nie działa, po prostu poczekaj na otwarcie przeglądarki, która po kilku sekundach przechwyci błąd.
6. `Microsoft.Azure.Cosmos.Emulator.exe /stopwprtraces`
7. Znajdź plik docdbemulator_000001.etl w folderze `%ProgramFiles%\Azure Cosmos DB Emulator`.
8. Otwórz bilet pomocy technicznej w [Azure Portal](https://portal.azure.com) i Uwzględnij plik ETL wraz z krokami Odtwórz.

### <a name="uninstall-the-local-emulator"></a><a id="uninstall"></a>Odinstalowywanie lokalnego emulatora

1. Zamknij wszystkie otwarte wystąpienia emulatora lokalnego, klikając prawym przyciskiem myszy ikonę emulatora usługi Azure Cosmos na pasku zadań, a następnie klikając polecenie Zakończ. Zamykanie wszystkich wystąpień może potrwać około minuty.
2. W polu wyszukiwania systemu Windows wpisz ciąg **Aplikacje i funkcje** i kliknij wynik **Aplikacje i funkcje (Ustawienia systemowe)**.
3. Przewiń listę aplikacji, wybierz pozycję **Emulator usługi Azure Cosmos DB**, kliknij przycisk **Odinstaluj**, potwierdź i ponownie kliknij przycisk **Odinstaluj**.
4. Gdy aplikacja zostanie odinstalowana, przejdź do katalogu `%LOCALAPPDATA%\CosmosDBEmulator` i usuń folder.

## <a name="next-steps"></a>Następne kroki

W tym samouczku opisaliśmy sposób użycia lokalnego emulatora do bezpłatnego programowania w środowisku lokalnym. Teraz możesz przechodzić do następnego samouczka i dowiedzieć się, jak eksportować certyfikaty emulatora TLS/SSL.

> [!div class="nextstepaction"]
> [Eksportowanie certyfikatów emulatora usługi Azure Cosmos](local-emulator-export-ssl-certificates.md)
