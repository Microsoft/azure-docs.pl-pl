---
title: Rozwiązywanie problemów z wdrożeniami usługi Azure SQL Edge
description: Informacje o możliwych błędach podczas wdrażania usługi Azure SQL Edge
keywords: SQL Edge, rozwiązywanie problemów, błędy wdrażania
services: sql-edge
ms.service: sql-edge
ms.topic: troubleshooting
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: 517fed0dd9eb1736344546bde9f79e52ee17182f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91333107"
---
# <a name="troubleshooting-azure-sql-edge-deployments"></a>Rozwiązywanie problemów z wdrożeniami usługi Azure SQL Edge 

Ten artykuł zawiera informacje o możliwych błędach podczas wdrażania i korzystania z kontenerów usługi Azure SQL Edge oraz zawiera techniki rozwiązywania problemów ułatwiające rozwiązywanie tych problemów. 

Usługa Azure SQL Edge obsługuje dwa modele wdrażania: 
- Połączone wdrożenie za pomocą Azure IoT Edge: usługa Azure SQL Edge jest dostępna w portalu Azure Marketplace i można ją wdrożyć jako moduł dla [Azure IoT Edge](../iot-edge/about-iot-edge.md). Aby uzyskać więcej informacji, zobacz [wdrażanie usługi Azure SQL Edge](deploy-portal.md).<br>

- Rozłączono wdrożenie: obrazy kontenerów usługi Azure SQL Edge można ściągnąć z centrum Docker i wdrożyć je jako autonomiczny kontener platformy Docker lub w klastrze Kubernetes. Aby uzyskać więcej informacji, zobacz [wdrażanie usługi Azure SQL Edge przy użyciu platformy Docker](disconnected-deployment.md) i [wdrażanie kontenera usługi Azure SQL Edge w Kubernetes](deploy-kubernetes.md).

## <a name="troubleshooting-iot-edge-device-and-deployments"></a>Rozwiązywanie problemów z urządzeniami i wdrożeniami IoT Edge

Jeśli wystąpi błąd podczas wdrażania programu SQL Edge za pomocą Azure IoT Edge, upewnij się, że `iotedge` usługa została prawidłowo skonfigurowana i uruchomiona. Następujące dokumenty mogą być pomocne podczas rozwiązywania problemów związanych z Azure IoT Edge:
- [Typowe problemy i rozwiązania dla Azure IoT Edge](../iot-edge/troubleshoot-common-errors.md).
- [Rozwiązywanie problemów z urządzeniem IoT Edge](../iot-edge/troubleshoot.md)

## <a name="docker-command-errors"></a>Błędy poleceń platformy Docker

W przypadku wystąpienia błędów dla jakichkolwiek `docker` poleceń upewnij się, że usługa platformy Docker jest uruchomiona, a następnie spróbuj uruchomić polecenie z podniesionymi uprawnieniami.

Na przykład w systemie Linux podczas uruchamiania poleceń może zostać wyświetlony następujący błąd `docker` :

```output
Cannot connect to the Docker daemon. Is the docker daemon running on this host?
```

Jeśli ten błąd wystąpi w systemie Linux, spróbuj uruchomić te same polecenia `sudo` . Jeśli to się nie powiedzie, sprawdź, czy usługa Docker jest uruchomiona, i w razie potrzeby uruchom ją.

```bash
sudo systemctl status docker
sudo systemctl start docker
```

W systemie Windows sprawdź, czy uruchamiasz program PowerShell lub wiersz polecenia jako administrator.

## <a name="azure-sql-edge-container-startup-errors"></a>Błędy uruchamiania kontenera usługi Azure SQL Edge

Jeśli nie można uruchomić kontenera programu SQL Edge, spróbuj wykonać następujące testy:

- Jeśli używasz Azure IoT Edge, upewnij się, że obrazy modułów zostały pobrane pomyślnie, a zmienne środowiskowe i opcje tworzenia kontenera są poprawnie określone w manifeście modułu.

- Jeśli używasz wdrożenia opartego na platformie Docker lub Kubernetes, upewnij się, że `docker run` polecenie jest poprawnie sformułowane. Aby uzyskać więcej informacji, zobacz [wdrażanie usługi Azure SQL Edge przy użyciu platformy Docker](disconnected-deployment.md) i [wdrażanie kontenera usługi Azure SQL Edge w Kubernetes](deploy-kubernetes.md).

- Jeśli wystąpi błąd, na przykład, próbujesz `failed to create endpoint CONTAINER_NAME on network bridge. Error starting proxy: listen tcp 0.0.0.0:1433 bind: address already in use.` zmapować port kontenera 1433 na port, który jest już używany. Może się tak zdarzyć, jeśli używasz programu SQL Edge lokalnie na komputerze-hoście. Może się również zdarzyć, gdy uruchomisz dwa kontenery programu SQL Edge i spróbujesz zmapować je na ten sam port hosta. W takim przypadku należy użyć `-p` parametru, aby zmapować port kontenera 1433 na inny port hosta. Na przykład: 

    ```bash
    sudo docker run --cap-add SYS_PTRACE -e 'ACCEPT_EULA=1' -e 'MSSQL_SA_PASSWORD=yourStrong(!)Password' -p 1433:1433 --name azuresqledge -d mcr.microsoft.com/azure-sql-edge-developer.
    ```

- Jeśli wystąpi błąd, na przykład `Got permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock: Get http://%2Fvar%2Frun%2Fdocker.sock/v1.30tdout=1&tail=all: dial unix /var/run/docker.sock: connect: permission denied` podczas próby uruchomienia kontenera, Dodaj użytkownika do grupy Docker w Ubuntu. Następnie wyloguj się i zaloguj ponownie, ponieważ ta zmiana wpłynie na nowe sesje. 

   ```bash
    usermod -aG docker $USER
   ```

- Sprawdź, czy istnieją komunikaty o błędach z kontenera.

   ```bash
   docker logs e69e056c702d
   ```

- Jeśli używasz dowolnego oprogramowania do zarządzania kontenerami, upewnij się, że obsługuje procesy kontenerów działające jako główne. Proces SqlServer w kontenerze jest uruchamiany jako element główny.

- Domyślnie kontenery usługi Azure SQL Edge działają jako użytkownik inny niż główny o nazwie `mssql` . Jeśli używasz punktów instalacji lub woluminów danych do utrwalania danych, upewnij się, że `mssql` użytkownik ma odpowiednie uprawnienia w woluminie. Aby uzyskać więcej informacji, zobacz [Uruchom jako użytkownik spoza katalogu głównego](configure.md#run-azure-sql-edge-as-non-root-user) i [Utrwalaj dane](configure.md#persist-your-data).

- Jeśli kontener platformy Docker programu SQL Edge zostanie zakończony natychmiast po uruchomieniu, Sprawdź dzienniki platformy Docker. Jeśli używasz programu PowerShell w systemie Windows z `docker run` poleceniem, użyj podwójnego cudzysłowu zamiast pojedynczego cudzysłowu. Za pomocą programu PowerShell Core należy używać apostrofów.

- Przejrzyj [dzienniki błędów programu SQL Edge](#errorlogs).

## <a name="sql-edge-connection-failures"></a>Błędy połączenia z usługą SQL Edge

Jeśli nie możesz połączyć się z wystąpieniem programu SQL Edge działającym w kontenerze, spróbuj wykonać następujące testy:

- Upewnij się, że kontener programu SQL Edge jest uruchomiony, przeglądając kolumnę **stan** `docker ps -a` danych wyjściowych. Jeśli nie, użyj, `docker start <Container ID>` aby je uruchomić.

- W przypadku zamapowania na port hosta innego niż domyślny (nie 1433) Upewnij się, że określasz port w parametrach połączenia. Mapowanie **portów można zobaczyć w kolumnie** `docker ps -a` dane wyjściowe. Aby uzyskać więcej informacji na temat nawiązywania połączenia z usługą Azure SQL Edge, zapoznaj [się z tematem Connect and Query Edge Azure SQL](connect.md)

- Jeśli wcześniej wdrożono program SQL Edge z zamapowanym woluminem danych lub kontenerem woluminów danych, a teraz korzystasz z istniejącego zamapowanego woluminu danych lub kontenera woluminu danych, program SQL Edge zignoruje wartość `MSSQL_SA_PASSWORD` zmiennej środowiskowej. Zamiast tego używane zostało wcześniej skonfigurowane hasło użytkownika. Dzieje się tak, ponieważ program SQL Edge ponownie używa istniejących plików głównych baz danych w mapowanym woluminie lub kontenerze woluminów danych. W przypadku uruchomienia tego problemu można użyć następujących opcji:

    - Połącz przy użyciu wcześniej używanego hasła, jeśli jest nadal dostępne.
    - Skonfiguruj program SQL Edge, aby używał innego mapowanego woluminu lub kontenera woluminów danych.
    - Usuń istniejące pliki Master Database (Master. mdf i Mastlog. mdf) z kontenera woluminu mapowanego lub woluminu danych.

- Przejrzyj [dzienniki błędów programu SQL Edge](#errorlogs).

## <a name="sql-edge-setup-and-error-logs"></a><a id="errorlogs"></a> Dzienniki instalacji i błędów programu SQL Edge

Domyślnie dzienniki błędów programu SQL Edge znajdują się w katalogu **/var/opt/MSSQL/log** w kontenerze i można uzyskać do nich dostęp przy użyciu dowolnego z następujących sposobów:

- Jeśli katalog hosta został zainstalowany do **/var/opt/MSSQL** podczas tworzenia kontenera, możesz zamiast tego zajrzeć do podkatalogu **dziennika** w mapowanej ścieżce na hoście.
- Za pomocą interakcyjnego wiersza polecenia, aby połączyć się z kontenerem. Jeśli kontener nie jest uruchomiony, najpierw uruchom kontener. Następnie użyj interakcyjnego wiersza polecenia, aby sprawdzić dzienniki. Identyfikator kontenera można uzyskać, uruchamiając polecenie `docker ps` .

    ```bash
    docker start <ContainerID>
    docker exec -it <ContainerID> "/bin/bash"
    ```

    Z sesji bash wewnątrz kontenera Uruchom następujące polecenia:

    ```bash
    cd /var/opt/mssql/log
    cat errorlog
    ```
- Jeśli kontener programu SQL Edge jest uruchomiony i można nawiązać połączenie z wystąpieniem przy użyciu narzędzi klienckich, można użyć procedury składowanej `sp_readerrorlog` w celu odczytania zawartości dziennika błędów programu SQL Edge.

## <a name="execute-commands-in-a-container"></a>Wykonaj polecenia w kontenerze

Jeśli masz uruchomiony kontener, możesz wykonać polecenia w kontenerze z terminalu hosta.

Aby uzyskać przebieg identyfikatora kontenera:

```bash
docker ps -a
```

Aby uruchomić Terminal bash w przebiegu kontenera:

```bash
docker exec -it <Container ID> /bin/bash
```

Teraz można uruchamiać polecenia tak, jakby były uruchamiane w terminalu wewnątrz kontenera. Po zakończeniu wpisz polecenie `exit` . Spowoduje to wyjście z sesji polecenia interaktywnego, ale nadal będzie można uruchomić kontener.

### <a name="enabling-verbose-logging"></a>Włączanie pełnego rejestrowania

Jeśli domyślny poziom rejestrowania dla aparatu przesyłania strumieniowego nie zapewnia wystarczającej ilości informacji, rejestrowanie debugowania dla aparatu przesyłania strumieniowego może być włączone w programie SQL Edge. Aby włączyć rejestrowanie debugowania, Dodaj `RuntimeLogLevel=debug` zmienną środowiskową do wdrożenia programu SQL Edge. Po włączeniu rejestrowania debugowania spróbuj odtworzyć problem i Sprawdź dzienniki pod kątem odpowiednich komunikatów lub wyjątków. 

> [!NOTE]
> Opcja pełnego rejestrowania powinna być używana tylko w celu rozwiązywania problemów, a nie do regularnego obciążenia produkcyjnego. 


## <a name="next-steps"></a>Następne kroki

- [Machine Learning i sztuczna inteligencja przy użyciu ONNX w programie SQL Edge](onnx-overview.md)
- [Przesyłanie strumieniowe danych w usłudze Azure SQL Edge](stream-data.md)
- [Przechowywanie i czyszczenie danych](data-retention-overview.md)
- [Wypełnianie odstępów czasu i brak wartości w postaci](imputing-missing-values.md)







