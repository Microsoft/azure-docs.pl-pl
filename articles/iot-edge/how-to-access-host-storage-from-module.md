---
title: Użyj magazynu lokalnego urządzenia IoT Edge z poziomu modułu Azure IoT Edge | Microsoft Docs
description: Użyj zmiennych środowiskowych i Utwórz opcje, aby umożliwić dostęp modułu do magazynu lokalnego IoT Edge urządzenia.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/14/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 4af63421e831318e6250825cffd1abad415b85bb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91447832"
---
# <a name="give-modules-access-to-a-devices-local-storage"></a>Zapewnianie modułom dostępu do magazynu lokalnego na urządzeniu

Oprócz przechowywania danych przy użyciu usług Azure Storage lub magazynu kontenerów na urządzeniu można również przeznaczyć magazyn na hoście IoT Edge samo urządzenie w celu zwiększenia niezawodności, szczególnie w przypadku działania w trybie offline.

## <a name="link-module-storage-to-device-storage"></a>Łączenie magazynu modułów z magazynem urządzeń

Aby włączyć łącze z magazynu modułów do magazynu w systemie hosta, należy utworzyć zmienną środowiskową dla modułu, która wskazuje folder magazynu w kontenerze. Następnie użyj opcji tworzenia, aby powiązać ten folder magazynu z folderem na komputerze-hoście.

Na przykład jeśli chcesz włączyć Centrum IoT Edge do przechowywania wiadomości w lokalnym magazynie urządzenia i pobrać je później, możesz skonfigurować zmienne środowiskowe i opcje tworzenia w Azure Portal w sekcji **Ustawienia środowiska uruchomieniowego** .

1. W przypadku agentów IoT Edge Hub i IoT Edge Dodaj zmienną środowiskową o nazwie **storageFolder** , która wskazuje katalog w module.
1. Dla Centrum IoT Edge i agenta IoT Edge Dodaj powiązania, aby połączyć katalog lokalny na komputerze hosta z katalogiem w module. Na przykład:

   ![Dodawanie opcji tworzenia i zmiennych środowiskowych dla magazynu lokalnego](./media/how-to-access-host-storage-from-module/offline-storage.png)

Lub można skonfigurować magazyn lokalny bezpośrednio w manifeście wdrożenia. Na przykład:

```json
"systemModules": {
    "edgeAgent": {
        "settings": {
            "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
            "createOptions": {
                "HostConfig": {
                    "Binds":["<HostStoragePath>:<ModuleStoragePath>"]
                }
            }
        },
        "type": "docker",
        "env": {
            "storageFolder": {
                "value": "<ModuleStoragePath>"
            }
        }
    },
    "edgeHub": {
        "settings": {
            "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
            "createOptions": {
                "HostConfig": {
                    "Binds":["<HostStoragePath>:<ModuleStoragePath>"],
                    "PortBindings":{"5671/tcp":[{"HostPort":"5671"}],"8883/tcp":[{"HostPort":"8883"}],"443/tcp":[{"HostPort":"443"}]}}}
        },
        "type": "docker",
        "env": {
            "storageFolder": {
                "value": "<ModuleStoragePath>"
            }
        },
        "status": "running",
        "restartPolicy": "always"
    }
}
```

Zamień `<HostStoragePath>` i `<ModuleStoragePath>` na ścieżkę magazynu hosta i modułu; obie wartości muszą być ścieżką bezwzględną.

Na przykład w systemie Linux `"Binds":["/etc/iotedge/storage/:/iotedge/storage/"]` oznacza, że katalog **/etc/iotedge/Storage** w systemie hosta jest mapowany do katalogu **/iotedge/Storage/** w kontenerze. W systemie Windows inny przykład `"Binds":["C:\\temp:C:\\contemp"]` oznacza, że katalog **c: \\ temp** w systemie hosta jest mapowany do katalogu **c: \\ ** , w którym znajduje się w kontenerze.

Ponadto na urządzeniach z systemem Linux upewnij się, że profil użytkownika dla modułu ma wymagane uprawnienia Odczyt, zapis i wykonywanie do katalogu systemu hosta. Powracanie do wcześniejszego przykładu włączenia IoT Edge centrum do przechowywania wiadomości w lokalnym magazynie urządzenia, należy przyznać uprawnienia do jego profilu użytkownika, UID 1000. (Agent IoT Edge działa jako element główny, więc nie potrzebuje dodatkowych uprawnień). Istnieje kilka sposobów zarządzania uprawnieniami katalogu w systemach Linux, w tym przy użyciu programu, `chown` zmiana właściciela katalogu, a następnie `chmod` zmiana uprawnień, takich jak:

```bash
sudo chown 1000 <HostStoragePath>
sudo chmod 700 <HostStoragePath>
```

Więcej informacji o opcjach tworzenia można znaleźć w dokumentacji [platformy Docker](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate).

## <a name="encrypted-data-in-module-storage"></a>Zaszyfrowane dane w magazynie modułów

Gdy moduły wywołują interfejs API obciążenia demona IoT Edge, aby szyfrować dane, klucz szyfrowania jest uzyskiwany przy użyciu identyfikatora modułu i identyfikatora generacji modułu. Identyfikator generacji jest używany do ochrony kluczy tajnych, jeśli moduł zostanie usunięty z wdrożenia, a następnie inny moduł z tym samym IDENTYFIKATORem modułu zostanie wdrożony na tym samym urządzeniu. Identyfikator generacji modułu można wyświetlić, korzystając z polecenia, w którym jest polecenie [AZ IoT Hub module-Identity show](/cli/azure/ext/azure-cli-iot-ext/iot/hub/module-identity#ext-azure-cli-iot-ext-az-iot-hub-module-identity-show).

Jeśli chcesz udostępnić pliki między modułami w ramach generacji, nie mogą one zawierać żadnych wpisów tajnych lub nie mogą zostać odszyfrowane.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać dodatkowy przykład uzyskiwania dostępu do magazynu hosta z modułu, zobacz temat [Zapisywanie danych na krawędzi za pomocą usługi Azure Blob Storage w IoT Edge](how-to-store-data-blob.md).
