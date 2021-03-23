---
title: Wdrażanie wydawcy programu Microsoft OPCe
description: W tym samouczku dowiesz się, jak wdrożyć wydawcę programu OPC w trybie autonomicznym.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: tutorial
ms.date: 3/22/2021
ms.openlocfilehash: c82d15541459b5b482e427fc707b92755aa02c6c
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104787756"
---
# <a name="tutorial-deploy-the-opc-publisher"></a>Samouczek: wdrażanie wydawcy OPC

OPC Publisher to w pełni obsługiwany produkt firmy Microsoft opracowany w ramach otwartego, który mostkuje przerwy między zasobami przemysłowymi i chmurą Microsoft Azure. Jest to nawiązywane przez połączenie z zasobami z obsługą OPC UA lub oprogramowaniem do łączności przemysłowej oraz publikowanie danych telemetrycznych na [platformie Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) w różnych formatach, w tym IEC62541 OPC UA pubsub Standard (od wersji 2,6 lub nowszej).

Jest on uruchamiany na [Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/) jako moduł lub w zwykłym Docker jako kontener. Ponieważ wykorzystuje [środowisko uruchomieniowe dla wielu platform .NET](https://docs.microsoft.com/dotnet/core/introduction), działa również natywnie w systemach Linux i Windows 10.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Wdrażanie wydawcy OPC
> * Uruchamianie najnowszej wydanej wersji wydawcy OPC jako kontenera
> * Określ opcje tworzenia kontenera w Azure Portal

Jeśli nie masz subskrypcji platformy Azure, Utwórz konto bezpłatnej wersji próbnej

## <a name="prerequisites"></a>Wymagania wstępne

- Należy utworzyć IoT Hub
- Należy utworzyć urządzenie IoT Edge

## <a name="deploy-the-opc-publisher-from-the-azure-marketplace"></a>Wdrażanie wydawcy OPC z poziomu portalu Azure Marketplace

1. Wybierz subskrypcję platformy Azure do użycia. Jeśli subskrypcja platformy Azure nie jest dostępna, należy ją utworzyć.
2. Wybierz IoT Hub, do którego chcesz wysłać dane. Jeśli żadna IoT Hub nie jest dostępna, należy ją utworzyć.
3. Wybierz urządzenie IoT Edge, na którym ma zostać uruchomiony Wydawca OPC (lub wprowadź nazwę nowego urządzenia IoT Edge, które ma zostać utworzone).
4. Kliknij pozycję Utwórz. Zostanie otwarta strona "Ustawianie modułów na urządzeniu" dla wybranego urządzenia IoT Edge.
5. Kliknij pozycję "OPCPublisher", aby otworzyć stronę "Update IoT Edge module" wydawcy OPC, a następnie wybierz pozycję "Opcje tworzenia kontenera".
6. Określ dodatkowe opcje tworzenia kontenera na podstawie użycia wydawcy OPC, zobacz następną sekcję poniżej.


### <a name="accessing-the-microsoft-container-registry-docker-containers-for-opc-publisher-manually"></a>Ręczne uzyskiwanie dostępu do kontenerów platformy Docker firmy Microsoft Container Registry dla wydawcy OPC

Najnowsza wydana wersja wydawcy OPC może być uruchamiana ręcznie za pośrednictwem:

```
docker run mcr.microsoft.com/iotedge/opc-publisher:latest <name>
```

Gdzie "name" jest nazwą kontenera.

## <a name="specifying-container-create-options-in-the-azure-portal"></a>Określanie opcji tworzenia kontenera w Azure Portal
Podczas wdrażania wydawcy OPC za pomocą Azure Portal opcje tworzenia kontenera można określić na stronie modułu aktualizacji IoT Edge wydawcy programu OPC. Te opcje tworzenia muszą mieć format JSON. Argumenty wiersza polecenia wydawcy OPC można określić za pomocą klawisza cmd, np.:
```
"Cmd": [
    "--pf=./pn.json",
    "--aa"
],
```

Typowy zestaw IoT Edge opcji tworzenia kontenera modułów dla wydawcy OPC jest:
```
{
    "Hostname": "opcpublisher",
    "Cmd": [
        "--pf=./pn.json",
        "--aa"
    ],
    "HostConfig": {
        "Binds": [
            "/iiotedge:/appdata"
        ]
    }
}
```

Po określeniu tych opcji program Publisher odczytaje plik konfiguracji `./pn.json` . Katalog roboczy wydawcy programu OPC jest ustawiany na `/appdata` Uruchamianie, a tym samym OPC Wydawca odczyta plik `/appdata/pn.json` wewnątrz kontenera Docker. Plik dziennika programu OPC Publisher zostanie zapisany w katalogu `/appdata` i `CertificateStores` zostanie także utworzony katalog (używany dla certyfikatów OPC UA). Aby udostępnić te pliki w systemie plików hosta IoT Edge, konfiguracja kontenera wymaga woluminu instalacji wiązania. `/iiotedge:/appdata`Powiązanie spowoduje zamapowanie katalogu `/appdata` na katalog hosta `/iiotedge` (który zostanie utworzony przez środowisko uruchomieniowe IoT Edge, jeśli nie istnieje).
**Bez tego woluminu instalacji powiązania wszystkie pliki konfiguracji wydawcy OPC zostaną utracone po ponownym uruchomieniu kontenera.**

Połączenie z serwerem OPC UA przy użyciu jego nazwy hosta bez serwera DNS skonfigurowanego w sieci można uzyskać, dodając `ExtraHosts` wpis do `HostConfig` sekcji:

```
"HostConfig": {
    "ExtraHosts": [
        "opctestsvr:192.168.178.26"
    ]
}
```

## <a name="next-steps"></a>Następne kroki 
Po wdrożeniu modułu OPC wydawcy Edge następnym krokiem jest jego skonfigurowanie:

> [!div class="nextstepaction"]
> [Konfigurowanie wydawcy OPC](tutorial-publisher-configure-opc-publisher.md)