---
title: Uruchamianie programu OPC Publisher — Azure | Dokumenty firmy Microsoft
description: W tym artykule opisano sposób uruchamiania i debugowania programu OPC Publisher. Dotyczy również zagadnień dotyczących wydajności i pamięci.
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
ms.openlocfilehash: c664d4859a306387b4eafa2f19ab5877ccf6eb1b
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686959"
---
# <a name="run-opc-publisher"></a>Uruchamianie wydawcy OPC

W tym artykule opisano sposób uruchamiania programu OPC Publisher w debugowaniu reklam. Dotyczy również zagadnień dotyczących wydajności i pamięci.

## <a name="command-line-options"></a>Opcje wiersza polecenia

Użycie aplikacji jest `--help` wyświetlane przy użyciu opcji wiersza polecenia w następujący sposób:

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

Zazwyczaj można określić ciąg połączenia właściciela usługi IoT Hub tylko przy pierwszym uruchomieniu aplikacji. Parametry połączenia są szyfrowane i przechowywane w magazynie certyfikatów platformy. W późniejszych uruchamia aplikacja odczytuje parametry połączenia z magazynu certyfikatów. Jeśli określisz parametry połączenia przy każdym uruchomieniu, urządzenie utworzone dla aplikacji w rejestrze urządzeń Usługi IoT Hub zostanie usunięte i odtworzone.

## <a name="run-natively-on-windows"></a>Uruchamianie natywnie w systemie Windows

Otwórz projekt **opcpublisher.sln** w programie Visual Studio, skompiluj rozwiązanie i opublikuj je. Aplikację można uruchomić w **opublikowanym katalogu target** w następujący sposób:

```cmd
dotnet opcpublisher.dll <applicationname> [<IoT Hubconnectionstring>] [options]
```

## <a name="use-a-self-built-container"></a>Korzystanie z kontenera wbudowanego

Zbuduj własny kontener i uruchom go w następujący sposób:

```sh/cmd
docker run <your-container-name> <applicationname> [<IoT Hubconnectionstring>] [options]
```

## <a name="use-a-container-from-microsoft-container-registry"></a>Używanie kontenera z rejestru kontenerów firmy Microsoft

W rejestrze kontenerów firmy Microsoft jest dostępny kontener. Rozpocznij go w następujący sposób:

```sh/cmd
docker run mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

Sprawdź [Docker Hub,](https://hub.docker.com/_/microsoft-iotedge-opc-publisher) aby zobaczyć obsługiwane systemy operacyjne i architektury procesorów. Jeśli architektura systemu operacyjnego i procesora CPU jest obsługiwana, program Docker automatycznie wybiera właściwy kontener.

## <a name="run-as-an-azure-iot-edge-module"></a>Uruchom jako moduł usługi Azure IoT Edge

OPC Publisher jest gotowy do użycia jako moduł [usługi Azure IoT Edge.](https://docs.microsoft.com/azure/iot-edge) W przypadku korzystania z programu OPC Publisher jako modułu usługi IoT Edge jedynymi obsługiwanymi protokołami transportu są **Amqp_Tcp_Only** i **Mqtt_Tcp_Only**.

Aby dodać program OPC Publisher jako moduł do wdrożenia usługi IoT Edge, przejdź do ustawień usługi IoT Hub w witrynie Azure portal i wykonaj następujące kroki:

1. Przejdź do **aplikacji IoT Edge** i utwórz lub wybierz urządzenie IoT Edge.
1. Wybierz pozycję **Ustaw moduły**.
1. Wybierz **pozycję Dodaj** w obszarze **Moduły wdrażania,** a następnie **moduł IoT Edge**.
1. W polu **Nazwa** wprowadź **wpisać program Publisher**.
1. W polu **Identyfikator URI obrazu** wprowadź`mcr.microsoft.com/iotedge/opc-publisher:<tag>`
1. Dostępne tagi można znaleźć w centrum [docker](https://hub.docker.com/_/microsoft-iotedge-opc-publisher)
1. Wklej następujący JSON do pola **Opcje tworzenia kontenera:**

    ```json
    {
        "Hostname": "publisher",
        "Cmd": [
            "--aa"
        ]
    }
    ```

    Ta konfiguracja konfiguruje IoT Edge, aby uruchomić kontener o nazwie **wydawca** przy użyciu obrazu programu OPC Publisher. Nazwa hosta systemu kontenera jest ustawiona na **wydawcę**. OPC Publisher jest wywoływany z następującym argumentem wiersza polecenia: `--aa`. Dzięki tej opcji program OPC Publisher ufa certyfikatom serwerów OPC UA, z którymi się łączy. Można użyć dowolnych opcji wiersza polecenia programu OPC Publisher. Jedynym ograniczeniem jest rozmiar **opcji tworzenia kontenera obsługiwanych** przez ioT Edge.

1. Pozostaw inne ustawienia bez zmian, a następnie wybierz pozycję **Zapisz**.
1. Jeśli chcesz przetwarzać dane wyjściowe programu OPC Publisher lokalnie z innym modułem usługi IoT Edge, wróć do strony **Set Modules.** Następnie przejdź do karty **Określanie tras** i dodaj nową trasę, która wygląda jak następujący JSON:

    ```json
    {
      "routes": {
        "processingModuleToIoT Hub": "FROM /messages/modules/processingModule/outputs/* INTO $upstream",
        "opcPublisherToProcessingModule": "FROM /messages/modules/publisher INTO BrokeredEndpoint(\"/modules/processingModule/inputs/input1\")"
      }
    }
    ```

1. Na stronie **Ustaw moduły** wybierz pozycję **Dalej**, aż do ostatniej strony konfiguracji zostanie dotrzeć.
1. Wybierz **opcję Prześlij,** aby wysłać konfigurację do usługi IoT Edge.
1. Po uruchomieniu usługi IoT Edge na urządzeniu brzegowym i **uruchomieniu wydawcy** kontenera platformy docker można wyewidencjonować dane wyjściowe dziennika programu OPC Publisher za pomocą `docker logs -f publisher` lub przez sprawdzenie pliku dziennika. W poprzednim przykładzie plik dziennika `d:\iiotegde\publisher-publisher.log`znajduje się powyżej . Można również użyć [narzędzia diagnostyki iot-edge-opc-publisher.](https://github.com/Azure-Samples/iot-edge-opc-publisher-diagnostics)

### <a name="make-the-configuration-files-accessible-on-the-host"></a>Udostępnianie plików konfiguracyjnych na hoście

Aby udostępnić pliki konfiguracyjne modułu usługi IoT Edge w systemie plików hosta, użyj następujących **opcji tworzenia kontenera**. Poniższy przykład dotyczy wdrożenia przy użyciu kontenerów systemu Linux dla systemu Windows:

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

Dzięki tym opc Publisher odczytuje węzły, które `./pn.json` powinien opublikować z pliku, `/appdata` a katalog roboczy kontenera jest ustawiony na uruchamianie. Dzięki tym ustawieniom program `/appdata/pn.json` OPC Publisher odczytuje plik z kontenera, aby uzyskać jego konfigurację. Bez `--pf` tej opcji program OPC Publisher próbuje `./publishednodes.json`odczytać domyślny plik konfiguracyjny .

Plik dziennika, przy użyciu `publisher-publisher.log`nazwy domyślnej, jest zapisywany i `/appdata` `CertificateStores` katalog jest również tworzony w tym katalogu.

Aby udostępnić wszystkie te pliki w systemie plików hosta, konfiguracja kontenera wymaga woluminu instalacji powiązania. Powiązanie `d://iiotedge:/appdata` mapuje `/appdata`katalog , który jest bieżącym katalogiem roboczym podczas `d://iiotedge`uruchamiania kontenera, do katalogu hosta. Bez tej opcji żadne dane pliku nie są zachowywane po następnym uruchomieniu kontenera.

Jeśli korzystasz z kontenerów systemu Windows, składnia parametru `Binds` jest inna. Przy uruchamianiu kontenera katalog `c:\appdata`roboczy jest . Aby umieścić plik konfiguracyjny w katalogu `d:\iiotedge`na hoście, należy określić następujące mapowanie w `HostConfig` sekcji:

```json
"HostConfig": {
    "Binds": [
        "d:/iiotedge:c:/appdata"
    ]
}
```

Jeśli korzystasz z kontenerów systemu Linux w systemie `Binds` Linux, składnia parametru jest ponownie inna. Przy uruchamianiu kontenera katalog `/appdata`roboczy jest . Aby umieścić plik konfiguracyjny w katalogu `/iiotedge` na hoście, należy określić następujące mapowanie w `HostConfig` sekcji:

```json
"HostConfig": {
    "Binds": [
        "/iiotedge:/appdata"
    ]
}
```

## <a name="considerations-when-using-a-container"></a>Zagadnienia dotyczące korzystania z kontenera

W poniższych sekcjach przedstawiono kilka rzeczy, o których należy pamiętać podczas korzystania z kontenera:

### <a name="access-to-the-opc-publisher-opc-ua-server"></a>Dostęp do serwera OPC Publisher OPC UA

Domyślnie serwer OPC Publisher OPC UA nasłuchuje na porcie 62222. Aby udostępnić ten port przychodzący w kontenerze, użyj następującego polecenia:

```sh/cmd
docker run -p 62222:62222 mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="enable-intercontainer-name-resolution"></a>Włączanie rozpoznawania nazw międzykontenerami

Aby włączyć rozpoznawanie nazw z poziomu kontenera do innych kontenerów, utwórz sieć mostka docker z definiowania użytkownika i połącz kontener z tą siecią `--network` za pomocą tej opcji. Przypisz również kontenerowi `--name` nazwę przy użyciu następującej opcji:

```sh/cmd
docker network create -d bridge iot_edge
docker run --network iot_edge --name publisher mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

Kontener jest teraz osiągalny `publisher` przy użyciu nazwy przez inne kontenery w tej samej sieci.

### <a name="access-other-systems-from-within-the-container"></a>Dostęp do innych systemów z poziomu kontenera

Inne kontenery można uzyskać za pomocą parametrów opisanych w poprzedniej sekcji. Jeśli system operacyjny, w którym jest hostowany program Docker, jest włączony dns, dostęp do wszystkich systemów znanych systemom DNS działa.

W sieciach korzystających z rozpoznawania nazw NetBIOS włącz dostęp `--add-host` do innych systemów, uruchamiając kontener z opcją. Ta opcja skutecznie dodaje wpis do pliku hosta kontenera:

```cmd/sh
docker run --add-host mydevbox:192.168.178.23  mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="assign-a-hostname"></a>Przypisywanie nazwy hosta

OPC Publisher używa nazwy hosta komputera, na której jest uruchomiona dla generowania certyfikatów i punktów końcowych. Docker wybiera losową nazwa hosta, jeśli nie `-h` jest ustawiona przez tę opcję. W poniższym przykładzie pokazano, jak ustawić wewnętrzną nazwa hosta kontenera na: `publisher`

```sh/cmd
docker run -h publisher mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="use-bind-mounts-shared-filesystem"></a>Użyj instalacji wiążących (współużytkowane gosna)

Zamiast używać systemu plików kontenerów, można wybrać system plików hosta do przechowywania informacji o konfiguracji i plików dziennika. Aby skonfigurować tę opcję, należy użyć `-v` opcji w trybie instalacji `docker run` wiązania.

## <a name="opc-ua-x509-certificates"></a>Certyfikaty OPC UA X.509

OPC UA używa certyfikatów X.509 do uwierzytelniania klienta i serwera OPC UA podczas nawiązywanie połączenia i szyfrowania komunikacji między nimi. OPC Publisher używa magazynów certyfikatów obsługiwanych przez stos OPC UA do zarządzania wszystkimi certyfikatami. Podczas uruchamiania program OPC Publisher sprawdza, czy istnieje certyfikat dla siebie. Jeśli w magazynie certyfikatów nie ma certyfikatu, a w wierszu polecenia nie jest jeden przekazywany, program OPC Publisher tworzy certyfikat z podpisem własnym. Aby uzyskać więcej informacji, zobacz **InitApplicationSecurityAsync** metody w `OpcApplicationConfigurationSecurity.cs`.

Certyfikaty z podpisem własnym nie zapewniają żadnych zabezpieczeń, ponieważ nie są podpisane przez zaufany urząd certyfikacji.

OPC Publisher udostępnia opcje wiersza polecenia, aby:

- Pobieranie informacji o csr bieżącego certyfikatu aplikacji używanego przez wydawcę OPC.
- Aprowizuj program OPC Publisher certyfikatem podpisanym przez urząd certyfikacji.
- Aprowizuj program OPC Publisher nową parą kluczy i pasującym certyfikatem podpisanym przez urząd certyfikacji.
- Dodawanie certyfikatów do zaufanego magazynu certyfikatów równorzędnych lub zaufanych wystawców.
- Dodaj lrW.
- Usuwanie certyfikatu z magazynu certyfikatów zaufanych elementów równorzędnych lub zaufanych wystawców.

Wszystkie te opcje umożliwiają przekazywanie parametrów przy użyciu plików lub ciągów zakodowanych base64.

Domyślnym typem magazynu dla wszystkich magazynów certyfikatów jest system plików, który można zmienić za pomocą opcji wiersza polecenia. Ponieważ kontener nie zapewnia magazynu trwałego w systemie plików, należy wybrać inny typ magazynu. Użyj opcji `-v` Platformy Docker, aby utrwalić magazyny certyfikatów w systemie plików hosta lub na woluminie platformy Docker. Jeśli używasz woluminu platformy Docker, można przekazać w certyfikatach przy użyciu base64 zakodowanych ciągów.

Środowisko wykonawcze wpływa na sposób utrwalenia certyfikatów. Unikaj tworzenia nowych magazynów certyfikatów przy każdym uruchomieniu aplikacji:

- Uruchamianie natywnie w systemie Windows, nie `Directory` można użyć magazynu certyfikatów aplikacji typu, ponieważ dostęp do klucza prywatnego kończy się niepowodzeniem. W takim przypadku należy `--at X509Store`użyć opcji .
- Działając jako kontener platformy dokowane Linux, można mapować magazyny certyfikatów do systemu plików hosta za pomocą opcji `-v <hostdirectory>:/appdata`uruchamiania platformy docker . Ta opcja sprawia, że certyfikat trwałe w całej aplikacji działa.
- Działając jako kontener dokowane Linux i chcesz użyć magazynu X509 dla certyfikatu aplikacji, użyj opcji `-v x509certstores:/root/.dotnet/corefx/cryptography/x509stores` uruchamiania platformy docker i opcji aplikacji`--at X509Store`

## <a name="performance-and-memory-considerations"></a>Zagadnienia dotyczące wydajności i pamięci

W tej sekcji omówiono opcje zarządzania pamięcią i wydajnością:

### <a name="command-line-parameters-to-control-performance-and-memory"></a>Parametry wiersza polecenia do sterowania wydajnością i pamięcią

Po uruchomieniu programu OPC Publisher należy pamiętać o wymaganiach dotyczących wydajności i zasobach pamięci dostępnych na hoście.

Pamięć i wydajność są współzależne i oba zależą od konfiguracji liczby węzłów skonfigurowanych do publikowania. Upewnij się, że następujące parametry spełniają Twoje wymagania:

- Centrum IoT wysyła interwał:`--si`
- Rozmiar komunikatu Centrum IoT (domyślnie): `1``--ms`
- Pojemność kolejki monitorowanych elementów:`--mq`

Parametr `--mq` steruje górną granicą pojemności kolejki wewnętrznej, która buforuje wszystkie powiadomienia o zmianie wartości węzła OPC. Jeśli program OPC Publisher nie może wysyłać wiadomości do Centrum IoT hub wystarczająco szybko, ta kolejka buforuje powiadomienia. Parametr ustawia liczbę powiadomień, które mogą być buforowane. Jeśli widzisz liczbę elementów w tej kolejce zwiększenie w przebiegu testu, a następnie, aby uniknąć utraty wiadomości należy:

- Zmniejszanie interwału wysyłania usługi IoT Hub
- Zwiększanie rozmiaru wiadomości w centrum IoT

Parametr `--si` wymusza, aby program OPC Publisher wysyłał wiadomości do Centrum IoT w określonym przedziale czasu. OPC Publisher wysyła wiadomość, gdy tylko zostanie `--ms` osiągnięty rozmiar wiadomości określony przez parametr `--si` lub natychmiast po osiągnięciu interwału określonego przez parametr. Aby wyłączyć opcję rozmiaru `--ms 0`wiadomości, użyj programu . W takim przypadku program OPC Publisher używa największego możliwego rozmiaru komunikatu Usługi IoT Hub 256 kB do danych wsadowych.

Parametr `--ms` umożliwia wsadowe wiadomości wysyłane do usługi IoT Hub. Protokół, którego używasz określa, czy obciążenie związane z wysłaniem wiadomości do usługi IoT Hub jest wysokie w porównaniu do rzeczywistego czasu wysyłania ładunku. Jeśli scenariusz pozwala na opóźnienie podczas pozyskiwania danych przez centrum IoT Hub, należy skonfigurować program OPC Publisher do używania największego rozmiaru wiadomości 256 kB.

Przed użyciem programu OPC Publisher w scenariuszach produkcyjnych należy przetestować wydajność i użycie pamięci w warunkach produkcji. Za pomocą `--di` parametru można określić interwał w sekundach, w który program OPC Publisher zapisuje informacje diagnostyczne.

### <a name="test-measurements"></a>Pomiary testowe

Poniższa przykładowa diagnostyka pokazuje pomiary z różnymi wartościami `--si` i `--ms` parametrami publikującymi 500 węzłów z interwałem publikowania OPC wynoszącym 1 sekundę.  W teście użyto kompilacji debugowania programu OPC Publisher w systemie Windows 10 natywnie przez 120 sekund. Protokół IoT Hub był domyślnym protokołem MQTT.

#### <a name="default-configuration---si-10---ms-262144"></a>Konfiguracja domyślna (--si 10 --ms 262144)

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

Domyślna konfiguracja wysyła dane do usługi IoT Hub co 10 sekund lub gdy 256 kB danych jest dostępne dla Usługi IoT Hub do pozyskiwania. Ta konfiguracja dodaje umiarkowane opóźnienie około 10 sekund, ale ma najniższe prawdopodobieństwo utraty danych ze względu na duży rozmiar wiadomości. Dane wyjściowe diagnostyki pokazują, że nie `monitored item notifications enqueue failure: 0`ma żadnych utraconych aktualizacji węzła OPC: .

#### <a name="constant-send-interval---si-1---ms-0"></a>Stały interwał wysyłania (--si 1 --ms 0)

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

Gdy rozmiar wiadomości jest ustawiony na 0, program OPC Publisher wewnętrznie partiuje dane przy użyciu największego obsługiwanego rozmiaru wiadomości usługi IoT Hub, który wynosi 256 kB. Dane wyjściowe diagnostyczne pokazują, że średni rozmiar wiadomości wynosi 115 019 bajtów. W tej konfiguracji program OPC Publisher nie traci żadnych aktualizacji wartości węzła OPC i w porównaniu z wartością domyślną ma mniejsze opóźnienie.

### <a name="send-each-opc-node-value-update---si-0---ms-0"></a>Wyślij każdą aktualizację wartości węzła OPC (--si 0 --ms 0)

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

Ta konfiguracja wysyła dla każdej wartości węzła OPC zmienić komunikat do Usługi IoT Hub. Diagnostyka pokazuje średni rozmiar wiadomości jest 234 bajtów, który jest mały. Zaletą tej konfiguracji jest to, że OPC Publisher nie dodaje żadnych opóźnień. Liczba utraconych aktualizacji wartości węzła OPC (`monitored item notifications enqueue failure: 44624`) jest wysoka, co sprawia, że ta konfiguracja nie nadaje się do scenariuszy z dużą ilością danych telemetrycznych, które mają zostać opublikowane.

### <a name="maximum-batching---si-0---ms-262144"></a>Maksymalne wsadowanie (--si 0 --ms 262144)

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

Ta konfiguracja partii jak najwięcej aktualizacji wartości węzła OPC, jak to możliwe. Maksymalny rozmiar komunikatu usługi IoT Hub wynosi 256 kB, który jest skonfigurowany w tym miejscu. Nie ma wymaganego interwału wysyłania, co oznacza, że ilość danych dla usługi IoT Hub do pozyskiwania określa opóźnienie. Ta konfiguracja ma najmniejsze prawdopodobieństwo utraty jakichkolwiek wartości węzłów OPC i nadaje się do publikowania dużej liczby węzłów. Korzystając z tej konfiguracji, upewnij się, że scenariusz nie ma warunków, w których występuje duże opóźnienie, jeśli rozmiar wiadomości 256 kB nie zostanie osiągnięty.

## <a name="debug-the-application"></a>Debugowanie aplikacji

Aby debugować aplikację, otwórz plik rozwiązania **opcpublisher.sln** w programie Visual Studio i użyj narzędzi debugowania programu Visual Studio.

Jeśli chcesz uzyskać dostęp do serwera OPC UA w programie OPC Publisher, upewnij się, że zapora umożliwia dostęp do portu, którego serwer nasłuchuje. Domyślny port to: 62222.

## <a name="control-the-application-remotely"></a>Zdalne sterowanie aplikacją

Konfigurowanie węzłów do publikowania można wykonać przy użyciu bezpośrednich metod Usługi IoT Hub.

OPC Publisher implementuje kilka dodatkowych wywołań metody bezpośredniej usługi IoT Hub do odczytu:

- Informacje ogólne.
- Informacje diagnostyczne dotyczące sesji OPC, subskrypcji i monitorowanych elementów.
- Informacje diagnostyczne dotyczące komunikatów i zdarzeń usługi IoT Hub.
- Dziennik uruchamiania.
- Ostatnie 100 wierszy dziennika.
- Zamknij aplikację.

Następujące repozytoria GitHub zawierają narzędzia do [konfigurowania węzłów do publikowania](https://github.com/Azure-Samples/iot-edge-opc-publisher-nodeconfiguration) i [odczytywania informacji diagnostycznych.](https://github.com/Azure-Samples/iot-edge-opc-publisher-diagnostics) Oba narzędzia są również dostępne jako kontenery w centrum platformy Docker Hub.

## <a name="use-a-sample-opc-ua-server"></a>Korzystanie z przykładowego serwera OPC UA

Jeśli nie masz prawdziwego serwera OPC UA, możesz użyć [przykładowego sterownika PLC OPC UA,](https://github.com/Azure-Samples/iot-edge-opc-plc) aby rozpocząć pracę. Ten przykładowy sterownik PLC jest również dostępny w u centrum docker.

Implementuje szereg tagów, które generują losowe dane i tagi z anomaliami. Można rozszerzyć próbkę, jeśli trzeba symulować dodatkowe wartości tagów.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy już wiesz, jak uruchomić OPC Publisher, zalecane następne kroki są, aby dowiedzieć się o [OPC Twin](overview-opc-twin.md) i [OPC Vault](overview-opc-vault.md).
