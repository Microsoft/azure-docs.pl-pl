---
title: Instalowanie i programowanie lokalnie za pomocą emulatora Azure Cosmos DB
description: Dowiedz się, jak zainstalować emulator Azure Cosmos DB i korzystać z niego w środowiskach Windows, Linux, macOS i Windows Docker. Za pomocą emulatora można lokalnie opracowywać i testować swoją aplikację bez konieczności tworzenia subskrypcji platformy Azure.
ms.service: cosmos-db
ms.topic: how-to
author: markjbrown
ms.author: mjbrown
ms.date: 09/22/2020
ms.custom: devx-track-csharp, contperfq1
ms.openlocfilehash: e846f00388ce21690729f62592c86b73cc42c3f3
ms.sourcegitcommit: 2989396c328c70832dcadc8f435270522c113229
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/19/2020
ms.locfileid: "92173789"
---
# <a name="install-and-use-the-azure-cosmos-emulator-for-local-development-and-testing"></a>Instalowanie i używanie emulatora usługi Azure Cosmos na potrzeby lokalnego tworzenia i testowania

Emulator usługi Azure Cosmos udostępnia środowisko lokalne, które emuluje usługę Azure Cosmos DB na potrzeby programowania. Korzystając z emulatora usługi Azure Cosmos, możesz tworzyć i testować aplikację lokalnie, bez konieczności tworzenia subskrypcji platformy Azure ani ponoszenia kosztów. Gdy aplikacja działa w emulatorze usługi Azure Cosmos, możesz przełączyć się do korzystania z konta usługi Azure Cosmos w chmurze. W tym artykule opisano sposób instalowania emulatora i korzystania z niego w środowiskach Windows, Linux, macOS i Windows Docker.

## <a name="download-the-emulator"></a>Pobieranie emulatora

Aby rozpocząć, Pobierz i zainstaluj najnowszą wersję emulatora usługi Azure Cosmos na komputerze lokalnym. W artykule [Informacje o wersji emulatora](local-emulator-release-notes.md) znajduje się lista wszystkich dostępnych wersji i aktualizacji funkcji wprowadzonych w poszczególnych wydaniach.

:::image type="icon" source="media/local-emulator/download-icon.png" border="false":::**[Pobieranie emulatora usługi Azure Cosmos](https://aka.ms/cosmosdb-emulator)**

Aplikacje można opracowywać przy użyciu emulatora usługi Azure Cosmos z kontami interfejsu API [SQL](local-emulator.md#sql-api), [Cassandra](local-emulator.md#cassandra-api), [MongoDB](local-emulator.md#azure-cosmos-dbs-api-for-mongodb), [Gremlin](local-emulator.md#gremlin-api)i [Table](local-emulator.md#table-api) . Obecnie Eksplorator danych w emulatorze w pełni obsługuje wyświetlanie danych SQL. dane utworzone za pomocą MongoDB, Gremlin/Graph i Cassandra aplikacje klienckie nie są w tej chwili widoczne. Aby dowiedzieć się więcej, zobacz [jak nawiązać połączenie z punktem końcowym emulatora](#connect-with-emulator-apis) z różnych interfejsów API.

## <a name="how-does-the-emulator-work"></a>Jak działa emulator?

Emulator Azure Cosmos zapewnia emulację o wysokiej dokładności usługi Azure Cosmos DB. Obsługuje ona równoważne funkcje, jak Azure Cosmos DB, które obejmują tworzenie danych, wykonywanie zapytań dotyczących danych, Inicjowanie obsługi i skalowanie kontenerów oraz wykonywanie procedur składowanych i wyzwalaczy. Możesz tworzyć i testować aplikacje za pomocą emulatora usługi Azure Cosmos i wdrażać je na platformie Azure na skalę globalną, aktualizując punkt końcowy połączenia Azure Cosmos DB.

Chociaż emulator dokładnie odzwierciedla działanie usługi Azure Cosmos DB, różni się od niej pod względem wdrożenia. Na przykład emulator używa standardowych składników systemu operacyjnego — między innymi lokalnego systemu plików jako magazynu trwałego i stosu protokołu HTTPS na potrzeby łączności. Funkcje, które opierają się na infrastrukturze platformy Azure, takiej jak replikacja globalna, opóźnienie w milisekundach jednocyfrowych dla operacji odczytu/zapisu i możliwość dostosowania poziomy spójności nie są stosowane podczas korzystania z emulatora.

Dane można migrować między emulatorem usługi Azure Cosmos i usługą Azure Cosmos DB przy użyciu narzędzia do [migracji danych Azure Cosmos DB](https://github.com/azure/azure-documentdb-datamigrationtool).

## <a name="differences-between-the-emulator-and-the-cloud-service"></a>Różnice między emulatorem a usługą w chmurze

Ponieważ emulator usługi Azure Cosmos udostępnia emulowane środowisko działające na lokalnej stacji roboczej dewelopera, istnieją pewne różnice w działaniu między emulatorem a kontem usługi Azure Cosmos w chmurze:

* Obecnie okienko **Eksplorator danych** w emulatorze w pełni obsługuje tylko klientów interfejsu API SQL. Widok **Eksplorator danych** i operacje dotyczące Azure Cosmos DB interfejsów API, takich jak MongoDB, Table, Graph i Cassandra, nie są w pełni obsługiwane.

* Emulator obsługuje tylko jedno stałe konto i dobrze znany klucz podstawowy. Nie można wygenerować ponownie klucza podczas korzystania z emulatora usługi Azure Cosmos, ale można zmienić klucz domyślny przy użyciu opcji [wiersza polecenia](emulator-command-line-parameters.md) .

* Za pomocą emulatora można utworzyć konto usługi Azure Cosmos tylko w trybie [przepływności aprowizacji](set-throughput.md) . obecnie nie obsługuje trybu [bezserwerowego](serverless.md) .

* Emulator nie jest skalowalną usługą i nie obsługuje dużej liczby kontenerów. W przypadku korzystania z emulatora usługi Azure Cosmos domyślnie można utworzyć maksymalnie 25 kontenerów o stałym rozmiarze o 400 RU/s (obsługiwane tylko przy użyciu zestawów SDK Azure Cosmos DB) lub 5 nieograniczoną liczbę kontenerów. Aby uzyskać więcej informacji na temat zmiany tej wartości, zobacz [Ustawianie artykułu wartość PartitionCount](emulator-command-line-parameters.md#set-partitioncount) .

* Emulator nie oferuje różnych [Azure Cosmos DB poziomów spójności](consistency-levels.md) , takich jak usługa w chmurze.

* Emulator nie oferuje [wieloregionowej replikacji](distribute-data-globally.md).

* Ponieważ kopia emulatora usługi Azure Cosmos może nie być zawsze aktualna przy użyciu najnowszych zmian w usłudze Azure Cosmos DB, zawsze należy odwołać się do [planisty wydajności Azure Cosmos DB](estimate-ru-with-capacity-planner.md) , aby dokładnie oszacować zapotrzebowanie na przepływność (jednostek ru) aplikacji.

* Emulator obsługuje maksymalny rozmiar właściwości identyfikatora wynoszący 254 znaków.

## <a name="install-the-emulator"></a>Zainstaluj emulator

Przed zainstalowaniem emulatora upewnij się, że masz następujące wymagania dotyczące sprzętu i oprogramowania:

* Wymagania dotyczące oprogramowania:
  * Obecnie obsługiwane są systemy Windows Server 2012 R2, Windows Server 2016, 2019 lub Windows 8, 10 systemu operacyjnego hosta. System operacyjny hosta z włączonym Active Directory nie jest obecnie obsługiwany.
  * 64-bitowy system operacyjny

* Minimalne wymagania sprzętowe:
  * 2 GB pamięci RAM
  * 10 GB dostępnego miejsca na dysku twardym

* Aby zainstalować, skonfigurować i uruchomić emulator usługi Azure Cosmos, musisz mieć uprawnienia administracyjne na tym komputerze. Emulator doda certyfikat, a także ustawi reguły zapory w celu uruchomienia jej usług. W związku z tym, aby emulator mógł wykonać takie operacje, wymagane są uprawnienia administratora.

Aby rozpocząć, Pobierz i zainstaluj najnowszą wersję [emulatora usługi Azure Cosmos](https://aka.ms/cosmosdb-emulator) na komputerze lokalnym. Jeśli podczas instalacji emulatora wystąpią jakiekolwiek problemy, zapoznaj się z artykułem [Rozwiązywanie problemów z emulatorem](troubleshoot-local-emulator.md) .

W zależności od wymagań systemowych można uruchomić emulator w [systemie Windows](#run-on-windows), [Docker for Windows](#run-on-windows-docker), [Linux lub macOS](#run-on-linux-macos) zgodnie z opisem w następnych sekcjach tego artykułu.

## <a name="check-for-emulator-updates"></a>Sprawdź dostępność aktualizacji emulatora

Każda wersja emulatora zawiera zestaw aktualizacji funkcji lub poprawek błędów. Aby wyświetlić dostępne wersje, przeczytaj artykuł [Informacje o wersji emulatora](local-emulator-release-notes.md) .

Po zakończeniu instalacji, jeśli użyto ustawień domyślnych, dane odpowiadające emulatorowi są zapisywane w lokalizacji%LOCALAPPDATA%\CosmosDBEmulator. Można skonfigurować inną lokalizację przy użyciu ustawień opcjonalne ścieżki danych. jest to `/DataPath=PREFERRED_LOCATION` [parametr wiersza polecenia](emulator-command-line-parameters.md). Nie ma gwarancji, że dane utworzone w jednej wersji emulatora usługi Azure Cosmos są dostępne w przypadku korzystania z innej wersji. Jeśli chcesz utrzymywać dane przez długi czas, zalecamy przechowywanie tych danych w ramach konta usługi Azure Cosmos, a nie emulatora usługi Azure Cosmos.

## <a name="use-the-emulator-on-windows"></a><a id="run-on-windows"></a>Korzystanie z emulatora w systemie Windows

Emulator usługi Azure Cosmos jest domyślnie instalowany w `C:\Program Files\Azure Cosmos DB Emulator` lokalizacji. Aby uruchomić emulator Azure Cosmos w systemie Windows, wybierz przycisk **Start** lub naciśnij klawisz systemu Windows. Zacznij wpisywać tekst **emulatora usługi Azure Cosmos**i wybierz emulator z listy aplikacji.

:::image type="content" source="./media/local-emulator/database-local-emulator-start.png" alt-text="Wybierz przycisk Start lub naciśnij klawisz systemu Windows, zacznij wpisywać tekst emulatora usługi Azure Cosmos i wybierz emulator z listy aplikacji":::

Po uruchomieniu emulatora zobaczysz ikonę w obszarze powiadomień paska zadań systemu Windows. Automatycznie otwiera Eksploratora danych usługi Azure Cosmos w przeglądarce przy użyciu `https://localhost:8081/_explorer/index.html` adresu URL adresu URL.

:::image type="content" source="./media/local-emulator/database-local-emulator-taskbar.png" alt-text="Wybierz przycisk Start lub naciśnij klawisz systemu Windows, zacznij wpisywać tekst emulatora usługi Azure Cosmos i wybierz emulator z listy aplikacji":::

Możesz również uruchomić i zatrzymać emulator z poleceń wiersza polecenia lub programu PowerShell. Aby uzyskać więcej informacji, zobacz artykuł dotyczący [narzędzia wiersza polecenia](emulator-command-line-parameters.md) .

Emulator usługi Azure Cosmos jest domyślnie uruchamiany na maszynie lokalnej ("localhost"), który nasłuchuje na porcie 8081. Adres jest wyświetlany jako `https://localhost:8081/_explorer/index.html`. Jeśli zamkniesz Eksploratora i chcesz otworzyć go później, możesz otworzyć adres URL w przeglądarce lub uruchomić go za pomocą emulatora usługi Azure Cosmos na ikonie na pasku zadań systemu Windows, jak pokazano poniżej.

:::image type="content" source="./media/local-emulator/database-local-emulator-data-explorer-launcher.png" alt-text="Wybierz przycisk Start lub naciśnij klawisz systemu Windows, zacznij wpisywać tekst emulatora usługi Azure Cosmos i wybierz emulator z listy aplikacji":::

## <a name="use-the-emulator-on-docker-for-windows"></a><a id="run-on-windows-docker"></a>Korzystanie z emulatora w Docker for Windows

Emulator Azure Cosmos można uruchomić w kontenerze platformy Docker systemu Windows. Aby uzyskać więcej informacji, zobacz [centrum platformy Docker](https://hub.docker.com/r/microsoft/azure-cosmosdb-emulator/) dla Docker pull polecenia i [GitHub](https://github.com/Azure/azure-cosmos-db-emulator-docker) `Dockerfile` . Obecnie emulator nie działa na platformie Docker dla Oracle Linux. Wykonaj poniższe instrukcje, aby uruchomić emulator w Docker for Windows:

1. Po zainstalowaniu [Docker for Windows](https://www.docker.com/docker-windows) przejdź do kontenerów systemu Windows, klikając prawym przyciskiem myszy ikonę platformy Docker na pasku narzędzi i wybierając pozycję **Przełącz na kontenery systemu Windows**.

1. Następnie ściągnij obraz emulatora z usługi Docker Hub, uruchamiając następujące polecenie w wybranej powłoce.

   ```bash
   docker pull mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator
   ```

1. Aby uruchomić obraz, uruchom następujące polecenia w zależności od wiersza polecenia lub środowiska programu PowerShell:

   # <a name="command-line"></a>[Wiersz polecenia](#tab/cli)

   ```bash

   md %LOCALAPPDATA%\CosmosDBEmulator\bind-mount

   docker run --name azure-cosmosdb-emulator --memory 2GB --mount "type=bind,source=%LOCALAPPDATA%\CosmosDBEmulator\bind-mount,destination=C:\CosmosDB.Emulator\bind-mount" --interactive --tty -p 8081:8081 -p 8900:8900 -p 8901:8901 -p 8902:8902 -p 10250:10250 -p 10251:10251 -p 10252:10252 -p 10253:10253 -p 10254:10254 -p 10255:10255 -p 10256:10256 -p 10350:10350 mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator
   ```
   Obrazy platformy Docker oparte na systemie Windows mogą nie być ogólnie zgodne z każdym systemem operacyjnym hosta Windows. Na przykład domyślny obraz emulatora usługi Azure Cosmos jest zgodny z systemami Windows 10 i Windows Server 2016. Jeśli potrzebujesz obrazu zgodnego z systemem Windows Server 2019, uruchom następujące polecenie zamiast:

   ```bash
   docker run --name azure-cosmosdb-emulator --memory 2GB --mount "type=bind,source=%hostDirectory%,destination=C:\CosmosDB.Emulator\bind-mount" --interactive --tty -p 8081:8081 -p 8900:8900 -p 8901:8901 -p 8902:8902 -p 10250:10250 -p 10251:10251 -p 10252:10252 -p 10253:10253 -p 10254:10254 -p 10255:10255 -p 10256:10256 -p 10350:10350 mcr.microsoft.com/cosmosdb/winsrv2019/azure-cosmos-emulator:latest
   ```

   # <a name="powershell"></a>[Program PowerShell](#tab/powershell)

   ```powershell

   md $env:LOCALAPPDATA\CosmosDBEmulator\bind-mount 2>null

   docker run --name azure-cosmosdb-emulator --memory 2GB --mount "type=bind,source=$env:LOCALAPPDATA\CosmosDBEmulator\bind-mount,destination=C:\CosmosDB.Emulator\bind-mount" --interactive --tty -p 8081:8081 -p 8900:8900 -p 8901:8901 -p 8902:8902 -p 10250:10250 -p 10251:10251 -p 10252:10252 -p 10253:10253 -p 10254:10254 -p 10255:10255 -p 10256:10256 -p 10350:10350 mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator

   ```

   Odpowiedź wygląda mniej więcej tak:

   ```bash
   Starting emulator
   Emulator Endpoint: https://172.20.229.193:8081/
   Primary Key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
   Exporting SSL Certificate
   You can import the SSL certificate from an administrator command prompt on the host by running:
   cd /d %LOCALAPPDATA%\CosmosDBEmulatorCert
   powershell .\importcert.ps1
   --------------------------------------------------------------------------------------------------
   Starting interactive shell
   ```
   ---

   > [!NOTE]
   > Jeśli podczas wykonywania `docker run` polecenia zostanie wyświetlony komunikat o błędzie dotyczącym konfliktu portów (czyli jeśli określony port jest już używany), należy przekazać port niestandardowy, zmieniając numery portów. Na przykład, można zmienić parametr "-p 8081:8081" na "-p 443:8081"

1. Teraz użyj punktu końcowego emulatora i klucza podstawowego z odpowiedzi i zaimportuj certyfikat TLS/SSL do hosta. Aby zaimportować certyfikat TLS/SSL, uruchom następujące kroki z poziomu wiersza polecenia administratora:

   # <a name="command-line"></a>[Wiersz polecenia](#tab/cli)

   ```bash
   cd  %LOCALAPPDATA%\CosmosDBEmulator\bind-mount
   powershell .\importcert.ps1
   ```

   # <a name="powershell"></a>[Program PowerShell](#tab/powershell)

   ```powershell
   cd $env:LOCALAPPDATA\CosmosDBEmulator\bind-mount
   .\importcert.ps1
   ```
   ---

1. Jeśli zamkniesz interaktywną powłokę po uruchomieniu emulatora, spowoduje to zamknięcie kontenera emulatora. Aby ponownie otworzyć Eksploratora danych, przejdź do następującego adresu URL w przeglądarce. Punkt końcowy emulatora znajduje się w powyższym komunikacie odpowiedzi.

   `https://<emulator endpoint provided in response>/_explorer/index.html`

Jeśli masz uruchomioną aplikację kliencką platformy .NET w kontenerze platformy Docker systemu Linux i jeśli korzystasz z emulatora usługi Azure Cosmos na maszynie hosta, użyj instrukcji w następnej sekcji, aby zaimportować certyfikat do kontenera platformy Docker systemu Linux.

### <a name="regenerate-the-emulator-certificates-when-running-on-a-docker-container"></a>Ponowne generowanie certyfikatów emulatora podczas uruchamiania w kontenerze platformy Docker

W przypadku uruchamiania emulatora w kontenerze platformy Docker Certyfikaty skojarzone z emulatorem są generowane ponownie za każdym razem, gdy zatrzymasz i uruchomisz odpowiednie kontenery. Z tego powodu trzeba ponownie zaimportować certyfikaty po każdym uruchomieniu kontenera. Aby obejść to ograniczenie, można użyć pliku redagowania platformy Docker w celu powiązania kontenera Docker z określonym adresem IP i obrazem kontenera.

Na przykład możesz użyć poniższej konfiguracji w pliku programu Docker, pamiętaj, aby sformatować go zgodnie z wymaganiami: 

```yml
version: '2.4' # Do not upgrade to 3.x yet, unless you plan to use swarm/docker stack: https://github.com/docker/compose/issues/4513

networks:
  default:
    external: false
    ipam:
      driver: default
      config:
        - subnet: "172.16.238.0/24"

services:

  # First create a directory that will hold the emulator traces and certificate to be imported
  # set hostDirectory=C:\emulator\bind-mount
  # mkdir %hostDirectory%

  cosmosdb:
    container_name: "azurecosmosemulator"
    hostname: "azurecosmosemulator"
    image: 'mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator'
    platform: windows
    tty: true
    mem_limit: 3GB
    ports:
        - '8081:8081'
        - '8900:8900'
        - '8901:8901'
        - '8902:8902'
        - '10250:10250'
        - '10251:10251'
        - '10252:10252'
        - '10253:10253'
        - '10254:10254'
        - '10255:10255'
        - '10256:10256'
        - '10350:10350'
    networks:
      default:
        ipv4_address: 172.16.238.246
    volumes:
        - '${hostDirectory}:C:\CosmosDB.Emulator\bind-mount'
```

## <a name="use-the-emulator-on-linux-or-macos"></a><a id="run-on-linux-macos"></a>Korzystanie z emulatora w systemie Linux lub macOS

Obecnie emulator usługi Azure Cosmos można uruchomić tylko w systemie Windows. W przypadku korzystania z systemu Linux lub macOS można uruchomić emulator w maszynie wirtualnej z systemem Windows hostowanej w funkcji hypervisor, takiej jak Parallels lub VirtualBox.

> [!NOTE]
> Za każdym razem, gdy uruchamiasz maszynę wirtualną z systemem Windows, która jest hostowana w funkcji hypervisor, należy ponownie zaimportować certyfikat, ponieważ adres IP maszyny wirtualnej ulegnie zmianie. Importowanie certyfikatu nie jest wymagane w przypadku skonfigurowania maszyny wirtualnej w celu zachowania adresu IP.

Wykonaj następujące kroki, aby użyć emulatora w środowiskach Linux lub macOS:

1. Uruchom następujące polecenie z maszyny wirtualnej z systemem Windows i Zanotuj adres IPv4:

   ```bash
   ipconfig.exe
   ```

1. W aplikacji Zmień adres URL punktu końcowego tak, aby używał adresu IPv4 zwróconego przez `ipconfig.exe` zamiast `localhost` .

1. Na maszynie wirtualnej z systemem Windows uruchom emulator Azure Cosmos z wiersza polecenia, korzystając z następujących opcji. Aby uzyskać szczegółowe informacje na temat parametrów obsługiwanych przez wiersz polecenia, zobacz [informacje na temat narzędzia wiersza polecenia emulatora](emulator-command-line-parameters.md):

   ```bash
   Microsoft.Azure.Cosmos.Emulator.exe /AllowNetworkAccess /Key=C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
   ```

1. Na koniec należy rozwiązać proces zaufania certyfikatu między aplikacją działającą w środowisku Linux lub Mac a emulatorem. Aby rozwiązać certyfikat, można użyć jednej z następujących dwóch opcji:

   1. [Zaimportuj certyfikat emulatora TLS/SSL do środowiska Linux lub Mac](#import-certificate) lub
   2. [Wyłącz weryfikację protokołu TLS/SSL w aplikacji](#disable-ssl-validation)

### <a name="option-1-import-the-emulator-tlsssl-certificate"></a><a id="import-certificate"></a>Opcja 1: Importowanie certyfikatu emulatora TLS/SSL

W poniższych sekcjach pokazano, jak zaimportować certyfikat emulatora TLS/SSL do środowiska Linux i macOS.

#### <a name="linux-environment"></a>Środowisko systemu Linux

Jeśli pracujesz w systemie Linux, .NET przekazuje na OpenSSL w celu sprawdzenia poprawności:

1. [Wyeksportuj certyfikat w formacie PFX](local-emulator-export-ssl-certificates.md#export-emulator-certificate). Opcja PFX jest dostępna podczas wybierania eksportu klucza prywatnego.

1. Skopiuj ten plik PFX do środowiska systemu Linux.

1. Konwertuj plik PFX na plik CRT

   ```bash
   openssl pkcs12 -in YourPFX.pfx -clcerts -nokeys -out YourCTR.crt
   ```

1. Skopiuj plik CRT do folderu, który zawiera niestandardowe certyfikaty w dystrybucji systemu Linux. Często w przypadku dystrybucji Debian znajdują się w `/usr/local/share/ca-certificates/` .

   ```bash
   cp YourCTR.crt /usr/local/share/ca-certificates/
   ```

1. Zaktualizuj certyfikaty TLS/SSL, co spowoduje zaktualizowanie `/etc/ssl/certs/` folderu.

   ```bash
   update-ca-certificates
   ```

#### <a name="macos-environment"></a>środowisko macOS

Jeśli pracujesz na komputerze Mac, wykonaj następujące czynności:

1. [Wyeksportuj certyfikat w formacie PFX](local-emulator-export-ssl-certificates.md#export-emulator-certificate). Opcja PFX jest dostępna podczas wybierania eksportu klucza prywatnego.

1. Skopiuj ten plik PFX do środowiska Mac.

1. Otwórz aplikację *dostęp do łańcucha kluczy* i zaimportuj plik PFX.

1. Otwórz listę certyfikatów i zidentyfikuj ją z nazwą `localhost` .

1. Otwórz menu kontekstowe dla danego elementu, wybierz opcję *Pobierz element* i w obszarze *zaufanie*w  >  *przypadku korzystania z tego certyfikatu* , wybierz pozycję *Zawsze ufaj*. 

   :::image type="content" source="./media/local-emulator/mac-trust-certificate.png" alt-text="Wybierz przycisk Start lub naciśnij klawisz systemu Windows, zacznij wpisywać tekst emulatora usługi Azure Cosmos i wybierz emulator z listy aplikacji":::
  
### <a name="option-2-disable-the-ssl-validation-in-the-application"></a><a id="disable-ssl-validation"></a>Opcja 2. wyłączenie weryfikacji SSL w aplikacji

Wyłączenie walidacji protokołu SSL jest zalecane tylko w celach programistycznych i nie powinno być wykonywane w środowisku produkcyjnym. W poniższych przykładach pokazano, jak wyłączyć weryfikację SSL dla aplikacji .NET i Node.js.

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

## <a name="enable-access-to-emulator-on-a-local-network"></a>Włączanie dostępu do emulatora w sieci lokalnej

Jeśli masz wiele maszyn przy użyciu pojedynczej sieci, a następnie skonfigurujesz emulator na jednej maszynie i chcesz uzyskać do niego dostęp z innego komputera. W takim przypadku należy włączyć dostęp do emulatora w sieci lokalnej.

Emulator możesz uruchomić w sieci lokalnej. Aby włączyć dostęp do sieci, należy określić `/AllowNetworkAccess` opcję w [wierszu polecenia](emulator-command-line-parameters.md), która wymaga również określenia `/Key=key_string` lub `/KeyFile=file_name` . Możesz użyć `/GenKeyFile=file_name` do wygenerowania pliku z kluczem losowym z góry. Następnie możesz przekazać ten program do `/KeyFile=file_name` lub `/Key=contents_of_file` .

Aby włączyć dostęp do sieci po raz pierwszy, użytkownik powinien zamknąć emulator i usunąć *%LocalAppData%\CosmosDBEmulator*katalogu danych emulatora.

## <a name="authenticate-connections-when-using-emulator"></a><a id="authenticate-requests"></a>Uwierzytelniaj połączenia w przypadku korzystania z emulatora

Podobnie jak w przypadku Azure Cosmos DB w chmurze, każde żądanie, które należy wykonać względem emulatora usługi Azure Cosmos, musi zostać uwierzytelnione. Emulator usługi Azure Cosmos obsługuje tylko bezpieczną komunikację za pośrednictwem protokołu TLS. Emulator Azure Cosmos obsługuje pojedyncze stałe konto i dobrze znane klucze uwierzytelniania na potrzeby uwierzytelniania przy użyciu klucza podstawowego. To konto i klucz są jedynymi poświadczeniami, które mogą być używane z emulatorem usługi Azure Cosmos. Oto one:

```bash
Account name: localhost:<port>
Account key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
```

> [!NOTE]
> Klucz podstawowy obsługiwany przez emulator usługi Azure Cosmos jest przeznaczony do użycia tylko z emulatorem. Nie można używać konta i klucza Azure Cosmos DB produkcyjnych z emulatorem usługi Azure Cosmos.

> [!NOTE]
> Jeśli emulator został uruchomiony przy użyciu opcji/Key, zamiast klucza domyślnego Użyj wygenerowanego klucza `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==` . Aby uzyskać więcej informacji na temat opcji/Key, zobacz [informacje dotyczące narzędzia wiersza polecenia.](emulator-command-line-parameters.md)

## <a name="connect-to-different-apis-with-the-emulator"></a><a id="connect-with-emulator-apis"></a>Łączenie z innymi interfejsami API za pomocą emulatora

### <a name="sql-api"></a>Interfejs API SQL

Po uruchomieniu emulatora usługi Azure Cosmos na pulpicie możesz użyć dowolnego obsługiwanego [zestawu SDK Azure Cosmos DB](sql-api-sdk-dotnet-standard.md) lub [interfejsu API REST Azure Cosmos DB](/rest/api/cosmos-db/) , aby móc korzystać z emulatora. Emulator usługi Azure Cosmos obejmuje również wbudowany Eksplorator danych, który umożliwia tworzenie kontenerów dla interfejsu API SQL lub Azure Cosmos DB dla interfejsu API usługi Mongo DB. Za pomocą Eksploratora danych można wyświetlać i edytować elementy bez pisania kodu.

```csharp
// Connect to the Azure Cosmos emulator running locally
CosmosClient client = new CosmosClient(
   "https://localhost:8081", 
    "C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==");

```

### <a name="azure-cosmos-dbs-api-for-mongodb"></a>Interfejs API usługi Azure Cosmos DB dla bazy danych MongoDB

Po uruchomieniu emulatora usługi Azure Cosmos na pulpicie możesz użyć [interfejsu API Azure Cosmos DB](mongodb-introduction.md) , aby MongoDB do współdziałania z emulatorem. Uruchom emulator z [wiersza polecenia](emulator-command-line-parameters.md) jako administrator z "/EnableMongoDbEndpoint". Następnie użyj następujących parametrów połączenia, aby nawiązać połączenie z kontem interfejsu API MongoDB:

```bash
mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true
```

### <a name="table-api"></a>Interfejs API tabel

Po uruchomieniu emulatora usługi Azure Cosmos na pulpicie możesz użyć [zestawu SDK interfejs API tabel Azure Cosmos DB](table-storage-how-to-use-dotnet.md) , aby korzystać z emulatora. Uruchom emulator z [wiersza polecenia](emulator-command-line-parameters.md) jako administrator z "/EnableTableEndpoint". Następnie uruchom następujący kod, aby nawiązać połączenie z kontem interfejsu API tabel:

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

Uruchom emulator z [wiersza polecenia](emulator-command-line-parameters.md) administratora z "/EnableCassandraEndpoint". Alternatywnie można również ustawić zmienną środowiskową `AZURE_COSMOS_EMULATOR_CASSANDRA_ENDPOINT=true` .

1. [Zainstaluj Język Python 2,7](https://www.python.org/downloads/release/python-2716/)

1. [Instalowanie interfejsu wiersza polecenia Cassandra/CQLSH](https://cassandra.apache.org/download/)

1. Uruchom następujące polecenia w zwykłym oknie wiersza polecenia:

   ```bash
   set Path=c:\Python27;%Path%
   cd /d C:\sdk\apache-cassandra-3.11.3\bin
   set SSL_VERSION=TLSv1_2
   set SSL_VALIDATE=false
   cqlsh localhost 10350 -u localhost -p C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw== --ssl
   ```

1. W powłoce CQLSH Uruchom następujące polecenia, aby nawiązać połączenie z punktem końcowym Cassandra:

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

Uruchom emulator z [wiersza polecenia](emulator-command-line-parameters.md)administratora z "/EnableGremlinEndpoint". Alternatywnie można również ustawić zmienną środowiskową `AZURE_COSMOS_EMULATOR_GREMLIN_ENDPOINT=true`

1. [Zainstaluj Apache-tinkerpop-Gremlin-Console-3.3.4](https://archive.apache.org/dist/tinkerpop/3.3.4).

1. Z poziomu Eksploratora danych emulatora Utwórz bazę danych "DB1" i kolekcję "coll1"; w przypadku klucza partycji wybierz pozycję "/name"

1. Uruchom następujące polecenia w zwykłym oknie wiersza polecenia:

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

1. W powłoce Gremlin Uruchom następujące polecenia, aby nawiązać połączenie z punktem końcowym Gremlin:

   ```bash
   :remote connect tinkerpop.server conf/remote-localcompute.yaml
   :remote console
   :> g.V()
   :> g.addV('person1').property(id, '1').property('name', 'somename1')
   :> g.addV('person2').property(id, '2').property('name', 'somename2')
   :> g.V()
   ```

## <a name="uninstall-the-local-emulator"></a><a id="uninstall"></a>Odinstalowywanie lokalnego emulatora

Aby odinstalować emulator, wykonaj następujące kroki:

1. Zamknij wszystkie otwarte wystąpienia emulatora lokalnego, klikając prawym przyciskiem myszy ikonę **emulatora usługi Azure Cosmos** na pasku zadań, a następnie wybierając pozycję **Zakończ**. Zamykanie wszystkich wystąpień może potrwać około minuty.

1. W polu wyszukiwania systemu Windows wpisz **aplikacje & funkcje** i wybierz pozycję **aplikacje & funkcje (Ustawienia systemowe)** .

1. Na liście aplikacji przewiń do **emulatora Azure Cosmos DB**, wybierz go, kliknij polecenie **Odinstaluj**, a następnie potwierdź i wybierz ponownie przycisk **Odinstaluj** .

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób korzystania z lokalnego emulatora do bezpłatnego tworzenia lokalnego. Teraz możesz przechodzić do następnych artykułów:

* [Eksportowanie certyfikatów emulatora usługi Azure Cosmos do użytku z aplikacjami Java, Python i Node.js](local-emulator-export-ssl-certificates.md)
* [Używanie parametrów wiersza polecenia i poleceń programu PowerShell do sterowania emulatorem](emulator-command-line-parameters.md)
* [Problemy z debugowaniem przy użyciu emulatora](troubleshoot-local-emulator.md)
