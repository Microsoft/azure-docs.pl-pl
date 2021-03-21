---
title: Uruchamianie programu OPC Publisher — Azure | Microsoft Docs
description: W tym artykule opisano sposób uruchamiania i debugowania wydawcy OPC. Rozwiązuje również problemy z wydajnością i pamięcią.
author: dominicbetts
ms.author: dobett
ms.date: 06/10/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: f7d6581a1892ebd74a1adba5c09c0af9d3cf9d43
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92079007"
---
# <a name="run-opc-publisher"></a>Uruchamianie wydawcy OPC

> [!IMPORTANT]
> Gdy aktualizujemy ten artykuł, zobacz [Azure Industrial IoT](https://azure.github.io/Industrial-IoT/) , aby uzyskać najbardziej aktualną zawartość.

W tym artykule opisano sposób uruchamiania wydawcy usługi AD Debug OPC. Rozwiązuje również problemy z wydajnością i pamięcią.

## <a name="command-line-options"></a>Opcje wiersza polecenia

Użycie aplikacji jest wyświetlane przy użyciu `--help` opcji wiersza polecenia w następujący sposób:

```sh/cmd
Current directory is: /appdata
Log file is: <hostname>-publisher.log
Log level is: info

OPC Publisher V2.3.0
Informational version: V2.3.0+Branch.develop_hans_methodlog.Sha.0985e54f01a0b0d7f143b1248936022ea5d749f9

Usage: opcpublisher.exe <applicationname> [<IoT Hubconnectionstring>] [<options>]

OPC Edge Publisher to subscribe to configured OPC UA servers and send telemetry to Azure IoT Hub.
To exit the application, just press CTRL-C while it is running.

applicationname: the OPC UA application name to use, required
                  The application name is also used to register the publisher under this name in the
                  IoT Hub device registry.

IoT Hubconnectionstring: the IoT Hub owner connectionstring, optional

There are a couple of environment variables which can be used to control the application:
_HUB_CS: sets the IoT Hub owner connectionstring
_GW_LOGP: sets the filename of the log file to use
_TPC_SP: sets the path to store certificates of trusted stations
_GW_PNFP: sets the filename of the publishing configuration file

Command line arguments overrule environment variable settings.

Options:
      --pf, --publishfile=VALUE
                              the filename to configure the nodes to publish.
                                Default: '/appdata/publishednodes.json'
      --tc, --telemetryconfigfile=VALUE
                              the filename to configure the ingested telemetry
                                Default: ''
  -s, --site=VALUE           the site OPC Publisher is working in. if specified
                                this domain is appended (delimited by a ':' to
                                the 'ApplicationURI' property when telemetry is
                                sent to IoT Hub.
                                The value must follow the syntactical rules of a
                                DNS hostname.
                                Default: not set
      --ic, --iotcentral     publisher will send OPC UA data in IoTCentral
                                compatible format (DisplayName of a node is used
                                as key, this key is the Field name in IoTCentral)
                                . you need to ensure that all DisplayName's are
                                unique. (Auto enables fetch display name)
                                Default: False
      --sw, --sessionconnectwait=VALUE
                              specify the wait time in seconds publisher is
                                trying to connect to disconnected endpoints and
                                starts monitoring unmonitored items
                                Min: 10
                                Default: 10
      --mq, --monitoreditemqueuecapacity=VALUE
                              specify how many notifications of monitored items
                                can be stored in the internal queue, if the data
                                can not be sent quick enough to IoT Hub
                                Min: 1024
                                Default: 8192
      --di, --diagnosticsinterval=VALUE
                              shows publisher diagnostic info at the specified
                                interval in seconds (need log level info).
                                -1 disables remote diagnostic log and diagnostic
                                output
                                0 disables diagnostic output
                                Default: 0
      --ns, --noshutdown=VALUE
                              same as runforever.
                                Default: False
      --rf, --runforever     publisher can not be stopped by pressing a key on
                                the console, but will run forever.
                                Default: False
      --lf, --logfile=VALUE  the filename of the logfile to use.
                                Default: './<hostname>-publisher.log'
      --lt, --logflushtimespan=VALUE
                              the timespan in seconds when the logfile should be
                                flushed.
                                Default: 00:00:30 sec
      --ll, --loglevel=VALUE the loglevel to use (allowed: fatal, error, warn,
                                info, debug, verbose).
                                Default: info
        --ih, --IoT Hubprotocol=VALUE
                              the protocol to use for communication with IoT Hub (
                                allowed values: Amqp, Http1, Amqp_WebSocket_Only,
                                  Amqp_Tcp_Only, Mqtt, Mqtt_WebSocket_Only, Mqtt_
                                Tcp_Only) or IoT EdgeHub (allowed values: Mqtt_
                                Tcp_Only, Amqp_Tcp_Only).
                                Default for IoT Hub: Mqtt_WebSocket_Only
                                Default for IoT EdgeHub: Amqp_Tcp_Only
      --ms, --IoT Hubmessagesize=VALUE
                              the max size of a message which can be send to
                                IoT Hub. when telemetry of this size is available
                                it will be sent.
                                0 will enforce immediate send when telemetry is
                                available
                                Min: 0
                                Max: 262144
                                Default: 262144
      --si, --IoT Hubsendinterval=VALUE
                              the interval in seconds when telemetry should be
                                send to IoT Hub. If 0, then only the
                                IoT Hubmessagesize parameter controls when
                                telemetry is sent.
                                Default: '10'
      --dc, --deviceconnectionstring=VALUE
                              if publisher is not able to register itself with
                                IoT Hub, you can create a device with name <
                                applicationname> manually and pass in the
                                connectionstring of this device.
                                Default: none
  -c, --connectionstring=VALUE
                              the IoT Hub owner connectionstring.
                                Default: none
      --hb, --heartbeatinterval=VALUE
                              the publisher is using this as default value in
                                seconds for the heartbeat interval setting of
                                nodes without
                                a heartbeat interval setting.
                                Default: 0
      --sf, --skipfirstevent=VALUE
                              the publisher is using this as default value for
                                the skip first event setting of nodes without
                                a skip first event setting.
                                Default: False
      --pn, --portnum=VALUE  the server port of the publisher OPC server
                                endpoint.
                                Default: 62222
      --pa, --path=VALUE     the enpoint URL path part of the publisher OPC
                                server endpoint.
                                Default: '/UA/Publisher'
      --lr, --ldsreginterval=VALUE
                              the LDS(-ME) registration interval in ms. If 0,
                                then the registration is disabled.
                                Default: 0
      --ol, --opcmaxstringlen=VALUE
                              the max length of a string opc can transmit/
                                receive.
                                Default: 131072
      --ot, --operationtimeout=VALUE
                              the operation timeout of the publisher OPC UA
                                client in ms.
                                Default: 120000
      --oi, --opcsamplinginterval=VALUE
                              the publisher is using this as default value in
                                milliseconds to request the servers to sample
                                the nodes with this interval
                                this value might be revised by the OPC UA
                                servers to a supported sampling interval.
                                please check the OPC UA specification for
                                details how this is handled by the OPC UA stack.
                                a negative value will set the sampling interval
                                to the publishing interval of the subscription
                                this node is on.
                                0 will configure the OPC UA server to sample in
                                the highest possible resolution and should be
                                taken with care.
                                Default: 1000
      --op, --opcpublishinginterval=VALUE
                              the publisher is using this as default value in
                                milliseconds for the publishing interval setting
                                of the subscriptions established to the OPC UA
                                servers.
                                please check the OPC UA specification for
                                details how this is handled by the OPC UA stack.
                                a value less than or equal zero will let the
                                server revise the publishing interval.
                                Default: 0
      --ct, --createsessiontimeout=VALUE
                              specify the timeout in seconds used when creating
                                a session to an endpoint. On unsuccessful
                                connection attemps a backoff up to 5 times the
                                specified timeout value is used.
                                Min: 1
                                Default: 10
      --ki, --keepaliveinterval=VALUE
                              specify the interval in seconds the publisher is
                                sending keep alive messages to the OPC servers
                                on the endpoints it is connected to.
                                Min: 2
                                Default: 2
      --kt, --keepalivethreshold=VALUE
                              specify the number of keep alive packets a server
                                can miss, before the session is disconneced
                                Min: 1
                                Default: 5
      --aa, --autoaccept     the publisher trusts all servers it is
                                establishing a connection to.
                                Default: False
      --tm, --trustmyself=VALUE
                              same as trustowncert.
                                Default: False
      --to, --trustowncert   the publisher certificate is put into the trusted
                                certificate store automatically.
                                Default: False
      --fd, --fetchdisplayname=VALUE
                              same as fetchname.
                                Default: False
      --fn, --fetchname      enable to read the display name of a published
                                node from the server. this will increase the
                                runtime.
                                Default: False
      --ss, --suppressedopcstatuscodes=VALUE
                              specifies the OPC UA status codes for which no
                                events should be generated.
                                Default: BadNoCommunication,
                                BadWaitingForInitialData
      --at, --appcertstoretype=VALUE
                              the own application cert store type.
                                (allowed values: Directory, X509Store)
                                Default: 'Directory'
      --ap, --appcertstorepath=VALUE
                              the path where the own application cert should be
                                stored
                                Default (depends on store type):
                                X509Store: 'CurrentUser\UA_MachineDefault'
                                Directory: 'pki/own'
      --tp, --trustedcertstorepath=VALUE
                              the path of the trusted cert store
                                Default: 'pki/trusted'
      --rp, --rejectedcertstorepath=VALUE
                              the path of the rejected cert store
                                Default 'pki/rejected'
      --ip, --issuercertstorepath=VALUE
                              the path of the trusted issuer cert store
                                Default 'pki/issuer'
      --csr                  show data to create a certificate signing request
                                Default 'False'
      --ab, --applicationcertbase64=VALUE
                              update/set this applications certificate with the
                                certificate passed in as bas64 string
      --af, --applicationcertfile=VALUE
                              update/set this applications certificate with the
                                certificate file specified
      --pb, --privatekeybase64=VALUE
                              initial provisioning of the application
                                certificate (with a PEM or PFX fomat) requires a
                                private key passed in as base64 string
      --pk, --privatekeyfile=VALUE
                              initial provisioning of the application
                                certificate (with a PEM or PFX fomat) requires a
                                private key passed in as file
      --cp, --certpassword=VALUE
                              the optional password for the PEM or PFX or the
                                installed application certificate
      --tb, --addtrustedcertbase64=VALUE
                              adds the certificate to the applications trusted
                                cert store passed in as base64 string (multiple
                                strings supported)
      --tf, --addtrustedcertfile=VALUE
                              adds the certificate file(s) to the applications
                                trusted cert store passed in as base64 string (
                                multiple filenames supported)
      --ib, --addissuercertbase64=VALUE
                              adds the specified issuer certificate to the
                                applications trusted issuer cert store passed in
                                as base64 string (multiple strings supported)
      --if, --addissuercertfile=VALUE
                              adds the specified issuer certificate file(s) to
                                the applications trusted issuer cert store (
                                multiple filenames supported)
      --rb, --updatecrlbase64=VALUE
                              update the CRL passed in as base64 string to the
                                corresponding cert store (trusted or trusted
                                issuer)
      --uc, --updatecrlfile=VALUE
                              update the CRL passed in as file to the
                                corresponding cert store (trusted or trusted
                                issuer)
      --rc, --removecert=VALUE
                              remove cert(s) with the given thumbprint(s) (
                                multiple thumbprints supported)
      --dt, --devicecertstoretype=VALUE
                              the IoT Hub device cert store type.
                                (allowed values: Directory, X509Store)
                                Default: X509Store
      --dp, --devicecertstorepath=VALUE
                              the path of the iot device cert store
                                Default Default (depends on store type):
                                X509Store: 'My'
                                Directory: 'CertificateStores/IoT Hub'
  -i, --install              register OPC Publisher with IoT Hub and then exits.
                                Default:  False
  -h, --help                 show this message and exit
      --st, --opcstacktracemask=VALUE
                              ignored, only supported for backward comaptibility.
      --sd, --shopfloordomain=VALUE
                              same as site option, only there for backward
                                compatibility
                                The value must follow the syntactical rules of a
                                DNS hostname.
                                Default: not set
      --vc, --verboseconsole=VALUE
                              ignored, only supported for backward comaptibility.
      --as, --autotrustservercerts=VALUE
                              same as autoaccept, only supported for backward
                                cmpatibility.
                                Default: False
      --tt, --trustedcertstoretype=VALUE
                              ignored, only supported for backward compatibility.
                                the trusted cert store will always reside in a
                                directory.
      --rt, --rejectedcertstoretype=VALUE
                              ignored, only supported for backward compatibility.
                                the rejected cert store will always reside in a
                                directory.
      --it, --issuercertstoretype=VALUE
                              ignored, only supported for backward compatibility.
                                the trusted issuer cert store will always
                                reside in a directory.
```

Zwykle parametry połączenia właściciela IoT Hub są określane tylko podczas pierwszego uruchomienia aplikacji. Parametry połączenia są szyfrowane i przechowywane w magazynie certyfikatów platformy. W nowszych uruchomieniach aplikacja odczytuje parametry połączenia z magazynu certyfikatów. Jeśli określisz parametry połączenia dla każdego przebiegu, urządzenie utworzone dla aplikacji w rejestrze IoT Hub urządzenia zostanie usunięte i utworzone ponownie.

## <a name="run-natively-on-windows"></a>Uruchamianie natywne w systemie Windows

Otwórz projekt **opcpublisher. sln** przy użyciu programu Visual Studio, skompiluj rozwiązanie i opublikuj go. Możesz uruchomić aplikację w **katalogu docelowym** , który opublikowano w następujący sposób:

```cmd
dotnet opcpublisher.dll <applicationname> [<IoT Hubconnectionstring>] [options]
```

## <a name="use-a-self-built-container"></a>Korzystanie z własnego kontenera

Utwórz własny kontener i uruchom go w następujący sposób:

```sh/cmd
docker run <your-container-name> <applicationname> [<IoT Hubconnectionstring>] [options]
```

## <a name="use-a-container-from-microsoft-container-registry"></a>Używanie kontenera z programu Microsoft Container Registry

Istnieje wstępnie skompilowany kontener dostępny w programie Microsoft Container Registry. Uruchom go w następujący sposób:

```sh/cmd
docker run mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

Sprawdź [centrum platformy Docker](https://hub.docker.com/_/microsoft-iotedge-opc-publisher) , aby zobaczyć obsługiwane systemy operacyjne i architektury procesora. Jeśli obsługiwana jest architektura systemu operacyjnego i procesora, platforma Docker automatycznie wybiera prawidłowy kontener.

## <a name="run-as-an-azure-iot-edge-module"></a>Uruchom jako moduł Azure IoT Edge

Wydawca OPC jest gotowy do użycia jako moduł [Azure IoT Edge](../iot-edge/index.yml) . W przypadku korzystania z programu OPC Publisher jako modułu IoT Edge jedynymi obsługiwanymi protokołami transportu są **Amqp_Tcp_Only** i **Mqtt_Tcp_Only**.

Aby dodać wydawcę OPC jako moduł do wdrożenia IoT Edge, przejdź do ustawień IoT Hub w Azure Portal i wykonaj następujące czynności:

1. Przejdź do **IoT Edge** i Utwórz lub wybierz urządzenie IoT Edge.
1. Wybierz pozycję **Ustaw moduły**.
1. Wybierz pozycję **Dodaj** w obszarze **moduły wdrażania** , a następnie **IoT Edge module**.
1. W polu **Nazwa** wprowadź wartość **Wydawca**.
1. W polu **Identyfikator URI obrazu** wprowadź `mcr.microsoft.com/iotedge/opc-publisher:<tag>`
1. Dostępne znaczniki można znaleźć w [centrum platformy Docker](https://hub.docker.com/_/microsoft-iotedge-opc-publisher)
1. Wklej następujący kod JSON do pola **Opcje tworzenia kontenera** :

    ```json
    {
        "Hostname": "publisher",
        "Cmd": [
            "--aa"
        ]
    }
    ```

    Ta konfiguracja służy do konfigurowania IoT Edge uruchamiania kontenera o nazwie **Wydawca** przy użyciu obrazu wydawcy programu OPC. Nazwa hosta systemu kontenera jest ustawiona na **Wydawca**. Wydawca OPC jest wywoływany z następującym argumentem wiersza polecenia: `--aa` . Po wybraniu tej opcji program OPC ufa certyfikatom serwerów OPC UA, z którymi nawiązuje połączenie. Możesz użyć dowolnych opcji wiersza polecenia OPC wydawcy. Jedynym ograniczeniem jest rozmiar **opcji tworzenia kontenera** obsługiwanych przez IoT Edge.

1. Pozostaw inne ustawienia bez zmian, a następnie wybierz pozycję **Zapisz**.
1. Jeśli chcesz przetworzyć dane wyjściowe wydawcy OPC lokalnie przy użyciu innego modułu IoT Edge, Wróć do strony **Ustawianie modułów** . Następnie przejdź do karty **Określanie tras** i Dodaj nową trasę, która wygląda jak w poniższym kodzie JSON:

    ```json
    {
      "routes": {
        "processingModuleToIoT Hub": "FROM /messages/modules/processingModule/outputs/* INTO $upstream",
        "opcPublisherToProcessingModule": "FROM /messages/modules/publisher INTO BrokeredEndpoint(\"/modules/processingModule/inputs/input1\")"
      }
    }
    ```

1. Na stronie **Ustawianie modułów** wybierz pozycję **dalej**, aż zostanie osiągnięta Ostatnia strona konfiguracji.
1. Wybierz pozycję **Prześlij** , aby wysłać konfigurację do IoT Edge.
1. Po rozpoczęciu IoT Edge na urządzeniu brzegowym i uruchomieniu **wydawcy** kontenera Docker można wyewidencjonować dane wyjściowe dziennika programu OPC za pomocą `docker logs -f publisher` lub, sprawdzając plik dziennika. W poprzednim przykładzie plik dziennika znajduje się powyżej `d:\iiotegde\publisher-publisher.log` . Możesz również użyć [Narzędzia IoT-Edge-OPC-Publisher-Diagnostics](https://github.com/Azure-Samples/iot-edge-opc-publisher-diagnostics).

### <a name="make-the-configuration-files-accessible-on-the-host"></a>Udostępnianie plików konfiguracyjnych na hoście

Aby udostępnić pliki konfiguracyjne modułu IoT Edge w systemie plików hosta, użyj następujących **opcji tworzenia kontenera**. Poniższy przykład jest wdrożeniem przy użyciu kontenerów systemu Linux w systemie Windows:

```json
{
    "Hostname": "publisher",
    "Cmd": [
        "--pf=./pn.json",
        "--aa"
    ],
    "HostConfig": {
        "Binds": [
            "d:/iiotedge:/appdata"
        ]
    }
}
```

Dzięki tym opcjom OPC Publisher odczytuje węzły, które powinny publikować z pliku `./pn.json` , a katalog roboczy kontenera jest ustawiany na `/appdata` przy uruchamianiu. Dzięki tym ustawieniom OPC Publisher odczytuje plik `/appdata/pn.json` z kontenera w celu pobrania jego konfiguracji. Bez `--pf` opcji program OPC próbuje odczytać domyślny plik konfiguracji `./publishednodes.json` .

Plik dziennika, przy użyciu nazwy domyślnej `publisher-publisher.log` , jest zapisywana `/appdata` i `CertificateStores` katalog jest również tworzony w tym katalogu.

Aby wszystkie te pliki były dostępne w systemie plików hosta, konfiguracja kontenera wymaga woluminu instalacji wiązania. `d://iiotedge:/appdata`Powiązanie mapuje katalog `/appdata` , który jest bieżącym katalogiem roboczym podczas uruchamiania kontenera, do katalogu hosta `d://iiotedge` . Bez tej opcji dane plików nie są utrwalane po uruchomieniu kontenera.

Jeśli używasz kontenerów systemu Windows, składnia `Binds` parametru jest inna. W trakcie uruchamiania kontenera katalog roboczy to `c:\appdata` . Aby umieścić plik konfiguracji w katalogu `d:\iiotedge` na hoście, określ następujące mapowanie w `HostConfig` sekcji:

```json
"HostConfig": {
    "Binds": [
        "d:/iiotedge:c:/appdata"
    ]
}
```

Jeśli używasz kontenerów systemu Linux w systemie Linux, składnia `Binds` parametru jest ponownie inna. W trakcie uruchamiania kontenera katalog roboczy to `/appdata` . Aby umieścić plik konfiguracji w katalogu `/iiotedge` na hoście, określ następujące mapowanie w `HostConfig` sekcji:

```json
"HostConfig": {
    "Binds": [
        "/iiotedge:/appdata"
    ]
}
```

## <a name="considerations-when-using-a-container"></a>Zagadnienia dotyczące korzystania z kontenera

W poniższych sekcjach wymieniono niektóre zagadnienia, które należy wziąć pod uwagę podczas korzystania z kontenera:

### <a name="access-to-the-opc-publisher-opc-ua-server"></a>Dostęp do serwera OPC Publisher OPC UA

Domyślnie serwer OPC Publisher OPC UA nasłuchuje na porcie 62222. Aby uwidocznić ten port wejściowy w kontenerze, użyj następującego polecenia:

```sh/cmd
docker run -p 62222:62222 mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="enable-intercontainer-name-resolution"></a>Włącz rozpoznawanie nazw w programie uncontainer

Aby włączyć rozpoznawanie nazw z kontenera do innych kontenerów, należy utworzyć użytkownika w celu zdefiniowania sieci mostka Docker i połączyć kontener z tą siecią przy użyciu `--network` opcji. Należy również przypisać nazwę kontenera przy użyciu `--name` opcji w następujący sposób:

```sh/cmd
docker network create -d bridge iot_edge
docker run --network iot_edge --name publisher mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

Kontener jest teraz dostępny przy użyciu nazwy `publisher` przez inne kontenery w tej samej sieci.

### <a name="access-other-systems-from-within-the-container"></a>Dostęp do innych systemów z poziomu kontenera

Inne kontenery można osiągnąć przy użyciu parametrów opisanych w poprzedniej sekcji. Jeśli system operacyjny, w którym jest hostowany usługa Docker, jest włączony DNS, uzyskuje dostęp do wszystkich systemów, które są znane działaniu DNS.

W sieciach korzystających z rozpoznawania nazw NetBIOS należy zapewnić dostęp do innych systemów, uruchamiając kontener przy użyciu `--add-host` opcji. Ta opcja efektywnie dodaje wpis do pliku hosta kontenera:

```cmd/sh
docker run --add-host mydevbox:192.168.178.23  mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="assign-a-hostname"></a>Przypisywanie nazwy hosta

Wydawca OPC używa nazwy hosta maszyny, na której jest uruchomiona, aby uzyskać certyfikat i generowanie punktu końcowego. Docker wybiera losową nazwę hosta, jeśli nie jest ona ustawiona przez `-h` opcję. Poniższy przykład pokazuje, jak ustawić wewnętrzną nazwę hosta kontenera `publisher` :

```sh/cmd
docker run -h publisher mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="use-bind-mounts-shared-filesystem"></a>Użyj instalacji powiązań (współużytkowany system plików)

Zamiast korzystać z systemu plików kontenera, można wybrać system plików hosta do przechowywania informacji o konfiguracji i plików dziennika. Aby skonfigurować tę opcję, użyj `-v` opcji `docker run` w trybie instalacji wiązania.

## <a name="opc-ua-x509-certificates"></a>OPC UA X. 509 certyfikaty

OPC UA używa certyfikatów X. 509 do uwierzytelniania klienta i serwera OPC UA podczas ustanawiania połączenia i szyfrowania komunikacji między nimi. Wydawca OPC używa magazynów certyfikatów obsługiwanych przez stos OPC UA do zarządzania wszystkimi certyfikatami. Po uruchomieniu program OPC sprawdza, czy istnieje certyfikat dla samego siebie. Jeśli nie ma certyfikatu w magazynie certyfikatów i jeden z nich nie został przesłany w wierszu polecenia, OPC Publisher tworzy certyfikat z podpisem własnym. Aby uzyskać więcej informacji, zobacz Metoda **InitApplicationSecurityAsync** w `OpcApplicationConfigurationSecurity.cs` .

Certyfikaty z podpisem własnym nie zapewniają żadnych zabezpieczeń, ponieważ nie są podpisane przez zaufany urząd certyfikacji.

Program OPC Publisher udostępnia opcje wiersza polecenia, aby:

- Pobierz informacje o CSR dla bieżącego certyfikatu aplikacji używanego przez wydawcę OPC.
- Udostępnianie wydawcy OPC z certyfikatem podpisanym przez urząd certyfikacji.
- Udostępnij wydawcę OPC z nową parą kluczy i zgodną z certyfikatem podpisanym przez urząd certyfikacji.
- Dodaj certyfikaty do magazynu zaufanych certyfikatów równorzędnych lub zaufanych wystawców.
- Dodaj listę CRL.
- Usuń certyfikat z magazynu certyfikatów zaufanych elementów równorzędnych lub zaufanych wystawców.

Wszystkie te opcje umożliwiają przekazywanie parametrów przy użyciu plików lub ciągów kodowanych algorytmem Base64.

Domyślny typ magazynu dla wszystkich magazynów certyfikatów to system plików, który można zmienić przy użyciu opcji wiersza polecenia. Ponieważ kontener nie zapewnia trwałego magazynu w systemie plików, należy wybrać inny typ magazynu. Użyj opcji Docker, `-v` Aby utrzymać magazyny certyfikatów w systemie plików hosta lub na woluminie platformy Docker. Jeśli używasz woluminu platformy Docker, możesz przekazać certyfikaty przy użyciu zakodowanych ciągów Base64.

Środowisko uruchomieniowe wpływa na sposób utrwalania certyfikatów. Unikaj tworzenia nowych certyfikatów przy każdym uruchomieniu aplikacji:

- Uruchamianie natywne w systemie Windows nie można użyć magazynu certyfikatów aplikacji typu, `Directory` ponieważ dostęp do klucza prywatnego kończy się niepowodzeniem. W takim przypadku należy użyć opcji `--at X509Store` .
- Uruchomiona jako kontener platformy Docker systemu Linux można mapować magazyny certyfikatów na system plików hosta z opcją uruchomienia platformy Docker `-v <hostdirectory>:/appdata` . Ta opcja powoduje, że certyfikat jest trwały w przypadku uruchamiania aplikacji.
- Uruchomiona jako kontener platformy Docker systemu Linux i chcesz użyć magazynu x509 dla certyfikatu aplikacji, użyj opcji uruchamiania platformy Docker `-v x509certstores:/root/.dotnet/corefx/cryptography/x509stores` i opcji aplikacji `--at X509Store`

## <a name="performance-and-memory-considerations"></a>Zagadnienia dotyczące wydajności i pamięci

W tej sekcji omówiono opcje zarządzania pamięcią i wydajnością:

### <a name="command-line-parameters-to-control-performance-and-memory"></a>Parametry wiersza polecenia do kontrolowania wydajności i pamięci

Po uruchomieniu programu OPC Publisher należy znać wymagania dotyczące wydajności i zasoby pamięci dostępne na hoście.

Pamięć i wydajność są wzajemnie zależne i są zależne od konfiguracji liczby węzłów skonfigurowanych do opublikowania. Upewnij się, że następujące parametry spełniają Twoje wymagania:

- IoT Hub wysyła interwał: `--si`
- Rozmiar komunikatu IoT Hub (wartość domyślna `1` ): `--ms`
- Pojemność kolejki monitorowanych elementów: `--mq`

`--mq`Parametr steruje górną granicą pojemności kolejki wewnętrznej, która buforuje wszystkie powiadomienia o zmianie wartości węzła OPC. Jeśli program OPC Publisher nie może wysyłać komunikatów do IoT Hub wystarczająco szybko, ta Kolejka buforuje powiadomienia. Parametr ustawia liczbę powiadomień, które mogą być buforowane. Jeśli zobaczysz liczbę elementów w tej kolejce rosnących w przebiegach testowych, aby uniknąć utraty komunikatów, należy:

- Zmniejszenie interwału wysyłania IoT Hub
- Zwiększ rozmiar komunikatu IoT Hub

`--si`Parametr wymusza OPC wydawcy do wysyłania komunikatów do IoT Hub w określonym interwale. Wydawca OPC wysyła komunikat, gdy tylko zostanie osiągnięty rozmiar komunikatu określony przez `--ms` parametr lub zaraz po osiągnięciu interwału określonego przez `--si` parametr. Aby wyłączyć opcję rozmiar wiadomości, użyj `--ms 0` . W takim przypadku OPC Publisher używa największego możliwego rozmiaru komunikatu IoT Hub 256 kB do danych wsadowych.

`--ms`Parametr umożliwia tworzenie wsadowych komunikatów wysyłanych do IoT Hub. Używany protokół określa, czy obciążenie wysyłania komunikatu do IoT Hub jest wysokie w porównaniu do rzeczywistego czasu wysyłania ładunku. Jeśli scenariusz zezwala na opóźnienie w przypadku, gdy dane pozyskane przez IoT Hub, należy skonfigurować wydawcę OPC do używania największego rozmiaru komunikatu 256 kB.

Przed użyciem wydawcy OPC w scenariuszach produkcyjnych Przetestuj użycie wydajności i pamięci w warunkach produkcyjnych. Można użyć `--di` parametru, aby określić interwał (w sekundach), przez który program OPC zapisuje informacje diagnostyczne.

### <a name="test-measurements"></a>Pomiary testów

W poniższym przykładzie diagnostyki przedstawiono pomiary z różnymi wartościami dla `--si` i `--ms` parametrów publikowania 500 węzłów z interwałem publikowania OPC wynoszącym 1 sekundę.  Test użył kompilacji debugowania wydawcy OPC w systemie Windows 10 natywnie przez 120 sekund. Protokół IoT Hub był domyślnym protokołem MQTT.

#### <a name="default-configuration---si-10---ms-262144"></a>Konfiguracja domyślna (--si 10--ms 262144)

```log
==========================================================================
OpcPublisher status @ 26.10.2017 15:33:05 (started @ 26.10.2017 15:31:09)
---------------------------------
OPC sessions: 1
connected OPC sessions: 1
connected OPC subscriptions: 5
OPC monitored items: 500
---------------------------------
monitored items queue bounded capacity: 8192
monitored items queue current items: 0
monitored item notifications enqueued: 54363
monitored item notifications enqueue failure: 0
monitored item notifications dequeued: 54363
---------------------------------
messages sent to IoT Hub: 109
last successful msg sent @: 26.10.2017 15:33:04
bytes sent to IoT Hub: 12709429
avg msg size: 116600
msg send failures: 0
messages too large to sent to IoT Hub: 0
times we missed send interval: 0
---------------------------------
current working set in MB: 90
--si setting: 10
--ms setting: 262144
--ih setting: Mqtt
==========================================================================
```

Konfiguracja domyślna wysyła dane do IoT Hub co 10 sekund, lub gdy dostępna jest 256 kB danych do IoT Hub do pozyskiwania. Ta konfiguracja dodaje Średni czas oczekiwania wynoszący około 10 sekund, ale ma najniższe prawdopodobieństwo utraty danych z powodu dużego rozmiaru wiadomości. Dane wyjściowe diagnostyki pokazują, że nie zostały utracone aktualizacje węzła OPC: `monitored item notifications enqueue failure: 0` .

#### <a name="constant-send-interval---si-1---ms-0"></a>Stały interwał wysyłania (--si 1--ms 0)

```log
==========================================================================
OpcPublisher status @ 26.10.2017 15:35:59 (started @ 26.10.2017 15:34:03)
---------------------------------
OPC sessions: 1
connected OPC sessions: 1
connected OPC subscriptions: 5
OPC monitored items: 500
---------------------------------
monitored items queue bounded capacity: 8192
monitored items queue current items: 0
monitored item notifications enqueued: 54243
monitored item notifications enqueue failure: 0
monitored item notifications dequeued: 54243
---------------------------------
messages sent to IoT Hub: 109
last successful msg sent @: 26.10.2017 15:35:59
bytes sent to IoT Hub: 12683836
avg msg size: 116365
msg send failures: 0
messages too large to sent to IoT Hub: 0
times we missed send interval: 0
---------------------------------
current working set in MB: 90
--si setting: 1
--ms setting: 0
--ih setting: Mqtt
==========================================================================
```

Gdy rozmiar wiadomości jest ustawiony na 0, OPC wydawcę wewnętrznie dane wsadowe przy użyciu największego obsługiwanego IoT Hub rozmiaru komunikatu, czyli 256 kB. W danych wyjściowych diagnostyki pokazywany jest średni rozmiar komunikatu 115 019 bajtów. W tej konfiguracji program Publisher OPC nie utraci żadnych aktualizacji wartości węzła OPC i w porównaniu z wartością domyślną ma małe opóźnienia.

### <a name="send-each-opc-node-value-update---si-0---ms-0"></a>Wyślij każdą aktualizację wartości węzła OPC (--si 0--MS 0)

```log
==========================================================================
OpcPublisher status @ 26.10.2017 15:39:33 (started @ 26.10.2017 15:37:37)
---------------------------------
OPC sessions: 1
connected OPC sessions: 1
connected OPC subscriptions: 5
OPC monitored items: 500
---------------------------------
monitored items queue bounded capacity: 8192
monitored items queue current items: 8184
monitored item notifications enqueued: 54232
monitored item notifications enqueue failure: 44624
monitored item notifications dequeued: 1424
---------------------------------
messages sent to IoT Hub: 1423
last successful msg sent @: 26.10.2017 15:39:33
bytes sent to IoT Hub: 333046
avg msg size: 234
msg send failures: 0
messages too large to sent to IoT Hub: 0
times we missed send interval: 0
---------------------------------
current working set in MB: 96
--si setting: 0
--ms setting: 0
--ih setting: Mqtt
==========================================================================
```

Ta konfiguracja wysyła dla każdej wartości węzła OPC Zmień komunikat na IoT Hub. Diagnostyka pokazuje średni rozmiar komunikatu wynosi 234 bajtów, co jest małe. Zaletą tej konfiguracji jest to, że Wydawca OPC nie dodaje żadnych opóźnień. Liczba utraconych aktualizacji wartości węzła OPC ( `monitored item notifications enqueue failure: 44624` ) jest wysoka, co sprawia, że ta konfiguracja nie nadaje się do scenariuszy z dużą ilością danych telemetrycznych do opublikowania.

### <a name="maximum-batching---si-0---ms-262144"></a>Maksymalna liczba partii (--si 0--MS 262144)

```log
==========================================================================
OpcPublisher status @ 26.10.2017 15:42:55 (started @ 26.10.2017 15:41:00)
---------------------------------
OPC sessions: 1
connected OPC sessions: 1
connected OPC subscriptions: 5
OPC monitored items: 500
---------------------------------
monitored items queue bounded capacity: 8192
monitored items queue current items: 0
monitored item notifications enqueued: 54137
monitored item notifications enqueue failure: 0
monitored item notifications dequeued: 54137
---------------------------------
messages sent to IoT Hub: 48
last successful msg sent @: 26.10.2017 15:42:55
bytes sent to IoT Hub: 12565544
avg msg size: 261782
msg send failures: 0
messages too large to sent to IoT Hub: 0
times we missed send interval: 0
---------------------------------
current working set in MB: 90
--si setting: 0
--ms setting: 262144
--ih setting: Mqtt
==========================================================================
```

Ta partia konfiguracji ma wiele aktualizacji wartości węzłów OPC, jak to możliwe. Maksymalny rozmiar komunikatu IoT Hub to 256 kB, który jest skonfigurowany w tym miejscu. Nie zażądano interwału wysyłania, co oznacza, że ilość danych dla IoT Hub do pozyskiwania określa czas oczekiwania. Ta konfiguracja ma najmniejsze prawdopodobieństwo utraty wartości węzła OPC i jest odpowiednia do publikowania dużej liczby węzłów. W przypadku korzystania z tej konfiguracji upewnij się, że w scenariuszu nie ma warunków, w których wprowadzono duże opóźnienia, jeśli rozmiar komunikatu 256 kB nie zostanie osiągnięty.

## <a name="debug-the-application"></a>Debugowanie aplikacji

Aby debugować aplikację, Otwórz plik rozwiązania **opcpublisher. sln** w programie Visual Studio i użyj narzędzi debugowania programu Visual Studio.

Jeśli musisz uzyskać dostęp do serwera OPC UA w wydawcze OPC, upewnij się, że zapora zezwala na dostęp do portu, na którym nasłuchuje serwer. Domyślnym portem jest: 62222.

## <a name="control-the-application-remotely"></a>Zdalne kontrolowanie aplikacji

Konfigurowanie węzłów do opublikowania można wykonać przy użyciu metod IoT Hub bezpośrednich.

Wydawca OPC implementuje kilka IoT Hub dodatkowych wywołań metody bezpośredniej do odczytania:

- Informacje ogólne.
- Informacje diagnostyczne dotyczące sesji OPC, subskrypcji i monitorowanych elementów.
- Informacje diagnostyczne dotyczące IoT Hub komunikatów i zdarzeń.
- Dziennik uruchamiania.
- Ostatnie 100 wierszy dziennika.
- Zamknij aplikację.

Poniższe repozytoria GitHub zawierają narzędzia służące do [konfigurowania węzłów do publikowania](https://github.com/Azure-Samples/iot-edge-opc-publisher-nodeconfiguration) i [odczytywania informacji diagnostycznych](https://github.com/Azure-Samples/iot-edge-opc-publisher-diagnostics). Oba narzędzia są również dostępne jako kontenery w usłudze Docker Hub.

## <a name="use-a-sample-opc-ua-server"></a>Korzystanie z przykładowego serwera OPC UA

Jeśli nie masz rzeczywistego serwera OPC UA, możesz rozpocząć pracę, korzystając z [przykładu OPC UA PLC](https://github.com/Azure-Samples/iot-edge-opc-plc) . Ta przykładowa PLC jest również dostępna w usłudze Docker Hub.

Implementuje ona szereg tagów, które generują losowe dane i Tagi z anomaliami. Można zwiększyć przykład, jeśli zachodzi potrzeba symulacji dodatkowych wartości tagów.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak uruchomić program OPC Publisher, zaleca się wykonanie następnych kroków, aby dowiedzieć się więcej o [OPCch](overview-opc-twin.md) i [OPC magazynach](overview-opc-vault.md).