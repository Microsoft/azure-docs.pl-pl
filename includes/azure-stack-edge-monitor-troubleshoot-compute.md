---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 08/30/2020
ms.author: alkohli
ms.openlocfilehash: 92ccb6127e624ace9e719ffd23324b3a1b971f72
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89272152"
---
Na urządzeniu brzegowym Azure Stack, na którym skonfigurowano rolę obliczeniową, można rozwiązywać problemy lub monitorować urządzenie przy użyciu dwóch różnych zestawów poleceń.

- Korzystanie z `iotedge` poleceń. Te polecenia są dostępne dla podstawowych operacji na urządzeniu.
- Korzystanie z `dkrdbe` poleceń. Te polecenia są dostępne dla obszernego zestawu operacji dla urządzenia.

Aby wykonać jeden z powyższych zestawów poleceń, należy [nawiązać połączenie z interfejsem programu PowerShell](#connect-to-the-powershell-interface).

### <a name="use-iotedge-commands"></a>Użyj `iotedge` poleceń

Aby wyświetlić listę dostępnych poleceń, Połącz się [z interfejsem programu PowerShell](#connect-to-the-powershell-interface) i Użyj `iotedge` funkcji.

```powershell
[10.100.10.10]: PS>iotedge -?                                                                                                                                                                                                 Usage: iotedge COMMAND

Commands:
   check
   list
   logs
   restart

[10.100.10.10]: PS>
```

W poniższej tabeli znajduje się krótki opis poleceń dostępnych dla `iotedge` :

|command  |Opis |
|---------|---------|
|`check`     | Przeprowadzaj automatyczne sprawdzanie typowych problemów z konfiguracją i połączeniem       |
|`list`     | Wyświetlanie listy modułów         |
|`logs`     | Pobieranie dzienników modułu        |
|`restart`     | Zatrzymywanie i ponowne uruchamianie modułu         |


### <a name="use-dkrdbe-commands"></a>Użyj `dkrdbe` poleceń

Aby wyświetlić listę dostępnych poleceń, Połącz się [z interfejsem programu PowerShell](#connect-to-the-powershell-interface) i Użyj `dkrdbe` funkcji.

```powershell
[10.100.10.10]: PS>dkrdbe -?
Usage: dkrdbe COMMAND

Commands:
   image [prune]
   images
   inspect
   login
   logout
   logs
   port
   ps
   pull
   start
   stats
   stop
   system [df]
   top

[10.100.10.10]: PS>
```
W poniższej tabeli znajduje się krótki opis poleceń dostępnych dla `dkrdbe` :

|command  |Opis |
|---------|---------|
|`image`     | Zarządzanie obrazami. Aby usunąć nieużywane obrazy, użyj: `dkrdbe image prune -a -f`       |
|`images`     | Wyświetlanie obrazów         |
|`inspect`     | Zwracanie informacji niskiego poziomu dotyczących obiektów platformy Docker         |
|`login`     | Logowanie do rejestru platformy Docker         |
|`logout`     | Wylogowywanie z rejestru platformy Docker         |
|`logs`     | Pobieranie dzienników kontenera        |
|`port`     | Wyświetlanie listy mapowań portów lub określonego mapowania dla kontenera        |
|`ps`     | Wyświetlanie listy kontenerów        |
|`pull`     | Ściąganie obrazu lub repozytorium z rejestru         |
|`start`     | Uruchom co najmniej jeden zatrzymany kontener         |
|`stats`     | Wyświetlanie strumienia na żywo statystyk użycia zasobów kontenerów         |
|`stop`     | Zatrzymaj jeden lub więcej uruchomionych kontenerów        |
|`system`     | Zarządzaj platformą Docker         |
|`top`     | Wyświetlanie uruchomionych procesów kontenera         |

Aby uzyskać pomoc dotyczącą dowolnego z dostępnych poleceń, użyj polecenia `dkrdbe <command-name> --help` .

Na przykład, aby zrozumieć użycie `port` polecenia, wpisz:

```powershell
[10.100.10.10]: P> dkrdbe port --help

Usage:  dkr port CONTAINER [PRIVATE_PORT[/PROTO]]

List port mappings or a specific mapping for the container
[10.100.10.10]: P> dkrdbe login --help

Usage:  docker login [OPTIONS] [SERVER]

Log in to a Docker registry.
If no server is specified, the default is defined by the daemon.

Options:
  -p, --password string   Password
      --password-stdin    Take the password from stdin
  -u, --username string   Username
[10.100.10.10]: PS>
```

Dostępne polecenia dla `dkrdbe` funkcji używają tych samych parametrów, które są używane dla normalnych poleceń platformy Docker. W przypadku opcji i parametrów używanych z poleceniem Docker przejdź do pozycji [Użyj wiersza polecenia platformy Docker](https://docs.docker.com/engine/reference/commandline/docker/).

### <a name="to-check-if-the-module-deployed-successfully"></a>Aby sprawdzić, czy moduł został wdrożony pomyślnie

Moduły obliczeniowe są kontenerami, w których zaimplementowano logikę biznesową. Aby sprawdzić, czy moduł obliczeniowy został pomyślnie wdrożony, uruchom `ps` polecenie i sprawdź, czy kontener (odpowiadający modułowi obliczeniowemu) jest uruchomiony.

Aby uzyskać listę wszystkich kontenerów (łącznie z wstrzymanymi), uruchom `ps -a` polecenie.

```powershell
[10.100.10.10]: P> dkrdbe ps -a
CONTAINER ID        IMAGE                                                COMMAND                   CREATED             STATUS              PORTS                                                                  NAMES
d99e2f91d9a8        edgecompute.azurecr.io/filemovemodule2:0.0.1-amd64   "dotnet FileMoveModuâ€¦"    2 days ago          Up 2 days                                                                                  movefile
0a06f6d605e9        edgecompute.azurecr.io/filemovemodule2:0.0.1-amd64   "dotnet FileMoveModuâ€¦"    2 days ago          Up 2 days                                                                                  filemove
2f8a36e629db        mcr.microsoft.com/azureiotedge-hub:1.0               "/bin/sh -c 'echo \"$â€¦"   2 days ago          Up 2 days           0.0.0.0:443->443/tcp, 0.0.0.0:5671->5671/tcp, 0.0.0.0:8883->8883/tcp   edgeHub
acce59f70d60        mcr.microsoft.com/azureiotedge-agent:1.0             "/bin/sh -c 'echo \"$â€¦"   2 days ago          Up 2 days                                                                                  edgeAgent
[10.100.10.10]: PS>
```

Jeśli wystąpił błąd podczas tworzenia obrazu kontenera lub podczas ściągania obrazu, uruchom polecenie `logs edgeAgent` .  `EdgeAgent` jest kontenerem środowiska uruchomieniowego IoT Edge, który jest odpowiedzialny za obsługę innych kontenerów.

Ponieważ powoduje `logs edgeAgent` zrzut wszystkich dzienników, dobrym sposobem na wyświetlenie ostatnich błędów jest użycie opcji `--tail 20` .


```powershell
[10.100.10.10]: PS>dkrdbe logs edgeAgent --tail 20
2019-02-28 23:38:23.464 +00:00 [DBG] [Microsoft.Azure.Devices.Edge.Util.Uds.HttpUdsMessageHandler] - Connected socket /var/run/iotedge/mgmt.sock
2019-02-28 23:38:23.464 +00:00 [DBG] [Microsoft.Azure.Devices.Edge.Util.Uds.HttpUdsMessageHandler] - Sending request http://mgmt.sock/modules?api-version=2018-06-28
2019-02-28 23:38:23.464 +00:00 [DBG] [Microsoft.Azure.Devices.Edge.Agent.Core.Agent] - Getting edge agent config...
2019-02-28 23:38:23.464 +00:00 [DBG] [Microsoft.Azure.Devices.Edge.Agent.Core.Agent] - Obtained edge agent config
2019-02-28 23:38:23.469 +00:00 [DBG] [Microsoft.Azure.Devices.Edge.Agent.Edgelet.ModuleManagementHttpClient] - Received a valid Http response from unix:///var/run/iotedge/mgmt.soc
k for List modules
--------------------CUT---------------------
--------------------CUT---------------------
08:28.1007774+00:00","restartCount":0,"lastRestartTimeUtc":"2019-02-26T20:08:28.1007774+00:00","runtimeStatus":"running","version":"1.0","status":"running","restartPolicy":"always
","type":"docker","settings":{"image":"edgecompute.azurecr.io/filemovemodule2:0.0.1-amd64","imageHash":"sha256:47778be0602fb077d7bc2aaae9b0760fbfc7c058bf4df192f207ad6cbb96f7cc","c
reateOptions":"{\"HostConfig\":{\"Binds\":[\"/home/hcsshares/share4-dl460:/home/input\",\"/home/hcsshares/share4-iot:/home/output\"]}}"},"env":{}}
2019-02-28 23:38:28.480 +00:00 [DBG] [Microsoft.Azure.Devices.Edge.Agent.Core.Planners.HealthRestartPlanner] - HealthRestartPlanner created Plan, with 0 command(s).
```

### <a name="to-get-container-logs"></a>Aby uzyskać dzienniki kontenerów

Aby uzyskać dzienniki dla określonego kontenera, najpierw utwórz listę kontenerów, a następnie Pobierz dzienniki dla odpowiedniego kontenera.

1. [Nawiąż połączenie z interfejsem programu PowerShell](#connect-to-the-powershell-interface).
2. Aby uzyskać listę uruchomionych kontenerów, uruchom `ps` polecenie.

    ```powershell
    [10.100.10.10]: P> dkrdbe ps
    CONTAINER ID        IMAGE                                                COMMAND                   CREATED             STATUS              PORTS                                                                  NAMES
    d99e2f91d9a8        edgecompute.azurecr.io/filemovemodule2:0.0.1-amd64   "dotnet FileMoveModuâ€¦"    2 days ago          Up 2 days                                                                                  movefile
    0a06f6d605e9        edgecompute.azurecr.io/filemovemodule2:0.0.1-amd64   "dotnet FileMoveModuâ€¦"    2 days ago          Up 2 days                                                                                  filemove
    2f8a36e629db        mcr.microsoft.com/azureiotedge-hub:1.0               "/bin/sh -c 'echo \"$â€¦"   2 days ago          Up 2 days           0.0.0.0:443->443/tcp, 0.0.0.0:5671->5671/tcp, 0.0.0.0:8883->8883/tcp   edgeHub
    acce59f70d60        mcr.microsoft.com/azureiotedge-agent:1.0             "/bin/sh -c 'echo \"$â€¦"   2 days ago          Up 2 days                                                                                  edgeAgent
    ```

3. Zanotuj identyfikator kontenera dla kontenera, dla którego są potrzebne dzienniki.

4. Aby pobrać dzienniki dla określonego kontenera, uruchom `logs` polecenie, podając identyfikator kontenera.

    ```powershell
    [10.100.10.10]: PS>dkrdbe logs d99e2f91d9a8
    02/26/2019 18:21:45: Info: Opening module client connection.
    02/26/2019 18:21:46: Info: Initializing with input: /home/input, output: /home/output.
    02/26/2019 18:21:46: Info: IoT Hub module client initialized.
    02/26/2019 18:22:24: Info: Received message: 1, SequenceNumber: 0 CorrelationId: , MessageId: 081886a07e694c4c8f245a80b96a252a Body: [{"ChangeType":"Created","ShareRelativeFilePath":"\\__Microsoft Data Box Edge__\\Upload\\Errors.xml","ShareName":"share4-dl460"}]
    02/26/2019 18:22:24: Info: Moving input file: /home/input/__Microsoft Data Box Edge__/Upload/Errors.xml to /home/output/__Microsoft Data Box Edge__/Upload/Errors.xml
    02/26/2019 18:22:24: Info: Processed event.
    02/26/2019 18:23:38: Info: Received message: 2, SequenceNumber: 0 CorrelationId: , MessageId: 30714d005eb048e7a4e7e3c22048cf20 Body: [{"ChangeType":"Created","ShareRelativeFilePath":"\\f [10]","ShareName":"share4-dl460"}]
    02/26/2019 18:23:38: Info: Moving input file: /home/input/f [10] to /home/output/f [10]
    02/26/2019 18:23:38: Info: Processed event.
    ```

### <a name="to-monitor-the-usage-statistics-of-the-device"></a>Aby monitorować statystyki użycia urządzenia

Aby monitorować pamięć, użycie procesora CPU i we/wy na urządzeniu, użyj `stats` polecenia.

1. [Nawiąż połączenie z interfejsem programu PowerShell](#connect-to-the-powershell-interface).
2. Uruchom polecenie, aby `stats` wyłączyć strumień na żywo i ściągnąć tylko pierwszy wynik.

   ```powershell
   dkrdbe stats --no-stream
   ```

   W poniższym przykładzie pokazano użycie tego polecenia cmdlet:

    ```
    [10.100.10.10]: P> dkrdbe stats --no-stream
    CONTAINER ID        NAME          CPU %         MEM USAGE / LIMIT     MEM %         NET I/O             BLOCK I/O           PIDS
    d99e2f91d9a8        movefile      0.0           24.4MiB / 62.89GiB    0.04%         751kB / 497kB       299kB / 0B          14
    0a06f6d605e9        filemove      0.00%         24.11MiB / 62.89GiB   0.04%         679kB / 481kB       49.5MB / 0B         14
    2f8a36e629db        edgeHub       0.18%         173.8MiB / 62.89GiB   0.27%         4.58MB / 5.49MB     25.7MB / 2.19MB     241
    acce59f70d60        edgeAgent     0.00%         35.55MiB / 62.89GiB   0.06%         2.23MB / 2.31MB     55.7MB / 332kB      14
    [10.100.10.10]: PS>
    ```

