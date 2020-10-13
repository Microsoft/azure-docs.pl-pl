---
title: Dokumentacja wiersza polecenia i programu PowerShell dla emulatora Azure Cosmos DB
description: Informacje o parametrach wiersza polecenia dla emulatora Azure Cosmos DB, sposobach kontrolowania emulatora przy użyciu programu PowerShell oraz zmieniania liczby kontenerów, które można utworzyć w emulatorze.
ms.service: cosmos-db
ms.topic: how-to
author: markjbrown
ms.author: mjbrown
ms.date: 09/17/2020
ms.custom: contperfq1
ms.openlocfilehash: f8bcadf25ac8e001657f2be012f99ddb507e672d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91445174"
---
# <a name="command-line-and-powershell-reference-for-azure-cosmos-db-emulator"></a>Dokumentacja wiersza polecenia i programu PowerShell dla emulatora Azure Cosmos DB

Emulator usługi Azure Cosmos udostępnia środowisko lokalne, które emuluje usługę Azure Cosmos DB na potrzeby tworzenia lokalnego. Po [zainstalowaniu emulatora](local-emulator.md)można kontrolować emulator przy użyciu wiersza polecenia i poleceń programu PowerShell. W tym artykule opisano, jak używać wiersza polecenia i poleceń programu PowerShell do uruchamiania i zatrzymywania emulatora, konfigurowania opcji i wykonywania innych operacji. Musisz uruchomić polecenia z lokalizacji instalacji.

##  <a name="manage-the-emulator-with-command-line-syntax"></a><a id="command-line"></a>Zarządzanie emulatorem przy użyciu składni wiersza polecenia

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

## <a name="manage-the-emulator-with-powershell"></a>Zarządzanie emulatorem przy użyciu programu PowerShell

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

## <a name="change-the-number-of-default-containers"></a><a id="set-partitioncount"></a>Zmień liczbę kontenerów domyślnych

Domyślnie można utworzyć maksymalnie 25 kontenerów o stałym rozmiarze (obsługiwane tylko przy użyciu zestawów SDK Azure Cosmos DB) lub 5 nieograniczoną liczbę kontenerów za pomocą emulatora usługi Azure Cosmos. Modyfikując wartość **PartitionCount** , można utworzyć maksymalnie 250 kontenerów o stałym rozmiarze lub 50 nieograniczoną liczbę kontenerów lub dowolną kombinację tych dwóch, która nie przekracza 250 kontenerów o stałym rozmiarze (gdzie jeden kontener o stałym rozmiarze = 5 kontenerów). Nie zaleca się jednak konfigurowania emulatora do uruchamiania z ponad 200 kontenerów o stałym rozmiarze. Ze względu na obciążenie, które dodaje do operacji we/wy dysku, co powoduje nieprzewidywalne przekroczenie limitu czasu podczas korzystania z interfejsów API punktu końcowego.

Jeśli podjęto próbę utworzenia kontenera po przekroczeniu liczby bieżącej partycji, Emulator zgłasza wyjątek serviceniedostępne z następującym komunikatem.

> Niestety, obecnie mamy wysokie zapotrzebowanie w tym regionie i w tej chwili nie można zrealizować Twojego żądania. Pracujemy w sposób ciągły, aby zwiększyć pojemność w trybie online, i zachęcamy do ponowienia próby.
> ActivityId: 12345678-1234-1234-1234-123456789abc

Aby zmienić liczbę kontenerów dostępnych w emulatorze usługi Azure Cosmos, uruchom następujące kroki:

1. Usuń wszystkie lokalne dane emulatora usługi Azure Cosmos, klikając prawym przyciskiem myszy ikonę **emulatora Azure Cosmos DB** na pasku zadań, a następnie klikając polecenie **Resetuj dane...**.

1. Usuń wszystkie dane emulatora z tego folderu `%LOCALAPPDATA%\CosmosDBEmulator` .

1. Zamknij wszystkie otwarte wystąpienia, klikając prawym przyciskiem myszy ikonę **emulatora usługi Azure Cosmos DB** w obszarze powiadomień, a następnie klikając polecenie **Exit** (Zakończ). Zamykanie wszystkich wystąpień może potrwać około minuty.

1. Zainstaluj najnowszą wersję [emulatora usługi Azure Cosmos](https://aka.ms/cosmosdb-emulator).

1. Uruchom emulator z flagą PartitionCount, ustawiając wartość <= 250. Przykład: `C:\Program Files\Azure Cosmos DB Emulator> Microsoft.Azure.Cosmos.Emulator.exe /PartitionCount=100`.
 
## <a name="next-steps"></a>Następne kroki

* [Eksportowanie certyfikatów emulatora usługi Azure Cosmos do użytku z aplikacjami Java, Python i Node.js](local-emulator-export-ssl-certificates.md)
* [Problemy z debugowaniem przy użyciu emulatora](troubleshoot-local-emulator.md)
